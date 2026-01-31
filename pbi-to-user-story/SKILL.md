---
name: pbi-to-user-story
description: >-
  Convert any informal Product Backlog Item (PBI) draft into the mandated Italian User Story
  template with Come/Voglio/Così da, Contesto/Problema, 1–2 obiettivi, and up to six
  Dato/Quando/Allora acceptance criteria. Use whenever a user asks to rewrite a PBI draft
  or notes into a structured, verifiable user story—without asking follow-up questions and
  without inventing missing details.
metadata:
  short-description: Convert PBI draft to User Story template
  version: "1.1"
---


# PBI to User Story

## Overview

Turn free-form PBIs (notes, chats, screenshot text) into a clean User Story ready for refinement.
Always output **only** the required template sections, headings, and order defined in the specification below.

## Required Inputs

* **BOZZA_PBI (mandatory):** Raw, informal text describing the backlog need.
* **CONTESTO_DI_PROGETTO (optional):** Domain, stakeholders, constraints. Use only if provided.

## Default behaviors (mandatory)

* Do **not** ask follow-up questions.
* Do **not** invent details or technologies not present in the draft.
* Keep system/service names, status names, and glossary terms **exactly** as written.
* Stay implementation-agnostic (describe behavior, not solution design).
* Avoid placeholders like `<INSERISCI_...>` in the final output. If information is missing, stay generic and record the gap in **Note operative** as `Dato mancante:` (or `Assunzione:` only if strictly necessary).

## Workflow

1. **Parse the draft**

   * Identify: actor/role, key need/action, expected value.
   * Extract any explicit constraints, validations, permissions, dependencies, KPIs, referenced systems, and domain terms.
   * If multiple roles appear, select the one most tied to the main need.
   * If the draft contains **multiple independent requests**, select the primary one as the story scope and list the others in **Note operative** as `Possibili PBIs separati:`.

2. **Normalize the story core**

   * Write the User Story using `Come / voglio / così da`.
   * If actor/need/value is missing:

     * Use **generic but truthful** wording (e.g., “Come operatore”, “Come utente autorizzato”).
     * Record missing specifics under **Note operative** as `Dato mancante: ...`.
   * Do not expand scope beyond what the draft states.

3. **Describe Contesto / Problema**

   * Summarize current pain and what the story unlocks in **2–6 lines**.
   * Mention legacy behavior, regulatory pressure, SLA/KPI impact, or customer impact **only if present** in the inputs.
   * If the draft contains only a request without background, describe the minimal observable problem (e.g., “Attualmente l’operazione richiede passaggi manuali…”), staying generic and faithful.

4. **Set objectives (Obiettivo del PBI)**

   * List **1** objective by default.
   * Add objective **2** only if the draft clearly implies a second distinct outcome (not a rephrasing).
   * Objectives must be outcome-oriented and verifiable (verb + object + intended result), without adding new features.

5. **Craft Acceptance Criteria (AC)**

   * Write **max 6** ACs.
   * Each AC must follow **Dato / Quando / Allora**.
   * Order: happy path first, then validations/permissions/errors **only if implied** by the draft.
   * Use observable outcomes (record created/updated, state changed, message shown, access denied).
   * Mention UI labels/fields only if explicitly provided; otherwise describe behavior generically (e.g., “messaggio di conferma”).
   * If the draft includes non-GWT bullets, convert them into GWT **without changing intent**.

6. **Populate Note operative (optional)**

   * Include only meaningful items such as:

     * constraints, dependencies, rollout needs, monitoring/observability hints, referenced systems.
     * `Dato mancante:` for missing required info that forced generic wording.
     * `Assunzione:` only when necessary to make ACs coherent and testable.
     * `Possibili PBIs separati:` for extra requests found in the draft.
   * If nothing meaningful exists, **omit the entire Note operative section**.

## Output Template (mandatory)

Always copy this structure verbatim. Replace bracketed areas with final content; do not add, rename, or reorder sections.

```
---
## Titolo
[una riga chiara, orientata al valore]

## User Story
**Come** [ruolo/attore]
**voglio** [bisogno/azione]
**così da** [beneficio/valore]

## Contesto / Problema
[2-6 righe descrittive]

## Obiettivo del PBI
1. [azione o risultato 1]
2. [azione o risultato 2]   <-- rimuovi se non serve

## Criteri di Accettazione (AC)
**AC1 -- [titolo breve]**
- Dato [precondizione]
- Quando [evento]
- Allora [risultato osservabile]

**AC2 -- [titolo breve]**
- Dato ...
- Quando ...
- Allora ...

(continua fino a un massimo di AC6)

## Note operative (opzionali)
- [vincoli, dipendenze, Dato mancante: ..., Assunzione: ..., rischi, esigenze di osservabilità, Possibili PBIs separati: ...]
---
```

## Acceptance Criteria Guidance

* Keep AC titles short and outcome-focused (es. “Visualizzazione dettaglio”, “Validazione input”, “Controllo permessi”).
* Use concrete, observable states; avoid subjective adjectives (“veloce”, “intuitivo”, “user-friendly”).
* Do not add failure/edge cases unless the draft hints at validations, permissions, or error handling.
* Prefer domain terms exactly as written in BOZZA_PBI (statuses, codes, metrics, labels).

## Naming and Fidelity Rules

* Preserve all system or service names verbatim (es. “EP-A”, “EProcs-test”).
* Reuse metric names, statuses, and glossary terms exactly as in the draft.
* If the draft is ambiguous, stay generic; record ambiguity as `Dato mancante:` rather than guessing.
* Never introduce tech or architecture decisions absent from the draft.

## Quality Checklist

1. Every template heading appears once, in order, with meaningful content (remove Objective item 2 or Note operative only when empty).
2. Story scope matches BOZZA_PBI with no extra functionality, roles, or technologies.
3. Acceptance Criteria (max six) follow Dato/Quando/Allora and cover success plus only the implied edge cases.
4. Language remains verifiable and implementation-agnostic.
5. All missing info is explicitly called out as `Dato mancante:`; assumptions are rare and labeled `Assunzione:`.
6. No placeholders like `<INSERISCI_...>` appear in the final output.

---
