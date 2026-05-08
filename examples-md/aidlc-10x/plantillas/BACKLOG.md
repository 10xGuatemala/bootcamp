<!-- Destino sugerido: BACKLOG.md en la raíz del repo de docs del producto -->

# Backlog

Items pendientes que **no pertenecen a ninguna versión específica**. Al planificar una nueva versión, los items relevantes se mueven a `releases/vX.Y.Z.md` (no se duplican: si está en el release, no está en el backlog).

## Convenciones

- Una línea por item, descripción suficiente para que el siguiente triage decida sin re-investigar.
- Cuando se cierre un item al pasarlo a release, marcar `[x] {{descripción}} — vX.Y.Z (req. N.M)` y dejarlo en su sección como histórico (no borrar).
- Items con muchos detalles (ej. diseño que requiere migración compleja) se describen aquí en bloque, no en una línea.

## Deuda Técnica

### {{Área 1, ej. Módulo X — Seguridad y arquitectura}}

- [ ] **{{Título corto}}:** {{descripción de una línea}}. **Archivos:** {{`archivo.cs:linea`, `otro.tsx`}}.
- [ ] **{{Título corto}}:** {{descripción}}. **Corrección:** {{esbozo de la solución, no obligatorio}}.

### {{Área 2}}

- [ ] **{{...}}**

## Mejoras

- [ ] **{{Título}}:** {{descripción de la oportunidad de mejora}}.

## Bugs conocidos sin versión asignada

- [ ] **{{Título}}:** {{síntoma}}. **Reproducir:** {{pasos breves}}. **Archivos sospechosos:** {{...}}.

## Items diferidos de versiones pasadas

> Items que aparecieron en una versión pero se sacaron del scope. Mantener referencia a la versión donde se discutieron.

- [ ] **{{Título}}:** {{descripción + por qué se difirió}}. **Originado:** vX.Y.Z (req. N.M). **Cuando se priorice:** asignar versión nueva.

## Ideas a evaluar

> Sin compromiso de implementación. Si pasa triage en una versión futura, sube a una sección de arriba o directo al `releases/vX.Y.Z.md`.

- {{Idea suelta — una línea}}.
