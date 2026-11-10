# Migrations

Used by `factory-kickoff` in *Update* mode. Apply every entry newer than the project's `factory/VERSION`, in order. Show the full list of renames and rewrites before applying, and never commit without confirmation.

Migrations are the only thing allowed to change `factory/project/` on update, and only as described here: rename, rewrite keys, add missing files. Never drop a value.

## Before 0.5.0

If `factory/proyecto/estado.md` is missing, copy only that file from the template (it is renamed in 0.7.0).

## Before 0.6.0

`factory-setup` was merged into `factory-kickoff`. In the Dark Factory block of `CLAUDE.md`, the mention of `dark-factory:factory-setup` goes away (the 0.7.0 block replacement below covers it).

## Before 0.7.0 — everything in English

### 1. Paths (`git mv`, so history is kept)

| Old | New |
|---|---|
| `factory/proyecto/` | `factory/project/` |
| `factory/project/estado.md` | `factory/project/status.md` (rebuilt, see step 4) |
| `factory/project/docs/00-analisis-problema.md` | `factory/project/docs/00-problem-analysis.md` |
| `factory/project/docs/01-analisis-requerimientos.md` | `factory/project/docs/01-requirements.md` |
| `factory/project/docs/03-planificacion-sprints.md` | `factory/project/docs/03-delivery-plan.md` |

`reglas.md`, `ESTRUCTURA.md` and `ARQUITECTURA.md` are generic: the update removes them and adds `rules.md`, `STRUCTURE.md` and `ARCHITECTURE.md`.

### 2. Config keys

Rewrite `factory/project/config.md` from the new template, carrying each value over by key. Values stay as they were (they are the project's); only `COMPLETAR` becomes `TODO`.

| Old | New |
|---|---|
| `proyecto.nombre` | `project.name` |
| `proyecto.objetivo` | `project.goal` |
| `proyecto.fecha_objetivo` | `project.target_date` |
| `reportes.idioma` | `project.language` (ex. `español` → `Spanish`) |
| `jira.sitio` | `jira.site` |
| `jira.cloud_id` | `jira.cloud_id` |
| `jira.proyecto` | `jira.project` |
| `jira.board_id` | `jira.board_id` |
| `jira.tipos` | `jira.issue_types` |
| `jira.campo_sp` | `jira.sp_field` |
| `jira.campo_sprint` | `jira.sprint_field` |
| `jira.prioridad` | `jira.priority_map` |
| `jira.prefijos_titulo` | `jira.title_prefixes` |
| `claude.project` | `claude.project` |
| `equipo.personas` | `team.members` |
| `equipo.cantidad` | `team.size` |
| `equipo.zona_horaria` | `team.timezone` |
| `equipo.hora_daily` | `team.daily_time` |
| `sprint.duracion_semanas` | `sprint.weeks` |
| `sprint.dia_inicio` | `sprint.start_day` |
| `sprint.fecha_referencia` | `sprint.reference_date` |
| `sprint.dias_habiles` | `sprint.working_days` |
| `sprint.velocidad_por_persona` | `sprint.velocity_per_person` |
| `reglas.wip_por_persona` | `rules.wip_per_person` |
| `reglas.sp_max_ticket` | `rules.max_ticket_size` (ex. `5` → `5 SP`) |
| `reglas.lote_confirmacion` | `rules.batch_confirm` |
| `reglas.dias_estancado` | `rules.stale_days` |
| `repo.url` | `repo.url` |
| `repo.rama_base` | `repo.base_branch` |
| `repo.nombre_rama` | `repo.branch_pattern` |
| `repo.commit` | `repo.commit_pattern` |
| `repo.tests` | `repo.tests` |
| `repo.stack` | `repo.stack` |
| `fabrica.disparo` | `factory.trigger` |
| `fabrica.rondas_review` | `factory.review_rounds` |
| `reportes.canales` | `reports.channels` |

If `reportes.idioma` was missing, ask for `project.language`.

New keys, with these values for a project coming from before 0.7.0 (they all used sprints): `work.method` = `scrum`, `work.estimation` = `story points`, `work.review_day` = `—`, `kanban.*` = `—`. Ask only if the person says the team no longer works in sprints.

### 3. `CLAUDE.md` and `AGENTS.md`

Replace the old Dark Factory block (it starts with the comment `Sección genérica de Dark Factory`) with the block from the new template. Leave everything else in the file untouched.

### 4. Status board

1. Create `factory/project/status.md` from the new template.
2. Carry over *Decisiones* → *Decisions* and *Pendientes* → *Pending* as they were.
3. Mark phases 1–3 ✅ when their data is already in the config; phase 4 (Organization) stays open so the kickoff resumes there and writes the setup guides. Setup mode: Full.
4. Copy `factory/project/setup/` from the template.
5. Run *Verify* to fill every row.

### 5. Outside the repo — record on the board

- **claude.ai Project:** the copies of the config and the docs have to be renamed to the new paths (O4, ⏳ for whoever owns the Project, unless you can write to it).
- **Scheduled tasks:** existing prompts keep working, since they point to `factory/jobs/<job>.md`, whose names didn't change. Offer to update their prompt to the English wording.
- **Tickets and PRs already written** keep their Spanish headings and paths: agents read them through the *Legacy names* table in `factory/STRUCTURE.md`. Don't rewrite them.
