# Fase 2 — Construcción

**Pregunta que responde:** ¿cómo se construye lo aprobado?
**Artefacto que produce:** código, migraciones, CHANGELOG con todos los items de `releases/vX.Y.Z.md` marcados `[x]`.
**Gate de salida:** confirmación humana de que el release está implementado y verificado en local o staging.

## Cuándo aplica esta fase

- Existe un `releases/vX.Y.Z.md` aprobado en Concepción.
- Hay items en estado `[ ] Pendiente` por implementar.
- El humano dice *"Usando AIDLC 10X, vamos a implementar vX.Y.Z"* o *"continúa con el req X.Y"*.

Si todos los items ya están en `[x]`, **no estás en esta fase** — saltá a [Operación](./operacion.md).

## Pasos

### Paso 1: Lectura de contexto del repo

Antes de tocar código, el agente lee:

1. El `releases/vX.Y.Z.md` aprobado, completo. No solo el item que va a implementar.
2. El `CLAUDE.md` (o `AGENTS.md`) del repo afectado. Si el item toca dos repos, lee los dos.
3. La sección `Notas` y `Precondiciones` del release — ahí pueden esconderse dependencias entre items.

Si algo de lo que dice el `CLAUDE.md` contradice el release, **eso es un bug del release**, no del CLAUDE. Para resolverlo: regresar a Concepción brevemente, ajustar el release, re-aprobarlo, volver. No se "negocia" en construcción.

### Paso 2: Implementación requerimiento por requerimiento

El agente trabaja **un item a la vez**, en el orden del release (a menos que el release declare otro orden en `Notas`).

Por cada item:

1. Releé la descripción del item completa.
2. Si necesita un dato que no está en el archivo, **pregunta al humano** — no asume. La respuesta se escribe en el archivo, en la sección del item, antes de codear.
3. Implementa siguiendo el flujo arquitectónico del `CLAUDE.md` del repo. Ese archivo declara las capas (ej. `Controller → Service → DataContext → Model` en una API .NET, hooks y componentes compartidos en un frontend React) y las reglas que aplican (sin `console.log` en código entregado, reutilización de helpers existentes, etc.). Si una decisión no está cubierta ahí, **se pregunta**, no se inventa.
4. Sin scope creep. Si el agente ve algo que vale la pena arreglar pero no está en el item, lo agrega al `BACKLOG.md` con descripción y archivos involucrados, y sigue.
5. Marca el item `[x] Implementado` en el `releases/vX.Y.Z.md` cuando el código está y compila.

### Paso 3: Migraciones BD

Si el item declara migración BD, el agente:

1. Crea (o completa) el script en `Database/migrations/vX.Y.Z/NNN-nombre.sql` con el nombre acordado en Concepción.
2. Escribe el script de tal forma que sea **idempotente cuando sea posible** (`CREATE TABLE IF NOT EXISTS`, `ALTER TABLE ... ADD COLUMN IF NOT EXISTS` en motores que lo soporten).
3. Si la migración requiere backfill o pasos en orden (alter → backfill → not-null), los separa en scripts numerados consecutivos, no los mete en uno solo.
4. Documenta en el header del SQL: versión, item del release que lo origina, comentarios sobre rollback si no es trivial.
5. Prueba en local contra una BD de desarrollo antes de marcar el item como implementado.

Reglas:

- Las migraciones nunca se ejecutan en `Program.cs` ni en código de inicio. Son scripts manuales que corre el operador en el deploy.
- No se borran columnas en una versión sin antes deprecarlas en una previa. Si la deprecación no se hizo, el item se baja a una versión futura, no se ejecuta a la fuerza.

### Paso 4: CHANGELOG por repo

Cada repo afectado mantiene su propio `CHANGELOG.md` (formato [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/)). Por cada item del release que tocó al repo, el agente agrega una línea bajo la sección `[Unreleased]`:

```
## [Unreleased]

### Added
- Toggle "Mostrar columna de impuesto" en preferencias del PDF de cotización (#1.1)

### Changed
- Renombrado `lib/preferenciasSello.ts` → `lib/preferenciasPdfCotizacion.ts` (#1.1)
```

El CHANGELOG es para humanos, no para el release.md. Es la forma corta y orientada al usuario; el release.md es el contrato técnico.

### Paso 5: Verificación local

Antes del gate de salida, el agente prepara la verificación:

- Build limpio en cada repo afectado (`dotnet build`, `pnpm run build`).
- Tests si existen (`dotnet test`, `pnpm test`).
- Lint y type-check del frontend.
- Lista de criterios de aceptación de cada item, copiada del `releases/vX.Y.Z.md`, para que el humano verifique manualmente.

El agente reporta en el chat:

```
Construcción de vX.Y.Z lista para verificación.

Items implementados (N/N):
  [x] 1.1 ...
  [x] 1.2 ...

Build:
  {{repo-api}}     → OK
  {{repo-frontend}}→ OK (con N warnings preexistentes documentados)

Tests:
  {{repo-api}}     → OK (M tests)
  {{repo-frontend}}→ {{resultado o "sin suite"}}

Migraciones BD probadas en local:
  Database/migrations/vX.Y.Z/001-...sql  → OK
  Database/migrations/vX.Y.Z/002-...sql  → OK

Criterios de aceptación a verificar manualmente:
  - 1.1: con el toggle apagado, el PDF no muestra columna IMPUESTOS ni líneas Base/Impuestos.
  - 1.2: ...
```

### Paso 6: Gate de aprobación

El agente cierra con:

```
¿Confirmas que vX.Y.Z está completo y verificado para pasar a Operación,
o hay items que ajustar?
```

No avanza a Operación sin esa confirmación. Si hay ajustes, vuelve al paso 2 con el item específico.

## Salida esperada de esta fase

- Todos los items de `releases/vX.Y.Z.md` en `[x]`.
- Branch `dev` con los commits del release, sin `main` tocado todavía.
- Migraciones BD en `Database/migrations/vX.Y.Z/` probadas en local.
- CHANGELOG actualizado en cada repo afectado, sección `[Unreleased]`.
- Build y tests verdes en cada repo.

## Errores comunes

- **Implementar un item desviando del CLAUDE.md** porque "es más simple así". Documentado, pasa; no documentado, es deuda inmediata.
- **Marcar `[x]` antes de probar.** El estado refleja lo que está hecho, no lo que está empezado.
- **Mezclar refactors no pedidos.** Si aparece la tentación de refactorizar archivos vecinos, va al `BACKLOG.md`. No se mete al PR del release.
- **Migraciones BD ejecutadas en código de inicio.** Acoplan deploy con primer arranque y rompen la separación schema/runtime.
- **CHANGELOG escrito al final, todo de un tirón.** Pierde fidelidad. Se actualiza por item, mientras se implementa.
- **`console.log` o código de depuración en lo entregado.** Antes del gate, el agente busca y limpia (incluido `Console.WriteLine`, `dump()`, `print()` según stack).

## Bloque estructurado para agentes

```yaml
fase: construccion
objetivo: implementar todos los items de releases/vX.Y.Z.md
precondicion: releases/vX.Y.Z.md aprobado en concepcion
entradas:
  - releases/vX.Y.Z.md aprobado
  - CLAUDE.md de cada repo afectado
pasos:
  - leer contexto completo del release y CLAUDE.md
  - implementar item por item, en orden
  - escribir migraciones BD numeradas, probarlas en local
  - actualizar CHANGELOG por repo
  - build, lint, type-check, tests
  - reporte de verificacion + gate
salidas:
  - todos los items en [x]
  - migraciones BD en Database/migrations/vX.Y.Z/
  - CHANGELOG.md actualizado por repo
  - branch dev listo
gate_de_salida:
  - confirmacion humana de verificacion en local o staging
reglas:
  - sin scope creep (lo extra va a BACKLOG.md)
  - sin defaults ocultos (preguntar y registrar)
  - sin console.log ni codigo de depuracion entregado
  - migraciones nunca en codigo de inicio
errores_comunes:
  - desviar del CLAUDE.md sin documentar
  - marcar [x] sin probar
  - refactors no pedidos
  - CHANGELOG retrospectivo
siguiente_fase: operacion.md
```
