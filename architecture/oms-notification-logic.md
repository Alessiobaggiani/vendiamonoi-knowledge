# OMS — Logica Notifiche Fornitori

**Creato:** 2026-04-18
**Fase:** Phase 1 — Fondamenta DB + Configurazione Fornitori
**Aggiornare questo file** ogni volta che si aggiunge una tabella, uno stato, una regola o si cambia la logica n8n.

---

## Perché esiste questo sistema

Vendiamonoi gestisce ordini su 20+ marketplace europei con 100+ fornitori.
Ogni ordine che arriva da un marketplace deve essere:
1. Assegnato al fornitore giusto (routing)
2. Comunicato al fornitore (dispatch — via email o portale)
3. Monitorato finché non viene spedito al cliente

Il problema: senza automazione, un operatore dovrebbe controllare manualmente ogni ordine ogni ora per sapere se il fornitore ha confermato, se ha mandato il tracking, se è in ritardo. Con volumi alti è impossibile.

**Soluzione:** n8n legge le regole dal database e manda le email automaticamente al momento giusto. Gli operatori intervengono solo quando qualcosa va storto.

---

## Le tabelle coinvolte (non toccare senza leggere prima)

| Tabella | Scopo | Chi scrive | Chi legge |
|---|---|---|---|
| `supplier_orders` | Un ordine assegnato a un fornitore | n8n (routing) | n8n, frontend |
| `supplier_order_transitions` | Storia immutabile di ogni cambio di stato | n8n | frontend (audit log) |
| `outbox_events` | Coda eventi da processare (Outbox Pattern) | n8n | n8n |
| `supplier_configs` | Come si comunica con ciascun fornitore | frontend | n8n |
| `email_templates` | I testi delle email (con variabili Handlebars) | frontend | n8n (Resend) |
| `supplier_notification_rules` | Quando mandare quale email | frontend | n8n |

**Regola d'oro:** queste tabelle formano un sistema coeso. Aggiungere una colonna a `supplier_orders` senza aggiornare n8n è un bug silenzioso. Aggiungere una regola a `supplier_notification_rules` senza il template corrispondente causa un errore runtime in n8n.

---

## La state machine degli ordini fornitore

Ogni `supplier_order` ha un campo `status` di tipo ENUM PostgreSQL (`supplier_order_status`). I valori possibili e le transizioni permesse:

```
                    ┌─────────────────┐
  [ordine arriva]   │ pending_routing │  n8n assegna il fornitore
                    └────────┬────────┘
                             │
              ┌──────────────┴──────────────┐
              │                             │
    ┌─────────▼─────────┐       ┌───────────▼──────────┐
    │  pending_dispatch │       │    pending_batch      │
    │  (invio immediato)│       │  (invio programmato)  │
    └─────────┬─────────┘       └───────────┬──────────┘
              │                             │
              └──────────────┬──────────────┘
                             │
                    ┌────────▼────────┐
                    │   dispatched    │  email/portale inviato
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │   confirmed     │  fornitore ha confermato
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  label_ready    │  etichetta spedizione pronta
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │    shipped      │  fornitore ha spedito
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │   delivered     │  consegnato al cliente ✓
                    └─────────────────┘

In qualsiasi momento:
  → failed      (invio fallito, retry programmato)
  → on_hold     (superato max_retries, aspetta operatore)
  → cancelled   (ordine annullato)
```

**IMPORTANTE:** I valori ENUM non possono essere eliminati da PostgreSQL dopo la creazione — solo aggiunti via `ALTER TYPE ADD VALUE`. Non rinominare mai uno stato esistente senza una migration complessa.

---

## Come funziona il dispatch (invio dell'ordine al fornitore)

n8n legge `supplier_configs` per ogni fornitore e decide il metodo:

| `dispatch_method` | Comportamento |
|---|---|
| `email_realtime` | Email inviata appena l'ordine arriva (stato → `dispatched`) |
| `email_batch` | Email raccolta e inviata all'orario `batch_send_time` configurato (stato → `pending_batch` poi `dispatched`) |
| `web_portal` | Operatore manuale su portale fornitore (stato rimane `pending_dispatch`) |

L'email viene inviata tramite **Resend** dal dominio `fornitori.vendiamonoi.it` usando il template puntato da `supplier_configs.email_template_id`.

---

## Il Transactional Outbox Pattern

**Problema:** se n8n crasha dopo aver aggiornato `supplier_orders` ma prima di inviare l'email, l'ordine risulta "inviato" ma l'email non è partita mai.

**Soluzione:** nella stessa transazione PostgreSQL che aggiorna `supplier_orders.status`, n8n scrive un evento in `outbox_events`. Un secondo worker n8n legge `outbox_events` e processa gli eventi in sospeso. Se n8n crasha, l'evento rimane in `outbox_events` e viene ripreso al prossimo ciclo.

Struttura `outbox_events`:
- `idempotency_key` — UNIQUE, costruita come `{supplier_order_id}:{event_type}:{retry_count}`. Impedisce doppio invio anche se n8n riprova.
- `status` — `pending` → `processing` → `processed` / `failed`
- `FOR UPDATE SKIP LOCKED` — più worker n8n possono girare in parallelo senza conflitti.

---

## Le regole di notifica automatica

La tabella `supplier_notification_rules` dice a n8n: *"quando un ordine è in stato X da Y ore, manda il template Z"*.

### Schema della tabella

| Colonna | Tipo | Significato |
|---|---|---|
| `supplier_id` | UUID nullable | NULL = regola globale per tutti i fornitori |
| `email_template_id` | UUID | FK → `email_templates.id` |
| `trigger_status` | TEXT | Lo stato `supplier_order_status` che avvia il countdown |
| `delay_hours` | INTEGER | Ore da attendere dopo il raggiungimento di `trigger_status` |
| `max_sends_per_order` | INTEGER | Max volte che questa regola scatta per lo stesso ordine |
| `is_active` | BOOLEAN | Se false, n8n la ignora |
| `priority` | INTEGER | Valore basso = eseguita prima (se più regole matchano) |
| `condition_json` | JSONB | Flag aggiuntivi letti da n8n |

### Logica di override per fornitore

Se esiste una regola con `supplier_id = X` e una con `supplier_id = NULL` per lo stesso `trigger_status`, n8n usa **solo quella specifica** del fornitore X e ignora quella globale.

Questo permette di avere comportamenti diversi per fornitori problematici (es. più reminder, attese più brevi).

### Le condizioni JSON (`condition_json`)

n8n legge questo JSONB e applica filtri aggiuntivi prima di inviare:

| Flag | Significato |
|---|---|
| `requires_missing_tracking: true` | Invia solo se `supplier_orders.tracking_number` è NULL |
| `requires_missing_confirmation: true` | Invia solo se lo stato non ha mai raggiunto `confirmed` |

Aggiungere nuovi flag richiede di aggiornare anche il workflow n8n che li legge.

### Regole precaricate (default globali)

| Template | Trigger | Attesa | Max | Logica |
|---|---|---|---|---|
| `supplier_confirm_request` | `dispatched` | 24h | 1x | Il fornitore non ha confermato entro 24h dall'invio — sollecito |
| `supplier_tracking_request` | `dispatched` | 48h | 2x | Dopo 48h senza tracking — prima richiesta (può ripetersi) |
| `supplier_tracking_reminder` | `confirmed` | 72h | 1x | Il fornitore ha confermato ma non ha mandato tracking dopo 3 giorni |
| `supplier_late_shipment_alert` | `confirmed` | 120h | 1x | 5 giorni dalla conferma senza spedizione — alert ritardo |
| `supplier_invoice_reminder` | `dispatched` | 168h | 1x | 7 giorni dall'invio ordine — reminder fattura |

---

## I template email disponibili

Tutti i template sono in `email_templates` con `recipient_type = 'supplier'`. Codici esistenti al 2026-04-18:

**Ordini:**
- `supplier_order_confirmation` — Template base invio ordine
- `supplier_order_urgent` — Ordine urgente
- `supplier_order_modified` — Ordine modificato

**Conferma e tracking:**
- `supplier_confirm_request` — Richiesta conferma presa in carico
- `supplier_tracking_request` — Richiesta numero tracking
- `supplier_tracking_reminder` — Reminder tracking (secondo invio)

**Spedizione:**
- `supplier_late_shipment_alert` — Alert ritardo spedizione

**Problemi prodotto:**
- `supplier_return_request` — Richiesta reso
- `supplier_damaged_product` — Prodotto danneggiato
- `supplier_wrong_product` — Prodotto errato
- `supplier_missing_item` — Articolo mancante
- `supplier_chargeback` — Addebito per problema

**Pagamenti:**
- `supplier_invoice_reminder` — Reminder fattura
- `supplier_invoice_overdue` — Fattura scaduta
- `supplier_invoice_dispute` — Contestazione fattura
- `supplier_credit_note_request` — Richiesta nota credito

I template usano variabili **Handlebars** (`{{variabile}}`). Le variabili disponibili sono documentate nel pannello laterale della pagina di modifica template nel gestionale.

---

## Cosa NON fare (errori comuni da evitare)

1. **Non aggiungere nuove regole senza il template corrispondente.** n8n fallirà silenziosamente se `email_template_id` non esiste.

2. **Non creare nuovi stati ENUM senza aggiornare la state machine di n8n.** Lo stato esisterà nel DB ma nessun workflow lo gestirà.

3. **Non modificare `idempotency_key` format in `outbox_events` senza svuotare la tabella.** Cambierà la struttura della chiave e creerà duplicati.

4. **Non usare `supplier_configs.email_template_id` per le notifiche automatiche.** Quel campo è per il template dell'ordine iniziale. Le notifiche automatiche usano `supplier_notification_rules.email_template_id`.

5. **Non eliminare mai righe da `supplier_order_transitions`.** È un audit log immutabile — la policy RLS consente solo INSERT e SELECT.

6. **Non mettere la `service_role` key nel frontend Refine.** Solo n8n server-side e Edge Functions possono usarla. Il frontend usa la `anon` key con RLS.

---

## Come aggiungere una nuova notifica automatica

Checklist completa:

- [ ] Crea il template in `email_templates` (via gestionale o migration SQL)
- [ ] Verifica che `recipient_type = 'supplier'` e `is_active = true`
- [ ] Crea la regola in `supplier_notification_rules` (via gestionale Fornitori → Regole Notifiche)
- [ ] Se la condizione richiede un nuovo flag in `condition_json`, aggiorna il workflow n8n
- [ ] Aggiorna questo documento con la nuova regola nella tabella "Regole precaricate"
- [ ] Sincronizza su GitHub knowledge + Obsidian

---

## Roadmap integrazioni n8n (da implementare in Phase 2)

- [ ] Workflow "OMS Routing" — legge ordini `pending_routing`, assegna fornitore, scrive in `outbox_events`
- [ ] Workflow "OMS Dispatch" — processa `outbox_events` pending, invia email via Resend, aggiorna status
- [ ] Workflow "OMS Notification Checker" — ogni 30 minuti, legge `supplier_notification_rules`, trova ordini che soddisfano le regole, invia notifiche
- [ ] Workflow "OMS Status Updater" — riceve webhook dai fornitori (o polling portali) per aggiornare status

---

*Documento mantenuto da: team Vendiamonoi OS*
*Ultima modifica: 2026-04-18 — Phase 1 completata*
