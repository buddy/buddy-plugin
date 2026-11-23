---
description: Review a Buddy pull request
argument-hint: <pr-number> [--auto]
allowed-tools: Bash(bdy:*), Agent, AskUserQuestion
---

Review Buddy pull request using the `pr-review` skill.

**Arguments:** `$ARGUMENTS` – the pull request number, optionally `--auto` (post threads and the
verdict without asking; used by the Buddy review action).

If no number is given, run `bdy pr ls` and ask the user which pull request to review (pick the
only open one if there is exactly one).
