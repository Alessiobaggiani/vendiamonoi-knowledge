# ClickUp API — Documentazione Tecnico-Operativa Approfondita

> Documentazione tecnico-operativa per Vendiamonoi.it S.R.L. — ClickUp come project management e operations hub centralizzato

## Metadata
- **Ultimo aggiornamento**: 2026-04-07
- **Versione**: 1.0
- **Autore**: Knowledge Base Team Vendiamonoi
- **Priorità**: P3.4
- **Target Audience**: Architetti di sistema, sviluppatori backend, team operations
- **Scope**: API REST v2, webhooks, integrazioni Make.com, Supabase, Notion

---

## 1. Executive Summary

ClickUp rappresenta il **central work operating system** di Vendiamonoi.it, orchestrando tutte le operazioni di project management, order processing, supplier onboarding e customer support. La piattaforma integra nativamente:

**Ruolo Strategico:**
- **Hierarchy Management**: Organizzazione gerarchica Workspace → Space → Folder → List → Task per modellare strutture organizzative complesse
- **Task Automation**: Engine di automazione nativa + webhook HTTP per trigger asincroni
- **Custom Fields**: 15+ tipi di campo per mappare dati specifici di business (marketplace channel, supplier status, order priority)
- **Time Tracking**: Tracciamento billable di attività con integrazione Fatture in Cloud
- **API REST v2**: 100+ endpoint per CRUD completo su tutti gli oggetti

**Connessioni Critiche per Vendiamonoi.it:**
1. **Supabase**: Sincronizzazione real-time di task via webhook → tabelle PostgreSQL
2. **Make.com**: Orchestrazione di workflow complessi (Shopify orders → ClickUp → fulfillment)
3. **Notion**: Esportazione di documentazione e knowledge base
4. **Qonto**: Tracciamento transazioni finanziarie
5. **Fatture in Cloud**: Mapping ore billable a fatture
6. **SellRapido**: Sincronizzazione status task con marketplace channel
7. **eDesk**: Escalation di ticket support

**API Characteristics:**
- REST v2 (non GraphQL)
- Rate limit: 100 req/min standard (1,000+ per Business Plus)
- Autenticazione: Personal Token + OAuth 2.0
- Response format: JSON (application/json)
- Webhook latency: Real-time (< 1 second)
- Versioning: Stabile v2, deprecazione lenta di v1