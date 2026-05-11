# examples-md

Colección de artefactos en Markdown que acompañan los módulos del bootcamp. Solo incluye **archivos `.md` que leen o producen agentes de IA** — plantillas de diseño, convenciones de agentes (CLAUDE.md, AGENTS.md), skills, y plantillas de gestión de proyecto.

Todos los archivos son Markdown válido y viven fuera de `docs/`, por lo que no se publican como páginas del sitio. Cópialos a tu repo respetando el path sugerido y adáptalos.

## Estructura

```
examples-md/
├── agents/              # Convenciones para agentes de IA
│   ├── CLAUDE.md
│   ├── AGENTS.md
│   └── skills/
│       ├── general/                         # Skills agnósticas de stack
│       │   ├── code-review.skill.md
│       │   ├── release-notes.skill.md
│       │   ├── generar-manual.skill.md
│       │   ├── generar-capturas-manual.skill.md
│       │   ├── generar-manual-completo.skill.md
│       │   ├── revisar-hallazgo-sast.skill.md
│       │   ├── entrevista-specs.skill.md
│       │   ├── escribir-slash-command.skill.md
│       │   └── scaffolding-desde-specs.skill.md
│       ├── net-core-web-api/                # Skills específicas del curso .NET
│       │   ├── estructura-proyecto-net.skill.md
│       │   ├── nuevo-endpoint-rest-net.skill.md
│       │   ├── nueva-entidad-ef-core.skill.md
│       │   ├── patrones-diseno-net.skill.md
│       │   └── checklist-produccion-net.skill.md
│       └── oracle-forms/                    # Skills para modernización de Oracle Forms
│           ├── migracion-forms.skill.md
│           └── consultas-parametrizadas-plsql.skill.md
├── design/              # Plantillas UX/UI
│   ├── DESIGN.md
│   ├── component-brief.md
│   └── screen-spec.md
├── project/             # Plantillas de gestión ágil + arranque + release
│   ├── specs.md                     # Contrato de arranque (Actividad 0)
│   ├── release-requirements.md      # Requerimientos por versión (release-centric)
│   ├── product-backlog.md
│   ├── sprint-plan.md
│   ├── sprint-goal.md
│   ├── retrospective.md
│   └── forms-migration/             # Paquete completo para migración Oracle Forms
│       ├── README.md
│       ├── migrar-forms.slash-command.md
│       ├── scripts-inventario.md
│       ├── scripts-reconciliacion.md
│       ├── inventario-fuentes-forms.md
│       ├── inventario-objetos-bd.md
│       ├── triggers-clasificados.md
│       ├── contrato-pantalla.md
│       ├── backlog-migracion-pantallas.md
│       └── plan-retiro-legacy.md
└── aidlc-10x/           # Ciclo de vida completo (Concepción → Construcción → Operación)
    ├── README.md                    # Cómo instalarlo en un proyecto
    ├── core-workflow.md             # Reglas maestras + frase de activación
    ├── principios.md                # Tenets no negociables
    ├── concepcion.md                # Fase 1
    ├── construccion.md              # Fase 2
    ├── operacion.md                 # Fase 3
    └── plantillas/
        ├── _release-template.md
        ├── BACKLOG.md
        └── RELEASING.md
```

Las skills se organizan por dominio. Las de `general/` aplican a cualquier stack; las de `net-core-web-api/` son específicas del curso [1.2 Desarrollo de Web APIs en .NET Core](../docs/desarrollo-web-y-movil/net-core-web-api/index.md) y asumen .NET 8 + Entity Framework Core; las de `oracle-forms/` acompañan el módulo [1.4.3 Migración desde Oracle Forms](../docs/desarrollo-web-y-movil/modernizacion-legacy/03-migracion-desde-oracle-forms.md) y aplican a equipos DBA modernizando aplicaciones Forms legacy.

`project/forms-migration/` es un paquete autocontenido para proyectos de migración desde Oracle Forms. Se copia entero al repositorio del proyecto y acompaña los módulos [1.4.3](../docs/desarrollo-web-y-movil/modernizacion-legacy/03-migracion-desde-oracle-forms.md), [1.4.4](../docs/desarrollo-web-y-movil/modernizacion-legacy/04-inventario-y-extraccion-forms.md) y [1.4.5](../docs/desarrollo-web-y-movil/modernizacion-legacy/05-consultas-parametrizadas-en-migracion.md). Incluye el slash command `/migrar-forms` y los scripts SQL/bash que el agente ejecuta.

`aidlc-10x/` es un paquete autocontenido que se copia entero al proyecto (típicamente como `aidlc/` en la raíz del repo de docs del producto). Es la versión 10X del [AI-DLC de AWS Labs](https://github.com/awslabs/aidlc-workflows), destilada del flujo de release usado en productos de 10X. Útil cuando un equipo arranca un producto nuevo o quiere formalizar el ciclo de uno existente. Mientras `project/release-requirements.md` y `project/specs.md` son **plantillas individuales**, `aidlc-10x/` es un **flujo completo** que orquesta esas plantillas en tres fases con gates humanos explícitos.

## Uso

Copia el archivo a tu repositorio respetando el path real sugerido en el comentario de cabecera de cada plantilla. Por ejemplo:

```bash
cp examples-md/agents/CLAUDE.md mi-repo/CLAUDE.md
cp examples-md/design/DESIGN.md mi-repo/DESIGN.md
```

Los archivos son plantillas **opinadas pero no dogmáticas**: mantén los encabezados y la estructura, reemplaza el contenido por el tuyo.
