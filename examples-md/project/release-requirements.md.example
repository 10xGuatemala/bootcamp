<!-- Destino sugerido: docs/requerimientos/vX.Y.Z.md -->

# vX.Y.Z — {{título descriptivo de la versión}}

**Estado:** Pendiente | En desarrollo | En pruebas | Publicado - YYYY-MM-DD
**Fecha objetivo:** YYYY-MM-DD
**Branch:** main (en {{lista de repos afectados}})

> Esta plantilla es **release-centric**, no sprint-centric: agrupa los cambios por versión que se publica en producción. Complementa (no reemplaza) a `sprint-plan.md` y `product-backlog.md`. Un sprint puede cerrar 0, 1 o N versiones; una versión puede cerrarse con trabajo de N sprints.

## Cuándo usar esta plantilla

- El producto se versiona con SemVer y se libera a producción con cadencia independiente del sprint.
- Hay múltiples repos que se coordinan en un mismo release (ej. API + Web + Docs).
- Hay migraciones de base de datos u otros cambios de infraestructura que requieren orden explícito.
- Soporte y operaciones necesitan un documento por versión para saber qué salió y cuándo.

## Requerimientos

### {{Módulo 1}}

#### X.Y {{Título del requerimiento}}

- **Tipo:** Feature | Bugfix | Mejora | Refactor
- **Repos:** API | Web | API + Web | Docs | Multi
- **Migración BD:** Sí (`migrations/vX.Y.Z/001-descripcion.sql`) | No
- **Estado:** [ ] Pendiente
- **Descripción:** Descripción con contexto suficiente para que alguien ajeno al sprint (o un agente de IA) pueda implementar o revisar el cambio sin más insumos.
- **Criterio de aceptación:** Qué debe ocurrir para cerrar este item.

#### X.Y {{Otro requerimiento}}

- **Tipo:** ...
- ...

### {{Módulo 2}}

#### ...

## Migraciones BD

Scripts a ejecutar **en orden numérico**, documentados conforme se implementan:

- [ ] `Database/migrations/vX.Y.Z/001-{{descripcion}}.sql`
- [ ] `Database/migrations/vX.Y.Z/002-{{otra-migracion}}.sql`

Todos los scripts deben ser **idempotentes** (`IF NOT EXISTS`, `IF EXISTS`) para permitir re-ejecución segura en ambientes de prueba.

## Cambios visibles al usuario

Lista de cambios que el manual de usuario debe reflejar. Enlaza al CHANGELOG público y a la sección del manual afectada. Si no hay cambios visibles, indicar "Ninguno".

- {{Cambio 1}} → actualizar sección del manual de `{{sistema}}/{{rol}}/NN-{{tarea}}.md`.
- {{Cambio 2}} → nueva sección en el manual.

## Decisiones de diseño

Contexto que no cabe en el CHANGELOG pero que conviene registrar para el futuro:

- {{Por qué se eligió X sobre Y}}.
- {{Dependencia entre este release y el siguiente}}.
- {{Limitación conocida aceptada}}.

## Dependencias entre requerimientos

Si algunos items deben implementarse en orden, listarlo explícitamente:

1. {{X.1}} debe cerrar antes que {{X.3}} porque…
2. {{Migración 001}} debe correr antes del deploy de la API nueva.

## Checklist de release

(Copiar desde `RELEASING.md` o equivalente al cortar la versión.)

### Pre-release
- [ ] Todos los items de arriba cerrados.
- [ ] CHANGELOG actualizado en cada repo afectado.
- [ ] Versión bumpeada en todos los manifests (`package.json`, `.csproj`, `pyproject.toml`, etc.).
- [ ] Migraciones probadas localmente contra copia de datos de producción.

### Despliegue
- [ ] Backup de BD.
- [ ] Scripts de migración aplicados en orden.
- [ ] Servicios desplegados en orden (BD → API → Web).
- [ ] Health check OK con versión correcta.

### Post-release
- [ ] Tags SemVer creados en cada repo que cambió.
- [ ] Estado del documento actualizado a `Publicado - YYYY-MM-DD`.
- [ ] Manuales de usuario actualizados si hay cambios visibles.
- [ ] Verificación de funcionalidad crítica en producción.

## Notas

Contexto adicional, decisiones tomadas durante el release, problemas encontrados y cómo se resolvieron. Útil para retrospectiva y para el próximo release.
