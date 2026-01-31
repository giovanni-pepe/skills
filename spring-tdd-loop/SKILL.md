---
name: spring-tdd-loop
description: >-
  Strict Spring Boot TDD workflow: discover the repo’s real test commands (no guessing), run red-green-refactor in tiny steps,
  write/adjust JUnit 5 + Mockito tests (MockMvc for HTTP) before code, keep the repo green, preserve backwards compatibility unless authorized,
  and finish with a change/test/commands summary. Use whenever Codex must modify a Spring Boot service with disciplined TDD.
---

# Spring TDD Loop

## Operating principles (keep short)
- **No guessing**: do not invent test commands, modules, profiles, or infra setup—discover them from wrappers/build files/CI.
- **Test-first**: every behavior change starts with a failing test (Red) before production code.
- **Scope lock**: change only what the current failing test requires; avoid refactors outside the slice.
- **Keep repo green**: if anything fails, stop and fix before continuing.

## Overview
Execute user-requested changes to a Spring Boot project through disciplined TDD: discover real commands and conventions, write tests first, iterate in very small steps, and preserve backwards compatibility unless explicitly authorized.

## Quick Start (discovery-driven)
1. Discover the build tool and real test tasks from `pom.xml`/`build.gradle(.kts)`, wrapper scripts (`mvnw`/`gradlew`), and CI config; record:
   - the default unit-test command
   - the “full” verification command (e.g., verify/check)
   - how integration tests are executed (task/profile/module)
2. **If commands are not already known, run `test-command-discovery` first and record unit/verify/IT commands.**
3. Identify external dependencies required by tests (DB/queue/IdP). Prefer deterministic local setups (Testcontainers/mocks) if the repo supports them; otherwise record required env/setup steps.
4. Slice the requested change into tiny increments, each proven by 1–2 tests.

## Step 1 – Inspect & Prepare
- Read `README`, build files, and existing `src/test/java` to learn conventions (naming, slices, Mockito vs Spring context).
- Identify where IT tests live (naming like `*IT`, separate source sets, Maven profiles, Gradle tasks) and record the exact way they are run.
- If the repo is multi-module, discover module structure from the build and only then use targeted module runs.

## Step 2 – Frame the Change (slice → test mapping)
- Translate the request into a checklist of vertical slices (smallest observable behaviors).
- For each slice:
  - locate the closest existing test to extend, or create a new minimal one
  - **Test mapping rule**: each slice must be backed by at least one test that fails before the code change and passes after
- For backwards compatibility, identify invariants and add regression assertions/tests before modifying code.

## Step 3 – Red/Green/Refactor Loop
1. **Red:** Add/modify a JUnit 5 test that fails for the new requirement.
   - Prefer Mockito for service seams, `@DataJpaTest` for repositories, and avoid `@SpringBootTest` unless required.
   - For HTTP changes, use MockMvc (or WebTestClient for reactive) and assert status, payload shape, and key edges.
2. **Green:** Implement the smallest code change to satisfy the test; touch only files involved in the slice.
3. **Verify:** Run the narrowest verified command first (single test/class if supported by the build), then the unit suite.
   - Re-run integration tests when touching persistence/messaging/HTTP boundaries, according to the repo’s IT execution method.
4. **Refactor:** Clean up only what was introduced in the slice while tests stay green; defer larger cleanups.

## Step 4 – HTTP & Contract Changes
- For controllers/REST clients:
  - Drive changes via MockMvc/WebTestClient tests first (success + validation failure + primary edge).
  - Update OpenAPI/contract files only after tests prove behavior.
  - Preserve backwards compatibility for payloads unless explicitly approved; add regression assertions where needed.

## Step 5 – Wrap Up
- Run the repo’s full verified command (verify/check) plus IT tasks/profiles if applicable.
- Summarize:
  - What changed and why (by slice)
  - Tests added/updated (class names + scenario focus)
  - Exact commands to rerun
- Note remaining risks, TODOs, and any required local setup for deterministic test runs.

## Guardrails
- Stay in tiny increments; if work feels large, split into subtasks and finish sequentially.
- Never remove legacy paths/defaults without explicit approval; prefer additive behavior.
- If external systems are involved, document setup/teardown steps or the repo’s preferred test isolation strategy.
