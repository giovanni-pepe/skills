---
name: pbi-to-user-story
description: Convert any informal Product Backlog Item draft into the mandated User Story template with Come/Voglio/Cosi da, objectives, and up to six Given/When/Then acceptance criteria; use whenever a user asks to rewrite a PBI draft or notes into a structured, verifiable user story.
---

# PBI to User Story

## Overview
Turn free-form PBIs (notes, chats, screenshot text) into a clean User Story ready for refinement. Always output only the required template sections, headings, and order defined in the specification below.

## Required Inputs
- **BOZZA_PBI (mandatory):** Raw, informal text describing the backlog need.
- **CONTESTO_DI_PROGETTO (optional):** Domain, stakeholders, constraints. Use only if provided.
- **Default behaviors:** Do not ask follow-up questions, do not invent details, keep service/system names exactly as written, and avoid introducing technologies absent from the draft.

## Workflow
1. **Parse the draft**
   - Identify actor or role, key action/need, and expected value. If multiple roles appear, select the one most tied to the main need.
   - Capture explicit constraints, validations, dependencies, KPIs, or referenced systems.
2. **Normalize the story core**
   - Write the User Story using the `Come / voglio / cosi da` framing. If any element is missing, use generic but truthful text (e.g., "Come operatore di <INSERISCI_SISTEMA>").
3. **Describe context/problem**
   - Summarize current pain and what the story unlocks in 2-6 lines. Mention legacy behavior, regulatory pressure, or customer impact when present.
4. **Set objectives**
   - List one or two measurable outcomes (e.g., "Consentire la consultazione di..."). Add the second bullet only if the draft implies an additional result.
5. **Craft Acceptance Criteria**
   - Maximum six AC. Each must follow Given/When/Then (Dato/Quando/Allora).
   - Cover happy path first, then validations, permission checks, or error handling. Reference observable results (record stored, message displayed, access denied).
   - Upgrade any existing bullet requirements into clean G/W/T form without changing intent.
6. **Populate Note operative**
   - List constraints, dependencies, monitoring needs, rollout notes, or placeholders for missing info.
   - When an assumption is necessary, add `Assunzione: ...`. If there is nothing meaningful, omit the section entirely.

## Output Template (mandatory)
Always copy this structure verbatim. Replace bracketed areas with final content; do not add, rename, or reorder sections.

```
---
## Titolo
[una riga chiara, orientata al valore]

## User Story
**Come** [ruolo/attore]
**voglio** [bisogno/azione]
**cosi da** [beneficio/valore]

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
- [vincoli, dipendenze, Assunzione: ..., rischi, esigenze di osservabilita]
---
```

## Acceptance Criteria Guidance
- Keep titles short and outcome-focused (es. "Visualizzazione dettaglio", "Validazione campi").
- Use concrete, observable states; avoid subjective adjectives.
- Mention UI labels or fields only if explicitly provided. Otherwise describe behavior generically (es. "messaggio di conferma").
- Include at least one edge or failure path whenever the draft hints at validation, permissions, or error handling.

## Naming and Fidelity Rules
- Preserve all system or service names verbatim (es. "EP-A", "EProcs-test").
- Reuse metric names, statuses, and glossary terms exactly as in the draft.
- If the draft is ambiguous, stay generic; only add assumptions when strictly required and mark them in Note operative.

## Quality Checklist
1. Every template heading appears once, in order, with meaningful content (remove Objective item 2 or Note operative only when empty).
2. Story scope matches BOZZA_PBI with no extra functionality or tech.
3. Acceptance Criteria (max six) follow Dato/Quando/Allora and cover success plus key edge cases.
4. Language remains verifiable and implementation-agnostic.
5. All assumptions are called out with `Assunzione:` in Note operative.
