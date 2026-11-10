# Scheduled tasks

Up to five tasks per project. The files in this folder are generic: each one is the full instruction of a job and reads the project's own values from `factory/project/` at run time, including the way of working (`work.method`: scrum, kanban or waterfall). Which tasks are on in this project, with their schedule and model, is in `factory/project/setup/scheduled-tasks.md`, written by the kickoff.

| Job | File | Model | Scrum | Kanban | Waterfall | Runs if… |
|---|---|---|---|---|---|---|
| Daily check | `daily-check.md` | Haiku | Board rules before the daily | Same, with flow rules | Same, with milestone rules | Chosen in the interview |
| Weekly review | `weekly-review.md` | Sonnet | Mid-sprint review | Flow review | Milestone progress review | Chosen in the interview (scrum: only if `sprint.weeks` ≥ 2) |
| Close review | `sprint-review.md` | Opus | At each sprint close | Monthly forecast | At each milestone | Chosen in the interview |
| Factory — implement | `factory-implement.md` | Sonnet | — | — | — | `factory.trigger` = A |
| Factory — review | `factory-review.md` | Opus | — | — | — | `factory.trigger` = A |

The close review keeps the file name `sprint-review.md` so existing scheduled tasks keep working; it handles all three methods.

## 1. The prompt of every task

The same for all; only the repo, branch and job change:

```
Project repo: <repo.url> (branch <repo.base_branch>)
Clone the repo and run the job defined in factory/jobs/<job>.md. Follow that file exactly.
```

Suggested task name: `[<jira.project>] <job>`.

## 2. Schedule (comes from the config)

Local time in `team.timezone`. Daily check time: 20 min before `team.daily_time`, or 08:40 if there is no daily.

| Job | Scrum | Kanban | Waterfall |
|---|---|---|---|
| Daily check | `CRON_TZ=<zone> <mm> <hh> * * 1-5` | same | same |
| Weekly review | Weekly on working day `D+2`, `D` = `sprint.start_day`: `CRON_TZ=<zone> 40 10 * * <D+2>` | Weekly on `work.review_day`: `CRON_TZ=<zone> 40 10 * * <day>` | Same as kanban |
| Close review | Weekly on day `D`: `CRON_TZ=<zone> 10 8 * * <D>`. The job reports only if a sprint closed. | Weekly on Monday: `CRON_TZ=<zone> 10 8 * * 1`. The job reports only on the first Monday of the month. | Weekly on Monday: `CRON_TZ=<zone> 10 8 * * 1`. The job reports only if a milestone was due or finished in the last 7 days. |
| Factory — implement | `CRON_TZ=<zone> 12 8-18/2 * * 1-5` | same | same |
| Factory — review | `CRON_TZ=<zone> 47 9-19/2 * * 1-5` | same | same |

Days: Monday = 1 … Friday = 5. The minutes avoid :00 and :30 on purpose: scheduled tasks pile up at those times and can be delayed. The close review runs weekly because cron can't count "every N weeks" or "first Monday of the month" reliably (day-of-month and day-of-week are OR'd); the job decides, and a run with nothing to report ends in one line and costs very little.

### Scrum by sprint length (example: D = Monday, daily 09:30)

| N | Daily check | Weekly review | Close | Real Opus runs per month |
|---|---|---|---|---|
| 1 week | Mon–Fri 09:10 | — | Monday 08:10 | ~4 |
| 2 weeks | Mon–Fri 09:10 | Wednesday 10:40 | Monday 08:10 | ~2 |
| 3 weeks | Mon–Fri 09:10 | Wednesday 10:40 | Monday 08:10 | ~1.3 |
| 4 weeks | Mon–Fri 09:10 | Wednesday 10:40 | Monday 08:10 | 1 |

## 3. Creating the tasks

With the plugin: `/dark-factory:factory-kickoff` (phase 4) computes the schedule, asks which board checks to turn on, creates the tasks itself when the session can, runs each one once, and writes `factory/project/setup/scheduled-tasks.md`.

By hand, for each chosen job:

1. New scheduled task with the name and prompt from section 1.
2. Schedule per section 2.
3. For the two runners: if the project uses a claude.ai Project, create the task **inside it**, so they can read the docs tickets cite in Context. Connectors: Atlassian. Project repo with read (checks) or push (runners). dark-factory repo with read if private.
4. Model: the one in the table above, in the task's settings.
5. Notifications per `reports.channels`.
6. Automatic approval on the three board checks (they only read).
7. Run it once by hand and check the result.
8. Mark it in `factory/project/status.md` and in `factory/project/setup/scheduled-tasks.md`.

## 4. Maintenance

| When this changes… | Do this |
|---|---|
| A threshold, the team, the velocity or the throughput | Edit `factory/project/config.md`. Tasks read it on their next run. |
| The method, sprint length, start day, review day or daily time | Run the kickoff: it updates the config, recomputes the schedule and updates the tasks. |
| The project ends | Pause the tasks. |
