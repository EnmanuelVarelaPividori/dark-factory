# Job: mid-sprint review

Suggested model: Sonnet. You act as a scrum master. Read only: don't modify tickets.

## Context

Read `factory/project/config.md` (Jira, team, sprint, rules, `project.language`). Capacity = `team.size` × `sprint.velocity_per_person` SP. Read the *Required to run* section of `factory/project/status.md`.

## Data

In Jira (`jira.cloud_id`, `jira.project`), with a narrow JQL and at most 100 per query:

- Active sprint: tickets, status, SP, assignee, status change dates.
- Completed and added to the sprint in the last 7 days.
- Blocked, stale (more than `rules.stale_days` working days without change) and unowned.
- Factory: pending tickets with `factory:go`, PRs in `factory:review`, `factory:changes`, `factory:needs-human`; tickets with `factory:blocked`.

## Analysis

1. **Position:** working day `d` of `sprint.working_days` and week `w` of `sprint.weeks`.
2. **Burndown:** SP Done vs. expected at this point (linear: committed × d / `sprint.working_days`). Forecast: on track / at risk / won't make it.
3. **Flow:** where work piles up (To Do, In Progress, Review).
4. **Scope:** SP added after the start and by whom.
5. **Backlog quality:** % of the sprint with `factory:ready`; tickets that look like logs (created already Done or titled as finished work).
6. **Factory:** tickets processed, average review rounds, escalated cases, and setup gaps on the status board.

In the **last week** of the sprint (`w` = `sprint.weeks`), recommendations aim at closing: what to finish, what to drop now and what to prepare for planning.

## Output

In `project.language`, at most 30 lines:

```
Mid-sprint review <jira.project> — week <w>/<N>, day <d>/<working_days>
Forecast: on track | at risk | won't make it (Done X / expected Y / committed Z SP)

Key numbers
- …

3 recommendations
1. <concrete action, who, on which tickets>
2. …
3. …
```

Recommendations are actions ("drop ABC-12 and ABC-15 from the sprint", "pair on X's review"), not generic scrum principles.
