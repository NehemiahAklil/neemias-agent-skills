# Pull Requests

Open a PR only at Step 8, after the commit exists and the developer has
confirmed "yes, push and open a PR."

**PRs are squash-merged into `main`.** The whole branch collapses to one
commit whose message is the **PR title** — so the PR title must be a valid
[Conventional Commit](https://www.conventionalcommits.org/en/v1.0.0/)
(`<type>(<scope>): <description>`), since it's what lands in `git log` and what
the release drafter parses. Individual branch commits stay casual (see
`commits.md`).

Before pushing, re-check the linked issue's task checkboxes against what's
implemented — see "Checkbox gate before commit/PR" in `issues.md`. The
`manage-issues` automation only closes the issue on merge if every checkbox is
ticked. (Trivial fast-path PRs have no issue, so there's no checkbox gate — see
the fast-path section in `SKILL.md`.)

## Steps

1. Push the branch:
   ```bash
   git push -u origin <branch-name>
   ```
2. Open a PR from `<branch-name>` → **`main`**, labeled the same as its
   implementation issue (`Feature`, `Bug`, `Enhancement`, … — mapping in
   `branching.md`). The `--title` **must be a valid Conventional Commit**, since
   squash merge makes it the commit message:
   ```bash
   gh pr create --base main --head <branch-name> \
     --title "<type>(<scope>): <concise summary>" \
     --label "<same label as the issue>" \
     --body-file /tmp/pr-body.md
   ```
   The PR label matters: the release drafter builds release notes and resolves
   the version bump from it. Add `Major` too if the change is breaking. When
   merging (a human does this), use **Squash and merge** so the title becomes
   the single commit on `main`.
3. The PR body summarizes the changes and references the issue with
   `Refs #<issueNumber>` — **not** an auto-close keyword (a branch may receive
   more commits later; the `manage-issues` workflow closes the issue on merge
   once all its task checkboxes are ticked).
4. **The agent never reviews, approves, or merges.** That's a human reviewer's
   job. Likewise, never add the `Publish` label (it makes the merge publish the
   draft release) or `Skip-Changelog` — those are the developer's calls.

## PR body template

```markdown
## Summary
<what this PR does, 1-3 sentences>

## Changes
- <file/area> — <what changed and why>

## Testing
<how it was verified, if applicable>

Refs #<issueNumber>
```

## No agent attribution

The agent is a ghost. **Never** add Claude/Anthropic attribution to the PR —
not in the title, the body, or any PR comment. No "Generated with Claude Code"
line, no 🤖 badge, no `Co-Authored-By`/`Co-authored-by` footer. The PR reads as
if the developer opened it themselves.

## If a PR already exists

If an open PR for this branch already exists, **don't duplicate it.** Note that
new commits were pushed to the existing PR and (if needed) update its body to
reflect the added work.

## Tooling fallback

If `gh` is unavailable, fall back to the GitHub MCP connector, then to giving
the developer the exact branch name, base, title, and body to open the PR
manually. Don't silently skip the PR step — surface the blocker.
