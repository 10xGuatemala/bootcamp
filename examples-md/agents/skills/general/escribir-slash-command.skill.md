<!-- Destino sugerido: .claude/skills/escribir-slash-command.skill.md -->

---
name: escribir-slash-command
description: Diseña un slash command (o skill compleja) que lee código fuente para generar documentación, reportes o artefactos derivados. Estructura args explícitos, bloque de configuración, proceso de discovery por pasos, reglas de exclusión y checklist de calidad.
---

# Skill: escribir-slash-command

Un slash command que lee archivos del repo y produce un artefacto — un manual, un reporte, un diagrama, un resumen — es como una función pura dentro del toolkit del agente: mismos argumentos, mismo output. El problema es que la mayoría de los comandos que se encuentran en la práctica no son puros. Tienen pasos ambiguos ("revisar el frontend"), paths escondidos en cada paso, defaults que cambian según el mes y reglas implícitas sobre qué dejar afuera. El resultado es un comando que cada ejecución interpreta distinto.

Este skill existe para que, cuando un equipo convierte un proceso manual en un slash command, el comando resultante sea reproducible: misma entrada, mismo output, mismo criterio de exclusión. No depende de qué día corre ni de qué agente lo ejecuta.

## Cuándo invocarme

Úsame cuando:
- Se necesita un comando reutilizable tipo `/nombre <arg1> <arg2>` que **lea múltiples archivos del repo** y genere un artefacto derivado (manual, reporte, diagrama, resumen).
- Hay un proceso manual repetitivo que el equipo hace varias veces y conviene automatizar con un contrato explícito.
- Se va a convertir una skill simple en una skill avanzada con discovery, exclusiones y validación.

**No me uses para:**
- Skills puramente conversacionales sin lectura de código — las convenciones simples de skill bastan.
- Tareas que caben en un prompt de una línea — ahí el skill es ceremonia.
- Automatizaciones que requieren ejecutar procesos largos o integrarse con infraestructura — eso es un tool, no un slash command.

## Entradas

1. **Propósito** del comando en una frase (ej. "generar un manual de usuario segmentado por sistema y rol").
2. **Argumentos** que recibe y sus valores válidos.
3. **Fuentes** que va a leer del repo (archivos de código, plantillas, configuración).
4. **Formato del output** esperado (un archivo, un árbol de carpetas, una tabla markdown).
5. **Criterios de exclusión** conocidos — qué combinaciones de argumentos deben omitir qué contenido.

## Anatomía de un slash command bien diseñado

Seis bloques, en este orden. Faltar cualquiera debilita la reproducibilidad.

### 1. Uso y argumentos

El primer bloque documenta cómo se invoca, con los argumentos explícitos:

```markdown
## Uso

```text
/nombre-comando <arg1> <arg2>
```

Argumentos recibidos: **$ARGUMENTS**

- `<arg1>` — {{qué representa}}. Valores válidos: `valor-a`, `valor-b`, `valor-c`.
- `<arg2>` — {{qué representa}}. Valores válidos: `rol-1`, `rol-2`.

Si se recibe un solo argumento, {{regla de aclaración explícita}}.
Si un argumento está fuera del set válido, detenerse y pedir corrección.
```

- Mal: `/generar-manual usuarios` — el agente "decide" qué usuarios, qué formato, qué profundidad.
- Bien: `/generar-manual ventas operador` — dos ejes explícitos, cada uno con valores válidos declarados.

**Valor para el agente:** argumentos explícitos eliminan la interpretación. Un agente rechaza entradas inválidas con mensaje útil en vez de producir output inconsistente.

### 2. Bloque de configuración

Las rutas y constantes del repo viven en un único bloque al inicio, no dispersas en los pasos:

```markdown
## Configuración

```text
FRONTEND_ROOT  = apps/web/src
BACKEND_ROOT   = services/api/src
OUTPUT_DIR     = docs/manuales
TEMPLATE       = docs/_templates/manual.md
```
```

- Mal: paso 3 dice "leer los archivos en `apps/web/src/components`", paso 5 dice "revisar `src/api/services`". Al reorganizar el repo, el comando se rompe en seis lugares.
- Bien: todos los paths refieren a `{{FRONTEND_ROOT}}` y `{{BACKEND_ROOT}}`. Reorganizar el repo toca una línea.

**Valor para el agente:** el bloque de configuración es una tabla de símbolos. El agente resuelve los paths una vez y los reusa en cada paso sin re-interpretar.

### 3. Tablas de referencia declaradas como guía, no como fuente de verdad

Cuando el comando opera sobre **matrices de combinaciones** (sistema × rol, entorno × servicio, módulo × capa), la matriz se declara como tabla **antes** del proceso, con la fuente real del código citada:

```markdown
## Referencia de sistemas y roles

| Sistema | Rol | Dónde se define |
|---|---|---|
| `ventas` | `operador` | `config/roles/ventas.ts` |
| `ventas` | `supervisor` | `config/roles/ventas.ts` |
| `operaciones` | `admin` | `config/roles/operaciones.ts` |

La tabla es **guía inicial**. El comando valida siempre contra el archivo citado — si la tabla y el archivo divergen, se detiene y pide aclaración.
```

- Mal: la tabla es la única fuente. Al agregar un rol nuevo, nadie actualiza la tabla, el comando ignora el rol silenciosamente.
- Bien: la tabla es guía y el comando verifica contra el archivo fuente de cada ejecución.

**Valor para el agente:** la tabla acelera la lectura; la citación del archivo garantiza que la fuente de verdad sea el código, no el markdown.

### 4. Proceso paso a paso con discovery explícito

Cada paso dice exactamente qué archivos lee, qué extrae de ellos y qué produce. Evita pasos genéricos del tipo "revisar el frontend":

```markdown
### Paso 3 — Leer archivos del frontend del rol

Usa Glob para listar `{{FRONTEND_ROOT}}/components/{{rol}}/**/*.{html,ts}`.

**Qué extraer de los `.html`:**
- Nombres literales de campos, botones y pestañas visibles.
- Condiciones `*ngIf="canX"` que dependan del rol.
- Mensajes de validación mostrados al usuario.

**Qué extraer de los `.ts`:**
- Nombres de los endpoints que consume cada componente.
- Reglas de visibilidad definidas en guards o servicios (`can(...)`, `hasRole(...)`).
- Rutas registradas en el routing module del rol.

**Qué hacer con lo extraído:**
- Construir una lista de pantallas con sus elementos visibles.
- Marcar cada acción con el endpoint backend que la respalda (para cruzar en el paso 4).
```

- Mal: "revisa el código del frontend y saca lo relevante para el rol". El agente interpreta "relevante" distinto cada vez.
- Bien: listas explícitas de qué elementos del código son insumo del output.

**Valor para el agente:** un paso específico es auditable. Dos ejecuciones del mismo paso producen listas idénticas si el código no cambió.

### 5. Reglas de exclusión explícita

Por cada dimensión del argumento, declara **qué NO debe aparecer** en el output:

```markdown
## Reglas de exclusión

- Si el sistema es `ventas`, no documentar módulos de `operaciones` ni del `portal-clientes`.
- Si el rol es `operador` (solo lectura en reportes), excluir acciones de edición de catálogos.
- Si una pantalla aparece en el menú pero su guard rechaza al rol, no documentarla.
- Si un endpoint existe en backend pero la UI no lo expone para este rol, tampoco se documenta.
```

- Mal: sin reglas de exclusión, el agente rellena por simetría — "si el manual del rol A tiene una sección de catálogos, el rol B también debería tenerla, aunque no la use".
- Bien: exclusiones explícitas. Un rol de solo lectura termina con un manual sin secciones de edición, no con secciones "bloqueadas para tu rol".

**Valor para el agente:** las exclusiones son un filtro determinista. El agente puede reportar "omití X porque Y" con referencia a la regla, en vez de "no parecía aplicar".

### 6. Checklist de calidad antes de guardar

Un bloque final de validaciones que el agente debe ejecutar antes de escribir el output:

```markdown
## Antes de guardar, verifico

- [ ] El título del artefacto refleja las dimensiones del argumento (`{{sistema}} — {{rol}}`).
- [ ] No hay contenido de otras combinaciones (la carpeta `ventas/operador/` no documenta pantallas de `operaciones/`).
- [ ] Todos los enlaces relativos apuntan a archivos que efectivamente voy a crear.
- [ ] Los nombres visibles en el manual coinciden con las strings del código fuente.
- [ ] El output se guarda en `{{OUTPUT_DIR}}/{{sistema}}/{{rol}}/` y no fuera.
- [ ] El README del directorio lista todos los archivos generados; no hay huérfanos.
```

**Valor para el agente:** un checklist al final convierte la entrega en una operación binaria. Si todos los items pasan, el output se guarda; si alguno falla, el agente reporta el gap y no escribe.

## Patrón de output: carpeta con README índice

Para comandos que generan **múltiples archivos relacionados**, prefiere la estructura:

```
{{OUTPUT_DIR}}/{{arg1}}/{{arg2}}/
├── README.md                # Índice con enlaces relativos
├── 01-{{tarea-principal-1}}.md
├── 02-{{tarea-principal-2}}.md
├── 10-{{secundario-1}}.md
└── 11-{{secundario-2}}.md
```

- Numeración `01-09` para el flujo principal o el ciclo operativo.
- Numeración `10+` para maestros, configuración y tareas secundarias.
- README regenerado desde la lista de archivos en cada ejecución — no se mantiene a mano.
- Verificación de enlaces al final del proceso.

La convención se comunica al lector y al siguiente agente: los archivos con número bajo son el camino crítico; los altos son referencia.

## Priorización de fuentes cuando varias responden lo mismo

Cuando un dato puede venir de varias fuentes (UI, backend, migraciones), declara el orden explícito:

1. **Fuente primaria** — lo que el usuario final ve (UI, contrato público de la API, schema).
2. **Fuente confirmadora** — lo que el backend implementa detrás de lo anterior.
3. **Fuente de último recurso** — código de implementación, cuando las dos anteriores no resuelven la duda.

Si la fuente primaria y la confirmadora divergen, el comando se **detiene y pide aclaración** — no inventa ni reconcilia por su cuenta.

- Mal: un manual que dice "el botón Guardar aparece después de llenar los campos" cuando en realidad el backend valida primero la autorización y el botón ni siquiera se muestra al rol del usuario.
- Bien: manual que describe lo que el usuario **ve**; si UI y backend discrepan, se reporta como hallazgo, no se oculta.

## Antes de entregar el comando, verifico

- [ ] Los argumentos están documentados con valores válidos y reglas de rechazo.
- [ ] Hay bloque de configuración con paths del repo — ningún path hardcoded en los pasos.
- [ ] Las matrices de combinación se declaran como tabla con la fuente de verdad citada en archivos reales.
- [ ] Cada paso dice qué leer, qué extraer y qué producir con granularidad suficiente para reproducirse.
- [ ] Hay sección de reglas de exclusión explícita cubriendo cada dimensión del argumento.
- [ ] Hay checklist de calidad al final, no en medio.
- [ ] El formato del output (árbol de archivos, nombres, numeración) está declarado con ejemplos concretos.
- [ ] El comando falla con mensaje útil si un argumento está fuera del set válido.
- [ ] El comando **no inventa** cuando las fuentes divergen — se detiene y pide aclaración.

## Errores comunes

- **Pasos genéricos** ("leer el frontend", "revisar el backend"). Sin qué extraer específico, cada ejecución produce algo distinto.
- **Argumentos implícitos.** El comando acepta cualquier cosa y "decide" — elimina la reproducibilidad.
- **Sin reglas de exclusión.** El agente rellena con contenido de otras combinaciones por analogía; el usuario termina con un manual que mezcla roles.
- **Paths hardcoded en cada paso.** Al reorganizar el repo hay que tocar todo el comando; nadie lo hace y el comando envejece.
- **Sin checklist.** El output pasa sin validación y termina con archivos huérfanos o enlaces rotos.
- **Tabla de referencia como fuente única.** La tabla envejece. El comando debe validar contra el código, usando la tabla solo como guía inicial.
- **Un único archivo gigante** cuando el resultado se divide lógicamente — dificulta mantenimiento y navegación.
- **Escribir en `{{OUTPUT_DIR}}` sin filtrar por argumentos.** Dos ejecuciones sobre argumentos distintos se pisan los archivos.

## Glosario

**Slash command** *(Slash command)* — comando reutilizable invocable por nombre desde el agente, con argumentos y proceso documentados. Ver [Claude Code · Slash commands](https://docs.claude.com/en/docs/agents-and-tools/claude-code/slash-commands) para la referencia oficial.

**Agent Skill** *(Agent Skill)* — paquete reutilizable que extiende las capacidades de un agente. Anthropic lo define como *"a way to teach Claude new capabilities using a structured folder"* ([Agent Skills overview](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview)).

**Discovery** *(Discovery phase)* — paso en que el comando lee los archivos del repo para construir el contexto antes de generar el output.

**Regla de exclusión** *(Exclusion rule)* — criterio explícito que determina qué contenido omitir según la combinación de argumentos, para evitar que el agente rellene por analogía.

**Fuente de verdad** *(Source of truth)* — archivo del código que se considera definitivo para una dimensión del comando; la tabla de referencia es guía, no fuente.

**Matriz de combinaciones** *(Combination matrix)* — conjunto de valores posibles de los argumentos del comando, con cada combinación produciendo un output distinto.

:::info Referencias primarias
- [Claude Code · Slash commands](https://docs.claude.com/en/docs/agents-and-tools/claude-code/slash-commands) — referencia oficial de slash commands.
- [Agent Skills overview](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) — referencia oficial de skills.
- [Anthropic · Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) — patrones arquitectónicos aplicables (orchestrator-workers, routing).
- [6.3 Arquitectura orientada a skills](../../../../docs/colaboracion-con-agentes-ia/03-arquitectura-orientada-a-skills.md) — módulo del bootcamp sobre diseño de skills reutilizables.
:::
