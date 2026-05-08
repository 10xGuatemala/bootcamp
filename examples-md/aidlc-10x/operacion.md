# Fase 3 — Operación

**Pregunta que responde:** ¿está vivo y sano en producción?
**Artefacto que produce:** tag SemVer empujado, `releases/vX.Y.Z.md` con estado *Publicado*, eventualmente reportes en `reportes/`.
**Gate de salida:** no hay siguiente fase. La salida es el cierre del ciclo de la versión.

## Cuándo aplica esta fase

- Construcción terminó y fue verificada.
- El humano dice *"Usando AIDLC 10X, vamos a liberar vX.Y.Z"* o *"saca el release"*.
- Todos los items de `releases/vX.Y.Z.md` están en `[x]`.

Si hay items en `[ ]`, **no estás en esta fase** — regresa a [Construcción](./construccion.md).

## Principio rector de esta fase

**El agente prepara, el humano autoriza, una acción a la vez.** Cada paso que toca producción tiene una autorización explícita. No hay "bundle" de pasos automatizados — incluso si todo es scriptable, la cadencia humano→agente→humano se mantiene para que cada acción tenga un punto de no retorno reconocible.

## Pasos

### Paso 1: Bump de versión

El agente actualiza la versión en cada manifest del repo afectado, según el stack:

- API .NET: `<repo-api>/<Proyecto>.csproj` → tag `<Version>X.Y.Z</Version>`.
- Frontend Node: `<repo-frontend>/package.json` → campo `"version": "X.Y.Z"`.
- Otros stacks: el equivalente que corresponda (`pyproject.toml`, `Cargo.toml`, etc.).
- Si solo cambió uno de los repos, el agente pregunta al humano si igual se hace bump del otro por trazabilidad. Es una práctica recomendable cuando un endpoint público (ej. `/api/health`) reporta la versión y conviene que API y frontend reporten la misma minor.

Tras el bump:

- Reinstalar para sincronizar lockfile (`pnpm install` si hubo cambio de versión).
- Verificar que `dotnet build` y `pnpm run build` siguen verdes.

### Paso 2: Actualización del estado del release

En `releases/vX.Y.Z.md`:

- Cambiar `Estado:` a `En pruebas` (si aún no se ha desplegado) o dejarlo en `En desarrollo` hasta que se confirme deploy.
- Verificar que la sección `Estrategia de ejecución` y `Notas` estén alineadas con lo que efectivamente pasó. Si en construcción surgió algo que valga la pena registrar (ej. una decisión de diseño que no estaba en el release original), se agrega a `Notas`.

### Paso 3: Backup y migraciones BD

**Antes de cualquier cosa que toque la BD de producción:**

1. **Backup.** El agente recuerda al humano el comando o procedimiento de backup. El humano lo ejecuta y confirma.
2. **Migraciones.** El agente lista los scripts de `Database/migrations/vX.Y.Z/` en orden, y se los entrega al humano. El humano los corre contra la BD de producción uno por uno y confirma cada uno antes de pasar al siguiente. Si una falla, **se detiene todo** y se entra en modo incidente (ver paso 7).

El agente nunca corre migraciones en producción por sí solo, ni siquiera si tiene credenciales. Es un gate explícito.

### Paso 4: Build de producción y deploy

Para cada repo:

```bash
# API (ejemplo .NET)
dotnet publish ./<Proyecto>.csproj -c Release -r linux-x64 --self-contained false -o ./publish

# Frontend (ejemplo Vite/Next/CRA con pnpm)
pnpm run build
```

Después, deploy con la herramienta del proyecto. El humano ejecuta — el agente no.

### Paso 5: Verificación post-deploy

El agente prepara una checklist y la presenta al humano:

```
Verificación post-deploy:
- [ ] /api/health responde y reporta version: X.Y.Z
- [ ] Frontend carga sin errores en consola
- [ ] Login funciona
- [ ] {{flujo crítico afectado por esta versión}}
- [ ] {{otro flujo crítico}}
```

Los flujos críticos a verificar salen del `releases/vX.Y.Z.md` — son los criterios de aceptación de cada item. El humano marca `[x]` conforme verifica. Si algo falla, se entra en modo incidente.

### Paso 6: Cierre del release

Cuando la verificación post-deploy es 100% verde:

1. **Merge `dev` → `main`** en cada repo afectado y en el repo de docs (donde vive el `releases/vX.Y.Z.md`).
2. **Tag SemVer en cada repo:**
   ```bash
   git tag -a vX.Y.Z -m "Release vX.Y.Z"
   git push origin vX.Y.Z
   ```
3. **Actualizar `releases/vX.Y.Z.md`:**
   - `Estado: Publicado - YYYY-MM-DD`.
   - Confirmar que la fecha del header coincide con la fecha real de deploy.
4. **Mover items del `[Unreleased]`** del CHANGELOG de cada repo a una sección `[X.Y.Z] - YYYY-MM-DD`.
5. **Archivar referencias.** Si el release usó documentos externos (Excel, PDF, mockups), copiarlos a `referencias/vX.Y.Z/` para que queden congelados con el release.

### Paso 7: Modo incidente (si algo sale mal)

Si una migración falla, el deploy revienta, o un flujo crítico no funciona post-deploy:

1. **Detener.** No avanzar a más pasos.
2. **Diagnóstico.** El agente ayuda a leer logs, revisar diferencias, identificar causa raíz. No revierte sin autorización.
3. **Decisión humana:** ¿rollback, hotfix en caliente, o aceptar y abrir un `vX.Y.Z+1` con el fix?
4. **Reporte.** Pasada la urgencia, se escribe un `reportes/YYYY-MM-DD-incidente-vX.Y.Z.md` con timeline, causa raíz, fix aplicado, y aprendizaje (ej. "la migración 002 necesitaba `LOCK TABLES` que no se documentó"). Ese reporte alimenta cambios al ciclo o al `CLAUDE.md`.

## Salida esperada de esta fase

- Tag `vX.Y.Z` en cada repo afectado, empujado al remote.
- `main` actualizado.
- `releases/vX.Y.Z.md` con estado `Publicado - YYYY-MM-DD`.
- CHANGELOG por repo con sección `[X.Y.Z] - YYYY-MM-DD`.
- `referencias/vX.Y.Z/` con material externo si aplica.
- `reportes/...md` si hubo incidente.

## Errores comunes

- **Saltarse el backup de BD.** Una migración mala sin backup convierte un hotfix en una restauración con pérdida de datos.
- **Tagear antes de verificar post-deploy.** Si hay rollback, queda un tag en el remote apuntando a una versión que nunca corrió en producción.
- **No mover el CHANGELOG de `[Unreleased]` a `[X.Y.Z]`.** Las próximas versiones empiezan a sumarle entradas a la sección que era de la versión anterior.
- **Documentar el incidente solo en chat.** Sin reporte en `reportes/`, el aprendizaje se evapora con la siguiente conversación.
- **Bumpear versión y tagear sin merge a `main`.** El tag en `dev` queda fuera del historial principal y es difícil de seguir.
- **Hotfix sin abrir un nuevo `releases/vX.Y.Z.md`.** Aunque sea pequeño, el hotfix es un release y necesita su contrato. Si es muy chico, el archivo es de tres líneas, pero existe.

## Bloque estructurado para agentes

```yaml
fase: operacion
objetivo: liberar vX.Y.Z a produccion y cerrar el ciclo
precondicion: todos los items de releases/vX.Y.Z.md en [x] y verificados en local
principio_rector:
  agente_prepara_humano_autoriza_una_accion_a_la_vez: true
pasos:
  - bump de version en cada repo
  - actualizar estado del release
  - backup BD + correr migraciones (humano ejecuta, agente acompaña)
  - build de produccion + deploy (humano ejecuta)
  - verificacion post-deploy con checklist
  - merge dev->main, tag SemVer, push
  - actualizar CHANGELOG por repo
  - archivar referencias en referencias/vX.Y.Z/
modo_incidente:
  - detener
  - diagnostico (sin revertir sin autorizacion)
  - decision humana: rollback | hotfix | release siguiente
  - reporte en reportes/YYYY-MM-DD-incidente-vX.Y.Z.md
salidas:
  - tag vX.Y.Z en cada repo, empujado
  - main actualizado
  - releases/vX.Y.Z.md estado Publicado - fecha
  - CHANGELOG por repo con seccion de la version
  - referencias/vX.Y.Z/ si aplica
errores_comunes:
  - saltarse backup BD
  - tagear antes de verificar
  - CHANGELOG sin mover de Unreleased
  - incidente sin reporte
  - hotfix sin release.md
fin_de_ciclo: true
```
