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

This section is completed after the skill and docs are implemented so RED
evidence is committed before GREEN evidence.
