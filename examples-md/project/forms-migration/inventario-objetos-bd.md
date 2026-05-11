<!-- Destino sugerido: <repo-migracion>/forms-migration/inventario-objetos-bd.md -->

# Inventario de objetos de base de datos

Paquetes, procedimientos, funciones, vistas y triggers del esquema legacy. Producido por las consultas SQL de `scripts-inventario.md § Listar objetos de base`.

## Resumen

| Tipo de objeto | Conteo total | VALID | INVALID |
|---|---|---|---|
| Paquetes | — | — | — |
| Procedimientos | — | — | — |
| Funciones | — | — | — |
| Vistas | — | — | — |
| Triggers | — | — | — |

Cualquier `INVALID` es bandera roja antes de migrar — la pantalla que lo invoca está rota.

## Paquetes, procedimientos y funciones

| Nombre | Tipo | Status | Última DDL | Invocado por |
|---|---|---|---|---|
| `<NOMBRE>` | PACKAGE | VALID | `<YYYY-MM-DD>` | `<form-id>`, `<form-id>` |

## Vistas

| Vista | Status | Consumida por |
|---|---|---|
| `<NOMBRE>` | VALID | `<form-id>` |

## Triggers de base

| Trigger | Tabla | Evento | Status |
|---|---|---|---|
| `<NOMBRE>` | `<TABLA>` | BEFORE INSERT | ENABLED |

## Dependencias relevantes

Cruce de `USER_DEPENDENCIES` que aporta señales al planificar la migración.

| Dependiente | Tipo | Depende de | Tipo | Notas |
|---|---|---|---|---|
| `<PAQ_A>` | PACKAGE | `<VISTA_B>` | VIEW | — |

## Discrepancias con `inventario-fuentes-forms.md`

Objetos en la base que ningún fuente Forms referencia, o referencias en Forms a objetos que no existen.

| Hallazgo | Lado | Hipótesis | Acción |
|---|---|---|---|
| `<PAQ_X>` solo en base | base | Invocado por job programado | Investigar `dba_scheduler_jobs` |
| `<PROC_Y>` solo en `.fmb` | fuente | Procedimiento eliminado, pantalla rota | Confirmar con cliente |

## Cambios

- `<YYYY-MM-DD>` — Inventario inicial.
