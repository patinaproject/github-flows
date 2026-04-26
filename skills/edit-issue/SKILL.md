---
name: edit-issue
description: Use when the user wants to edit an existing GitHub issue (title, body, labels, assignees, milestone, state, close reason, or relationships). Prefers GraphQL for newer fields (issue Relationships, close-reason enums) and falls back to `gh issue edit` only for fields the REST CLI covers.
---

<!-- TODO: author ./workflow.md and replace this block with `Follow the instructions in ./workflow.md.` -->

This skill edits a GitHub issue in the current repository.

When the user invokes `/edit-issue`, identify the target issue (number or URL), determine which fields they want to change, and apply each change through the most appropriate API:

- **GraphQL** for newer fields that the REST CLI does not expose well: issue **Relationships** (`addIssueDependency` / `removeIssueDependency` with `BLOCKED_BY` / `BLOCKS`, `addSubIssue` / `removeSubIssue`, `Relates to #N` body prose), and **close reason** (`closeIssue` mutation with `stateReason` enum: `COMPLETED`, `NOT_PLANNED`, `DUPLICATE`).
- **`gh issue edit`** for fields the REST CLI covers cleanly: title, body, labels, assignees, milestone.
- **`gh issue close` / `gh issue reopen`** only when no close reason is being set; otherwise use the GraphQL `closeIssue` mutation so the reason is preserved.

Same-repo only — refuse cross-repo `owner/repo#N` edits and direct the user to `gh issue edit -R <other/repo>` instead.
