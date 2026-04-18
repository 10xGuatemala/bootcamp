---
sidebar_position: 0
title: Introducción
slug: /gestion-proyectos/ciclo-del-proyecto
---

# Ciclo del proyecto

Esta sección recorre el ciclo completo de ejecución de un proyecto ágil: del problema al cierre. Cada módulo incluye **plantillas listas** para copiar a tu repositorio y adaptar al contexto de tu equipo.

```mermaid
flowchart LR
    P[Plan<br/>problema + alcance] --> B[Backlog<br/>historias priorizadas]
    B --> S[Sprint<br/>planning + daily]
    S --> M[Métricas<br/>OKRs]
    M --> R[Retro<br/>mejora continua]
    R --> C[Cierre<br/>lecciones]
    classDef step fill:#0d4d92,color:#fff,stroke:#0b417b,rx:8,ry:8
    class P,B,S,M,R,C step
```

## Módulos

<DocCardList />

<div className="github-only-toc">

**Contenido:**

- [4.3.1 Plan, problema y alcance](./01-plan-problema-alcance.md)
- [4.3.2 Product Backlog](./02-product-backlog.md)
- [4.3.3 Sprint Planning y Daily](./03-sprint-planning-y-daily.md)
- [4.3.4 Medición con OKRs](./04-medicion-con-okrs.md)
- [4.3.5 Retrospectivas](./05-retrospectivas.md)
- [4.3.6 Cierre y lecciones aprendidas](./06-cierre-y-lecciones.md)

</div>

---

<AuthorCredit />
