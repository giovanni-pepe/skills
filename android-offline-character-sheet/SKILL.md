---
name: android-offline-character-sheet
description: Bootstrap and evolve the Ultima Torcia Android app with Jetpack Compose + Room + MVVM to deliver offline character CRUD, sheet editing, autosave, counters/flags, and JSON import/export. Use whenever the repo lacks Android scaffolding or character-sheet workflows need updates.
---

# Android Offline Character Sheet

## Overview
- Provides a repeatable workflow for turning an empty Kotlin repo into the Ultima Torcia Toolkit Android app.
- Focuses on offline-first persistence, low-friction Compose UI, and private JSON-based sharing (no backend).

## Usage Workflow

### 1. Confirm tooling and commands
1. Inspect the repo root for `settings.gradle(.kts)` and `build.gradle(.kts)`; if missing, generate them with Android Studio Arctic Fox+ templates.
2. Prefer Gradle Kotlin DSL; include the Android Gradle Plugin, Kotlin, Jetpack Compose BOM, Room, DataStore (if needed), Coroutines, and KSP for Room.
3. Document real commands once created (`./gradlew assembleDebug`, `./gradlew test`, `./gradlew connectedAndroidTest`) using `test-command-discovery`.

### 2. Scaffold modules and configuration
1. Create a single `:app` module initially; keep package `it.ultimatorcia.toolkit` (adjust if repo dictates).
2. Enable Compose (`buildFeatures { compose = true }`, `composeOptions { kotlinCompilerExtensionVersion }`) and Kotlin serialization if JSON export/import uses it.
3. Set minSdk (e.g., 26+) and compileSdk (latest stable) to unlock Storage Access Framework (SAF) and PDF viewer APIs.

### 3. Model the data with Room
1. Define `CharacterEntity` with identity (name, raceRole, pregio), resources, counters, boolean flags, and large text sections. Use embedded/embedded prefix for grouping.
2. Represent stats as a separate `StatEntity` linked via `characterId`, storing `diceCount`, `flatModifier`, and `notes`.
3. Add `PdfDocumentEntity` and `PdfBookmarkEntity` placeholders to keep schema aligned with future skills.
4. Use `@TypeConverter` for lists or enum wrappers; consider `enum class StatType`.
5. Provide DAO interfaces plus a single `ToolkitDatabase` with `Room.databaseBuilder` configured for destructive migrations only during prototyping (replace later).

### 4. Architect the presentation layer
1. Follow MVVM: Repository (Room + JSON + SAF) → `ViewModel` → Compose UI.
2. Use `StateFlow` or `MutableStateFlow` to expose character sheets; remember to map Room entities to UI models.
3. Provide navigation destinations: `CharacterListScreen`, `CharacterDetailScreen`, `PdfLibraryScreen`.
4. Keep UI stateless where possible; hoist state to ViewModel and use `collectAsStateWithLifecycle`.

### 5. Implement character CRUD and sheet editing
1. `CharacterListScreen`: lazy column with swipe-to-delete, duplicate action, last-updated caption.
2. `CharacterDetailScreen`: sections for Identity, Resources, Counters, Flags, Equipment Notes, Stats.
3. Use `OutlinedTextField`, `NumberPicker`-style controls, `AssistChip`/`FilterChip` for flags, and `Counter` composables with +/- buttons bounded between 0 and configured max.

### 6. Autosave + offline persistence
1. Every field change triggers a `ViewModel` intent that updates in-memory state and writes to Room using `viewModelScope.launch`.
2. Buffer rapid edits with `debounce` or `snapshotFlow` to prevent thrashy writes.
3. Surface status indicator in the top app bar: `Saving…` when a job is running, `Saved` otherwise. Use `derivedStateOf` over `Job?`.
4. Show snackbar only on failures; log via `Log.e`.

### 7. Export/Import JSON
1. Define a stable DTO (`CharacterPayload`) decoupled from Room to keep JSON clean; include stats and metadata.
2. Export: `share` intent or `ACTION_CREATE_DOCUMENT` to write JSON; ensure coroutine uses `withContext(Dispatchers.IO)`.
3. Import: `ACTION_OPEN_DOCUMENT`, parse JSON, detect duplicates by name+timestamp; either prompt or auto-append suffix.
4. Serialize via Kotlinx serialization or Moshi; include tests verifying round-trip fidelity.

### 8. Validation
1. Unit tests: Repository CRUD, converters, JSON round-trip, counter bounds.
2. Instrumented tests (once emulator commands known) for autosave and navigation smoke.
3. Manual checklist before delivery: create, edit, delete, duplicate, export/import, offline restart.

## Reference Layout

```
app/
  src/main/java/it/ultimatorcia/toolkit/
    data/
      db/ToolkitDatabase.kt
      entity/CharacterEntity.kt
      dao/CharacterDao.kt
    repository/CharacterRepository.kt
    model/CharacterSheet.kt
  src/main/java/.../ui/
    list/CharacterListScreen.kt
    detail/CharacterDetailScreen.kt
    components/CounterField.kt
  src/main/java/.../viewmodel/
    CharacterListViewModel.kt
    CharacterDetailViewModel.kt
```

Use this as a guide, adapting package names to the repo if different.
