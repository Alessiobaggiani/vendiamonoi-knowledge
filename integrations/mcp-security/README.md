# Standard di Sicurezza MCP Server

> **Stato:** ATTIVO — Regola obbligatoria per tutte le AI e tutti i computer
> **Responsabile:** Alessio Baggiani
> **Ultimo aggiornamento:** 08/04/2026
> **Applicazione:** Ogni sessione Claude, ogni agente, ogni macchina

---

## Regola Fondamentale

**NESSUN server MCP può essere aggiunto, modificato o raccomandato senza completare l'audit di sicurezza descritto in questo documento.** Questa regola si applica a Claude, a qualsiasi AI agent, e a qualsiasi operatore umano.

---

## Checklist Pre-Installazione (OBBLIGATORIA)

Prima di aggiungere qualsiasi server MCP a `openclaw.json` o qualsiasi altra configurazione:

### 1. Verifica Ufficialità
- [ ] Il fornitore del servizio (Google, GitHub, Notion, Supabase, Microsoft, ecc.) pubblica un proprio server MCP ufficiale?
- [ ] Se sì: **usare SOLO quello ufficiale**. Fine della discussione.
- [ ] Se no: procedere con cautela ai punti successivi.

### 2. Verifica Autore
- [ ] Chi ha pubblicato il pacchetto su npm? Controllare maintainer, organizzazione GitHub, storia dei contributi.
- [ ] L'autore ha altri progetti noti e affidabili?
- [ ] Uno sviluppatore singolo sconosciuto con zero track record è un **red flag**.

### 3. Segnali di Adozione
- [ ] Download settimanali npm (< 1.000/settimana = rischio elevato)
- [ ] Stelle GitHub (< 100 = rischio elevato)
- [ ] Fork e contributori attivi
- [ ] Issues aperte e gestite

### 4. Audit Dipendenze
- [ ] Revisione `package.json` — dipendenze sospette?
- [ ] Pacchetti che non dovrebbero esserci? (es. `express` in un server stdio-only)
- [ ] Eseguire `npm audit` sul pacchetto

### 5. Revisione Codice
- [ ] Il codice invia dati a server non appartenenti al servizio?
- [ ] Presenza di codice offuscato, `eval()`, o raccolta credenziali?
- [ ] Le credenziali vengono salvate in modo sicuro? (permessi file, no log)
- [ ] L'autore ha mai pubblicato credenziali per errore nel repo?

### 6. Stato Manutenzione
- [ ] Ultimo aggiornamento? (> 6 mesi senza update = rischio)
- [ ] Il pacchetto è deprecato?
- [ ] Le issues vengono gestite?

### 7. Esposizione Credenziali
- [ ] Quali token/segreti richiede il server?
- [ ] Li conserva in modo sicuro?
- [ ] Qual è il raggio d'azione se le credenziali vengono compromesse?

---

## Matrice Decisionale

| Situazione | Azione |
|---|---|
| Esiste pacchetto ufficiale del fornitore | **Usarlo. Nessuna eccezione.** |
| No ufficiale, community noto (1000+ stelle, più maintainer) | Presentare risultati audit all'utente, decidere insieme |
| No ufficiale, autore sconosciuto/piccolo | Sconsigliare fortemente, raccomandare di aspettare alternativa ufficiale |
| Qualsiasi pacchetto con red flag di sicurezza | **NON installare. Spiegare il motivo.** |

---

## Registro Server MCP Approvati

Questi sono gli UNICI server MCP autorizzati per l'infrastruttura Vendiamonoi:

| Server | Pacchetto | Ufficiale | Stato | Note |
|---|---|---|---|---|
| filesystem | `@modelcontextprotocol/server-filesystem` | Sì (Anthropic) | APPROVATO | Scoped a directory specifiche |
| playwright | `@playwright/mcp` | Sì (Microsoft) | APPROVATO | Sostituto di `@executeautomation/playwright-mcp-server` |
| notion | `@notionhq/notion-mcp-server` | Sì (Notion) | APPROVATO | Sostituto di `@suekou/mcp-notion-server` |
| github | `@modelcontextprotocol/server-github` | Sì (Anthropic) | TEMPORANEO | Deprecato — migrare a `ghcr.io/github/github-mcp-server` quando Docker/Go disponibile |
| supabase | `@supabase/mcp-server-supabase` | Sì (Supabase) | APPROVATO | Sostituto di `supabase-mcp` (non ufficiale) |
| notebooklm | `@m4ykeldev/notebooklm-mcp` | NO — community | IN REVISIONE | Nessuna alternativa ufficiale Google. Autore con scarsa igiene sicurezza. Non riceve credenziali sensibili. |
| make | Server remoto Make.com | Sì (Make) | APPROVATO | URL diretto, nessun pacchetto npm |

---

## Pacchetti RIMOSSI (Blacklist)

Questi pacchetti sono stati rimossi e **non devono mai essere reinstallati**:

| Pacchetto | Motivo Rimozione | Data |
|---|---|---|
| `supabase-mcp` (npm: cappahccino) | NON ufficiale, autore sconosciuto, dipendenze sospette (`express`, `cors`), token Supabase esposto a codice non verificato | 08/04/2026 |
| `@suekou/mcp-notion-server` | NON ufficiale, sostituito da server Notion ufficiale `@notionhq/notion-mcp-server` | 08/04/2026 |
| `@executeautomation/playwright-mcp-server` | NON ufficiale, sostituito da server Microsoft ufficiale `@playwright/mcp` | 08/04/2026 |
| `notebooklm-mcp` (PleasePrompto) | Sostituito con `@m4ykeldev/notebooklm-mcp` per feature set più completo (32 tool vs 5) | 08/04/2026 |

---

## Procedura di Aggiunta Nuovo Server

1. **Ricerca**: cercare se esiste un server ufficiale del fornitore
2. **Audit**: completare TUTTA la checklist sopra
3. **Report**: presentare i risultati all'utente PRIMA di modificare qualsiasi configurazione
4. **Approvazione**: l'utente deve approvare esplicitamente
5. **Registrazione**: aggiungere il server a questo registro con tutti i dettagli
6. **Push**: aggiornare questo file su GitHub dopo ogni modifica

---

## Incidenti Passati

| Data | Incidente | Azione Correttiva |
|---|---|---|
| 08/04/2026 | `supabase-mcp` di autore sconosciuto installato con token Supabase — pacchetto aveva dipendenze sospette | Sostituito con ufficiale, token da ruotare |
| 08/04/2026 | Server MCP aggiunti senza audit di sicurezza | Creato questo standard obbligatorio |
| 08/04/2026 | `@suekou/mcp-notion-server` e `playwright-mcp-server` non ufficiali in uso | Sostituiti con server ufficiali dei rispettivi fornitori |

---

## TODO

- [ ] Ruotare il token Supabase (era esposto al pacchetto non ufficiale)
- [ ] Migrare server GitHub a `ghcr.io/github/github-mcp-server` (richiede Docker o Go)
- [ ] Decidere se mantenere `@m4ykeldev/notebooklm-mcp` o rimuoverlo
- [ ] Valutare se Google rilascerà un server NotebookLM ufficiale
