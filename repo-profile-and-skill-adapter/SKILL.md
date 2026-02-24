---
name: repo-profile-and-skill-adapter
description: >-
  Analyze the repository to produce a concise profile (architecture, tooling, conventions, tests, security, db/migrations, local env),
  and perform a gap analysis to recommend and generate new repo-specific skills (without modifying existing skills).
  Use whenever starting work in a repo or when outputs feel misaligned with repo patterns.
metadata:
  short-description: Profile repo and generate new repo-specific skills
---

# Repo Profile & Skill Adapter

## Goal
Output three artifacts:
1) **Repo Profile**: verified facts about the repo (no guessing).
2) **Skill Creation Plan**: new repo-specific skills to create (without altering existing skills), including why.
3) **Gap Analysis**: prioritized recommendations for repo-specific skills, with evidence.

## Operating mode
- Read-only repo analysis
- No guessing: every claim must be backed by files/config found in the repo.
- **Do not modify or override existing skills**. Existing skills remain unchanged.
- If repo-specific needs are detected, **propose and generate new skills** instead.

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
- DB hints (Postgres/Oracle), Liquibase/Flyway configuration and changelog layout.
- Transaction patterns, repositories, test DB setup (compose/testcontainers/embedded).

### 6) Local environment
- docker-compose files, services, required env vars, startup scripts.

### 7) Skill Creation Plan (NEW SKILLS ONLY)
Instead of producing overrides for existing skills, generate **new repo-specific skills** when justified by repo evidence.

For each new skill to create, output:
- **Skill Name**
- **Why it exists (repo evidence)**
- **When to use it**
- **Inputs/assumptions**
- **What it does (high-level workflow)**
- **Output format**
- **Priority**: P0 / P1 / P2

If useful, also emit a **draft skill spec** (`name`, `description`, `workflow`, `guardrails`) for immediate creation.

### 8) Gap Analysis (do we need new skills?)
- Scan the repo for recurring complexity that would benefit from specialized skills.
- Recommend new skills **only when there is clear evidence**.
- Avoid generic skills if existing ones already cover the need.

#### Gap signals (examples)
Recommend skills if you find evidence such as:
- Heavy Liquibase usage with complex contexts/labels/rollback needs → `liquibase-impact-check`
- Keycloak/OAuth2 with custom claim mapping, multiple issuers/audiences → `keycloak-oauth2-config-audit`
- Extensive docker-compose reliance for local dev/test → `docker-compose-test-harness`
- Strict hexagonal boundaries or multiple adapters → `hexagonal-boundary-check`
- Messaging (Kafka/Rabbit) with contract tests or retry/DLQ patterns → `messaging-contract-and-retry-check`
- Observability requirements (structured logs, correlation ids, GDPR/PII rules) → `logging-observability-review`
- Multi-module builds with frequent partial builds → `module-scope-test-runner`

## Output format (keep concise)

### Repo Profile
- 8–15 bullets max
- Only verified facts
- Include file references where possible

### Skill Creation Plan
- 3–7 new skills max (prioritized)
- For each skill:
  - Name
  - Why created (repo evidence)
  - When to use
  - What it does
  - Priority
- **Explicitly list which skills were created and why**
- **Do not propose overrides to existing skills**

### Gap Analysis
- 3–7 recommendations max (prioritized)
- If no clear gaps: explicitly say **“No new repo-specific skills needed”** and explain why

## Guardrails
- If something is ambiguous (e.g., multiple CI commands), prefer the CI gate and list alternatives explicitly.
- Never recommend new patterns if the repo already has an established one.
- Do not modify, override, or redefine existing skills.
- Create new skills only when there is repo evidence or repeated pain points.
- Keep outputs concise and actionable.