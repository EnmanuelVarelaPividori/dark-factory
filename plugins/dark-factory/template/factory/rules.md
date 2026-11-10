# Factory rules

Numeric values (WIP, max ticket size, rounds…) and the way of working (`work.method`: scrum, kanban or waterfall) live in `factory/project/config.md`. The rules live here; where they differ by method, it says so.

## Definition of Ready

A ticket can be started — enter a sprint (scrum), be pulled into In Progress (kanban) or be scheduled in a milestone (waterfall) — and enter the factory only if it meets all of these:

- [ ] Story in AS A / I WANT / SO THAT form.
- [ ] Description with a concrete example.
- [ ] Verifiable acceptance criteria: each one can be marked met / not met by looking at the system or a test.
- [ ] Traced to a requirement `R-x.y` in `factory/project/docs/01-requirements.md` or, if there is none, an explicit justification.
- [ ] Epic assigned and dependencies listed; blocking ones are Done, or earlier in the same sprint.
- [ ] Size ≤ `rules.max_ticket_size` (in SP with story points, in working days without estimates). If larger, it is split before entering.
- [ ] Type: core, configuration or client-specific.
- [ ] No duplicates in the backlog.
- [ ] A **Context** section with the Project docs the implementer has to read (path + why + required / reference), or `not applicable`. The essentials of those docs are summarized in the ticket.

Only a ticket that meets all of them gets `factory:ready`. Without that label, the factory doesn't touch it.

## Definition of Done

- [ ] Every acceptance criterion verified in the PR, one by one, with evidence (test, screenshot or verification step).
- [ ] Automated review approved and human review approved.
- [ ] Tests (`repo.tests`) pass in CI.
- [ ] Docs in `factory/project/docs/` updated if the ticket changed something they describe.
- [ ] Ticket in Done with a link to the merged PR.

## Board rules

- **WIP:** at most `rules.wip_per_person` tickets In Progress per person.
- **Capacity:**
  - Scrum: the sprint's total SP doesn't exceed `team.size × sprint.velocity_per_person`. What doesn't fit goes to the backlog.
  - Kanban: In Progress doesn't exceed `kanban.wip_in_progress`. Pull the next ticket only when one finishes.
  - Waterfall: the tickets of a milestone fit before its date at the measured pace; if not, the milestone's scope or date is renegotiated, not silently stretched.
- **No log tickets.** If the work is already done, it goes as a comment or subtask on the existing ticket. If there is no ticket, one that meets the DoR is created before continuing the work, not after.
- **Batches:** more than `rules.batch_confirm` tickets in one load requires showing the draft and confirming before creating.
- **Scope changes:** adding to the running sprint (scrum) or to a milestone already planned (waterfall) is a scope change: the daily check reports it. In kanban, reprioritizing the backlog is normal; what counts is WIP.

## Labels

| Label | Where | Who sets it | Means |
|---|---|---|---|
| `factory:ready` | Ticket | `ticket-quality` | Meets the Definition of Ready. |
| `factory:go` | Ticket | A person | Authorizes the factory to take it. |
| `factory:wip` | Ticket | Implement runner | A run is working on it; stops two runs taking the same one. |
| `factory:blocked` | Ticket | Implement runner | Failed the pre-flight; the comment says what is missing. |
| `factory:review` | PR | Implement runner | PR ready for review. |
| `factory:changes` | PR | Review runner | Findings to fix. |
| `factory:approved` | PR | Review runner | Ready for the developer. |
| `factory:needs-human` | PR and ticket | Any runner | No agreement after the last round, or something it can't solve alone. |

## Review cycle

1. The implement runner opens the PR with the acceptance-criteria checklist and sets `factory:review`.
2. The review runner runs in a **new** session, without the implementer's reasoning. It answers `APPROVED` or `CHANGES` with numbered findings.
3. With `CHANGES` it sets `factory:changes`; the implementer fixes only those findings and goes back to `factory:review`.
4. At most `factory.review_rounds` rounds. No approval in the last one: `factory:needs-human` and a summary of the disagreement. Without a limit, two agents can argue forever.
5. With `APPROVED`: `factory:approved`, and the developer reviews and merges.

## Language

Everything the agents write for people — tickets, PR bodies, review comments, Jira comments, reports, generated docs — is written in `project.language`. Labels, file names, config keys, verdict words (`APPROVED`, `CHANGES`) and the section headings of the ticket template stay in English, so the agents can find them.

## Where each kind of information lives

| Information | Where | Who reads it |
|---|---|---|
| Project values (Jira, team, repo, rules, language) | `factory/project/config.md` | Everyone |
| What is set up and what is missing | `factory/project/status.md` | Everyone; the runners before starting |
| General scope: problem, `R-x.y` requirements, backlog, plan | `factory/project/docs/` (repo) and its copy in the Project | Everyone |
| Detailed research, decisions, diagrams, minutes | claude.ai Project (`claude.project` in the config) | Planners; the runners when the ticket asks in **Context** |
| What's needed to implement a ticket | The ticket | Implementer and reviewer |
| Passwords, tokens, keys | **Never in the repo.** In the tools' own settings (connectors, CI secrets) | — |

## Never

- Merge without a person.
- Work a ticket without `factory:ready` and `factory:go`.
- Change a ticket's scope: extras are proposed as new tickets.
- Pick silently when a ticket contradicts the docs: comment on the ticket and on the PR.
- Write secrets in any file of the repo.
