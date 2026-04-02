# Vendiamonoi Knowledge Base

> **Azienda:** Vendiamonoi.it S.R.L. Unipersonale
> **Repository:** Cervello tecnico permanente — documentazione, architettura, specifiche, pattern
> **Responsabile:** Alessio Baggiani
> **Uso primario:** Source of truth per Claude AI nelle sessioni future

---

## Struttura Repository

```
vendiamonoi-knowledge/
├── README.md
├── strategy/                          ← Mappa strategica e competenze (1) 🆕
│   └── master-checklist/              ← 1.000+ punti competenza, 10 domini
├── api-docs/                          ← Documentazione tecnica software aziendali (14)
├── architecture/                      ← Architettura piattaforme deep dive (8)
│   ├── base44/
│   ├── channable/
│   ├── channelengine/
│   ├── make/                          ← 5 file: README + 4 parti expert-level
│   ├── mirakl/
│   ├── n8n/                           ← 4 parti expert-level
│   ├── shopify/
│   └── supabase/
├── automation-flows/                  ← Blueprint operativi e workflow produzione (3)
│   ├── make-blueprints/
│   ├── n8n-workflows/
│   └── cross-platform/
├── research/                          ← Analisi comparative e benchmark (2)
│   ├── marketplace-comparison/
│   └── automation-tools-benchmark/
├── app-design/                        ← Design piattaforma Vendiamonoi (2)
│   ├── requirements/
│   └── architecture/
├── marketplace-specs/                 ← Specifiche tecniche marketplace (20)
├── integrations/                      ← Pattern di integrazione universali (1)
└── data-models/                       ← Standard dati prodotto (1)
```

---

## strategy/ — Mappa Strategica 🆕

| Documento | Righe | Contenuto |
|-----------|-------|-----------||
| Master Checklist | ~1.090 | Mappa competenze strategiche: 1.000+ punti su 10 domini (Marketplace Ops, Pricing, Advertising, Supply Chain, Finance, Customer Service, Technology, Analytics, Growth, Shopify). Ogni punto = area da padroneggiare a livello world-class. Guida lo sviluppo della knowledge base. |

### I 10 Domini della Master Checklist

| # | Dominio | Punti | Focus |
|---|---------|-------|-------|
| 1 | Marketplace Operations & Listing | ~180 | Listing, SEO, immagini, varianti, A+, compliance |
| 2 | Pricing & Competitive Intelligence | ~130 | Dynamic pricing, repricing, Buy Box, intelligence |
| 3 | Advertising & Marketing | ~120 | PPC, display, multi-marketplace ads, attribution |
| 4 | Supply Chain & Logistics | ~80 | Fulfillment, inventory, supplier, logistics, returns |
| 5 | Financial Management | ~60 | Unit economics, cost, cash flow, tax, reconciliation |
| 6 | Customer Service & Reputation | ~70 | Response, reviews, seller metrics, disputes |
| 7 | Technology & Automation | ~130 | ERP, PIM, OMS, API, pipeline, AI/ML, monitoring |
| 8 | Data Analytics & BI | ~80 | Sales, inventory, customer, predictive, dashboards |
| 9 | Growth Strategy & Scaling | ~100 | Expansion, brand, private label, team, compliance |
| 10 | Shopify | ~80 | Store, SEO, apps, payments, markets, headless |

---

## architecture/ — Architettura Piattaforme (8)

### Architetture Standard (1 file ciascuna)

| Software | Righe | Contenuto | Data |
|----------|-------|-----------|------|
| Base44 | 1.138 | Stack React+Vite+Deno, Entity system, SDK, CLI, pricing | 01/04/2026 |
| Channable | 2.214 | Feed management, rules engine, 2500+ channels, PPC, API | 01/04/2026 |
| ChannelEngine | 1.498 | Merchant API v2, Channel API, webhooks, order lifecycle | 01/04/2026 |
| Mirakl | 2.987 | Shop API, Operator API, MCM, order state machine, invoicing | 01/04/2026 |
| Shopify | 2.093 | Admin API REST+GraphQL, Storefront API, Markets, Plus | 01/04/2026 |
| Supabase | 2.166 | PostgreSQL, PostgREST, GoTrue, Realtime, Edge Functions | 01/04/2026 |

### Make.com — Deep Expert Documentation (18.543 righe totali)

| Parte | Righe | Contenuto |
|-------|-------|-----------|
| README.md (overview) | 2.524 | Scenario architecture, bundles/operations, IML overview, data stores |
| Part 1: Core Architecture | 2.772 | Execution engine, bundle model, operation counting, module taxonomy, design patterns |
| Part 2: Connections & Routing | 3.891 | Connection types (OAuth2/API Key/JWT), router patterns, filter logic, iterator/aggregator |
| Part 3: IML & Data Stores | 4.378 | IML complete reference, data stores CRUD, webhooks, CSV/XML/JSON |
| Part 4: Error Handling & Production | 4.978 | 5 error directives, retry patterns, performance, blueprint engineering, 10 production patterns |

### n8n — Deep Expert Documentation (14.430 righe totali)

| Parte | Righe | Contenuto |
|-------|-------|-----------|
| Part 1: Core Architecture | 2.863 | Workflow engine, item model, node taxonomy, credential system |
| Part 2: Credentials & Routing | 4.428 | OAuth2/API Key/custom auth, IF/Switch, JS expressions, Luxon dates |
| Part 3: Error Handling & Webhooks | 2.523 | Error workflow, try/catch, retry, sub-workflows, webhook security |
| Part 4: Self-Hosting & Production | 4.616 | Docker/K8s, queue mode, PostgreSQL, Redis, REST API, production patterns |

---

## automation-flows/ — Blueprint e Workflow Operativi (3)

| Documento | Righe | Contenuto |
|-----------|-------|-----------|
| Make Blueprints | ~6.064 | 18+ blueprint produzione completi |
| n8n Workflows | ~3.047 | Data pipeline ETL, AI automations, feed generation |
| Cross-Platform | ~2.257 | Architettura ibrida Make+n8n, decision matrix, SOP |

---

## research/ — Analisi e Benchmark (2)

| Documento | Righe | Contenuto |
|-----------|-------|-----------|
| Marketplace Comparison | ~1.702 | Matrice 20 marketplace EU, commissioni, raccomandazioni |
| Automation Tools Benchmark | ~1.297 | Make vs n8n vs Zapier vs Lambda (50+ dimensioni) |

---

## app-design/ — Design Piattaforma Vendiamonoi (2)

| Documento | Righe | Contenuto |
|-----------|-------|-----------|
| Requirements | ~2.766 | 9 moduli funzionali, user roles, acceptance criteria |
| Architecture | ~2.548 | Next.js + Supabase, PostgreSQL schema (20+ tabelle) |

---

## marketplace-specs/ — Specifiche Marketplace (20)

| Marketplace | Righe | Piattaforma | Paesi |
|-------------|-------|-------------|-------|
| Amazon | 2.396 | Proprietaria | EU (15+) |
| Bol.com | 2.117 | Proprietaria | NL, BE |
| BricoBravo | 938 | Proprietaria | IT |
| Carrefour | 2.076 | Mirakl | FR, ES, IT, BE, RO, PL |
| Cdiscount | 1.876 | Octopia | FR |
| Conrad Electronic | 2.382 | Mirakl | DE, AT, CH |
| eBay | 1.053 | Proprietaria | EU (20+) |
| ePrice | 1.011 | Proprietaria | IT |
| IBS.it | 1.549 | Proprietaria | IT |
| Kaufland | 2.379 | Proprietaria | DE, CZ, SK, PL, AT, ES, IT |
| Leroy Merlin | 1.055 | Mirakl | FR, IT, ES, PL |
| ManoMano | 1.374 | Mirakl | FR, IT, ES, DE, UK, BE |
| MediaWorld | 1.527 | Mirakl | IT (+ MediaMarkt EU) |
| METRO Italia | 1.288 | Mirakl | IT |
| METRO Markets | 1.630 | Mirakl | DE |
| Mirakl Marketplaces | 1.297 | Mirakl | Multi |
| Rue du Commerce | 789 | Mirakl | FR |
| Shein | 2.475 | Proprietaria | EU (15+), Global |
| Temu | 8.021 | Proprietaria | EU (20+), Global |
| Vente-Unique | 1.048 | Proprietaria | FR, IT, ES, DE, BE, PT, NL |

## integrations/ + data-models/

| Documento | Righe | Contenuto |
|-----------|-------|-----------|
| API Design Patterns | 2.899 | REST, OAuth2, rate limiting, retry/backoff, pagination, webhooks |
| Product Information | 2.886 | GS1/GTIN, SKU management, taxonomy, PIM, compliance EU |

---

## Statistiche Globali

| Metrica | Valore |
|---------|--------|
| **Totale documenti** | 58 |
| **Totale righe** | ~116.000+ |
| **Directory principali** | 9 |
| **Marketplace documentati** | 20 |
| **Architetture piattaforme** | 8 |
| **Blueprint operativi** | 3 |
| **Ricerche/benchmark** | 2 |
| **Design piattaforma** | 2 |
| **Documenti strategici** | 1 (Master Checklist) |
| **Punti competenza mappati** | 1.000+ |
| **Domini strategici** | 10 |
| **Ultimo aggiornamento** | 02/04/2026 |

---

## Come Usa Claude Questo Repository

1. **All'inizio di ogni sessione**, Claude legge i file .md necessari via `get_file_contents`
2. **GitHub è la source of truth** — contenuto completo, formato markdown
3. **Obsidian** è il mirror locale per accesso offline di Alessio
4. **Notion** contiene versioni operative

### Guida Rapida per Claude

Quando pianifichi **strategia e priorità**, leggi prima:
- `strategy/master-checklist/README.md` (mappa 1.000+ competenze su 10 domini)

Quando lavori su **Make.com**, leggi:
- `architecture/make/README.md` + `part1` → `part4`

Quando lavori su **n8n**, leggi:
- `architecture/n8n/part1` → `part4`

Quando costruisci **automazioni**, leggi:
- `automation-flows/make-blueprints/README.md`
- `automation-flows/n8n-workflows/README.md`
- `automation-flows/cross-platform/README.md`

Quando analizzi **marketplace**, leggi:
- `marketplace-specs/<nome>/README.md`
- `research/marketplace-comparison/README.md`

Quando progetti la **piattaforma**, leggi:
- `app-design/requirements/README.md`
- `app-design/architecture/README.md`

---

## Log Aggiornamenti

| Data | Azione |
|------|--------|
| 02/04/2026 | 🆕 Master Checklist strategica: 1.000+ punti competenza, 10 domini, mappa completa |
| 02/04/2026 | ✅ Phase 1 completata: automation-flows, research, app-design |
| 02/04/2026 | ✅ Make.com: 4 parti expert-level (16.019 righe) |
| 02/04/2026 | ✅ n8n: 4 parti expert-level (14.430 righe) |
| 01/04/2026 | ✅ Batch 6: Conrad, Shein, Temu (12.878 righe) |
| 01/04/2026 | ✅ Batch 5: RdC, ePrice, IBS, ManoMano, BricoBravo, Vente-Unique |
| 01/04/2026 | ✅ Batch 4: Leroy Merlin, METRO Markets/Italia, MediaWorld, Carrefour |
| 01/04/2026 | ✅ Batch 3: Shopify arch, Bol.com, Cdiscount |
| 01/04/2026 | ✅ Batch 2: Mirakl, Kaufland, Channable, Make.com |
| 01/04/2026 | ✅ Batch 1: ChannelEngine, Supabase, Amazon, eBay, API patterns |
| 01/04/2026 | ✅ Base44 + software aziendali (api-docs) |
| 30/03/2026 | 🚀 Repository creato |
