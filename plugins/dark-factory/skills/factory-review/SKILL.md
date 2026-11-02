---
name: factory-review
description: Revisar un PR de la fábrica contra su ticket de Jira, los requisitos y las convenciones del repo, en una sesión independiente de quien lo implementó, y devolver APROBADO o CAMBIOS con hallazgos numerados. Usar con "revisá el PR X", cuando un PR tiene factory:review, o en cada ronda del ciclo revisar ↔ corregir.
---

# Factory review

Revisás como alguien que no vio cómo se escribió el código. No confíes en la descripción del PR: verificá contra el diff.

## 1. Juntar el contexto

1. `CLAUDE.md`, `factory/ESTRUCTURA.md`, `factory/reglas.md` y `factory/proyecto/config.md`.
2. El ticket: criterios de aceptación, requisitos `R-x.y`, tipo (núcleo / config / cliente).
3. La story en `factory/proyecto/docs/02-product-backlog.md` y los requisitos citados en `factory/proyecto/docs/01-analisis-requerimientos.md`.
4. El diff completo y los tests. Si podés, corré `repo.tests`.
5. Las rondas anteriores de review en el PR: no repitas hallazgos ya resueltos ni cambies de criterio entre rondas.

## 2. Revisar, en este orden

| # | Pregunta | Bloquea |
|---|---|---|
| 1 | ¿Cada criterio de aceptación está cumplido? Verificalo en código o test, uno por uno. | Sí |
| 2 | ¿El PR hace algo que el ticket no pide? (alcance extra) | Sí |
| 3 | ¿Respeta los docs y requisitos? ¿Contradice alguna regla de negocio? | Sí |
| 4 | ¿Hay tests para la lógica nueva y pasan? | Sí |
| 5 | ¿Rompe algo existente: datos, permisos, aislamiento multiempresa, trazabilidad? | Sí |
| 6 | ¿Reutiliza lo que ya existe o abre un camino paralelo? | Sí, si duplica un flujo |
| 7 | Convenciones del repo, legibilidad, nombres | No (sugerencia) |

Solo reportá hallazgos con un escenario concreto de falla ("con X, pasa Y"). Nada de opiniones de estilo como bloqueantes.

## 3. Responder

Comentario en el PR con este formato exacto:

```markdown
## factory-review — ronda {n}/{max}
**Veredicto:** APROBADO | CAMBIOS

### Criterios de aceptación
- CA1 ✅ verificado en `tests/…`
- CA2 ❌ <qué falta>

### Hallazgos
1. **[bloquea]** `archivo:línea` — <problema>. Escenario: <cómo falla>. Sugerencia: <qué hacer>.
2. **[sugerencia]** …
```

- `APROBADO` solo si no hay ningún hallazgo `[bloquea]`. Poné la etiqueta `factory:approved` y avisá en el ticket que espera review humano.
- `CAMBIOS`: cambiá `factory:review` por `factory:changes` y devolvé el turno a `factory-implement`.
- En la ronda máxima sin aprobación: `factory:needs-human` y un resumen de 3 líneas del desacuerdo.

## Nunca

- Aprobar con criterios de aceptación sin verificar.
- Pedir cambios fuera del alcance del ticket (proponé un ticket nuevo).
- Hacer los cambios vos mismo: el reviewer no implementa.
