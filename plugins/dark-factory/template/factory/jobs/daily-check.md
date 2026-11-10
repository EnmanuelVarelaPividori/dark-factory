# Job: daily check

Suggested model: Haiku. Read only: don't modify tickets.

## Context

1. Read `factory/project/config.md`. Every value comes from there: Jira (`jira.*`), team, `work.method`, rules and `project.language`.
2. Read the *Required to run* section of `factory/project/status.md`: if any row is not ✅ or —, add the **Setup** rule below.

## Data

Query Jira (`jira.cloud_id`, project `jira.project`) with a narrow JQL: key, summary, status, assignee, labels, `jira.sp_field`, `jira.sprint_field`, created, updated, due date; at most 100 per query.

- **Scrum:** the active sprint. Work out its working day `d` of `sprint.working_days` from its start date (without Jira sprints: `sprint.reference_date` and `sprint.weeks`).
- **Kanban:** everything not Done that has been started (In Progress, Review) plus what moved in the last 24 h.
- **Waterfall:** the tickets of the current milestone: the first milestone in `factory/project/docs/03-delivery-plan.md` whose date is today or later and that isn't finished.

## Rules to check

All methods:

1. **WIP:** people with more than `rules.wip_per_person` tickets In Progress.
2. **Not Ready:** tickets started without `factory:ready`, or larger than `rules.max_ticket_size`.
3. **No owner:** In Progress and unassigned.
4. **Stale:** In Progress without changes for more than `rules.stale_days` working days.
5. **Factory:** tickets with `factory:blocked` or `factory:needs-human`.
6. **Setup:** required rows of the status board that are not ✅ (IDs).

By method:

| Scrum | Kanban | Waterfall |
|---|---|---|
| **Capacity:** sprint SP vs. `team.size` × `sprint.velocity_per_person`; excess in SP and % | **Column WIP:** In Progress vs. `kanban.wip_in_progress` | **Milestone date:** days left vs. open tickets; flag if the open ones can't fit at the recent pace |
| **Added scope:** tickets that entered the sprint in the last 24 h; flag more than `rules.batch_confirm` from one person | **Aging:** started tickets older than twice the median cycle time of the last 4 weeks | **Added scope:** tickets added to the current milestone in the last 24 h |
| **Pace:** `d` > `sprint.working_days` / 2 and less than 30 % of the SP Done | — | **Overdue:** tickets of a past milestone still open |

## Output

In `project.language`, at most 15 lines:

```
Daily check <jira.project> — <date> · <scrum: day d/working_days | kanban: n in progress | waterfall: <milestone>, n days left>
Status: OK | ATTENTION (n rules broken)
- <rule>: <concrete data> (<keys>)
```

If nothing is broken, one line: "Daily check <jira.project> — nothing to report". No advice: that belongs to the weekly review.
