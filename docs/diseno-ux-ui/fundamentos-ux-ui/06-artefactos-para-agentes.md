---
sidebar_position: 6
sidebar_label: 3.1.6 Artefactos de diseño para agentes
---

# Artefactos de diseño para agentes

Los módulos anteriores establecen **qué** es UX/UI y **por qué** importan las convenciones. Este cierra el ciclo entregando los **artefactos copiables** que convierten esas decisiones en insumo procesable para un agente de IA. El objetivo es paralelo al de [6.2 Context engineering](../../colaboracion-con-agentes-ia/02-context-engineering-claude-md.md): **que un agente pueda generar o revisar una UI sin tener que adivinar tu sistema**.

Sin estos artefactos, un agente produce interfaces genéricas: tipografías por defecto, paleta azul-blanco, espaciados arbitrarios, componentes que no se parecen a los tuyos. Con ellos, produce pantallas que un revisor humano reconoce como "las nuestras".

## Qué entrega este módulo

Cuatro artefactos, todos en formato de texto plano y versionables junto al código:

1. **`DESIGN.md`** — contexto de diseño permanente (análogo a `CLAUDE.md` pero para UI).
2. **`design-tokens.json`** — tokens de color, tipografía, espaciado y radios.
3. **Plantilla de brief de componente** — formato estructurado para pedir un componente nuevo.
4. **Prompts canónicos** — tres plantillas probadas: wireframe textual, componente, revisión heurística.

No prescribimos herramientas (Figma, Tailwind, Radix). Los artefactos son neutrales: los adaptas a tu stack reemplazando valores, no la estructura.

## Artefacto 1 — `DESIGN.md`

`DESIGN.md` es al diseño lo que `CLAUDE.md` es a la ingeniería: un archivo vivo en la raíz del repo que un agente lee **antes** de proponer cualquier pantalla o componente. Su propósito es eliminar las preguntas obvias ("¿qué paleta usas?", "¿tienes guías de accesibilidad?") y las decisiones reinventadas en cada tarea.

```markdown
# DESIGN.md

## Propósito
Contexto de diseño para agentes de IA que generen o revisen UI de este producto.
Mantener bajo 400 líneas. Si crece, mover detalles a archivos hermanos enlazados.

## Producto
- **Nombre:** [Producto]
- **Audiencia:** [quién lo usa — nivel técnico, contexto, dispositivo principal]
- **Tono visual:** [ej. "sobrio institucional", "cálido B2C", "técnico denso"]
- **Principios:** [3-5 principios accionables, no adjetivos]
  - Claridad sobre densidad — preferimos una pantalla con menos datos bien jerarquizados a una con todo visible.
  - Explicitud sobre inferencia — estados, errores y vacíos siempre tienen texto.
  - Consistencia sobre creatividad — una tabla se ve como todas las tablas.

## Sistema visual
- **Tokens:** ver `design-tokens.json` (fuente de verdad).
- **Tipografía base:** [familia, tamaño base, escala modular].
- **Grid:** [12-col / 8px baseline / etc.].
- **Iconografía:** [librería, trazo, tamaños permitidos].

## Accesibilidad — mínimos no negociables
- Contraste AA (4.5:1 texto normal, 3:1 texto grande).
- Foco visible en todos los elementos interactivos.
- Áreas táctiles ≥ 44×44px.
- Todo flujo completable solo con teclado.
- Alt text en toda imagen con contenido informativo.

## Componentes — inventario
| Componente | Estado | Ubicación | Notas |
|------------|--------|-----------|-------|
| Button | Estable | `src/ui/Button` | 3 variantes: primary, secondary, ghost |
| Table | Estable | `src/ui/Table` | Paginación server-side |
| Modal | En revisión | `src/ui/Modal` | Revisar foco tras cerrar |

## Reglas de composición
- Un Modal no contiene otro Modal.
- Un formulario tiene un solo botón primario.
- Las tablas con >50 filas requieren filtros o paginación.

## Lo que NO hacemos
- No usar emojis en UI de producto.
- No introducir una librería de componentes nueva sin discusión.
- No diseñar pantallas sin estado vacío definido.

## Referencias
- Guía de marca: [enlace interno].
- Figma: [enlace].
- Tokens: `design-tokens.json`.
```

:::tip Cómo mantenerlo vivo
`DESIGN.md` se revisa cada sprint junto con el código. Si un agente repite la misma pregunta dos veces en tareas distintas, la respuesta pertenece aquí.
:::

## Artefacto 2 — `design-tokens.json`

Los tokens son la **fuente única de verdad** del sistema visual. Un agente que tiene tokens no inventa colores: los cita. Un equipo con tokens versionados puede cambiar la paleta completa modificando un archivo.

```json
{
  "$schema": "https://design-tokens.github.io/community-group/format/",
  "color": {
    "brand": {
      "primary":   { "value": "#0d4d92", "description": "Acción principal, enlaces, foco" },
      "secondary": { "value": "#ef9b50", "description": "Acento, no para acciones críticas" }
    },
    "neutral": {
      "900": { "value": "#1a2f4d" },
      "700": { "value": "#3a4a66" },
      "500": { "value": "#6b7a94" },
      "300": { "value": "#c8d0de" },
      "100": { "value": "#f2f4f8" },
      "000": { "value": "#ffffff" }
    },
    "semantic": {
      "success": { "value": "#2f8f5a" },
      "warning": { "value": "#b5791f" },
      "danger":  { "value": "#b33a3a" },
      "info":    { "value": "#2b6cb0" }
    }
  },
  "font": {
    "family": {
      "base":    { "value": "'Source Sans 3', system-ui, sans-serif" },
      "heading": { "value": "'Space Grotesk', system-ui, sans-serif" },
      "mono":    { "value": "'JetBrains Mono', ui-monospace, monospace" }
    },
    "size": {
      "xs":   { "value": "0.75rem" },
      "sm":   { "value": "0.875rem" },
      "base": { "value": "1rem" },
      "lg":   { "value": "1.125rem" },
      "xl":   { "value": "1.25rem" },
      "2xl":  { "value": "1.5rem" },
      "3xl":  { "value": "1.875rem" }
    },
    "weight": {
      "regular":  { "value": 400 },
      "medium":   { "value": 500 },
      "semibold": { "value": 650 },
      "bold":     { "value": 700 }
    }
  },
  "spacing": {
    "0":  { "value": "0" },
    "1":  { "value": "0.25rem" },
    "2":  { "value": "0.5rem" },
    "3":  { "value": "0.75rem" },
    "4":  { "value": "1rem" },
    "6":  { "value": "1.5rem" },
    "8":  { "value": "2rem" },
    "12": { "value": "3rem" }
  },
  "radius": {
    "sm":   { "value": "0.25rem" },
    "md":   { "value": "0.5rem" },
    "lg":   { "value": "1rem" },
    "full": { "value": "9999px" }
  },
  "shadow": {
    "sm": { "value": "0 1px 2px rgba(0,0,0,0.06)" },
    "md": { "value": "0 4px 12px rgba(0,0,0,0.08)" },
    "lg": { "value": "0 12px 32px rgba(0,0,0,0.12)" }
  }
}
```

Los valores de arriba son un **punto de partida**, no una paleta recomendada. Reemplázalos con los de tu marca. La estructura sí es la que recomendamos: formato [Design Tokens Community Group](https://design-tokens.github.io/community-group/format/), porque permite exportar a CSS, Tailwind, Android y iOS con herramientas estándar ([Style Dictionary](https://amzn.github.io/style-dictionary/)).

## Artefacto 3 — Plantilla de brief de componente

Cuando le pides a un agente "necesito un botón", recibes un botón genérico. Cuando le pasas un brief estructurado, recibes tu botón. Esta plantilla funciona tanto para humanos como para agentes.

```markdown
# Brief: [NombreComponente]

## Propósito
Qué problema resuelve en una oración. Si requiere dos oraciones, probablemente son dos componentes.

## API — propiedades
| Prop | Tipo | Default | Descripción |
|------|------|---------|-------------|
| variant | `"primary" \| "secondary" \| "ghost"` | `"primary"` | Jerarquía visual |
| size | `"sm" \| "md" \| "lg"` | `"md"` | Área táctil y tipografía |
| loading | `boolean` | `false` | Deshabilita y muestra spinner |

## Estados visuales
- **Default** — reposo, listo para interacción.
- **Hover** — cambio de fondo o borde, no de layout.
- **Focus** — anillo visible (token `color.brand.primary`, 2px offset).
- **Active** — feedback táctil breve.
- **Disabled** — contraste reducido a AA mínimo, cursor not-allowed.
- **Loading** — deshabilita interacción, reemplaza label por spinner inline.

## Accesibilidad
- Elemento semántico: `<button>` (no `<div onClick>`).
- Role implícito correcto; no forzar `role`.
- Texto del botón accionable ("Guardar cambios", no "OK").
- Si el label es solo icono: `aria-label` obligatorio.

## Estados vacíos / error / carga
No aplica a botón individual, pero todo componente contenedor (Table, List, Card) debe definir los tres.

## Ejemplo de uso
\`\`\`tsx
<Button variant="primary" size="md" onClick={handleSave}>
  Guardar cambios
</Button>
\`\`\`

## Qué NO hace
- No navega (para eso, `<Link>`).
- No abre modales sin confirmación en acciones destructivas.
```

:::info Por qué "Qué NO hace" es obligatorio
Los componentes crecen por acumulación silenciosa: un botón que además navega, que además abre modales, que además muestra tooltips. La sección *Qué NO hace* es el límite explícito que impide esa deriva. Un agente la respeta si está escrita; si no, la reinventa.
:::

## Artefacto 4 — Prompts canónicos

Tres plantillas listas. Copia, reemplaza los campos entre corchetes, envía.

### Prompt 1 — Wireframe textual

Útil en fase temprana, cuando todavía no hay componentes implementados.

```text
Actúa como diseñador UX. Lee DESIGN.md y design-tokens.json antes de responder.

Tarea: describir un wireframe textual (sin código) para:

[Pantalla o flujo: ej. "listado de facturas con filtros por estado y fecha"]

Requisitos:
- Audiencia: [perfil del usuario].
- Acciones primarias: [1-3 acciones].
- Datos visibles por fila/tarjeta: [campos mínimos].
- Estados a cubrir: cargado, vacío, error, permiso denegado.

Entrega:
1. Árbol de la pantalla (header, filtros, contenido, acciones).
2. Jerarquía visual (qué es primario, secundario, terciario).
3. Comportamiento responsive (desktop / tablet / móvil).
4. Lista de componentes del sistema que reutilizarías.
5. Componentes nuevos que habría que crear (si los hay) con justificación.

No escribas código. No propongas paleta ni tipografía — usa los tokens definidos.
```

### Prompt 2 — Generar componente

Útil cuando el brief ya existe y quieres el primer borrador.

```text
Lee DESIGN.md, design-tokens.json y el brief adjunto.

Tarea: implementar el componente [NombreComponente] según el brief.

Stack objetivo:
- [React 18 + TypeScript / Vue 3 + TS / etc.]
- [Tailwind v4 / CSS Modules / Styled Components]
- [Testing: Vitest + Testing Library / Jest]

Restricciones:
- Usa exclusivamente tokens de design-tokens.json. Si necesitas un valor que no existe, pídelo antes de crear uno ad-hoc.
- Cubre todos los estados listados en el brief.
- Incluye 3-5 pruebas que validen: render por defecto, estado disabled, callback de click, accesibilidad por teclado.
- No introduzcas dependencias nuevas sin justificarlo.

Entrega:
1. Código del componente.
2. Código de las pruebas.
3. Ejemplo de uso en comentario al inicio del archivo.
4. Lista de decisiones tomadas que no estaban en el brief (para revisión humana).
```

### Prompt 3 — Revisión heurística

Útil para auditar una pantalla existente.

```text
Actúa como revisor UX. Lee DESIGN.md antes de responder.

Tarea: revisión heurística de [captura / URL / descripción de pantalla] usando las
10 heurísticas de Nielsen + accesibilidad WCAG AA.

Entrega una tabla con:
| Heurística | Severidad (0-4) | Observación | Recomendación |

Donde severidad es:
- 0: no aplica
- 1: estética, cosmético
- 2: menor, corrige cuando puedas
- 3: mayor, alta prioridad
- 4: catastrófico, bloquea flujo o vulnera accesibilidad

Ordena de severidad mayor a menor.

No inventes datos que no veas. Si falta contexto para juzgar una heurística, dilo
explícitamente ("no puedo evaluar sin ver el estado de error").
```

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** dotar al proyecto de los artefactos mínimos para que un agente de IA genere o revise UI sin adivinar el sistema de diseño.

**Entradas:**
- Producto con al menos una pantalla real (aunque sea un MVP).
- Paleta de marca y tipografía ya decididas.
- Lista inicial de componentes reutilizados.

**Pasos:**
1. Crear `DESIGN.md` en la raíz, completando las 8 secciones propuestas. No dejar TBD; si no hay dato, declararlo explícitamente.
2. Extraer tokens de la UI actual a `design-tokens.json` (colores usados, escalas tipográficas, espaciados).
3. Para cada componente reutilizado, escribir el brief usando la plantilla del módulo.
4. Probar los tres prompts canónicos con una pantalla real; comparar salidas contra el criterio humano.
5. Iterar `DESIGN.md` con las preguntas que el agente tuvo que hacer — cada pregunta repetida es una sección faltante.

**Salidas:**
- `DESIGN.md` versionado (150-400 líneas).
- `design-tokens.json` con todos los valores que aparecen en la UI actual.
- Briefs para los 5-10 componentes más usados.
- Reducción medible en tiempo de ida-y-vuelta al pedir una UI a un agente.

**Errores comunes:**
- Copiar los tokens del ejemplo sin adaptarlos a la marca real.
- Omitir la sección "Qué NO hacemos" en `DESIGN.md` — es la que más evita regresiones.
- Generar tokens pero no cablearlos al CSS/Tailwind, dejándolos como documentación decorativa.
- Usar los prompts canónicos sin haber escrito primero `DESIGN.md`: los resultados son genéricos.

**Referencias cruzadas:**
- [3.1.5 Convenciones de UI consistentes](./05-convenciones-de-ui-consistentes.md)
- [6.2 Context engineering](../../colaboracion-con-agentes-ia/02-context-engineering-claude-md.md)
- [6.5 Diseño de prompts y verificación](../../colaboracion-con-agentes-ia/05-diseno-de-prompts-y-verificacion.md)

</div>

---

## Glosario

**Design token** *(design token)* — valor atómico del sistema visual (color, tamaño, espaciado) nombrado y versionado, que actúa como fuente única de verdad para todas las plataformas.

**`DESIGN.md`** *(design context file)* — archivo de contexto de diseño en la raíz del repo, análogo a `CLAUDE.md`, que un agente lee antes de generar UI.

**Brief de componente** *(component brief)* — documento estructurado que define propósito, API, estados, accesibilidad y límites de un componente antes de implementarlo.

**Revisión heurística** *(heuristic evaluation)* — método de evaluación de usabilidad donde un experto inspecciona la interfaz contra un conjunto de principios (típicamente las 10 heurísticas de Nielsen).

**Style Dictionary** *(style dictionary)* — herramienta de Amazon de código abierto que transforma un archivo de tokens en artefactos para múltiples plataformas (CSS, iOS, Android, Flutter).

:::info Referencias primarias
- [Design Tokens Community Group — Format Specification](https://design-tokens.github.io/community-group/format/)
- [Style Dictionary — Amazon](https://amzn.github.io/style-dictionary/)
- [Nielsen Norman Group — 10 Usability Heuristics](https://www.nngroup.com/articles/ten-usability-heuristics/)
- [WCAG 2.2 — Web Content Accessibility Guidelines](https://www.w3.org/TR/WCAG22/)
- [Anthropic — Prompt Engineering Guide](https://docs.anthropic.com/claude/docs/prompt-engineering)
:::

---

<AuthorCredit />
