---
name: messaging-contract-and-retry-check
description: Audit and harden Camel-based messaging with Azure Service Bus in hexagonal Maven multi-module repos. Use to inventory message contracts, retry/DLQ behavior, exception handling, idempotency, and route safety, and to add focused JUnit 5 tests when changing routes.
---

# Messaging Contract & Retry Check

## Goal
Produce a repo-grounded assessment and improvements for messaging reliability:
- Contract inventory (payload + required headers)
- Retry/DLQ behavior (failure handling, timing, classification)
- Route safety (idempotency, poison messages, observability)
- Tests & verification (JUnit 5 focused tests + commands)

## Operating Mode
- Read-only by default; if changes are requested, apply via java-hexagonal-tdd-loop (tests first).
- No guessing; derive contracts, retry settings, and DLQ behavior from actual routes/config.
- Respect CODING_RULES.md boundaries (no adapter-to-adapter coupling).

## Step 1 - Discover routes and shared policies
- Locate Camel route builders in inbound/outbound adapters.
- Identify base route classes/policies and extract:
  - retry count and delays/backoff
  - exception classification (transient vs permanent)
  - dead-letter / failure endpoint behavior
  - header/property propagation
- Locate Azure Service Bus integration points (Camel components, connection config, credentials loading).

## Step 2 - Build the message contract inventory
For each route in scope, record:
- Ingress: from endpoint (queue/topic/subscription), consumer settings
- Payload: expected type/format and schema hints
- Headers/properties: required and optional (correlation id, message id, etc.)
- Egress: to endpoint(s), produced payload/headers
- Side effects: DB writes, SOAP/HTTP calls, file writes
Output a concise contract table per route.

## Step 3 - Failure modes and retry/DLQ semantics
- Identify failure points: deserialization, validation, downstream calls, business rules.
- Determine actual behavior per failure class:
  - retried or not
  - attempts and delay strategy
  - DLQ/error channel behavior
  - message ack timing
- Check poison-message risks and misclassification.

## Step 4 - Idempotency and duplicate handling
- Verify at-least-once safety:
  - message id/correlation id use
  - idempotent consumer or app-level dedupe
  - transactional boundaries (DB vs ack)
- If missing and needed, recommend minimal repo-aligned dedupe pattern.

## Step 5 - Observability and diagnostics
- Confirm correlation id / trace id propagation.
- Verify route start/end + failure logging.
- Ensure retry attempts and final failure are visible.
- Avoid logging sensitive payloads; follow repo logging guidance.

## Step 6 - Tests (focused, JUnit 5)
- Use java-hexagonal-tdd-loop if implementing changes.
- Prefer light tests:
  - contract mapping unit tests
  - exception classification unit tests
  - Camel route tests only if repo already has a harness
- For each route changed, cover:
  1) happy path
  2) transient failure (retry classification)
  3) permanent failure (no endless retry; failure handling)

## Step 7 - Recommendations and verification checklist
- Provide prioritized list:
  - P0 safety issues
  - P1 reliability improvements
  - P2 observability improvements
- Include verified commands to run (use test-command-discovery if needed), plus Spotless if Java changed.

## Guardrails
- Do not redefine retry policy if centralized in base routes; extend existing mechanism.
- Do not add new headers/contracts without documenting them.
- Do not introduce new infrastructure unless clearly required and consistent with repo patterns.
- Avoid broad refactors; keep changes scoped to routes in scope.
