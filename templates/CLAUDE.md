@AGENTS.md

## Claude Code specifics

The rules imported above from `AGENTS.md` are this project's mandatory
workflow, shared across every AI coding tool used on this repo. Claude Code
additionally has the `devflow` Skill installed at `.claude/skills/devflow/` —
the same workflow, with full body templates, `gh` commands, and edge cases.
Consult it (don't just work from memory) for any prompt that changes code,
tests, config, docs, or dependencies — the Skill should trigger automatically,
but invoke it explicitly if it doesn't.
