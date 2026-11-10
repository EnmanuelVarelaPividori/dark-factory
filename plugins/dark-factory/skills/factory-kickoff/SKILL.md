---
name: factory-kickoff
description: The single entry point to set up Dark Factory in a project repo — a personal setup assistant. A short interview in four phases (Quick or Full), installs and configures the repo, does the organization setup itself when the session has the tools (scheduled tasks, GitHub labels, branch protection), writes step-by-step guides for the rest, and keeps the shared status board factory/project/status.md. Also resumes, updates the template, checks status, and makes every project-specific change (config values, extensions, flows, context sources) through chat. Use for "start the dark factory", "install / set up / configure the factory", "factory kickoff", "update the factory", "resume the factory setup", "check the factory status", "what's missing for the factory to work?", "add a flow", "customize the tickets / the review", "use <connector> as context", "change <setting> in the factory", in any language.
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
| The person asks to change something in the factory (a value, an extension, a flow, a context source) | **Change** (section *Change*). |
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
| `work.method` | Jira: active or closed sprints → scrum; a board without sprints → kanban; phases with dates in the docs → waterfall |
| `team.members` | Jira assignees of the last 90 days |
| `claude.project` | The current Project's name, if the session runs inside one |
| Existing docs | Planning docs in the Project or in another folder of the repo (ex. `docs/`) |

## How to ask

- **Multiple choice by default.** Every question offers 2–4 options, your proposal first and marked "(recommended)", so most answers are one click. The person can always type another answer. If the session has a multiple-choice question tool, use it; otherwise, numbered options in one message.
- **Free text only for what can't be listed:** names, URLs, dates, times, accounts and one-sentence descriptions. Even then, offer the detected values as options when you have them (ex. the Jira projects you found, the current Project's name, "no fixed date").
- **Use multi-select** where several answers fit (report channels, board checks, team members found in Jira).
- At most 4 questions at a time.
- "I don't know" is always an option: `TODO` in the config and a pending item.
- After each phase, update the status board. If `factory/` doesn't exist yet, keep the answers in the conversation and write them in phase 3.

## Setup modes

Asked in phase 1. Recorded on the board (*Setup mode*) and changeable at any time.

| | Quick (recommended for a first run) | Full |
|---|---|---|
| Phase 2 | One question: use the detected values and defaults, or review them block by block | Every block, one question round each, plus planning docs: convert, build missing ones, resolve contradictions |
| Phase 4 | Required items only (R4–R6); recommended items offered at the end as one multi-select | Every item, one by one |
| Smoke test | Left pending | Offered at the end |

## Phase 1 — Basics

Two short rounds.

**Round A — how we'll work** (all multiple choice)

| Question | Options |
|---|---|
| Language for everything the factory writes (`project.language`) | The language the person writes in (recommended) · English · Spanish · Dutch |
| Setup mode | Quick (recommended for a first run) · Full |
| Access (only what failed in the capability check) | Ex. "Connect Atlassian now" · "Continue without Jira for now" — or "Run `gh auth login` so I can create the labels" · "Leave GitHub for later" |

Before the access question, show the capability check as two short lists: **I can do** / **you'll need to do**, given this session. Without access to the repo nothing can be installed: say so and stop.

From here on, speak `project.language`.

**Round B — the project**

| Question | Options |
|---|---|
| Project name | Detected (repo or Jira project name) · type another |
| Goal, in one sentence (what everything is prioritized against) | Free text (offer a draft from the README or the docs as the first option, if you have one) |
| Target date | No fixed date · type a date |
| Jira project | The detected projects (up to 3) · type another key |
| How the team works (`work.method`) | Detected first: Scrum — sprints · Kanban — continuous flow · Waterfall — phases and milestones · Not sure, explain |
| Use a claude.ai Project? | Yes (recommended when there is real research or more than one person plans) · Not now |

Detect the method from Jira when you can: active or closed sprints → scrum; a board without sprints → kanban; a plan with phases and dates in the docs → waterfall. If the person picks "Not sure", explain in one line each and ask again.

Before the claude.ai Project question, explain briefly, in your own words:

- A Project keeps the planning context in one place: research, decisions, diagrams and the domain rules live as files and instructions there, so every chat and task in it starts with that context instead of someone pasting it again.
- It keeps tickets short: a ticket points to the exact doc ("read `decisions/…` before implementing") instead of copying it, and the runner tasks created inside the Project can read those docs.
- `ticket-quality` used inside it searches that research to fill the ticket's Context.
- Without a Project the factory still works: everything a runner needs must then be in the ticket and in `factory/project/docs/`. It can be added later.

If yes: its name (options: the current Project's, if you are in one · type a name). If no: `claude.project` = `—`.

## Phase 2 — How you work

**Quick mode:** show one table with every remaining config value, detected or default, each marked as such, and ask one question: **Use these (recommended)** · **Review them block by block** · **Change a few lines** (then ask which, as a multi-select of the blocks). Ask separately only for what you couldn't detect and has no default.

**Full mode**, or when reviewing: one round per block, in this order. Show detected values as the first option; skip a question when the value is already certain.

**Team and cadence**

| Question | Options |
|---|---|
| Who develops (`team.members`) | Multi-select of the people found in Jira (assignees of the last 90 days) · type others |
| Daily meeting (`team.daily_time`) | 09:00 · 09:30 · 10:00 · No daily |
| Scrum — sprint length | 1 week · 2 weeks (recommended) · 3 weeks · 4 weeks |
| Scrum — sprint start day | Monday (recommended) · Tuesday · Wednesday · Thursday |
| Scrum — start date of a real sprint | Detected from Jira (active sprint) · type a date |
| Kanban / waterfall — weekly review day (`work.review_day`) | Monday · Tuesday · Wednesday (recommended) · Thursday |
| Kanban — team WIP limit (`kanban.wip_in_progress`) | Team size (recommended) · Team size × 1.5 · No limit |
| Waterfall — milestones | Take them from `03-delivery-plan.md` (recommended, if it has dates) · Build them now from the docs · I'll add them later |

**Estimation and pace**

| Question | Options |
|---|---|
| Estimation (`work.estimation`) | Story points (recommended for scrum) · No estimates (recommended for kanban) |
| Scrum — velocity per person per sprint | Measured from the last sprints in Jira (show the number) · Estimate 5 SP · Estimate 8 SP · Estimate 13 SP |
| Kanban — throughput per week | Measured from Jira (show the number) · Estimate: team size × 1 · Estimate: team size × 2 · Measure it later |
| Largest ticket (`rules.max_ticket_size`) | With story points: 3 SP · 5 SP (recommended) · 8 SP. Without estimates: 2 days · 3 days (recommended) · 5 days |

**Jira**

| Question | Options |
|---|---|
| Board | The detected boards · No board |
| Priority for MUST / SHOULD / COULD | High / Medium / Low (recommended) · Highest / High / Medium · The detected priority scheme |
| Layer prefixes in titles | None (recommended unless detected) · `[BE]` / `[FE]` · The prefixes detected in existing titles |

**Repo**

| Question | Options |
|---|---|
| Base branch | The detected branches (`main`, `develop`, …) |
| Branch names | The detected pattern · `{jira_key}-{slug}` · `feature/{jira_key}-{slug}` |
| Commit messages | The detected convention · `{jira_key}: {summary}` · Conventional commits: `feat({jira_key}): {summary}` |
| Test commands | The commands found in CI · There are no tests yet · type them |
| Stack and scope | Show what you detected: Confirm (recommended) · Correct it (free text) |

**Factory**

| Question | Options |
|---|---|
| How tickets enter the factory (`factory.trigger`) | A — a person adds `factory:go`, runners check every 2 h (recommended) · B — Jira Automation calls CI right away |
| WIP per person | 1 · 2 (recommended) · 3 |
| Review rounds before asking a person | 2 · 3 (recommended) · 4 |
| Report channels (multi-select) | Push · Email · Jira comment |

Derived without asking: `team.size` from the members; scrum `sprint.working_days` = N × 5 and `rules.stale_days` = max(2, N + 1); kanban and waterfall `rules.stale_days` = 3; `rules.batch_confirm` = 5. Keys of the methods not in use are `—`.

**Planning docs.** Show what you found and which fixed name each maps to: `00-problem-analysis.md`, `01-requirements.md`, `02-product-backlog.md`, `03-delivery-plan.md`. Ask per doc found: **Move it under the fixed name (recommended)** · **Copy it, keep the original** · **Leave it**.

- Full mode, then: **Convert to the factory format** (`R-x.y` IDs on requirements; stories with metadata, criteria as a checklist, `Dependencies` and `Requirements`; the delivery plan in the shape of `work.method`) · **Later** (pending item O1). For each missing doc: **Build it from the Project / the other docs** · **Leave it pending**. Contradictions: one question each, with the conflicting versions as the options.
- Quick mode: converting and building become pending item O1.
- Never move or rewrite a doc without confirmation.

## Phase 3 — Install

Show the list of files that will be created or changed. If the current branch is the base branch, the commit question below proposes a new branch.

1. Copy all of `${CLAUDE_PLUGIN_ROOT}/template/factory/` to `factory/`, **except `factory/project/`** if it already exists.
2. If `factory/project/` doesn't exist: copy `template/factory/project/` (config, status board, setup guide skeletons, `docs/README.md`). Without a claude.ai Project, don't copy `setup/claude-project.md`.
3. Write `factory/project/config.md` with what was detected and answered. Unknowns stay `TODO`.
4. Move or convert the docs as agreed in phase 2.
5. `CLAUDE.md` and `AGENTS.md`: if they don't exist, copy the template's. If they exist without the Dark Factory block, append it at the end. Never delete content.
6. `.claude/settings.json`: if it doesn't exist, copy the template. If it exists, merge `extraKnownMarketplaces` and `enabledPlugins` without touching the rest.
7. Fill the board: setup mode, phases 1–3, rows R1–R3 and O1 per *Verify*.

Ask before writing: **Write them (recommended)** · **Show me the files first** · **Not yet**.

The setup is only shared once it is pushed. Ask: **New branch and PR (recommended)** · **Push to the current branch** · **Don't commit yet**. Never commit or push without confirmation.

## Phase 4 — Organization

Go through the items below in order. For each, say in one line what it is and whether you can do it in this session, then offer three answers:

- **Do it now** — you do it (after showing exactly what you'll create), or the person does it while you wait and you verify it.
- **Assign to someone** — ask who (options: the team members, or type a name); the board gets ⏳ with that owner, and the steps go in the setup guide or the row's note.
- **Skip for now** — the board gets ⏭. If the item is required (R-row), say that the runners won't start until it's done.

Ask it as one multiple-choice question per item: **Do it now** · **Assign to someone** · **Skip for now** (put first whatever you recommend for that item).

In Quick mode, go through the required items (4.1–4.3) one by one, then show the recommended ones as a single multi-select ("which of these do now?"); the rest become ⏭.

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

Optional. Ask as a multi-select, with what each one answers for `work.method` (table in `factory/jobs/README.md`): **Daily check** · **Weekly review** (scrum: only if N ≥ 2) · **Close review**. Recommend all that apply. Create the chosen ones as in 4.3 (standalone, read access, automatic approval is a ⏳ setting for the person) and mark the rest as off in the guide.

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
2. Full mode: offer the smoke test (O10) — **Now (recommended)** · **Later**: pick a small ticket (1–2 SP) from the backlog, check or create it with `ticket-quality`, and explain that a person sets `factory:go`. O10 turns ✅ when it reaches a human merge. Quick mode: O10 stays ⏭.
3. Offer to commit and push the board and the setup guides.

## Update

When `factory/VERSION` differs from the plugin's:

1. Replace the generic files of `factory/` with those in `${CLAUDE_PLUGIN_ROOT}/template/factory/`, **without touching `factory/project/`** except as migrations say. Show which files changed, were added or were removed.
2. Apply every migration in `migrations.md` newer than the installed version, in order. Show the list of renames before applying them.
3. Compare the keys of `template/factory/project/config.md` with the project's config. New keys are added with their default or `TODO` and asked in one short round.
4. If the template adds files to `factory/project/` that the project lacks (ex. a new setup guide), copy just those.
5. Re-validate the project's extensions against the new generic files (O11): a heading that changed or disappeared is shown with a proposed fix.
6. Continue with *Verify*. Never commit without confirmation.

## Verify

The single checklist, used at the end of phase 4 and in check-status mode. For each row: check it, set ✅ / ❌ / ⏳ / ⏭ / — with the date, and offer to fix what you can. Keep ⏳ owners and ⏭ unless the check now passes.

| ID | How to check | Can you fix it? |
|---|---|---|
| R1 | Every generic file exists; `factory/VERSION` = plugin version | Yes: *Update* |
| R2 | No `TODO` in `project.*`, `jira.*`, `work.*`, `repo.*`, `factory.*`, nor in the keys of the method in use (`sprint.*` or `kanban.*`) | Ask the missing values |
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
| O11 | Every file in `factory/project/extensions/`, `flows/` and `context.md` passes the *Validation* list in `factory/EXTENDING.md`. None: — | Propose the fix, per *Change* |

## Change

Any project-specific change after the setup — a config value, an extension, a flow, a context source, a custom key — follows `factory/EXTENDING.md` exactly. In short:

1. Place it: say in one line which file and which kind of change. If it belongs to the generic system (a new job, label, runner behavior, status row), say so, offer to write it up as a proposal for the dark-factory repo, and stop.
2. Ask what you need as multiple choice. Ex. for a context source: **Kind**: MCP connector · App · claude.ai Project doc · Public URL; **Used by** (multi-select): the skills; **Needed**: Optional · Required · Required for some tickets. For an extension: **Add** · **Replace a section** (options: the sections that aren't locked).
3. Check it against the rules: target heading exists, not locked, within the length limits, no secrets, no conflict with `factory/rules.md` or another extension.
4. Show the new file or the diff and ask: **Apply it (recommended)** · **Adjust it** · **Cancel**.
5. Apply it, update the board (O11 and a one-line decision), then ask: **New branch and PR (recommended)** · **Push to the current branch** · **Don't commit yet**.

When a requested change breaks a locked rule (ex. "let the runner merge", "skip review for small tickets"), say which rule, why it is locked, and offer the closest allowed change.

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
