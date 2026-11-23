---
description: Open a Buddy pull request or address its review feedback
argument-hint: [pr-number] [--auto]
allowed-tools: Bash(bdy:*), Bash(git:*), Read, Edit, Write, Glob, Grep, AskUserQuestion
---

Act as the pull request author using the `pr-author` skill.

**Arguments:** `$ARGUMENTS` – optional pull request number, optionally `--auto` (no questions;
used by Buddy automation).

- Number given → address the review feedback on that pull request.
- No number → check `bdy pr ls` for an open pull request from the current branch: if there is
  one with OPEN threads, address its feedback; otherwise create a new pull request.
