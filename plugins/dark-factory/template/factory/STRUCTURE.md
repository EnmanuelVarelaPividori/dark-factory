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
    ├── rules.md               GENERIC · Definition of Ready / Done, board, labels, review, language
    ├── VERSION                GENERIC · Installed template version
    ├── templates/
    │   └── ticket.md          GENERIC · Ticket format
    ├── jobs/
    │   ├── README.md          GENERIC · Schedule rules and prompt of the scheduled tasks
    │   ├── daily-check.md     GENERIC · Job: daily check
    │   ├── weekly-review.md   GENERIC · Job: mid-sprint review
    │   ├── sprint-review.md   GENERIC · Job: sprint close review
    │   ├── factory-implement.md  GENERIC · Job: implement runner
    │   └── factory-review.md     GENERIC · Job: review runner
    └── project/               THIS PROJECT'S OWN
        ├── config.md          Project values: Jira, team, sprint, rules, repo, language, reports
        ├── status.md          Status board: what is set up in the repo and in the organization, and who owns what's missing
        ├── setup/
        │   ├── claude-project.md    How to set up the claude.ai Project
        │   └── scheduled-tasks.md   Which scheduled tasks are on, schedule, model, prompt
        └── docs/
            ├── README.md                GENERIC · What goes in each doc and in what format
            ├── 00-problem-analysis.md
            ├── 01-requirements.md       Requirements with IDs R-x.y
            ├── 02-product-backlog.md    Stories with a Requirements field
            └── 03-sprint-plan.md
```

## Rules for agents

1. **GENERIC files are not edited in the project.** They come from the dark-factory plugin and are replaced on update. If you find something to improve, say so (or propose the change in the dark-factory repo); don't change it here.
2. **The project's own things go only in `factory/project/`.** Values in `config.md`; setup state in `status.md`; scope in `docs/`. Don't create factory configuration anywhere else.
3. **Fixed paths and names.** The generic files refer to `factory/project/…` by name. Don't rename or move them.
4. **Read the config before acting.** Every value (Jira key, base branch, test commands, thresholds, language) comes from `factory/project/config.md`. Don't assume values or copy them into other files.
5. **Check the status board before running as a runner.** If a row under *Required to run* in `factory/project/status.md` is not ✅ or —, stop and report which ones (see the runner skills).
6. **Write for people in `project.language`.** Labels, file names, config keys, verdict words and ticket headings stay in English.
7. **No secrets in the repo.** Every file under `factory/` is pushed and shared by the team: never write passwords, tokens, API keys or personal data. Those live in the tools' own settings.
8. **Detailed research doesn't go in the repo.** It lives in the claude.ai Project (`claude.project`). If a ticket cites Project docs in its **Context** section, read them if you have access; if a required doc isn't accessible and the ticket isn't enough, don't guess: mark the ticket blocked with a comment. Don't copy research into the repo.
9. **Docs are the source of truth for scope.** Before implementing, read the story in `02-product-backlog.md` and its requirements in `01-requirements.md`. If the ticket contradicts the docs, say so on the ticket and in the PR; never pick silently.
10. **Work rules are in `factory/rules.md`.** In particular: only tickets with `factory:ready` and `factory:go`, one ticket = one branch = one PR, no scope changes, never merge.
11. **Files outside `factory/`.** `CLAUDE.md`, `AGENTS.md` and `.claude/settings.json` can hold the repo's own content besides the factory's: add, don't replace.

## Legacy names (before 0.7.0)

Tickets, PRs and Project docs written before 0.7.0 may use the old Spanish names. Treat them as these:

| Old | Now |
|---|---|
| `factory/proyecto/` | `factory/project/` |
| `estado.md`, `reglas.md`, `ESTRUCTURA.md`, `ARQUITECTURA.md` | `status.md`, `rules.md`, `STRUCTURE.md`, `ARCHITECTURE.md` |
| `00-analisis-problema.md`, `01-analisis-requerimientos.md`, `03-planificacion-sprints.md` | `00-problem-analysis.md`, `01-requirements.md`, `03-sprint-plan.md` |
| Ticket headings *Historia*, *Descripción y ejemplo*, *Criterios de aceptación*, *Contexto*, *Notas para la fábrica* | *Story*, *Description and example*, *Acceptance criteria*, *Context*, *Notes for the factory* |
| Review verdicts `APROBADO` / `CAMBIOS`, "ronda" | `APPROVED` / `CHANGES`, "round" |

## If something is missing

| Situation | What to do |
|---|---|
| `factory/` doesn't exist or a generic file is missing | Run `/dark-factory:factory-kickoff` (or ask a person to). |
| `factory/VERSION` differs from the plugin's | Run `/dark-factory:factory-kickoff` to update. |
| `config.md` has `TODO` values you need | Ask a person for the value; never invent it. |
| You want to know what's missing for the factory to work | Read `factory/project/status.md`, or run `/dark-factory:factory-kickoff` to re-check and resume. |
| Docs are missing in `factory/project/docs/` | They are written in the claude.ai Project and copied here. |
