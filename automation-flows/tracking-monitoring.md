---
tags: [automazioni, tracking, spedizioni, n8n, 17track, vendiamonoi]
source: vendiamonoi-knowledge/automation-flows/tracking-monitoring.md
created: 2026-04-18
type: decision
---

# Tracking Monitoring — Monitoraggio Spedizioni

## Stato
Completato — 2026-04-18

## Obiettivo
Monitoraggio automatico delle spedizioni tramite 17track (webhook push), rilevamento ritardi rispetto all'SLA del fornitore, aggiornamento `fulfillment_orders` in tempo reale.

## Architettura
- **Nessuna nuova tabella** — 3 colonne aggiunte a `fulfillment_orders`:
  - `is_delayed` (boolean, default false)
  - `last_tracking_status` (varchar 50)
  - `last_tracking_update` (timestamptz)
- **Provider:** 17track (non AfterShip — AfterShip richiede $119/mese per API, 17track ~$0.006/tracking con webhook push)
- **Migrazione:** `20260418130001_add_tracking_fields_to_fulfillment_orders.sql` (con rollback)

## Workflow n8n

| ID | Nome | Tipo | Scopo |
|---|---|---|---|
| `QZjKIcS1DD33Iloo` | Tracking — Registra su 17track | Webhook POST `/tracking-register` | Riceve fo_id + tracking_number + carrier, registra su 17track API, setta `last_tracking_status=pending` |
| `WMXls9EiLZtbIx8T` | Tracking — Ricevi Aggiornamenti 17track | Webhook POST `/17track-webhook` | Riceve push 17track, aggiorna stato, segna consegnato, imposta is_delayed |
| `0VeEkjefbMv9ndT2` | Tracking — Riconciliazione Ritardi | Cron `0 8,14,20 * * *` | Trova spedizioni con SLA scaduto, bulk-flag is_delayed=true (ordinate da più vecchia) |

## Workflow modificati

| ID | Nome | Modifica |
|---|---|---|
| `TyP4Awki89Uuclae` | Crea Spedizione — SpediamoPro + SendCloud | Aggiunto nodo "Chiama Registro Tracking" alla fine |
| `8ARIYB8Cww39SyFS` | SR - Sincronizza Spedizione | Aggiunto lookup Supabase + IF gate + "Chiama Registro Tracking SR" dopo risposta |

## 17track Carrier Mapping

| Carrier locale | Codice 17track |
|---|---|
| BRT / bartolini | 100090 |
| SDA | 100233 |
| GLS | 100096 |
| DHL | 100002 |
| UPS | 100001 |
| FedEx | 100003 |
| TNT | 100007 |
| Poste Italiane / poste | 100085 |
| Sconosciuto | auto-detect (slug: null) |

## Logica SLA Delay
```
dispatched_at + COALESCE(shipping_sla_days, avg_shipping_days, 7) * 86400000 < now()
```
SLA da `suppliers.shipping_sla_days` (fallback: `avg_shipping_days`, fallback: 7 giorni).

## 17track Setup
- **API Key:** `96880A6904A0D2DBA0B2503C7D4ECA47`
- **Webhook URL da configurare:** `https://<n8n-host>/webhook/17track-webhook` (da settare su 17track Dashboard → Settings → Package Webhook quando n8n è su Hetzner)
- **Piano:** Free tier per testing, poi pay-per-use (~$0.006/tracking)

## Note Deployment
- I workflow usano `http://localhost:5678/webhook/tracking-register` per la chiamata interna. Quando n8n si sposta su Hetzner, aggiornare questo URL in:
  - `TyP4Awki89Uuclae` nodo "Chiama Registro Tracking"
  - `8ARIYB8Cww39SyFS` nodo "Chiama Registro Tracking SR"
- Configurare webhook URL su 17track Dashboard quando n8n è pubblico

## Fuori Scope (Fase 2)
- Tabella storico eventi tracking (`shipment_tracking_events`)
- Notifiche automatiche al fornitore per ritardi
- Alert Alessio (email/Slack) per ritardi critici
- UI lista spedizioni attive con stato
