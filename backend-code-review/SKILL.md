---
name: backend-code-review
description: Usa quando l'utente chiede una code review backend di branch, PR, diff o file cambiati prima del merge. Analizza solo le aggiunte nel branch corrente, adatta i criteri al repo corrente e usa la skill come guida di analisi, senza imporre un formato di output.
metadata:
  short-description: Review backend diff-scoped con findings per severita
---

# Review Codice Backend

Usa questa skill per review backend pre-merge, audit di diff o controllo di file cambiati. L'obiettivo e' trovare problemi reali e regressioni probabili nel codice aggiunto nel branch corrente, non fare refactor o review generale della codebase.

## Principi Operativi

- Parti sempre dal perimetro reale richiesto, ma quando la review e' sul branch corrente considera solo le righe aggiunte rispetto alla base branch.
- Leggi prima il diff delle aggiunte, poi solo il contesto minimo necessario per capire contratto, layering, persistenza e convenzioni del repo.
- Cerca regole locali del repo prima di applicare criteri generici di backend engineering.
- Usa questa skill solo per guidare l'analisi: non prescrive alcun output obbligatorio.
- Non proporre fix di default e non estendere la review fuori dallo scope richiesto.
- Non analizzare rimozioni, contesto invariato o codice fuori dalle aggiunte, salvo il minimo indispensabile per capire l'impatto delle righe nuove.

## Workflow

1. Determina la base della review del branch corrente.
2. Ispeziona solo gli hunk aggiunti (`+`) e identifica quelli che cambiano comportamento, contratto pubblico o confini applicativi.
3. Leggi il contesto locale strettamente necessario: controller o handler vicini, service, mapper, repository, DTO, test e convenzioni del repo.
4. Valuta esclusivamente le aggiunte rispetto ai criteri di review backend qui sotto.

## Criteri Di Review

Controlla sempre, sul codice aggiunto:

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

## Note

- La skill non definisce un formato di risposta obbligatorio.
- Serve solo a vincolare il perimetro e i criteri dell'analisi.
