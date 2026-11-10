# Job: close review

Suggested model: Opus. Read only: don't modify tickets or docs. The file keeps the name `sprint-review.md` so existing scheduled tasks keep working; it covers every `work.method`.

## 0. Is there something to close?

Read `factory/project/config.md`. This task runs every week; decide whether to report:

| Method | Report when | Period reviewed |
|---|---|---|
| Scrum | A sprint closed in the last 7 days (Jira `jira.cloud_id`, `jira.project`). Without Jira sprints: the weeks between `sprint.reference_date` and today are a multiple of `sprint.weeks` | The closed sprint |
| Kanban | Today is the first Monday of the month | The previous month |
| Waterfall | A milestone in `factory/project/docs/03-delivery-plan.md` was due or finished in the last 7 days | That milestone |

If not: end with a single line, "Nothing to close this week" (in `project.language`).

## 1. Period vs. plan

With `factory/project/docs/03-delivery-plan.md`:

- **Scrum:** SP committed, completed and carried over. Real vs. planned velocity. Planned tickets not done, and done ones not planned.
- **Kanban:** tickets finished, throughput per week, median cycle time, and how they compare with the previous month. Whether the top priorities in the plan were the ones that moved.
- **Waterfall:** milestone tickets done vs. planned, date met or missed (by how many days), tickets moved to the next milestone.

## 2. Requirements coverage

With `factory/project/docs/01-requirements.md` and `02-product-backlog.md` (skip this section if they don't exist yet, and say so):

- By priority (MUST / SHOULD / COULD): total requirements, with a story, with a Done story.
- MUST requirements without a story or with the story pending: list them.

## 3. Forecast

- **Scrum:** average velocity of the last 3 sprints (or as many as there are). Remaining sprints = pending SP ÷ velocity. Estimated end = end of this sprint + remaining sprints × `sprint.weeks` weeks.
- **Kanban:** average throughput of the last 4 weeks (or as many as there are). Remaining weeks = pending tickets ÷ throughput. Estimated end = today + remaining weeks.
- **Waterfall:** for each remaining milestone, its open tickets ÷ the recent pace vs. its date.

Compare with `project.target_date` (and with the milestone dates in waterfall). If it doesn't make it: what to cut first (COULD, then SHOULD), with size and keys, until the date fits.

## 4. Drift from the docs

Decisions visible in tickets or PRs that contradict `factory/project/docs/`. List them so someone updates the docs.

## Output

In `project.language`:

```
Close review — <sprint | month | milestone> — <project.name>
Verdict: on track | at risk | off schedule

Period: <scrum: X/Y SP, velocity Z vs. plan W | kanban: n tickets, throughput t/week, cycle time c days | waterfall: X/Y tickets, date met / missed by n days>
MUST requirements: a/b done · SHOULD: c/d · COULD: e/f
Estimated end: <date> (target <project.target_date>)

Risks
- …
Proposed cut (if needed)
- …
Docs to update
- …
Update in factory/project/config.md: <sprint.velocity_per_person | kanban.throughput_per_week> = <measured value>
```

Show the input numbers of each calculation so they can be checked.
