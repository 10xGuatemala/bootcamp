<!-- Destino sugerido: RELEASING.md en la raíz del repo de docs del producto -->

# Guía de Release

Checklist reutilizable para publicar una nueva versión bajo AIDLC 10X. Es la forma operacional concreta de la [Fase 3 — Operación](../operacion.md).

Copiar la sección de checklist al `releases/vX.Y.Z.md` que se está liberando, o seguirla en orden directamente.

## Pre-requisitos

- Todos los requerimientos en `releases/vX.Y.Z.md` están en `[x]`.
- Los cambios viven en el branch `dev` de cada repo afectado.
- Se probó la funcionalidad en local o staging.
- La herramienta de deploy del proyecto está configurada (script, CLI, pipeline o `deploy.config.yaml` según convención del producto).

## Checklist de Release vX.Y.Z

### Pre-release (preparación)

- [ ] Todos los requerimientos en `releases/vX.Y.Z.md` marcados `[x]`.
- [ ] CHANGELOG.md actualizado con sección `[Unreleased]` en cada repo afectado.
- [ ] Migraciones SQL revisadas y probadas en ambiente local.
- [ ] Versión actualizada en `.csproj` (API) y/o `package.json` (Web).
- [ ] `pnpm install` (o equivalente) ejecutado si se cambió `package.json` (sincronizar lock).

### Base de datos (humano ejecuta, agente acompaña)

- [ ] Backup de BD de producción confirmado.
- [ ] Ejecutar scripts de `Database/migrations/vX.Y.Z/` en orden numérico, uno por uno, confirmando cada uno.

### Build

- [ ] `dotnet publish ...` (API).
- [ ] `pnpm run build` (Web).

### Deploy (humano ejecuta)

- [ ] `deploy ... --env production`.
- [ ] Verificar `/api/health` reporta `version: X.Y.Z`.
- [ ] Verificar carga del frontend en navegador limpio (sin caché).

### Verificación post-deploy

- [ ] Login funciona.
- [ ] {{Flujo crítico 1 — específico de la versión}}.
- [ ] {{Flujo crítico 2 — específico de la versión}}.
- [ ] Sin errores en consola del navegador en flujos verificados.
- [ ] Sin errores nuevos en logs del servidor en los primeros 10 minutos.

### Post-release

- [ ] Merge `dev` → `main` en los repos que cambiaron.
- [ ] Merge `dev` → `main` en el repo de docs.
- [ ] Crear tag `vX.Y.Z` en cada repo afectado y en docs:
  ```bash
  git tag -a vX.Y.Z -m "Release vX.Y.Z"
  git push origin vX.Y.Z
  ```
- [ ] Mover entradas de `[Unreleased]` a `[X.Y.Z] - YYYY-MM-DD` en cada CHANGELOG.
- [ ] Actualizar `releases/vX.Y.Z.md` → `Estado: Publicado - YYYY-MM-DD`.
- [ ] Archivar referencias externas en `referencias/vX.Y.Z/` si aplica.

## Hotfix (parche urgente en producción)

Para correcciones que no pueden esperar:

1. Crear branch `hotfix/vX.Y.Z` desde `main`.
2. Crear `releases/vX.Y.Z.md` aunque sea breve — el contrato existe siempre.
3. Implementar la corrección siguiendo Fase 2 (con menos formalidad si la urgencia lo justifica, registrando la excepción en `Notas` del release).
4. Seguir el checklist normal de release.
5. Merge a `main` **y** a `dev` (para no perder el fix en la próxima versión).

## Si algo sale mal en operación

Activar el modo incidente descrito en [`operacion.md` §7](../operacion.md). En resumen:

1. Detener el avance.
2. Diagnosticar sin revertir sin autorización.
3. Decisión humana: rollback / hotfix / aceptar y abrir versión siguiente.
4. Reporte en `reportes/YYYY-MM-DD-incidente-vX.Y.Z.md` con timeline, causa raíz, fix y aprendizaje.
