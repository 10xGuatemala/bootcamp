---
sidebar_position: 1
title: Plan, problema y alcance
sidebar_label: 4.3.1 Plan, problema y alcance
---

# Plan, problema y alcance

Antes de armar backlog, ceremonias o sprints, hay que responder tres preguntas: **qué problema resolvemos, para quién, y qué entra/no entra en el alcance**. Saltarse esto es la causa raíz de la mayoría de proyectos que entregan código pero no resuelven nada.

## El Círculo Dorado aplicado a proyectos

Simon Sinek lo describe como *Why → How → What*. En proyectos:

- **Why (por qué):** el problema del usuario o negocio que justifica invertir esfuerzo.
- **How (cómo):** la estrategia — qué tipo de solución, qué restricciones respetamos.
- **What (qué):** las features concretas.

Los proyectos que fallan casi siempre arrancaron por el *What* (*"hagamos una app que…"*) sin validar el *Why*.

## Plantilla: Problem Statement

Copia esta plantilla a tu repo en `docs/release/vX.Y.Z-problem.md` al abrir un release:

```markdown
# Problem Statement — v0.1.0

## Problema

[1–3 frases. Qué está pasando hoy, a quién afecta, qué cuesta.]

## Evidencia

- [Dato, métrica, cita de usuario, ticket recurrente]
- [Otro dato]

## Usuario objetivo

[Rol concreto, no "usuario final". Ej: "Analista de soporte N1 que atiende 30+ tickets/día"]

## Resultado esperado

[Cuando esto esté resuelto, ¿qué cambia? Una sola frase.]

## Fuera de alcance (explícito)

- [Lo que NO vamos a resolver en este release]
- [Otra cosa fuera]

## Éxito medible

- [Métrica 1 con baseline y target]
- [Métrica 2]
```

**Regla:** si no puedes llenar *Evidencia* y *Éxito medible*, el problema no está lo suficientemente entendido para invertir un sprint.

## Plantilla: Scope Statement

```markdown
# Scope — v0.1.0

## Incluido

- [Capability 1 — breve]
- [Capability 2]

## Excluido

- [Lo que alguien preguntaría si entra y no entra]

## Supuestos

- [Lo que estamos asumiendo cierto sin validar, y que si resulta falso cambia el alcance]

## Restricciones

- Tiempo: [ventana disponible]
- Equipo: [headcount comprometido]
- Presupuesto / infra: [si aplica]
- Cumplimiento: [regulaciones, políticas internas]

## Criterios de aceptación del release

- [Qué tiene que ser verdad para declarar este release "terminado"]
```

## Idea vs. Requerimiento

Una **idea** es una intuición. Un **requerimiento** es una idea que ya pasó por:

1. Evidencia de que el problema existe.
2. Identificación del usuario concreto.
3. Criterio medible de éxito.
4. Definición explícita de qué queda fuera.

Un backlog lleno de *ideas* se traduce en sprints donde se debate cada historia de cero. Un backlog de *requerimientos* se traduce en sprints que ejecutan.

## Errores al plantear el problema

- **Falsa urgencia.** "Urgente" sin evidencia; suele significar que alguien con poder lo pidió, no que el usuario lo necesite ya.
- **Solución disfrazada de problema.** "Necesitamos una app móvil" no es un problema — es una solución hipotética.
- **Alcance sin *Fuera*.** Solo listar lo incluido deja todo lo demás en ambigüedad; siempre declarar exclusiones.
- **Éxito no medible.** "Mejor experiencia de usuario" no es medible. "Reducir tickets de soporte N1 de 300 a 150 por semana" sí lo es.

## Glosario

**Círculo Dorado** *(Golden Circle)* — modelo *Why → How → What* popularizado por Simon Sinek en *Start With Why*; aplicado a proyectos, obliga a articular el propósito antes del entregable.

**Problem Statement** *(Problem Statement)* — enunciado conciso que describe qué está pasando, a quién afecta, con qué evidencia y qué resultado se espera. Parte del *business case* descrito en la [PMBOK Guide](https://www.pmi.org/pmbok-guide-standards).

**Scope Statement** *(Project Scope Statement)* — según la [PMBOK Guide](https://www.pmi.org/pmbok-guide-standards): *"descripción del alcance del proyecto, entregables principales, supuestos y restricciones"*. Atlassian lo resume como *"el proceso de definir qué trabajo se necesita para completar el proyecto"* ([Atlassian · Project scope](https://www.atlassian.com/work-management/project-management/project-scope)).

**Idea** *(Idea)* — intuición sin evidencia; insumo previo al requerimiento. No está lista para entrar al backlog sin validación.

**Requerimiento** *(Requirement)* — *"condición o capacidad que debe cumplir o poseer un sistema o componente para satisfacer un contrato, estándar o especificación"* ([PMBOK Guide](https://www.pmi.org/pmbok-guide-standards)); incluye evidencia, usuario concreto, criterio de éxito medible y exclusiones.

**Supuesto** *(Assumption)* — factor considerado cierto, real o seguro sin evidencia; si resulta falso, cambia el alcance ([PMBOK Guide](https://www.pmi.org/pmbok-guide-standards)).

**Criterio de aceptación del release** *(Release Acceptance Criteria)* — condiciones verificables que un entregable debe cumplir para ser aceptado por el sponsor o cliente; base del *scope verification* en la [PMBOK Guide](https://www.pmi.org/pmbok-guide-standards).

:::info Referencias primarias
- [PMBOK Guide séptima edición (PMI)](https://www.pmi.org/pmbok-guide-standards) — definición canónica de alcance, requerimientos y supuestos.
- [Atlassian · Project Scope](https://www.atlassian.com/work-management/project-management/project-scope) — guía práctica sobre alcance de proyecto (inglés).
- [Simon Sinek · Start With Why](https://simonsinek.com/books/start-with-why/) — origen del Golden Circle.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** convertir una idea o petición en un Problem Statement + Scope Statement listos para entrar al backlog.

**Entradas:**
- Idea o petición en lenguaje libre.
- Contexto del negocio (usuarios, métricas actuales, restricciones conocidas).
- Tickets, datos o citas de usuarios disponibles.

**Pasos:**
1. Extraer el problema real (no la solución sugerida). Preguntar *"¿qué pasaría si no hacemos nada?"*.
2. Buscar evidencia concreta; si no existe, declarar *supuesto*.
3. Identificar usuario objetivo en rol concreto.
4. Definir éxito medible con baseline y target.
5. Listar exclusiones explícitas — lo que alguien podría asumir que entra.
6. Llenar ambas plantillas (Problem + Scope).

**Salidas:**
- `docs/release/vX.Y.Z-problem.md` con ambas plantillas llenas.

**Errores comunes:**
- Aceptar el enunciado del solicitante como problema sin desempacarlo.
- Saltar la sección de éxito medible por no tener datos.
- Omitir exclusiones.
- Confundir urgencia con prioridad.

**Referencias cruzadas:**
- [4.3.2 Product Backlog](./02-product-backlog.md)
- [5.1 De la idea al release](../../documentacion-y-requerimientos/01-de-la-idea-al-release.md)
</div>

---

<AuthorCredit />
