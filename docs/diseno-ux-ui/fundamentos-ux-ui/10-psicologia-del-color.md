---
sidebar_position: 10
sidebar_label: 3.1.10 Psicología del color
---

# Psicología del color

Rosa termina un cobro. En la esquina aparece un mensaje con un borde rojo. Antes de leer la frase, ya supo que algo está mal — el rojo se lo dijo. Si el borde hubiera sido verde, habría asumido que todo está correcto y habría despedido a la clienta sin confirmar que el pago realmente entró.

El color en una interfaz no es decoración: **es código visual que comunica significado** antes de que el usuario lea una sola palabra. Un botón rojo sugiere acción irreversible; un borde verde sugiere que algo está correcto; un fondo amarillo sugiere precaución. Esa lectura ocurre en milisegundos y modela la percepción de todo lo demás.

Este módulo no trata de "qué color queda bonito" — eso es un criterio subjetivo y de marca. Trata de cómo **elegir colores que funcionen como sistema semántico**: que comuniquen estado, jerarquía y acción de forma predecible y accesible.

## Qué comunica el color

Los humanos asociamos colores con estados emocionales y con categorías funcionales. Estas asociaciones están moldeadas por biología (el rojo como alerta, el verde como vida) y por cultura (significados que varían entre regiones). En productos digitales, el uso consistente crea un "idioma" que el usuario aprende:

| Color | Uso funcional típico | Emoción dominante |
|-------|---------------------|-------------------|
| **Rojo** | Error, acción destructiva, alerta crítica | Urgencia, peligro |
| **Verde** | Éxito, confirmación, estado saludable | Seguridad, progreso |
| **Amarillo / ámbar** | Advertencia, atención sin bloqueo | Precaución |
| **Azul** | Información, enlaces, identidad institucional | Confianza, calma |
| **Gris** | Contenido secundario, estados inactivos | Neutralidad |
| **Morado** | Diferenciación, énfasis no convencional | Creatividad |

Son patrones, no reglas absolutas. Lo importante es la **consistencia dentro de tu producto**: si el verde significa "éxito" en una pantalla, no puede significar "acción primaria" en otra.

## El contexto cultural importa

Los significados no son universales. En Occidente el blanco se asocia con limpieza o neutralidad; en varias culturas asiáticas se asocia con luto. El rojo es auspicioso en China y peligro en la mayoría de contextos occidentales.

Para productos con audiencia internacional:

- No construyas mensajes críticos **solo** sobre color (usa también iconos y texto).
- Revisa las asociaciones dominantes en tus mercados prioritarios.
- Prioriza la accesibilidad y la semántica funcional sobre asociaciones culturales específicas.

## Roles funcionales en un sistema

Un sistema de diseño maduro no define "colores bonitos"; define **roles semánticos** que luego se implementan con valores concretos de color. Esto permite cambiar la paleta sin tocar la lógica de diseño.

### Colores primarios y de marca

- **Primary**: el color de acción principal. Botones CTA, enlaces, elementos activos.
- **Secondary**: acciones secundarias y elementos de énfasis menor.
- **Brand**: identidad visual (header, logo). No siempre coincide con primary.

### Colores semánticos

- **Success** (verde): confirmaciones, estados saludables.
- **Warning** (amarillo/ámbar): precauciones, avisos no bloqueantes.
- **Error / Danger** (rojo): errores, acciones destructivas.
- **Info** (azul): mensajes informativos, ayudas contextuales.

### Colores neutros

Los grises son el músculo silencioso de la interfaz. Una paleta de 6–10 grises bien calibrados hace el 70 % del trabajo visual:

- Fondos (`bg-primary`, `bg-secondary`, `bg-tertiary`).
- Bordes (`border-subtle`, `border-default`, `border-strong`).
- Texto (`text-primary`, `text-secondary`, `text-muted`, `text-disabled`).

Un sistema con solo 3 grises fuerza a que todo se sienta "plano" o "sobrecontrastado". Un sistema con 20 grises indica falta de criterio. El rango útil está en la mitad.

## Contraste y accesibilidad

El color no comunica igual a todas las personas. Cerca del 8 % de los hombres y el 0,5 % de las mujeres tienen algún tipo de daltonismo. Además, condiciones de iluminación, brillo de pantalla y edad afectan cómo se percibe el color.

Por eso, **el color nunca debe ser el único canal de información**:

- Un error no es solo un borde rojo: también es un icono y un mensaje textual.
- Un enlace no es solo azul: también es subrayado o indicado con peso tipográfico.
- Un checkbox seleccionado no es solo de color distinto: tiene una marca (✓) dentro.

### Ratios de contraste WCAG

El estándar [WCAG 2.2](https://www.w3.org/TR/WCAG22/) define ratios mínimos de contraste entre texto y fondo:

| Nivel | Texto normal | Texto grande (≥18 pt o 14 pt bold) |
|-------|-------------:|-----------------------------------:|
| **AA** (mínimo legal) | 4.5:1 | 3:1 |
| **AAA** (ideal) | 7:1 | 4.5:1 |

Herramientas para medir: [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/), plugins de Figma como Stark, o extensiones de navegador.

Un contraste insuficiente es la falla de accesibilidad más común en productos digitales.

## Paleta como sistema, no como lista

Un error frecuente es definir la paleta como una lista plana de 12 colores. El resultado es caótico: cada diseñador elige el que "se ve mejor".

Una paleta bien estructurada tiene:

1. **Escalas**: cada color tiene variantes (50, 100, 200… 900). Los tonos claros se usan para fondos y estados hover; los medios para texto secundario; los oscuros para texto principal y bordes fuertes.
2. **Tokens semánticos**: `color-action-primary` en vez de `blue-500`. El valor apunta a la escala, pero el nombre describe la función.
3. **Reglas de uso documentadas**: *"El primary-500 se usa solo en botones primarios; nunca en texto corrido"*.

Ver [3.2.1 Design tokens y estándares](../sistemas-de-diseno/01-design-tokens-y-estandares.md) para cómo implementar esto en código.

## Estados y el color

Cada elemento interactivo necesita variantes de color por estado:

- **Default**: en reposo.
- **Hover**: mouse encima (en desktop).
- **Active / pressed**: en el momento del clic.
- **Focus**: con teclado; debe ser muy visible para accesibilidad.
- **Disabled**: bajo contraste, sin interacción.

Una tabla del sistema suele resolverlos automáticamente ajustando luminosidad sobre el color base (por ejemplo, hover = 10 % más oscuro que default).

## Errores frecuentes

- **Confiar solo en color** para indicar estado (rompe accesibilidad).
- **Paletas demasiado amplias** que nadie entiende cómo aplicar.
- **Inconsistencias semánticas**: mismo color con dos significados distintos.
- **Rojos y verdes indistinguibles** para daltonismo deuteranopia/protanopia.
- **Colores de marca saturados como colores de acción**: agotan visualmente si cubren áreas grandes.

:::warning Rosa bajo las luces del salón
El teléfono de Rosa refleja las luces fluorescentes del salón y, a veces, la luz directa de la ventana. Un texto gris claro sobre fondo blanco que se ve bien en tu pantalla de oficina puede ser invisible para ella. Siempre valida contrastes bajo condiciones reales de uso, no solo en el monitor del diseñador.
:::

## Puente al siguiente módulo

El color resuelve significado y estado. Pero una interfaz **se lee**: el usuario pasa más tiempo procesando texto que interpretando colores. Por eso la siguiente palanca es la tipografía — su escala, su peso y su legibilidad hacen la diferencia entre un producto profesional y uno que cansa leer. Eso es el [módulo 3.1.11](./11-tipografia.md).

## Glosario

**Color semántico** — color asignado a un significado funcional (éxito, error), no a una preferencia estética.

**Token de color** — nombre referenciable (ej. `color-action-primary`) que apunta a un valor de color y permite cambiar implementación sin tocar código.

**Escala de color** — serie de variantes del mismo tono (ej. 50–900) ordenadas por luminosidad.

**Contraste** *(Contrast)* — relación numérica entre luminosidad de dos colores; determina legibilidad.

**Daltonismo** *(Color blindness)* — conjunto de condiciones que afectan la percepción de ciertos colores; la más común es no distinguir rojo de verde.

:::info Referencias primarias
- [Material Design · Color system](https://m3.material.io/styles/color/overview) — sistema de referencia.
- [WCAG 2.2 · Contrast](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum) — requisitos de contraste.
- [Refactoring UI · Color](https://www.refactoringui.com/) — guía práctica de paletas.
- [Stripe Design · Accessible color systems](https://stripe.com/blog/accessible-color-systems) — caso real.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** definir y aplicar una paleta de color que comunique significado de forma consistente y accesible.

**Entradas:**
- Identidad de marca existente (colores principales).
- Requisitos de accesibilidad (nivel WCAG objetivo).
- Contextos de uso (modo claro, modo oscuro, impresión).
- Inventario de estados que necesitan color (error, éxito, advertencia, info, disabled).

**Pasos:**
1. Derivar escalas para cada color principal (50–900) con luminosidades consistentes.
2. Definir tokens semánticos (primary, success, warning, error, info, neutrales).
3. Verificar contraste texto/fondo contra WCAG AA (mínimo) o AAA (ideal) para cada par usado.
4. Validar con simuladores de daltonismo (deuteranopia, protanopia, tritanopia).
5. Documentar reglas de uso: dónde sí, dónde no.
6. Auditar el producto actual contra la paleta y marcar deudas.

**Salidas:**
- Paleta en formato de tokens (ver 3.2.1) con escalas y semánticos.
- Tabla de contrastes validados.
- Guía de uso con ejemplos correctos e incorrectos.
- Lista de inconsistencias detectadas en producto actual.

**Errores comunes:**
- Definir la paleta como lista plana sin escalas ni tokens semánticos.
- Aprobar contrastes "a ojo" sin verificar ratios.
- Usar color como único canal de comunicación.
- Inflar la paleta con tonos que nadie sabe cuándo usar.
- Olvidar estados (hover, focus, disabled) en la definición inicial.

**Referencias cruzadas:**
- [3.1.11 Tipografía](./11-tipografia.md)
- [3.1.12 Jerarquía visual y Gestalt](./12-jerarquia-visual-y-gestalt.md)
- [3.1.13 Accesibilidad básica](./13-accesibilidad-basica.md)
- [3.2.1 Design tokens y estándares](../sistemas-de-diseno/01-design-tokens-y-estandares.md)
</div>
