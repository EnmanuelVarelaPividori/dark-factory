# Expected structure — instructions for AI agents

This repo uses **Dark Factory**. This file tells any AI agent (Claude Code, Codex or another) what structure to expect, what it may touch and where each thing goes. It is generic: the same in every project.

## Expected tree

```
<repo root>/
├── CLAUDE.md                  Context for Claude Code. Imports factory/STRUCTURE.md, factory/rules.md and factory/project/config.md
├── AGENTS.md                  Same purpose for other agents: points to this file
├── .claude/
│   └── settings.json          Enables the dark-factory plugin (extraKnownMarketplaces + enabledPlugins)
└── factory/
    ├── README.md              GENERIC · Flow and map of the system
    ├── STRUCTURE.md           GENERIC · This file
    ├── ARCHITECTURE.md        GENERIC · Who loads what, label cycle, updates
    ├── CHECKLIST.md           GENERIC · Full setup reference
    ├── EXTENDING.md           GENERIC · How the project customizes the factory: extensions, flows, context, limits, locked rules
    ├── rules.md               GENERIC · Definition of Ready / Done, board, labels, review, language
    ├── VERSION                GENERIC · Installed template version
    ├── templates/
    │   └── ticket.md          GENERIC · Ticket format
    ├── jobs/
    │   ├── README.md          GENERIC · Schedule rules and prompt of the scheduled tasks
    │   ├── daily-check.md     GENERIC · Job: daily check
    │   ├── weekly-review.md   GENERIC · Job: weekly review (sprint, flow or milestone)
    │   ├── sprint-review.md   GENERIC · Job: close review (sprint, month or milestone)
    │   ├── factory-implement.md  GENERIC · Job: implement runner
    │   └── factory-review.md     GENERIC · Job: review runner
    └── project/               THIS PROJECT'S OWN
        ├── config.md          Project values: Jira, team, way of working, rules, repo, language, reports
        ├── status.md          Status board: what is set up in the repo and in the organization, and who owns what's missing
        ├── setup/
        │   ├── claude-project.md    How to set up the claude.ai Project
        │   └── scheduled-tasks.md   Which scheduled tasks are on, schedule, model, prompt
        ├── context.md         Optional · MCP connectors, apps and sources the skills use for context
        ├── extensions/        Optional · <name>.md adds to or replaces sections of a generic skill, job, rules or ticket template
        ├── flows/             Optional · <name>.md project-specific procedures (hotfix, release…)
        └── docs/
            ├── README.md                GENERIC · What goes in each doc and in what format
            ├── 00-problem-analysis.md
            ├── 01-requirements.md       Requirements with IDs R-x.y
            ├── 02-product-backlog.md    Stories with a Requirements field
            └── 03-delivery-plan.md     Sprints, flow priorities or milestones, per work.method
```

## Rules for agents

1. **GENERIC files are not edited in the project.** They come from the dark-factory plugin and are replaced on update. If you find something to improve, say so (or propose the change in the dark-factory repo); don't change it here.
2. **The project's own things go only in `factory/project/`.** Values in `config.md`; setup state in `status.md`; scope in `docs/`; customizations in `extensions/`, `flows/` and `context.md`. Don't create factory configuration anywhere else.
3. **Project files win, except locked rules.** Apply `factory/project/extensions/<name>.md` and `context.md` when running a skill or job, as `factory/EXTENDING.md` says. Changes to these files are made only through a chat with Claude, following that file's formats and limits.
4. **Fixed paths and names.** The generic files refer to `factory/project/…` by name. Don't rename or move them.
5. **Read the config before acting.** Every value (Jira key, base branch, test commands, thresholds, language) comes from `factory/project/config.md`. Don't assume values or copy them into other files.
6. **Check the status board before running as a runner.** If a row under *Required to run* in `factory/project/status.md` is not ✅ or —, stop and report which ones (see the runner skills).
7. **Write for people in `project.language`.** Labels, file names, config keys, verdict words and ticket headings stay in English.
8. **No secrets in the repo.** Every file under `factory/` is pushed and shared by the team: never write passwords, tokens, API keys or personal data. Those live in the tools' own settings.
9. **Detailed research doesn't go in the repo.** When the project uses a claude.ai Project (`claude.project` is not `—`), it lives there. If a ticket cites Project docs in its **Context** section, read them if you have access; if a required doc isn't accessible and the ticket isn't enough, don't guess: mark the ticket blocked with a comment. Don't copy research into the repo.
10. **Docs are the source of truth for scope.** When they exist, before implementing, read the story in `02-product-backlog.md` and its requirements in `01-requirements.md`. If the ticket contradicts the docs, say so on the ticket and in the PR; never pick silently.
11. **Work rules are in `factory/rules.md`.** In particular: only tickets with `factory:ready` and `factory:go`, one ticket = one branch = one PR, no scope changes, never merge.
12. **Files outside `factory/`.** `CLAUDE.md`, `AGENTS.md` and `.claude/settings.json` can hold the repo's own content besides the factory's: add, don't replace.

## Legacy names (before 0.7.0)

Tickets, PRs and Project docs written before 0.7.0 may use the old Spanish names. Treat them as these:

| Old | Now |
|---|---|
| `factory/proyecto/` | `factory/project/` |
| `estado.md`, `reglas.md`, `ESTRUCTURA.md`, `ARQUITECTURA.md` | `status.md`, `rules.md`, `STRUCTURE.md`, `ARCHITECTURE.md` |
| `00-analisis-problema.md`, `01-analisis-requerimientos.md`, `03-planificacion-sprints.md` | `00-problem-analysis.md`, `01-requirements.md`, `03-delivery-plan.md` |
| Ticket headings *Historia*, *Descripción y ejemplo*, *Criterios de aceptación*, *Contexto*, *Notas para la fábrica* | *Story*, *Description and example*, *Acceptance criteria*, *Context*, *Notes for the factory* |
| Review verdicts `APROBADO` / `CAMBIOS`, "ronda" | `APPROVED` / `CHANGES`, "round" |

## If something is missing

| Situation | What to do |
|---|---|
| `factory/` doesn't exist or a generic file is missing | Run `/dark-factory:factory-kickoff` (or ask a person to). |
| `factory/VERSION` differs from the plugin's | Run `/dark-factory:factory-kickoff` to update. |
| `config.md` has `TODO` values you need | Ask a person for the value; never invent it. |
| You want to know what's missing for the factory to work | Read `factory/project/status.md`, or run `/dark-factory:factory-kickoff` to re-check and resume. |
| You want to change how the factory works in this project | Ask Claude; it follows `factory/EXTENDING.md`. |
| Docs are missing in `factory/project/docs/` | They are written in the claude.ai Project and copied here. |
