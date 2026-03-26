---
name: global-standards
description: Apply a strong cross-project engineering baseline for implementation, review, refactor, and bugfix work. Use this as the default operating playbook unless the active repository documents stricter or conflicting rules in CODING_RULES.md, README.md, CONTRIBUTING.md, or pull request templates.
metadata:
  short-description: Apply default engineering standards across projects
---

# Global Engineering Standards

## Overview

Use this skill as the default working baseline across repositories. It defines strong defaults for discovery, implementation, review, testing, security, and change hygiene, while explicitly allowing repo-local overrides.

## Discovery First

Before applying defaults, inspect the active repository for local instructions. Read, in this order when present: `CODING_RULES.md`, `README.md`, `CONTRIBUTING.md`, `.github/pull_request_template.md`, and the build or CI files that reveal real commands.

## Precedence

Honor instructions in this order:

1. System, developer, and user instructions
2. Repository-specific rules and templates
3. This skill

If the repository documents a rule that conflicts with this skill, follow the repository and treat the difference as an explicit override.

## Default Operating Rules

- Ground decisions in actual files and commands. Do not invent scripts, build targets, module boundaries, or deployment steps.
- Prefer the smallest coherent change that solves the task without broad refactors unless the request requires it.
- Preserve existing architecture, naming, and dependency direction inside the touched area.
- Prefer code that is easy to scan and explain: clear names, small coherent units, shallow nesting, and explicit control flow over clever shortcuts.
- When the worktree is dirty, never revert unrelated changes. Work around them unless they directly block the task.
- Before editing, identify how the change will be validated. After editing, run the narrowest meaningful checks first and broader checks only when warranted.
- Keep comments rare and useful. Add them only where the code would otherwise be slow to parse correctly.

## Baseline Standards

### Workflow

Follow the defaults in [workflow.md](references/workflow.md) for branching, commit hygiene, validation sequencing, and pull request expectations.

### Architecture

Follow [architecture.md](references/architecture.md) for boundary preservation, public contracts, data changes, and documentation expectations.

### Code Style

Follow [code-style.md](references/code-style.md) for readability, naming, local complexity, comment discipline, and maintainability expectations.

### Testing

Follow [testing.md](references/testing.md) for the minimum test bar, validation depth, and how to handle repositories with weak or missing tests.

### Security

Follow [security.md](references/security.md) for secret handling, input validation, logging safety, and rollout-sensitive changes.

### Repository Overrides

Follow [repo-overrides.md](references/repo-overrides.md) when a repository defines `CODING_RULES.md` or needs a documented exception.

## Task-specific Modes

- Implementation and bugfix work: preserve behavior outside the requested scope, add targeted validation, and surface rollout risk when touching auth, data, messaging, or configuration.
- Refactors: keep behavior stable, validate before and after, and avoid mixing opportunistic rewrites into behavior changes.
- Code reviews: prioritize findings first, ordered by severity, with concrete file references and testing gaps called out.
- Urgent fixes: still inspect local rules first, but bias toward the narrowest safe patch and the fastest meaningful regression check.

## Reusable Templates

Use the templates in `assets/` when a repository needs local conventions or change-management scaffolding:

- `assets/CODING_RULES.template.md`
- `assets/pull_request_template.md`
- `assets/technical-decision-template.md`
