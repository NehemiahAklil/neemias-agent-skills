# Plans (`plans/`) and the Project Map (`PROJECT.md`)

## Save the plan

Once the plan is finalized and implemented, write it to `plans/` (create the
directory if missing).

**Filename:** `plans/<sequence>-<branch-type>-<short-title>_<issueNumber>.md`

- `<sequence>`: a global, ever-incrementing integer across the whole project —
  count the existing files in `plans/` and add 1. It never resets.
- The rest is the branch name with `/` replaced by `-`.

Example: branch `feature/csv-export-reports_1`, and there are already 3 plan
files → `plans/4-feature-csv-export-reports_1.md`.

A new plan file (next sequence number) is created for **every prompt**,
including continuing-work prompts. Multiple files can share a branch/issue
suffix — that's intentional; it shows the build-up of work over time.

**Content** — an enhanced summary of the prompt and the agreed approach, not a
transcript:

```markdown
# Plan — <title>

**Branch:** <branch-name>
**Issue:** #<issueNumber>
**Date:** <date>

## Goal
<summary>

## Approach
<approach, alternatives considered>

## Changes
- <file/area> — <what changed>
```

## Update `PROJECT.md` — immediately after saving the plan

This is mandatory and runs **right after** saving the plan — no exceptions. The
plan's `## Changes` list is the source of truth for what to update:

1. For every **new** file, folder, or route in that list, add a row to the
   `## Project Structure` table in `PROJECT.md`.
2. For any **removed or renamed** path, update or remove the relevant row.
3. For any **tech-stack decision**, fill in the `## Tech Stack` section.
4. **Never add** `.env` files, environment variables, or secrets — those belong
   in `.env.example` only.

`PROJECT.md` is committed in the **same commit** as the plan file and the code
change — never separately, never skipped.

## `PROJECT.md` initialization

When a project is first initialized, create a blank `PROJECT.md` in the project
root before any other work begins:

```markdown
# Project Map

> **Navigation rule:** When a prompt targets a specific area, go directly to the
> mapped path. Do not scan unrelated directories or read files outside the
> relevant path unless the task explicitly requires cross-cutting changes.

## Tech Stack
<!-- To be filled as the project is set up -->

## Project Structure
<!-- Updated automatically after every plan is saved -->

| Path | Purpose |
|---|---|

## Notes
<!-- Project-specific decisions and gotchas -->
```

> **Never add `.env`, `.env.*`, or any environment variable details to
> `PROJECT.md`.** Environment variables are documented in `.env.example` only.
