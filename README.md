# Dark Factory

Template and Claude Code plugin to go from planning to code with Claude: planning docs → quality tickets → implementation → review against the ticket → human merge. With automatic board checks (daily, weekly, close review), for scrum, kanban or waterfall teams.

The system is **generic**: it names no project and is written in English. Each project gets the same `factory/` folder and keeps its own things in `factory/project/`, including the language its agents write in (`project.language`).

## Start in a project

**From Claude Code**, inside the project repo:

```
/plugin install dark-factory --marketplace EnmanuelVarelaPividori/dark-factory
/dark-factory:factory-kickoff
```

**From a claude.ai Project** (with the repo folder connected and Jira available), paste:

```
Start the dark factory for this project.
System: https://github.com/EnmanuelVarelaPividori/dark-factory — follow plugins/dark-factory/skills/factory-kickoff/SKILL.md.
Project repo: <local folder or GitHub URL>.
```

The kickoff is a setup assistant. It interviews you in short rounds, in your language, and then:

- installs and configures `factory/`, `CLAUDE.md`, `AGENTS.md` and `.claude/settings.json`;
- does the organization setup itself when the session has the tools: scheduled tasks, GitHub labels, branch protection;
- writes step-by-step guides for what only a person can do (the claude.ai Project, task models, the account skill);
- keeps everything on a shared status board, `factory/project/status.md`.

Run it again any time to resume, update the template or check the status ("check the factory status"). Once every *Required to run* row on the board is ✅, the runners start on their own schedule; a person still decides which tickets go in (`factory:go`) and merges.

## What each project gets

```
<repo>/
├── CLAUDE.md                  imports factory/STRUCTURE.md, factory/rules.md, factory/project/config.md
├── AGENTS.md                  the same for other agents
├── .claude/settings.json      enables the plugin
└── factory/
    ├── README.md  STRUCTURE.md  ARCHITECTURE.md  CHECKLIST.md  rules.md  VERSION    generic
    ├── templates/ticket.md                                                       generic
    ├── jobs/  README.md  daily-check.md  weekly-review.md  sprint-review.md
    │          factory-implement.md  factory-review.md                            generic
    └── project/                                                                  the project's own
        ├── config.md          settings, incl. project.language
        ├── status.md          status board: repo + organization, owner, date
        ├── setup/             claude-project.md, scheduled-tasks.md
        └── docs/  00-problem-analysis.md  01-requirements.md
                   02-product-backlog.md   03-delivery-plan.md
```

Everything under `factory/` is pushed and shared by the team, so it never holds secrets. Details: [`factory/STRUCTURE.md`](plugins/dark-factory/template/factory/STRUCTURE.md) (for agents) and [`factory/README.md`](plugins/dark-factory/template/factory/README.md) (flow).

## This repo

```
dark-factory/
├── README.md                          This file
├── CLAUDE.md, AGENTS.md               Instructions for agents that maintain the template
├── .claude-plugin/marketplace.json    Claude Code plugin marketplace
└── plugins/dark-factory/
    ├── .claude-plugin/plugin.json     Plugin manifest and version
    ├── skills/
    │   ├── factory-kickoff/           Setup assistant: install, organization setup, status, update (+ migrations.md)
    │   ├── ticket-quality/            Tickets that meet the Definition of Ready
    │   ├── factory-implement/         Implement a Ready ticket
    │   └── factory-review/            Review a PR against its ticket
    └── template/                      What gets copied to each repo, shaped like the repo root
        ├── CLAUDE.md, AGENTS.md
        ├── .claude/settings.json
        └── factory/
```

## Skills

| Skill | Where it is used | For |
|---|---|---|
| `dark-factory:factory-kickoff` | Claude Code or a claude.ai Project | Setup assistant: interview, install, organization setup, status board, updates. |
| `dark-factory:ticket-quality` | claude.ai Project (as an account skill) or Claude Code | Create or refine tickets. |
| `dark-factory:factory-implement` | Scheduled task or Claude Code | Implement a ticket and fix findings. |
| `dark-factory:factory-review` | Scheduled task | Review a PR in an independent session. |

To use `ticket-quality` in claude.ai, save `plugins/dark-factory/skills/ticket-quality/SKILL.md` as a skill of your account.

## Access

If this repo is private, every person and scheduled task that uses the plugin needs read access.
