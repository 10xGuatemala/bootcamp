---
sidebar_position: 1
sidebar_label: 3.2.1 Design tokens y estándares
---

# Design tokens y estándares

:::tip Archivos copiables
La plantilla de `DESIGN.md` vive como archivo real en [`examples-md/design/`](https://github.com/10xGuatemala/bootcamp/tree/main/examples-md/design). Cópiala a tu repo y adáptala. Los fragmentos JSON de tokens y de configuración de Style Dictionary se muestran en los bloques de código de este módulo: pégalos directamente en tu proyecto cuando corresponda.
:::

Un sistema de diseño sin tokens es una guía de estilo decorativa: el equipo la cita en reuniones y la ignora en producción. Los **design tokens** convierten decisiones visuales (color primario, espaciado base, radio de esquinas) en **valores nombrados y versionados** que viven en un archivo, se exportan a múltiples plataformas y funcionan como fuente única de verdad para humanos y agentes.

Este módulo muestra cómo estructurar tokens usando el estándar [Design Tokens Community Group (DTCG)](https://design-tokens.github.io/community-group/format/), exportarlos con [Style Dictionary](https://amzn.github.io/style-dictionary/), y complementarlos con un archivo `DESIGN.md` que da el contexto que los tokens solos no pueden capturar.

## Por qué tokens y no variables CSS

Las variables CSS (`--color-primary: #0d4d92;`) resuelven un problema: evitar repetir el literal. Los tokens resuelven otros cuatro que las variables no tocan:

1. **Multiplataforma.** Un mismo token (`color.brand.primary`) se exporta a CSS, SCSS, JavaScript, iOS (Swift), Android (XML), Flutter, o Figma. La plataforma cambia; el nombre no.
2. **Semántica.** `color.semantic.danger` es más estable que `color.red.600`. Si mañana la marca cambia a naranja como color de error, el token sigue siendo `danger`.
3. **Versionado.** El archivo de tokens vive en el repo y se revisa por pull request. Cada cambio de paleta tiene autor, fecha y motivo.
4. **Procesabilidad por agentes.** Un agente que lee `design-tokens.json` no inventa colores: cita los que existen. Si necesita un valor que no hay, se detiene y pregunta.

## Estructura de un token

El formato DTCG define cada token como un objeto con al menos `$value`, más metadatos opcionales (`$description`, `$type`):

```json
{
  "color": {
    "brand": {
      "primary": {
        "$value": "#0d4d92",
        "$type": "color",
        "$description": "Acción principal, enlaces, foco"
      }
    }
  }
}
```

Los tokens se agrupan jerárquicamente. La convención es **tres niveles**: categoría → subcategoría → nombre. Más profundo se vuelve ruido; menos profundo genera colisiones.

## Ejemplo completo — `design-tokens.json`

Este archivo es **un punto de partida**, no una paleta recomendada. Reemplaza los valores con los de tu marca; la estructura sí es la que recomendamos mantener.

```json
{
  "$schema": "https://design-tokens.github.io/community-group/format/",
  "color": {
    "brand": {
      "primary":   { "$value": "#0d4d92", "$description": "Acción principal, enlaces, foco" },
      "secondary": { "$value": "#ef9b50", "$description": "Acento — nunca para acciones críticas" }
    },
    "neutral": {
      "900": { "$value": "#1a2f4d" },
      "700": { "$value": "#3a4a66" },
      "500": { "$value": "#6b7a94" },
      "300": { "$value": "#c8d0de" },
      "100": { "$value": "#f2f4f8" },
      "000": { "$value": "#ffffff" }
    },
    "semantic": {
      "success": { "$value": "#2f8f5a" },
      "warning": { "$value": "#b5791f" },
      "danger":  { "$value": "#b33a3a" },
      "info":    { "$value": "#2b6cb0" }
    }
  },
  "font": {
    "family": {
      "base":    { "$value": "'Source Sans 3', system-ui, sans-serif" },
      "heading": { "$value": "'Space Grotesk', system-ui, sans-serif" },
      "mono":    { "$value": "'JetBrains Mono', ui-monospace, monospace" }
    },
    "size": {
      "xs":   { "$value": "0.75rem" },
      "sm":   { "$value": "0.875rem" },
      "base": { "$value": "1rem" },
      "lg":   { "$value": "1.125rem" },
      "xl":   { "$value": "1.25rem" },
      "2xl":  { "$value": "1.5rem" },
      "3xl":  { "$value": "1.875rem" }
    },
    "weight": {
      "regular":  { "$value": 400 },
      "medium":   { "$value": 500 },
      "semibold": { "$value": 650 },
      "bold":     { "$value": 700 }
    }
  },
  "spacing": {
    "0": { "$value": "0" },
    "1": { "$value": "0.25rem" },
    "2": { "$value": "0.5rem" },
    "3": { "$value": "0.75rem" },
    "4": { "$value": "1rem" },
    "6": { "$value": "1.5rem" },
    "8": { "$value": "2rem" }
  },
  "radius": {
    "sm":   { "$value": "0.25rem" },
    "md":   { "$value": "0.5rem" },
    "lg":   { "$value": "1rem" },
    "full": { "$value": "9999px" }
  },
  "shadow": {
    "sm": { "$value": "0 1px 2px rgba(0,0,0,0.06)" },
    "md": { "$value": "0 4px 12px rgba(0,0,0,0.08)" },
    "lg": { "$value": "0 12px 32px rgba(0,0,0,0.12)" }
  }
}
```

## Exportar con Style Dictionary

[Style Dictionary](https://amzn.github.io/style-dictionary/) es una herramienta de Amazon que lee un archivo DTCG y genera artefactos para cualquier plataforma. Configuración mínima:

```json
// config.json
{
  "source": ["design-tokens.json"],
  "platforms": {
    "css": {
      "transformGroup": "css",
      "buildPath": "build/css/",
      "files": [{ "destination": "_tokens.css", "format": "css/variables" }]
    },
    "js": {
      "transformGroup": "js",
      "buildPath": "build/js/",
      "files": [{ "destination": "tokens.js", "format": "javascript/es6" }]
    },
    "tailwind": {
      "transformGroup": "js",
      "buildPath": "build/tailwind/",
      "files": [{ "destination": "tokens.js", "format": "javascript/module" }]
    }
  }
}
```

Tras ejecutar `style-dictionary build`, obtienes:

- `build/css/_tokens.css` → `--color-brand-primary: #0d4d92;`
- `build/js/tokens.js` → `export const ColorBrandPrimary = "#0d4d92";`
- `build/tailwind/tokens.js` → objeto listo para `theme.extend` en `tailwind.config.js`.

El mismo archivo fuente alimenta tres plataformas. Si mañana añades iOS, añades una plataforma al config; no reescribes tokens.

## `DESIGN.md` — contexto que los tokens no capturan

Los tokens dicen **qué valores existen**. `DESIGN.md` dice **cuándo usarlos**, **por qué son así**, y **qué está prohibido**. Es al diseño lo que `CLAUDE.md` es a la ingeniería: un archivo vivo en la raíz del repo que un agente lee antes de proponer cualquier pantalla.

```markdown
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

:::tip La sección "Lo que NO hacemos" es la más importante
Los sistemas de diseño se deterioran por **acumulación silenciosa**, no por decisiones explícitas. Alguien añade un componente "solo por esta vez", otro copia un color "porque se parece", un tercero introduce una librería "para ir rápido". Escribir los límites es lo único que detiene la deriva.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** establecer la infraestructura de tokens y contexto de diseño que permita a humanos y agentes producir UI consistente sin adivinar.

**Entradas:**
- Producto con al menos una UI existente (aunque sea un MVP).
- Paleta de marca y tipografía ya decididas.
- Herramienta de build (Node + npm/pnpm para Style Dictionary).

**Pasos:**
1. Inventariar los valores visuales que ya usa la UI actual (colores, tipografías, espaciados).
2. Escribir `design-tokens.json` con esos valores en formato DTCG.
3. Instalar y configurar Style Dictionary; generar `tokens.css` y cablearlo al CSS de la app.
4. Crear `DESIGN.md` en la raíz con las 7 secciones propuestas. No dejar TBD.
5. Probar: pedir a un agente que genere un componente usando solo `DESIGN.md` + `design-tokens.json`. Si reinventa valores, la documentación está incompleta.

**Salidas:**
- `design-tokens.json` versionado, alineado al 100% con la UI actual.
- `DESIGN.md` de 150-400 líneas, sin secciones TBD.
- Build pipeline que regenera artefactos al cambiar tokens.
- Reducción medible de "preguntas básicas" hechas por agentes o desarrolladores nuevos.

**Errores comunes:**
- Copiar los tokens del ejemplo sin adaptarlos a la marca.
- Generar tokens pero no cablearlos al CSS, dejándolos como documentación decorativa.
- Olvidar la sección "Lo que NO hacemos" en `DESIGN.md`.
- Crear tokens sin tipo semántico (`color.red.600` en vez de `color.semantic.danger`).

**Referencias cruzadas:**
- [3.1.14 Convenciones de UI consistentes](../fundamentos-ux-ui/14-convenciones-de-ui-consistentes.md)
- [3.2.2 Wireframes y prototipado](./02-wireframes-y-prototipado.md)
- [3.2.3 Arquitectura de componentes](./03-arquitectura-de-componentes.md)
- [6.2 Context engineering](../../colaboracion-con-agentes-ia/02-context-engineering-claude-md.md)

</div>

---

## Glosario

**Design token** *(design token)* — valor atómico del sistema visual (color, tamaño, espaciado) nombrado y versionado, que actúa como fuente única de verdad para todas las plataformas.

**DTCG** *(Design Tokens Community Group)* — grupo del W3C que define el formato estándar para archivos de tokens. El formato especifica campos como `$value`, `$type` y `$description`.

**Style Dictionary** *(style dictionary)* — herramienta de código abierto de Amazon que transforma un archivo de tokens en artefactos para múltiples plataformas (CSS, iOS, Android, Flutter).

**`DESIGN.md`** *(design context file)* — archivo de contexto de diseño en la raíz del repo, análogo a `CLAUDE.md`, que un agente lee antes de generar UI.

**Token semántico** *(semantic token)* — token nombrado por propósito (`danger`, `success`) en lugar de apariencia (`red-600`). Sobrevive a cambios de paleta.

:::info Referencias primarias
- [Design Tokens Community Group — Format Specification](https://design-tokens.github.io/community-group/format/)
- [Style Dictionary — Amazon](https://amzn.github.io/style-dictionary/)
- [W3C Design Tokens Module](https://www.w3.org/community/design-tokens/)
- [Figma — Design Tokens best practices](https://www.figma.com/best-practices/tokens-in-design-systems/)
:::

---

<AuthorCredit />
