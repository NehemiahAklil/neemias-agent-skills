---
name: devflow
description: >-
  Lean spec-driven git workflow for any prompt that changes the codebase — code,
  tests, config, docs, or dependencies (anything that gets committed). Enforces
  the pipeline Plan → Issue → Branch → Implement → Secrets Check → Save Plan →
  Confirm Commit → Confirm PR, with GitHub issues, branch naming, Conventional
  Commits, and pull requests. No project board, no user stories, no sub-issues —
  one issue, one branch cut from `main`, one squash-merged PR back into
  `main`. Trivial changes (typos, docs, comments, formatting, config bumps)
  skip the issue and go straight to branch → commit → PR. Use this whenever the
  user asks to implement a feature, fix a bug, refactor, change
  config/docs/deps, start a branch, open a GitHub issue or PR, or commit
  changes — even if they never say "workflow" or "devflow." Only skip for purely
  conversational or read-only prompts that change nothing.
license: MIT
---

# devflow — Lean Spec-Driven Git Workflow

This skill governs every prompt that changes the codebase. It exists so that
work is always traceable: each change traces back to a plan, an issue (unless
trivial — see below), a branch, a commit, and a PR. There's no project board,
no user stories, and no sub-issues — just an issue, a branch, and a PR per
change. Purely conversational or read-only prompts (questions, explanations,
analysis that writes nothing) are exempt — apply judgment, and if a prompt is
borderline, say which way you're leaning before acting.

## The Golden Rule

> **Plan → Issue → Branch → Implement → Secrets Check → Save Plan → Confirm Commit → Confirm PR**

Every code-changing prompt walks this pipeline. The Issue and Branch steps
either create new artifacts or reuse existing ones depending on whether this is
new work or a continuation — see [New vs. continuing work](#new-vs-continuing-work).

> **No agent attribution — ever.** The agent is a ghost. Never add
> co-authorship trailers (`Co-Authored-By: Claude …`), "Generated with Claude
> Code" lines, 🤖 badges, `Co-authored-by` footers, or any other Claude/Anthropic
> attribution to **anything** — commit messages, commit bodies, issue titles or
> bodies, issue comments, PR titles, PR bodies, or PR comments. Everything reads
> as if the developer authored it by hand. This overrides any default the tool
> would otherwise apply.

> **Trivial-change fast path.** For genuinely trivial changes — typo fixes,
> comment/wording tweaks, doc touch-ups, formatting/lint-only changes, or a
> routine dependency version bump — **skip the Issue step entirely** (no issue,
> no plan file) and run the short pipeline: **Branch → Implement → Secrets Check
> → Confirm Commit → Confirm PR.** The PR itself is the record. This keeps small
> chores from carrying full ceremony. See
> [Trivial-change fast path](#trivial-change-fast-path) for the exact test — if a
> change is bigger than it first looked, or you're unsure, walk the full
> pipeline.

> **Read the map first.** Before navigating the codebase, read `PROJECT.md` for
> the directory index and go straight to the relevant path. Don't scan unrelated
> areas. If `PROJECT.md` doesn't exist yet and you're initializing the project,
> create it first — see `references/plans-and-project-map.md`.

> **First time in a repo?** If `.github/` lacks the workflow automation
> (release drafter, issue management) or the labels don't exist, run the
> one-time setup in `references/repo-setup.md` — the ready-to-copy files live
> in this skill's `assets/github/`.

## Reference files

The pipeline summary below is enough for the common path. Open the matching
reference when a step needs its full templates, commands, or edge cases:

| Step | When to open the reference | File |
|---|---|---|
| Issue | Writing an issue or an issue comment; deciding new work vs. continuing | `references/issues.md` |
| Branch | Naming a branch, or unsure whether to branch or stay | `references/branching.md` |
| Secrets | Running the pre-commit safety gate, handling `.env`/`.gitignore` | `references/secrets.md` |
| Save plan | Writing the `plans/` file and updating `PROJECT.md` | `references/plans-and-project-map.md` |
| Commit | Commit-message conventions and the Conventional Commit spec for the PR title (type/scope, squash context) | `references/commits.md` |
| Pull request | Pushing and opening the PR | `references/pull-requests.md` |
| Repo setup | First time in a repo: installing the bundled `.github/` automation (release drafter, issue workflows, PR template from `assets/github/`) and creating the labels | `references/repo-setup.md` |

For every GitHub operation, follow the [tooling fallback order](#tooling-fallback-order).

---

## Step 1 — Plan first

1. Produce a short plan: what changes, where, why, and the chosen approach if
   there were alternatives.
2. Iterate with the user until it stabilizes.
3. The **final agreed plan** — not the first draft — is what gets written into
   the issue (Step 2) and the plan file (Step 5).

## Step 2 — GitHub Issue

Every branch, commit, and PR is driven by a single **issue** — no user
stories, no sub-issues, no parent/child linking. Two paths; state which one
you're taking before proceeding:

- **New work:** create a standalone top-level issue. Its number names the
  branch and is referenced in the PR body (`Refs #<issueNumber>`).
- **Continuing work on the current branch:** no new issue; add a comment to
  the existing issue instead.

Body templates, `gh` commands, and label/assignee rules are in
`references/issues.md`.

## Step 3 — Branch

- **New work:** fetch and check out `main`, then branch
  `<type>/<short-title>_<issueNumber>` (e.g. `feature/csv-export-reports_1`).
- **Continuing work:** stay on the existing branch (recreate it by name from
  `main` if it was deleted).

Branch-type vocabulary, slug rules, and the new-vs-continuing test are in
`references/branching.md`.

## Step 4 — Implement

Make the planned changes. Keep the issue's task checklist and "Files Touched"
list in sync as you go — they're the live record of what this work touches.
Before moving on to Step 5, tick off every task checkbox the diff completes
(`- [ ]` → `- [x]`). This is a hard gate, not a nice-to-have: the
`manage-issues` automation only closes the issue when its PR merges **and**
every checkbox is checked — a stale unchecked box leaves the issue open with a
"Cannot close" comment even though the work is done. See "Checkbox gate before
commit/PR" in `references/issues.md`.

## Step 5 — Secrets safety gate

Before staging anything, scan the working tree and diff for sensitive files or
values (`.env`, keys, tokens, connection strings, credentials). This protects
the developer from an irreversible mistake — a secret pushed to a remote is
effectively compromised.

If you find something: don't stage it, add a `.gitignore` entry if it should
never be tracked, **stop, tell the developer what you found and why, and ask how
to proceed.** Wait for their decision. Full handling — env vars, `.env.example`,
already-committed secrets, rotation — is in `references/secrets.md`.

## Step 6 — Save the plan and update `PROJECT.md`

Once the plan is finalized and implemented:

1. Write it to `plans/<sequence>-<branch-type>-<short-title>_<issueNumber>.md`
   (`<sequence>` is a global, ever-incrementing integer — count existing files
   + 1, never reset). A new plan file is created for **every prompt**, including
   continuing-work prompts, so the build-up of work over time is visible.
2. **Immediately** update `PROJECT.md` from the plan's `## Changes` list — add
   rows for new files/folders/routes, update or remove renamed/deleted paths,
   record any tech-stack decisions. Never add `.env` files or secrets to it.

`PROJECT.md` is updated in the **same commit** as the plan file and the code
change — never separately, never skipped. Templates and the initialization
block are in `references/plans-and-project-map.md`.

## Step 7 — Stage & commit (confirm once, at the end)

PRs are **squash-merged**, so the branch's individual commits collapse into one
on merge and the PR title — not any single commit — becomes the permanent
history entry (see Step 8). That means per-commit messages can be relaxed: a
clear, descriptive subject is enough, and casual/WIP commits during the branch
are fine. The strict Conventional Commit format lives on the **PR title**.

When implementation is complete, ask:

> "Implementation is complete. Should I stage and commit these changes?"

- **No** → stop, leave it uncommitted.
- **Yes** → run `git status`, present the full list of changed/untracked files,
  and have the user confirm or trim it. Stage only the agreed list, then commit
  with a clear, descriptive subject. Referencing the issue with `#<issueNumber>`
  is optional now (the durable link is `Refs #<issueNumber>` in the PR body) but
  still encouraged. Message conventions and safety rules are in
  `references/commits.md`.

## Step 8 — Pull Request (confirm once, after commit)

Before asking, re-check the issue's task checkboxes against what's actually
implemented (`references/issues.md`) — this is the last chance to tick off
completed items before the PR triggers the close-on-merge automation.

If committed, ask:

> "Changes are committed. Should I push this branch and open a PR to `main`?"

- **No** → stop.
- **Yes** → push (`git push -u origin <branch-name>`), open a PR
  `<branch-name>` → `main`. Because the PR is **squash-merged**, its **title
  must be a valid Conventional Commit** (`<type>(<scope>): <description>`) — it
  becomes the single squashed commit on `main`. The body summarizes the
  changes and references the issue with `Refs #<issueNumber>` (not auto-close).
  The agent never reviews, approves, or merges — that's a human's job. Details
  in `references/pull-requests.md`.

If an open PR already exists, don't duplicate it — note that new commits were
added.

---

## New vs. continuing work

- **Continuing:** a follow-up, fix, or extension of the branch's current scope
  ("also handle X", "fix that typo") → reuse the issue (comment) and the branch.
- **New:** an unrelated feature/fix/enhancement → restart the pipeline: new plan
  → new issue → new branch from `main`.

If it's ambiguous, state your judgment before proceeding so the user can correct
you.

## Trivial-change fast path

Some changes are too small to justify an issue and a plan file. For these, skip
Step 2 (Issue) and Step 6 (Save plan) and run **Branch → Implement → Secrets
Check → Confirm Commit → Confirm PR**. The PR is the whole record.

**Qualifies as trivial** (all of): no behavior change users could observe, no
new dependency added, touches one small area, and a reviewer would understand it
from the diff alone. Typical cases:

- Typo, comment, or wording fixes.
- Documentation / README touch-ups.
- Pure formatting, lint, or import-sort changes.
- A routine dependency **version bump** (not a swap or a major upgrade with
  breaking changes).

**Does NOT qualify — walk the full pipeline:** anything that changes behavior,
adds/removes a dependency, spans multiple areas, needs explaining beyond the
diff, or that you're unsure about. When in doubt, it's not trivial.

On the fast path:

- **Branch** off `main` as usual, but there's no issue number, so name it
  `<type>/<short-title>` (e.g. `docs/fix-readme-typo`, `chore/bump-eslint`).
- **Commit** with a normal Conventional Commit subject — **no `#<issueNumber>`**
  suffix, since there's no issue.
- **PR** into `main` as usual; the body summarizes the change. There's no
  `Refs #<issueNumber>` line and no checkbox gate to reconcile.

Everything else (secrets gate, confirm-before-commit, confirm-before-PR, squash
merge, agent-never-merges) applies unchanged.

## Tooling fallback order

For all GitHub operations, in order:

1. **`gh` CLI** (assume installed and authenticated).
2. **Connected GitHub MCP** connector if `gh` is unavailable.
3. **Manual fallback:** if neither works, tell the user what's missing and give
   them the exact commands/content (issue, branch, commit message, PR) to run by
   hand. Don't silently skip steps — surface the blocker and still do the
   non-GitHub parts (branch via plain `git`, plan file, `PROJECT.md`) where you
   can.

## Quick reference

| Situation | Issue | Branch | Plan file |
|---|---|---|---|
| New work | New standalone top-level issue | New, from `main`, named `<type>/<title>_<issue#>` | New, next sequence # |
| Follow-up on current branch | Comment on existing issue | Same branch (reuse/recreate by name) | New, next sequence #, same branch suffix |
| Trivial (typo/docs/format/bump) | **None** | New, from `main`, named `<type>/<title>` (no issue #) | **None** |
