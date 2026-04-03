# Settore 8 — Automazione (Cron, Webhooks, Gmail)

> Guida completa ai sistemi di automazione di OpenClaw: cron jobs per task schedulati, webhooks per trigger esterni, integrazione email/Gmail, heartbeat vs cron, e architetture ibride con n8n/Make.

---

## Indice

1. [Panoramica Automazione](#1-panoramica-automazione)
2. [Cron Jobs — Task Schedulati](#2-cron-jobs--task-schedulati)
3. [Cron vs Heartbeat](#3-cron-vs-heartbeat)
4. [Comandi CLI Cron](#4-comandi-cli-cron)
5. [Job Types: Main Session vs Isolated](#5-job-types-main-session-vs-isolated)
6. [Webhooks — Trigger Esterni](#6-webhooks--trigger-esterni)
7. [Configurazione Webhooks](#7-configurazione-webhooks)
8. [Email & Gmail Integration](#8-email--gmail-integration)
9. [Gmail Watch API — Real-time](#9-gmail-watch-api--real-time)
10. [Architettura Ibrida: OpenClaw + n8n/Make](#10-architettura-ibrida-openclaw--n8nmake)
11. [Pattern di Automazione](#11-pattern-di-automazione)
12. [Sicurezza Automazione](#12-sicurezza-automazione)
13. [Configurazione Vendiamonoi](#13-configurazione-vendiamonoi)
14. [Fonti & Riferimenti](#14-fonti--riferimenti)

---

## 1. Panoramica Automazione

OpenClaw offre tre sistemi di automazione complementari:

| Sistema | Trigger | Direzione | Uso principale |
|---|---|---|---|
| **Cron Jobs** | Tempo (schedule) | Interno → Azione | Task ricorrenti programmati |
| **Webhooks** | Evento esterno (HTTP) | Esterno → OpenClaw | Reazione a eventi real-time |
| **Email/Gmail** | Email in arrivo | Esterno → OpenClaw | Processamento email automatico |
| **Heartbeat** | Intervallo fisso | Interno → Check | Monitoraggio continuo |

### Come si complementano

```
Cron Jobs         →  "Ogni giorno alle 9:00 controlla nuovi ordini"
Webhooks          →  "Quando Shopify invia un ordine, processalo subito"
Gmail Watch       →  "Quando arriva email dal fornitore, estrai i dati"
Heartbeat         →  "Ogni 30 min controlla se c'è qualcosa di urgente"
```

---

## 2. Cron Jobs — Task Schedulati

### Come funzionano

I cron jobs girano dentro il **processo Gateway** (non dentro il modello). I job sono persistenti: salvati in `~/.openclaw/cron/jobs.json`, sopravvivono ai restart.

### Tre modi di schedulare

| Metodo | Sintassi | Esempio | Uso |
|---|---|---|---|
| `--at` | Data/ora specifica | `--at "2026-04-04T09:00"` | One-time, task singolo |
| `--every` | Intervallo (ms) | `--every 3600000` | Ogni N millisecondi |
| `--cron` | Espressione cron | `--cron "0 9 * * 1-5"` | Scheduling avanzato (standard cron) |

### Esempi pratici

```bash
# Report ordini ogni mattina alle 9 (lun-ven)
openclaw cron add \
  --name "daily-orders-report" \
  --cron "0 9 * * 1-5" \
  --message "Genera il report ordini di ieri. Controlla tutti i marketplace."

# Sync inventario ogni 2 ore
openclaw cron add \
  --name "inventory-sync" \
  --every 7200000 \
  --message "Sincronizza inventario con i fornitori. Segnala discrepanze."

# Task one-time domani alle 14
openclaw cron add \
  --name "supplier-followup" \
  --at "2026-04-04T14:00" \
  --message "Invia follow-up al fornitore XYZ per l'ordine #1234."

# Check prezzi competitor ogni giorno alle 7
openclaw cron add \
  --name "price-check" \
  --cron "0 7 * * *" \
  --session isolated \
  --message "Controlla prezzi competitor su Amazon per i top 50 SKU."
```

### Anti-spike automatico

Le espressioni ricorrenti "top-of-hour" vengono automaticamente sfalsate di ±5 minuti per evitare picchi di carico.

- `--exact` — forza il timing preciso
- `--stagger 30s` — finestra di sfalsamento personalizzata

### Retry con backoff esponenziale

In caso di errori consecutivi:

```
1° errore → retry dopo 30s
2° errore → retry dopo 1m
3° errore → retry dopo 5m
4° errore → retry dopo 15m
5°+ errore → retry dopo 60m
```

Il backoff si resetta automaticamente dopo un run di successo.

---

## 3. Cron vs Heartbeat

### La differenza fondamentale

| Aspetto | Cron | Heartbeat |
|---|---|---|
| **Funzione** | Avvia un lavoro specifico | Controlla se c'è qualcosa da fare |
| **Trigger** | Schedule programmato | Intervallo fisso (default 30 min) |
| **Contesto** | Può essere isolato o in sessione | Sempre in sessione principale |
| **Costo LLM** | Solo quando esegue | Ogni intervallo ($0-300/mese, vedi Settore 4) |
| **Configurazione** | CLI `openclaw cron add` | `HEARTBEAT.md` nel workspace |

### Come interagiscono

```
Cron Job (--wake now)      → Sveglia l'heartbeat immediatamente
Cron Job (--wake next)     → Inietta evento, heartbeat lo processa al prossimo giro
Cron Job (--session isolated) → Esegue indipendentemente dall'heartbeat
```

### Quando usare cosa

| Scenario | Usa | Perché |
|---|---|---|
| Report giornaliero | Cron | Orario preciso, task definito |
| "C'è qualcosa di urgente?" | Heartbeat | Monitoraggio continuo |
| Sync inventario ogni 2h | Cron | Task specifico, schedulato |
| Processare email in arrivo | Webhook + Heartbeat | Evento esterno + processing |
| Task one-time | Cron `--at` | Esecuzione singola |

---

## 4. Comandi CLI Cron

### Gestione completa

```bash
# Aggiungere un job
openclaw cron add --name "job-name" --cron "0 9 * * *" --message "Prompt"

# Elencare tutti i job
openclaw cron list
# Output: ID, nome, schedule, stato, ultimo run, prossimo run

# Dettagli di un job
openclaw cron show <jobId>

# Modificare un job
openclaw cron edit <jobId> --message "Nuovo prompt" --model sonnet

# Eseguire manualmente (force run)
openclaw cron run <jobId>

# Vedere storico esecuzioni
openclaw cron runs --id <jobId> --limit 50

# Abilitare/disabilitare temporaneamente
openclaw cron enable <jobId>
openclaw cron disable <jobId>

# Rimuovere un job permanentemente
openclaw cron remove <jobId>
```

### Opzioni avanzate per `cron add`

| Flag | Funzione | Default |
|---|---|---|
| `--name` | Nome identificativo | Obbligatorio |
| `--cron` / `--every` / `--at` | Schedule | Obbligatorio (uno dei tre) |
| `--message` | Prompt da eseguire | Obbligatorio |
| `--session` | `main` o `isolated` | `main` |
| `--model` | Modello da usare | Default agente |
| `--wake` | `now`, `next-heartbeat`, `none` | `now` |
| `--exact` | Timing preciso (no stagger) | `false` |
| `--stagger` | Finestra sfalsamento | `5m` |
| `--enabled` | Attivo alla creazione | `true` |

### Disabilitare il sistema cron

```yaml
# In config
cron:
  enabled: false

# Oppure via variabile d'ambiente
OPENCLAW_SKIP_CRON=1
```

---

## 5. Job Types: Main Session vs Isolated

### Main Session Jobs

- Iniettano un **system event** nella sessione principale dell'agente
- L'agente ha **pieno contesto conversazionale**: sa cosa stavi facendo, cosa è successo prima
- Ideale per: reminders, follow-up, task che richiedono contesto

```bash
# Esempio: reminder con contesto
openclaw cron add \
  --name "daily-standup" \
  --cron "0 9 * * 1-5" \
  --session main \
  --message "È ora dello standup. Riassumi cosa abbiamo fatto ieri e cosa c'è da fare oggi."
```

### Isolated Jobs

- Girano in una **sessione dedicata** con slate pulita
- Nessun contesto precedente, nessuna conversazione accumulata
- L'agente riceve solo il prompt e lavora
- Ideale per: task indipendenti, scraping, report, sync

```bash
# Esempio: task indipendente
openclaw cron add \
  --name "competitor-scraping" \
  --cron "0 7 * * *" \
  --session isolated \
  --message "Scraping prezzi competitor per top 50 SKU su Amazon.de. Salva risultati in /data/prices/."
```

### Tabella confronto

| Aspetto | Main Session | Isolated |
|---|---|---|
| Contesto | Pieno (conversazione, memory) | Solo prompt |
| Costo token | Più alto (contesto accumulato) | Più basso (clean slate) |
| Velocità | Può essere lento (contesto grande) | Veloce |
| Conflitti | Possibili con conversazione attiva | Nessuno |
| Uso ideale | Reminder, follow-up, summary | Scraping, sync, report isolati |

---

## 6. Webhooks — Trigger Esterni

### Come funzionano

Il Gateway di OpenClaw espone un **endpoint HTTP** che riceve eventi da servizi esterni. Quando un evento arriva, viene iniettato nella sessione dell'agente.

```
Servizio esterno  →  HTTP POST  →  OpenClaw Gateway  →  Agente
(Shopify, n8n,       /hooks            (routing)         (processing)
 Zapier, custom)
```

### Payload webhook

```json
{
  "text": "Nuovo ordine #12345 da Amazon.de: 3x SKU-ABC, totale €89.90",
  "mode": "now"
}
```

| Campo | Obbligatorio | Funzione | Default |
|---|---|---|---|
| `text` | Sì | Descrizione dell'evento | — |
| `mode` | No | `now` (heartbeat immediato) o `next` (prossimo heartbeat) | `now` |

### Session key

Ogni webhook ha una session key nel formato `hook:<uuid>` che identifica la connessione e permette il routing verso la sessione corretta.

---

## 7. Configurazione Webhooks

### Abilitare webhooks

```yaml
# ~/.openclaw/config/hooks.yaml
hooks:
  enabled: true
  token: "wh_your-secure-token-here"  # Obbligatorio
  path: /hooks                         # Default
  port: 18790                           # Porta dedicata (opzionale)
```

### Autenticazione

Ogni richiesta deve includere il token via header:

```bash
# Metodo raccomandato
curl -X POST https://openclaw.vendiamonoi.it/hooks \
  -H "Authorization: Bearer wh_your-secure-token-here" \
  -H "Content-Type: application/json" \
  -d '{"text": "Nuovo ordine #12345 da Amazon"}'

# Alternativa
curl -X POST https://openclaw.vendiamonoi.it/hooks \
  -H "x-openclaw-token: wh_your-secure-token-here" \
  -H "Content-Type: application/json" \
  -d '{"text": "Nuovo ordine #12345 da Amazon"}'
```

### Sicurezza webhook

| Regola | Perché |
|---|---|
| Token dedicato (non riusare gateway token) | Blast radius limitato |
| Hook agent con tools.profile ristretto | Least privilege |
| HTTPS obbligatorio | Protezione dati in transito |
| Rate limiting | Prevenire abuse |
| Validazione payload | Prevenire injection |

---

## 8. Email & Gmail Integration

### Tre metodi di integrazione

| Metodo | Latenza | Complessità | Costo | Consigliato per |
|---|---|---|---|---|
| **IMAP/SMTP polling** | 1-5 min | Bassa | $0 | Setup semplice, piccoli volumi |
| **Gmail REST API** | 10-30 sec | Media | $0 | Accesso strutturato, label, thread |
| **Gmail Watch API** | < 5 sec | Alta | $0 | Real-time, grandi volumi |

### Metodo 1: IMAP/SMTP (skill built-in)

OpenClaw ha skill ufficiali per IMAP/SMTP:

```bash
# Installare skill email
openclaw skills install imap-smtp-email
```

**Configurazione:**

```yaml
# Variabili d'ambiente
IMAP_HOST: imap.gmail.com
IMAP_PORT: 993
IMAP_USER: orders@vendiamonoi.it
IMAP_PASSWORD: app-password-here   # Google App Password
SMTP_HOST: smtp.gmail.com
SMTP_PORT: 587
SMTP_USER: orders@vendiamonoi.it
SMTP_PASSWORD: app-password-here
```

**Polling via cron:**

```bash
openclaw cron add \
  --name "email-check" \
  --every 300000 \
  --message "Controlla nuove email da fornitori. Estrai conferme ordine e tracking."
```

### Metodo 2: Gmail REST API

```yaml
# Variabili d'ambiente
GMAIL_CLIENT_ID: xxx.apps.googleusercontent.com
GMAIL_CLIENT_SECRET: GOCSPX-xxx
GMAIL_REFRESH_TOKEN: xxx
```

Permette accesso strutturato a thread, label, metadata e query avanzate.

### Metodo 3: Gmail Watch API (vedi sezione successiva)

---

## 9. Gmail Watch API — Real-time

### Architettura

```
Email arriva → Gmail Watch API → Google Pub/Sub → VPS webhook → OpenClaw hook → AI processing
```

### Setup step-by-step

1. **Google Cloud Console**: Abilitare Gmail API e Pub/Sub API
2. **Creare topic Pub/Sub**: `projects/vendiamonoi/topics/gmail-notifications`
3. **Creare subscription**: Push verso `https://openclaw.vendiamonoi.it/hooks`
4. **Registrare watch**:

```javascript
// Registrare watch su Gmail
const response = await gmail.users.watch({
  userId: 'me',
  requestBody: {
    topicName: 'projects/vendiamonoi/topics/gmail-notifications',
    labelIds: ['INBOX'],
    labelFilterBehavior: 'INCLUDE'
  }
});
// Watch scade dopo 7 giorni → rinnovare via cron
```

5. **Cron per rinnovare watch** (scade ogni 7 giorni):

```bash
openclaw cron add \
  --name "gmail-watch-renew" \
  --cron "0 0 */6 * *" \
  --session isolated \
  --message "Rinnova Gmail Watch API subscription."
```

### Flusso completo per Vendiamonoi

```
1. Email dal fornitore → Gmail
2. Gmail Watch → Pub/Sub → webhook OpenClaw
3. OpenClaw legge email via Gmail API
4. Estrae: conferma ordine, tracking, documento
5. Aggiorna database (Supabase via MCP)
6. Aggiorna marketplace (ChannelEngine)
7. Notifica su Telegram/Slack
```

---

## 10. Architettura Ibrida: OpenClaw + n8n/Make

### Il concetto

**OpenClaw è il cervello** (ragiona, decide, scrive). **n8n/Make sono le mani** (connettono servizi, muovono dati, eseguono workflow deterministici).

### Quando usare cosa

| Task | OpenClaw | n8n/Make |
|---|---|---|
| Decidere cosa fare con un ordine | ✅ | ❌ |
| Spostare dati da A a B | ❌ | ✅ |
| Scrivere risposta cliente personalizzata | ✅ | ❌ |
| Webhook → trasformazione → API call | ❌ | ✅ |
| Analizzare email fornitore e estrarre dati | ✅ | ❌ |
| Trigger sequenziale di 10 API | ❌ | ✅ |

### Pattern ibrido consigliato

```
n8n/Make (Orchestratore)          OpenClaw (Cervello)
         │                              │
         ├── Ricevi webhook Shopify ──────┤
         │   (nuovo ordine)               │
         │                                ├── Analizza ordine
         │                                ├── Verifica disponibilità
         │                                ├── Decide azione
         │                                └── Ritorna decisione
         │                              │
         ├── Esegui decisione ───────────┤
         │   (conferma, spedizione,       │
         │    cancellazione)              │
         │                              │
         ├── Aggiorna marketplace ────────┤
         ├── Invia notifica ──────────────┤
         └── Logga risultato ─────────────┘
```

### Come connettere

**n8n → OpenClaw** (via webhook):

```bash
# n8n HTTP Request node
Method: POST
URL: https://openclaw.vendiamonoi.it/hooks
Headers:
  Authorization: Bearer wh_token
Body:
  {"text": "Nuovo ordine Shopify #12345: 2x SKU-ABC, €45.90, spedizione Italia"}
```

**OpenClaw → n8n** (via webhook n8n):

```bash
# Dall'agente OpenClaw, via tool web_request
curl -X POST https://n8n.vendiamonoi.it/webhook/order-processed \
  -H "Content-Type: application/json" \
  -d '{"orderId": "12345", "action": "confirm", "supplier": "ABC"}'
```

### Make.com integration

Stessa logica di n8n:
- Make scenario con webhook → chiama OpenClaw per analisi AI
- OpenClaw cron/webhook → chiama Make scenario per esecuzione workflow

---

## 11. Pattern di Automazione

### Pattern 1: Morning Briefing

```bash
openclaw cron add \
  --name "morning-briefing" \
  --cron "0 8 * * 1-5" \
  --session main \
  --message "Buongiorno! Prepara il briefing mattutino:\n\
1. Nuovi ordini da ieri sera\n\
2. Ordini in attesa di tracking\n\
3. Email fornitori non risposte\n\
4. Alert marketplace (performance, listing rimossi)\n\
5. Azioni urgenti per oggi"
```

### Pattern 2: Order Processing Pipeline

```
Webhook (nuovo ordine)
  → OpenClaw analizza ordine
  → Verifica fornitore in memory/fornitori.md
  → Controlla disponibilità
  → Se disponibile: conferma + richiedi spedizione
  → Se non disponibile: cerca alternativa + notifica
  → Logga tutto in memory/ordini.md
```

### Pattern 3: Supplier Follow-up Automatico

```bash
openclaw cron add \
  --name "supplier-followup" \
  --cron "0 10 * * *" \
  --session isolated \
  --message "Controlla ordini inviati ai fornitori >48h fa senza conferma. \
Invia email di follow-up educata. Logga in memory/followup.md."
```

### Pattern 4: Price Monitoring

```bash
openclaw cron add \
  --name "price-monitor" \
  --cron "0 7,19 * * *" \
  --session isolated \
  --message "Monitora prezzi dei top 50 SKU su Amazon.de e eBay.de. \
Confronta con i nostri prezzi. Segnala variazioni >10%. Salva report in /data/prices/."
```

### Pattern 5: Email Processing Automatico

```bash
openclaw cron add \
  --name "email-processor" \
  --every 300000 \
  --message "Controlla nuove email da fornitori. Per ogni email:\n\
- Se conferma ordine: estrai numero ordine e aggiorna database\n\
- Se tracking: estrai tracking number e aggiorna marketplace\n\
- Se problema: notifica su Telegram\n\
- Se fattura: salva in /docs/fatture/"
```

### Pattern 6: Listing Health Check

```bash
openclaw cron add \
  --name "listing-health" \
  --cron "0 6 * * 1" \
  --session isolated \
  --message "Audit settimanale listing:\n\
1. Listing sospesi o rimossi\n\
2. Listing con stock 0\n\
3. Listing con prezzo anomalo\n\
4. Listing senza immagini\n\
5. Nuove categorie disponibili\n\
Genera report e invia su Telegram."
```

---

## 12. Sicurezza Automazione

### Rischi specifici

| Rischio | Descrizione | Mitigazione |
|---|---|---|
| **Runaway cron** | Job che gira all'infinito | Timeout per job, monitoring |
| **Token exposure** | Webhook token in log/codice | Env vars, rotazione periodica |
| **Injection via webhook** | Payload malevolo | Validazione input, sanitization |
| **Cost explosion** | Cron troppo frequente con modelli costosi | Budget alerts, modello economico per cron |
| **Email parsing** | Phishing/malware via email | Sandbox processing, no attachment exec |

### Checklist sicurezza automazione

```yaml
# 8 regole
1. Token webhook dedicato e ruotato mensilmente
2. HTTPS obbligatorio per tutti gli endpoint
3. Rate limiting su webhook endpoint
4. Timeout per ogni cron job
5. Modello economico per cron isolati (Haiku/Flash, non Opus)
6. Monitoring costi LLM per automazione
7. Log centralizzati per audit
8. Sandbox per email processing (mai eseguire allegati)
```

### Costi automazione

| Pattern | Frequenza | Modello | Costo stimato/mese |
|---|---|---|---|
| Morning briefing | 1x/giorno | Sonnet | ~$3-5 |
| Email check | Ogni 5 min | Haiku | ~$5-10 |
| Price monitoring | 2x/giorno | Haiku | ~$2-4 |
| Supplier follow-up | 1x/giorno | Sonnet | ~$1-2 |
| Listing health | 1x/settimana | Sonnet | ~$0.50 |
| **Heartbeat (base)** | Ogni 30 min | Haiku | ~$5-15 |
| **Totale automazione** | | | **~$15-35/mese** |

---

## 13. Configurazione Vendiamonoi

### Cron jobs raccomandati

```bash
# === GIORNALIERI ===

# Morning briefing (lun-ven 8:00)
openclaw cron add --name "morning-briefing" \
  --cron "0 8 * * 1-5" --session main \
  --message "Briefing mattutino: nuovi ordini, tracking pending, email fornitori, alert marketplace."

# Check email fornitori (ogni 5 min)
openclaw cron add --name "email-check" \
  --every 300000 --model haiku \
  --message "Controlla email fornitori. Estrai conferme ordine e tracking."

# Supplier follow-up (ogni giorno 10:00)
openclaw cron add --name "supplier-followup" \
  --cron "0 10 * * 1-5" --session isolated \
  --message "Follow-up ordini fornitori senza conferma >48h."

# === BISETTIMANALI ===

# Price monitoring (7:00 e 19:00)
openclaw cron add --name "price-monitor" \
  --cron "0 7,19 * * *" --session isolated --model haiku \
  --message "Monitora prezzi top 50 SKU su Amazon.de e eBay.de."

# === SETTIMANALI ===

# Listing health (lunedì 6:00)
openclaw cron add --name "listing-health" \
  --cron "0 6 * * 1" --session isolated \
  --message "Audit settimanale listing: sospesi, stock 0, prezzi anomali."

# Inventory reconciliation (venerdì 18:00)
openclaw cron add --name "inventory-reconcile" \
  --cron "0 18 * * 5" --session isolated \
  --message "Riconciliazione inventario: confronta stock fornitori vs marketplace."
```

### Webhook configuration

```yaml
# ~/.openclaw/config/hooks.yaml
hooks:
  enabled: true
  token: "${WEBHOOK_TOKEN}"    # Da .env
  path: /hooks

# Webhook endpoints per servizi esterni:
# Shopify:        POST /hooks  → "Nuovo ordine Shopify #ID"
# ChannelEngine:  POST /hooks  → "Nuovo ordine CE #ID da marketplace"
# n8n:            POST /hooks  → "Workflow result: {action}"
# Make:           POST /hooks  → "Scenario result: {data}"
```

### Email configuration

```yaml
# Setup consigliato: IMAP polling + Gmail Watch per urgenze

# IMAP per polling regolare
IMAP_HOST: imap.gmail.com
IMAP_PORT: 993
IMAP_USER: orders@vendiamonoi.it
IMAP_PASSWORD: ${GMAIL_APP_PASSWORD}

# Gmail Watch per real-time (opzionale, richiede GCP)
# Topic: projects/vendiamonoi/topics/gmail-notifications
# Subscription: push a https://openclaw.vendiamonoi.it/hooks
```

### Architettura ibrida Vendiamonoi

```
┌─────────────────────────────────────────────────────┐
│                    TRIGGER LAYER                     │
│                                                     │
│  Cron (scheduled)  │  Webhooks (event)  │  Email    │
└────────┬───────────┴─────────┬──────────┴────┬──────┘
         │                     │               │
         ▼                     ▼               ▼
┌─────────────────────────────────────────────────────┐
│              OPENCLAW (AI Brain)                     │
│  Analisi → Decisione → Azione → Log                 │
└────────────────────┬────────────────────────────────┘
                     │
         ┌───────────┼───────────┐
         ▼           ▼           ▼
┌──────────┐  ┌──────────┐  ┌──────────┐
│ n8n/Make │  │ MCP      │  │ Direct   │
│ Workflow │  │ Servers  │  │ API      │
│ (hands)  │  │ (bridge) │  │ (tools)  │
└──────────┘  └──────────┘  └──────────┘
         │           │           │
         ▼           ▼           ▼
┌─────────────────────────────────────────────────────┐
│              SERVIZI ESTERNI                         │
│  Shopify │ Marketplace │ Fornitori │ DB │ Telegram  │
└─────────────────────────────────────────────────────┘
```

### Costi stimati automazione Vendiamonoi

| Automazione | Costo/mese |
|---|---|
| Morning briefing (Sonnet, 1x/giorno) | ~$3-5 |
| Email check (Haiku, ogni 5 min) | ~$5-10 |
| Price monitoring (Haiku, 2x/giorno) | ~$2-4 |
| Supplier follow-up (Sonnet, 1x/giorno) | ~$1-2 |
| Listing health (Sonnet, 1x/settimana) | ~$0.50 |
| Inventory reconcile (Sonnet, 1x/settimana) | ~$0.50 |
| Heartbeat base (Haiku, ogni 30 min) | ~$5-15 |
| **Totale** | **~$17-37/mese** |

---

## 14. Fonti & Riferimenti

### Documentazione ufficiale
- Cron Jobs: https://docs.openclaw.ai/automation/cron-jobs
- Webhooks: https://docs.openclaw.ai/automation/webhook
- Gmail Pub/Sub: https://docs.openclaw.ai/automation/gmail-pubsub

### Guide e tutorial
- Adapt — Cron & Heartbeat Business Guide: https://adapt.com/blog/openclaw-cron-heartbeat
- Open-Claw.bot — Cron Jobs Docs: https://open-claw.bot/docs/tools/automation/cron-jobs/
- OpenClaws.io — Cron vs Heartbeat: https://openclaws.io/docs/automation/cron-vs-heartbeat
- DeepWiki — Automation & Cron: https://deepwiki.com/openclaw/openclaw/3.7-automation-and-cron
- Zen van Riel — Cron Jobs Guide: https://zenvanriel.com/ai-engineer-blog/openclaw-cron-jobs-proactive-ai-guide/
- Stack Junkie — Cron Setup Guide: https://www.stack-junkie.com/blog/openclaw-cron-jobs-automation-guide
- DEV Community — Cron Jobs: https://dev.to/hex_agent/openclaw-cron-jobs-automate-your-ai-agents-daily-tasks-4dpi

### Webhooks
- LumaDock — Webhooks Explained: https://lumadock.com/tutorials/openclaw-webhooks-explained
- Zen van Riel — Webhooks Guide: https://zenvanriel.com/ai-engineer-blog/openclaw-webhooks-external-integration-guide/
- Stanza — Webhook Triggers: https://www.stanza.dev/courses/openclaw-automation/webhooks-events/openclaw-automation-webhook-triggers

### Email/Gmail
- LumaDock — Gmail Integration: https://lumadock.com/tutorials/openclaw-gmail-integration-email-automation
- Expanso — Gmail Automation: https://expanso.io/expanso-hearts-openclaw/gmail/
- AgentMail — Connect OpenClaw to Gmail: https://www.agentmail.to/blog/connect-openclaw-to-gmail
- IMAP/SMTP skill: https://playbooks.com/skills/openclaw/skills/imap-smtp-email

### Integrazioni ibride
- ClawTank — OpenClaw vs n8n vs Zapier: https://clawtank.dev/blog/openclaw-vs-n8n-zapier
- Kollox — n8n Meets OpenClaw: https://kollox.com/architecting-multi-agent-ai-workflows-n8n-meets-openclaw/
- SkyWork — n8n vs OpenClaw: https://skywork.ai/skypage/en/n8n-openclaw-ai-automation/

---

> **Nota per Vendiamonoi**: L'automazione è il cuore dell'efficienza operativa. Iniziare con: (1) Morning briefing cron, (2) Email check ogni 5 min, (3) Webhook da Shopify/ChannelEngine. Poi aggiungere price monitoring e listing health. Usare Haiku per task ripetitivi ad alta frequenza, Sonnet per task che richiedono ragionamento.

---

*Ultimo aggiornamento: aprile 2026*
*Documento parte della Master Class OpenClaw — vendiamonoi-knowledge*
