---
name: vendiamonoi-session-conclude
description: >
  Chiusura sessione e salvataggio stato per Vendiamonoi.it. ATTIVA OBBLIGATORIAMENTE questa skill alla FINE DI OGNI SESSIONE OPERATIVA per salvare lo stato e garantire zero context loss. MANDATORY TRIGGERS: fine sessione, concludi sessione, salva stato, chiudi sessione, aggiorna status, session conclude, wrap up, salva progresso, stato sessione, conclude, standup conclude, fine lavoro, prossima sessione, context save, session end. Anche quando l'utente dice "per oggi basta", "ci vediamo", "salva tutto", "chiudiamo", "wrap up", "stop", o quando il contesto sta per esaurirsi.
---

# Vendiamonoi Session Conclude — Zero Context Loss

Responsabile di chiudere la sessione salvando TUTTO lo stato necessario per la prossima sessione.

## Quando Attivare

- L'utente dice "concludi", "chiudiamo", "basta per oggi", "salva"
- Il contesto sta per esaurirsi (>80% utilizzato)
- Un milestone importante è stato completato
- Su richiesta esplicita

## Procedura

### Step 1: Audit della Sessione

Raccogli:
- Task completati in questa sessione
- Task parziali (non finiti)
- Decisioni prese
- File creati/modificati (GitHub, Obsidian, Notion)
- Errori/lezioni apprese
- Prossimi step logici

### Step 2: Aggiorna STATUS.md su GitHub

Usa `mcp__github__push_files` per aggiornare `STATUS.md` nel repo `Alessiobaggiani/vendiamonoi-knowledge`.

**PRIMA leggi STATUS.md attuale** con `mcp__github__get_file_contents` per non perdere contenuto esistente.

#### Sezioni da aggiornare:

**1. Session Metadata (commento HTML)**
```html
<!-- session-metadata
last_updated: [YYYY-MM-DD]
last_session_focus: [descrizione-con-trattini]
open_threads: [N]
next_priority: [prossimo-step]
staleness_days: 0
-->
```

**2. Stato Attuale del Progetto** — aggiorna con cambiamenti reali

**3. Thread Aperti** — lista task non completati:
```markdown
1. **[Nome]** — [Descrizione] | Priorità: [Alta/Media/Bassa]
   - Stato: [dove si è fermato]
   - Prossimo step: [cosa fare]
```

**4. Decisioni Recenti** — aggiungi alla tabella (mantieni ultime 10)

**5. Prossimi Step Possibili** — aggiorna basandoti sulla sessione

**6. Log Sessioni Recenti** — aggiungi riga (mantieni ultime 15):
```markdown
| [OGGI] | [Focus sessione] | [Deliverable principali] |
```

### Step 3: Aggiorna PROGRESS.md (se applicabile)

Solo se completati task tracciati in `strategy/master-checklist/PROGRESS.md`.

### Step 4: Registra Errori (se applicabile)

Se emersi errori → usa skill `cowork-error-registry` o aggiorna `error-registry/`.

### Step 5: Conferma all'Utente

```
✅ Sessione salvata.
📊 Stato: [riassunto 1 riga]
📌 Thread aperti: [N]
🔜 Prossima sessione riprende da: [punto]
💾 STATUS.md aggiornato (commit: [sha])
```

## Regole

- SEMPRE aggiornare STATUS.md, anche per sessioni brevi
- SEMPRE aggiornare il metadata HTML
- MAI inventare — documenta solo ciò che è realmente accaduto
- MAI procedere con nuovi task dopo il conclude
- STATUS.md deve restare sotto 3KB
- Se push fallisce → riprova una volta, poi avvisa utente
