# Changelog

Todos los cambios notables de este proyecto se documentarán en este archivo.

El formato se basa en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/),
y el proyecto se adhiere a [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## 3.1.1 - 2026-04-18

### Changed

- Encabezados H2/H3 del cuerpo normalizados sin numeración en 51 archivos (207 encabezados). La numeración jerárquica ahora vive exclusivamente en `sidebar_label` y el sidebar; antes el cuerpo mezclaba `4.2.1.0`, `1.1` y sin numerar.

### Fixed

- Reemplazada "desambiguar" por "decidir" en versionado semántico (se leía forzada).
- Título "El ciclo, a detalle" → "El ciclo paso a paso" en el módulo de idea al release.

## 3.1.0 - 2026-04-18

### Added

- Nueva sección **Gestión ágil de proyectos** (renombrada desde "Gestión de Proyectos") con dos subcategorías:
  - **Agilidad y Scrum** (5 módulos): manifiesto ágil, framework Scrum (roles/eventos/artefactos), niveles de planeamiento (visión → daily), integración PMBOK ↔ Scrum, y liderazgo y facilitación con los 10 principios de Shackleton aplicados a entornos ágiles.
  - **Ciclo del proyecto** (6 módulos): plan/problema/alcance con Círculo Dorado, Product Backlog (MoSCoW + INVEST), Sprint Planning y Daily, medición con OKRs frente a métricas de actividad, retrospectivas (4Ls / Start-Stop-Continue / Mad-Sad-Glad) y cierre + lecciones aprendidas con las 18 causas recurrentes de fracaso.
- **Glosario por módulo** al final de cada archivo, con 4–8 términos en formato `**Término** *(English)* — definición`, seguido de un bloque `:::info Referencias primarias` con enlaces canónicos (Scrum Guide 2020, PMBOK 7ª edición, OWASP LLM Top 10, Anthropic docs, Diátaxis, Kimball Group, Nielsen Norman Group, etc.). Cubre los 60+ módulos del sitio.
- **Bloque estructurado para agentes** (`### Bloque estructurado para agentes` con Objetivo / Entradas / Pasos / Salidas / Errores comunes / Referencias cruzadas) en todos los módulos del sitio — antes solo estaba en las rutas de Colaboración con Agentes de IA y Modernización legacy.
- Definiciones explícitas de "idea" vs "requerimiento" en el primer módulo de Documentación y Requerimientos.
- Bloque de navegación visible en GitHub (`<div className="github-only-toc">`) en los 15 `index.md` de categoría — oculto en Docusaurus vía CSS, muestra lista de hijos con sus `sidebar_label` para facilitar la lectura del repo en el mirror público.
- Swizzle `src/theme/MDXComponents.tsx` que registra `DocCardList` y `AuthorCredit` como componentes MDX globales. Permite eliminar las líneas de `import` de todos los `.md` y que GitHub los renderice limpios.

### Changed

- Etiqueta de la categoría raíz: `4. Gestión de Proyectos` → `4. Gestión ágil de proyectos`.
- **Licencia migrada a CC BY-ND 4.0 con excepción explícita para IA**: reemplaza la estructura anterior (CC BY-NC-SA 4.0 para contenido + LICENSE-CODE propietario) por una licencia unificada basada en un estándar reconocido, más una sección que permite expresamente usar el contenido como *training data*, contexto o *retrieval* para modelos y agentes de IA, incluyendo uso comercial, con la condición de que la salida del sistema no redistribuya el contenido textualmente. El texto público ya no expone detalles de la estructura interna del proyecto (Docusaurus, React, `src/`).
- **Sidebar rediseñado** con tipografía escalonada por nivel (categoría 0.88rem/650, subcategoría 0.82rem/550, documento hoja 0.78rem/400), familia unificada a Source Sans 3, línea guía vertical sutil en sublistas para anclar la profundidad, padding y line-height compactos para acomodar los 3 niveles de navegación sin saturar.
- **Consistencia transversal del contenido**:
  - Numeración `X.Y.Z` aplicada de forma uniforme en los `sidebar_label` de todos los módulos.
  - 58 enlaces de referencias cruzadas sincronizados con el `sidebar_label` de cada archivo destino (25 archivos).
  - Headings convertidos a *sentence case* en las rutas `fundamentos-de-proyectos/`, `introduccion-bi/` e `introduccion-visualizacion-datos/`.
  - "SCRUM" → "Scrum", "Módulos del Curso" → "Módulos del curso", "7ma edición" → "séptima edición" (PMBOK) unificados en todo el corpus.
  - Admonitions `:::tip` sin títulos redundantes ("TIP", "Tip:", "**Dato Relevante:**").
  - Vocabulario: "subruta" reemplazada por "sección" (término actual).
- Diagrama de categorías en Documentación y Requerimientos convertido a ciclo cerrado (flujo feedback del usuario y auditoría hacia la idea).
- Valores por defecto del componente `AuthorCredit` alineados a la nueva licencia (`CC BY-ND 4.0 + excepción IA`).

### Fixed

- Typos: "Imporntante" → "Importante", "Manten" → "Mantén", "comunición" → "comunicación", "utiles" → "útiles", más el doble espacio tras `:::tip` en un archivo.

### Removed

- Experimento de badges de metadata (`nivel`, `tiempoLectura`) y el swizzle `src/theme/DocItem/Content` asociado: no rendereaba en la UI y se retiró el componente `DocMeta` por completo.

## 3.0.2 - 2026-04-17

### Fixed

- Tabla Skill vs CLAUDE.md en la landing de "Colaboración con Agentes de IA": la fila "Se invoca" ahora describe que las skills se activan por `description` (con invocación explícita como alternativa), y la fila "Dónde vive" refleja la estructura oficial `skills/<nombre>/SKILL.md` en lugar de un archivo suelto.
- Typo `CancelationToken` → `CancellationToken` en el módulo de seguridad al ejecutar herramientas externas.

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
