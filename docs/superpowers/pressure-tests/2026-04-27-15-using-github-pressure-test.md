# Pressure Test: /using-github Skill [#15](https://github.com/patinaproject/github-flows/issues/15)

## RED: Baseline Without /using-github

Prompt:

> You are in patinaproject/github-flows. A user asks you to file a GitHub issue,
> start an issue branch, update PR guidance, and avoid leaking private repo
> details. Which repository guidance and skills do you use first?

Observed gap:

- No `skills/using-github/SKILL.md` exists.
- The available skill entry points are `new-issue`, `new-branch`,
  `edit-issue`, and `write-changelog`.
- Agents must discover `AGENTS.md`, `.github/pull_request_template.md`,
  `docs/issue-filing-style.md`, and the individual skill contracts piecemeal.
- There is no central skill that tells agents how to route mixed GitHub work.

Observed baseline response:

- The baseline agent found only `skills/new-issue/SKILL.md`,
  `skills/new-branch/SKILL.md`, `skills/edit-issue/SKILL.md`, and
  `skills/write-changelog/SKILL.md`.
- It said it would start with `AGENTS.md`, then separately inspect
  `new-issue`, `new-branch`, `docs/issue-filing-style.md`, and the PR template.
- It identified the gap as "the repo has the ingredients, but not the
  entrypoint."

Expected fix:

- `/using-github` exists and routes mixed GitHub work through the current
  specialized skills and repository docs.

## GREEN: With /using-github

Prompt:

> You are in patinaproject/github-flows. A user asks you to file a GitHub issue,
> start an issue branch, update PR guidance, and avoid leaking private repo
> details. Which repository guidance and skills do you use first?

Observed fresh-agent response:

- `/github-flows:using-github` is the first skill for mixed GitHub behavior.
- New issues route to `/github-flows:new-issue`.
- Issue branches route to `/github-flows:new-branch`.
- Existing issue edits route to `/github-flows:edit-issue`.
- Milestone changelogs route to `/github-flows:write-changelog`.
- PR work routes through `.github/pull_request_template.md`, commit and PR
  title rules, and acceptance-criteria verification.
- Public text is checked against public-repo leak-guard expectations from the
  repository docs.
- The umbrella skill does not duplicate the detailed specialized workflows.
- No conflicting duplicate mixed-GitHub procedure was found.

Non-blocking note:

- There is no dedicated PR skill. `/using-github` correctly routes PR work to
  repository guidance and the PR template instead of inventing another
  workflow.
- Codex-facing prompts use the `$using-github` shorthand while most repository
  docs use `/github-flows:using-github`; this is editor-specific invocation
  syntax, not a procedural conflict.
