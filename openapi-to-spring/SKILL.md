---
name: openapi-to-spring
description: >-
  Implement Spring Boot endpoints from an OpenAPI spec: locate YAML/JSON contracts, honor existing springdoc/generator conventions,
  create/adjust controllers + DTOs + validation + error mapping, back everything with MockMvc tests (happy path, validation, edge),
  and finish with a checklist of endpoints, tests, and commands.
---

# OpenAPI to Spring

## Operating principles (keep short)
- Mirror existing naming/structure; do not introduce new architectural patterns unless explicitly asked.
- Keep changes scoped to requested endpoints; no speculative refactors.
- Treat the OpenAPI contract as source of truth; if deviations are needed, call them out explicitly.
- **No guessing**: don’t invent file paths, commands, error schemas, or generator workflows—discover them in-repo.
- **TDD required**: implement changes following `spring-tdd-loop` (tests first, tiny red/green steps, keep repo green).

## Overview
Turn OpenAPI contracts into production-ready Spring Boot endpoints while preserving the repository’s established patterns. Discover the spec and conventions, implement controllers + DTOs + validation/error handling, cover each path with tests, verify with real repo commands, and report a final checklist.

## Step 1 – Discover the Contract & Conventions
- Locate the OpenAPI files (`openapi.yaml`, `openapi/*.yml`, `src/main/resources/swagger`, `docs/api`). Use `rg -l "openapi"` or search for `springdoc`, `SwaggerConfig`, or `OpenAPI` beans.
- Determine how the project consumes the spec:
  - **springdoc annotations only**: controllers manually annotated—extend them inline.
  - **Generated stubs** (`openapi-generator`, `swagger-codegen`): look for `@Generated` interfaces/classes (e.g., `UserApi`). Prefer re-running generators if automation is part of the workflow; otherwise implement the generated interfaces inside the repo’s `controller/api` packages.
  - **Multiple specs**: confirm which file the user wants implemented and whether versioning (`v1`, `beta`) affects package names.
- Capture naming/package conventions and stick to them. Never introduce new base packages unless explicitly required.
- Discover the repo’s real verification commands (wrappers/CI) and record them for Step 7.

## Step 2 – Map Operations to Work Items (slice-friendly)
- For each path + HTTP verb in scope, record:
  - Request body schema + required query/path params.
  - Success response schema(s) and status codes.
  - Additional responses (validation errors, not found, conflict, etc.).
- Identify the controller/service class that should own the behavior; reuse existing layers/mappers.
- Organize work into slices (one endpoint or closely related endpoints) that can be implemented via TDD loops.

## Step 3 – Model DTOs & Validation
- Create DTOs in the repo’s preferred style and naming.
- Map OpenAPI constraints to Bean Validation (`@NotNull`, `@Size`, `@Pattern`, `@Min/@Max`, etc.).
- Reuse shared component schemas/enums/value objects; avoid duplicates.
- Ensure serialization matches repo strategy (naming, optional fields, date/uuid formats).

## Step 4 – Implement Controllers & Wiring (test-driven)
- Drive each endpoint through MockMvc tests first (success + validation + primary edge), then implement the minimal code to pass.
- When stubs exist, implement the generated interface and respect its mappings/metadata.
- Keep controllers thin; delegate to services/handlers per existing architecture.

## Step 5 – Error Model & Exception Mapping (contract-aligned)
- Extend existing `@ControllerAdvice`/ProblemDetail mechanism; don’t introduce a new one.
- Map validation exceptions to documented 400 error payload shape.
- Map domain errors to documented status codes + payload (404/409/403/401) using explicit rules and centralized error codes/enums if defined.
- If repo constraints force deviations, call them out explicitly and propose the minimal resolution (implementation vs spec update).

## Step 6 – MockMvc Test Coverage
- For every endpoint touched, ensure tests cover:
  1) success path
  2) validation failure (400 + documented error body)
  3) primary edge (404/409/empty list/etc. as per spec)
- Reuse existing fixtures/builders/auth helpers; follow established test naming.

## Step 7 – Verification & Checklist
- Run the repo’s standard commands as discovered (avoid guessing).
- Re-run generator tasks if part of the workflow and include them in the summary.
- Deliver a closing checklist:
  - Endpoints (path + method + controller)
  - Tests (classes/methods)
  - Commands executed
  - Notes (deviations, env setup, follow-ups)

## Guardrails
- Keep changes scoped and repo green at all times (per `spring-tdd-loop`).
- No unrelated cleanup; no speculative refactors.
