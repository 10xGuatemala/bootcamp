<!-- Destino sugerido: <repo-migracion>/forms-migration/inventario-fuentes-forms.md -->

# Inventario de fuentes Forms

Inventario crudo de archivos fuente de Oracle Forms y Reports presentes en el legacy. Producido por el script `scripts-inventario.md § Listar fuentes`.

## Resumen

| Tipo | Conteo | Última actualización |
|---|---|---|
| `.fmb` (formularios) | — | — |
| `.pll` (librerías) | — | — |
| `.mmb` (menús) | — | — |
| `.rdf` (reportes) | — | — |

## Detalle

Una fila por archivo, ordenado alfabéticamente por ruta.

| Archivo | Tipo | Tamaño | Última modificación | Notas |
|---|---|---|---|---|
| `<ruta/archivo.fmb>` | fmb | `<KB>` | `<YYYY-MM-DD>` | — |
| `<ruta/archivo.pll>` | pll | `<KB>` | `<YYYY-MM-DD>` | Compartida por N pantallas (ver `backlog-migracion-pantallas.md`) |

## Preguntas abiertas

Archivos encontrados que ningún área de negocio reconoce, o archivos esperados que no aparecen. Cada pregunta queda abierta hasta resolverse con el cliente.

- [ ] `<archivo>` — `<contexto del hallazgo>` — responsable: `<nombre>` — fecha apertura: `<YYYY-MM-DD>`

## Cambios

Una línea por modificación del inventario.

- `<YYYY-MM-DD>` — Inventario inicial generado con `scripts-inventario.md § Listar fuentes`.
