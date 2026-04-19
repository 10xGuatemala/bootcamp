---
sidebar_position: 9
sidebar_label: 3.1.9 Pruebas de usabilidad
---

# Pruebas de usabilidad

Tenemos personas, escenarios y una arquitectura de información. Todo en papel parece coherente. La pregunta incómoda es: **¿funciona cuando una Rosa real intenta usarlo?** Esa pregunta solo se responde observando, no debatiendo.

Las **pruebas de usabilidad** son el método más rentable de toda la caja de herramientas de UX: con pocos participantes y pocas horas detectan la mayoría de los problemas reales que los usuarios tendrán con el producto. El método es simple — **pedirle a un usuario que intente completar una tarea mientras lo observas** — pero su impacto es desproporcionado.

Según [Nielsen Norman Group](https://www.nngroup.com/articles/why-you-only-need-to-test-with-5-users/), **observando a solo 5 usuarios puedes identificar aproximadamente el 85 % de los problemas de usabilidad** de una interfaz. Hacer más pruebas con más usuarios en la misma iteración produce rendimientos decrecientes; es mejor iterar y volver a probar.

:::tip La regla de los 5 usuarios
Dos rondas de 5 usuarios valen más que una ronda de 10. La razón: entre una ronda y la siguiente cambias el diseño, así que los 5 usuarios de la segunda ronda te revelan problemas nuevos que los primeros no podrían haber visto.
:::

## El principio del método

Una prueba de usabilidad no pregunta *"¿te gusta esta pantalla?"*. Pregunta *"¿puedes lograr esto?"* y observa. La diferencia es crítica:

- *"¿Te gusta el diseño?"* → produce opinión, sesgada por cortesía.
- *"Intenta registrar el cobro de esta clienta y agendar su próxima cita"* → produce comportamiento observable.

Lo que el usuario **hace** importa más que lo que **dice**. El usuario puede decir *"sí, está claro"* mientras tardó tres minutos en encontrar el botón. Esa fricción es la que el método captura.

## Elementos de una prueba

| Elemento | Descripción |
|----------|-------------|
| **Participante** | Usuario representativo de un segmento relevante |
| **Tarea** | Acción concreta que debe completar, con criterio de éxito observable |
| **Prototipo o producto** | Lo que va a usar (puede ser papel, Figma interactivo, o la app real) |
| **Facilitador** | Quien guía la sesión sin intervenir |
| **Observadores** | Equipo que toma notas sin interrumpir |
| **Protocolo** | Guion con la secuencia, preguntas de inicio y de cierre |

### La tarea bien formulada

Una mala tarea guía al usuario hacia la respuesta correcta. Una buena tarea describe el objetivo sin prescribir la ruta:

- Mala: *"Haz clic en 'Cobros' y luego en 'Nuevo cobro'"*.
- Buena: *"Acabas de terminar un servicio de corte con Marta. Te va a pagar con tarjeta y quiere agendar la próxima cita. Muéstrame cómo lo harías."*

La segunda obliga al usuario a tomar decisiones; cada decisión es una señal de si la IA y las etiquetas funcionan.

## Tipos de prueba

### Moderada vs. no moderada

- **Moderada**: un facilitador guía la sesión en vivo (presencial o remota). Permite profundizar con preguntas; es cara.
- **No moderada**: el usuario completa las tareas solo con una herramienta (UserTesting, Lookback, Maze). Es más barata y escalable; pierde el seguimiento conversacional.

### Cualitativa vs. cuantitativa

- **Cualitativa** (5–8 usuarios): detecta qué falla y por qué. Es el uso principal.
- **Cuantitativa** (30+ usuarios): mide métricas agregadas (tasa de éxito, tiempo de tarea). Útil solo cuando necesitas comparar dos diseños con rigor estadístico.

### Por fidelidad del prototipo

- **Papel o mockups estáticos**: útiles en fase temprana; revelan problemas de IA y etiquetado.
- **Prototipos interactivos** (Figma): revelan problemas de flujo y micro-interacciones.
- **Producto real**: revela problemas de rendimiento, estados de error y datos reales.

## Protocolo recomendado

1. **Bienvenida y consentimiento** (2 min): explica el propósito, aclara que estás probando el sistema, no al usuario.
2. **Warm-up** (3 min): preguntas de contexto sobre el usuario (rol, herramientas habituales).
3. **Tareas** (20–30 min): 3–5 tareas, cada una con criterio de éxito observable.
4. **Think aloud**: pide al usuario que piense en voz alta mientras intenta la tarea. Sin este hábito, solo ves lo que hace; con él, ves por qué.
5. **Preguntas de cierre** (5 min): *"¿Qué te costó más?, ¿qué te resultó fácil?, ¿qué cambiarías?"*.
6. **Agradecimiento y compensación**.

Duración total por sesión: ~45 minutos. Más allá de eso, el participante se cansa y la calidad de los datos cae.

## Qué observar

Tanto si la prueba es presencial como grabada, documenta:

- **Éxito/fracaso** de cada tarea (con criterio previamente definido).
- **Tiempo** hasta completar.
- **Puntos de fricción**: dudas, retrocesos, búsquedas, errores.
- **Citas literales** del usuario durante el think-aloud.
- **Emociones visibles**: frustración, confusión, satisfacción.
- **Rutas tomadas**: si abrió el menú A cuando esperabas que fuera al menú B.

## Métricas útiles

Cuando necesitas cuantificar resultados:

- **Tasa de éxito de tarea** (*task success rate*): % de usuarios que completaron la tarea sin ayuda.
- **Tiempo de tarea** (*time on task*): mediana y distribución.
- **Errores por tarea**: cantidad de acciones incorrectas antes de completar.
- **SUS (System Usability Scale)**: cuestionario de 10 preguntas que produce un score 0–100. Útil para comparar versiones a lo largo del tiempo.

## Análisis y acción

Después de las sesiones:

1. Agrupa los problemas observados por patrón (no por usuario).
2. Cuantifica: *"4 de 5 usuarios no encontraron X"* es más accionable que *"varios no encontraron X"*.
3. Prioriza por severidad (¿bloquea la tarea?) × frecuencia (¿cuántos usuarios lo toparon?).
4. Convierte los 3–5 problemas más graves en tickets de diseño con evidencia.
5. Itera el diseño, vuelve a probar. **Dos rondas de 5 usuarios valen más que una ronda de 10.**

## Cuándo no hacer prueba de usabilidad

- Cuando ya tienes datos cuantitativos abundantes (analytics claros) que responden la pregunta.
- Cuando el problema es de arquitectura de información más profunda — aplica primero card sorting y tree testing (ver 3.1.8).
- Cuando solo buscas validar una opinión del equipo: invita a usuarios reales, no simulaciones del equipo mismo.

## Puente al siguiente módulo

Hasta aquí, los módulos 05–09 cubren **el ciclo de UX**: investigar, segmentar, modelar, estructurar y validar. A partir del módulo 10 cambiamos de lente: pasamos del *proceso* al *aspecto visual concreto* de la interfaz. El color, la tipografía, la jerarquía visual y la accesibilidad son las palancas que traducen una pantalla correcta en una pantalla que además **se ve y se usa bien**. Empezamos por el color en el [módulo 3.1.10](./10-psicologia-del-color.md).

## Glosario

**Prueba de usabilidad** *(Usability test)* — sesión en la que un usuario intenta completar tareas mientras un equipo observa.

**Think aloud** — técnica donde el usuario verbaliza su razonamiento durante la tarea.

**Tasa de éxito** *(Task success rate)* — proporción de usuarios que completan una tarea sin ayuda.

**SUS** *(System Usability Scale)* — cuestionario estandarizado de 10 ítems que produce un índice de usabilidad percibida.

**Severidad** — gravedad de un problema detectado; suele clasificarse como bloqueante, mayor, menor, cosmético.

:::info Referencias primarias
- [Nielsen Norman Group · Why You Only Need to Test with 5 Users](https://www.nngroup.com/articles/why-you-only-need-to-test-with-5-users/) — justificación estadística del método.
- [Handbook of Usability Testing (Jeffrey Rubin)](https://www.wiley.com/en-us/Handbook+of+Usability+Testing) — protocolo clásico.
- [Measuring the User Experience (Tullis & Albert)](https://www.sciencedirect.com/book/9780128180808/measuring-the-user-experience) — métricas cuantitativas.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** detectar problemas reales de usabilidad con bajo costo y convertirlos en decisiones de diseño accionables.

**Entradas:**
- Prototipo o producto a evaluar.
- Perfiles objetivo para reclutar participantes (3–8 por iteración).
- Tareas críticas del producto.
- Disponibilidad de sala, herramientas de grabación o plataforma remota.

**Pasos:**
1. Formular 3–5 tareas representativas, cada una con criterio de éxito observable.
2. Reclutar 5 usuarios representativos del segmento prioritario.
3. Preparar protocolo (warm-up, tareas, cierre) y consentimiento.
4. Ejecutar sesiones moderadas con think-aloud, tomar notas y grabar con permiso.
5. Consolidar problemas por patrón; cuantificar (cuántos usuarios, cuántas tareas).
6. Priorizar por severidad × frecuencia y generar tickets con evidencia.
7. Iterar el diseño y volver a probar.

**Salidas:**
- Reporte con problemas priorizados, evidencia (citas, clips) y métricas.
- Lista de tickets de diseño con criterios de aceptación.
- Versión actualizada del prototipo o producto.

**Errores comunes:**
- Formular tareas que revelan la respuesta correcta al usuario.
- Intervenir como facilitador cuando el usuario se traba (contamina el dato).
- Confundir opiniones ("me gusta") con datos de comportamiento.
- Reclutar al equipo interno como participantes.
- No priorizar los hallazgos y quedarse con un documento descriptivo sin acción.

**Referencias cruzadas:**
- [3.1.5 UX Research](./05-ux-research.md)
- [3.1.7 Personas y escenarios](./07-personas-y-escenarios.md)
- [3.1.8 Arquitectura de información](./08-arquitectura-de-informacion.md)
- [3.1.13 Accesibilidad básica](./13-accesibilidad-basica.md)
</div>
