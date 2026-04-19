---
sidebar_position: 7
sidebar_label: 3.1.7 Personas y escenarios
---

# Personas y escenarios

Del módulo anterior salimos con segmentos caracterizados y patrones de fricción. El problema es que **"segmento A: dueñas unipersonales de pymes de servicios, edad media 40 años, competencia digital media"** es una descripción útil para un reporte pero inútil cuando alguien del equipo está decidiendo el texto de un botón. El equipo necesita **a Rosa**, no un segmento.

Las **personas** y los **escenarios** son dos herramientas complementarias que convierten los datos brutos de investigación en modelos que el equipo puede usar todos los días. Una persona condensa en un arquetipo —con nombre, foto, contexto y objetivos— a un segmento de usuarios reales. Un escenario narra cómo esa persona atraviesa una situación concreta usando el producto.

Son útiles porque el cerebro razona mejor sobre *"María, contadora externa que atiende 40 pymes desde casa"* que sobre *"segmento B: profesionales externos, 30–50 años, densidad de uso alta"*. El riesgo contrario también existe: una persona inventada sin datos de respaldo es peor que no tener persona, porque le da forma humana a nuestros sesgos.

## Personas: arquetipos basados en datos

Una persona es una **representación detallada de un tipo de usuario**, construida a partir de patrones observados en investigación real (entrevistas, analytics, tickets). No es un usuario promedio ni un usuario ideal: es un arquetipo concreto que representa las necesidades, competencias y fricciones de un segmento.

### Qué contiene una persona útil

Una persona mal hecha es decorativa (foto, nombre, frase motivacional, cero datos). Una persona útil contiene, como mínimo:

| Elemento | Ejemplo |
|----------|---------|
| **Nombre y foto** | Ayudan a humanizar, pero son lo menos importante |
| **Segmento al que representa** | "Dueña unipersonal de salón — 45% de la base activa" |
| **Contexto de uso** | Dónde, cuándo, con qué dispositivo |
| **Objetivos** | Qué intenta lograr con el producto (cobrar rápido, no perder citas) |
| **Comportamientos observados** | Basados en datos reales de investigación |
| **Frustraciones** | Dónde falla hoy el sistema para este usuario |
| **Competencias digitales** | Bajo, medio, alto — con ejemplos concretos |
| **Citas** | Fragmentos literales de entrevistas que ilustran su perspectiva |

**Regla no negociable**: cada afirmación en una persona debe poder vincularse a un dato real (una entrevista, una cifra de analytics, un patrón en soporte). Si no, es ficción.

### Ejemplo: persona de una app de gestión para salones

```markdown
## Persona: Rosa (Dueña unipersonal de salón)

**Segmento:** dueña de salón de 1–3 sillas — 45 % de la base activa.
**Edad:** 41 años.
**Contexto:** dueña de un salón de belleza. Atiende personalmente a sus
clientas mientras cobra, agenda la próxima cita y registra el stock. Usa la
app desde el teléfono entre cita y cita, y desde una tablet en recepción.

**Objetivos:**
- Cobrar en menos de 30 segundos sin que la clienta espere.
- Que la próxima cita quede agendada antes de que la clienta salga del salón.
- Saber cada lunes cuánto vendió la semana anterior y qué productos bajaron
  de stock.

**Competencia digital:** media. Maneja Instagram Business, WhatsApp y la app
bancaria sin problemas; abandona flujos con más de 5 pasos o con formularios
llenos de campos opcionales.

**Frustraciones:**
- Cuando una clienta llega antes de tiempo, la interrumpe a media pantalla y
  la sesión se cae sin guardar.
- No distingue en los reportes "venta" de "ingreso cobrado"; duda al leerlos.
- Bajo las luces fluorescentes del salón, los avisos de color tenue se
  pierden en el reflejo de la pantalla.
- Las notificaciones de citas confirmadas, cobros y stock bajo aparecen todas
  iguales; no sabe cuál requiere acción ya.

**Cita (entrevista E12):** "Si tengo que escoger entre atender bien a la
clienta o meterle datos a la app, gana la clienta. La app tiene que seguirme
a mí, no al revés."

**Implicaciones de diseño:**
- Acción primaria (cobrar / agendar) a un solo tap desde la ficha de la cita.
- Autoguardado agresivo y recuperación transparente de sesión.
- Contraste AA+ en avisos, probado bajo luz fuerte.
- Notificaciones priorizadas por acción requerida, no cronológicas.
```

Observa que no hay frase motivacional ni foto inventada: hay segmento con volumen, comportamiento verificable, citas y derivaciones de diseño concretas.

### Cuántas personas tener

- **1–2**: probablemente insuficientes; estás generalizando demasiado.
- **3–5**: rango típico para productos con audiencia segmentada.
- **Más de 7**: probablemente estás creando personas demasiado finas que nadie usa.

Cada persona debe representar un segmento con volumen y necesidades distintas. No crees una por cada variación menor.

## Escenarios: la persona en movimiento

Un **escenario** es una narración breve que describe **cómo una persona específica atraviesa una situación concreta** usando el producto. Convierte a la persona en un actor con una meta, un contexto y un resultado.

### Estructura de un escenario

```markdown
**Contexto:** sábado 11:50 am. Rosa acaba de terminar un corte. La clienta
está en la caja lista para pagar; detrás de ella ya llegó la siguiente cita.

**Disparador:** Rosa abre la ficha de la cita actual para cerrar el cobro.

**Objetivo de Rosa:** cobrar, agendar la próxima cita y liberar la silla en
menos de 90 segundos.

**Acciones esperadas:**
1. Toca "Cerrar cita" desde la ficha abierta.
2. Confirma el servicio y el monto ya cargados.
3. Elige método de pago (efectivo / tarjeta / transferencia).
4. Ofrece la próxima cita sugerida (misma clienta, +4 semanas).
5. La clienta confirma y Rosa marca la silla como libre.

**Fricciones probables:**
- La clienta pide propina incluida; el flujo no la contempla con un tap.
- La siguiente clienta le habla y Rosa pierde el foco del formulario.
- La transferencia llega tarde y Rosa no sabe si marcarla como "cobrada".

**Éxito observable:** cobro registrado, próxima cita agendada y silla liberada
en < 90 segundos, sin que ninguna clienta espere más de 30 segundos.
```

### Tipos de escenarios

- **Escenarios de éxito** (*happy path*): cómo debería fluir todo cuando no hay fricciones.
- **Escenarios de error**: qué pasa cuando algo falla (conexión, dato inválido, timeout).
- **Escenarios de borde**: situaciones poco frecuentes pero críticas (primer uso, cambio de dispositivo, recuperación de cuenta).

Un flujo diseñado solo para el *happy path* falla en producción. Los escenarios de error y de borde son tan importantes como el feliz.

## Cómo se usan en el día a día

- **En reuniones de priorización**: *"¿Esto le sirve a Rosa o solo a Contador Experto?"*
- **En revisiones de diseño**: *"¿Cómo se comporta esta pantalla en el escenario de conexión intermitente?"*
- **En especificaciones**: los escenarios generan criterios de aceptación concretos.
- **Con agentes de IA**: darle al agente la persona y el escenario como contexto produce soluciones mucho más alineadas que una descripción abstracta.

Una persona que solo vive en un PDF al fondo de un Drive no sirve. Debe estar visible, citable y actualizarse cuando la investigación dice lo contrario.

:::tip Rosa como ejemplo recurrente
Usaremos a Rosa en los módulos siguientes para ilustrar decisiones concretas: qué pantalla diseñarle, cómo probar un prototipo con ella, qué color de alerta percibirá bajo las luces del salón, y por qué un contraste insuficiente la excluye. Mantén a tus personas así de vivas en tu propio equipo.
:::

## Errores frecuentes

- **Persona inventada** sin datos: le da forma humana a los supuestos del equipo.
- **Persona demográfica vacía**: "mujer, 35 años, profesional" no dice nada sobre cómo diseñar.
- **Personas que no se actualizan**: la base de usuarios cambia; las personas deben revisarse.
- **Escenarios solo de éxito**: dejan fuera el 30–40 % de casos reales.
- **Tratar la persona como estadística**: es un arquetipo ilustrativo, no un promedio matemático.

## Puente al siguiente módulo

Con Rosa y sus escenarios podemos imaginar cómo navegaría el portal. Pero antes de pedirle eso al diseñador visual, hay una pregunta estructural: *¿cómo está organizada la información que Rosa necesita encontrar?* Una interfaz bonita sobre una arquitectura mal pensada es una trampa elegante. Eso resuelve el [módulo 3.1.8](./08-arquitectura-de-informacion.md).

## Glosario

**Persona** *(User persona)* — arquetipo de usuario basado en investigación, representativo de un segmento.

**Escenario** *(Scenario)* — narración breve de cómo una persona atraviesa una situación concreta usando el producto.

**Happy path** — secuencia de acciones asumiendo que nada falla; es solo una parte del diseño.

**User journey** *(Recorrido del usuario)* — vista más amplia que encadena múltiples escenarios a lo largo del tiempo.

:::info Referencias primarias
- [Nielsen Norman Group · Personas: Study Guide](https://www.nngroup.com/articles/persona/) — cómo construir y usar personas.
- [About Face (Alan Cooper)](https://www.cooper.com/journal/) — origen del método de personas.
- [User Story Mapping (Jeff Patton)](https://jpattonassociates.com/user-story-mapping/) — cómo encadenar escenarios en un flujo coherente.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** transformar hallazgos de investigación en personas y escenarios usables por todo el equipo para alinear decisiones de diseño.

**Entradas:**
- Resultados de investigación (entrevistas, analytics, tickets).
- Segmentación de usuarios ya validada.
- Flujos o funcionalidades pendientes de diseñar.

**Pasos:**
1. Identificar 3–5 segmentos relevantes y redactar una persona por cada uno.
2. Validar que cada afirmación en la persona esté soportada por datos reales.
3. Por cada flujo crítico, redactar al menos un escenario de éxito, uno de error y uno de borde.
4. Extraer de los escenarios criterios de aceptación verificables.
5. Publicar personas y escenarios en ubicación visible para el equipo.
6. Revisar y actualizar cada 6–12 meses con nueva investigación.

**Salidas:**
- Fichas de persona con segmento, contexto, objetivos, frustraciones y citas.
- Set de escenarios por flujo (éxito + error + borde).
- Criterios de aceptación derivados de los escenarios.

**Errores comunes:**
- Inventar personas sin datos o decorarlas sin información accionable.
- Confundir persona con usuario promedio.
- Diseñar solo para el happy path y descubrir errores en producción.
- Crear personas que nadie cita en decisiones; pasan a ser artefacto decorativo.
- No revisar personas tras cambios en la base de usuarios.

**Referencias cruzadas:**
- [3.1.5 UX Research](./05-ux-research.md)
- [3.1.6 Análisis de usuarios y competencia](./06-analisis-usuarios-y-competencia.md)
- [3.1.9 Pruebas de usabilidad](./09-pruebas-de-usabilidad.md)
</div>
