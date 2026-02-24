---
name: django-angular-keycloak-oauth2-config-audit
description: Audit and harden Keycloak plus OAuth2/JWT configuration across the combined Django backend and Angular frontend in this repo. Use when changing auth settings, Keycloak realms/clients, token claims, webhook security, or when login/authorization behavior differs across backend, frontend, and docker environments.
---

# Django + Angular Keycloak OAuth2 Config Audit

## Goal
Detect and fix configuration drift or security risks in the repo auth chain: Keycloak -> JWT/OIDC claims -> Django authorization -> Angular route and API access.

## Workflow
1. Inventory auth sources.
- Backend settings in `backend/backend/settings.py` (`SIMPLE_JWT`, `AUTHENTICATION_BACKENDS`, Keycloak vars, claim names).
- Frontend OIDC settings in `frontend/apps/app/src/environments/environment*.ts` and auth module wiring.
- Environment-specific compose values in `docker/testing/docker-compose.yml` and `docker/staging/docker-compose.yml`.

2. Check parity and drift.
- Verify issuer, audience, JWK URL, realm, and client IDs match per environment.
- Verify group claim naming aligns between backend (`USER_GROUPS_CLAIM`) and frontend authorization checks.
- Verify backend API URL and frontend token forwarding rules are aligned.

3. Audit endpoint protection.
- Confirm default DRF permissions are appropriate.
- Inspect explicit auth exceptions, especially Keycloak webhook endpoints.
- Flag unauthenticated paths and require rationale.

4. Validate role semantics.
- Ensure Keycloak groups map to Django groups predictably.
- Verify frontend checks (`user.groups`) match backend group assignment behavior.

5. Propose minimal hardening.
- Prefer targeted config fixes and tests over broad auth rewrites.
- Add or update backend tests with DRF APIClient and mocked Keycloak integration where needed.

## Output Contract
- Provide findings ordered by severity with exact file references.
- Include concrete fix steps and verification commands.
- Explicitly list assumptions when environment secrets are not available.

## Guardrails
- Do not invent issuers, audiences, or client IDs.
- Do not assume webhook endpoints are safe because they are internal.
- Keep behavior backward compatible unless a security bug requires a break.
