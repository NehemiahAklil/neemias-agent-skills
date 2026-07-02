# Neemias Agent Skills

My personal collection of Agent Skills, structured the same way as
[Anthropic's official skills repo](https://github.com/anthropics/skills): a
top-level `skills/` folder where every skill is a self-contained
`skills/<skill-name>/SKILL.md` (+ optional `references/`, `scripts/`,
`assets/`).

The first skill here is **devflow**: a lean, portable "no code change without
a plan, an issue, a branch, and a PR" workflow for AI coding assistants. It
exists so every change an assistant makes is traceable: plan → GitHub issue →
branch → implementation → secrets check → saved plan → commit → PR. Unlike
heavier variants of this workflow, there's no project board, no user stories,
and no sub-issues — one issue, one branch cut from `main`, one squash-merged
PR back into `main` — plus a fast path that skips the issue entirely for
trivial changes (typos, docs, formatting, routine version bumps).

> **Plan → Issue → Branch → Implement → Secrets Check → Save Plan → Confirm Commit → Confirm PR**

It applies to any prompt that changes a codebase (code, tests, config, docs,
or dependencies) and is skipped only for purely conversational or read-only
prompts.

## What's in this repo

| Path | Purpose |
|---|---|
| `skills/devflow/` | The skill itself — `SKILL.md` plus `references/*.md` (issues, branching, commits, PRs, secrets, plans) and `assets/github/` (release drafter, PR template, workflows). This is the **single source of truth** for the workflow; everything else in this repo is a thin adapter pointing back at it. |
| `templates/` | Ready-to-copy files for a project that installs `devflow` locally: a `.claude/settings.json` that pre-approves the commands the skill runs, plus entry-point files for AI tools that don't have Claude Code's Skill system (each one is a short pointer that surfaces the golden rule and sends the assistant to `AGENTS.md` / the skill's reference files for the full detail). |

## Installing a skill

Skills in this repo can be installed **globally** (available in every
project on your machine) or **locally** (checked into one specific project
and shared with anyone who clones it). Only `devflow` needs the extra step of
copying `templates/` — a skill with no cross-tool ambitions is just the one
`cp`.

### Global install (all your projects, Claude Code only)

```bash
cp -r skills/<skill-name>/ ~/.claude/skills/
```

Claude Code picks it up in every project automatically. Skip `templates/`
for a global install — it hardcodes paths relative to a project root
(`.claude/skills/devflow/references/...`), so it only makes sense once the
skill is copied into a specific repo.

### Local install (one project, every harness)

```bash
cp -r skills/<skill-name>/ <target-repo>/.claude/skills/
```

For `devflow` specifically, also copy the cross-tool adapter files:

```bash
cp -r templates/. <target-repo>/
```

(`templates/.` includes the dotfiles/dotdirs — `.claude/`, `.github/`,
`.agents/`.)

Then, one-time per repo:

1. Follow the setup in
   `<target-repo>/.claude/skills/devflow/references/repo-setup.md`: install
   the bundled `.github/` automation and create the standard labels.
2. Review `<target-repo>/.claude/settings.json` — it pre-approves the exact
   `gh`/`git` commands the skill runs (issue/PR/label management, branch
   checkout, add, commit, push) so Claude Code doesn't prompt for each one
   individually. It does **not** loosen anything destructive (force-push,
   hard reset, etc.) — those still prompt. Adjust it if a repo wants tighter
   or looser defaults.
3. Commit the copied files — they're project configuration, not secrets.

That's it: every harness listed below will pick up the workflow the next
time it's used in that repo.

## Supported harnesses

This is a convention, not a technical gate — it works by putting the workflow
in front of whichever instruction-file mechanism each tool actually reads,
and relying on the assistant to follow it. If you need a hard backstop (e.g.
blocking a merge outright), pair this with server-side branch protection —
that's outside the scope of these files.

| Harness | Entry point | How it's read |
|---|---|---|
| Claude Code | `CLAUDE.md` (imports `AGENTS.md`) + `.claude/skills/devflow/` | The Skill auto-triggers on codebase-changing prompts by description; `CLAUDE.md` is read every session as a backstop. `.claude/settings.json` pre-approves its commands. |
| OpenAI Codex (CLI & cloud) | `AGENTS.md` | Native support; Codex walks from the repo root down to the CWD and merges any `AGENTS.md` files it finds. |
| Cursor | `AGENTS.md` | Cursor reads root `AGENTS.md` natively as an alternative to `.cursor/rules/`. |
| Antigravity (2026-04+) | `AGENTS.md` | Native root-level support, added in Antigravity's April 2026 update. |
| Antigravity (older builds) | `.agents/agents.md` | Legacy convention — the template file just redirects to `AGENTS.md`; delete it once you've confirmed your build reads the root file. |
| GitHub Copilot — coding agent | `AGENTS.md` | Supported since August 2025. |
| GitHub Copilot — Chat/inline (VS Code, Visual Studio, JetBrains, Neovim, github.com, CLI) | `.github/copilot-instructions.md` | Repo-wide custom instructions, always injected; points at `AGENTS.md` for the full workflow. |
| Windsurf / Devin Desktop | `AGENTS.md` | Native support. |

## Keeping things in sync

`skills/devflow/` is authoritative. `templates/AGENTS.md` is a condensed
mirror of the same pipeline for tools without a Skill system, and
`templates/CLAUDE.md` / `templates/.github/copilot-instructions.md` /
`templates/.agents/agents.md` are thin pointers on top of that — they
shouldn't need edits often. `templates/.claude/settings.json` only needs
updating if the skill starts running a new class of command.

When you change a rule (e.g. how the fast path works), update the relevant
file(s) under `skills/devflow/references/` first, then check whether
`templates/AGENTS.md`'s summary of that step drifted out of sync and update
it too.

## Customizing per repo

Labels and the branch-type mapping vary per repo — see
`skills/devflow/references/repo-setup.md` for the one-time setup and the
label table to adjust if a repo uses different conventions.

## Adding another skill

Create `skills/<new-skill-name>/SKILL.md` (frontmatter: `name`,
`description`) plus any `references/`, `scripts/`, or `assets/` it needs. It
doesn't need an entry in `templates/` unless it should also work outside
Claude Code.
