---
sidebar_position: 4
sidebar_label: 3.2.4 UX writing para interfaces dinámicas
---

# UX writing para interfaces dinámicas

El texto de una interfaz es **parte del sistema de diseño**, no un adorno que se añade al final. Una tabla con 50 columnas inútiles se arregla con texto más preciso; un formulario confuso se arregla reescribiendo los labels, no rediseñando los inputs; un estado de error que dice "Oops, algo salió mal" pierde al usuario donde un mensaje claro lo hubiera retenido.

Este módulo trata el **UX writing** como infraestructura con dos restricciones modernas: internacionalización (i18n) y claridad para modelos de lenguaje (LLMs). El mismo texto debe funcionar en español, inglés y japonés; y debe ser procesable por un agente que resume, traduce o responde a partir de él.

## Principios de UX writing

Seis reglas que resuelven la mayoría de las decisiones:

1. **Claridad sobre brevedad.** "Guardar cambios" es mejor que "Guardar"; "Eliminar usuario permanentemente" es mejor que "Eliminar".
2. **Verbos accionables.** Botones empiezan con verbo en infinitivo ("Crear", "Enviar", "Archivar"). "OK" y "Aceptar" pierden contexto.
3. **Voz activa, presente.** "Guardamos tus cambios" vence a "Los cambios fueron guardados".
4. **Específico sobre genérico.** "No encontramos facturas con esos filtros" vence a "Sin resultados".
5. **Honesto sobre optimista.** "Esto tomará unos minutos" vence a "Ya casi listo" cuando en realidad tomará 10 minutos.
6. **Sin jerga interna.** Si tu app dice "Provisionar recurso" y el usuario final no es DevOps, el botón dice "Crear" o "Activar".

## Jerarquía textual — qué existe y cómo escribirlo

| Tipo | Propósito | Longitud | Ejemplo |
|------|-----------|----------|---------|
| **Label** | Nombra un campo o acción | 1-3 palabras | *Correo electrónico* |
| **Placeholder** | Ejemplo del formato esperado | 3-8 palabras | *nombre@empresa.com* |
| **Helper text** | Explica qué hacer cuando no es obvio | 5-15 palabras | *Usa el mismo correo con el que te registraste* |
| **Error message** | Dice qué falla y cómo corregir | 5-20 palabras | *El correo debe incluir @ — revisa y reintenta* |
| **Empty state** | Ofrece salida cuando no hay datos | 10-40 palabras + acción | *Aún no has creado ninguna factura. [Crear la primera]* |
| **Toast** | Confirma o alerta sin bloquear | 5-15 palabras | *Factura enviada a juan@ejemplo.com* |
| **Confirmation** | Previene acción destructiva | 15-40 palabras | *Esto eliminará la factura #1234 de forma permanente. No se puede deshacer. [Cancelar] [Eliminar]* |

:::tip El placeholder NO es label
Es un error frecuente usar placeholder como label ("Correo electrónico" dentro del input). Al escribir, el usuario pierde la referencia. El label siempre va **fuera y arriba** del input; el placeholder, si existe, **ejemplifica** el formato.
:::

## i18n — diseñar pensando en cada idioma desde el día uno

La regla práctica: **lo que escribes hoy alguien lo traducirá mañana**. Si diseñas asumiendo que el texto es español, rompes tres semanas después cuando llega la traducción al alemán (palabras largas), al japonés (sin espacios), al árabe (right-to-left).

### Reglas de diseño que respetan i18n

1. **Reserva 30-50% de espacio extra** para textos traducidos. "Settings" en inglés son 8 caracteres; en alemán "Einstellungen" son 13; en español "Configuración" son 13.
2. **No concatenes strings.** `"Hola, " + userName + "!"` se rompe en japonés (donde el saludo va al final). Usa templates con placeholders nombrados: `"Hola, {name}"`.
3. **Evita verbos como botones sin contexto.** "Post" en inglés es ambiguo (¿publicar? ¿correo? ¿poste?) y difícil de traducir. "Publicar entrada" sobrevive.
4. **Pluraliza correctamente.** El español tiene 2 formas (singular/plural); el ruso tiene 4; el árabe tiene 6. Usa librerías ICU MessageFormat, no `if (count === 1)`.
5. **Fechas, números y monedas son locales.** `1,000.50` (en-US) es `1.000,50` (es-ES) y `1 000,50` (fr-FR). Formatea con API nativa (`Intl.NumberFormat`, `Intl.DateTimeFormat`), nunca a mano.
6. **No embebas texto en imágenes.** Un banner con texto hardcoded requiere un archivo nuevo por cada idioma. SVG con `<text>` traducible es preferible.

### Ejemplo — pluralización correcta

```javascript
// Mal — solo funciona en inglés y español
const msg = count === 1
  ? `${count} item`
  : `${count} items`;

// Bien — ICU MessageFormat
import { IntlMessageFormat } from 'intl-messageformat';

const msg = new IntlMessageFormat(
  '{count, plural, =0 {Sin elementos} one {# elemento} other {# elementos}}',
  'es'
);
msg.format({ count: 0 }); // "Sin elementos"
msg.format({ count: 1 }); // "1 elemento"
msg.format({ count: 5 }); // "5 elementos"
```

### Estructura de archivos de traducción

```json
// locales/es/common.json
{
  "button.save": "Guardar cambios",
  "button.cancel": "Cancelar",
  "empty.invoices.title": "Aún no tienes facturas",
  "empty.invoices.cta": "Crear la primera",
  "error.network": "No pudimos conectar. Revisa tu conexión e intenta de nuevo.",
  "confirm.delete.user": "Esto eliminará al usuario {name} de forma permanente."
}
```

La clave (`empty.invoices.title`) funciona como identificador estable; el valor se traduce por idioma. Nunca uses el texto en español como clave — cambiar el copy rompe todas las traducciones.

## Claridad para LLMs

Cuando un agente de IA lee tu interfaz (vía screenshot, accessibility tree, o texto extraído), la calidad de su comprensión depende de **qué tan unívocamente nombraste las cosas**.

### Reglas que mejoran la procesabilidad

1. **Nombres explícitos sobre iconos solos.** Un botón solo con icono de papelera depende de que el modelo reconozca el icono. Un botón con label "Eliminar" es inambiguo.
2. **Mensajes de error específicos con códigos.** *"ERR-AUTH-001: Token expirado. Vuelve a iniciar sesión."* Un agente puede actuar sobre ese código; "Error de autenticación" lo obliga a inferir.
3. **Estados visibles en texto, no solo en color.** "Error" escrito vence a "fondo rojo". Un LLM no ve color en un accessibility tree; ve texto.
4. **Estructura predecible.** Toda tabla tiene header; todo form tiene submit explícito; toda modal tiene cerrar y acción primaria. Desviaciones confunden tanto al humano como al modelo.
5. **Accessibility tree bien formado.** Labels asociados a inputs vía `for`/`htmlFor`, `aria-label` en botones de solo icono, landmarks semánticos (`<main>`, `<nav>`, `<aside>`). Lo que lee un lector de pantalla es lo que lee un agente.

### Ejemplo — accesible vs inaccesible

```html
<!-- Inaccesible (para humanos y LLMs) -->
<div onclick="deleteUser()">
  <img src="trash.svg" />
</div>

<!-- Accesible -->
<button
  type="button"
  onclick="deleteUser()"
  aria-label="Eliminar usuario Juan Pérez">
  <img src="trash.svg" alt="" aria-hidden="true" />
  <span class="sr-only">Eliminar usuario Juan Pérez</span>
</button>
```

El botón accesible lo lee correctamente un usuario con lector de pantalla, un agente que parsea el DOM, y un modelo multimodal que solo ve la captura. El div inaccesible depende de que cada uno "adivine" que el icono es un botón de borrar.

## Microcopy por estado — plantillas listas

### Estado vacío — primera vez

```
[Ilustración sutil]
Aún no has creado ninguna factura.
Las facturas aparecerán aquí cuando emitas la primera.
[Botón primario: Crear factura]
```

### Estado vacío — tras filtro

```
No encontramos facturas que coincidan con los filtros actuales.
[Link: Limpiar filtros]
```

### Error de red

```
No pudimos cargar las facturas.
Revisa tu conexión e intenta de nuevo.
[Botón: Reintentar]
```

### Error de validación de campo

```
El correo debe incluir @ — revisa y reintenta.
(Visible junto al input, no en toast global.)
```

### Confirmación destructiva

```
¿Eliminar factura #1234?
Esta acción es permanente y no se puede deshacer.
[Botón secundario: Cancelar] [Botón destructivo: Eliminar]
```

### Éxito

```
Toast: Factura enviada a juan@ejemplo.com
(3-5 segundos, auto-dismiss, descartable con Esc.)
```

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** producir y mantener el texto de la interfaz como parte del sistema de diseño, con restricciones explícitas de i18n y claridad para agentes.

**Entradas:**
- Lista de pantallas y componentes que contienen texto.
- Idiomas objetivo (mínimo el de diseño + uno adicional para validar el sistema).
- `DESIGN.md` con tono y principios definidos.

**Pasos:**
1. Inventariar todos los strings hardcoded en la UI actual; extraerlos a archivos de traducción con claves semánticas.
2. Revisar cada string contra los 6 principios de UX writing y las reglas de i18n.
3. Configurar librería de i18n con MessageFormat (ICU) para pluralización y formato.
4. Ejecutar pseudo-localización: reemplazar cada string por su versión expandida (30-50% más larga) y detectar cortes.
5. Añadir `aria-label` y estructura semántica donde el icono/visual carga significado.
6. Validar que un agente puede describir cada pantalla solo con el accessibility tree.

**Salidas:**
- Archivos de traducción con claves semánticas, sin strings hardcoded en código.
- Pseudo-localización integrada en el pipeline de build.
- Accessibility tree que se lee coherente en lector de pantalla y al ser parseado.

**Errores comunes:**
- Usar el texto español como clave de traducción (cualquier cambio de copy rompe todas las traducciones).
- Concatenar strings con variables (rompe orden sintáctico en idiomas con estructura distinta).
- Confiar en el color para comunicar estado (inaccesible y procesable solo parcialmente por LLMs).
- Pluralizar con `if (count === 1)` en lugar de librería ICU.

**Referencias cruzadas:**
- [3.2.1 Design tokens y estándares](./01-design-tokens-y-estandares.md)
- [3.2.3 Handoff técnico](./03-handoff-tecnico.md)
- [5. Documentación y requerimientos](../../documentacion-y-requerimientos/01-de-la-idea-al-release.md)

</div>

---

## Glosario

**UX writing** *(UX writing)* — disciplina que diseña el texto de una interfaz como parte del sistema: labels, mensajes, estados, microcopy.

**i18n** *(internationalization)* — preparación del producto para funcionar en múltiples idiomas y regiones sin cambios de código. Abreviado por los 18 caracteres entre la "i" inicial y la "n" final.

**l10n** *(localization)* — adaptación concreta del producto a un idioma o región específica (traducción + formatos locales).

**MessageFormat / ICU** *(ICU MessageFormat)* — sintaxis estándar para mensajes traducibles con pluralización, género y variables. Mantenida por la Unicode Consortium.

**Pseudo-localización** *(pseudo-localization)* — técnica que reemplaza el texto con versiones expandidas y con caracteres especiales para detectar problemas de layout antes de traducir.

**Accessibility tree** *(accessibility tree)* — representación estructural de la UI que usan lectores de pantalla y agentes de IA. Derivada del DOM más los atributos ARIA.

**Microcopy** *(microcopy)* — textos cortos de la interfaz: botones, tooltips, mensajes de estado, confirmaciones.

:::info Referencias primarias
- [Nielsen Norman Group — Microcopy](https://www.nngroup.com/articles/microcopy/)
- [Unicode CLDR — ICU MessageFormat](https://unicode-org.github.io/icu/userguide/format_parse/messages/)
- [Google Material — Writing](https://m3.material.io/foundations/content-design/style-guide/overview)
- [WAI-ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
- [Shopify Polaris — Content guidelines](https://polaris.shopify.com/content)
:::

---

<AuthorCredit />
