# examples-md

Colección de artefactos en Markdown que acompañan los módulos del bootcamp. Solo incluye **archivos `.md` que leen o producen agentes de IA** — plantillas de diseño, convenciones de agentes (CLAUDE.md, AGENTS.md), skills, y plantillas de gestión de proyecto.

Todos los archivos llevan la extensión `.md.example` para que no sean interpretados como documentación del sitio, pero son Markdown válido: cópialos a tu repo, renómbralos al nombre real (quitando `.example`) y adáptalos.

## Estructura

```
examples-md/
├── agents/              # Convenciones para agentes de IA
│   ├── CLAUDE.md.example
│   ├── AGENTS.md.example
│   └── skills/
│       ├── general/                         # Skills agnósticas de stack
│       │   ├── code-review.skill.md.example
│       │   ├── release-notes.skill.md.example
│       │   ├── redactar-manual-usuario.skill.md.example
│       │   ├── revisar-hallazgo-sast.skill.md.example
│       │   ├── entrevista-specs.skill.md.example
│       │   └── scaffolding-desde-specs.skill.md.example
│       └── net-core-web-api/                # Skills específicas del curso .NET
│           ├── estructura-proyecto-net.skill.md.example
│           ├── nuevo-endpoint-rest-net.skill.md.example
│           ├── nueva-entidad-ef-core.skill.md.example
│           ├── patrones-diseno-net.skill.md.example
│           └── checklist-produccion-net.skill.md.example
├── design/              # Plantillas UX/UI
│   ├── DESIGN.md.example
│   ├── component-brief.md.example
│   └── screen-spec.md.example
└── project/             # Plantillas de gestión ágil + arranque + release
    ├── specs.md.example                     # Contrato de arranque (Actividad 0)
    ├── release-requirements.md.example      # Requerimientos por versión (release-centric)
    ├── product-backlog.md.example
    ├── sprint-plan.md.example
    ├── sprint-goal.md.example
    └── retrospective.md.example
```

Las skills se organizan por dominio. Las de `general/` aplican a cualquier stack; las de `net-core-web-api/` son específicas del curso [1.2 Desarrollo de Web APIs en .NET Core](../docs/desarrollo-web-y-movil/net-core-web-api/index.md) y asumen .NET 8 + Entity Framework Core.

## Uso

Copia el archivo a tu repositorio respetando el path real sugerido en el comentario de cabecera de cada plantilla. Por ejemplo:

```bash
cp examples-md/agents/CLAUDE.md.example mi-repo/CLAUDE.md
cp examples-md/design/DESIGN.md.example mi-repo/DESIGN.md
```

Los archivos son plantillas **opinadas pero no dogmáticas**: mantén los encabezados y la estructura, reemplaza el contenido por el tuyo.
