---
name: factory-implement
description: Implement in Claude Code a Jira ticket labeled factory:ready + factory:go, from the ticket and the repo docs to a PR with the acceptance-criteria checklist, and fix factory-review findings. Use when a scheduled task or a person asks to "take ticket X", "process the factory queue" or "fix the review findings", in any language.
---

# Factory implement

Everything you write for people (ticket comments, PR body, replies to findings, the run report) goes in `project.language`. Headings, labels and file names stay in English.

## 0. Status gate (before anything else)

1. Read `factory/project/status.md`, section *Required to run*.
2. If any row is not ✅ or —:
   - **In a scheduled run:** don't touch any ticket or PR. End with one line: "ATTENTION — Factory <jira.project> not ready: <IDs and items> (see factory/project/status.md)".
   - **With a person in the session:** show the missing rows and ask whether to continue anyway. Continue only on an explicit yes.
3. If the board says ✅ but something fails in practice (Jira doesn't answer, a label doesn't exist, you can't push), stop the same way and name it: the board needs a refresh with `factory-kickoff`.

If `factory/project/status.md` doesn't exist, the repo predates the board: stop and ask for `/dark-factory:factory-kickoff` to be run.

## 1. Pre-flight (don't write code until it passes)

1. Read `CLAUDE.md`, `factory/STRUCTURE.md`, `factory/rules.md` and `factory/project/config.md`.
2. Fetch the ticket: summary, description, criteria, labels, dependencies and status. Tickets written before 0.7.0 may use Spanish headings and paths: read them through the *Legacy names* table in `factory/STRUCTURE.md`.
3. Check:
   - [ ] It has `factory:ready` and `factory:go`.
   - [ ] The acceptance criteria are verifiable.
   - [ ] Blocking dependencies are Done (or merged).
   - [ ] Size ≤ `rules.max_ticket_size`.
   - [ ] There is no other open PR for the same ticket.
   - [ ] The Project docs marked **required** in the Context section can be read (see step 2), or the ticket summarizes what's needed.
4. If anything fails: comment on the ticket what's missing, set `factory:blocked` and stop. Don't guess requirements.

## 2. Context

- Read the story in `factory/project/docs/02-product-backlog.md` and the `R-x.y` requirements it cites in `factory/project/docs/01-requirements.md`, if those docs exist. Without them, the ticket is the scope.
- Project docs in the ticket's **Context** section (only when `claude.project` is not `—`): if the session can access the Project (Projects tool), read each one by its path (`project_read`; if the path doesn't exist, `project_search` with the title). **Required** ones are always read; **reference** ones when the ticket leaves doubts.
- If a **required** doc can't be read and the ticket isn't enough to implement without guessing: comment on the ticket which doc is missing, set `factory:blocked` and stop.
- If a Project doc contradicts the ticket or `factory/project/docs/`: the ticket wins; comment the contradiction on the ticket and in the PR.
- Explore the related code before designing. Reuse what exists: don't open a path parallel to a flow that already does the same.
- If the ticket contradicts the docs or the existing code, say so on the ticket and in the PR; never pick silently.

## 3. Implement

- Branch per `repo.branch_pattern`, from `repo.base_branch`.
- Do only what the acceptance criteria ask. Out-of-scope findings go as a note in the PR (proposed ticket), not in the diff.
- Tests for new logic. Run `repo.tests` and make them pass.
- Commits per `repo.commit_pattern`.
- Never write secrets into the code or the repo.

## 4. Open the PR

PR body:

```markdown
Ticket: {KEY} — {title}
Requirements: R-x.y, …
Context read: <Project docs you read> · Not accessible: <if any>

## Acceptance criteria
- [x] AC1 — <how it is verified: test `…`, route, screenshot>
- [x] AC2 — …
- [ ] AC3 — <if it couldn't be done: why>

## Changes
<what and where, briefly>

## Out of scope / notes
<deviations, doubts, proposed tickets>
```

Then: comment the PR link on the ticket, move it to "In Review", set `factory:review` on the PR and remove `factory:wip` from the ticket.

## 5. Fix review findings

When the PR has `factory:changes` (the review answered `CHANGES`):

- Fix **only** the numbered findings. Don't reopen other parts.
- Answer each finding on the PR: fixed (with commit) or rejected (with a concrete reason).
- Switch `factory:changes` to `factory:review` to ask for a new review. The PR keeps the round count. At round `factory.review_rounds` without approval, set `factory:needs-human`, summarize the disagreement and stop.

## Never

- Merge.
- Touch tickets without `factory:go`.
- Change planning docs to match the code without saying so in the PR.
- Run past a status gate that failed without a person's explicit yes.
