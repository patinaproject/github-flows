# Design: Realign repo to Bootstrap 1.3.0 [#26](https://github.com/patinaproject/using-github/issues/26)

## Intent

Realign `patinaproject/using-github` with the Bootstrap 1.3.0 baseline while
preserving the repository-specific GitHub workflow skill behavior that makes
this repo distinct. The work should produce a concrete audit, apply accepted
baseline updates, and document any intentionally deferred or out-of-scope gaps.

## Requirements

- AC-26-1: Given the current repository state, when it is compared with
  Bootstrap 1.3.0, then any required baseline differences are identified and
  either applied or documented as intentionally out of scope.
- AC-26-2: Given baseline updates are needed, when the realignment is
  implemented, then Markdown, GitHub templates, plugin metadata, and local
  tooling conventions remain consistent with repository guidance.
- AC-26-3: Given the work is complete, when validation runs, then the
  documented Markdown and repository checks pass or any remaining gaps are
  clearly noted.

## Approach

Use Bootstrap 1.3.0 realignment mode as the source of truth for baseline
coverage. The Executor will walk the audit checklist in ordered batches:
plugin manifests, commit and PR conventions, PNPM tooling, agent and repo docs,
AI platform surfaces, workflows, Superpowers scaffolding, repository settings,
labels, and sampled title hygiene.

For each local file gap, the Executor will compare the repository file against
the Bootstrap 1.3.0 template or checklist expectation, classify the gap as
missing, stale, or divergent, and apply the conservative baseline update when
it does not overwrite repository-specific behavior. If a recommended change is
not safe to apply automatically, the Executor will document the gap and the
reason instead of forcing it through.

Remote settings and label checks will be audited through `gh` where available.
Repository settings writes and reserved release-label edits require explicit
operator confirmation under the Bootstrap contract, so the implementation may
report those as follow-up actions rather than mutating them unilaterally.

## Boundaries

This issue covers baseline realignment only. It does not redesign the
`using-github` workflow model, rename the repository, change issue or PR
semantics beyond the Bootstrap 1.3.0 baseline, or cut a release.

## Verification

Validation should include the Bootstrap self-test where applicable:

- `pnpm install`
- `pnpm exec commitlint --help`
- `pnpm lint:md`
- `echo "feat: bad" | pnpm exec commitlint`
- `echo "feat: #1 ok" | pnpm exec commitlint`

Additional targeted checks should verify plugin version lockstep, Markdown
template references, GitHub Actions pinning, release workflow permissions, and
any applied Bootstrap audit recommendations.

## Concerns

No approval-relevant concerns remain. The only expected constraint is that
Bootstrap realignment mode forbids unconfirmed remote or destructive rewrites;
those items should be reported as explicit follow-up gaps when confirmation is
needed.
