# Make.com Blueprints — Architettura Automazioni Vendiamonoi.it
## EXPERT-LEVEL TECHNICAL DEEP-DIVE

**Documento**: Architettura completa blueprint Make.com per distribuzione digitale multi-marketplace
**Target**: CTO, Automation Architects, Senior Engineers — Vendiamonoi.it
**Versione**: 2026
**Ultimo aggiornamento**: 2026-04-07
**Complemento a**: architecture/make/ (Part 1-4), architecture/sellrapido/, architecture/supabase/

---

## INDICE COMPLETO

1. [Inventario Scenari Reali Vendiamonoi](#1-inventario-scenari-reali-vendiamonoi)
   - 1.1 Overview Account Make.com
   - 1.2 Scenari per Area Funzionale
   - 1.3 Struttura Cartelle Attuale
   - 1.4 Analisi Gap e Raccomandazioni
2. [Blueprint JSON — Anatomia Completa](#2-blueprint-json--anatomia-completa)
   - 2.1 Struttura Root
   - 2.2 Flow Array e Modules
   - 2.3 Routes, Filters, Metadata
   - 2.4 Scheduling Configuration
   - 2.5 Connection Mapping
3. [Design Pattern per E-Commerce Marketplace](#3-design-pattern-per-e-commerce-marketplace)
   - 3.1 Order Processing (Webhook → Validate → Route → Fulfill)
   - 3.2 Stock Synchronization (Schedule → Fetch → Compare → Update)
   - 3.3 Catalog Management (Trigger → Enrich → Transform → Publish)
   - 3.4 Price Update (Change → Calculate → Push)
   - 3.5 Invoice Automation (FIC → Supabase → Reconciliation)
   - 3.6 Customer Service Automation
4. [Architecture Best Practices](#4-architecture-best-practices)
   - 4.1 Naming Convention
   - 4.2 Folder Organization
   - 4.3 Error Handling Patterns
   - 4.4 Data Stores (Cache, State, Dedup)
   - 4.5 Webhook Design e Security
   - 4.6 Router + Filter Patterns
   - 4.7 Iterator + Aggregator per Batch
   - 4.8 Sub-Scenario / RPC Pattern
   - 4.9 Rate Limiting e Throttling
5. [Integrazione Supabase](#5-integrazione-supabase)
   - 5.1 HTTP Module → Supabase REST API
   - 5.2 Pattern Sync Incrementale
   - 5.3 Bulk Operations
   - 5.4 Real-Time Event Processing
6. [Integrazione SellRapido](#6-integrazione-sellrapido)
   - 6.1 Order Flow Completo
   - 6.2 Stock Flow
   - 6.3 Tracking Upload
   - 6.4 API SellRapido via HTTP Module
7. [Blueprint Management](#7-blueprint-management)
   - 7.1 Version Control (GitHub)
   - 7.2 Export/Import
   - 7.3 Environment Management (Dev/Staging/Prod)
   - 7.4 Validation e Testing Checklist
8. [Scenari Target — Roadmap Automazioni](#8-scenari-target--roadmap-automazioni)

---

# 1. Inventario Scenari Reali Vendiamonoi

## 1.1 Overview Account Make.com

| Parametro | Valore |
|-----------|--------|
| **Organizzazione** | My Organization (ID: 1739040) |
| **Piano** | Pro |
| **Team** | My Team (ID: 596362) |
| **Zona** | eu2.make.com |
| **Operazioni/mese** | 120.000 |
| **Scenari totali** | 36 |
| **Cartelle** | 16 |
| **Utenti attivi** | 3 (Alessio Baggiani, Canice Olekamma, Camilla Beggiato) |
| **Scenari attivi** | ~5 (la maggior parte disattivati) |
| **Esecuzioni totali significative** | SellRapido → Supabase Ordini (107 exec, 6141 ops) |

## 1.2 Scenari per Area Funzionale

### 📦 Ordini & Marketplace

| ID | Nome | Moduli | Scheduling | Exec | Stato |
|----|------|--------|-----------|------|-------|
| 8856551 | **SellRapido → Supabase \| Ordini Marketplace** | http, builtin, supabase | Ogni 15min | 107 | ⚠️ Off |
| 8867890 | **SellRapido → Supabase \| Aggiornamento Stati Ordini** | http, builtin, supabase | Daily 05:00 | 1 | ⚠️ Off |
| 5798189 | **SellRapido x Shopify - Creazione Ordini Sito** | http, builtin, util, google-sheets, bitrix24 | Ogni 15min (Lun-Sab 06:15-22:15) | 1 | ❌ Invalid |
| 6128585 | **Accettare gli ordini da Shopify per accettarli anche su SellRapido** | shopify, http | Immediately | 0 | ⚠️ Off |

### 🚚 Spedizioni & Logistica

| ID | Nome | Moduli | Scheduling | Exec | Stato |
|----|------|--------|-----------|------|-------|
| 8879057 | **Fulfillment \| Route New Orders** | http | Ogni 15min | 0 | ⚠️ Off |
| 8879067 | **Fulfillment \| Email Order Sender** | http, builtin, email | Ogni 15min | 0 | ⚠️ Off |
| 8879070 | **Fulfillment \| Tracking Upload SellRapido** | http, builtin | Ogni 15min | 0 | ⚠️ Off |
| 7567286 | **Tracking Spedizioni SpediamoPro** | http, json, google-sheets, builtin, util, email | Ogni 15min | 0 | ⚠️ Off |

### 💰 Fatturazione & Finance

| ID | Nome | Moduli | Scheduling | Exec | Stato |
|----|------|--------|-----------|------|-------|
| 8871871 | **FIC → Supabase \| Fatture v2** | fatture-in-cloud, http | Daily 06:00 | 16 | ⚠️ Off |
| 8844531 | **FIC → Supabase \| Note di Credito Ricevute** | fatture-in-cloud, http | Daily 06:10 | 0 | ⚠️ Off |
| 6188874 | **Fatture EMESSE (Fatture in cloud)** | fatture-in-cloud, google-sheets | Daily 05:00 | 0 | ⚠️ Off |
| 6382845 | **Fatture RICEVUTE (Fatture in cloud)** | fatture-in-cloud, google-sheets | Daily 06:30 | 0 | ⚠️ Off |
| 6640838 | **Integration Fatture in Cloud** | fatture-in-cloud | Ogni 15min | 0 | ⚠️ Off |
| 6640689 | **Note Credito RICEVUTE (Fatture in cloud)** | fatture-in-cloud, google-sheets | Daily 06:30 | 0 | ⚠️ Off |
| 5614099 | **Integration Qonto, Fatture in Cloud** | qonto, fatture-in-cloud | Ogni 15min | 0 | ⚠️ Off |

### 📊 Reporting & Fornitori

| ID | Nome | Moduli | Scheduling | Exec | Stato |
|----|------|--------|-----------|------|-------|
| 5125071 | **Aziende Partner nel report mensile Fornitura** | google-sheets, builtin, notion | Daily 15:33 | 0 | VECCHIO |
| 5124759 | **Portali Rivendita report Mensile Fornitura** | google-sheets, notion | Daily 15:33 | 0 | ⚠️ Off |
| 5258922 | **Integration Google Sheets, Notion** | google-sheets, notion | Daily 07:50 | 0 | ❌ Invalid |

### 🏢 CRM / Bitrix24

| ID | Nome | Moduli | Scheduling | Exec | Stato |
|----|------|--------|-----------|------|-------|
| 7240824 | **Anagrafica fornitori → Bitrix24 da Notion** | notion, bitrix24 | Ogni 15min | 0 | ⚠️ Off |
| 5027555 | **Bitrix24 - Resi B2B HUB - Affari** | google-sheets, builtin, bitrix24 | Ogni 15min | 0 | VECCHIO |
| 6876625 | **Incarichi Bitrix24 su NotionCalendar** | bitrix24, notion | Ogni 1h (Lun-Ven) | 0 | ❌ Invalid |
| 7717686 | **Monitoraggio Aggiornamento Stati Bitrix24** | bitrix24, google-sheets, builtin | Ogni 15min | 0 | ⚠️ Off |
| 7719386 | **Monitoraggio Creazione Ordini Bitrix24** | bitrix24, google-sheets | Ogni 15min | 0 | ⚠️ Off |
| 6428015 | **Integration Bitrix24, Google Sheets** | bitrix24, google-sheets | Ogni 15min | 0 | ⚠️ Off |
| 8631030 | **Integration Bitrix24** | bitrix24 | Ogni 15min | 0 | ⚠️ Off |

### 🔧 Utility & Diagnostica

| ID | Nome | Moduli | Scheduling | Exec | Stato |
|----|------|--------|-----------|------|-------|
| 8845166 | **DIAGNOSTICA — SellRapido Channel Codes** | http, builtin | On-demand | 3 | ⚠️ Off |
| 1481934 | **Alessio Automation** | email, regexp, util, builtin, json | Ogni 15min | 0 | ⚠️ Off |

### 🛍️ E-Commerce

| ID | Nome | Moduli | Scheduling | Exec | Stato |
|----|------|--------|-----------|------|-------|
| 8631045 | **Integration Shopify** | shopify | Ogni 15min | 0 | ⚠️ Off |
| 6241009 | **Scarico fatture inviate fatture in cloud** | fatture-in-cloud, google-sheets | Ogni 15min | 0 | ⚠️ Off |

## 1.3 Struttura Cartelle Attuale

```
Make.com Organization
├── 📦 Ordini & Marketplace (2 scenari)
├── 🚚 Spedizioni & Logistica (3 scenari)
├── 💰 Fatturazione & Finance (2 scenari)
├── 📦 Catalogo & Fornitori (0 scenari) ← VUOTA
├── 📊 Reporting & Analytics (0 scenari) ← VUOTA
├── 🔧 Utility & Manutenzione (0 scenari) ← VUOTA
├── Vendiamonoi.it - Gestione completa (0 scenari) ← VUOTA
├── Bitrix24 (4 scenari)
├── Distribuzione Vendiamonoi.it (2 scenari)
├── eDesk - Webhook (1 scenario)
├── Fatture in cloud (4 scenari)
├── Liquidation Pallet (1 scenario)
├── Notion (3 scenari)
├── SellRapido (1 scenario)
├── Shopify Centralizzazione (0 scenari) ← VUOTA
└── SpediamoPro (1 scenario)
```

### Osservazioni Critiche

1. **Duplicazione cartelle**: Ci sono cartelle vecchie ("Fatture in cloud", "Bitrix24") e nuove ("💰 Fatturazione & Finance"). Serve consolidamento.
2. **4 cartelle vuote** con emoji: 📦 Catalogo, 📊 Reporting, 🔧 Utility, Vendiamonoi.it master — struttura preparata ma non popolata.
3. **Scenari marcati VECCHIO**: `Aziende Partner` e `Bitrix24 - Resi B2B HUB` hanno nomi con "METODO VECCHIO".
4. **3 scenari Invalid**: Connessioni scadute o moduli rotti.
5. **Scenario più attivo**: `SellRapido → Supabase | Ordini Marketplace` (107 esecuzioni, 6141 operazioni) — core del business.

## 1.4 Analisi Gap e Raccomandazioni

| Gap | Priorità | Dettaglio |
|-----|----------|----------|
| Nessun scenario di stock sync attivo | 🔴 CRITICA | Manca sincronizzazione stock fornitore → SellRapido → marketplace |
| Nessun scenario catalogo | 🔴 CRITICA | Cartella 📦 Catalogo vuota — nessun onboarding prodotti automatizzato |
| Fulfillment non attivo | 🟡 ALTA | 3 scenari fulfillment creati ma mai eseguiti |
| Fatturazione v2 con errori | 🟡 ALTA | FIC → Supabase Fatture v2 ha 9 errori su 16 esecuzioni (56% failure) |
| Duplicazione struttura | 🟢 MEDIA | Cartelle legacy vs nuove emoji — consolidare |
| Scenari senza naming convention | 🟢 MEDIA | Nomi inconsistenti ("Integration HTTP", "Integration Bitrix24") |
| Nessun reporting automatizzato | 🟢 MEDIA | Cartella 📊 vuota |
| Nessun error monitoring | 🟡 ALTA | Nessun scenario di alerting per errori |

---

# 2. Blueprint JSON — Anatomia Completa

## 2.1 Struttura Root

Un blueprint Make.com è un export JSON completo dello scenario:

```json
{
  "name": "SellRapido → Supabase | Ordini Marketplace",
  "flow": [...],
  "metadata": {
    "version": 1,
    "scenario": {
      "roundtrips": 1,
      "maxErrors": 3,
      "autoCommit": true,
      "autoCommitTriggerLast": true,
      "sequential": false,
      "confidential": false,
      "dataloss": false,
      "dlq": false
    },
    "designer": {
      "orphans": [],
      "notes": []
    }
  }
}
```

## 2.2 Flow Array e Modules

L'array `flow` contiene tutti i moduli in sequenza:

```json
{
  "flow": [
    {
      "id": 1,
      "module": "http:ActionSendData",
      "version": 3,
      "parameters": {
        "handleErrors": true,
        "useNewZLibDeCompress": true
      },
      "mapper": {
        "url": "https://api.sellrapido.com/v1/orders",
        "method": "get",
        "headers": [
          {"name": "Authorization", "value": "Bearer {{connection.apiKey}}"}
        ],
        "qs": [
          {"name": "from", "value": "{{formatDate(addDays(now; -1); 'YYYY-MM-DD')}}"}
        ]
      },
      "metadata": {
        "designer": {"x": 0, "y": 0},
        "restore": {},
        "expect": []
      }
    },
    {
      "id": 2,
      "module": "builtin:BasicRouter",
      "routes": [
        {
          "flow": [
            {
              "id": 3,
              "module": "supabase:ActionUpsertRow",
              "mapper": {"table": "orders", "data": "..."}
            }
          ],
          "filter": {
            "name": "Ordine valido",
            "conditions": [[{"a": "{{2.status}}", "b": "valid", "o": "text:equal"}]]
          }
        }
      ]
    }
  ]
}
```

### Campi Chiave per Modulo

| Campo | Tipo | Descrizione |
|-------|------|-------------|
| `id` | number | Identificatore unico nel flow |
| `module` | string | Tipo modulo (`http:ActionSendData`, `supabase:ActionUpsertRow`, `builtin:BasicRouter`) |
| `version` | number | Versione del modulo |
| `parameters` | object | Configurazione statica (handleErrors, connection, etc.) |
| `mapper` | object | Mapping dati dinamici (URL, body, headers, IML expressions) |
| `metadata` | object | Posizione designer, restore info |
| `routes` | array | Solo per Router: array di branch con flow e filter |
| `filter` | object | Condizione per esecuzione modulo (pre-filter) |

## 2.3 Routes, Filters, Metadata

### Filtri

```json
{
  "filter": {
    "name": "Solo ordini Amazon",
    "conditions": [
      [
        {"a": "{{1.marketplace}}", "b": "amazon", "o": "text:equal"}
      ]
    ]
  }
}
```

Operatori disponibili: `text:equal`, `text:notEqual`, `text:contain`, `text:notContain`, `text:startsWith`, `text:endsWith`, `text:matchesPattern`, `number:equal`, `number:greater`, `number:less`, `boolean:equal`, `exist`, `notExist`, `array:contains`.

### Metadata Designer

Posizionamento visuale dei moduli:

```json
{
  "metadata": {
    "designer": {
      "x": 300,
      "y": 150,
      "name": "Fetch Orders from SellRapido"
    }
  }
}
```

## 2.4 Scheduling Configuration

Tipi di scheduling disponibili:

```json
// Immediately (webhook-triggered)
{"type": "immediately"}

// Interval fisso
{"type": "indefinitely", "interval": 900}  // ogni 15 minuti

// Daily
{"type": "daily", "time": "06:00"}

// Con restrizioni orarie
{
  "type": "indefinitely",
  "interval": 3600,
  "restrict": [{
    "days": [1, 2, 3, 4, 5],     // Lun-Ven
    "time": ["07:50", "18:45"],   // dalle 7:50 alle 18:45
    "months": [1,2,3,4,5,6,7,8,9,10,11,12]
  }]
}

// On-demand (solo manuale)
{"type": "on-demand"}
```

## 2.5 Connection Mapping

Le connessioni nel blueprint sono referenziate per ID. All'import su altra organizzazione, serve re-mapping:

```json
{
  "parameters": {
    "connection": 12345  // ID connessione specifica
  }
}
```

**CRITICO**: Blueprint import richiede ri-autenticazione di TUTTE le connessioni. Documentare sempre quali connessioni servono.

---

# 3. Design Pattern per E-Commerce Marketplace

## 3.1 Order Processing

### Pattern: Webhook → Validate → Route → Fulfill → Invoice

```
Webhook/Schedule (ordine da SellRapido)
  │
  ▼
┌─────────────────┐
│ HTTP: Fetch     │ GET SellRapido API /orders?from=last_sync
│ Orders          │
└───────┬─────────┘
        │
        ▼
┌─────────────────┐
│ Iterator        │ Split array ordini in bundle singoli
└───────┬─────────┘
        │
        ▼
┌─────────────────┐
│ Router          │
├─ Route 1: Ordine valido (EAN ok, stock ok, prezzo ok)
│  ├─ Supabase: Upsert ordine
│  ├─ Router per marketplace:
│  │  ├─ Amazon → flow specifico
│  │  ├─ eBay → flow specifico
│  │  └─ Altro → flow generico
│  └─ FIC: Genera fattura
└─ Route 2: Ordine invalido
   └─ Log errore + alert
```

### Scenario Reale: `SellRapido → Supabase | Ordini Marketplace` (ID: 8856551)

Questo è lo scenario più eseguito dell'account (107 esecuzioni, 6141 operazioni):

- **Trigger**: Schedule ogni 15 minuti
- **Moduli**: http (fetch da SellRapido API), builtin (router/iterator), supabase (upsert ordini)
- **Flow**: Fetch ordini → Itera → Upsert su Supabase con dedup
- **Errori**: 3 errori su 107 esecuzioni (2.8% failure rate — accettabile)

## 3.2 Stock Synchronization

### Pattern: Schedule → Fetch → Compare → Update

```
Schedule Trigger (ogni 30min-2h)
  │
  ▼
HTTP: GET stock da Fornitore A (API/CSV/FTP)
  │
  ▼
HTTP: GET stock da Fornitore B
  │
  ▼
Code/JSON: Normalizza formato
  │
  ▼
Data Store: Confronta vs stock precedente
  │
  ▼
Router:
├─ Cambiato → HTTP POST Supabase update + HTTP POST SellRapido update
├─ Stock = 0 → Pausa offerta su marketplace
└─ Invariato → Skip
```

**NOTA**: Questo scenario NON esiste ancora nell'account. È il gap più critico identificato.

## 3.3 Catalog Management

### Pattern: Trigger → Enrich → Transform → Publish

```
Trigger (nuovo prodotto in Supabase / webhook fornitore)
  │
  ▼
HTTP: Fetch dettagli prodotto
  │
  ▼
Code: Arricchisci dati
  ├─ Category mapping (Data Store)
  ├─ Calcolo prezzo per marketplace
  ├─ Generazione titolo SEO per lingua
  └─ Validazione EAN (check digit)
  │
  ▼
Iterator: Per ogni marketplace target
  │
  ▼
Router per marketplace:
├─ Amazon → JSON_LISTINGS_FEED format
├─ eBay → Inventory API format
├─ Kaufland → CSV semicolon format
└─ Mirakl → Product + Offer separation
  │
  ▼
HTTP: Push a SellRapido / direct API
  │
  ▼
Supabase: Update stato listing
```

**NOTA**: Anche questo scenario NON esiste. Cartella "📦 Catalogo & Fornitori" vuota.

## 3.4 Price Update

### Pattern: Change Trigger → Calculate → Push

```
Trigger (aggiornamento listino fornitore / cambio commissione)
  │
  ▼
HTTP: Fetch prodotti con prezzo cambiato
  │
  ▼
Iterator: Per ogni prodotto
  │
  ▼
Code: Calcolo prezzo
  │  Formula: (Costo × (1 + Margine)) / (1 - Commissione) × (1 + IVA)
  │
  ▼
Data Store: Fetch commissioni per marketplace
  │
  ▼
Router per marketplace:
├─ Amazon IT (22% IVA, 15% commissione)
├─ Amazon DE (19% IVA, 15% commissione)
├─ eBay IT (22% IVA, 12% commissione)
└─ Kaufland DE (19% IVA, 10% commissione)
  │
  ▼
HTTP: Push prezzi a SellRapido
  │
  ▼
Supabase: Log variazioni prezzo
```

### IML per Calcolo Prezzo

```
{{round(
  (1.costPrice * (1 + 0.30)) / (1 - 1.commissionRate) * (1 + 1.vatRate)
; 2)}}
```

## 3.5 Invoice Automation (FIC → Supabase)

### Scenario Reale: `FIC → Supabase | Fatture v2` (ID: 8871871)

- **Trigger**: Daily alle 06:00
- **Moduli**: fatture-in-cloud (list fatture), http (push a Supabase)
- **Esecuzioni**: 16 (con 9 errori — 56% failure rate)
- **Problema**: Alto tasso di errore, necessita debug

### Pattern Fatturazione Completo

```
Schedule Daily 06:00
  │
  ▼
FIC: List fatture emesse (da ultima sync)
  │
  ▼
Iterator: Per ogni fattura
  │
  ▼
HTTP: Upsert su Supabase (tabella fatture)
  │
  ▼
Error Handler: Break + Retry 3x
  │
  ▼
Aggregator: Conteggio successi/errori
  │
  ▼
Email/Slack: Report giornaliero
```

### Scenario Reale: `FIC → Supabase | Note di Credito Ricevute` (ID: 8844531)

- **Trigger**: Daily alle 06:10
- **Moduli**: fatture-in-cloud, http (x3) — multipli HTTP call per enrichment
- **Pattern**: Fetch note credito → Fetch dettagli → Push Supabase → Riconcilia

## 3.6 Customer Service Automation

### Pattern: eDesk Webhook

Esiste già cartella "eDesk - Webhook" con 1 scenario (Integration Webhooks, ID: 7172310):

```
eDesk Webhook (messaggio cliente)
  │
  ▼
Gateway: Receive webhook
  │
  ▼
Router:
├─ Tipo: Spedizione → Fetch tracking → Auto-respond
├─ Tipo: Reso → Crea ticket → Notifica team
├─ Tipo: Prodotto → Fetch info prodotto → Template response
└─ Tipo: Escalation → Alert management
```

---

# 4. Architecture Best Practices

## 4.1 Naming Convention

### Formato Raccomandato

```
[Sorgente] → [Destinazione] | [Azione] [Dettaglio]

Esempi reali dall'account (già buoni):
✅ "SellRapido → Supabase | Ordini Marketplace"
✅ "FIC → Supabase | Fatture v2"
✅ "FIC → Supabase | Note di Credito Ricevute"
✅ "Fulfillment | Route New Orders"
✅ "Fulfillment | Email Order Sender"
✅ "Fulfillment | Tracking Upload SellRapido"
✅ "DIAGNOSTICA — SellRapido Channel Codes"

Esempi da migliorare:
❌ "Integration HTTP" → ✅ "Supplier → GSheets | Stock Sync SpediamoPro"
❌ "Integration Bitrix24" → ✅ "Bitrix24 | Watch New Deals"
❌ "Alessio Automation" → ✅ "Email → GSheets | Order Parsing"
```

### Regole

| Elemento | Convenzione | Esempio |
|----------|------------|--------|
| Scenario | `[Source] → [Dest] \| [Action]` | `SellRapido → Supabase \| Ordini` |
| Scenario senza dest | `[Domain] \| [Action]` | `Fulfillment \| Route Orders` |
| Diagnostica | `DIAGNOSTICA — [Descrizione]` | `DIAGNOSTICA — SellRapido Channels` |
| Deprecato | `[DEPRECATO] Nome originale` | `[DEPRECATO] Bitrix24 Resi B2B` |
| Modulo nel flow | Verbo + Oggetto | `Fetch Orders`, `Upsert to Supabase` |
| Data Store | `DS_[Domain]_[Purpose]` | `DS_Orders_Dedup`, `DS_Prices_Cache` |
| Connection | `[Tool]_[Environment]` | `Supabase_PROD`, `FIC_PROD` |

## 4.2 Folder Organization

### Struttura Raccomandata (Consolidamento)

```
Vendiamonoi Make.com
├── 📦 Ordini & Marketplace
│   ├── SellRapido → Supabase | Ordini Marketplace
│   ├── SellRapido → Supabase | Aggiornamento Stati
│   └── Shopify → SellRapido | Sync Ordini
│
├── 🚚 Spedizioni & Logistica
│   ├── Fulfillment | Route New Orders
│   ├── Fulfillment | Email Order Sender
│   ├── Fulfillment | Tracking Upload SellRapido
│   └── SpediamoPro | Tracking Spedizioni
│
├── 💰 Fatturazione & Finance
│   ├── FIC → Supabase | Fatture v2
│   ├── FIC → Supabase | Note di Credito
│   ├── FIC → GSheets | Fatture Emesse (legacy)
│   └── Qonto → FIC | Riconciliazione
│
├── 📦 Catalogo & Fornitori
│   ├── [DA CREARE] Supplier → Supabase | Stock Sync
│   ├── [DA CREARE] Supabase → SellRapido | Catalog Publish
│   └── [DA CREARE] Supplier → Supabase | Price Update
│
├── 📊 Reporting & Analytics
│   ├── [DA CREARE] Daily | Order Summary Report
│   └── [DA CREARE] Monthly | Supplier Performance
│
├── 🏢 CRM & Fornitori
│   ├── Notion → Bitrix24 | Anagrafica Fornitori
│   ├── Bitrix24 | Monitoraggio Stati
│   └── Bitrix24 | Monitoraggio Ordini
│
├── 📨 Customer Service
│   └── eDesk | Webhook Listener
│
├── 🔧 Utility & Diagnostica
│   ├── DIAGNOSTICA — SellRapido Channel Codes
│   └── [DA CREARE] Health Check | Monitor Connessioni
│
└── 🗑️ Archivio (scenari deprecati)
    ├── [DEPRECATO] Aziende Partner report mensile
    ├── [DEPRECATO] Bitrix24 - Resi B2B HUB
    └── [DEPRECATO] Scenari non più in uso
```

## 4.3 Error Handling Patterns

### Pattern Raccomandato per Ogni Scenario

```
Modulo API Call
  │
  ├─ Error Handler: Break
  │   ├─ Auto-retry: enabled
  │   ├─ Tentativi: 3
  │   ├─ Intervallo iniziale: 10 secondi
  │   ├─ Backoff esponenziale: ON (10s → 30s → 90s)
  │   └─ Condizioni: connectionerror, ratelimiterror
  │
  ├─ Se max retry superato:
  │   ├─ Data Store: Log errore con timestamp e dettagli
  │   ├─ Email/Slack: Alert al team
  │   └─ Incomplete Execution: salvata per retry manuale
  │
  └─ Successo: continua flow
```

### Tipi Error Handler per Caso d'Uso

| Caso | Handler | Perché |
|------|---------|--------|
| API marketplace (ordini, stock) | **Break + Retry** | Dato critico, deve arrivare |
| Notifica email/Slack | **Resume** | Non critico, workflow deve continuare |
| Operazione irrecuperabile | **Rollback** | Annulla tutto il bundle, mantieni consistenza |
| Log/analytics | **Ignore** | Skip e continua, nessun dato perso |
| Webhook queue processing | **Commit** | Salva quanto fatto, continua col prossimo |

### Incomplete Executions

- Abilitare "Allow storing of incomplete executions" su OGNI scenario production
- Le esecuzioni fallite vanno in coda
- Intervento manuale: fix problema → resume
- Previene perdita dati su failure temporanei

## 4.4 Data Stores — Cache, State, Deduplication

### Pattern 1: Cache API (Ridurre Chiamate)

```
Data Store: DS_Cache_MarketplaceFees
Schema:
  - key (text, unique): marketplace_id
  - commission_rate (number)
  - vat_rate (number)
  - cached_at (date)
  - expires_at (date)

Logica:
1. Prima di calcolo prezzo: cerca in DS_Cache
2. Se trovato E non scaduto: usa valore cached
3. Se scaduto/non trovato: fetch da API, aggiorna cache

TTL consigliati:
  - Commissioni marketplace: 7 giorni
  - Tassi cambio: 1 ora
  - Category mappings: 24 ore
  - Dati cliente: 12 ore
  - Stock fornitore: 15-30 minuti
```

### Pattern 2: Deduplicazione (Prevenire Duplicati)

```
Data Store: DS_Processed_Orders
Schema:
  - order_id (text, unique): ID ordine marketplace
  - processed_at (date): timestamp elaborazione
  - source (text): marketplace di origine

Logica:
1. Ordine arriva
2. Cerca order_id in DS_Processed_Orders
3. Se trovato: SKIP (già elaborato)
4. Se non trovato: elabora + salva record
5. Pulizia schedulata: elimina record > 30 giorni
```

### Pattern 3: State Management (Workflow Multi-Step)

```
Data Store: DS_Workflow_State
Schema:
  - workflow_id (text, unique)
  - current_step (text): "fetching", "validating", "publishing"
  - data_snapshot (text): JSON stato corrente
  - last_update (date)
  - status (text): "pending", "processing", "complete", "failed"
```

## 4.5 Webhook Design e Security

### Architettura Queue (Alta Volume)

**Problema**: Marketplace invia 100 ordini/secondo, Make processa ~30/secondo.

**Soluzione a 2 scenari**:

```
Scenario 1 (Producer) — Webhook Receiver:
  Trigger: Webhook
  Azioni:
    1. Valida payload JSON
    2. Aggiungi a Data Store con status="queued"
    3. Return 200 OK (< 1 secondo)

Scenario 2 (Consumer) — Scheduled Processor:
  Trigger: Schedule ogni 1 minuto
  Azioni:
    1. Search Data Store status="queued" (limit: 10)
    2. Per ogni item:
       a. Update status → "processing"
       b. Elabora (validate, route, fulfill)
       c. Update status → "done" o "failed"
    3. Retry failed con backoff esponenziale
```

### HMAC Verification

Per webhook da servizi che firmano il payload:

```
// Nel filtro dopo il webhook:
{{hmac(1.body; "sha256"; "YOUR_WEBHOOK_SECRET"; "hex")}}
  ==
{{1.headers.`X-Signature`}}
```

### Rate Limits Webhook Make

- Max 30 richieste/secondo per webhook
- Errore 400 "Queue is full" se superato
- Default queue size: 50 richieste
- Soluzione: gateway esterno come buffer

## 4.6 Router + Filter Patterns

### Multi-Marketplace Router

```
Ordine ricevuto
  │
  ▼
Router:
├─ Filter: marketplace == "amazon_it" → Flow Amazon IT
├─ Filter: marketplace == "amazon_de" → Flow Amazon DE
├─ Filter: marketplace == "ebay_it" → Flow eBay IT
├─ Filter: marketplace == "kaufland_de" → Flow Kaufland
├─ Filter: marketplace starts with "mirakl_" → Flow Mirakl generico
└─ Fallback (no filter) → Log marketplace sconosciuto + alert
```

### Error Status Router

```
API Response
  │
  ▼
Router:
├─ Filter: statusCode == 200 → Successo
├─ Filter: statusCode == 429 → Rate limited → Sleep + Retry
├─ Filter: statusCode >= 500 → Server error → Break handler
└─ Fallback → Log + Alert team
```

## 4.7 Iterator + Aggregator per Batch

### Pattern Batch Upload Prodotti

```
Lista 500 prodotti
  │
  ▼
Iterator: Split in bundle singoli
  │
  ▼
Per ogni prodotto:
  ├─ Fetch category mapping (da Data Store cache)
  ├─ Calcola prezzo per marketplace
  ├─ Valida campi obbligatori
  └─ Trasforma in formato marketplace
  │
  ▼
Array Aggregator: Raccogli tutti i prodotti processati
  │
  ▼
HTTP: Singola chiamata batch API (invece di 500 chiamate singole)
```

**Vantaggi**: 1 operazione batch vs 500 singole = risparmio operazioni Make.com.

## 4.8 Sub-Scenario / RPC Pattern

### Sub-Scenari Riutilizzabili Consigliati per Vendiamonoi

| Sub-Scenario | Input | Output | Usato da |
|-------------|-------|--------|----------|
| `Validate_EAN13` | ean (string) | {valid: bool, error: string} | Tutti i flussi catalogo |
| `Calculate_Marketplace_Price` | cost, marketplace_id | {price, margin, fees} | Pricing, catalogo |
| `Transform_To_Marketplace_Format` | product, target_mp | {marketplace_format} | Pubblicazione |
| `Check_Stock_Availability` | sku, warehouse | {available, reserved} | Ordini, stock sync |
| `Generate_Invoice_Data` | order_id | {invoice_data} | Fatturazione |

### Struttura Sub-Scenario

```
Parent Scenario: SellRapido → Supabase | Ordini
  │
  ├─ Call sub-scenario: Validate_Order
  │   Input: {order_json, marketplace}
  │   Output: {valid: true, normalized_order: {...}}
  │
  ├─ Call sub-scenario: Calculate_Marketplace_Price
  │   Input: {cost: 25.00, marketplace: "amazon_it"}
  │   Output: {price: 49.99, margin: 0.28, commission: 7.50}
  │
  └─ Continue main flow...
```

## 4.9 Rate Limiting e Throttling

### Limiti per Marketplace

| API | Limite | Strategia |
|-----|--------|----------|
| SellRapido | Variabile | Batch operations, scheduled sync |
| Amazon SP-API | 500/15min per resource | Batch feeds, interval scheduling |
| eBay API | 50-1000/hr per call type | Sleep 1-2s tra chiamate |
| Supabase | Per endpoint | Batch upsert, connection pooling |
| Fatture in Cloud | Rate limited | Daily sync, cache results |
| Kaufland | 100/min | Queue + scheduled processor |

### Strategie

1. **Sleep Module**: 1-2 secondi tra chiamate API marketplace
2. **Batch Requests**: 1 chiamata bulk invece di N singole
3. **Queue + Scheduled Processing**: Webhook salva in coda, scenario schedulato processa
4. **Cache in Data Store**: Riduce chiamate API del 70-80%
5. **Token Bucket** (avanzato): Data Store come rate limiter con conteggio token

---

# 5. Integrazione Supabase

## 5.1 HTTP Module → Supabase REST API

### Configurazione Base

```
URL: https://<project_ref>.supabase.co/rest/v1/<table_name>
Method: POST / PATCH / GET / DELETE
Headers:
  - Authorization: Bearer <SUPABASE_SERVICE_ROLE_KEY>
  - apikey: <SUPABASE_ANON_KEY>
  - Content-Type: application/json
  - Prefer: resolution=merge-duplicates  (per upsert)
```

### Modulo Nativo Supabase in Make

Make offre moduli nativi Supabase:
- Create Row
- List Rows (con filtri)
- Retrieve Row
- Update Row
- Delete Row
- Arbitrary Authorized API Call

**Scenario reale**: `SellRapido → Supabase | Ordini Marketplace` usa moduli `supabase` nativi.

## 5.2 Pattern Sync Incrementale

```
Data Store: DS_Sync_LastTimestamp
  - entity (unique): "orders", "invoices", "stock"
  - last_sync (date): ultimo sync riuscito

Flow:
1. Fetch DS_Sync_LastTimestamp per entity "orders"
2. HTTP GET SellRapido /orders?from={{last_sync}}
3. Per ogni ordine: Supabase upsert
4. Se tutto ok: update DS_Sync_LastTimestamp = now()
5. Se errore: NON aggiornare timestamp (retry al prossimo ciclo)
```

## 5.3 Bulk Operations

```
Per import cataloghi 10.000+ prodotti:

1. Fetch prodotti da fornitore (paginazione)
2. Iterator: split in batch da 100
3. Per batch:
   HTTP POST https://xyz.supabase.co/rest/v1/products
   Headers: Prefer: resolution=merge-duplicates
   Body: [...100 prodotti...]
4. Aggregator: conteggio successi
5. Log risultato
```

## 5.4 Real-Time Event Processing

```
Supabase Database Webhook (row inserted/updated)
  │
  ▼
Make Custom Webhook
  │
  ▼
Router:
├─ INSERT new order → Trigger fulfillment flow
├─ UPDATE order status → Push status a SellRapido
└─ UPDATE stock = 0 → Pause marketplace listing
```

---

# 6. Integrazione SellRapido

## 6.1 Order Flow Completo

```
Cliente ordina su Amazon/eBay/Marketplace
  │
  ▼
SellRapido (Channel Manager)
  ├─ Riceve ordine
  ├─ Normalizza dati
  └─ Disponibile via API
  │
  ▼
Make.com: Schedule ogni 15min
  ├─ HTTP GET /orders?from=last_sync
  ├─ Iterator: per ordine
  ├─ Supabase: upsert ordine
  ├─ Router per tipo:
  │  ├─ Dropship → Email fornitore con dettagli ordine
  │  └─ Magazzino → Crea picking list
  └─ FIC: Genera fattura
  │
  ▼
Fornitore spedisce
  │
  ▼
Make.com: Fulfillment | Tracking Upload SellRapido
  ├─ Supabase: fetch ordini con tracking
  ├─ HTTP POST SellRapido: update tracking
  └─ SellRapido → Marketplace: tracking visibile al cliente
```

## 6.2 Stock Flow

```
Fornitore (CSV/API/FTP)
  │
  ▼
Make.com: Supplier → Supabase | Stock Sync
  ├─ HTTP GET stock da fornitore
  ├─ Normalizza formato
  ├─ Supabase: confronta vs stock corrente
  ├─ Router:
  │  ├─ Cambiato → Supabase UPDATE + SellRapido API update
  │  ├─ Esaurito → SellRapido: pausa offerta
  │  └─ Invariato → Skip
  └─ SellRapido sincronizza a tutti i marketplace
```

## 6.3 Tracking Upload

### Scenario Reale: `Fulfillment | Tracking Upload SellRapido` (ID: 8879070)

- **Moduli**: http (x3), builtin
- **Flow**: Fetch ordini da Supabase con tracking → Filter solo quelli non sincronizzati → HTTP PUT a SellRapido con tracking_number
- **Stato**: Creato ma non ancora eseguito

## 6.4 API SellRapido via HTTP Module

### Configurazione HTTP

```
Base URL: https://api.sellrapido.com/v1/
Auth: Bearer Token o API Key header

Endpoint principali:
  GET  /orders?from=YYYY-MM-DD&to=YYYY-MM-DD
  GET  /orders/{id}
  PUT  /orders/{id}/tracking
  GET  /products?page=1&limit=100
  PUT  /products/{sku}/stock
  PUT  /products/{sku}/price
```

### Scenario Reale: `DIAGNOSTICA — SellRapido Channel Codes` (ID: 8845166)

- **Esecuzioni**: 3 (552 operazioni, 10MB transfer)
- **Uso**: On-demand per debug dei codici canale SellRapido
- **Moduli**: http (x3), builtin — fetch multipli endpoint per diagnostica

---

# 7. Blueprint Management

## 7.1 Version Control (GitHub)

### Struttura Consigliata nel Repository

```
vendiamonoi-knowledge/
└── automation-flows/
    └── make-blueprints/
        ├── README.md (questo documento)
        ├── ordini/
        │   ├── sellrapido-supabase-ordini_v1.0.json
        │   └── sellrapido-supabase-stati_v1.0.json
        ├── fatturazione/
        │   ├── fic-supabase-fatture_v2.0.json
        │   └── fic-supabase-note-credito_v1.0.json
        ├── fulfillment/
        │   ├── route-new-orders_v1.0.json
        │   ├── email-order-sender_v1.0.json
        │   └── tracking-upload_v1.0.json
        ├── shared/
        │   ├── sub-validate-ean13_v1.0.json
        │   └── sub-calculate-price_v1.0.json
        └── deprecated/
            └── ... scenari archiviati
```

### Naming File Blueprint

```
{slug-scenario}_v{major}.{minor}.json

Esempio: sellrapido-supabase-ordini_v1.2.json
```

## 7.2 Export/Import

### Export

1. Apri scenario in Make
2. Click icona ellipsi nel control panel
3. Seleziona "Export Blueprint"
4. Salva come `blueprint.json`
5. Rinomina con naming convention
6. Push su GitHub

### Import

1. Pagina Scenari → "Create a new scenario"
2. Click ellipsi → "Import Blueprint"
3. Seleziona file `.json`
4. **CRITICO**: Ri-configurare:
   - Ri-autenticare TUTTE le connessioni
   - Aggiornare URL webhook
   - Verificare riferimenti Data Store
   - Test con dati campione

## 7.3 Environment Management

### Setup a 3 Ambienti

| Ambiente | Scopo | Connessioni | Naming |
|----------|-------|-------------|--------|
| **DEV** | Costruzione e test | API keys test | Nessun prefisso speciale |
| **STAGING** | Pre-production | API reali, dati isolati | Scenario clonato |
| **PROD** | Live | Connessioni production | Scenari nelle cartelle 📦🚚💰 |

**Strategia attuale Vendiamonoi**: Singolo ambiente (tutto è PROD). Raccomandazione: creare almeno DEV folder per testing.

## 7.4 Validation e Testing Checklist

### Pre-Deployment

- [ ] Tutti i moduli hanno connessioni valide
- [ ] Nessun modulo orfano (non collegato)
- [ ] Tutti i filtri hanno condizioni valide
- [ ] Error handler configurato su ogni API call (Break + Retry min)
- [ ] Webhook URL corretti
- [ ] Data Store references esistono
- [ ] Espressioni IML sintatticamente corrette
- [ ] Performance: < 5 minuti esecuzione media
- [ ] Costo operazioni stimato e accettabile
- [ ] Test con dati campione superato
- [ ] Logging/notifiche errore configurate
- [ ] Incomplete executions abilitate

### Test Cases

1. **Happy path**: Dati validi, successo atteso
2. **Error path**: API failure, timeout, dati invalidi
3. **Edge cases**: Array vuoti, campi mancanti, caratteri speciali
4. **Rate limits**: Esecuzioni rapide multiple
5. **Cleanup**: Verifica Data Stores non crescono senza limite

---

# 8. Scenari Target — Roadmap Automazioni

## Fase 1: Stabilizzare Esistente (Settimana 1-2)

| Azione | Scenario | Priorità |
|--------|----------|----------|
| Debug errori | FIC → Supabase Fatture v2 (56% failure) | 🔴 |
| Attivare | SellRapido → Supabase Ordini | 🔴 |
| Attivare | SellRapido → Supabase Stati | 🔴 |
| Fix connessioni | 3 scenari invalid | 🟡 |
| Consolidare | Unire cartelle legacy → nuove cartelle emoji | 🟢 |
| Rinominare | Scenari con naming convention | 🟢 |
| Deprecare | Scenari METODO VECCHIO | 🟢 |

## Fase 2: Completare Fulfillment (Settimana 3-4)

| Azione | Scenario | Priorità |
|--------|----------|----------|
| Testare e attivare | Fulfillment \| Route New Orders | 🔴 |
| Testare e attivare | Fulfillment \| Email Order Sender | 🔴 |
| Testare e attivare | Fulfillment \| Tracking Upload | 🔴 |
| Creare | Error Monitoring \| Alert Scenario | 🟡 |

## Fase 3: Stock & Catalogo (Settimana 5-8)

| Azione | Scenario | Priorità |
|--------|----------|----------|
| Creare | Supplier → Supabase \| Stock Sync | 🔴 |
| Creare | Supabase → SellRapido \| Stock Push | 🔴 |
| Creare | Supabase → SellRapido \| Catalog Publish | 🟡 |
| Creare | Supplier → Supabase \| Price Update | 🟡 |

## Fase 4: Reporting & Analytics (Settimana 9-12)

| Azione | Scenario | Priorità |
|--------|----------|----------|
| Creare | Daily \| Order Summary Report | 🟢 |
| Creare | Weekly \| Stock Health Check | 🟢 |
| Creare | Monthly \| Supplier Performance | 🟢 |
| Creare | Monthly \| Marketplace KPI | 🟢 |

---

## FONTI E RIFERIMENTI

### Make.com Documentation
- [Blueprint Help Center](https://help.make.com/blueprints)
- [Scenarios API](https://developers.make.com/api-documentation/api-reference/scenarios-greater-than-blueprints)
- [Error Handling](https://help.make.com/how-to-handle-errors)
- [Break Error Handler](https://help.make.com/break-error-handler)
- [Exponential Backoff](https://help.make.com/exponential-backoff)
- [Data Stores](https://help.make.com/data-stores)
- [Router Module](https://www.make.com/en/help/modules/router)
- [IML Functions](https://help.make.com/general-functions)
- [Webhooks](https://help.make.com/webhooks)
- [Subscenarios](https://www.make.com/en/blog/introducing-subscenarios)

### Integrazioni
- [Supabase Integration](https://www.make.com/en/integrations/supabase)
- [Supabase REST API](https://supabase.com/docs/guides/api)
- [SellRapido Integrations](https://www.sellrapido.com/en/integrations/)
- [Fatture in Cloud API](https://developers.fattureincloud.it/)

### Best Practices
- [Naming Conventions](https://slashrepeat.com/best-method-naming-make-com-scenarios/)
- [Webhook Rate Limits](https://hookdeck.com/webhooks/platforms/how-to-solve-make-com-webhook-rate-limit-errors)
- [HMAC Verification](https://codehooks.io/blog/secure-zapier-make-n8n-webhooks-signature-verification)

---

**Documento creato**: 2026-04-07
**Dati reali da**: Make.com Organization 1739040, Team 596362
**Complemento**: architecture/make/ (Part 1-4), architecture/sellrapido/, architecture/supabase/, data-models/product-information/
