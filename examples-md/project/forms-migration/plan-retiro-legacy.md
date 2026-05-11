<!-- Destino sugerido: <repo-migracion>/forms-migration/plan-retiro-legacy.md -->

# Plan de retiro del legacy

El proyecto de migración no termina con la última pantalla migrada; termina cuando el código Forms ya no opera y los artefactos heredados al equipo DBA están documentados. Este plan se redacta al inicio del proyecto y se actualiza en cada cutover.

## Pantallas que sobreviven en legacy

Decisiones explícitas de **no** migrar una pantalla. Cada entrada exige razón firmada por el responsable técnico y de negocio.

| `form-id` | Razón para no migrar | Modo de operación residual | Aprobada por | Fecha |
|---|---|---|---|---|
| `<reporte-fin-anio>` | Ejecutado una vez al año por contabilidad; costo de migración no se paga | Forms queda accesible solo desde estaciones de contabilidad, en modo solo lectura | `<nombre>` | `<YYYY-MM-DD>` |

## Bibliotecas `.pll` huérfanas

Librerías Forms cuyos consumidores ya están todos migrados o retirados.

| Librería | Última pantalla que la usó | Procedimientos asociados | Acción | Fecha |
|---|---|---|---|---|
| `<utilidades-legacy.pll>` | `<form-id>` (cutover 2026-06-15) | `validar_fecha`, `formato_moneda` | Retirar junto con la pantalla | `<YYYY-MM-DD>` |

## Objetos heredados al equipo DBA

Vistas, procedimientos y triggers que el sistema nuevo invoca y que el equipo DBA mantiene a partir del cutover.

| Objeto | Tipo | Invocado por | Responsable DBA | Documentación |
|---|---|---|---|---|
| `<vista_envios_pendientes>` | VIEW | `EnvioService` | `<nombre>` | `<enlace al runbook>` |
| `<siguiente_correlativo>` | PROCEDURE | Todos los servicios que generan correlativos | `<nombre>` | `<enlace>` |

## Ventana de respaldo en frío

Tras el último cutover, el código Forms queda en *cold storage* por un período acordado.

- **Inicio:** `<YYYY-MM-DD>` *(fecha del último cutover)*
- **Duración:** `<30 | 60 | 90>` días
- **Ubicación:** `<servidor o ruta de respaldo>`
- **Criterio de archivo definitivo:** cero incidentes atribuibles al sistema nuevo durante la ventana.
- **Procedimiento de rollback:** `<enlace al runbook>` — qué hacer si aparece una regla no detectada.

## Cierre del proyecto

El proyecto cierra cuando:

- [ ] Todas las pantallas en `backlog-migracion-pantallas.md` están en estado `Cutover` o `Pospuesta` con razón firmada.
- [ ] Las bibliotecas `.pll` huérfanas listadas arriba están retiradas.
- [ ] El equipo DBA aceptó por escrito la lista de objetos heredados.
- [ ] La ventana de cold storage venció sin incidentes.
- [ ] Se ejecutó retrospectiva final y quedó archivada.

## Cambios

- `<YYYY-MM-DD>` — Plan inicial.
