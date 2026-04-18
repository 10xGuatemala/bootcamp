---
sidebar_position: 3
sidebar_label: 3.2.3 Handoff técnico
---

# Handoff técnico

:::tip Archivos copiables
La plantilla de spec y los tres prompts canónicos viven como archivos reales en [`examples/`](https://github.com/10xGuatemala/bootcamp/tree/main/docs/diseno-ux-ui/sistemas-de-diseno/examples).
:::

El **handoff** es el momento en que una pantalla pasa de diseño a implementación. Hecho bien, desarrollo construye sin preguntar y el resultado coincide con lo diseñado. Hecho mal, genera semanas de *"¿cuál era el margen aquí?"*, *"¿qué pasa cuando la tabla está vacía?"*, *"¿el hover es este color o este otro?"* — y una UI inconsistente como resultado.

Este módulo define **qué entrega diseño** y **qué asume desarrollo** para que no haya zona gris. Aplica igual cuando el implementador es humano y cuando es un agente de IA: la diferencia entre un agente que adivina y uno que acierta está en la especificación.

## Qué entrega diseño — los 5 pilares

Cualquier pantalla lista para implementar especifica estos cinco pilares. Si falta uno, el handoff no está completo.

### 1. Spacing

Todo margen, padding y gap se expresa como **token**, no como píxel:

```markdown
Header de la tabla:
- padding-top: spacing.4 (1rem)
- padding-bottom: spacing.3 (0.75rem)
- gap entre columnas: spacing.6 (1.5rem)
```

Si el diseño usa un valor que no existe como token, es señal de que **falta un token**, no de que hay que hardcodear. Detén el handoff, añade el token, continúa.

### 2. Tipografía

Cada texto especifica: token de familia, token de tamaño, token de peso, y altura de línea cuando no sea el default.

```markdown
Título de la sección:
- family: font.family.heading
- size: font.size.2xl
- weight: font.weight.semibold
- line-height: 1.2
```

### 3. Estados — la tabla de los 7

Todo componente interactivo o contenedor debe especificar **7 estados**. Omitir uno garantiza que desarrollo lo inventa — y no como tú lo querías.

| Estado | Cuándo | Qué muestra |
|--------|--------|-------------|
| **Default** | Condición normal, con datos | UI estándar |
| **Hover** | Puntero sobre elemento interactivo | Cambio sutil de fondo/borde, sin cambio de layout |
| **Focus** | Elemento navegado por teclado | Anillo visible (2px, offset 2px, `color.brand.primary`) |
| **Active/Pressed** | Durante el click o tap | Feedback táctil breve |
| **Disabled** | No interactivo | Contraste reducido a AA mínimo, `cursor: not-allowed` |
| **Loading** | Esperando datos o acción | Skeleton, spinner, o estado intermedio identificable |
| **Empty** | Sin datos | Texto + ilustración/icono + acción sugerida |
| **Error** | Falló la operación | Mensaje claro + acción de recuperación |

Las tablas, listas y formularios **siempre** tienen Loading, Empty y Error. No es opcional.

### 4. Comportamiento responsive

Tres breakpoints recomendados; más breakpoints generan más puntos de mantenimiento que valor.

| Breakpoint | Ancho | Uso típico |
|------------|-------|------------|
| Mobile | < 640px | Layout vertical, navegación oculta tras menú |
| Tablet | 640px – 1024px | Layouts híbridos, sidebars colapsables |
| Desktop | ≥ 1024px | Layout completo |

Para cada breakpoint el handoff especifica: **qué se oculta**, **qué se reordena**, **qué cambia de tamaño**, y **qué se mantiene igual**. Evita "que se adapte": eso es pedirle al desarrollador que diseñe por ti.

:::tip Regla del 80/20
Diseña primero para desktop si tu audiencia es interna/B2B, mobile-first si es pública/B2C. Luego adapta al otro. Diseñar los dos en paralelo desde el día uno multiplica el costo sin proporcional beneficio.
:::

### 5. Interacción y microinteracciones

- **Transiciones:** duración (`150ms` rápida, `250ms` media, `400ms` lenta), curva (`ease-out` para entrada, `ease-in` para salida).
- **Feedback de éxito:** toast, cambio de estado visible, navegación — no todo al mismo tiempo.
- **Feedback de error:** mensaje adyacente al input que falló, no modal genérico.
- **Confirmaciones:** solo para acciones destructivas (eliminar, desactivar). El resto confunde.

## Especificación mínima de una pantalla

Toda pantalla lista para implementar incluye este bloque. Si algo no aplica, declararlo explícitamente ("sin estado vacío — la tabla siempre tiene al menos una fila por arquitectura del sistema").

```markdown
# Spec: [NombrePantalla]

## Propósito
Qué hace el usuario en esta pantalla. Una oración.

## Ruta y permisos
- Ruta: /facturas
- Requiere: rol "Administrador" o "Contador"
- Si el usuario no tiene permisos: redirigir a /sin-permiso

## Estructura
Tree de componentes con tokens de spacing:
- Header (padding: spacing.4)
  - Título (font.size.2xl)
  - Botón primario "Nueva factura"
- Filtros (margin-top: spacing.6)
  - SearchBar
  - DateRangePicker
  - Select "Estado"
- Tabla (margin-top: spacing.4)
  - ...

## Datos
- Fuente: GET /api/facturas?estado=&desde=&hasta=
- Paginación: 25 por página, server-side
- Orden default: fecha descendente

## Estados
- Cargando: skeleton de tabla (5 filas)
- Vacío: ilustración + "No hay facturas" + botón "Crear primera"
- Error de red: toast + botón "Reintentar"
- Sin resultados tras filtro: "No hay facturas que coincidan con los filtros"

## Responsive
- Desktop (≥1024): filtros horizontales, tabla completa
- Tablet (640-1023): filtros colapsables, tabla con scroll horizontal
- Mobile (<640): filtros en modal, tabla reemplazada por cards

## Accesibilidad
- Foco inicial: en el campo de búsqueda
- Tab order: Buscar → Fecha → Estado → Nueva factura → Tabla
- Contraste AA en toda la pantalla
- Tabla navegable por teclado (flechas entre celdas)

## Analytics
- Evento: "facturas_view" al cargar
- Evento: "filtro_aplicado" con payload {filtro, valor}

## Qué NO hace esta pantalla
- No permite editar facturas (eso vive en /facturas/:id)
- No muestra totales agregados (eso vive en /dashboard)
```

## Prompts canónicos para handoff

Tres plantillas listas para usar con agentes de IA en el handoff. Copia, reemplaza los corchetes, envía.

### Prompt 1 — Wireframe textual (antes de implementar)

```text
Actúa como diseñador UX. Lee DESIGN.md y design-tokens.json antes de responder.

Tarea: describir un wireframe textual (sin código) para:

[Pantalla o flujo: ej. "listado de facturas con filtros por estado y fecha"]

Requisitos:
- Audiencia: [perfil].
- Acciones primarias: [1-3 acciones].
- Datos visibles por fila: [campos mínimos].
- Estados a cubrir: default, cargado, vacío, error, permiso denegado.

Entrega:
1. Árbol de la pantalla (header, filtros, contenido, acciones) con tokens de spacing.
2. Jerarquía visual (qué es primario, secundario, terciario).
3. Comportamiento en los 3 breakpoints.
4. Lista de componentes del sistema que reutilizarías.
5. Componentes nuevos a crear (si los hay) con brief resumido.

No escribas código. Usa exclusivamente tokens existentes.
```

### Prompt 2 — Implementar desde spec (durante desarrollo)

```text
Lee DESIGN.md, design-tokens.json y la spec adjunta.

Tarea: implementar [NombrePantalla] según la spec.

Stack:
- [React 18 + TypeScript / Blazor / Vue 3]
- [Tailwind v4 / CSS Modules]
- [Testing: Vitest + Testing Library / bUnit]

Restricciones:
- Solo tokens de design-tokens.json. Si falta un valor, detén y pregunta.
- Cubre TODOS los estados de la spec (default, loading, empty, error, ...).
- Componentes reutilizados se importan; no se duplican.
- Incluye pruebas de: render, estados, navegación por teclado.

Entrega:
1. Código de la pantalla.
2. Código de componentes nuevos (si los hay) con sus briefs.
3. Pruebas.
4. Lista de decisiones tomadas fuera de la spec — para revisión humana.
```

### Prompt 3 — Revisión heurística (antes de merge)

```text
Actúa como revisor UX. Lee DESIGN.md antes de responder.

Tarea: revisión heurística de [captura / URL / descripción] contra:
- 10 heurísticas de Nielsen.
- WCAG 2.2 nivel AA.
- La tabla de 7 estados de nuestro sistema.

Entrega una tabla ordenada de severidad mayor a menor:

| Heurística | Severidad (0-4) | Observación | Recomendación |

Severidad:
- 0: no aplica
- 1: cosmético
- 2: menor, corregible cuando se pueda
- 3: mayor, alta prioridad
- 4: catastrófico — bloquea flujo o vulnera accesibilidad

Si falta contexto para juzgar algo, dilo explícitamente ("no puedo evaluar el estado
de error sin ver cómo se ve cuando la API falla").
```

## Errores clásicos de handoff

| Error | Qué pasa | Cómo evitarlo |
|-------|----------|---------------|
| "Usa el color de marca" sin token | Desarrollo hardcodea `#0d4d92`, la marca cambia, hay que buscar literales | Siempre citar el token: `color.brand.primary` |
| Especificar solo desktop | Mobile sale al azar | Incluir los 3 breakpoints o declarar "solo desktop" explícitamente |
| Omitir estado vacío | Primera vez que la tabla tiene 0 filas: pantalla en blanco | Estados loading/empty/error son obligatorios en todo contenedor |
| "Que se vea bonito en móvil" | Sin spec, cada developer lo resuelve distinto | Describir qué se oculta, qué se reordena, qué cambia de tamaño |
| Figma que no coincide con el repo | Desarrollo copia de Figma, QA compara contra Figma, pero Figma no fue actualizado | Handoff acaba cuando alguien marca la spec como "implementada" y archiva |

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** producir especificaciones de pantalla tan completas que un implementador (humano o agente) pueda construir sin preguntar.

**Entradas:**
- Wireframe o diseño visual.
- `DESIGN.md` y `design-tokens.json` actualizados.
- Lista de componentes del sistema disponibles.

**Pasos:**
1. Redactar la spec con las 8 secciones propuestas; no dejar TBD.
2. Listar los 7 estados para cada contenedor; documentar "no aplica" cuando corresponda.
3. Especificar los 3 breakpoints; describir qué cambia en cada uno.
4. Citar tokens — nunca píxeles literales, nunca nombres de color.
5. Entregar junto al diseño una sección "Qué NO hace esta pantalla".
6. Validar con el implementador antes de cerrar handoff.

**Salidas:**
- Documento de spec versionado junto al código.
- Lista de tokens nuevos (si el diseño los requirió).
- Confirmación de handoff cerrado tras implementación.

**Errores comunes:**
- Describir solo el estado default, dejando loading/empty/error al implementador.
- Escribir "responsive" sin definir breakpoints ni qué cambia.
- Usar valores literales en lugar de tokens.
- Cerrar handoff sin que el implementador haya confirmado que puede construir con lo entregado.

**Referencias cruzadas:**
- [3.2.1 Design tokens y estándares](./01-design-tokens-y-estandares.md)
- [3.2.2 Arquitectura de componentes](./02-arquitectura-de-componentes.md)
- [6.5 Diseño de prompts y verificación](../../colaboracion-con-agentes-ia/05-diseno-de-prompts-y-verificacion.md)

</div>

---

## Glosario

**Handoff** *(handoff)* — transferencia de una pantalla o componente desde diseño hacia implementación. Incluye spec, tokens, estados, y criterios de aceptación.

**Spec** *(specification)* — documento que detalla estructura, comportamiento, estados y datos de una pantalla. Es el contrato entre diseño y desarrollo.

**Breakpoint** *(breakpoint)* — umbral de ancho de viewport donde el layout cambia (típicamente mobile / tablet / desktop).

**Skeleton** *(skeleton screen)* — placeholder visual que imita la estructura del contenido mientras se carga, reduciendo la percepción de espera.

**Revisión heurística** *(heuristic evaluation)* — método donde un experto inspecciona la UI contra un conjunto de principios (típicamente las 10 heurísticas de Nielsen).

:::info Referencias primarias
- [Nielsen Norman Group — 10 Usability Heuristics](https://www.nngroup.com/articles/ten-usability-heuristics/)
- [WCAG 2.2 — Web Content Accessibility Guidelines](https://www.w3.org/TR/WCAG22/)
- [Material Design — States](https://m3.material.io/foundations/interaction/states/overview)
- [Figma — Handoff best practices](https://help.figma.com/hc/en-us/articles/360040028114)
:::

---

<AuthorCredit />
