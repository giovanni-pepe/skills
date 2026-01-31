---
name: openapi-to-jakarta-rest
description: Implement Jakarta EE REST/Servlet endpoints from an OpenAPI spec in hexagonal Maven multi-module repos (non-Spring). Use when translating OpenAPI into JAX-RS/Servlet resources, DTO records + manual mappers, contract-aligned validation and errors, JUnit 5 + Mockito tests, and verified Maven + Spotless commands.
---

# OpenAPI to Jakarta REST

## Operating Principles
- Treat OpenAPI as source of truth; call out deviations explicitly.
- Do not guess paths, commands, packages, error shapes, or wiring; discover in-repo.
- Keep hexagonal boundaries: DTO/mapping in inbound adapter; no adapter leakage into domain/application.
- Follow java-hexagonal-tdd-loop: tests first, tiny red/green steps, keep repo green.

## Step 1 - Discover contract, scope, and conventions
- Locate OpenAPI file(s) (docs/, openapi/, src/main/resources/). If multiple specs exist, pick the one used or specified.
- Confirm inbound implementation style:
  - JAX-RS Application class and base path.
  - Existing servlets and mapping patterns, only if needed.
- Capture verified conventions:
  - DTO location and style (records + Jackson annotations).
  - Mapper location and style (manual mapper classes).
  - Error response style (JAX-RS Response or servlet error codes; any shared error DTO).
  - Wiring/DI (CDI @Inject vs manual bootstrap wiring).
- Discover real verification commands using test-command-discovery if not already known.

## Step 2 - Map OpenAPI operations to work items
For each in-scope path + method, record:
- Path/query params and constraints.
- Request body schema requirements.
- Success responses (status + schema).
- Error responses (status + schema/shape).
Group into tiny TDD slices (one endpoint or tight group).

## Step 3 - DTOs, mapping, and validation
- Create/adjust DTOs as Java records in inbound adapter DTO package, follow naming (*Dto, *Request, *Response).
- Implement DTO <-> domain mapping in dedicated manual mapper classes.
- Validation:
  - If Bean Validation is present and used, map OpenAPI constraints to annotations.
  - If not, validate explicitly in resource/servlet and return contract-aligned 400.
- Reuse shared schemas/enums/value objects where appropriate, but keep domain free of transport concerns.

## Step 4 - Implement JAX-RS resources / servlets (test-driven)
- Prefer JAX-RS resources for REST endpoints; use servlets only when the repo already does.
- Match annotations and media types to the contract (@Path, @GET/@POST, @Consumes/@Produces).
- Keep endpoints thin:
  - Map inbound DTO -> application input.
  - Delegate to use-case/service.
  - Map result -> outbound DTO.
- Ensure serialization/optional fields align with OpenAPI.

## Step 5 - Error model and exception mapping
- Discover existing error payload shape; reuse shared error DTO if present.
- Map failures to contract responses:
  - Validation -> 400 with documented error shape.
  - Not found / conflict -> 404/409 per contract.
- Implement mapping with JAX-RS Response/ExceptionMapper or servlet sendError.
- If deviation is unavoidable, call it out and propose minimal resolution.

## Step 6 - Tests (JUnit 5 + Mockito)
- Follow java-hexagonal-tdd-loop: failing tests first.
- Prefer light tests:
  - Resource unit tests for mapping, delegation, status + payload.
  - Mapper unit tests for transformation and null/optional handling.
- Cover per endpoint:
  1) Success path.
  2) Validation failure.
  3) Primary edge case (404/409/etc.).
- Keep tests deterministic; avoid external services unless a harness exists.

## Step 7 - Verify and report checklist
- Run verified Maven commands and Spotless checks.
- Provide checklist:
  - Endpoints implemented (path + method + class).
  - DTOs/mappers added or updated.
  - Error mapping components.
  - Tests added/updated (module + class names).
  - Commands executed (exact).

## Guardrails
- Do not introduce Spring or MockMvc.
- Do not violate CODING_RULES.md dependency boundaries.
- Keep changes scoped; no speculative refactors.
- Keep repo green at all times.
