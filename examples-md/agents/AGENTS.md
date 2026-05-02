<!-- Destino sugerido: AGENTS.md (raíz del repo) -->

# AGENTS.md

Este archivo define convenciones **comunes a cualquier agente de IA** que asista en este repositorio (Claude Code, Cursor, Copilot Workspace, etc.). Si un agente tiene un archivo propio (`CLAUDE.md`, `.cursorrules`), ese archivo extiende —no contradice— lo que aquí se declara.

## Alcance autorizado

El agente **puede** modificar sin preguntar:
- Archivos bajo `src/`, `tests/`, `docs/`.
- Archivos de configuración que no afecten infra (formatters, linters).

El agente **debe pedir confirmación** antes de:
- Modificar `.env*`, secretos, credenciales o claves.
- Cambiar esquema de base de datos o migraciones.
- Modificar pipelines CI/CD (`.github/`, `.gitlab-ci.yml`).
- Eliminar archivos bajo control de versiones que no haya creado en la sesión actual.

## Estilo de commits

- Conventional Commits en español: `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`.
- Mensaje corto y genérico para CI/infra (no filtrar detalles de arquitectura).
- Un commit por cambio lógico — no agrupar por conveniencia.

## Verificación obligatoria

Antes de proponer un PR o cerrar una tarea:

1. **Compilación limpia** — `dotnet build` sin warnings nuevos.
2. **Tests pasando** — incluyendo los nuevos si aplican.
3. **Linter/formatter** — `dotnet format` aplicado.
4. **Sin secretos** — revisa el diff contra patrones de API keys, tokens.

## Salidas esperadas del agente

- **Plan antes de editar**: si el cambio afecta 3+ archivos, resume el plan primero.
- **Diff legible**: cambios pequeños, bien localizados, sin reformateo masivo mezclado con lógica.
- **Post-condición verificable**: indica cómo saber que el cambio funciona (comando, endpoint, test).

## Errores comunes a evitar

- Crear documentación paralela que se desactualice (README.md dentro de cada subcarpeta).
- Generar tests que no fallarían si la implementación estuviera rota.
- Introducir dependencias nuevas sin evaluación.
- Refactors "de paso" que no pertenecen al cambio solicitado.
