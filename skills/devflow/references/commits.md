# Commits — Conventional Commits

**PRs are squash-merged**, so the branch's commits collapse into one on merge
and the **PR title** becomes the permanent history entry — that's the string
that must follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
specification (see `pull-requests.md`). The payoff is that the merged message
becomes machine-parsable — it drives changelog generation, semantic version
bumps, and CI automation — and a human scanning `git log` on `main` sees at
a glance what each merged change does.

Because of that, **per-commit messages on the branch are relaxed**: a clear,
descriptive subject is enough, and casual/WIP/fixup commits during development
are fine — they disappear on squash. You don't need strict `<type>(<scope>)`
form or an issue suffix on every commit. The format spec below is what the **PR
title** must satisfy; apply it to individual commits only when you want a tidy
pre-squash history.

Two workflow-specific rules still hold:

- **Issues are referenced with `#<issueNumber>`, never `Closes`/`Fixes`.** The
  durable link is `Refs #<issueNumber>` in the PR body; a `#<issueNumber>` in a
  commit subject is a must but never an auto-close keyword.
- Never invent a scope or type that doesn't match the diff.

## When to commit

Only at Step 7, after implementation is complete and the developer has confirmed
"yes, stage and commit." First run `git status`, present the full list of
changed/untracked files, and stage only the list the developer agrees to.

## Format

This is the format the **PR title** must follow (it becomes the squashed
commit). The same shape works for a tidy per-commit message; the trailing
`#<issueNumber>` is a commit-only extra — **don't put it in a PR
title** (GitHub appends the PR number there, and the issue link lives in the PR
body as `Refs #<issueNumber>`).

```
<type>(<optional scope>): <description> #<issueNumber>

<optional body>

```

- **type** — required; see the table below. It mirrors the branch `<type>`
  (`feature` → `feat`, `fix` → `fix`, etc.), refined per the actual change.
- **scope** — optional; the area/module affected, in parentheses, e.g.
  `feat(reports):`. Use it in larger projects where it adds clarity. **Never put
  an issue number in the scope.**
- **description** — one line, imperative mood, present tense, lowercase, no
  trailing period, ideally < 72 characters ("add export" not "added export").
- **body** — optional, 1–3 sentences of **prose**, not a bullet list — bullets
  render poorly in `git log`/`git show`. One blank line after the description.
- **issueNumber** — commit-only: `#<issueNumber>` at the end of the
  first line of a commit. Never an auto-close keyword, never in the PR title.

## Write for a reader with no context

The description and body must stand on their own for someone who never saw
the conversation that produced the change — a future contributor, a reviewer,
or the developer six months from now. Don't reference internal artifacts the
reader can't see: no "Stage 1", no plan-file names or sequence numbers, no
"as discussed" or "per the plan". Describe _what changed and why_ in terms of
the actual code/behavior, as if introducing it cold.

### Types

| Type       | Purpose                                                 |
| ---------- | ------------------------------------------------------- |
| `feat`     | New feature                                             |
| `fix`      | Bug fix                                                 |
| `docs`     | Documentation only                                      |
| `style`    | Formatting/style, no logic change                       |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf`     | Performance improvement                                 |
| `test`     | Add or update tests                                     |
| `build`    | Build system or dependencies                            |
| `ci`       | CI/config changes                                       |
| `chore`    | Maintenance/misc                                        |
| `revert`   | Revert a previous commit                                |

### Breaking changes

```
# Exclamation mark after type/scope
feat(api)!: remove deprecated v1 endpoint #12
```

or as a footer:

```
feat(config): allow config to extend other configs #12

BREAKING CHANGE: the `extends` key behavior changed.
```

## How to choose type, scope, and description

Analyze the actual diff — don't guess from the prompt:

```bash
git diff --staged          # what you're about to commit
git status --porcelain     # full picture
```

From the diff, decide:

- **Type** — what kind of change dominates this diff?
- **Scope** — which module/area do the changed paths belong to?
- **Description** — the one-line summary of what changed.

One logical change per commit. If the staged set spans two unrelated changes,
that's a signal to split it.

## Examples

**Example 1**
Diff: a new CSV export button + service on the reports page.

```
feat(reports): add CSV export for monthly data #1

Generates a downloadable CSV from the report table view.
```

**Example 2**
Diff: fix an off-by-one in pagination.

```
fix(pagination): correct last-page item count #7
```

**Example 3**
Diff: README + setup docs only.

```
docs: document local env setup #3
```

## Executing the commit

```bash
# Single line (no body)
git commit -m "fix(pagination): correct last-page item count #7"

# Multi-line with body — heredoc avoids quoting headaches
git commit -m "$(cat <<'EOF'
feat(reports): add CSV export for monthly data #1

Generates a downloadable CSV from the report table view.
EOF
)"
```

## No agent attribution

The agent is a ghost. **Never** add any Claude/Anthropic attribution to a commit:
no `Co-Authored-By: Claude …` trailer, no "Generated with Claude Code" line, no
🤖 badge, no `Co-authored-by` footer of any kind. The subject and body contain
only the description of the change — nothing that reveals an agent wrote it. The
commit must read as if the developer authored it by hand.

## Git safety protocol

- **Never commit secrets** — the pre-commit gate in `secrets.md` runs first.
- Never update git config.
- Never run destructive commands (`--force`, hard reset) without an explicit
  request.
- Never skip hooks (`--no-verify`) unless the user asks.
- Never force-push to `main`/`master`.
- If a commit fails due to a hook, fix the issue and create a **new** commit —
  don't amend.
