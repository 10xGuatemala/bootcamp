<!-- Destino sugerido: <repo-migracion>/forms-migration/backlog-migracion-pantallas.md -->

# Backlog de migración de pantallas

Cola priorizada de pantallas Forms a migrar. Cada fila enlaza al contrato detallado en `pantallas/<form-id>.md`.

## Criterio de priorización

Las pantallas se ordenan por una combinación de tres factores:

1. **Riesgo de la migración** (alto = pantalla compleja, dependencias múltiples, integraciones externas).
2. **Valor de tenerla migrada** (alto = pantalla usada a diario por el negocio, o bloqueante para retirar Forms).
3. **Aprendizaje generado** (alto = la pantalla revela patrones que se repetirán en pantallas posteriores).

Empezar por **bajo riesgo + alto aprendizaje** entrena al equipo sin comprometer la operación. Dejar **alto riesgo + alto valor** para mitad del proyecto, cuando el equipo ya tiene tracción.

## Cola

| Orden | `form-id` | Riesgo | Valor | Aprendizaje | Estado | Sprint objetivo | Contrato |
|---|---|---|---|---|---|---|---|
| 1 | `<catalogo-paises>` | Bajo | Bajo | Alto | Cutover | S1 | [pantallas/catalogo-paises.md](./pantallas/catalogo-paises.md) |
| 2 | `<consulta-clientes>` | Bajo | Medio | Alto | Paralelo | S2 | [pantallas/consulta-clientes.md](./pantallas/consulta-clientes.md) |
| 3 | `<envios-recepcion>` | Alto | Alto | Medio | Implementación | S3-S4 | [pantallas/envios-recepcion.md](./pantallas/envios-recepcion.md) |

## Estados posibles

- **Pendiente** — todavía no se documentó el contrato.
- **Contrato** — `pantallas/<form-id>.md` aprobado por el área de negocio.
- **Implementación** — pantalla nueva en construcción.
- **Paralelo** — ambas pantallas operativas; reconciliación diaria activa.
- **Cutover** — pantalla vieja retirada o en solo lectura.
- **Pospuesta** — decisión explícita de no migrar (acompañada de razón en `plan-retiro-legacy.md § Pantallas que sobreviven en legacy`).

## Avance global

| Total pantallas en `inventario-fuentes-forms.md` | Migradas (cutover) | En paralelo | En implementación | Pendientes |
|---|---|---|---|---|
| `<N>` | `<n>` | `<n>` | `<n>` | `<n>` |

Cualquier afirmación de "llevamos el X %" se mide contra este total — el de `inventario-fuentes-forms.md`, no contra una percepción.

## Cambios

- `<YYYY-MM-DD>` — Backlog inicial.
