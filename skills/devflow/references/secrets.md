# Secrets, `.gitignore`, and Environment Variables

The cost of a leaked secret is asymmetric: a key pushed to a remote must be
treated as compromised and rotated, even if you delete it seconds later. That's
why this gate is a hard stop — it's cheaper to pause and ask than to clean up
after a leak.

## While implementing

- Add any likely-sensitive file to `.gitignore` (create it if missing)
  **before or while** introducing it: `.env`, `.env.*`, `*.pem`, `*.key`,
  `*.p12`, `secrets.*`, local config, build output, etc.
- Never hardcode secrets, keys, tokens, passwords, or connection strings. Read
  them from environment variables. If a local env file is needed, maintain an
  `.env.example` with **placeholder values only**, and gitignore the real
  `.env`.

## Pre-commit safety gate (before staging in Step 7)

Before staging, scan the working tree and diff for sensitive files or values:

```bash
git status --porcelain
git diff
git diff --staged
```

Look for both **files** (the patterns above) and **values** embedded in code
(long random tokens, `password=`, `api_key=`, AWS-style keys, full database
URLs, private-key headers).

If you find something:

1. **Don't stage or commit it** (unstage with `git restore --staged <file>` if
   it's already staged).
2. Add a `.gitignore` entry if it should never be tracked. If it was **already
   committed/tracked** in a previous commit, flag that separately — gitignoring
   won't untrack it, and the secret is already in history.
3. **Stop. Tell the developer what you found and why**, then ask how to proceed
   — for example: move it to an env var / secret manager, replace it with a
   placeholder + gitignore, or rotate the credential if it was already
   committed.
4. **Wait for their decision** before continuing to the commit step.

## `.env.example`

`.env.example` is the one place environment variables are documented — with
placeholder values, never real ones. It is committed; the real `.env` is not.
Never copy env var names or values into `PROJECT.md` either (see
`plans-and-project-map.md`).
