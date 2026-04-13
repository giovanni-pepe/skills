---
name: documentoinformatico-refactor-guard
description: Review and apply conservative refactors in the DocumentoInformatico repository to improve readability, reduce duplication, and enforce existing Java, Spring, and hexagonal architecture conventions without changing public contracts by default.
---

# DocumentoInformatico Refactor Guard

## Overview

Use this skill only for the `documentoinformatico` repository when the user asks to refactor or
improve existing code for readability, duplication reduction, or Java/Spring/hexagonal
consistency.

This is a `review + fix` skill with conservative scope. It must preserve behavior and existing
public contracts unless the user explicitly approves broader changes.

Read [repo-rules.md](references/repo-rules.md) before making substantial changes.

## When To Use

Use this skill when the user asks for tasks such as:

- improve readability of a service, mapper, resource, or adapter class
- reduce duplicated Java logic inside the same module or layer
- align code with existing Java 21, Spring Boot, MapStruct, or hexagonal conventions in this repo
- review a touched area for architectural drift before applying safe refactors

Do not use this skill as a generic Java/Spring refactor skill for unrelated repositories.

## Operating Contract

Always structure the work in this order:

1. Findings: identify concrete readability, duplication, or architectural issues.
2. Conservative refactor plan: state the safe edits you will apply in the touched area.
3. Edits: implement only the approved conservative refactors.
4. Validation notes: report what was checked and any remaining risk.

If no safe refactor is justified, return findings only.

## Required Workflow

### 1. Re-ground in touched modules

Before editing:

- inspect the files the user wants changed
- identify which modules are touched
- restate the relevant boundary rules for those modules
- prefer repo-local conventions over generic framework advice

### 2. Limit interventions to three categories

Only prioritize:

- readability problems
- duplicated logic
- architectural drift

Ignore opportunistic redesign outside those categories unless the user explicitly broadens scope.

### 3. Apply only conservative refactors

Allowed default refactors:

- extract private methods from long methods
- extract same-module helper classes when duplication is repeated and local
- simplify branching and reduce nesting
- normalize mapper, resource, and service structure to existing repo patterns
- remove copy-paste boilerplate within the current layer
- improve naming when behavior stays unchanged

### 4. Preserve repo boundaries

Treat these as hard guardrails:

- `domain` may depend only on JDK and `domain`
- `application` may depend only on JDK, `application`, and `domain`
- input adapters must not depend on `application.service` implementations
- prefer use case and port abstractions over wiring directly to concrete services across layers

### 5. Stop before structural changes

Do not make these changes without explicit user approval:

- REST DTO or response contract changes
- OpenAPI or Swagger behavior changes
- database schema or Liquibase changes
- message contracts or queue payload changes
- module dependency graph changes
- package boundary rewrites
- ownership moves across `domain`, `application`, and adapters

If the best solution requires one of these, stop after findings and explain the blocker briefly.

## Repo-Specific Preferences

Prefer patterns already present in the repository:

- MapStruct mappers for adapter and DTO translation
- `ApplicationLogger` inside `application` instead of introducing framework logging there
- constructor injection
- ports in `application.port.in` and `application.port.out`
- implementation classes in the existing service or adapter layers instead of new abstraction
  layers

When refactoring, keep naming and packaging aligned with nearby code unless the current local
pattern is clearly causing duplication or drift.

## Validation Rules

- Before running Maven from the repository root, remember that the root build binds Spotless
  `apply` to `validate`, so root Maven lifecycle commands are mutating.
- For review-only analysis, avoid root lifecycle commands when a non-mutating inspection is
  sufficient.
- After edits, prefer the narrowest meaningful validation first.
- If you need a broader Maven check, state clearly that it may rewrite files because of Spotless.

## Decision Rules For Common Requests

### Long application service with repeated logging or branching

- keep the class in `application`
- extract private helpers or same-module collaborators
- preserve use case and port boundaries
- do not introduce Spring dependencies into core modules

### Duplicated adapter mapping logic

- prefer MapStruct consolidation or default helper methods
- keep DTO and domain contracts unchanged by default
- avoid pushing adapter-specific mapping concerns into `domain` or `application`

### Request to "clean up" `domain`

- preserve pure core boundaries
- reject Spring, Jackson, SLF4J, Jakarta Validation, Swagger, or similar infrastructure
  dependencies in `domain`
- favor naming, extraction, and local simplification only

### Request that implies redesign

- report findings first
- describe the broader structural change that would be needed
- stop and ask before implementing it
