<!-- Destino sugerido: raíz del repo nuevo como specs.md -->

# specs.md — {{nombre del proyecto}}

> **Contrato** que resulta de la entrevista de especificación. Todo lo que aparece aquí es una decisión tomada, no una propuesta. Si una sección dice "por decidir", el proyecto no está listo para scaffolding.

**Fecha:** YYYY-MM-DD
**Responsable:** @persona
**Versión del contrato:** 0.1.0

---

## 1. Objetivo del proyecto

En 2–4 frases, qué problema resuelve y para quién. Sin jerga técnica interna.

## 2. Stack

| Capa | Tecnología | Versión | Justificación breve |
|------|-----------|---------|---------------------|
| Runtime | {{Node / .NET / Python / ...}} | {{X.Y}} | |
| Lenguaje | {{TS / C# / Python ...}} | {{X.Y}} | |
| Framework | {{React / Next / ASP.NET / ...}} | {{X.Y}} | |
| Estilos | {{Tailwind / CSS Modules / ...}} | | |
| Estado | {{Zustand / Redux / Context / ninguno}} | | |
| Persistencia | {{PostgreSQL / MySQL / SQLite / ninguna}} | | |
| Auth | {{JWT / OAuth / ninguno}} | | |
| Package manager | {{pnpm / npm / yarn / NuGet / pip}} | {{X.Y}} | |

## 3. Estructura de carpetas

El árbol exacto que se va a crear. Sin opciones, sin "o algo así".

```
{{nombre-proyecto}}/
├── {{carpeta1}}/
│   └── ...
├── {{carpeta2}}/
│   └── ...
└── ...
```

Ejemplo (Atomic Design):
```
src/
├── atoms/
├── molecules/
├── organisms/
├── store/
└── ...
```

## 4. Convenciones

- **Naming de archivos:** {{kebab-case / PascalCase / snake_case}} para cada tipo.
- **Exports:** {{named / default / mixto con regla explícita}}.
- **Imports:** {{relativos / alias con prefijo `@/`}}.
- **Tests:** {{ubicación (`__tests__/` vs. colocated), framework, naming `*.test.ts`}}.
- **Commits:** {{Conventional Commits con estos tipos: feat, fix, chore, docs, refactor}}.

## 5. Scripts acordados

Los comandos que debe exponer `package.json` / `Makefile` / equivalente:

```
{{dev}}      - Arranca desarrollo local.
{{build}}    - Genera el artefacto productivo.
{{test}}     - Corre la suite de tests.
{{lint}}     - Linter/formatter en modo verificación.
{{format}}   - Linter/formatter en modo corrección.
```

## 6. Dependencias iniciales

Lista exacta. Si una dependencia aún no se conoce versión fijada, queda pendiente y el scaffolding no arranca.

```
# Producción
{{paquete}} @ {{versión exacta o rango}}

# Desarrollo
{{paquete}} @ {{versión exacta o rango}}
```

## 7. Decisiones cerradas

Lista de las preguntas de la entrevista con su respuesta. Formato: una decisión por línea.

- **Monorepo o repo único?** {{repo único}}.
- **Testing framework?** {{Vitest}}.
- **Lint?** {{ESLint + Prettier}}.
- **CI/CD?** {{GitHub Actions, workflow `.github/workflows/ci.yml` con lint + test + build}}.
- **Licencia?** {{MIT / propietaria / sin licencia}}.
- ...

## 8. Fuera de alcance (v0)

Lo que **no** se va a crear ahora aunque haya sido discutido. Evita scope creep en el scaffolding.

- {{i18n}}
- {{autenticación completa}}
- {{CMS}}

## 9. Historial del contrato

- **0.1.0 (YYYY-MM-DD):** primera versión aprobada. Scaffolding procede.
- **0.2.0 (YYYY-MM-DD):** {{cambio al contrato tras primer sprint, justificación}}.

---

**Aprobación:** este documento queda aprobado cuando @{{responsable}} confirma por escrito. Solo entonces se ejecuta el scaffolding.
