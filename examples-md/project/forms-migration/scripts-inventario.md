<!-- Destino sugerido: <repo-migracion>/forms-migration/scripts-inventario.md -->

# Scripts de inventario

Bloques de código listos para extraer y ejecutar. Cada bloque produce uno de los artefactos de `inventario-fuentes-forms.md` o `inventario-objetos-bd.md`.

## Listar fuentes

```bash
# Listar archivos fuente Forms y Reports presentes en el repositorio.
# Salida: inventario-fuentes-forms.txt — base para la tabla de inventario-fuentes-forms.md
find . \( -name "*.fmb" -o -name "*.pll" -o -name "*.mmb" -o -name "*.rdf" \) \
  -not -path "*/node_modules/*" \
  -not -path "*/.git/*" \
  -printf "%p\t%s\t%TY-%Tm-%Td\n" \
  | sort > inventario-fuentes-forms.txt

# Conteos por tipo (alimenta la tabla "Resumen").
awk -F'\t' '{
  ext = $1; sub(/.*\./, "", ext);
  count[ext]++;
} END {
  for (e in count) printf "%-4s %d\n", e, count[e];
}' inventario-fuentes-forms.txt
```

## Convertir `.fmb` a XML

```bash
# Requiere Oracle Forms Builder instalado y FORMS_PATH configurado.
# Sin Forms Builder, evaluar alternativas como pyforms (parser Python),
# herramientas comerciales (PITSS.CON, AuraPlayer) o reverse engineering
# del binario .fmb si la organización lo permite.
for fmb in $(awk -F'\t' '/\.fmb\t/ {print $1}' inventario-fuentes-forms.txt); do
  frmf2xml "$fmb"
done
```

## Listar objetos de base

Ejecutar contra la base Oracle del legacy con un usuario de solo lectura.

```sql
-- Salida 1: objetos del usuario actual con su estado.
-- Alimenta inventario-objetos-bd.md § "Paquetes, procedimientos y funciones".
SELECT object_type, object_name, status,
       TO_CHAR(last_ddl_time, 'YYYY-MM-DD') AS last_ddl
FROM   user_objects
WHERE  object_type IN ('PACKAGE','PROCEDURE','FUNCTION','VIEW','TRIGGER')
ORDER  BY object_type, object_name;

-- Salida 2: dependencias del esquema actual.
-- Alimenta inventario-objetos-bd.md § "Dependencias relevantes".
SELECT name, type, referenced_name, referenced_type
FROM   user_dependencies
WHERE  referenced_owner = USER
ORDER  BY name, referenced_name;

-- Salida 3: triggers de Forms eventualmente registrados en la base.
SELECT trigger_name, table_name, triggering_event, status
FROM   user_triggers
ORDER  BY table_name, trigger_name;
```

Exportar las tres salidas a CSV con `sqlplus`:

```bash
sqlplus -s "$USUARIO_RO/$CLAVE@$TNS" <<'SQL' > inventario-objetos-bd.csv
SET PAGESIZE 0 FEEDBACK OFF VERIFY OFF HEADING ON ECHO OFF
SET MARKUP CSV ON DELIMITER ',' QUOTE ON
@inventario-objetos-bd.sql
SQL
```

## Extraer triggers de un `.fmb` ya convertido a XML

```bash
# Listar todos los triggers definidos en un formulario.
# Salida: tabla básica para triggers-clasificados.md.
extract_triggers() {
  local xml="$1"
  local form_id
  form_id=$(basename "$xml" .xml)
  python3 - <<PY
import xml.etree.ElementTree as ET
import sys
tree = ET.parse("$xml")
ns = {"f": "http://xmlns.oracle.com/Forms"}
for t in tree.iter():
    if t.tag.endswith("Trigger"):
        name = t.get("Name", "")
        text = (t.findtext(".//{*}TriggerText") or "").strip().replace("\n", " ")
        print(f"$form_id\t{name}\t{text[:120]}")
PY
}

for xml in *.xml; do
  extract_triggers "$xml"
done > triggers-extraidos.tsv
```

El `triggers-extraidos.tsv` es la entrada para clasificar manualmente (o asistido por agente) en `triggers-clasificados.md`.

## Verificar la parametrización tras migrar un trigger

```sql
-- Tras convertir un procedimiento a EXECUTE IMMEDIATE ... USING, verificar
-- que la sentencia llega a Oracle con bind variables.
SELECT sql_text, executions, parse_calls
FROM   v$sql
WHERE  parsing_schema_name = USER
  AND  sql_text LIKE '%<patrón identificador>%'
ORDER  BY last_active_time DESC
FETCH  FIRST 20 ROWS ONLY;
```

El `sql_text` debe mostrar `:bind` (o `:1`, `:2`), no literales. `executions` crece a lo largo del día; `parse_calls` se mantiene bajo.
