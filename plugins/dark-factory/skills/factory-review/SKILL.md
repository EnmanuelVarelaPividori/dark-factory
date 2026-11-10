---
name: factory-review
description: Review a factory PR against its Jira ticket, the requirements and the repo conventions, in a session independent from whoever implemented it, and answer APPROVED or CHANGES with numbered findings. Use for "review PR X", when a PR has factory:review, or in each round of the review ↔ fix cycle, in any language.
---

# Factory review

You review as someone who didn't see how the code was written. Don't trust the PR description: check against the diff. Write the comment in `project.language`; the heading, verdict words and labels stay in English.

**Project extensions.** Before anything else, read these files if they exist and apply them as `factory/EXTENDING.md` says: `factory/project/extensions/factory-review.md`, `factory/project/extensions/rules.md`, and `factory/project/context.md` (sources this skill should use). **Add** blocks go in at their place, **Replace** blocks are followed instead of the generic section. Locked sections are never replaced. A block whose heading doesn't exist is ignored and reported as "ATTENTION — extension …".

## 0. Status gate (before anything else)

1. Read `factory/project/status.md`, section *Required to run*.
2. If any row is not ✅ or —:
   - **In a scheduled run:** don't touch any PR. End with one line: "ATTENTION — Review <jira.project> not ready: <IDs and items> (see factory/project/status.md)".
   - **With a person in the session:** show the missing rows and ask whether to continue anyway. Continue only on an explicit yes.
3. If the board says ✅ but something fails in practice, stop the same way and name it.

If `factory/project/status.md` doesn't exist, stop and ask for `/dark-factory:factory-kickoff` to be run.

## 1. Gather the context

1. `CLAUDE.md`, `factory/STRUCTURE.md`, `factory/rules.md` and `factory/project/config.md`.
2. The ticket: acceptance criteria, `R-x.y` requirements, type (core / configuration / client). Older tickets may use Spanish headings: see *Legacy names* in `factory/STRUCTURE.md`.
3. The story in `factory/project/docs/02-product-backlog.md` and the requirements cited in `factory/project/docs/01-requirements.md`, if those docs exist.
4. The Project docs in the ticket's **Context** section, if the session has access (`project_read` by path). Without access, review against the ticket and say so in the comment.
5. The full diff and the tests. If you can, run `repo.tests`.
6. Previous review rounds on the PR (any comment starting with `## factory-review`, in any language): don't repeat findings already resolved and don't change criteria between rounds.

## 2. Review, in this order

| # | Question | Blocks |
|---|---|---|
| 1 | Is each acceptance criterion met? Verify it in code or a test, one by one. | Yes |
| 2 | Does the PR do something the ticket doesn't ask for? (extra scope) | Yes |
| 3 | Does it respect the docs, the requirements and the decisions in the Project docs cited in Context? Does it contradict a business rule? | Yes |
| 4 | Are there tests for the new logic, and do they pass? | Yes |
| 5 | Does it break something existing: data, permissions, multi-company isolation, traceability? Does it add a secret to the repo? | Yes |
| 6 | Does it reuse what exists or open a parallel path? | Yes, if it duplicates a flow |
| 7 | Repo conventions, readability, naming | No (suggestion) |

Only report findings with a concrete failure scenario ("with X, Y happens"). No style opinions as blockers.

## 3. Answer

PR comment with this exact format:

```markdown
## factory-review — round {n}/{max}
**Verdict:** APPROVED | CHANGES

### Acceptance criteria
- AC1 ✅ verified in `tests/…`
- AC2 ❌ <what is missing>

### Findings
1. **[blocks]** `file:line` — <problem>. Scenario: <how it fails>. Suggestion: <what to do>.
2. **[suggestion]** …
```

- `APPROVED` only if there is no `[blocks]` finding. Set the `factory:approved` label and note on the ticket that it awaits human review.
- `CHANGES`: switch `factory:review` to `factory:changes` and hand back to `factory-implement`.
- In the last round without approval: `factory:needs-human` and a 3-line summary of the disagreement.

## Never

- Approve with acceptance criteria unverified.
- Ask for changes outside the ticket's scope (propose a new ticket).
- Make the changes yourself: the reviewer doesn't implement.
- Run past a status gate that failed without a person's explicit yes.
