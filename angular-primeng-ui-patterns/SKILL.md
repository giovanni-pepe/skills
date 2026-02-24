---
name: angular-primeng-ui-patterns
description: Build and refine pure Angular UI using PrimeNG and repository UI conventions. Use when creating components, page composition, i18n-aware templates, and shared UI library elements in this Nx workspace.
---

# Angular PrimeNG UI Patterns

## Goal
Deliver frontend UI changes that match the repo visual, structural, and localization patterns.

## Workflow
1. Choose the right library location.
- Shared presentational widgets belong in `libs/ui/*`.
- Route-specific smart screens belong in `libs/pages/*`.

2. Reuse PrimeNG and existing wrappers.
- Align with app-level PrimeNG configuration and theme usage.
- Prefer existing shared UI modules/components over duplicating primitives.

3. Keep component architecture consistent.
- Keep templates simple and logic in component classes/services.
- Use SCSS styling with existing include-path and style conventions.
- Follow OnPush-oriented component patterns used in repo.

4. Keep i18n and UX consistency.
- Add translation keys in the repo naming style.
- Reuse layout shell and page composition patterns for new screens.

5. Verify.
- `cd frontend && npm run ci:format:check`
- `cd frontend && npm run ci:lint`
- `cd frontend && npm run ci:build`

## Guardrails
- Avoid ad hoc design systems when repo components already exist.
- Avoid hardcoded user-facing strings when translation is expected.
- Keep reusable UI logic out of route components when it belongs in `libs/ui`.
