# Security Policy

## Reporting a vulnerability

**Please do not open a public GitHub issue for a security vulnerability.**

Report privately through a
[GitHub security advisory](https://github.com/skillsgateway/.github/security/advisories/new).
This keeps the details confidential while a fix is prepared.

### What to include

- A clear description of the vulnerability
- The affected component and version
- Steps to reproduce, or a proof of concept
- Your assessment of the impact

### Response

This project is maintained by a small team. Reports are investigated as quickly
as is practical, but no specific response time is guaranteed.

Disclosure is [coordinated](https://en.wikipedia.org/wiki/Coordinated_vulnerability_disclosure):
a timeline will be agreed with you before any details are published.

## Supported versions

Only the latest release receives security fixes. Older versions are not patched
— upgrade to the latest release.

## Scope

**In scope:**

- Authentication or authorization bypass
- Serving content that was never approved, or bypassing an approval control
- Arbitrary code execution from untrusted input
- Exposure of credentials, tokens, or audit data
- Tampering with, or erasing, audit records
- A dependency vulnerability with a demonstrated exploit path

**Out of scope** — file these as ordinary bugs:

- Issues requiring physical access to the host
- Resource-exhaustion denial of service with no exploit path
- Issues in development or test tooling with no production impact
- Findings from an automated scanner with no demonstrated impact

## Credit

Researchers who disclose responsibly are credited, unless they prefer to remain
anonymous.
