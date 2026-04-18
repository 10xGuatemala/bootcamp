---
sidebar_position: 2
title: Product Backlog
sidebar_label: 4.3.2 Product Backlog
---

# Product Backlog

El Product Backlog es la **fuente de verdad del alcance**: una lista ordenada de todo lo que *podría* construirse. No es un documento muerto — se refina cada sprint, cambia cuando el aprendizaje lo justifica y siempre está priorizado.

## Anatomía de una historia

La plantilla clásica:

```
Como [rol de usuario]
Quiero [acción]
Para [beneficio / resultado]
```

Ejemplo:

```
Como analista de soporte
Quiero ver los últimos 5 tickets del mismo cliente al abrir un caso
Para no repetir preguntas y cerrar el caso más rápido
```

Elementos mínimos de una historia lista para sprint:

| Elemento | Para qué sirve |
|---|---|
| **Título breve** | Referencia rápida en board y conversación |
| **Historia** | Quién / qué / por qué |
| **Criterios de aceptación** | Lista verificable de qué tiene que funcionar |
| **Notas técnicas** | Pistas, restricciones, dependencias |
| **Estimación** | Story points o talla (S/M/L) |
| **Prioridad / orden** | Posición en el backlog |

## Priorización con MoSCoW

MoSCoW es una técnica simple para ordenar backlog en contextos de release:

| Categoría | Significado | Uso |
|---|---|---|
| **Must have** | Sin esto el release no va. | ~60% de capacidad |
| **Should have** | Importante pero no bloqueante. | ~20% |
| **Could have** | Bueno tenerlo si queda tiempo. | ~20% |
| **Won't have** (this release) | Fuera de este release — explícito. | — |

**Trampa común:** todo termina siendo *Must*. Si >70% del backlog es Must, significa que no se priorizó — solo se etiquetó.

## Plantilla: Product Backlog

Copia a `docs/release/vX.Y.Z-backlog.md`:

```markdown
# Product Backlog — v0.1.0

## Must have

### PB-01 — Título corto

**Historia:**
Como [rol], quiero [acción], para [beneficio].

**Criterios de aceptación:**
- [ ] Dado [contexto], cuando [acción], entonces [resultado esperado].
- [ ] …

**Notas técnicas:**
- [Dependencia, restricción o pista]

**Estimación:** M (5 pts)

---

### PB-02 — …

## Should have

### PB-10 — …

## Could have

### PB-20 — …

## Won't have (este release)

- PB-99 — [razón por la que queda fuera]
```

## Refinamiento continuo

El backlog **se refina durante el sprint**, no al final. El equipo dedica ~10% del tiempo del sprint a refinar los ítems de los siguientes 1–2 sprints:

- Clarificar criterios de aceptación.
- Descomponer historias demasiado grandes.
- Eliminar ítems que ya no aplican.
- Reordenar por valor.

**INVEST** — cualidades de una buena historia:

- **I**ndependent: se puede entregar sin depender de otras.
- **N**egotiable: el *cómo* se conversa, no se impone.
- **V**aluable: entrega valor a alguien.
- **E**stimable: el equipo puede dar una talla.
- **S**mall: cabe en un sprint.
- **T**estable: tiene criterios de aceptación verificables.

Si una historia falla 2+ pruebas INVEST, aún no está lista para entrar a sprint.

## Anti-patterns comunes

- **Backlog de 500 ítems.** Nadie lo lee. Archiva lo que lleva más de 3 meses sin mover.
- **Historias técnicas sin usuario.** *"Refactorizar módulo X"* no tiene *Para*. Se justifica como *spike* o como parte de una historia de usuario.
- **Criterios de aceptación escritos después del desarrollo.** Eso no es aceptación — es reporte.
- **El PO prioriza por presión política.** El backlog refleja quién gritó más, no qué entrega más valor.

## Glosario

**Product Backlog** *(Product Backlog)* — *"lista emergente y ordenada de lo que se necesita para mejorar el producto. Es la única fuente del trabajo realizado por el Scrum Team"* ([Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf)).

**User story** *(User Story)* — formato *"Como [rol], quiero [acción], para [beneficio]"* popularizado por Mike Cohn en [*User Stories Applied*](https://www.mountaingoatsoftware.com/books/user-stories-applied); captura el valor desde la perspectiva del usuario, no la solución técnica.

**Criterios de aceptación** *(Acceptance Criteria)* — lista verificable (a menudo en formato *Given / When / Then* de BDD) que define cuándo una historia está terminada; complementa la Definition of Done del equipo ([Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf)).

**MoSCoW** *(MoSCoW prioritization)* — técnica de priorización en cuatro categorías: *Must have, Should have, Could have, Won't have*; originada en el framework DSDM.

**INVEST** *(INVEST criteria)* — acrónimo propuesto por Bill Wake para historias de calidad: *Independent, Negotiable, Valuable, Estimable, Small, Testable* ([xp123.com · INVEST in Good Stories](https://xp123.com/articles/invest-in-good-stories-and-smart-tasks/)).

**Refinamiento** *(Product Backlog Refinement)* — *"acto de dividir y definir aún más los elementos del Product Backlog en elementos más pequeños y precisos"* ([Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf)); actividad continua, no un evento formal.

**Épica** *(Epic)* — historia demasiado grande para un único Sprint; se descompone en historias más pequeñas antes de entrar al Sprint Backlog. Concepto descrito por Mike Cohn en [*User Stories Applied*](https://www.mountaingoatsoftware.com/books/user-stories-applied).

:::info Referencias primarias
- [Scrum Guide 2020 (español europeo)](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf) — definición oficial del Product Backlog y su refinamiento.
- [Mike Cohn · *User Stories Applied*](https://www.mountaingoatsoftware.com/books/user-stories-applied) — referencia canónica de user stories y épicas.
- [Bill Wake · INVEST in Good Stories](https://xp123.com/articles/invest-in-good-stories-and-smart-tasks/) — artículo original del criterio INVEST.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** construir o refinar un Product Backlog priorizado con historias que cumplen INVEST.

**Entradas:**
- Problem Statement y Scope vigentes.
- Lista de ideas o historias en lenguaje libre.
- Capacidad estimada del equipo por sprint.

**Pasos:**
1. Convertir cada idea a formato *Como / Quiero / Para*.
2. Añadir criterios de aceptación verificables (dado / cuando / entonces).
3. Evaluar cada historia contra INVEST; descomponer las que no pasen *Small*.
4. Clasificar con MoSCoW; verificar que Must ≤ 60% de capacidad.
5. Ordenar dentro de cada categoría por valor / riesgo.
6. Marcar fuera de release explícitamente (Won't have).

**Salidas:**
- `docs/release/vX.Y.Z-backlog.md` con historias INVEST y priorización MoSCoW.

**Errores comunes:**
- Todo marcado como Must.
- Historias sin criterios de aceptación.
- Épicas enteras tratadas como una sola historia.
- Backlog sin archivo — crece indefinidamente.

**Referencias cruzadas:**
- [4.3.1 Plan, problema y alcance](./01-plan-problema-alcance.md)
- [4.3.3 Sprint Planning y Daily](./03-sprint-planning-y-daily.md)
</div>

---

<AuthorCredit />
