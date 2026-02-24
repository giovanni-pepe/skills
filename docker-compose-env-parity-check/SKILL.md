---
name: docker-compose-env-parity-check
description: Audit docker compose environment parity and service drift across development, testing, and staging in this repo. Use when deployment behavior diverges by environment, when adding env vars/services, or when backend, Keycloak, Redis, and Postgres wiring must remain consistent.
---

# Docker Compose Env Parity Check

## Goal
Find configuration drift in compose stacks and propose minimal, safe harmonization.

## Workflow
1. Build a service matrix.
- Enumerate services in `docker/development/docker-compose.yml`, `docker/testing/docker-compose.yml`, and `docker/staging/docker-compose.yml`.
- Highlight services present only in some environments.

2. Build an env var matrix.
- Compare Django-related blocks (`DATABASE_URL`, `KEYCLOAK_*`, `JWT_*`, cache, static/media paths).
- Compare Keycloak and database variables where applicable.
- Cross-check local defaults in `backend/.env.example`.

3. Check runtime startup assumptions.
- Confirm migration and static collection commands match deployment intent.
- Confirm sidecar/background services (qcluster, logging helpers) are intentionally different when they differ.

4. Report drift by risk.
- Security drift: auth endpoints, issuers, secrets wiring.
- Functional drift: missing services, wrong hostnames, changed command chains.
- Operability drift: logging/metrics toggles and startup behavior.

5. Propose minimal fixes.
- Prefer additive, explicit changes.
- Keep environment-specific values where required, but normalize keys and structure.

## Output Contract
- Provide a parity table by service and env var.
- Mark each difference as intentional, suspicious, or broken.
- Include exact file-level edits to align stacks.

## Guardrails
- Do not flatten environment differences that are required by domain or infra.
- Do not rotate or expose secrets while auditing.
- Do not assume development compose reflects staging requirements.
