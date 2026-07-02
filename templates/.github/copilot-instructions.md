# Repository instructions for GitHub Copilot

This repository's contribution workflow is mandatory for any prompt that
changes code, tests, config, docs, or dependencies. Read `AGENTS.md` at the
repository root before acting on such a prompt, and follow it exactly. Skip
it only for purely conversational or read-only requests that change nothing.

Golden rule: **Plan → Issue → Branch → Implement → Secrets Check → Save Plan
→ Confirm Commit → Confirm PR.**

Trivial changes (typos, docs, comments, formatting, routine version bumps)
skip the Issue and Save-plan steps — see the fast-path section in `AGENTS.md`.

Full detail — issue handling, branch naming, commit/PR-title format
(PRs are squash-merged into `main`), and secrets handling — is in `AGENTS.md`
and `.claude/skills/devflow/references/`.
