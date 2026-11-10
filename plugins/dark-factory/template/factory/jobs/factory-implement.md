# Job: factory — implement

Suggested model: Sonnet. Needs push access to the project repo. Never merge. One piece of work per run.

## 1. Context

If the project uses a claude.ai Project (`claude.project` is not `—`), create this task **inside it**: that way the session can read the docs tickets cite in **Context**. Otherwise it is a standalone task.

1. Read `CLAUDE.md` (it imports `factory/project/config.md` and `factory/rules.md`).
2. Use the `dark-factory:factory-implement` skill for everything below. If the session doesn't have the plugin, read the skill from https://github.com/EnmanuelVarelaPividori/dark-factory, file `plugins/dark-factory/skills/factory-implement/SKILL.md`, and follow it the same way.
3. The skill applies the project extensions (`factory/project/extensions/factory-implement.md`, which also covers this job) and starts with the status board gate. If it stops there, end with its one-line report: don't touch any ticket.

## 2. Pick a single piece of work

In this order:

1. An open PR with `factory:changes`: fix the findings of the last review.
2. If none: the `jira.project` ticket with `factory:go`, without `factory:wip`, without `factory:blocked` and without an open PR. First by Jira priority, then by age.
3. If none at all: end with one line, "Factory <jira.project>: no work".

## 3. Work

1. Set `factory:wip` on the ticket. Remove it when you finish, also if you fail.
2. Follow the skill: pre-flight, implementation, tests (`repo.tests`), PR; or fixing findings.
3. When done, the PR is left with `factory:review`, or the ticket with `factory:blocked` or `factory:needs-human`.

## Output

In `project.language`, at most 5 lines: ticket, what happened, link to the PR. If the result needs a person (blocked, needs-human, setup not ready), start with "ATTENTION".
