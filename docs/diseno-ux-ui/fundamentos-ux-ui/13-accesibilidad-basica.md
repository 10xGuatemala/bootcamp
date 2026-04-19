---
sidebar_position: 13
sidebar_label: 3.1.13 Accesibilidad básica
---

# Accesibilidad básica

La mamá de Rosa tiene 72 años y también usa la app — vende bordados desde casa y lleva sus pedidos ahí. Usa la misma interfaz que su hija, pero con las letras al 150 %, con cataratas incipientes, y con un temblor leve en el pulgar derecho. Si el portal le funciona a ella, le funciona a todos. Si solo le funciona al diseñador que lo construyó, el producto excluye al 20–30 % de la población — y en un servicio público, eso es inaceptable legal y éticamente.

Diseñar accesible no es *"agregar una capa"* al final del proyecto: es **diseñar bien desde el principio**. Una interfaz accesible funciona para personas con discapacidad visual, motora, auditiva o cognitiva — y, como efecto colateral, **funciona mejor para todos**. El subtítulo que ayuda a un lector de pantalla también ayuda al usuario apurado; el contraste que permite leer a alguien con baja visión también se lee bien bajo el sol.

En la mayoría de países, accesibilidad no es solo buena práctica: **es obligación legal** para servicios públicos y cada vez más para el sector privado. Ignorarla implica riesgo reputacional, exclusión real de usuarios y, en muchos casos, multas.

Este módulo cubre los fundamentos que todo diseñador y desarrollador debe dominar antes de publicar. Para un producto que atiende a dueñas de pymes y a sus equipos, la accesibilidad es especialmente crítica: la base incluye personas mayores que se incorporan tarde a lo digital, personas con discapacidad visual o motora, y quienes usan la app bajo luz directa o con una mano mientras hacen otra cosa.

## El estándar: WCAG 2.2

Las [Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/) son el estándar internacional. Se organizan en cuatro principios: **Perceptible, Operable, Comprensible, Robusto** (acrónimo POUR).

Cada criterio se clasifica en tres niveles:

- **A**: mínimos imprescindibles; sin ellos la interfaz es inaccesible para grupos enteros.
- **AA**: el objetivo práctico; normativa legal en la mayoría de jurisdicciones.
- **AAA**: ideal; aplicar donde sea posible, pero no siempre es viable en todo el producto.

**Objetivo razonable**: cumplir WCAG 2.2 AA en todo el producto.

## Perceptible

Que el usuario pueda percibir la información, por el canal que sea.

### Contraste de color

Relación mínima entre texto y fondo:

| Texto | AA | AAA |
|-------|---:|----:|
| Normal | 4.5:1 | 7:1 |
| Grande (≥18 pt o 14 pt bold) | 3:1 | 4.5:1 |
| Componentes de UI (bordes de inputs, iconos) | 3:1 | — |

Ver [3.1.10 Psicología del color](./10-psicologia-del-color.md) para cómo aplicarlo desde la paleta.

### Alternativas textuales

- Toda imagen significativa debe tener **`alt` text** que describa su contenido o función.
- Imágenes decorativas: `alt=""` para que el lector de pantalla las ignore.
- Iconos con función (un sobre que indica "enviar"): `aria-label` o texto adicional.

### No depender solo del color

Un borde rojo que indica error no es suficiente: debe ir con icono y mensaje. Este principio se repite en color, tipografía y jerarquía visual — y es la regla de oro de accesibilidad.

### Texto redimensionable

El usuario debe poder aumentar el texto hasta 200 % sin que la interfaz se rompa. Usa unidades relativas (`rem`, `em`) en lugar de píxeles fijos para texto.

## Operable

Que el usuario pueda interactuar con la interfaz por el método que use.

### Navegación por teclado

**Toda** funcionalidad debe ser usable sin mouse:

- `Tab` avanza al siguiente elemento interactivo; `Shift+Tab` retrocede.
- `Enter` activa botones y enlaces; `Space` activa botones y checkboxes.
- Flechas navegan dentro de componentes compuestos (menús, listas, tabs).
- `Esc` cierra modales y menús.

Prueba concreta: desconecta el mouse y navega toda la pantalla. Si no puedes, la accesibilidad por teclado está rota.

### Indicador de foco visible

Cuando un elemento está activo por teclado, debe tener un **indicador visual claro** (un outline, un halo). Nunca hagas `outline: none` sin reemplazarlo: es uno de los errores más comunes y más graves.

El indicador debe contrastar con el fondo (3:1 mínimo) y ser visible incluso para alguien con baja visión.

### Tiempo suficiente

Evita timeouts agresivos. Si la sesión va a expirar, **avisa y permite extenderla**. Una dueña que está cerrando caja o armando un reporte puede necesitar 20 minutos entre interrupciones; perder todo porque la sesión expiró en 10 es una falla de accesibilidad, no solo de UX.

### Áreas táctiles

En mobile, los elementos interactivos deben tener al menos **44×44 px** de área táctil. Botones más pequeños excluyen a personas con temblor, artritis o motricidad fina reducida.

## Comprensible

Que el usuario entienda la información y el funcionamiento.

### Lenguaje claro

- Usa palabras del usuario, no jerga interna (*"asiento contable"* vs *"movimiento"*, o *"rubro"* vs *"categoría de servicio"*).
- Oraciones cortas. Evita subordinadas anidadas.
- Define acrónimos la primera vez que aparecen.
- Nivel de lectura objetivo: secundaria completa para audiencia general.

### Etiquetas y ayudas en formularios

- **Todo input tiene label visible**, no solo placeholder.
- Mensajes de error específicos y accionables: no *"dato inválido"*, sino *"el teléfono debe tener 8 dígitos sin guiones ni espacios"*.
- Asocia errores con su campo mediante `aria-describedby`.
- Indica campos obligatorios de forma consistente (asterisco + texto explicativo).

### Consistencia

Los elementos que hacen lo mismo deben verse y llamarse igual en todo el producto. La consistencia reduce la carga cognitiva para todos, y es especialmente importante para usuarios con limitaciones cognitivas.

## Robusto

Que la interfaz funcione con tecnologías asistivas (lectores de pantalla, controles por voz, magnificadores).

### HTML semántico

Usa las etiquetas correctas: `<button>` para botones, `<a>` para enlaces, `<nav>` para navegación, `<main>` para contenido principal, encabezados `<h1>`–`<h6>` en orden lógico.

Un `<div onclick>` puede verse como botón pero no lo es para un lector de pantalla. Este error invalida muchos productos aparentemente "bonitos".

### ARIA cuando sea necesario

Las atribuciones ARIA (`aria-label`, `aria-describedby`, `role`) suplen lo que el HTML no cubre, pero **no reemplazan HTML semántico**. Regla: **"No ARIA es mejor que ARIA mal usado"**.

Casos útiles:
- `aria-label` para botones con solo icono.
- `aria-live` para regiones que se actualizan dinámicamente (resultados de búsqueda, mensajes).
- `role="alert"` para mensajes urgentes.

### Compatibilidad con lector de pantalla

Prueba tu producto con VoiceOver (macOS/iOS), NVDA (Windows, gratuito) o TalkBack (Android). Navega una tarea completa escuchando solo lo que anuncia el lector. Si algo se pierde o se anuncia incomprensible, corrige.

## Checklist mínimo antes de publicar

- [ ] Contraste texto/fondo ≥ 4.5:1 en cuerpo; ≥ 3:1 en títulos grandes.
- [ ] Toda imagen relevante tiene `alt` descriptivo.
- [ ] Mensajes de error no dependen solo de color.
- [ ] Navegación completa por teclado, con foco visible.
- [ ] Áreas táctiles ≥ 44×44 px en mobile.
- [ ] Inputs con label visible, no solo placeholder.
- [ ] HTML semántico: `<button>`, `<a>`, encabezados en orden.
- [ ] Tiempos de sesión configurables o notificación antes de expirar.
- [ ] Texto redimensionable al 200 % sin romper el layout.
- [ ] Prueba con un lector de pantalla al menos una tarea crítica.

## Herramientas

- [axe DevTools](https://www.deque.com/axe/) — extensión de navegador para auditoría automática.
- [WAVE](https://wave.webaim.org/) — evaluador de accesibilidad.
- [Lighthouse](https://developer.chrome.com/docs/lighthouse/) — incluye reporte de accesibilidad.
- [Contrast Checker (WebAIM)](https://webaim.org/resources/contrastchecker/) — ratios WCAG.
- **Lectores de pantalla**: VoiceOver (Mac/iOS), NVDA (Windows, gratuito), TalkBack (Android).

Las herramientas automáticas detectan ~30 % de los problemas. El resto requiere prueba manual y con usuarios reales, idealmente personas con discapacidad.

:::warning El 30 % del que sí te avisan es el fácil
axe, Lighthouse y WAVE detectan contrastes, atributos `alt` faltantes y HTML semántico roto. No detectan si el orden de lectura es correcto, si el foco por teclado sigue el flujo lógico, si las etiquetas de formularios describen correctamente el campo, ni si un lector de pantalla puede completar una tarea. Eso solo se descubre probando.
:::

## Puente al próximo módulo

Con UX, arquitectura, pruebas, color, tipografía, jerarquía y accesibilidad cubiertos, nos queda un último tema que une todo: las **convenciones** que hacen que cientos de pantallas se sientan como la misma aplicación. Eso es el [módulo 3.1.14](./14-convenciones-de-ui-consistentes.md) — y cierra la sección de fundamentos antes de pasar a los sistemas de diseño concretos (3.2).

## Glosario

**WCAG** *(Web Content Accessibility Guidelines)* — estándar internacional de accesibilidad web.

**Tecnología asistiva** *(Assistive technology)* — software o hardware que ayuda a personas con discapacidad a usar productos digitales (lector de pantalla, magnificador, control por voz).

**Lector de pantalla** *(Screen reader)* — software que lee en voz alta el contenido de la interfaz.

**ARIA** *(Accessible Rich Internet Applications)* — conjunto de atributos HTML que complementan la semántica para tecnologías asistivas.

**Contraste de color** — ratio numérico entre luminosidad de texto y fondo; determina legibilidad.

**Foco** *(Focus)* — elemento actualmente activo por teclado; debe ser visible siempre.

:::info Referencias primarias
- [WCAG 2.2](https://www.w3.org/TR/WCAG22/) — estándar completo.
- [MDN · Accessibility](https://developer.mozilla.org/es/docs/Web/Accessibility) — guías técnicas.
- [Inclusive Components (Heydon Pickering)](https://inclusive-components.design/) — patrones accesibles probados.
- [A11y Project](https://www.a11yproject.com/) — recursos comunitarios.
- [ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/) — cuándo y cómo usar ARIA.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** diseñar y verificar una interfaz que cumpla WCAG 2.2 AA y sea usable por personas con discapacidad visual, motora, auditiva o cognitiva.

**Entradas:**
- Diseño o producto a auditar.
- Nivel WCAG objetivo (mínimo AA).
- Personas representativas incluyendo usuarios con discapacidad.
- Herramientas automáticas disponibles y lector de pantalla.

**Pasos:**
1. Auditoría automática con axe / Lighthouse; resolver los bloqueantes.
2. Verificar contrastes en la paleta de color contra AA.
3. Probar navegación completa por teclado en flujos críticos.
4. Revisar que toda imagen significativa tenga `alt` descriptivo.
5. Probar con lector de pantalla al menos una tarea crítica de cada segmento.
6. Validar en prueba de usabilidad con al menos una persona con discapacidad.
7. Documentar deudas de accesibilidad y priorizar por impacto.

**Salidas:**
- Reporte de accesibilidad con problemas por nivel (A / AA / AAA).
- Lista de tickets priorizados.
- Evidencia de prueba manual (grabación con lector de pantalla).
- Checklist de accesibilidad integrado al flujo de QA.

**Errores comunes:**
- Confiar solo en auditoría automática; detecta ~30 % de los problemas.
- Hacer `outline: none` sin reemplazo para el foco por teclado.
- Placeholder como única etiqueta en formularios.
- `<div onclick>` en lugar de `<button>`.
- Dependencia solo del color para comunicar estado.
- Abusar de ARIA en lugar de usar HTML semántico.
- Probar sin usuarios con discapacidad real.

**Referencias cruzadas:**
- [3.1.10 Psicología del color](./10-psicologia-del-color.md)
- [3.1.11 Tipografía](./11-tipografia.md)
- [3.1.12 Jerarquía visual y Gestalt](./12-jerarquia-visual-y-gestalt.md)
- [3.1.14 Convenciones de UI consistentes](./14-convenciones-de-ui-consistentes.md)
</div>
