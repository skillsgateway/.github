# Contributing

Organization-wide contribution conventions for [@skillsgateway](https://github.com/skillsgateway).

A repository with its own `CONTRIBUTING.md` overrides this one — read that first
if it exists, since it will carry the build commands and quality gates this
document deliberately leaves out.

Please follow the [Code of Conduct](CODE_OF_CONDUCT.md) in all interactions.

> **Current status:** the Skills Gateway source repository is private and not yet
> open to outside contributions. This document is the standing convention for
> when it opens, and applies to anyone with access today.

## Reporting issues

Use the issue templates. Search existing issues first to avoid duplicates.

**Do not report security vulnerabilities in a public issue** — see
[SECURITY.md](SECURITY.md).

## Submitting changes

1. Branch from `main`, named `<type>/<kebab-description>` — e.g. `feat/pat-rotation`.
2. Make the change, and keep documentation in the same pull request as the
   behavior it describes.
3. Commit following [Conventional Commits](https://www.conventionalcommits.org/),
   signing off every commit (see below).
4. Open a pull request against `main` and fill in the template.

Merging is done by a maintainer. Pull requests are squash-merged, and **the pull
request title becomes the commit subject on `main`** — so the title has to be a
valid Conventional Commit, not just the commits inside it.

## Commit messages and pull request titles

```
<type>(<optional scope>): <short description>
```

| Type | When to use |
|---|---|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | Code restructure with no behavior change |
| `chore` | Routine maintenance, tooling |
| `security` | Security fix or hardening |
| `revert` | Reverts a previous commit |
| `test` | Adding or updating tests |
| `docs` | Documentation only |
| `perf` | Performance improvement |
| `style` | Formatting, whitespace — no logic change |
| `ci` | CI/CD pipeline changes |
| `build` | Build system or tooling changes |

Scope is optional. A breaking change is marked with `!` after the type/scope
(`feat!:`) and explained in the commit body.

This list is enforced on pull request titles by the shared
`common-check-semantic-pr.yml` workflow.

## Developer Certificate of Origin

Every commit must be signed off, certifying you have the right to submit it
under the repository's license. See [`dco.txt`](dco.txt) for the full text.

```
git commit -s -m "feat: add the thing"
```

This appends a `Signed-off-by:` trailer using your git `user.name` and
`user.email`. Set both before your first commit. To sign off work you have
already committed:

```
git rebase --signoff main
```
