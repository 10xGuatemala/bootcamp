---
sidebar_position: 5
title: Retrospectivas
sidebar_label: 4.3.5 Retrospectivas
---

# Retrospectivas

La Sprint Retrospective es la ceremonia más fácil de hacer mal: termina convertida en queja colectiva o en una lista de mejoras que nadie ejecuta. Bien hecha, es el único mecanismo que tiene un equipo ágil para **mejorar su propio proceso de forma continua**.

## Regla de oro

Una retro vale por lo que **cambia en el siguiente sprint**, no por lo que se dijo. Si tres retros consecutivas no producen cambios observables, la retro está rota.

## Estructura básica (cinco fases)

Tomado del libro *Agile Retrospectives* (Derby & Larsen):

1. **Set the stage** (5 min) — abrir la retro, recordar objetivo, checkin breve.
2. **Gather data** (15–20 min) — qué pasó en el sprint, sin interpretación.
3. **Generate insights** (20–30 min) — por qué pasó, patrones, causas.
4. **Decide what to do** (15–20 min) — 1–2 acciones concretas con owner y fecha.
5. **Close** (5 min) — reconocimientos, confirmación de acciones.

**Timebox total:** 1.5 h para sprint de 2 semanas.

## Formato: 4Ls

Bueno para equipos nuevos o retrospectivas simples:

| L | Pregunta |
|---|---|
| **Liked** | ¿Qué nos gustó? |
| **Learned** | ¿Qué aprendimos? |
| **Lacked** | ¿Qué nos faltó? |
| **Longed for** | ¿Qué deseamos? |

### Plantilla: 4Ls

Copia a `docs/sprints/sprint-N-retro.md`:

```markdown
# Retro — Sprint N

**Fecha:** YYYY-MM-DD
**Participantes:** [lista]

## Liked

- [Qué funcionó, qué queremos preservar]

## Learned

- [Algo que aprendimos — técnico, de proceso, de producto]

## Lacked

- [Qué faltó o fue insuficiente]

## Longed for

- [Qué desearíamos tener]

## Acciones para el siguiente sprint

| Acción | Owner | Fecha |
|--------|-------|-------|
| [Concreta, medible] | Nombre | YYYY-MM-DD |
```

## Formato: Start / Stop / Continue

Bueno cuando el equipo ya está maduro y quiere ir al punto:

- **Start:** ¿Qué deberíamos empezar a hacer?
- **Stop:** ¿Qué deberíamos dejar de hacer?
- **Continue:** ¿Qué deberíamos seguir haciendo?

Más corto, pero pide mayor auto-crítica del equipo.

## Formato: Mad / Sad / Glad

Útil cuando el sprint fue emocionalmente cargado (incidente, rework grande, fricción interpersonal):

- **Mad:** ¿Qué me frustró?
- **Sad:** ¿Qué me decepcionó?
- **Glad:** ¿Qué me alegró?

Permite descargar antes de pasar a analizar.

## Variar el formato

Usar el mismo formato cada sprint durante un año produce retros mecánicas. Variar el formato cada 3–4 sprints mantiene la reflexión fresca. Algunas opciones adicionales: *Starfish*, *Sailboat*, *Timeline*, *5 Whys sobre un incidente concreto*.

## Acciones: concretas o no van

El output más importante de la retro son **1 o 2 acciones concretas**, no 10 aspiracionales.

### Regla de una acción

Una acción de retro debe cumplir:

- **Quién** la ejecuta (owner nombrado).
- **Qué** es (verbo concreto).
- **Cuándo** está hecha (fecha o "al cierre del próximo sprint").
- **Cómo se verifica** que se hizo.

**Mal:** *"Mejorar la comunicación con QA."*
**Bien:** *"Luis agenda 15 min de handoff con QA al abrir cada PR grande, a partir del sprint N+1."*

## Anti-patterns comunes

- **Quejas sin acción.** La retro se convierte en terapia grupal; nadie se lleva tarea.
- **Misma acción cada sprint.** *"Mejorar la definición de done"* aparece 4 retros seguidas — nadie la ejecuta.
- **Retro con el jefe presente.** La gente no habla honesto. SM debe negociar esto.
- **Saltarse la retro "porque vamos corriendo".** Es exactamente cuando más se necesita.
- **10 acciones por retro.** Ninguna se ejecuta. Mejor 1 y hecha, que 10 y archivadas.

## Glosario

**Sprint Retrospective** *(Sprint Retrospective)* — según la [Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf): *"el propósito es planificar maneras de aumentar la calidad y la efectividad"*. Ceremonia de cierre del Sprint enfocada en el proceso, no en el producto. Fundamento explicado en [Derby & Larsen · *Agile Retrospectives*](https://pragprog.com/titles/dlret/agile-retrospectives/).

**4Ls** *(Liked, Learned, Lacked, Longed for)* — formato de retrospectiva con cuatro cuadrantes; plantilla popular en [Atlassian Team Playbook · Retrospectives](https://www.atlassian.com/team-playbook/plays/retrospective) y [Retromat](https://retromat.org/).

**Start / Stop / Continue** *(Start / Stop / Continue)* — formato corto de retro; qué empezar, qué dejar de hacer y qué continuar. Catalogado en [Retromat](https://retromat.org/) como una de las plantillas más usadas.

**Mad / Sad / Glad** *(Mad, Sad, Glad)* — formato de retrospectiva emocional; útil cuando el Sprint acumuló tensión o incidentes. Referenciado en [Retromat](https://retromat.org/) y [Atlassian](https://www.atlassian.com/team-playbook/plays/retrospective).

**Acción de retro** *(Retrospective Action Item)* — compromiso concreto con responsable, plazo y criterio de verificación. Derby & Larsen en [*Agile Retrospectives*](https://pragprog.com/titles/dlret/agile-retrospectives/) recomiendan máximo 1–2 acciones por retro para no diluir foco.

**Timeline / Sailboat / Starfish** *(Timeline / Sailboat / Starfish)* — formatos alternativos documentados en [Retromat](https://retromat.org/) que rompen la mecánica repetitiva: *Timeline* reconstruye eventos, *Sailboat* identifica anclas y vientos, *Starfish* clasifica en 5 ejes.

:::info Referencias primarias
- [Retromat](https://retromat.org/) — catálogo interactivo de formatos de retrospectiva (inglés/español).
- [Atlassian Team Playbook · Retrospective](https://www.atlassian.com/team-playbook/plays/retrospective) — guía práctica paso a paso.
- [Derby & Larsen · *Agile Retrospectives*](https://pragprog.com/titles/dlret/agile-retrospectives/) — libro canónico sobre estructura y facilitación de retros.
- [Scrum Guide 2020 (español europeo)](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf) — propósito oficial de la Sprint Retrospective.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** facilitar una retrospectiva que produzca 1–2 acciones concretas con owner y fecha.

**Entradas:**
- Duración y Sprint Goal del sprint que cierra.
- Incidencias u observaciones del sprint (incidentes, rework, bloqueos).
- Acciones pendientes de la retro anterior y su estado.

**Pasos:**
1. Primero revisar estado de acciones de la retro anterior; marcar hechas / no hechas.
2. Elegir formato (4Ls, Start/Stop/Continue, Mad/Sad/Glad) según el tono del sprint.
3. Facilitar recolección de datos (post-its o sección por persona).
4. Agrupar y buscar patrones; no saltar a soluciones.
5. Priorizar 1–2 acciones; validar que cumplan *quién / qué / cuándo / cómo se verifica*.
6. Registrar en `docs/sprints/sprint-N-retro.md`.

**Salidas:**
- Archivo de retro con datos, insights y acciones.
- Estado actualizado de acciones previas.

**Errores comunes:**
- Acciones vagas sin owner ni fecha.
- Misma acción repetida sin ejecutarse.
- Retro sin referencia a acciones anteriores.
- Demasiadas acciones — priorizar es decidir qué no se hace.

**Referencias cruzadas:**
- [4.3.3 Sprint Planning y Daily](./03-sprint-planning-y-daily.md)
- [4.3.4 Medición con OKRs](./04-medicion-con-okrs.md)
- [4.3.6 Cierre y lecciones aprendidas](./06-cierre-y-lecciones.md)
</div>

---

<AuthorCredit />
