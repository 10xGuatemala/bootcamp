# DESIGN.md

## Propósito
Contexto de diseño para agentes de IA que generen o revisen UI de este producto.
Mantener bajo 400 líneas. Si crece, mover detalles a archivos hermanos enlazados.

## Producto
- **Nombre:** [Producto]
- **Audiencia:** [perfil — nivel técnico, contexto, dispositivo principal]
- **Tono visual:** [ej. "sobrio institucional", "cálido B2C", "técnico denso"]
- **Principios:** [3-5 principios accionables, no adjetivos]
  - Claridad sobre densidad — preferimos una pantalla con menos datos bien jerarquizados a una con todo visible.
  - Explicitud sobre inferencia — estados, errores y vacíos siempre tienen texto.
  - Consistencia sobre creatividad — una tabla se ve como todas las tablas.

## Sistema visual
- **Tokens:** ver `design-tokens.json` (fuente de verdad).
- **Tipografía base:** ver token `font.family.base`.
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
