---
name: new-branch
description: Use when the user wants to start work on a GitHub issue — fetch the repo's default branch, create a branch named `<issue-number>-<kebab-title>` (GitHub's default format), rebase it on the default branch, and install dependencies via the highest-priority lockfile (`pnpm-lock.yaml` → `pnpm install`; `yarn.lock` → `yarn install`; `bun.lockb` → `bun install`; `package-lock.json` → `npm install`).
---

Follow the instructions in ./workflow.md.
