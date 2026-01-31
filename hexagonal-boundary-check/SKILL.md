---
name: hexagonal-boundary-check
description: Audit Maven multi-module hexagonal/ports-and-adapters repos for boundary violations. Use to read CODING_RULES.md and module poms, verify dependency directions, detect adapter?adapter or domain?adapter coupling, flag layering leaks, and recommend minimal fixes.
---

# Hexagonal Boundary Check

## Goal
Identify and prevent architectural boundary violations by producing:
- Verified boundary rules (from CODING_RULES.md and module structure)
- Detected violations (with exact file/package/module evidence)
- Minimal remediation plan (move code, introduce ports, update wiring)

## Operating Mode
- Read-only
- No guessing; every rule and violation must cite concrete repo evidence.

## Step 1 - Establish rules
- Read CODING_RULES.md (or equivalent) and extract:
  - allowed dependency directions
  - forbidden couplings
  - package/module naming conventions
- Read root pom.xml and module poms to map:
  - module list and responsibilities
  - declared inter-module dependencies

## Step 2 - Build boundary map
- For each module, record:
  - primary top-level packages
  - entry points (REST inbound, Camel routes, persistence adapters, SOAP adapters)
- Identify shared utilities/modules if they exist and how they are allowed to be used.

## Step 3 - Detect module-level violations
- Compare declared Maven dependencies against the rule set:
  - domain must not depend on adapters/bootstrap
  - adapters should not depend on each other unless explicitly allowed
  - application should not depend on concrete outbound implementations
- Flag direct forbidden dependencies and suspicious chains if evident in poms.

## Step 4 - Detect code-level boundary leaks
Scan for violations (examples):
- Domain leaks
  - imports of adapter/framework packages inside domain
  - DTOs or serialization annotations in domain
- Application leaks
  - application importing concrete adapters
  - application returning transport DTOs
- Adapter?adapter coupling
  - rest-in importing jpa-out classes (or vice versa)
  - camel adapters importing rest adapter types
- Bootstrap leakage
  - business logic in bootstrap

Record each violation with:
- file path
- offending import/reference
- why it violates CODING_RULES.md

## Step 5 - Recommend minimal fixes
For each violation, recommend the smallest safe change:
- Move transport DTOs and Jackson annotations into inbound adapter.
- Introduce a port in application; implement in outbound adapter; wire in bootstrap.
- Replace adapter-to-adapter calls with a port or application service.
- Avoid new shared modules unless rules allow it.

Note wiring implications (update AppBootstrap.java or CDI wiring).

## Step 6 - Optional enforcement
- If the repo already uses ArchUnit, Enforcer, or custom checks, extend those.
- If absent, optionally propose a lightweight enforcement approach (P2) only if asked.

## Output Format
- Boundary rules (verified): 5-10 bullets
- Violations: grouped by type with evidence
- Remediation plan: minimal fix per violation
- Follow-up checks: commands/tests to rerun (use verified Maven/Spotless commands if known)

## Guardrails
- Avoid large refactors; focus on minimal fixes.
- Do not invent architecture; use CODING_RULES.md and established patterns.
- If a violation might be intentional, flag as needs confirmation.
