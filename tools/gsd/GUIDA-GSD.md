# Guida GSD per Vendiamonoi — Cosa Fare in Ogni Situazione

> Questa guida ti dice esattamente quale comando GSD usare in base a COSA vuoi fare.
> Non devi ricordare nulla — consulta questa guida e copia il comando.

---

## DOVE SEI ADESSO

**Progetto attivo:** Gestione Fornitori & Evasione Ordini Automatizzata
**Milestone:** v1 — Evasione Ordini Automatizzata
**Fasi totali:** 6 (0 completate)
**Phase 1:** Fondamenta DB + Config Fornitori → 3 piani creati, da eseguire
**Prossimo comando:** `/gsd-execute-phase 1`

---

## SEZIONE 1 — COMANDI PER LAVORARE (uso quotidiano)

### "Voglio vedere a che punto sono"
```
/gsd-progress
```
Ti mostra barra progresso, percentuale completamento, cosa è stato fatto, cosa fare dopo. Usalo come prima cosa quando riprendi il lavoro.

### "Voglio riprendere il lavoro dopo una pausa"
```
/gsd-resume-work
```
Carica il contesto dell'ultima sessione e ti dice dove eri rimasto. Alternativa: `/gsd-progress` fa praticamente lo stesso.

### "Devo eseguire la fase pianificata"
```
/gsd-execute-phase 1
```
Esegue tutti i piani della fase (in parallelo se possibile). Cambia il numero per la fase che vuoi eseguire. Se una fase ha più wave:
```
/gsd-execute-phase 1 --wave 1
```
Esegue solo la prima wave e si ferma.

### "Ho finito una fase, voglio verificare che funziona tutto"
```
/gsd-verify-work 1
```
Test di accettazione conversazionale. Ti mostra cosa dovrebbe funzionare e ti chiede se funziona davvero. Se trova problemi, crea automaticamente un piano di fix.

### "Devo fare un lavoretto veloce senza tutto il processo"
```
/gsd-quick
```
Per task piccoli ma non banali (30 min - 2 ore). Crea un mini-piano e lo esegue. Non tocca la roadmap principale. Per task ancora più piccoli:
```
/gsd-fast "aggiungi colonna X alla tabella Y"
```
Zero pianificazione, esegue e basta. Max 3 file modificati.

### "Devo mettere in pausa il lavoro"
```
/gsd-pause-work
```
Salva il contesto esatto di dove sei. Quando riprendi, `/gsd-resume-work` lo ripristina.

---

## SEZIONE 2 — COMANDI PER PIANIFICARE

### "Devo pianificare una fase prima di eseguirla"
```
/gsd-plan-phase 2
```
Crea i piani dettagliati (PLAN.md) per la fase indicata. Fa ricerca, validazione, e genera task eseguibili. Prerequisito: la fase deve esistere nella ROADMAP.

### "Voglio discutere come dovrebbe funzionare una fase prima di pianificarla"
```
/gsd-discuss-phase 2
```
Conversazione guidata dove spieghi la tua visione. Crea CONTEXT.md con le tue preferenze. Opzionale ma utile quando hai idee specifiche su UX o comportamento. Variante batch (2-5 domande alla volta):
```
/gsd-discuss-phase 2 --batch
```

### "Voglio fare ricerca tecnica approfondita su una fase"
```
/gsd-research-phase 3
```
Lancia 4 agenti ricercatori in parallelo. Utile per fasi complesse o domini nuovi (es. Puppeteer per portali web, Sendcloud API, Recharts per grafici).

### "Voglio vedere cosa Claude intende fare PRIMA che parta"
```
/gsd-list-phase-assumptions 3
```
Claude ti mostra il suo piano senza creare file. Puoi correggerlo prima che inizi.

---

## SEZIONE 3 — GESTIONE PROGETTO

### "Voglio aggiungere una nuova fase alla roadmap"
```
/gsd-add-phase "Importazione automatica catalogo fornitori"
```
Aggiunge una Phase 7 alla fine della roadmap.

### "Devo inserire un lavoro urgente tra due fasi esistenti"
```
/gsd-insert-phase 2 "Fix critico vincolo database"
```
Crea Phase 2.1 tra Phase 2 e Phase 3. Per lavori urgenti che non possono aspettare.

### "Una fase futura non serve più, voglio rimuoverla"
```
/gsd-remove-phase 6
```
Cancella la fase e rinumera quelle successive. Funziona solo su fasi non ancora iniziate.

---

## SEZIONE 4 — MILESTONE (versioni del progetto)

### "Ho completato tutte le 6 fasi, voglio chiudere v1 e iniziare v2"
```
/gsd-complete-milestone 1.0.0
```
Archivia tutto (roadmap, requirements, fasi) in `milestones/v1.0/`. Crea un tag git. Poi:
```
/gsd-new-milestone "v2.0 — Portale Fornitori + Multi-warehouse"
```
Stesso processo di `/gsd-new-project` ma per la versione successiva. Genera nuova roadmap e requirements.

### "Dopo aver chiuso il milestone, voglio ripartire con numeri fase da 1"
```
/gsd-new-milestone --reset-phase-numbers "v2.0 Features"
```

### "Ho chiuso il milestone ma ci sono dei gap"
```
/gsd-audit-milestone
```
Verifica se tutti i requisiti sono coperti e funzionanti. Genera MILESTONE-AUDIT.md con eventuali gap. Se trova problemi:
```
/gsd-plan-milestone-gaps
```
Crea automaticamente le fasi mancanti per colmare i gap.

---

## SEZIONE 5 — WORKSPACE (lavoro parallelo)

### "Devo lavorare su due cose contemporaneamente senza che si confondano"

Ogni workspace ha la sua cartella `.planning/` indipendente ma condivide lo stesso codice. Utile quando hai più aree di lavoro attive (backend API, frontend, integrazioni n8n) e vuoi che non si inquinino a vicenda.

---

## SEZIONE 6 — UTILITÀ

### "Ho un'idea, non voglio perderla"
```
/gsd-note "aggiungere webhook Sendcloud per tracking real-time"
```
Per vederle tutte: `/gsd-note list`
Per promuovere a todo: `/gsd-note promote 3`

### "Ho trovato un task da fare ma non adesso"
```
/gsd-add-todo "Configurare webhook bounce Resend"
```
Per rivedere: `/gsd-check-todos`

### "Sto debuggando un problema"
```
/gsd-debug "descrizione problema"
```
Sopravvive a `/clear`. Senza argomenti riprende sessione attiva.

### "Devo fare un PR pulito senza file .planning/"
```
/gsd-ship 1
```
Per branch pulito: `/gsd-pr-branch main`

### "Voglio che altri AI reviewino il mio piano"
```
/gsd-review --phase 1 --all
```
Poi incorpori feedback: `/gsd-plan-phase 1 --reviews`

### "Non so quale comando usare"
```
/gsd-do "quello che vuoi fare in parole tue"
```

### "Voglio cambiare i modelli AI usati da GSD"
```
/gsd-set-profile quality    # Opus ovunque (massima qualità)
/gsd-set-profile balanced   # Opus planning, Sonnet execution (default)
/gsd-set-profile budget     # Sonnet writing, Haiku ricerca (economico)
```

---

## WORKFLOW TIPO PER VENDIAMONOI

### Workflow giornaliero standard
1. `/gsd-progress` → vedi dove sei
2. `/gsd-execute-phase N` → esegui la fase corrente
3. `/gsd-verify-work N` → verifica che funziona
4. `/gsd-plan-phase N+1` → pianifica la prossima (in nuova chat)

### Quando trovi un bug durante lo sviluppo
1. `/gsd-debug "descrizione problema"`
2. (debug, fix)
3. `/gsd-debug` → riprendi se hai fatto /clear

### Quando hai un'idea per dopo
- `/gsd-note "l'idea"` → salva veloce
- `/gsd-add-todo "task specifico"` → salva come todo
- `/gsd-plant-seed "idea per v2"` → salva con trigger per milestone futuro

### Quando devi fare qualcosa di piccolo e urgente
- `/gsd-fast "cosa banale"` → 1-3 file, zero overhead
- `/gsd-quick` → task medio, mini-pianificazione
- `/gsd-quick --full` → task medio ma vuoi massima qualità

### Quando finisci tutto il milestone v1
1. `/gsd-audit-uat` → verifica tutto
2. `/gsd-audit-milestone` → controlla copertura requisiti
3. `/gsd-plan-milestone-gaps` → colma eventuali gap
4. `/gsd-complete-milestone 1.0.0` → archivia e tagga
5. `/gsd-new-milestone "v2.0"` → inizia v2

---

## MAPPA DECISIONALE RAPIDA

| Vuoi... | Comando |
|---------|--------|
| Vedere lo stato | `/gsd-progress` |
| Riprendere lavoro | `/gsd-resume-work` |
| Eseguire fase | `/gsd-execute-phase N` |
| Pianificare fase | `/gsd-plan-phase N` |
| Discutere fase | `/gsd-discuss-phase N` |
| Ricercare fase | `/gsd-research-phase N` |
| Verificare fase | `/gsd-verify-work N` |
| Task piccolo | `/gsd-quick` |
| Task banale | `/gsd-fast "desc"` |
| Aggiungere fase | `/gsd-add-phase "desc"` |
| Fase urgente | `/gsd-insert-phase N "desc"` |
| Debuggare | `/gsd-debug "problema"` |
| Salvare idea | `/gsd-note "idea"` |
| Salvare todo | `/gsd-add-todo "task"` |
| Creare PR | `/gsd-ship N` |
| Review AI | `/gsd-review --phase N --all` |
| Chiudere milestone | `/gsd-complete-milestone X.Y.Z` |
| Nuovo milestone | `/gsd-new-milestone "nome"` |
| Non sai che fare | `/gsd-do "parole tue"` |
| Cambiare modelli | `/gsd-set-profile balanced` |
| Configurare | `/gsd-settings` |
| Aggiornare GSD | `/gsd-update` |

---

*Guida creata: 2026-04-16*
*Progetto: Vendiamonoi OS — Gestione Fornitori & Evasione Ordini*
