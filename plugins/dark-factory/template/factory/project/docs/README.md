# Planning docs

Source of truth for the project's scope. They are worked on in the claude.ai Project and copied here with the same names: the implementer, the reviewer and the sprint review read them. File names are fixed and in English; the content is written in `project.language`.

| File | What it holds | Format the skills and jobs expect |
|---|---|---|
| `00-problem-analysis.md` | The why: context, core problem, identified problems, needs, out of scope, constraints | Free |
| `01-requirements.md` | What gets built and with what priority | Each requirement in one row with ID `R-<section>.<n>` and priority `MUST` / `SHOULD` / `COULD` |
| `02-product-backlog.md` | User stories by epic | Each story: `### US-xxx — Title`, a metadata line with priority, SP, epic, sprint and Jira key, **Story**, **Acceptance criteria** as a checklist, **Dependencies** and **Requirements** (`R-x.y` IDs) |
| `03-sprint-plan.md` | Sprints, tickets per sprint, assumed velocity | Summary table per sprint and detail per sprint with US, Jira key, priority and SP |

Rules:

- If the docs contradict each other, write it down and resolve it; never pick silently.
- A scope change goes here first, then into the tickets.
- File names don't change: the generic files in `factory/` refer to them by name.
