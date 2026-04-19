---
sidebar_position: 5
sidebar_label: 3.1.5 UX Research
---

# UX Research

En los primeros módulos distinguimos UX de UI y aprendimos que el diseño debe partir del usuario. Pero eso plantea una pregunta inmediata: **¿cómo sabemos qué necesita realmente el usuario?** La respuesta no es una sesión de brainstorming del equipo; es investigación con método.

La investigación de usuarios (**UX Research**) es el conjunto de técnicas que usamos para entender necesidades, comportamientos y percepciones de quienes van a usar el producto. Sin investigación, el diseño es una conversación entre el equipo y sus propios supuestos: *creemos* que el usuario necesita X, *asumimos* que entenderá la etiqueta Y, *suponemos* que no le molesta el flujo Z. La investigación reemplaza ese lenguaje por datos.

Este módulo presenta cuándo investigar, qué métodos elegir según el objetivo y cómo equilibrar el costo de la investigación con la calidad de las decisiones que habilita.

## Sin investigación, no es UX

Diseñar una interfaz "pensando en el usuario" no equivale a hacer UX. La diferencia está en **haber escuchado al usuario con un método que minimice el sesgo**. Si todo lo que sabemos del usuario proviene de nuestra intuición o de reportes internos del negocio, estamos haciendo diseño de interfaz, no diseño de experiencia.

Un equipo que no investiga produce productos que parecen funcionar hasta que se miden: formularios con tasas de abandono altas, flujos que se completan con errores, funcionalidades que nadie usa.

## Cuándo se aplica la investigación

La investigación no es una fase única al principio. Aplica en tres momentos distintos, y cada uno responde preguntas diferentes:

| Momento | Pregunta guía | Ejemplo de método |
|---------|---------------|-------------------|
| **Antes** de diseñar | ¿Qué necesita el usuario? ¿Qué problema real estamos resolviendo? | Entrevistas, etnografía, análisis de usuarios |
| **Durante** el diseño | ¿La solución propuesta funciona para el usuario? | Pruebas de concepto, diseño participativo, pruebas de usabilidad con prototipos |
| **Después** del lanzamiento | ¿El producto está funcionando? ¿Dónde están los problemas? | Analytics, A/B testing, encuestas de satisfacción, análisis de clicks |

Saltarse la investigación *antes* es la causa más frecuente de productos que no se usan. Saltarse la investigación *después* es la causa más frecuente de productos que no mejoran.

## Los cuatro cuadrantes de UX Research

Una forma útil de organizar los métodos es por **qué tipo de dato producen**:

| | Lo que la gente **dice** | Lo que la gente **hace** |
|--|---|---|
| **Cualitativo (opiniones/observación directa)** | Entrevistas, focus groups, diseño participativo | Pruebas de usabilidad, etnografía, tracking visual |
| **Cuantitativo (medición agregada)** | Encuestas, formularios de satisfacción, pruebas de concepto | Analytics, A/B testing, análisis de clicks, tree testing |

- **Escuchar opiniones** (arriba-izquierda): qué piensa el usuario, qué dice que haría.
- **Medir expectativas** (abajo-izquierda): cómo se distribuyen esas opiniones en una población más grande.
- **Observar acciones** (arriba-derecha): qué hace el usuario cuando interactúa con el producto.
- **Medir acciones** (abajo-derecha): cuántas veces ocurre cada acción y con qué resultado.

Los métodos que miden lo que la gente **hace** pesan más que los que registran lo que la gente **dice** — porque las personas son malas prediciendo su propio comportamiento. Pero los métodos de opinión son indispensables para entender *por qué* hacen lo que hacen.

## Métodos principales y cuándo usarlos

| Método | Tipo | Uso típico |
|--------|------|------------|
| **Entrevistas** | Cualitativo · dice | Explorar necesidades no articuladas; contexto de uso |
| **Focus groups** | Cualitativo · dice | Reacción grupal a conceptos; rescatar con cuidado (riesgo de sesgo grupal) |
| **Diseño participativo** | Cualitativo · dice | Co-diseñar con usuarios clave cuando el dominio es muy especializado |
| **Pruebas de concepto** | Cuantitativo · dice | Validar si una idea resuena antes de invertir en diseño detallado |
| **Pruebas de usabilidad** | Cualitativo · hace | Detectar fricciones específicas en un flujo (el método más rentable) |
| **Etnografía** | Cualitativo · hace | Entender el contexto real de uso (lugar de trabajo, dispositivos) |
| **Tracking visual / eye-tracking** | Cualitativo · hace | Investigar jerarquía visual, atención en pantallas complejas |
| **Análisis de clicks / heatmaps** | Cuantitativo · hace | Ver dónde interactúa realmente la gente en páginas en producción |
| **A/B testing** | Cuantitativo · hace | Comparar dos variantes con tráfico real y métricas objetivas |
| **Encuestas** | Cuantitativo · dice | Medir actitudes/preferencias en muestras grandes |
| **Analytics** | Cuantitativo · hace | Identificar dónde abandonan los usuarios un flujo |
| **Tree testing** | Cuantitativo · hace | Validar arquitectura de información antes de diseñar pantallas |

## Objetivos de investigación

Un método sin objetivo produce datos sin decisiones. Antes de elegir el método, redacta el objetivo como una pregunta que la investigación debe responder:

- Mal objetivo: *"Vamos a hacer entrevistas a usuarios."*
- Buen objetivo: *"¿Por qué las dueñas de salón abandonan el registro de un cobro en el paso de seleccionar método de pago?"*

Un buen objetivo:

1. Nombra una decisión que vas a tomar con el resultado.
2. Es lo suficientemente estrecho para ser respondido por un método viable.
3. Indica qué tipo de dato necesitas (explorar, medir, observar, comparar).

## Costo vs. calidad de la decisión

Toda investigación tiene un costo (tiempo, reclutamiento, horas de análisis). La pregunta no es si hay que investigar, sino **cuánta investigación justifica la decisión que vas a tomar**.

- **Decisión barata y reversible** (el color de un botón en una pantalla interna): una prueba rápida con 3 usuarios o un A/B test bastan.
- **Decisión cara e irreversible** (rediseño del flujo de cobro en una app que usan cientos de pymes): semanas de investigación con múltiples métodos — el costo de equivocarse supera cualquier ahorro.

La regla práctica: **invierte en investigación proporcional al costo de equivocarte**.

## Ejemplo aplicado: app de gestión para pymes de servicios

Una app para gestionar un salón de belleza, una barbería o un estudio de tatuajes atiende a perfiles muy distintos: la dueña que hace todo sola, la recepcionista que solo agenda, el estilista que cobra al final del servicio. El abandono de cualquiera de ellos en un flujo crítico se traduce en:

- Cobros no registrados (pérdida directa de ingresos).
- Citas dobles o perdidas (frustración de clientas y reputación dañada).
- Dueñas que terminan llevando todo en un cuaderno paralelo (la app pierde valor).

Investigar con las usuarias cuáles son los puntos de fricción reales —entrevistas + analytics del producto— produce decisiones de diseño mucho más precisas que asumir que *"ya existe un tutorial, si no lo entienden es porque no lo leen"*.

:::warning Sin investigación, no es UX
Si todo lo que sabes del usuario proviene de la intuición del equipo o de reportes internos del negocio, estás haciendo diseño de interfaz, no diseño de experiencia.
:::

## Puente al siguiente módulo

Una vez entendido **que hay que investigar** y **qué método usar**, la siguiente pregunta es más concreta: *¿a quién investigo?* Segmentar bien a los usuarios y mirar cómo otros resuelven el mismo problema es lo que convierte una decisión aislada de método en un programa de investigación útil. Eso es el [módulo 3.1.6](./06-analisis-usuarios-y-competencia.md).

## Glosario

**UX Research** *(User research)* — conjunto de métodos para entender al usuario antes, durante y después del diseño.

**Método cualitativo** *(Qualitative method)* — produce descripciones ricas con pocos participantes; responde *por qué*.

**Método cuantitativo** *(Quantitative method)* — produce medidas agregadas con muchos participantes; responde *cuánto*.

**Objetivo de investigación** *(Research goal)* — pregunta específica que la investigación debe responder para habilitar una decisión.

**Sesgo de confirmación** *(Confirmation bias)* — tendencia a interpretar los datos de forma que confirmen supuestos previos; principal enemigo de toda investigación.

:::info Referencias primarias
- [Nielsen Norman Group · When to Use Which User-Experience Research Methods](https://www.nngroup.com/articles/which-ux-research-methods/) — marco de los cuatro cuadrantes.
- [Nielsen Norman Group · UX Research Cheat Sheet](https://www.nngroup.com/articles/ux-research-cheat-sheet/) — qué método usar en cada fase.
- [Measuring the User Experience (Tullis & Albert)](https://www.sciencedirect.com/book/9780128180808/measuring-the-user-experience) — métricas cuantitativas de UX.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** elegir el método de investigación correcto para un objetivo dado y justificar el costo con base en la decisión que habilita.

**Entradas:**
- Decisión de producto o diseño pendiente.
- Perfil de usuarios disponibles para investigar.
- Restricciones de tiempo y presupuesto.
- Datos ya existentes (analytics, tickets de soporte, investigaciones previas).

**Pasos:**
1. Formular el objetivo de investigación como pregunta específica y accionable.
2. Identificar si necesitas datos de *opinión* o de *comportamiento*, cualitativos o cuantitativos.
3. Ubicar el objetivo en uno de los cuatro cuadrantes.
4. Elegir el método más barato que responda la pregunta con suficiente rigor.
5. Definir criterio de éxito: cuántos participantes, en qué plazo, con qué métricas.
6. Ejecutar, analizar y documentar hallazgos con evidencia citable.

**Salidas:**
- Plan de investigación con método, participantes y duración.
- Hallazgos priorizados con evidencia (citas, métricas, grabaciones).
- Decisión de diseño vinculada a cada hallazgo.

**Errores comunes:**
- Elegir método por popularidad ("hagamos focus groups") en vez de por objetivo.
- Preguntar al usuario qué quiere en vez de observar qué hace.
- Investigar sin decisión pendiente: genera informes que nadie lee.
- Generalizar hallazgos cualitativos (de 5 usuarios) como si fueran cuantitativos.
- Omitir investigación *posterior* al lanzamiento y perder la oportunidad de mejorar.

**Referencias cruzadas:**
- [3.1.6 Análisis de usuarios y competencia](./06-analisis-usuarios-y-competencia.md)
- [3.1.7 Personas y escenarios](./07-personas-y-escenarios.md)
- [3.1.9 Pruebas de usabilidad](./09-pruebas-de-usabilidad.md)
</div>
