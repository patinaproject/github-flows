# Realign Repo To Bootstrap 1.3.0 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Audit and realign `patinaproject/using-github` with the Bootstrap 1.3.0 baseline.

**Architecture:** Treat Bootstrap 1.3.0 as the baseline contract and this repository's `AGENTS.md` as the local authority. Apply safe local file updates directly, record confirmation-gated remote or release-setting changes as documented gaps, and preserve the existing `using-github` skill behavior.

**Tech Stack:** Markdown skills and docs, JSON plugin manifests, PNPM, Husky, markdownlint-cli2, commitlint, GitHub CLI, GitHub Actions YAML.

---

## Task 1: Audit Bootstrap 1.3.0 Baseline Drift

**Files:**

- Read: `/Users/tlmader/.codex/plugins/cache/patinaproject-skills/bootstrap/1.3.0/skills/bootstrap/audit-checklist.md`
- Read: `AGENTS.md`
- Read: `.github/LABELS.md`
- Read: `.github/workflows/*.yml`
- Create: `docs/bootstrap-1.3.0-realignment-audit.md`

- [ ] **Step 1: Run the local baseline inventory**

  Run:

  ```bash
  find . -maxdepth 3 -type f \( -path './.claude-plugin/*' -o -path './.codex-plugin/*' -o -path './.cursor/*' -o -path './.github/*' -o -path './scripts/*' -o -name 'package.json' -o -name 'AGENTS.md' -o -name 'CLAUDE.md' -o -name 'README.md' -o -name 'RELEASING.md' -o -name 'CONTRIBUTING.md' -o -name 'SECURITY.md' -o -name '.markdownlint.jsonc' -o -name '.markdownlintignore' -o -name '.nvmrc' -o -name '.editorconfig' -o -name '.gitattributes' -o -name '.gitignore' -o -name 'release-please-config.json' -o -name '.release-please-manifest.json' -o -name '.windsurfrules' \) -print | sort
  ```

  Expected: output includes every required Bootstrap core file and agent-plugin surface, or the missing file becomes an audit gap.

- [ ] **Step 2: Run remote baseline probes**

  Run:

  ```bash
  gh repo view --json nameWithOwner,visibility,defaultBranchRef
  gh api repos/:owner/:repo --jq '{allow_squash_merge, allow_merge_commit, allow_rebase_merge, squash_merge_commit_title, squash_merge_commit_message, delete_branch_on_merge, allow_update_branch}'
  gh api repos/:owner/:repo/actions/permissions/workflow --jq .default_workflow_permissions
  gh api repos/:owner/:repo/rulesets --jq '.[] | select(.target=="tag")'
  gh label list --json name,color,description --jq '.'
  ```

  Expected: repository metadata resolves; permission or settings drift is recorded rather than silently changed.

- [ ] **Step 3: Write the audit report**

  Create `docs/bootstrap-1.3.0-realignment-audit.md` with:

  ```markdown
  # Bootstrap 1.3.0 Realignment Audit

  ## Summary

  Short summary of whether the repo is aligned, partially aligned, or blocked.

  ## Applied Local Changes

  - List every local baseline change applied by this branch.

  ## Remaining Follow-Ups

  - List remote settings, labels, or release checks that need operator confirmation.

  ## Verification

  - List every validation command and result.
  ```

- [ ] **Step 4: Commit the audit report**

  Run:

  ```bash
  git add docs/bootstrap-1.3.0-realignment-audit.md
  git commit -m "docs: #26 add bootstrap realignment audit"
  ```

## Task 2: Apply Safe Local Baseline Updates

**Files:**

- Modify: `.github/LABELS.md` if reserved release label documentation is stale
- Modify: `.github/workflows/release.yml` if Bootstrap 1.3.0 workflow permissions are missing
- Modify: `AGENTS.md`, `README.md`, `RELEASING.md`, or `docs/file-structure.md` only when the audit finds stale Bootstrap 1.3.0 guidance
- Modify: `docs/bootstrap-1.3.0-realignment-audit.md`

- [ ] **Step 1: Inspect candidate local gaps**

  Run targeted checks from the audit report:

  ```bash
  rg 'autorelease: pending|autorelease: tagged' .github/LABELS.md AGENTS.md
  rg 'permissions:' .github/workflows/release.yml .github/workflows/lint-actions.yml .github/workflows/lint-md.yml .github/workflows/lint-pr.yml
  pnpm check:versions
  ```

  Expected: version checks pass; any local docs or workflow gaps are clear enough to patch.

- [ ] **Step 2: Patch only local baseline drift**

  Update local files only when the Bootstrap 1.3.0 expectation is unambiguous and does not replace repository-specific `using-github` behavior. Do not mutate GitHub repository settings or labels from this task; record those as follow-ups in the audit report.

- [ ] **Step 3: Verify local references**

  Run:

  ```bash
  rg 'Bootstrap 1.3.0|autorelease: pending|permissions:' AGENTS.md README.md RELEASING.md .github docs
  pnpm lint:md
  pnpm check:versions
  ```

  Expected: Markdown and version checks pass; references are intentional.

- [ ] **Step 4: Commit local realignment updates**

  Run:

  ```bash
  git add .github AGENTS.md README.md RELEASING.md docs/file-structure.md docs/bootstrap-1.3.0-realignment-audit.md
  git commit -m "chore: #26 realign bootstrap baseline files"
  ```

## Task 3: Run Bootstrap Verification Self-Test

**Files:**

- Modify: `docs/bootstrap-1.3.0-realignment-audit.md`

- [ ] **Step 1: Run Bootstrap self-test commands**

  Run:

  ```bash
  pnpm install
  pnpm exec commitlint --help
  pnpm lint:md
  bash -lc 'echo "feat: bad" | pnpm exec commitlint; test $? -ne 0'
  bash -lc 'echo "feat: #1 ok" | pnpm exec commitlint'
  ```

  Expected: install, help, lint, and valid commitlint sample pass; the invalid commitlint sample exits non-zero and the wrapper exits zero.

- [ ] **Step 2: Run targeted Bootstrap probes**

  Run:

  ```bash
  pnpm check:versions
  find skills -maxdepth 2 -name SKILL.md -print
  rg 'uses: [^#@]*@[A-Za-z0-9_.-]+$' .github/workflows || true
  gh api repos/:owner/:repo/actions/permissions/workflow --jq .default_workflow_permissions
  ```

  Expected: manifests match package version; only intended skills ship; workflow action references remain SHA-pinned; workflow permissions result is recorded.

- [ ] **Step 3: Update audit verification section**

  Record each command outcome in `docs/bootstrap-1.3.0-realignment-audit.md`, including any remote follow-up that still requires operator confirmation.

- [ ] **Step 4: Commit verification notes**

  Run:

  ```bash
  git add docs/bootstrap-1.3.0-realignment-audit.md
  git commit -m "test: #26 record bootstrap verification"
  ```

## Task 4: Local Review And Publish

**Files:**

- Read: `.github/pull_request_template.md`
- Create: `/tmp/using-github-26-pr-body.md`

- [ ] **Step 1: Run local review**

  Review the diff for accidental changes to `skills/using-github/SKILL.md`, plugin version drift, unpinned actions, and undocumented Bootstrap gaps.

- [ ] **Step 2: Push branch**

  Run:

  ```bash
  git push -u origin 26-realign-repo-to-bootstrap-1-3-0
  ```

- [ ] **Step 3: Create PR**

  Render `/tmp/using-github-26-pr-body.md` using `.github/pull_request_template.md` headings in order, include `Closes #26`, and add one `### AC-26-n` subsection per acceptance criterion with verification notes.

  Run:

  ```bash
  gh pr create --title "chore: #26 realign repo to bootstrap 1.3.0" --body-file /tmp/using-github-26-pr-body.md
  ```

- [ ] **Step 4: Check publish state**

  Run:

  ```bash
  gh pr view --json number,url,headRefName,mergeStateStatus,reviewDecision,statusCheckRollup
  gh pr checks --watch --interval 10
  ```

  Expected: PR exists and required checks either pass or any pending/failing state is triaged before handoff.

## Self-Review

- Spec coverage: Task 1 covers AC-26-1; Task 2 covers AC-26-2; Task 3 covers AC-26-3; Task 4 covers the Superteam publish requirement.
- Placeholder scan: no placeholders remain; each task has exact paths, commands, and expected outcomes.
- Scope check: the plan stays within Bootstrap 1.3.0 realignment and explicitly avoids redesigning `using-github` workflow behavior.
