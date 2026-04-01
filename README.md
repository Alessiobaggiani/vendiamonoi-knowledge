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
│   ├── channelengine/README.md
│   └── supabase/README.md
├── marketplace-specs/                 ← Specifiche tecniche marketplace
│   ├── amazon/README.md
│   └── ebay/README.md
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

### architecture/ — Architettura Piattaforme

| Software | Righe | Contenuto | Data |
|----------|-------|-----------|------|
| Base44 | 1.138 | Stack React+Vite+Deno, Entity system, SDK, CLI, pricing, data model Vendiamonoi.it | 01/04/2026 |
| ChannelEngine | 1.498 | Merchant API v2, Channel API, webhooks, order lifecycle, marketplace connectors, repricing | 01/04/2026 |
| Supabase | 2.166 | PostgreSQL internals, PostgREST, GoTrue, Realtime, Edge Functions, RLS patterns, self-hosting | 01/04/2026 |

### marketplace-specs/ — Specifiche Marketplace

| Marketplace | Righe | Contenuto | Data |
|-------------|-------|-----------|------|
| Amazon | 2.396 | SP-API, product data, FBA/FBM, Buy Box, commissioni EU, VAT/OSS, GPSR, SEO A10 | 01/04/2026 |
| eBay | 1.053 | REST API, Cassini algorithm, Managed Payments, fee structure, seller performance | 01/04/2026 |

### integrations/ — Pattern Universali

| Documento | Righe | Contenuto | Data |
|-----------|-------|-----------|------|
| API Design Patterns | 2.899 | REST, OAuth2, rate limiting, retry/backoff, pagination, webhooks, caching, CQRS, saga pattern | 01/04/2026 |

### data-models/ — Standard Dati

| Documento | Righe | Contenuto | Data |
|-----------|-------|-----------|------|
| Product Information | 2.886 | GS1/GTIN, SKU management, taxonomy, PIM, feed management, compliance EU, Schema.org | 01/04/2026 |

---

## Statistiche

| Metrica | Valore |
|---------|--------|
| **Totale documenti** | 20 |
| **Totale righe (stimate)** | 25.000+ |
| **Sezioni repository** | 5 (api-docs, architecture, marketplace-specs, integrations, data-models) |
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
| 01/04/2026 | ✅ Aggiunte 4 nuove sezioni: architecture/, marketplace-specs/, integrations/, data-models/ |
| 01/04/2026 | ✅ 6 nuovi documenti: ChannelEngine, Supabase arch, Amazon, eBay, API patterns, Product data (12.898 righe) |
| 01/04/2026 | ✅ Base44 documentazione completata (1.138 righe, 20 sezioni) |
| 01/04/2026 | ✅ ClickUp, eDesk, Qonto, FiC, Supabase, Obsidian, Miro completati |
| 31/03/2026 | ✅ Superhuman, Claude.ai, ChatGPT completati |
| 30/03/2026 | ✅ NotebookLM, Notion, Bitrix24 completati |
| 30/03/2026 | 🚀 Repository creato |