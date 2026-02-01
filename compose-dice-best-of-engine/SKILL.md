---
name: compose-dice-best-of-engine
description: Implement and test the Ultima Torcia best-of dice roller in Jetpack Compose. Use when adding runtime die selection, per-stat diceCount/modifier persistence, deterministic RNG, and UI feedback for rolls.
---

# Compose Dice Best Of Engine

## Overview
- Guides creation of a deterministic, testable dice system matching Ultima Torcia rules: roll N dice of a user-selected die, keep the highest, then apply flat modifiers.
- Covers persistence, UI wiring, analytics, and tests to guarantee reliable tabletop-speed interactions.

## Workflow

### 1. Understand stat requirements
1. Stats: `Agilità`, `Coraggio`, `Forza`, `Intelligenza`, `Magia`, `Manualità`, `Percezione`, `Socialità`.
2. Each stat stores `diceCount` (>=1), `flatModifier` (can be negative), optional `notes`.
3. Persist per character in Room (`StatEntity`) with `statType: StatType`, `diceCount`, `flatModifier`, `notes`.

### 2. Design the RNG boundary
1. Introduce `DiceRoller` interface with `suspend fun roll(die: Die, count: Int): RollResult`.
2. Default implementation wraps `kotlin.random.Random` seeded via `System.currentTimeMillis()`.
3. Provide `FakeDiceRoller`/`DeterministicDiceRoller` for tests, seeded list of outcomes.
4. Die definitions: enum `Die(val sides: Int)` supporting `D4`, `D6`, `D8`, `D10`, `D12`, `D20`, extendable.

### 3. Implement rolling logic
1. Validate `count >= 1`; clamp upper bound (e.g., 5) via UI.
2. For each roll: produce `IntRange(1, die.sides)`.
3. Keep `rolls: List<Int>`, compute `best = rolls.max()`, `total = best + flatModifier`.
4. Return `RollDisplay` containing `rolls`, `best`, `modifier`, `total`, `timestamp`.
5. Add `RollFormatter` utility for display strings (e.g., `"[2, 7] → best 7 + 3 = 10"`).

### 4. Compose UI wiring
1. Provide a shared `DieSelector` (segmented buttons/dropdown) to set current die in `ViewModel`.
2. Each stat row: slider/stepper for `diceCount`, number field for modifier, `notes` text area, and `Roll` button.
3. On tap: call `viewModel.roll(statType)`, update `uiState.lastRolls[statType]`.
4. Display output chip/card showing `rolls`, `best`, `modifier`, `total`; include color-coded success if GM provides threshold.
5. Use `rememberUpdatedState` plus `LaunchedEffect` to animate result flashes/vibration (optional).

### 5. Persistence + syncing
1. Store `diceCount`/`flatModifier` updates immediately via repository `updateStat(statType, count, modifier)`.
2. Keep unsaved UI input in `MutableStateFlow` and coalesce writes using `debounce(300.ms)`.
3. On `roll()`, re-read freshest values from state rather than trusting DB to avoid stale writes.

### 6. Testing guidance
1. Pure unit tests (JVM): `DiceRollerTest` verifying best-of logic for multiple dice counts, modifiers ±, deterministic sequences.
2. ViewModel tests using `TestDispatcher`, injecting `FakeDiceRoller`, verifying state emissions.
3. Snapshot Compose tests (if using Robolectric or `compose-ui-test`) for die selection and button enabling/disabling.
4. Cover boundary cases: min die (D4), high modifiers, diceCount > 1, negative modifiers, 0 or negative counts blocked.

### 7. Error handling + UX polish
1. Prevent roll action when diceCount invalid; show inline validation message.
2. If `DiceRoller` throws (e.g., RNG failure), emit snackbar with retry.
3. Provide haptic feedback on each roll using `HapticFeedbackType.LongPress` for single-tap clarity.

### 8. Telemetry (optional)
1. Log event `DiceRollPerformed` with statType, die, diceCount, modifier (no PII) for debugging (local logcat).
2. Keep logging togglable; do not introduce analytics SDKs since offline-only.

Use this skill each time dice mechanics are touched to ensure behavior stays consistent and fully tested.
