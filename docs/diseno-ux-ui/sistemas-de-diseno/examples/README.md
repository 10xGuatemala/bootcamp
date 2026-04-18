# Ejemplos copiables — Sistemas de Diseño

Esta carpeta contiene los ejemplos que los módulos de la sección **3.2 Sistemas de Diseño e Implementación** referencian. Están en formato `.example` (y `README.md` para este índice) para que se puedan copiar directamente al repo del proyecto que los vaya a usar, renombrando al nombre final.

| Archivo | Usar en | Renombrar a | Módulo de referencia |
|---------|---------|-------------|----------------------|
| `DESIGN.md.example` | Raíz del repo | `DESIGN.md` | [3.2.1 Design tokens](../01-design-tokens-y-estandares.md) |
| `design-tokens.json.example` | Raíz del repo o `src/design/` | `design-tokens.json` | [3.2.1 Design tokens](../01-design-tokens-y-estandares.md) |
| `style-dictionary.config.json.example` | Raíz del repo | `style-dictionary.config.json` | [3.2.1 Design tokens](../01-design-tokens-y-estandares.md) |
| `component-brief.md.example` | `docs/design/briefs/` del proyecto | `[nombre-componente].md` | [3.2.2 Arquitectura de componentes](../02-arquitectura-de-componentes.md) |
| `Button.tsx.example` | `src/ui/Button/` | `Button.tsx` | [3.2.2 Arquitectura de componentes](../02-arquitectura-de-componentes.md) |
| `Button.razor.example` | `Components/Button/` | `Button.razor` | [3.2.2 Arquitectura de componentes](../02-arquitectura-de-componentes.md) |
| `screen-spec.md.example` | `docs/design/specs/` del proyecto | `[nombre-pantalla].md` | [3.2.3 Handoff técnico](../03-handoff-tecnico.md) |
| `prompt-wireframe.txt.example` | Documentación interna | `prompt-wireframe.txt` | [3.2.3 Handoff técnico](../03-handoff-tecnico.md) |
| `prompt-implementar.txt.example` | Documentación interna | `prompt-implementar.txt` | [3.2.3 Handoff técnico](../03-handoff-tecnico.md) |
| `prompt-revision-heuristica.txt.example` | Documentación interna | `prompt-revision-heuristica.txt` | [3.2.3 Handoff técnico](../03-handoff-tecnico.md) |

## Por qué `.example`

La extensión `.example` evita que estos archivos interfieran con el proyecto al que se copian. Por ejemplo, un `DESIGN.md` y un `design-tokens.json` en la raíz de un repo son archivos de producción; los de aquí son plantillas. El sufijo es la señal de que hay que adaptar valores antes de usarlos.

Los archivos son **neutrales** — no están ligados a una marca concreta. Reemplaza los colores, tipografías y nombres por los de tu producto.
