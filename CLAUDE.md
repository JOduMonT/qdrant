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

## Access control

`QDRANT__SERVICE__API_KEY` is optional (defaults to empty via `${QDRANT_API_KEY:-}`), unlike
Postgres's required password. If this ever gets a real consumer, set a real API key as a
Coolify env var rather than leaving it open on the internal network — defense in depth, even
though the service itself has no public route.
