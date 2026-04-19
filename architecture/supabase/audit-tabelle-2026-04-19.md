---
tags: [architettura, database, supabase, audit, vendiamonoi]
source: vendiamonoi-knowledge/architecture/supabase/audit-tabelle-2026-04-19.md
created: 2026-04-19
type: audit-architetturale
---

# Audit tabelle Supabase — Vendiamonoi OS

**Data:** 2026-04-19
**Progetto:** smozehzbhoeceioqxodx
**Tabelle analizzate:** 52 (+ 12 viste)

## Come leggere questo documento

Ogni tabella ha un verdetto:

| Icona | Significato | Cosa fare |
|---|---|---|
| 🟢 **CORE** | Tabella essenziale, molto usata dal codice | Non toccare |
| 🔵 **SUPPORTO** | Tabella di configurazione o storico, utile | Non toccare |
| 🟡 **VERIFICARE** | Non usata dal frontend, ma potrebbe servire a Make/n8n/trigger | Controllare prima di rimuovere |
| 🔴 **SOSPETTA DUPLICAZIONE** | Probabile doppione di un'altra tabella | Analisi approfondita, poi decidere |
| ⚫ **VUOTA/INUTILIZZATA** | Nessun dato e nessun uso | Candidata alla rimozione |

**"Usi nel codice"** = numero di file nel repo `vendiamonoi-os` che referenziano la tabella. Non copre Make/n8n (da verificare separatamente).

---

## Mappa concettuale per dominio

```
📦 ORDINI (marketplace)
├─ orders                         → testata ordine (1 riga per ordine)
├─ order_items                    → righe prodotto (N per ordine)
├─ order_events                   → log cambiamenti stato
└─ order_status_history           → storico stato (doppione di order_events?)

🚚 EVASIONE/FULFILLMENT
├─ fulfillment_orders             → workflow evasione (1 per ordine)
├─ fulfillment_entries            → assegnazione articolo→fornitore
├─ fulfillment_status_history     → storico stati fulfillment
├─ fulfillment_sla_config         → SLA per marketplace
├─ shipment_labels                → etichette spedizione
├─ tracking_entries               → tracking inseriti
├─ daily_order_batches            → batch giornalieri ordini
├─ supplier_dispatch_batches      → batch invio fornitore (doppione?)
├─ supplier_orders                → ordine al fornitore (doppione di fulfillment_orders?)
└─ supplier_order_transitions     → storico stati supplier_orders

👥 FORNITORI
├─ suppliers                      → anagrafica
├─ supplier_aliases               → nomi alternativi
├─ supplier_contacts              → contatti multipli
├─ supplier_configs               → config operativa (versione A)
├─ supplier_fulfillment_config    → config operativa (versione B – doppione?)
├─ supplier_notification_rules    → regole notifiche email
├─ supplier_operations            → operazioni generiche (usata?)
├─ performance_scores             → punteggi performance
└─ oms_event_rules                → regole evento OMS

🏪 MARKETPLACE
├─ marketplaces                   → anagrafica canali
├─ marketplace_listings           → pubblicazioni prodotti su marketplace
├─ marketplace_payouts            → payout ricevuti
└─ catalog_marketplace_category_commissions → commissioni per categoria

📋 CATALOGO (sistema nuovo)
├─ catalog_listini                → listini master
├─ catalog_products               → prodotti da feed
├─ catalog_feed_sources           → sorgenti feed
├─ catalog_publication_channels   → canali di pubblicazione
├─ catalog_price_rules            → regole pricing
└─ catalog_shipping_rules         → regole spedizione

📋 CATALOGO (sistema vecchio?)
├─ products                       → prodotti (legacy?)
├─ product_categories             → categorie prodotti
└─ star_skus                      → SKU top performer

💰 FATTURAZIONE
├─ supplier_invoices              → fatture fornitore
├─ supplier_invoice_lines         → righe fattura
├─ invoice_order_links            → collegamenti fattura↔ordine
├─ financial_movements            → movimenti finanziari
├─ financial_events               → eventi finanziari (doppione?)
└─ anomalies                      → anomalie finanziarie

🎧 CUSTOMER SERVICE / PROBLEMI
├─ cs_tickets                     → ticket eDesk
├─ cs_messages                    → messaggi ticket
├─ cs_webhook_debug               → debug webhook
├─ problem_cases                  → casi problema (resi, danni)
└─ case_events                    → eventi su casi problema

📐 SISTEMA / CONFIG
├─ countries                      → paesi EU
├─ warehouses                     → magazzini
├─ email_templates                → template email
├─ carrier_selection_rules        → regole scelta corriere
├─ outbox_events                  → coda eventi outbox
└─ guides                         → documentazione in-app
```

---

## 🔴 Sospette duplicazioni — da risolvere per prime

Queste sono le coppie/triplette che probabilmente causano il tuo problema "annullo qua e non si aggiorna là".

### 1. `orders.marketplace_status` vs `fulfillment_orders.fulfillment_status`

**Cosa succede:** sono due stati diversi su due tabelle diverse. Non è un doppione — è un design corretto (stato lato marketplace ≠ stato evasione interna) — ma **manca la sincronizzazione**.

**Situazione attuale:**
- `orders` ha anche `status`, `internal_status`, `warehouse_status`, `fulfillment_mode`, `cancellation_reason`, `cancellation_fault`, `status_reason`, `status_fault`, `status_updated_at`, `status_updated_by` → **8 colonne di stato sulla stessa tabella**, frutto di aggiunte successive
- `fulfillment_orders` ha `fulfillment_status` + storico in `fulfillment_status_history`
- `supplier_orders.status` → terzo stato

**Azione:** questa non è una pulizia di tabelle, è una **decisione architetturale** da prendere prima. Ti propongo una discussione dedicata.

### 2. `products` (legacy) vs `catalog_products` (nuovo)

| Tabella | Righe reali | Usi nel codice |
|---|---|---|
| `products` | ~0 | 0 ❗ |
| `catalog_products` | 500 | 4 ✅ |

**Verdetto:** `catalog_products` è il sistema attivo. `products` è **legacy da prima dell'introduzione del sistema catalogo**. Ma: `order_items.product_id`, `marketplace_listings.product_id`, `star_skus`, `products.supplier_id` puntano a `products`. Prima di rimuovere bisogna migrare le FK su `catalog_products` o decidere che `products` resti come "prodotto venduto storicamente" e `catalog_products` come "prodotto nel listino attuale" (sono due concetti diversi).

**Azione proposta:** non toccare ora. Decidere la **semantica** prima di pulire.

### 3. `supplier_orders` vs `fulfillment_orders`

| Tabella | Righe reali | Usi nel codice | Commento DB |
|---|---|---|---|
| `supplier_orders` | 356 | 0 ❗ | nessuno |
| `fulfillment_orders` | ~0 | 5 ✅ | "Workflow di evasione per ogni ordine" |

**Verdetto:** **quasi certo doppione storico**. `supplier_orders` ha dati veri (356 ordini) ma **nessun codice frontend la legge**. `fulfillment_orders` è la versione nuova con più campi (SLA, tracking, etichette). Probabilmente è stata creata per sostituire `supplier_orders` ma **non è mai stata fatta la migrazione dei dati**.

**Azione proposta:** verificare se Make/n8n scrive ancora in `supplier_orders`. Se sì, spegnere quel flusso. Poi migrare i 356 record in `fulfillment_orders` e droppare `supplier_orders` + `supplier_order_transitions`.

### 4. `supplier_configs` vs `supplier_fulfillment_config`

| Tabella | Usi nel codice | Commento DB |
|---|---|---|
| `supplier_configs` | 12 ✅ | nessuno |
| `supplier_fulfillment_config` | 0 ❗ | "Configurazione operativa per fornitore" |

**Verdetto:** una è usata dal frontend (`supplier_configs`), l'altra — con un commento più dettagliato e più campi (FTP, API, crittografia password) — **non è mai stata agganciata**. Probabile tentativo di upgrade non completato.

**Azione proposta:** scegliere quale tenere. Se `supplier_fulfillment_config` è il design futuro (più completo), migrare i dati e swappare. Altrimenti droppare.

### 5. `order_events` vs `order_status_history`

| Tabella | Righe reali | Usi nel codice |
|---|---|---|
| `order_events` | 4476 | 0 ❗ |
| `order_status_history` | ~0 | 0 ❗ |

**Verdetto:** `order_events` ha dati veri (popolata da trigger, probabile), `order_status_history` è vuota. Entrambe non lette dal frontend. **Doppione**.

**Azione proposta:** capire chi scrive in `order_events` (quasi sicuro un trigger Postgres), e droppare `order_status_history` se davvero inutilizzata.

### 6. `financial_events` vs `financial_movements`

Entrambe non usate dal frontend (usi=0), entrambe con stesse FK verso ordini/fornitori/fatture.

**Verdetto:** probabile doppione. Da verificare cosa scrive dove.

### 7. `daily_order_batches` vs `supplier_dispatch_batches`

Due tabelle per l'invio batch ordini al fornitore. Nessuna usata nel codice frontend al momento.

**Verdetto:** da decidere quale usare per il flusso di invio batch.

---

## 🟢 CORE — non toccare

| Tabella | Righe | Ruolo | Usi |
|---|---|---|---|
| `orders` | 4476 | Testata ordine marketplace | 54 |
| `order_items` | 785 | Righe articolo ordine | 24 |
| `suppliers` | 92 | Anagrafica fornitori | 40 |
| `marketplaces` | 77 | Anagrafica canali di vendita | 7 |
| `email_templates` | — | Template email notifiche | 26 |
| `problem_cases` | — | Casi problema (resi, danni) | 21 |
| `supplier_invoices` | 550 | Fatture fornitore | 12 |
| `supplier_configs` | — | Config operativa fornitore | 12 |
| `supplier_notification_rules` | — | Regole notifiche fornitore | 8 |
| `case_events` | — | Timeline casi problema | 12 |
| `fulfillment_orders` | — | Workflow evasione | 5 |
| `fulfillment_entries` | — | Assegnazione articolo→fornitore | 7 |
| `shipment_labels` | — | Etichette spedizione | 3 |
| `cs_tickets` / `cs_messages` | 41/441 | Customer service | 3/3 |
| `guides` | — | Documentazione in-app | 3 |

---

## 🔵 SUPPORTO — non toccare, ma verificare utilizzo

Tabelle di configurazione/storico. Anche se usi=0, hanno un ruolo preciso e sono probabilmente scritte da trigger o da flussi esterni.

| Tabella | Ruolo presunto |
|---|---|
| `countries` | Anagrafica paesi + aliquote IVA (FK da marketplaces/suppliers) |
| `catalog_listini` | Listini master |
| `catalog_products` (500) | Prodotti importati da feed |
| `catalog_feed_sources` | Sorgenti feed |
| `catalog_publication_channels` | Canali pubblicazione |
| `catalog_price_rules` | Regole pricing |
| `catalog_shipping_rules` | Regole spedizione |
| `fulfillment_sla_config` | SLA per marketplace |
| `fulfillment_status_history` | Audit trail fulfillment (scritto da trigger) |
| `oms_event_rules` | Regole evento OMS |
| `carrier_selection_rules` | Regole scelta corriere automatica |
| `supplier_aliases` | Nomi alternativi (per match fatture) |
| `supplier_contacts` | Contatti multipli fornitore |
| `performance_scores` (74) | Punteggi calcolati |
| `star_skus` (17) | SKU top performer |
| `warehouses` | Magazzini |
| `outbox_events` | Coda eventi pattern outbox |
| `tracking_entries` | Tracking inseriti (scritti da n8n?) |

---

## 🟡 VERIFICARE — non usate dal frontend, possibile uso Make/n8n

Queste non appaiono nel codice frontend. Prima di toccarle, controllare:
1. Flussi Make.com che potrebbero scriverle
2. Workflow n8n (quando attivato)
3. Trigger Postgres che le popolano
4. RLS policies che le proteggono (se RLS attivo con policy = tabella considerata "di uso futuro")

- `anomalies` — anomalie finanziarie, FK a problem_cases
- `cs_webhook_debug` — debug webhook eDesk (probabile strumento di debug lasciato lì)
- `invoice_order_links` — collegamenti fattura↔ordine (sostituita da `supplier_invoice_lines`?)
- `marketplace_listings` — pubblicazioni marketplace (legata a `products` legacy)
- `marketplace_payouts` — payout marketplace (funzione non ancora sviluppata)
- `catalog_marketplace_category_commissions` — commissioni per categoria
- `product_categories` — categorie prodotti (legata a `products` legacy)

---

## 👁 Viste (`v_*`) — sono tutte OK

Le viste non sono tabelle: sono query salvate. Non occupano spazio, si aggiornano da sole. Tienile tutte — sono la "lente" che unisce tabelle diverse per il frontend/report:

- `v_orders_enriched` — ordini + items + margini
- `v_orders_marketplace` — ordini + fulfillment + supplier (super completa)
- `v_fulfillment_dashboard` — dashboard evasione
- `v_sla_alerts` — alert SLA
- `v_supplier_kpi` / `v_supplier_fulfillment_stats` / `v_supplier_performance_unified` — KPI fornitori
- `v_supplier_payment_schedule` — calendario pagamenti
- `v_marketplace_kpi` — KPI marketplace
- `v_order_financial_summary` / `v_financial_reconciliation` — riconciliazione finanziaria
- `v_open_anomalies` — anomalie aperte

---

## Piano di pulizia consigliato (ordine importante)

### Step 1 — Prima di tutto: definire gli stati
Prima di toccare le tabelle, bisogna decidere **chi è il padrone di "stato ordine"**. Propongo:
- `orders.status` = stato unificato (nuovo, da accettare, accettato, evaso, spedito, consegnato, annullato, in problema)
- `orders.marketplace_status` = stato raw letto da SellRapido (solo lettura)
- `fulfillment_orders.fulfillment_status` = stato operativo interno (routing, dispatched, tracking…)
- Le 3 fonti vengono sincronizzate da **una funzione SQL + trigger** ("quando cambia X, aggiorna Y")

Senza questo step, qualsiasi pulizia rischia di rompere il problema di sincronizzazione invece di risolverlo.

### Step 2 — Le 7 colonne di stato su `orders` vanno ridotte
Oggi `orders` ha: `marketplace_status`, `internal_status`, `status`, `status_reason`, `status_fault`, `warehouse_status`, `fulfillment_mode`, `cancellation_reason`, `cancellation_fault`. Troppe. Ne bastano 3 con regole chiare.

### Step 3 — Risolvere le duplicazioni (nell'ordine)
1. `supplier_orders` → migrare in `fulfillment_orders` e droppare
2. `order_status_history` → droppare (mantenere solo `order_events`)
3. `supplier_fulfillment_config` → decidere se sostituisce `supplier_configs` o viceversa
4. `financial_events` vs `financial_movements` → decidere unico owner
5. `daily_order_batches` vs `supplier_dispatch_batches` → scegliere uno

### Step 4 — Valutare il sistema catalogo
Il sistema `catalog_*` (nuovo) convive con `products` + `product_categories` + `marketplace_listings` (vecchio). Scegliere quale è il futuro e migrare.

### Step 5 — Rimuovere strumenti di debug/demo
- `cs_webhook_debug` (se in produzione non serve)

---

## Come procedere in sicurezza

Per ogni tabella candidata alla rimozione:

1. **Backup specifico** — `CREATE TABLE _archive_nome_tabella AS SELECT * FROM nome_tabella`
2. **Controllo FK entranti** — `SELECT tc.table_name, kcu.column_name FROM information_schema.table_constraints tc JOIN information_schema.key_column_usage kcu ON tc.constraint_name=kcu.constraint_name WHERE ...`
3. **Controllo trigger/funzioni** che scrivono nella tabella
4. **Controllo RLS policies**
5. **Grep finale** nel repo frontend + scenari Make + workflow n8n
6. Solo allora `DROP TABLE`, preceduto da una migrazione Supabase con rollback (come da regola CLAUDE.md)

---

## Note importanti

- ⚠️ Le `approx_rows = -1` nel report originale indicano tabelle mai analizzate da Postgres (`VACUUM ANALYZE` mai eseguito) oppure tabelle vuote. Per numeri esatti usare `SELECT COUNT(*)`.
- ⚠️ Make.com e n8n potrebbero scrivere in tabelle che il frontend non legge. **Verificare sempre gli scenari Make attivi** prima di droppare.
- ⚠️ Alcuni campi `USER-DEFINED` sono enum Postgres. Rimuovere una tabella significa anche pulire gli enum associati.
