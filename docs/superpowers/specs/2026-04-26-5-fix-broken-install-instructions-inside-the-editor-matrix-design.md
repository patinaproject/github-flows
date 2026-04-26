# Design: Fix broken install instructions inside the editor matrix [#5](https://github.com/patinaproject/github-flows/issues/5)

## Context

`README.md` has an "Install in another editor" `<details>` matrix that documents how to install or wire up `github-flows` in editors beyond the Quick start (Claude Code, Codex CLI, Codex App, Copilot, Cursor, Windsurf, Aider, Zed, Cline, Opencode, Continue.dev).

The plugin actually ships four skills under `skills/`:

- `/github-flows:new-issue`
- `/github-flows:edit-issue`
- `/github-flows:new-branch`
- `/github-flows:write-changelog`

There is no skill named `github-flows`, and Codex marketplace registration and plugin installation are distinct operations. Two sections of the matrix currently contradict that reality.

## Problem

The matrix contains two confirmed copy-paste bugs that make the documented install path fail:

1. **Claude Code section, step 3** (`README.md` lines 65–67) tells the reader to run:

   ```text
   /github-flows:github-flows
   ```

   That slash-command does not exist. A reader following the steps gets "unknown command" rather than a successful invocation.

2. **OpenAI Codex CLI section, step 2** (`README.md` lines 79–81) repeats the marketplace-add command instead of installing the plugin:

   ```bash
   codex plugin marketplace add patinaproject/github-flows@v0.1.0
   ```

   Step 1 already adds the marketplace. Step 2 should install the plugin from that marketplace. The current command also uses `patinaproject/github-flows` as the marketplace slug, while step 1 adds `patinaproject/skills` — the two steps disagree on the marketplace identifier.

A whole-matrix sweep surfaced no further duplicated or non-existent commands. The other editor sections either rely on natively-read repo files (`AGENTS.md`, `.cursor/rules/github-flows.mdc`, `.windsurfrules`, `.github/copilot-instructions.md`) or use plain natural-language invocations, none of which name a non-existent skill.

## Proposed changes

All edits are confined to the `<details>` block in `README.md`. No restructuring; section order, headings, and surrounding prose stay the same.

### Claude Code section (step 3)

Replace the bogus command with a real, smoke-test-worthy invocation. `/github-flows:new-issue` is the most representative entry point and matches the Quick start, so use it:

```text
/github-flows:new-issue patinaproject/github-flows "Tried github-flows install steps"
```

Keep the surrounding prose ("Open a target repository (or an issue in one) in Claude Code and invoke:") unchanged. Optionally tighten the lead-in to clarify it is a smoke test, mirroring the Quick start's framing.

### OpenAI Codex CLI section (step 2)

Replace the duplicated `codex plugin marketplace add ...` line with the actual install command, and align the marketplace slug with step 1 (`patinaproject/skills`).

The intended shape is symmetric with the Claude Code install (`<plugin>@<marketplace>`):

```bash
codex plugin install github-flows@patinaproject-skills
```

**Concern:** the exact `codex plugin install` invocation cannot be confirmed from anything in this repo. `.codex-plugin/plugin.json` declares the plugin name (`github-flows`) and shape but does not document the CLI install grammar, and no doc in this repo pins it down. Executor must verify the exact command against current Codex CLI plugin docs before landing the change. If the real command differs (for example `codex plugin install github-flows --marketplace patinaproject/skills`, or pulls directly from the repo without a marketplace alias), use that and update step 1 if needed for consistency.

The "pin to a tag for reproducible installs" parenthetical should be kept only if the verified Codex CLI grammar actually supports a `@<tag>` suffix on installs. If it doesn't, drop the parenthetical rather than invent syntax.

### Whole-matrix sweep

No other section needs changes. Executor should still re-read each section end-to-end as a final pass to satisfy AC-5-3.

## Acceptance Criteria

### AC-5-1

Claude Code section step 3 invokes a slash-command that exists in this plugin. Verification:

- [ ] After the edit, `README.md` step 3 names one of `new-issue`, `edit-issue`, `new-branch`, or `write-changelog` (recommend `new-issue`).
- [ ] `grep -n '/github-flows:github-flows' README.md` returns no matches.

### AC-5-2

OpenAI Codex CLI section step 2 installs the plugin rather than re-adding the marketplace. Verification:

- [ ] After the edit, step 2 uses `codex plugin install` (verified against current Codex CLI plugin docs), not `codex plugin marketplace add`.
- [ ] The marketplace identifier referenced in step 2 is consistent with step 1.
- [ ] `grep -nc 'codex plugin marketplace add' README.md` returns `1` (only the step-1 occurrence remains).

### AC-5-3

The install matrix as a whole contains no duplicated or non-existent commands. Verification:

- [ ] Re-read every section under "Install in another editor" and confirm each command is unique within its section and references something that exists (a real skill, real file, or real CLI command).
- [ ] `pnpm lint:md` passes.

## Out of scope

- Restructuring the install matrix or changing section order.
- Adding the hero image referenced by the HTML comment near the top of `README.md`.
- Quick start changes outside the `<details>` matrix.
- Any edits outside `README.md`.
