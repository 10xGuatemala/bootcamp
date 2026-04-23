---
id: index
sidebar_position: 1
---

# Desarrollo de Web APIs en .NET Core

Crear APIs web eficientes y seguras es esencial para cualquier aplicación moderna. En este curso, exploraremos cómo desarrollar Web APIs con .NET Core siguiendo buenas prácticas generales que aseguren mantenibilidad, escalabilidad y seguridad. Si estás comenzando o deseas mejorar tus habilidades, este curso es clave para iniciarte.

:::info Alcance: implementación, no requerimientos
Los ejemplos de este módulo — clases, atributos, DTOs, `DbContext`, inyección de dependencias — pertenecen al **Cómo** (diseño técnico e implementación). No son historias de usuario ni requerimientos funcionales; son la **solución técnica** que sigue al *Qué* definido en [5. Documentación y requerimientos](../../documentacion-y-requerimientos/01-de-la-idea-al-release.md).

Cuando veas un controlador con `[Authorize(Roles = "...")]` o un servicio con un `DbContext` inyectado, interpreta esos detalles como **ejemplos de implementación** para un requerimiento ya aprobado — no como la forma correcta de describir un requerimiento.
:::

:::tip Skills copiables para este curso
Tres skills específicas de .NET 8 + EF Core viven en [`examples-md/agents/skills/net-core-web-api/`](https://github.com/10xGuatemala/bootcamp/tree/main/examples-md/agents/skills/net-core-web-api):

- **`nuevo-endpoint-rest-net`** — agregar un endpoint nuevo con controller delgado, servicio, DTOs, autorización y convenciones Swagger. Ver módulos [1.2.2](./02-capa-controlador.md) y [1.2.3](./03-capa-servicios.md).
- **`nueva-entidad-ef-core`** — agregar una entidad sincronizando Model, DbContext y scripts DDL/DML, con Data Annotations sobre Fluent API. Ver módulo [1.2.4](./04-capa-datos.md).
- **`checklist-produccion-net`** — verificar alineación de versión entre `.csproj`, CHANGELOG y `/health`, con variante multi-repo (API + Web + BD) cuando el producto vive en varios repositorios. Ver también [5.2 Versionado semántico en equipos](../../documentacion-y-requerimientos/02-versionado-semantico-en-equipos.md).

Cópialas a `.claude/skills/` de tu repo y cualquier agente (Claude Code, Cursor, Codex) podrá invocarlas por nombre.
:::

## Módulos del curso

<DocCardList />

<div className="github-only-toc">

**Contenido:**

- [1.2.0 Recursos para el Desarrollo de Web APIs en .NET](./00-recursos-para-desarrollo-net.md)
- [1.2.1 Arquitectura de Backend API Rest en .NET Core](./01-arquitectura-de-backend.md)
- [1.2.2 La Capa de Controlador en un Backend API REST](./02-capa-controlador.md)
- [1.2.3 La Capa de Servicios en un Backend API REST](./03-capa-servicios.md)
- [1.2.4 La Capa de Datos en un Backend API REST](./04-capa-datos.md)
- [1.2.5 Patrones de diseño en APIs REST](./05-patrones-de-diseno-en-apis-rest.md)

</div>
