---
name: factory-implement
description: Implementar en Claude Code un ticket de Jira marcado factory:ready + factory:go, desde el ticket y los docs del repo hasta un PR con la checklist de criterios de aceptación, y corregir hallazgos de factory-review. Usar cuando una tarea programada o una persona pide "tomá el ticket X", "procesá la cola de la fábrica" o "corregí los hallazgos del review".
---

# Factory implement

## 1. Pre-flight (no escribas código hasta pasarlo)

1. Leé `CLAUDE.md`, `factory/ESTRUCTURA.md`, `factory/reglas.md` y `factory/proyecto/config.md`.
2. Traé el ticket: resumen, descripción, criterios, etiquetas, dependencias y estado.
3. Verificá:
   - [ ] Tiene `factory:ready` y `factory:go`.
   - [ ] Los criterios de aceptación son verificables.
   - [ ] Las dependencias bloqueantes están Done (o mergeadas).
   - [ ] ≤5 SP.
   - [ ] No hay otro PR abierto para el mismo ticket.
   - [ ] Los docs del Project marcados **obligatorio** en la sección Contexto se pueden leer (ver paso 2), o el ticket resume lo necesario.
4. Si algo falla: comentá en el ticket qué falta, poné la etiqueta `factory:blocked` y terminá. No adivines requisitos.

## 2. Contexto

- Leé la story en `factory/proyecto/docs/02-product-backlog.md` y los requisitos `R-x.y` que cita en `factory/proyecto/docs/01-analisis-requerimientos.md`.
- Docs del Project en la sección **Contexto** del ticket: si la sesión tiene acceso al Project (herramienta Projects), leé cada uno por su ruta (`project_read`; si la ruta no existe, `project_search` con el título). Los **obligatorio** se leen siempre; los **referencia**, si el ticket deja dudas.
- Si un doc **obligatorio** no se puede leer y el ticket no alcanza para implementar sin adivinar: comentá en el ticket qué doc falta, poné `factory:blocked` y terminá.
- Si un doc del Project contradice el ticket o `factory/proyecto/docs/`: manda el ticket; comentá la contradicción en el ticket y en el PR.
- Explorá el código relacionado antes de diseñar. Reutilizá lo que existe: no abras caminos paralelos a un flujo que ya hace lo mismo.
- Si el ticket contradice los docs o el código existente, comentalo en el ticket y en el PR; no elijas en silencio.

## 3. Implementar

- Rama según `repo.nombre_rama`, desde `repo.rama_base`.
- Hacé solo lo que piden los criterios de aceptación. Lo que encuentres fuera de alcance va como nota en el PR (propuesta de ticket), no en el diff.
- Tests para la lógica nueva. Corré `repo.tests` y que pasen.
- Commits con `repo.commit`.

## 4. Abrir el PR

Cuerpo del PR:

```markdown
Ticket: {KEY} — {título}
Requisitos: R-x.y, …
Contexto leído: <docs del Project que leíste> · No accesibles: <si alguno>

## Criterios de aceptación
- [x] CA1 — <cómo se verifica: test `…`, ruta, captura>
- [x] CA2 — …
- [ ] CA3 — <si no se pudo: por qué>

## Cambios
<qué y dónde, breve>

## Fuera de alcance / notas
<desvíos, dudas, tickets propuestos>
```

Después: comentá el link del PR en el ticket, movelo a "In Review", poné `factory:review` en el PR y sacá `factory:wip` del ticket.

## 5. Corregir hallazgos del review

Cuando el PR tiene `factory:changes` (el review respondió `CAMBIOS`):

- Corregí **solo** los hallazgos numerados. No reabras otras partes.
- Respondé cada hallazgo en el PR: corregido (con commit) o rechazado (con razón concreta).
- Cambiá `factory:changes` por `factory:review` para pedir nuevo review. El contador de rondas lo lleva el PR. En la ronda `fabrica.rondas_review` sin aprobación, poné `factory:needs-human`, resumí el desacuerdo y frená.

## Nunca

- Mergear.
- Tocar tickets que no tengan `factory:go`.
- Cambiar docs de planificación para que coincidan con el código sin decirlo en el PR.
