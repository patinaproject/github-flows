# Pull Request Workflow

**Goal:** Prepare and open a pull request that satisfies the repository's PR
template, title format, and acceptance-criteria conventions, with the
public-repo leak guard applied to the PR body before submission.

`pull request procedure` only opens PRs in the current working directory's
default `gh` repository.

---

## Checklist (use TodoWrite)

Create todos for each step before starting:

- [ ] Step 1: Resolve repo + branch + linked issue
- [ ] Step 2: Read PR template
- [ ] Step 3: Draft title (squash-commit format)
- [ ] Step 4: Draft body using template headings
- [ ] Step 5: Public-repo leak guard
- [ ] Step 6: Confirm with user
- [ ] Step 7: Open the PR
- [ ] Step 8: Report

---

## Step 1: Resolve target

Resolve the current repo and branch:

```bash
gh repo view --json nameWithOwner,visibility \
  --jq '{nameWithOwner, visibility}'
git rev-parse --abbrev-ref HEAD
```

On non-zero exit from `gh repo view` or output that fails the regex
`^[A-Za-z0-9_.-]+/[A-Za-z0-9_.-]+$` for `nameWithOwner`, halt:

> "Could not resolve current repository — refusing to open PR. Ensure the
> working directory has a configured `gh` remote and try again."

Store as `$repo` and `$visibility`. Capture the branch as `$branch`.

**Same-repo only.** If the user passed `-R other/repo`, refuse:

> `` `pull request procedure` only opens PRs in the current working directory's default `gh` repository. To open a PR in another repo, use `gh pr create -R <other/repo>` directly. ``

**Resolve the linked issue.** Branches created by `workflows/new-branch.md`
are named `<N>-<kebab-title>`; extract `<N>` as `$issueNumber` when the branch
matches that pattern. If the branch name does not encode an issue number, ask
the user for the linked issue (or confirm there is none) before proceeding.

When `$issueNumber` is set, fetch the issue to capture its title and
acceptance-criteria block:

```bash
gh issue view "$issueNumber" --json number,title,state,body
```

Store the fetched body as `$issueBody` for AC extraction in Step 4.

---

## Step 2: Read PR template

Read the repository's PR template verbatim:

```bash
cat .github/pull_request_template.md
```

If the file is missing, halt:

> "`.github/pull_request_template.md` not found — refusing to open PR. Add the
> template or run from a repository that ships one."

Capture the template's section headings in source order. The PR body must
reproduce those headings, in that order, with content filled in. Do not
rename, reorder, or invent headings.

---

## Step 3: Draft title

Use the repository's PR title format for squash merges:

`type: #<issue> short description`

For breaking changes, use `type!: #<issue> short description`. Do not include
a scope (e.g. `feat(repo): …` is rejected by commitlint). Keep the subject
within 72 characters.

`type` comes from the conventional-commits set used by this repo (`feat`,
`fix`, `docs`, `chore`, `refactor`, `test`, etc.). Pick the type that matches
the dominant change in the diff.

If `$issueNumber` is unset (no linked issue), ask the user how to title the PR
before proceeding — do not invent an issue number.

Store the result as `$prTitle`.

---

## Step 4: Draft body

Build the body section-by-section using the headings captured in Step 2.

**Linked issue.** Use `Closes #<issue>` when the PR completes the issue,
`Related to #<issue>` otherwise. Omit the section if no issue applies.

**Acceptance criteria.** When the linked issue defines acceptance criteria,
extract every `AC-<issue>-<n>` ID from `$issueBody` and reproduce one section
per relevant AC, using the template's `### AC-<issue>-<n>` heading style. The
heading contains only the AC ID. Put a short outcome summary on the line below
the heading, then verification rows directly under the AC they validate.

- Use checkboxes only for testing/verification rows.
- Evidence rows use `runner | env | @handle | ISO` in fixed order. Omit only
  when the AC is explicitly marked `[platform: none]`.
- The `⚠️ E2E gap` row appears ONLY when automated coverage has a real gap a
  reviewer must consciously accept. Omit the row entirely when coverage is
  comprehensive — never use placeholder text like `n/a` or `none required`.
- The `Manual test:` row stays unchecked until a human reviewer performs the
  steps and flips the box in the GitHub UI.
- If an AC is deferred or out of scope, say so in the summary text and do not
  add fake verification checkboxes.

**Validation.** Additional validation not tied to a single AC.

**Docs updated.** Use the template's checkbox set (`Not needed` /
`Updated in this PR`).

If the issue defines no acceptance criteria, keep the `Acceptance criteria`
heading from the template but say so in one line under it; do not invent ACs.

Render the assembled body to a temporary file (e.g. via `mktemp`) so it can
be passed to `gh pr create --body-file`. Inline `--body` is acceptable only
when every template heading is reproduced verbatim.

---

## Step 5: Public-repo leak guard

If `$visibility != PUBLIC`, skip this step.

Otherwise, scan the rendered body for:

- URL-shaped tokens matching `https://github.com/<org>/<repo>/...`. For each
  unique `<org>/<repo>`, run:

  ```bash
  gh repo view -R <org>/<repo> --json visibility --jq .visibility
  ```

  Treat exit-non-zero / 404 / `PRIVATE` as a **leak**.
- File paths inside fenced code blocks shaped like a private-repo filesystem
  path. When the shape is suspicious but not provable, treat as **ambiguous**
  rather than a confirmed leak.

**On confirmed leak**, refuse:

> `Public-repo leak guard: the PR body references {private-repo|path}, but
> this PR targets a PUBLIC repository. Please rewrite to a public-safe
> summary, or open the PR against a private mirror first.`

**On ambiguous detection**, do not silently rewrite. Warn the user and ask
for a review pass before continuing.

---

## Step 6: Confirm

Present the title and body to the user along with the resolved repo and
branch:

> "**Repo:** {resolved nameWithOwner} ({visibility})
> **Branch:** {$branch}
> **Title:** {$prTitle}
>
> {body}
>
> Approve to open the PR, revise to change, or cancel to abort."

Allow up to 3 revise cycles, then halt with:

> "Maximum revise cycles reached — refusing. Re-run the workflow with a fresh
> draft."

After any revision, re-run the Step 5 leak guard before re-presenting.

On `cancel`, exit cleanly with no mutations.

---

## Step 7: Open the PR

Push the branch if it has no upstream:

```bash
git push -u origin "$branch"
```

Create the PR with the rendered body file:

```bash
gh pr create \
  --title "$prTitle" \
  --body-file <rendered-body-path>
```

If the user supplied additional flags such as `--draft` or `--reviewer`,
forward them. Do not pass `-R other/repo`.

Capture the PR URL from stdout.

---

## Step 8: Report

Print a single block summarizing what was opened:

```text
PR:    {url}
Title: {prTitle}
Repo:  {nameWithOwner}
Branch: {branch}
Linked issue: #{issueNumber}  (omit when no issue)
```

Stop after the report. Do not merge, request reviews beyond the flags the
user passed, or take any further action — those are out of scope for
`pull request procedure`.

---

## Refusal Conditions

Stop and do NOT open a PR if:

1. `gh repo view` cannot resolve the current repository (Step 1).
2. The user passed `-R other/repo` (Step 1) — emit the verbatim same-repo
   refusal.
3. `.github/pull_request_template.md` is missing (Step 2).
4. The public-repo leak guard fires on a confirmed leak (Step 5).
5. Maximum revise cycles (3) reached at Step 6.

---

## Quick Reference

| Step | Action | Blocks on |
|------|--------|-----------|
| 1 | Resolve repo + branch + linked issue | Cross-repo → refuse; bad `gh repo view` → halt |
| 2 | Read PR template | Missing template → halt |
| 3 | Draft title in `type: #N description` form | Missing issue number → ask user |
| 4 | Draft body using template headings in order | AC IDs missing on issue → keep heading, note absence |
| 5 | Public-repo leak guard | Confirmed leak → refuse; ambiguous → warn |
| 6 | Confirm with user | Wait for approve / revise (≤3) / cancel |
| 7 | `gh pr create --body-file` | Inherit `gh` exit code |
| 8 | Report PR URL | Always |

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Inventing PR section names | Reproduce the template headings verbatim, in source order |
| Skipping the issue number in the title | `type: #N short description` is mandatory; ask the user if no branch-encoded issue |
| Adding a scope like `feat(repo): …` | commitlint rejects scopes — drop them |
| Including `⚠️ E2E gap` with placeholder text | Omit the row entirely when coverage is comprehensive |
| Inventing acceptance criteria | Mirror the issue's `AC-<issue>-<n>` IDs; if none exist, say so |
| Pasting body inline without checking headings | Prefer `--body-file` so the rendered body is reviewable before submit |
| Opening a PR with `-R other/repo` | Refuse — same-repo only; user must `cd` into that worktree |
| Skipping the public-repo leak guard | Always run on `PUBLIC` repos; re-run after every revise cycle |
