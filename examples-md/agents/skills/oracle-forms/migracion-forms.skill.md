<!-- Destino sugerido: .claude/skills/migracion-forms.skill.md -->

---
name: migracion-forms
description: Acompaña a un equipo DBA en la migración de una aplicación Oracle Forms a un stack moderno (Oracle APEX, .NET 8+ o Java 21+). Produce inventario verificable de la app legacy, extracción de la lógica PL/SQL embebida, decisión de stack destino con la matriz 10X, plan de coexistencia de datos sin duplicar fuentes de verdad y checklist de cutover — nunca "reescribimos todo desde cero".
---

# Skill: migracion-forms

Las aplicaciones Oracle Forms suelen llevar décadas funcionando. La tentación natural al modernizarlas es abrir un proyecto en limpio y reescribirlo todo: nuevo lenguaje, nueva arquitectura, nuevas pantallas. Casi siempre es un error caro. La lógica que sobrevivió veinte años en producción contiene reglas de negocio que nadie documentó nunca, y empezar desde cero significa redescubrir esas reglas a través de incidentes en el sistema nuevo. Este skill existe para conducir la migración como un trabajo gradual, verificable, con la base Oracle como fuente de verdad durante la transición y con decisiones explícitas en cada paso — no como una odisea de reescritura de fe.

## Cuándo invocarme

Úsame cuando:

- Una organización tiene una aplicación Oracle Forms en producción y plantea modernizarla a web o móvil.
- Se necesita un inventario realista del legacy antes de cotizar o planificar el proyecto.
- Hay que decidir entre Oracle APEX, .NET o Java como destino y la conversación amenaza con resolverse por moda.
- El proyecto ya arrancó y conviene auditar si la coexistencia de datos está bien planteada o si se están creando fuentes duplicadas de verdad.
- Se quiere extraer la lógica PL/SQL embebida en `triggers` de Forms para llevarla al backend nuevo sin perder reglas.

**No me uses para:**

- Reescribir línea por línea PL/SQL a C# o Java — mi enfoque es identificar qué se queda en la base y qué se lleva, no traducir mecánicamente.
- Diseñar la UI del sistema nuevo — eso pertenece a los skills de diseño y a los módulos UX/UI del bootcamp.
- Definir el contrato de un endpoint nuevo en .NET — usa `nuevo-endpoint-rest-net`.
- Decidir patrones estructurales del backend (Strategy, Factory, Builder) — usa `patrones-diseno-net`.
- Reemplazar el blog [Alternativas para migrar de Oracle Forms](https://www.10x.gt/blog/transformacion-digital/alternativas-para-migrar-de-oracle-forms/) ni el módulo [1.4.3 Migración desde Oracle Forms](../../../../docs/desarrollo-web-y-movil/modernizacion-legacy/03-migracion-desde-oracle-forms.md) — soy la versión operativa que un agente ejecuta; ambos son la teoría que debes leer antes.

## Entradas

1. **Acceso a los fuentes de Forms** — al menos los archivos `.fmb` (formularios), `.pll` (librerías compartidas), `.mmb` (menús) y `.rdf` (reportes), o sus equivalentes exportados a XML (`.fmb` convertido con `frmf2xml`).
2. **Acceso de lectura a la base Oracle** — usuario con `SELECT` sobre `USER_OBJECTS`, `USER_DEPENDENCIES`, `USER_SOURCE`, `USER_TRIGGERS`, `USER_VIEWS`, `USER_PROCEDURES`.
3. **Inventario funcional declarado** — qué pantallas usan las áreas de negocio (caja, despacho, contabilidad), aunque sea una lista informal en una hoja.
4. **Contexto del proyecto** — datos en Oracle hoy, experiencia del equipo, criticidad del sistema, integraciones externas, plazos.
5. **Restricciones inamovibles** — datos que no pueden salir de Oracle por regulación, pantallas que no pueden detenerse durante el cutover, ventanas de mantenimiento permitidas.

Si falta el acceso a fuentes Forms o a la base, el inventario que produzca será aspiracional. Pedirlo antes de empezar.

## Reglas obligatorias

- **Mantén en la base lo que ya funciona.** Vistas con lógica de filtrado, procedimientos que autorizan operaciones, tablas de auditoría — sobreviven la migración. Lo que cambia es quién las invoca, no lo que hacen.
- **Una sola fuente de verdad.** Si el legacy escribe en una tabla, el sistema nuevo escribe en la misma tabla cuando corresponde. Cero copias paralelas "por si acaso".
- **Decide el stack destino antes de mover una línea.** APEX, .NET 8+ o Java 21+ no son intercambiables; cambiar de stack a la mitad del proyecto es reescritura.
- **No traduzcas PL/SQL línea por línea al backend.** Identifica el patrón real (reglas duplicadas, formatos rígidos, validaciones) y modela el patrón, no el código.
- **Las bibliotecas `.pll` son código compartido.** Antes de migrar un `.fmb`, identifica de qué `.pll` depende y trata esa biblioteca como un módulo del backend nuevo, no como un parche por formulario.
- **Cada pantalla migrada debe poder coexistir con su versión legacy** durante un período acordado. Si no hay forma de probar la pantalla nueva en paralelo con la vieja, el cutover es una apuesta.
- **Documenta lo que el agente descubre.** Una regla de negocio extraída de un trigger debe quedar registrada en un artefacto del proyecto, no solo en el commit que la migra.

## Pasos

### Paso 1: Inventariar los componentes Forms

Antes de planear hay que saber qué existe. El inventario tiene dos lados: los archivos fuente y los objetos de base de datos que esos archivos consumen.

Del lado del filesystem:

```bash
find . -name "*.fmb" -o -name "*.pll" -o -name "*.mmb" -o -name "*.rdf" \
  | sort | tee inventario-fuentes-forms.txt
```

Convierte los `.fmb` a XML para que el contenido sea procesable:

```bash
# Requiere Oracle Forms Builder instalado.
frmf2xml *.fmb
```

Del lado de la base, ejecuta un script de descubrimiento que rinde una foto del legacy:

```sql
-- Triggers de Forms registrados en la base (algunos proyectos los publican).
SELECT trigger_name, table_name, triggering_event, status
FROM   user_triggers
ORDER  BY table_name, trigger_name;

-- Procedimientos, funciones y paquetes que Forms invoca.
SELECT object_type, object_name, status, last_ddl_time
FROM   user_objects
WHERE  object_type IN ('PACKAGE','PROCEDURE','FUNCTION')
ORDER  BY object_type, object_name;

-- Vistas que las pantallas consumen como fuente de datos.
SELECT view_name FROM user_views ORDER BY view_name;

-- Dependencias: quién depende de qué.
SELECT name, type, referenced_name, referenced_type
FROM   user_dependencies
WHERE  referenced_owner = USER;
```

- Mal: tomar la lista de pantallas que el área de negocio recuerda y considerarla "el inventario". Quedan fuera utilitarios internos, reportes que solo corre contabilidad a fin de mes y procedimientos huérfanos que algún `trigger` invoca.
- Bien: cruzar el inventario de archivos con `user_objects` y `user_dependencies`. Lo que aparece en la base pero no en los fuentes, o al revés, es una pregunta abierta para el equipo cliente — y suele revelar lo más interesante.

**Valor para el agente:** un inventario verificable es la línea base. Cualquier afirmación posterior ("ya migramos el 30 %") se mide contra esta lista, no contra una percepción.

### Paso 2: Extraer la lógica embebida en triggers de Forms

Los `triggers` de Forms (`WHEN-VALIDATE-ITEM`, `WHEN-BUTTON-PRESSED`, `POST-QUERY`, `KEY-COMMIT`) son el lugar donde décadas de reglas de negocio quedaron escritas. Migrar la pantalla sin extraer estas reglas es perder la migración.

Procedimiento por cada `.fmb` convertido a XML:

```bash
# Listar todos los triggers de un formulario.
grep -A2 '<Trigger ' formulario.xml | grep 'Name=' | sort -u
```

Para cada `trigger`, extrae el cuerpo PL/SQL y clasifícalo:

| Categoría | Qué hacer en la migración |
|---|---|
| Validación de un campo (formato, rango, existencia) | Migrar al backend nuevo como validación de DTO o regla de servicio. Mantener en la base solo si la regla protege integridad transaccional. |
| Cálculo derivado (totales, fechas calculadas) | Migrar al backend si el dato no necesita persistirse. Si se persiste, dejar en un trigger de tabla o columna virtual. |
| Llamada a procedimiento del paquete | Mantener el procedimiento en la base; el backend lo invoca. No reescribir el procedimiento en C#/Java. |
| Lógica de navegación entre pantallas (`GO_BLOCK`, `GO_ITEM`) | Tirar a la basura — la navegación pertenece al frontend nuevo y no se traduce. |
| Manejo de UI (mensajes, color de fondo) | Tirar a la basura — pertenece al diseño del frontend, no a la lógica. |

- Mal: copiar el cuerpo de `WHEN-VALIDATE-ITEM` y traducirlo a un `[Validator]` de C# preservando hasta los nombres de variables. Trae deuda nueva con cara de moderna.
- Bien: leer el cuerpo, identificar la regla (ej. "el correlativo de envío no puede ser menor que el último emitido"), documentar la regla en el backlog del módulo de destino y modelarla con la API del stack nuevo.

**Valor para el agente:** un agente que clasifica triggers contra esta tabla sabe qué llevar y qué dejar. La decisión es verificable contra el código, no contra intuición.

### Paso 3: Decidir el stack destino con la matriz 10X

Antes de mover una línea, ubica el proyecto en la matriz que propone el blog de 10X. La copia operativa:

| Pregunta clave | Oracle APEX | .NET 8+ (C#) | Java 21+ |
|---|---|---|---|
| ¿Dónde están tus datos hoy? | En Oracle y seguirán allí | SQL Server, MySQL, PostgreSQL u otra base relacional | Fuentes variadas o migración planificada a entornos distribuidos |
| ¿Qué experiencia técnica tiene tu equipo? | Fuerte en PL/SQL, familiaridad con Forms | Stack Microsoft, Visual Studio, APIs REST | Java, Spring, arquitecturas empresariales complejas |
| ¿Qué tanto necesitas escalar o integrar con otras plataformas? | Escalabilidad moderada, integración limitada al ecosistema Oracle | Alta escalabilidad e integración con múltiples servicios | Máxima escalabilidad, microservicios y sistemas distribuidos |
| ¿Qué tan crítica es la aplicación? | Importante, pero no misión crítica | Misión crítica, bajo control | Misión crítica, con alta disponibilidad y resiliencia |

Reglas para usar la matriz:

- **Si las cuatro respuestas apuntan a la misma columna, la decisión es mecánica.** Documéntala y avanza.
- **Si las respuestas se dispersan**, la conversación honesta es sobre compensaciones — no sobre "cuál es mejor". Probablemente haya que formar al equipo antes de elegir, o aceptar un destino que no es óptimo en una dimensión pero sí en las otras tres.
- **Una decisión firmada por responsable técnico y de negocio vale más que una comparación de rendimiento.** La matriz disciplina la conversación; no la sustituye.

Salida obligatoria de este paso: un documento corto con la columna elegida, el razonamiento por cada fila y los nombres que firman la decisión.

**Valor para el agente:** una columna elegida con firma cierra el debate. El resto del skill opera sobre esa decisión sin volver a abrirla.

### Paso 4: Diseñar la coexistencia de datos

Durante la transición el sistema nuevo y el viejo conviven sobre la misma base Oracle. Dos reglas no negociables:

1. **No duplicar fuentes de verdad.** Si el legacy escribe en `ENVIOS`, el sistema nuevo escribe en `ENVIOS`. No `ENVIOS_NEW`, no réplicas, no tablas espejo.
2. **Delegar en la base lo que la base hace bien.** Correlativos únicos, integridad referencial, transacciones — resueltas con `SELECT FOR UPDATE`, claves únicas y `COMMIT/ROLLBACK`, no con locks distribuidos del backend.

Patrón típico para correlativos compartidos:

```sql
-- En la base, mantener el procedimiento que ya funciona.
CREATE OR REPLACE PROCEDURE siguiente_correlativo (
  p_tipo  IN  VARCHAR2,
  p_valor OUT NUMBER
) AS
BEGIN
  SELECT valor + 1
    INTO p_valor
    FROM correlativos
   WHERE tipo = p_tipo
   FOR UPDATE;

  UPDATE correlativos
     SET valor = p_valor
   WHERE tipo = p_tipo;
END;
```

```csharp
// En el backend nuevo, invocar el procedimiento existente.
var p = new OracleCommand("siguiente_correlativo", connection)
{
    CommandType = CommandType.StoredProcedure
};
p.Parameters.Add("p_tipo", OracleDbType.Varchar2).Value = "ENVIO";
var salida = p.Parameters.Add("p_valor", OracleDbType.Decimal);
salida.Direction = ParameterDirection.Output;
await p.ExecuteNonQueryAsync();
```

- Mal: implementar la generación de correlativos en C# con un `lock` en memoria — falla cuando hay más de una instancia del backend, y compite con el legacy cuando ambos están vivos.
- Bien: invocar el procedimiento existente desde ambos lados. El `SELECT FOR UPDATE` garantiza unicidad transaccional sin importar quién llama.

**Valor para el agente:** un agente que diseña la coexistencia respetando estas reglas produce arquitecturas mixtas estables. Las arquitecturas "puras nuevas" se rompen en el primer corte de luz.

### Paso 5: Migrar pantalla por pantalla con coexistencia verificable

Cada migración de pantalla sigue el mismo ciclo:

1. **Documentar el contrato de la pantalla vieja**: qué datos lee, qué escribe, qué reglas valida, qué reportes dispara.
2. **Implementar la pantalla nueva** consumiendo la misma base, vía API del stack destino.
3. **Operar en paralelo**: el área de negocio usa la pantalla nueva, pero la vieja sigue disponible como red de seguridad.
4. **Reconciliar diariamente**: un job compara registros creados/modificados por ambos sistemas. Cero discrepancias durante el período acordado es la señal de luz verde.
5. **Cutover** cuando la reconciliación es estable: la pantalla vieja queda en solo lectura o se retira.

- Mal: hacer "big bang" — un fin de semana se apaga la pantalla vieja y se enciende la nueva. Sin red de seguridad, cualquier regla no extraída se descubre en producción.
- Bien: operar dos meses con ambas pantallas en paralelo, reconciliando datos, antes de retirar la vieja. La operación nunca se detiene.

**Valor para el agente:** el ciclo es repetible. Cada pantalla migrada produce las mismas cinco salidas verificables, y el avance del proyecto se mide pantalla por pantalla.

### Paso 6: Planificar el cutover y el retiro del legacy

El cutover no es un evento; es el resultado de pantallas migradas en serie. Aun así, retirar Forms requiere planificación:

- **Inventario de pantallas restantes**: las últimas en migrar suelen ser las raras (reportes anuales, mantenimientos esporádicos). Decidir si migrar o aceptar que sobrevivirán en el legacy operado por excepción.
- **Bibliotecas `.pll` huérfanas**: si una librería ya no es invocada por ninguna pantalla viva, marcarla para retiro junto con sus procedimientos asociados.
- **Documentación de qué quedó en la base**: vistas, procedimientos, triggers que ahora invoca el backend nuevo. El equipo DBA hereda esta lista.
- **Plan de regresión**: durante 30–90 días después del retiro, mantener el código Forms en un servidor cold para poder volver atrás si aparece una regla no detectada.

**Valor para el agente:** el cutover sin retiro es una migración que no terminó. El skill obliga a planificar el final desde el inicio.

## Salidas

- **`inventario-fuentes-forms.txt`** — lista de `.fmb`, `.pll`, `.mmb`, `.rdf` y su tamaño.
- **`inventario-objetos-bd.csv`** — paquetes, procedimientos, funciones, vistas, triggers que la app legacy depende, con `LAST_DDL_TIME`.
- **`triggers-clasificados.md`** — por cada `trigger` de Forms, su clasificación (validación / cálculo / llamada a paquete / navegación / UI) y destino en el sistema nuevo.
- **`decision-stack.md`** — matriz 10X firmada con la columna elegida y el razonamiento por fila.
- **`coexistencia-datos.md`** — qué tablas son compartidas, qué procedimientos se invocan desde ambos sistemas, cómo se generan correlativos.
- **`backlog-migracion-pantallas.md`** — pantallas en orden de migración, con contrato documentado y criterio de cutover por pantalla.

## Antes de entregar, verifico

- [ ] El inventario de archivos cruza con el inventario de objetos de base — toda discrepancia está marcada como pregunta abierta.
- [ ] Cada `trigger` de Forms está clasificado contra la tabla del Paso 2; ninguno quedó como "ver después".
- [ ] La matriz 10X está completa con respuesta explícita en las cuatro filas y firma de responsable técnico y de negocio.
- [ ] No hay tablas duplicadas en el diseño de coexistencia. Si parece haber duplicación, está justificada por escrito.
- [ ] Las pantallas migradas tienen un plan de operación en paralelo con la pantalla vieja, no un cutover frío.
- [ ] Cada regla extraída de un `trigger` quedó registrada en el backlog del módulo destino — no solo en el commit que la migra.
- [ ] La lista de bibliotecas `.pll` reutilizables está mapeada a módulos del backend nuevo.
- [ ] El plan de retiro del legacy contempla 30–90 días de cold storage.

## Errores comunes

- **Reescribir todo desde cero.** Encarece el proyecto sin mejorarlo. Mantén en la base lo que sigue funcionando.
- **Duplicar fuentes de verdad** con tablas `_NEW` para no tocar las viejas. Genera divergencia inevitable.
- **Traducir PL/SQL línea por línea.** El legacy es un espejo imperfecto de la lógica real; la migración es la ocasión de pulir el espejo, no de copiarlo a otro idioma.
- **Elegir el stack por moda.** La matriz 10X existe para evitar exactamente eso. Firmar la decisión cierra el debate.
- **Hacer cutover sin operación en paralelo.** Cualquier regla no extraída se descubre con un cliente molesto, no con un test.
- **Olvidar las bibliotecas `.pll`.** Migrar pantallas que dependen de `.pll` no migradas genera duplicación silenciosa en el backend.
- **Confundir lógica de UI con lógica de negocio** en los triggers. Los mensajes y colores van a la basura; las validaciones cruzan al backend.
- **Saltarse el inventario** porque "ya sabemos qué tiene el sistema". Quien hace la migración rara vez sabe; quien sabía ya no está.

## Glosario

**Forms Builder** *(Oracle Forms Builder)* — herramienta de Oracle para diseñar `.fmb`. El `frmf2xml` convierte `.fmb` a XML procesable por agentes y scripts.

**`.fmb` / `.pll` / `.mmb` / `.rdf`** — formulario, biblioteca compartida de PL/SQL, menú y reporte de Oracle Forms/Reports respectivamente.

**`WHEN-VALIDATE-ITEM`** *(trigger de Forms)* — punto de extensión donde se ejecuta PL/SQL al validar un campo. Sitio típico de reglas de negocio enterradas.

**Fuente de verdad** *(Source of truth)* — sistema o tabla que define el valor canónico de un dato. Una migración bien hecha no crea fuentes nuevas; redirige los lectores y escritores a la existente.

**Cutover** *(Cutover)* — momento en que la operación cambia del sistema viejo al nuevo. En este skill, el cutover es el resultado de pantallas migradas en serie, no un evento único.

**Coexistencia** *(Coexistence)* — período en que sistema legacy y sistema nuevo conviven sobre la misma base de datos. Su duración la fija la velocidad de migración y la tolerancia del negocio.

**Matriz 10X** — tabla de cuatro preguntas (datos, equipo, escalabilidad, criticidad) publicada por 10X que disciplina la decisión entre APEX, .NET y Java.

:::info Referencias primarias

- [10X · Alternativas para migrar de Oracle Forms](https://www.10x.gt/blog/transformacion-digital/alternativas-para-migrar-de-oracle-forms/) — fuente de la matriz de decisión.
- [1.4.3 Migración desde Oracle Forms](../../../../docs/desarrollo-web-y-movil/modernizacion-legacy/03-migracion-desde-oracle-forms.md) — módulo del bootcamp del que este skill es la versión operativa.
- [1.4.1 El costo oculto del legacy](../../../../docs/desarrollo-web-y-movil/modernizacion-legacy/01-costo-oculto-del-legacy.md) — contexto sobre cuándo modernizar.
- [1.4.2 Migración progresiva](../../../../docs/desarrollo-web-y-movil/modernizacion-legacy/02-migracion-progresiva.md) — estrategias generales que este skill aplica al caso Forms.
- [Oracle · Forms-to-APEX migration](https://www.oracle.com/tools/apex-forms-modernization.html) — referencia oficial cuando el destino es APEX.
- [Skill `consultas-parametrizadas-plsql`](./consultas-parametrizadas-plsql.skill.md) — complementa este skill cuando los triggers usan SQL dinámico con concatenación.
- [Paquete `project/forms-migration/`](../../../project/forms-migration/README.md) — plantillas, scripts y slash command `/migrar-forms` que aterrizan este skill en un proyecto real.

:::
