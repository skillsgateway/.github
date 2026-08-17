# `.github`

Organization-level defaults for [@skillsgateway](https://github.com/skillsgateway).

This repository is public so that GitHub applies what is in it. That is not a
style choice — community health files and issue/pull-request templates are only
inherited org-wide from a **public** `.github` repository, and they apply to
private repositories too. A private `.github` repository is ignored entirely.

Nothing product-specific belongs here. Everything in this repository is
world-readable, and the product repository is not public yet.

## What is here

| Path | Applies to |
|---|---|
| `profile/README.md` | The organization profile page |
| `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md` | Every repository without its own copy |
| `.github/ISSUE_TEMPLATE/` | New issues in every repository without its own templates |
| `.github/pull_request_template.md` | New pull requests, same rule |
| `.github/renovate.json5` | The shared Renovate preset, and this repository's own config |
| `.github/workflows/common-*.yml` | Reusable workflows, called by other repositories |

A repository's own file always wins over the default. That is the intended
mechanism for anything too specific to live here — a repository with a detailed,
product-specific `CONTRIBUTING.md` keeps it, and only inherits the rest.

## Using the Renovate preset

One line in the consuming repository's `renovate.json5`:

```json5
{
  extends: ["github>skillsgateway/.github//.github/renovate.json5"],
}
```

Repository-specific rules — dependency groupings, ecosystem pins — go in that
file alongside the `extends`, not in the preset.

The preset references labels by name. **Renovate does not create labels**, and
silently drops any that do not exist, so the consuming repository needs them
before the config does anything. See the label list in `.github/renovate.json5`.

## Calling a reusable workflow

```yaml
jobs:
  semantic-pr:
    uses: skillsgateway/.github/.github/workflows/common-check-semantic-pr.yml@main
```
