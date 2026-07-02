# Branching

## New work

1. Fetch and check out **`main`** — always branch from `main`, unless the
   current branch is the relevant continuing branch (see below).
2. Create the branch: `<type>/<short-title>_<issueNumber>`
   - `<type>`: `feature`, `fix`, `hotfix`, `enhancement`, `chore`, `refactor`,
     `docs`, `test`, etc.
   - `<short-title>`: lowercase, hyphenated slug describing the work.
   - `<issueNumber>`: the issue number created in Step 2.

   Example: issue #3 "Add GET /tasks Endpoint" →
   `feature/add-get-tasks-endpoint_3`

   ```bash
   git fetch origin
   git checkout main
   git pull
   git checkout -b feature/csv-export-reports_1
   ```

**Trivial fast-path work** (typo/docs/formatting/version-bump — see the
fast-path section in `SKILL.md`) has no issue, so drop the `_<issueNumber>`
suffix: name it `<type>/<short-title>` (e.g. `docs/fix-readme-typo`).

## Continuing work

Stay on the existing branch. Don't create a new one even if its PR was merged.
If the branch was deleted, recreate it from `main` with the **same name**.

## New vs. continuing — the test

- **Continuing:** a follow-up, fix, or extension of the branch's current scope
  ("also handle X", "fix that typo") → reuse the issue (comment) and the branch.
- **New:** an unrelated feature/fix/enhancement → restart the whole pipeline:
  new plan → new issue → new branch from `main`.

If it's ambiguous, state your judgment before proceeding so the user can correct
you.

## Branch type → everything else

The `<type>` you pick here drives downstream choices, so keep them consistent:

| Branch `<type>` | Issue/PR label | Commit `<type>` |
|---|---|---|
| `feature` | `Feature` | `feat` |
| `fix` | `Bug` | `fix` |
| `hotfix` | `Hot Fix` | `fix` |
| `enhancement` | `Enhancement` | `feat` or `refactor` (per change) |
| `refactor` | `Dev Task` | `refactor` |
| `docs` | `Task` | `docs` |
| `chore`, `test`, other | `Task` (default) | `chore`, `test`, etc. |

Breaking changes additionally get the `Major` label; security fixes get
`Security`. Label names must match `.github/release-drafter.yml` exactly —
they drive release notes and the version bump. If a label doesn't exist yet,
create it — see `repo-setup.md`.

See `commits.md` for the full Conventional Commit type list.
