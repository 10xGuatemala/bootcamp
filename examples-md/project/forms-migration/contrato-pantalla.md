<!-- Destino sugerido: <repo-migracion>/forms-migration/pantallas/<form-id>.md -->

# Contrato de pantalla — `<form-id>`

Documenta una pantalla Forms antes de migrarla. Una pantalla = un archivo. El `form-id` es estable y aparece en `backlog-migracion-pantallas.md` y en commits relacionados.

## Identidad

- **`form-id`:** `<envios-recepcion>` *(kebab-case sin acentos, único en el proyecto)*
- **Archivo `.fmb`:** `<ruta/al/formulario.fmb>`
- **Título visible en Forms:** `<Recepción de envíos>`
- **Área de negocio responsable:** `<Operaciones / Logística>`
- **Criticidad operativa:** `<Alta | Media | Baja>` *(alta = la pantalla detenida frena ingresos / cumplimiento regulatorio)*

## Datos

### Lee de

| Origen | Tipo | Notas |
|---|---|---|
| `<VISTA_X>` | view | Filtra por usuario logueado vía `USER` |
| `<PAQUETE.FUNCION>` | function | Devuelve catálogo de tipos |

### Escribe en

| Destino | Tipo | Operación | Notas |
|---|---|---|---|
| `<TABLA_Y>` | table | INSERT | Correlativo generado por `siguiente_correlativo('ENVIO')` |
| `<TABLA_AUDIT>` | table | INSERT | Auditoría de usuario, fecha, IP |

## Reglas de negocio aplicadas

Listar las reglas con su id de `triggers-clasificados.md § Reglas extraídas`.

- **RN-001** — Correlativo no puede ser menor que el último emitido.
- **RN-007** — Si el cliente está inactivo, no se permite envío.

## Salidas a otros sistemas

| Destino | Mecanismo | Formato | Notas |
|---|---|---|---|
| Servidor SFTP del operador logístico | Archivo en directorio acordado | EDIFACT IFCSUM | Nombre `IFCSUM-<correlativo>.txt` |

## Reportes asociados

| Reporte | Archivo | Notas |
|---|---|---|
| `<rep-recibo-envio.rdf>` | Reports Builder | Migra a APEX Reports / SSRS / equivalente en el target |

## Bibliotecas `.pll` que consume

| Librería | Uso |
|---|---|
| `<utilidades.pll>` | Formato de fechas, validaciones genéricas |

## Criterio de cutover

La pantalla nueva puede sustituir a la vieja cuando:

- [ ] La reconciliación diaria reporta cero discrepancias durante al menos `<N>` días hábiles consecutivos.
- [ ] El área de negocio firmó aceptación de UAT.
- [ ] Las reglas RN-XXX listadas están todas verificadas con prueba automatizada.
- [ ] Los reportes asociados generan salida byte-equivalente (o equivalente acordado) a los del legacy.

## Riesgos identificados

- `<Regla regulatoria sobre numeración de envíos cambia en 2027 — verificar antes de migrar>`

## Estado

| Fase | Estado | Fecha | Responsable |
|---|---|---|---|
| Contrato documentado | Aprobado | `<YYYY-MM-DD>` | `<nombre>` |
| Pantalla nueva implementada | En curso | — | — |
| Operación en paralelo | — | — | — |
| Reconciliación estable | — | — | — |
| Cutover por pantalla | — | — | — |
