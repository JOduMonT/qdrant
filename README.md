# qdrant

Shared Qdrant vector database. Standalone-usable with plain Docker Compose, or deployed as
shared tenant infrastructure on Coolify. No consumer yet as of Phase 0 — deployed ahead of a
concrete app that needs vector storage.

## Standalone

```bash
cp .env.example .env
# optionally set QDRANT_API_KEY in .env
docker compose up -d
```

## On Coolify (as shared tenant infra)

Deployed with `docker_compose_location` set to `/docker-compose.coolify.yaml`, which joins
the `coolify` external Docker network so other apps in the same tenant can reach it by the
service name `qdrant`. No public domain/FQDN.
