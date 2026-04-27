# Design: Rename repository to using-github [#22](https://github.com/patinaproject/github-flows/issues/22)

## Goal

Rename the repository identity from `patinaproject/github-flows` to
`patinaproject/using-github` while preserving the existing skill contract. The
repo should be ready for a maintainer to perform the GitHub settings rename, and
public documentation should point readers at the new canonical repository slug
where it is referring to this repository's location.

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

## Current State

The repo and plugin are still named `github-flows`. The newer entry-point skill
is `using-github`, but the plugin namespace and slash-command examples still use
`github-flows:`. Those plugin invocation names are runtime contract identifiers,
not necessarily repository-location references.

The repository also contains historical design docs, plans, changelog entries,
issue links, and release links that point at `patinaproject/github-flows`.
Historical artifacts should remain accurate records unless they are part of
current user-facing setup or canonical metadata.

## Approach

Use a targeted repository-slug sweep instead of a broad text rename.

1. Update current canonical repository URL references in active docs and
   metadata, such as badges, release links, issue-filing guidance, release
   automation documentation, and any package/plugin repository field if present.
2. Keep plugin namespace examples such as `$github-flows:using-github` and
   `/github-flows:new-issue` unchanged unless the text is explicitly describing
   the GitHub repository slug. Renaming the shipped plugin namespace is outside
   this issue.
3. Preserve historical changelog links and prior Superpowers artifacts unless a
   current workflow depends on them as canonical live references.
4. Add clear maintainer guidance for the manual GitHub settings rename and for
   users who may want to update local remotes after GitHub redirect handling.

This keeps the change reviewable and avoids breaking installed plugin
invocations while still preparing the repository for its new canonical URL.

## Companion Repository Work

The `patinaproject/skills` marketplace and catalog surfaces are tracked in
patinaproject/skills#35. This repository should not attempt to update those
files directly. It may link to that issue where coordination context is useful.

## Validation

- Run `rg 'patinaproject/github-flows|github.com/patinaproject/github-flows'`
  and review remaining matches as intentional historical records, compatibility
  notes, or plugin namespace examples.
- Run `pnpm lint:md`.
- Review the changed docs with `sed -n '1,220p' <file>` for formatting and
  markdownlint-sensitive line length.

## Out of Scope

- Performing the GitHub repository settings rename from code.
- Renaming skill directories, skill names, or plugin invocation namespaces.
- Updating `patinaproject/skills` marketplace files beyond the companion issue.
- Rewriting historical changelog entries, old plans, or old design docs only to
  change archival links.
