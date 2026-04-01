# eDesk — Documentazione Tecnica Completa

## Informazioni Generali

| Campo | Dettaglio |
|---|---|
| **Nome** | eDesk (ex xSellco) |
| **Tipo** | Piattaforma di customer service multi-canale per e-commerce e marketplace |
| **Sito** | [https://www.edesk.com](https://www.edesk.com) |
| **Developer Portal** | [https://developers.edesk.com](https://developers.edesk.com) |
| **API Reference** | [https://developers.edesk.com/reference](https://developers.edesk.com/reference) |
| **Help Center** | [https://support.edesk.com](https://support.edesk.com) |
| **App Store** | [https://www.edesk.com/appstore](https://www.edesk.com/appstore) |
| **Fondazione** | 2012 (come xSellco, Ray Nolan) — rebrand eDesk nel 2021 |
| **Sede** | Dublino, Irlanda |
| **Uffici** | Irlanda, UK, USA |
| **Conversazioni** | 50+ milioni al mese |
| **Integrazioni** | 300+ marketplace, webstore e canali social |
| **Partnership** | Unica soluzione CS su Amazon e Walmart developer councils |
| **Mirakl Partner** | 300+ marketplace Mirakl supportati nativamente |

---

## Contesto Vendiamonoi.it

### Utilizzo in Vendiamonoi.it

eDesk è la piattaforma di customer service principale di Vendiamonoi.it S.R.L. Unipersonale, utilizzata per:

1. **Inbox unificata** — tutti i messaggi da 20-30+ marketplace europei in un'unica interfaccia
2. **Gestione ticket** — creazione, assegnazione, prioritizzazione e risoluzione ticket da marketplace
3. **SLA marketplace** — monitoraggio e rispetto dei tempi di risposta richiesti da ogni marketplace
4. **Automazione risposte** — HandsFree AI, auto-reply, template automatizzati per query ripetitive
5. **Traduzione automatica** — messaggi clienti in 60+ lingue tradotti automaticamente
6. **Dati ordine integrati** — ogni ticket mostra ordine, tracking, prodotto, storico cliente
7. **Gestione resi** — workflow resi/rimborsi con dati ordine da marketplace
8. **Reporting** — KPI per marketplace, SLA compliance, performance agenti, CSAT

### Flusso Operativo

```
MESSAGGI IN ENTRATA:
  Cliente marketplace (Amazon, eBay, Kaufland, Leroy Merlin...)
    → Messaggio buyer-seller / ticket / reclamo
    → eDesk inbox unificata
    → AI Classification (40+ categorie)
    → Sentiment Analysis (positivo/neutro/negativo)

GESTIONE:
  eDesk Smart Inbox
    → Auto-assign (round-robin o rule-based)
    → HandsFree: risoluzione automatica se query standard
    → Agente: risposta manuale con template + snippets + dati ordine
    → Traduzione automatica se lingua diversa

RISOLUZIONE:
  Agente risponde da eDesk
    → Messaggio sincronizzato su marketplace originale
    → SLA timer fermato
    → Ticket chiuso / in attesa risposta cliente

REPORTING:
  Dashboard eDesk
    → SLA compliance per marketplace
    → CSAT score
    → Performance agenti
    → Volumi per canale
```

---

## 1. Architettura della Piattaforma

### 1.1 Stack e Servizi

| Componente | Funzione |
|---|---|
| **Smart Inbox** | Inbox unificata AI-powered con categorizzazione automatica, filtri smart, bulk actions |
| **Ticket Management** | Creazione automatica ticket, assegnazione, routing, prioritizzazione, collision detection |
| **AI Engine** | Classification (40+ categorie), sentiment analysis, smart reply, auto-translation, summaries |
| **HandsFree** | Auto-risoluzione ticket senza intervento umano basata su AI classification |
| **Templates & Snippets** | Template manuali, auto-reply, rule-only, AI-selected. Snippets con dati personalizzati |
| **Message Rules** | Automazione rule-based: triggers, condizioni, azioni su ticket |
| **SLA Manager** | Tracking SLA per canale/marketplace con escalation e alerting |
| **Reporting** | Dashboard real-time, CSAT, agent performance, SLA compliance, export CSV |
| **eDesk Talk** | VoIP integrato (round-robin, voicemail, ticket automatico) |
| **Live Chat** | Chat widget per webstore |
| **API v1** | REST API per integrazioni custom |
| **Webhooks** | Notifiche su eventi via Message Rules |

### 1.2 Canali Supportati

| Categoria | Canali |
|---|---|
| **Marketplace** | Amazon (21 marketplace), eBay (20 canali), Kaufland, Fnac-Darty, Cdiscount, Leroy Merlin, ManoMano, Rakuten, Allegro, MediaMarkt, Decathlon, Yoox, 300+ via Mirakl |
| **Webstore** | Shopify, WooCommerce, BigCommerce, Magento, PrestaShop |
| **Email** | Email nativa su tutti i piani |
| **Social** | Facebook (DM, post, commenti), Instagram (DM, commenti, menzioni), Twitter/X, WhatsApp |
| **Voice** | eDesk Talk (VoIP), Aircall (integrazione avanzata) |
| **Chat** | Live Chat widget integrato |
| **Channel Connectors** | ChannelEngine, Mirakl (300+ marketplace) |

### 1.3 Marketplace Europei Chiave per Vendiamonoi.it

| Marketplace | Integrazione | Dettaglio |
|---|---|---|
| **Amazon** (IT, DE, FR, ES, etc.) | Nativa | 21 marketplace, buyer-seller messaging, ordini, FBA tracking, feedback |
| **eBay** | Nativa | 20 canali, messaggi, casi Resolution Center, tracking |
| **Kaufland** | Nativa | Ordini, spedizioni, dati cliente, tracking numbers |
| **Fnac-Darty** | Nativa | Messaggi bidirezionali, dati ordine |
| **Cdiscount** | Nativa (Octopia API) | Messaggi buyer, dati cliente |
| **Leroy Merlin** | Nativa (unica piattaforma CS) | Ordini automatici in ogni ticket, via Mirakl |
| **ManoMano** | Via Mirakl | 300+ marketplace Mirakl |
| **Rakuten** | Nativa | Messaggi e ordini |
| **Allegro** | Nativa | Mercato polacco |
| **MediaMarkt** | Nativa | Mercato tedesco |
| **ChannelEngine** | Nativa | Connettore per marketplace aggiuntivi |
| **Mirakl** | Nativa | 300+ marketplace supportati |

---

## 2. AI e Automazione

### 2.1 AI Classification

| Funzionalità | Dettaglio |
|---|---|
| **Categorie** | 40+ classificazioni AI out-of-the-box |
| **Esempi** | "Delivery issue with tracking", "Delivery issue without tracking", "Return request", "Order cancellation", "Product inquiry" |
| **Accuratezza** | 95%+ |
| **Uso** | Routing automatico, HandsFree automation, reporting per tipo query |

### 2.2 AI Smart Reply

| Funzionalità | Dettaglio |
|---|---|
| **Funzione** | Genera risposte contestuali con un click |
| **Efficacia** | Risolve fino al 73% in più di query |
| **Contesto** | Include dati ordine, tracking, storico ticket |
| **Benefit** | Riduce tempi formazione, scala il team |

### 2.3 HandsFree (Auto-Risoluzione)

| Funzionalità | Dettaglio |
|---|---|
| **Funzione** | Risolve automaticamente il primo messaggio del ticket senza intervento umano |
| **Basato su** | AI Classification + Template associato |
| **Priorità** | Ha priorità su auto-reply e Message Rules |
| **Limitazioni** | Non disponibile per eBay cases e ticket Mirakl (richiede selezione Customer/Operator) |
| **Template** | Solo template manuali (non auto-reply, OOO o rule-only) |
| **Richiede** | AI Automation subscription (add-on) |

### 2.4 Auto-Translation

| Funzionalità | Dettaglio |
|---|---|
| **Lingue** | 60+ lingue supportate |
| **Detection** | Rilevamento automatico lingua messaggio |
| **Traduzione in entrata** | Messaggi tradotti automaticamente nella lingua dell'agente |
| **Traduzione in uscita** | Prompt per tradurre risposta nella lingua del cliente prima dell'invio |

### 2.5 Sentiment Analysis

- Analisi automatica del tono del messaggio cliente
- Classificazione: positivo, neutro, negativo
- Colonna sentiment visibile in mailbox
- Aiuta agenti a prioritizzare ticket urgenti/negativi

### 2.6 AI Summaries

- Riassunto automatico delle conversazioni
- Estrazione contenuto chiave dal ticket
- Utile per ticket con storico lungo

---

## 3. Automazione e Workflow

### 3.1 Message Rules

Automazione rule-based per gestione ticket:

**Struttura regola:**

```
QUANDO: [trigger/condizione]
  → fonte marketplace
  → lingua del messaggio
  → valore ordine
  → prodotto specifico
  → parola chiave nel messaggio
  → sentiment (positivo/neutro/negativo)

ALLORA: [azione]
  → Assegna a agente/team
  → Applica tag
  → Applica template
  → Invia a webhook URL
  → Cambia priorità
  → Sposta in cartella
```

**Esempi per Vendiamonoi.it:**

```
Regola 1: Messaggi Amazon IT → Tag "Amazon-IT" → Assegna a Team Italia
Regola 2: Messaggi in tedesco → Auto-translate → Tag "DE" → Assegna a Team DE
Regola 3: Ordine > €500 → Tag "VIP" → Priorità alta → Assegna a Senior Agent
Regola 4: Parola "reso" o "rimborso" → Tag "Return" → Template reso standard
Regola 5: Sentiment negativo → Priorità urgente → Assegna a Team Leader
Regola 6: Messaggio Kaufland → Tag "Kaufland" → Auto-reply SLA
```

### 3.2 Auto-Reply Templates

| Tipo | Uso |
|---|---|
| **Auto-Reply** | Risposta automatica quando team non disponibile (sera, weekend, volumi alti) |
| **Out of Office (OOO)** | Chiusure programmate (festività, ferie) |
| **Rule-Only** | Attivato solo da Message Rules per query specifiche |

**Funzione critica:** Le auto-reply fermano il timer SLA su marketplace con regole di risposta rigorose.

### 3.3 Template e Snippets

**Template:**
- Template manuali — selezionati manualmente dall'agente
- Template AI — selezionati automaticamente da AI classification
- Template HandsFree — usati per auto-risoluzione

**Snippets:**
- Company Snippets — creati da admin, disponibili per tutto il team
- Personal Snippets — creati da singolo utente
- Inserimento con `#` nel campo risposta
- Personalizzazione automatica: nome cliente, numero ordine, data spedizione, indirizzo

### 3.4 Assegnazione Ticket

| Metodo | Funzione |
|---|---|
| **Round-Robin** | Rotazione automatica tra agenti, distribuzione equa workload |
| **By Roles** | Assegna a tutti gli utenti con un ruolo specifico |
| **By Users** | Assegna a utenti specifici selezionati |
| **Rule-Based** | Assegnazione tramite Message Rules (condizioni marketplace, lingua, etc.) |
| **Disponibilità** | Orari lavorativi configurabili, "Don't assign on these dates" per ferie |

---

## 4. Team Management

### 4.1 Ruoli

| Ruolo | Permessi |
|---|---|
| **Admin** | Accesso completo: impostazioni, configurazione, VAT, timezone, gestione utenti e ruoli |
| **Team Leader** | Configura Smart Tools (Message Rules, template, tag, AI, snippets), accede a tutte le mailbox views del team, gestisce chat e knowledge base |
| **Support Agent** | Gestisce i propri ticket, risponde ai clienti, usa template e snippets |

### 4.2 Collaborazione

| Funzionalità | Dettaglio |
|---|---|
| **@mention** | Coinvolgi colleghi direttamente nel ticket |
| **Internal Share** | Condividi ticket con utenti non-eDesk |
| **Internal Notes** | Note interne visibili solo al team |
| **Collision Detection** | Previene lavoro duplicato sullo stesso ticket |
| **Assignment Tracking** | Tracciamento assegnazioni e responsabilità |

---

## 5. Dati Ordine e Gestione Resi

### 5.1 Order Data Integration

| Funzionalità | Dettaglio |
|---|---|
| **Sync automatico** | Dati ordine sincronizzati da tutte le piattaforme di vendita |
| **Dati visibili** | Data ordine, metodo pagamento, stato spedizione, tracking, prodotti |
| **Storico** | Comunicazioni precedenti sullo stesso ordine |
| **Timeline** | Ciclo di vita ordine completo nel ticket |
| **AI context** | Smart Reply include contesto live dell'ordine |

### 5.2 Webhook per Aggiornamenti Ordine

```
Evento spedizione (shipped, in delivery, delivered)
  → Webhook → eDesk
  → Aggiornamento automatico nel ticket
  → Previene domande ripetitive sullo stato
```

### 5.3 Gestione Resi

| Funzionalità | Dettaglio |
|---|---|
| **Integrazione RMA** | ReturnLogic, AfterShip, Loop |
| **Dati ordine real-time** | Da tutti i marketplace |
| **Template resi** | Risposte policy-compliant con un click |
| **Automazione** | 80% delle query sui resi automatizzabili |
| **Shopify** | Modifica ordini, resi e cancellazioni direttamente da eDesk |

---

## 6. API v1

### 6.1 Panoramica

| Aspetto | Dettaglio |
|---|---|
| **Base URL** | `https://api.edesk.com/v1/` |
| **Formato** | JSON |
| **Autenticazione** | Token-based (API Token generato da Settings > API Tokens) |
| **Piano richiesto** | Professional o superiore per accesso API |
| **Documentazione** | [https://developers.edesk.com/reference](https://developers.edesk.com/reference) |
| **Getting Started** | [https://support.edesk.com/getting-started-with-the-edesk-api](https://support.edesk.com/getting-started-with-the-edesk-api) |

### 6.2 Autenticazione

```bash
# Header autenticazione
Authorization: Bearer {api_token}
Content-Type: application/json
```

Token generabile da: Settings → API Tokens nel pannello eDesk.

### 6.3 Endpoints Principali

#### Tickets

```bash
# Lista ticket
GET /v1/tickets
  ?status=open
  &page=1
  &per_page=50

# Dettaglio ticket
GET /v1/tickets/{ticket_id}

# Crea ticket
POST /v1/tickets

# Aggiorna ticket
PUT /v1/tickets/{ticket_id}
```

#### Messages

```bash
# Crea messaggio in un thread
POST /v1/tickets/{ticket_id}/messages

# Visualizza messaggio
GET /v1/tickets/{ticket_id}/messages/{message_id}

# Aggiorna messaggio
PUT /v1/tickets/{ticket_id}/messages/{message_id}
```

#### Orders (Sales Orders)

```bash
# Lista ordini
GET /v1/orders

# Crea ordine
POST /v1/orders

# Dettaglio ordine
GET /v1/orders/{order_id}

# Aggiorna ordine
PUT /v1/orders/{order_id}

# Aggiungi tracking
POST /v1/orders/{order_id}/tracking
```

#### Tags

```bash
# Lista tag
GET /v1/tags

# Crea tag
POST /v1/tags

# Aggiorna tag
PUT /v1/tags/{tag_id}

# Elimina tag
DELETE /v1/tags/{tag_id}
```

#### Templates

```bash
# Lista template
GET /v1/templates

# Crea template
POST /v1/templates

# Aggiorna template
PUT /v1/templates/{template_id}

# Elimina template
DELETE /v1/templates/{template_id}
```

#### Custom Fields

```bash
# Lista custom fields
GET /v1/custom_fields

# Aggiorna custom field
PUT /v1/custom_fields/{field_id}
```

#### Channels

```bash
# Lista canali
GET /v1/channels

# Crea canale
POST /v1/channels
```

#### Customers

```bash
# Visualizza cliente
GET /v1/customers/{customer_id}
```

#### Reports

```bash
# Lista report
GET /v1/reports

# Crea report
POST /v1/reports
```

### 6.4 Webhooks

**Configurazione:** Tramite Message Rules — quando una regola si attiva, invia dati al webhook URL configurato.

**Payload webhook:**

```json
{
  "email": "cliente@example.com",
  "name": "Nome Cliente",
  "channel_id": "ch_123",
  "channel_title": "Amazon IT",
  "ticket_id": "t_456",
  "message_id": "m_789",
  "subject": "Ordine #12345 - Problema spedizione",
  "sales_order_id": "so_101112"
}
```

**Nota:** Il webhook posta le informazioni del thread quando una Message Rule si attiva, ma non include il contenuto completo del messaggio. Per ottenere il contenuto, usare l'API GET ticket/message dopo aver ricevuto il webhook.

### 6.5 Casi d'Uso API per Vendiamonoi.it

```
1. SYNC ORDINI
   Supabase (database ordini)
     → API eDesk: POST /v1/orders
     → Crea/aggiorna ordini in eDesk
     → Dati ordine disponibili nei ticket

2. TRACKING UPDATE
   Make.com (webhook spedizione)
     → API eDesk: POST /v1/orders/{id}/tracking
     → Aggiorna tracking number
     → Cliente vede stato aggiornato

3. EXPORT TICKET PER ANALISI
   Schedule (settimanale)
     → API eDesk: GET /v1/tickets?status=closed
     → Analisi tempi risoluzione per marketplace
     → Google Sheets: dashboard performance

4. TAG AUTOMATICI DA ESTERNO
   Make.com / Supabase
     → API eDesk: POST /v1/tags (applica tag)
     → Categorizzazione cross-system

5. TEMPLATE MANAGEMENT
   Aggiornamento template centralizzato
     → API eDesk: PUT /v1/templates/{id}
     → Sincronizza template da sistema centrale
```

---

## 7. Reporting e Analytics

### 7.1 Dashboard

| Metrica | Descrizione |
|---|---|
| **SLA Compliance** | Percentuale rispetto SLA per canale/marketplace |
| **CSAT Score** | Customer Satisfaction medio e per agente |
| **First Response Time** | Tempo medio prima risposta |
| **Resolution Time** | Tempo medio risoluzione |
| **Ticket Volume** | Volume ticket per canale/periodo |
| **Agent Performance** | Metriche per agente (velocità, volume, CSAT) |
| **Channel-specific SLA** | Compliance per marketplace |

### 7.2 Report Estraibili

| Tipo Report | Descrizione |
|---|---|
| **Agents** | Performance per agente |
| **Channels** | Attività per canale/marketplace |
| **Tickets** | Analisi ticket (volume, status, tempi) |
| **Tags** | Distribuzione per tag |
| **SLA Breaches** | Violazioni SLA con dettaglio |
| **HandsFree** | Risultati automazione AI |
| **Languages** | Distribuzione per lingua |
| **AI Resolutions** | Ticket risolti da AI |
| **eBay Cases** | Casi eBay specifici |
| **Custom Fields** | Report su campi personalizzati |
| **Chats** | Metriche live chat |

**Export:** CSV scaricabile — Report schedulabili — Alert su soglie SLA — Widget personalizzabili.

---

## 8. Pricing e Piani

### 8.1 Piani

| Piano | Prezzo | Marketplace/Store | Ticket | Note |
|---|---|---|---|---|
| **Starter** | ~$39/utente/mese | 1 connessione | Illimitati | Entry-level |
| **Professional** | ~$59/utente/mese | 5 connessioni | Illimitati | Accesso API, collaborazione avanzata |
| **Performance** | ~$99/utente/mese | 10 connessioni | Illimitati | Tutte le feature avanzate |
| **Enterprise** | ~$158/utente/mese | Illimitate | Illimitati | Custom, support dedicato |

### 8.2 Add-On AI

| Add-On | Funzione |
|---|---|
| **AI Automation** | HandsFree, classification, smart reply, summaries, sentiment |
| **Disponibilità** | Su tutti i piani |
| **Pricing** | Subscription separata |

### 8.3 Note

- **Free trial** disponibile
- **Numero agenti** modificabile in qualsiasi momento
- **Ticket illimitati** su tutti i piani
- **300+ integrazioni** su tutti i piani

**Per Vendiamonoi.it:** piano **Performance** (10 connessioni, ~$99/utente/mese) o **Enterprise** (illimitate) raccomandato per 20-30+ marketplace.

---

## 9. Pattern Architetturali per Vendiamonoi.it

### 9.1 Architettura Customer Service

```
                    ┌──────────────────────────┐
                    │   20-30+ MARKETPLACE      │
                    │   (Amazon IT/DE/FR/ES,    │
                    │    eBay, Kaufland,         │
                    │    Fnac, Leroy Merlin,     │
                    │    Cdiscount, ManoMano...) │
                    └──────────┬───────────────┘
                               │
                    ┌──────────▼───────────────┐
                    │       eDesk              │
                    │   (Smart Inbox)          │
                    │                          │
                    │ • AI Classification      │
                    │ • Sentiment Analysis     │
                    │ • Auto-Translation       │
                    │ • HandsFree              │
                    │ • SLA Tracking           │
                    └──────────┬───────────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
     ┌────────▼──────┐ ┌──────▼──────┐ ┌──────▼──────┐
     │  Team Italia  │ │  Team EU    │ │  Team VIP   │
     │  (Amazon IT,  │ │  (DE, FR,   │ │  (Ordini    │
     │   eBay IT)    │ │   ES, etc.) │ │   > €500)   │
     └───────────────┘ └─────────────┘ └─────────────┘
```

### 9.2 Struttura Tag Raccomandata

```
eDesk Tags per Vendiamonoi.it:

📦 MARKETPLACE
  ├── MKP-Amazon-IT
  ├── MKP-Amazon-DE
  ├── MKP-Amazon-FR
  ├── MKP-eBay
  ├── MKP-Kaufland
  ├── MKP-FnacDarty
  ├── MKP-LeroyMerlin
  ├── MKP-Cdiscount
  ├── MKP-ManoMano
  └── MKP-Other

🔄 TIPO QUERY
  ├── QRY-Tracking
  ├── QRY-Reso
  ├── QRY-Rimborso
  ├── QRY-Prodotto
  ├── QRY-Spedizione
  ├── QRY-Cancellazione
  ├── QRY-Fattura
  └── QRY-Altro

⚡ PRIORITÀ
  ├── PRI-Urgente (sentiment negativo)
  ├── PRI-VIP (ordine > €500)
  └── PRI-Normale

🌍 LINGUA
  ├── LANG-IT
  ├── LANG-DE
  ├── LANG-FR
  ├── LANG-ES
  └── LANG-EN
```

### 9.3 Message Rules Raccomandate

```
Regola 1: Auto-Tag Marketplace
  QUANDO: Canale = Amazon IT
  ALLORA: Tag "MKP-Amazon-IT"

Regola 2: Escalation Sentiment Negativo
  QUANDO: Sentiment = Negativo
  ALLORA: Tag "PRI-Urgente" → Assegna Team Leader

Regola 3: VIP Orders
  QUANDO: Valore ordine > €500
  ALLORA: Tag "PRI-VIP" → Priorità alta

Regola 4: Auto-Reply SLA (fuori orario)
  QUANDO: Ora = 19:00-08:00 OR giorno = Sabato/Domenica
  ALLORA: Auto-Reply template "Fuori orario"
  (ferma timer SLA marketplace)

Regola 5: HandsFree Tracking
  QUANDO: AI Classification = "Delivery issue with tracking"
  ALLORA: HandsFree → Template "Tracking standard"
  (risoluzione automatica senza agente)

Regola 6: Resi Automatici
  QUANDO: AI Classification = "Return request"
  ALLORA: Tag "QRY-Reso" → Template "Procedura reso"

Regola 7: Lingua Tedesca
  QUANDO: Lingua = DE
  ALLORA: Tag "LANG-DE" → Assegna Team EU
```

### 9.4 Integrazione con Stack Vendiamonoi.it

```
eDesk ←→ Make.com:
  • Webhook eDesk (Message Rule) → Make → Supabase (log ticket)
  • Make schedule → eDesk API (export ticket) → Google Sheets (report)

eDesk ←→ Supabase:
  • Supabase (ordine nuovo) → Make → eDesk API POST /orders
  • Supabase (tracking update) → Make → eDesk API POST /orders/{id}/tracking

eDesk ←→ Fatture in Cloud:
  • Cliente richiede fattura → Agente eDesk → Make → FiC genera fattura
  • Fattura generata → Make → eDesk (nota interna con link fattura)

eDesk ←→ Google Sheets:
  • Report settimanale: eDesk API → Make → Google Sheets (dashboard CS)
  • KPI marketplace: SLA compliance, CSAT, volumi per canale
```

---

## 10. Sicurezza e Compliance

| Aspetto | Dettaglio |
|---|---|
| **GDPR** | Conforme (azienda irlandese, soggetta a GDPR EU) |
| **Data hosting** | Cloud EU (verificare con support per data residency specifica) |
| **Autenticazione** | Token-based API, credenziali utente per accesso piattaforma |
| **Ruoli** | Admin, Team Leader, Support Agent con permessi granulari |
| **Audit** | User logs disponibili nei report |

---

## 11. Troubleshooting

### 11.1 Errori Comuni

| Errore | Causa | Soluzione |
|---|---|---|
| Messaggi non sincronizzati | Credenziali marketplace scadute | Riconnettere marketplace da Settings > Integrations |
| SLA breach | Timer non fermato | Configurare auto-reply per fermare SLA fuori orario |
| HandsFree non attivo | Template non manuale | Usare solo template manuali per HandsFree (no auto-reply/OOO) |
| HandsFree su Mirakl | Non supportato | HandsFree non disponibile per ticket Mirakl — usare Message Rules |
| API 401 | Token invalido/scaduto | Rigenerare token da Settings > API Tokens |
| Webhook vuoto | Solo metadata nel payload | Usare API GET ticket dopo webhook per contenuto completo |
| Traduzione non attiva | Lingua non rilevata | Verificare impostazioni lingua agente e auto-translation |

### 11.2 Best Practice

- **Configurare auto-reply** per fuori orario su tutti i marketplace con SLA rigorosi
- **Usare HandsFree** per le query più comuni (tracking, stato ordine) — libera agenti per casi complessi
- **Tag strutturati** — usare prefissi (MKP-, QRY-, PRI-, LANG-) per organizzazione e reporting
- **Snippet con `#`** — velocizza risposta inserendo dati personalizzati
- **Round-robin** — garantisce distribuzione equa del carico
- **Collision detection** — evita che due agenti lavorino sullo stesso ticket
- **Report schedulati** — monitorare SLA compliance settimanalmente
- **AI Classification** — review periodico delle classificazioni per ottimizzare HandsFree

---

## 12. Risorse e Riferimenti

| Risorsa | URL |
|---|---|
| Sito ufficiale | https://www.edesk.com |
| Pricing | https://www.edesk.com/pricing |
| App Store | https://www.edesk.com/appstore |
| Developer Portal | https://developers.edesk.com |
| API Reference | https://developers.edesk.com/reference |
| Authentication | https://developers.edesk.com/reference/authentication |
| Help Center | https://support.edesk.com |
| Getting Started API | https://support.edesk.com/getting-started-with-the-edesk-api |
| Token Generation | https://support.edesk.com/how-to-generate-a-token-edesk-api |
| API Use Cases | https://support.edesk.com/4-ways-use-edesk-api |
| Message Rules | https://support.edesk.com/message-rules |
| HandsFree | https://support.edesk.com/handsfree-automation |
| Templates | https://support.edesk.com/templates |
| Snippets | https://support.edesk.com/snippets |
| Smart Inbox | https://www.edesk.com/smart-inbox |
| AI Features | https://www.edesk.com/ai |
| Integrations | https://support.edesk.com/integrations |
| Amazon Integration | https://support.edesk.com/amazon |
| eBay Integration | https://support.edesk.com/ebay |
| Kaufland Integration | https://support.edesk.com/connecting-kaufland-with-edesk |
| Mirakl Integration | https://support.edesk.com/edesk-mirakl-integration |
| Reporting | https://support.edesk.com/edesk-reporting-measure |

---

## Changelog

| Data | Versione | Modifica |
|---|---|---|
| 2026-04-01 | 1.0 | Creazione documentazione completa |
