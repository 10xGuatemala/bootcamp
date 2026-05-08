---
sidebar_position: 9
sidebar_label: 6.9 Fase 1 · Concepción del release
---

# Fase 1: Concepción del release

El módulo anterior introduce las tres fases del ciclo. Esta es la primera y la que más decide el resto: si la Concepción se hace mal, ninguna disciplina en Construcción la salva. La trampa más común es empezar a codear cuando todavía hay decisiones abiertas, amparados en *"ya las cerramos en el camino"*. Las decisiones tomadas en el camino se llaman defaults ocultos.

Esta fase produce un único artefacto: `releases/vX.Y.Z.md` aprobado como contrato de implementación.

## ¿Qué es la Concepción?

La fase del ciclo donde el equipo decide **qué entra a una versión y con qué nivel de detalle**, hasta el punto en que la implementación pueda ejecutarse sin necesidad de la conversación que la originó. Termina cuando el `releases/vX.Y.Z.md` está aprobado por el humano como contrato.

La Concepción no resuelve cómo se construye — eso es la siguiente fase. Solo decide qué, por qué, en qué versión, y con qué nivel de detalle. Si la fase termina con preguntas abiertas, la fase no terminó.

## Por qué la Concepción importa

- **El costo de una decisión cambia tres órdenes de magnitud según cuándo se tome.** Cambiar un campo de un endpoint en Concepción cuesta editar una línea del `release.md`; en Construcción cuesta refactorizar; en Operación puede costar una migración con downtime.
- **Una descripción ambigua se interpreta de N maneras.** Si el item dice *"agregar opción de PDF sin impuestos"*, hay tres lugares posibles donde colocar el toggle, cuatro nombres posibles para la preferencia, y dos formas de persistirlo. La descripción tiene que cerrar las cuatro decisiones, no abrirlas.
- **Sin contrato no hay auditoría.** Si dentro de seis meses alguien pregunta *"¿por qué pusimos el toggle en localStorage y no en backend?"*, la respuesta tiene que estar escrita. La conversación se evaporó.
- **Una versión cerrada protege al equipo del scope creep.** Una vez aprobado el `release.md`, lo que no está adentro no se construye. Si surge algo nuevo, va al backlog o abre una versión siguiente — no se cuela.
- **El agente trabaja mejor con contrato que con intuición.** Una sesión de implementación con `release.md` claro se completa en una pasada; sin él, oscila entre revisiones y consultas.

## Objetivo

Producir `releases/vX.Y.Z.md` aprobado por el humano como contrato de implementación, con todos los requerimientos cerrados, criterios de aceptación verificables, y migraciones BD nombradas.

## Entradas

- `BACKLOG.md` con items pendientes (puede estar vacío si es una idea fresca).
- Conversación con el humano sobre qué se quiere lograr en la versión.
- Decisión de tipo SemVer: `MAJOR`, `MINOR` o `PATCH`.
- `aidlc/_release-template.md` instalado (disponible en [el paquete `aidlc-10x/plantillas/`](https://github.com/10xGuatemala/bootcamp/blob/main/examples-md/aidlc-10x/plantillas/_release-template.md)).
- Compromiso del humano de no autorizar el gate hasta leer el archivo.

## Pasos para conducir la Concepción

### Paso 1: Capturar la idea en el `BACKLOG.md`, no en el chat

Cuando llega una idea — de un usuario, soporte, una conversación de pasillo — el primer movimiento del agente no es codear. Es agregar una línea al `BACKLOG.md` con módulo afectado, una línea de descripción y referencias.

- Mal: *"Sí, eso lo hago en la próxima."* Sin captura, la idea se va a perder o se va a recordar de tres maneras distintas.
- Bien: *"Lo agrego al `BACKLOG.md` con esta línea. Cuando planeemos la próxima versión, lo evaluamos en triage."*

**Valor para el agente:** el backlog es la pila estable. Las conversaciones cambian; el archivo queda. Cuando llega la hora de planear la versión, el agente no depende de su memoria — lee el archivo.

### Paso 2: Triage y asignación de versión

Para abrir la versión nueva, el agente y el humano revisan el `BACKLOG.md` y deciden tres cosas:

1. **Qué entra.** Lista cerrada. Lo que no entra ahora va a una versión futura o se queda en backlog.
2. **Qué tipo de versión SemVer.** El criterio es operacional, no estético: ¿hay breaking change en API o esquema BD? `MAJOR`. ¿Hay feature visible al usuario o nuevo endpoint sin breaking? `MINOR`. ¿Es bugfix o refactor sin cambio funcional? `PATCH`.
3. **Qué repos toca.** Cada item declara `API`, `Web`, `API+Web`, `Docs`, etc. Esa decisión condiciona los bumps de versión y los CHANGELOG.

El agente nunca decide la versión. Pregunta y registra. Si el humano duda entre `MINOR` y `PATCH`, el agente expone el criterio:

```text
- ¿Aparece algo nuevo visible para el usuario? (botón, columna, opción)
- ¿Cambia el contrato del API (nuevo endpoint, nuevo campo)?
- Si la respuesta a alguna es Sí, es MINOR; si todas son No, es PATCH.
```

### Paso 3: Redactar `releases/vX.Y.Z.md` un requerimiento a la vez

A partir del [`_release-template.md`](https://github.com/10xGuatemala/bootcamp/blob/main/examples-md/aidlc-10x/plantillas/_release-template.md), el agente crea el archivo y va llenando los requerimientos uno por uno. Cada requerimiento queda con: tipo, repos, migración BD, estado, descripción y criterio de aceptación.

La prueba de la descripción es:

> *Si separás este requerimiento del resto del archivo y se lo das a otro agente que nunca participó en la conversación, ¿puede implementarlo?*

Si la respuesta es no, la descripción todavía depende del chat y hay que reescribirla.

- Mal: *"Como dijimos antes, hay que renombrar el archivo de preferencias y agregar el toggle nuevo."* — depende de "antes".
- Bien: *"Renombrar `lib/preferenciasSello.ts` → `lib/preferenciasPdfCotizacion.ts` migrando interfaz y helpers. Agregar campo `mostrarColumnaImpuesto: true` por default. Mismo `STORAGE_KEY` para no perder preferencias previas."* — autocontenido.

**Valor para el agente:** una descripción autocontenida le permite implementar el item en una sesión nueva, sin contexto previo. Es la garantía de que el contrato sobrevive al cambio de personas, herramientas o tiempo.

### Paso 4: Nombrar las migraciones BD desde ahora

Si algún requerimiento toca BD, en este paso se nombran los scripts SQL que vendrán:

```text
Database/migrations/v1.16.0/
  001-agregar-columna-empresas-zona-horaria.sql
  002-backfill-zona-horaria-default.sql
  003-not-null-zona-horaria.sql
```

El nombre y el orden se deciden ahora, no en construcción. Esto evita que dos requerimientos peleen por el mismo `001-`. El contenido SQL se escribe en la siguiente fase.

- Mal: *"Eso lo veo cuando esté codeando."* Tres requerimientos quieren ser el `001-`.
- Bien: *"Los listo en el `release.md` con nombres explícitos y orden, aunque el SQL todavía esté vacío."*

### Paso 5: Cerrar con `Notas` y `Estrategia de ejecución`

Antes del gate, el agente completa dos secciones del `release.md`:

- **Estrategia de ejecución:** orden de implementación si importa, repos afectados con su bump de versión, restricciones específicas (*ej. "el toggle vive en localStorage, no en backend"*).
- **Notas:** precondiciones (*"Precondición: v1.14.1 publicado y estable"*), decisiones de diseño que afectan a más de un requerimiento, dependencias internas (*"req 1.2 depende de req 1.1"*), excepciones a principios o gates si se acordaron.

Si una nota dice *"por decidir"*, la fase no terminó.

### Paso 6: Gate de aprobación

El agente cierra con un mensaje canónico:

```text
He preparado releases/v1.16.0.md con N requerimientos.
Resumen:
- v1.16.0 (MINOR): {{título}}
- Repos: API + Web
- Migración BD: Sí (3 scripts)
- Precondiciones: v1.15.0 publicado

¿Apruebas este archivo como contrato de implementación,
o quieres ajustar algo antes de pasar a Construcción?
```

El agente **no toca código** hasta recibir aprobación. Si el humano pide cambios, los aplica al archivo y vuelve a presentar el gate. Iteración tantas veces como sea necesaria — el archivo es barato; el rollback de implementación, no.

## Salidas

- `releases/vX.Y.Z.md` con estado `Pendiente` o `En desarrollo`, todos los requerimientos cerrados, criterios de aceptación claros.
- Items movidos del `BACKLOG.md` (eliminados de la pila): no se duplican. Si está en el release, no está en el backlog.
- Migraciones BD nombradas en orden, listas para que la siguiente fase escriba el SQL.
- Branch `dev` actualizado y listo para recibir commits (creado si no existe).

## Errores comunes

- **Saltarse triage y empezar a redactar requerimientos sueltos.** Sin lista cerrada, el documento crece sin disciplina; cada nuevo párrafo abre dos decisiones más.
- **Descripciones que dependen del chat.** *"Como dijimos antes..."* es bandera roja. La descripción debe ser ejecutable en frío.
- **Posponer la decisión de migraciones BD a Construcción.** Es donde más choque entre requerimientos hay; decidirlo tarde duele.
- **Aprobar verbalmente sin actualizar el archivo.** Si el humano dice *"sí, agrégale Y"* y el agente no lo escribe en el archivo antes del gate, Y desaparece.
- **Mezclar `BACKLOG.md` con `releases/vX.Y.Z.md`.** El backlog es ideas; el release es contrato. Un item en ambos lados se actualiza solo en uno y diverge.
- **Cerrar la fase con un *"creo que está bien"*.** El gate exige confirmación explícita. Una aprobación tibia produce una construcción tibia.

## Prompt de auditoría

Si querés validar un `releases/vX.Y.Z.md` antes de aprobarlo, dale al agente:

```text
Lee releases/vX.Y.Z.md como si fueras un agente nuevo sin acceso
a la conversación previa. Por cada requerimiento, dime:

1. ¿Puedo implementarlo solo con la descripción del item?
2. ¿El criterio de aceptación es verificable sin ambigüedad?
3. ¿Está claro qué archivo o módulo voy a tocar?
4. ¿Si el item dice "Migración BD: Sí", el script está nombrado?

Marca cada requerimiento como ✅ Listo o ❌ Falta + qué falta.
No infieras ni completes; solo audita.
```

:::tip Una pregunta a la vez, también en triage
El triage es el momento donde más se rompe el principio de *"una pregunta a la vez"*. La tentación es resolver los 12 items del backlog de un tirón. Resistilo: ítem por ítem, decisión registrada, siguiente. Un triage mal hecho condena la fase entera.
:::

## Puente al siguiente módulo

Con el `releases/vX.Y.Z.md` aprobado, la conversación cambia: ya no se decide qué construir, se construye lo decidido. El módulo [6.10 Fase 2: Construcción dirigida por release.md](./10-fase-2-construccion-dirigida-por-release.md) cubre cómo el agente lee el contrato, implementa item por item, escribe migraciones y CHANGELOG, y cierra con la verificación que abre el siguiente gate.

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** producir `releases/vX.Y.Z.md` aprobado como contrato de implementación.

**Entradas:**
- `BACKLOG.md` con items pendientes.
- Conversación sobre qué se quiere lograr.
- Tipo SemVer deseado.
- Plantilla `aidlc/plantillas/_release-template.md`.

**Pasos:**
1. Capturar ideas en `BACKLOG.md`, no en chat.
2. Triage: lista cerrada para esta versión + tipo SemVer + repos afectados.
3. Redactar `releases/vX.Y.Z.md` un requerimiento a la vez, con descripción autocontenida.
4. Nombrar las migraciones BD desde ahora, en orden.
5. Cerrar `Estrategia de ejecución` y `Notas`.
6. Gate de aprobación humana.

**Salidas:**
- `releases/vX.Y.Z.md` aprobado.
- `BACKLOG.md` actualizado (items movidos).
- Migraciones BD nombradas en orden.
- Branch `dev` listo.

**Errores comunes:**
- Saltarse triage.
- Descripciones dependientes de la conversación.
- Posponer migraciones BD.
- Aprobar verbalmente sin actualizar el archivo.
- Mezclar `BACKLOG.md` con el release.

**Referencias cruzadas:**
- [6.8 Ciclo de vida AIDLC 10X](./08-ciclo-de-vida-aidlc-10x.md)
- [6.10 Fase 2: Construcción dirigida por release.md](./10-fase-2-construccion-dirigida-por-release.md)
- [5.1 De la idea al release](../documentacion-y-requerimientos/01-de-la-idea-al-release.md)
- [5.4 Trazabilidad requerimiento ↔ release](../documentacion-y-requerimientos/04-trazabilidad-requerimiento-release.md)
</div>

---

## Glosario

**Release como contrato** *(Release-as-contract)* — práctica de tratar el archivo `releases/vX.Y.Z.md` como un acuerdo cerrado entre humano y agente. Una vez aprobado, lo que no está adentro no se construye en esa versión.

**Descripción autocontenida** *(Self-contained description)* — descripción de un requerimiento que puede leerse y ejecutarse sin acceso a la conversación que la originó. Es la prueba operativa de un requerimiento bien redactado.

**Triage** *(Triage)* — paso de Concepción donde el equipo decide qué items del backlog entran a la versión nueva, su tipo SemVer y qué repos toca. Cierra la lista del release antes de la redacción detallada.

**Backlog** *(Backlog)* — pila de ideas y items pendientes que **no pertenecen a ninguna versión** todavía. Es la entrada del triage. No duplica con `releases/vX.Y.Z.md`: si un item está en el release, sale del backlog.

**Criterio de aceptación** *(Acceptance criterion)* — frase verificable que declara qué prueba el humano para cerrar el item. Sin criterio, el item no se puede dar por hecho ni en Construcción ni en Operación.

:::info Referencias primarias
- [Semantic Versioning 2.0.0](https://semver.org/lang/es/) — base para el tipo de versión decidido en triage.
- [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/) — formato de CHANGELOG por repo que la Construcción asume.
- [awslabs/aidlc-workflows · Inception phase](https://github.com/awslabs/aidlc-workflows) — fase de Inception del AIDLC original (Apache 2.0).
:::

---

<AuthorCredit />
