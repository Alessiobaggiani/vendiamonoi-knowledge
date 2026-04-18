---
tags: [performance, dashboard, score-engine, supabase, vendiamonoi]
source: vendiamonoi-os/docs/superpowers/specs/2026-04-17-performance-dashboard-design.md
created: 2026-04-17
type: feature-spec
---

# Performance Dashboard MVP — Design Spec

**Stato:** Implementato e live  
**Branch:** `feature/performance-dashboard` in `vendiamonoi-os`

## Obiettivo

Modulo di analisi KPI per fornitori e marketplace, aggiornato automaticamente ogni ora dal database. Nessun intervento manuale.

---

## Score Engine

Funzione PostgreSQL `refresh_performance_scores(p_days integer DEFAULT 90)` che calcola un punteggio 0–100 per ogni coppia Fornitore × Marketplace dagli ordini degli ultimi 90 giorni.

### Formula composita

| Dimensione | Peso | Calcolo |
|---|---|---|
| Revenue Density | 30% | Fatturato normalizzato sul max |
| Catalog Breadth | 25% | SKU unici normalizzati sul max |
| Order Volume | 20% | Ordini totali normalizzati sul max |
| SLA Reliability | 15% | 100 se avg_shipping ≤ SLA, altrimenti −20pt/giorno |
| Growth Signal | 10% | Ratio ordini_ultimi30gg / ordini_prev30gg, cap 2× |

### Tier

| Tier | Score | Colore |
|---|---|---|
| A | ≥ 70 | Verde `#52c41a` |
| B | 40–69 | Arancione `#faad14` |
| C | < 40 | Rosso `#ff4d4f` |

---

## Schedulazione automatica (pg_cron)

Job attivo su Supabase — ogni ora al minuto 0:
```sql
SELECT cron.schedule('refresh-performance-scores', '0 * * * *',
  $$ SELECT public.refresh_performance_scores(90) $$);
```
Risultato live primo calcolo: **73 score**, **17 star SKU**.

---

## Tabelle database

### `performance_scores`
- `supplier_id` FK + `marketplace_code` — UNIQUE
- `score` NUMERIC(5,2) — composito
- 5 sotto-score dimensionali
- Aggregati: `total_revenue`, `total_margin`, `total_orders`, `unique_skus_sold`
- Trend: `orders_last_30d`, `orders_prev_30d`
- `calculated_at` — timestamp ultimo calcolo

### `star_skus`
- Prodotti con ≥ 5 vendite in 90gg
- `is_stock_candidate` = ≥ 10 vendite
- RLS attivo, accesso solo `authenticated`

---

## Pagine frontend

### Executive Overview (`/performance/overview`)
- 4 KPI card: Fatturato, Margine, Ordini, Margine%
- Badge distribuzione tier A/B/C
- Pannello anomalie (fornitori in calo con >5 ordini)
- Tabella marketplace aggregata
- Top-5 fornitori per score

### Marketplace Health (`/performance/marketplace`)
- Tabella per canale: fatturato, margine, margine%, ordini, fornitori, score medio, warning score min, share ricavi

### Supplier Command Center (`/fornitori/performance`)
- Card tier A/B/C cliccabili (filtro)
- Tabella: tier badge, score, trend%, fatturato 90gg, SKU unici, SLA check, canali

---

## Stack tecnico

Supabase PostgreSQL · React 19 · Refine.dev 5 · Ant Design 5 · TypeScript · Vitest 4 (9 test)
