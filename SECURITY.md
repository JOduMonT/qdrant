# Security Policy

Deployment config for a shared Qdrant vector database, standalone with Docker Compose or as shared tenant infrastructure on Coolify.

## Supported versions

Only the current `main` branch is supported. Fixes land on `main`; there are no release branches.

## Reporting a vulnerability

Please report privately. Do not open a public issue or pull request.

- **Preferred:** [report a vulnerability](https://github.com/JOduMonT/qdrant/security/advisories/new) through GitHub private vulnerability reporting.
- **Email:** jodumont+security@gmail.com
- Include what you found, the affected file or service, steps to reproduce and the impact you see.
- Do not access, change or delete data that is not yours, and do not run denial-of-service or automated scanning against live systems.

You can expect an acknowledgement within 3 business days and a status update within 10. Confirmed issues are fixed as quickly as severity allows, and you are credited in the fix unless you prefer not to be.

## Scope

In scope:

- Compose files: whether the REST and gRPC ports are published, API key handling, volume permissions.

Out of scope:

- Qdrant itself: report it to Qdrant.
- Social engineering and physical attacks.

## How this repository is kept safe

- Dependabot alerts and security updates are on; a vulnerable dependency gets an automatic pull request. Routine version bumps are opened by Renovate, and `.github/dependabot.yml` keeps Dependabot's own version updates off to avoid duplicate pull requests.
- Dependabot pull requests are merged automatically by `.github/workflows/dependabot-auto-merge.yml` once every other check passes. Major version bumps are left open for review.
- GitHub secret scanning with push protection and CodeQL code scanning are enabled.
- Qdrant must never be reachable from the internet without an API key; keep it on the internal Coolify network.
- Image bumps come from Renovate and are smoke-tested in CI.
