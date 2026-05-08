<!-- Destino sugerido: copiar a releases/vX.Y.Z.md y completar -->

# vX.Y.Z — {{Título descriptivo de la versión}}

**Estado:** Pendiente | En desarrollo | En pruebas | Publicado - YYYY-MM-DD
**Fecha objetivo:** YYYY-MM-DD
**Branch:** dev (en {{lista de repos afectados}})

{{MAJOR | MINOR | PATCH}} (SemVer): {{una línea explicando por qué este nivel — ej. "nueva opción visible al usuario, sin breaking changes ni cambios en el contrato API"}}.

{{Párrafo de 2–4 líneas que describe el alcance de la versión a alto nivel. Es el resumen que un humano ajeno al sprint debe poder leer y entender qué entra en esta versión.}}

## Requerimientos

### {{Módulo 1}}

#### 1.1 {{Título del requerimiento}}

- **Tipo:** Feature | Bugfix | Mejora | Refactor
- **Repos:** API | React | API + React | Docs | Multi
- **Migración BD:** Sí (`Database/migrations/vX.Y.Z/001-descripcion.sql`) | No | Posible
- **Estado:** [ ] Pendiente
- **Descripción:**

  {{Descripción detallada con todo el contexto que un agente o ingeniero ajeno a la conversación necesita para implementar el requerimiento. Si la descripción depende de cosas dichas en chat, falla la prueba — reescribir.}}

  **{{Repo afectado, ej. React}}:**
  - {{Archivo o módulo a tocar}}: {{qué cambia y por qué}}.
  - {{Otro archivo}}: {{detalle}}.

  **{{Otro repo, ej. API}}:**
  - {{...}}

- **Criterio de aceptación:** {{qué prueba el humano para cerrar este item — frase verificable}}.

#### 1.2 {{Título del siguiente requerimiento}}
- **Tipo:** ...
- **Repos:** ...
- **Migración BD:** ...
- **Estado:** [ ] Pendiente
- **Descripción:** ...
- **Criterio de aceptación:** ...

### {{Módulo 2}}

#### 2.1 {{...}}
...

## Migraciones BD

- [ ] `Database/migrations/vX.Y.Z/001-descripcion.sql` — {{una línea de qué hace}}.
- [ ] `Database/migrations/vX.Y.Z/002-otra.sql` — {{...}}.

> Si esta versión no requiere migraciones, escribir explícitamente: *Ninguna requerida.*

## Estrategia de ejecución

- {{Orden de implementación si importa, ej. "primero req 1.1 porque 1.2 depende"}}.
- {{Repos afectados y bumps de versión, ej. "Bump frontend 1.14.0 → 1.15.0; bump API 1.14.1 → 1.15.0 por trazabilidad"}}.
- {{Restricciones específicas, ej. "El toggle vive en localStorage, no en backend"}}.

## Notas

- **Precondición:** {{versión previa que debe estar publicada y estable, ej. "v1.14.1 publicado y estable (✅ YYYY-MM-DD)"}}.
- {{Decisiones de diseño relevantes que afectan a más de un requerimiento}}.
- {{Excepciones a principios o gates, si las hay}}.
- {{Posible vX.Y+1: planeo a futuro relacionado con esta versión}}.
