---
name: frontend-code-review
description: Usa quando l'utente chiede una code review frontend di branch, PR, diff o file cambiati prima del merge. Analizza solo le modifiche in scope, adatta i criteri al repo corrente e restituisce findings ordinati per critical, major e minor con riferimenti puntuali ai file, coprendo regressioni UI, contratti tra view, state e API, edge case di rendering e test mancanti, senza proporre fix di default.
metadata:
  short-description: Review frontend diff-scoped con findings per severita
---

# Review Codice Frontend

Usa questa skill per review frontend pre-merge, audit di diff o controllo di file cambiati. L'obiettivo e' trovare problemi reali e regressioni probabili nel codice introdotto, non riscrivere il feature work o fare una review generale della codebase.

## Principi Operativi

- Parti sempre dal perimetro reale richiesto: branch contro base branch, PR diff, file specifici o solo codice aggiunto/modificato.
- Leggi prima il diff, poi solo il contesto minimo necessario per capire comportamento, convenzioni e rischio di regressione.
- Cerca regole locali del repo prima di applicare criteri generici di frontend engineering.
- Restituisci findings prima di qualunque sintesi o overview.
- Non proporre fix di default e non estendere la review fuori dallo scope richiesto.
- Se non trovi problemi, dichiaralo esplicitamente e segnala solo rischi residui o test gap rilevanti.

## Workflow

1. Determina la base della review e limita l'analisi al codice cambiato.
2. Ispeziona il diff completo e identifica i punti che cambiano comportamento, contratti o flussi utente.
3. Leggi il contesto locale necessario: componenti vicini, hook/store, client API, router, test esistenti, convenzioni del repo.
4. Valuta il diff rispetto ai criteri di review frontend qui sotto.
5. Riporta solo findings azionabili, ordinati per severita': `critical`, `major`, `minor`.

## Criteri Di Review

Controlla sempre, sul codice cambiato:

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

## Output Atteso

- Elenca i findings per severita': `critical`, `major`, `minor`.
- Per ogni finding indica file reference puntuale e una spiegazione breve del rischio o della regressione.
- Mantieni l'output asciutto: niente changelog, niente riassunti lunghi, niente review della codebase fuori scope.
- Se un punto dipende da un'ipotesi, dichiarala chiaramente.
- Se non ci sono findings, scrivi esplicitamente che non ne hai trovati e segnala solo eventuali gap di test o rischi residui.