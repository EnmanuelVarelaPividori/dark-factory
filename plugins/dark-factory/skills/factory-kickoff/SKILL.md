---
name: factory-kickoff
description: The single entry point to set up Dark Factory in a project repo — a personal setup assistant. Guided interview in short rounds; installs and configures the repo, does the organization setup itself when the session has the tools (scheduled tasks, GitHub labels, branch protection), writes step-by-step guides for the rest, and keeps the shared status board factory/project/status.md. Also resumes, updates the template and checks status. Use for "start the dark factory", "install / set up / configure the factory", "factory kickoff", "update the factory", "resume the factory setup", "check the factory status", "what's missing for the factory to work?", in any language.
---

# Factory kickoff

A personal setup assistant for one project. It takes the project from nothing to a working factory and keeps it that way: interview, install, organization setup, status board, updates. Works from Claude Code or from a claude.ai Project.

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

1. **Do it yourself when you can.** For every setup item, if this session has a tool that does it, offer to do it and do it after confirmation. Only when no tool exists, write exact steps in `factory/project/setup/` and mark the item ⏳ with an owner. Never ask a person to do something you can do.
2. **The status board is the truth.** `factory/project/status.md` says, for the repo and the organization, what is ✅ / ❌ / ⏳ / —, who owns it and when it changed. The runners refuse to start until its *Required to run* rows are ✅ or —.
3. **Everything you write is shared.** All of `factory/` is pushed. Never write passwords, tokens, API keys, personal data or connector credentials into any file. If a person pastes one, don't store it and tell them it doesn't belong in the repo.
4. **Speak the project's language.** Run the interview in `project.language` once it is known (before that, in the language the person writes in). Files, keys, labels and headings stay in English; prose you write into the project's files (setup guides, notes, decisions) goes in `project.language`.
5. **Never invent values.** "I don't know" leaves `TODO` in the config and a pending item with an owner.

## 0. Take stock (before saying anything)

### Mode

| Repo state | Mode |
|---|---|
| No `factory/` | **Interview** from round 1. |
| `factory/VERSION` differs from `${CLAUDE_PLUGIN_ROOT}/template/factory/VERSION` | **Update** (section *Update*). Then continue with the row that applies. |
| `factory/project/status.md` has rounds not ✅ | **Resume:** show in 3 lines what is done and what's missing, and continue from the first open round. |
| Everything up to date | **Check status** (section *Verify*): refresh the board, show what is not ✅ and offer to fix it. |

A round whose data is already in the config is not asked again: show the values as confirmed, mark the round ✅ and move on.

### Capabilities

Find out which tools this session actually has, silently. You'll show the result in round 1.

| Capability | How to tell | Unlocks |
|---|---|---|
| Write the project repo | File tools on the repo folder, or a clone you can push | Install, status board, migrations |
| Push / open PRs | `git push` works, or a GitHub tool with write access | Sharing the setup with the team |
| GitHub admin | `gh auth status` and `gh api repos/<owner>/<repo>` → `permissions.admin` | Labels (write is enough), branch protection (admin) |
| Jira | Atlassian connector tools | Detecting `jira.*`, R5 |
| Scheduled tasks | Tools to create / list / run scheduled tasks (search for them if they load on demand) | Creating and testing the tasks, R8, O4 |
| claude.ai Project | Project tools (`project_read`, `project_search`) and the name of the current Project | Reading research, checking O1–O2, creating runners inside it |

## How to ask

- **Detect before asking** (see *Detect*). Each question carries your proposal as the first option, marked "(recommended)", so the answer can be "yes". What you already know for sure isn't asked: show it as confirmed.
- **Short rounds:** at most 4 questions per round. If the session has a multiple-choice question tool, use it; otherwise, numbered in one message.
- **One round per topic**, in the order below. Skip the ones that don't apply.
- **"Ask me everything at once":** if the person asks, gather the open questions of rounds 2 to 7 into one message, with your proposal on each, and continue with round 8.
- **After each round**, update `factory/project/status.md`. If `factory/` doesn't exist yet, keep the answers in the conversation and write them in round 8. If the conversation stops, the board is where it resumes.

## Detect (without asking)

Do it at the start; it feeds the proposals of every round.

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
| Existing docs | Planning docs in the Project or in another folder of the repo (ex. `docs/`) |

## Rounds

### Round 1 — Language and access

1. Confirm `project.language` (proposal: the language the person is writing in). From here on, speak it.
2. Show the capability table as two short lists: **I can do** / **you'll need to do**, given this session.
3. Ask only about what fails and can be fixed now. Ex.: "I can't reach the repo folder. Do you want to add it, or should I work on a clone from GitHub?", "There's no Atlassian connector in this session; want to connect it before we go on?", "`gh` isn't logged in; if you run `gh auth login`, I can create the labels myself."

Without access to the repo nothing can be installed: say so and stop there.

### Round 2 — Project

- Project name.
- Goal in one sentence (what everything is prioritized against).
- Target date.
- Name of the claude.ai Project with the research (`claude.project`). If you are inside a Project, propose its name.

### Round 3 — Planning docs

Show what you found and which fixed doc each one maps to: `00-problem-analysis.md`, `01-requirements.md`, `02-product-backlog.md`, `03-sprint-plan.md`.

- If they are in another folder of the repo: move them to `factory/project/docs/` with those names? Don't move without confirmation.
- Convert them to the factory format? (`R-x.y` IDs on requirements; stories with metadata, criteria as a checklist, `Dependencies` and `Requirements`.)
- If one is missing: build it from what's in the Project, or leave it pending?
- If docs contradict each other: list the contradictions and ask how to resolve each (max 4 per round; extra rounds if there are more).

### Round 4 — Jira

- Which Jira project? (options: the detected ones)
- Do you use sprints in Jira? On which board?
- Priority mapping for MUST / SHOULD / COULD (propose High / Medium / Low).
- Do you use layer prefixes in titles (ex. `[BE]`, `[FE]`)?

### Round 5 — Team and sprint

- How many people develop, and what are their Jira accounts?
- Sprint length (1 / 2 / 3 / 4 weeks) and start day.
- Start date of a real sprint, daily time and time zone.
- Velocity per person per sprint: measured or estimated? (if estimated, mark it so).

Derived, without asking: `sprint.working_days` = N × 5; `rules.stale_days` = max(2, N + 1).

### Round 6 — Repo

Show what you detected and ask for confirmation or correction:

- Base branch and branch name pattern.
- Commit convention.
- Test commands (the ones CI runs).
- Stack and folders with own code; is anything out of the factory's scope?

### Round 7 — Factory rules

Propose the defaults and ask whether they change:

- Trigger: A (label + scheduled task, recommended) or B (automation).
- WIP per person (2), max SP per ticket (5), tickets per batch that need confirmation (5), review rounds (3).
- Reports: channels (push, email).

### Round 8 — Install

Before writing, show the list of files that will be created or changed and ask for confirmation. If the current branch is the base branch, propose a new branch.

1. Copy all of `${CLAUDE_PLUGIN_ROOT}/template/factory/` to `factory/`, **except `factory/project/`** if it already exists.
2. If `factory/project/` doesn't exist: copy `template/factory/project/` (config, status board, setup guide skeletons and `docs/README.md`).
3. Write `factory/project/config.md` with what was detected and answered. Unknowns stay `TODO`.
4. Move or convert the docs as agreed in round 3.
5. `CLAUDE.md` and `AGENTS.md`: if they don't exist, copy the template's. If they exist without the Dark Factory block, append it at the end. Never delete content.
6. `.claude/settings.json`: if it doesn't exist, copy the template. If it exists, merge `extraKnownMarketplaces` and `enabledPlugins` without touching the rest.
7. Fill the status board: interview rounds so far, and rows R1–R4 per *Verify*.

The setup is only shared once it is pushed. Offer to commit it on a branch and open a PR (or push, if the team works that way). Never commit or push without confirmation.

### Round 9 — claude.ai Project

1. Write `factory/project/setup/claude-project.md` from the skeleton, in `project.language`: Project name, a description and instructions drafted from the docs (domain context only), the files to upload with their paths, the folder layout, the account skill and where the runners go.
2. Show the description and instructions draft and ask for corrections.
3. If you are inside the Project and can read it: check what is already there (O1, O2) and say what's missing. If you can write to it, offer to add the missing files yourself.
4. What only a person can do (creating the Project, uploading files, saving `ticket-quality` as an account skill): ask who will do it, and record each as ⏳ with that owner on the board.

### Round 10 — Scheduled tasks

With `factory/jobs/README.md`:

1. Compute the schedule from the config. With N = 1 there is no mid-sprint review; if `factory.trigger` ≠ A, there are no runners.
2. Ask which board checks to turn on (daily, mid-sprint, close). They are optional; propose all that apply.
3. Show a table: name, cron, local time, suggested model, where it lives, connectors, repo access.
4. Write `factory/project/setup/scheduled-tasks.md` with the chosen tasks and the prompt.
5. **If the session can create scheduled tasks**, offer to create them yourself:
   - Use the short prompt from `factory/jobs/README.md`. Never put config values or secrets in the prompt: the job reads them at run time.
   - Notifications per `reports.channels`.
   - Runners belong inside the claude.ai Project. If this session is in that Project, create them here; if not, create them and record "move into the Project" as ⏳ (R8), or let the person create them from the Project — ask which.
   - After creating, run each once and check the result. Mark **Created** ✅ in the guide and update R8 and O4.
6. If the session can't create tasks: the guide is the instruction; record the tasks as ⏳ with an owner.
7. Choosing each task's model (R9) and turning on automatic approval for the board checks are made in each task's settings: record them as ⏳ for the person, unless the tool let you set them.

### Round 11 — GitHub

1. **Labels** (R6): `factory:review`, `factory:changes`, `factory:approved`, `factory:needs-human`. If `gh` can write to the repo, show the `gh label create` commands and run them on confirmation. If not, ⏳ with an owner.
2. **Branch protection** (R7): check `repo.base_branch` with `gh api repos/<owner>/<repo>/branches/<branch>/protection`. If it is missing and you have admin, show the command that requires a pull request review before merging and run it on confirmation. Without admin: ⏳ for a repo admin, with the exact setting in the note.
3. If the dark-factory repo is private: who needs read access (people and tasks) goes as ⏳ (O6).

### Round 12 — Verify

Run *Verify* over every row. Ask about the ⏳ items one by one (done / not yet / later), in rounds of 4, and record the answers with owner and date. Offer to commit and push the updated board.

### Round 13 — Smoke test

- Shall we pick a small ticket (1–2 SP) now to test the full cycle? Propose candidates from the backlog.
- If yes: check it or create it with `ticket-quality`, and explain that a person has to set `factory:go` on it. O7 turns ✅ when it reaches a human merge.

## Update

When `factory/VERSION` differs from the plugin's:

1. Replace the generic files of `factory/` with those in `${CLAUDE_PLUGIN_ROOT}/template/factory/`, **without touching `factory/project/`** except as migrations say. Show which files changed, were added or were removed.
2. Apply every migration in `migrations.md` newer than the installed version, in order. Show the list of renames before applying them.
3. Compare the keys of `template/factory/project/config.md` with the project's config. New keys are added with their default or `TODO` and asked as one more round.
4. If the template adds files to `factory/project/` that the project lacks (ex. a new setup guide), copy just those.
5. Continue with *Verify*. Never commit without confirmation.

## Verify

The single checklist, used in round 12 and in check-status mode. For each row: check it, set ✅ / ❌ / ⏳ / — with the date, and offer to fix what you can.

| ID | How to check | Can you fix it? |
|---|---|---|
| R1 | Every generic file exists; `factory/VERSION` = plugin version | Yes: *Update* |
| R2 | No `TODO` in `project.*`, `jira.*`, `repo.*`, `factory.*` of the config | Ask the missing values |
| R3 | The 4 docs exist with the fixed names; requirements have `R-x.y`; stories have `Requirements` | Round 3 |
| R4 | `CLAUDE.md` imports `@factory/STRUCTURE.md`, `@factory/rules.md`, `@factory/project/config.md`; `AGENTS.md` points to `factory/STRUCTURE.md`; `.claude/settings.json` has `dark-factory@dark-factory: true` | Yes |
| R5 | A simple JQL on `jira.project` answers | Ask to connect Atlassian |
| R6 | `gh label list` has the four PR labels | Yes, with write access |
| R7 | Base branch protection requires a pull request review | Yes, with admin |
| R8 | If `factory.trigger` = A: both runner tasks exist and are enabled (list them if you can); inside the Project is confirmed by a person unless you can see it. Trigger B: — | Create; moving into the Project is a person's step |
| R9 | Each runner task has a model chosen (confirmed by a person unless the task listing shows it) | Person |
| O1 | If you can read the Project: description and instructions present. Otherwise the person confirms | Person |
| O2 | `factory/project/config.md` and the 4 docs present in the Project with the same paths | If you can write to the Project |
| O3 | Person confirms `ticket-quality` is an account skill | Person |
| O4 | The tasks marked **On** in `setup/scheduled-tasks.md` exist | Create |
| O5 | Each developer confirms `/plugin` shows `dark-factory` enabled | Person |
| O6 | dark-factory repo public: —. Private: person confirms read access | Person |
| O7 | A ticket went through the full cycle to a human merge | Round 13 |

## The status board

- Start from `template/factory/project/status.md`; never drop a row.
- Each row: status, owner (a person's name, or "kickoff" when you did it), date, and a short note in `project.language` (what's missing or how it was done). No secrets, no tokens, no personal data beyond names.
- Update *Last updated* with the date and who ran the kickoff.
- Interview rounds: ✅ done / ⏳ in progress / — not applicable, with the date.
- *Decisions*: one line per decision made in the interview. *Pending*: what's left, each with an owner.
- Values go in the config, not in the board: the board records what is done and what's missing.
- The board is shared only once pushed: at the end of each session that changed it, offer to commit and push.

## Close

Always end with:

1. What is ready (one line per block).
2. What's pending, each with who does it, as on the board.
3. How to resume: "Say *resume the factory setup* (or *check the factory status*) and I'll continue from `factory/project/status.md`".

## Don't

- Overwrite `factory/project/`, or delete content from `CLAUDE.md`, `AGENTS.md` or `settings.json`.
- Edit generic files with project values.
- Write any file without showing the list first, or commit or push without confirmation.
- Create scheduled tasks, labels or branch protection without showing what you'll create first.
- Write secrets anywhere in the repo or in task prompts.
- Invent Jira, team or velocity values.
- Ask a person to do something you have a tool for.
