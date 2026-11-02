---
doc: factory-config
descripcion: Variables del sistema Dark Factory para un proyecto. Copiar a config.md y completar. Lo leen las skills y los jobs.
---

# Configuración del proyecto

## Proyecto

| Clave | Valor | Ejemplo (HiQual P1) |
|---|---|---|
| `proyecto.nombre` | | HiQual P1 — Certificación IEC 62052-31:2024 |
| `proyecto.objetivo` | Una frase: contra qué se mide toda priorización | Enfrentar la primera auditoría IEC 62052-31 con el sistema funcionando |
| `proyecto.fecha_objetivo` | | 2027-02 |
| `docs.ruta` | Carpeta de los docs en el repo | `docs/p1-iec-62052-31/` |
| `docs.requerimientos` | Archivo con IDs `R-x.y` | `01-analisis-requerimientos.md` |
| `docs.backlog` | | `02-product-backlog.md` |
| `docs.plan` | | `03-planificacion-sprints.md` |

## Jira

| Clave | Valor | Ejemplo |
|---|---|---|
| `jira.sitio` | | giisoftware.atlassian.net |
| `jira.cloud_id` | | 19e2180c-a658-441f-b9a7-f1cbfe61361a |
| `jira.proyecto` | Clave | HIQ |
| `jira.board_id` | | |
| `jira.tipos` | IDs de Story / Epic / Task / Bug / Subtask | 10078 / 10079 / 10076 / 10077 / 10080 |
| `jira.campo_sp` | Story points | customfield_10016 |
| `jira.campo_sprint` | | customfield_10020 |
| `jira.prioridad` | MUST / SHOULD / COULD → id | 2 / 3 / 4 |
| `jira.prefijos_titulo` | Si el equipo usa capas en el título | `[BE]`, `[FE]` |

## Equipo y sprint

| Clave | Valor | Ejemplo |
|---|---|---|
| `equipo.personas` | Nombres o cuentas de Jira | |
| `equipo.cantidad` | Personas que desarrollan (para la capacidad) | 1 |
| `sprint.duracion_semanas` | N. De acá salen los horarios de los jobs (ver `jobs/README.md`) | 2 |
| `sprint.dia_inicio` | Día en que arranca cada sprint | lunes |
| `sprint.fecha_referencia` | Inicio de un sprint real, ancla para contar ciclos | 2026-10-26 |
| `sprint.dias_habiles` | N × 5 | 10 |
| `sprint.velocidad_por_persona` | SP por sprint, medidos, no deseados. Recalcular cada sprint. | |
| `equipo.zona_horaria` | Zona IANA | Europe/Amsterdam |
| `equipo.hora_daily` | | 09:30 |
| `reglas.wip_por_persona` | | 2 |
| `reglas.dias_estancado` | `max(2, N + 1)` días hábiles | 3 |
| `reglas.sp_max_ticket` | | 5 |
| `reglas.lote_confirmacion` | Tickets por carga que exigen confirmación | 5 |

## Repo y fábrica

| Clave | Valor | Ejemplo |
|---|---|---|
| `repo.url` | | |
| `repo.rama_base` | | main |
| `repo.nombre_rama` | Patrón | `{jira_key}-{slug}` |
| `repo.commit` | Convención | `{jira_key}: {resumen}` |
| `repo.tests` | Comando | `ddev exec phpunit` |
| `repo.stack` | Notas clave para el implementador | Drupal 11, contrib antes que custom, config sobre código |
| `fabrica.disparo` | A (etiqueta + tarea programada) o B (automatización) | A |
| `fabrica.rondas_review` | | 3 |

## Reportes

| Clave | Valor | Ejemplo |
|---|---|---|
| `reportes.canales` | push, email, comentario en Jira | push + email |
| `reportes.idioma` | | español |
