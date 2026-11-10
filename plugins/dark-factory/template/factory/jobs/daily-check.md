# Job: daily check

Suggested model: Haiku. Read only: don't modify tickets.

## Context

1. Read `factory/project/config.md`. Every value comes from there: Jira (`jira.*`), team, sprint, rules and `project.language`.
2. Sprint capacity = `team.size` × `sprint.velocity_per_person` SP.
3. Read the *Required to run* section of `factory/project/status.md`: if any row is not ✅ or —, add rule 9 below.

## Data

Query Jira (`jira.cloud_id`, project `jira.project`) for the active sprint with a narrow JQL: key, summary, status, assignee, labels, `jira.sp_field`, `jira.sprint_field`, created, updated; at most 100 per query.

Work out the sprint's working day `d` of `sprint.working_days`, from the active sprint's start date. If Jira has no sprints, use `sprint.reference_date` and `sprint.weeks`.

## Rules to check

1. **WIP:** people with more than `rules.wip_per_person` tickets In Progress.
2. **Capacity:** sprint SP vs. capacity; excess in SP and %.
3. **Added scope:** tickets that entered the sprint in the last 24 h; flag more than `rules.batch_confirm` from one person.
4. **Not Ready:** sprint tickets without `factory:ready` or with more than `rules.max_sp_per_ticket` SP.
5. **No owner:** In Progress and unassigned.
6. **Stale:** In Progress without changes for more than `rules.stale_days` working days.
7. **Factory:** tickets with `factory:blocked` or `factory:needs-human`.
8. **Pace:** if `d` > `sprint.working_days` / 2 and less than 30 % of the SP is Done.
9. **Setup:** required rows of the status board that are not ✅ (IDs).

## Output

In `project.language`, at most 15 lines:

```
Daily check <jira.project> — <date> · day <d>/<sprint.working_days>
Status: OK | ATTENTION (n rules broken)
- <rule>: <concrete data> (<keys>)
```

If nothing is broken, one line: "Daily check <jira.project> — day <d>/<sprint.working_days>, nothing to report". No advice: that belongs to the mid-sprint review.
