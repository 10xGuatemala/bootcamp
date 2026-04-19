<!-- Destino sugerido: docs/product/backlog.md o herramienta de gestión -->

# Product Backlog — {{producto}}

> Lista ordenada por valor. El tope de la lista es lo próximo a entrar en sprint.

## Convenciones

- **Formato del item**: `[ID] Título — estimación (puntos) — responsable propuesto`
- **Estados**: `idea` · `refinado` · `listo para sprint` · `en progreso` · `hecho`
- Refinamiento: todo item debe estar **refinado** antes de ser candidato a sprint (tiene criterios de aceptación y estimación).

## Épicas activas

### E1 — Autenticación multifactor

Objetivo de negocio: reducir incidentes de cuentas comprometidas.

| ID | Título | Estado | Estimación | Criterios de aceptación |
|----|--------|--------|------------|-------------------------|
| AUTH-12 | Activar TOTP por usuario | refinado | 5 | Usuario puede vincular app autenticadora; backup codes generados; revocación desde perfil |
| AUTH-13 | Forzar MFA en roles admin | idea | ? | — |
| AUTH-14 | Notificación de login nuevo dispositivo | refinado | 3 | Email al usuario con IP, user-agent y timestamp |

### E2 — Mejoras de reporte

| ID | Título | Estado | Estimación | Criterios de aceptación |
|----|--------|--------|------------|-------------------------|
| RPT-22 | Export CSV de ventas mensuales | refinado | 2 | Incluye columnas {A,B,C}; respeta zona horaria del tenant |

## Deuda técnica priorizada

- `TECH-5` Migrar `DataContext` a nueva versión de EF Core (bloqueante para .NET 9).
- `TECH-7` Eliminar `Commons.Legacy.JwtHelper` (reemplazado por `Commons v1.8`).

## Ideas por explorar

- Integración con firma electrónica nacional.
- Modo offline para tableta en sucursales con conectividad intermitente.
