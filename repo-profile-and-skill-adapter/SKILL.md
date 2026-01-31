---
name: repo-profile-and-skill-adapter
description: >-
  Analyze the repository to produce a concise profile (architecture, tooling, conventions, tests, security, db/migrations, local env),
  derive concrete overrides for existing skills, and perform a gap analysis to recommend repo-specific skills to add (if needed).
  Use whenever starting work in a repo or when outputs feel misaligned with repo patterns.
metadata:
  short-description: Profile repo, adapt skills, and recommend missing skills
---

# Repo Profile & Skill Adapter

## Goal
Output three artifacts:
1) **Repo Profile**: verified facts about the repo (no guessing).
2) **Skill Overrides**: concrete instructions to adapt existing skills to this repo.
3) **Gap Analysis**: whether repo-specific skills are needed, with prioritized recommendations.

## Operating mode
- Read-only
- No guessing: every claim must be backed by files/config found in the repo.

## Workflow

### 1) Tooling & commands
- Detect Maven/Gradle + wrapper presence.
- Extract real commands from build files + CI + scripts/Makefile:
  - unit tests, full verify/check, integration tests, lint/format (if any)

### 2) Architecture
- Identify structure: hexagonal (ports/adapters), layered, multi-module boundaries.
- Record module list and each module purpose.

### 3) Web/API conventions
- Controller location/naming, DTO style (record/Lombok), mappers (MapStruct/manual), JSON strategy.
- Error handling pattern (`@ControllerAdvice`, ProblemDetail, custom error schema).

### 4) Security profile (Keycloak/OAuth2)
- Resource server vs session, issuer/jwk config style, claim→authority mapping, role model (realm/client roles, scopes).
- Existing test helpers for security (MockMvc jwt(), WithMockUser, mock JwtDecoder, etc.).

### 5) Persistence & migrations
- DB hints (Postgres/Oracle), Liquibase configuration and changelog layout.
- Transaction patterns, repositories, test DB setup (compose/testcontainers/embedded).

### 6) Local environment
- docker-compose files, services, required env vars, startup scripts.

### 7) Emit Skill Overrides (existing skills)
For each existing skill (at minimum: `test-command-discovery`, `spring-tdd-loop`, `openapi-to-spring`, `spring-security-review`, `create-plan-orchestrator`), output 5–10 bullet overrides such as:
- exact verified commands
- verified module paths/locations
- repo conventions (DTO/mapper/tests)
- security test strategy
- Liquibase conventions and constraints

### 8) Gap Analysis (do we need new skills?)
- Scan the repo for recurring complexity that would benefit from specialized skills. Recommend new skills only when there is clear evidence.
- For each recommended skill, output:
  - **Name**
  - **Why needed (evidence in repo)**
  - **What it would do (high-level)**
  - **Priority**: P0 (critical), P1 (high), P2 (nice-to-have)

#### Gap signals (examples)
Recommend skills if you find evidence such as:
- Heavy Liquibase usage with complex contexts/labels/rollback needs → recommend `liquibase-impact-check`
- Keycloak/OAuth2 with custom claim mapping, multiple issuers/audiences → recommend `keycloak-oauth2-config-audit`
- Extensive docker-compose reliance for local dev/test → recommend `docker-compose-test-harness`
- Strict hexagonal boundaries or multiple adapters → recommend `hexagonal-boundary-check`
- Messaging (Kafka/Rabbit) with contract tests or retry/DLQ patterns → recommend `messaging-contract-and-retry-check`
- Observability requirements (structured logs, correlation ids, GDPR/PII rules) → recommend `logging-observability-review`
- Multi-module builds with frequent partial builds → recommend `module-scope-test-runner`

## Output format (keep concise)
- Repo Profile: 8–15 bullets max
- Skill Overrides: grouped per skill, 5–10 bullets each
- Gap Analysis: 3–7 recommended skills max (prioritized)

## Guardrails
- If something is ambiguous (e.g., multiple CI commands), prefer the CI gate and list alternatives explicitly.
- Never recommend new patterns if the repo already has an established one.
- Avoid recommending skills unless there is repo evidence or repeated pain points.