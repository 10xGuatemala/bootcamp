---
sidebar_position: 11
sidebar_label: 3.1.11 Tipografía
---

# Tipografía

El color le dijo a Rosa que algo estaba mal. La tipografía es la que le dirá **qué** está mal, **qué hacer** y **qué pasará si ignora el aviso**. Si los títulos se confunden con el cuerpo, si el mensaje de error está en una fuente ilegible, o si el botón de acción es tan pequeño como el texto secundario, la pantalla falla aunque los colores estén bien elegidos.

La tipografía hace el 80 % del trabajo visual de una interfaz. Si la jerarquía tipográfica está clara, el usuario entiende qué es título, qué es cuerpo y qué es secundario **antes de leer**. Si está mal, ningún color o espaciado lo salva: el ojo no encuentra dónde descansar ni dónde empezar.

Este módulo cubre lo mínimo útil: anatomía, cómo construir una escala modular, emparejamiento, legibilidad y convenciones para UI.

## Anatomía básica

Los términos que aparecen al elegir y configurar una tipografía:

| Término | Qué es | Por qué importa |
|---------|--------|-----------------|
| **Serif** | Fuente con remates (ej. Georgia, Merriweather) | Transmite tradición; suele usarse en títulos largos |
| **Sans-serif** | Sin remates (ej. Inter, Source Sans) | Legibilidad en pantalla; default de la mayoría de UI |
| **Mono / monospace** | Todos los caracteres tienen el mismo ancho (ej. JetBrains Mono) | Código, datos tabulares |
| **Peso (weight)** | Espesor del trazo: 100 (thin) → 900 (black) | Define jerarquía sin cambiar tamaño |
| **Altura de la x** | Altura de las minúsculas | Afecta legibilidad a tamaños pequeños |
| **Interlineado / line-height** | Espacio vertical entre líneas | Clave para texto largo |
| **Tracking / letter-spacing** | Espacio horizontal entre caracteres | Ajustes finos en títulos grandes |
| **Kerning** | Ajuste de espacio entre pares específicos | Detalle tipográfico, automático en fuentes modernas |

## Escala modular

Una escala es el conjunto de tamaños disponibles en tu sistema. Usar tres o cuatro tamaños distintos crea jerarquía; usar doce produce caos. La mayoría de sistemas maduros definen entre **5 y 7 tamaños**.

Una escala común basada en una razón de 1.25 (*major third*):

| Token | Tamaño | Uso |
|-------|-------:|-----|
| `text-xs` | 12 px | Etiquetas, notas al pie |
| `text-sm` | 14 px | Texto secundario, tablas densas |
| `text-base` | 16 px | Cuerpo por defecto |
| `text-lg` | 18 px | Destaque moderado |
| `text-xl` | 20 px | Subtítulos |
| `text-2xl` | 24 px | Títulos de sección |
| `text-3xl` | 32 px | Títulos de página |
| `text-4xl` | 40 px | Display, landings |

Razones comunes: 1.125 (*major second*, muy conservador), 1.25 (default equilibrado), 1.333 (*perfect fourth*, contraste alto). Elige una razón y aplícala en todo el producto.

## Pesos y jerarquía

Dos o tres pesos suelen bastar:

- **Regular (400)**: cuerpo.
- **Medium (500)** o **Semibold (600)**: énfasis, etiquetas, títulos pequeños.
- **Bold (700)**: títulos principales.

Evita mezclar 5 pesos distintos — hace la jerarquía ilegible. Si necesitas más contraste, cambia tamaño antes que peso.

## Emparejamiento (pairing)

La regla más segura: **una sola familia tipográfica** en toda la UI. Una fuente sans-serif moderna (Inter, Source Sans, IBM Plex Sans) con múltiples pesos resuelve el 95 % de los casos.

Si quieres dos familias, la convención estándar es:

- **Display / títulos**: fuente con personalidad (ej. Space Grotesk, Merriweather).
- **Cuerpo**: fuente legible neutra (ej. Inter, Source Sans).

Reglas al emparejar:

- Contraste claro entre las dos familias (no pares dos sans-serif muy parecidas).
- Proporciones compatibles (altura-x similar).
- Una para títulos, la otra para texto corrido — no mezclar por capricho.

Este proyecto usa `Source Sans 3` para cuerpo y `Space Grotesk` para títulos (ver `package.json`): ejemplo de pairing funcional.

## Legibilidad en pantalla

### Tamaño mínimo

- **16 px** como cuerpo por defecto. Por debajo de 14 px, muchos usuarios necesitan zoom.
- **12 px es el piso absoluto** — solo para metadatos (timestamps, captions).

### Longitud de línea (measure)

Texto corrido legible: **45–75 caracteres por línea**. En interfaces:

- Texto muy angosto (< 40 caracteres) fuerza saltos de línea incómodos.
- Texto muy ancho (> 80 caracteres) cansa al regresar a la siguiente línea.

Para contenido tipo documentación, `max-width: 70ch` es una apuesta segura.

### Interlineado

- Cuerpo: `line-height` 1.5–1.7.
- Títulos: 1.1–1.3 (más apretado; los títulos respiran con espacio exterior, no interior).

Un line-height de 1.0 en texto corrido es un error clásico: las líneas se encaraman y la lectura se vuelve agotadora.

### Peso del texto

El color del texto corrido influye tanto como la tipografía:

- `text-primary` — gris muy oscuro (no negro puro; reduce fatiga visual).
- `text-secondary` — gris medio para información complementaria.
- Evita negro `#000` sobre blanco `#fff` para párrafos largos: el contraste es innecesariamente duro.

## Tipografía en formularios

- Labels: mismo tamaño que cuerpo, medium o semibold para destacar.
- Inputs: mismo tamaño que cuerpo (16 px mínimo en mobile para evitar zoom automático en iOS).
- Placeholders: **nunca** como única etiqueta — desaparecen al escribir. Úsalos solo como pista adicional.
- Mensajes de error: mismo tamaño que el input, color semántico de error, debajo del campo.

## Tipografía en datos y tablas

Las tablas con números deben usar **variante tabular** (*tabular figures*, `font-variant-numeric: tabular-nums;`). Así los dígitos ocupan el mismo ancho y las columnas alinean perfectamente.

Para código: fuente monospaced + mayor interlineado (1.6) que el cuerpo. El código es texto que se explora, no se lee linealmente.

## Rendimiento

Las fuentes web pesan. Carga solo lo que usas:

- **Subconjuntos**: incluye solo los caracteres necesarios (Latin, Latin-extended).
- **Pesos mínimos**: 2–3 pesos por familia, no los 9 disponibles.
- **Preload** de las fuentes críticas para evitar *flash of unstyled text* (FOUT).
- **`font-display: swap`** para que el contenido aparezca con fuente del sistema mientras carga la personalizada.

### No sirvas desde Google Fonts en producción

Embeber `<link>` directo a `fonts.googleapis.com` ya **no** es la opción eficiente. Cada visita agrega una conexión adicional (DNS + TLS + HTTP a un tercer dominio), impide el *preload* confiable de la fuente porque el CSS intermedio se descubre tarde, y —desde Chrome 86 en adelante— ya no comparte caché entre sitios, por lo que el supuesto beneficio del CDN compartido desapareció. A eso se suma el costo en privacidad (consulta por IP en cada render) y un obstáculo real para cumplir GDPR en la UE.

La alternativa recomendada hoy:

- **Alojar la fuente localmente** en tu propio dominio, con `font-display: swap` y `preload` sobre el `woff2` de mayor prioridad.
- **Usar [Fontsource](https://fontsource.org/)** (o [@fontsource/*](https://www.npmjs.com/org/fontsource) en npm), que empaqueta las mismas familias abiertas que ofrece Google pero como dependencias locales, con subconjuntos y pesos seleccionables y *tree-shaking* natural en un bundler moderno.

Este proyecto usa `@fontsource/*` (ver `package.json`) por esa razón: misma familia, cero requests a terceros, control total sobre qué pesos y subconjuntos entran al bundle.

:::tip Una sola familia casi siempre alcanza
Emparejar dos familias se siente sofisticado pero añade riesgo: más peso de red, más inconsistencias, más decisiones. Una sans-serif moderna con 2–3 pesos resuelve el 95 % de los productos. Empieza ahí y justifica si necesitas una segunda familia.
:::

## Puente al siguiente módulo

Color y tipografía tienen ahora tokens y reglas. Pero una pantalla con paleta y tipografía correctas puede seguir siendo confusa si el ojo no sabe dónde mirar primero. Lo que falta es la composición: la **jerarquía visual** que decide el orden del recorrido. Eso es el [módulo 3.1.12](./12-jerarquia-visual-y-gestalt.md).

## Glosario

**Familia tipográfica** *(Typeface / font family)* — conjunto de estilos relacionados (Regular, Bold, Italic) con diseño coherente.

**Fuente** *(Font)* — implementación específica de una familia (archivo .woff2).

**Escala tipográfica** *(Type scale)* — set de tamaños disponibles en el sistema de diseño.

**Altura de la x** *(x-height)* — altura de las letras minúsculas; clave para legibilidad.

**Line-height** *(Interlineado)* — espacio vertical entre líneas, expresado como múltiplo del tamaño de fuente.

**Measure** *(Longitud de línea)* — ancho de la columna de texto, medido en caracteres por línea.

:::info Referencias primarias
- [Practical Typography (Matthew Butterick)](https://practicaltypography.com/) — referencia detallada.
- [Google Fonts](https://fonts.google.com/) — biblioteca libre con optimización.
- [Modular Scale](https://www.modularscale.com/) — calcular razones de escala.
- [Refactoring UI · Typography](https://www.refactoringui.com/) — patrones aplicados.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** definir un sistema tipográfico consistente que soporte jerarquía, legibilidad y rendimiento.

**Entradas:**
- Identidad de marca (si hay familia ya definida).
- Contextos principales (cuerpo de texto, títulos, datos tabulares, código).
- Restricciones de rendimiento (móvil, red lenta).
- Requisitos de accesibilidad (tamaño mínimo, contraste).

**Pasos:**
1. Seleccionar 1 (o máximo 2) familias tipográficas; justificar la decisión.
2. Definir la escala: razón y tokens (`text-xs` a `text-4xl`).
3. Definir pesos disponibles (2–3) y su uso semántico.
4. Establecer line-heights por tipo de contenido (cuerpo, título, código).
5. Documentar reglas para formularios, tablas, código, estados.
6. Configurar carga optimizada (subset, preload, font-display).
7. Auditar el producto actual contra la escala y registrar deudas.

**Salidas:**
- Escala tipográfica documentada como tokens.
- Guía de aplicación por tipo de contenido.
- Configuración de carga de fuentes en el proyecto.
- Inventario de inconsistencias en el producto actual.

**Errores comunes:**
- Mezclar demasiadas familias o pesos.
- Usar tamaños ad-hoc fuera de la escala.
- Placeholders como única etiqueta en formularios.
- Texto corrido en negro puro sobre blanco puro.
- No usar `tabular-nums` en columnas numéricas.
- Cargar todos los pesos aunque solo uses 3.

**Referencias cruzadas:**
- [3.1.10 Psicología del color](./10-psicologia-del-color.md)
- [3.1.12 Jerarquía visual y Gestalt](./12-jerarquia-visual-y-gestalt.md)
- [3.1.13 Accesibilidad básica](./13-accesibilidad-basica.md)
- [3.2.1 Design tokens y estándares](../sistemas-de-diseno/01-design-tokens-y-estandares.md)
</div>
