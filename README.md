# 10X Bootcamp

> **Fundamentos claros para personas. Skills de IA reutilizables para equipos.**

Contenido abierto y en español para desarrollar habilidades en tecnología, diseño y gestión. Redactado para aprendizaje humano y **estructurado para que agentes de IA** (Claude Code, Cursor, Codex, Antigravity, Kiro) lo conviertan en *skills* reutilizables.

**Sitio en vivo:** [bootcamp.10x.gt](https://bootcamp.10x.gt) · **Autor:** [10X de Guatemala, S.A.](https://www.10x.gt) · **Licencia:** [CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0/deed.es) + excepción IA

---

## Rutas de aprendizaje

| # | Ruta | De qué trata |
|---|------|--------------|
| 1 | [Desarrollo Web y Móvil](./docs/desarrollo-web-y-movil) | .NET Core Web API, REST, SonarQube + SCA, seguridad en APIs y SPAs, modernización de sistemas legacy. |
| 2 | [Inteligencia de Negocio](./docs/inteligencia-negocio) | Fundamentos de BI y visualización para respaldar decisiones con datos. |
| 3 | [Diseño UX/UI](./docs/diseno-ux-ui) | Diseño centrado en la persona usuaria: interfaces intuitivas, accesibles y consistentes. |
| 4 | [Gestión de Proyectos](./docs/gestion-proyectos) | Planificar, coordinar y ejecutar con metodologías ágiles y buenas prácticas. |
| 5 | [Documentación y Requerimientos](./docs/documentacion-y-requerimientos) | De la idea al release: requerimientos versionados, manuales, SemVer en equipos, trazabilidad. |
| 6 | [Colaboración con Agentes de IA](./docs/colaboracion-con-agentes-ia) | Context engineering (`CLAUDE.md` / `AGENTS.md`), arquitectura orientada a skills, seguridad al ejecutar herramientas externas, diseño de prompts, seguridad de chatbots. |

Cada módulo termina con un **bloque estructurado** (objetivo, entradas, pasos, salidas, errores comunes) pensado para que un agente lo parsee y genere una skill a partir del texto.

---

## Cómo usar este contenido

### 📖 Para estudiar

- **Mejor experiencia:** [bootcamp.10x.gt](https://bootcamp.10x.gt) — incluye navegación lateral, diagramas Mermaid renderizados, búsqueda y modo oscuro.
- **Alternativa:** leer los `.md` directamente aquí en GitHub.

### 🤖 Para trabajar con agentes de IA

Clona un módulo específico al repositorio de tu proyecto y pídele al agente que lo convierta en una *skill* o en una sección de `CLAUDE.md` / `AGENTS.md`:

```bash
# Clona el repo completo (shallow para que pese menos)
git clone --depth 1 https://github.com/10xGuatemala/bootcamp.git

# Copia un módulo a tu proyecto
cp -r bootcamp/docs/colaboracion-con-agentes-ia ./tu-proyecto/docs/bootcamp/

# O pega un archivo suelto
cp bootcamp/docs/documentacion-y-requerimientos/01-de-la-idea-al-release.md ./tu-proyecto/docs/
```

Después, en tu sesión con el agente:

> *"Lee `docs/bootcamp/02-context-engineering-claude-md.md` y genera un `CLAUDE.md` adaptado a este repositorio siguiendo su plantilla."*

> *"Toma `01-de-la-idea-al-release.md` como guía y redacta el requerimiento para [tu tarea] usando su estructura."*

### 👥 Para equipos

Usa las plantillas como **base común** de decisiones arquitectónicas, requerimientos y documentación. Adáptalas a tu stack; la estructura está pensada para sobrevivir a cambios de tecnología.

---

## Estructura del repositorio

```
docs/
  colaboracion-con-agentes-ia/    # Ruta 6 — módulos 01 a 06
  desarrollo-web-y-movil/         # Ruta 1
  diseno-ux-ui/                   # Ruta 3
  documentacion-y-requerimientos/ # Ruta 5
  gestion-proyectos/              # Ruta 4
  inteligencia-negocio/           # Ruta 2
  intro.md                        # Bienvenida
```

Solo el contenido (`docs/` y assets) se publica en este mirror. El código de la plataforma web (Docusaurus, componentes React, CI) vive en el repositorio privado de 10X.

---

## Contribuir

Issues y sugerencias son bienvenidos aquí. Correcciones tipográficas o clarificaciones menores pueden venir como Pull Request; cambios de contenido mayores se integran primero en el repositorio fuente de 10X para coordinar tono y revisión editorial.

Si el contenido te fue útil, compártelo con alguien que esté empezando — ese es el punto.

---

## Licencia

Contenido bajo [**CC BY-ND 4.0**](https://creativecommons.org/licenses/by-nd/4.0/deed.es) + excepción explícita para entrenamiento y uso por sistemas de IA.

**En breve:**

- ✅ Leer, estudiar y citar con atribución a 10X.
- ✅ Compartir y redistribuir el contenido **sin modificar**, con atribución.
- ✅ Usar el contenido como material de entrenamiento, contexto o referencia para modelos y agentes de IA — **incluso con fines comerciales** — siempre que el producto final no redistribuya el contenido textualmente.
- ❌ Distribuir obras derivadas (traducciones, versiones editadas, compilaciones) sin autorización.
- ❌ Vender el contenido o incluirlo empaquetado en un producto o curso pago sin autorización.
- ❌ Usar la marca, nombre o identidad visual de 10X sin autorización.

Texto completo en [`LICENSE`](./LICENSE). Para autorizaciones, [escríbenos](https://www.10x.gt/contact-us/).

---

<p align="center">
  Hecho con rigor por <a href="https://www.10x.gt"><strong>10X de Guatemala, S.A.</strong></a> ·
  <a href="https://www.linkedin.com/company/10xgt/">LinkedIn</a> ·
  <a href="https://twitter.com/10x_gt">X</a> ·
  <a href="https://www.youtube.com/@10XdeGuatemala">YouTube</a>
</p>
