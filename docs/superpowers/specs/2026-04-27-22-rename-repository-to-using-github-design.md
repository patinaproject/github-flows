# Design: Rename repository to using-github [#22](https://github.com/patinaproject/github-flows/issues/22)

## Goal

Rename the repository identity from `patinaproject/github-flows` to
`patinaproject/using-github` and collapse the shipped skill surface to the
single `using-github` entry point. The repo should be ready for a maintainer to
perform the GitHub settings rename, and public documentation should point
readers at the new canonical repository slug where it is referring to this
repository's location.

## Requirements

- AC-22-1: Given the repository rename has been applied in GitHub settings, when
  a user opens `https://github.com/patinaproject/using-github`, then the
  repository resolves as the canonical project location.
- AC-22-2: Given repository-owned documentation or metadata references the
  canonical repository slug, when this issue is implemented, then those
  references use `patinaproject/using-github` where appropriate.
- AC-22-3: Given existing users may have links or remotes pointing at
  `patinaproject/github-flows`, when the rename is complete, then compatibility
  expectations are documented for GitHub redirects or local remote updates.
- AC-22-4: Given the plugin is installed after this change, when an agent lists
  available skills from this repository, then only the `using-github` skill is
  shipped.
- AC-22-5: Given users previously invoked the removed specialized skills, when
  they read the release-facing documentation for this change, then the removal
  is marked as a breaking change and points them to `using-github` as the
  remaining entry point.

## Current State

The repo and plugin are still named `github-flows`. The newer entry-point skill
is `using-github`, but the plugin also ships `new-issue`, `edit-issue`,
`new-branch`, and `write-changelog` as directly invokable skills. Removing those
specialized skills is a breaking change for users who invoke them directly.

The repository also contains historical design docs, plans, changelog entries,
issue links, and release links that point at `patinaproject/github-flows`.
Historical artifacts should remain accurate records unless they are part of
current user-facing setup or canonical metadata.

## Approach

Use a targeted repository-slug sweep paired with an explicit skill-surface
reduction.

1. Update current canonical repository URL references in active docs and
   metadata, such as badges, release links, issue-filing guidance, release
   automation documentation, and any package/plugin repository field if present.
2. Remove all skill directories except `skills/using-github`.
3. Update `using-github` so it remains a useful router for GitHub work without
   instructing agents to invoke removed local skills.
4. Update current install, usage, and release-facing documentation to mark the
   specialized skill removal as a breaking change and to name `using-github` as
   the supported entry point.
5. Preserve historical changelog links and prior Superpowers artifacts unless a
   current workflow depends on them as canonical live references.
6. Add clear maintainer guidance for the manual GitHub settings rename and for
   users who may want to update local remotes after GitHub redirect handling.

This keeps the change reviewable while making the intended breaking change
visible instead of silently leaving removed direct-entry skills documented.

## Companion Repository Work

The `patinaproject/skills` marketplace and catalog surfaces are tracked in
patinaproject/skills#35. This repository should not attempt to update those
files directly. It may link to that issue where coordination context is useful.

## Validation

- Run `find skills -maxdepth 2 -name SKILL.md -print` and confirm only
  `skills/using-github/SKILL.md` remains.
- Run `rg 'new-issue|edit-issue|new-branch|write-changelog' skills README.md
  AGENTS.md docs/issue-filing-style.md` and review remaining matches as either
  historical, migration, or breaking-change context.
- Run `rg 'patinaproject/github-flows|github.com/patinaproject/github-flows'`
  and review remaining matches as intentional historical records, compatibility
  notes, or plugin namespace examples.
- Run `pnpm lint:md`.
- Review the changed docs with `sed -n '1,220p' <file>` for formatting and
  markdownlint-sensitive line length.

## Out of Scope

- Performing the GitHub repository settings rename from code.
- Keeping backwards-compatible direct invocations for removed specialized
  skills.
- Updating `patinaproject/skills` marketplace files beyond the companion issue.
- Rewriting historical changelog entries, old plans, or old design docs only to
  change archival links.
