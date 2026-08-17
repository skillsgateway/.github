<!--
  Organization default. A repository that needs more — an evidence section, a
  gate checklist — adds its own .github/pull_request_template.md, which
  overrides this one.

  Note the filename: a single pull_request_template.md is applied automatically,
  whereas a PULL_REQUEST_TEMPLATE/ directory only applies via a ?template=
  query parameter and so is silently skipped in normal use.

  The PR title becomes the commit subject on main (squash merge, PR_TITLE), so
  it must be a valid Conventional Commit — not just the commits inside it.
-->

## What & why

<!-- What changes, and the problem it solves. Link the issue. -->

Closes #

## How it was verified

<!-- The commands you ran and what they reported. Numbers, not adjectives. -->

## Checklist

- [ ] PR title is a valid [Conventional Commit](https://www.conventionalcommits.org/)
- [ ] Every commit is signed off (`git commit -s`) per the [DCO](https://github.com/skillsgateway/.github/blob/main/dco.txt)
- [ ] Documentation updated in this PR, if behavior, API, configuration or UI changed
- [ ] Tests added or updated to cover the change
- [ ] I have read the [Code of Conduct](https://github.com/skillsgateway/.github/blob/main/CODE_OF_CONDUCT.md)
