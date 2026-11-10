# Changing the factory in this project

How this project's factory is customized, always through a chat with Claude. Claude follows these rules for every change; people can read them to know what is possible. The goal: a small, solid core that is hard to break, and project-specific additions that stay small and visible.

## The model

- **Generic by default.** Skills (from the dark-factory plugin) and the generic files in `factory/` define how the factory works. They are never edited in this repo.
- **The project extends, it doesn't fork.** Everything specific to this project lives in `factory/project/`. When a project file covers something, it wins over the generic one, except for the locked rules below.
- **Every change goes through chat.** Ask Claude ("add a hotfix flow", "use Sentry for bug tickets", "add a Security section to tickets", "change the review day"). Claude makes the change with `/dark-factory:factory-kickoff`, following this file.

## What can be changed, and where

| I want to… | File in `factory/project/` | Kind |
|---|---|---|
| Change a value (team, method, thresholds, language…) | `config.md` | Edit a value |
| Keep a value only my extensions use | `config.md`, section *Custom*, keys `custom.<name>` | Add a key |
| Add steps or checks to a skill, a job, the rules or the ticket template | `extensions/<name>.md` | Extension: **Add** |
| Do a part of a skill, a job or the ticket template differently | `extensions/<name>.md` | Extension: **Replace** a section |
| Add a project-specific procedure (hotfix, client request, release…) | `flows/<name>.md` | Flow |
| Tell the skills which MCP connectors, apps or sources to use for context | `context.md` | Context source |
| Change the scope (requirements, backlog, plan) | `docs/0X-*.md` | Doc |

`<name>` is one of: `ticket-quality`, `factory-implement`, `factory-review`, `daily-check`, `weekly-review`, `sprint-review`, `rules`, `ticket`. An extension of `factory-implement` or `factory-review` applies to the skill and to its scheduled job.

Anything else (a new job, a new label, a change to how the runners pick work, a change to the status board) is a change to the generic system: propose it in the dark-factory repo instead.

## Extension format

```markdown
---
extends: ticket-quality
description: One line — what this changes and why
---

## Add after "3. Draft with the template"
- For tickets that touch payments, add a **Security** section listing the data handled.

## Replace "6. Capacity and destination"
- New tickets always go to the backlog; only the product owner moves them into a sprint.
```

- Each block is `## Add after "<exact heading>"`, `## Add before "<exact heading>"` or `## Replace "<exact heading>"`, where the heading is copied exactly from the generic file (without the `#` marks).
- **Add** is always allowed, as long as it doesn't contradict a locked rule.
- **Replace** swaps the whole section under that heading. It is not allowed on locked sections.
- Values come from the config (`custom.*` or the normal keys), not pasted into the extension.
- At most **60 lines** per extension file. Prose in `project.language`, headings in English.

## Flow format

```markdown
---
name: hotfix
use_when: One line — the requests this flow handles
uses: ticket-quality, factory-implement
---

# Flow: hotfix

## Steps
1. …

## Done when
- …
```

- A flow runs when a person asks for it in a chat, or when `ticket-quality` or the kickoff see that a request matches its `use_when`. The runners don't pick up flows; to change what a runner does, use an extension.
- A flow builds on the generic skills (`uses`) and follows `factory/rules.md`.
- At most **80 lines** per flow and **10 flows** per project.

## Context sources format

`context.md` is one table:

```markdown
| Source | Kind | Used by | For | Needed |
|---|---|---|---|---|
| Sentry | MCP connector | factory-implement, factory-review | Stack traces on bug tickets | Optional |
| Figma | MCP connector | ticket-quality, factory-implement | Designs linked in UI tickets | Required for UI tickets |
```

- **Kind:** MCP connector, app, claude.ai Project doc, or public URL.
- **Needed:** `Optional`, `Required`, or `Required for <kind of ticket>`. When a required source isn't available in the session, the skill treats it like a failed pre-flight for the tickets that need it: `factory:blocked` with a comment naming the source.
- Names only. Never credentials, tokens or private links with keys in them: connectors are set up in each tool's settings.
- At most **15 sources**.

## Locked rules

These come from the generic system and nothing in the project can remove or weaken them. Extensions may add to them, never replace them.

| File | Locked |
|---|---|
| Everywhere | No secrets in the repo · agents never merge · `factory:go` is set by a person · review happens in a separate session · write for people in `project.language` · the *Project extensions* section of each job |
| `rules` | The whole file: extensions can only **Add** (extra DoR or DoD items, extra board rules) |
| `factory-implement` (skill and job) | "0. Status gate (before anything else)" · "Never" · job "1. Context" |
| `factory-review` (skill and job) | "0. Status gate (before anything else)" · "3. Answer" · "Never" · job "1. Context" |
| `ticket-quality` | "5. Gate: Definition of Ready" · "Don't" |
| `ticket` | "1. Story" · "3. Acceptance criteria" · "5. Context" |

## Length limits

Every run loads these files, so they stay short. Longer means more cost and agents that drift.

| File | Limit |
|---|---|
| `config.md` | One line per value · at most 20 `custom.*` keys |
| `status.md` | Notes and decisions one line each · at most 30 decisions (remove obsolete ones) |
| `context.md` | 15 sources, one row each |
| `extensions/<name>.md` | 60 lines each |
| `flows/<name>.md` | 80 lines each · 10 flows |
| `docs/` | No limit, but research and detail go in the claude.ai Project, not here |

If something doesn't fit, it is either a doc, a flow, or a change for the generic system.

## How Claude makes a change

1. **Understand and place it.** Say in one line which file and which kind of change (table above). If it is a change to the generic system, say so and stop.
2. **Check it against this file:** target heading exists, not locked, within limits, no secrets, no contradiction with `factory/rules.md` or another extension.
3. **Show the change** (new file or diff) and ask: **Apply it (recommended)** · **Adjust it** · **Cancel**.
4. **Apply it**, update `status.md` (row O11, and a one-line decision), and ask: **New branch and PR (recommended)** · **Push to the current branch** · **Don't commit yet**.
5. One change per commit, with a message that says what and why.

## Validation (status row O11)

The kickoff checks every project file on each *Verify* and after each plugin update:

- Each extension's `extends` is a valid name; every target heading exists in the current generic file; no Replace on a locked section; within 60 lines.
- Each flow has `name`, `use_when` and `uses`; `uses` names existing skills; within limits; nothing that bypasses a locked rule.
- `context.md` follows the table format, within 15 rows, with no credentials.
- Every `custom.*` key an extension uses exists in the config.
- No secrets in any file under `factory/`.

If a target heading disappeared after an update, the skill applying the extension ignores that block, follows the generic section, and reports it ("ATTENTION — extension <file>: section "<heading>" not found"). The kickoff then offers to fix the extension.
