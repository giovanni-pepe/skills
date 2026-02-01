---
name: android-dice-mechanics-test
description: >-
  Add unit tests (and minimal implementation if missing) for the dice roll mechanics used in the Android app:
  runtime die selection, multiple rolls, keep-best (best-of), and flat modifiers. Use when implementing or
  validating the roll logic for character stats.
---

# Android Dice Mechanics Test

## Goal
- Add/adjust JVM unit tests that specify and validate dice mechanics:
  - runtime die selection (`diceSides`)
  - `diceCount` rolls
  - keep-best (best-of)
  - `flatModifier` applied after best selection
- Keep tests deterministic (inject RNG) and runnable via Gradle (`./gradlew :app:testDebugUnitTest` or repo equivalent).

## Operating Principles
- No guessing: scopri percorsi, comandi e convenzioni dal repo.
- Scope lock: modifica solo roll logic + test; niente refactor gratuiti.
- Repo alignment: segui packages, naming e stile test esistenti.
- Test-first: loop TDD (test -> codice -> verifica) mantenendo il build verde.

## When to Use
- Il repo ha già un modulo Android e serve definire/completare il roll logic per i personaggi.
- Esistono bug o ambiguità su best-of e modifier.
- Servono test deterministici per evitare RNG flaky.
- Non usare se manca ancora lo scaffolding Android (usa `android-compose-room-scaffold` prima) o se lavori solo sulla UI.

## Workflow

### Step 1 — Discovery (facts first)
1. Trova dove vive il roll logic:
   - cerca `roll`, `dice`, `Random`, `RollResult`, `Stat`.
   - annota modulo (`:app`, `:core`, ecc.) e package.
2. Esamina il test harness:
   - JUnit4 vs JUnit5, kotest, mockk, ecc.
   - struttura `src/test/java|kotlin`.
3. Verifica i comandi gradle:
   - `./gradlew :app:testDebugUnitTest` oppure il comando reale documentato (non inventare).

**Handoff facts da registrare**
- File/moduli da toccare: sorgenti roll logic + nuovi/aggiornati test.
- Comandi da eseguire: comando test reale (di solito `./gradlew :app:testDebugUnitTest`, eventualmente `./gradlew test`).
- Vincoli/assunzioni: `diceCount` tiri, tieni il massimo, applica `flatModifier`, `diceSides` è input runtime, RNG iniettabile.

### Step 2 — Scope definition (worklist)
1. Definisci una funzione/classe pura `rollBestOf(...)` con RNG iniettabile (se manca).
   - Done quando il codice produttivo accetta un RNG e può essere testato.
2. Scrivi test per best-of e modifier.
   - Done quando i test descrivono scenari tipici (es. 3d6 keep best + modifier) e falliscono inizialmente.
3. Copri edge case (diceCount bounds, negative modifier, invalid diceSides/diceCount).
   - Documenta se lanci eccezioni o clampi.
4. Esegui i comandi Gradle richiesti e assicurati che passino.

### Step 3 — Loop TDD
1. Scrivi un test mirato che fallisce (es. best-of con risultati noti da RNG fake).
2. Implementa il minimo codice per passare (preferisci funzioni pure senza dipendenze Android).
3. Esegui il comando più stretto (`./gradlew :app:testDebugUnitTest --tests ...` se possibile, poi completo).
4. Refactor limitato al nuovo codice (niente sorprese su UI/NAV).

### Step 4 — Edge cases & risks
- Edge cases da coprire (scegline ≥2):
  - `diceCount = 1` → ritorna l’unico tiro.
  - `diceCount > 1` → best è il max di tutti i tiri.
  - `flatModifier` negativo/positivo.
  - Input invalidi:
    - `diceSides <= 1` o `diceCount <= 0` (decidi: `IllegalArgumentException` vs clamp).
    - Limita `diceCount` massimo (es. 5) se definito dal dominio.
- Rischi:
  - Uso di `Random.Default` rende i test flaky → sempre iniettare RNG.
  - Mescolare logica con UI rende difficile testare → mantieni logica in package domain/data.

### Step 5 — Verification & checklist
- Comandi:
  - `./gradlew :app:testDebugUnitTest`
  - opzionale `./gradlew test` se richiesto dal repo.
- Checklist finale:
  - Cambiamenti per slice (logica/test).
  - Test aggiunti/aggiornati (nomi classi + scenari).
  - Comandi eseguiti (precisi).
  - Note su scelte invalid input / RNG injection / follow-up (es. collegare alla UI).

## Guardrails
- Non introdurre pattern nuovi o API pubbliche inutili.
- Mantieni la logica roll separata dal layer Android.
- Se tocchi wiring condiviso (DI, ViewModel), descrivi l’impatto e falli minimi.
