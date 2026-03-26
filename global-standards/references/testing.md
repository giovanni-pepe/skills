# Testing Standards

- Every behavior change should add or update the closest targeted test unless the repository genuinely lacks a viable test path.
- Prefer repository-native commands over guessed tool invocations. If the repo exposes a wrapper script, use it as the canonical command.
- Match validation depth to risk: unit or focused tests for local logic, integration or end-to-end coverage for wiring, auth, persistence, or messaging changes.
- For bug fixes, add a regression check whenever the harness can express the failure.
- If tests cannot be added or run, state the reason explicitly and describe the next best validation that was performed.
- Treat no-test repositories as a signal to be more careful, not as permission to skip validation discussion.
