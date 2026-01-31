---
name: spring-security-review
description: >-
  Review and update Spring Security configs for a feature: inventory SecurityFilterChain/method security/JWT-session/CORS-CSRF,
  apply minimal authorization changes (no guessing, repo-grounded), add MockMvc security tests (auth/unauth/forbidden),
  flag risky defaults, and close with a security note plus verification steps using the repo’s real commands.
---

# Spring Security Review

## Operating principles (keep short)
- **No guessing**: do not invent endpoints, roles, claims, matchers, filter order, or commands—discover them from code/config/CI.
- **Scope lock**: limit changes to the feature’s endpoints/handlers; avoid broad refactors.
- Preserve existing patterns (JWT vs session, method vs http security) unless required to meet the requirement.
- **TDD required**: implement changes following `spring-tdd-loop` (tests first, tiny red/green steps, keep repo green).

## Overview
Use this skill whenever a user wants Spring Security behavior verified or adjusted for a specific feature. Identify the current setup, implement minimal authorization changes, add targeted security tests, flag risky defaults, and report verification steps based on the repo’s actual tooling.

## Step 1 – Inventory the Current Setup
- Locate security config (`SecurityFilterChain`, `WebSecurityCustomizer`, `@EnableMethodSecurity`) and note how many filter chains exist and what each targets.
- Determine auth mechanism (JWT/resource server vs session/basic) by inspecting actual beans/filters/config.
- Capture CORS/CSRF posture and why (browser-facing? cookies? pure API?).
- List security-related properties (issuer, audiences, allowed origins) from the repo’s config sources.

## Step 2 – Frame the Requested Change (scope lock)
- Translate the requirement into concrete rules tied to verified routes/handlers:
  - Record exact endpoints in scope (path patterns, methods) and intended access (public/authenticated/role/scope).
  - **Test mapping rule**: every recorded rule must be matched by at least one test in Step 4 (preferably allowed + unauth + forbidden when applicable).
- Choose the minimal touch point (existing matcher style or method security style).
- Identify which filter chain owns the feature endpoints; avoid touching unrelated chains unless required.
- Extract the repo’s real test/build commands from wrappers/CI for Step 6.

## Step 3 – Implement Authorization Changes (test-driven)
- Write failing security tests first for the rule(s) in scope (401/403/2xx), then apply the minimal matcher/annotation changes to pass.
- Keep matcher rules ordered correctly (first-match wins) and grouped consistently.
- For JWT: update claim→authority mapping consistently (existing converters).
- For sessions: update role/authority mapping consistently (UserDetailsService/authorities mapping).
- CSRF decision rule:
  - browser + cookies/session → prefer CSRF enabled (or selective ignores per repo pattern)
  - stateless bearer-token API → CSRF may remain disabled with documented rationale
- CORS: restrict origins/methods/headers to minimum required; avoid wildcard unless justified.

## Step 4 – Add Security Tests (rule coverage guaranteed)
- For each endpoint/rule recorded in Step 2, add tests that cover:
  - authenticated allowed (2xx)
  - unauthenticated (typically 401)
  - authenticated but insufficient authority (typically 403) when relevant
- 401 vs 403 rule-of-thumb:
  - no/invalid credentials → 401
  - valid credentials but missing authority/scope/role → 403
  (Assert according to the repo’s configured entry point/exception handling.)
- Keep tests deterministic (mock JwtDecoder or use test support) and fast (prefer WebMvc slice unless IT is required).

## Step 5 – Evaluate Risky Defaults
- Flag overly broad `permitAll()`/`anyRequest().permitAll()` that exceed the feature scope.
- Flag blanket `csrf().disable()` / permissive CORS (`*`) without context-correct rationale.
- Check security headers relevant to the feature context; call out missing/outdated ones only if they impact the reviewed behavior.

## Step 6 – Verification & Reporting
- Run the repo’s real verification commands discovered earlier (tests + any security module tasks).
- Conclude with:
  - Security note (change, impact, residual risks, recommendations)
  - Verification steps (exact commands + required local setup)
