<!-- Destino sugerido: docs/sprints/YYYY-SXX-retro.md -->

# Retrospectiva — {{sprint}}

**Fecha:** YYYY-MM-DD
**Facilitador:** @persona
**Asistentes:** @a, @b, @c, @d

## Contexto (1 línea)

¿Qué comprometimos vs. qué entregamos? ¿Eventos inesperados?

> Ejemplo: Comprometidos 23 pts, entregados 20. Incidente en producción el día 4 consumió ~6 horas del equipo.

## ¿Qué salió bien?

Aprendizajes a **mantener** y compartir con otros equipos.

- **Pair-programming en AUTH-13** redujo riesgo de la migración; sin incidentes en prod.
- **DoD con test en staging** nos atrapó un bug de timezone antes de release.

## ¿Qué no salió bien?

Problemas concretos — sin nombres, con hechos.

- **Retraso en email template** (DOC/Legal): tardó 3 días en aprobarse, bloqueó AUTH-14.
- **Flakiness en tests E2E** volvió a aparecer tras merge del viernes.

## ¿Qué vamos a cambiar?

Acciones **con dueño y fecha**. Si no cumplen SMART, no entran.

| Acción | Dueño | Cuándo | Cómo sabemos que funcionó |
|--------|-------|--------|---------------------------|
| Enviar borrador de emails a Legal en día 1 del sprint | @ana | Próximo sprint | Email aprobado antes del día 3 |
| Quarantine de test flaky `CheckoutE2E.ShouldApplyDiscount` | @diana | Esta semana | 0 builds rojos por ese test en 10 días |

## Métricas del sprint

- Velocidad: 20 / 23 pts (87%).
- Tickets reabiertos: 1.
- Incidentes en producción: 1 (post-deploy AUTH-12, mitigado en 40 min).

## Tendencia (últimos 3 sprints)

| Sprint | Comprometido | Entregado | Incidentes |
|--------|-------------:|----------:|-----------:|
| S06 | 25 | 24 | 0 |
| S07 | 22 | 22 | 0 |
| S08 | 23 | 20 | 1 |
