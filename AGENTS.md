# Agent instructions

Everything under `safe-settings/` is applied to every repository in the
organization on the next sync. A change here changes branch protection for all
of them, so the rules below are decisions, not defaults to tune.

## Decided by the owner, not to be changed or proposed

- **`required_approving_review_count` stays at 1** on `protect-main` and
  `protect-main-meta`. Third-party contributors are expected, and their PRs
  need a maintainer's approving review. GitHub refusing self-approval is known
  and accepted: the owner's own PRs merge through the admin bypass. Lowering
  the count was rejected in skillsgateway/skillsgateway#547 (2026-10-01) and
  proposed again anyway in #15. Do not propose it a third time.
- **The `OrganizationAdmin` bypass stays `bypass_mode: always`**, decided
  with the review count above.

If a task seems to need either change, stop and ask the owner. Do not open a
PR for it, and do not describe it in another repository's docs as if it were
coming.
