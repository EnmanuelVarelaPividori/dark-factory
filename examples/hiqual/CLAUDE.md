# HiQual P1 — Certificación IEC 62052-31:2024

## Configuración de la fábrica

@factory/config.md

## Contexto

- Objetivo: Que HiQual enfrente su primera auditoría de certificación IEC 62052-31:2024 con el sistema funcionando y la información ordenada
- Docs de planificación (fuente de verdad del alcance): `docs/p1-iec-62052-31/`
  - Requerimientos con IDs `R-x.y`: `docs/p1-iec-62052-31/01-analisis-requerimientos.md`
  - Backlog: `docs/p1-iec-62052-31/02-product-backlog.md` · Plan: `docs/p1-iec-62052-31/03-planificacion-sprints.md`

Antes de implementar un ticket, leé la story del backlog y los requisitos que referencia. Si el ticket contradice los docs, no elijas en silencio: comentalo en el PR y en el ticket.

## Stack y convenciones

- Drupal 11 (`drupal/core-recommended` ^11.4), PHP 8.4, entorno local con DDEV.
- Código propio: módulos `hiqual_core`, `hiqual_app`, `hiqual_demo` (`web/modules/custom/`) y theme `hiqual_portal` (`web/themes/custom/`).
- `apps/landing/` es otra aplicación (landing pública): fuera del alcance de la fábrica.
- Preferir módulos contrib maduros y entidades/configuración antes que código custom.

- Rama: `claude/{jira_key}-{slug}` desde `develop` (ej. `claude/HIQ-89-dashboard-inicial`)
- Commits: `{jira_key}: {resumen en imperativo}` (ej. `HIQ-89: Add certification progress widget`)
- Tests: `ddev exec bash scripts/test-kernel.sh`. Si se toca el theme: `npm --prefix web/themes/custom/hiqual_portal run typecheck && npm --prefix web/themes/custom/hiqual_portal test`. Un PR sin tests para su lógica nueva no está terminado.

## Reglas de la fábrica

- Solo se trabajan tickets con `factory:ready` y `factory:go`. Si falta algo del Definition of Ready, comentá qué falta y no programes.
- Un ticket = una rama = un PR. No mezclar tickets.
- No cambiar alcance: lo que no está en los criterios de aceptación no se hace. Si hace falta, se propone como ticket nuevo.
- Nunca mergear. El merge lo hace una persona.
- Skills del plugin `dark-factory` (activado en `.claude/settings.json`): `dark-factory:factory-implement` para implementar, `dark-factory:factory-review` para revisar, `dark-factory:ticket-quality` para crear tickets.
- Playbook y reglas completas: https://github.com/EnmanuelVarelaPividori/dark-factory
