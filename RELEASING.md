# Releasing

How a release is cut in this organisation. The reusable workflows here implement it; each
repository's own `release.yml` wires them together with its build and publish jobs.

## Versioning: there is nothing to bump by hand

**The git tag is the only version.** No repository holds a version string a human edits.
Java/Maven projects derive it from git state with
[Maveniverse Nisse](https://github.com/maveniverse/nisse) —
`<version>${nisse.jgit.dynamicVersion}</version>`.

This is why every build checks out with `fetch-depth: 0`: a shallow clone makes Nisse
compute the *wrong* version rather than fail, which is the worst of both outcomes.

**Tags carry no `v` prefix.** `1.0.0`, never `v1.0.0`. Nisse tolerates both, but the
changelog config, the published image tag and the workflow triggers all expect the bare
form, so a prefix is a translation step that exists only to be forgotten in one of them.

## Cutting a release

Release is a manual dispatch — never a push, never a schedule. Pick the branch in the
"Run workflow" dropdown; that ref is what gets tagged.

| Input | Meaning |
|---|---|
| `version` | Leave empty to derive it from the Conventional Commits since the last tag. |
| `prerelease` | `none` for a real release; `rc`/`b`/`a` for a candidate. The number is chosen for you. |
| `ref` | Branch to release from. Empty = the branch dispatched on. |
| `force` | Required before a hand-entered `version` that disagrees with the derived one is accepted. |
| `dry-run` | **Defaults to on.** Resolves and reports; tags nothing, publishes nothing. |

A dry run is `prepare` and nothing else, so previewing a release is cheap. Do it first.

Releases may only be cut from `main`, `hotfix/*` or `release/*`, and only from a commit
that is reachable from that branch and does not already carry a release tag.

## The approval gate

> [!IMPORTANT]
> The `stable` environment must exist **with a required reviewer**. GitHub creates a
> missing environment on demand with *no protection rules*, so an unconfigured `stable`
> means the approval is inert and the release runs straight through. Environment
> protection rules are free on public repositories.

**The approval is on the publish, not on the tag.** That is deliberate. Everything before
the publish is reversible and invisible: a tag can be deleted, and the release is created
as a prerelease, which `/releases/latest` excludes. The gate sits on the two things that
cannot be taken back — publishing to a real registry, and promotion to latest, the moment
a release becomes the one people get.

Approving there also means approving with more to go on: by then the gates are green, the
version is resolved and asserted, and the notes are rendered. Gating the tag job instead
would mean approving a version string and little else.

One consequence: because nothing gates the tag, abandoning a release leaves a tag and a
prerelease behind. Delete them, or fix forward with a patch version — usually cleaner.

## Why a prerelease and not a draft

Both hide a release from `/releases/latest`. But a draft's assets are not readable
without an authenticated token, so a verification step could not download them over the
same path a consumer takes. A prerelease is publicly readable by exact tag while no
consumer resolving "latest" can see it — so the published bytes can be checked for real
before the release becomes real.

## The workflows

Four reusable workflows. A reusable workflow cannot call back into its caller's
workflows, which is why this is four pieces rather than one.

| Workflow | Does |
|---|---|
| `common-release-prepare.yml` | Resolves the version, generates the notes, writes the summary. Nothing durable — a dry run is this alone. |
| `common-release-tag.yml` | Tags, pushes, creates the prerelease. Deliberately **not** the gate. |
| `common-release-assets.yml` | Asserts the artifacts carry the version, attaches them. |
| `common-release-promote.yml` | Promotes to latest. A no-op for a release candidate. |

```
prepare  (dry run stops here)
  → the repo's own gates, on the branch
  → tag + prerelease            common-release-tag.yml
  → [approval: stable] publish  the caller's publish jobs
  → assets                      common-release-assets.yml
  → verify the published bytes
  → promote                     common-release-promote.yml
```

Guard `promote` on "no job failed" rather than "all succeeded": a release candidate
deliberately skips publish jobs, and a skipped dependency would otherwise cascade.

### A note on `$/`

The reusable workflows reference this repository's composite actions as
`$/.github/actions/…` — [self-repository
syntax](https://github.blog/changelog/2026-07-30-reference-same-repository-actions-with-self-repository-syntax/).
It resolves to *this* repository at the exact commit running, not the caller's checkout,
where a workspace-relative `./` would look. A caller that pins a workflow by SHA
therefore gets the actions at that same SHA, and the two cannot drift apart.

Two consequences:

- **actionlint does not understand `$/` yet** and rejects it as a malformed `uses:`
  ([rhysd/actionlint#711](https://github.com/rhysd/actionlint/issues/711)). A lint here
  needs a message-scoped `-ignore` for exactly that error; a genuinely unpinned action
  must still fail.
- **It needs runner 2.336.0 or newer** and is unavailable on GitHub Enterprise Server.
  Both are fine for GitHub-hosted runners on github.com.

## Commit type → changelog section

From `.github/cliff.toml`, which a repository may override with its own `cliff.toml` at
its root. Security first, not alphabetical: "is there a fix I need" outranks "what's new".

| Type | Section |
|---|---|
| `security` | Security |
| `feat` | Features |
| `fix` | Bug Fixes |
| `perf` | Performance |
| `refactor` | Refactoring |
| `docs` | Documentation |
| `test` | Testing |
| `style` | Style |
| `revert` | Reverts |
| `chore` | Miscellaneous |
| `ci`, `build` | omitted — maintainer concerns, not consumer ones |

A `ci`/`build` scope is skipped too, so `fix(ci):` is not advertised as a user-facing
fix. A breaking change (`!` or a `BREAKING CHANGE:` footer) always surfaces, including
for an otherwise-skipped type.

If nothing in the range earns a bump — only skipped types, or nothing conventional —
`prepare` fails saying so rather than reporting "tag already exists".
