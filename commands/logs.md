---
description: Show logs of a Buddy app or pipeline run
argument-hint: [name] [app-id]
allowed-tools: Bash(bdy:*), AskUserQuestion
model: sonnet
---

Show status and logs of an application running in a Buddy sandbox, or of a pipeline run.

**Arguments:**
- `$1`: Optional sandbox or pipeline name
- `$2`: Optional app id (sandbox apps only)

## Workflow

1. **Resolve source:** match `$1` against sandbox names (`bdy sandbox list`) first, then pipeline names (`bdy pipeline list`)
   - If `$1` not provided: if exactly one sandbox is running and no pipelines exist, use it; otherwise pick the source via AskUserQuestion (list sandboxes and pipelines)
   - If `$1` matches both a sandbox and a pipeline: ask which one via AskUserQuestion

### Sandbox app logs

2. **Resolve app:** if `$2` not provided, run `bdy sandbox app list <sandbox>` — if exactly one app, use it; otherwise pick via AskUserQuestion

3. **Show status first:** `bdy sandbox app status <sandbox> <app-id>` — report whether the app is running or crashed

4. **Show logs:** `bdy sandbox app logs <sandbox> <app-id>`

5. **Diagnose:** if the logs contain errors, summarize the root cause and suggest a fix. Common issues:
   - App binds to `127.0.0.1` instead of `0.0.0.0` → endpoint unreachable
   - Missing dependencies → re-run install via `bdy sandbox exec command <sandbox> "cd /app && npm install" --wait`
   - Crash loop → check start command with `bdy sandbox app status`

### Pipeline run logs

2. **Latest run:** `bdy pipeline run list <identifier>` to find the run id, then `bdy pipeline run status <identifier> <run-id>`

3. **Show logs** of failed (or currently running) actions: `bdy pipeline run logs <identifier> <run-id> <action-run-id>`

4. **Diagnose:** summarize the failing action and root cause (build error, failing test, missing variable). Suggest a fix; after applying it, offer `bdy pipeline run retry <identifier> <run-id>` (or `/ci` for a fresh run)

> **CLI tool:** `bdy` (not "buddy")
