---
name: nx-test-coverage-enablement
description: Introduce pragmatic test and coverage capability in this Nx Angular workspace with minimal disruption. Use when frontend libraries or pages lack tests, when CI misses quality gates, or when adding test targets to projects generated with unitTestRunner set to none.
---

# Nx Test Coverage Enablement

## Goal
Enable reliable Nx frontend testing where it is missing, prioritizing high-value paths and low-friction adoption.

## Workflow
1. Audit current test baseline.
- Confirm `frontend/nx.json` generator defaults and existing project targets.
- Detect projects without `test` targets or with no spec files.

2. Select initial scope.
- Prioritize `libs/core/*` and `libs/pages/*` touched by active features.
- Start with deterministic unit tests before expanding to wide e2e coverage.

3. Wire test targets minimally.
- Add or adjust project-level test targets consistently.
- Keep lint and format integration aligned with existing scripts.

4. Add focused test cases.
- Cover auth guards/services, API adapters, and route-level logic first.
- Prefer small, behavior-focused tests over snapshot-heavy suites.

5. Verify via existing commands.
- `cd frontend && npm run test`
- `cd frontend && npm run ci:lint`
- `cd frontend && npm run ci:format:check`
- `cd frontend && npm run ci:build`

## Output Contract
- Report added test targets and test files.
- Report coverage intent for each added suite.
- List remaining gaps and suggested follow-up sequence.

## Guardrails
- Do not force blanket coverage thresholds in first adoption step.
- Avoid broad refactors solely to make tests possible.
- Keep generated code outside manual test target churn unless needed.
