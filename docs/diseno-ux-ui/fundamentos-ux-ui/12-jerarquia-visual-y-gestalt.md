---
sidebar_position: 12
sidebar_label: 3.1.12 Jerarquía visual y Gestalt
---

# Jerarquía visual y Gestalt

Rosa abre la pantalla de cobro entre clientas. Su ojo tiene 1–2 segundos para encontrar dónde se confirma el pago. Si la pantalla tiene tres botones del mismo tamaño, cuatro avisos con el mismo color y un enlace de términos tan destacado como la acción principal, Rosa va a dudar — y su duda termina en una clienta esperando o un cobro sin registrar.

Cuando abres una pantalla, tu ojo no lee de izquierda a derecha: **escanea**. Salta al título, descarta los bordes, se detiene en el botón que destaca, vuelve al texto secundario solo si algo en la tarea lo requiere. Esa ruta no es aleatoria: la diseña —con intención o sin ella— la **jerarquía visual** que creaste al componer la pantalla.

Este módulo explica cómo funciona esa lectura automática usando dos herramientas: los principios de agrupación **Gestalt** (cómo el ojo asocia elementos) y las palancas concretas de jerarquía visual (tamaño, peso, color, espaciado, contraste). Entender ambas permite decidir dónde quieres que mire el usuario y luego verificar si mira ahí.

## Por qué el ojo necesita una jerarquía

Una interfaz sin jerarquía trata todos los elementos como igualmente importantes. El resultado es que el usuario se ve obligado a **leerlo todo para decidir qué es relevante** — un trabajo que debería hacer el diseño.

La prueba más simple está en tu propio producto: abre una pantalla, cierra los ojos 5 segundos, vuelve a abrirla y nota dónde cae tu mirada primero. Si cae en el lugar donde ocurre la tarea principal, la jerarquía funciona. Si cae en el logo del header o en un banner decorativo, la jerarquía está compitiendo contra ti.

:::tip La prueba del entrecerrado
Si entrecierras los ojos hasta que la pantalla se vuelve borrosa, los elementos importantes deben seguir siendo identificables como manchas distintas. Si todo se funde en una sola mancha gris, no hay jerarquía.
:::

## Los principios Gestalt

La Gestalt es una escuela de psicología de la percepción que describe **cómo el cerebro agrupa elementos visuales** automáticamente. Seis principios explican la mayoría de decisiones de layout:

### Proximidad

Los elementos cercanos entre sí se perciben como **un grupo**. Es la palanca más fuerte.

```
Label del campo
[ Input                          ]
Mensaje de ayuda

                                          (espacio grande)

Label del siguiente campo
[ Input                          ]
```

Si el espacio entre el label y su input es igual al espacio entre secciones, el usuario no sabe qué label pertenece a qué input. El ajuste no está en añadir una línea divisoria: está en que el label esté **más cerca** de su input que del siguiente bloque.

### Similitud

Los elementos que comparten forma, color o tipografía se perciben como pertenecientes a una misma categoría.

Por eso todos los botones primarios se ven igual: el usuario aprende que "lo que se ve así, hace lo mismo". Romper la similitud sin razón (un botón primario con estilo distinto en una pantalla) rompe el contrato de aprendizaje.

### Cierre

El cerebro completa figuras incompletas. Un bloque de información delimitado por esquinas en lugar de un borde completo se percibe igualmente como una unidad. Esto permite interfaces más ligeras visualmente sin perder estructura.

### Continuidad

Los elementos alineados en una línea (real o imaginaria) se perciben como relacionados. Por eso las **grillas** funcionan: alinear todo contra columnas comunes crea una sensación de orden incluso cuando los elementos son diversos.

### Figura-fondo

El ojo separa automáticamente la figura (primer plano) del fondo. Un modal con fondo oscuro deja claro qué está activo y qué está pausado. Un botón con fondo coloreado sobre una pantalla neutra queda definitivamente en primer plano.

### Destino común

Los elementos que se mueven o cambian juntos se perciben como un grupo. Por eso animaciones sutiles (un conjunto de filas que aparecen desplegándose con la misma animación) refuerzan la idea de que son una unidad.

## De la Gestalt a la jerarquía visual

Gestalt explica cómo agrupa el ojo. La **jerarquía visual** decide **en qué orden** recorre los grupos. Se construye con cinco palancas que trabajan juntas:

### Tamaño

Más grande = más importante. Es la palanca más obvia, y por eso la más abusada. No todo puede ser grande; si tres elementos gritan, ninguno gana.

Un título a 32 px sobre texto a 16 px crea jerarquía sin más ayuda. Subir el título a 64 px no duplica su importancia: produce desbalance.

### Peso

El peso (bold, semibold) crea énfasis sin cambiar tamaño. Útil cuando necesitas distinguir etiquetas dentro de un mismo tamaño tipográfico.

### Color

El contraste de color llama la atención más fuerte que el tamaño en muchos casos. Un botón azul saturado en una pantalla gris captura la mirada aunque mida menos que el título. Usa esta palanca con cuidado: si todo tiene color, nada destaca.

### Espaciado

El espacio en blanco alrededor de un elemento amplifica su importancia. Un call-to-action rodeado de aire transmite "esto es lo importante de la pantalla" sin tener que ser gigante. La densidad visual mata la jerarquía.

### Posición

La esquina superior izquierda concentra la primera mirada en culturas de lectura de izquierda a derecha. Los elementos al inicio del flujo de lectura reciben más atención que los del final. En móvil, la zona del pulgar (parte inferior central) gana prioridad para acciones frecuentes.

## Diseñar la ruta del ojo

Una pantalla bien compuesta tiene **un primer elemento claro, un segundo elemento claro, y un orden implícito de lectura**. La prueba es preguntarse:

1. ¿Dónde cae la mirada a los 0.5 segundos?
2. ¿Dónde va después?
3. ¿Dónde aparece la acción principal que el usuario debe tomar?

Si esas respuestas no coinciden con el flujo de la tarea, hay un problema de jerarquía, no un problema de color o tipografía.

### Patrones típicos de lectura

- **F-pattern**: común en páginas con mucho texto. El ojo barre la primera línea horizontalmente, desciende y barre la segunda línea parcialmente, luego cae verticalmente. Aplica a landing pages y listados.
- **Z-pattern**: páginas con menos texto y un CTA. El ojo va de arriba-izquierda a arriba-derecha, diagonal hacia abajo-izquierda, y termina en abajo-derecha (donde suele estar el CTA).

No hay que diseñar *para* estos patrones mecánicamente, pero conocerlos ayuda a colocar elementos críticos donde el ojo ya tiende a mirar.

## Errores frecuentes

- **Todo del mismo peso**: una pantalla donde nada destaca porque todo intenta destacar.
- **Densidad excesiva**: eliminar espacio creyendo que "más información por pantalla" es mejor; en realidad pierde la jerarquía.
- **Romper similitud sin razón**: botones primarios distintos entre pantallas; el usuario pierde la señal aprendida.
- **Jerarquía apoyada solo en color**: falla con daltonismo y con pantallas en sol.
- **Asumir lectura lineal**: los usuarios no leen de arriba a abajo; escanean. Diseña para el escaneo primero, la lectura después.

## Cómo verificar la jerarquía

- **Prueba del entrecerrar los ojos**: si entrecierras los ojos hasta que la pantalla se vuelve borrosa, los elementos importantes deben seguir siendo identificables como manchas distinguibles.
- **Prueba de 5 segundos**: muéstrale la pantalla 5 segundos a alguien y pregunta qué recuerda. Lo que recuerdan primero refleja lo que la jerarquía está destacando.
- **Heatmaps y eye-tracking** (ver [3.1.5 UX Research](./05-ux-research.md)): la confirmación empírica.

## Puente al siguiente módulo

Una pantalla con color, tipografía y jerarquía correctos funciona para la mayoría. Pero en un producto que usan dueñas de negocios, asistentes y clientas de perfiles muy distintos, la "mayoría" no es suficiente: hay personas con baja visión, con daltonismo, navegando por teclado, o usando lectores de pantalla. La accesibilidad no es una capa opcional — es obligación legal en la mayoría de jurisdicciones y requisito ético siempre. Eso es el [módulo 3.1.13](./13-accesibilidad-basica.md).

## Glosario

**Gestalt** — escuela de psicología que describe cómo el cerebro agrupa elementos visuales.

**Jerarquía visual** *(Visual hierarchy)* — orden de importancia que el ojo percibe al escanear una composición.

**Espacio en blanco** *(Whitespace)* — área sin contenido que delimita, agrupa o enfatiza; no es espacio "vacío" sino activo.

**F-pattern / Z-pattern** — rutas típicas de escaneo visual en pantallas con texto o con CTA prominente.

**Contraste visual** — diferencia perceptible entre dos elementos en tamaño, color, peso o posición.

:::info Referencias primarias
- [Laws of UX (Jon Yablonski)](https://lawsofux.com/) — principios de Gestalt y percepción aplicados a UI.
- [Nielsen Norman Group · Visual Hierarchy](https://www.nngroup.com/articles/visual-hierarchy-ux-definition/) — aplicación a interfaces.
- [Refactoring UI · Hierarchy](https://www.refactoringui.com/) — ejemplos prácticos.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** componer pantallas con jerarquía clara para que el usuario identifique la acción principal en el primer vistazo.

**Entradas:**
- Objetivo principal de la pantalla (qué debe hacer el usuario).
- Contenido a presentar y su prioridad relativa.
- Convenciones del sistema de diseño disponible.
- Restricciones de densidad (cantidad mínima de información por pantalla).

**Pasos:**
1. Identificar el elemento #1 de la pantalla (acción primaria o información crítica).
2. Decidir qué palancas aplicar: tamaño, peso, color, espaciado, posición.
3. Usar proximidad para agrupar elementos relacionados y separar grupos distintos.
4. Aplicar similitud consistente a elementos de la misma categoría.
5. Aplicar la prueba del entrecerrado y la prueba de 5 segundos antes de pasar a desarrollo.
6. Verificar en pruebas de usabilidad que la jerarquía soporta la tarea.

**Salidas:**
- Pantalla con un primer y segundo elemento claros.
- Grupos delimitados por espaciado, no solo por líneas o cajas.
- Documentación de las decisiones de jerarquía (qué se destacó y por qué).

**Errores comunes:**
- Intentar destacar todo; nada destaca.
- Reducir espaciado para "caber más"; pierde la jerarquía.
- Usar color como única palanca; falla en accesibilidad.
- Romper similitud entre pantallas sin razón.
- Ignorar el patrón de escaneo (F/Z) al ubicar elementos críticos.

**Referencias cruzadas:**
- [3.1.10 Psicología del color](./10-psicologia-del-color.md)
- [3.1.11 Tipografía](./11-tipografia.md)
- [3.1.13 Accesibilidad básica](./13-accesibilidad-basica.md)
- [3.1.14 Convenciones de UI consistentes](./14-convenciones-de-ui-consistentes.md)
</div>
