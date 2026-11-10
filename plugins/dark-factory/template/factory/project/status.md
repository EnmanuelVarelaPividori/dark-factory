---
doc: factory-status
description: Status board of Dark Factory in this project — what is set up in the repo and in the organization, what is missing and who owns it. Kept by factory-kickoff; read by the runners before they start.
---

# Factory status

Pushed and shared by the whole team. **Never write passwords, tokens, API keys or personal data here**: only names, IDs and states.

- **Refresh it:** run `/dark-factory:factory-kickoff` (or say "check the factory status"). It re-checks everything it can and commits the update after confirmation.
- **Did a ⏳ step by hand?** Mark it ✅ with your name and the date, or run the kickoff to verify it.
- **Legend:** ✅ done · ❌ missing · ⏳ assigned to a person · ⏭ skipped for now · — not applicable

Setup mode: — · Last updated: — · by —

## Required to run

The runners (`factory-implement`, `factory-review`) read this table first and **stop** if any row is not ✅ or —. Board checks still run, but report the gaps.

| ID | Item | Status | Owner | Updated | Note |
|---|---|---|---|---|---|
| R1 | Template installed and `factory/VERSION` = plugin version | ⏳ | | | |
| R2 | Config has no `TODO` in `project.*`, `jira.*`, `work.*`, `repo.*`, `factory.*` or the keys of the method in use | ⏳ | | | |
| R3 | `CLAUDE.md`, `AGENTS.md`, `.claude/settings.json` connected | ⏳ | | | |
| R4 | Jira reachable (Atlassian connector) | ⏳ | | | |
| R5 | GitHub PR labels exist | ⏳ | | | |
| R6 | Runner tasks created with push access, inside the claude.ai Project if one is used (— if `factory.trigger` = B) | ⏳ | | | |

## Recommended

| ID | Item | Status | Owner | Updated | Note |
|---|---|---|---|---|---|
| O1 | Planning docs present, requirements with `R-x.y` IDs | ⏳ | | | |
| O2 | Base branch protected: merge only with human review | ⏳ | | | |
| O3 | claude.ai Project set up as in `setup/claude-project.md` (— if not used) | ⏳ | | | |
| O4 | Config and docs copied into the Project with the same paths (— if not used) | ⏳ | | | |
| O5 | `ticket-quality` saved as an account skill in claude.ai | ⏳ | | | |
| O6 | Board checks created as chosen in `setup/scheduled-tasks.md` | ⏳ | | | |
| O7 | Model chosen in each scheduled task | ⏳ | | | |
| O8 | Plugin accepted in Claude Code by each developer | ⏳ | | | |
| O9 | dark-factory repo readable by people and tasks (— if public) | ⏳ | | | |
| O10 | Smoke test: one small ticket went through the full cycle | ⏳ | | | |

## Setup

| Phase | Status | Date |
|---|---|---|
| 1. Basics | ⏳ | |
| 2. How you work | ⏳ | |
| 3. Install | ⏳ | |
| 4. Organization | ⏳ | |

## Decisions

- —

## Pending

| Pending | Owner |
|---|---|
| — | |
