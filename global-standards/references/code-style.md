# Code Style Standards

- Optimize for readability first. Prefer code that another engineer can understand quickly without reconstructing hidden intent.
- Use names that describe domain meaning or behavior, not temporary implementation details. Avoid vague names such as `data`, `obj`, `temp`, or `manager` unless the surrounding abstraction makes them precise.
- Keep functions and methods locally coherent. If a block mixes parsing, validation, side effects, and formatting, split it only when the extracted unit gets a clear name and reduces cognitive load.
- Prefer straightforward control flow: early returns over deeply nested branches, explicit conditions over compact but opaque expressions, and linear happy paths where possible.
- Keep related logic together. Do not force readers to jump across files or helpers for trivial one-line indirections.
- Avoid over-abstraction. Introduce helpers, interfaces, or utility layers only when they remove duplication or clarify responsibilities.
- Keep comments sparse. Use them for non-obvious intent, invariants, or edge-case reasoning; do not restate what the code already says clearly.
- Remove dead code, obsolete branches, and stale TODOs when they are in the touched area and safe to clean up.
- Follow repository-native formatting and linting tools, but treat formatting compliance as the floor, not the definition of readable code.
