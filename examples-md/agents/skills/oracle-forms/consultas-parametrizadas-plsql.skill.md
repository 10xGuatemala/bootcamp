<!-- Destino sugerido: .claude/skills/consultas-parametrizadas-plsql.skill.md -->

---
name: consultas-parametrizadas-plsql
description: Convierte SQL dinámico concatenado de PL/SQL legacy y de aplicaciones cliente (Java/JPA, .NET) a consultas parametrizadas con bind variables. Detecta el patrón vulnerable a inyección, propone la versión segura, y explica el beneficio doble — seguridad y plan de ejecución cacheado en Oracle. No es un escáner SAST: es un asistente de remediación con ejemplos por stack.
---

# Skill: consultas-parametrizadas-plsql

Las aplicaciones Oracle Forms y los backends que conviven con ellas tienden a construir SQL por concatenación. La frase típica del autor del código original ("solo es para una pantalla interna, los usuarios son confiables") envejece mal: la pantalla interna acaba expuesta en una intranet, la intranet acaba con VPN para terceros, y un parámetro mal escapado abre la base entera. Este skill existe para convertir esos puntos de concatenación a consultas parametrizadas de forma sistemática — no porque las consultas parametrizadas sean una moda, sino porque resuelven dos problemas con una sola decisión: cierran la puerta a la inyección y le dan al optimizador de Oracle un plan de ejecución reutilizable.

## Cuándo invocarme

Úsame cuando:

- Una migración desde Oracle Forms encuentra `EXECUTE IMMEDIATE` con strings concatenados o `DBMS_SQL` armado por concatenación en triggers, paquetes o procedimientos.
- Un backend Java o .NET que sustituye a Forms recibe input del usuario y arma queries por interpolación de strings.
- Un hallazgo de SAST reporta SQL injection (CWE-89) y se necesita la remediación concreta, no solo el reporte.
- Se audita código legacy de PL/SQL antes de exponerlo detrás de una API REST nueva.
- Se está modernizando ORMs y conviene confirmar que el patrón `setParameter` se aplica de forma consistente.

**No me uses para:**

- Hacer pentesting o explotar la vulnerabilidad — soy un skill de remediación, no ofensivo.
- Reescribir un procedimiento entero por estilo cuando el SQL ya es estático y seguro.
- Diseñar el modelo de autorización o el filtrado por tenant — eso es un problema distinto y se resuelve antes de ejecutar el query.
- Sustituir las herramientas SAST (`b3scanner`, SonarQube, herramientas comerciales) — el SAST detecta el patrón; este skill lo corrige.
- Inventar parámetros que el negocio no pidió. La pregunta "este `ORDER BY` también debería ser parámetro" solo se contesta con el equipo.

## Entradas

1. **Snippet o archivo con el SQL dinámico vulnerable**, identificado por SAST o por revisión humana.
2. **Stack donde vive el código** — PL/SQL (paquete/procedimiento/trigger), Java/JPA, Java/JDBC, .NET/Dapper, .NET/EF Core, Python, etc.
3. **Forma de los inputs** — qué viene del usuario (texto libre, identificador, fecha, lista), qué viene de configuración interna.
4. **Restricciones operacionales** — si el query es de alta concurrencia y conviene minimizar parsing, si forma parte de un batch grande, si la consulta varía estructuralmente (no solo en valores).
5. **Contrato con el llamador** — ¿es código interno? ¿Lo invoca una API pública? ¿Hay un middleware que ya valida tipos? Cambia qué tan defensivo debo ser en la conversión.

Si el snippet no incluye los inputs que se concatenan, pedirlo antes de proponer la versión segura. La parametrización mal aplicada es tan riesgosa como no hacerla.

## Reglas obligatorias

- **Toda variable que provenga de usuario externo se pasa como bind variable, sin excepción.** No "este caso es seguro porque lo valido antes": valídalo Y parametrízalo.
- **`ORDER BY` y nombres de tabla/columna no se parametrizan con bind variables** — Oracle no acepta bind variables en cláusulas estructurales. Si esos valores vienen de input, se validan contra una lista cerrada antes de interpolar.
- **`LIKE` con bind variable acepta los comodines `%` y `_`** — la parametrización segura del patrón no exime de escapar los comodines si el negocio los requiere literales.
- **`EXECUTE IMMEDIATE` con `USING` es la forma correcta en PL/SQL.** Concatenar en `EXECUTE IMMEDIATE` es prácticamente equivalente a habilitar inyección.
- **En Java/JPA usa `setParameter` con parámetros nombrados (`:nombre`), no posicionales (`?N`)**, salvo que el equipo ya tenga convención. Los nombrados son más resistentes a refactors.
- **En .NET, `string.Format` y `$"..."` aplicados a SQL son siempre incorrectos.** Sin excepción, sin "pero esto es interno".
- **Cita siempre el beneficio doble** cuando expliques la remediación: seguridad + plan de ejecución cacheado. El segundo argumento convence a equipos que ven la seguridad como burocracia.
- **No inventes el contexto del query.** Si el snippet no muestra de dónde viene el input, marca como pregunta abierta antes de proponer la solución.

## Pasos

### Paso 1: Identificar el patrón vulnerable

Cuatro patrones cubren la mayoría de los casos:

**a) PL/SQL con `EXECUTE IMMEDIATE` concatenado:**

```sql
-- VULNERABLE
PROCEDURE buscar_usuario(p_login IN VARCHAR2) AS
  v_sql VARCHAR2(4000);
  v_id  NUMBER;
BEGIN
  v_sql := 'SELECT id FROM usuarios WHERE login = ''' || p_login || '''';
  EXECUTE IMMEDIATE v_sql INTO v_id;
END;
```

Inyección típica: `p_login = ''' OR ''1''=''1`.

**b) Java/JPA con `createNativeQuery` y concatenación:**

```java
// VULNERABLE — patrón del blog 10X sobre consultas parametrizadas.
String query = "SELECT * FROM usuarios WHERE login = " + login;
Usuarios usuario = em.createNativeQuery(query, Usuarios.class).getSingleResult();
```

Inyección típica: `login = "'John' or 1=1; DELETE FROM usuarios; '"` — devuelve usuarios y, peor, ejecuta un `DELETE`.

**c) .NET con interpolación de strings:**

```csharp
// VULNERABLE
var sql = $"SELECT * FROM Clientes WHERE Correo = '{correo}'";
var clientes = await connection.QueryAsync<Cliente>(sql);
```

**d) Python con `%` o `.format()`:**

```python
# VULNERABLE
cursor.execute("SELECT * FROM productos WHERE nombre = '%s'" % nombre)
```

- Mal: ver el snippet, asumir el stack, proponer una solución. Java y .NET se parecen pero el API es distinto.
- Bien: identificar el patrón, confirmar el stack con el usuario, mostrar exactamente cómo se ve en producción.

**Valor para el agente:** clasificar el patrón en uno de estos cuatro evita que el agente proponga un fix genérico que el equipo tenga que adaptar a su API real.

### Paso 2: Convertir a la versión parametrizada

Por cada patrón del Paso 1, la versión segura equivalente:

**a) PL/SQL con `EXECUTE IMMEDIATE USING`:**

```sql
-- SEGURO
PROCEDURE buscar_usuario(p_login IN VARCHAR2) AS
  v_id NUMBER;
BEGIN
  EXECUTE IMMEDIATE
    'SELECT id FROM usuarios WHERE login = :1'
    INTO v_id
    USING p_login;
END;
```

Si el query es estático, evita `EXECUTE IMMEDIATE` por completo:

```sql
-- AÚN MEJOR cuando el query no varía estructuralmente.
PROCEDURE buscar_usuario(p_login IN VARCHAR2) AS
  v_id NUMBER;
BEGIN
  SELECT id INTO v_id FROM usuarios WHERE login = p_login;
END;
```

**b) Java/JPA con `setParameter` nombrado:**

```java
// SEGURO — patrón recomendado en el blog 10X.
String query = "SELECT u FROM Usuarios u WHERE u.login = :login";
TypedQuery<Usuarios> typedQuery = em.createQuery(query, Usuarios.class);
typedQuery.setParameter("login", login);
Usuarios usuario = typedQuery.getSingleResult();
```

Cuando es JDBC plano:

```java
// SEGURO
try (PreparedStatement ps = conn.prepareStatement(
        "SELECT id FROM usuarios WHERE login = ?")) {
    ps.setString(1, login);
    try (ResultSet rs = ps.executeQuery()) {
        if (rs.next()) return rs.getLong("id");
    }
}
```

**c) .NET con parámetros nombrados:**

```csharp
// SEGURO (Dapper)
var clientes = await connection.QueryAsync<Cliente>(
    "SELECT * FROM Clientes WHERE Correo = :correo",
    new { correo });
```

```csharp
// SEGURO (Oracle.ManagedDataAccess)
using var cmd = new OracleCommand(
    "SELECT * FROM Clientes WHERE Correo = :correo", connection);
cmd.BindByName = true;
cmd.Parameters.Add("correo", OracleDbType.Varchar2).Value = correo;
```

**d) Python con `cx_Oracle` / `oracledb`:**

```python
# SEGURO
cursor.execute(
    "SELECT * FROM productos WHERE nombre = :nombre",
    {"nombre": nombre},
)
```

- Mal: pasar de `+` a `String.format(...)` y declarar la victoria. El formateo de strings no es parametrización; sigue construyendo la query antes de enviarla.
- Bien: el SQL final que llega a Oracle es **literal y constante**; los valores viajan por canal separado. Esa es la prueba de que la conversión es real.

**Valor para el agente:** el equipo recibe el fix listo para pegar. No "considera usar parámetros"; el código antes/después con el API correcto del stack.

### Paso 3: Manejar los casos donde la parametrización no aplica directamente

Algunos casos parecen requerir SQL dinámico estructural — `ORDER BY` por columna elegida por el usuario, nombre de tabla calculado, `IN (...)` con cantidad variable. Reglas:

**`ORDER BY` con columna dinámica:**

```sql
-- VULNERABLE
v_sql := 'SELECT * FROM productos ORDER BY ' || p_columna;
```

```sql
-- SEGURO: validar contra lista cerrada antes de interpolar.
IF p_columna NOT IN ('nombre', 'precio', 'fecha_creacion') THEN
  RAISE_APPLICATION_ERROR(-20001, 'Columna de orden inválida.');
END IF;
v_sql := 'SELECT * FROM productos ORDER BY ' || p_columna;
EXECUTE IMMEDIATE v_sql;
```

**`IN (...)` con cantidad variable:**

```java
// SEGURO en Java/JPA.
String query = "SELECT u FROM Usuarios u WHERE u.id IN :ids";
em.createQuery(query, Usuarios.class)
  .setParameter("ids", listaIds)
  .getResultList();
```

```csharp
// SEGURO en Dapper (.NET).
var usuarios = await connection.QueryAsync<Usuario>(
    "SELECT * FROM Usuarios WHERE Id IN :ids",
    new { ids = listaIds });
```

**`LIKE` con comodines literales:**

Cuando el usuario busca un texto que contiene `%` o `_` y quiere encontrarlo literal, parametriza Y escapa:

```sql
-- SEGURO: el comodín del bind sigue interpretándose como wildcard;
-- el ESCAPE protege los caracteres del input.
SELECT * FROM productos
WHERE  nombre LIKE :patron ESCAPE '\';
-- En el cliente:
-- patron = '%' || REPLACE(REPLACE(input, '\\', '\\\\'), '%', '\\%') || '%'
```

**Valor para el agente:** estos tres casos confunden a desarrolladores y se "resuelven" con concatenación porque no encuentran el patrón parametrizado. Tenerlos en el skill cierra la salida fácil.

### Paso 4: Explicar el beneficio doble

Cuando el equipo se resiste con "es solo una pantalla interna", el argumento sale del blog de 10X y del manual de Oracle: parametrizar también mejora el rendimiento. Cada query estática llega a Oracle con la misma forma textual; el optimizador la parsea una vez, guarda el plan en el *library cache* y lo reusa. La versión concatenada llega con una forma distinta cada vez (`login = 'ana'`, `login = 'juan'`, ...), forzando un *hard parse* por ejecución.

| Aspecto | SQL concatenado | SQL parametrizado |
|---|---|---|
| Vulnerable a inyección | Sí | No (en los valores; las cláusulas estructurales requieren validación aparte) |
| Plan de ejecución en cache | Se invalida con cada valor distinto | Se reutiliza para todas las invocaciones |
| `LIBRARY_CACHE_HIT_RATIO` | Degradado bajo carga | Estable |
| Costo de mantener el código | Validaciones replicadas, escapes manuales | Una sola llamada, sin escapes |

Cuando expliques el beneficio, cita esta tabla. La conversación deja de ser "seguridad vs. velocidad de entrega" y pasa a ser "una sola decisión que resuelve los dos".

**Valor para el agente:** los argumentos económicos convencen a equipos donde la seguridad ya se intentó por la vía moral. Tener la tabla a mano es la diferencia entre obtener el fix y dejar el ticket abierto.

### Paso 5: Verificar la remediación

Tres verificaciones antes de cerrar la conversión:

1. **El SQL final que llega a Oracle no contiene literales de usuario.** Conecta con un monitor (`V$SQL`, `dba_hist_sqltext`) o un log de queries en el ORM. La forma textual debe verse igual sin importar el valor pasado.
2. **El plan se reutiliza.** Después de unas pocas ejecuciones, `V$SQL.EXECUTIONS` para esa sentencia debería crecer mientras que `PARSE_CALLS` se mantiene bajo.
3. **El test de inyección clásico no rompe la app.** Pasa un valor como `'; DROP TABLE x; --` o `' OR '1'='1` por el campo correspondiente. Debe quedar registrado como una búsqueda fallida o devolver cero resultados, nunca como un comportamiento alterado.

- Mal: marcar el ticket como cerrado porque el código compila.
- Bien: tomar capturas de `V$SQL` antes y después, registrar la prueba de inyección y archivarla con el commit.

**Valor para el agente:** la verificación convierte la remediación en evidencia. Auditorías futuras pueden reabrir el hallazgo si hay regresión, pero no si solo hay sospecha.

## Formato de salida

Cuando me invoques con un snippet, devuelvo este formato:

```markdown
## Hallazgo

**Stack:** {PL/SQL | Java/JPA | Java/JDBC | .NET/Dapper | .NET/EF | Python | ...}
**Patrón:** {a, b, c, d del Paso 1}
**Riesgo:** {SQL injection (CWE-89) + parse repetido}

## Antes (vulnerable)

\`\`\`{lang}
{snippet original}
\`\`\`

## Después (parametrizado)

\`\`\`{lang}
{snippet corregido}
\`\`\`

## Notas

- {Cualquier caveat: ORDER BY validado contra lista, LIKE con ESCAPE, etc.}
- {Cualquier supuesto que hice por falta de contexto, marcado para revisión.}

## Verificación

1. Confirmar que `V$SQL` muestra la sentencia con `:bind` y no con literales.
2. Pasar input clásico de prueba (`' OR '1'='1`) y verificar que el comportamiento no cambia.
3. Revisar que `PARSE_CALLS` se mantiene bajo tras múltiples ejecuciones.
```

## Antes de entregar, verifico

- [ ] El stack está correctamente identificado y la API propuesta corresponde a la versión que usa el equipo.
- [ ] Toda variable de origen externo viaja como bind variable, no como texto interpolado.
- [ ] Si hay `ORDER BY` o nombre de tabla dinámico, está validado contra una lista cerrada antes de interpolar.
- [ ] El `LIKE` con comodines del usuario usa `ESCAPE` cuando los comodines deben ser literales.
- [ ] El fix preserva el comportamiento funcional original — no cambié inadvertidamente el filtro al refactorizar.
- [ ] La tabla de beneficios (seguridad + plan cacheado) está citada cuando el equipo se resiste por costo de cambio.
- [ ] Las verificaciones del Paso 5 están enumeradas explícitamente.
- [ ] No inventé inputs que no estaban en el snippet original; supuestos están marcados como tales.

## Errores comunes

- **Pasar de `+` a `String.format(...)` y declarar victoria.** El formateo de strings no es parametrización. La query final sigue contaminada.
- **Parametrizar valores pero dejar el nombre de tabla concatenado.** Sigue habiendo inyección por la cláusula estructural; valida contra lista cerrada.
- **Confundir parametrización con escapado manual.** Escapar comillas en código de aplicación es una carrera perdida; las bind variables le ceden el trabajo al driver, que lo hace bien.
- **Usar `EXECUTE IMMEDIATE` cuando el query es estático.** Si la sentencia no cambia de forma, va como SQL normal — sin SQL dinámico no hay riesgo de inyección.
- **Olvidar el `ESCAPE` en `LIKE` con comodines literales.** El bind protege contra inyección pero no contra `%` o `_` que el usuario escribió como búsqueda literal.
- **Argumentar solo con seguridad** cuando el equipo prioriza velocidad. La conversación cambia cuando se cita el beneficio de plan cacheado.
- **Marcar el ticket cerrado sin la verificación.** El código compilando no es evidencia; `V$SQL` y el test de inyección sí.
- **Aplicar el fix sin entender de dónde viene el input.** Sin saber qué llega del usuario, la parametrización es ritual, no protección.

## Glosario

**Bind variable** *(Bind variable)* — marcador en una sentencia SQL (`:nombre` o `:1`) cuyo valor se envía al driver por canal separado. Oracle ve la sentencia y los valores como dos entradas distintas.

**Consulta parametrizada** *(Parameterized query)* — sentencia donde todos los valores externos son bind variables. Cierre canónico para SQL injection sobre valores.

**SQL injection** *(CWE-89)* — clase de vulnerabilidad donde input del usuario se interpreta como código SQL. Catálogo en [CWE-89](https://cwe.mitre.org/data/definitions/89.html).

**`EXECUTE IMMEDIATE`** *(PL/SQL)* — sentencia para ejecutar SQL dinámico en PL/SQL. La forma `USING` enlaza bind variables; sin `USING`, concatenar valores es prácticamente equivalente a habilitar inyección.

**Hard parse** *(Hard parse)* — proceso completo de análisis sintáctico, semántico y generación de plan que Oracle ejecuta cuando una sentencia llega por primera vez. Reutilizar la sentencia (mismo texto) evita el hard parse.

**Library cache** *(Library cache)* — área de la SGA de Oracle donde se almacenan planes de ejecución para reutilización. Parametrizar mejora el `LIBRARY_CACHE_HIT_RATIO`.

**`V$SQL`** *(V$SQL view)* — vista dinámica de Oracle que muestra sentencias parseadas, sus planes y métricas de ejecución. Herramienta canónica para verificar que la parametrización funciona en producción.

:::info Referencias primarias

- [10X · Importancia de las consultas parametrizadas en SQL](https://www.10x.gt/blog/impulsa-tu-codigo-19/importancia-de-las-consultas-parametrizadas-en-sql-6/) — artículo base, ejemplo Java/JPA + Oracle.
- [OWASP · SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html) — referencia canónica de defensa.
- [CWE-89 · Improper Neutralization of Special Elements in an SQL Command](https://cwe.mitre.org/data/definitions/89.html) — taxonomía del riesgo.
- [Oracle · Using Bind Variables in PL/SQL](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/static-sql.html) — referencia oficial.
- [Skill `migracion-forms`](./migracion-forms.skill.md) — contexto cuando el SQL dinámico vive en triggers de Forms y la conversión es parte de la migración.
- [Skill `revisar-hallazgo-sast`](../general/revisar-hallazgo-sast.skill.md) — triaje cuando el patrón lo detecta un escáner antes de llegar a este skill.
- [Skill `code-review`](../general/code-review.skill.md) — bloqueante de seguridad por concatenación SQL en revisiones de PR.

:::
