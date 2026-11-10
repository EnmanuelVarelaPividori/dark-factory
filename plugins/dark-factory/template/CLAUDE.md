# Dark Factory

<!-- Generic Dark Factory section. If this repo already has a CLAUDE.md, this block is appended at the end without removing anything. -->

This repo uses Dark Factory. Structure, rules and project values:

@factory/STRUCTURE.md
@factory/rules.md
@factory/project/config.md

- Project scope: `factory/project/docs/` (`R-x.y` requirements in `01-requirements.md`, stories in `02-product-backlog.md`).
- Setup status of the repo and the organization: `factory/project/status.md`.
- Branch, commit, test and stack conventions: the `repo.*` keys of the config. Write for people in `project.language`.
- No secrets in any file under `factory/`: it is pushed and shared by the team.
- Project customizations (extensions, flows, context sources) live in `factory/project/` and are changed only through a chat with Claude, following `factory/EXTENDING.md`. When a request matches a flow in `factory/project/flows/`, follow that flow.
- Skills: `dark-factory:factory-implement` (implement), `dark-factory:factory-review` (review), `dark-factory:ticket-quality` (tickets), `dark-factory:factory-kickoff` (install, update, check status).
