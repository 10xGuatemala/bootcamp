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
│       ├── code-review.skill.md.example
│       └── release-notes.skill.md.example
├── design/              # Plantillas UX/UI
│   ├── DESIGN.md.example
│   ├── component-brief.md.example
│   └── screen-spec.md.example
└── project/             # Plantillas de gestión ágil
    ├── product-backlog.md.example
    ├── sprint-plan.md.example
    ├── sprint-goal.md.example
    └── retrospective.md.example
```

## Uso

Copia el archivo a tu repositorio respetando el path real sugerido en el comentario de cabecera de cada plantilla. Por ejemplo:

```bash
cp examples-md/agents/CLAUDE.md.example mi-repo/CLAUDE.md
cp examples-md/design/DESIGN.md.example mi-repo/DESIGN.md
```

Los archivos son plantillas **opinadas pero no dogmáticas**: mantén los encabezados y la estructura, reemplaza el contenido por el tuyo.
