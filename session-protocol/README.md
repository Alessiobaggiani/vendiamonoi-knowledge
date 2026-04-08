# Vendiamonoi Session Protocol v2.0

> Protocollo operativo per sessioni Claude efficienti, zero context loss, zero sprechi di token.

## Principi Fondamentali

### 1. Ogni sessione parte da zero — ma non deve ripartire da zero

Claude non ha memoria tra sessioni. Il protocollo compensa con:
- **STATUS.md** — stato persistente del progetto (aggiornato ogni fine sessione)
- **Knowledge base GitHub** — cervello tecnico permanente
- **Skills** — conoscenza codificata che si carica automaticamente
- **Session logs** — storia delle sessioni precedenti

### 2. Token = denaro — ogni token deve produrre valore

Pattern anti-spreco:
- **Context loader intelligente** — carica SOLO i file rilevanti per il task
- **Subagent per ricerca** — investigazioni in contesto isolato
- **Compact a milestone** — non aspettare il limite, comprimi ai punti naturali
- **/clear tra task diversi** — non accumulare contesto irrilevante
- **CLI > MCP** quando possibile (meno token overhead)

### 3. Mai duplicare lavoro

Prima di QUALSIASI ricerca o creazione contenuto:
1. Controlla cosa esiste già su GitHub
2. Controlla cosa esiste già su Notion
3. Solo se non esiste → procedi

## Workflow Sessione

### FASE 1 — STANDUP (primi 30 secondi)

Attivata automaticamente dalla skill `vendiamonoi-context-loader`.

```
1. Leggi STATUS.md da GitHub
   → Conosci lo stato del progetto
   → Sai cosa è stato fatto nell'ultima sessione
   → Sai quali thread sono aperti
   → Sai quali sono i prossimi step

2. Analizza la richiesta dell'utente
   → Determina il dominio del task
   → Identifica i file di contesto necessari

3. Carica contesto selettivo (dalla matrice context-loader)
   → Solo i file rilevanti per il task corrente
   → Mai caricare tutto il repo

4. Conferma stato + contesto all'utente
   → "Stato: [ultimo aggiornamento]. Contesto caricato: [files]. Procedo con [task]."
```

### FASE 2 — LAVORO (90% della sessione)

Durante il lavoro operativo:

- **Prima di creare contenuto** → verifica che non esista già
- **Usa subagent per ricerche** → mantieni il contesto principale pulito
- **Compact dopo ogni milestone importante** → `/compact Focus su [area]`
- **Documenta decisioni importanti** → saranno salvate nel conclude
- **Usa TodoWrite** → traccia progresso visibile all'utente

### FASE 3 — CONCLUDE (ultimi 2 minuti)

Attivata manualmente o automaticamente a fine sessione.

```
1. Aggiorna STATUS.md su GitHub
   → Stato attuale del progetto
   → Thread aperti (task non completati)
   → Decisioni prese in questa sessione
   → Prossimi step raccomandati
   → Log della sessione

2. Aggiorna session-metadata nel commento HTML
   → last_updated
   → last_session_focus
   → open_threads count
   → next_priority
   → staleness_days

3. Se necessario, aggiorna PROGRESS.md
   → Solo se ci sono stati avanzamenti tracciabili

4. Conferma all'utente
   → "Sessione salvata. Stato: [riassunto]. Prossima sessione può riprendere da [punto]."
```

## Matrice Contesto (Context Loader)

Il context loader usa una matrice decisionale per caricare SOLO i file rilevanti:

| Dominio Task | File Caricati | Token Stimati |
|-------------|---------------|---------------|
| Sempre | STATUS.md | ~500 |
| Automazioni Make | architecture/make/* | ~15K |
| Automazioni n8n | architecture/n8n/* | ~15K |
| Marketplace specifico | marketplace-specs/<nome>/* | ~5-10K |
| Database/Supabase | architecture/supabase/* | ~8K |
| Architettura app | app-design/* | ~5K |
| Pricing | pricing-strategy/* | ~8K |
| Customer service | customer-service/* | ~6K |
| Ricerca comparativa | research/* | ~5K |

**Budget target:** <25K token di contesto caricato per sessione.

## Anti-Pattern da Evitare

### 1. Kitchen Sink Session
**Problema:** Task diversi nella stessa sessione senza `/clear`.
**Soluzione:** `/clear` tra task non correlati.

### 2. Ricerca Duplicata
**Problema:** Ricercare/creare contenuto che esiste già.
**Soluzione:** SEMPRE controllare GitHub prima di iniziare.

### 3. Context Overflow
**Problema:** Leggere troppi file nella sessione principale.
**Soluzione:** Usare subagent per investigazioni, solo i risultati tornano nel contesto.

### 4. CLAUDE.md Bloat
**Problema:** CLAUDE.md troppo lungo (>200 righe), Claude ignora le regole.
**Soluzione:** Tenere CLAUDE.md sotto 200 righe, spostare dettagli in skills.

### 5. Sessione Infinita
**Problema:** Sessione unica per tutto senza mai compattare.
**Soluzione:** Compact a ogni milestone, /clear tra task diversi.

### 6. Agente Autonomo Non Controllato
**Problema:** Agenti di ricerca che creano file non richiesti o modificano tracker.
**Soluzione:** Istruzioni esplicite su cosa possono e non possono fare.

## Session Log Format

Ogni sessione viene loggata in STATUS.md nella tabella "Log Sessioni Recenti":

```markdown
| Data | Focus | Output Principale |
|------|-------|-------------------|
| YYYY-MM-DD | [Descrizione breve del focus] | [Deliverable principali] |
```

Per sessioni particolarmente importanti, creare un file dedicato in:
```
session-protocol/logs/YYYY-MM-DD-<descrizione>.md
```

## Scheduled Tasks

Task automatici configurabili per lavoro autonomo:

| Task | Frequenza | Descrizione |
|------|-----------|-------------|
| Changelog Monitor | Settimanale | Controlla aggiornamenti API/changelog dei software nello stack |
| KB Staleness Check | Settimanale | Verifica file nella knowledge base più vecchi di 90 giorni |
| Marketplace Updates | Mensile | Controlla nuove policy/requisiti dei marketplace attivi |

## Metriche di Efficienza

Per valutare se il protocollo funziona:

- **Tempo al primo output utile:** <2 minuti (standup incluso)
- **Token spesi in contesto:** <25K per sessione
- **Duplicazioni:** 0 (nessun contenuto ricreato)
- **Context loss tra sessioni:** 0 (STATUS.md sempre aggiornato)
- **Thread persi:** 0 (tutti tracciati in STATUS.md)
