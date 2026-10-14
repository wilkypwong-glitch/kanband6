---
description: Push the current code to a GitHub repo (pass the repo URL or owner/repo)
argument-hint: <repo-url | owner/repo> [branch]
allowed-tools: Bash(git status:*), Bash(git remote:*), Bash(git branch:*), Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(git log:*), Bash(git diff:*)
---

Push this project's code to GitHub using the repo details the user supplied: `$ARGUMENTS`

## Steps

1. **Parse the arguments.** Accept a full URL (`https://github.com/owner/repo.git`), an SSH URL, or `owner/repo` (expand to `https://github.com/owner/repo.git`). An optional second token is the branch name; default to the current branch. If no repo details were given, ask the user for them and stop.

2. **Inspect the repo.** Run `git status` and `git remote -v`. Show the user a short summary of what will be pushed.

3. **Handle the remote.**
   - No `origin`: `git remote add origin <url>`.
   - `origin` exists with a different URL: tell the user and ask whether to replace it or push to the given URL under another remote name. Do not overwrite silently.
   - `origin` matches: continue.

4. **Commit pending changes (only if there are any).**
   - List untracked/modified files and ask the user to confirm before staging anything unexpected (e.g. screenshots, `.env`, secrets, large binaries).
   - Stage only the confirmed files (avoid blind `git add -A`), then commit with a concise message describing the change.
   - End the commit message with the attribution line from the session's system reminder, if one is given.

5. **Push.** Run `git push -u origin <branch>`.
   - Never use `--force` unless the user explicitly asks for it.
   - If the push is rejected (non-fast-forward), report it and ask how to proceed (pull/rebase vs. force); don't decide on their behalf.
   - If authentication fails, tell the user to sign in (e.g. `gh auth login` or a credential prompt) rather than asking them to paste tokens in chat.

6. **Report.** Confirm the branch, remote URL and latest commit hash pushed, and give the GitHub link to the repo.

## Constraints

- Respect the project's CLAUDE.md (single-file app; the FormSubmit email must only appear in the config constant — don't alter code while pushing).
- Do not modify git config or rewrite history.
