---
description: Run or set up Buddy CI/CD pipelines
argument-hint: [pipeline]
allowed-tools: Bash(bdy:*), Read, Glob, Write, AskUserQuestion
model: sonnet
---

Run a Buddy CI/CD pipeline and watch the result, or set up a new pipeline for the project.

**Arguments:** `$1` — optional pipeline name to run (skips selection)

## Workflow

1. **Check context:** `bdy whoami` — verify login and project link. If the directory is not linked to a project, follow the buddy skill auth flow (`bdy proj link`)

2. **List pipelines:** `bdy pipeline list`
   - If `$1` matches a pipeline → **Run** it (step 4)
   - If pipelines exist and no `$1` → ask via AskUserQuestion: run an existing pipeline (list them) or set up a new one
   - If no pipelines exist → offer to **Set up** a pipeline (step 3)

### Set up a pipeline

3. **Design the pipeline YAML** using the built-in YAML helper — do NOT guess the schema:
   - Pipeline schema + full example: `bdy yaml pipeline`
   - Find action types: `bdy yaml actions list --search <keyword>` (186 types available)
   - Action schema + example: `bdy yaml actions info <TYPE>` (e.g. `bdy yaml actions info BUILD`)

   Detect the stack (`package.json`, `requirements.txt`, `go.mod`, `Dockerfile`, ...) and pick actions:
   - **BUILD** — shell commands in a Docker container (install, lint, test, build). Requires `docker_image_name` (e.g. `library/node`). Use `cached_dirs` for dependency caching
   - **PUBLISH_ARTIFACT_VERSION** — publish static site / build output to the artifact registry
   - **DEPLOY_TO_SANDBOX** / **SANDBOX_EXEC** — deploy files and run commands on a sandbox
   - Other integrations (S3, Docker, ESLint, Cypress, Slack...) — search with `bdy yaml actions list --search`

   Ask via AskUserQuestion what the pipeline should do ("Build + test" / "Build + test + deploy" / "Custom") and how it triggers — map the answer to `events`:
   - On push → `events: [{type: PUSH, refs: [refs/heads/main]}]`
   - Scheduled → `events: [{type: SCHEDULE, cron: "...", timezone: "..."}]`
   - Manual → no `events`

   Then:
   - Write `buddy.yml` (top-level is a list: `- pipeline: <id>` with `actions:`)
   - Do NOT hardcode secrets — use pipeline `variables` and reference them in commands
   - Validate: `bdy yaml validate buddy.yml`
   - Register: `bdy pipeline create --yaml @buddy.yml`
   - To modify an existing pipeline: fetch current config with `bdy pipeline yaml <identifier>`, edit, then `bdy pipeline update <identifier> @buddy.yml`

### Run a pipeline

4. **Start and follow:** `bdy pipeline run start <identifier> --wait` — streams execution until it finishes. Use `run_in_background: true` for long runs. Pass variables with `-v key:val` if the pipeline needs them

5. **On failure:** fetch logs of the failed action via `bdy pipeline run logs <identifier> <run-id> <action-run-id>`, summarize the root cause, and suggest a fix. After the fix: `bdy pipeline run retry <identifier> <run-id>` (or `run start` for a fresh run)

6. **Show results:** run status, duration, and hint to `/logs <pipeline>` for details

> **CLI tool:** `bdy` (not "buddy")
