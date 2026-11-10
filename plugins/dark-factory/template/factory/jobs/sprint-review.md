# Job: sprint close review

Suggested model: Opus. Read only: don't modify tickets or docs.

## 0. Did a sprint close?

Read `factory/project/config.md`. This task runs every week, but sprints last `sprint.weeks` weeks.

1. Look in Jira (`jira.cloud_id`, `jira.project`) for a sprint closed in the last 7 days.
2. If Jira has no sprints: weeks between `sprint.reference_date` and today. If it is not a multiple of `sprint.weeks`, nothing closed.
3. If nothing closed: end with a single line, "No sprint close this week" (in `project.language`).

## 1. Closed sprint vs. plan

With `factory/project/docs/03-sprint-plan.md`:

- SP committed, completed and carried over. Real vs. planned velocity.
- Planned tickets for that sprint that weren't done, and done ones that weren't planned.

## 2. Requirements coverage

With `factory/project/docs/01-requirements.md` and `02-product-backlog.md`:

- By priority (MUST / SHOULD / COULD): total requirements, with a story, with a Done story.
- MUST requirements without a story or with the story pending: list them.

## 3. Forecast

- Average velocity of the last 3 sprints (or as many as there are).
- Remaining sprints = pending SP ÷ velocity. Estimated end = end of this sprint + remaining sprints × `sprint.weeks` weeks.
- Compare with `project.target_date`. If it doesn't make it: what to cut first (COULD, then SHOULD), with SP and keys, until the date fits.

## 4. Drift from the docs

Decisions visible in tickets or PRs that contradict `factory/project/docs/`. List them so someone updates the docs.

## Output

In `project.language`:

```
Sprint close review — <sprint> — <project.name>
Verdict: on track | at risk | off schedule

Sprint: X/Y SP (real velocity Z vs. plan W)
MUST requirements: a/b done · SHOULD: c/d · COULD: e/f
Estimated end: <date> (target <project.target_date>)

Risks
- …
Proposed cut (if needed)
- …
Docs to update
- …
Update in factory/project/config.md: sprint.velocity_per_person = <measured value>
```

Show the input numbers of each calculation so they can be checked.
