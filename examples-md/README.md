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
│       └── net-core-web-api/                # Skills específicas del curso .NET
│           ├── estructura-proyecto-net.skill.md
│           ├── nuevo-endpoint-rest-net.skill.md
│           ├── nueva-entidad-ef-core.skill.md
│           ├── patrones-diseno-net.skill.md
│           └── checklist-produccion-net.skill.md
├── design/              # Plantillas UX/UI
│   ├── DESIGN.md
│   ├── component-brief.md
│   └── screen-spec.md
└── project/             # Plantillas de gestión ágil + arranque + release
    ├── specs.md                     # Contrato de arranque (Actividad 0)
    ├── release-requirements.md      # Requerimientos por versión (release-centric)
    ├── product-backlog.md
    ├── sprint-plan.md
    ├── sprint-goal.md
    └── retrospective.md
```

Las skills se organizan por dominio. Las de `general/` aplican a cualquier stack; las de `net-core-web-api/` son específicas del curso [1.2 Desarrollo de Web APIs en .NET Core](../docs/desarrollo-web-y-movil/net-core-web-api/index.md) y asumen .NET 8 + Entity Framework Core.

## Uso

Copia el archivo a tu repositorio respetando el path real sugerido en el comentario de cabecera de cada plantilla. Por ejemplo:

```bash
cp examples-md/agents/CLAUDE.md mi-repo/CLAUDE.md
cp examples-md/design/DESIGN.md mi-repo/DESIGN.md
```

Los archivos son plantillas **opinadas pero no dogmáticas**: mantén los encabezados y la estructura, reemplaza el contenido por el tuyo.
