---
name: angular-oidc-guard-hardening
description: Audit and harden frontend-only OIDC authentication and route authorization flows in this Angular repo. Use when login redirects, token refresh, route guards, or group-based access checks behave incorrectly.
---

# Angular OIDC Guard Hardening

## Goal
Improve reliability and security of Angular auth flows without changing backend contracts.

## Workflow
1. Audit environment auth config.
- Check issuer and clientId in `apps/app/src/environments/environment*.ts`.
- Confirm apiUrl matches the intended resource server base URL.

2. Audit auth wiring.
- Review `CoreAuthenticationModule.forRoot` and OAuth module settings.
- Verify storage and token forwarding behavior are intentional.

3. Audit guards and interceptor behavior.
- Review authentication guards for redirect loops and fallback behavior.
- Review 401 handling and refresh logic in HTTP interceptor.
- Validate session termination and logout handling.

4. Audit authorization checks.
- Verify group-based checks map to expected `user.groups` claims.
- Ensure route-level authorization decisions match product intent.

5. Harden minimally.
- Make the smallest change that resolves incorrect or risky behavior.
- Keep backward compatibility unless a security risk requires stricter behavior.

6. Verify.
- `cd frontend && npm run ci:lint`
- `cd frontend && npm run ci:format:check`
- `cd frontend && npm run ci:build`

## Guardrails
- Do not edit backend JWT contracts in this skill.
- Do not weaken guard behavior to silence navigation errors.
- Do not add broad exception paths for protected routes.
