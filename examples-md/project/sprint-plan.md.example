<!-- Destino sugerido: docs/sprints/2026-S08-plan.md -->

# Sprint Plan — 2026-S08 (lunes 20-abr a viernes 01-may)

## Sprint Goal

> **En 2 semanas, activaremos TOTP para los roles admin y superadmin**, y el usuario final podrá gestionar sus factores desde su perfil.

## Capacidad

- Equipo: 4 personas.
- Días laborables: 10.
- Capacidad ajustada (feriado 01-may, on-call, reuniones): **28 puntos**.

## Commitments

| ID | Título | Puntos | Responsable | DoD específico |
|----|--------|--------|-------------|----------------|
| AUTH-12 | Activar TOTP por usuario | 5 | @ana | Feature flag + tests + docs |
| AUTH-13 | Forzar MFA en roles admin | 8 | @bruno | Migración segura sin dejar usuarios fuera |
| AUTH-14 | Notificación de login nuevo dispositivo | 3 | @ana | Email renderizado en staging |
| RPT-22 | Export CSV ventas mensuales | 2 | @carlos | Generado para 3 tenants de prueba |
| TECH-7 | Eliminar `JwtHelper` legacy | 3 | @diana | Build verde tras el borrado |
| DOC-4 | Actualizar CLAUDE.md con nuevos flujos MFA | 2 | @diana | PR mergeado |

**Total comprometido: 23 puntos** (margen del 18% para imprevistos).

## Riesgos conocidos

- AUTH-13 toca datos en producción; necesita ventana de mantenimiento.
- @bruno con vacaciones miércoles y jueves de la semana 2 — plan B: @ana cubre.

## Definition of Done (global)

- Código en `main` con PR aprobado por 1 revisor.
- Tests automáticos pasando.
- Observabilidad: logs estructurados en rutas nuevas.
- Documentación actualizada si cambia contrato público.
