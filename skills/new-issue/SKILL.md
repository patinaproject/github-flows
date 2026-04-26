---
name: new-issue
description: Use when the user wants to file a new GitHub issue. Helps agents write better issues — no prescribed implementation details, exercises all GitHub fields via GraphQL where useful, smart label selection, duplicate-issue check (offers a follow-up comment instead of filing again), and a public-repo leak guard that blocks links to private-repo URLs or content.
---

<!-- TODO: author ./workflow.md and replace this block with `Follow the instructions in ./workflow.md.` -->

This skill files a new GitHub issue in the current repository.

Behavior summary (for the eventual `workflow.md`):

- **Smart labels.** Read the repo's labels via `gh label list --json name,description` and pick from descriptions; never invent labels.
- **All fields, GraphQL where useful.** Issue body, labels, assignees, milestone via REST/`gh`; relationships (`addSubIssue`, `addIssueDependency` with `BLOCKED_BY` / `BLOCKS`) and project-v2 assignment via GraphQL.
- **No implementation prescription.** The issue describes the problem, desired behavior, and acceptance criteria — never the code path or library to use unless the technology is genuinely load-bearing.
- **Duplicate check.** Before filing, run `gh issue list --search` against the title and key terms (state `all`, includes closed). If a likely duplicate is found, surface it and offer to post a follow-up **comment** on the existing issue instead of filing a new one.
- **Public-repo leak guard.** If the target repo is **public** (`gh repo view --json visibility`), refuse to include any `https://github.com/<org>/<private-repo>/...` link, private-repo file paths, or quoted contents from a private repo in the issue body. Ask the user for a public-safe summary instead.
- **Same-repo only.** Refuse cross-repo `-R other/repo` requests; direct to `gh issue create -R <other/repo>` directly.

Reference shape for the body (final `workflow.md` should mirror the patinaproject `/new-issue` skill's 5-section template: Problem, Proposal, Acceptance Criteria, Context, Out of Scope).
