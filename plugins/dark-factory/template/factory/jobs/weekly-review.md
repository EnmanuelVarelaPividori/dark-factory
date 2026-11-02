# Job: review de mitad de sprint

Modelo sugerido: Sonnet. Actuás como scrum master. Solo lectura: no modifiques tickets.

## Contexto

Leé `factory/proyecto/config.md` (Jira, equipo, sprint, reglas). Capacidad = `equipo.cantidad` × `sprint.velocidad_por_persona` SP.

## Datos

En Jira (`jira.cloud_id`, `jira.proyecto`), con JQL acotada y máximo 100 por consulta:

- Sprint activo: tickets, estado, SP, responsable, fechas de cambio de estado.
- Completados y agregados al sprint en los últimos 7 días.
- Bloqueados, estancados (más de `reglas.dias_estancado` días hábiles sin cambio) y sin dueño.
- Fábrica: tickets con `factory:go` pendientes, PRs en `factory:review`, `factory:changes`, `factory:needs-human`; tickets `factory:blocked`.

## Análisis

1. **Posición:** día hábil `d` de `sprint.dias_habiles` y semana `s` de `sprint.duracion_semanas`.
2. **Burndown:** SP Done vs. esperados a esta altura (lineal: comprometidos × d / `sprint.dias_habiles`). Pronóstico: llega / en riesgo / no llega.
3. **Flujo:** dónde se acumula el trabajo (To Do, In Progress, Review).
4. **Alcance:** SP agregados después del inicio y por quién.
5. **Calidad del backlog:** % del sprint con `factory:ready`; tickets que parecen bitácora (creados ya en Done o con título de tarea hecha).
6. **Fábrica:** tickets procesados, rondas de review promedio, casos escalados.

En la **última semana** del sprint (`s` = `sprint.duracion_semanas`), las recomendaciones apuntan a cerrar: qué terminar, qué sacar ahora y qué preparar para la planning.

## Salida

En `reportes.idioma`, máximo 30 líneas:

```
Review de mitad de sprint <jira.proyecto> — semana <s>/<N>, día <d>/<dias_habiles>
Pronóstico: llega | en riesgo | no llega (Done X / esperado Y / comprometido Z SP)

Números clave
- …

3 recomendaciones
1. <acción concreta, quién, sobre qué tickets>
2. …
3. …
```

Las recomendaciones son acciones ("sacar ABC-12 y ABC-15 del sprint", "pareo en el review de X"), no principios genéricos de scrum.
