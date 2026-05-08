# core-workflow.md — Reglas maestras AIDLC 10X

Este documento es la entrada única para el agente. Todo lo demás se carga desde aquí.

## Frase de activación

El usuario inicia el ciclo con:

> *Usando AIDLC 10X, ...*

Cuando el agente reconoce esa frase, ejecuta el flujo de abajo. Si nunca aparece, opera normal — AIDLC 10X **no se aplica por default**, se invoca.

## Flujo de decisión

```
Usuario: "Usando AIDLC 10X, ..."
   │
   ▼
1. Lee `principios.md` (no negociable, lo aplica todo el tiempo)
   │
   ▼
2. Clasifica el pedido en una fase:
   ├── Idea nueva sin versión asignada       → Fase 1: Concepción
   ├── Versión ya redactada y aprobada       → Fase 2: Construcción
   ├── Implementación cerrada, falta liberar → Fase 3: Operación
   └── No queda claro                        → Pregunta al usuario, no asumas
   │
   ▼
3. Carga la guía de la fase identificada (concepcion.md / construccion.md / operacion.md)
   │
   ▼
4. Avanza paso a paso, una pregunta a la vez
   │
   ▼
5. Antes de cruzar un gate de aprobación, se detiene y pide confirmación humana
```

## Las tres fases

### Fase 1 — Concepción (¿qué y por qué?)

Decidir **qué entra a una versión** y **con qué nivel de detalle** para que la implementación sea ejecutable sin más interpretaciones.

- Entrada: idea suelta, ticket, conversación, item del `BACKLOG.md`.
- Trabajo: triage, asignación de versión, redacción del `releases/vX.Y.Z.md` desde la plantilla, identificación de migraciones BD y repos afectados.
- Salida: archivo `releases/vX.Y.Z.md` con todos los requerimientos cerrados y descripción ejecutable.
- **Gate humano:** *"Aprueba este release.md como contrato de implementación."* Sin esa aprobación, no se pasa a Construcción.

Detalle: [`concepcion.md`](./concepcion.md).

### Fase 2 — Construcción (¿cómo?)

Implementar **lo que dice el `releases/vX.Y.Z.md`**, en el orden y con las restricciones acordadas. Sin scope creep.

- Entrada: `releases/vX.Y.Z.md` aprobado.
- Trabajo: leer `CLAUDE.md` del repo afectado, implementar requerimiento por requerimiento, marcar `[x]` conforme se completan, escribir migraciones BD numeradas, actualizar CHANGELOG.
- Salida: commits en `dev`, migraciones en `Database/migrations/vX.Y.Z/`, CHANGELOG actualizado, todos los items en `releases/vX.Y.Z.md` marcados `[x]`.
- **Gate humano:** *"Pruébalo en local / staging y confirma que todo lo del release está hecho."* Sin esa confirmación, no se pasa a Operación.

Detalle: [`construccion.md`](./construccion.md).

### Fase 3 — Operación (¿está vivo y sano?)

Empaquetar, liberar y observar. Es la única fase que toca producción.

- Entrada: rama `dev` con todo el release implementado y verificado.
- Trabajo: bump de versión en `.csproj` / `package.json`, merge a `main`, tag SemVer, deploy, verificación post-deploy.
- Salida: tag `vX.Y.Z` empujado, `releases/vX.Y.Z.md` con estado *Publicado* y fecha, reporte si hubo incidente.
- **Gate humano:** cada acción que toca producción (deploy, push de tag) requiere autorización explícita. El agente prepara, el humano ejecuta o autoriza.

Detalle: [`operacion.md`](./operacion.md).

## Reglas que valen para las tres fases

1. **Una pregunta a la vez.** Si necesitas tres datos, haz tres preguntas en tres turnos, no un cuestionario.
2. **No defaults ocultos.** Si una decisión no está en el `releases/vX.Y.Z.md` ni en el `CLAUDE.md`, pregúntale al humano. No la inventes.
3. **No cruzar gates sin confirmación.** Los gates están marcados explícitamente en cada fase. El agente puede preparar todo lo que sigue, pero no ejecuta hasta tener "ok".
4. **No saltarse fases.** No se construye antes de tener un release aprobado; no se libera antes de tener construcción confirmada. Si el humano insiste, el agente lo señala y registra la excepción en `Notas` del release.
5. **La documentación es el contrato.** `releases/vX.Y.Z.md`, `CLAUDE.md` y `BACKLOG.md` son fuente única. Si la conversación contradice al documento, se actualiza el documento — no se ejecuta sobre la conversación.
6. **Trazabilidad por versión.** Todo cambio funcional pertenece a una versión. Si no pertenece a ninguna, va al `BACKLOG.md` hasta que se le asigne una.

## Errores típicos del agente que este ciclo previene

- *"Mientras estaba ahí, también arreglé X"* → scope creep, X no estaba en el release.
- *"Asumí que querías Y porque es lo común"* → default oculto.
- *"Lo subí a producción"* → cruce de gate sin autorización.
- *"Hice la migración inline en el `Program.cs` para que sea más simple"* → contradice `CLAUDE.md` y `releases/vX.Y.Z.md`.

Si el agente comete uno de estos, el humano lo señala y el agente revierte. La memoria de la sesión registra el patrón para no repetirlo.

## Lectura recomendada antes de empezar

1. [`principios.md`](./principios.md) — los tenets que sostienen el ciclo.
2. La fase que toca según el pedido del usuario.
3. Las [plantillas](./plantillas) si vas a crear archivos nuevos.
