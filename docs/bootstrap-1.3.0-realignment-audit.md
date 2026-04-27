# Bootstrap 1.3.0 Realignment Audit

## Summary

The repository is partially aligned with Bootstrap 1.3.0. Core tooling,
plugin manifests, AI editor surfaces, workflow permissions, merge settings, and
Superpowers scaffolding are present. This branch records the audit and applies
safe local documentation/workflow wording updates while preserving the
`using-github` skill contract.

## Bootstrap Checks

| Area | Result | Notes |
| --- | --- | --- |
| Core repo tooling | Aligned | PNPM, Husky, markdownlint, commitlint, version sync scripts, and public `SECURITY.md` are present. |
| GitHub metadata | Partially aligned | Templates and workflows are present. Local label docs now match Bootstrap 1.3.0 release-label wording. Remote release labels exist but have empty descriptions. |
| Agent and repo docs | Aligned | Required docs are present and stale removed-skill wording was refreshed. |
| Claude configuration | Aligned | `.claude/settings.json` enables `superteam` and `superpowers`. |
| AI platform surfaces | Aligned | Claude, Codex, Copilot, Cursor, Windsurf, release-please, and skill surfaces are present. |
| Superpowers opt-in | Aligned | `docs/superpowers/specs/` and `docs/superpowers/plans/` exist. |
| Repository merge settings | Aligned | `gh api repos/:owner/:repo` reports the expected squash-only merge settings. |
| Workflow permissions | Aligned | `gh api repos/:owner/:repo/actions/permissions/workflow --jq .default_workflow_permissions` returns `write`. |
| Tag rulesets | Aligned | No tag-target rulesets were returned. |
| Commit / PR title hygiene | Aligned with historical exception | Recent release commits use `chore: release ...`, which is expected for Release Please; other sampled commits follow the issue-tagged convention. No open PR titles were present during audit. |

## Applied Local Changes

- Added this Bootstrap 1.3.0 realignment audit.
- Refreshed `.github/LABELS.md` release-label wording and reserved-label colors.
- Removed stale removed-skill wording from `docs/file-structure.md`.
- Refreshed `RELEASING.md` release-flow guidance from the Bootstrap 1.3.0 Patina Project supplement.
- Refreshed `.github/workflows/release.yml` trigger comment/order to match the Bootstrap 1.3.0 Patina Project supplement.

## Remaining Follow-Ups

- Remote labels `autorelease: pending` and `autorelease: tagged` exist with
  color `ededed`, but both have empty descriptions. Bootstrap 1.3.0 reserves
  these labels for Release Please automation and requires non-empty
  descriptions. The `bootstrap` contract says not to mutate labels without
  explicit confirmation, so this branch documents the gap instead of editing
  the remote labels.
- Release immutability is UI-only in GitHub's standard repository API and was
  not verified by CLI. Confirm **Settings -> General -> Releases -> Enable
  release immutability** by eye.
- End-to-end release smoke was not run because this branch should not cut or
  simulate a release. Existing release workflow configuration and repository
  permissions were audited instead.

## Verification

- `find . -maxdepth 3 ...` confirmed required baseline files and agent-plugin
  surfaces are present.
- `gh repo view --json nameWithOwner,visibility,defaultBranchRef` returned
  `patinaproject/using-github`, `PUBLIC`, default branch `main`.
- `gh api repos/:owner/:repo --jq '{allow_squash_merge, allow_merge_commit, allow_rebase_merge, squash_merge_commit_title, squash_merge_commit_message, delete_branch_on_merge, allow_update_branch}'` returned the expected Bootstrap merge settings.
- `gh api repos/:owner/:repo/actions/permissions/workflow --jq .default_workflow_permissions` returned `write`.
- `gh api repos/:owner/:repo/rulesets --jq '.[] | select(.target=="tag")'` returned no tag rulesets.
- `gh label list --json name,color,description --jq '.'` showed the reserved release labels are present, but their descriptions are empty.
