---
name: angular-nx-test-target-setup
description: Add and standardize pure Angular Nx unit test targets with incremental coverage adoption. Use when projects were generated without tests, when test targets are missing, or when frontend confidence is too low for safe refactoring.
---

# Angular Nx Test Target Setup

## Goal
Introduce practical, maintainable test targets and focused unit tests in the Angular Nx workspace.

## Workflow
1. Audit baseline.
- Inspect `frontend/nx.json` defaults and project-level `project.json` targets.
- Identify projects with no `test` target or no spec files.

2. Prioritize by risk.
- Start with auth, routing, API adapter, and feature logic with highest change frequency.
- Add tests incrementally instead of enforcing repo-wide blanket coverage in one step.

3. Add test targets.
- Configure per-project `test` targets consistently.
- Keep command style aligned with existing frontend scripts.

4. Add focused tests.
- Favor behavior-driven unit tests over brittle snapshots.
- Keep tests near feature code and avoid cross-library coupling.

5. Verify.
- `cd frontend && npm run test`
- `cd frontend && npm run ci:lint`
- `cd frontend && npm run ci:format:check`
- `cd frontend && npm run ci:build`

## Guardrails
- Do not force strict global thresholds before baseline stability exists.
- Do not refactor large UI areas only to satisfy test harness setup.
- Keep generated code out of manual test-target churn unless necessary.
