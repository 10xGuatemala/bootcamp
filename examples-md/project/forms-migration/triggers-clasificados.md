<!-- Destino sugerido: <repo-migracion>/forms-migration/triggers-clasificados.md -->

# Triggers de Forms clasificados

Por cada `trigger` encontrado en los `.fmb` (convertidos a XML con `frmf2xml`), su clasificación y destino en la migración. Categorías definidas en [1.4.4 Inventario y extracción desde Oracle Forms](../../../docs/desarrollo-web-y-movil/modernizacion-legacy/04-inventario-y-extraccion-forms.md#paso-3-clasificar-los-triggers-de-forms).

## Resumen por categoría

| Categoría | Conteo | Destino |
|---|---|---|
| Validación de campo | — | Backend (DTO / regla de servicio) |
| Cálculo derivado | — | Backend o columna virtual |
| Llamada a procedimiento | — | Mantener en base; invocar desde backend |
| Navegación (`GO_BLOCK`, `GO_ITEM`) | — | Descartar (lo asume el frontend) |
| Manejo de UI (mensajes, colores) | — | Descartar (lo asume el diseño nuevo) |

## Detalle

Una fila por trigger.

| Formulario | Trigger | Tipo (`WHEN-*`, `KEY-*`, `POST-*`) | Categoría | Regla extraída | Destino concreto | Estado |
|---|---|---|---|---|---|---|
| `<form-id>` | `<NOMBRE_TRIGGER>` | `WHEN-VALIDATE-ITEM` | Validación de campo | El correlativo de envío no puede ser menor que el último emitido | `EnvioService.ValidarCorrelativo()` | Pendiente |
| `<form-id>` | `<NOMBRE_TRIGGER>` | `WHEN-BUTTON-PRESSED` | Llamada a procedimiento | — | `EnvioPkg.confirmar()` (sin cambios) | Pendiente |
| `<form-id>` | `<NOMBRE_TRIGGER>` | `KEY-NEXT-ITEM` | Navegación | — | Descartar | Aprobado |

## Reglas extraídas (catálogo)

Cada regla descubierta queda con un id reusable en el resto del proyecto.

- **RN-001** — El correlativo de envío no puede ser menor que el último emitido. *Origen:* `formulario-envios.fmb` `WHEN-VALIDATE-ITEM` sobre `correlativo`. *Destino:* `EnvioService.ValidarCorrelativo`.
- **RN-002** — `<...>`

## Cambios

- `<YYYY-MM-DD>` — Clasificación inicial.
