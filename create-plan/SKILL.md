---
name: create-plan-orchestrator
description: Create a concise but execution-ready plan that can be handed off to other skills. Use only when the user explicitly asks for a plan related to a coding task.
metadata:
  short-description: Create an execution-ready plan for orchestration
---

# Create Plan Orchestrator

## Goal

Turn a user prompt into a **single, actionable, execution-ready plan** that can be handed off to other skills, delivered in the final assistant message.

## Operating mode

- **Read-only**: do not write, create, or modify files.
- **No code**: do not include code blocks, pseudo-code, or API signatures.
- **No guessing**: never invent file paths, commands, frameworks, or tooling. If unknown, reference where to discover them.

## Minimal workflow

1. **Prefer repo-aware planning**
   - If the repo’s conventions/commands/architecture are not already known, run **`repo-profile-and-skill-adapter`** first (read-only) to obtain:
     - Repo Profile (architecture, conventions, test strategy, security/db/local env)
     - Skill Overrides (how to adapt openapi/security/tdd/commands)
     - Gap Analysis (repo-specific skills to add/use)
   - Use the profile/overrides as the source of truth; do not restate unverified assumptions.

2. **Scan context quickly (discovery-first)**
   - Read `README.md` and obvious docs (`docs/`, `CONTRIBUTING.md`, `ARCHITECTURE.md`).
   - Inspect tooling config to identify *real* commands and structure:
     - `package.json` scripts / `Makefile` targets / `pom.xml` / `build.gradle` / CI workflows.
   - Skim likely-touch areas based on the task (modules, services, endpoints, configs).
   - Note constraints: language/framework, formatting/lint, test strategy, release/deploy shape.

3. **Ask follow-ups only if blocking**
   - Prefer **0 questions**.
   - Ask **at most 1–2** questions only if required to avoid unsafe planning.
   - If not blocked, state assumptions in the intro paragraph (e.g., “Assuming X…”).
   - Prefer multiple-choice.

4. **Produce the plan using the template below**
   - Intro (1–3 sentences) must include: intent + approach + key assumptions.
   - Scope must be crisp.
   - Action items must be **6–10**, atomic, ordered:
     - Always include **tooling discovery** (commands) early, unless already known via repo profile.
     - Always include **code location discovery** (where to change) early, unless already known via repo profile.
     - Always include **validation** (tests/lint) later.
     - Always include **edge cases/risks** (or explicitly state “none identified yet” as a task to confirm).
     - If touching **APIs, auth, data schema/infra**, include **rollout + rollback**.
   - **Orchestration requirement**: when listing steps, prefer the repo-profile’s recommended skills and apply their overrides.

5. **Output only the plan**
   - No meta explanation outside the template.
   - Follow the template exactly.

## Plan template (follow exactly)

```markdown
# Plan

<1–3 sentences: what we’re doing, why, the high-level approach, and key assumptions if any.>

## Scope
- In:
- Out:

## Action items
[ ] <Step 1>
[ ] <Step 2>
[ ] <Step 3>
[ ] <Step 4>
[ ] <Step 5>
[ ] <Step 6>

## Open questions
- <Question 1>
- <Question 2>
- <Question 3>