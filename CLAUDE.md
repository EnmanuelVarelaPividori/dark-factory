# Maintaining Dark Factory

This repo is the **system**, not a project. It holds the Claude Code plugin and the template copied into each project repo.

## Rules

1. **Nothing project-specific.** No client names, Jira keys, project repo URLs or concrete stacks. Examples use neutral values (`ABC`, `mycompany.atlassian.net`).
2. **English only.** Skills, template, keys, file names and examples are in English. What agents write for people follows each project's `project.language`; never hard-code another language.
3. **Generic files refer to the project only by fixed paths:** `factory/project/config.md` (by key, ex. `repo.base_branch`), `factory/project/status.md`, `factory/project/setup/` and `factory/project/docs/0X-*.md`. Never pasted values or `{{...}}` placeholders.
4. **`template/` has the same shape as a project repo root.** Whatever you add there shows up in every project on update.
5. **`template/factory/project/` is the project's starting skeleton:** copied once, never overwritten by the kickoff. Changes there don't reach existing projects; if they need migrating, add an entry to `plugins/dark-factory/skills/factory-kickoff/migrations.md`.
6. **Fixed paths and names.** Renaming or moving a file in `template/`, or renaming a config key, breaks references in skills, jobs and `STRUCTURE.md`. If needed, update every reference, add the migration, and add the old name to *Legacy names* in `STRUCTURE.md` when tickets or PRs may still cite it.
7. **Status board rows are a contract.** The runners gate on the *Required to run* IDs of `template/factory/project/status.md`, and the kickoff's *Verify* table checks them. Change both together.
8. **No secrets, ever.** Every project file is pushed and shared. Nothing in the template may ask for or store passwords, tokens or keys.
9. **Versioning.** Any change in `template/` or `skills/` bumps the version in `plugins/dark-factory/.claude-plugin/plugin.json` and in `plugins/dark-factory/template/factory/VERSION` (the same).
10. **Validate before committing:** `claude plugin validate .` and `claude plugin validate plugins/dark-factory`.

## Where each thing is

| I want to change… | File |
|---|---|
| Rules (DoR, DoD, WIP, labels, review, language) | `plugins/dark-factory/template/factory/rules.md` |
| Expected structure / instructions for agents / legacy names | `plugins/dark-factory/template/factory/STRUCTURE.md` |
| Scheduled task prompts | `plugins/dark-factory/template/factory/jobs/` |
| Config keys | `plugins/dark-factory/template/factory/project/config.md` + the skills and jobs that use them + a migration |
| Status board rows | `plugins/dark-factory/template/factory/project/status.md` + *Verify* in `factory-kickoff` + the gate in `factory-implement` / `factory-review` |
| Setup guides | `plugins/dark-factory/template/factory/project/setup/` + rounds 9–10 of `factory-kickoff` |
| A skill's behavior | `plugins/dark-factory/skills/<skill>/SKILL.md` |
| Install, interview, update, verify | `plugins/dark-factory/skills/factory-kickoff/SKILL.md` |
| Migrations between versions | `plugins/dark-factory/skills/factory-kickoff/migrations.md` |
