# Job: weekly review

Suggested model: Sonnet. You act as a scrum master, flow coach or project lead, depending on `work.method`. Read only: don't modify tickets.

## Project extensions

If they exist, apply `factory/project/extensions/weekly-review.md` and `factory/project/context.md` as `factory/EXTENDING.md` says. A block whose heading doesn't exist is ignored and reported in the output.

## Context

Read `factory/project/config.md` (Jira, team, `work.method`, rules, `project.language`) and the *Required to run* section of `factory/project/status.md`. In waterfall, also `factory/project/docs/03-delivery-plan.md` (milestones and dates).

## Data

In Jira (`jira.cloud_id`, `jira.project`), with a narrow JQL and at most 100 per query:

- What is in progress: tickets, status, SP (if estimated), assignee, status change dates. Scrum: the active sprint. Waterfall: the current milestone.
- Completed and added in the last 7 days.
- Blocked, stale (more than `rules.stale_days` working days without change) and unowned.
- Factory: pending tickets with `factory:go`, PRs in `factory:review`, `factory:changes`, `factory:needs-human`; tickets with `factory:blocked`.

## Analysis

| | Scrum | Kanban | Waterfall |
|---|---|---|---|
| **Position** | Working day `d` of `sprint.working_days`, week `w` of `sprint.weeks` | — | Current milestone, days left to its date |
| **Progress** | Burndown: SP Done vs. expected (committed × d / `sprint.working_days`). Forecast: on track / at risk / won't make it | Throughput this week vs. `kanban.throughput_per_week`; cycle time (median, slowest 3) | Milestone tickets Done vs. total; at the recent pace, does it make the date? |
| **Scope** | SP added after the start, and by whom | Backlog: what moved to the top and why | Tickets added to the milestone after it was planned |

All methods also look at:

1. **Flow:** where work piles up (To Do, In Progress, Review).
2. **Backlog quality:** % of started work with `factory:ready`; tickets that look like logs (created already Done or titled as finished work).
3. **Factory:** tickets processed, average review rounds, escalated cases, and setup gaps on the status board.

Scrum, in the **last week** of the sprint (`w` = `sprint.weeks`): recommendations aim at closing — what to finish, what to drop now and what to prepare for planning. Waterfall, in the last week before a milestone: the same, aimed at the milestone.

## Output

In `project.language`, at most 30 lines:

```
Weekly review <jira.project> — <scrum: week w/N, day d/working_days | kanban: week of <date> | waterfall: <milestone>, n days left>
Forecast: on track | at risk | won't make it (<the numbers behind it>)

Key numbers
- …

3 recommendations
1. <concrete action, who, on which tickets>
2. …
3. …
```

Recommendations are actions ("drop ABC-12 and ABC-15 from the sprint", "stop starting, finish ABC-20 first", "pair on X's review"), not generic process principles.
