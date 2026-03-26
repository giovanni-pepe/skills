# Repository Overrides

- Treat `CODING_RULES.md` as the repository's primary local override document.
- Read local rules before editing so repository exceptions shape the approach from the start rather than appearing late in the task.
- Repository rules override this skill whenever they are more specific or intentionally different.
- Keep repository override files concise and operational. Prefer concrete commands, boundaries, test expectations, and rollout notes over generic prose.
- When a repository needs a temporary or exceptional deviation, document it with a short note derived from `assets/technical-decision-template.md`.
- If the repository is silent on a topic, fall back to this skill and then to the codebase's existing conventions.
