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
├── api-docs/                          ← Documentazione tecnica software aziendali (14)
├── architecture/                      ← Architettura piattaforme deep dive (8)
│   ├── base44/
│   ├── channable/
│   ├── channelengine/
│   ├── make/                          ← 5 file: README + 4 parti expert-level
│   │   ├── README.md                  (2.524 righe — overview originale)
│   │   ├── part1-core-architecture.md (2.772 righe)
│   │   ├── part2-connections-routers-logic.md (3.891 righe)
│   │   ├── part3-iml-datastores-webhooks.md (4.378 righe)
│   │   └── part4-errorhandling-performance-production.md (4.978 righe)
│   ├── mirakl/
│   ├── n8n/                           ← 4 parti expert-level (NUOVO)
│   │   ├── part1-core-architecture.md (2.863 righe)
│   │   ├── part2-credentials-routing-expressions.md (4.428 righe)
│   │   ├── part3-errorhandling-subworkflows-webhooks.md (2.523 righe)
│   │   └── part4-selfhosting-performance-production.md (4.616 righe)
│   ├── shopify/
│   └── supabase/
├── marketplace-specs/                 ← Specifiche tecniche marketplace (20)
│   ├── amazon/
│   ├── bolcom/
│   ├── bricobravo/
│   ├── carrefour/
│   ├── cdiscount/
│   ├── conrad/
│   ├── ebay/
│   ├── eprice/
│   ├── ibs/
│   ├── kaufland/
│   ├── leroy-merlin/
│   ├── manomano/
│   ├── mediaworld/
│   ├── metro-italia/
│   ├── metro-markets/
│   ├── mirakl-marketplaces/
│   ├── rue-du-commerce/
│   ├── shein/
│   ├── temu/
│   └── vente-unique/
├── integrations/                      ← Pattern di integrazione universali (1)
│   └── api-design-patterns/
└── data-models/                       ← Standard dati prodotto (1)
    └── product-information/
```

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
| Part 3: IML & Data Stores | 4.378 | IML complete reference (string/number/date/array/conditional), data stores CRUD, webhooks, CSV/XML/JSON |
| Part 4: Error Handling & Production | 4.978 | 5 error directives, retry patterns, performance optimization, blueprint engineering, 10 production e-commerce patterns |

### n8n — Deep Expert Documentation (14.430 righe totali) 🆕

| Parte | Righe | Contenuto |
|-------|-------|-----------|
| Part 1: Core Architecture | 2.863 | Workflow engine, item model, node taxonomy, execution lifecycle, credential system, n8n vs Make comparison |
| Part 2: Credentials & Routing | 4.428 | OAuth2/API Key/custom auth, IF/Switch nodes, expression system (JS), data transformation, Luxon dates |
| Part 3: Error Handling & Webhooks | 2.523 | Error workflow, try/catch pattern, retry strategies, sub-workflows, webhook security, advanced nodes |
| Part 4: Self-Hosting & Production | 4.616 | Docker/K8s deployment, queue mode, PostgreSQL, Redis, REST API, production patterns, Make vs n8n comparison |

## marketplace-specs/ — Specifiche Marketplace (20)

| Marketplace | Righe | Piattaforma | Focus | Paesi |
|-------------|-------|-------------|-------|-------|
| Amazon | 2.396 | Proprietaria | Multi-categoria | EU (15+) |
| Bol.com | 2.117 | Proprietaria | Multi-categoria | NL, BE |
| BricoBravo | 938 | Proprietaria | Bricolage, DIY, casa | IT |
| Carrefour | 2.076 | Mirakl | FMCG, multi-categoria | FR, ES, IT, BE, RO, PL |
| Cdiscount | 1.876 | Octopia | Multi-categoria | FR |
| Conrad Electronic | 2.382 | Mirakl | Elettronica, componenti, B2B industriale | DE, AT, CH |
| eBay | 1.053 | Proprietaria | Multi-categoria | EU (20+) |
| ePrice | 1.011 | Proprietaria | Elettronica, IT | IT |
| IBS.it | 1.549 | Proprietaria | Libri, media, general | IT |
| Kaufland | 2.379 | Proprietaria | Multi-categoria | DE, CZ, SK, PL, AT, ES, IT |
| Leroy Merlin | 1.055 | Mirakl | Home improvement, DIY | FR, IT, ES, PL |
| ManoMano | 1.374 | Mirakl | DIY, giardino, casa | FR, IT, ES, DE, UK, BE |
| MediaWorld | 1.527 | Mirakl | Consumer electronics | IT (+ MediaMarkt EU) |
| METRO Italia | 1.288 | Mirakl | B2B HoReCa | IT |
| METRO Markets | 1.630 | Mirakl | B2B HoReCa | DE |
| Mirakl Marketplaces | 1.297 | Mirakl | Catalogo 15+ marketplace EU | Multi |
| Rue du Commerce | 789 | Mirakl | Elettronica, IT, casa | FR |
| Shein | 2.475 | Proprietaria | Fast fashion, beauty, lifestyle | EU (15+), Global |
| Temu | 8.021 | Proprietaria | General merchandise, ultra-low-price | EU (20+), Global |
| Vente-Unique | 1.048 | Proprietaria | Mobili, arredamento | FR, IT, ES, DE, BE, PT, NL |

## integrations/ + data-models/

| Documento | Righe | Contenuto |
|-----------|-------|-----------|
| API Design Patterns | 2.899 | REST, OAuth2, rate limiting, retry/backoff, pagination, webhooks, CQRS, saga |
| Product Information | 2.886 | GS1/GTIN, SKU management, taxonomy, PIM, feed management, compliance EU |

---

## Statistiche Globali

| Metrica | Valore |
|---------|--------|
| **Totale documenti** | 50 |
| **Totale righe** | ~95.000+ |
| **Marketplace documentati** | 20 |
| **Marketplace Mirakl-powered** | 9 |
| **Marketplace proprietari** | 11 |
| **Architetture piattaforme** | 8 |
| **— di cui expert-level (multi-parte)** | 2 (Make.com, n8n) |
| **Software aziendali** | 14 |
| **Pattern universali** | 2 |
| **Lingue** | Italiano |
| **Ultimo aggiornamento** | 02/04/2026 |

---

## Come Usa Claude Questo Repository

1. **All'inizio di ogni sessione**, Claude legge i file .md necessari via `get_file_contents`
2. **GitHub è la source of truth** — contenuto completo, formato markdown
3. **Obsidian** è il mirror locale per accesso offline di Alessio
4. **Notion** contiene versioni operative con contenuto sostanziale incorporato

### Guida Rapida per Claude — Automazioni

Quando lavori su **Make.com**, leggi prima:
- `architecture/make/README.md` (overview)
- `architecture/make/part1-core-architecture.md` (moduli, bundles, operations)
- `architecture/make/part2-connections-routers-logic.md` (routing, filtri)
- `architecture/make/part3-iml-datastores-webhooks.md` (IML, data stores)
- `architecture/make/part4-errorhandling-performance-production.md` (errori, produzione)

Quando lavori su **n8n**, leggi prima:
- `architecture/n8n/part1-core-architecture.md` (nodi, items, engine)
- `architecture/n8n/part2-credentials-routing-expressions.md` (routing, JS expressions)
- `architecture/n8n/part3-errorhandling-subworkflows-webhooks.md` (errori, sub-workflow)
- `architecture/n8n/part4-selfhosting-performance-production.md` (deploy, produzione)

Quando lavori su un **marketplace specifico**, leggi:
- `marketplace-specs/<nome>/README.md`

---

## Log Aggiornamenti

| Data | Azione |
|------|--------|
| 02/04/2026 | ✅ Make.com: espansione a 4 parti expert-level (16.019 righe aggiunte) |
| 02/04/2026 | ✅ n8n: nuova architettura completa 4 parti (14.430 righe) |
| 01/04/2026 | ✅ Batch 6: Conrad Electronic, Shein, Temu (12.878 righe) |
| 01/04/2026 | ✅ Batch 5: Rue du Commerce, ePrice, IBS.it, ManoMano, BricoBravo, Vente-Unique (6.709 righe) |
| 01/04/2026 | ✅ Batch 4: Leroy Merlin, METRO Markets, METRO Italia, MediaWorld, Carrefour (7.576 righe) |
| 01/04/2026 | ✅ Batch 3: Shopify architettura, Bol.com, Cdiscount (6.086 righe) |
| 01/04/2026 | ✅ Batch 2: Mirakl platform + marketplaces, Kaufland, Channable, Make.com (11.401 righe) |
| 01/04/2026 | ✅ Batch 1: ChannelEngine, Supabase arch, Amazon, eBay, API patterns, Product data (12.898 righe) |
| 01/04/2026 | ✅ Base44 + software aziendali (api-docs) |
| 30/03/2026 | 🚀 Repository creato |
