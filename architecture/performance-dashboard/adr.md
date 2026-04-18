---
tags: [adr, performance, score-engine, vendiamonoi]
source: vendiamonoi-os
created: 2026-04-17
type: architecture-decision
---

# ADR — Performance Dashboard MVP

## Score pre-calcolato + pg_cron invece di query real-time

**Contesto:** Aggregare 90 giorni di ordini su decine di coppie fornitore-marketplace ad ogni caricamento pagina sarebbe lento e costoso.

**Decisione:** Calcolo in background ogni ora via pg_cron, risultati letti dalla tabella `performance_scores` già pronti.

**Conseguenze:**
- Pro: pagine veloci, nessun carico DB al click
- Contro: dati con max ~60 min di ritardo (accettabile per analisi strategica)

---

## Tier A/B/C su soglie fisse (70/40)

**Decisione:** Soglie fisse — A ≥ 70, B ≥ 40, C < 40. Non percentili.

**Conseguenze:** Se tutti i fornitori sono forti, tutti saranno A. Soglie configurabili in futuro se necessario.

---

## Nessun pulsante manuale

**Decisione:** Rimosso "Aggiorna Score" da tutte le pagine. Al suo posto: timestamp "Aggiornato: [ora]".

**Motivazione:** Richiesta esplicita dell'utente — la dashboard deve essere automatica.
