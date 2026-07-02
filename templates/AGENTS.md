# Agent Instructions — devflow

**Mandatory for any prompt that changes anything that gets committed** — code,
tests, config, docs, dependencies. Skip only purely conversational or
read-only prompts. If borderline, say which way you're leaning before acting.

The authoritative workflow is the `devflow` skill at `.claude/skills/devflow/`.
If your tool loads skills, it triggers automatically. **If it doesn't — or
you're not sure it did — open `.claude/skills/devflow/SKILL.md` now and follow
it.** Everything below is only a summary; the skill's `references/` hold the
templates, commands, and edge cases for each step.

## The Golden Rule

> **Plan → Issue → Branch → Implement → Secrets Check → Save Plan → Confirm Commit → Confirm PR**

1. **Plan** — agree a short plan with the developer; the final version goes
   into the issue and the plan file.
2. **Issue** — a single standalone issue drives new work (no user stories, no
   sub-issues, no board). Continuing work on the current branch → a comment on
   the existing issue instead of a new one. → `references/issues.md`
3. **Branch** — new work: from `main`, named `<type>/<short-title>_<issueNumber>`.
   Continuing work: stay on the existing branch. → `references/branching.md`
4. **Implement** — keep the issue's task checklist and "Files Touched" in
   sync. Read `PROJECT.md` first to navigate (create it if missing).
5. **Secrets check** — before staging, scan for secrets (`.env`, keys,
   tokens, credentials). Found something → don't stage it, stop, ask.
   → `references/secrets.md`
6. **Save plan** — write `plans/<seq>-<branch-type>-<short-title>_<issueNumber>.md`
   and update `PROJECT.md`, in the same commit as the code.
   → `references/plans-and-project-map.md`
7. **Commit** — ask first ("stage and commit?"), confirm the file list, then
   commit. PRs are squash-merged, so per-commit messages can be casual — the
   strict Conventional Commit format lives on the PR title, not every commit.
   → `references/commits.md`
8. **PR** — ask first ("push and open a PR to `main`?"). The PR **title**
   must be a valid Conventional Commit (it becomes the squashed commit); the
   body references the issue with `Refs #<issueNumber>` (not auto-close).
   Never review, approve, or merge. → `references/pull-requests.md`

**Trivial-change fast path:** typo/comment/doc/formatting/routine-version-bump
changes skip the Issue and Save-plan steps entirely — just Branch → Implement
→ Secrets Check → Commit → PR. See the fast-path section in `SKILL.md`.

**New vs. continuing:** follow-up within the branch's scope → reuse issue and
branch; unrelated work → restart the pipeline from a new plan. State your
judgment if ambiguous.

**First time in this repo:** if `.github/` automation or the labels are
missing, run `references/repo-setup.md`.

## Tooling

`gh` CLI first; the GitHub MCP connector if `gh` is unavailable; otherwise
give the developer the exact commands/content to run manually. Never silently
skip a step — surface the blocker and still do the non-GitHub parts.
