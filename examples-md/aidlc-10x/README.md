<!-- Destino sugerido: copia esta carpeta a `aidlc/` en la raíz del repo del producto -->

# AIDLC 10X — Ciclo de Vida de Desarrollo con IA

Versión 10X del [AI-DLC](https://github.com/awslabs/aidlc-workflows) de AWS Labs, adaptada al flujo real con el que se construyen productos en 10X: documentación de release como contrato, migraciones BD versionadas, `CLAUDE.md` por repo, tag SemVer como gatillo de deploy.

Es **markdown puro**: no requiere skills, slash commands ni un IDE específico. El agente lo carga como contexto y lo sigue.

## Tres fases

| Fase | Pregunta | Artefacto principal |
|------|----------|---------------------|
| **[Concepción](./concepcion.md)** | ¿Qué construir y por qué? | `releases/vX.Y.Z.md` aprobado |
| **[Construcción](./construccion.md)** | ¿Cómo? | Código, migraciones, CHANGELOG |
| **[Operación](./operacion.md)** | ¿Está vivo y sano? | Tag, deploy, reportes |

Cada fase tiene gates de aprobación humana explícitos. El agente nunca cruza un gate sin confirmación.

## Frase de activación

Para iniciar, se le dice al agente:

> *Usando AIDLC 10X, vamos a trabajar en {{vX.Y.Z | una idea nueva | un hotfix}}.*

El agente entonces:

1. Lee [`core-workflow.md`](./core-workflow.md) y [`principios.md`](./principios.md).
2. Identifica en qué fase está el trabajo (Concepción / Construcción / Operación).
3. Carga la guía de esa fase y empieza a hacer **una pregunta a la vez** hasta llegar al gate.

## Cómo instalarlo en un proyecto

1. Copia esta carpeta completa al proyecto: `cp -r aidlc-10x/ ../mi-proyecto/aidlc/`.
2. En el `CLAUDE.md` (o `AGENTS.md`) del repo, agrega:

   ```markdown
   ## Ciclo de vida

   Este proyecto sigue **AIDLC 10X**. Antes de cualquier cambio funcional:

   1. Lee `aidlc/core-workflow.md` y `aidlc/principios.md`.
   2. Identifica la fase del trabajo solicitado.
   3. Sigue la guía de esa fase. Pregunta una vez a la vez. No cruces gates sin confirmación humana.
   ```

3. Crea las carpetas que el ciclo espera: `releases/`, `BACKLOG.md`, `RELEASING.md` (usa las [plantillas](./plantillas)).
4. Verifica que cada repo del producto tenga un `CLAUDE.md` con su contexto arquitectónico.

## Qué NO es este paquete

- **No es un sustituto de un product backlog** — convive con Jira, Linear, GitLab Issues. Lo que vive aquí es el contrato de release, no la pila de ideas.
- **No es un framework de proyecto** — no impone stack, no genera scaffolding. Para arranque desde cero, primero pasa por [6.7 De specs a proyecto real](https://bootcamp.10x.gt/docs/colaboracion-con-agentes-ia/07-de-specs-a-proyecto-real), después adopta este ciclo.
- **No es CI/CD** — describe el flujo humano + agente. La automatización (pipelines, deploy) se conecta al final, en la fase de Operación.

## Cómo se conecta con el resto del bootcamp

AIDLC 10X es la capa de **orquestación** del flujo. Las piezas individuales ya están cubiertas en otras rutas del bootcamp:

- **Antes del ciclo (arranque):** [6.7 De specs a proyecto real](https://bootcamp.10x.gt/docs/colaboracion-con-agentes-ia/07-de-specs-a-proyecto-real) — entrevista, contrato `specs.md` y scaffolding reproducible.
- **Contexto del agente:** [6.2 Context engineering y CLAUDE.md](https://bootcamp.10x.gt/docs/colaboracion-con-agentes-ia/02-context-engineering-claude-md) — el `CLAUDE.md` por repo que cada fase asume.
- **Skills reutilizables:** [6.3 Arquitectura orientada a skills](https://bootcamp.10x.gt/docs/colaboracion-con-agentes-ia/03-arquitectura-orientada-a-skills) — cómo encapsular tareas recurrentes que las fases invocan.
- **Versionado y trazabilidad:** la ruta [Documentación y Requerimientos](https://bootcamp.10x.gt/docs/documentacion-y-requerimientos) — SemVer en equipos, CHANGELOG, trazabilidad requerimiento → release.
- **Plantillas individuales** (subset de las que orquesta este paquete): `examples-md/project/specs.md`, `examples-md/project/release-requirements.md`, `examples-md/agents/CLAUDE.md`.

Este paquete no reemplaza a esas piezas — las **secuencia** en un ciclo con gates humanos. Si solo necesitas una plantilla suelta, usa la de `examples-md/project/`. Si necesitas el flujo completo Concepción → Construcción → Operación con disciplina de gates, usa este paquete.

## Origen

Este paquete destila el flujo de release usado por equipos de producto de 10X de Guatemala, donde la disciplina de un archivo `releases/vX.Y.Z.md` por versión, migraciones BD numeradas y `CLAUDE.md` por repo se ha probado a lo largo de decenas de releases en producción. El [`_release-template.md`](./plantillas/_release-template.md) de este paquete es una destilación directa de esa práctica.

## Licencia

Este paquete es de 10X de Guatemala, S.A. y se publica con la misma licencia del bootcamp. Adaptación de [awslabs/aidlc-workflows](https://github.com/awslabs/aidlc-workflows) (Apache 2.0) — los términos del original aplican a la metodología subyacente.
