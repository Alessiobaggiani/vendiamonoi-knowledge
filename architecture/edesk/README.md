# eDesk — Architettura Completa, AI, API, Customer Service Multi-Marketplace

## Overview

eDesk (ex xSellco) è la piattaforma di customer service multi-canale leader per e-commerce e marketplace, con 50+ milioni di conversazioni/mese e 300+ integrazioni native. Fondata nel 2012 a Dublino (Irlanda), è l'unica soluzione CS presente su Amazon e Walmart developer councils, e partner nativo Mirakl (300+ marketplace).

Per Vendiamonoi.it, eDesk è il sistema di customer service principale: inbox unificata per 20-30+ marketplace europei, gestione ticket con AI classification, auto-traduzione 60+ lingue, HandsFree auto-risoluzione, Ava AI chatbot, monitoraggio SLA marketplace, gestione resi, e reporting performance.

---

## Informazioni Generali

| Campo | Dettaglio |
|---|---|
| **Nome** | eDesk (ex xSellco) |
| **Tipo** | Piattaforma CS multi-canale per e-commerce/marketplace |
| **Sito** | https://www.edesk.com |
| **Developer Portal** | https://developers.edesk.com |
| **API Reference** | https://developers.edesk.com/reference |
| **Help Center** | https://support.edesk.com |
| **App Store** | https://www.edesk.com/appstore |
| **Fondazione** | 2012 (xSellco), rebrand eDesk 2021 |
| **Sede** | Dublino, Irlanda |
| **Uffici** | Irlanda, UK, USA |
| **Conversazioni** | 50+ milioni/mese |
| **Integrazioni** | 300+ marketplace, webstore, social |
| **Partnership** | Amazon Developer Council, Walmart Developer Council, Mirakl Partner |

---

## Piani e Pricing

| Piano | Prezzo/utente/mese | Connessioni | Ticket | API | Note |
|---|---|---|---|---|---|
| **Starter** | ~$39 | 1 | Illimitati | ❌ | Entry-level |
| **Professional** | ~$59 | 5 | Illimitati | ✅ | + collaborazione avanzata |
| **Performance** | ~$99 | 10 | Illimitati | ✅ | + tutte le feature avanzate |
| **Enterprise** | ~$158 | Illimitate | Illimitati | ✅ | + custom, support dedicato |

### Add-On AI Automation

- Subscription separata disponibile su tutti i piani
- Include: HandsFree, AI Classification, Smart Reply, Summaries, Sentiment Analysis
- Ava AI Chatbot: add-on separato con pricing basato su conversazioni

### Raccomandazione Vendiamonoi

Piano **Performance** (~$99/utente/mese) o **Enterprise** (~$158) per 20-30+ marketplace. Con 2-3 agenti CS: ~$200-475/mese + AI add-on.

---

## Architettura della Piattaforma

### Stack Componenti

| Componente | Funzione | Priorità Vendiamonoi |
|---|---|---|
| **Smart Inbox** | Inbox unificata AI-powered, categorizzazione automatica, filtri smart, bulk actions | ⭐⭐⭐ |
| **Ticket Management** | Creazione automatica, assegnazione, routing, prioritizzazione, collision detection | ⭐⭐⭐ |
| **AI Engine** | Classification (40+ categorie), sentiment, smart reply, translation, summaries | ⭐⭐⭐ |
| **Ava AI Chatbot** | Chatbot AI 24/7 per pre-sale e support, order tracking, product info | ⭐⭐ |
| **HandsFree** | Auto-risoluzione ticket senza intervento umano | ⭐⭐⭐ |
| **Knowledge Base** | Help center self-service per clienti, riduce ticket fino al 30% | ⭐⭐ |
| **Templates & Snippets** | Template manuali/auto/AI, snippets con dati personalizzati (#) | ⭐⭐⭐ |
| **Message Rules** | Automazione rule-based: trigger, condizioni, azioni su ticket | ⭐⭐⭐ |
| **SLA Manager** | Tracking SLA per canale/marketplace, escalation, alerting | ⭐⭐⭐ |
| **CSAT** | Survey soddisfazione cliente post-risoluzione, multilingua | ⭐⭐ |
| **Reporting** | Dashboard real-time, CSAT, agent performance, SLA compliance | ⭐⭐⭐ |
| **eDesk Talk** | VoIP integrato (round-robin, voicemail, ticket automatico) | ⭐ |
| **Live Chat** | Chat widget per webstore | ⭐ |
| **API v1** | REST API per integrazioni custom | ⭐⭐⭐ |

### Canali Supportati

| Categoria | Canali |
|---|---|
| **Marketplace** | Amazon (21 marketplace), eBay (20 canali), Kaufland, Fnac-Darty, Cdiscount, Leroy Merlin, ManoMano, Rakuten, Allegro, MediaMarkt, Decathlon, Yoox, 300+ via Mirakl |
| **Webstore** | Shopify, WooCommerce, BigCommerce, Magento, PrestaShop |
| **Email** | Email nativa su tutti i piani |
| **Social** | Facebook (DM, post, commenti), Instagram (DM, commenti, menzioni), Twitter/X, WhatsApp |
| **Voice** | eDesk Talk (VoIP), Aircall |
| **Chat** | Live Chat widget, Ava AI Chatbot |
| **Channel Connectors** | ChannelEngine, Mirakl (300+ marketplace) |

### Marketplace Europei Chiave per Vendiamonoi

| Marketplace | Integrazione | SLA Risposta | Note |
|---|---|---|---|
| **Amazon** (IT/DE/FR/ES/etc.) | Nativa | 24h (obbligatorio) | 21 marketplace, buyer-seller messaging, >90% response rate richiesto |
| **eBay** | Nativa | 24-48h | 20 canali, Resolution Center, feedback |
| **Kaufland** | Nativa | 48h | Ordini, spedizioni, tracking |
| **Fnac-Darty** | Nativa | 48h | Messaggi bidirezionali |
| **Cdiscount** | Nativa (Octopia) | 48h | Messaggi buyer |
| **Leroy Merlin** | Nativa (Mirakl) | 48h | Via Mirakl, ordini automatici in ticket |
| **ManoMano** | Via Mirakl | 48h | 300+ marketplace Mirakl |
| **Carrefour** | Via Mirakl | 48h | Marketplace Mirakl |
| **MediaMarkt** | Nativa | 48h | Mercato tedesco |
| **Bol.com** | Nativa | 24h | Mercato NL/BE |
| **Allegro** | Nativa | 24h | Mercato polacco |

---

## AI e Automazione

### AI Classification

| Aspetto | Dettaglio |
|---|---|
| **Categorie** | 40+ classificazioni out-of-the-box |
| **Accuratezza** | 95%+ |
| **Principali categorie** | Product Query & Pre-Sales, Shipping Inquiries, Cancellations, Returns, Refunds, Delivery Issue (with/without tracking), Order Status, Invoice Request, Damaged Item |
| **Uso** | Routing automatico, HandsFree, reporting per tipo query |
| **Apprendimento** | Il modello migliora nel tempo apprendendo dai pattern del team |

### AI Smart Reply

| Aspetto | Dettaglio |
|---|---|
| **Funzione** | Genera risposte contestuali con un click |
| **Efficacia** | Risolve fino al 73% in più di query |
| **Contesto** | Include dati ordine, tracking, storico ticket |
| **Training** | Utilizza Training Content (Knowledge Base, prodotti, FAQ) |
| **Benefit** | Riduce tempi formazione nuovi agenti, scala il team |

### AI Sentiment Analysis

| Aspetto | Dettaglio |
|---|---|
| **Classificazione** | Positivo, Neutro, Negativo |
| **Visibilità** | Colonna sentiment in mailbox view |
| **Uso** | Prioritizzazione ticket negativi, escalation automatica |
| **Trigger** | Utilizzabile nelle Message Rules per routing/escalation |

### AI Summaries

- Riassunto automatico conversazioni lunghe
- Estrazione contenuto chiave
- Utile per handover tra agenti e ticket con storico esteso

### Auto-Translation

| Aspetto | Dettaglio |
|---|---|
| **Lingue** | 60+ lingue supportate |
| **Detection** | Rilevamento automatico lingua messaggio |
| **In entrata** | Traduce nella lingua dell'agente |
| **In uscita** | Prompt per tradurre risposta nella lingua del cliente |
| **Fondamentale** | Per Vendiamonoi con 10+ lingue (IT, DE, FR, ES, NL, PL, etc.) |

### HandsFree (Auto-Risoluzione) ⭐

| Aspetto | Dettaglio |
|---|---|
| **Funzione** | Risolve automaticamente il primo messaggio del ticket senza intervento umano |
| **Basato su** | AI Classification + Template associato |
| **Priorità** | Ha priorità su auto-reply e Message Rules |
| **Template** | Solo template manuali (non auto-reply, OOO o rule-only) |
| **Richiede** | AI Automation subscription (add-on) |
| **Performance** | Automatizza fino al 65% del supporto |
| **Riduzione tempi** | Fino all'80% di riduzione tempi risposta |
| **Scalabilità** | 3x più ticket con lo stesso headcount |

#### Limitazioni HandsFree

- **NON disponibile** per eBay cases (richiede selezione Customer/Operator)
- **NON disponibile** per ticket Mirakl (richiede selezione risposta tipo)
- Per questi canali: usare Message Rules con template auto-reply

---

## Ava AI Chatbot ⭐ (Novità 2025-2026)

### Overview

Ava è il chatbot AI di eDesk, costruito specificamente per e-commerce. Non è un semplice chatbot FAQ: si integra con dati prodotto, ordini, tracking e policy aziendali per risolvere richieste complesse 24/7.

### Capacità

| Funzione | Dettaglio |
|---|---|
| **Pre-sale support** | Raccomandazioni prodotto, info promozioni, dettagli spedizione |
| **Order tracking** | Query stato ordine in tempo reale, tracking number, date consegna |
| **Returns/Refunds** | Guida cliente nel processo reso secondo policy |
| **FAQ automatiche** | Risposte da Knowledge Base e Training Content |
| **Handover umano** | Escalation ad agente quando necessario |
| **CSAT per chatbot** | Survey soddisfazione integrata nel flusso chat |

### Performance

| KPI | Valore |
|---|---|
| Pre-sale inquiries gestite | Fino al 72% |
| Query risolte automaticamente | Fino al 70% |
| Conversazioni che supportano acquisto | 75% |
| Ordini preceduti da conversazione Ava | 1 su 4 |
| Vendite fuori orario | 65% delle vendite online |

### Content Hub (Training)

Ava apprende da un Content Hub che puoi popolare con:

1. **Knowledge Base eDesk** — articoli help center già creati
2. **Product Feed** — sync automatico da Shopify (ogni 24h), prodotti attivi con prezzi, descrizioni, varianti
3. **FAQ** — domande/risposte personalizzate
4. **Testo personalizzato** — free text per policy specifiche (reso, spedizione, garanzia)
5. **URL sito web** — crawling pagine del tuo sito
6. **Blog** — contenuti informativi

### Configurazione Consigliata per Vendiamonoi

```
Content Hub Ava:
├── Policy reso standard (per marketplace)
├── Tempi spedizione per paese (IT: 2-3gg, DE: 3-5gg, FR: 3-5gg, etc.)
├── FAQ commissioni/fatturazione
├── Info garanzia prodotti
├── Procedura reso per marketplace
├── Contatti e orari support
└── Info azienda Vendiamonoi
```

### Limitazioni Ava

- Disponibile principalmente per webstore (Shopify, custom)
- NON attivo direttamente su buyer-seller messaging marketplace (Amazon, eBay)
- Per marketplace: usare HandsFree + Message Rules + Templates
- Pricing basato su volume conversazioni (add-on separato)

---

## Knowledge Base Self-Service

### Overview

eDesk include un Knowledge Base builder per creare help center customer-facing, riducendo il volume ticket fino al 30%.

### Funzionalità

| Aspetto | Dettaglio |
|---|---|
| **Articoli** | Editor rich text, categorie, tag, SEO |
| **Design** | Layout personalizzabile, logo, colori brand |
| **Mobile** | Responsive su tutti i device |
| **Ricerca** | Full-text search per clienti |
| **Submit ticket** | Form integrato per escalation a ticket |
| **Multilingua** | Template CSAT in più lingue |
| **Integrazione Ava** | KB utilizzata come source per Ava Content Hub |

### Uso per Vendiamonoi

- Help center per webstore Shopify (se attivo)
- Source per Ava AI Chatbot
- Riduce ticket ripetitivi (tracking, reso, fattura)

---

## CSAT (Customer Satisfaction)

### Configurazione

| Aspetto | Dettaglio |
|---|---|
| **Attivazione** | Settings → Mailbox Settings → Channels → Customer Satisfaction tab |
| **Canali** | Email e Live Chat |
| **Timing** | Configurabile: X giorni dopo risoluzione ticket |
| **Lingua** | Auto-detect lingua ticket, template multilingua |
| **Piano richiesto** | Performance+, Professional, Enterprise |

### Reporting CSAT

- Dashboard: Insights → Other → Customer Satisfaction
- Toggle tra Tickets e Chat insights
- CSAT medio per agente, per canale, per periodo
- Export CSV per analisi avanzata

---

## SLA Management Marketplace ⭐

### SLA per Marketplace (Requisiti)

| Marketplace | Tempo Risposta | Penalizzazione | Note |
|---|---|---|---|
| **Amazon** | 24h (inclusi weekend e festivi) | Performance notice se <90% in 30gg | Impatta Buy Box, account health |
| **eBay** | 24-48h | Feedback negativo, riduzione visibilità | Nessuna penalizzazione automatica diretta |
| **Kaufland** | 48h | Impatta seller metrics | — |
| **Bol.com** | 24h | Impatta service level | Mercato NL/BE |
| **Cdiscount** | 48h | Impatta score venditore | — |
| **Fnac-Darty** | 48h | Impatta visibilità | — |
| **Mirakl marketplace** | 48h (default) | Varia per marketplace | 300+ marketplace |

### Come Funziona il Timer SLA in eDesk

1. **Start**: il timer parte quando eDesk riceve il messaggio cliente
2. **Stop**: il timer si ferma quando l'agente invia una risposta
3. **Auto-reply**: le auto-reply fermano il timer SLA (fondamentale per fuori orario)
4. **Escalation**: se il timer si avvicina alla scadenza, eDesk può escalare automaticamente
5. **Breach**: se il timer scade, il ticket viene segnalato come SLA breach

### Configurazione SLA in eDesk

- Settings → SLA Targets
- Configura target per marketplace/canale
- Alert prima della scadenza
- Report SLA compliance per canale e agente

### Strategia SLA per Vendiamonoi

```
PRIORITÀ MASSIMA (24h):
  → Amazon (tutti i mercati) → response rate >90% obbligatorio
  → Bol.com → 24h target

PRIORITÀ ALTA (48h):
  → eBay, Kaufland, Cdiscount, Fnac-Darty, Mirakl

STRATEGIA FUORI ORARIO:
  → Message Rule: ora 19:00-08:00 OR weekend
  → Auto-reply template "Fuori orario, risponderemo entro 24h"
  → FERMA il timer SLA su tutti i marketplace
  → Previene SLA breach notturni/weekend

TARGET INTERNO:
  → First Response Time: <4h (orario lavorativo)
  → Resolution Time: <24h
  → SLA Compliance: >95%
  → CSAT: >4.5/5
```

---

## Automazione e Workflow

### Message Rules

Sistema rule-based per automazione ticket.

**Struttura:**
```
QUANDO: [trigger/condizione]
  → fonte marketplace
  → lingua messaggio
  → valore ordine
  → prodotto specifico
  → parola chiave
  → sentiment (positivo/neutro/negativo)
  → AI classification

ALLORA: [azione]
  → Assegna a agente/team
  → Applica tag
  → Applica template
  → Invia a webhook URL
  → Cambia priorità
  → Sposta in cartella
```

### Message Rules Raccomandate per Vendiamonoi

| # | Nome | Condizione | Azione |
|---|---|---|---|
| 1 | Auto-Tag Marketplace | Canale = Amazon IT | Tag "MKP-Amazon-IT" |
| 2 | Auto-Tag Marketplace | Canale = eBay | Tag "MKP-eBay" |
| 3 | Auto-Tag Marketplace | Canale = Kaufland | Tag "MKP-Kaufland" |
| 4 | Escalation Negativo | Sentiment = Negativo | Tag "PRI-Urgente" → Assegna Team Leader |
| 5 | VIP Orders | Valore ordine > €500 | Tag "PRI-VIP" → Priorità alta |
| 6 | Fuori Orario | Ora 19-08 OR weekend | Auto-reply "Fuori orario" (ferma SLA) |
| 7 | HandsFree Tracking | AI Class = "Delivery issue with tracking" | HandsFree → Template tracking |
| 8 | Resi Auto | AI Class = "Return request" | Tag "QRY-Reso" → Template reso |
| 9 | Lingua DE | Lingua = DE | Tag "LANG-DE" → Assegna Team EU |
| 10 | Lingua FR | Lingua = FR | Tag "LANG-FR" → Assegna Team EU |
| 11 | Fattura | Parola chiave "fattura" OR "invoice" | Tag "QRY-Fattura" → Template fattura |
| 12 | Webhook Supabase | Qualsiasi nuovo ticket | Webhook → Make.com → Supabase |

### Template Types

| Tipo | Uso | HandsFree |
|---|---|---|
| **Manuale** | Selezionato dall'agente | ✅ Compatibile |
| **Auto-Reply** | Risposta automatica fuori orario/volumi alti | ❌ No |
| **Out of Office** | Chiusure programmate | ❌ No |
| **Rule-Only** | Attivato solo da Message Rules | ❌ No |
| **AI-Selected** | Selezionato automaticamente da AI | ✅ Compatibile |

### Snippets

- **Company Snippets**: creati da admin, disponibili per tutto il team
- **Personal Snippets**: creati da singolo utente
- **Inserimento**: digita `#` nel campo risposta per lista snippets
- **Variabili dinamiche**: nome cliente, numero ordine, data spedizione, indirizzo, tracking

### Struttura Tag Raccomandata

```
📦 MARKETPLACE
  MKP-Amazon-IT, MKP-Amazon-DE, MKP-Amazon-FR, MKP-Amazon-ES
  MKP-eBay, MKP-Kaufland, MKP-FnacDarty, MKP-LeroyMerlin
  MKP-Cdiscount, MKP-ManoMano, MKP-Bol, MKP-Other

🔄 TIPO QUERY
  QRY-Tracking, QRY-Reso, QRY-Rimborso, QRY-Prodotto
  QRY-Spedizione, QRY-Cancellazione, QRY-Fattura, QRY-Altro

⚡ PRIORITÀ
  PRI-Urgente (sentiment negativo)
  PRI-VIP (ordine > €500)
  PRI-Normale

🌍 LINGUA
  LANG-IT, LANG-DE, LANG-FR, LANG-ES, LANG-EN, LANG-NL, LANG-PL

🤖 AUTOMAZIONE
  AUTO-HandsFree (risolto da AI)
  AUTO-Template (risolto da template)
  AUTO-Escalated (escalato a umano)
```

---

## Gestione Resi e Rimborsi

### Workflow Resi con eDesk

```
1. RICHIESTA RESO
   Cliente invia messaggio → eDesk inbox
   → AI Classification: "Return request"
   → Message Rule: Tag "QRY-Reso"
   → [HandsFree]: se reso standard, auto-risposta con procedura
   → [Agente]: se reso complesso, gestione manuale

2. VERIFICA
   Agente verifica:
   → Ordine dentro finestra reso (14gg EU, o policy marketplace)
   → Prodotto idoneo (no personalizzati, deperibili, etc.)
   → Storico cliente (flag se alto tasso reso = possibile fraud)

3. AUTORIZZAZIONE
   Agente autorizza reso:
   → Template con istruzioni spedizione reso
   → Etichetta reso (se prevista)
   → Tracking reso

4. RIMBORSO
   Prodotto ricevuto e verificato:
   → Rimborso tramite marketplace
   → Nota di credito in Fatture in Cloud (via Make.com)
   → Aggiornamento Supabase
   → Ticket chiuso
```

### Automazione Resi (80% automatizzabile)

eDesk permette di automatizzare l'80% delle query resi con:
- Template pre-configurati per ogni marketplace
- Smart Rules: IF ordine marked 'Delivered' AND motivo reso = 'Damaged' THEN invia link sostituzione
- Fraud detection: flag automatico per clienti con alto tasso reso
- Integrazione RMA: ReturnLogic, AfterShip, Loop

---

## Team Management

### Ruoli

| Ruolo | Permessi |
|---|---|
| **Admin** | Tutto: impostazioni, configurazione, API, gestione utenti |
| **Team Leader** | Smart Tools (Message Rules, template, tag, AI, snippets), mailbox views team, chat, KB |
| **Support Agent** | Gestione propri ticket, risposte, template, snippets |

### Assegnazione Ticket

| Metodo | Funzione |
|---|---|
| **Round-Robin** | Rotazione automatica, distribuzione equa workload |
| **By Roles** | Assegna a tutti gli utenti con un ruolo specifico |
| **By Users** | Assegna a utenti specifici |
| **Rule-Based** | Via Message Rules (marketplace, lingua, etc.) |
| **Disponibilità** | Orari configurabili, esclusione date ferie |

### Collaborazione

| Funzionalità | Dettaglio |
|---|---|
| **@mention** | Coinvolgi colleghi nel ticket |
| **Internal Share** | Condividi con utenti non-eDesk |
| **Internal Notes** | Note visibili solo al team |
| **Collision Detection** | Previene lavoro duplicato sullo stesso ticket |

---

## API v1

### Panoramica

| Aspetto | Dettaglio |
|---|---|
| **Base URL** | `https://api.edesk.com/v1/` |
| **Formato** | JSON |
| **Autenticazione** | Bearer Token (generato da Settings → API Tokens) |
| **Piano richiesto** | Professional+ per accesso API |
| **Documentazione** | https://developers.edesk.com/reference |

### Autenticazione

```bash
Authorization: Bearer {api_token}
Content-Type: application/json
```

### Mappa Completa Endpoint

#### Tickets

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `GET` | `/v1/tickets` | Lista ticket (filtri: status, page, per_page) |
| `GET` | `/v1/tickets/{ticket_id}` | Dettaglio ticket |
| `POST` | `/v1/tickets` | Crea ticket |
| `PUT` | `/v1/tickets/{ticket_id}` | Aggiorna ticket |

#### Messages

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `POST` | `/v1/tickets/{ticket_id}/messages` | Crea messaggio in thread |
| `GET` | `/v1/tickets/{ticket_id}/messages/{message_id}` | Visualizza messaggio |
| `PUT` | `/v1/tickets/{ticket_id}/messages/{message_id}` | Aggiorna messaggio |

#### Orders

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `GET` | `/v1/orders` | Lista ordini |
| `POST` | `/v1/orders` | Crea ordine |
| `GET` | `/v1/orders/{order_id}` | Dettaglio ordine |
| `PUT` | `/v1/orders/{order_id}` | Aggiorna ordine |
| `POST` | `/v1/orders/{order_id}/tracking` | Aggiungi tracking |

#### Tags

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `GET` | `/v1/tags` | Lista tag |
| `POST` | `/v1/tags` | Crea tag |
| `PUT` | `/v1/tags/{tag_id}` | Aggiorna tag |
| `DELETE` | `/v1/tags/{tag_id}` | Elimina tag |

#### Templates

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `GET` | `/v1/templates` | Lista template |
| `POST` | `/v1/templates` | Crea template |
| `PUT` | `/v1/templates/{template_id}` | Aggiorna template |
| `DELETE` | `/v1/templates/{template_id}` | Elimina template |

#### Custom Fields

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `GET` | `/v1/custom_fields` | Lista custom fields |
| `PUT` | `/v1/custom_fields/{field_id}` | Aggiorna custom field |

#### Channels

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `GET` | `/v1/channels` | Lista canali |
| `POST` | `/v1/channels` | Crea canale |

#### Customers

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `GET` | `/v1/customers/{customer_id}` | Visualizza cliente |

#### Reports

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `GET` | `/v1/reports` | Lista report |
| `POST` | `/v1/reports` | Crea report |

### Webhooks

Configurati tramite Message Rules. Quando una regola si attiva, invia POST al webhook URL.

**Payload:**
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

**IMPORTANTE**: Il webhook contiene solo metadata (no contenuto messaggio). Per il contenuto completo: `GET /v1/tickets/{ticket_id}` dopo ricezione webhook.

---

## Integrazione con Stack Vendiamonoi

### Architettura

```
20-30+ Marketplace
    ↓ messaggi buyer-seller
eDesk (Smart Inbox + AI)
    ↓ webhook (via Message Rules)
Make.com (automazione)
    ↓ sync dati
Supabase (database centrale)
    ↓ analytics + reporting
    
SellRapido → Make → eDesk API (sync ordini + tracking)
Fatture in Cloud → Make → eDesk (nota interna link fattura)
```

### Scenari Make.com per eDesk

#### Scenario 1: Sync Ordini SellRapido → eDesk

```
Trigger: SellRapido webhook (nuovo ordine confermato)
→ Make: estrai dati ordine (ID, prodotti, indirizzo, marketplace)
→ eDesk API: POST /v1/orders (crea ordine in eDesk)
→ Supabase: registra sync
```

#### Scenario 2: Tracking Update → eDesk

```
Trigger: SellRapido/corriere webhook (tracking aggiornato)
→ Make: estrai tracking number + carrier
→ eDesk API: POST /v1/orders/{id}/tracking
→ Risultato: tracking visibile nel ticket, previene domande ripetitive
```

#### Scenario 3: Ticket → Supabase (Analytics)

```
Trigger: eDesk webhook (Message Rule: nuovo ticket)
→ Make: ricevi payload
→ Make: GET /v1/tickets/{id} (contenuto completo)
→ Supabase: INSERT ticket_log (marketplace, tipo, sentiment, timestamp)
→ Dashboard: analytics CS per marketplace
```

#### Scenario 4: Richiesta Fattura → Fatture in Cloud

```
Trigger: eDesk webhook (Message Rule: tag "QRY-Fattura")
→ Make: estrai dati ordine da eDesk
→ Make: cerca ordine in Supabase
→ FiC API: create_issued_document (type=invoice)
→ FiC API: send_e_invoice (invio SDI)
→ eDesk API: POST /v1/tickets/{id}/messages (nota interna: "Fattura #X emessa")
```

#### Scenario 5: Export KPI Settimanale

```
Trigger: Schedulato (ogni lunedì)
→ eDesk API: GET /v1/tickets?status=closed&date_from=...
→ Make: calcola KPI (response time, resolution time, SLA breach)
→ Google Sheets: dashboard CS settimanale
→ [Opzionale] Email/Slack: report al team
```

### Sincronizzazione con Supabase

#### Tabella `edesk_tickets`

```sql
CREATE TABLE edesk_tickets (
  id BIGINT PRIMARY KEY,
  edesk_ticket_id TEXT UNIQUE NOT NULL,
  marketplace TEXT NOT NULL,
  channel_id TEXT,
  customer_email TEXT,
  customer_name TEXT,
  subject TEXT,
  status TEXT,  -- open, pending, resolved, closed
  ai_classification TEXT,
  sentiment TEXT,  -- positive, neutral, negative
  priority TEXT,
  assigned_agent TEXT,
  tags TEXT[],
  order_id TEXT,  -- link a ordine marketplace
  first_response_time_seconds INTEGER,
  resolution_time_seconds INTEGER,
  sla_breached BOOLEAN DEFAULT FALSE,
  handsfree_resolved BOOLEAN DEFAULT FALSE,
  csat_score DECIMAL(2,1),
  language TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  resolved_at TIMESTAMPTZ,
  raw_data JSONB
);

CREATE INDEX idx_edesk_tickets_marketplace ON edesk_tickets(marketplace);
CREATE INDEX idx_edesk_tickets_status ON edesk_tickets(status);
CREATE INDEX idx_edesk_tickets_classification ON edesk_tickets(ai_classification);
CREATE INDEX idx_edesk_tickets_sentiment ON edesk_tickets(sentiment);
CREATE INDEX idx_edesk_tickets_created ON edesk_tickets(created_at);
```

#### Vista Analytics

```sql
CREATE VIEW v_cs_analytics AS
SELECT
  marketplace,
  DATE_TRUNC('week', created_at) AS week,
  COUNT(*) AS total_tickets,
  COUNT(*) FILTER (WHERE handsfree_resolved) AS auto_resolved,
  ROUND(AVG(first_response_time_seconds)/60) AS avg_frt_minutes,
  ROUND(AVG(resolution_time_seconds)/3600, 1) AS avg_resolution_hours,
  COUNT(*) FILTER (WHERE sla_breached) AS sla_breaches,
  ROUND(100.0 * COUNT(*) FILTER (WHERE NOT sla_breached) / COUNT(*), 1) AS sla_compliance_pct,
  ROUND(AVG(csat_score), 2) AS avg_csat,
  COUNT(DISTINCT assigned_agent) AS agents_active
FROM edesk_tickets
GROUP BY marketplace, DATE_TRUNC('week', created_at)
ORDER BY week DESC, marketplace;
```

---

## Reporting e KPI

### Dashboard eDesk

| Report | Metriche |
|---|---|
| **SLA Compliance** | % rispetto SLA per canale/marketplace |
| **CSAT Score** | Soddisfazione media e per agente |
| **First Response Time** | Tempo medio prima risposta |
| **Resolution Time** | Tempo medio risoluzione |
| **Ticket Volume** | Volume per canale/periodo |
| **Agent Performance** | Velocità, volume, CSAT per agente |
| **HandsFree** | Ticket risolti da AI vs umano |
| **Languages** | Distribuzione per lingua |
| **Tags** | Distribuzione per tag |
| **AI Resolutions** | Efficacia automazione |
| **eBay Cases** | Casi eBay specifici |

### KPI Target per Vendiamonoi

| KPI | Target | Frequenza Monitoraggio |
|---|---|---|
| SLA Compliance Amazon | >95% | Giornaliera |
| SLA Compliance globale | >90% | Settimanale |
| First Response Time | <4h (orario lavorativo) | Settimanale |
| Resolution Time | <24h | Settimanale |
| CSAT Score | >4.5/5 | Mensile |
| HandsFree Resolution Rate | >40% | Mensile |
| Ticket/Agente/Giorno | <50 | Settimanale |
| SLA Breaches | <5% | Giornaliera |

---

## Errori Comuni da Evitare

### 1. Non Configurare Auto-Reply per Fuori Orario

**Errore**: Non avere auto-reply attive la sera e nel weekend.
**Conseguenza**: SLA breach su Amazon (24h inclusi weekend!) e altri marketplace.
**Soluzione**: Message Rule ora 19-08 + weekend → auto-reply template → ferma timer SLA.

### 2. Usare HandsFree su Canali Non Supportati

**Errore**: Aspettarsi che HandsFree funzioni su eBay cases e ticket Mirakl.
**Conseguenza**: Ticket non risolti, SLA breach.
**Soluzione**: Per eBay/Mirakl, usare Message Rules + template auto-reply.

### 3. Non Strutturare i Tag

**Errore**: Tag creati ad-hoc senza convenzione.
**Conseguenza**: Reporting inutilizzabile, impossibile filtrare per marketplace/tipo.
**Soluzione**: Prefissi standard (MKP-, QRY-, PRI-, LANG-, AUTO-).

### 4. Ignorare il Webhook Metadata-Only

**Errore**: Aspettarsi contenuto messaggio completo nel webhook.
**Conseguenza**: Automazioni Make.com che non hanno i dati necessari.
**Soluzione**: Webhook → ricevi ticket_id → GET /v1/tickets/{id} per contenuto completo.

### 5. Non Sincronizzare Ordini

**Errore**: Non pushare ordini da SellRapido a eDesk via API.
**Conseguenza**: Agenti senza dati ordine nel ticket, tempi risposta maggiori.
**Soluzione**: Make.com scenario: SellRapido webhook → eDesk POST /v1/orders.

### 6. Non Monitorare SLA per Marketplace

**Errore**: Guardare solo SLA globale senza breakdown per marketplace.
**Conseguenza**: Amazon potrebbe avere 85% SLA (sotto soglia) mentre il globale è 92%.
**Soluzione**: Report SLA per canale, alert specifici per Amazon (soglia >90%).

### 7. Ava Solo su Webstore

**Errore**: Pensare che Ava funzioni su buyer-seller messaging di Amazon/eBay.
**Chiarimento**: Ava funziona su webstore (Shopify, custom). Per marketplace: HandsFree.

---

## Sicurezza e Compliance

| Aspetto | Dettaglio |
|---|---|
| **GDPR** | Conforme (azienda irlandese, soggetta a GDPR EU) |
| **Data hosting** | Cloud EU |
| **Autenticazione** | Token-based API, credenziali utente per piattaforma |
| **Ruoli** | Admin, Team Leader, Support Agent con permessi granulari |
| **Audit** | User logs nei report |
| **Encryption** | 128-bit SSL |

---

## Fonti e Riferimenti

### Documentazione Ufficiale

- [eDesk Homepage](https://www.edesk.com)
- [Pricing](https://www.edesk.com/pricing)
- [Developer Portal](https://developers.edesk.com)
- [API Reference](https://developers.edesk.com/reference)
- [API Authentication](https://developers.edesk.com/reference/authentication)
- [Help Center](https://support.edesk.com)
- [Getting Started API](https://support.edesk.com/getting-started-with-the-edesk-api)
- [API Use Cases](https://support.edesk.com/4-ways-use-edesk-api)
- [Token Generation](https://support.edesk.com/how-to-generate-a-token-edesk-api)

### Feature Documentation

- [Smart Inbox](https://www.edesk.com/smart-inbox)
- [AI Features](https://www.edesk.com/ai)
- [Ava AI Chatbot](https://www.edesk.com/ava-ai-ecommerce-chatbot/)
- [Ava Content Hub](https://support.edesk.com/building-a-content-hub)
- [Ava Setup](https://support.edesk.com/step-by-step-chatbot-setup)
- [Knowledge Base](https://www.edesk.com/knowledge-base/)
- [Knowledge Base Setup](https://support.edesk.com/edesk-knowledge-base)
- [Message Rules](https://support.edesk.com/message-rules)
- [HandsFree](https://support.edesk.com/handsfree-automation)
- [Templates](https://support.edesk.com/templates)
- [Snippets](https://support.edesk.com/snippets)
- [CSAT Setup](https://support.edesk.com/csat)
- [CSAT Chatbot](https://support.edesk.com/csat-for-chatbots)
- [SLA Targets](https://support.edesk.com/setting-sla-targets)
- [AI Assist](https://support.edesk.com/ai-assist)
- [AI Assist with Profiles](https://support.edesk.com/ai-assist-features-with-profiles)

### Integrazioni Marketplace

- [Amazon Integration](https://support.edesk.com/amazon)
- [eBay Integration](https://support.edesk.com/ebay)
- [Kaufland Integration](https://support.edesk.com/connecting-kaufland-with-edesk)
- [Mirakl Integration](https://support.edesk.com/edesk-mirakl-integration)
- [Integrations Hub](https://support.edesk.com/integrations)
- [App Store](https://www.edesk.com/appstore)

### SLA e Best Practice

- [Amazon Messaging SLA](https://www.edesk.com/blog/amazon-message-system/)
- [Amazon vs eBay CS Requirements](https://www.edesk.com/blog/amazon-vs-ebay-customer-service/)
- [SLA Differentiation](https://www.edesk.com/blog/sla-differentiation-amazon-vs-shopify/)
- [Agent SLA Tracking](https://www.edesk.com/blog/agent-sla-tracking-software-for-marketplaces/)
- [Reduce Response Times](https://www.edesk.com/blog/reduce-customer-service-response-times/)
- [Returns Automation](https://www.edesk.com/blog/how-to-automate-product-returns/)
- [Refund Workflow](https://www.edesk.com/blog/streamline-refund-request-workflow/)
- [Knowledge Base Best Practice](https://www.edesk.com/blog/building-knowledge-base-reduce-support-tickets/)
- [Mirakl API Integration](https://www.edesk.com/blog/mirakl-api/)
