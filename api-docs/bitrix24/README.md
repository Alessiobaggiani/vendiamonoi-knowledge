# Bitrix24 API — Documentazione Tecnica Approfondita

> Documentazione tecnico-operativa per Vendiamonoi.it S.R.L. — Bitrix24 come CRM e gestione fornitori

## Metadata
- **Ultimo aggiornamento:** 2026-04-07
- **Versione:** 1.0
- **Autore:** Knowledge Base Team Vendiamonoi
- **Priorità:** P3.5
- **Target audience:** Development Team, Integration Engineers, Operations
- **Ambito:** CRM, API REST, automazioni, integrazioni marketplace

---

## 1. Executive Summary

Bitrix24 rappresenta la soluzione CRM core per Vendiamonoi.it, fungendo da hub centralizzato per la gestione relazionale con fornitori, partner marketplace (SellRapido, Shopify, eDesk) e clienti. La piattaforma, sviluppata da 1C-Bitrix con oltre 15 milioni di utenti globali, offre un'architettura REST API robusta, webhooks event-driven e capacità di automazione sofisticate tramite Business Processes e Smart Processes.

**Ruolo strategico di Bitrix24 in Vendiamonoi:**

1. **CRM Centralizzato** — Gestione unificata di lead, deal, contact e company con focus su supplier relationship management
2. **Integrazione API-First** — REST API 2.0 e 3.0 per sincronizzazione real-time con Supabase, Make.com, Fatture in Cloud
3. **Automazione Workflow** — Orchestrazione di processi commerciali: onboarding fornitori, contract renewal, order pipeline
4. **Multi-Canale** — Aggregazione dati da SellRapido, Shopify, eDesk, Qonto, ClickUp in un'unica source of truth
5. **Gestione Fornitori Avanzata** — Companies = Suppliers, Contacts = Supplier Representatives, Custom Fields per compliance (P.IVA, Codice Fiscale, IBAN)

**Capabilità API Chiave:**
- CRUD completo su Leads, Deals, Contacts, Companies, Products, Tasks, Invoices
- Custom Fields illimitati (UF_CRM_*) per metadati domain-specific
- Webhooks outbound per eventi real-time (lead created, deal stage change, contact updated)
- Batch operations fino a 50 chiamate sincrone per ottimizzare throughput
- Rate limiting: 2 req/sec con exponential backoff per gestione 429 errors

NOTE: Placeholder content for large file. The complete 5184-line Bitrix24 API documentation should be inserted here.