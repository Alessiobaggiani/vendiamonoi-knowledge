# Supabase Update — Aprile 2026

**Data:** 17 aprile 2026
**Fonte:** Newsletter ufficiale Supabase (email a direzione@vendiamonoi.it)
**Rilevanza Vendiamonoi:** Alta — Supabase è il database centrale del gestionale Vendiamonoi OS

---

## 1. Multigres Operator — Open Source

**Cosa è:** Kubernetes operator per gestire cluster PostgreSQL distribuiti e shardati su più zone/regioni.

**Funzionalità principali:**
- Gestione diretta dei pod (no StatefulSets) per controllo granulare
- Rolling upgrades a zero-downtime con awareness dei primary
- Backup pgBackRest con Point-In-Time Recovery (PITR)
- Tracing distribuito via OpenTelemetry (OTel)
- Sharding automatizzato con TableGroups e Shards come risorse first-class
- Gestione certificati integrata e webhook di validazione
- Metriche Prometheus, dashboard Grafana, logging strutturato

**Architettura:** Modello parent/child — l'utente gestisce solo il MultigresCluster root e i template, l'operator crea e reconcilia automaticamente le risorse figlie (Cells, TableGroups, Shards, TopoServers).

**Limitazioni attuali:**
- Un solo database per cluster (chiamato `postgres`)
- Shards devono essere named `0-inf`
- Pod e cell sono append-only (non rimuovibili)

**Repo GitHub:** https://github.com/multigres/multigres-operator

**Impatto Vendiamonoi:** Non immediatamente rilevante per il nostro setup attuale (Supabase hosted), ma importante per il futuro se decidessimo di fare self-hosting o scaling orizzontale del database. Tiene aperta l'opzione di migrare a infrastruttura Kubernetes propria senza vendor lock-in.

---

## 2. GitHub Integration su tutti i piani (incluso Free)

**Cosa è:** L'integrazione GitHub che permette CI/CD deployment delle migrazioni è ora disponibile su TUTTI i piani, incluso il Free tier.

**Come funziona:**
- Connetti il tuo repo GitHub al progetto Supabase
- Le migrazioni vengono deployate automaticamente dal branch main via CI/CD
- Non richiede branching (feature dei piani superiori)
- Funziona anche sul piano gratuito

**Impatto Vendiamonoi: ALTO**
- Il nostro repo `vendiamonoi-os` può ora essere collegato direttamente a Supabase
- Le migrazioni SQL pushate su GitHub vengono applicate automaticamente
- Elimina il passaggio manuale di applicare migrazioni via Dashboard o CLI
- Migliora il workflow dev → staging → production
- **Azione consigliata:** Configurare l'integrazione GitHub per il progetto `smozehzbhoeceioqxodx`

**Docs:** https://supabase.com/docs/guides/deployment/branching/github-integration

---

## 3. Supabase Docs via SSH

**Cosa è:** Server SSH pubblico (`supabase.sh`) che espone tutta la documentazione Supabase come filesystem virtuale di file markdown.

**Come funziona:**
- Comando: `ssh supabase.sh` per navigare i docs con `grep`, `find`, `ls`, `cat`
- Comando setup per Claude Code: `ssh supabase.sh setup | claude`
- I comandi girano dentro `just-bash` (shell emulata sandboxed in Node.js)
- I docs sono montati come file markdown in un VFS (Virtual File System)

**Benefici per AI agents:**
- Gli agenti AI (Claude Code, Cursor) possono consultare la documentazione ufficiale senza allucinare API inesistenti
- Stessa interfaccia (shell) che usano per esplorare il codice
- Si può aggiungere uno snippet all'`AGENTS.md` per istruire l'agente a consultare i docs prima di lavorare con Supabase

**Impatto Vendiamonoi: MEDIO-ALTO**
- Migliora drasticamente la qualità del codice generato da Claude Code/Cursor per Supabase
- **Azione consigliata:** Aggiungere il setup snippet al CLAUDE.md del repo vendiamonoi-os

**Blog:** https://supabase.com/blog/supabase-docs-over-ssh
**Repo:** https://github.com/supabase-community/supabase-ssh

---

## 4. Security Newsletter

**Cosa è:** Newsletter dedicata esclusivamente ad aggiornamenti di sicurezza di Supabase, inviata solo quando ci sono update importanti.

**Impatto Vendiamonoi: MEDIO**
- Essenziale per essere informati su vulnerabilità che potrebbero impattare il nostro database
- **Azione consigliata:** Iscrivere direzione@vendiamonoi.it alla security newsletter

**Signup:** https://supabase.com/security

---

## 5. Miglioramenti Supabase Studio

### 5a. "Fix with Assistant" (Claude/ChatGPT)

Bottoni "Fix with Assistant" aggiunti in vari punti di Studio con dropdown per scegliere tra Claude e ChatGPT. Quando incontri un errore (migrazione fallita, query con errore, policy RLS non valida), puoi inviare il contesto direttamente a un AI assistant per suggerimenti di fix.

**Impatto Vendiamonoi: MEDIO** — Velocizza il debug di query SQL e policy RLS direttamente da Studio.

### 5b. Browser Tabs migliorati

Le tab del browser ora mostrano il path di navigazione esatto, permettendo di distinguere tab diverse a colpo d'occhio (es. "Table Editor > suppliers" vs "Table Editor > orders").

**Impatto Vendiamonoi: BASSO** — Quality of life improvement per chi lavora con molte tab Supabase aperte.

### 5c. Schema Visualiser migliorato

- Le linee di relazione tra tabelle sono ora cliccabili
- Tabelle e colonne hanno azioni contestuali (context menu)
- Popover informativi appaiono tra tabelle collegate

**Impatto Vendiamonoi: MEDIO** — Utile per visualizzare e navigare lo schema del database Vendiamonoi OS (26 tabelle con relazioni complesse).

---

## 6. Push Protection per Secret Keys su GitHub

**Cosa è:** Le secret key di Supabase (service_role key, API keys) sono ora protette da GitHub Push Protection. Se provi a committare accidentalmente una chiave Supabase, GitHub blocca il push PRIMA che arrivi al repo.

**Come funziona:**
- GitHub ha aggiunto i pattern di Supabase (personal access tokens, secret keys) ai suoi secret scanner
- 39 tipi di token hanno ora push protection attiva di default
- Il push viene bloccato prima di raggiungere il repository

**Perché è critico:** Una service_role key di Supabase esposta permetterebbe di leggere ogni riga di ogni tabella del database, bypassando completamente le RLS policies.

**Impatto Vendiamonoi: ALTO**
- Protezione automatica contro esposizione accidentale delle chiavi del database di produzione
- **Azione consigliata:** Verificare che push protection sia attiva sui repo `vendiamonoi-os` e `vendiamonoi-knowledge`

---

## 7. State of Startups 2026 Survey

Supabase sta conducendo il sondaggio "State of Startups 2026" — 10 minuti su stack, AI adoption, e cosa si sta costruendo. T-shirt gratuita alla pubblicazione dei risultati.

**Impatto Vendiamonoi:** Nessuno diretto, ma il report finale potrebbe contenere benchmark utili su stack e AI adoption nel nostro settore.

---

## Riepilogo Azioni per Vendiamonoi

| Priorità | Azione | Effort |
|----------|--------|--------|
| **ALTA** | Configurare GitHub Integration per il progetto Supabase | 30 min |
| **ALTA** | Verificare Push Protection attiva sui repo GitHub | 10 min |
| **MEDIA** | Aggiungere SSH docs snippet al CLAUDE.md di vendiamonoi-os | 5 min |
| **MEDIA** | Iscrivere direzione@ alla Supabase Security Newsletter | 2 min |
| **BASSA** | Esplorare Schema Visualiser aggiornato per mappare le 26 tabelle | 20 min |
| **FUTURA** | Valutare Multigres se si considera self-hosting | — |

---

## Eventi rilevanti

- **13 aprile 2026:** Sugu Sougoumarane — Multigres at CMU "PostgreSQL vs. The World" (Zoom, gratuito)
- **23 aprile 2026:** Multigres talk a Postgres Conference, San Jose (codice sconto: `2026_SUPABASE20` per -20%)
- **29-30 aprile 2026:** Stripe Sessions, San Francisco
- **30 aprile 2026:** Workshop "Debug Your Full Supabase Stack with Sentry" (10am PT)
