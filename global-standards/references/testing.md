# Testing Standards

- Default to TDD. Every behavior change should start with the closest targeted failing automated test unless the task is pure analysis/documentation or the repository genuinely lacks a viable test path.
- If the current harness is weak but improvable at reasonable cost, strengthen the harness first instead of skipping the failing-test step.
- Prefer repository-native commands over guessed tool invocations. If the repo exposes a wrapper script, use it as the canonical command.
- Match validation depth to risk: unit or focused tests for local logic, integration or end-to-end coverage for wiring, auth, persistence, or messaging changes.
- For bug fixes, add a regression check whenever the harness can express the failure.
- If tests cannot be added or run, state the reason explicitly and describe the next best validation that was performed.
- Treat no-test repositories as a signal to be more careful, not as permission to skip validation discussion.
