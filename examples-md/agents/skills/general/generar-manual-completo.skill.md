<!-- Destino sugerido: .claude/skills/generar-manual-completo.skill.md -->

---
name: generar-manual-completo
description: Orquesta de extremo a extremo la generación y validación de un manual de usuario. Invoca `generar-manual` para producir el `.md`; si el destino requiere imágenes, invoca `generar-capturas-manual`; al final reporta verde / amarillo / rojo. No redacta ni captura por sí mismo; coordina y valida.
---

# Skill: generar-manual-completo

Generar un manual completo es una cadena de pasos: redactar, decidir si hacen falta capturas y validar. Cuando un humano la hace a mano, los pasos pierden coherencia entre sí — una sección cita un botón **Emitir** que la UI ya no muestra, o un spec genera una imagen que el manual nunca referencia. Cada artefacto se ve bien por separado y la suma es un manual roto.

Este skill resuelve la cadena con un agente coordinador. Su única responsabilidad es **invocar las skills correctas en orden y validar el resultado conjunto**. No abre código fuente, no escribe markdown, no genera capturas. Lee los artefactos producidos por las skills delegadas y reporta lo que sobrevivió y lo que no.

## Cuándo invocarme

Úsame cuando:
- Hay que producir o validar un manual nuevo desde cero, con capturas opcionales según el destino.
- Después de un release que cambió la UI: revisar el manual y regenerar capturas solo si el destino las requiere.
- Se va a integrar la generación de manuales en un pipeline (CI, hooks de pre-release): este skill expone una interfaz reproducible.
- Un equipo nuevo adopta el patrón de tres agentes y necesita un punto de entrada único.

**No me uses para:**
- Redactar un único procedimiento aislado — usa [`generar-manual`](./generar-manual.skill.md) directamente.
- Generar solo capturas para un manual ya escrito — usa [`generar-capturas-manual`](./generar-capturas-manual.skill.md) directamente.
- Validar un manual existente sin regenerarlo (auditoría pura) — eso amerita un skill `validar-manual` aparte.

## Diseño

Este skill es un **orquestador**, no hace el trabajo pesado. Su valor es:

1. Encadenar las skills en el orden correcto (capturas son opcionales).
2. Validar la consistencia del resultado entre el manual y los artefactos producidos.
3. Reportar al usuario un resumen estructurado: verde / amarillo / rojo.

### Adaptación al destino del manual

Antes de invocar las fases, identifico el destino del manual y elijo el modo:

| Destino | Modo | Fases ejecutadas |
|---------|------|------------------|
| Sitio web leído por humanos | **Completo** | Fase 1 (manual) + Fase 2 (capturas) + Fase 3 (validación cruzada) |
| Chatbot / RAG / soporte automatizado | **Solo Markdown** | Fase 1 + Fase 3 (solo reglas del `.md`) |
| Producto sin UI (CLI, API, librería) | **Solo Markdown** | Fase 1 + Fase 3 (solo reglas del `.md`) |
| Equipo sin infra E2E aún | **Solo Markdown** | Fase 1 + Fase 3; capturas a mano si las quieren |

**Heurística automática:** si el `.md` producido en fase 1 no contiene referencias `![alt](.../NN.png)` ni slots `Ilustración N:`, salto la fase 2 sin pedir confirmación cuando el destino es Solo Markdown. Si contiene contrato de imágenes pero el usuario indicó destino "chatbot", advierto y sugiero quitar esas referencias antes de generar imágenes que nadie va a consumir. Si el destino es lectura humana y no hay contrato de imágenes, marco amarillo para revisión humana.

Multi-agente (opcional): las fases 1 y 2 son delegables a sub-agentes para mantener limpio el contexto principal. La fase 3 (validación) se queda en el contexto principal porque cruza ambos artefactos.

Por defecto, ejecutar las fases en el contexto principal (sin sub-agentes) salvo que el contexto esté cerca del límite. Cuando se delega:

- Pasar el contenido literal de la skill delegada como parte del prompt del sub-agente, junto con los argumentos.
- Pedir al sub-agente que reporte: archivos creados/modificados, líneas clave y decisiones no obvias.
- Si la fase 1 falla, **no** lanzar la fase 2 — captura sin manual no tiene sentido.

## Entradas

1. **Módulo** y **flujo** (ej. `ventas`, `02-cotizaciones`) — los argumentos del comando.
2. **Repos accesibles** del frontend, backend y docs.
3. **Convenciones del agente** declaradas en `CLAUDE.md` o `AGENTS.md`.
4. **Credenciales E2E** en `.env` para el rol del manual, solo si el destino requiere capturas.

Si falta el flujo (`<NN-flujo>`), preguntar antes de continuar. Este skill produce **un** manual validado; las capturas se agregan solo cuando el destino lo justifica.

## Reglas obligatorias

- **No redactar ni capturar.** Si me sorprendo escribiendo Markdown del manual o código del spec, detener y delegar a la skill correspondiente.
- **Verificar antes de delegar a la siguiente fase.** No lanzar fase 2 sin haber confirmado que el archivo del manual existe y tiene contenido válido.
- **Distinguir tipos de fallo.** Fase 1 fallida = abortar. Fase 2 fallida por entorno = ejecutar fase 3 sobre el manual. Fase 2 fallida por código = reportar el spec específico.
- **Reportar verde / amarillo / rojo, nunca "OK / Falló".** Cada error indica archivo y causa probable.
- **No "arreglar" silenciosamente** una violación de regla. Los problemas se reportan, no se ocultan.

## Proceso

### Fase 1 — Generar el manual

Invocar [`generar-manual`](./generar-manual.skill.md) con los argumentos recibidos. Esta skill produce o actualiza:

- `manuales/<modulo>/<NN-flujo>.md`
- `manuales/<modulo>/README.md` (si corresponde)

Antes de pasar a la fase 2, verificar:

- El archivo `<NN-flujo>.md` existe.
- Contiene al menos un procedimiento con la estructura obligatoria: tarea concreta, precondiciones, pasos, resultado esperado y recuperación ante errores.
- Contar cuántas referencias `![alt](.../NN.png)` o slots `Ilustración N:` hay en el cuerpo. Ese conteo es el objetivo de capturas para fase 2.

Si la fase 1 falla o el archivo no se escribe, **abortar** y reportar el error específico al usuario.

### Fase 2 — Generar las capturas (opcional)

**Antes de invocar:** si el `.md` de fase 1 no contiene referencias `![alt](.../NN.png)` ni slots `Ilustración N:`, saltar esta fase y pasar directamente a fase 3 cuando el destino no requiere imágenes. El reporte final lo indica con un mensaje informativo: *"Manual sin capturas: variante Markdown puro (chatbot, sin UI o curado a mano)"*. Si el destino sí es lectura humana, reportar amarillo porque puede faltar apoyo visual.

Si el manual sí declara contrato de imágenes, invocar [`generar-capturas-manual`](./generar-capturas-manual.skill.md) con los mismos argumentos. Esta skill produce o actualiza:

- `e2e-docs/specs/<NN-flujo>.spec.ts`
- (Si era necesario) infraestructura base en `e2e-docs/` (helpers, config, .env.example).
- (Si era necesario) script `docs:screenshots:<flujo>` en `package.json`.
- Imágenes en `imagenes/<modulo>/<NN-flujo>/`.
- El manual actualizado solo en referencias de imagen cuando había slots pendientes o rutas/alt text inconsistentes.

Antes de pasar a la fase 3, verificar:

- El spec corrió limpio (`exit 0`, `1 passed`).
- Los `.png` esperados (los que el manual referencia) existen en disco.
- Los slots `Ilustración N:` quedaron resueltos como referencias `![alt](path)` cuando aplica.

Si la fase 2 falla por entorno (credenciales faltantes, dev server no arranca, base de datos sin sembrar), reportar el problema sin abortar la fase 3 — la validación del manual sigue siendo útil aunque las capturas fallen.

### Fase 3 — Validación cruzada

Esta es la fase que justifica que esta skill exista. Cruzo ambos artefactos:

**Validaciones obligatorias** (rojo si fallan):

1. **Imágenes referenciadas existen** *(solo si el manual declara `![alt]`)*: para cada referencia en el manual, confirmar que el archivo existe. Listar los faltantes. En modo "Solo Markdown" esta validación se omite.
2. **Sin duplicados sospechosos** *(solo si hay `.png`)*: comparar tamaño en bytes. Si dos archivos que deberían ser distintos pesan **exactamente** lo mismo, reportar pares duplicados.
3. **Reglas del manual** (mínimo verificable):
   - Cada procedimiento está titulado como tarea (`Cómo {verbo + resultado}`), no como pantalla.
   - Cada procedimiento contiene precondiciones, pasos, resultado esperado y recuperación ante errores.
   - Los botones, campos y mensajes mencionados aparecen en la UI real o en capturas literales.
   - Los permisos o roles se nombran de forma concreta cuando condicionan una acción.
   - No aparecen frases ambiguas ("permisos de escritura" sin nombrar el permiso concreto).
   - No se citan detalles internos de código o archivos técnicos en el cuerpo del manual.
4. **Spec y script registrados** *(solo modo completo)*: `e2e-docs/specs/<NN-flujo>.spec.ts` existe y `package.json` tiene `docs:screenshots:<NN-flujo-corto>`. En modo "Solo Markdown" esta validación se omite.

5. **Coherencia con el destino declarado**: si el destino es chatbot/RAG y el manual igual contiene `![alt]` o slots `Ilustración N:`, marcar como rojo y sugerir quitar las referencias. Si el destino es humanos y el manual no tiene ninguna captura ni slot, marcar como amarillo (puede ser intencional, pero conviene confirmar).

**Validaciones recomendadas** (amarillo si fallan):

- **Numeración coherente**: `Ilustración N:` aumenta secuencialmente sin saltos.
- **Match alt ↔ archivo**: el alt-text de cada `![alt](.../NN-imagen.png)` describe lo que el nombre del archivo sugiere.
- **Anclas de TOC válidas**: cada `[texto](#anchor)` resuelve a un `##` o `###` real.
- **Capturas no triviales**: cada `.png` pesa más de 50 KB. Una captura menor a 20 KB suele indicar pantalla en blanco o spinner.

**Validaciones opcionales** (informativas):

- **Cobertura del menú**: el manual cubre todas las opciones del módulo según la fuente autoritativa del menú. Si faltan, listar las omitidas.
- **Tamaño relativo**: si una captura tiene exactamente el mismo tamaño que la anterior, advertir.

### Fase 4 — Reporte al usuario

Imprimir un resumen estructurado:

```text
## Manual: <NN-flujo>.md

✅ <count> validaciones pasadas
⚠️  <count> warnings
❌ <count> errores

### ✅ Lo que está bien
- ...

### ⚠️ Warnings
- ...

### ❌ Errores
- ...

### Próximos pasos
- Si hay errores: lista accionable de cómo arreglarlos.
- Si todo está bien: invitar al usuario a revisar visualmente 2-3 capturas
  para confirmar que muestran lo que dice el alt-text.
```

Mantener el reporte conciso. No volver a explicar las reglas — solo decir cuál se violó y dónde.

## Comportamiento ante errores

| Escenario | Acción |
|-----------|--------|
| Fase 1 falla (manual no se escribió) | Abortar. Reportar el error. No correr fase 2 ni 3. |
| Fase 2 no aplica (modo Solo Markdown) | Saltar capturas. Hacer fase 3 solo sobre estructura y fidelidad del `.md`. |
| Fase 2 falla por entorno (credenciales, dev server, DB) | Saltar capturas. Hacer fase 3 sobre el manual existente. Reportar que las imágenes no se generaron por causa de entorno. |
| Fase 2 falla por código (spec con bug, selector roto) | Reportar el error específico del spec (archivo y línea). Sugerir reabrir `generar-capturas-manual` después de arreglar. |
| Fase 3 encuentra rojos | El comando termina con éxito (no es un crash), pero el reporte es explícito sobre lo que necesita corrección. |

## Ejemplo de invocación

### Modo completo (sitio web para humanos)

```text
Usando el skill generar-manual-completo, genera y valida el manual
del flujo `02-cotizaciones` del módulo `ventas`.
```

Salida esperada (caso feliz):

```text
## Manual: ventas/02-cotizaciones.md

✅ 14 validaciones pasadas
⚠️  1 warning
❌ 0 errores

### ⚠️ Warnings
- 04-detalle-cotizacion.png pesa 47 KB (umbral: 50 KB) — revisar visualmente.

### Próximos pasos
- Abrir 2-3 capturas para confirmar que muestran el contenido esperado.
- El manual queda listo para incluir en el próximo release.
```

Salida esperada (con errores accionables):

```text
## Manual: ventas/02-cotizaciones.md

✅ 10 validaciones pasadas
⚠️  2 warnings
❌ 2 errores

### ❌ Errores
- Imagen faltante: imagenes/ventas/02-cotizaciones/05-confirmacion.png
  → El manual la referencia pero el spec no la genera. Revisar el bloque
    `screenshots` en `e2e-docs/specs/02-cotizaciones.spec.ts`.
- Acceso al menú incorrecto: el manual cita "Comercial → Cotizaciones",
  pero la fuente autoritativa del menú declara "Ventas → Cotizaciones".

### Próximos pasos
- Arreglar los dos rojos antes de mergear.
- Después, reinvocar este skill para revalidar.
```

### Modo Solo Markdown (chatbot, sin UI o curado a mano)

```text
Usando el skill generar-manual-completo, genera y valida el manual
del flujo `02-cotizaciones` del módulo `ventas`. Destino: chatbot
interno (RAG sobre el catálogo de manuales). No generes capturas.
```

Salida esperada:

```text
## Manual: ventas/02-cotizaciones.md

ℹ️  Modo: Solo Markdown (destino: chatbot)
✅ 8 validaciones pasadas
⚠️  0 warnings
❌ 0 errores

### Notas
- Fase 2 saltada: el destino no requiere imágenes y el manual no las
  declara.
- Fase 3 validó únicamente reglas del Markdown (estructura,
  procedimientos, citas literales del UI, glosario).

### Próximos pasos
- El manual está listo para indexarse en el RAG.
- Si más adelante el destino cambia a sitio web humano, reinvocar este
  skill sin el flag "destino: chatbot" para agregar capturas.
```

## Antes de cerrar la tarea, verifico

- [ ] Fase 1 y fase 3 se ejecutaron; fase 2 se ejecutó o se saltó con motivo claro.
- [ ] El reporte final lista verde / amarillo / rojo sin ambigüedad.
- [ ] Si hay rojos, los próximos pasos son accionables (qué archivo, qué línea, qué cambiar).
- [ ] No se modificaron archivos fuera del alcance del flujo (manual, spec, imágenes, package.json).
- [ ] No se "arregló" silenciosamente una violación de regla — los problemas se reportan, no se ocultan.
- [ ] El reporte distingue fallos por entorno de fallos por código.

## Errores comunes

- **Hacer trabajo de las skills delegadas** dentro del orquestador. Si edito el manual o el spec, dejé de orquestar y empecé a duplicar lógica.
- **Saltar la fase 3 cuando "todo se ve bien"**. La fase de validación es barata y atrapa los errores que las skills no pueden ver desde su lado.
- **Confundir fallo de entorno con fallo de código**. El equipo arregla lo que no está roto y el bug real queda escondido.
- **Reporte binario** ("OK" / "Falló"). Obliga al usuario a abrir todos los archivos para entender qué pasó.
- **No verificar entre fases**. Si lanzo fase 2 sin confirmar que el manual se escribió, la captura genera imágenes huérfanas o falla con un mensaje confuso.
- **Aceptar warnings como rojos** o viceversa. Mezclar severidades destruye la utilidad del reporte.

## Glosario

**Orquestador** *(Orchestrator)* — agente cuya función es invocar otras skills en orden y validar el resultado conjunto. No realiza el trabajo especializado.

**Fase observable** *(Observable phase)* — paso del orquestador cuyo éxito se comprueba leyendo un archivo o un conteo concreto, no por ausencia de error de la skill anterior.

**Validación cruzada** *(Cross-artifact validation)* — verificación que confronta dos artefactos producidos por skills distintas (manual `.md` contra capturas `.png`) para detectar inconsistencias.

**Fallo de entorno** *(Environment failure)* — error por infraestructura externa (credenciales, dev server, BD). Se reporta distinto del fallo de código y no invalida la validación del manual.

**Reporte verde / amarillo / rojo** *(Three-tier report)* — formato de salida que distingue validaciones pasadas, advertencias y errores, cada uno con archivo y causa probable.

**Sub-agente** *(Sub-agent)* — agente delegado al que el orquestador pasa una skill con su prompt y argumentos para mantener limpio el contexto principal. Opcional; usar solo cuando el contexto del orquestador esté cerca del límite.

:::info Referencias primarias
- [Anthropic — Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) — patrones de orquestación (prompt chaining, orchestrator-workers, evaluator-optimizer) y cuándo usar cada uno.
- [Anthropic — Multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system) — estudio de un orquestador real coordinando sub-agentes.
- [Skill `generar-manual`](./generar-manual.skill.md) — fase 1 del orquestador.
- [Skill `generar-capturas-manual`](./generar-capturas-manual.skill.md) — fase 2 del orquestador.
- [5.5 Generación de manuales con agentes](../../../../docs/documentacion-y-requerimientos/05-generar-manuales-con-agentes.md) — módulo del bootcamp que describe el patrón de tres agentes; este skill es el orquestador.
:::
