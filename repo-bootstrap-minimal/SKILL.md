---
name: repo-bootstrap-minimal
description: Create a minimal, runnable repo bootstrap spec without assumptions. Use when the user wants a scaffold plan and checklist but must choose language, runtime, build tool, architecture, tests, formatting, packaging, modules, and CI explicitly.
---

# Repo Bootstrap Minimal

## Goal
Produce a minimal project scaffold spec and checklist so that:
- The repo has a source of truth (README + rules) for future skills.
- At least one passing test and a verified command set exist.
- Structure reflects the user's explicitly chosen stack.

## Operating Principles
- No assumptions: do not pick language/framework/tools/architecture defaults.
- Ask only what is required to generate a correct bootstrap.
- Minimal footprint: scaffold only what is needed to run tests and enforce formatting.
- Skill-ready: ensure repo-profile-and-skill-adapter and test-command-discovery can operate.

## Required Inputs
Ask the user for these (compact, multiple-choice when possible):
1) Language & version
2) Runtime / framework
3) Packaging
4) Build tool + wrapper preference
5) Architecture
6) Testing stack
7) Formatting / lint gate
8) Modules (if multi-module)
9) CI (or human gate commands)

## Outputs
Provide three artifacts:

### A) Scaffold Spec
- Repository tree (top-level + modules)
- Minimal build config files
- Minimal runtime entry point (if applicable)
- Minimal test file(s) and locations
- Minimal formatting config
- Minimal docs: README + CODING_RULES.md (or ARCHITECTURE.md)

### B) Verified Command Contract
Commands that must pass on a fresh clone:
- Format check
- Unit tests
- Full verify/check
- Optional integration tests (only if requested)

### C) Definition of Done
- Repo builds
- Formatting gate passes
- At least one test passes
- README documents commands
- Rules file defines boundaries (if architecture requires it)

## Workflow

### Step 1 - Collect inputs (blocking)
- Ask for all 9 categories above.
- If anything is missing, ask for it (no substitutes).

### Step 2 - Generate the minimal scaffold spec
- Create a minimal repo tree aligned with chosen architecture/modules.
- Specify exact files to create and their purpose (no code unless requested).
- Represent chosen packaging/runtime only to the minimal needed extent.

### Step 3 - Define the command contract
- Derive commands from the chosen build tool and formatting/testing choices.
- If CI is selected, align with CI gate.
- If no CI, define human-gate commands.

### Step 4 - Seed tests and formatting gates
- Specify one minimal test per chosen test stack.
- Specify formatting config and how to apply it.

### Step 5 - Seed documentation and rules
- README must include prerequisites and commands (format/test/verify/run).
- Rules file must include module responsibilities and dependency directions.

### Step 6 - Handoff to profiling skills
- Ensure test-command-discovery can find real commands.
- Ensure repo-profile-and-skill-adapter can infer architecture/conventions.

## Guardrails
- Do not recommend technologies or defaults.
- Do not generate large boilerplate.
- Code scaffolding is a separate step if explicitly requested.
