# Qonto API — Deep-Dive Technical Documentation

> Documentazione tecnico-operativa per Vendiamonoi.it S.R.L. — Qonto come business bank e infrastruttura finanziaria

## Metadata
- Ultimo aggiornamento: 2026-04-07
- Versione: 1.0
- Autore: Knowledge Base Team Vendiamonoi
- Priorità: P3.3

---

## 1. Executive Summary

Qonto è una piattaforma di business banking europea fondata nel 2017 a Parigi, che funge da soluzione di gestione finanziaria integrata per PMI e liberi professionisti. Con una valutazione di €4.4 miliardi (2025), oltre 600,000 clienti attivi e profittabilità raggiunta dal 2023, Qonto rappresenta l'infrastruttura finanziaria ideale per Vendiamonoi.it S.R.L., azienda di distribuzione digitale italiana che opera su marketplace europei (Amazon, eBay, Allegro, Carrefour, eMAG) con esigenze complesse di gestione del flusso di cassa, riconciliazione multi-marketplace, e integrazione con sistemi di accounting.

**Qonto fornisce a Vendiamonoi.it**:
- Conti bancari italiani con IBAN (IT) per pagamenti domestici e SEPA
- API REST v2 per automazione trasferimenti, lettura transazioni, e webhooks real-time
- OAuth 2.0 per integrazioni di terze parti sicure
- Gestione carte virtuali e fisiche con controlli di spesa dettagliati
- Direct Debit SEPA per riscossioni
- Integrazione nativa con Fatture in Cloud per riconciliazione contabile automatica
- Connettore Make.com per automazioni workflow senza codice
- Sub-account management fino a 5 conti simultanei (uno per funzione: operativo, marketing, operations)

**Caso d'uso strategico**: Come business bank, Qonto centralizza tutte le operazioni di pagamento e riconciliazione, eliminando la necessità di molteplici conti bancari tradizionali e consentendo automazione end-to-end del ciclo finanziario attraverso API, webhook, e integrazioni con l'ecosistema Vendiamonoi (Supabase, Make.com, Notion, Fatture in Cloud).

---

## 2. Panoramica Piattaforma Qonto

### 2.1 Storia e Posizionamento Aziendale