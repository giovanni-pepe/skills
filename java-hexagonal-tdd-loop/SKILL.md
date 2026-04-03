---
name: java-hexagonal-tdd-loop
description: Strict Java/Jakarta EE hexagonal TDD workflow for Maven multi-module repos (non-Spring, WAR, JAX-RS/Servlet, CDI wiring). Use when modifying this repo or similar hexagonal Java EE services; require command discovery, red-green-refactor with JUnit 5 + Mockito, Spotless checks, and boundary enforcement.
metadata:
  short-description: "Drive Java hexagonal changes with TDD"
---

# Java Hexagonal TDD Loop

## Operating Principles
- Before coding, inspect the locally available skills and combine this skill with any more specific local skill that matches the task.
- Do not guess commands, modules, profiles, or wiring; discover from pom.xml and repo docs.
- Treat TDD as mandatory for behavior changes; each slice starts with a failing automated test.
- Preserve hexagonal boundaries per CODING_RULES.md; do not create adapter-to-adapter deps; keep domain pure.
- Apply DRY, SOLID, KISS, and YAGNI pragmatically while refactoring after green.
- Keep repo green; fix failures before moving on.
- Run Spotless check when Java code changes.

## Quick Start
1. Inspect the locally available skills and select any repo- or task-specific skills that should be layered with this one.
2. If commands are not known, run test-command-discovery and record unit, full verify, and Spotless commands.
3. Read CODING_RULES.md to confirm module ownership and dependency rules.
4. Identify target module and define a small vertical slice.

## Step 1 - Inspect & Prepare
- Inspect root pom.xml for modules and Java version.
- Locate existing tests and naming conventions.
- Identify wiring points: CDI beans.xml and bootstrap/AppBootstrap.java.
- For persistence, note persistence.xml and how to build EntityManagerFactory.

## Step 2 - Frame the Change (slice -> test mapping)
- Split the request into the smallest observable behaviors.
- Choose the lightest test type:
  - domain: pure unit tests.
  - application: Mockito mocks of ports.
  - adapters: mapping/translation and port interactions.
  - JPA adapter: integration-ish tests using persistence.xml if required.
  - REST inbound: unit-level tests for resources/servlets and mappers unless a harness exists.
- Ensure each slice has at least one test that fails before the code change and passes after.

## Step 3 - Red/Green/Refactor Loop
1. Red: add or modify a JUnit 5 test that fails.
2. Green: implement the minimal code to pass.
3. Verify: run the narrowest verified Maven target if supported; then run the unit suite.
4. Refactor: clean only what you touched while keeping tests green, improving duplication, responsibility clarity, and simplicity without speculative abstractions.

## Step 4 - Module Guidance (hexagonal)
- Domain: avoid frameworks/adapters; use value objects.
- Application: orchestrate use cases; depend on ports; mock ports in tests.
- Inbound adapters (rest-in-adapter, camel-in-adapter): keep DTO <-> domain mapping in mappers; respect base path /api/v1.
- Outbound adapters (jpa-out-adapter, camel-out-adapter, eprocs-out-adapter): test translation and port contracts; for SOAP/CXF/JAAS or Camel routes, prefer unit tests unless a heavier harness exists.
- Bootstrap: keep wiring in bootstrap/AppBootstrap.java; update only for wiring changes.

## Step 5 - Formatting & Verification
- If Java code changed, run Spotless check.
- Run the full verification command discovered earlier.

## Step 6 - Wrap Up
Summarize:
- Changes by slice
- Tests added or updated (module + class + scenario)
- Commands executed (exact)
- Remaining risks/TODOs and required local setup (WAR build, Docker/Tomcat, config dir)

## Guardrails
- Do not add Spring or Spring test utilities.
- Do not violate CODING_RULES.md dependency rules.
- Prefer additive, backward-compatible behavior unless explicitly authorized.
- If a requested change cannot start from a failing test, explain the blocker and either create the missing test seam first or state why the task is analysis-only.
