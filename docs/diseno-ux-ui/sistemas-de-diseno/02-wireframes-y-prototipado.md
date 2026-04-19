---
sidebar_position: 2
sidebar_label: 3.2.2 Wireframes y prototipado
---

# Wireframes y prototipado

Los módulos de fundamentos (3.1) nos dieron investigación, personas, arquitectura de información y teoría visual. La sección 3.2 define tokens, componentes y handoff. Entre ambos hay un paso que muchos equipos se saltan y termina costándoles semanas de rediseño: **poner el diseño en un formato testeable antes de invertir en pixeles finales**.

Ese paso es el wireframing y el prototipado. Lo que parece "solo dibujar cajas" es, en realidad, **la última oportunidad barata de cambiar de opinión**. Cuanto más tarde se detecta un problema de flujo o de arquitectura, más caro cuesta arreglarlo — y el costo crece de forma no lineal con la fidelidad del diseño.

## La escalera de fidelidad

No existe "el wireframe correcto". Existe una escalera de fidelidades, cada una con un propósito distinto:

| Fidelidad | Qué valida | Costo de crear | Costo de cambiar |
|-----------|------------|---------------:|-----------------:|
| **Sketch en papel** | Flujo grueso, estructura general | Minutos | Nulo |
| **Wireframe lo-fi** (cajas grises) | Jerarquía, contenido, etiquetado | Horas | Bajo |
| **Wireframe interactivo** | Navegación, ruta del usuario | Horas–días | Bajo-medio |
| **Mockup hi-fi** (visual final) | Estética, marca, detalles visuales | Días | Medio-alto |
| **Prototipo interactivo hi-fi** | Interacciones, microinteracciones, estados | Días–semanas | Alto |

La regla operativa: **sube al siguiente escalón solo cuando el actual ya no responde la pregunta pendiente**. Saltar escalones ahorra tiempo en el corto plazo y lo paga con intereses cuando el equipo tiene que rehacer un mockup de 30 pantallas porque el flujo estaba mal.

## Por qué un wireframe no es un mockup sin color

La tentación de saltar directo a hi-fi es real: *"Figma lo hace tan fácil, ¿para qué dibujo cajas grises?"*. La diferencia es lo que pasa **en las reuniones de revisión**.

- Con un wireframe lo-fi, la conversación gira en torno a **qué información aparece, dónde y en qué orden**. Nadie discute si el azul del botón es el correcto, porque no hay azul.
- Con un mockup hi-fi, la conversación se desvía automáticamente a decisiones visuales. El flujo mal pensado pasa desapercibido porque la estética captura la atención.

Un wireframe es un **contrato sobre información y flujo**. Un mockup es un **contrato sobre apariencia**. Firmar el segundo sin el primero es comprometerse con un diseño antes de saber si funciona.

:::tip Qué debe contener un wireframe lo-fi
- Regiones claras (header, contenido, acciones, ayuda).
- Jerarquía por tamaño y posición — no por color.
- Etiquetas finales de texto (el copy real, no "Lorem ipsum").
- Estados mínimos: vacío, con datos, error.

Y qué **no** debe contener: color de marca, tipografía final, iconos definitivos, imágenes reales. Todo eso pertenece al siguiente escalón.
:::

## Mobile first como disciplina, no como moda

Diseñar primero para móvil no es una preferencia estética; es una **limitación útil que fuerza priorización**. En un ancho de 375 px el equipo no puede esconder complejidad detrás de columnas auxiliares o tooltips laterales. Lo que no cabe en móvil revela una decisión pendiente: o no pertenece a la tarea principal, o la tarea está mal definida.

Cuatro decisiones que mobile-first obliga a tomar desde el wireframe:

1. **Contenido prioritario**: qué aparece sobre el *fold*, qué va después.
2. **Navegación simplificada**: menús cortos, rutas directas a las 3–5 tareas frecuentes.
3. **Interacción táctil**: áreas mínimas de 44×44 px; no hay hover que rescate a un elemento pequeño.
4. **Carga rápida**: lo que pesa en móvil también pesa en desktop. Decidir desde el wireframe qué imágenes son indispensables.

Diseñar desktop-first y luego "responsive" suele significar en la práctica: esconder elementos en móvil hasta que quepan. El resultado es una experiencia móvil degradada, no diseñada.

## AI-first: la conversación como interfaz

Mobile-first llegó cuando el contexto de uso cambió: ya no era razonable asumir que el usuario estaba sentado frente a un monitor. Hoy hay un cambio análogo en marcha, esta vez no de dispositivo sino de **quién mira la pantalla**. Marc Benioff, CEO de Salesforce, lo resumió en 2026 con la frase *"In the agentic enterprise, the conversation is the interface"* — un agente de IA no abre una pantalla, llama una API o conversa por texto. Sam Altman, CEO de OpenAI, ya había anticipado que *"toda empresa se convierte, en la práctica, en una API company"*.

Esto no significa que los wireframes dejen de importar. Significa que **una parte creciente de tu producto se consume sin UI visual**, y el diseño tiene que contemplarlo desde el wireframe. Tres consecuencias prácticas:

1. **Cada pantalla tiene una contraparte conversacional.** Para la pantalla de "Cerrar cobro" de Rosa debe existir un equivalente invocable por voz o texto ("cobra la cita de Marta con tarjeta y agenda la siguiente en 4 semanas"). Si no existe, el producto pierde una superficie entera.
2. **Las acciones deben tener contratos, no solo botones.** Cada botón del wireframe representa una intención. Esa intención debe poder expresarse como función con entradas tipadas, salidas observables y efectos claros — porque un agente la va a invocar. El wireframe empieza a parecerse más a un diseño de API con pantalla adjunta que al revés.
3. **El estado debe ser legible por máquina, no solo visible.** Un "pago pendiente" marcado solo con un color naranja es invisible para un agente. El estado necesita un token semántico (`status: "pending_payment"`) que alimente tanto la UI visual como la respuesta conversacional.

:::tip Dos disciplinas, no una sustitución
AI-first no reemplaza mobile-first; lo extiende. Mobile-first impone priorización de contenido y área táctil. AI-first impone que cada acción tenga un contrato explícito y que cada estado sea consultable. Un wireframe robusto pasa ambos filtros: *¿esto cabe en 375 px?* y *¿esto es invocable y observable desde una conversación?*
:::

Para profundizar en cómo estructurar las capacidades del producto para consumo por agentes, ver el módulo [6.3 Arquitectura orientada a skills](../../colaboracion-con-agentes-ia/03-arquitectura-orientada-a-skills.md).

## Gestalt aplicada al wireframe

Un wireframe no usa color ni tipografía final, pero sigue teniendo que **comunicar jerarquía y agrupación**. La [teoría Gestalt](../fundamentos-ux-ui/12-jerarquia-visual-y-gestalt.md) resuelve esto con tres palancas que funcionan sin color:

- **Proximidad**: elementos cercanos se perciben agrupados. El espaciado hace el trabajo que más tarde hará el color.
- **Similitud de forma**: cajas del mismo tamaño y estilo se leen como categoría. Distintos tamaños crean jerarquía.
- **Región común**: encerrar elementos en una caja los agrupa aunque no estén especialmente cerca.

Si un wireframe comunica su estructura solo con estas tres palancas, el mockup hi-fi posterior será mucho más fácil: la jerarquía ya está resuelta y el color solo la refuerza.

## Flujos de usuario vs. wireframes

Son artefactos distintos, y confundirlos produce diseños débiles.

- **Flujo de usuario**: diagrama que muestra la secuencia de pantallas y decisiones del usuario para completar una tarea. Responde *¿qué pantallas hacen falta?*
- **Wireframe**: representación del contenido y estructura de **una** pantalla concreta. Responde *¿qué aparece en esta pantalla?*

El flujo va primero. Sin él, el equipo termina diseñando pantallas huérfanas que no encajan en un recorrido. La secuencia correcta: **flujo → wireframe → prototipo**.

Un flujo típico puede representarse como diagrama simple:

```
[Inicio sesión] → [Agenda del día] → ¿hay cita terminada sin cobrar?
                                     ├─ Sí → [Ficha de cita] → [Cerrar cobro] → [Agendar siguiente]
                                     └─ No → [Nueva cita] → [Seleccionar clienta] → [Confirmar]
```

Cada nodo del flujo es candidato a wireframe. No todos lo merecen: pantallas triviales (una confirmación) pueden resolverse en el prototipo sin wireframe dedicado.

## Los 8 principios de arquitectura de información aplicados al wireframe

[Dan Brown](https://rosenfeldmedia.com/books/communicating-design/) formuló 8 principios que conviene tener a mano al estructurar un wireframe. Cada uno previene un error específico:

1. **Principio del objeto** — trata cada contenido como "vivo": crecerá, cambiará, se relacionará con otros. Diseña la pantalla para acomodar el crecimiento (un listado que hoy tiene 5 items mañana tendrá 500).
2. **Principio de elección** — menos opciones bien organizadas > muchas opciones mal agrupadas. Si una pantalla tiene 7 acciones primarias, ninguna es primaria.
3. **Principio de divulgación** — muestra solo la información necesaria para el paso actual. La divulgación progresiva evita saturar.
4. **Principio de ejemplaridad** — cuando uses categorías, muestra ejemplos de lo que cada una contiene. "Documentos" significa cosas distintas para distintas personas.
5. **Principio de la puerta principal** — la página de inicio no es la única entrada; los usuarios llegan por buscadores, enlaces directos, notificaciones. Cada pantalla interior debe poder explicarse sola.
6. **Principio de clasificación múltiple** — distintas personas buscan lo mismo por distintas rutas. Ofrece al menos dos formas de llegar a los contenidos críticos (ej. menú + buscador).
7. **Principio de navegación centrada** — el menú debe responder a una estrategia, no ser un agregado histórico. Si hay 15 items, probablemente necesitas reagrupar.
8. **Principio de crecimiento** — el contenido crecerá. Diseña la IA y el wireframe para que acomode ese crecimiento sin rediseño.

Revisar estos 8 principios sobre un wireframe antes de avanzar ahorra la mayoría de los rediseños futuros.

## Prototipos: del wireframe estático a la simulación

Cuando el wireframe responde *qué aparece*, el prototipo interactivo responde *qué pasa al tocar*. Se construye conectando pantallas con transiciones, estados y microinteracciones.

### Prototipos de baja fidelidad

- **Propósito**: validar flujo de navegación — *"¿el usuario llega de la pantalla A a la pantalla E sin perderse?"*
- **Herramientas**: wireframes clickeables en Figma, Balsamiq, o incluso papel con frames en post-it.
- **Participantes**: 3–5 usuarios, sesiones cortas.
- **Lo que revela**: etiquetas ambiguas, pasos faltantes, rutas alternativas no consideradas.

### Prototipos de alta fidelidad

- **Propósito**: validar interacciones, estados (cargando, error, vacío), microinteracciones y percepción de calidad.
- **Herramientas**: Figma interactivo (estándar actual), Framer (animación avanzada), ProtoPie.
- **Participantes**: 5–8 usuarios, sesiones más largas.
- **Lo que revela**: fricciones finas, errores de feedback (el usuario no sabe si el clic registró), problemas de accesibilidad que no se veían en estático.

:::warning La trampa del prototipo hi-fi total
Intentar prototipar **todos** los estados y pantallas en alta fidelidad produce explosión combinatoria: 30 pantallas × 4 estados × 3 breakpoints = 360 variantes que nadie mantendrá. Prototipa hi-fi solo las pantallas y estados que **necesitas validar con usuarios**; el resto queda documentado en el handoff.
:::

## Microinteracciones: cuándo y por qué

Una microinteracción es una animación breve que da **feedback al usuario** sobre algo que acaba de pasar: un botón que cambia de color al recibir clic, un ícono de "guardado" que aparece y desaparece, una lista que se reordena con transición.

Criterios para decidir si añadirla:

- **Informa o guía**: *sí*. Un check verde que confirma el envío reduce ansiedad.
- **Decora**: *no*. Animaciones sin propósito añaden peso y latencia.
- **Reemplaza mensaje de texto**: *a veces*. Un check puede reemplazar un mensaje, pero no si la acción tiene consecuencias importantes (en ese caso, ambos).

Regla: **toda microinteracción debe tener un propósito funcional**. Si al removerla el usuario no se pierde información, probablemente sobra.

## Conectar el prototipo con la prueba de usabilidad

El prototipo no es el entregable final — es **un instrumento para probar**. Cerrar el ciclo significa:

1. Formular tareas concretas que el usuario intentará completar sobre el prototipo (ver [3.1.9 Pruebas de usabilidad](../fundamentos-ux-ui/09-pruebas-de-usabilidad.md)).
2. Ejecutar 5 sesiones con think-aloud.
3. Identificar fricciones y decidir si se resuelven en el prototipo actual (iterando) o si requieren volver al wireframe (cambio de flujo).
4. Solo cuando el prototipo supera la prueba, avanzar al handoff.

Iterar dos rondas de 5 usuarios sobre un prototipo **bien acotado** es más barato y más preciso que construir la feature y descubrir los problemas en producción.

## Herramientas — cuándo usar cuál

| Herramienta | Mejor para | Evitar cuando |
|-------------|-----------|---------------|
| **Papel + post-it** | Ideación rápida, sesiones colaborativas | Compartir remoto con stakeholders |
| **Balsamiq** | Wireframes lo-fi deliberadamente "crudos" | Alta fidelidad, handoff |
| **Figma** | Todo el espectro: wireframes, mockups, prototipos interactivos, handoff | Animaciones complejas tipo Lottie |
| **Sketch** | Mockups hi-fi en equipos Mac-only | Colaboración multiplataforma |
| **Adobe XD** | Equipos ya integrados en Creative Cloud | Comunidad activa (menor que Figma) |
| **Penpot** | Alternativa open-source, self-hosted | Algunas integraciones de terceros |
| **Framer** | Interacciones complejas, microanimaciones | Wireframes tempranos |
| **ProtoPie** | Prototipos con lógica condicional avanzada | Trabajo colaborativo en vivo |

Figma se ha convertido en el estándar de facto porque cubre todo el espectro desde wireframe hasta handoff; eso es lo que ahorra el coste de migrar artefactos entre herramientas.

## Errores comunes

- **Saltar directo a hi-fi**: la conversación se pierde en colores antes de validar el flujo.
- **Wireframes con color de marca**: ya no son wireframes; son mockups pobres.
- **Prototipar todo**: explosión de estados que nadie mantendrá actualizados.
- **Confundir flujo con wireframe**: termina en pantallas que no encajan en un recorrido.
- **Prototipo sin prueba**: se construye el artefacto pero no se valida — se pierde la mitad del valor.
- **Lorem ipsum en wireframe lo-fi**: el copy real cambia la percepción de jerarquía; usa texto real desde el inicio.

## Glosario

**Wireframe** — representación esquemática de una pantalla sin diseño visual final; prioriza estructura, contenido y jerarquía.

**Mockup** — representación visual completa de una pantalla (colores, tipografía, imágenes); aún no es interactivo.

**Prototipo** *(Prototype)* — pantallas conectadas con transiciones e interacciones; simula el uso sin ser código real.

**Fidelidad** *(Fidelity)* — grado de detalle y realismo de un artefacto de diseño; va de lo-fi (cajas grises) a hi-fi (indistinguible del producto real).

**Flujo de usuario** *(User flow)* — secuencia de pantallas y decisiones que el usuario atraviesa para completar una tarea.

**Microinteracción** — animación breve que da feedback sobre una acción específica del usuario.

**Mobile first** — enfoque de diseño que parte de la pantalla más pequeña y escala hacia arriba, forzando priorización.

:::info Referencias primarias
- [Communicating Design (Dan Brown)](https://rosenfeldmedia.com/books/communicating-design/) — origen de los 8 principios de IA citados.
- [Nielsen Norman Group · Wireframing & Prototyping](https://www.nngroup.com/articles/ux-prototypes-low-or-high-fidelity/) — cuándo usar cada fidelidad.
- [Laws of UX (Jon Yablonski)](https://lawsofux.com/) — principios de Gestalt y percepción aplicados.
- [Figma · Interactive prototyping](https://help.figma.com/hc/en-us/categories/360002051613-Prototyping) — referencia de la herramienta estándar.
- [Marc Benioff (CEO de Salesforce) · "APIs are the new UI" — The Decoder (2026)](https://the-decoder.com/salesforce-ceo-marc-benioff-says-apis-are-the-new-ui-for-ai-agents/) — tesis de la conversación como interfaz en la empresa agéntica.
- [Parker Harris (Salesforce) · "Stop logging into Salesforce" (2026)](https://salesforcedevops.net/index.php/2026/03/31/parker-harris-stop-logging-into-salesforce-slackbot-march-2026/) — cómo Salesforce materializa el paradigma AI-first.
- Sam Altman (CEO de OpenAI), declaraciones públicas (2026) sobre *"every company becomes an API company"* — marco complementario del cambio hacia consumo programático.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** producir wireframes y prototipos que validen flujo, arquitectura de información e interacciones antes de invertir en mockups de alta fidelidad o código.

**Entradas:**
- Resultados de investigación, personas y arquitectura de información.
- Lista de tareas críticas del producto.
- Restricciones de plataforma (mobile first, desktop, tableta).
- Herramienta de diseño disponible (Figma recomendado).

**Pasos:**
1. Dibujar el flujo de usuario de las tareas críticas antes de cualquier pantalla.
2. Crear wireframes lo-fi de las pantallas del flujo, con texto real y jerarquía sin color.
3. Validar wireframe contra los 8 principios de Dan Brown.
4. Conectar pantallas en un prototipo lo-fi clickeable.
5. Ejecutar 5 pruebas de usabilidad con tareas concretas; iterar.
6. Subir al siguiente escalón (hi-fi interactivo) solo para los flujos que ya pasaron la validación lo-fi.
7. Prototipar hi-fi solo los estados y pantallas que requieren validación adicional (microinteracciones, estados de error, accesibilidad).
8. Documentar en el handoff lo que no fue prototipado.

**Salidas:**
- Diagramas de flujo de usuario versionados.
- Wireframes lo-fi con texto real por pantalla del flujo crítico.
- Prototipo navegable probado con al menos 5 usuarios.
- Lista de iteraciones aplicadas con la evidencia que las motivó.

**Errores comunes:**
- Saltar del research directo a mockups hi-fi.
- Incluir color de marca en wireframes lo-fi.
- Prototipar todos los estados en lugar de los que requieren validación.
- Confundir flujo con wireframe.
- Avanzar al handoff sin prueba de usabilidad sobre el prototipo.

**Referencias cruzadas:**
- [3.1.8 Arquitectura de información](../fundamentos-ux-ui/08-arquitectura-de-informacion.md)
- [3.1.9 Pruebas de usabilidad](../fundamentos-ux-ui/09-pruebas-de-usabilidad.md)
- [3.1.12 Jerarquía visual y Gestalt](../fundamentos-ux-ui/12-jerarquia-visual-y-gestalt.md)
- [3.2.3 Arquitectura de componentes](./03-arquitectura-de-componentes.md)
- [3.2.4 Handoff técnico](./04-handoff-tecnico.md)
</div>

<AuthorCredit />
