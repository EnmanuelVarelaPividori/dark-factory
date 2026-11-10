# Instructions for agents

<!-- Generic Dark Factory section. If this repo already has an AGENTS.md, this block is appended at the end without removing anything. -->

This repo uses Dark Factory. Before working, read in this order:

1. `factory/STRUCTURE.md` — expected structure and what may be touched.
2. `factory/rules.md` — Definition of Ready / Done, labels, review cycle, language.
3. `factory/project/config.md` — project values: Jira, base branch, conventions, test commands, `project.language`.
4. `factory/project/status.md` — what is set up and what is missing.
5. `factory/project/docs/` — scope: requirements (`01-…`) and backlog (`02-…`).
6. `factory/EXTENDING.md` — how this project extends the generic files: `factory/project/extensions/`, `flows/` and `context.md`. Project files win, except for locked rules.

The files in `factory/` outside `factory/project/` are generic: they are not edited in this repo. No file under `factory/` may hold secrets.
