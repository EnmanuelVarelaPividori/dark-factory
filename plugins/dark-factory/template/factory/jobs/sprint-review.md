# Job: evaluación de cierre de sprint

Modelo sugerido: Opus. Solo lectura: no modifiques tickets ni docs.

## 0. ¿Cerró un sprint?

Leé `factory/proyecto/config.md`. Esta tarea corre todas las semanas, pero los sprints duran `sprint.duracion_semanas` semanas.

1. Buscá en Jira (`jira.cloud_id`, `jira.proyecto`) un sprint cerrado en los últimos 7 días.
2. Si Jira no tiene sprints: semanas entre `sprint.fecha_referencia` y hoy. Si no es múltiplo de `sprint.duracion_semanas`, no hubo cierre.
3. Si no hubo cierre: terminá con una sola línea, "Sin cierre de sprint esta semana".

## 1. Sprint cerrado vs. plan

Con `factory/proyecto/docs/03-planificacion-sprints.md`:

- SP comprometidos, completados y arrastrados. Velocidad real vs. planeada.
- Tickets del plan para ese sprint que no se hicieron, y hechos que no estaban en el plan.

## 2. Cobertura de requisitos

Con `factory/proyecto/docs/01-analisis-requerimientos.md` y `02-product-backlog.md`:

- Por prioridad (MUST / SHOULD / COULD): requisitos totales, con story, con story Done.
- Requisitos MUST sin story o con la story pendiente: listarlos.

## 3. Pronóstico

- Velocidad promedio de los últimos 3 sprints (o los que haya).
- Sprints restantes = SP pendientes ÷ velocidad. Fin estimado = fin de este sprint + sprints restantes × `sprint.duracion_semanas` semanas.
- Comparar con `proyecto.fecha_objetivo`. Si no llega: qué recortar primero (COULD, después SHOULD), con SP y claves, hasta que la fecha cierre.

## 4. Desvíos de los docs

Decisiones visibles en tickets o PRs que contradicen `factory/proyecto/docs/`. Listar para que alguien actualice los docs.

## Salida

En `reportes.idioma`:

```
Evaluación de cierre — <sprint> — <proyecto.nombre>
Veredicto: en camino | en riesgo | fuera de plazo

Sprint: X/Y SP (velocidad real Z vs. plan W)
Requisitos MUST: a/b hechos · SHOULD: c/d · COULD: e/f
Fin estimado: <fecha> (objetivo <proyecto.fecha_objetivo>)

Riesgos
- …
Recorte propuesto (si hace falta)
- …
Docs a actualizar
- …
Actualizar en factory/proyecto/config.md: sprint.velocidad_por_persona = <valor medido>
```

Mostrá los números de entrada de cada cálculo para que se puedan verificar.
