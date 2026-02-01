---
name: android-compose-room-scaffold
description: >-
  Bootstrap a missing Android project in this repo: create Gradle/Android scaffolding with a single :app module
  configured for Kotlin + Jetpack Compose + Room (KSP) + Coroutines, plus a minimal launchable Compose screen and
  a tiny Room sample that compiles. Use when the repo has no Android/Gradle setup yet.
---

# Android Compose + Room Scaffold

## Goal
- Create standard Android/Gradle scaffolding (root plus :app) that builds in Android Studio.
- Enable Jetpack Compose and add Room (via KSP) plus Coroutines dependencies.
- Provide a minimal app entry point (Compose screen) and minimal Room database/DAO/entity that compiles.
- Verify with Gradle commands (`./gradlew test`, `./gradlew :app:assembleDebug`).

## Operating Principles
- No guessing: non inventare comandi, path, convenzioni o configurazioni; scopri tutto dal repo o dai requisiti.
- Scope lock: limita le modifiche all'area richiesta e evita refactor speculativi.
- Repo alignment: segui le convenzioni scoperte dal profiling (naming, package, test style, wiring).
- Test-first: usa il loop TDD del repo (test -> codice -> verifica) e mantieni tutto verde.

## When to Use
- Repo senza `settings.gradle(.kts)`, `build.gradle(.kts)`, Gradle wrapper, o moduli Android.
- Repo che deve diventare un'app Android con Compose + Room offline e ha bisogno della prima impalcatura.
- CI/build che falliscono per mancanza della struttura Android.
- Non usare se esiste già un progetto Android e serve solo una feature; in quel caso usa skill specifiche.

## Workflow

### Step 1 - Discovery (facts first)
1. Controlla il root del repo per build system e struttura:
   - `settings.gradle(.kts)`, `build.gradle(.kts)`, Gradle wrapper, cartella `gradle/wrapper`
   - Moduli Android esistenti (`app/`, `android/`, ecc.)
   - Convenzioni di package/naming (es. `com.company.project`)
2. Identifica eventuali comandi di verifica (README/CI). Se non esistono, adotta:
   - `./gradlew test`
   - `./gradlew :app:assembleDebug`
3. Rileva vincoli:
   - Versioni Kotlin/AGP/Compose/Room richieste dal repo
   - Min/compile SDK indicati

**Handoff facts da registrare**
- File/moduli da toccare: root Gradle files, Gradle wrapper, modulo `:app`.
- Comandi da eseguire: `./gradlew test`, `./gradlew :app:assembleDebug`.
- Vincoli/assunzioni: se non ci sono indicazioni, usa Java 17, Compose abilitato, Room con KSP, Coroutines Android, preferendo Version Catalog (`gradle/libs.versions.toml`) salvo diversa convenzione.

### Step 2 - Scope definition (worklist)
1. Aggiungi Gradle wrapper e file root (`settings.gradle.kts`, `build.gradle.kts`, `gradle.properties`).
   - Done quando: il wrapper vede `:app`.
2. Aggiungi Version Catalog (`gradle/libs.versions.toml`) con versioni e alias.
   - Done quando: gli script compilano usando `libs.*`.
3. Crea modulo `:app` con Compose abilitato e dipendenze per Compose (BOM, Material3, activity-compose), Room (runtime/ktx + KSP compiler) e Coroutines.
   - Done quando: `assembleDebug` va a buon fine.
4. Aggiungi codice minimo:
   - `MainActivity` che mostra un `Text`
   - Room `@Database`, `@Dao`, `@Entity` di esempio
   - Done quando: KSP gira e compila.
5. Aggiungi test JVM minimo (`SmokeTest`).
   - Done quando: `./gradlew test` passa.

### Step 3 - Eseguire con loop TDD
Per ogni slice:
1. Aggiungi il test/target più piccolo che fallisce (es. wrapper mancante, modulo mancante).
2. Implementa il minimo indispensabile per farlo passare.
3. Esegui il comando più mirato (`./gradlew tasks`, `./gradlew :app:compileDebugKotlin`, poi `test` e `:app:assembleDebug`).
4. Refactor solo sui file appena aggiunti.

### Step 4 - Edge cases & risks
- Line ending Windows: assicurati che `gradlew.bat` sia incluso e con permessi corretti.
- Assenza di `JAVA_HOME` in CI: resta su Java 17 toolchain standard.
- KSP non applicato: verifica il plugin e le configurazioni di Room.
- Mismatch versioni Compose/Kotlin/AGP: centralizza nel catalogo e mantieni versioni compatibili.
- Namespace e `applicationId`: mantieni coerenza tra `android { namespace = ... }` e i package di codice.

### Step 5 - Verification & checklist
- Esegui:
  - `./gradlew test`
  - `./gradlew :app:assembleDebug`
- Checklist finale:
  - Modifiche effettuate (per slice)
  - Test aggiunti/aggiornati (nomi classi)
  - Comandi eseguiti (precisi)
  - Assunzioni rilevanti (SDK, versioni) e follow-up (aggiungere CI, lint, ecc.)

## Guardrails
- Non introdurre pattern nuovi se non richiesti.
- Non allargare la surface area con nuove API pubbliche/contratti se non necessari.
- Se tocchi configurazioni condivise, spiega l'impatto e mantienilo minimo.
