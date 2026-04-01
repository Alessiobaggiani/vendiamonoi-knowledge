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
├── architecture/                      ← Architettura piattaforme deep dive (7)
│   ├── base44/
│   ├── channable/
│   ├── channelengine/
│   ├── make/
│   ├── mirakl/
│   ├── shopify/
│   └── supabase/
├── marketplace-specs/                 ← Specifiche tecniche marketplace (17)
│   ├── amazon/
│   ├── bolcom/
│   ├── bricobravo/
│   ├── carrefour/
│   ├── cdiscount/
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
│   └── vente-unique/
├── integrations/                      ← Pattern di integrazione universali (1)
│   └── api-design-patterns/
└── data-models/                       ← Standard dati prodotto (1)
    └── product-information/
```

---

## architecture/ — Architettura Piattaforme (7)

| Software | Righe | Contenuto | Data |
|----------|-------|-----------|------|
| Base44 | 1.138 | Stack React+Vite+Deno, Entity system, SDK, CLI, pricing | 01/04/2026 |
| Channable | 2.214 | Feed management, rules engine, 2500+ channels, PPC, API | 01/04/2026 |
| ChannelEngine | 1.498 | Merchant API v2, Channel API, webhooks, order lifecycle | 01/04/2026 |
| Make.com | 2.524 | Scenario architecture, bundles/operations, IML, data stores | 01/04/2026 |
| Mirakl | 2.987 | Shop API, Operator API, MCM, order state machine, invoicing | 01/04/2026 |
| Shopify | 2.093 | Admin API REST+GraphQL, Storefront API, Markets, Plus | 01/04/2026 |
| Supabase | 2.166 | PostgreSQL, PostgREST, GoTrue, Realtime, Edge Functions | 01/04/2026 |

## marketplace-specs/ — Specifiche Marketplace (17)

| Marketplace | Righe | Piattaforma | Focus | Paesi |
|-------------|-------|-------------|-------|-------|
| Amazon | 2.396 | Proprietaria | Multi-categoria | EU (15+) |
| Bol.com | 2.117 | Proprietaria | Multi-categoria | NL, BE |
| BricoBravo | 938 | Proprietaria | Bricolage, DIY, casa | IT |
| Carrefour | 2.076 | Mirakl | FMCG, multi-categoria | FR, ES, IT, BE, RO, PL |
| Cdiscount | 1.876 | Octopia | Multi-categoria | FR |
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
| **Totale documenti** | 39 |
| **Totale righe** | ~52.000+ |
| **Marketplace documentati** | 17 |
| **Marketplace Mirakl-powered** | 8 |
| **Marketplace proprietari** | 9 |
| **Architetture piattaforme** | 7 |
| **Software aziendali** | 14 |
| **Pattern universali** | 2 |
| **Lingue** | Italiano |
| **Ultimo aggiornamento** | 01/04/2026 |

---

## Come Usa Claude Questo Repository

1. **All'inizio di ogni sessione**, Claude legge i file .md necessari via `get_file_contents`
2. **GitHub è la source of truth** — contenuto completo, formato markdown
3. **Obsidian** è il mirror locale per accesso offline di Alessio
4. **Notion** contiene versioni operative con contenuto sostanziale incorporato

---

## Log Aggiornamenti

| Data | Azione |
|------|--------|
| 01/04/2026 | ✅ Batch 5: Rue du Commerce, ePrice, IBS.it, ManoMano, BricoBravo, Vente-Unique (6.709 righe) |
| 01/04/2026 | ✅ Batch 4: Leroy Merlin, METRO Markets, METRO Italia, MediaWorld, Carrefour (7.576 righe) |
| 01/04/2026 | ✅ Batch 3: Shopify architettura, Bol.com, Cdiscount (6.086 righe) |
| 01/04/2026 | ✅ Batch 2: Mirakl platform + marketplaces, Kaufland, Channable, Make.com (11.401 righe) |
| 01/04/2026 | ✅ Batch 1: ChannelEngine, Supabase arch, Amazon, eBay, API patterns, Product data (12.898 righe) |
| 01/04/2026 | ✅ Base44 + software aziendali (api-docs) |
| 30/03/2026 | 🚀 Repository creato |
