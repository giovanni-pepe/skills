---
name: angular-nx-feature-slice-scaffold
description: Scaffold and wire pure Angular feature slices in this Nx workspace. Use when adding new pages, route trees, or frontend libraries under core, ui, or pages without backend changes.
---

# Angular Nx Feature Slice Scaffold

## Goal
Create or modify Angular feature slices that follow this repository's Nx and library conventions.

## Workflow
1. Classify the slice.
- Use `libs/core` for cross-cutting logic and services.
- Use `libs/ui` for reusable presentational components.
- Use `libs/pages` for route-level smart features.

2. Place files by repo conventions.
- Keep naming consistent with existing library structure.
- Prefer OnPush components and standalone-compatible patterns already used in the repo.
- Keep page route definitions inside page libs and wire lazy loading in `apps/app/src/app/app.routes.ts`.

3. Wire module boundaries.
- Export from each lib `src/index.ts` as needed.
- Avoid cross-page coupling; pages should not depend on other page libs.
- Keep imports aligned with existing path alias conventions.

4. Keep UX conventions.
- Reuse existing layout shell and route guard strategy.
- Keep translation keys consistent with repo localization guidance.

5. Verify.
- `cd frontend && npm run ci:format:check`
- `cd frontend && npm run ci:lint`
- `cd frontend && npm run ci:build`

## Guardrails
- Do not introduce backend dependencies in pure Angular slices.
- Do not bypass existing route guard and layout composition patterns.
- Keep generated client files out of manual edits.
