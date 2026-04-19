---
sidebar_position: 6
sidebar_label: 3.2.6 Microcopy híbrido (humano + IA)
---

# Microcopy híbrido: humano + agente de IA

El módulo [3.2.5](./05-ux-writing-interfaces-dinamicas.md) trató el UX writing como infraestructura del sistema de diseño. Este módulo cierra una pregunta más específica: **cómo redactar microcopy que funcione simultáneamente para el usuario humano y para los agentes de IA** que hoy consumen la misma interfaz.

## ¿Qué es el microcopy?

El **microcopy** son los textos cortos y funcionales que viven dentro de la interfaz: etiquetas de botones, placeholders de formularios, mensajes de error, tooltips, estados vacíos, confirmaciones, textos de ayuda debajo de un campo. No es el contenido "editorial" (artículos, descripciones largas de producto): es el texto que **guía la acción** en el momento exacto en que el usuario tiene que decidir algo.

Aunque cada pieza mide entre una y quince palabras, el microcopy hace tres trabajos que ninguna otra capa del diseño resuelve sola:

- **Reduce la incertidumbre** — le dice al usuario qué va a pasar si hace clic, qué formato espera un campo, qué significa un ícono.
- **Previene errores** — un helper text claro ("Usa el mismo correo con el que te registraste") evita el soporte que genera un label ambiguo.
- **Recupera al usuario cuando algo falla** — un buen mensaje de error es la diferencia entre un usuario que corrige y sigue, y uno que abandona.

Un estudio clásico de usabilidad: cambiar la etiqueta de un botón de *"Enviar"* a *"Crear mi cuenta"* puede subir la conversión de formularios entre 10 % y 30 %. Sin tocar colores, layout ni flujo — solo el microcopy. Por eso el texto de la interfaz es **parte del diseño**, no un adorno que se redacta al final.

## Por qué escribir microcopy híbrido

La premisa es simple: si el texto de un botón solo tiene sentido con la foto al lado, un humano con baja visión, un lector de pantalla y un agente que procesa el DOM reciben todos el mismo mensaje empobrecido. Diseñar microcopy híbrido no es añadir "algo extra para la IA" — es escribir mejor para todos.

## Objetivo

Estandarizar la comunicación en la interfaz para **maximizar la claridad del usuario** y, al mismo tiempo, **proporcionar un contexto técnico inequívoco** que permita a los agentes automatizar tareas, auditar pantallas y entender la función de cada componente sin ambigüedad.

## Entradas

Para redactar microcopy efectivo, el diseñador o desarrollador debe tener a mano:

- **Voz de marca.** El tono definido en `DESIGN.md`: técnico, neutral, directo, cercano. Sin tono, cada pantalla suena distinto.
- **Glosario semántico.** Términos estandarizados del negocio. Si la app dice "Matrícula" en una pantalla y "ID de estudiante" en otra, tanto el humano como el agente pierden el hilo.
- **Estado del componente.** El contexto de la interacción: éxito, error, carga, estado vacío, primera vez. El mismo texto no sirve para los cinco estados.

## Pasos para crear microcopy híbrido

### Paso 1: Verbos de acción imperativos

Toda etiqueta de acción (botón, enlace de comando, item de menú) **empieza con verbo**.

- Mal: *"Hacer clic para enviar formulario"*.
- Bien: *"Enviar solicitud"*.

**Valor para el agente:** permite mapear la acción directamente a una intención clara. Un botón `Enviar solicitud` con `id="btn-submit-request"` se vuelve auto-explicable para un agente que automatiza flujos; un botón `Continuar` junto a tres más iguales lo confunde.

### Paso 2: Autonomía de contexto

El texto debe explicarse por sí mismo sin depender exclusivamente de su posición visual.

- Mal: *"Eliminar"* (al lado de una foto).
- Bien: *"Eliminar fotografía"*.

**Valor para el agente:** los agentes que procesan el DOM o el accessibility tree entienden el impacto de la acción sin visión espacial. El mismo texto sirve para un lector de pantalla y para el usuario visual rápido que no mira la miniatura.

### Paso 3: Mensajes de error de tres partes

Para que tanto el humano como la IA puedan corregir el flujo, todo mensaje de error debe comunicar:

1. **Problema** — qué falló.
2. **Causa** — por qué falló.
3. **Solución** — qué hacer para corregirlo.

> *"No se pudo guardar el perfil (problema) porque la imagen excede los 2 MB (causa). Sube un archivo más ligero (solución)."*

Tres partes no siempre requieren tres oraciones: *"El correo debe incluir @ — revisa y reintenta"* es un error de tres partes en una frase.

## Salidas

### Especificación de UX writing (`UXW_SPEC.md`)

Cada componente del sistema de diseño se acompaña de un bloque de referencia legible para un agente. Es la tabla que traduce "diseño" a "contrato":

| Elemento | Texto (microcopy) | ID / Selector sugerido |
|----------|-------------------|------------------------|
| Botón principal | `Confirmar suscripción` | `btn-confirm-subscription` |
| Texto de ayuda | `Introduce tu correo institucional.` | `helper-email-input` |
| Error de red | `Error de conexión. Reintenta en 30 s.` | `msg-network-error` |
| Estado vacío | `Aún no hay citas para hoy.` | `empty-state-today` |

La tabla vive junto al brief del componente (módulo [3.2.3](./03-arquitectura-de-componentes.md)) y se versiona con el resto del sistema.

## Errores comunes y su impacto

| Error | Impacto humano | Impacto en agente |
|-------|----------------|-------------------|
| **Ambigüedad** | Incertidumbre sobre qué hace la acción. | Alucinación sobre la consecuencia del clic; el agente "decide" por el usuario. |
| **Falta de etiquetas ARIA** | Inaccesibilidad para lectores de pantalla. | "Ceguera" del agente ante elementos interactivos que no tienen rol ni nombre. |
| **Exceso de texto** | Fatiga cognitiva; lectura fragmentada. | Consumo innecesario de tokens en la ventana de contexto del modelo. |
| **Jerga interna** | El usuario final no entiende. | El agente puede traducir literalmente y propagar el error. |
| **Texto dependiente de posición** | Usuarios con baja visión pierden el significado. | El agente pierde la relación componente ↔ intención. |

## Prompt de auditoría

Copia este prompt en tu agente de IA para validar microcopy existente:

```text
Actúa como Senior UX Writer. Audita el siguiente microcopy:

[INSERTAR TEXTO]

Evalúalo con estos criterios:
1. Uso de verbos imperativos accionables.
2. Autonomía de contexto (¿se entiende por sí solo, sin ver la pantalla?).
3. Estructura de error de tres partes (problema + causa + solución), si aplica.
4. Brevedad y densidad de información (¿sobra alguna palabra?).
5. Consistencia con el glosario semántico del producto.

Sugiere una versión optimizada si no cumple el estándar 10X. Si ya cumple, indícalo y no reescribas.
```

Este prompt puede integrarse como **check de QA** en el pipeline: antes de merge, el agente audita los strings nuevos y deja comentarios en el PR.

:::tip El test del copy+paste
Si copias el texto de un botón, lo pegas en un chat sin contexto, y un humano no puede adivinar qué pasa al pulsarlo — el texto falla. El mismo test aplica al agente: si no puede describir qué hace la acción sin "ver" la pantalla, el microcopy está incompleto.
:::

## Puente al siguiente módulo

Con microcopy híbrido, tokens, componentes y handoff, el sistema de diseño está completo. Lo que sigue es cómo se **entrega** ese sistema a desarrollo y producto: documentación, requerimientos y el ciclo idea → release. Eso es el [módulo 5](../../documentacion-y-requerimientos/01-de-la-idea-al-release.md).

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** redactar y auditar microcopy que sirva simultáneamente al usuario humano y a los agentes de IA que procesan la interfaz.

**Entradas:**
- Voz de marca documentada en `DESIGN.md`.
- Glosario semántico del producto.
- Inventario de estados por componente (default, loading, error, empty, success).
- Pantallas y componentes objetivo.

**Pasos:**
1. Extraer todos los strings de la UI a un archivo de traducciones con claves semánticas.
2. Aplicar los 3 pasos (verbos imperativos, autonomía de contexto, errores de 3 partes) a cada string.
3. Crear el `UXW_SPEC.md` por componente con la tabla microcopy + selector.
4. Ejecutar el prompt de auditoría sobre los strings revisados.
5. Validar con lector de pantalla que el accessibility tree describe la pantalla sin ver el visual.
6. Integrar el prompt de auditoría como check opcional en el pipeline de PRs.

**Salidas:**
- `UXW_SPEC.md` por componente con microcopy + IDs estables.
- Auditoría documentada con el prompt estándar.
- Cobertura de atributos ARIA sobre elementos interactivos.
- Reducción medible de jerga interna y texto redundante.

**Errores comunes:**
- Escribir solo para el diseño visual y descubrir los problemas con lector de pantalla después.
- Botones que dicen "OK", "Aceptar" o "Continuar" sin objeto.
- Mensajes de error que solo dicen el problema ("Algo salió mal").
- IDs de elementos generados automáticamente (`button-9f2a`) en lugar de semánticos (`btn-confirm-subscription`).
- Glosario inconsistente entre pantallas y entre código y copy.

**Referencias cruzadas:**
- [3.2.3 Arquitectura de componentes](./03-arquitectura-de-componentes.md)
- [3.2.4 Handoff técnico](./04-handoff-tecnico.md)
- [3.2.5 UX writing para interfaces dinámicas](./05-ux-writing-interfaces-dinamicas.md)
- [3.1.13 Accesibilidad básica](../fundamentos-ux-ui/13-accesibilidad-basica.md)

</div>

---

## Glosario

**Microcopy híbrido** — redacción que cumple simultáneamente con los criterios de usabilidad humana y de procesabilidad por agentes de IA y lectores de pantalla.

**Autonomía de contexto** — propiedad de un texto que puede entenderse sin depender de su posición visual ni de elementos vecinos.

**Error de tres partes** — convención de redacción de mensajes de error que incluye problema, causa y solución en el mismo mensaje.

**`UXW_SPEC.md`** — archivo de referencia por componente que documenta microcopy, estados e IDs estables. Complemento del `component-brief.md` (módulo 3.2.3).

**Selector estable** — atributo `id`, `data-testid` o equivalente cuyo nombre describe la función del elemento y no cambia entre builds, permitiendo que tests y agentes lo referencien con seguridad.

:::info Referencias primarias
- [Nielsen Norman Group — Microcopy](https://www.nngroup.com/articles/microcopy/)
- [WAI-ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
- [Shopify Polaris — Content guidelines](https://polaris.shopify.com/content)
- [Material Design 3 — Writing](https://m3.material.io/foundations/content-design/style-guide/overview)
:::

---

<AuthorCredit />
