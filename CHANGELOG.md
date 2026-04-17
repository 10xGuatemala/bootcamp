# Changelog

Todos los cambios notables de este proyecto se documentarán en este archivo.

El formato se basa en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/),
y el proyecto se adhiere a [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Unreleased

## 3.0.1 - 2026-04-17

### Changed

- Allowlist del mirror público reducida a `docs/`, `README.public.md`, `LICENSE` y `CHANGELOG.md`. Se excluyen `static/`, `sidebars.ts` y `LICENSE-CODE` porque son específicos del sitio o del código, no del contenido.
- `README.public.md` sin referencia a `LICENSE-CODE` (ya no se publica en el mirror).

## 3.0.0 - 2026-04-17

Release mayor. Consolida la reestructura de contenido (+2 categorías), el rediseño visual alineado a marca 10X, el upgrade de toolchain (Docusaurus 3.10 + pnpm) y la apertura del repo a un mirror público en GitHub bajo licenciamiento triple.

### Added

#### Contenido

- Nueva categoría **Colaboración con Agentes de IA** (6 módulos): fundamentos de colaboración, context engineering (`CLAUDE.md` / `AGENTS.md`), arquitectura orientada a skills, seguridad al ejecutar herramientas externas, diseño de prompts y verificación (con patrón *ejecutor + revisor sobre contexto compartido*), y seguridad de chatbots con IA.
- Nueva categoría **Documentación y Requerimientos** (4 módulos) separada de Gestión de Proyectos: de la idea al release (con Círculo Dorado y diferencia idea vs. requerimiento), SemVer en equipos, manuales de usuario final y trazabilidad requerimiento → release.
- Nueva subcategoría **Modernización de sistemas legacy** en Desarrollo Web y Móvil: costo oculto del legacy y migración progresiva (strangler fig).
- Módulos SAST/SCA y Seguridad en APIs/SPAs en Desarrollo Web y Móvil.
- Bloque estructurado para agentes al final de cada módulo nuevo — plantilla lista para que un agente lo parsee y genere una skill reutilizable.
- Páginas *landing* (`index.md`) en cada categoría raíz con `DocCardList`.

#### UI / Tema

- **Pill de versión en el navbar** (lee de `package.json` vía `customFields.version`) — visible en desktop, oculto en móvil.
- Soporte nativo de diagramas **Mermaid** (`@docusaurus/theme-mermaid`) con tema neutral/dark alineado a marca 10X.
- Componentes nuevos: `AuthorCredit` (atribución institucional configurable), `DotMesh` (malla de puntos animada en hero y footer, adaptativa a dark mode), `PoweredBy10X`, `Button` (API con `icon: ReactNode` para SVG inline).
- Swizzle de tema: `Navbar/Logo` (título bicolor + pill de versión), `Footer/Layout` (malla animada + bottom bar), `DocCard` (títulos multilínea, descripciones con clamp de 3 líneas).
- Banners SVG para las 2 categorías nuevas (`banner-agentes.svg`, `banner-docs.svg`).

#### Infraestructura / Release

- **Pipeline `.gitlab-ci.yml`** con stage `mirror` que publica un snapshot squash-per-release al repo público de GitHub cuando se empuja un tag SemVer (`vX.Y.Z`). Historial interno permanece en GitLab.
- Allowlist `.mirror-include` para controlar qué se publica al mirror (solo `docs/`, `static/`, `sidebars.ts`, licencias, `CHANGELOG.md`, `README.public.md`).
- **Licenciamiento triple** para el mirror público:
  - `LICENSE` — CC BY-NC-SA 4.0 para contenido (`docs/`, diagramas, textos).
  - `LICENSE-CODE` — propietario para código fuente (componentes, estilos, CI).
  - Cláusula de marcas registradas para nombre e identidad visual 10X.
- `README.public.md` orientado al mirror de GitHub: tagline, tabla de las seis rutas, guías de uso para estudio y para agentes de IA, referencia de estructura.
- `static/robots.txt` con sitemap para indexado.
- `.npmrc` con políticas de seguridad: `save-exact`, `minimum-release-age=1440` (24 h), `block-exotic-subdeps`.
- Client modules: `fonts.ts` (Fontsource self-hosted), `gtag-fallback.ts`.

### Changed

- **Breaking — toolchain:** Docusaurus 3.7.0 → 3.10.0, React 18.3.1, TypeScript 5.6.3, migración npm → **pnpm 10.33**. `package-lock.json` eliminado; `pnpm-lock.yaml` es ahora la fuente de verdad. Dockerfile actualizado para pnpm + `corepack enable`.
- **Rediseño visual del home** alineado a marca 10X: narrativa "fundamentos claros para personas, skills de IA reutilizables", hero con malla de puntos animada, grid de 6 rutas con badge *Nuevo*, paleta azul `#0d4d92` / naranja `#ef9b50`, tipografías Space Grotesk + Source Sans 3.
- Fuentes migradas de **Google Fonts CDN → `@fontsource/*` self-hosted** (Source Sans 3, Space Grotesk) — elimina dependencia externa y mejora privacidad.
- **Font Awesome reemplazado por SVG inline** en los botones del home; `@import` del CDN de FA eliminado.
- Reorganización: `docs/gestion-proyectos/ciclo-de-vida-y-documentacion/` movido a `docs/documentacion-y-requerimientos/` con nuevo módulo de trazabilidad.
- `AuthorCredit` ahora usa atribución institucional por defecto (`10X de Guatemala`, links corporativos); el bloque "Co-creado con..." es opcional.
- Componente `CustomsComponents/CustomButton` renombrado a `Button/` con API basada en `icon: ReactNode`.
- Mobile UX: menú lateral con contraste legible (texto blanco sobre fondo azul), carets invertidos; breakpoint navbar mobile preservado.
- Toggle de modo oscuro reducido a **dos fases** (`respectPrefersColorScheme: false`) — un clic alterna claro/oscuro sin pasar por "sistema".
- Config `onBrokenMarkdownLinks` migrado a `markdown.hooks.onBrokenMarkdownLinks` (compatibilidad Docusaurus v4).
- `FooterLayout`: eliminadas props `logo?` y `copyright?` no utilizadas.
- `DotMesh`: `initParticles()` movido fuera del bucle de `draw`; ahora solo se llama en `resize`.
- `docs/intro.md` actualizado para reflejar las seis rutas de aprendizaje.

### Removed

- **Breaking:** directorio `site-next/` consolidado en la raíz tras la migración.
- Import de Google Fonts y Font Awesome desde `custom.css`.
- Logos huérfanos en `static/img/`: `logo-10x-bootcamp.svg`, `logo-10x-box.svg`, `logo.svg`.
- Documento interno `PLAN-ACTUALIZACION-BOOTCAMP.md` (no pertenece al repo público).
- Frase aspiracional "Un bootcamp bilingüe en espíritu y monolingüe en ejecución" del hero.

### Fixed

- SVG `flujo-proyecto-backend-net.drawio.svg`: atributo `content=` con XML encodeado removido (bloqueaba a `image-size@2.0.2` en el build).
- Hover del navbar: sin fondo naranja translúcido, solo cambio de color de texto.
- Overlap del navbar en móvil: regla `.navbar__link { display: inline-flex }` envuelta en `@media (min-width: 997px)` para no pisar el `display: none` móvil de Docusaurus.
- Decisión tautológica "¿Invertir tiempo?" reemplazada por "¿Vale la pena? impacto vs. esfuerzo" en el flujograma idea → release.

## 1.3.0 - 2025-04-14

### Added

- Curso de Fundamentos de SonarQube en la carpeta de Desarrollo Web y Móvil.

## 1.2.0 - 2025-02-11

### Changed

- Actualización de Docusaurus a la versión 3.7.0.

### Fixed

- Revisión de archivos `MD` con `markdownlint`.
- Revisión de `admonitions`.

## 1.1.0 - 2025-02-10

### Added

- Archivo CHANGELOG.
- Archivo README.
- Archivo de configuración para crear contenedor (Docker o Podman).

## 1.0.0 - 2024-12-28

### Added

- Primera versión.
