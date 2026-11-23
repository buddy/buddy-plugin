---
name: pr-review
description: >-
  Review a Buddy pull request with `bdy pr`: read the changed files, open threads on the exact
  lines, verify the author's fixes on re-review and give a verdict. Use when the user asks to
  review, check, go through or give feedback on a pull request ("review PR #123", "sprawdź PR
  #123", "przejrzyj pull 132"), or to re-review it after the author's changes. Not for only
  showing a PR or what changed in it (use the buddy skill), creating a PR or fixing review
  comments (use pr-author).
---

# Reviewing a Buddy pull request

You are the **reviewer**. You work only through `bdy pr`: no checkout, no code edits, no git.
The CLI prepares the review for you (file order, MUST_REVIEW triage, batches, existing threads),
so follow its "next steps" instead of inventing your own plan. Run `bdy pr <command> --help`
for flags.

## Before you start

1. **Login.** If `bdy` answers `Token not provided` / `Not logged in`, follow the auth section of
   the `buddy` skill.
2. **Project.** Pull requests live in a Buddy project. Both commands below are local (no
   network):
   - the user named the workspace / project → pass `-w <ws> -p <project>` to every command;
   - `bdy context show git` resolves (this directory is a clone of a Buddy repository) → pass
     `--context git` to every command – the active context may be a global default pointing
     somewhere else;
   - otherwise `bdy context show` shows the active context and where its values come from:
     `link` or `environment` → use it;
   - only `config` (a global default), or nothing → in automatic mode stop with a clear message;
     otherwise show "PR #N *title* in ws/project" after step 3 of the workflow and ask whether this
     is the right one, or ask for the workspace and project (`bdy workspace ls`, `bdy project ls`)
     and pass them as `-w/-p`. Do not change the user's configuration (`bdy proj link`,
     `bdy context use`) unless they ask for it.
3. **Mode.**
   - **Automatic** if the request says `--auto`, "automatically", "CI", or you have no way to
     ask the user (non-interactive run). Post threads and the verdict yourself, never ask.
   - **Otherwise ask the user once** (AskUserQuestion; together with the project question, if
     there is one): preview the findings first, or review and post automatically. In preview
     mode collect findings without posting, show them as a numbered list (`path:line` – problem
     – failure scenario), post the ones the user accepts, then ask about the verdict.

## Workflow

1. **Start:** `bdy pr review start <N>` – idempotent; prints the summary: version, reviews,
   threads, files, MUST_REVIEW triage, batches and the next steps. Note the version (`vK`) and
   pass `--rev vK` to every later command, so a push during the review does not mix versions.
   If it says the author cannot review the pull request, stop and tell the user.
2. **Read every MUST_REVIEW file**, several files per call:
   `bdy pr diff <N> <path> <path> … --rev vK`. Group the files so one call stays at about
   5k tokens (`bdy pr file ls` shows the size of each file) – a bigger output does not fit in
   your context in one piece (see "Large outputs"). Reading a diff marks its files as viewed.
   Open a single file again (`--full`) only when the collapsed hunks hide context you need.
   - If the summary lists **batches**, give each batch to a `pr-batch-reviewer` subagent in
     parallel (PR number, version, batch number, the PR description) and review the files
     outside the batches yourself. Do not read a large PR alone – it ends with unread files.
     No `pr-batch-reviewer`? Use any subagent you have, with "Reviewing one batch" below as its
     instructions. No subagents at all? Go through the batches yourself, one after another,
     following the same section – finish each batch before the next one.
   - Read the rest of the files when they relate to what you found (callers, tests, config).
3. **Collect the findings** – yours and every subagent's. Subagents read the code you did not:
   **post every finding they return**. Do not drop one because it looks minor to you or because
   you cannot confirm it from what you read yourself; you may look at the code to word it
   better. Merge only findings about the same problem at the same place.
4. **Before opening a thread** check the existing threads on that file (shown in `pr diff`).
   Same problem already reported → reply in that thread (`--thread <id>`), do not duplicate.
5. **Open a thread** on the exact line:
   `bdy pr comment add <N> --rev vK --path <path> --new-line <n> -m "<text>"`
   (`--old-line` for a removed line). Say what breaks and when (input / state → wrong result),
   and the fix if it is clear. When fixing a problem needs changes in more than one place (for
   example the code and a test, or a definition and its callers), open a thread at **each place
   that has to change**, each pointing to the others – the author works line by line.
6. **Re-review** (the PR was reviewed before; `pr diff` shows changes since your review by
   default): first go through the threads marked RESOLVED by the author:
   - fixed, or `by_design` / `wontfix` with a convincing reason → `bdy pr comment close <N> --thread <id>`
   - not fixed or not convincing → `bdy pr comment reopen <N> --thread <id> -m "<why>"`

   Then review the new changes like steps 2–5 (`bdy pr file ls <N> --since-review`).
7. **Verdict** on the version you read:
   - problems that must be fixed → `bdy pr review request-changes <N> --rev vK`
   - otherwise → `bdy pr review approve <N> --rev vK`
   - add a short summary comment: `bdy pr comment add <N> -m "<summary>"`

   If the CLI refuses the verdict because MUST_REVIEW files are unread, read them (or hand them
   to subagents) and try again. Never use `--force` unless the user asks for it.

## Reviewing one batch

For a subagent, or for you when you work through the batches alone.

1. `bdy pr file ls <N> --batch <B> --rev vK` – the files of the batch in review order, with
   their size in tokens.
2. Read them in that order, several files per call (`bdy pr diff <N> <path> <path> … --rev vK`),
   about 5k tokens per call; a file bigger than that alone. Read **all** of it before you
   judge. Saving diffs to files without reading them does not count – the problems are found in
   the reading, not in the opening. Open a single file with `--full` when you need more context.
3. Look for real problems: wrong logic, broken edge cases, security issues, races, resource
   leaks, API / contract breaks, tests that no longer test what they claim. Skip style nits,
   personal preferences, behaviour changes the PR description states as intended, and what is
   already reported in the existing threads. A short list of real problems beats a long list
   of maybes – this is where findings are filtered, so report only what you would post.
4. Record each problem as `path`, `line` (new / old), `problem`, `scenario`, `fix`. A problem
   that needs changes in several places gets one item per place, each naming the others.
   A subagent returns the list and `read: <files read> / <files in the batch>` without posting.

## Large outputs

A command output that is too long for your tool (in Claude Code above ~30k characters) is cut
to a short preview and the rest saved to a file. Do not judge from the preview: read the saved
file to the end (in parts, with your file-reading tool), or repeat the command with fewer files.
`bdy pr diff <N> --batch <k>` prints a whole batch (up to ~40k tokens) at once, so it always
ends up in such a file – prefer groups of paths.

## Finish

Tell the user (or print, in CI) the verdict, the number of threads opened / closed / reopened
and the PR link. Do not trigger the author or another review round – the loop is configured on
the Buddy side.
