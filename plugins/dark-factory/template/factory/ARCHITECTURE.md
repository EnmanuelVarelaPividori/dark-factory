# Architecture

## Three layers

| Layer | Where it lives | What it holds | How it changes |
|---|---|---|---|
| **System** | The [dark-factory](https://github.com/EnmanuelVarelaPividori/dark-factory) repo, installed as a plugin | Skills and the `factory/` template | Commit in dark-factory + version bump |
| **Installed template** | `factory/` in each repo (everything except `project/`) | Playbook, rules, checklist, job prompts, ticket format | `/dark-factory:factory-kickoff` updates it; never edited by hand |
| **Project** | `factory/project/` in each repo | Config, status board, setup guides and planning docs | Written by the kickoff, edited in the project (and in its claude.ai Project) |

Generic files never name a project: they read `factory/project/…` by fixed paths. All of it is in English; what the agents write for people follows `project.language`.

## What gets added to each repo

```
<repo>/
├── CLAUDE.md                imports factory/STRUCTURE.md, factory/rules.md and factory/project/config.md
├── AGENTS.md                same for non-Claude agents (points to factory/STRUCTURE.md)
├── .claude/settings.json    enables the dark-factory plugin
└── factory/                 generic template + project/
```

If the repo already had `CLAUDE.md`, `AGENTS.md` or `.claude/settings.json`, the kickoff adds what's needed without overwriting anything. The full structure and the rules for agents are in `factory/STRUCTURE.md`.

## Repo, organization and status board

A working factory needs things in two places:

| Where | Examples | Who sets it up |
|---|---|---|
| **Repo** | `factory/`, config, docs, `CLAUDE.md`, plugin enabled | The kickoff writes it; a person confirms the commit |
| **Organization** | Jira access, GitHub labels and branch protection, claude.ai Project, scheduled tasks, account skill | The kickoff does what the session has tools for; the rest is written as steps in `factory/project/setup/` for a person |

`factory/project/status.md` is the single board for both: every item with ✅ / ❌ / ⏳, owner and date. It is pushed, so the whole team sees the same state; it holds no secrets. The runners read its *Required to run* section before starting and stop if something is missing.

## Repo and Project: what goes in each

The claude.ai Project is optional. It keeps the planning context in one place, so chats and tasks start with it and tickets can cite docs instead of copying them. Without one, the ticket and `factory/project/docs/` must carry everything a runner needs.

| | Repo (`factory/project/`) | claude.ai Project (`claude.project`) |
|---|---|---|
| For | What the code needs: general and stable | All the planning work, with the detail |
| Content | Config, status, setup guides and the 4 general docs (problem, `R-x.y` requirements, backlog, plan) | The same 4 docs + detailed research, decisions, diagrams, minutes, drafts |
| Who reads it | Every agent and runner | Planners; the runners when a ticket cites a doc in **Context** |

The **ticket** is the bridge: it carries what's essential to implement and, in its **Context** section, names the Project docs to read (path + why + required / reference).

Suggested folder layout in the Project, so cited paths stay stable:

```
<Project>
├── Description and instructions      what the project is and domain context (no ticket rules, no Jira data)
├── factory/project/config.md          copy from the repo
├── factory/project/docs/00…03-*.md    copy from the repo (or synced from GitHub)
├── research/<topic>.md                detailed research
├── decisions/<YYYY-MM-DD>-<topic>.md  decisions and their why
└── diagrams/<topic>.md                diagrams and flows
```

If a Project path changes, open tickets that cite it go stale: don't move cited docs.

## Who loads what

| Where Claude runs | Config and rules | Skills | Instruction |
|---|---|---|---|
| claude.ai Project | `factory/project/…` docs in the Project | `ticket-quality` as an account skill | The conversation |
| A developer's Claude Code | `CLAUDE.md` imports config and rules | Plugin (Claude Code offers to install it when opening the repo) | The developer |
| Scheduled task (any job) | Clones the repo: `CLAUDE.md` + `factory/` | Plugin; if missing, the job reads the skill from the dark-factory repo | `factory/jobs/<job>.md` |
| Runners created inside the Project (if one is used) | The above + the Project docs the ticket cites (`project_read`) | Same | Same |

## Prompt of each scheduled task

Every task uses the same prompt; only the repo and the job change:

```
Project repo: <url> (branch <base_branch>)
Clone the repo and run the job defined in factory/jobs/<job>.md. Follow that file exactly.
```

Changing a threshold, the team or the velocity is editing `factory/project/config.md`: tasks read it on their next run. A task only needs touching when the schedule changes (sprint length, start day, daily time).

## Label cycle

```
A person sets factory:go
        │
        ▼
[Implement runner] ── status board not ready ──► stops, reports what's missing
        │
        ├── pre-flight fails ──► factory:blocked + comment
        │ OK
        ▼
PR opened + factory:review
        │
        ▼
[Review runner] ── CHANGES ──► factory:changes ──► [Implement runner] fixes ──► factory:review ──┐
        │                                                                                       │
        │◄──────────────────────────────────────────────────────────────────────────────────────┘
        │ APPROVED                         last round without approval
        ▼                                          │
factory:approved ──► developer reviews and merges  ▼
                                          factory:needs-human
```

## Updating the template in a project

1. Improve the file in dark-factory, bump `version` in `plugins/dark-factory/.claude-plugin/plugin.json` and in `template/factory/VERSION`.
2. In each project: `/dark-factory:factory-kickoff`. It compares `factory/VERSION` with the plugin's, replaces the generic files, runs the migrations and re-checks the status board. **It never overwrites `factory/project/`**; migrations only rename paths and keys.
