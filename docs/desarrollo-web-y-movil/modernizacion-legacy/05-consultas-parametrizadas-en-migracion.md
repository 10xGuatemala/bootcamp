---
sidebar_position: 5
title: Consultas parametrizadas en migraciones legacy
sidebar_label: 1.4.5 Consultas parametrizadas en migraciones
---

# Consultas parametrizadas en migraciones legacy

Cuando una aplicación Oracle Forms se modernizó por capas a lo largo de los años, el SQL dinámico construido por concatenación aparece en todos los rincones. Triggers que arman queries con `EXECUTE IMMEDIATE 'SELECT ... WHERE col = ''' || p_valor || ''''`, paquetes que pegan strings de filtros opcionales con `||`, backends Java o .NET que sustituyen pantallas pero copian el mismo patrón "porque así estaba antes". La migración es la única ventana económica que tienes para corregir esto sin disfrazarlo como un proyecto de seguridad — y, hecho bien, mejora el rendimiento al mismo tiempo.

Este módulo retoma el artículo [Importancia de las consultas parametrizadas en SQL](https://www.10x.gt/blog/impulsa-tu-codigo-19/importancia-de-las-consultas-parametrizadas-en-sql-6/) publicado en el blog de 10X y lo aplica al contexto concreto de un sistema en migración desde Oracle Forms: dónde aparece el patrón vulnerable, cómo convertirlo en cada stack destino (PL/SQL, .NET, Java), qué hacer con los casos que parecen no admitir parametrización, y cómo dejar evidencia de que la corrección realmente quedó hecha.

## Qué es una consulta parametrizada

Una consulta parametrizada es una sentencia SQL donde **el texto del query y los valores que el usuario aporta viajan por canales separados**. En lugar de armar la sentencia concatenando el valor en la cadena, la sentencia incluye un marcador (`:correo`, `:1`, `?`) y el driver de base de datos envía la sentencia y los valores como dos entradas distintas. Oracle parsea la sentencia una vez, guarda el plan de ejecución para reutilizarlo, y nunca interpreta los valores como código SQL.

La forma vulnerable y la forma segura se diferencian en una decisión de una línea, pero la consecuencia operativa es muy distinta. El blog de 10X lo ilustra con Java/JPA:

```java
// Vulnerable: el valor de login se concatena al texto del query.
String query = "SELECT * FROM usuarios WHERE login = " + login;
Usuarios usuario = em.createNativeQuery(query, Usuarios.class).getSingleResult();
```

```java
// Seguro: el query es texto fijo; el valor viaja por setParameter.
String query = "SELECT u FROM Usuarios u WHERE u.login = :login";
TypedQuery<Usuarios> typedQuery = em.createQuery(query, Usuarios.class);
typedQuery.setParameter("login", login);
Usuarios usuario = typedQuery.getSingleResult();
```

En la versión vulnerable, un input como `'John' or 1=1; DELETE FROM usuarios; '` cambia la estructura de la sentencia y ejecuta operaciones que el autor del código no anticipó. En la versión segura, ese mismo input se busca como un nombre de usuario literal y no encuentra nada — el ataque deja de existir como vector.

## Por qué importa en una migración

En un proyecto de migración hay tres razones específicas por las que las consultas parametrizadas son una decisión palanca y no un detalle de implementación.

La primera es **superficie de exposición**. Una pantalla Forms vivía detrás de un cliente pesado en una red interna, con acceso autenticado contra Oracle. El sistema nuevo vive detrás de un endpoint HTTP, expuesto en intranet hoy y probablemente en una red más amplia mañana. El mismo SQL concatenado que toleró veinte años en Forms se vuelve un riesgo crítico al primer día en que un proxy lo expone a un usuario menos contenido.

La segunda es **costo de remediación**. Convertir un trigger PL/SQL a `EXECUTE IMMEDIATE ... USING` o un método Java a `setParameter` cuesta minutos cuando estás migrando la pantalla que lo usa. Hacerlo después, cuando el código ya quedó en producción y nadie recuerda exactamente qué entradas acepta, cuesta una auditoría completa.

La tercera es **rendimiento**. El optimizador de Oracle reutiliza el plan de ejecución cuando el texto de la sentencia es idéntico entre ejecuciones. Las sentencias concatenadas llegan con texto distinto en cada llamada (`login = 'ana'`, `login = 'juan'`, ...) y disparan un *hard parse* por ejecución. Bajo carga, eso degrada el `LIBRARY_CACHE_HIT_RATIO` y carga al servidor con trabajo que las parametrizadas evitan. La conversación con el equipo deja de ser "seguridad contra velocidad de entrega" y pasa a ser "una decisión que paga las dos cosas".

## Objetivo

Al terminar este módulo, sabes identificar los patrones de SQL concatenado que sobreviven a una migración desde Forms, convertirlos a consultas parametrizadas en PL/SQL, Java y .NET, manejar los tres casos donde la parametrización directa no aplica (`ORDER BY`, `IN (...)`, `LIKE` con comodines literales) y verificar con evidencia que la corrección quedó hecha — no solo que el código compila.

## Entradas

- Acceso de lectura al código PL/SQL del legacy (`USER_SOURCE`, `USER_TRIGGERS`, archivos `.fmb`/`.pll` convertidos a XML con `frmf2xml`).
- Acceso al repositorio del backend nuevo (Java, .NET, Python u otro).
- Un hallazgo de SAST con un caso real de inyección, o una revisión humana que detectó el patrón.
- Conocimiento de qué inputs vienen del usuario y cuáles son constantes internas — la conversión cambia según el origen del valor.
- Acceso a una vista `V$SQL` o equivalente para verificar el resultado en producción.

## Pasos para corregir el patrón

### Paso 1: Detectar el patrón vulnerable

El patrón vulnerable tiene cuatro caras frecuentes en sistemas migrados desde Forms. Conviene reconocer las cuatro porque cada una requiere un fix con un API distinto.

```sql
-- a) PL/SQL con EXECUTE IMMEDIATE concatenado.
v_sql := 'SELECT id FROM usuarios WHERE login = ''' || p_login || '''';
EXECUTE IMMEDIATE v_sql INTO v_id;
```

```java
// b) Java/JPA con createNativeQuery concatenado (patrón del blog 10X).
String query = "SELECT * FROM usuarios WHERE login = " + login;
em.createNativeQuery(query, Usuarios.class).getSingleResult();
```

```csharp
// c) .NET con interpolación de strings.
var sql = $"SELECT * FROM Clientes WHERE Correo = '{correo}'";
var clientes = await connection.QueryAsync<Cliente>(sql);
```

```python
# d) Python con format o %.
cursor.execute("SELECT * FROM productos WHERE nombre = '%s'" % nombre)
```

- Mal: *"Aplicamos validación de input en el frontend, entonces el query está protegido."* La validación cliente nunca es suficiente; el atacante envía el request sin pasar por el frontend.
- Bien: *"Detecté concatenación en `usuarios_pkg.buscar_login` y en `UsuariosController.cs:42`. Ambos reciben input del usuario. Plan: parametrizar los dos en el mismo PR."*

**Valor para el agente:** un agente que clasifica el hallazgo en uno de estos cuatro patrones sabe exactamente qué transformación aplicar. No hay un "fix genérico" — cada stack tiene su API y mezclarlos genera regresiones.

### Paso 2: Convertir a la versión parametrizada

Para cada patrón del paso anterior, la conversión equivalente preserva la lógica y elimina la concatenación:

```sql
-- a) PL/SQL — usar USING para enlazar bind variables.
EXECUTE IMMEDIATE
  'SELECT id FROM usuarios WHERE login = :1'
  INTO v_id
  USING p_login;
```

Si el query no varía estructuralmente, prescinde de `EXECUTE IMMEDIATE` y usa SQL estático directo:

```sql
-- a-mejor) Cuando el query es fijo, no hay SQL dinámico en absoluto.
SELECT id INTO v_id FROM usuarios WHERE login = p_login;
```

```java
// b) Java/JPA con setParameter nombrado.
String query = "SELECT u FROM Usuarios u WHERE u.login = :login";
TypedQuery<Usuarios> typedQuery = em.createQuery(query, Usuarios.class);
typedQuery.setParameter("login", login);
Usuarios usuario = typedQuery.getSingleResult();
```

```csharp
// c) .NET — Dapper con parámetros nombrados.
var clientes = await connection.QueryAsync<Cliente>(
    "SELECT * FROM Clientes WHERE Correo = :correo",
    new { correo });
```

```python
# d) Python — cx_Oracle / python-oracledb con dict de bind.
cursor.execute(
    "SELECT * FROM productos WHERE nombre = :nombre",
    {"nombre": nombre},
)
```

- Mal: cambiar `+` por `String.format(...)` o `$"..."` y declarar la victoria. El formateo de strings no es parametrización; la sentencia final sigue contaminada con valores de usuario.
- Bien: comprobar que el texto de la sentencia que llega a la base es **constante**, y que el valor viaja por un canal separado. Esa es la prueba operativa de que la conversión es real.

**Valor para el agente:** el agente entrega el fix listo para pegar — no una recomendación. La diferencia entre "considera parametrizar" y "reemplaza la línea X por esta otra" es la diferencia entre un ticket cerrado y uno que se queda abierto seis meses.

### Paso 3: Manejar los casos donde no aplica directo

Tres situaciones parecen demandar SQL dinámico estructural y se "resuelven" volviendo a concatenar. Hay alternativas seguras para las tres.

**`ORDER BY` con columna elegida por el usuario.** Oracle no acepta bind variables en cláusulas estructurales. La solución no es concatenar el valor: es validar contra una lista cerrada antes de interpolar.

```sql
IF p_columna NOT IN ('nombre', 'precio', 'fecha_creacion') THEN
  RAISE_APPLICATION_ERROR(-20001, 'Columna de orden inválida.');
END IF;
v_sql := 'SELECT * FROM productos ORDER BY ' || p_columna;
EXECUTE IMMEDIATE v_sql;
```

**`IN (...)` con cantidad variable de valores.** Tanto JPA como Dapper aceptan colecciones como parámetro y expanden los placeholders por debajo.

```java
String query = "SELECT u FROM Usuarios u WHERE u.id IN :ids";
em.createQuery(query, Usuarios.class)
  .setParameter("ids", listaIds)
  .getResultList();
```

**`LIKE` con comodines del usuario.** Si el negocio quiere que `%` o `_` en la entrada se traten como literales, parametriza Y escapa:

```sql
SELECT * FROM productos
WHERE  nombre LIKE :patron ESCAPE '\';
```

```text
-- En el cliente, el patrón se arma escapando los comodines del usuario:
patron = '%' || REPLACE(REPLACE(input, '\\', '\\\\'), '%', '\\%') || '%'
```

- Mal: *"`ORDER BY` necesita ser dinámico, así que aquí sí se concatena."* La concatenación queda y mañana llega un input que rompe.
- Bien: *"La columna de orden viene del usuario; queda parametrizada como validación contra `{'nombre','precio','fecha_creacion'}` antes de interpolar. Cualquier otro valor lanza error."*

**Valor para el agente:** los tres casos son la ruta de escape clásica para "no se puede parametrizar". Documentarlos cierra esa puerta.

### Paso 4: Verificar la corrección con evidencia

Marcar un ticket como cerrado porque el código compila no es verificación. La conversión genera tres pruebas observables que conviene archivar junto con el commit:

```sql
-- Primera prueba: la sentencia llega a Oracle con bind variables, no con literales.
SELECT sql_text, executions, parse_calls
FROM   v$sql
WHERE  sql_text LIKE '%usuarios WHERE login%'
ORDER  BY last_active_time DESC;
```

La columna `sql_text` debe mostrar `:login` (o `:1`), no `'ana'` o `'juan'`. Si aún ves literales, el cambio no llegó a producción o el ORM lo está saltando.

La segunda prueba es **comportamiento bajo input clásico de ataque**. Pasa por la API o pantalla una entrada como `' OR '1'='1` o `'; DROP TABLE x; --`. La respuesta debe ser "no encontrado" o equivalente — nunca debe alterar el comportamiento del sistema ni ejecutar acciones.

La tercera prueba es **reutilización del plan**. Tras varias ejecuciones, `executions` debe crecer mientras `parse_calls` se mantiene bajo. Si `parse_calls` crece a la par, alguien sigue armando texto distinto en cada llamada.

**Valor para el agente:** la evidencia convierte la corrección en un artefacto auditable. Si en seis meses alguien sospecha regresión, hay registros que comprobar.

## Salidas

- Lista de archivos y procedimientos donde se detectó SQL concatenado, con su clasificación de patrón (a, b, c, d).
- Pull request (o equivalente) con la conversión a parametrización, archivo por archivo.
- Documento corto con los tres casos especiales (`ORDER BY`, `IN`, `LIKE`) en el código del proyecto y cómo se resolvieron.
- Capturas o exportes de `V$SQL` antes y después, mostrando que las sentencias llegan ahora con bind variables.
- Registro de la prueba de inyección clásica ejecutada contra el endpoint o procedimiento, con la respuesta esperada.

## Errores comunes

- **Cambiar `+` por `String.format(...)` y declarar victoria.** El formateo de strings no es parametrización; la sentencia final sigue contaminada con valores de usuario. Para humanos: lleva a un cierre falso del ticket. Para agentes: invalida cualquier verificación posterior basada en `V$SQL`.
- **Parametrizar los valores y dejar el nombre de tabla o `ORDER BY` concatenado.** Cierra una puerta y deja otra abierta. Cualquier auditoría seria lo detecta y el equipo pierde credibilidad.
- **Confundir parametrización con escapado manual.** Escapar comillas en el código de aplicación es una carrera perdida; las bind variables ceden el trabajo al driver, que lo hace correctamente en todos los casos del estándar.
- **Argumentar solo con seguridad.** En equipos que priorizan velocidad de entrega, el argumento de seguridad llega sordo. El argumento de plan de ejecución cacheado convence donde el de seguridad no logró traccionar.
- **Marcar el ticket cerrado sin la evidencia de `V$SQL`.** Para humanos: deja la puerta para que alguien revierta el cambio sin que nadie note. Para agentes: la próxima auditoría no puede verificar si el código actual cumple.
- **Aplicar el fix sin entender de dónde viene el input.** Si no sabes qué llega del usuario, la parametrización es ritual. Antes de convertir, traza el input desde la entrada hasta la sentencia.

## Prompt de auditoría

Para revisar un repositorio entero contra estas reglas, copia este prompt en la sesión del agente:

```
Audita el código PL/SQL y de aplicación de este repositorio contra el módulo
"Consultas parametrizadas en migraciones legacy". Por cada hallazgo, reporta:

1. Archivo y línea donde aparece el SQL concatenado.
2. Patrón al que pertenece (a: PL/SQL EXECUTE IMMEDIATE concatenado;
   b: Java/JPA createNativeQuery con +; c: .NET con interpolación;
   d: Python con %).
3. Conversión sugerida con código antes/después.
4. Si es un caso especial (ORDER BY dinámico, IN variable, LIKE con comodín
   literal), indica cuál y la estrategia segura aplicable.
5. Prueba de verificación esperada (sentencia en V$SQL, input de inyección
   clásico, reutilización de plan).

Salida en tabla: archivo:línea, patrón, severidad (Bloqueante / Sugerencia),
fix sugerido, prueba de verificación.
```

:::tip Una sola decisión, dos problemas resueltos
La parametrización no se vende como medida de seguridad; se vende como decisión técnica que cierra la inyección y reduce el parsing en Oracle. Cuando el equipo entiende el beneficio doble, deja de discutirlo.
:::

## Puente al siguiente módulo

Con la estrategia definida (1.4.3), el inventario en marcha (1.4.4) y las consultas parametrizadas como práctica estándar en el código nuevo, el siguiente paso es definir cómo el sistema migrado conversa con el resto del entorno — APIs REST consumibles por nuevos clientes web y móviles. La ruta [1.1 Capacitación en servicios web y APIs REST](../capacitacion-servicios-web-api-rest/index.md) cubre ese terreno: contratos, autenticación, versionado y observabilidad de endpoints que reemplazan progresivamente las pantallas Forms.

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** convertir todo SQL dinámico construido por concatenación en sistemas migrados desde Oracle Forms a consultas parametrizadas equivalentes, dejando evidencia verificable de la corrección.

**Entradas:**
- Código PL/SQL del legacy y código del backend nuevo (Java/.NET/Python).
- Hallazgo de SAST o revisión humana que identifica el patrón.
- Conocimiento del origen de cada input (usuario externo vs. constante interna).
- Acceso a `V$SQL` o equivalente para verificación.

**Pasos:**
1. Clasificar cada hallazgo en uno de los cuatro patrones (a-d) según el stack donde vive.
2. Aplicar la transformación equivalente: `USING` en PL/SQL, `setParameter` en Java/JPA, parámetros nombrados en .NET/Dapper, dict de bind en Python.
3. Cuando el query es estático, prescindir de `EXECUTE IMMEDIATE` y usar SQL directo.
4. Resolver `ORDER BY` dinámico con lista cerrada validada; `IN (...)` con colecciones; `LIKE` con `ESCAPE` cuando los comodines son literales.
5. Verificar en `V$SQL` que la sentencia llega con bind variables, no con literales.
6. Probar input clásico de inyección (`' OR '1'='1`) y registrar la respuesta esperada.
7. Confirmar reutilización del plan: `executions` crece mientras `parse_calls` se mantiene.

**Salidas:**
- Pull request con las conversiones aplicadas, una por archivo.
- Tabla de hallazgos con patrón, severidad y fix.
- Evidencia de `V$SQL` antes y después.
- Registro de la prueba de inyección y de la reutilización de plan.

**Errores comunes:**
- Cambiar `+` por interpolación de strings y dar el caso por resuelto.
- Parametrizar valores y dejar `ORDER BY` o nombre de tabla concatenados.
- Argumentar solo con seguridad ante equipos que priorizan entrega.
- Cerrar el ticket sin evidencia observable.

**Referencias cruzadas:**
- [1.4.3 Migración desde Oracle Forms](./03-migracion-desde-oracle-forms.md)
- [1.4.4 Inventario y extracción desde Oracle Forms](./04-inventario-y-extraccion-forms.md)
- [1.4.2 Migración progresiva (strangler fig)](./02-migracion-progresiva.md)
- [1.1.4 Autenticación y Autorización en APIs RESTful](../capacitacion-servicios-web-api-rest/04-autenticacion-autorizacion-rest.md)

</div>

---

## Glosario

**Bind variable** *(Bind variable)* — marcador en una sentencia SQL (`:nombre` o `:1`) cuyo valor se envía al driver por canal separado del texto del query.

**Consulta parametrizada** *(Parameterized query)* — sentencia donde todos los valores externos viajan como bind variables y el texto del query es constante entre ejecuciones.

**SQL injection** *(CWE-89)* — clase de vulnerabilidad donde input del usuario se interpreta como código SQL por concatenación directa al texto del query.

**`EXECUTE IMMEDIATE`** *(PL/SQL)* — sentencia para ejecutar SQL dinámico en PL/SQL. La forma `USING` enlaza bind variables; sin `USING`, concatenar valores equivale a habilitar inyección.

**Hard parse** *(Hard parse)* — análisis completo y generación de plan que Oracle ejecuta la primera vez que ve una sentencia. Las sentencias parametrizadas evitan el hard parse al repetirse.

**Library cache** *(Library cache)* — área de la SGA de Oracle donde se almacenan planes de ejecución para reutilización; parametrizar mejora su tasa de aciertos.

**`V$SQL`** *(V$SQL view)* — vista dinámica de Oracle que muestra sentencias parseadas y sus métricas; herramienta canónica para verificar parametrización en producción.

:::info Referencias primarias

- [Importancia de las consultas parametrizadas en SQL — blog 10X](https://www.10x.gt/blog/impulsa-tu-codigo-19/importancia-de-las-consultas-parametrizadas-en-sql-6/) — artículo base de este módulo.
- [OWASP · SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html) — referencia canónica de defensa.
- [CWE-89 · Improper Neutralization of Special Elements in an SQL Command](https://cwe.mitre.org/data/definitions/89.html) — taxonomía del riesgo.
- [Oracle · Using Bind Variables in PL/SQL](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/static-sql.html) — referencia oficial.
- [1.4.3 Migración desde Oracle Forms](./03-migracion-desde-oracle-forms.md) — contexto del proyecto en el que aplican estas conversiones.

:::

---

<AuthorCredit note={<>Basado en el artículo <a href="https://www.10x.gt/blog/impulsa-tu-codigo-19/importancia-de-las-consultas-parametrizadas-en-sql-6/" target="_blank" rel="noopener noreferrer">"Importancia de las consultas parametrizadas en SQL"</a> del <a href="https://www.10x.gt/blog/" target="_blank" rel="noopener noreferrer">blog de 10X</a>, aplicado al contexto de migraciones legacy desde Oracle Forms.</>} />
