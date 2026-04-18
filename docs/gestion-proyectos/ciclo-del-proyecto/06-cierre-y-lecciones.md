---
sidebar_position: 6
title: Cierre y lecciones aprendidas
sidebar_label: 4.3.6 Cierre y lecciones aprendidas
---

# Cierre y lecciones aprendidas

En Scrum "puro", el proyecto no tiene cierre — solo hay producto con vida continua. Pero en la realidad de muchas organizaciones, los proyectos sí cierran: se entregan a operación, se cierra el contrato, el equipo se disuelve, el producto pasa a *mantenimiento*. Y ese cierre merece un ritual explícito.

## Cuándo un proyecto "cierra" de verdad

Distinguir tres situaciones que suelen confundirse:

| Situación | Qué hacer |
|---|---|
| **Fin de release** | No cierra el proyecto — solo se hace una Sprint Review ampliada y se sigue. |
| **Transición a operación** | Cierra el proyecto como iniciativa. Se arma un handover formal. |
| **Cancelación** | El proyecto se detiene por decisión de negocio. Cierre honesto, lecciones capturadas. |

La cancelación **no es fracaso** — a veces es la decisión más sabia. Lo que sí es fracaso es prolongar un proyecto muerto durante meses antes de reconocerlo.

## Checklist de cierre técnico

Antes de declarar un proyecto cerrado:

```markdown
# Cierre — Proyecto X

## Código y repos

- [ ] Repositorio etiquetado con versión final (`vX.Y.Z`)
- [ ] CHANGELOG.md al día hasta el último release
- [ ] Branches de trabajo eliminadas o archivadas
- [ ] README actualizado: estado del proyecto, owner de mantenimiento

## Infraestructura

- [ ] Ambientes productivos documentados (URL, credenciales, contactos)
- [ ] Backups configurados y verificados
- [ ] Monitoring y alertas con destinatarios actualizados
- [ ] Dependencias externas listadas (APIs, servicios, licencias que vencen)

## Documentación

- [ ] Manual de usuario final publicado
- [ ] Documentación técnica (arquitectura, runbooks) en repo
- [ ] ADRs (decisiones arquitectónicas) completos
- [ ] Requerimientos firmados por release en `docs/release/`

## Operación

- [ ] Equipo de operación / soporte capacitado
- [ ] Runbook de incidentes con escalamiento definido
- [ ] Contacto de respaldo del equipo original
- [ ] Ventana de soporte acordada (semanas / meses de garantía)

## Contrato / negocio

- [ ] Acta de entrega firmada
- [ ] Facturación al día
- [ ] Licencias transferidas si aplica
- [ ] Propiedad intelectual documentada
```

## Post-mortem del proyecto

Distinto a una retrospectiva de sprint: el post-mortem **mira todo el proyecto**.

### Plantilla

Copia a `docs/projects/NOMBRE-postmortem.md`:

```markdown
# Post-mortem — Proyecto X

**Fecha de cierre:** YYYY-MM-DD
**Duración total:** N meses
**Equipo:** [roles y nombres]
**Stakeholders:** [patrocinador, cliente final]

## Resumen ejecutivo

[2–3 párrafos. Qué se entregó, qué problema resolvió, qué cambió.]

## Resultados vs. objetivos iniciales

| OKR / Objetivo | Target | Logrado | Nota |
|----------------|--------|---------|------|
| … | … | … | … |

## Línea de tiempo

- Mes 1 — [hito]
- Mes 2 — [hito]
- …

## Qué funcionó

- [Práctica, decisión o factor que sumó]
- …

## Qué no funcionó

- [Sin rodeos. Lo que costó caro, lo que no se repetiría]
- …

## Decisiones clave y su resultado

| Decisión | Cuándo | Resultado |
|----------|--------|-----------|
| [Stack elegido] | Mes 1 | Bien / Mal / Neutro |
| [Cambio de alcance en mes 3] | Mes 3 | … |

## Lecciones transferibles

- [Aprendizaje que aplica a futuros proyectos del equipo / organización]
- …

## Métricas finales del proyecto

- Historias entregadas: N
- Incidentes en producción: N
- Satisfacción del usuario final: X/10
- Presupuesto vs. plan: ±X%
```

## Lecciones aprendidas ≠ documento muerto

El error clásico: el *post-mortem* se escribe, se guarda en una carpeta, y nadie lo lee para el siguiente proyecto. Algunas prácticas que ayudan:

- **Indexar las lecciones por tema** (no por proyecto). Un equipo nuevo busca *"autenticación"* o *"integración con terceros"*, no *"Proyecto X de 2022"*.
- **Revisar post-mortems pasados al planear un nuevo proyecto**. Agregar como paso del *kickoff*.
- **Corto y punzante gana a largo y exhaustivo**. Un post-mortem de 3 páginas que se lee vale más que uno de 30 que se archiva.

## Las 18 causas recurrentes de fracaso

Una lista recopilada de post-mortems de proyectos IT (adaptada de bibliografía clásica de gestión de proyectos). Si tu post-mortem menciona una de estas sin un plan para el siguiente proyecto, la lección no está realmente capturada:

1. No estábamos tratando el problema correcto.
2. Diseñamos lo que no era.
3. Utilizamos la tecnología equivocada.
4. No diseñamos una buena agenda para el proyecto.
5. No contábamos con el patrocinador adecuado.
6. El equipo no congeniaba.
7. No involucramos a la gente adecuada.
8. No comunicamos adecuadamente lo que estábamos haciendo.
9. No prestamos atención a los riesgos del proyecto ni a las cuestiones de administración.
10. El proyecto costó mucho más de lo que se esperaba.
11. No comprendimos ni informamos del progreso de acuerdo con el plan.
12. Intentamos hacer demasiado.
13. No realizamos suficientes pruebas.
14. No supimos adiestrar al cliente.
15. No "tiramos del enchufe" del proyecto cuando deberíamos haberlo hecho.
16. Tropezamos en la línea de meta.
17. El proveedor no cumplió con lo que debía.
18. No teníamos un plan B por si el producto fallaba.

**Uso práctico:** al iniciar un nuevo proyecto, repasar esta lista con el equipo y preguntar *"¿cuáles de estas son más probables en nuestro contexto?"*. Las respuestas alimentan el registro de riesgos inicial.

## Celebrar el cierre

Sí, explícitamente: celebrar. Un proyecto que se entregó merece reconocimiento al equipo. Esto no es cosmético — marca transición, da sensación de logro y facilita que el equipo se prepare para lo siguiente.

Si el proyecto fue cancelado, **también se reconoce**: decidir parar a tiempo es una virtud, no una derrota.

## Glosario

**Handover** *(Handover / Transition)* — transferencia formal del producto al equipo de operación o soporte. En la [PMBOK Guide](https://www.pmi.org/pmbok-guide-standards) forma parte del grupo de procesos de *Closing*: entrega de documentación, capacitación y canales de contacto.

**Post-mortem** *(Post-mortem / Project Retrospective)* — análisis retrospectivo del proyecto completo que captura qué funcionó y qué no; insumo principal del proceso *Lessons Learned* descrito por el [PMI](https://www.pmi.org/learning/library/lessons-learned-next-level-communicating-7991).

**Runbook** *(Runbook)* — guía operativa con procedimientos paso a paso para incidentes y tareas recurrentes en producción; práctica SRE ampliamente adoptada y mencionada en estándares de operaciones.

**ADR** *(Architecture Decision Record)* — registro breve de una decisión arquitectónica, su contexto y consecuencias; formato popularizado por Michael Nygard para mantener trazabilidad del *por qué* detrás de la arquitectura.

**Ventana de soporte** *(Support Window / Warranty Period)* — período acordado tras la entrega durante el cual el equipo original atiende dudas o defectos; habitualmente definido en el *Project Charter* o contrato ([PMBOK Guide](https://www.pmi.org/pmbok-guide-standards)).

**Cancelación** *(Project Termination / Early Closure)* — decisión formal de detener un proyecto antes de completar su alcance; la [PMBOK Guide](https://www.pmi.org/pmbok-guide-standards) la reconoce como resultado válido del cierre cuando el caso de negocio deja de sustentar la inversión. El PMI enfatiza capturar lecciones aprendidas incluso en cierre temprano ([PMI · Lessons Learned](https://www.pmi.org/learning/library/lessons-learned-next-level-communicating-7991)).

:::info Referencias primarias
- [PMBOK Guide séptima edición (PMI)](https://www.pmi.org/pmbok-guide-standards) — procesos y entregables de cierre de proyecto.
- [PMI · Lessons Learned: Taking It to the Next Level](https://www.pmi.org/learning/library/lessons-learned-next-level-communicating-7991) — metodología canónica para capturar lecciones.
- [Scrum Guide 2020 (español europeo)](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Spanish-European.pdf) — referencia complementaria sobre inspección y adaptación.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** cerrar un proyecto con handover técnico completo y post-mortem útil para el futuro.

**Entradas:**
- Estado actual de repositorios, infraestructura y documentación.
- OKRs / objetivos iniciales del proyecto.
- Retros y decisiones clave del proyecto (ADRs si existen).

**Pasos:**
1. Correr checklist de cierre técnico; marcar pendientes y asignar owners.
2. Redactar post-mortem siguiendo la plantilla: resumen, línea de tiempo, qué funcionó / no funcionó, lecciones transferibles.
3. Indexar el post-mortem por tema (no solo por fecha) en el repositorio de conocimiento.
4. Si hay transición a operación, confirmar capacitación y runbook.
5. Agendar celebración o reconocimiento del equipo.

**Salidas:**
- `docs/projects/NOMBRE-cierre-checklist.md` completo.
- `docs/projects/NOMBRE-postmortem.md` con plantilla llena.
- Referencia del post-mortem añadida al índice general de lecciones.

**Errores comunes:**
- Declarar cierre sin checklist técnico completo.
- Post-mortem sin "qué no funcionó" por miedo político.
- Lecciones archivadas sin indexar por tema.
- Cancelación presentada como fracaso en vez de decisión acertada.

**Referencias cruzadas:**
- [4.3.5 Retrospectivas](./05-retrospectivas.md)
- [4.3.4 Medición con OKRs](./04-medicion-con-okrs.md)
- [5.3 Manuales de usuario final](../../documentacion-y-requerimientos/03-manuales-de-usuario-final.md)
- [5.4 Trazabilidad requerimiento → release](../../documentacion-y-requerimientos/04-trazabilidad-requerimiento-release.md)
</div>

---

<AuthorCredit />
