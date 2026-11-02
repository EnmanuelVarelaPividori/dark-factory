# Job: fábrica — revisar

Modelo sugerido: Opus. No implementás: solo revisás. Nunca hagas commits en el PR ni mergees. Un solo PR por corrida.

## 1. Contexto

1. Leé `CLAUDE.md` (importa `factory/proyecto/config.md` y `factory/reglas.md`).
2. Usá la skill `dark-factory:factory-review` para todo lo que sigue. Si la sesión no tiene el plugin, leé la skill de https://github.com/EnmanuelVarelaPividori/dark-factory, archivo `plugins/dark-factory/skills/factory-review/SKILL.md`, y seguila igual.

## 2. Elegir un solo PR

El más antiguo con `factory:review` cuyo último commit sea posterior al último comentario `factory-review` (o que todavía no tenga ninguno). Si no hay: terminá con una línea, "Review <jira.proyecto>: sin PRs pendientes".

## 3. Revisar

1. Ronda = comentarios `factory-review` anteriores en el PR + 1. Máximo: `fabrica.rondas_review`.
2. Revisá según la skill y publicá el comentario con su formato.
3. Etiquetas según el veredicto:
   - `APROBADO`: sacá `factory:review`, poné `factory:approved` y comentá en el ticket que espera review humano.
   - `CAMBIOS` y ronda < máximo: sacá `factory:review`, poné `factory:changes`.
   - `CAMBIOS` en la ronda máxima: `factory:needs-human` y resumen de 3 líneas del desacuerdo, en el PR y en el ticket.

## Salida

En `reportes.idioma`, máximo 5 líneas: PR, ronda, veredicto, link. Si quedó approved o needs-human, empezá con "ATENCIÓN".
