---
name: ticket-quality
description: Create or refine Jira tickets with Dark Factory quality — searches for duplicates, uses the ticket template, asks what's missing, applies the Definition of Ready and shows the draft before creating. Use for "create a ticket", "add this to the backlog", "turn this story into Jira", "refine this ticket", or when loading several tickets at once, in any language.
---

# Ticket quality

Turns a need into a ticket the factory can run without asking. The goal isn't creating tickets: it is that every ticket in the backlog stands for real, traceable, ready work.

## 0. Load the context

1. Find the project configuration in `factory/project/config.md`: in the repo or, inside a claude.ai Project, the doc with that same path (older projects: `factory/proyecto/config.md`). Rules in `factory/rules.md`. If there is no config, ask for the minimum (Jira key, cloudId, docs path, language) and continue.
2. Write the ticket and talk to the person in `project.language`. Headings and field names of the template stay in English.
3. Read the backlog story (`factory/project/docs/02-product-backlog.md`) and the requirements (`R-x.y`, in `factory/project/docs/01-requirements.md`) related to the request. If the request isn't in the docs, say so: it may be new scope. If the docs don't exist yet, say so once and continue from the request.

## 1. Classify the request

| If the request is… | Do this |
|---|---|
| New work, not done yet | Continue with step 2. |
| A log of something already done ("I fixed X", "I did Y") | Don't create a story. Find the ticket it belongs to and propose a comment or subtask there. If there is none, point out that the work was done without a ticket and propose one that meets the DoR, marked as done. |
| A bug | Bug template: steps to reproduce, expected vs. actual, environment, source ticket or PR if known. |
| Several things mixed | Split them into separate tickets before continuing. |

## 2. Search for duplicates

Before drafting, search Jira with a narrow JQL (few fields, at most 25 results):

- `project = {key} AND text ~ "{2–3 keywords}" AND statusCategory != Done`
- The same search by story ID (`US-xxx`) and by requirement (`R-x.y`).

If there are matches, show them and ask whether it's a duplicate, an extension of the existing one or something different. Don't create until you have an answer.

## 3. Draft with the template

Use the ticket template (`factory/templates/ticket.md`; if missing, `${CLAUDE_PLUGIN_ROOT}/template/factory/templates/ticket.md` or `plugins/dark-factory/template/factory/templates/ticket.md` in https://github.com/EnmanuelVarelaPividori/dark-factory): metadata, Story, Description with example, Acceptance criteria, Stakeholders and Context.

- Every acceptance criterion must be verifiable by looking at the system or a test. Rewrite vague ones ("works fine", "is fast").
- Each criterion defines a single condition.
- Trace to `R-x.y` requirements. If there is none, write why the ticket exists.
- Build the **Context** section. If the project doesn't use a claude.ai Project (`claude.project` is `—`), write `Project: not applicable` and make sure the ticket carries everything. Inside a claude.ai Project, search (`project_search`) for research, decisions or diagrams related to the request. List each doc with its path, what to look at and whether it is required or reference. Summarize in the description or criteria what's essential from each required doc: the runners may not have access to the Project. If nothing is relevant, `Project: not applicable`.
- Classify: core, configuration or client-specific. If a client request pushes client logic into the core, flag it and propose solving it with configuration.

## 4. Ask what's missing

If something is missing that you can't infer from the docs, ask, in one message and with at most 3 questions. Offer your best proposal for each, so the answer can be "yes". Worth asking: affected role, a real example, a measurable success criterion, a doubtful dependency.

Don't ask what's already in the docs.

## 5. Gate: Definition of Ready

Check the draft against this list and show the result:

- [ ] Story AS A / I WANT / SO THAT
- [ ] Concrete example
- [ ] Verifiable criteria
- [ ] Requirement `R-x.y` or justification
- [ ] Epic and dependencies
- [ ] ≤ `rules.max_sp_per_ticket` SP. If larger, propose the split before continuing.
- [ ] Type (core / configuration / client)
- [ ] Context section complete (or `not applicable`)
- [ ] No duplicates

If everything is OK, the ticket gets the `factory:ready` label. If something is missing, it is created without the label and with a comment saying what's missing.

## 6. Capacity and destination

- By default, the ticket goes to the **backlog**, not to the active sprint.
- If asked to put it in the active sprint: add up the sprint's current SP and compare with capacity (`team.size` × `sprint.velocity_per_person` from the config). If it exceeds it, say so and propose what to drop. Adding to the running sprint is a scope change: note it in the comment.

## 7. Show and create

1. Show the full draft (or a summary table if there are several: title, SP, priority, requirements, DoR status).
2. If there are more than `rules.batch_confirm` tickets, or any doubt, wait for explicit confirmation.
3. Before creating, fetch the issue type's fields (`getJiraIssueTypeMetaWithFields`).
4. Create with: type, `parent` = epic, priority per `jira.priority_map`, SP, labels and the description in markdown.
5. Answer with the created key and the DoR status. Don't summarize the content again.

## Don't

- Create tickets to log work already done.
- Create in batch without showing the draft.
- Set `factory:go`: a person decides that.
- Invent criteria the docs don't support without marking them as a proposal.
