# Job: fábrica — implementar

Modelo sugerido: Sonnet. Necesita push al repo del proyecto. Nunca mergees. Un solo trabajo por corrida.

## 1. Contexto

1. Leé `CLAUDE.md` (importa `factory/proyecto/config.md` y `factory/reglas.md`).
2. Usá la skill `dark-factory:factory-implement` para todo lo que sigue. Si la sesión no tiene el plugin, leé la skill de https://github.com/EnmanuelVarelaPividori/dark-factory, archivo `plugins/dark-factory/skills/factory-implement/SKILL.md`, y seguila igual.

## 2. Elegir un solo trabajo

En este orden:

1. Un PR abierto con `factory:changes`: corregí los hallazgos del último review.
2. Si no hay: el ticket de `jira.proyecto` con `factory:go`, sin `factory:wip`, sin `factory:blocked` y sin PR abierto. Primero por prioridad de Jira, después por antigüedad.
3. Si no hay ninguno: terminá con una línea, "Fábrica <jira.proyecto>: sin trabajo".

## 3. Trabajar

1. Poné `factory:wip` en el ticket. Sacala al terminar, también si fallás.
2. Seguí la skill: pre-flight, implementación, tests (`repo.tests`), PR; o corrección de hallazgos.
3. Al terminar, el PR queda con `factory:review`, o el ticket con `factory:blocked` o `factory:needs-human`.

## Salida

En `reportes.idioma`, máximo 5 líneas: ticket, qué pasó, link al PR. Si el resultado necesita a una persona (blocked, needs-human), empezá con "ATENCIÓN".
