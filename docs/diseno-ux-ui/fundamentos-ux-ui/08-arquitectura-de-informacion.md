---
sidebar_position: 8
sidebar_label: 3.1.8 Arquitectura de información
---

# Arquitectura de información

Rosa quiere marcar que una clienta pagó con transferencia pendiente de confirmar. Abre la app y se encuentra con un menú que dice *"Operaciones"* → *"Movimientos"* → *"Pendientes"*. Ella busca "Cobros", no "Operaciones". ¿Qué hace? O adivina, o llama al soporte, o deja la clienta yéndose sin marcar el cobro. El problema no está en los colores ni en la tipografía: está en cómo se **organizó la información** antes de diseñar cualquier pantalla.

La **arquitectura de información** (*Information Architecture*, IA) es la práctica de decidir cómo organizar las partes de un producto digital para que sea comprensible. Antes de diseñar una pantalla, la IA responde a preguntas más básicas: *¿qué secciones existen?, ¿cómo se agrupan?, ¿cómo se llaman?, ¿cómo se llega de una a otra?* Si la IA está mal, ninguna pantalla bonita la rescata: los usuarios se perderán en los menús y abandonarán antes de llegar a la tarea.

En una app de gestión usada entre cita y cita, una IA clara es lo que permite que la dueña del salón encuentre el cobro pendiente, la próxima cita o el producto con stock bajo **sin leer un manual**.

## Para qué sirve

Una buena arquitectura de información produce tres resultados concretos:

### Navegación clara

Los usuarios encuentran de forma rápida y predecible lo que buscan: formularios, fechas límite, ayudas, estados de trámites. Un menú bien diseñado no necesita un buscador para ser útil — aunque ambos se complementan.

### Reducción de errores

Una organización intuitiva minimiza decisiones equivocadas: marcar el cobro bajo el servicio incorrecto, cancelar una cita cuando solo se quería reagendar, descontar stock de un producto que no era el vendido. La agrupación y etiquetado correctos evitan que el usuario tenga que adivinar.

### Optimización de procesos

Tareas complejas —como cerrar la caja del día con cobros en efectivo, tarjeta y transferencias— se dividen en **pasos lógicos y comprensibles**. El usuario entiende dónde está, cuánto falta, y qué tiene que tener a mano en cada paso. La IA bien hecha convierte procesos largos en secuencias manejables.

## Los tres pilares de la información

Toda decisión de arquitectura de información se apoya en tres fuentes de contexto:

### Usuarios

Las personas que usarán el producto: sus segmentos, su comportamiento, sus búsquedas típicas y los términos con los que ellos nombran las cosas (no con los que nosotros las nombramos).

Una sección puede llamarse internamente *"Movimientos por cuenta de ingresos"*, pero si la usuaria la busca como *"Cobros del día"*, ese es el nombre que debe aparecer en el menú.

### Contenido

Los textos, imágenes, datos, formularios y procesos que hay que presentar. La pregunta clave es: **¿cuánto contenido tengo, cómo se relaciona entre sí, y qué estructura emerge naturalmente?**

Si el contenido es homogéneo y extenso (catálogo de 200 servicios), la estructura tiende a ser jerárquica. Si el contenido es heterogéneo y cruzado (un paquete promocional que combina corte + tinte + manicure), probablemente necesitas taxonomías múltiples o etiquetado facetado.

### Contexto

Los objetivos del producto, restricciones legales (emisión de comprobantes, privacidad de datos de clientas), el presupuesto, la tecnología disponible y sus limitaciones. Por ejemplo, la sección de comprobantes no puede ocultarse aunque complique el flujo principal: la ley obliga a emitirlos. El contexto enmarca lo que es posible.

## Los 8 principios de Dan Brown

Dan Brown propuso ocho principios que funcionan como heurísticas de diseño de IA. No son reglas absolutas, pero cada uno responde a un error común y ayuda a discutir decisiones con criterio compartido:

1. **Principio del objeto.** El contenido está vivo: crece, cambia, se relaciona con otros contenidos. Trátalo como objetos con ciclo de vida, no como páginas estáticas. *Ejemplo:* una ficha de clienta no es un texto — es un objeto que acumula citas, cobros y notas.
2. **Principio de elección.** La gente cree que quiere muchas opciones, pero en realidad rinde mejor con **menos opciones bien organizadas**. Un menú con 7 secciones claras supera a uno con 30 mezcladas.
3. **Principio de divulgación (disclosure).** Muestra solo lo necesario para ayudar al usuario a decidir el siguiente paso. No inundes con detalle al inicio; revélalo progresivamente conforme avanza.
4. **Principio de ejemplaridad.** Describe las categorías con **ejemplos concretos** de lo que contienen. "Cobros (pendientes, del día, comprobantes)" comunica más que "Cobros" a secas.
5. **Principio de la puerta principal (front door).** La mitad de los usuarios no llega por la página de inicio — llega por enlaces profundos, búsquedas o notificaciones. Cada pantalla debe ser auto-suficiente: decir dónde estás y cómo volver.
6. **Principio de clasificación múltiple.** Las personas buscan información por rutas distintas (por nombre, por fecha, por estado). Ofrece **varios caminos al mismo contenido** sin duplicarlo: categorías + filtros + búsqueda.
7. **Principio de navegación centrada.** Los menús deben tener una lógica explícita y mantenerse estables. Cambiar el orden o los nombres entre secciones rompe la memoria que el usuario ya construyó.
8. **Principio de crecimiento.** El contenido de hoy será una fracción del de mañana. Diseña la estructura para que acepte secciones y contenidos nuevos sin rehacerse — o asumirás deuda de IA a los 6 meses.

:::tip Cómo usar los principios
No los apliques como checklist al final. Úsalos como **preguntas durante el diseño**: "¿esta pantalla cumple el principio de la puerta principal?", "¿este menú tiene crecimiento?". Sirven más para detectar faltas que para validar aciertos.
:::

## Herramientas de arquitectura de información

### Mapa de sitio (sitemap)

Diagrama que muestra **todas las secciones del producto y cómo se jerarquizan**. Es el primer artefacto de IA: permite ver el bosque completo antes de entrar a cada árbol.

```
Inicio (hoy)
├── Agenda
│   ├── Hoy
│   ├── Próximos días
│   └── Clientas recurrentes
├── Cobros
│   ├── Pendientes
│   ├── Del día
│   └── Comprobantes emitidos
├── Clientas
│   ├── Fichas
│   ├── Historial de servicios
│   └── Preferencias y notas
├── Catálogo
│   ├── Servicios
│   ├── Productos
│   └── Promociones
├── Reportes
│   ├── Ventas de la semana
│   └── Stock bajo
└── Configuración
    ├── Equipo
    ├── Horarios
    └── Ayuda
```

### Taxonomía y etiquetado

Las **categorías** que agrupan contenido y los **nombres** que se les asignan. Una decisión de etiquetado aparentemente menor (*"Cobros"* vs *"Operaciones"* vs *"Movimientos"*) puede duplicar o reducir a la mitad la tasa de acierto al primer clic.

Regla: usar el lenguaje del usuario, no el lenguaje interno. El análisis de búsquedas reales dentro del sitio es una fuente valiosa.

### Tree testing

Método de investigación específico para IA. Se le presenta al usuario el árbol de navegación (sin interfaz, solo la jerarquía textual) y se le pide encontrar donde haría cierta tarea. Las respuestas revelan qué etiquetas son ambiguas y qué agrupaciones no se entienden.

Herramientas: [Treejack](https://www.optimalworkshop.com/treejack/), [UserTesting](https://www.usertesting.com/). Un tree testing con 15–20 usuarios detecta los problemas más graves de la arquitectura en pocas horas.

### Card sorting

Complementario al tree testing: se le dan al usuario tarjetas con nombres de contenidos y se le pide agruparlos como le parezca. Revela **cómo organiza el usuario la información** y ayuda a descubrir categorías que el equipo no había considerado.

### Prototipos y wireframes

Representaciones de baja fidelidad donde se valida que la IA propuesta se traduce bien en pantallas navegables. Permiten probar con usuarios antes de invertir en diseño visual detallado.

### Diseño de navegación

La traducción visible de la arquitectura: menús principales, secundarios, breadcrumbs, filtros, buscador. Una misma arquitectura puede presentarse como menú vertical, horizontal o con mega-menú según el dispositivo y la cantidad de contenido.

## Cuándo aplicar arquitectura de información

- **En la fase de planificación**, antes de diseñar pantallas.
- **En un rediseño o escalamiento**, cuando al producto se le añadieron secciones sin revisar la estructura global y empieza a sentirse "con tantos menús que ya no se encuentra nada".
- **Cuando detectas problemas de navegación** en datos de analytics (tasa de rebote alta en landing, búsquedas internas muy usadas, rutas largas para tareas frecuentes) o en feedback de usuarios.

## Principios operativos

- **Prioriza lo frecuente sobre lo completo**. Lo que se usa a diario debe estar a un clic; lo excepcional puede estar a tres.
- **Minimiza la profundidad**. Árboles de 3 niveles son más usables que de 5. Si necesitas más profundidad, replantea la agrupación.
- **Evita categorías "cajón de sastre"** tipo *"Otros"* o *"Varios"*: indican que la taxonomía no está resuelta.
- **Un contenido, un hogar**. Un trámite puede aparecer en varias rutas (shortcuts, búsqueda), pero tiene **una** ruta canónica. Sin esto, las URLs se multiplican y el SEO se fragmenta.

:::warning El costo de una IA mal pensada
Cambiar la arquitectura después de haber publicado el producto implica romper enlaces externos, confundir a usuarios que ya aprendieron la ruta anterior, y reescribir documentación. Invertir una semana en IA antes de diseñar pantallas ahorra meses de rediseño después.
:::

## Puente al siguiente módulo

Tenemos un sitemap y etiquetas. Ahora necesitamos **verificar con usuarias** si Rosa puede cerrar el cobro y agendar la próxima cita sin perderse. Ese es el trabajo de las pruebas de usabilidad, el método más rentable de toda la caja de herramientas de UX. Eso es el [módulo 3.1.9](./09-pruebas-de-usabilidad.md).

## Glosario

**Arquitectura de información** *(Information architecture)* — organización, estructura y etiquetado de contenido para que sea comprensible y navegable.

**Sitemap** *(Mapa de sitio)* — diagrama jerárquico de todas las secciones del producto.

**Taxonomía** *(Taxonomy)* — sistema de categorías y etiquetas usado para agrupar contenido.

**Tree testing** — método de investigación que valida si la jerarquía permite al usuario encontrar contenido.

**Card sorting** — método de investigación donde los usuarios agrupan contenidos para revelar su modelo mental.

**Wayfinding** — conjunto de pistas (breadcrumbs, títulos, menús activos) que ayudan al usuario a saber dónde está y cómo regresar.

:::info Referencias primarias
- [Information Architecture for the Web and Beyond (Rosenfeld, Morville, Arango)](https://www.oreilly.com/library/view/information-architecture-4th/9781491911686/) — obra de referencia de IA.
- [Eight Principles of Information Architecture — Dan Brown (A List Apart)](https://alistapart.com/article/eight-principles-of-information-architecture/) — los 8 principios explicados por su autor.
- [Nielsen Norman Group · IA topic](https://www.nngroup.com/topic/information-architecture/) — artículos aplicados.
- [Optimal Workshop · Tree testing guide](https://www.optimalworkshop.com/learn/tree-testing/) — método práctico.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** diseñar o auditar una arquitectura de información que permita a los usuarios encontrar contenido y completar tareas con mínima fricción.

**Entradas:**
- Inventario de contenido del producto (pantallas, flujos, entidades).
- Segmentación de usuarios y sus tareas frecuentes.
- Datos existentes: analytics de navegación, búsquedas internas, tickets de soporte.
- Restricciones regulatorias o de negocio que obligan a presentar cierto contenido.

**Pasos:**
1. Inventariar el contenido existente y agruparlo por tipo.
2. Identificar las 10–15 tareas más frecuentes de cada segmento de usuario.
3. Proponer un sitemap inicial priorizando lo frecuente.
4. Validar con card sorting (si hay dudas de agrupación) y tree testing (si hay dudas de etiquetas).
5. Iterar el sitemap hasta que la tasa de acierto al primer clic supere el umbral (≥ 70 %).
6. Traducir la IA a menús y navegación concretos; verificar que un contenido tenga una ruta canónica.
7. Medir en producción (analytics, búsquedas internas) y refinar.

**Salidas:**
- Sitemap documentado y versionado.
- Taxonomía con etiquetas validadas con usuarios.
- Mapeo de tareas frecuentes a rutas de navegación.
- Resultados de tree testing con hallazgos accionables.

**Errores comunes:**
- Diseñar la IA desde el organigrama interno, no desde las tareas del usuario.
- Etiquetar con jerga interna en lugar del lenguaje del usuario.
- Crear profundidad excesiva por miedo a "mezclar" temas.
- Usar categorías comodín ("Otros") que esconden decisiones no tomadas.
- Omitir validación con tree testing y descubrir los problemas solo en producción.

**Referencias cruzadas:**
- [3.1.5 UX Research](./05-ux-research.md)
- [3.1.6 Análisis de usuarios y competencia](./06-analisis-usuarios-y-competencia.md)
- [3.1.9 Pruebas de usabilidad](./09-pruebas-de-usabilidad.md)
</div>
