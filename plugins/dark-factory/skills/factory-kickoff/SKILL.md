---
name: factory-kickoff
description: The single entry point to set up Dark Factory in a project repo — a personal setup assistant. A short interview in four phases (Quick or Full), installs and configures the repo, does the organization setup itself when the session has the tools (scheduled tasks, GitHub labels, branch protection), writes step-by-step guides for the rest, and keeps the shared status board factory/project/status.md. Also resumes, updates the template and checks status. Use for "start the dark factory", "install / set up / configure the factory", "factory kickoff", "update the factory", "resume the factory setup", "check the factory status", "what's missing for the factory to work?", in any language.
---

# Factory kickoff

A personal setup assistant for one project. It takes the project from nothing to a working factory and keeps it that way. Works from Claude Code or from a claude.ai Project.

- **With the plugin installed:** the template is in `${CLAUDE_PLUGIN_ROOT}/template/` and the migrations in `${CLAUDE_PLUGIN_ROOT}/skills/factory-kickoff/migrations.md`.
- **Without the plugin** (for example from a claude.ai Project): read `plugins/dark-factory/template/` and `plugins/dark-factory/skills/factory-kickoff/migrations.md` from https://github.com/EnmanuelVarelaPividori/dark-factory and follow this skill the same way.

The template has the same shape as a repo root:

```
template/
├── CLAUDE.md, AGENTS.md          generic blocks (appended, never overwrite)
├── .claude/settings.json         enables the plugin (merged, never overwrite)
└── factory/                      generic, except factory/project/
    └── project/                  project skeleton: created once, never overwritten
```

## Principles

1. **Do it yourself when you can.** For every setup item, if this session has a tool that does it, offer to do it and do it after confirmation. Only when no tool exists, write exact steps in `factory/project/setup/` and assign the item to a person. Never ask a person to do something you can do.
2. **Flexible, not complex.** Detect and propose defaults so most answers are "yes". Every organization item can be done now, assigned to someone, or skipped for now; nothing forces a long session.
3. **The status board is the truth.** `factory/project/status.md` says, for the repo and the organization, what is ✅ / ❌ / ⏳ / ⏭ / —, who owns it and when it changed. The runners refuse to start until its *Required to run* rows are ✅ or —.
4. **Everything you write is shared.** All of `factory/` is pushed. Never write passwords, tokens, API keys, personal data or connector credentials into any file. If a person pastes one, don't store it and tell them it doesn't belong in the repo.
5. **Speak the project's language.** Run the interview in `project.language` once it is known (before that, in the language the person writes in). Files, keys, labels and headings stay in English; prose you write into the project's files (setup guides, notes, decisions) goes in `project.language`.
6. **Never invent values.** "I don't know" leaves `TODO` in the config and a pending item with an owner.

## 0. Take stock (before saying anything)

### Mode

| Repo state | What to do |
|---|---|
| No `factory/` | Start at phase 1. |
| `factory/VERSION` differs from `${CLAUDE_PLUGIN_ROOT}/template/factory/VERSION` | **Update** (section *Update*), then continue with the row that applies. |
| A setup phase on the board is not ✅ | **Resume:** show in 3 lines what is done and what's missing, and continue from the first open phase. |
| Everything up to date | **Check status** (section *Verify*): refresh the board, show what is not ✅ or —, and offer to fix it, including items skipped earlier. |

Anything already in the config is not asked again: show it as confirmed.

### Capabilities

Find out which tools this session actually has, silently. You'll show the result in phase 1.

| Capability | How to tell | Unlocks |
|---|---|---|
| Write the project repo | File tools on the repo folder, or a clone you can push | Install, status board, migrations |
| Push / open PRs | `git push` works, or a GitHub tool with write access | Sharing the setup with the team |
| GitHub | `gh auth status`; `gh api repos/<owner>/<repo>` → `permissions` | Labels (write), branch protection (admin) |
| Jira | Atlassian connector tools | Detecting `jira.*`, R4 |
| Scheduled tasks | Tools to create / list / run scheduled tasks (search for them if they load on demand) | Creating and testing the tasks, R6, O6 |
| claude.ai Project | Project tools (`project_read`, `project_search`) and the name of the current Project | Reading research, checking O3–O4, creating runners inside it |

### Detect (without asking)

| Config key | From |
|---|---|
| `project.language` | The language the person writes in |
| `repo.url` | `git remote get-url origin` |
| `repo.base_branch` | Target of the last merged PRs, or `origin/HEAD` |
| `repo.branch_pattern`, `repo.commit_pattern` | Patterns in `git branch -r` and `git log --oneline -30` |
| `repo.tests` | CI workflows, `README`, `package.json`, `composer.json`, `Makefile` |
| `repo.stack` | Manifests, local environment (`.ddev/`, `docker-compose`), folders with own code |
| `jira.*` | With the Atlassian connector: site, cloudId, projects, issue type IDs, story points and sprint fields (`getJiraIssueTypeMetaWithFields`), boards |
| `team.timezone` | The session's time zone |
| `claude.project` | The current Project's name, if the session runs inside one |
| Existing docs | Planning docs in the Project or in another folder of the repo (ex. `docs/`) |

## How to ask

- Each question carries your proposal as the first option, marked "(recommended)", so the answer can be "yes".
- At most 4 questions at a time. If the session has a multiple-choice question tool, use it; otherwise, numbered in one message.
- "I don't know" is a valid answer: `TODO` in the config and a pending item.
- After each phase, update the status board. If `factory/` doesn't exist yet, keep the answers in the conversation and write them in phase 3.

## Setup modes

Asked in phase 1. Recorded on the board (*Setup mode*) and changeable at any time.

| | Quick (recommended for a first run) | Full |
|---|---|---|
| Phase 2 | Detected values and defaults, confirmed in one table | The same, plus planning docs: convert, build missing ones, resolve contradictions |
| Phase 4 | Required items only (R4–R6); recommended items offered at the end as one "do now / later" list | Every item, one by one |
| Smoke test | Left pending | Offered at the end |

## Phase 1 — Basics

Two short rounds.

**Round A — how we'll work**

1. Confirm `project.language` (proposal: the language the person is writing in). From here on, speak it.
2. Quick or Full (explain in one line each, recommend Quick for a first run).
3. Show the capability check as two short lists: **I can do** / **you'll need to do**, given this session. Offer to fix what can be fixed now, ex.: "There's no Atlassian connector here; want to connect it first?", "If you run `gh auth login`, I can create the labels myself." Without access to the repo nothing can be installed: say so and stop.

**Round B — the project**

1. Name, goal in one sentence (what everything is prioritized against) and target date.
2. Jira project (options: the detected ones).
3. Use a claude.ai Project? Explain briefly, in your own words, then recommend:
   - A Project keeps the planning context in one place: research, decisions, diagrams and the domain rules live as files and instructions there, so every chat and task in it starts with that context instead of someone pasting it again.
   - It keeps tickets short: a ticket points to the exact doc ("read `decisions/…` before implementing") instead of copying it, and the runner tasks created inside the Project can read those docs.
   - `ticket-quality` used inside it searches that research to fill the ticket's Context.
   - Without a Project the factory still works: everything a runner needs must then be in the ticket and in `factory/project/docs/`.
   - Recommend **yes** when there is real research or planning behind the project or more than one person plans; **not now** for a small project with self-explanatory tickets. It can be added later.
4. If yes: its name (propose the current Project's, if you are in one). If no: `claude.project` = `—`.

## Phase 2 — How you work

Show **one table** with every remaining config value, detected or default, marked as such, and ask: "confirm all, or tell me which lines to change". Ask only for what is unknown or that you couldn't detect, at most 4 at a time.

| Block | Values |
|---|---|
| Jira | board (or no Jira sprints), priority mapping (propose MUST/SHOULD/COULD → High/Medium/Low), title prefixes |
| Team and sprint | people and their Jira accounts, sprint length and start day, a real sprint's start date, daily time, time zone, velocity per person (mark "(estimated)" if not measured) |
| Repo | base branch, branch pattern, commit convention, test commands, stack and what's out of scope |
| Factory | trigger A (label + scheduled task, recommended) or B (automation); WIP 2, max SP 5, batch 5, review rounds 3; report channels |

Derived without asking: `sprint.working_days` = N × 5; `rules.stale_days` = max(2, N + 1).

**Planning docs.** Show what you found and which fixed name each maps to: `00-problem-analysis.md`, `01-requirements.md`, `02-product-backlog.md`, `03-sprint-plan.md`.

- Quick: offer only to move or copy them under those names. Converting them to the factory format becomes a pending item (O1).
- Full: also offer to convert them (`R-x.y` IDs on requirements; stories with metadata, criteria as a checklist, `Dependencies` and `Requirements`), to build missing ones from the Project, and to resolve contradictions one by one (at most 4 at a time).
- Never move or rewrite a doc without confirmation.

## Phase 3 — Install

Show the list of files that will be created or changed, and ask once. If the current branch is the base branch, propose a new branch.

1. Copy all of `${CLAUDE_PLUGIN_ROOT}/template/factory/` to `factory/`, **except `factory/project/`** if it already exists.
2. If `factory/project/` doesn't exist: copy `template/factory/project/` (config, status board, setup guide skeletons, `docs/README.md`). Without a claude.ai Project, don't copy `setup/claude-project.md`.
3. Write `factory/project/config.md` with what was detected and answered. Unknowns stay `TODO`.
4. Move or convert the docs as agreed in phase 2.
5. `CLAUDE.md` and `AGENTS.md`: if they don't exist, copy the template's. If they exist without the Dark Factory block, append it at the end. Never delete content.
6. `.claude/settings.json`: if it doesn't exist, copy the template. If it exists, merge `extraKnownMarketplaces` and `enabledPlugins` without touching the rest.
7. Fill the board: setup mode, phases 1–3, rows R1–R3 and O1 per *Verify*.

The setup is only shared once it is pushed: offer to commit on a branch and open a PR (or push, if the team works that way). Never commit or push without confirmation.

## Phase 4 — Organization

Go through the items below in order. For each, say in one line what it is and whether you can do it in this session, then offer three answers:

- **Do it now** — you do it (after showing exactly what you'll create), or the person does it while you wait and you verify it.
- **Assign to someone** — ask who; the board gets ⏳ with that owner, and the steps go in the setup guide or the row's note.
- **Skip for now** — the board gets ⏭. If the item is required (R-row), say that the runners won't start until it's done.

In Quick mode, go through the required items (4.1–4.3) one by one, then show the recommended ones as a single list and ask which to do now; the rest become ⏭.

### 4.1 Jira (R4)

A simple JQL on `jira.project` answers. If it fails: the fix is connecting the Atlassian connector in this session's settings.

### 4.2 GitHub (R5, O2)

1. **Labels** (R5): `factory:review`, `factory:changes`, `factory:approved`, `factory:needs-human`. With write access, show the `gh label create` commands and run them.
2. **Branch protection** (O2): check `repo.base_branch` with `gh api repos/<owner>/<repo>/branches/<branch>/protection`. If missing and you have admin, show the command that requires a pull request review before merging and run it. Without admin, the exact setting goes in the row's note for a repo admin.

### 4.3 Runner tasks (R6, O7)

Only if `factory.trigger` = A; otherwise R6 is —.

1. Compute their schedule with `factory/jobs/README.md` and show it: name, cron, local time, suggested model, where it lives, connectors, push access.
2. **If you can create scheduled tasks:** create them with the short prompt from `factory/jobs/README.md` (never config values or secrets in the prompt), notifications per `reports.channels`, then run each once and check the result.
3. **Where they live:** with a claude.ai Project, they belong inside it. If this session is in that Project, create them here; if not, create them and add "move into the Project" as ⏳, or let the person create them from the Project — ask which. Without a Project, they are standalone tasks.
4. The model of each task (O7) is set in the task's own settings: ⏳ for the person, unless your tool set it.
5. Record everything in `factory/project/setup/scheduled-tasks.md`.

### 4.4 Board checks (O6)

Optional. Offer daily, mid-sprint (only if N ≥ 2) and sprint close, with what each answers (table in `factory/README.md`). Create the chosen ones as in 4.3 (standalone, read access, automatic approval is a ⏳ setting for the person) and mark the rest as off in the guide.

### 4.5 claude.ai Project (O3, O4, O5)

Only if one is used; otherwise O3 and O4 are —.

1. Write `factory/project/setup/claude-project.md` from the skeleton, in `project.language`: name, a description and instructions drafted from the docs (domain context only), files to upload with their paths, folder layout, where the runners go.
2. Show the description and instructions draft and ask for corrections.
3. If you are inside the Project and can read it, check what's there; if you can write to it, offer to add the missing files yourself.
4. Saving `ticket-quality` as an account skill (O5) is useful with or without a Project, whenever tickets are created from claude.ai.

### 4.6 Team access (O8, O9)

Each developer accepts the plugin in Claude Code (`/plugin` shows `dark-factory` enabled). If the dark-factory repo is private, people and tasks need read access. Usually assigned, not done now.

### Wrap up

1. Run *Verify* and fill the board.
2. Full mode: offer the smoke test (O10): pick a small ticket (1–2 SP) from the backlog, check or create it with `ticket-quality`, and explain that a person sets `factory:go`. O10 turns ✅ when it reaches a human merge. Quick mode: O10 stays ⏭.
3. Offer to commit and push the board and the setup guides.

## Update

When `factory/VERSION` differs from the plugin's:

1. Replace the generic files of `factory/` with those in `${CLAUDE_PLUGIN_ROOT}/template/factory/`, **without touching `factory/project/`** except as migrations say. Show which files changed, were added or were removed.
2. Apply every migration in `migrations.md` newer than the installed version, in order. Show the list of renames before applying them.
3. Compare the keys of `template/factory/project/config.md` with the project's config. New keys are added with their default or `TODO` and asked in one short round.
4. If the template adds files to `factory/project/` that the project lacks (ex. a new setup guide), copy just those.
5. Continue with *Verify*. Never commit without confirmation.

## Verify

The single checklist, used at the end of phase 4 and in check-status mode. For each row: check it, set ✅ / ❌ / ⏳ / ⏭ / — with the date, and offer to fix what you can. Keep ⏳ owners and ⏭ unless the check now passes.

| ID | How to check | Can you fix it? |
|---|---|---|
| R1 | Every generic file exists; `factory/VERSION` = plugin version | Yes: *Update* |
| R2 | No `TODO` in `project.*`, `jira.*`, `repo.*`, `factory.*` of the config | Ask the missing values |
| R3 | `CLAUDE.md` imports `@factory/STRUCTURE.md`, `@factory/rules.md`, `@factory/project/config.md`; `AGENTS.md` points to `factory/STRUCTURE.md`; `.claude/settings.json` has `dark-factory@dark-factory: true` | Yes |
| R4 | A simple JQL on `jira.project` answers | Ask to connect Atlassian |
| R5 | `gh label list` has the four PR labels | Yes, with write access |
| R6 | Trigger A: both runner tasks exist and are enabled (list them if you can); with a Project, a person confirms they are inside it unless you can see it. Trigger B: — | Create; moving into the Project is a person's step |
| O1 | The 4 docs exist with the fixed names; requirements have `R-x.y`; stories have `Requirements` | Phase 2 (Full) |
| O2 | Base branch protection requires a pull request review | Yes, with admin |
| O3 | Project used: description and instructions present (read it if you can, otherwise a person confirms). Not used: — | Person |
| O4 | Project used: `factory/project/config.md` and the 4 docs present in it with the same paths. Not used: — | If you can write to the Project |
| O5 | A person confirms `ticket-quality` is an account skill | Person |
| O6 | The board checks marked **On** in `setup/scheduled-tasks.md` exist | Create |
| O7 | Each scheduled task has a model chosen (a person confirms unless the listing shows it) | Person |
| O8 | Each developer confirms `/plugin` shows `dark-factory` enabled | Person |
| O9 | dark-factory repo public: —. Private: a person confirms read access for people and tasks | Person |
| O10 | A ticket went through the full cycle to a human merge | Smoke test |

## The status board

- Start from `template/factory/project/status.md`; never drop a row.
- Each row: status, owner (a person's name, or "kickoff" when you did it), date, and a short note in `project.language` (what's missing or how it was done). No secrets, no tokens, no personal data beyond names.
- Header: setup mode, last update date and who ran the kickoff.
- *Setup*: each phase ✅ done / ⏳ in progress, with the date.
- *Decisions*: one line per decision made in the interview. *Pending*: what's left, each with an owner.
- Values go in the config, not on the board: the board records what is done and what's missing.
- The board is shared only once pushed: at the end of each session that changed it, offer to commit and push.

## Close

Always end with:

1. What is ready (one line per block), and whether the runners can start (all *Required to run* rows ✅ or —).
2. What's pending, each with its owner, and what was skipped.
3. How to resume: "Say *resume the factory setup* (or *check the factory status*) and I'll continue from `factory/project/status.md`".

## Don't

- Overwrite `factory/project/`, or delete content from `CLAUDE.md`, `AGENTS.md` or `settings.json`.
- Edit generic files with project values.
- Write any file without showing the list first, or commit or push without confirmation.
- Create scheduled tasks, labels or branch protection without showing what you'll create first.
- Write secrets anywhere in the repo or in task prompts.
- Invent Jira, team or velocity values.
- Ask a person to do something you have a tool for.
