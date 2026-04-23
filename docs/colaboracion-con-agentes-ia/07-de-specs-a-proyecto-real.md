---
sidebar_position: 7
title: De specs a proyecto real
sidebar_label: 6.7 De specs a proyecto real
---

# De specs a proyecto real

Cuando un equipo arranca un proyecto nuevo con un agente de IA, la tentación es abrir una carpeta vacía y pedir *"créame un proyecto con React, Tailwind y Zustand"*. El agente responde, genera código, y a los tres días aparecen preguntas que nunca se decidieron: *¿por qué Zustand y no Context? ¿por qué kebab-case en unos archivos y PascalCase en otros? ¿de dónde salió ese ESLint?*

Este módulo describe un flujo alternativo, en cuatro pasos, que separa **decidir** de **ejecutar**: primero una entrevista de especificación, luego un contrato firmado (`specs.md`), y solo después el scaffolding.

## ¿Qué es la Actividad 0?

Un ritual de arranque en cuatro pasos que produce dos artefactos: un **contrato** (`specs.md`) con todas las decisiones cerradas, y un **scaffolding** reproducible construido a partir de ese contrato. El agente no adivina; registra decisiones del humano y las ejecuta.

## Por qué la Actividad 0 importa

- **Los defaults ocultos son deuda desde el primer commit.** Si el agente eligió Vitest porque "es lo habitual", nadie lo decidió; nadie puede defenderlo cuando el equipo crezca y alguien pregunte por qué.
- **El scaffolding es la primera oportunidad de alinear al equipo.** Si se hace al tanteo, cada miembro arrancará con un modelo mental distinto del proyecto.
- **Una entrevista guiada lleva 20 minutos; rehacer el arranque lleva una semana.** El tradeoff es evidente cuando ya pasaste por el segundo escenario.
- **El agente trabaja mejor con un contrato que con intuiciones.** Un `specs.md` en la raíz es contexto que el agente lee en cada sesión futura.

## Objetivo

Llegar al primer commit con un `specs.md` aprobado, un árbol de carpetas que respeta el contrato, y dependencias instaladas en sus versiones acordadas — sin decisiones implícitas.

## Entradas

- Una intención de proyecto en una frase (*"un dashboard para que operaciones vea las alarmas de la planta"*).
- Restricciones duras conocidas (stack obligado por el cliente, fecha, presupuesto).
- Una carpeta vacía o casi vacía.
- Un agente de IA capaz de leer/escribir archivos y ejecutar comandos (Claude Code, Cursor, Codex u otro).

## Pasos para arrancar un proyecto desde specs

### Paso 1: Lanzar la entrevista de especificación

Abres la carpeta del proyecto con tu agente y le das un prompt fundacional: *"Vamos a definir las specs de un proyecto. Haz preguntas una por una, sin asumir defaults, hasta que tengamos todas las decisiones cerradas."*

- Mal: *"Hazme un proyecto React con Tailwind y Zustand, estructura Atomic Design."* El agente parte de tu frase y rellena los 30 huecos que faltan con sus propios defaults.
- Bien: *"Conduce una entrevista de especificación. Una pregunta a la vez. Cuando tengamos todo, genera `specs.md`."*

**Valor para el agente:** la entrevista convierte una intención difusa en un set de decisiones verificables. El agente deja de adivinar y empieza a registrar.

### Paso 2: Responder y decidir

El agente pregunta por **cinco bloques en orden** — no es negociable, cada uno depende del anterior:

1. **Objetivo** — problema que resuelve el proyecto y usuario final.
2. **Stack** — runtime, framework, persistencia, autenticación, package manager, todo con versión fijada.
3. **Estructura y convenciones** — organización de carpetas, naming por tipo de archivo, tests, lint.
4. **Operación** — scripts, CI/CD desde día uno, licencia.
5. **Alcance** — lista explícita de qué queda fuera de la v0.

Saltar al stack sin cerrar el objetivo produce decisiones huérfanas; cerrar el stack sin estructura produce convenciones divergentes al segundo archivo. La regla cardinal de la entrevista es **una pregunta a la vez**: el agente pregunta, tú respondes, el agente confirma y avanza. Cada respuesta es una línea del contrato. Si dudas, el agente ofrece dos alternativas con tradeoff — nunca impone.

- Mal: *"Tú decide, lo que sea más común."* Defaults ocultos por la puerta de atrás.
- Mal: veinte preguntas en un solo mensaje que producen veinte respuestas superficiales.
- Bien: *"No sé qué testing framework usar. ¿Cuáles son las dos opciones razonables y qué ganamos con cada una?"* El agente expone; tú decides; el contrato registra.

**Valor para el agente:** una respuesta ambigua lo obliga a repreguntar en vez de interpretar. Ambigüedad en el arranque se amplifica en cada sesión siguiente.

### Paso 3: Revisar y aprobar `specs.md`

Cuando todas las secciones están cerradas, el agente genera `specs.md`. Léelo línea por línea. Si una sección dice *"por decidir"*, el proyecto no está listo para el paso 4.

- Mal: *"Se ve bien, procede."* Sin revisión, los errores del paso 2 se cristalizan.
- Bien: *"En §3 dice que uso alias `@/`, pero §4 muestra imports relativos. Aclaremos."* Validar contradicciones antes del scaffolding cuesta cero; después cuesta una refactorización.

**Valor para el agente:** el documento aprobado es el insumo único del paso 4. Si algo no está ahí, el agente no lo ejecuta.

### Paso 4: Dejar que la IA ejecute el scaffolding

Con `specs.md` aprobado, el agente corre los comandos: `git init`, inicializa el manifest, instala dependencias en las versiones fijadas, crea la estructura de carpetas (`atoms/`, `molecules/`, `organisms/`, `store/` u otra definida en el contrato), registra los scripts acordados y hace el commit inicial.

- Mal: *"Ya que estás, agrega un ESLint y un Prettier con reglas típicas."* Scope creep en el primer minuto del proyecto — decisiones nuevas que nadie registró.
- Bien: *"Solo lo que está en specs. Si detectas que falta algo obvio, pregúntame; no lo añadas por tu cuenta."*

**Valor para el agente:** el contrato restringe el espacio de acción. Un scaffolding reproducible es el que cualquier miembro del equipo puede regenerar leyendo `specs.md` — sin que cada ejecución traiga sorpresas.

## Salidas

- `specs.md` en la raíz del repo, con frontmatter de versión y fecha de aprobación.
- Árbol de carpetas exactamente como el contrato lo declara.
- Manifest del gestor de paquetes (`package.json` / `.csproj` / `pyproject.toml`) con dependencias en versiones fijadas.
- Un único commit inicial: `chore: initial scaffolding from specs.md v0.1.0`.
- Reporte del agente: comandos ejecutados, versiones efectivamente instaladas, divergencias (si las hubo) con lo acordado.

## Errores comunes

- **Saltarse el paso 2 con un cuestionario exprés.** Veinte preguntas en una sola respuesta producen veinte respuestas superficiales. Una a la vez obliga a pensar.
- **Dejar secciones como "por decidir"** y aun así avanzar al scaffolding. El contrato queda incompleto y el equipo descubre las decisiones en un PR tres semanas después.
- **Aceptar `latest` en las dependencias.** La reproducibilidad que `specs.md` busca garantizar se pierde. Fija versiones exactas o rangos con justificación.
- **Permitir que el agente agregue archivos "de cortesía"** (`.nvmrc`, `.editorconfig`, README largo, ejemplos de componentes) que no estaban en specs. Si el equipo los quería, debieron aparecer en la entrevista.
- **Hacer `git push` al final del paso 4.** Publicar es decisión humana. El scaffolding termina en la carpeta local.
- **No versionar `specs.md`.** Sin `v0.1.0 (YYYY-MM-DD)` y sin historial, un cambio futuro al contrato queda sin trazabilidad.

## Prompt de auditoría

Cuando heredes un proyecto que no siguió la Actividad 0 y quieras reconstruir su contrato, usa este prompt:

```text
Toma este repositorio y redacta un specs.md retroactivo que declare:
stack con versiones exactas, estructura de carpetas literal,
convenciones observables (naming, imports, tests), scripts del
manifest, dependencias con versión fijada, y fuera-de-alcance
deducible de lo que NO está presente. Marca como "a decidir" cualquier
sección donde el código sea inconsistente. No inventes convenciones
que no puedas justificar con el estado actual del repo.
```

Ese `specs.md` retroactivo es la base para normalizar el proyecto y para que futuras sesiones del agente respeten un contrato explícito.

:::tip Archivos copiables
El flujo completo vive como artefactos listos para usar en [`examples-md/`](https://github.com/10xGuatemala/bootcamp/tree/main/examples-md):

- Plantilla del contrato: [`project/specs.md.example`](https://github.com/10xGuatemala/bootcamp/blob/main/examples-md/project/specs.md.example).
- Skill que conduce los pasos 1–3: [`agents/skills/general/entrevista-specs.skill.md.example`](https://github.com/10xGuatemala/bootcamp/blob/main/examples-md/agents/skills/general/entrevista-specs.skill.md.example).
- Skill que ejecuta el paso 4: [`agents/skills/general/scaffolding-desde-specs.skill.md.example`](https://github.com/10xGuatemala/bootcamp/blob/main/examples-md/agents/skills/general/scaffolding-desde-specs.skill.md.example).

Copia los tres archivos a tu repo (`.claude/skills/` y la raíz para `specs.md`) y el flujo queda disponible para cualquier miembro del equipo.
:::

## Puente al siguiente módulo

La Actividad 0 produce un `specs.md` que servirá de contexto base para el `CLAUDE.md` del proyecto. Cuando tu equipo opere sobre ese repo en las semanas siguientes, los principios de [6.2 Context engineering y CLAUDE.md](./02-context-engineering-claude-md.md) te indicarán cómo mantener ese contexto vivo, y los de [6.3 Arquitectura orientada a skills](./03-arquitectura-orientada-a-skills.md) te ayudarán a convertir las tareas recurrentes del proyecto en skills reutilizables. Si cortas versiones SemVer de lo que construyas, complementa con [5.2 Versionado semántico en equipos](../documentacion-y-requerimientos/02-versionado-semantico-en-equipos.md).

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** producir `specs.md` aprobado y un scaffolding reproducible en una carpeta vacía, sin decisiones implícitas.

**Entradas:**
- Intención del proyecto en una frase.
- Restricciones duras conocidas (stack, fecha, presupuesto).
- Carpeta de trabajo vacía o casi vacía.

**Pasos:**
1. Lanzar la entrevista de especificación con un prompt fundacional.
2. Responder una pregunta a la vez, sin defaults ocultos; cada respuesta es una línea del contrato.
3. Generar `specs.md` completo; revisarlo línea por línea; aprobar.
4. Ejecutar el scaffolding únicamente a partir de `specs.md` aprobado.

**Salidas:**
- `specs.md` versionado en la raíz del repo.
- Árbol de carpetas literal al contrato.
- Manifest con dependencias en versión fijada.
- Commit inicial único.

**Errores comunes:**
- Cuestionario masivo en vez de entrevista guiada.
- Scaffolding con secciones del contrato sin cerrar.
- Dependencias en `latest` o sin versión fijada.
- Archivos añadidos "de cortesía" fuera de specs.
- Push no solicitado al final del paso 4.

**Referencias cruzadas:**
- [6.2 Context engineering y CLAUDE.md](./02-context-engineering-claude-md.md)
- [6.3 Arquitectura orientada a skills](./03-arquitectura-orientada-a-skills.md)
- [5.1 De la idea al release](../documentacion-y-requerimientos/01-de-la-idea-al-release.md)
- [5.2 Versionado semántico en equipos](../documentacion-y-requerimientos/02-versionado-semantico-en-equipos.md)
</div>

---

## Glosario

**Prompt fundacional** *(Foundational prompt)* — prompt inicial que instruye al agente a conducir la entrevista de especificación en lugar de responder a la intención cruda del usuario. Redirige el flujo de *"genérame algo"* a *"pregúntame hasta cerrar decisiones"*.

**Contrato de arranque** *(Specs contract)* — documento `specs.md` que declara todas las decisiones tomadas antes del scaffolding. Funciona como fuente única de verdad para el paso de ejecución y como contexto para sesiones futuras del agente.

**Scaffolding reproducible** *(Reproducible scaffolding)* — generación de la estructura inicial del proyecto tal que cualquier miembro del equipo, partiendo del mismo `specs.md`, obtiene el mismo resultado sin sorpresas del agente.

**Default oculto** *(Hidden default)* — decisión que el agente toma sin preguntar, amparado en un *"suele usarse"* o *"es lo típico"*. Es la principal fuente de deuda en proyectos arrancados sin contrato.

**Scope creep en el arranque** *(Initial scope creep)* — ampliación del alcance durante el scaffolding, al añadir archivos, herramientas o convenciones que no aparecen en `specs.md`. Se controla prohibiendo al agente añadir nada fuera del contrato.

:::info Referencias primarias
- [Anthropic · Claude Code documentation](https://docs.claude.com/en/docs/claude-code) — documentación oficial del agente en línea de comandos.
- [Diátaxis framework](https://diataxis.fr/) — taxonomía de documentación técnica; `specs.md` se clasifica como *reference*.
- [Semantic Versioning 2.0.0](https://semver.org/lang/es/) — base para versionar el contrato y el proyecto resultante.
:::

---

<AuthorCredit />
