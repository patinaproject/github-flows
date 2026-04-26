---
name: new-branch
description: Use when the user wants to start work on a GitHub issue — fetch latest `main`, create a branch named `<issue-number>-<kebab-title>` (GitHub's default format), rebase it on `main`, and install dependencies (`pnpm install` if `pnpm-lock.yaml` is present, else `npm install` or `yarn install` based on the lockfile).
---

Follow the instructions in ./workflow.md.
