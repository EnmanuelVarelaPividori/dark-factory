# Setup checklist

`/dark-factory:factory-kickoff` walks through this list as a guided interview, does what it can itself, and records the result in `factory/project/status.md` (the IDs in brackets are the status rows). This list is the full reference; in Quick mode only the required rows (R) are set up first. Each block ends with a check.

## 1. Planning (claude.ai Project, optional)

Skip the Project items if the project doesn't use one; then tickets and `factory/project/docs/` must carry everything a runner needs.


- [ ] Project created with a description: what it is, for whom, goal. The instructions hold only domain context (glossary, business or client rules), not ticket rules or Jira data: those come from `factory/rules.md`, the skills and `factory/project/config.md`. Steps in `factory/project/setup/claude-project.md`. [O3]
- [ ] Markdown docs in the Project with the paths of `factory/project/docs/` (see `factory/project/docs/README.md`). [O4]
- [ ] Requirements with `R-x.y` IDs; every backlog story with its `Requirements` field. [O1]
- [ ] Contradictions between docs resolved and written down.
- [ ] Detailed research in the Project with stable paths (`research/`, `decisions/`, `diagrams/`); `claude.project` in the config with the Project's name.
- [ ] **Check:** in the Project, "which requirements does US-xxx cover?" answers the same as the backlog.

## 2. Repo

- [ ] Plugin installed and `/dark-factory:factory-kickoff` run.
- [ ] `factory/` with the template; `factory/VERSION` equal to the plugin's. [R1]
- [ ] `factory/project/config.md` complete (no `TODO` values), including `project.language`. [R2]
- [ ] `factory/project/docs/` with the 4 docs (the same as the Project, if one is used). [O1]
- [ ] `CLAUDE.md` imports `@factory/STRUCTURE.md`, `@factory/rules.md` and `@factory/project/config.md`; `AGENTS.md` points to `factory/STRUCTURE.md`. [R3]
- [ ] `.claude/settings.json` enables `dark-factory@dark-factory`. [R3]
- [ ] No secrets anywhere under `factory/`.
- [ ] **Check:** a new Claude Code session in the repo correctly answers "where is the backlog and how is a branch named?".

## 3. Jira and GitHub

- [ ] Jira reachable through the Atlassian connector. [R4]
- [ ] Epics created, with their key written in the backlog.
- [ ] Board statuses include "In Review".
- [ ] PR labels created on GitHub: `factory:review`, `factory:changes`, `factory:approved`, `factory:needs-human`. [R5]
- [ ] Base branch protected: merge only with human review. [O2]
- [ ] **Check:** a simple JQL on the project answers from the Project and from Claude Code.

## 4. Skills

- [ ] `ticket-quality` saved as an account skill in claude.ai (to create tickets from claude.ai). [O5]
- [ ] In Claude Code, `/plugin` shows `dark-factory` enabled, for each developer. [O8]
- [ ] If the dark-factory repo is private: everyone who uses the plugin and every scheduled task has read access. [O9]
- [ ] **Check:** a deliberately incomplete ticket makes `ticket-quality` ask instead of create; a duplicate is detected.

## 5. Scheduled tasks

- [ ] Schedule computed with `factory/jobs/README.md` from the config, and the chosen tasks written in `factory/project/setup/scheduled-tasks.md`.
- [ ] Board checks chosen in the interview (optional): daily (Haiku), weekly review (Sonnet; in scrum only with sprints of 2+ weeks), close review (Opus: sprint close, monthly forecast or milestone, per `work.method`). [O6]
- [ ] If `factory.trigger` = A: implement runner (Sonnet) and review runner (Opus), with push access to the repo, created **inside the Project** when one is used, so they read the docs cited in Context. [R6]
- [ ] Model chosen in each task's settings. [O7]
- [ ] All with the short prompt, the Atlassian connector, repo access and notifications per `reports.channels`.
- [ ] Automatic approval on the board checks (they only read).
- [ ] **Check:** each task run once by hand; the report arrives with correct data, in `project.language`.

## 6. Factory smoke test [O10]

- [ ] A small ticket (1–2 SP, or a day of work without estimates), Ready and with `factory:go`, goes all the way: PR with criteria checklist → review with verdict → fix if needed → `factory:approved` → human merge → ticket Done.
- [ ] With a Project: a ticket that cites a Project doc as required is read by the runner (the PR says "Context read").
- [ ] A ticket without `factory:ready` but with `factory:go` ends up `factory:blocked` with a comment saying what's missing, and no code.

## 7. Team

- [ ] The team knows the flow, the rules in `factory/rules.md` and where the status board is.
- [ ] Initial backlog cleanup if it comes from before: duplicates, log tickets and tickets without a requirement are closed or rewritten with `ticket-quality`.
- [ ] Real pace measured and updated in the config: velocity after 2 sprints (scrum) or throughput after 4 weeks (kanban).
- [ ] Improvements to the template found in this project are taken to the dark-factory repo, not edited in the local copy.
