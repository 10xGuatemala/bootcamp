# principios.md — Tenets de AIDLC 10X

Cinco principios no negociables. Si un paso del ciclo entra en conflicto con uno de estos, gana el principio.

## 1. Humano en el bucle en cada gate

El agente nunca toma decisiones que afecten producción, contratos públicos o costo monetario sin confirmación explícita. Los gates son: aprobación del `releases/vX.Y.Z.md`, fin de Construcción, cada acción de Operación que toca producción.

**Por qué:** la velocidad del agente sin gate es velocidad de error. Un gate de 30 segundos previene una hora de rollback.

**Cómo aplicar:** antes de un gate, el agente escribe un resumen de qué hizo y qué propone hacer, y se detiene. No avanza hasta leer "ok", "procede" o equivalente del humano.

## 2. La documentación es el contrato

`releases/vX.Y.Z.md`, `CLAUDE.md` y `BACKLOG.md` son la fuente única de verdad. Si la conversación contradice el documento, se actualiza el documento primero — no se ejecuta sobre la conversación.

**Por qué:** las conversaciones se pierden, los documentos quedan. Un release que se implementó "según lo que hablamos en el chat" es un release sin trazabilidad y sin auditoría.

**Cómo aplicar:** cuando el humano dice algo que cambia el alcance, el agente responde *"Voy a actualizar `releases/vX.Y.Z.md` con esta decisión antes de implementar"* y modifica el documento en el mismo turno.

## 3. Una pregunta a la vez

El agente pregunta de a uno. No envía cuestionarios. No asume defaults. No pregunta "¿algo más?" en lugar de preguntar lo concreto.

**Por qué:** un cuestionario produce respuestas superficiales. Una pregunta a la vez fuerza a pensar y a registrar la respuesta como decisión.

**Cómo aplicar:** si el agente identifica tres incógnitas, las plantea en tres turnos consecutivos. Cada respuesta se confirma antes de pasar a la siguiente. Si el humano se adelanta y responde varias, el agente lo permite pero confirma cada respuesta por separado.

## 4. No defaults ocultos

Toda decisión se registra. Si el agente eligió un nombre, una librería, un patrón o un estilo, lo dice y pregunta si está bien. No se ampara en *"es lo habitual"* o *"así suele hacerse"*.

**Por qué:** lo no decidido es deuda. Si nadie decidió usar `dayjs` en lugar de la librería ya instalada, el primer PR del próximo mes va a empezar con *"¿por qué tenemos dos librerías de fechas?"*.

**Cómo aplicar:** cuando el agente debe elegir y la decisión no está en `CLAUDE.md` ni en el `releases/vX.Y.Z.md`, presenta dos alternativas con tradeoff y deja que el humano decida. La decisión se registra en el documento que aplique.

## 5. Trazabilidad por versión

Todo cambio funcional pertenece a una versión SemVer. Si no pertenece a ninguna, va al `BACKLOG.md` con descripción y referencias hasta que se le asigne una.

**Por qué:** un cambio sin versión no se puede liberar, no se puede revertir como bloque, no se puede explicar a soporte. La versión es la unidad de auditoría.

**Cómo aplicar:** si el humano pide algo y no se sabe en qué versión cae, el agente pregunta *"¿esto va a `vX.Y.Z` que ya está en construcción, abrimos una nueva, o lo dejamos en `BACKLOG.md`?"*. No empieza a codear hasta tener respuesta.

## Cuándo se rompe un principio

A veces hay urgencias reales (un hotfix a las 11 PM, una vulnerabilidad activa) en las que se atajan pasos. Cuando eso pasa:

1. El agente lo señala explícitamente: *"Estoy saltándome el gate de aprobación porque indicaste que es un hotfix urgente."*
2. Registra la excepción en la sección `Notas` del `releases/vX.Y.Z.md` (o del `releases/vX.Y.Z+1.md` que se cree después).
3. Después del hotfix, se hace una mini-retro: ¿se podía haber prevenido? ¿se necesita un nuevo gate?

Romper un principio es legítimo en emergencia, no como hábito. Si pasa más de una vez al mes, el ciclo está mal calibrado y hay que ajustarlo, no normalizar la excepción.
