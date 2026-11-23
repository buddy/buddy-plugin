---
name: pr-author
description: >-
  Act as the author of a Buddy pull request: open a PR from the current branch with `bdy pr
  create` (optionally after making and committing the change), or address review feedback – fix
  the code, push, and answer and resolve the review threads. Use when the user asks to open,
  create or "załóż" a pull request, to make a change and open a PR with it, or to fix / respond
  to review comments on their PR. Not for reviewing a PR (use pr-review) or only showing one
  (use the buddy skill).
---

# Authoring a Buddy pull request

You are the **author**. You work in the local working copy (read, edit, test, commit, push)
and talk to reviewers through `bdy pr`. Run `bdy pr <command> --help` for flags.

## Before you start

1. **Login.** If `bdy` answers `Token not provided` / `Not logged in`, follow the auth section of
   the `buddy` skill.
2. **Project.** A pull request is opened between branches of **this** repository, so the
   project must be the one this clone belongs to. Both commands below are local (no network):
   - `bdy context show git` – workspace and project from the `origin` remote. If it resolves
     (the remote is a Buddy repository), pass `--context git` to every `bdy` command – the
     active context may be a global default pointing somewhere else;
   - if it fails (the remote is not a Buddy repository), `bdy context show` shows the active
     context: a `link` (`.bdy/project.json` in this directory) is fine; a global default,
     environment or nothing → do not guess: tell the user what you found and ask for the
     workspace and project; pass them as `-w <ws> -p <project>`.

   Do not change the user's configuration (`bdy proj link`, `bdy context use`) unless they ask.
3. **Automatic mode** if the request says `--auto`, "automatically", "CI", or you cannot ask the
   user: make the decisions below yourself and do not ask questions.

## A. Open a pull request

What you may do depends on what the user asked for – the request is the consent, do not add
more and do not ask for less:

| request | commit | push | open the PR |
|---|---|---|---|
| "open a PR" / "otwórz PR" | no | no | from what is already on the server |
| "commit everything and open a PR" | yes | yes | yes |
| "do X and open a PR" | yes (after implementing and testing X) | yes | yes |

1. **Check the state** (read only): current branch, base branch (the repository default unless
   the user names one), uncommitted changes (`git status`), whether the branch exists on the
   remote and has unpushed commits (`git status -sb`, `git log @{u}..`). Never open a PR from the
   base branch – for "do X" create a branch first; for "open a PR" on the base branch, ask.
2. **"Do X and open a PR":** create a branch, implement X, run the project's tests / build for
   it, commit. **"Commit everything":** commit the current changes with a message from the diff.
3. **Write the title and description** from the changes that will be in the PR
   (`git diff <base>...HEAD`, plus what is still to be pushed):
   - what and why, in a few sentences
   - **intended behaviour changes** listed explicitly (reviewers should not flag them as bugs)
   - how it was tested, and anything the reviewer should look at closely
4. **Ask once before opening** (skip in automatic mode – then open it):
   - open the PR now
   - show me the title, description and the list of commits / files first
   - open it as a draft

   For "open a PR" with something missing on the server (the branch is not pushed, commits are
   not pushed, changes are not committed), say exactly what would be left out and put it in the
   same question, e.g. "2 commits are not pushed: push and open / open with what is on the
   server / show me first". Without the branch on the server a PR cannot be opened – then the
   choice is push or stop. In automatic mode never push for a plain "open a PR"; stop and report.
5. **Push** if the request or the answer allows it (`git push -u origin <branch>`), then
   `bdy pr create --base <base> --head <branch> --title "<title>" -F <file>` (`--draft` if
   chosen, `--reviewer <login>` if the user names reviewers).
6. Report the PR number and link, and anything left out of it.

## B. Address review feedback

1. **Plan or apply** (skip in automatic mode): ask once whether to show the plan first (per
   thread: fix / by design / won't fix / question) or apply it right away. With a plan, do not
   commit, push or answer threads until the user accepts it.
2. `bdy pr get <N>` – current version, verdicts, threads. Then
   `bdy pr comment ls <N> --threads` – OPEN threads with code around them. Use
   `--thread <id>` to read a long discussion.
3. Make sure the local branch is the PR head branch and up to date (`git pull`).
4. For every OPEN thread decide one of:
   - **fix** – change the code; if the reviewer is right about the problem but the proposed fix
     is wrong, fix it your way and say so
   - **by_design** – the behaviour is intended; explain why (point to the description, spec, test)
   - **wontfix** – valid but out of scope; explain, and suggest a follow-up if it matters
   - **question** – the comment is unclear; ask in the thread and leave it OPEN

   Do not fix what the reviewer did not ask for. Keep each fix minimal.
5. Run the project's tests / build for what you changed. Commit (one commit per thread or a few
   logical commits) and push – addressing feedback means pushing the fixes. The push creates a
   new PR version.
6. Answer every thread you handled:
   - `bdy pr comment resolve <N> --thread <id> --reason fixed -m "Fixed in <sha>: <what changed>"`
   - `bdy pr comment resolve <N> --thread <id> --reason by_design -m "<why>"`
   - `bdy pr comment resolve <N> --thread <id> --reason wontfix -m "<why>"`
   - question → `bdy pr comment add <N> --thread <id> -m "<question>"`

   RESOLVED means "done, to be verified by the reviewer". Never `close` a reviewer's thread and
   never resolve a thread without a reply.

## Finish

Report: commits pushed, threads resolved per reason, open questions. Do not start another review
round yourself – the review loop is configured on the Buddy side. Locally, if the user wants it,
`bdy pr review request <N> <login>` asks for a re-review.
