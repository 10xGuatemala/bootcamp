---
sidebar_position: 4
title: Medición con OKRs
sidebar_label: 4.3.4 Medición con OKRs
---

# Medición con OKRs

Una de las fallas más comunes al gestionar proyectos es **medir totales de actividades en vez de impacto**. Los OKRs (*Objectives and Key Results*) son una herramienta simple para desplazar la conversación de *"¿cuánto hicimos?"* a *"¿qué cambió en el mundo por lo que hicimos?"*.

## El problema: "vamos 70%"

Imagina este reporte en una reunión de portafolio:

> *"Vamos al 70% del plan. Entregamos 14 de 20 historias comprometidas."*

Suena bien. Hasta que alguien pregunta:

- ¿Las 14 historias entregadas resuelven un problema completo de usuario?
- ¿Mueven alguna métrica que le importe al negocio?
- ¿Las 6 que faltan incluyen la única que el usuario realmente necesitaba?

**Entregar 14 historias no es lo mismo que entregar valor.** Un equipo puede ir "al 70%" del plan y al 0% del impacto.

Este es el vicio que los OKRs buscan corregir: separar **actividad** (lo que hacemos) de **resultado** (lo que cambia porque lo hicimos).

## Anatomía de un OKR

Un OKR tiene dos partes:

- **Objective (O):** un enunciado cualitativo, inspirador, sin números. Responde *"¿qué queremos lograr?"*.
- **Key Results (KR):** 2–5 resultados medibles que, si se cumplen, significan que el Objective se logró.

Un KR bien escrito tiene:

1. Una métrica con nombre claro.
2. Una línea base (de dónde partimos).
3. Un target (a dónde queremos llegar).
4. Una ventana de tiempo.

### Ejemplo

```
O: Reducir la fricción del flujo de soporte para analistas N1.

KR1: Bajar tiempo promedio de primer contacto
     de 8 min a 3 min (trimestre Q2).
KR2: Reducir tickets escalados a N2 por falta de contexto
     de 180/semana a 60/semana.
KR3: Subir satisfacción del analista (encuesta interna)
     de 6.2/10 a 8.0/10.
```

Nota lo que **no** aparece: "entregar las 20 historias". Entregar historias es un *medio*, no un resultado.

## OKRs vs. métricas de actividad

| Métrica de actividad | KR |
|---|---|
| Historias entregadas | Tiempo de primer contacto reducido |
| Líneas de código | Tickets escalados reducidos |
| Commits por semana | Satisfacción del usuario subida |
| % del plan completado | Conversión del flujo objetivo subida |
| Velocidad del sprint | Errores en producción bajando |

Las métricas de actividad sirven *internamente* al equipo (son diagnóstico de proceso). Los KRs sirven al negocio (son evidencia de impacto). **No se reemplazan — coexisten.** Pero el reporte al portafolio va en términos de KRs, no de actividad.

## Plantilla: OKRs

Copia a `docs/okrs/YYYY-QN.md`:

```markdown
# OKRs — Q2 2026

## Contexto

[Qué prioridad estratégica del negocio sostienen estos OKRs]

## Objective 1

**O:** [Enunciado cualitativo, sin números]

**Key Results:**

| KR | Métrica | Baseline | Target | Fuente del dato |
|----|---------|----------|--------|-----------------|
| KR1 | [nombre métrica] | [valor inicio Q] | [valor objetivo] | [dashboard, query, encuesta] |
| KR2 | … | … | … | … |
| KR3 | … | … | … | … |

**Revisión:** cada 2 semanas en la Sprint Review.

---

## Objective 2

…
```

## Regla de 3 a 5

- **Máximo 3 Objectives por equipo.** Más de 3 significa que no se priorizó.
- **2 a 5 KRs por Objective.** Uno solo no captura la complejidad; más de 5 es una lista de tareas.
- **Cadencia trimestral.** Los OKRs anuales son difusos; los mensuales no dan tiempo al impacto.

## Grading honesto

Al cierre del trimestre, cada KR se puntúa de 0 a 1:

- **0.0–0.3:** no avanzamos. Hay que entender por qué.
- **0.4–0.6:** *zona ambiciosa* — el objetivo era difícil y aprendimos mucho.
- **0.7–1.0:** logrado. Si todos están arriba de 0.8, el equipo se puso targets fáciles.

El grading no se usa para premiar o castigar — se usa para **aprender**. Un KR que quedó en 0.2 con análisis honesto enseña más que tres KRs en 0.9 con targets cómodos.

## Anti-patterns comunes

- **OKR cascada de arriba hacia abajo sin negociación.** El equipo no los siente suyos y los cumple en el papel.
- **KRs que son entregables, no métricas.** *"Lanzar el nuevo flujo"* no es KR — es tarea.
- **Cambiar targets a mitad del trimestre** para que el grading se vea mejor.
- **Sumar OKRs sin retirar los anteriores.** Al final del año hay 15 objectives activos y ninguno se cumple.
- **Reportar "vamos 70%"** como si fuera un KR — es actividad, no impacto.

## Glosario

**OKR** *(Objectives and Key Results)* — framework popularizado por Andy Grove en Intel y llevado a Google por John Doerr en [*Measure What Matters*](https://www.whatmatters.com/); separa actividad de resultado. En palabras de Doerr: *"ideas are easy. Execution is everything"*.

**Objective (O)** *(Objective)* — *"descripción cualitativa, memorable, de lo que se quiere lograr"* ([whatmatters.com](https://www.whatmatters.com/resources)). Enunciado inspirador, sin números, que da dirección.

**Key Result (KR)** *(Key Result)* — *"benchmark y monitor de cómo llegamos al objetivo"* ([whatmatters.com](https://www.whatmatters.com/resources)); resultado medible con métrica, baseline, target y ventana de tiempo. Se recomiendan 2–5 por Objective.

**Baseline** *(Baseline)* — valor inicial de la métrica al arranque del trimestre; sin baseline no se puede cuantificar la mejora. Principio de medición descrito en [Google re:Work — Set goals with OKRs](https://rework.withgoogle.com/guides/set-goals-with-okrs/).

**Target** *(Target)* — valor al que se quiere llegar dentro de la ventana del OKR. Doerr propone *"stretch goals"* en [*Measure What Matters*](https://www.whatmatters.com/): metas ambiciosas que incomodan al equipo.

**Grading** *(Grading / Scoring)* — puntuación honesta de 0.0 a 1.0 al cierre del trimestre según metodología Google ([rework.withgoogle.com](https://rework.withgoogle.com/guides/set-goals-with-okrs/)); se usa para aprender, no para compensar ni castigar.

**Zona ambiciosa** *(Sweet Spot)* — rango 0.4–0.6 de grading; según [Google re:Work](https://rework.withgoogle.com/guides/set-goals-with-okrs/) *"si cumples el 100% de tus OKRs consistentemente, no son lo suficientemente ambiciosos"*.

**Métrica de actividad** *(Output Metric / Vanity Metric)* — cuánto hicimos (historias cerradas, commits, % del plan); útil internamente pero no reporta valor de negocio. Contrasta con *outcome metrics* que miden cambios en el mundo ([whatmatters.com](https://www.whatmatters.com/resources)).

:::info Referencias primarias
- [John Doerr · *Measure What Matters* / whatmatters.com](https://www.whatmatters.com/) — referencia canónica sobre OKRs.
- [Google re:Work · Set goals with OKRs](https://rework.withgoogle.com/guides/set-goals-with-okrs/) — guía práctica usada internamente en Google.
- [whatmatters.com · Resources](https://www.whatmatters.com/resources) — plantillas y ejemplos de OKRs.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** diseñar y revisar OKRs trimestrales que midan impacto y no actividad.

**Entradas:**
- Prioridades estratégicas del negocio para el trimestre.
- Métricas existentes del producto / servicio con baseline.
- Lista de iniciativas propuestas (candidatas a *iniciativas*, no a KRs).

**Pasos:**
1. Redactar cada Objective sin números, en lenguaje cualitativo, orientado a resultado.
2. Para cada O, proponer 2–5 KRs con métrica, baseline, target y fuente del dato.
3. Validar que cada KR mida *impacto*, no actividad ni entregable.
4. Verificar que el total no exceda 3 Objectives.
5. Definir cadencia de revisión (recomendado: cada 2 semanas en Sprint Review).
6. Al cierre del trimestre, *grading* honesto (0–1) y notas de aprendizaje.

**Salidas:**
- `docs/okrs/YYYY-QN.md` con plantilla completa.
- Notas de *grading* al final del trimestre.

**Errores comunes:**
- KRs que son entregables (*"lanzar X"*).
- Más de 3 Objectives por equipo.
- Reportar avance como "vamos N%" del plan en vez de posición en KRs.
- Mover targets a mitad de trimestre.
- Ausencia de baseline (no se puede medir mejora si no hay punto de partida).

**Referencias cruzadas:**
- [4.2.3 Niveles de planeamiento](../agilidad-y-scrum/03-niveles-de-planeamiento.md)
- [4.3.5 Retrospectivas](./05-retrospectivas.md)
</div>

---

<AuthorCredit />
