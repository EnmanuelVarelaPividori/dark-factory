---
job: sprint-review
nombre: "[{{jira.proyecto}}] Evaluación de cierre de sprint"
cuando: semanal, día D (= {{sprint.dia_inicio}}) por la mañana; reporta 1 de cada {{sprint.duracion_semanas}} semanas
cron: "CRON_TZ={{equipo.zona_horaria}} 10 8 * * {D}"
modelo: Opus
conectores: Atlassian + acceso a los docs (repo o Project)
notificaciones: push + email
---

# Prompt

Evaluás el avance del proyecto {{proyecto.nombre}} al cierre de sprint. Objetivo: {{proyecto.objetivo}}. Fecha objetivo: {{proyecto.fecha_objetivo}}. Solo leés: no modificás tickets ni docs.

## 0. ¿Cerró un sprint?

Esta tarea corre todas las semanas, pero los sprints duran {{sprint.duracion_semanas}} semanas.

1. Buscá en Jira (cloudId {{jira.cloud_id}}, proyecto {{jira.proyecto}}) un sprint cerrado en los últimos 7 días.
2. Si Jira no tiene sprints, calculá: semanas entre {{sprint.fecha_referencia}} y hoy. Si ese número no es múltiplo de {{sprint.duracion_semanas}}, no hubo cierre.
3. Si no hubo cierre: terminá con una sola línea, "Sin cierre de sprint esta semana", y nada más.

## 1. Sprint cerrado vs. plan ({{docs.ruta}}{{docs.plan}})

- SP comprometidos, completados y arrastrados. Velocidad real vs. la planeada.
- Tickets del plan para ese sprint que no se hicieron, y tickets hechos que no estaban en el plan.

## 2. Cobertura de requisitos ({{docs.ruta}}{{docs.requerimientos}} + {{docs.backlog}})

- Por prioridad (MUST / SHOULD / COULD): requisitos totales, con story, con story Done.
- Requisitos MUST sin story o con la story pendiente: listarlos.

## 3. Pronóstico

- Velocidad promedio de los últimos 3 sprints (o los que haya).
- Sprints restantes = SP pendientes ÷ velocidad. Fecha estimada de fin = fin de este sprint + sprints restantes × {{sprint.duracion_semanas}} semanas.
- Comparar con {{proyecto.fecha_objetivo}}. Si no llega: qué recortar primero (COULD, después SHOULD), con SP y claves, hasta que la fecha cierre.

## 4. Desvíos de los docs

Decisiones visibles en tickets o PRs que contradicen los docs de planificación. Listar para que alguien actualice los docs.

## Salida en {{reportes.idioma}}

```
Evaluación de cierre — {sprint} — {{proyecto.nombre}}
Veredicto: en camino | en riesgo | fuera de plazo

Sprint: X/Y SP (velocidad real Z vs. plan W)
Requisitos MUST: a/b hechos · SHOULD: c/d · COULD: e/f
Fin estimado: {fecha} (objetivo {{proyecto.fecha_objetivo}})

Riesgos
- …
Recorte propuesto (si hace falta)
- …
Docs a actualizar
- …
Actualizar en config.md: sprint.velocidad_por_persona = {valor medido}
```

Mostrá los números de entrada de cada cálculo, para que alguien pueda verificarlos.
