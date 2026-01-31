---
name: cxf-ws-security-config-audit
description: Audit and harden outbound SOAP CXF WS-Security/JAAS configuration in hexagonal Maven multi-module repos. Use when modifying CXF clients, interceptors, or config loading to inventory codegen, auth chain, required runtime config, risks, and focused JUnit 5 tests.
---

# CXF WS-Security Config Audit

## Goal
Produce a repo-grounded security/config assessment for outbound SOAP:
- Client + codegen inventory
- Auth mechanism map (WS-Security, JAAS/Kerberos, callbacks/interceptors)
- Config requirements (properties, keystores, JAAS/login config, filesystem paths)
- Risks and hardening recommendations
- Tests and verification (JUnit 5 focused tests + commands)

## Operating Mode
- Read-only by default; if changes are requested, apply via java-hexagonal-tdd-loop (tests first).
- No guessing; derive behavior from actual poms, generated sources usage, interceptors, and configuration classes.
- Respect CODING_RULES.md boundaries (outbound adapter contains CXF client concerns).

## Step 1 - Discover SOAP client structure and code generation
- Locate CXF WSDL codegen configuration in module poms (cxf-codegen-plugin, wsdl paths).
- Identify generated packages/classes and how they are referenced.
- Locate outbound adapter entry points where CXF clients are built and invoked.

## Step 2 - Map the security chain (WS-Security + JAAS/Kerberos)
- Identify interceptors/handlers:
  - WSS4J in/out interceptors
  - JAAS/Kerberos interceptors or callbacks
  - custom CallbackHandler/PasswordCallback/WSS4J props
- Record:
  - where interceptors are registered (client factory, bus, binding)
  - ordering dependencies
  - credentials used (principal, key alias, token type)

## Step 3 - Configuration requirements
- Inspect config loading (e.g., application.config.dir and files it expects).
- Enumerate required runtime artifacts:
  - properties files and required keys
  - keystore/truststore paths and passwords (do not print secrets)
  - JAAS login config locations/options
  - system properties or env vars required
- Check deployment descriptors affecting security:
  - web.xml and jboss-deployment-structure.xml
  - Dockerfile/compose for mounted config or JVM options

## Step 4 - Risk review
Flag issues with evidence and minimal mitigations:
- Hidden prerequisites
- Weak defaults
- Secret handling
- Crypto settings issues (only if detectable)
- Error handling misclassification (auth vs transient)
- Observability gaps

## Step 5 - Make config failures explicit
If changes are requested, recommend and implement via TDD:
- Validate required config keys/files early with actionable messages.
- Centralize required properties list.
- Distinguish misconfiguration vs auth vs transient errors.

## Step 6 - Tests (JUnit 5, focused)
- Use java-hexagonal-tdd-loop if implementing changes.
- Prefer lightweight tests:
  - config loader tests with temp directories/mocked inputs
  - interceptor unit tests verifying request/context props
  - classification tests for error types
- Avoid real Kerberos dependencies unless repo has a harness.

## Step 7 - Report and verification checklist
Provide:
- Codegen: plugin + wsdl locations + generated packages
- Client wiring: where CXF client and interceptors are set up
- Required runtime config: files/keys/system properties (no secrets)
- Risks found: P0/P1/P2 with evidence
- Tests added/updated: module + class names
- Commands executed: verified Maven + Spotless
- Manual verification steps (if any)

## Guardrails
- Do not change security mechanisms unless requested.
- Do not log or echo secrets.
- Keep changes scoped to outbound adapter config.
- If messaging retry exists, ensure auth/misconfig errors are non-retryable unless designed otherwise.
