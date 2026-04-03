---
name: backend-code-review
description: Usa quando l'utente chiede una code review backend di branch, PR, diff o file cambiati prima del merge. Analizza solo le modifiche in scope, adatta i criteri al repo corrente e restituisce findings ordinati per critical, major e minor con riferimenti puntuali ai file, coprendo regressioni lato API, contratti HTTP e DTO, validazioni, query, error handling, autorizzazione e test mancanti, senza proporre fix di default.
metadata:
  short-description: Review backend diff-scoped con findings per severita
---

# Review Codice Backend

Usa questa skill per review backend pre-merge, audit di diff o controllo di file cambiati. L'obiettivo e' trovare problemi reali e regressioni probabili nel codice introdotto, non fare refactor o review generale della codebase.

## Principi Operativi

- Parti sempre dal perimetro reale richiesto: branch contro base branch, PR diff, file specifici o solo codice aggiunto/modificato.
- Leggi prima il diff, poi solo il contesto minimo necessario per capire contratto, layering, persistenza e convenzioni del repo.
- Cerca regole locali del repo prima di applicare criteri generici di backend engineering.
- Restituisci findings prima di qualunque sintesi o overview.
- Non proporre fix di default e non estendere la review fuori dallo scope richiesto.
- Se non trovi problemi, dichiaralo esplicitamente e segnala solo rischi residui o test gap rilevanti.

## Workflow

1. Determina la base della review e limita l'analisi al codice cambiato.
2. Ispeziona il diff completo e identifica i punti che cambiano comportamento, contratto pubblico o confini applicativi.
3. Leggi il contesto locale necessario: controller o handler vicini, service, mapper, repository, DTO, test e convenzioni del repo.
4. Valuta il diff rispetto ai criteri di review backend qui sotto.
5. Riporta solo findings azionabili, ordinati per severita': `critical`, `major`, `minor`.

## Criteri Di Review

Controlla sempre, sul codice cambiato:

- regressioni di comportamento e semantica lato API
- contratti HTTP, DTO, serializer, mapper e persistenza
- validazioni mancanti o spostate nel layer sbagliato
- query non bounded, ordinamenti impliciti e paginazione incoerente
- casi null, empty, not found, conflict ed error handling
- autorizzazione e boundary di accesso toccati dal diff
- effetti collaterali non coperti da test
- backward compatibility del contratto pubblico
- maintainability locale: layering, responsabilita', duplicazione, complessita', leggibilita'

Non fare una review esaustiva di security o performance di default. Cita questi aspetti solo quando il diff introduce un rischio concreto ed evidente.

## Output Atteso

- Elenca i findings per severita': `critical`, `major`, `minor`.
- Per ogni finding indica file reference puntuale e una spiegazione breve del rischio o della regressione.
- Mantieni l'output asciutto: niente changelog, niente riassunti lunghi, niente review della codebase fuori scope.
- Se un punto dipende da un'ipotesi, dichiarala chiaramente.
- Se non ci sono findings, scrivi esplicitamente che non ne hai trovati e segnala solo eventuali gap di test o rischi residui.