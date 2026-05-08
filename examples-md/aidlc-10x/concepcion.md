# Fase 1 — Concepción

**Pregunta que responde:** ¿qué construir y por qué?
**Artefacto que produce:** `releases/vX.Y.Z.md` aprobado.
**Gate de salida:** confirmación humana de que el archivo es el contrato de implementación.

## Cuándo aplica esta fase

- Llega una idea suelta sin versión asignada.
- Se decide promover items del `BACKLOG.md` a una versión.
- Hay que abrir una nueva versión (`vX.Y.Z+1`) por feature, mejora o hotfix.
- El usuario dice *"Usando AIDLC 10X, vamos a planear vX.Y.Z"*.

Si el `releases/vX.Y.Z.md` ya existe y está aprobado, **no estás en esta fase** — saltá a [Construcción](./construccion.md).

## Pasos

### Paso 1: Captura inicial

Cuando llega una idea (de un usuario, un cliente, soporte, una conversación), va al `BACKLOG.md` antes que a cualquier otra parte.

- **Mal:** *"Sí, te lo agrego en la próxima versión"* — sin haber decidido versión, sin haber dimensionado.
- **Bien:** *"Lo agrego al `BACKLOG.md` con esta descripción. Cuando planeemos la próxima versión, vemos si entra."*

El item del backlog tiene como mínimo: módulo afectado, una línea de descripción, referencias (archivos, tickets, conversaciones). No requiere análisis técnico todavía.

### Paso 2: Triage y asignación de versión

Para abrir una versión nueva o agregar items a una versión planeada, el agente y el humano revisan el `BACKLOG.md` y deciden:

1. **Qué entra a esta versión.** Lista cerrada, no negociable después del gate.
2. **Tipo de versión SemVer:**
   - `MAJOR` — breaking change en API pública o esquema BD.
   - `MINOR` — feature visible al usuario o nuevo endpoint sin breaking.
   - `PATCH` — bugfix, mejora interna, refactor sin cambio funcional.
3. **Repos afectados.** Cada item declara si toca solo API, solo Web, ambos, docs, multi.
4. **Migraciones BD.** Si algún item toca el esquema, se anota desde aquí — no aparece sorpresivamente en construcción.

El agente nunca decide la versión. Pregunta y registra. Si el humano duda entre MINOR y PATCH, el agente expone el criterio (¿hay UI nueva? ¿hay endpoint nuevo? ¿el contrato cambia?) y deja la decisión.

### Paso 3: Redacción del `releases/vX.Y.Z.md`

A partir de la [`_release-template.md`](./plantillas/_release-template.md), el agente crea el archivo y lo va llenando **un requerimiento a la vez**. Cada requerimiento debe quedar con:

- **Tipo** (Feature / Bugfix / Mejora / Refactor).
- **Repos** afectados.
- **Migración BD** (Sí con archivo nombrado / No / Posible).
- **Estado** (`[ ] Pendiente`).
- **Descripción** detallada — el agente la escribe pensando en otro agente que va a implementarla **sin acceso a esta conversación**. Si la descripción depende de cosas dichas en el chat, falla la prueba y debe reescribirse.
- **Criterio de aceptación** cuando aplique — qué prueba el humano para cerrar el item.

Cada item del `releases/vX.Y.Z.md` debe poder leerse como una orden de trabajo independiente: si lo separas del resto del archivo y se lo pasas a otro agente, debería ser implementable sin más contexto que el `CLAUDE.md` del repo afectado.

### Paso 4: Identificación de migraciones BD

Si algún requerimiento toca BD, en este paso se nombran los scripts SQL que vendrán:

```
Database/migrations/v1.16.0/
  001-agregar-columna-empresas-zona-horaria.sql
  002-backfill-zona-horaria-default.sql
  003-not-null-zona-horaria.sql
```

El nombre y el orden se deciden ahora, no en construcción. Esto evita que dos requerimientos peleen por el mismo `001-`. El contenido SQL se escribe en construcción.

### Paso 5: Notas y precondiciones

Al final del archivo, sección `Notas`:

- Versión previa que debe estar publicada antes (ej. *Precondición: v1.14.1 publicado y estable*).
- Decisiones de diseño que afectan a más de un requerimiento.
- Dependencias internas (ej. *req 1.2 depende de req 1.1*).
- Excepciones a principios o gates (ver [`principios.md`](./principios.md)).

### Paso 6: Gate de aprobación

El agente termina presentando:

```
He preparado releases/vX.Y.Z.md con N requerimientos.
Resumen:
- vX.Y.Z (MINOR): {{título}}
- Repos: {{lista}}
- Migración BD: {{Sí (M scripts) | No}}
- Precondiciones: {{...}}

¿Apruebas este archivo como contrato de implementación, o quieres ajustar algo antes de pasar a Construcción?
```

El agente **no toca código** hasta recibir aprobación. Si el humano pide cambios, los aplica al archivo y vuelve a presentar el gate. Iteración tantas veces como sea necesaria.

## Salida esperada de esta fase

- `releases/vX.Y.Z.md` con estado `Pendiente` o `En desarrollo`, todos los requerimientos cerrados, criterios de aceptación claros.
- Items movidos del `BACKLOG.md` (eliminados de la lista) — no se duplican: si está en el release, no está en el backlog.
- Branch `dev` actualizado y listo para recibir commits (creado si no existe).

## Errores comunes

- **Saltarse triage y empezar a redactar requerimientos sueltos.** Sin lista cerrada, el documento crece sin disciplina y se vuelve negociable.
- **Descripciones que dependen de la conversación.** *"Como dijimos antes..."* es bandera roja: la descripción debe ser ejecutable en frío.
- **Posponer la decisión de migraciones BD a construcción.** Es donde más choque entre requerimientos hay; decidirlo tarde duele.
- **Aprobar verbalmente sin actualizar el archivo.** Si el humano dice *"sí, agrégale Y"* y el agente no lo escribe en el archivo antes del gate, Y desaparece.
- **Mezclar BACKLOG con release.** El backlog es ideas; el release es contrato. No se duplica un item entre ambos.

## Bloque estructurado para agentes

```yaml
fase: concepcion
objetivo: producir releases/vX.Y.Z.md aprobado
entradas:
  - BACKLOG.md
  - conversacion con el humano
  - tipo deseado (MAJOR | MINOR | PATCH)
pasos:
  - capturar ideas en BACKLOG.md
  - triage: lista cerrada para esta version
  - redactar releases/vX.Y.Z.md desde plantilla
  - nombrar migraciones BD si aplican
  - documentar precondiciones y notas
  - gate de aprobacion humana
salidas:
  - releases/vX.Y.Z.md aprobado
  - BACKLOG.md actualizado (items removidos)
  - branch dev preparado
gate_de_salida:
  - confirmacion humana explicita
errores_comunes:
  - saltarse triage
  - descripciones dependientes de conversacion
  - posponer migraciones BD
  - aprobar sin actualizar archivo
siguiente_fase: construccion.md
```
