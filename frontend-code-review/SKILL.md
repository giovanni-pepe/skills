---
name: frontend-code-review
description: Usa quando l'utente chiede una code review frontend di branch, PR, diff o file cambiati prima del merge. Analizza solo le aggiunte nel branch corrente, adatta i criteri al repo corrente e usa la skill come guida di analisi, senza imporre un formato di output.
metadata:
  short-description: Review frontend diff-scoped con findings per severita
---

# Review Codice Frontend

Usa questa skill per review frontend pre-merge, audit di diff o controllo di file cambiati. L'obiettivo e' trovare problemi reali e regressioni probabili nel codice aggiunto nel branch corrente, non riscrivere il feature work o fare una review generale della codebase.

## Principi Operativi

- Parti sempre dal perimetro reale richiesto, ma quando la review e' sul branch corrente considera solo le righe aggiunte rispetto alla base branch.
- Leggi prima il diff delle aggiunte, poi solo il contesto minimo necessario per capire comportamento, convenzioni e rischio di regressione.
- Cerca regole locali del repo prima di applicare criteri generici di frontend engineering.
- Usa questa skill solo per guidare l'analisi: non prescrive alcun output obbligatorio.
- Non proporre fix di default e non estendere la review fuori dallo scope richiesto.
- Non analizzare rimozioni, contesto invariato o codice fuori dalle aggiunte, salvo il minimo indispensabile per capire l'impatto delle righe nuove.

## Workflow

1. Determina la base della review del branch corrente.
2. Ispeziona solo gli hunk aggiunti (`+`) e identifica quelli che cambiano comportamento, contratti o flussi utente.
3. Leggi il contesto locale strettamente necessario: componenti vicini, hook/store, client API, router, test esistenti, convenzioni del repo.
4. Valuta esclusivamente le aggiunte rispetto ai criteri di review frontend qui sotto.

## Criteri Di Review

Controlla sempre, sul codice aggiunto:

- regressioni comportamentali visibili all'utente
- incoerenze con pattern UI gia' presenti nel repo
- contratti tra UI, state management e API consumate
- gestione di loading, empty, error e disabled states
- edge case di rendering e branch condizionali
- accesso a dati null, undefined o stale
- rischi di race condition, stato incoerente o aggiornamenti non sincronizzati
- impatto su routing, guard, query params, deep link e navigazione
- test mancanti, indeboliti o non allineati al comportamento introdotto
- maintainability locale: naming, branching, duplicazione, coupling, leggibilita'

Non fare una review esaustiva di accessibilita', performance o security di default. Cita questi aspetti solo quando il diff introduce un rischio concreto ed evidente.

## Note

- La skill non definisce un formato di risposta obbligatorio.
- Serve solo a vincolare il perimetro e i criteri dell'analisi.
