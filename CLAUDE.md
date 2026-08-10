# CLAUDE.md — qdrant

Shared Qdrant vector database instance. Read `README.md` first for the standalone-vs-Coolify
basics — this file is the "don't repeat past mistakes" layer.

## This is shared infrastructure, not an app

Deployed once per tenant, reused by every app in that tenant that needs vector storage. It
must **never** get a public domain or a published host port — same convention as this
fleet's shared Postgres. When creating the Coolify application, explicitly suppress the
auto-assigned domain: `PATCH /applications/<uuid>` with
`{"docker_compose_domains": [{"name":"qdrant","domain":""}]}` — Coolify assigns a random
public `<uuid>.jdmnt.co` hostname by default if you don't.

## No consumer yet

Deployed ahead of a concrete need, per explicit request — nothing in this tenant uses it as
of Phase 0. Don't treat its existence as proof anything depends on it; check the fleet Hub's
`registry.yaml` (if you have access — it's private) for what actually has a `depends_on:
qdrant` entry before assuming this is load-bearing for anything.

## Version pinning

Pinned to a concrete `qdrant/qdrant` tag, never `:latest` — check
`gh api repos/qdrant/qdrant/releases/latest` for the current one before bumping, or let
Renovate open the PR. Same "always latest stable, alpine when available" policy as the rest
of this fleet applies, though Qdrant doesn't ship an alpine variant — its default image is
what's used.

## Security hardening

Both compose files carry a hardening block per the
[OWASP Docker Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html):
healthcheck, log rotation, `cap_drop: ALL`, `no-new-privileges`, resource limits, and a
read-only root filesystem. Validated against a real container (including a real
collection-create write) before landing on the live instance.

**Two `tmpfs` mounts are required under `read_only: true`, not one.** `/tmp` alone isn't
enough — Qdrant also needs its own `./snapshots/tmp` (relative to `/qdrant`, so
`/qdrant/snapshots` as an absolute path) writable, or it panics on startup:
`Failed to create snapshots temp directory`.

**Never `tmpfs` `/qdrant` itself.** That shadows the image's own baked-in `entrypoint.sh`
(which lives at that path) and the container fails to start at all — a different, more
confusing failure than the snapshots one above. Mount only the specific subdirectories that
need to be writable, not the parent.

No healthcheck tooling (`curl`/`wget`) ships in this image — the healthcheck uses bash's
`/dev/tcp` instead, confirmed working against the real `/healthz` endpoint.

## Access control

`QDRANT__SERVICE__API_KEY` is optional (defaults to empty via `${QDRANT_API_KEY:-}`), unlike
Postgres's required password. If this ever gets a real consumer, set a real API key as a
Coolify env var rather than leaving it open on the internal network — defense in depth, even
though the service itself has no public route.
