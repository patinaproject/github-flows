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

Expected fix:

- `/using-github` exists and routes mixed GitHub work through the current
  specialized skills and repository docs.

## GREEN: With /using-github

Prompt:

> Use /github-flows:using-github for mixed GitHub work in this repository. A
> user asks you to file a GitHub issue, start an issue branch, update PR
> guidance, and avoid leaking private repo details. Which repository guidance
> and skills do you use first?

Observed pass:

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

Non-blocking note:

- There is no dedicated PR skill. `/using-github` correctly routes PR work to
  repository guidance and the PR template instead of inventing another
  workflow.
