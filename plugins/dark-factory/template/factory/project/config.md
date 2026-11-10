---
doc: factory-config
description: Dark Factory configuration for this project. Read by the skills, the scheduled jobs and CLAUDE.md. Written by factory-kickoff; anything unknown stays TODO.
---

# Project configuration

This file is pushed and shared by the whole team. **Never put passwords, tokens, API keys or other secrets here**: only names, IDs and settings.

## Project

| Key | Value | What goes here |
|---|---|---|
| `project.name` | TODO | Project name |
| `project.goal` | TODO | One sentence: what every prioritization is measured against |
| `project.target_date` | TODO | YYYY-MM-DD |
| `project.language` | TODO | Language for everything the agents write for people: tickets, PRs, comments, reports, generated docs and the kickoff interview. Ex. `English`, `Spanish` |

## Jira

| Key | Value | What goes here |
|---|---|---|
| `jira.site` | TODO | Ex. `mycompany.atlassian.net` |
| `jira.cloud_id` | TODO | Site UUID |
| `jira.project` | TODO | Project key, ex. `ABC` |
| `jira.board_id` | TODO | Board that holds the sprints; `—` if Jira sprints are not used |
| `jira.issue_types` | TODO | IDs of Story / Epic / Task / Bug / Subtask |
| `jira.sp_field` | TODO | Story points field, ex. `customfield_10016` |
| `jira.sprint_field` | TODO | Sprint field, ex. `customfield_10020` |
| `jira.priority_map` | TODO | MUST / SHOULD / COULD → priority id |
| `jira.title_prefixes` | — | Layer prefixes in titles, if used (ex. `[BE]`, `[FE]`) |

## claude.ai Project

| Key | Value | What goes here |
|---|---|---|
| `claude.project` | TODO | Name of the claude.ai Project with the detailed research. Tickets cite it in **Context** |

## Team and sprint

| Key | Value | What goes here |
|---|---|---|
| `team.members` | TODO | Jira accounts of the people who develop |
| `team.size` | TODO | Number of people who develop |
| `team.timezone` | TODO | IANA zone, ex. `Europe/Amsterdam` |
| `team.daily_time` | TODO | HH:MM |
| `sprint.weeks` | TODO | N. Sets the schedule of the scheduled tasks |
| `sprint.start_day` | TODO | Weekday each sprint starts on |
| `sprint.reference_date` | TODO | Start date of any real sprint (anchor for counting cycles) |
| `sprint.working_days` | TODO | N × 5 |
| `sprint.velocity_per_person` | TODO | SP per person per sprint. Measured, not wished for; update at every sprint close. Mark `(estimated)` until measured |

## Rules

| Key | Value | What goes here |
|---|---|---|
| `rules.wip_per_person` | 2 | Tickets In Progress per person |
| `rules.max_sp_per_ticket` | 5 | Max SP for a ticket to enter a sprint |
| `rules.batch_confirm` | 5 | Tickets per batch that require confirmation before creating |
| `rules.stale_days` | TODO | Working days without change = stale. Rule: `max(2, N + 1)` |

## Repo

| Key | Value | What goes here |
|---|---|---|
| `repo.url` | TODO | Repo URL |
| `repo.base_branch` | TODO | Branch PRs go to |
| `repo.branch_pattern` | TODO | Ex. `{jira_key}-{slug}` |
| `repo.commit_pattern` | TODO | Ex. `{jira_key}: {summary}` |
| `repo.tests` | TODO | Test commands (the same ones CI runs) |
| `repo.stack` | TODO | What the implementer needs to know: language, framework, folders with own code, what is out of scope, preferences (ex. libraries before custom code) |

## Factory and reports

| Key | Value | What goes here |
|---|---|---|
| `factory.trigger` | A | A: label + scheduled task · B: automation |
| `factory.review_rounds` | 3 | Max review rounds before `factory:needs-human` |
| `reports.channels` | push + email | push, email, Jira comment |
