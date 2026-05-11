<!-- Destino sugerido: .claude/commands/migrar-forms.md -->

---
name: migrar-forms
description: Activa el flujo de migración de Oracle Forms. Valida precondiciones, ejecuta el inventario cruzado, clasifica triggers y produce el backlog de migración. Cero supuestos — pregunta antes de avanzar cuando una precondición falta.
---

# Slash command: /migrar-forms

Punto de entrada para un agente que asiste un proyecto de migración desde Oracle Forms. Activa los skills [`migracion-forms`](../agents/skills/oracle-forms/migracion-forms.skill.md) y [`consultas-parametrizadas-plsql`](../agents/skills/oracle-forms/consultas-parametrizadas-plsql.skill.md), y produce los artefactos del paquete `forms-migration/`.

## Uso

```
/migrar-forms
```

Sin argumentos. El agente arranca con la fase 1 (precondiciones) y avanza por las fases siguientes con confirmación explícita en cada una.

## Fases que ejecuta el agente

### Fase 1 · Verificar precondiciones

Antes de tocar archivos, el agente confirma con el usuario:

1. **Acceso al filesystem del legacy** — ruta a los archivos `.fmb`, `.pll`, `.mmb`, `.rdf`.
2. **Acceso a la base de datos Oracle** — TNS o cadena de conexión + usuario con `SELECT` sobre `USER_OBJECTS`, `USER_DEPENDENCIES`, `USER_SOURCE`, `USER_TRIGGERS`, `USER_VIEWS`.
3. **Disponibilidad de `frmf2xml`** — el utilitario de Forms Builder para convertir `.fmb` a XML. Si no está, el agente propone alternativas (parsers Python, herramientas comerciales) antes de continuar.
4. **Decisión de stack destino** — APEX, .NET 8+ o Java 21+, justificada con la matriz 10X firmada. Si no está, el agente refiere al módulo [1.4.3](../../docs/desarrollo-web-y-movil/modernizacion-legacy/03-migracion-desde-oracle-forms.md) y no avanza.
5. **Carpeta `forms-migration/` lista** — si no existe en el repo del proyecto, el agente copia las plantillas desde este paquete.

Si alguna precondición falla, el agente reporta cuál y se detiene. No improvisa.

### Fase 2 · Inventariar

El agente ejecuta los bloques de [`scripts-inventario.md`](./scripts-inventario.md):

- `§ Listar fuentes` → puebla `inventario-fuentes-forms.md`.
- `§ Listar objetos de base` → puebla `inventario-objetos-bd.md`.
- Cruza ambos lados y agrega discrepancias a la sección "Discrepancias" del segundo archivo.

Salida: dos artefactos vivos + lista de preguntas abiertas para el cliente.

### Fase 3 · Convertir `.fmb` y clasificar triggers

- `§ Convertir .fmb a XML` → genera los `.xml`.
- `§ Extraer triggers` → produce `triggers-extraidos.tsv`.
- Por cada trigger, el agente propone clasificación (validación / cálculo / paquete / navegación / UI) y la registra en `triggers-clasificados.md` para revisión humana.

El agente **nunca** clasifica un trigger sin confirmación cuando la categoría no es evidente.

### Fase 4 · Documentar contratos y armar backlog

- Por cada pantalla en `inventario-fuentes-forms.md`, el agente copia `contrato-pantalla.md` a `pantallas/<form-id>.md` y llena lo que puede inferir del XML (datos que lee, datos que escribe, librerías `.pll` consumidas).
- Los campos que requieren conversación con el negocio (criticidad, reportes asociados, RN aplicables) quedan marcados como `<pendiente>`.
- El agente puebla `backlog-migracion-pantallas.md` con orden tentativo según los criterios del módulo [1.4.4](../../docs/desarrollo-web-y-movil/modernizacion-legacy/04-inventario-y-extraccion-forms.md). El responsable del proyecto valida o ajusta.

### Fase 5 · Plan de retiro inicial

El agente esqueleta `plan-retiro-legacy.md` con:

- Pantallas candidatas a "sobrevivir en legacy" (las muy ocasionales o de bajo valor).
- Bibliotecas `.pll` que aparecen huérfanas en el cruce inicial.
- Objetos de base que ningún `.fmb` referencia y que probablemente queden con el equipo DBA.

Todo como propuesta para discusión, no como decisión.

## Reglas de operación

- **El agente nunca avanza de fase sin confirmación explícita.** Cada fase termina con un resumen de lo producido y la pregunta "¿Continuar a la fase siguiente?".
- **El agente nunca commitea ni hace push.** Produce y modifica archivos; el commit es decisión humana.
- **El agente respeta el slug `form-id`** en kebab-case sin acentos. Si encuentra un `.fmb` con caracteres no válidos, propone un slug y pide confirmación.
- **Las preguntas abiertas se acumulan en una sola lista**, no se dispersan por archivo. El usuario puede contestarlas en bloque al cierre de cada fase.
- **Cuando aparece SQL dinámico concatenado**, el agente lo marca para procesar con el skill [`consultas-parametrizadas-plsql`](../agents/skills/oracle-forms/consultas-parametrizadas-plsql.skill.md) — no lo corrige en el mismo paso del inventario.

## Salida final del flujo

Al cerrar la fase 5, el repositorio del proyecto tiene:

- `forms-migration/inventario-fuentes-forms.md` poblada.
- `forms-migration/inventario-objetos-bd.md` poblada y cruzada.
- `forms-migration/triggers-clasificados.md` con propuestas para cada trigger.
- `forms-migration/pantallas/<form-id>.md` esqueletada por pantalla.
- `forms-migration/backlog-migracion-pantallas.md` con orden tentativo.
- `forms-migration/plan-retiro-legacy.md` con candidatos iniciales.
- Una lista única de preguntas abiertas listas para revisar con el cliente.

Con esto la primera reunión de planificación pasa de "no sabemos qué tiene el sistema" a "hablemos de estas decisiones concretas".
