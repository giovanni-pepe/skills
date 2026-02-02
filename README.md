# Codex Skills - Toolkit personale per sviluppi con Codex
### Condiviso per colmare il vuoto di skill disponibili e raccogliere migliorie dalla community

Queste sono le skill che utilizzo per i miei sviluppi personali con Codex.
Ho deciso di pubblicarle perche' ho notato che in giro ce n'e' una certa assenza di skills.
Se qualcuno ha migliorie da proporre alle skills, sono ben accette. Apri una PR!

---

# Guida operativa repository

Questa guida descrive il flusso consigliato per lavorare su un repository:
- **già creato** (codice già presente), oppure
- **vuoto** (da bootstrap).

Obiettivo: rendere l’onboarding tecnico ripetibile, ridurre le scelte “a intuito” e lavorare in modo allineato alle convenzioni del repo.

---

## Indice

- [Repo già creato (codice presente)](#repo-già-creato-codice-presente)
- [Repo vuoto (bootstrap)](#repo-vuoto-bootstrap)
- [Template per le skills](#template-per-le-skills)
- [Note e buone pratiche](#note-e-buone-pratiche)

---

## Repo già creato (codice presente)

Quando il repository contiene già codice e convenzioni, seguire questi passi.

### 1) Profilazione del repo

Eseguire:

- `repo-profile-and-skill-adapter`

Questo step serve a capire:
- quali **skills esistenti** sono compatibili con il repo
- quali **skills mancano** e vanno create/adattate

**Output atteso**
- lista delle skills utilizzabili
- lista delle skills mancanti / da creare
- eventuali note su convenzioni/tooling rilevanti

---

### 2) Creazione skills mancanti (se necessarie)

Se mancano skills:
1. crearle usando il template in [Template per le skills](#template-per-le-skills)
2. committarle su `main`

---

### 3) Salvare le skills da usare per il progetto

Per evitare di rilanciare ogni volta la profilazione, salvare un file che fissi il set di skills approvate per il progetto.

Esempi di nomi file:
- `skills-project.yaml`
- `skills.lock.md`

**Contenuto minimo consigliato**
- elenco skills approvate
- note operative (es. comandi di verify, moduli principali, convenzioni importanti)

---

### 4) Pianificazione operativa

Eseguire:

- `create-plan`

e seguire la worklist risultante.

**Output atteso**
- lista azioni ordinata (vertical slices)
- acceptance check per ciascun item
- comandi di verifica da eseguire

---

## Repo vuoto (bootstrap)

Quando il repository è vuoto (o quasi), procedere così.

### 1) Bootstrap minimale

Eseguire:

- `repo-bootstrap-minimal`

fornendo i parametri nel seguente formato.

#### REPO_BOOTSTRAP_INPUTS (template)

```text
REPO_BOOTSTRAP_INPUTS
1) Language & version: <e.g., Java 21>
2) Runtime/framework: <e.g., Spring Boot | Jakarta EE | Quarkus | plain Java | ...>
3) Packaging: <jar | war | native | container-only>
4) Build tool: <Maven | Gradle | npm | pnpm | ...> ; Wrapper: <yes/no>
5) Architecture: <hexagonal | layered | modular | single-module microservice | ...>
6) Testing stack:
   - Unit: <e.g., JUnit5+Mockito | ...>
   - Integration tests: <none | yes> ; Mechanism: <Failsafe | Gradle source set | ...>
   - Testcontainers: <yes/no>
7) Formatting/lint gate:
   - Tool: <Spotless+GJF | Checkstyle | ktlint | eslint | ...>
   - Enforce in verify/check: <yes/no>
8) Modules (if multi-module):
   - <moduleName>: <role>
   - <moduleName>: <role>
   (or write: "single-module")
9) CI:
   - <none | GitHub Actions | GitLab CI | Jenkins>
   - If CI != none: gate commands: <list>
END
```

#### Esempio

```text
REPO_BOOTSTRAP_INPUTS
1) Language & version: Java 21
2) Runtime/framework: Jakarta EE
3) Packaging: war
4) Build tool: Maven ; Wrapper: no
5) Architecture: hexagonal
6) Testing stack:
   - Unit: JUnit5+Mockito
   - Integration tests: none ; Mechanism: -
   - Testcontainers: no
7) Formatting/lint gate:
   - Tool: Spotless+GJF
   - Enforce in verify/check: yes
8) Modules:
   - domain: core domain model
   - application: use cases + ports
   - rest-in-adapter: inbound HTTP (JAX-RS)
   - jpa-out-adapter: outbound persistence
   - bootstrap: WAR wiring + runtime
9) CI: none
END
```

---

### 2) Seguire la guida “Repo già creato”

Dopo il bootstrap, eseguire i passi descritti in:
- [Repo già creato (codice presente)](#repo-già-creato-codice-presente)

Motivo: anche se il repo è appena nato, serve fissare skills, convenzioni e piano operativo in modo ripetibile.

---

## Template per le skills

Le skills sono documenti operativi ripetibili. Devono essere:
- concrete e orientate a outcome verificabili
- allineate alle convenzioni del repo (naming, test style, wiring, tooling)

### Template

```yaml
---
name: <skill-name-kebab-case>
description: >-
  <One-liner: cosa fa la skill e quando usarla. Se repo-specific, esplicitalo.>
metadata:
  short-description: <Short label>
---

# <Skill Title>

## Goal
- <1–3 bullets: outcome concreti, es. “Implement X”, “Add tests for Y”, “Verify con Z commands”.>

## Operating principles
- **No guessing**: non inventare comandi, path, convenzioni o configurazioni: scoprili nel repo.
- **Scope lock**: limitare le modifiche all’area richiesta; evitare refactor speculativi.
- **Repo alignment**: seguire convenzioni scoperte dal profiling (naming, package, test style, wiring).
- **Test-first**: usare il loop TDD del repo (test → code → verify) e mantenere tutto green.

## When to use
- Use when: <trigger chiari>
- Don’t use when: <non-trigger chiari> (rimanda ad altre skills)

## Workflow

### Step 1 — Discovery (facts first)
- Individuare i punti d’intervento (moduli/package/classi/config) con search e doc esistenti.
- Identificare i comandi reali di verifica (o confermare che siano già noti).
- Catturare le convenzioni rilevanti:
  - code location/naming
  - test style/harness
  - wiring/DI (se applicabile)
  - contract/error handling (se applicabile)

**Output (handoff facts)**
- Files/modules da toccare:
- Comandi da eseguire:
- Vincoli/assunzioni:

### Step 2 — Scope definition (worklist)
- Tradurre la richiesta in una worklist piccola (3–8 item), ordinata per vertical slices.
- Per ogni item indicare l’acceptance check (cosa significa “done”).

### Step 3 — Execute via TDD loop
Per ogni slice:
1) aggiungere/aggiustare un test che fallisce e dimostra il comportamento richiesto
2) implementare il minimo per passare
3) eseguire i comandi più “stretti” possibili, poi suite più ampia se serve
4) refactor solo di ciò che hai introdotto nella slice

### Step 4 — Edge cases & risks
- Identificare 2–4 edge case rilevanti e coprirli con test o documentarli esplicitamente.
- Segnalare punti “rischiosi” (shared config/wiring) e mantenere modifiche minime.

### Step 5 — Verification & checklist
- Eseguire verify completo del repo + eventuale lint/format gate.
- Produrre checklist finale:
  - Changes made (per slice)
  - Tests added/updated (nomi classi + coverage)
  - Commands executed (esatti)
  - Note: deviazioni, follow-up, setup locale richiesto

## Guardrails
- Non introdurre nuovi pattern architetturali se non richiesto.
- Non aumentare la surface area (nuove API pubbliche/contratti) se non necessario.
- Se tocchi wiring/config condivisi, evidenzia l’impatto e mantienilo minimo.
```

---

## Note e buone pratiche

- **Profilazione prima di sviluppare**: evita di introdurre convenzioni nuove dove il repo ne ha già.
- **File di skills per progetto**: riduce attrito e rende il flusso replicabile.
- **Piano prima del coding**: `create-plan` come punto di verità (vertical slices + acceptance).
