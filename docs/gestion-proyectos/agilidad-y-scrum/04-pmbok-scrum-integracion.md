---
sidebar_position: 4
title: PMBOK y Scrum — integración
sidebar_label: 4.2.4 PMBOK y Scrum — integración
---

# PMBOK y Scrum — integración

En la práctica, muchos proyectos viven en organizaciones que reportan a una PMO con lenguaje PMBOK y, al mismo tiempo, ejecutan con equipos que usan Scrum. La pregunta no es *"¿cuál es mejor?"* — es *"¿cómo los hacemos convivir sin teatro?"*.

## Qué cubre cada uno

| Dimensión | PMBOK | Scrum |
|---|---|---|
| **Alcance** | Cuerpo de conocimiento general — aplica a cualquier proyecto. | Framework específico para productos con requisitos evolutivos. |
| **Unidad de control** | Proyecto con inicio, fin y alcance definidos. | Producto con vida continua, entregado en incrementos. |
| **Plan** | Detallado al inicio (WBS, cronograma, presupuesto). | Progresivo — detalle crece al acercarse al sprint. |
| **Cambio** | Proceso formal de *change control*. | Se espera y se gestiona vía backlog. |
| **Roles** | PM como rol integrador con autoridad. | PO (qué/por qué) + SM (proceso) + Devs (cómo). |
| **Métricas** | % de avance vs. plan base, EVM. | Velocidad observada, valor entregado, DoD. |

Ninguno reemplaza al otro. PMBOK describe **el portafolio y el contrato**; Scrum describe **cómo un equipo construye el producto dentro de ese contrato**.

## Dónde encajan sin fricción

- **Iniciación del proyecto (PMBOK)** → *charter*, stakeholders, visión. Esa visión alimenta el Product Backlog inicial.
- **Planeación de alcance (PMBOK)** → en Scrum se traduce a *épicas* en el backlog, no a WBS fijo.
- **Ejecución (Scrum)** → el equipo corre sprints; el PM reporta avance al portafolio en términos de incrementos entregados, no de tareas cerradas.
- **Control (PMBOK + Scrum)** → las *Sprint Reviews* son el mecanismo principal de control de alcance y calidad. El EVM tradicional no aplica bien; los reportes al portafolio usan incrementos usables como evidencia.
- **Cierre (PMBOK)** → lecciones aprendidas formales + entrega a operación.

## Dónde hay fricción real

1. **Compromiso de alcance fijo a 12 meses** con *change control* costoso. Scrum asume que el alcance evoluciona; si el contrato no lo permite, se hace *"ScrumFall"* — ceremonias encima de un cascada.
2. **Reportes de avance en % del plan base**. Scrum mide entregas. Forzar *"vamos 70%"* sobre un plan fijo reintroduce el problema que Scrum buscaba evitar (ver [Medición con OKRs](../ciclo-del-proyecto/04-medicion-con-okrs.md)).
3. **Rol de PM vs. rol de PO**. Si el PM intenta priorizar el backlog y asignar historias, invade al PO y al equipo. El PM encaja mejor como *puente* al portafolio, gestión de riesgos transversales y coordinación inter-equipos.
4. **WBS vs. Product Backlog**. El WBS descompone trabajo hasta paquetes estimables; el backlog ordena historias por valor. Mantener ambos en paralelo suele producir dos fuentes de verdad que divergen.

## Recomendaciones prácticas

- **Traducir, no reemplazar.** Si el portafolio exige *status report*, reportar *"N historias entregadas, M en progreso, DoD cumplido"* en vez de % arbitrarios.
- **Un solo backlog.** El Product Backlog es la fuente de verdad del alcance. El WBS, si existe, se deriva de él.
- **PM como *liaison***. El PM coordina lo que Scrum no cubre: contratos, proveedores, dependencias entre equipos, riesgos corporativos.
- **Change control proporcional.** Cambios dentro del sprint no van a comité; cambios de visión sí. Definir el umbral en el *charter*.
- **Lecciones aprendidas = retrospectivas acumuladas.** Al cierre del proyecto, la documentación formal se alimenta de lo que ya salió de las retros sprint a sprint.

## Glosario

**PMBOK** *(Project Management Body of Knowledge)* — estándar del PMI; en su [séptima edición](https://www.pmi.org/pmbok-guide-standards) evolucionó de procesos prescriptivos a 12 principios y 8 dominios de desempeño aplicables a enfoques predictivos, híbridos y adaptativos.

**WBS** *(Work Breakdown Structure)* — *"descomposición jerárquica del alcance total del trabajo que debe realizar el equipo"* según la [PMBOK Guide](https://www.pmi.org/pmbok-guide-standards); cada nivel descendente representa una definición más detallada del trabajo.

**EVM** *(Earned Value Management)* — técnica de control que integra alcance, cronograma y costo para medir desempeño y progreso; estándar PMI descrito en la [PMBOK Guide](https://www.pmi.org/pmbok-guide-standards).

**Change control** *(Integrated Change Control)* — proceso formal para revisar, aprobar o rechazar cambios a la línea base; parte del dominio de desempeño de *Project Work* en la [PMBOK Guide séptima edición](https://www.pmi.org/pmbok-guide-standards).

**Charter** *(Project Charter)* — *"documento emitido por el iniciador o patrocinador del proyecto que autoriza formalmente su existencia"* ([PMBOK Guide](https://www.pmi.org/pmbok-guide-standards)).

**PMO** *(Project Management Office)* — estructura organizacional que estandariza procesos de gobierno de proyectos y facilita el intercambio de recursos, metodologías y herramientas ([PMI](https://www.pmi.org/pmbok-guide-standards)).

**ScrumFall** *(ScrumFall / Water-Scrum-Fall)* — *anti-pattern* ampliamente documentado: aplicar ceremonias de Scrum sobre un plan fijo tipo cascada. Discutido en la [Scrum Guide 2020](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf) como implementación incompleta del framework.

**Disciplined Agile (DA)** *(Disciplined Agile)* — kit de herramientas del PMI que guía a equipos para elegir su *way of working* combinando Scrum, Kanban, SAFe y prácticas tradicionales ([PMI · Disciplined Agile](https://www.pmi.org/disciplined-agile)).

:::info Referencias primarias
- [PMBOK Guide séptima edición (PMI)](https://www.pmi.org/pmbok-guide-standards) — principios y dominios de desempeño.
- [Scrum Guide 2020 (español europeo)](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf) — definición oficial de Scrum.
- [PMI · Disciplined Agile](https://www.pmi.org/disciplined-agile) — kit de herramientas para elegir *way of working*.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** diagnosticar fricciones entre estructura PMBOK de una organización y ejecución Scrum de un equipo, y proponer traducciones que eviten teatro.

**Entradas:**
- Artefactos PMBOK vigentes (charter, WBS, cronograma, reportes de avance).
- Artefactos Scrum vigentes (backlog, sprint backlog, DoD).
- Formatos de reporte requeridos por la PMO.

**Pasos:**
1. Mapear cada artefacto PMBOK a su equivalente Scrum (o marcar "sin equivalente").
2. Identificar fuentes duplicadas de verdad (WBS paralelo al backlog, por ejemplo).
3. Detectar fricciones: reportes de % arbitrario, change control que bloquea adaptación, rol de PM invadiendo al PO.
4. Proponer *traducciones*: cómo cumplir el requisito PMBOK usando evidencia Scrum.
5. Definir umbral de cambio: qué se gestiona en el sprint, qué escala al portafolio.

**Salidas:**
- Tabla de equivalencias y traducciones.
- Lista de fricciones con ajuste propuesto.
- Definición de umbral de change control.

**Errores comunes:**
- Mantener WBS y Product Backlog en paralelo.
- Reportar al portafolio en % de plan base en vez de incrementos entregados.
- PM priorizando el backlog.
- Crear un comité de cambio para cada historia del sprint.

**Referencias cruzadas:**
- [4.2.3 Niveles de planeamiento](./03-niveles-de-planeamiento.md)
- [4.1.6 Metodologías y Estándares para la Gestión de Proyectos](../fundamentos-de-proyectos/06-metodologias-proyectos.md)
- [4.3.4 Medición con OKRs](../ciclo-del-proyecto/04-medicion-con-okrs.md)
</div>

---

<AuthorCredit />
