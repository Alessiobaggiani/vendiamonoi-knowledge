# Make.com — Blueprint Operativi per Distribuzione Digitale

**Vendiamonoi.it — Infrastruttura Automazione Completa**

Versione: 1.0 | Data: 2026-04-02 | Audience: DevOps, Automation Engineers, Platform Architects

---

## EXECUTIVE SUMMARY

Questo documento contiene 10 blueprint operativi production-ready per automatizzare completamente la distribuzione digitale multicanale di Vendiamonoi.it. Ogni blueprint è costruito per operare simultaneamente su 20+ marketplace europei (Amazon, eBay, Kaufland, Mirakl, Bol.com, Cdiscount, Rakuten, ecc.) gestendo milioni di SKU da 100+ supplier.

**Requisiti Infrastrutturali Make.com:**
- Pro Team Plan ($2,000/mese) per operations volume stimato 500M+/mese
- Data Stores: 15+ per state management, queue processing, reconciliation
- Webhooks: 20+ per marketplace/carrier integrations
- API Connections: 50+ per supplier, marketplace, carrier, ERP integrations

---

## SEZIONE 1: ORDER MANAGEMENT AUTOMATION

### 1.1 Blueprint: Order Intake Multi-Marketplace

**Trigger Configuration:**
- Type: Webhooks (incoming data)
- Endpoints: 20+ separate webhook URLs, uno per marketplace/carrier
- Authentication: OAuth 2.0 per Amazon, eBay; API Key per Kaufland, Mirakl; Custom Token per Bol.com
- Idempotency: Webhook ID stored in data store to prevent duplicate processing

**Flow Architecture:**

```
Webhook Receiver
    ↓
Parse Marketplace Identifier (webhook URL path parameter)
    ↓
Router Module (20-way split)
    ├→ Amazon Handler
    ├→ eBay Handler
    ├→ Kaufland Handler
    ├→ Mirakl Handler
    ├→ Bol.com Handler
    ├→ Cdiscount Handler
    ├→ Rakuten Handler
    └→ [16+ other marketplaces]
    ↓
Data Normalization (standard internal order schema)
    ↓
Deduplication Check (data store lookup)
    ↓
[Decision: New Order or Duplicate?]
    ├→ New: Continue to ERP
    └→ Duplicate: Log + Exit
    ↓
ERP Order Creation
    ↓
Marketplace Confirmation (API call back to marketplace)
    ↓
Order Status Update to Data Store
    ↓
```