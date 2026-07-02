# One-Time Repo Setup

Run through this checklist the **first time this workflow is used in a repo**
(or when you notice a piece is missing — e.g. no `.github/workflows/`, no
labels, release drafter not configured). Skip anything that already exists;
never overwrite a file the repo already has without asking the developer.

## 1. Install the `.github/` automation

The skill ships ready-to-copy GitHub automation in `assets/github/`. Copy it
into the target repo's `.github/` directory:

| Asset | Installs to | Purpose |
|---|---|---|
| `pull_request_template.md` | `.github/pull_request_template.md` | Standard PR body (issue link, changes, remaining tasks) |
| `release-drafter.yml` | `.github/release-drafter.yml` | Release-notes categories + semver resolution from PR labels |
| `workflows/release.yml` | `.github/workflows/release.yml` | Drafts a release on PRs to `main`; publishes the draft when a merged PR carries the `Publish` label |
| `workflows/manage-issues.yml` | `.github/workflows/manage-issues.yml` | Reopens/closes issues referenced in PR commits (respects unchecked task boxes) |
| `workflows/deploy.yml` | `.github/workflows/deploy.yml` | **Optional / project-specific** — SSH deploy triggered on the branch in the `DEPLOY_BRANCH` repo variable; needs `SSH_PRIVATE_KEY` secret and `DEPLOY_SERVER_*` variables. Only install if the project deploys this way, and confirm with the developer first |

```bash
mkdir -p .github/workflows
cp <skill-dir>/assets/github/pull_request_template.md .github/
cp <skill-dir>/assets/github/release-drafter.yml .github/
cp <skill-dir>/assets/github/workflows/release.yml .github/workflows/
cp <skill-dir>/assets/github/workflows/manage-issues.yml .github/workflows/
# deploy.yml only on explicit confirmation — see table above
```

Committing these files is itself a code change — it goes through the normal
pipeline (issue, branch, plan file, confirmed commit, PR).

## 2. Create the labels

These names must match `.github/release-drafter.yml` exactly — the drafter
categorizes release notes and resolves the version bump from **PR labels**.

```bash
gh label create "Feature"                  --color 1d76db --description "New feature" --force
gh label create "Bug"                      --color d73a4a --description "Bug fix" --force
gh label create "Hot Fix"                  --color b60205 --description "Urgent production fix" --force
gh label create "Enhancement"              --color a2eeef --description "Improvement to existing behavior" --force
gh label create "Dev Task"                 --color 0e8a16 --description "Internal dev work (refactor, tooling)" --force
gh label create "Task"                     --color d4c5f9 --description "Chore, docs, tests, misc" --force
gh label create "Dev Process Optimization" --color c2e0c6 --description "Workflow/process improvement" --force
gh label create "Security"                 --color ee0701 --description "Security fix" --force
gh label create "Major"                    --color 5319e7 --description "Breaking change (major version bump)" --force
gh label create "Publish"                  --color 0052cc --description "Merged PR publishes the draft release" --force
gh label create "Skip-Changelog"           --color cccccc --description "Exclude PR from release notes" --force
```

How they're used:

- **Issue/PR work labels** (`Feature`, `Bug`, `Hot Fix`, `Enhancement`,
  `Dev Task`, `Task`, `Security`) — applied per the branch-type mapping in
  `branching.md`, to both the issue and its PR.
- **`Major`** — added *in addition* on breaking changes; bumps the major
  version.
- **`Publish`** / **`Skip-Changelog`** — PR-only control labels; a human
  decides these (see `pull-requests.md`).

If the repo/org uses GitHub **issue types**, map them the same way: type
`Feature` for features, `Bug` for fixes, `Task` for everything else.
