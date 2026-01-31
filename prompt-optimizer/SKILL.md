---
name: prompt-optimizer
description: Transform any rough user goal or prompt into a copy/paste-ready optimized prompt with explicit persona, context, structured instructions, examples, constraints, and defaults; use whenever the user requests prompt improvements, refinements, or "make this a better prompt."
---

# Prompt Optimizer

## Overview
Convert the provided `PROMPT_BASE` (plus optional `CONTESTO` and `STILE`) into a production-ready Markdown prompt that downstream models can run without clarification. The optimized prompt must follow the required heading order and embed defaults or placeholders whenever the user omits details--no follow-up questions, no commentary outside the final prompt.

## Required Inputs
- **PROMPT_BASE (mandatory):** Raw goal or instruction from the user. Treat it as the authoritative objective.
- **CONTESTO (optional):** Extra background such as audience, platforms, constraints, or domain notes.
- **STILE (optional):** Tone/voice/language hints. When absent, fall back to defaults.
- **Defaults (hard requirements):**
  - Lingua: Italiano
  - Stile: chiaro, diretto, senza gergo inutile
  - Lunghezza: quanto serve ma preferisci sintesi strutturata
  - Fonti esterne: nessuna, salvo richiesta esplicita
  - Placeholder syntax: `<INSERISCI_[DESCRIZIONE]>` for missing specific data (nomi, metriche, date, ecc.)

## Workflow
1. **Capture inputs**
   - Copy PROMPT_BASE verbatim.
   - Append CONTESTO/STILE when provided; otherwise annotate that defaults apply.
2. **Clarify objective (internal)**
   - Rephrase PROMPT_BASE into a crisp objective plus success criteria (how the user will judge completion). Do not expose the intermediate reasoning.
3. **Assemble sections (strict order)**
   1. `# Ruolo`: Define persona, expertise, and behavior guardrails required to solve the task.
   2. `# Obiettivo`: Describe the precise deliverable, desired impact, and KPIs derived from PROMPT_BASE.
   3. `# Contesto`: Summarize domain, audience, constraints, and any CONTESTO info; inject placeholders for missing data.
   4. `# Istruzioni`: Enumerate step-by-step actions, decision rules, and reminders (output-only, no guessing, apply defaults).
   5. `# Formato di output`: Provide the exact schema (Markdown skeletons, JSON shape, table columns, bullet tokens).
   6. `# Esempi`: Supply 2-3 mini few-shot samples showing `Input sintetico -> Estratto output` aligned with the task scope; reuse placeholders when specifics are unknown.
   7. `# Vincoli`: List mandatory restrictions (lingua, banned behaviors, placeholder policy, citation rules, quality bars).
   8. `# Checklist qualita`: Provide verifications the downstream assistant must run before finalizing (section completeness, placeholder use, adherence to format, validation of defaults).
   9. `# Defaults & Placeholder`: Restate fallback assumptions (lingua, stile, fonti) and how to apply `<INSERISCI_...>` when data stays missing.
4. **Polish**
   - Ensure the entire prompt is in Italian unless STILE explicitly overrides.
   - Remove meta comments such as "here is your improved prompt."
   - Confirm each heading appears once, uses Markdown `#`, and the order never changes.

## Section Construction Guidance
- **Ruolo:** Combine persona + responsibilities + safety constraints. Example phrasing: `Agisci come prompt engineer senior specializzato in <settore>...`.
- **Istruzioni:** Use numbered steps. Include checks like "Non inventare dati; sostituisci con `<INSERISCI_...>`" and "Non fare domande aggiuntive."
- **Formato di output:** Provide literal structures (e.g., fenced code blocks, bullet outlines, JSON templates). Specify column names, key order, and any required labels.
- **Esempi:** Tailor them to PROMPT_BASE. When the domain is unclear, default to generic work artifacts (email, piano attivita, lista decisionale) but still show input vs output expectations.
- **Vincoli:** Reiterate "Output-only," language requirements, prohibition on citing unknown sources, checks on hallucinations.
- **Checklist qualita:** Minimum items: section coverage, adherence to defaults, validation of examples, format accuracy, placeholder verification.

## Quality Assurance Checklist (for Codex)
1. PROMPT_BASE intent appears explicitly inside Obiettivo, Istruzioni, and Formato.
2. All nine headings are present, in order, with substantive content.
3. Examples reflect the task domain or the documented generic fallback.
4. Defaults and placeholder guidance are explicit and actionable.
5. Final output is pure Markdown and contains only the optimized prompt.
