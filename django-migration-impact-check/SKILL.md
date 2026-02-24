---
name: django-migration-impact-check
description: Review Django migration safety and rollout impact for this repo. Use when creating or modifying migrations, changing model defaults, adding data migrations, or preparing deploy pipelines that execute migrate automatically.
---

# Django Migration Impact Check

## Goal
Prevent unsafe schema or data migrations from breaking startup, deploy, or rollback flows.

## Workflow
1. Inspect migration scope.
- Identify affected apps (`core`, `app`, `shared`, `sync_keycloak`).
- Inspect operations for schema changes, data moves, and irreversible steps.

2. Analyze deploy impact.
- Remember containers run `python manage.py migrate` on startup in testing/staging compose flows.
- Identify migrations that can lock tables, rewrite large columns, or require downtime.

3. Analyze reversibility.
- Verify reverse operations exist for custom `RunPython` migrations.
- Flag data-loss operations and justify one-way migrations.

4. Validate local command path.
- Use existing repo commands for migration generation and execution.
- Confirm resulting migration files are deterministic and minimal.

5. Produce rollout notes.
- Document ordering constraints and prerequisite env changes.
- State whether migration is safe for zero-downtime rollout.

## Verification Commands
- `cd backend && npm run dev:migration:generate`
- `cd backend && npm run dev:migration:run`
- Optionally run backend startup path to ensure post-migrate boot succeeds.

## Guardrails
- Do not merge model changes without matching migrations.
- Avoid bundling unrelated schema changes into one migration.
- Treat data seeding migrations as high-risk; require explicit rationale.
