---
doc: factory-status
description: Status board of Dark Factory in this project — what is set up in the repo and in the organization, what is missing and who owns it. Kept by factory-kickoff; read by the runners before they start.
---

# Factory status

Pushed and shared by the whole team. **Never write passwords, tokens, API keys or personal data here**: only names, IDs and states.

- **Refresh it:** run `/dark-factory:factory-kickoff` (or say "check the factory status"). It re-checks everything it can and commits the update after confirmation.
- **Did a ⏳ step by hand?** Mark it ✅ with your name and the date, or run the kickoff to verify it.
- **Legend:** ✅ done · ❌ missing · ⏳ waiting for a person · — not applicable

Last updated: — · by —

## Required to run

The runners (`factory-implement`, `factory-review`) read this table first and **stop** if any row is not ✅ or —. Board checks still run, but report the gaps.

| ID | Item | Status | Owner | Updated | Note |
|---|---|---|---|---|---|
| R1 | Template installed and `factory/VERSION` = plugin version | ⏳ | | | |
| R2 | Config has no `TODO` in `project.*`, `jira.*`, `repo.*`, `factory.*` | ⏳ | | | |
| R3 | Planning docs present, requirements with `R-x.y` IDs | ⏳ | | | |
| R4 | `CLAUDE.md`, `AGENTS.md`, `.claude/settings.json` connected | ⏳ | | | |
| R5 | Jira reachable (Atlassian connector) | ⏳ | | | |
| R6 | GitHub PR labels exist | ⏳ | | | |
| R7 | Base branch protected: merge only with human review | ⏳ | | | |
| R8 | Runner tasks created inside the claude.ai Project, with push access (only if `factory.trigger` = A) | ⏳ | | | |
| R9 | Runner tasks have a model chosen | ⏳ | | | |

## Recommended

| ID | Item | Status | Owner | Updated | Note |
|---|---|---|---|---|---|
| O1 | claude.ai Project set up as in `setup/claude-project.md` | ⏳ | | | |
| O2 | Config and docs copied into the Project with the same paths | ⏳ | | | |
| O3 | `ticket-quality` saved as an account skill in claude.ai | ⏳ | | | |
| O4 | Board checks created as in `setup/scheduled-tasks.md` (optional, chosen in the interview) | ⏳ | | | |
| O5 | Plugin accepted in Claude Code by each developer | ⏳ | | | |
| O6 | dark-factory repo readable by people and tasks (only if private) | ⏳ | | | |
| O7 | Smoke test: one small ticket went through the full cycle | ⏳ | | | |

## Setup interview

| # | Round | Status | Date |
|---|---|---|---|
| 1 | Language and access | ⏳ | |
| 2 | Project | ⏳ | |
| 3 | Planning docs | ⏳ | |
| 4 | Jira | ⏳ | |
| 5 | Team and sprint | ⏳ | |
| 6 | Repo | ⏳ | |
| 7 | Factory rules | ⏳ | |
| 8 | Install | ⏳ | |
| 9 | claude.ai Project | ⏳ | |
| 10 | Scheduled tasks | ⏳ | |
| 11 | GitHub | ⏳ | |
| 12 | Verify | ⏳ | |
| 13 | Smoke test | ⏳ | |

## Decisions

- —

## Pending

| Pending | Owner |
|---|---|
| — | |
