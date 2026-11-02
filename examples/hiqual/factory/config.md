---
doc: factory-config
proyecto: HiQual P1
descripcion: Configuración Dark Factory de HiQual. Ejemplo completo de config.md. Las filas marcadas COMPLETAR no se pudieron sacar del repo ni de los docs.
---

# Configuración del proyecto — HiQual P1

## Proyecto

| Clave | Valor |
|---|---|
| `proyecto.nombre` | HiQual P1 — Certificación IEC 62052-31:2024 |
| `proyecto.objetivo` | Que HiQual enfrente su primera auditoría de certificación IEC 62052-31:2024 con el sistema funcionando y la información ordenada |
| `proyecto.fecha_objetivo` | 2027-02-28 |
| `docs.ruta` | `docs/p1-iec-62052-31/` |
| `docs.requerimientos` | `01-analisis-requerimientos.md` |
| `docs.backlog` | `02-product-backlog.md` |
| `docs.plan` | `03-planificacion-sprints.md` |

## Jira

| Clave | Valor |
|---|---|
| `jira.sitio` | giisoftware.atlassian.net |
| `jira.cloud_id` | 19e2180c-a658-441f-b9a7-f1cbfe61361a |
| `jira.proyecto` | HIQ |
| `jira.board_id` | COMPLETAR (HIQ hoy no usa sprints en Jira) |
| `jira.tipos` | Story 10078 · Epic 10079 · Task 10076 · Bug 10077 · Subtask 10080 |
| `jira.campo_sp` | customfield_10016 |
| `jira.campo_sprint` | customfield_10020 |
| `jira.prioridad` | MUST → 2 (High) · SHOULD → 3 (Medium) · COULD → 4 (Low) |
| `jira.prefijos_titulo` | `[BE]`, `[FE]` |

## Equipo y sprint

| Clave | Valor |
|---|---|
| `equipo.personas` | COMPLETAR (cuentas de Jira de quienes desarrollan) |
| `equipo.cantidad` | 1 |
| `equipo.zona_horaria` | Europe/Amsterdam |
| `equipo.hora_daily` | 09:30 |
| `sprint.duracion_semanas` | 2 |
| `sprint.dia_inicio` | lunes |
| `sprint.fecha_referencia` | COMPLETAR (inicio real de un sprint; ej. 2026-11-02) |
| `sprint.dias_habiles` | 10 |
| `sprint.velocidad_por_persona` | 25 (planificada; reemplazar por la medida) |

## Reglas

| Clave | Valor |
|---|---|
| `reglas.wip_por_persona` | 2 |
| `reglas.sp_max_ticket` | 5 |
| `reglas.lote_confirmacion` | 5 |
| `reglas.dias_estancado` | 3 |

## Repo y fábrica

| Clave | Valor |
|---|---|
| `repo.url` | https://github.com/EnmanuelVarelaPividori/hiqual |
| `repo.rama_base` | develop |
| `repo.nombre_rama` | `claude/{jira_key}-{slug}` (ej. `claude/HIQ-89-dashboard-inicial`) |
| `repo.commit` | `{jira_key}: {resumen en imperativo}` (ej. `HIQ-89: Add certification progress widget`) |
| `repo.tests` | `ddev exec bash scripts/test-kernel.sh` · theme: `npm --prefix web/themes/custom/hiqual_portal run typecheck && npm --prefix web/themes/custom/hiqual_portal test` |
| `repo.stack` | Drupal 11 (core-recommended ^11.4) · PHP 8.4 · DDEV · módulos custom `hiqual_core`, `hiqual_app`, `hiqual_demo` · theme `hiqual_portal` · landing aparte en `apps/landing/` (fuera del alcance de la fábrica). Preferir módulos contrib maduros y entidades/configuración antes que código custom. |
| `fabrica.disparo` | A (etiqueta + tarea programada) |
| `fabrica.rondas_review` | 3 |

## Reportes

| Clave | Valor |
|---|---|
| `reportes.canales` | push + email |
| `reportes.idioma` | español |
