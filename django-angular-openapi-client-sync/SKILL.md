---
name: django-angular-openapi-client-sync
description: Keep DRF API contracts and generated Angular client code synchronized in this combined stack repo. Use when backend serializers/views change API shape, when endpoints are added or removed, or when frontend types/services from ng-openapi-gen are stale.
---

# Django + Angular OpenAPI Client Sync

## Goal
Apply backend API changes and safely propagate them to the generated Angular client without manual drift.

## Workflow
1. Change backend contract first.
- Update DRF views, serializers, and schema-facing annotations.
- Preserve response conventions: camelCase rendering/parsing and repo error code format.

2. Regenerate frontend client.
- Run repo script: `cd frontend && npm run dev:api-client:generate`.
- Ensure generation path remains `libs/core/api-backend/src/lib` based on `config.json`.

3. Review generated diff.
- Expect changes in generated `api.ts`, `models.ts`, service fns, and model files.
- Do not hand-edit generated files except for emergency hotfixes explicitly requested.

4. Adapt frontend callers.
- Update consuming libs/pages for renamed fields or payload changes.
- Validate route and auth flows still call the correct endpoints.

5. Verify.
- Run `cd frontend && npm run ci:format:check`.
- Run `cd frontend && npm run ci:lint`.
- Run `cd frontend && npm run ci:build`.

## Repo Anchors
- Contract generation command is defined in `frontend/package.json`.
- OpenAPI generator config is in `frontend/libs/core/api-backend/config.json`.
- DRF spectacular settings are in `backend/backend/settings.py`.

## Guardrails
- Keep backend and frontend changes in one coherent patch.
- Treat generated file churn as signal; investigate unexpected removals.
- Do not bypass generation by manually patching only TypeScript models.
