---
name: new-branch
description: Use when the user wants to start work on a GitHub issue — fetch latest `main`, create a branch named `<issue-number>-<kebab-title>` (GitHub's default format), rebase it on `main`, and install dependencies (`pnpm install` if `pnpm-lock.yaml` is present, else `npm install` or `yarn install` based on the lockfile).
---

<!-- TODO: author ./workflow.md and replace this block with `Follow the instructions in ./workflow.md.` -->

This skill checks out a fresh branch for a GitHub issue.

Behavior summary (for the eventual `workflow.md`):

1. **Resolve the issue.** Accept an issue number, URL, or `#N` from the invocation args. Fetch its title via `gh issue view <N> --json number,title`.
2. **Compute the branch name.** Use GitHub's default format: `<issue-number>-<kebab-title>` — lowercase, hyphen-separated, ASCII-only (drop punctuation, collapse whitespace, trim trailing hyphens). Example: issue 42 "Let agents use GitHub more ergonomically" → `42-let-agents-use-github-more-ergonomically`.
3. **Sync `main`.** `git fetch origin main` then verify the working tree is clean. Refuse if uncommitted changes are present (suggest `git stash` first).
4. **Create and rebase.** `git checkout -b <branch> origin/main`. If the branch already exists locally, switch to it and `git rebase origin/main`.
5. **Install dependencies.** Pick the package manager from the lockfile in repo root: `pnpm-lock.yaml` → `pnpm install`; `yarn.lock` → `yarn install`; `package-lock.json` → `npm install`. If no lockfile and no `package.json`, skip silently. If multiple lockfiles, prefer `pnpm-lock.yaml`.
6. **Report.** Print the branch name, base SHA, and the install command that ran.

Same-repo only.
