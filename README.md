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
├── api-docs/                          ← Documentazione tecnica software aziendali
│   ├── notebooklm/README.md
│   ├── notion/README.md
│   ├── bitrix24/README.md
│   ├── chatgpt/README.md
│   ├── claude-ai/README.md
│   ├── superhuman/README.md
│   ├── miro/README.md
│   ├── obsidian/README.md
│   ├── supabase/README.md
│   ├── fatture-in-cloud/README.md
│   ├── qonto/README.md
│   ├── edesk/README.md
│   └── clickup/README.md
├── architecture/                      ← Architettura piattaforme (deep dive)
│   ├── base44/README.md
│   ├── channable/README.md
│   ├── channelengine/README.md
│   ├── make/README.md
│   ├── mirakl/README.md
│   ├── shopify/README.md
│   └── supabase/README.md
├── marketplace-specs/                 ← Specifiche tecniche marketplace
│   ├── amazon/README.md
│   ├── bolcom/README.md
│   ├── carrefour/README.md
│   ├── cdiscount/README.md
│   ├── ebay/README.md
│   ├── kaufland/README.md
│   ├── leroy-merlin/README.md
│   ├── mediaworld/README.md
│   ├── metro-italia/README.md
│   ├── metro-markets/README.md
│   └── mirakl-marketplaces/README.md
├── integrations/                      ← Pattern di integrazione universali
│   └── api-design-patterns/README.md
└── data-models/                       ← Standard dati prodotto
    └── product-information/README.md
```

---

## Sezioni e Contenuti

### api-docs/ — Software Aziendali (14 documentati)

| # | Software | Righe | Sezioni | Data |
|---|----------|-------|---------|------|
| 1 | NotebookLM | — | — | 30/03/2026 |
| 2 | Notion | — | — | 30/03/2026 |
| 3 | Bitrix24 | — | — | 30/03/2026 |
| 4 | ChatGPT | — | — | 31/03/2026 |
| 5 | Claude.ai | — | — | 31/03/2026 |
| 6 | Superhuman | — | — | 31/03/2026 |
| 7 | Miro PRO | — | — | 01/04/2026 |
| 8 | Obsidian | — | — | 01/04/2026 |
| 9 | Supabase | 2.586 | 21 | 01/04/2026 |
| 10 | Fatture in Cloud | 1.333 | 11 | 01/04/2026 |
| 11 | Webkull | ⏭️ Saltato | — | — |
| 12 | Qonto | — | 14 | 01/04/2026 |
| 13 | eDesk | 770 | 12 | 01/04/2026 |
| 14 | ClickUp | 948 | 16 | 01/04/2026 |

### architecture/ — Architettura Piattaforme (7 documentate)

| Software | Righe | Contenuto | Data |
|----------|-------|-----------|------|
| Base44 | 1.138 | Stack React+Vite+Deno, Entity system, SDK, CLI, pricing, data model | 01/04/2026 |
| Channable | 2.214 | Feed management, rules engine, 2500+ channels, PPC automation, API | 01/04/2026 |
| ChannelEngine | 1.498 | Merchant API v2, Channel API, webhooks, order lifecycle, connectors | 01/04/2026 |
| Make.com | 2.524 | Scenario architecture, bundles/operations, IML, data stores, error handling | 01/04/2026 |
| Mirakl | 2.987 | Shop API, Operator API, MCM, order state machine, invoicing, Connect | 01/04/2026 |
| Shopify | 2.093 | Admin API REST+GraphQL, Storefront API, product model, Markets, Plus | 01/04/2026 |
| Supabase | 2.166 | PostgreSQL internals, PostgREST, GoTrue, Realtime, Edge Functions, RLS | 01/04/2026 |

### marketplace-specs/ — Specifiche Marketplace (11 documentati)

| Marketplace | Righe | Piattaforma | Contenuto | Data |
|-------------|-------|-------------|-----------|------|
| Amazon | 2.396 | Proprietaria | SP-API, FBA/FBM, Buy Box, commissioni EU, VAT/OSS, GPSR, SEO A10 | 01/04/2026 |
| Bol.com | 2.117 | Proprietaria | Seller API v10, LVB/FBR, commissioni NL+BE, performance, advertising | 01/04/2026 |
| Carrefour | 2.076 | Mirakl | Multi-country FR/ES/IT/BE/RO/PL, FMCG, CFS fulfillment, Retail Media | 01/04/2026 |
| Cdiscount | 1.876 | Octopia | Catalogo EAN, FBC fulfillment, commissioni Francia, CNIL/GDPR | 01/04/2026 |
| eBay | 1.053 | Proprietaria | REST API, Cassini algorithm, Managed Payments, fee structure | 01/04/2026 |
| Kaufland | 2.379 | Proprietaria | HMAC-SHA256 auth, Units concept, order state machine, 7 paesi | 01/04/2026 |
| Leroy Merlin | 1.055 | Mirakl | Home improvement, bricolage/bagno/giardino, multi-country, commissioni | 01/04/2026 |
| MediaWorld | 1.527 | Mirakl | Consumer electronics, RAEE/WEEE, energy labels, EPREL, IMEI tracking | 01/04/2026 |
| METRO Italia | 1.288 | Mirakl | B2B HoReCa Italia, fatturazione elettronica SDI, HACCP, cold chain | 01/04/2026 |
| METRO Markets | 1.630 | Mirakl | B2B HoReCa DE, professional buyers, bulk ordering, HACCP compliance | 01/04/2026 |
| Mirakl Marketplaces | 1.297 | Mirakl | Catalogo 15+ marketplace EU Mirakl-powered | 01/04/2026 |

### integrations/ — Pattern Universali

| Documento | Righe | Contenuto | Data |
|-----------|-------|-----------|------|
| API Design Patterns | 2.899 | REST, OAuth2, rate limiting, retry/backoff, pagination, webhooks, caching, CQRS, saga | 01/04/2026 |

### data-models/ — Standard Dati

| Documento | Righe | Contenuto | Data |
|-----------|-------|-----------|------|
| Product Information | 2.886 | GS1/GTIN, SKU management, taxonomy, PIM, feed management, compliance EU | 01/04/2026 |

---

## Statistiche

| Metrica | Valore |
|---------|--------|
| **Totale documenti** | 33 |
| **Totale righe (stimate)** | 45.000+ |
| **Sezioni repository** | 5 (api-docs, architecture, marketplace-specs, integrations, data-models) |
| **Architetture piattaforme** | 7 |
| **Marketplace specs** | 11 |
| **Marketplace Mirakl-powered** | 7 (Carrefour, Leroy Merlin, MediaWorld, METRO Italia, METRO Markets, + catalogo Mirakl) |
| **Marketplace proprietari** | 4 (Amazon, Bol.com, eBay, Kaufland) |
| **Lingue** | Italiano |
| **Ultimo aggiornamento** | 01/04/2026 |

---

## Come Usa Claude Questo Repository

1. **All'inizio di ogni sessione**, Claude legge i file .md necessari via `get_file_contents`
2. **GitHub è la source of truth** — contenuto completo, formato markdown, leggibile in un colpo
3. **Obsidian** è il mirror locale per accesso offline di Alessio
4. **Notion** contiene versioni operative con contenuto sostanziale incorporato

---

## Log Aggiornamenti

| Data | Azione |
|------|--------|
| 01/04/2026 | ✅ Batch 4: Leroy Merlin, METRO Markets, METRO Italia, MediaWorld, Carrefour (7.576 righe) |
| 01/04/2026 | ✅ Batch 3: Shopify architettura, Bol.com, Cdiscount (6.086 righe) |
| 01/04/2026 | ✅ Batch 2: Mirakl platform + marketplaces, Kaufland, Channable, Make.com (11.401 righe) |
| 01/04/2026 | ✅ Batch 1: ChannelEngine, Supabase arch, Amazon, eBay, API patterns, Product data (12.898 righe) |
| 01/04/2026 | ✅ Base44 documentazione completata (1.138 righe, 20 sezioni) |
| 01/04/2026 | ✅ ClickUp, eDesk, Qonto, FiC, Supabase, Obsidian, Miro completati |
| 31/03/2026 | ✅ Superhuman, Claude.ai, ChatGPT completati |
| 30/03/2026 | ✅ NotebookLM, Notion, Bitrix24 completati |
| 30/03/2026 | 🚀 Repository creato |
