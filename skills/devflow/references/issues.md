# GitHub Issues

Every branch, commit, and PR is tied to an **implementation issue.** There are
two paths. **State which one you're taking before proceeding.**

---

## New work — create a standalone issue

Any feature, fix, or change that isn't a follow-up on the current branch gets
its own top-level issue. Its number drives the branch name and every commit
message.

- **Title:** short, descriptive, Title Case.
- **Label:** label matching the branch `<type>` (full mapping in
  `branching.md`).
- **Assignee:** `--assignee "@me"`.
- **Body:**

```markdown
## Context
<1-3 sentences>

## Execution Plan
<finalized plan>

## Tasks
- [ ] <subtask 1>
- [ ] <subtask 2>

## Files Touched
- `path` — what changed and why
```

```bash
gh issue create \
  --title "Fix CORS Headers On API" \
  --label "Bug" \
  --assignee "@me" \
  --body-file /tmp/issue-body.md
```

The number returned is your **issue number** — use it for the branch
(`fix/cors-headers-api_<N>`) and, most importantly, in the PR body as
`Refs #<N>` (the durable issue link, since PRs squash-merge). Adding `#<N>` to
commit subjects is optional — see `commits.md`.

---

## Continuing work on the current branch

A follow-up, fix, or extension of an already-open issue on the current branch
→ **no new issue.** Add a comment to the existing issue instead:

```markdown
## Update — <summary>

### Execution Plan
<plan for this follow-up>

### New / Updated Tasks
- [ ] <subtask>

### Files Touched
- `path` — what changed and why
```

```bash
gh issue comment <issueNumber> --body-file /tmp/issue-comment.md
```

---

## No agent attribution

The agent is a ghost. **Never** add Claude/Anthropic attribution to an issue —
not in the title, the body, or any issue comment. No "Generated with Claude
Code" line, no 🤖 badge, no `Co-Authored-By`/`Co-authored-by` footer. Every
issue and comment reads as if the developer wrote it.

## Keeping issues current

As implementation proceeds, keep the issue's task checklist and "Files Touched"
list in sync — they're the live record of what this work touches. Update them
via comments or editing the issue body as tasks complete.

### Checkbox gate before commit/PR

The `manage-issues` workflow (`assets/github/workflows/manage-issues.yml`)
auto-closes an issue when its linked PR merges **only if every `- [ ]` in the
issue body is checked.** If any remain unchecked, it leaves the issue open and
posts "Cannot close issue: N unchecked checkboxes remain after merging PR
#M" — a silent gap between "PR merged" and "issue closed" that's easy to miss
since it happens after the developer has moved on.

To prevent that, **before staging for commit (Step 7) and again before opening
a PR (Step 8)**, re-read the issue body and tick off every task the diff
actually completes:

```bash
gh issue view <issueNumber> --json body --jq '.body' > /tmp/issue-body.md
# edit /tmp/issue-body.md: flip completed items from "- [ ]" to "- [x]"
gh issue edit <issueNumber> --body-file /tmp/issue-body.md
```

Only check a box if the corresponding work is genuinely done in the diff being
committed — never check ahead of the code just to satisfy the automation. If a
task genuinely isn't done yet (partial PR, follow-up planned), leave it
unchecked and say so.