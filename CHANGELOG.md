# Changelog

Cambios de contenido del bootcamp público. Solo se listan adiciones y modificaciones de cursos y módulos.

El formato se basa en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/) y el proyecto sigue [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## 3.5.0 — 2026-04-22

### Added

- **6.7 De specs a proyecto real** — nuevo módulo que cierra la ruta 6 (colaboración con agentes). Describe el flujo de arranque guiado (entrevista → contrato `specs.md` → scaffolding reproducible) con los cinco bloques ordenados (Objetivo → Stack → Estructura → Operación → Alcance) y la regla cardinal "una pregunta a la vez".
- **Reorganización de skills copiables** en `examples-md/agents/skills/`, ahora por dominio:
  - **`general/`** (agnósticas de stack): `code-review`, `release-notes`, `redactar-manual-usuario`, `revisar-hallazgo-sast`, `entrevista-specs`, `scaffolding-desde-specs`, `escribir-slash-command`.
  - **`net-core-web-api/`** (curso 1.2): `estructura-proyecto-net`, `nuevo-endpoint-rest-net`, `nueva-entidad-ef-core`, `patrones-diseno-net`, `checklist-produccion-net`.
- **Plantillas nuevas en `examples-md/project/`:** `specs.md.example` (contrato de arranque, Actividad 0) y `release-requirements.md.example` (requerimientos por versión, release-centric, con variante multi-repo para productos API + Web + BD).
- **Tips copiables cruzados** en 6.3 Arquitectura orientada a skills, 1.2 index (net-core-web-api), 4.5 SAST/SCA en validación, 5.2 Versionado semántico en equipos y 5.3 Manuales de usuario final — enlazan a las skills y plantillas correspondientes del repo público.

### Changed

- **1.2.1 Arquitectura de backend** — nueva sección "Estructura de carpetas del proyecto" con árbol literal (`Auth/`, `Commons/` con sub-arquitectura, `Modules/{Dominio}/`, `Extensions/`, `Database/`), regla de los dos consumidores, tabla de sufijos por rol (15 filas) y patrón de bootstrap modular con extension methods. Glosario y `agent-block` sincronizados.
- **1.2.2 Capa de Controlador** — regla rápida `400 Bad Request` vs `422 Unprocessable Entity`, tabla de sufijos DTO (`Request` / `Response` / `FilterDto`) y tabla de decisión `Controller → Service` vs `Controller → DbContext`. Glosario y `agent-block` sincronizados.
- **1.2.3 Capa de Servicios** — nueva buena práctica: `AsNoTracking()` por default en consultas de solo lectura, con ejemplos Mal/Bien/`AnyAsync`. Glosario y errores comunes actualizados.
- **1.2.4 Capa de Datos** — tabla de naming C#↔SQL (PK/FK/UNIQUE/índices), Data Annotations sobre Fluent API con tres razones explícitas, convención de zona horaria para `DateTime`, matriz tabla dedicada vs catálogo genérico y checklist de sincronía Model ↔ DDL. Glosario y `agent-block` sincronizados.
- **1.2.5 Patrones de diseño en APIs REST** — nueva sección "Regla del tres" y reemplazo de la tabla existente por matriz dolor → patrón → ubicación con sufijos y carpetas (`Commons/Application/…`, `Modules/{Dominio}/StateMachine/`). Glosario y `agent-block` sincronizados.

## 3.4.0 — 2026-04-19

### Added

- **Fundamentos UX/UI (3.1.5 – 3.1.13):** UX Research, Análisis de usuarios y competencia, Personas y escenarios, Arquitectura de información (con los 8 principios de Dan Brown), Pruebas de usabilidad, Psicología del color, Tipografía, Jerarquía visual y Gestalt, Accesibilidad básica.
- **3.2.2 Wireframes y prototipado.**
- **3.2.6 Microcopy híbrido (humano + IA)** — verbos imperativos, autonomía de contexto, errores de tres partes y `UXW_SPEC.md`.
- **`examples-md/`** — plantillas copiables fuera del árbol de docs: `design/`, `agents/` (CLAUDE.md, AGENTS.md, skills) y `project/` (sprint plan, backlog, retrospective).

## 3.3.0 — 2026-04-18

### Added

- **3.2 Sistemas de Diseño e Implementación** — el puente entre UX/UI y código:
  - 3.2.1 Design tokens y estándares (formato DTCG, Style Dictionary, `DESIGN.md`).
  - 3.2.2 Arquitectura de componentes (Atomic Design, contratos).
  - 3.2.3 Handoff técnico (los 7 estados, 3 breakpoints, prompts canónicos).
  - 3.2.4 UX writing para interfaces dinámicas (microcopy, ICU MessageFormat).

### Changed

- El antiguo "3.1.6 Artefactos de diseño para agentes" se disuelve: su contenido vive ahora en 3.2.1, 3.2.2 y 3.2.3.

## 3.2.0 — 2026-04-18

### Added

- **3.1.6 Artefactos de diseño para agentes** — plantilla `DESIGN.md`, `design-tokens.json` (DTCG), brief de componente y tres prompts canónicos.

### Changed

- Resuelto conflicto **Qué vs Cómo** entre los módulos de Documentación/Requerimientos (sección 5) y Desarrollo (sección 1) con bloques de trazabilidad explícita.

## Versiones anteriores

El historial completo (incluyendo cambios internos de infraestructura, build y sitio) vive en el repositorio fuente de 10X. Aquí solo se listan cambios visibles en el contenido del bootcamp.
