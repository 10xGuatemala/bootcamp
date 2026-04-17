# Convenciones de UI consistentes

Diseñar una pantalla bonita es un buen principio; diseñar **cientos de pantallas que se sientan como la misma aplicación** es el verdadero trabajo de UI. Las convenciones — acuerdos compartidos sobre cómo se ven y se comportan los elementos — son lo que distingue a un producto que transmite precisión y madurez de uno que transmite ruido e improvisación.

Este módulo reúne convenciones de UI que sobreviven a cambios de stack, de librería de componentes y de equipo. No hablamos de una herramienta en particular, sino de **los acuerdos que deberías tomar** antes de escribir la primera línea de estilos.

## Por qué las convenciones importan más que el estilo individual

Un equipo sin convenciones produce interfaces donde cada pantalla es una micro-decisión: este botón lleva icono, aquel no; este texto usa gris medio, aquel un gris distinto; una fecha se muestra como `2026-04-16`, otra como `16/04/26`. El usuario no puede articular qué está mal, pero percibe **inconsistencia**, y la inconsistencia comunica descuido.

Las convenciones resuelven esto por diseño: una vez acordadas, la decisión ya está tomada y el equipo puede concentrarse en el problema real.

## Iconografía consistente

**Regla general:** los botones de acción llevan un icono a la izquierda del texto. Los botones solo-icono (cerrar, menú, refrescar) son la excepción permitida, no la norma.

- Usa **una sola familia de iconos** en todo el producto (por ejemplo, una librería open source con cobertura amplia y estilo uniforme). Mezclar familias — unos con esquinas redondeadas, otros planos, otros con relleno — rompe la sensación de unidad.
- El icono refuerza la acción, no la reemplaza. *"Guardar"* con un icono de disquete es claro; un disquete solo es ambiguo.
- Elige un tamaño base y respétalo. Variaciones menores (16 px, 20 px, 24 px) para jerarquía; no introduzcas un cuarto tamaño solo porque "se ve mejor" en un lugar puntual.

**Patrón recomendado:**

```
[ 🔍 ] Buscar
[ ➕ ] Nuevo elemento
[ ✔ ] Confirmar
```

**Patrón a evitar:** texto seguido del icono, iconos de distintas familias en la misma pantalla, o botones de acción sin icono cuando el resto sí lo lleva.

## Tokens de color sobre valores hardcoded

La peor decisión que puede tomar un equipo de UI es escribir `#3B82F6` en 200 archivos distintos. Cuando llegue el rediseño — y siempre llega — el costo de cambiar el azul es proporcional a cuántas veces lo repetiste.

**Regla:** nunca un color literal en el código de producto. Siempre un token.

- Define una **paleta semántica** (no nombres de colores, sino roles): `primary`, `secondary`, `accent`, `muted`, `success`, `warning`, `destructive`, `foreground`, `background`.
- Los componentes consumen **roles**, no colores. Un botón primario usa `primary`, no `#0d4d92`. Un texto de error usa `destructive`, no `red-600`.
- Cuando un diseñador pide "ese mismo azul pero un poco más oscuro", la respuesta no es `#0a3d72`: es agregar `primary-dark` al sistema de tokens o cuestionar si realmente hace falta.

**Ventaja práctica:** cambiar de tema (claro/oscuro), rebrandear el producto o ajustar contraste para accesibilidad se vuelve una edición en un solo archivo.

### Negro puro: el error silencioso

Un detalle pequeño con impacto visible: **evita `#000000` para texto**. En pantallas retroiluminadas, el negro puro vibra y cansa. Usa grises oscuros suaves:

- Texto principal: tonos `#111111`, `#171717`, `#1f1f1f`.
- Texto secundario: grises `#525252`, `#666666`, `#737373`.

Lo mismo con el blanco: `#ffffff` sobre fondos claros puede producir halo. Considera `#fafafa` o `#f5f5f5` para superficies neutras.

## Jerarquía tipográfica y pesos

La tipografía es el sistema de señalización de tu interfaz: le dice al usuario qué es título, qué es cuerpo, qué es metadato. Si todo pesa igual, todo compite por atención y nada gana.

**Regla práctica:** usa una escala reducida de pesos y respétala.

- `400` (normal) para cuerpo y celdas de tabla.
- `500` (medium) para encabezados de tabla, etiquetas de formulario, botones secundarios.
- `600` (semibold) para títulos de sección y botones primarios.
- **Evita `700` (bold)** como peso principal. El *bold* puro compite con los títulos y hace que el texto se vea pesado; `semibold` suele comunicar jerarquía con menos ruido.

Los tamaños funcionan igual: define 4 a 6 tamaños (p. ej., 12, 14, 16, 20, 24, 32 px) y úsalos en toda la aplicación. Si necesitas un séptimo, probablemente el problema es de jerarquía, no de tamaño.

## Componentes propios antes que elementos nativos

En aplicaciones pequeñas es común usar `<input>`, `<textarea>`, `<select>` nativos del navegador. En aplicaciones medianas y grandes, esto se vuelve un problema: cada navegador los renderiza distinto, no respetan tus tokens de color, y cada nueva necesidad (contador de caracteres, icono de validación, estado de carga) obliga a parchar caso por caso.

**Regla:** cuando una aplicación va a crecer, envuelve los elementos de formulario en componentes propios desde el inicio.

- Un `<TextArea>` propio puede incluir contador de caracteres, límites consultados a backend, íconos de validación — una sola vez, consumido en todas partes.
- Un `<Button>` propio garantiza que el icono a la izquierda sea automático, que los estados (disabled, loading, danger) sean consistentes, que nadie pueda saltárselo.

El costo inicial (escribir el wrapper) se recupera la tercera vez que el equipo pide "agregar este detalle a todos los inputs".

## Qué comunica tu interfaz

Antes del primer pixel, define **qué debe sentir el usuario frente a tu producto**. Esta decisión guía cada duda posterior: color, peso tipográfico, espaciado, animaciones.

**Ejemplo — producto empresarial serio:**

- **Comunicar:** precisión, calma, tecnología madura, inteligencia sin ruido, claridad.
- **Evitar:** sobrecargado, agresivamente futurista, infantil, excesivamente corporativo tradicional, visualmente barato.

Cuando un equipo debate si un botón debería tener un *glow* animado o una tabla debería usar colores alternos vibrantes, la respuesta no es una votación: es volver a esta definición. ¿El *glow* animado comunica "precisión y calma"? No. Entonces no va.

Otros ejemplos posibles:

- **Producto infantil educativo:** juguetón, cálido, motivador, seguro. Evitar: frío, corporativo, clínico.
- **Producto médico:** confiable, legible, sin ambigüedad, clínico. Evitar: lúdico, ornamental, tendencias estéticas.

Sin esta definición, cada decisión se toma por gusto individual y el producto termina sin personalidad clara.

## Lista de verificación antes de aprobar una pantalla

Antes de declarar terminada una pantalla, revísala contra estas convenciones:

1. ¿Los botones de acción llevan icono a la izquierda, de la familia establecida y del tamaño establecido?
2. ¿No hay colores literales (`#xxxxxx`, `rgb(...)`) en el código? ¿Todo consume tokens?
3. ¿El texto usa los pesos definidos (`400`, `500`, `600`) y evita `700`?
4. ¿Los tamaños tipográficos pertenecen a la escala establecida?
5. ¿Los elementos de formulario usan los componentes propios, no los nativos?
6. ¿La pantalla es coherente con la intención emocional del producto?
7. ¿Un usuario que ya conoce otra pantalla del producto puede usar esta sin reaprender?

La séptima pregunta es la más importante: **las convenciones existen para que el aprendizaje en una pantalla se transfiera a todas las demás.**

## Resumen

- Las convenciones de UI son lo que transforma pantallas individuales en un producto coherente.
- Acuerda iconografía, tokens de color, jerarquía tipográfica y componentes base **antes** de escalar.
- Evita literales: ni colores hardcoded ni tamaños mágicos. Todo consume tokens.
- Define qué debe comunicar tu interfaz y úsalo como criterio para resolver dudas de estilo.
- Los componentes propios sobre elementos nativos ahorran refactors masivos en el mediano plazo.

## Preservar consistencia al delegar UI a un agente

Cuando un agente de IA genera o modifica pantallas, necesita las mismas convenciones que el equipo humano — pero expresadas en un archivo que pueda leer antes de actuar. El patrón habitual es un `DESIGN.md` en la raíz del repositorio con paleta, tipografía, tokens, reglas de componentes y ejemplos de "sí/no". Sin ese archivo, el agente interpola desde patrones genéricos y el resultado se siente ajeno a la marca.

Una referencia útil de cómo se ve ese archivo en productos reales es [VoltAgent/awesome-design-md](https://github.com/VoltAgent/awesome-design-md), que cura `DESIGN.md` extraídos de 68+ sitios. El detalle de cómo integrar este archivo al flujo del agente se trata en [6.2 · Context engineering y CLAUDE.md](../../colaboracion-con-agentes-ia/02-context-engineering-claude-md.md).
