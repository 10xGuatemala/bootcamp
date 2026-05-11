<!-- Destino sugerido: <repo-migracion>/forms-migration/README.md -->

# Plantillas para migración desde Oracle Forms

Conjunto de artefactos copiables que un agente de migración Forms produce y mantiene a lo largo del proyecto. Las plantillas acompañan los módulos:

- [1.4.3 Migración desde Oracle Forms](../../../docs/desarrollo-web-y-movil/modernizacion-legacy/03-migracion-desde-oracle-forms.md) — estrategia y matriz 10X de stack destino.
- [1.4.4 Inventario y extracción desde Oracle Forms](../../../docs/desarrollo-web-y-movil/modernizacion-legacy/04-inventario-y-extraccion-forms.md) — inventario cruzado, clasificación de triggers, ciclo de migración.
- [1.4.5 Consultas parametrizadas en migraciones](../../../docs/desarrollo-web-y-movil/modernizacion-legacy/05-consultas-parametrizadas-en-migracion.md) — práctica concreta para todo el código nuevo.

Y los skills:

- [`migracion-forms.skill.md`](../../agents/skills/oracle-forms/migracion-forms.skill.md) — playbook completo.
- [`consultas-parametrizadas-plsql.skill.md`](../../agents/skills/oracle-forms/consultas-parametrizadas-plsql.skill.md) — remediación de SQL dinámico.

## Cómo usar este paquete

1. Copia la carpeta `forms-migration/` a la raíz del repositorio del proyecto de migración.
2. Carga el slash command `migrar-forms.slash-command.md` en `.claude/commands/` (o equivalente del agente que uses).
3. Activa el flujo con `/migrar-forms` — el agente valida precondiciones y arranca el inventario.
4. Mantén las plantillas vivas: cada pantalla migrada, cada `.pll` retirado, cada paquete heredado actualiza el artefacto correspondiente en el mismo PR.

## Estructura

```
forms-migration/
├── README.md                            # Este archivo
├── migrar-forms.slash-command.md        # Activador para Claude Code / Cursor / Codex
├── scripts-inventario.md                # Bloques SQL y bash listos para extraer
├── scripts-reconciliacion.md            # Esqueleto del job de reconciliación diaria
├── inventario-fuentes-forms.md          # Plantilla: archivos .fmb/.pll/.mmb/.rdf
├── inventario-objetos-bd.md             # Plantilla: paquetes, procedimientos, vistas
├── triggers-clasificados.md             # Plantilla: clasificación de triggers
├── contrato-pantalla.md                 # Plantilla por pantalla a migrar
├── backlog-migracion-pantallas.md       # Cola priorizada de pantallas
└── plan-retiro-legacy.md                # Plan de cierre del proyecto
```

## Convenciones

- **Markdown vivo.** Las plantillas se editan en el mismo PR del cambio que documentan; no son entregables congelados.
- **Una fuente de verdad por dato.** El inventario de archivos vive en `inventario-fuentes-forms.md`; no se duplica en el backlog ni en el plan de retiro — se enlaza.
- **Identificadores reproducibles.** Cada pantalla tiene un id estable (`form-id`) que aparece en `contrato-pantalla.md`, en el backlog y en commits.
- **Diff-friendly.** Tablas con una fila por entidad, ordenadas alfabéticamente, para que `git diff` sea legible.
