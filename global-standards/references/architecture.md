# Architecture Standards

- Preserve the architecture already in use in the touched area. Prefer local consistency over introducing a new pattern.
- Keep dependency direction explicit. Do not cross boundaries just to avoid adding a mapper, adapter, or helper.
- Keep public contracts stable unless the request explicitly changes them. When a contract changes, update callers, tests, and change notes together.
- Add new modules, packages, or abstractions only when the codebase shows a recurring need that the current structure cannot absorb cleanly.
- Treat schema, configuration, API, and deployment changes as rollout-sensitive. Document forward and rollback expectations when touching them.
- Generated code, migrations, and framework-mandated structures may follow their own conventions, but their boundaries should remain clear.
