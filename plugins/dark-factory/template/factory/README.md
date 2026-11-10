# Dark Factory

A system to go from planning to code with Claude, without bloated boards or tickets used as a log.

This folder is **the same in every project**, except `project/`, which holds this project's own things. Don't edit the generic files in a project: they are improved in the [dark-factory](https://github.com/EnmanuelVarelaPividori/dark-factory) repo and updated with `/dark-factory:factory-kickoff`.

## Structure

```
factory/
├── README.md               This file: flow and map of the system
├── STRUCTURE.md            Expected structure and instructions for AI agents
├── ARCHITECTURE.md         Who loads what, label cycle, updates
├── CHECKLIST.md            Full setup reference
├── rules.md                Definition of Ready / Done, board rules, labels, review cycle, language
├── VERSION                 Installed template version
├── templates/
│   └── ticket.md           Ticket format
├── jobs/
│   ├── README.md           Schedule rules and how to create the scheduled tasks
│   ├── daily-check.md      Daily board check
│   ├── weekly-review.md    Mid-sprint review
│   ├── sprint-review.md    Sprint close review
│   ├── factory-implement.md  Runner: implement a ticket
│   └── factory-review.md     Runner: review a PR
└── project/                ← THE ONLY PART THAT IS THIS PROJECT'S OWN
    ├── config.md           Jira, team, sprint, repo, rules, language, reports
    ├── status.md           Status board: what is set up and what is missing, repo and organization
    ├── setup/
    │   ├── claude-project.md   How to set up the claude.ai Project
    │   └── scheduled-tasks.md  The scheduled tasks chosen for this project
    └── docs/
        ├── README.md                What goes in each doc and in what format (generic)
        ├── 00-problem-analysis.md
        ├── 01-requirements.md       requirements with IDs R-x.y
        ├── 02-product-backlog.md    stories with a Requirements field
        └── 03-sprint-plan.md
```

Every generic file refers to `project/` by these fixed paths. That is why they work the same in any project.

At the repo root, also: `CLAUDE.md` and `AGENTS.md` (instructions for AI agents, they import or point to `STRUCTURE.md`) and `.claude/settings.json` (enables the plugin).

**Nothing in `factory/` holds secrets.** It is pushed and shared by the team.

## Is the factory ready?

Open `project/status.md`. It shows, for the repo and the organization, what is ✅ done, ❌ missing or ⏳ waiting for a person, and who owns it. To refresh it, run `/dark-factory:factory-kickoff` or ask "check the factory status".

## The flow

```
0. KICKOFF       factory-kickoff: interview, install, organization setup, status board
       │
1. PLAN          claude.ai Project: research, diagrams, docs → factory/project/docs/
       │
2. TICKET        ticket-quality skill: duplicates, template, questions, Definition of Ready → backlog
       │
3. BUILD         Implement runner: takes a Ready ticket with factory:go and opens a PR
       │
4. REVIEW        Review runner: another session reviews the PR against ticket + docs. Review ↔ fix cycle (max N rounds)
       │
5. MERGE         A developer reviews the final version and merges. Nothing merges without a person.
```

The rules of each step are in `rules.md`.

**Repo vs. Project:** `project/` holds the general things the code needs. Detailed research lives in the claude.ai Project; each ticket names in its **Context** section which Project docs to read. See `ARCHITECTURE.md`.

## Scheduled tasks

| Job | Frequency (derived from sprint length) | Suggested model | Answers |
|---|---|---|---|
| `jobs/daily-check.md` | Working days, before the daily | Haiku | Was any board rule broken today? |
| `jobs/weekly-review.md` | Weekly, mid-week. Not with 1-week sprints. | Sonnet | How is the sprint going and what would a scrum master do this week? |
| `jobs/sprint-review.md` | At each sprint close (runs weekly; reports only if one closed) | Opus | Are we on track against the plan and the requirements? Will we make the date? |
| `jobs/factory-implement.md` | Every 2 h in working hours | Sonnet | Implements a ticket or fixes a PR. |
| `jobs/factory-review.md` | Every 2 h, 1 h offset | Opus | Reviews a PR in a new session. |

The board checks are optional; which ones run in this project is in `project/setup/scheduled-tasks.md`. Every task uses the same short prompt: the project repo and which job to run. Everything else is read from the repo at run time. Details in `jobs/README.md`.

**Why those models:** the daily check is mechanical rules; the weekly one reads the board and recommends; the close review crosses requirements, plan and actual state, and cut decisions come out of it. The implementer works small, clear tickets; the reviewer is the gate before a person and should be the most demanding.

## Skills (`dark-factory` plugin)

| Skill | For |
|---|---|
| `dark-factory:factory-kickoff` | Install the factory with a guided interview, set up the organization side, resume, update and check status. |
| `dark-factory:ticket-quality` | Create or refine tickets that meet the Definition of Ready. |
| `dark-factory:factory-implement` | Implement a Ready ticket and fix review findings. |
| `dark-factory:factory-review` | Review a PR against its ticket and the docs. |

## Factory trigger

Chosen per project in `project/config.md` (`factory.trigger`):

| Option | How | Pros | Cons |
|---|---|---|---|
| **A. Label + scheduled task** (recommended to start) | Every 2 h the runners look for tickets with `factory:go` and PRs with `factory:review` / `factory:changes`. | No extra infrastructure. Easy to pause. | Hours of latency. |
| **B. Automation** | Jira Automation fires a webhook (for example to CI with Claude Code) when `factory:go` is added. | Immediate. | Needs Jira Automation, CI and secrets set up (secrets in CI, never in the repo). |

Either way `factory:go` is set by a person: the factory never assigns itself work.
