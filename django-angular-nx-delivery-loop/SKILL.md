---
name: django-angular-nx-delivery-loop
description: Execute combined Django backend plus Nx Angular frontend changes in Waterjade Forge with repo-native commands and verification. Use when implementing or fixing features that touch backend APIs, frontend routes/libs, authentication wiring, OpenAPI client generation, or cross-layer behavior.
---

# Django + Angular Nx Delivery Loop

## Goal
Deliver feature or bugfix changes in this repository without drifting from its Django, Nx, Docker, and Keycloak conventions.

## Workflow
1. Identify scope quickly.
- Detect whether the task is backend, frontend, or cross-layer.
- If cross-layer, plan backend contract change before frontend consumption.

2. Anchor on repo-native structure.
- Backend apps: `core`, `shared`, `app`, `sync_keycloak`.
- Frontend workspace: `frontend/apps/app` plus `frontend/libs/{core,ui,pages}`.
- Keep API wiring under `backend/app/urls.py` and `backend/backend/urls.py`.

3. Implement minimal change.
- Backend: prefer DRF `APIView` plus serializers, keep camelCase response strategy and custom exception handler behavior.
- Frontend: keep route guards and lazy loaded page libs consistent with existing patterns.
- Preserve generated boundaries: never hand-edit generated API client files unless explicitly requested.

4. Verify with existing commands.
- Root orchestration: `npm run dev:setup`, `npm run dev:start`.
- Backend lifecycle: `cd backend && npm run dev:migration:generate`, `cd backend && npm run dev:migration:run`, `cd backend && npm run dev:start`.
- Frontend quality: `cd frontend && npm run ci:format:check`, `cd frontend && npm run ci:lint`, `cd frontend && npm run ci:build`.
- Frontend tests when available: `cd frontend && npm run test`, `cd frontend && npm run ci:e2e`.

5. Finish with explicit impact notes.
- List changed backend endpoints, frontend consumers, and config/env dependencies.
- Call out anything not executed locally.

## Repo Anchors
- Root scripts orchestrate backend, frontend, and docker lifecycle.
- Backend defaults to JWT auth with Keycloak settings in `backend/backend/settings.py`.
- Frontend auth is configured through environment files and `CoreAuthenticationModule.forRoot`.
- CI workflows rely on reusable templates; treat local scripts as source of truth for executable commands.

## Guardrails
- Do not introduce Spring-specific patterns or commands.
- Do not change environment contracts unless required by the feature.
- Keep changes small and reversible; avoid broad refactors when a focused patch is sufficient.
- Prefer adding tests near changed backend modules and preserving existing lint/format rules.
