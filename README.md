# `.github`

Organization-level defaults for [@skillsgateway](https://github.com/skillsgateway).

This repository is public so that GitHub applies what is in it. That is not a
style choice — community health files and issue/pull-request templates are only
inherited org-wide from a **public** `.github` repository, and they apply to
private repositories too. A private `.github` repository is ignored entirely.

Nothing product-specific belongs here. Everything in this repository is
world-readable.

## What is here

| Path | Applies to |
|---|---|
| `profile/README.md` | The organization profile page |
| `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md` | Every repository without its own copy |
| `.github/ISSUE_TEMPLATE/` | New issues in every repository without its own templates |
| `.github/pull_request_template.md` | New pull requests, same rule |
| `safe-settings/` | Repository settings for every repo in the organization |
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

## Repository settings as code

`safe-settings/` is the single source of truth for repository settings across the
organization. [`github/safe-settings`](https://github.com/github/safe-settings) applies
it from `.github/workflows/safe-settings.yml` — on every push to `main` that touches
`safe-settings/`, weekly for drift correction, and on manual dispatch.

| Path | Owns |
|---|---|
| `safe-settings/settings.yml` | org-wide repository defaults **and the label set** |
| `safe-settings/suborgs/product.yml` | `skillsgateway`: the `protect-main` ruleset and its required checks, the `stable` / `test` environments |
| `safe-settings/suborgs/meta.yml` | this repo: protection without required status checks |
| `safe-settings/deployment-settings.yml` | scope — an empty exclude list, deliberately |

Things that bite, in rough order of how much time they cost:

- **safe-settings deletes any label not listed** in `settings.yml`. Never prune the label
  list to tidy the file.
- **A required status check that a repo never produces blocks every PR on it forever.**
  That is why the meta suborg has none, and why a new context must have run on a real PR
  before it is added to `product.yml`.
- **Org-level rulesets need GitHub Team**, so rulesets are declared at suborg scope and
  created per repo, which is free. **Repository rulesets need a public repo** on a free
  plan — the reason this configuration lives here rather than in a private admin repo.
- **There is no `DRY_RUN`.** The knob is `FULL_SYNC_NOP`, exposed as the `nop` dispatch
  input, and its reporting path crashes in 2.1.18 — so it is not a usable preview. Diff
  intent against the live API instead.
- **Merging a settings PR is the apply.** There is no separate step.

Authentication is the `skillsgateway-safe-settings` GitHub App: variable
`SAFE_SETTINGS_APP_ID` and secret `SAFE_SETTINGS_PRIVATE_KEY` on this repository. The
secret is a private key with write access to every repo in the org — it is never exposed
to a fork, because the workflow only runs on `push` to `main`, on schedule, and on manual
dispatch.

The full onboarding route, including registering the App from a manifest, is written up
as the `github-tools:safe-settings` skill in
[jimisola/claude-plugins](https://github.com/jimisola/claude-plugins).

