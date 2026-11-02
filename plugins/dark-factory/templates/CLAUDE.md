# {{proyecto.nombre}}

<!-- Plantilla Dark Factory. Reemplazar {{...}} con los valores de factory/config.md. -->

## Configuración de la fábrica

@factory/config.md

## Contexto

- Objetivo: {{proyecto.objetivo}}
- Docs de planificación (fuente de verdad del alcance): `{{docs.ruta}}`
  - Requerimientos con IDs `R-x.y`: `{{docs.ruta}}{{docs.requerimientos}}`
  - Backlog: `{{docs.ruta}}{{docs.backlog}}` · Plan: `{{docs.ruta}}{{docs.plan}}`

Antes de implementar un ticket, leé la story del backlog y los requisitos que referencia. Si el ticket contradice los docs, no elijas en silencio: comentalo en el PR y en el ticket.

## Stack y convenciones

{{repo.stack}}

- Rama: `{{repo.nombre_rama}}` desde `{{repo.rama_base}}`
- Commits: `{{repo.commit}}`
- Tests: `{{repo.tests}}`. Un PR sin tests para su lógica nueva no está terminado.

## Reglas de la fábrica

- Solo se trabajan tickets con `factory:ready` y `factory:go`. Si falta algo del Definition of Ready, comentá qué falta y no programes.
- Un ticket = una rama = un PR. No mezclar tickets.
- No cambiar alcance: lo que no está en los criterios de aceptación no se hace. Si hace falta, se propone como ticket nuevo.
- Nunca mergear. El merge lo hace una persona.
- Skills del plugin `dark-factory` (activado en `.claude/settings.json`): `dark-factory:factory-implement` para implementar, `dark-factory:factory-review` para revisar, `dark-factory:ticket-quality` para crear tickets.
- Playbook y reglas completas: https://github.com/EnmanuelVarelaPividori/dark-factory
