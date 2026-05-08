---
sidebar_position: 11
sidebar_label: 6.11 Fase 3 · Operación con humano en el bucle
---

# Fase 3: Operación con humano en el bucle

Construcción cerró con build verde, items en `[x]` y branch `dev` listo. La fase final es la única del ciclo que toca producción, y por eso es la que más se rompe cuando se trata como continuación natural de las anteriores. La diferencia operativa con Concepción y Construcción es estructural: en aquellas, los gates están al final; en Operación, **cada acción tiene su gate**. El agente prepara, el humano autoriza, una acción a la vez.

Esta fase no produce código. Produce un tag SemVer empujado, un release publicado, y — si todo sale bien — silencio hasta la próxima versión.

## ¿Qué es la Operación?

La fase del ciclo donde el equipo libera la versión a producción. Incluye bump de versión en cada manifest, backup y migraciones BD, build de producción, deploy, verificación post-deploy, merge a `main`, tag SemVer y cierre del `releases/vX.Y.Z.md`. Si algo falla, activa el modo incidente: detener, diagnosticar, decidir, reportar.

El principio rector — heredado del [AIDLC original](https://github.com/awslabs/aidlc-workflows) — es **agente prepara, humano autoriza, una acción a la vez**. Incluso si todo es scriptable, la cadencia humano-agente-humano se mantiene. Cada paso que toca producción tiene un punto de no retorno reconocible.

## Por qué la Operación se rompe distinto

- **El agente puede tener credenciales sin tener autorización.** Que pueda ejecutar `deploy production` no significa que deba. La autorización es del humano, no de las credenciales.
- **Los errores en producción son visibles para clientes, no para el equipo.** Un bug en local cuesta una hora; el mismo bug en producción cuesta una conversación con el cliente y, si es serio, una restauración.
- **Una migración BD mala sin backup convierte un release en un incidente de datos.** Esa es la única acción del ciclo que puede tener consecuencias irreversibles. Por eso el backup precede a cualquier ejecución de migración, sin excepción.
- **El tag empujado es público.** Una vez que `vX.Y.Z` existe en el remote, el equipo, los CI, los clientes y los próximos releases dependen de él. Tagear antes de verificar es prometer algo que tal vez no funciona.
- **El silencio post-deploy no es éxito.** Si nadie verifica los flujos críticos, "no hubo error" no significa "la versión funciona". Significa "todavía no se reveló el error".

## Objetivo

Liberar `vX.Y.Z` a producción con backup BD verificado, migraciones ejecutadas en orden, deploy verificado, tag SemVer empujado, `releases/vX.Y.Z.md` cerrado con fecha y CHANGELOG migrado de `[Unreleased]` a la sección de la versión.

## Entradas

- Construcción terminada y verificada (todos los items en `[x]`).
- Branch `dev` con build verde en cada repo.
- Acceso a la BD de producción para backup y migraciones (humano).
- Acceso a la herramienta de deploy (humano).
- Tiempo razonable de baja carga si las migraciones lo requieren.

## Pasos para conducir la Operación

### Paso 1: Bump de versión en cada manifest

El agente actualiza la versión en cada manifest del repo afectado, según el stack:

- API .NET: `<repo-api>/<Proyecto>.csproj` → tag `<Version>X.Y.Z</Version>`.
- Frontend Node: `<repo-frontend>/package.json` → campo `"version": "X.Y.Z"`.
- Otros stacks: el equivalente que corresponda (`pyproject.toml`, `Cargo.toml`, etc.).

Si solo cambió uno de los repos, el agente pregunta al humano si igual se hace bump del otro por trazabilidad. Es una práctica recomendable cuando un endpoint público (ej. `/api/health`) reporta la versión y conviene que API y frontend reporten la misma minor.

Tras el bump:

- Reinstalar para sincronizar lockfile (`pnpm install` si hubo cambio de versión).
- Verificar que `dotnet build` y `pnpm run build` siguen verdes.

- Mal: *"Bumpeo solo el repo que cambió, los demás los dejo."* Cuando un cliente reporta un bug y dice "estoy en v1.16.0", el equipo no sabe a qué versión del API se refiere.
- Bien: *"Bumpo ambos por trazabilidad. La sección `Estrategia de ejecución` del release lo declaró."*

### Paso 2: Backup de BD antes de cualquier migración

**Antes** de cualquier acción que toque la BD de producción, el agente le recuerda al humano el comando de backup. El humano lo ejecuta y confirma. El agente no avanza hasta tener confirmación.

```text
[AGENTE]
Antes de ejecutar las migraciones, el primer paso es backup.
Comando sugerido (ajusta según tu motor):

  mysqldump -u <user> -p <db> > backup-pre-v1.16.0-$(date +%F).sql

Confirma cuando el backup esté hecho y verificado.
```

Esa pausa salva la versión cuando una migración falla. El día que un script tenga un bug — y va a tener uno — el backup será la diferencia entre rollback de cinco minutos y restauración con pérdida de datos.

### Paso 3: Migraciones BD una por una

Con backup confirmado, el agente lista los scripts de `Database/migrations/vX.Y.Z/` en orden y los entrega al humano. El humano corre cada script contra la BD de producción y confirma antes de pasar al siguiente.

```text
[AGENTE]
Migración 1 de 3:

  source Database/migrations/v1.16.0/001-agregar-columna-empresas-zona-horaria.sql

¿Procedes? (responde "ok" cuando esté ejecutado y verificado)

[HUMANO]
ok

[AGENTE]
Migración 2 de 3:

  source Database/migrations/v1.16.0/002-backfill-zona-horaria-default.sql

¿Procedes?
```

Si **una falla**, se detiene todo y se entra en modo incidente (paso 7). El agente nunca corre migraciones en producción por sí solo, ni siquiera si tiene credenciales — es un gate explícito.

### Paso 4: Build de producción y deploy

Para cada repo afectado:

```bash
# API (ejemplo .NET)
dotnet publish ./<Proyecto>.csproj -c Release -r linux-x64 --self-contained false -o ./publish

# Frontend (ejemplo Vite/Next/CRA con pnpm)
pnpm run build
```

Después, deploy con la herramienta del proyecto (script, CLI, pipeline). El humano ejecuta — el agente prepara el comando.

### Paso 5: Verificación post-deploy con checklist

El agente prepara una checklist y la presenta:

```text
Verificación post-deploy v1.16.0:
- [ ] /api/health responde y reporta version: 1.16.0
- [ ] Frontend carga sin errores en consola (navegador limpio, sin caché)
- [ ] Login funciona
- [ ] Flujo crítico de la versión: con el toggle apagado, el PDF
      no muestra columna IMPUESTOS ni líneas Base/Impuestos
- [ ] Sin errores nuevos en logs del servidor en los primeros 10 minutos
```

Los flujos críticos a verificar salen del `releases/vX.Y.Z.md` — son los criterios de aceptación de cada item. El humano marca `[x]` conforme verifica.

- Mal: *"Cargó el frontend, está bien."* No probó el flujo de la versión.
- Bien: *"Verifico cada criterio del release. Si uno falla, activo modo incidente."*

### Paso 6: Cierre del release

Cuando la verificación post-deploy es 100% verde:

1. **Merge `dev` → `main`** en cada repo afectado y en el repo de docs (donde vive el `releases/vX.Y.Z.md`).
2. **Tag SemVer en cada repo:**
   ```bash
   git tag -a v1.16.0 -m "Release v1.16.0"
   git push origin v1.16.0
   ```
3. **Actualizar `releases/v1.16.0.md`:** `Estado: Publicado - 2026-05-12` con la fecha real.
4. **Mover entradas de `[Unreleased]`** a `[1.16.0] - 2026-05-12` en cada CHANGELOG.
5. **Archivar referencias.** Si el release usó documentos externos (Excel, PDF, mockups), copiarlos a `referencias/v1.16.0/` para que queden congelados con el release.

### Paso 7: Modo incidente cuando algo sale mal

Si una migración falla, el deploy revienta, o un flujo crítico no funciona post-deploy:

1. **Detener.** No avanzar a más pasos.
2. **Diagnóstico.** El agente ayuda a leer logs, comparar diferencias, identificar causa raíz. **No revierte sin autorización.**
3. **Decisión humana:** rollback, hotfix en caliente, o aceptar el bug y abrir un `vX.Y.Z+1` con el fix.
4. **Reporte.** Pasada la urgencia, se escribe `reportes/YYYY-MM-DD-incidente-vX.Y.Z.md` con timeline, causa raíz, fix aplicado, y aprendizaje (ej. *"la migración 002 necesitaba `LOCK TABLES` que no se documentó"*). Ese reporte alimenta cambios al ciclo o al `CLAUDE.md` para que no vuelva a pasar.

- Mal: *"Lo arreglé en caliente, no hace falta reporte."* La próxima vez nadie sabe qué se hizo ni por qué.
- Bien: *"Reporte breve en `reportes/`. Aprendizaje agregado al `CLAUDE.md` del repo afectado."*

## Salidas

- Tag `vX.Y.Z` en cada repo afectado, empujado al remote.
- `main` actualizado.
- `releases/vX.Y.Z.md` con estado `Publicado - YYYY-MM-DD`.
- CHANGELOG por repo con sección `[X.Y.Z] - YYYY-MM-DD`.
- `referencias/vX.Y.Z/` con material externo si aplica.
- `reportes/...md` si hubo incidente.

## Errores comunes

- **Saltarse el backup de BD.** Una migración mala sin backup convierte un hotfix en una restauración con pérdida de datos.
- **Tagear antes de verificar post-deploy.** Si hay rollback, queda un tag en el remote apuntando a una versión que nunca corrió.
- **No mover el CHANGELOG de `[Unreleased]` a `[X.Y.Z]`.** Las próximas versiones empiezan a sumarle entradas a la sección que era de la versión anterior.
- **Documentar el incidente solo en chat.** Sin reporte en `reportes/`, el aprendizaje se evapora con la siguiente conversación.
- **Bumpear versión y tagear sin merge a `main`.** El tag en `dev` queda fuera del historial principal y es difícil de seguir.
- **Hotfix sin abrir un nuevo `releases/vX.Y.Z.md`.** Aunque sea pequeño, el hotfix es un release y necesita su contrato. Si es muy chico, el archivo es de tres líneas, pero existe.
- **Verificación post-deploy "general".** "Cargó la home" no equivale a "el flujo de la versión funciona". Cada criterio de aceptación tiene que verificarse uno por uno.

## Prompt de auditoría

Antes de empujar el tag, el humano puede pedir al agente:

```text
Audita el branch dev contra main para release v1.16.0:

1. ¿Las versiones en .csproj y package.json coinciden con v1.16.0?
2. ¿Cada item del releases/v1.16.0.md está [x]?
3. ¿El CHANGELOG de cada repo afectado tiene su sección [Unreleased]
   con entradas que mapean a items del release?
4. ¿Las migraciones de Database/migrations/v1.16.0/ están todas
   numeradas y documentadas?
5. ¿La sección Estado del release.md está lista para "Publicado"
   o todavía dice "En desarrollo"?

Marca cada punto ✅ Listo o ❌ Falta + qué falta.
```

:::tip El gate por acción no es burocracia
Cuando se ejecuta el ciclo varias veces, la tentación es agrupar pasos: *"hago backup + migración + deploy de un tirón"*. Resistilo. La separación por acción es lo que permite, en una migración fallida, detener antes del deploy. Si los pasos están encadenados, un error en el paso 2 te llevó al paso 4 sin chance de detener.
:::

## Puente al siguiente módulo

El ciclo cerró: tag empujado, release publicado, CHANGELOG actualizado. La siguiente versión empieza por la primera fase otra vez — captura en `BACKLOG.md`, triage, redacción del nuevo `releases/vX.Y.Z+1.md`. Si en este ciclo apareció algún patrón que vale la pena documentar — una desviación del `CLAUDE.md` aceptada, una excepción a un principio, un incidente con su reporte — pasalo al `CLAUDE.md` del repo afectado antes del próximo arranque. La memoria del ciclo vive en los archivos, no en la cabeza del equipo.

Si todavía no instalaste el paquete `aidlc-10x`, el módulo [6.8 Ciclo de vida AIDLC 10X](./08-ciclo-de-vida-aidlc-10x.md) cubre el setup desde cero.

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** liberar `vX.Y.Z` a producción con humano-en-el-bucle por acción.

**Precondición:** Construcción cerrada con todos los items en `[x]` y verificación local.

**Principio rector:** agente prepara, humano autoriza, una acción a la vez.

**Pasos:**
1. Bump de versión en cada manifest.
2. Recordar backup BD; humano confirma antes de avanzar.
3. Migraciones BD una por una, con confirmación humana cada vez.
4. Build de producción + deploy (humano ejecuta).
5. Verificación post-deploy con checklist derivada del release.
6. Merge `dev → main`, tag SemVer, push.
7. Actualizar CHANGELOG, archivar referencias.
8. Modo incidente si aplica + reporte en `reportes/`.

**Salidas:**
- Tag `vX.Y.Z` empujado.
- `main` actualizado.
- `releases/vX.Y.Z.md` con estado `Publicado - YYYY-MM-DD`.
- CHANGELOG por repo con sección de la versión.
- `reportes/` actualizado si hubo incidente.

**Errores comunes:**
- Saltarse backup BD.
- Tagear antes de verificar.
- CHANGELOG sin mover de `[Unreleased]`.
- Incidente sin reporte.
- Hotfix sin `release.md`.
- Verificación post-deploy genérica.

**Referencias cruzadas:**
- [6.10 Fase 2: Construcción dirigida por release.md](./10-fase-2-construccion-dirigida-por-release.md)
- [6.8 Ciclo de vida AIDLC 10X](./08-ciclo-de-vida-aidlc-10x.md)
- [5.2 Versionado semántico en equipos](../documentacion-y-requerimientos/02-versionado-semantico-en-equipos.md)
- [5.4 Trazabilidad requerimiento ↔ release](../documentacion-y-requerimientos/04-trazabilidad-requerimiento-release.md)
</div>

---

## Glosario

**Humano en el bucle** *(Human-in-the-loop)* — patrón donde el agente prepara cada acción y el humano la autoriza explícitamente antes de ejecutarse. En Operación se aplica por acción individual, no como gate de fin de fase.

**Punto de no retorno** *(Point of no return)* — acción que, una vez ejecutada, requiere esfuerzo desproporcionado para revertirse. En Operación los ejemplos típicos son: ejecutar migración sin backup, push de tag al remote, deploy a producción sin verificación previa.

**Verificación post-deploy** *(Post-deploy verification)* — checklist derivada de los criterios de aceptación del `releases/vX.Y.Z.md` que el humano ejecuta contra el ambiente productivo recién desplegado. Su éxito habilita el cierre del release.

**Modo incidente** *(Incident mode)* — protocolo que se activa cuando una acción de Operación falla. Detener, diagnosticar sin revertir sin autorización, decisión humana entre rollback / hotfix / nueva versión, y reporte en `reportes/` con causa raíz y aprendizaje.

**Reporte de incidente** *(Incident report)* — documento `reportes/YYYY-MM-DD-incidente-vX.Y.Z.md` que registra timeline, causa raíz, fix y aprendizaje de un incidente de Operación. Su valor está en alimentar cambios al ciclo y al `CLAUDE.md` para que el incidente no se repita.

:::info Referencias primarias
- [awslabs/aidlc-workflows · Operations phase](https://github.com/awslabs/aidlc-workflows) — fase de Operations del AIDLC original (Apache 2.0).
- [Semantic Versioning 2.0.0](https://semver.org/lang/es/) — base para los tags `vX.Y.Z` empujados al cierre del release.
- [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/) — formato del CHANGELOG por repo que se cierra al final de Operación.
- [Google SRE · Postmortem culture](https://sre.google/sre-book/postmortem-culture/) — cultura de reportes blameless aplicable a `reportes/` de incidentes.
:::

---

<AuthorCredit />
