# Ticket template

Title: `[LAYER] US-xxx — Verb + object` (layer only if the project uses prefixes; see `jira.title_prefixes` in the config).

Headings and field names stay in English exactly as below, so the runners can find them. Everything you write under them goes in `project.language`.

```markdown
**Epic:** EP-xx · **Requirements:** R-x.y, R-x.z · **Type:** core | configuration | client-specific
**Module:** <module> · **Priority:** MUST | SHOULD | COULD · **SP:** 1 | 2 | 3 | 5 (omit without estimates)
**Dependencies:** US-xxx (Done) · US-yyy (earlier in this sprint or milestone)

## 1. Story
AS A <role>
I WANT <action>
SO THAT <benefit>

## 2. Description and example
<What changes and why, in 2–4 sentences.>

Example: <a concrete case with real data: who, what they enter, what they see afterwards.>

## 3. Acceptance criteria
- [ ] AC1 — <verifiable condition; how it is checked>
- [ ] AC2 — …
- [ ] AC3 — …

## 4. Stakeholders
<Who validates and who uses the result.>

## 5. Context
- Requirements: R-x.y — <title> (`factory/project/docs/01-requirements.md`)
- Project «<claude.project>» › `<path/of/doc.md>` — <what to look at there and why> · required | reference
- Project «<claude.project>» › `<path/of/doc.md>` — …

## Notes for the factory (optional)
<Modules or files touched, technical constraints, out of scope for this ticket.>
```

## Context section

- Only when the project uses a claude.ai Project (`claude.project` is not `—`); otherwise `- Project: not applicable` and the ticket carries everything.
- Detailed research (research, decisions, diagrams, minutes) lives in the claude.ai Project, not in the repo. The ticket names it by **path inside the Project**.
- **The essentials go in the ticket.** If a Project decision defines how to implement, summarize it in the description or in an acceptance criterion; the doc is for going deeper.
- Mark each doc as **required** (it can't be implemented well without reading it) or **reference** (it helps, but the ticket is enough).
- No relevant docs in the Project: `- Project: not applicable`.

## Verifiable criteria

| Bad | Good |
|---|---|
| "The system is fast." | "Search answers in under 2 s with 5,000 records loaded." |
| "It can be approved." | "A user with the Approver role changes the status from Draft to Approved; who, when and the previous status are recorded, and the record can't be edited." |
| "Works fine with permissions." | "A user without approval permission gets 403 when trying to approve." |
| "Shows the company data." | "A user of company B sees no record of company A (automated test)." |
