---
doc: factory-config
descripcion: Configuración Dark Factory de este proyecto. Lo leen las skills, los jobs y CLAUDE.md. Completar todas las filas; lo que no se sepa queda como COMPLETAR.
---

# Configuración del proyecto

## Proyecto

| Clave | Valor | Qué va |
|---|---|---|
| `proyecto.nombre` | COMPLETAR | Nombre del proyecto |
| `proyecto.objetivo` | COMPLETAR | Una frase: contra qué se mide toda priorización |
| `proyecto.fecha_objetivo` | COMPLETAR | AAAA-MM-DD |

## Jira

| Clave | Valor | Qué va |
|---|---|---|
| `jira.sitio` | COMPLETAR | Ej. `miempresa.atlassian.net` |
| `jira.cloud_id` | COMPLETAR | UUID del sitio |
| `jira.proyecto` | COMPLETAR | Clave del proyecto, ej. `ABC` |
| `jira.board_id` | COMPLETAR | Board con los sprints; `—` si no se usan sprints en Jira |
| `jira.tipos` | COMPLETAR | IDs de Story / Epic / Task / Bug / Subtask |
| `jira.campo_sp` | COMPLETAR | Campo de story points, ej. `customfield_10016` |
| `jira.campo_sprint` | COMPLETAR | Campo de sprint, ej. `customfield_10020` |
| `jira.prioridad` | COMPLETAR | MUST / SHOULD / COULD → id de prioridad |
| `jira.prefijos_titulo` | — | Prefijos de capa en el título, si se usan (ej. `[BE]`, `[FE]`) |

## Equipo y sprint

| Clave | Valor | Qué va |
|---|---|---|
| `equipo.personas` | COMPLETAR | Cuentas de Jira de quienes desarrollan |
| `equipo.cantidad` | COMPLETAR | Cantidad de personas que desarrollan |
| `equipo.zona_horaria` | COMPLETAR | Zona IANA, ej. `Europe/Amsterdam` |
| `equipo.hora_daily` | COMPLETAR | HH:MM |
| `sprint.duracion_semanas` | COMPLETAR | N. Define los horarios de las tareas programadas |
| `sprint.dia_inicio` | COMPLETAR | Día de la semana en que arranca cada sprint |
| `sprint.fecha_referencia` | COMPLETAR | Fecha de inicio de cualquier sprint real (ancla para contar ciclos) |
| `sprint.dias_habiles` | COMPLETAR | N × 5 |
| `sprint.velocidad_por_persona` | COMPLETAR | SP por persona y sprint. Medida, no deseada; actualizar en cada cierre |

## Reglas

| Clave | Valor | Qué va |
|---|---|---|
| `reglas.wip_por_persona` | 2 | Tickets In Progress por persona |
| `reglas.sp_max_ticket` | 5 | SP máximos para entrar a un sprint |
| `reglas.lote_confirmacion` | 5 | Tickets por carga que exigen confirmación |
| `reglas.dias_estancado` | COMPLETAR | Días hábiles sin cambio = estancado. Regla: `max(2, N + 1)` |

## Repo

| Clave | Valor | Qué va |
|---|---|---|
| `repo.url` | COMPLETAR | URL del repo |
| `repo.rama_base` | COMPLETAR | Rama a la que van los PRs |
| `repo.nombre_rama` | COMPLETAR | Patrón, ej. `{jira_key}-{slug}` |
| `repo.commit` | COMPLETAR | Convención, ej. `{jira_key}: {resumen}` |
| `repo.tests` | COMPLETAR | Comandos de tests (los mismos que corre CI) |
| `repo.stack` | COMPLETAR | Lo que el implementador tiene que saber: lenguaje, framework, carpetas de código propio, qué queda fuera de alcance, preferencias (ej. librerías antes que código propio) |

## Fábrica y reportes

| Clave | Valor | Qué va |
|---|---|---|
| `fabrica.disparo` | A | A: etiqueta + tarea programada · B: automatización |
| `fabrica.rondas_review` | 3 | Rondas máximas de review antes de `factory:needs-human` |
| `reportes.canales` | push + email | push, email, comentario en Jira |
| `reportes.idioma` | español | Idioma de reportes y comentarios |
