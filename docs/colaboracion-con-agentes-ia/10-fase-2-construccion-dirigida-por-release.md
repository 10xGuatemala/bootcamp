---
sidebar_position: 10
sidebar_label: 6.10 Fase 2 · Construcción dirigida por release.md
---

# Fase 2: Construcción dirigida por release.md

Concepción cerró con un `releases/vX.Y.Z.md` aprobado. La fase de Construcción es la que muchos esperan que sea "ahora le digo al agente que codee y listo". No lo es: es la fase donde más fácil se cuela el scope creep, donde un *"ya que estoy aquí"* convierte un release de tres items en un PR de quince archivos no relacionados. La disciplina que protege esta fase es simple — el contrato manda — pero hay que practicarla.

Esta fase produce código, migraciones y CHANGELOG, todos trazables al `releases/vX.Y.Z.md`. Termina cuando todos los items están en `[x]` y el humano confirma la verificación.

## ¿Qué es la Construcción?

La fase del ciclo donde el agente **ejecuta el contrato**. No decide qué se hace — eso ya se decidió. Implementa los items del `releases/vX.Y.Z.md` siguiendo el flujo arquitectónico del `CLAUDE.md` de cada repo, escribe las migraciones BD nombradas en Concepción, actualiza CHANGELOG por repo, y cierra con verificación local lista para el siguiente gate.

Si en algún momento de Construcción surge una decisión que no está en el contrato, **no se inventa**. Se pregunta al humano y se actualiza el contrato — o se sale a Concepción brevemente, se ajusta, se re-aprueba, y se vuelve.

## Por qué la Construcción importa (y por qué se rompe)

- **Es la fase con mayor velocidad de error.** El agente puede tocar veinte archivos en una sesión; sin disciplina, esa velocidad se convierte en deuda. La regla operativa: cada toque tiene origen en un item del `release.md`.
- **El scope creep no se ve venir.** El agente *quiere ayudar*. Si nota código duplicado, refactoriza; si ve un `console.log`, lo borra. Cada uno de esos toques, individualmente, parece bueno. En conjunto, generan un PR irrevisable.
- **Una migración BD mal escrita es un incidente en Operación.** La calidad del SQL escrito acá se cobra después con downtime. La verificación local no es opcional.
- **El CHANGELOG retrospectivo pierde fidelidad.** Escribirlo al final, "todo de un tirón", produce entradas vagas. Se actualiza por item, mientras se implementa.
- **Sin verificación, el `[x]` miente.** Marcar un item como hecho sin probarlo es la forma más rápida de empujar a Operación una versión rota.

## Objetivo

Implementar todos los items del `releases/vX.Y.Z.md` aprobado, sin scope creep, con migraciones BD probadas localmente, CHANGELOG actualizado por repo, y verificación que abre el gate hacia Operación.

## Entradas

- `releases/vX.Y.Z.md` aprobado en Concepción.
- `CLAUDE.md` o `AGENTS.md` de cada repo afectado por la versión.
- Branch `dev` actualizado.
- BD local de desarrollo para probar migraciones.

## Pasos para conducir la Construcción

### Paso 1: Leer el contexto completo, no solo el item del momento

Antes de tocar código, el agente lee:

1. El `releases/vX.Y.Z.md` aprobado, completo. No solo el item que va a implementar.
2. El `CLAUDE.md` (o `AGENTS.md`) del repo afectado. Si el item toca dos repos, los dos.
3. Las secciones `Notas` y `Precondiciones` del release — ahí pueden esconderse dependencias entre items.

- Mal: *"Voy directo al item 1.2 que es el que más urge."* Si 1.1 cambia el nombre de un módulo y 1.2 lo usa, el agente va a editar el viejo.
- Bien: *"Leo el release entero, mapeo dependencias entre items, decido el orden con el humano si no está explícito en `Notas`."*

**Valor para el agente:** un orden de implementación derivado del contrato es reproducible. Un orden derivado de la urgencia del momento es opaco.

### Paso 2: Implementar un item a la vez, siguiendo el flujo arquitectónico

Por cada item del release, en el orden acordado:

1. Releer la descripción completa.
2. Si necesita un dato que no está en el archivo, **preguntar al humano**. La respuesta se escribe en el archivo, en la sección del item, antes de codear.
3. Implementar siguiendo el flujo del `CLAUDE.md` del repo. El `CLAUDE.md` declara las capas (ej. `Controller → Service → DataContext → Model` en una API .NET; hooks compartidos y componentes shared en un frontend React) y las reglas operativas (sin `console.log` en código entregado, reutilizar helpers existentes, etc.).
4. **Sin scope creep.** Si el agente ve algo que vale la pena arreglar pero no está en el item, lo agrega al `BACKLOG.md` con descripción y archivos involucrados, y sigue.
5. Marca el item `[x]` solo cuando el código está, compila y se probó al menos en build.

- Mal: *"Aproveché y refactoreé el módulo vecino que estaba duplicado."* Tres archivos extra en el PR, ninguno relacionado con el release.
- Bien: *"Vi duplicación en `helpers/fechas.ts`. Lo agrego al `BACKLOG.md` con detalle. Sigo con el item 1.2."*

### Paso 3: Migraciones BD numeradas e idempotentes

Si el item declara migración BD, el agente:

1. Crea (o completa) el script en `Database/migrations/vX.Y.Z/NNN-nombre.sql` con el nombre acordado en Concepción.
2. Escribe el script idempotente cuando el motor lo permita (`ADD COLUMN IF NOT EXISTS` en MySQL 8+, `CREATE TABLE IF NOT EXISTS` siempre).
3. Si la migración requiere pasos en orden (alter → backfill → not-null), los separa en scripts numerados consecutivos. **No los mete en uno solo.**
4. Documenta en el header del SQL: versión, item del release que lo origina, comentarios sobre rollback si no es trivial.
5. Prueba en local contra una BD de desarrollo antes de marcar el item como implementado.

Reglas que valen para todas las migraciones:

- **Las migraciones no se ejecutan en código de inicio.** Nada de `DbContext.Database.Migrate()` al arrancar la app, nada de `init-scripts/` que corren al levantar. Son scripts manuales que corre el operador en el deploy. Esa separación es la que permite hacer rollback selectivo.
- **No se borran columnas en una versión** sin antes deprecarlas en una previa. Si la deprecación no se hizo, el item se baja a una versión futura, no se ejecuta a la fuerza.

### Paso 4: CHANGELOG por repo, mientras se implementa

Cada repo afectado mantiene su propio `CHANGELOG.md` en formato [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/). Por cada item del release que tocó al repo, el agente agrega una línea bajo la sección `[Unreleased]`:

```text
## [Unreleased]

### Added
- Toggle "Mostrar columna de impuesto" en preferencias del PDF (#1.1)

### Changed
- Renombrado `lib/preferenciasSello.ts` → `lib/preferenciasPdfCotizacion.ts` (#1.1)
```

El CHANGELOG es para humanos: la versión corta orientada al usuario. El `releases/vX.Y.Z.md` es el contrato técnico. Los dos coexisten — no se reemplazan.

- Mal: *"El CHANGELOG lo escribo al final con todo de un tirón."* Pierde fidelidad: olvida el `Changed`, mete cosas en `Added` que eran fixes.
- Bien: *"Por cada item que cierro, agrego su línea al CHANGELOG. Cuando termino el release, ya está actualizado."*

### Paso 5: Verificación local antes del gate

Antes de cerrar la fase, el agente prepara la verificación:

- Build limpio en cada repo afectado (`dotnet build`, `pnpm run build`).
- Tests si existen (`dotnet test`, `pnpm test`).
- Lint y type-check del frontend.
- Lista de criterios de aceptación de cada item, copiada del `releases/vX.Y.Z.md`, para que el humano verifique manualmente.

El agente reporta en el chat:

```text
Construcción de v1.16.0 lista para verificación.

Items implementados (3/3):
  [x] 1.1 Toggle "Mostrar columna de impuesto"
  [x] 1.2 Renombrado del módulo de preferencias
  [x] 2.1 Bump de versión por trazabilidad

Build:
  api      → OK
  frontend → OK (con N warnings preexistentes)

Migraciones BD probadas en local:
  Database/migrations/v1.16.0/001-...sql → OK
  Database/migrations/v1.16.0/002-...sql → OK

Criterios de aceptación a verificar manualmente:
  - 1.1: con el toggle apagado, el PDF no muestra columna de impuesto.
  - 1.2: las preferencias previas en localStorage siguen funcionando.
```

### Paso 6: Gate de salida

El agente cierra con:

```text
¿Confirmas que v1.16.0 está completo y verificado para pasar a Operación,
o hay items que ajustar?
```

No avanza a Operación sin esa confirmación. Si hay ajustes, vuelve al paso 2 con el item específico — no rehace todo.

## Salidas

- Todos los items de `releases/vX.Y.Z.md` en `[x]`.
- Branch `dev` con los commits del release; `main` sin tocar todavía.
- Migraciones BD en `Database/migrations/vX.Y.Z/` probadas en local.
- CHANGELOG actualizado en cada repo afectado, sección `[Unreleased]`.
- Build y tests verdes en cada repo.

## Errores comunes

- **Implementar un item desviando del `CLAUDE.md`** porque "es más simple así". Si la desviación está documentada y aceptada, pasa; si no, es deuda inmediata.
- **Marcar `[x]` antes de probar.** El estado refleja lo que está hecho, no lo que está empezado.
- **Refactors no pedidos.** Si aparece la tentación de refactorizar archivos vecinos, va al `BACKLOG.md`. No se mete al PR del release.
- **Migraciones BD ejecutadas en código de inicio.** Acoplan deploy con primer arranque y rompen la separación schema/runtime; además impiden rollback granular.
- **CHANGELOG escrito al final, todo de un tirón.** Pierde fidelidad; aparece en una sola sección y mezcla `Added` con `Changed` con `Fixed`.
- **`console.log` o código de depuración en lo entregado.** Antes del gate, el agente busca y limpia (incluido `Console.WriteLine`, `dump()`, `print()` según stack).
- **Saltarse la verificación local.** "Compila, listo." Compilar no prueba la funcionalidad; los criterios de aceptación sí.

## Prompt de auditoría

Antes de aprobar el gate de salida, el humano puede pedirle al agente:

```text
Compara el branch dev contra main. Por cada archivo modificado,
dime a qué item del releases/vX.Y.Z.md pertenece. Si encuentras
un archivo modificado que no se puede mapear a ningún item,
señálalo como "potencial scope creep" y pregúntame qué hacer.
No infieras la conexión: si no está explícita en el release,
es scope creep.
```

Ese prompt detecta el `helpers/fechas.ts` que se coló sin item.

:::tip Marcá `[x]` con disciplina
El `[x]` no es decoración. Es la única forma de saber, sin abrir el código, qué hace falta. Si marcás antes de probar, el release entra a Operación con humo. Marcá solo cuando el código está, compila, y los criterios de aceptación son verificables — aunque la verificación final la haga el humano en el siguiente gate.
:::

## Puente al siguiente módulo

Con la Construcción cerrada y el gate aprobado, el branch `dev` está listo para liberarse. La fase final es la única que toca producción y la que más gates tiene por acción individual. El módulo [6.11 Fase 3: Operación con humano en el bucle](./11-fase-3-operacion-con-humano-en-el-bucle.md) cubre el bump de versión, el backup BD, el deploy, la verificación post-deploy y el modo incidente cuando algo sale mal.

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** implementar todos los items de `releases/vX.Y.Z.md` aprobado, sin scope creep, listos para liberarse.

**Precondición:** `releases/vX.Y.Z.md` aprobado en Concepción.

**Entradas:**
- `releases/vX.Y.Z.md` aprobado.
- `CLAUDE.md` de cada repo afectado.
- Branch `dev`.
- BD local para probar migraciones.

**Pasos:**
1. Leer release completo y `CLAUDE.md` de cada repo.
2. Implementar item por item, siguiendo el flujo arquitectónico.
3. Escribir migraciones BD numeradas, idempotentes, probadas en local.
4. Actualizar CHANGELOG por repo, mientras se implementa.
5. Build, lint, type-check, tests.
6. Reporte de verificación + gate de salida.

**Salidas:**
- Todos los items en `[x]`.
- Migraciones en `Database/migrations/vX.Y.Z/`.
- CHANGELOG por repo con sección `[Unreleased]`.
- Branch `dev` listo.

**Errores comunes:**
- Desviar del `CLAUDE.md` sin documentar.
- Marcar `[x]` sin probar.
- Refactors no pedidos (scope creep).
- Migraciones en código de inicio.
- CHANGELOG retrospectivo.
- `console.log` en lo entregado.

**Referencias cruzadas:**
- [6.9 Fase 1: Concepción del release](./09-fase-1-concepcion-del-release.md)
- [6.11 Fase 3: Operación con humano en el bucle](./11-fase-3-operacion-con-humano-en-el-bucle.md)
- [6.2 Context engineering y CLAUDE.md](./02-context-engineering-claude-md.md)
- [5.2 Versionado semántico en equipos](../documentacion-y-requerimientos/02-versionado-semantico-en-equipos.md)
</div>

---

## Glosario

**Scope creep** *(Scope creep)* — ampliación silenciosa del alcance durante la implementación. En Construcción, son los toques que no se pueden mapear a ningún item del `releases/vX.Y.Z.md`. Se previene moviendo la idea al `BACKLOG.md` y siguiendo con el item del momento.

**Migración idempotente** *(Idempotent migration)* — script SQL escrito de tal forma que ejecutarlo dos veces produce el mismo resultado que ejecutarlo una. Suele lograrse con cláusulas `IF NOT EXISTS` y `IF EXISTS` cuando el motor las soporta.

**CHANGELOG retrospectivo** *(Retrospective CHANGELOG)* — antipatrón de escribir el CHANGELOG al final de la fase, "todo de un tirón". Pierde fidelidad y mezcla categorías. Se evita actualizándolo por item.

**Verificación local** *(Local verification)* — paso final de Construcción que ejecuta build, tests y revisión manual contra los criterios de aceptación, en local o staging. Es la entrada al gate de salida hacia Operación.

**Flujo arquitectónico** *(Architectural flow)* — secuencia de capas declarada en el `CLAUDE.md` del repo (ej. `Controller → Service → DataContext → Model`). La Construcción la respeta; las desviaciones se documentan o se reabre Concepción.

:::info Referencias primarias
- [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/) — formato del CHANGELOG por repo.
- [Anthropic · Claude Code documentation](https://docs.claude.com/en/docs/claude-code) — referencia del agente que ejecuta la Construcción.
- [awslabs/aidlc-workflows · Construction phase](https://github.com/awslabs/aidlc-workflows) — fase de Construction del AIDLC original (Apache 2.0).
:::

---

<AuthorCredit />
