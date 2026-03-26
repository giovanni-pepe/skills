# Workflow Standards

- Start with repository discovery. Read local rules, then derive the real commands from wrappers, manifests, and CI before changing code.
- Respect the repository's branch policy. If a new branch is needed and the repo has no stricter rule, use the `codex/` prefix.
- Follow Conventional Commits whenever commitlint, release automation, or existing history indicates that convention.
- Keep commits and pull requests scoped to one coherent intent. Separate behavior changes from unrelated cleanup.
- Identify the narrowest meaningful validation path before editing. After editing, run focused checks first, then broader checks if the risk justifies them.
- If the worktree is dirty, never revert unrelated edits. Integrate around them unless they directly conflict with the requested change.
- Use repository pull request templates when present. Otherwise, summarize impact, validation, risk, rollback, and security notes explicitly.
