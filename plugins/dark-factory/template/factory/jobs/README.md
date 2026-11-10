# Scheduled tasks

Up to five tasks per project. The files in this folder are generic: each one is the full instruction of a job and reads the project's own values from `factory/project/` at run time. Which tasks are on in this project, with their schedule and model, is in `factory/project/setup/scheduled-tasks.md`, written by the kickoff.

| Job | File | Model | Runs if… |
|---|---|---|---|
| Daily check | `daily-check.md` | Haiku | Chosen in the interview (optional) |
| Mid-sprint review | `weekly-review.md` | Sonnet | Chosen in the interview and `sprint.weeks` ≥ 2 |
| Sprint close review | `sprint-review.md` | Opus | Chosen in the interview (optional) |
| Factory — implement | `factory-implement.md` | Sonnet | `factory.trigger` = A |
| Factory — review | `factory-review.md` | Opus | `factory.trigger` = A |

## 1. The prompt of every task

The same for all; only the repo, branch and job change:

```
Project repo: <repo.url> (branch <repo.base_branch>)
Clone the repo and run the job defined in factory/jobs/<job>.md. Follow that file exactly.
```

Suggested task name: `[<jira.project>] <job>`.

## 2. Schedule (comes from the config)

`D` = `sprint.start_day` (Monday = 1 … Friday = 5). Local time in `team.timezone`.

| Job | When | Cron |
|---|---|---|
| Daily check | Working days, 20 min before `team.daily_time` | `CRON_TZ=<zone> <mm> <hh> * * 1-5` |
| Mid-sprint review | Weekly, working day `D+2` | `CRON_TZ=<zone> 40 10 * * <D+2>` |
| Sprint close review | Weekly, day `D` in the morning. The job decides whether a sprint closed and, if not, ends in one line. | `CRON_TZ=<zone> 10 8 * * <D>` |
| Factory — implement | Every 2 h in working hours | `CRON_TZ=<zone> 12 8-18/2 * * 1-5` |
| Factory — review | Every 2 h, 1 h after the implementer | `CRON_TZ=<zone> 47 9-19/2 * * 1-5` |

The minutes avoid :00 and :30 on purpose: scheduled tasks pile up at those times and can be delayed. The close review runs weekly because cron can't count "every N weeks"; with N = 2, one run out of two ends in one line and costs very little.

### By sprint length (example: D = Monday, daily 09:30)

| N | Daily check | Mid-sprint | Close | Real Opus runs per month |
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
| A threshold, the team or the velocity | Edit `factory/project/config.md`. Tasks read it on their next run. |
| Sprint length, start day or daily time | Edit the config and run the kickoff: it recomputes the schedule and updates the tasks (creating or removing the mid-sprint one per N). |
| The project ends | Pause the tasks. |
