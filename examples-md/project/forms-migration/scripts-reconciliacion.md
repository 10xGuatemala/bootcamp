<!-- Destino sugerido: <repo-migracion>/forms-migration/scripts-reconciliacion.md -->

# Scripts de reconciliación

Esqueleto del job que compara registros creados o modificados por el sistema viejo (Forms) y por el sistema nuevo durante la operación en paralelo. La reconciliación corre diaria, idealmente fuera de horario operativo.

Cero discrepancias durante `N` días hábiles consecutivos (definido en `contrato-pantalla.md § Criterio de cutover`) es la señal de luz verde para el cutover por pantalla.

## Patrón general

```sql
-- Cada pantalla tiene su query de reconciliación. El patrón se repite:
-- 1) Subconsulta de lo que escribió Forms desde la última corrida.
-- 2) Subconsulta de lo que escribió el sistema nuevo en el mismo período.
-- 3) FULL OUTER JOIN para encontrar registros que aparecen en uno y no en otro,
--    o que difieren en campos relevantes.
WITH
  forms_data AS (
    SELECT id, correlativo, monto, fecha_creacion, usuario_creacion
    FROM   <tabla_compartida>
    WHERE  fecha_creacion >= TRUNC(SYSDATE) - 1
      AND  origen_sistema = 'FORMS'
  ),
  nuevo_data AS (
    SELECT id, correlativo, monto, fecha_creacion, usuario_creacion
    FROM   <tabla_compartida>
    WHERE  fecha_creacion >= TRUNC(SYSDATE) - 1
      AND  origen_sistema = 'NUEVO'
  )
SELECT
  COALESCE(f.id, n.id) AS id,
  CASE
    WHEN f.id IS NULL                THEN 'SOLO_NUEVO'
    WHEN n.id IS NULL                THEN 'SOLO_FORMS'
    WHEN f.monto <> n.monto          THEN 'DIFIERE_MONTO'
    WHEN f.correlativo <> n.correlativo THEN 'DIFIERE_CORRELATIVO'
    ELSE 'OK'
  END AS diagnostico,
  f.monto AS monto_forms,
  n.monto AS monto_nuevo,
  f.usuario_creacion AS usuario_forms,
  n.usuario_creacion AS usuario_nuevo
FROM   forms_data f
FULL OUTER JOIN nuevo_data n ON f.id = n.id
WHERE  COALESCE(f.id, n.id) IS NOT NULL
ORDER  BY diagnostico, id;
```

## Salida diaria

El job produce un archivo `reconciliacion-<form-id>-<YYYY-MM-DD>.csv` con la salida del query y un resumen al pie:

```text
form-id: envios-recepcion
fecha: 2026-05-12
total comparado:      1247
OK:                   1247
SOLO_FORMS:              0
SOLO_NUEVO:              0
DIFIERE_MONTO:           0
DIFIERE_CORRELATIVO:     0

veredicto: VERDE
```

Cualquier categoría distinta de `OK` con conteo > 0 dispara veredicto `ROJO` y se investiga antes de la siguiente corrida.

## Cadencia y archivo

- **Cadencia:** diaria, después del cierre operativo.
- **Retención:** los `reconciliacion-*.csv` se archivan al menos durante toda la ventana de cold storage (`plan-retiro-legacy.md § Ventana de respaldo en frío`).
- **Alertas:** un veredicto `ROJO` genera notificación inmediata al responsable técnico de la pantalla.

## Plantilla de bash para automatizar

```bash
#!/usr/bin/env bash
set -euo pipefail

FORM_ID="${1:?form-id requerido}"
FECHA="$(date +%Y-%m-%d)"
OUT_DIR="reconciliacion/${FORM_ID}"
mkdir -p "$OUT_DIR"

OUT="${OUT_DIR}/reconciliacion-${FORM_ID}-${FECHA}.csv"

sqlplus -s "$RECON_USER/$RECON_PASS@$RECON_TNS" <<SQL > "$OUT"
SET PAGESIZE 0 FEEDBACK OFF VERIFY OFF HEADING ON ECHO OFF
SET MARKUP CSV ON DELIMITER ',' QUOTE ON
@queries/reconciliacion-${FORM_ID}.sql
SQL

# Resumir y decidir veredicto.
python3 forms-migration/scripts/resumir-reconciliacion.py "$OUT" >> "$OUT"

if grep -q "veredicto: ROJO" "$OUT"; then
  echo "Reconciliación ROJA para ${FORM_ID} — investigar ${OUT}"
  exit 2
fi
```
