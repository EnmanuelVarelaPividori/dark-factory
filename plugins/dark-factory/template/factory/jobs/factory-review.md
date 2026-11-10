# Job: factory — review

Suggested model: Opus. You don't implement: you only review. Never commit to the PR and never merge. One PR per run.

## 1. Context

If the project uses a claude.ai Project (`claude.project` is not `—`), create this task **inside it**: that way the session can read the docs tickets cite in **Context**. Otherwise it is a standalone task.

1. Read `CLAUDE.md` (it imports `factory/project/config.md` and `factory/rules.md`).
2. Use the `dark-factory:factory-review` skill for everything below. If the session doesn't have the plugin, read the skill from https://github.com/EnmanuelVarelaPividori/dark-factory, file `plugins/dark-factory/skills/factory-review/SKILL.md`, and follow it the same way.
3. The skill starts with the status board gate. If it stops there, end with its one-line report: don't touch any PR.

## 2. Pick a single PR

The oldest with `factory:review` whose last commit is newer than its last `factory-review` comment (or that has none yet). If none: end with one line, "Review <jira.project>: no pending PRs".

## 3. Review

1. Round = previous `factory-review` comments on the PR + 1. Max: `factory.review_rounds`.
2. Review per the skill and post the comment in its format.
3. Labels per the verdict:
   - `APPROVED`: remove `factory:review`, set `factory:approved` and comment on the ticket that it awaits human review.
   - `CHANGES` and round < max: remove `factory:review`, set `factory:changes`.
   - `CHANGES` in the last round: `factory:needs-human` and a 3-line summary of the disagreement, on the PR and on the ticket.

## Output

In `project.language`, at most 5 lines: PR, round, verdict, link. If it ended approved, needs-human or setup not ready, start with "ATTENTION".
