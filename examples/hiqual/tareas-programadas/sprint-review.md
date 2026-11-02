---
job: sprint-review
nombre: "[HIQ] Evaluación de cierre de sprint"
cuando: semanal, día D (= lunes) por la mañana; reporta 1 de cada 2 semanas
cron: "CRON_TZ=Europe/Amsterdam 10 8 * * 1"
modelo: Opus
conectores: Atlassian + acceso a los docs (repo o Project)
notificaciones: push + email
---

# Prompt

Evaluás el avance del proyecto HiQual P1 — Certificación IEC 62052-31:2024 al cierre de sprint. Objetivo: Que HiQual enfrente su primera auditoría de certificación IEC 62052-31:2024 con el sistema funcionando y la información ordenada. Fecha objetivo: 2027-02-28. Solo leés: no modificás tickets ni docs.

## 0. ¿Cerró un sprint?

Esta tarea corre todas las semanas, pero los sprints duran 2 semanas.

1. Buscá en Jira (cloudId 19e2180c-a658-441f-b9a7-f1cbfe61361a, proyecto HIQ) un sprint cerrado en los últimos 7 días.
2. Si Jira no tiene sprints, calculá: semanas entre COMPLETAR (inicio real de un sprint; ej. 2026-11-02) y hoy. Si ese número no es múltiplo de 2, no hubo cierre.
3. Si no hubo cierre: terminá con una sola línea, "Sin cierre de sprint esta semana", y nada más.

## 1. Sprint cerrado vs. plan (docs/p1-iec-62052-31/03-planificacion-sprints.md)

- SP comprometidos, completados y arrastrados. Velocidad real vs. la planeada.
- Tickets del plan para ese sprint que no se hicieron, y tickets hechos que no estaban en el plan.

## 2. Cobertura de requisitos (docs/p1-iec-62052-31/01-analisis-requerimientos.md + 02-product-backlog.md)

- Por prioridad (MUST / SHOULD / COULD): requisitos totales, con story, con story Done.
- Requisitos MUST sin story o con la story pendiente: listarlos.

## 3. Pronóstico

- Velocidad promedio de los últimos 3 sprints (o los que haya).
- Sprints restantes = SP pendientes ÷ velocidad. Fecha estimada de fin = fin de este sprint + sprints restantes × 2 semanas.
- Comparar con 2027-02-28. Si no llega: qué recortar primero (COULD, después SHOULD), con SP y claves, hasta que la fecha cierre.

## 4. Desvíos de los docs

Decisiones visibles en tickets o PRs que contradicen los docs de planificación. Listar para que alguien actualice los docs.

## Salida en español

```
Evaluación de cierre — {sprint} — HiQual P1 — Certificación IEC 62052-31:2024
Veredicto: en camino | en riesgo | fuera de plazo

Sprint: X/Y SP (velocidad real Z vs. plan W)
Requisitos MUST: a/b hechos · SHOULD: c/d · COULD: e/f
Fin estimado: {fecha} (objetivo 2027-02-28)

Riesgos
- …
Recorte propuesto (si hace falta)
- …
Docs a actualizar
- …
Actualizar en config.md: sprint.velocidad_por_persona = {valor medido}
```

Mostrá los números de entrada de cada cálculo, para que alguien pueda verificarlos.
