---
name: angular-openapi-client-consumer
description: Implement frontend features that consume ng-openapi generated TypeScript clients in this Angular workspace. Use when updating API service calls, models, and adapters while preserving generated-code boundaries.
---

# Angular OpenAPI Client Consumer

## Goal
Integrate generated API clients safely into Angular features and keep consumer code maintainable.

## Workflow
1. Locate generated sources.
- Use `frontend/libs/core/api-backend/src/lib` as generated root.
- Treat files marked as generated as read-only.

2. Update consumer code, not generated code.
- Implement adapters or feature services in non-generated libs.
- Keep mapping and transformation logic outside generated files.
- Prefer strict typed models from generated client.

3. Handle response and error paths.
- Normalize API usage patterns in feature-level services.
- Keep UI-facing errors consistent with existing frontend error handling.

4. Detect contract drift.
- If required types/endpoints are missing, request or run schema regeneration workflow instead of manual patching.

5. Verify.
- `cd frontend && npm run ci:format:check`
- `cd frontend && npm run ci:lint`
- `cd frontend && npm run ci:build`

## Guardrails
- Never hand-edit generated client files unless explicitly requested.
- Avoid leaking generated service details directly into UI components.
- Keep API-side effects centralized in services, not components.
