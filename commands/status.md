---
description: Show running Buddy deployments, artifacts, tunnels, and pipelines
argument-hint: [name]
allowed-tools: Bash(bdy:*), AskUserQuestion
model: sonnet
---

Show an overview of everything running on Buddy: sandboxes, BUCKET artifacts (static sites), and tunnels.

**Arguments:** `$1` — optional resource name to show details for a single sandbox/artifact only

## Workflow

1. **Check auth:** `bdy whoami` — if not logged in, follow the auth flow from the buddy skill

2. **Sandboxes:** `bdy sandbox list`, then for each running sandbox:
   - Apps: `bdy sandbox app list <name>` (include status)
   - Endpoints: `bdy sandbox endpoint list <name>` (public URLs)

3. **Artifacts (BUCKET):** `bdy artifact list` — BUCKET artifacts host static sites, so ALWAYS include them:
   - List versions: `bdy artifact version list <name>`
   - Get public URL of the latest version: `bdy artifact version get <name> <version>`

4. **Tunnels:** `bdy tunnel list` — show active tunnels with local port and public URL

5. **Pipelines:** `bdy pipeline list` — show each pipeline with the status of its latest run (e.g. success, failed, in progress). For failed or running pipelines include the run id so the user can dig in with `bdy pipeline run status <identifier> <id>` and `bdy pipeline run logs <identifier> <id> <action-id>`

6. **Show results** as a compact summary grouped by type. For each resource: name, status, public URL. End with hints: `/logs <name>` for debugging, `/domain <name>` to attach a custom domain

> If a subcommand differs, check `bdy <command> --help` instead of guessing flags.

> **CLI tool:** `bdy` (not "buddy")
