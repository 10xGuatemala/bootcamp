---
sidebar_position: 3
title: Sprint Planning y Daily
sidebar_label: 4.3.3 Sprint Planning y Daily
---

# Sprint Planning y Daily

Las ceremonias del sprint son la **mecánica diaria** que hace que el backlog se convierta en incremento. Si se hacen bien son cortas y útiles; si se hacen mal, son la razón número uno por la que los equipos "odian Scrum".

## Sprint Planning

**Objetivo:** responder dos preguntas.

1. **¿Qué podemos entregar este sprint?** (el *qué* — lo decide el PO con entrada del equipo)
2. **¿Cómo lo vamos a construir?** (el *cómo* — lo decide el equipo)

### Duración

| Duración del sprint | Timebox de planning |
|---|---|
| 1 semana | 2 h |
| 2 semanas | 4 h |
| 3 semanas | 6 h |
| 4 semanas | 8 h |

Pasarse del timebox suele significar que el backlog no estaba refinado.

### Plantilla: Sprint Planning

```markdown
# Sprint N — Planning

**Fecha:** YYYY-MM-DD
**Duración del sprint:** 2 semanas (cierra YYYY-MM-DD)
**Capacidad del equipo:** [horas disponibles o puntos]

## Objetivo del sprint (Sprint Goal)

[Una frase. "Entregar flujo de login con MFA habilitado para staging."]

## Historias comprometidas

| ID | Título | Estimación | Responsable |
|----|--------|------------|-------------|
| PB-01 | … | 5 | — |
| PB-02 | … | 3 | — |

**Total:** 8 pts (capacidad: 10 pts)

## Plan de ejecución

- Día 1–3: PB-01 (desglose en tareas técnicas abajo)
- Día 4–6: PB-02
- Día 7–8: buffer / QA / review

## Tareas técnicas (por historia)

### PB-01
- [ ] Diseñar esquema de tabla
- [ ] Endpoint POST /login
- [ ] Tests de integración
- [ ] …

## Riesgos / dependencias

- [Dependencia externa, incógnita técnica, vacación]
```

### Sprint Goal

El *Sprint Goal* es el pegamento del sprint. Responde *"¿para qué sirvió este sprint?"* en una frase. Si el equipo termina todas las historias pero el Sprint Goal no se logra, el sprint no fue exitoso.

## Daily Scrum

**Objetivo:** sincronizar el día. No es reporte al jefe.

### Reglas

- **15 minutos**, de pie, misma hora todos los días.
- Solo los *Developers* hablan; SM facilita si hace falta, PO puede estar presente y escuchar.
- Preguntas clásicas:
  1. ¿Qué hice desde el daily anterior que acerca al Sprint Goal?
  2. ¿Qué haré hoy que acerque al Sprint Goal?
  3. ¿Qué me bloquea?

**Formato alternativo (más útil en equipos maduros):** *"caminar el board"* — ir de derecha (done) a izquierda (to do) revisando cada historia en progreso. Evita que cada persona recite una lista individual sin conectar con el sprint.

### Plantilla: Daily

```markdown
# Daily — YYYY-MM-DD

## Sprint Goal
[una frase]

## Estado del board
- En progreso: PB-01 (Ana), PB-02 (Luis)
- En review: PB-03
- Done: PB-04, PB-05

## Impedimentos
- [Bloqueo 1, owner, próxima acción]

## Decisiones
- [Cualquier cambio de plan]
```

La plantilla **no se llena todos los días** — solo se usa si el equipo quiere dejar registro. El daily en sí mismo es oral.

## Qué *no* es un Daily

- **No** es reporte a un jefe.
- **No** es discutir arquitectura — eso se hace después, con quienes corresponda.
- **No** es "status update" — si no hay conexión con el Sprint Goal, no pertenece.
- **No** es para asignar tareas.

Un daily que se convierte en una reunión de 45 min con debate técnico es una señal clara: falta otra reunión que el equipo no está programando.

## Sprint Review vs. Retrospective

No confundir:

| Review | Retrospective |
|---|---|
| Inspecciona **el producto** | Inspecciona **el proceso** |
| Muestra incremento a stakeholders | Interna al equipo |
| Ajusta el Product Backlog | Ajusta el *cómo* trabajamos |
| Participan: PO, Devs, stakeholders | Participan: PO, SM, Devs |

Ver [05 · Retrospectivas](./05-retrospectivas.md) para el detalle de la retro.

## Glosario

**Sprint Planning** *(Sprint Planning)* — evento que inicia el Sprint y cuyo propósito es planificar el trabajo; responde tres preguntas: *¿por qué es valioso este Sprint?* (Sprint Goal), *¿qué se puede Hacer este Sprint?* y *¿cómo se realizará el trabajo elegido?* ([Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf)).

**Sprint Goal** *(Sprint Goal)* — *"único propósito del Sprint"* y uno de los tres compromisos del Sprint Backlog ([Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf)); da foco a las historias comprometidas.

**Timebox** *(Timebox)* — duración máxima fija asignada a un evento; la [Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf) establece *"un máximo de ocho horas para un Sprint de un mes"* para Sprint Planning y 15 minutos para Daily Scrum.

**Daily Scrum** *(Daily Scrum)* — *"evento de 15 minutos para los Developers del Scrum Team"* ([Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf)); inspecciona el progreso hacia el Sprint Goal y adapta el Sprint Backlog. No es un reporte de estatus.

**Board** *(Scrum/Task Board)* — tablero físico o digital que visualiza el estado del trabajo del Sprint (*to do / in progress / review / done*); apoya la transparencia, uno de los tres pilares de Scrum ([Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf)).

**Caminar el board** *(Walk the board)* — formato alternativo del Daily Scrum que revisa historias de derecha a izquierda (priorizando las más cercanas a *done*) en vez de persona por persona; enfoca la conversación en el flujo, no en el individuo.

:::info Referencias primarias
- [Scrum Guide 2020 (español europeo)](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf) — definición oficial de Sprint Planning, Daily Scrum y timeboxes.
- [Scrum Alliance · About Scrum](https://www.scrumalliance.org/about-scrum) — recursos introductorios a los eventos de Scrum (inglés).
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** facilitar un Sprint Planning productivo y dejar trazabilidad del plan del sprint.

**Entradas:**
- Product Backlog priorizado.
- Capacidad del equipo para el sprint.
- Velocidad observada en los 3–5 sprints anteriores.

**Pasos:**
1. Redactar Sprint Goal en una frase que conecte las historias comprometidas.
2. Seleccionar historias del top del backlog hasta llenar capacidad (no velocidad).
3. Para cada historia, descomponer en tareas técnicas.
4. Identificar dependencias y riesgos; escalar los que requieran acción externa.
5. Llenar plantilla de Sprint Planning.

**Salidas:**
- `docs/sprints/sprint-N-planning.md` con plantilla completa.

**Errores comunes:**
- Comprometer >capacidad confiando en que "velocidad nos alcanza".
- Sprint sin Sprint Goal claro.
- Historias sin descomponer entrando al sprint.
- Daily que se convierte en reunión de diseño.

**Referencias cruzadas:**
- [4.3.2 Product Backlog](./02-product-backlog.md)
- [4.3.5 Retrospectivas](./05-retrospectivas.md)
</div>

---

<AuthorCredit />
