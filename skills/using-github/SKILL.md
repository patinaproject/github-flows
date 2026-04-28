---
name: using-github
description: Use when working with GitHub in this repository — creating or editing issues, starting an issue branch, preparing a pull request, or rendering a milestone changelog. Triggers include "file an issue", "create issue", "edit issue #N", "start work on #N", "open a PR", "prepare pull request", and "generate changelog for milestone X".
---

# Using GitHub

Use this skill as the single entry point for GitHub work. It owns issue
creation, issue editing, issue branch creation, milestone changelog rendering,
and pull request preparation.

## First Checks

- Read root repository guidance such as `AGENTS.md`.
- Read `docs/issue-filing-style.md` when filing or editing issues — it is the
  single source of truth for issue-filing conventions in this repo.
- Read local docs that govern the files or GitHub surface being changed.
- Use repository templates for issues and pull requests.
- Use canonical labels from the remote label inventory.
- Do not manually apply or remove reserved release automation labels.
- Keep public-repo output free of private repository URLs, private paths, and
  private content.
- Stay in the current working directory's default `gh` repository unless the
  repository guidance explicitly allows cross-repo work.

## Required Procedures

Follow the matching procedure before taking GitHub actions. These files are
supporting workflow contracts for this skill, not separate installable skills.

- New issue: follow `workflows/new-issue.md`.
- Existing issue edit: follow `workflows/edit-issue.md`.
- Start issue work: follow `workflows/new-branch.md`.
- Milestone changelog: follow `workflows/write-changelog.md`.
- Pull request: follow `workflows/pull-request.md`.

## Shared GitHub Rules

- Branches for issue work use `<issue-number>-<kebab-title>` from the default
  branch.
- Commits and squash PR titles use `type: #123 short description` with no
  scope, unless the change is breaking. Breaking changes use `type!: #123 short
  description`.
- GitHub issue titles are plain-language summaries, not conventional commits.
- Relationships are same-repo `#N` references unless repository guidance says
  otherwise.
- Public issue, PR, and changelog text must pass the public-repo leak guard.
- Duplicate checks happen before filing new issues.
- Label choices come from `gh label list`; do not invent labels.
- Pull request bodies use the repository template headings in order.

## Public-Repo Leak Guard

Before creating or updating public issues, PRs, changelog text, or rendered
release notes:

1. Resolve the target repository and visibility with `gh repo view`.
2. If the target is public, scan the draft for private GitHub URLs and private
   path-shaped content.
3. Refuse confirmed leaks.
4. Surface ambiguous content for explicit review instead of silently rewriting.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Looking for a separate sub-skill per workflow | None exist — this skill and its `workflows/` files are the entry point. |
| Inventing labels or templates | Read the repository label inventory and templates. |
| Treating PR creation as just a `gh pr create` command | Satisfy the repository PR template, title format, and acceptance-criteria rules first. |
| Including private repository context in public text | Rewrite as a public-safe summary or file in a private repository first. |
