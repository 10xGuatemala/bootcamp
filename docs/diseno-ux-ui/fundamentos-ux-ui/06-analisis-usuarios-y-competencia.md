---
sidebar_position: 6
sidebar_label: 3.1.6 Análisis de usuarios y competencia
---

# Análisis de usuarios y competencia

En el módulo anterior definimos qué método de investigación usar según la pregunta. Ahora aplicamos ese marco a dos preguntas que siempre anteceden al diseño: **¿quiénes son mis usuarios y qué les cuesta hoy?** y **¿cómo resuelven otros el mismo problema?** El análisis de usuarios y el análisis de competencia son las dos lentes que las responden. Juntas ahorran semanas de diseño hecho "de memoria" y reducen el riesgo de repetir errores que alguien más ya cometió y resolvió.

## Análisis de la competencia

Es el proceso de **evaluar cómo otros resuelven problemas similares** al tuyo para aprender de sus soluciones — y de sus errores. No se trata de copiar: se trata de entender patrones que los usuarios ya conocen, convenciones que esperan, y puntos donde otros fallaron y tú puedes mejorar.

### Qué mirar

- **Flujos equivalentes**: ¿cómo resuelve el competidor el mismo paso que estoy diseñando?
- **Convenciones de interfaz**: ¿usan un patrón estándar o uno propio?
- **Contenido y lenguaje**: ¿cómo nombran las cosas?, ¿qué términos evitan?
- **Puntos de fricción**: ¿dónde abandona el usuario su producto?

### ¿Y si mi producto es muy de nicho?

No importa. La "competencia" no es solo el producto que ataca el mismo mercado: es **cualquier sistema comparable que resuelva un problema similar**. Para una app de gestión de salones, los referentes útiles incluyen:

- Competidores directos de nicho (Fresha, Booksy, Treatwell).
- SaaS generalistas con flujos equivalentes (Square Appointments, Calendly para la agenda; Stripe/Square para cobros).
- Apps contiguas que tus usuarias ya usan a diario (WhatsApp Business para recordatorios, Instagram Business para catálogo).

Evaluar cómo estas apps manejan la agenda, el cobro o el recordatorio a clientas —qué pasos redujeron, qué patrones estandarizaron, qué ayudas ofrecen— es benchmark público y barato.

### Cómo documentarlo

Una matriz simple suele bastar:

| Competidor | Patrón observado | Fricciones | Qué adoptar | Qué evitar |
|------------|------------------|------------|-------------|------------|
| Fresha | Cobro con tarjeta en 2 taps desde la ficha de la cita | Exige tarjeta guardada | Flujo de 2 taps | Forzar tarjeta |
| Booksy | Recordatorio por WhatsApp con confirmación de un clic | Plantilla de texto rígida | Confirmación 1-clic | Texto no editable |

La matriz obliga a ser específico: no vale *"tienen una experiencia bonita"*, debes nombrar el patrón.

## Análisis de usuarios

Consiste en **entender quiénes son tus usuarios**, qué necesitan, qué valoran, en qué son competentes y dónde tienen dificultades. Es la base sobre la que luego se construyen las *personas* (módulo 3.1.7) y las pruebas de usabilidad.

### Segmentación

No todas las usuarias son iguales. Identificar los **segmentos** relevantes —categorías con necesidades y comportamientos distintos— es el primer paso. En una app de gestión para pymes de servicios, por ejemplo:

| Segmento | Volumen | Nivel digital | Necesidad dominante |
|----------|--------:|---------------|---------------------|
| Dueña unipersonal (hace todo sola) | Muy alto | Medio | Simplicidad + pocos taps |
| Salón pequeño (2–3 estilistas) | Alto | Medio | Roles, agenda compartida |
| Cadena pequeña (2–5 locales) | Medio | Alto | Reportes, dashboard multi-local |
| Recepcionista / asistente | Alto | Medio-alto | Eficiencia en agenda y cobro |

Cada segmento justifica decisiones de diseño distintas: el flujo para una dueña unipersonal debe ser directo y guiado; el de una cadena debe permitir vista consolidada y permisos por rol.

### Dimensiones a investigar

Por cada segmento relevante, responde con datos:

- **Demografía y contexto**: edad, ubicación, tamaño del negocio, sector.
- **Competencia digital**: qué dispositivos usa, qué tan cómodo se siente con formularios en línea.
- **Motivaciones**: qué está tratando de lograr cuando entra al producto.
- **Barreras**: qué le impide avanzar hoy (lenguaje, tiempo, conocimiento, herramientas).
- **Canales que prefiere**: portal, app, agencia presencial, call center, contador.

### Cómo obtener estos datos

Combina al menos dos fuentes:

1. **Datos internos**: analytics del producto, tickets de soporte, llamadas al call center, abandono en formularios.
2. **Investigación directa**: entrevistas y encuestas (ver [3.1.5 UX Research](./05-ux-research.md)).
3. **Datos oficiales**: estadísticas públicas, censos, estudios sectoriales.

Los datos internos dicen *qué* pasa; la investigación directa dice *por qué*. Necesitas ambos.

## Integrar los dos análisis

La fuerza del método está en **cruzar** lo que aprendiste de los usuarios con lo que viste en la competencia:

- *Usuarias nos dicen que no entienden el término "servicio gravado"* + *competidor usa "servicio cobrado" con éxito* → adoptar "servicio cobrado".
- *Analytics muestra abandono en el paso 4 de 7 del cobro* + *competidor resolvió el mismo paso con datos pre-llenados desde la cita* → probar pre-llenado en un piloto.

Sin el cruce, tienes dos informes aislados. Con el cruce, tienes hipótesis de diseño priorizables.

:::tip Cruzar siempre antes de decidir
Un hallazgo del análisis de usuarios sin validación con la competencia puede llevarte a reinventar la rueda. Un hallazgo de la competencia sin validación con tus usuarios puede llevarte a copiar algo que a tu audiencia no le sirve. La fuerza está en el cruce.
:::

## Puente al siguiente módulo

Tenemos segmentos caracterizados y patrones observados en la competencia. Pero una tabla de segmentos no ayuda cuando un diseñador está decidiendo dónde va un botón. Para eso necesitamos convertir esos segmentos en **personas** que el equipo pueda citar en cada decisión, y en **escenarios** concretos donde esa persona aparece en acción. Ese es el [módulo 3.1.7](./07-personas-y-escenarios.md).

## Glosario

**Análisis de competencia** *(Competitive analysis)* — evaluación sistemática de productos comparables para identificar patrones y oportunidades.

**Segmentación de usuarios** *(User segmentation)* — división de la base de usuarios en grupos con necesidades y comportamientos similares.

**Benchmark** — referencia externa contra la cual comparar tu producto en una dimensión específica (tiempo de tarea, tasa de error, satisfacción).

**Competencia indirecta** — producto que no resuelve el mismo problema pero usa patrones análogos que los usuarios ya conocen.

:::info Referencias primarias
- [Nielsen Norman Group · Competitive Usability Evaluations](https://www.nngroup.com/articles/competitive-usability-evaluations/) — método formal para benchmarking UX.
- [Just Enough Research (Erika Hall)](https://abookapart.com/products/just-enough-research) — cómo hacer investigación ligera pero útil.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** producir una visión compartida de usuarios y competidores que sirva de base para decisiones de diseño priorizadas.

**Entradas:**
- Producto o flujo bajo análisis.
- Acceso a datos internos (analytics, soporte).
- Lista de competidores directos e indirectos relevantes.
- Muestra mínima de usuarios representativos por segmento.

**Pasos:**
1. Identificar segmentos de usuario relevantes y priorizarlos por volumen/impacto.
2. Caracterizar cada segmento con datos (dimensiones demográficas, contexto, barreras).
3. Listar 3–5 competidores (directos + indirectos) y ubicar el flujo equivalente en cada uno.
4. Documentar patrones, fricciones, ideas a adoptar y anti-patrones por competidor.
5. Cruzar hallazgos de usuarios con observaciones de competencia.
6. Priorizar 3–5 hipótesis de diseño con evidencia.

**Salidas:**
- Tabla de segmentos con dimensiones caracterizadas.
- Matriz de competencia con patrones y fricciones.
- Lista priorizada de hipótesis de diseño con referencias a la evidencia.

**Errores comunes:**
- Tratar a todos los usuarios como un bloque homogéneo.
- Copiar patrones del competidor sin validarlos con los propios usuarios.
- Hacer análisis descriptivo sin extraer hipótesis accionables.
- Limitar competencia solo a productos directos e ignorar patrones análogos.
- Omitir datos internos y construir el análisis solo sobre impresiones.

**Referencias cruzadas:**
- [3.1.5 UX Research](./05-ux-research.md)
- [3.1.7 Personas y escenarios](./07-personas-y-escenarios.md)
- [3.1.8 Arquitectura de información](./08-arquitectura-de-informacion.md)
</div>
