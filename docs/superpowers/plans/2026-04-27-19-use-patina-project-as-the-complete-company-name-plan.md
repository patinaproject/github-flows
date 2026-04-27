# Plan: Use Patina Project as the complete company name [#19](https://github.com/patinaproject/github-flows/issues/19)

> **For agentic workers:** REQUIRED SUB-SKILL: Use
> `superpowers:executing-plans` to implement this plan task-by-task. Steps use
> checkbox (`- [ ]`) syntax for tracking.

**Goal:** Update public documentation so company-display prose uses "Patina
Project" while `patina` identifiers remain unchanged.

**Architecture:** Treat this as a targeted Markdown audit. First capture the
current whole-word `Patina` matches, then update only company-display prose,
then rerun the audit and lint to prove identifiers are preserved.

**Tech Stack:** Markdown, `rg`, `sed`, `markdownlint-cli2`, Git.

---

## File Structure

- Modify `README.md`: update marketplace display wording and related link text.
- Modify `RELEASING.md`: update company-display prose in release guidance.
- Modify `docs/superpowers/specs/2026-04-26-1-bootstrap-and-ship-skills-design.md`:
  update the historical "Patina baseline" display wording.
- Modify `docs/superpowers/specs/2026-04-26-4-make-the-readme-awesome-design.md`:
  update historical README design copy that names the marketplace and plugins.
- Modify `docs/superpowers/plans/2026-04-26-4-make-the-readme-awesome-plan.md`:
  update historical README plan copy that names the marketplace.

## Task 1: Capture The Failing Audit

**Files:**

- Inspect: `README.md`
- Inspect: `RELEASING.md`
- Inspect: `docs/**/*.md`

- [ ] **Step 1: Run the whole-word audit**

Run:

```bash
rg -n '\bPatina\b|patina' README.md RELEASING.md docs/**/*.md
```

Expected: output includes company-display prose such as "Patina marketplace",
"Patina plugins", "outside Patina", and "Patina baseline", plus preserved
identifier matches such as `patinaproject/skills` and
`patina-project-automation`.

- [ ] **Step 2: Classify matches before editing**

Classify these company-display matches for editing:

```text
README.md: Add/Register the Patina marketplace
README.md: Patina marketplace link text
RELEASING.md: forks outside Patina
RELEASING.md: Patina marketplace manifest
docs/superpowers/specs/2026-04-26-1-bootstrap-and-ship-skills-design.md: Patina baseline
docs/superpowers/specs/2026-04-26-4-make-the-readme-awesome-design.md: Patina marketplace / Patina plugins
docs/superpowers/plans/2026-04-26-4-make-the-readme-awesome-plan.md: Add the Patina marketplace / Patina marketplace link text
```

Expected: identifiers such as `patinaproject/skills`,
`github-flows@patinaproject-skills`, `patina-project-automation`, URLs, and
repo slugs are not selected for editing.

## Task 2: Update Company-Display Prose

**Files:**

- Modify: `README.md`
- Modify: `RELEASING.md`
- Modify: `docs/superpowers/specs/2026-04-26-1-bootstrap-and-ship-skills-design.md`
- Modify: `docs/superpowers/specs/2026-04-26-4-make-the-readme-awesome-design.md`
- Modify: `docs/superpowers/plans/2026-04-26-4-make-the-readme-awesome-plan.md`

- [ ] **Step 1: Replace company-display references**

Make these wording-only replacements:

```text
Patina marketplace -> Patina Project marketplace
Patina plugins -> Patina Project plugins
outside Patina -> outside Patina Project
Patina baseline -> Patina Project baseline
Patina marketplace manifest -> Patina Project marketplace manifest
```

Expected: public-facing sentences now use "Patina Project" as the complete
company name.

- [ ] **Step 2: Preserve identifiers**

Do not edit these identifier-like strings:

```text
patinaproject
patinaproject/skills
github-flows@patinaproject-skills
patina-project-automation
https://github.com/patinaproject/...
```

Expected: AC-19-2 remains satisfied after the prose cleanup.

## Task 3: Verify Acceptance Criteria

**Files:**

- Inspect: changed Markdown files

- [ ] **Step 1: Rerun the audit**

Run:

```bash
rg -n '\bPatina\b|patina' README.md RELEASING.md docs/**/*.md
```

Expected: remaining whole-word "Patina" matches are either "Patina Project" or
intentional references in issue #19's design artifact; remaining lowercase
`patina` matches are preserved identifiers.

- [ ] **Step 2: Review the diff**

Run:

```bash
git diff -- README.md RELEASING.md docs/
```

Expected: diff contains wording-only documentation changes and no identifier
renames.

- [ ] **Step 3: Run markdown lint**

Run:

```bash
pnpm lint:md
```

Expected: markdownlint reports zero errors.

- [ ] **Step 4: Commit implementation**

Run:

```bash
git add README.md RELEASING.md docs/superpowers/specs/2026-04-26-1-bootstrap-and-ship-skills-design.md docs/superpowers/specs/2026-04-26-4-make-the-readme-awesome-design.md docs/superpowers/plans/2026-04-26-4-make-the-readme-awesome-plan.md
git commit -m "docs: #19 use complete company name"
```

Expected: the commit succeeds after pre-commit markdownlint and version checks.

## Self-Review

- Spec coverage: Task 2 covers AC-19-1, and Task 3 explicitly verifies both
  AC-19-1 and AC-19-2.
- Placeholder scan: no placeholder instructions are present.
- Scope check: the plan changes Markdown wording only and preserves all
  machine-readable identifiers named in the approved design.
