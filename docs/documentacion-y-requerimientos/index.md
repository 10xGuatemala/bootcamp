---
sidebar_position: 1
title: Introducción
slug: /documentacion-y-requerimientos
---

# Documentación y Requerimientos

Esta categoría aborda cómo una idea se convierte en un cambio **trazable**, **versionado** y **documentado** — de forma que personas y agentes de IA puedan reconstruir la historia de cualquier entrega.

No es gestión de proyectos (metodología, sprints, equipos). Es **la evidencia escrita** del producto: qué se pidió, qué versión lo incluye, cómo lo usa el usuario final, y cómo se conecta cada pieza.

:::info Glosario mínimo
- **Skill**: instrucción reutilizable que un agente de IA puede invocar (por ejemplo: "redactar requerimiento", "actualizar CHANGELOG"). Se alimenta de plantillas markdown versionadas.
- **SemVer** (*Semantic Versioning*): convención `MAJOR.MINOR.PATCH` para comunicar cambios compatibles vs. incompatibles. Ver [semver.org](https://semver.org/lang/es/).
- **CHANGELOG**: archivo (`CHANGELOG.md`) que lista los cambios entregados por versión en lenguaje humano.
:::

## Por qué separarlo de Gestión de Proyectos

La documentación funcional tiene audiencias, tono y plantillas propias:

| Gestión de Proyectos | Documentación y Requerimientos |
|----------------------|-------------------------------|
| Metodología, roles, sprints | Requerimientos funcionales, CHANGELOG, manuales |
| Equipo técnico y líderes | Usuarios finales, soporte, equipos consumidores |
| "¿Cómo trabajamos?" | "¿Qué entregamos y qué cambió?" |

Mezclarlas confunde a los lectores y complica que un agente genere skills específicas ("redactar requerimiento" vs. "planificar sprint").

## Mapa de la categoría

El flujo no es lineal: cada entrega al usuario final y cada evidencia para el agente/auditoría generan **nuevas ideas** que alimentan el siguiente ciclo.

```mermaid
flowchart LR
    I[Idea] --> R[Requerimiento<br/>módulo 01]
    R --> D[Desarrollo]
    D --> V[Versión<br/>módulo 02]
    V --> M[Manual<br/>módulo 03]
    V --> T[Trazabilidad<br/>módulo 04]
    M --> O[Orquestación<br/>con agentes<br/>módulo 05]
    O --> U[Usuario final]
    T --> A[Agente / auditoría]
    U -.feedback / métricas.-> I
    A -.hallazgos / auditoría.-> I
    classDef node fill:#ffffff,stroke:#0d4d92,stroke-width:2px,color:#0d4d92,rx:8,ry:8
    classDef out fill:#ef9b50,color:#1a2f4d,stroke:#b56a2a,rx:8,ry:8
    class I,R,D,V,M,T,O node
    class U,A out
```

Las flechas punteadas cierran el ciclo: sin retroalimentación desde producción y auditoría, la documentación se convierte en una línea recta que no aprende.

## Módulos del curso

<DocCardList />

<div className="github-only-toc">

**Contenido:**

- [5.1 De la idea al release](./01-de-la-idea-al-release.md)
- [5.2 Versionado semántico en equipos](./02-versionado-semantico-en-equipos.md)
- [5.3 Manuales de usuario final](./03-manuales-de-usuario-final.md)
- [5.4 Trazabilidad requerimiento → release](./04-trazabilidad-requerimiento-release.md)
- [5.5 Generación de manuales con agentes](./05-generar-manuales-con-agentes.md)

</div>

<AuthorCredit />
