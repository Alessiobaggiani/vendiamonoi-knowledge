# FattureInCloud API v2 — Integrazione Vendiamonoi OS

## Panoramica

FattureInCloud (FIC) è il gestionale di fatturazione utilizzato da Vendiamonoi.it S.R.L.
L'integrazione via API v2 permette di automatizzare fatturazione, riconciliazione e gestione contabile.

## Credenziali

| Campo | Valore |
|---|---|
| App Name | OS_Vendiamonoi |
| App ID | 18301 |
| Client ID | PeujT1OPdU5D1W2fxgen0Vl1UlNUqqbS |
| Company ID | 1303892 |
| Company Name | Vendiamonoi.it |
| Auth Type | Token personale (Bearer) |
| Base URL | https://api-v2.fattureincloud.it |
| Piano | Complete (scade 06/11/2026) |

> **NOTA**: Il token Bearer è salvato in modo sicuro e NON va committato in chiaro.
> Usare variabili d'ambiente o secrets manager (es. Supabase Vault, n8n credentials).

## Autenticazione

Tutte le richieste usano Bearer token nell'header Authorization:

```
Authorization: Bearer a/eyJ0eXAi...
```

Il token è permanente (tipo "Token personale"), non scade e non richiede refresh.

## Permessi Configurati

Tutti i moduli sono in modalità **write** (lettura + scrittura):

| Modulo FIC | Scope | Livello |
|---|---|---|
| Situazione | fic_situation | write |
| Clienti | fic_clients | write |
| Fornitori | fic_suppliers | write |
| Prodotti | fic_products | write |
| Documenti emessi | fic_issued_documents | write |
| Documenti ricevuti | fic_received_documents | write |
| Ricevute | fic_receipts | write |
| Calendario | fic_calendar | write |
| Archivio | fic_archive | write |
| Tasse | fic_taxes | write |
| Magazzino | fic_stock | write |
| Prima nota | fic_cashbook | write |
| Impostazioni | fic_settings | write |
| Email | fic_emails | write |
| Dipendenti | dic_employees | write |
| Timesheet | dic_timesheet | write |
| Impostazioni DIC | dic_settings | write |

## Endpoint Principali

### Aziende collegate
```
GET /user/companies
```

### Clienti
```
GET /c/{company_id}/entities/clients
POST /c/{company_id}/entities/clients
GET /c/{company_id}/entities/clients/{client_id}
PUT /c/{company_id}/entities/clients/{client_id}
DELETE /c/{company_id}/entities/clients/{client_id}
```

### Fornitori
```
GET /c/{company_id}/entities/suppliers
POST /c/{company_id}/entities/suppliers
GET /c/{company_id}/entities/suppliers/{supplier_id}
PUT /c/{company_id}/entities/suppliers/{supplier_id}
DELETE /c/{company_id}/entities/suppliers/{supplier_id}
```

### Fatture Emesse
```
GET /c/{company_id}/issued_documents?type=invoice
POST /c/{company_id}/issued_documents
GET /c/{company_id}/issued_documents/{document_id}
PUT /c/{company_id}/issued_documents/{document_id}
DELETE /c/{company_id}/issued_documents/{document_id}
```

Tipi documento: invoice, credit_note, receipt, proforma, delivery_note, order, work_report, supplier_order

### Fatture Ricevute
```
GET /c/{company_id}/received_documents?type=expense
POST /c/{company_id}/received_documents
```

### Prodotti
```
GET /c/{company_id}/products
POST /c/{company_id}/products
GET /c/{company_id}/products/{product_id}
PUT /c/{company_id}/products/{product_id}
```

### Prima Nota (Cashbook)
```
GET /c/{company_id}/cashbook
POST /c/{company_id}/cashbook
```

### Archivio
```
GET /c/{company_id}/archive
POST /c/{company_id}/archive
```

## Paginazione

Tutte le liste supportano paginazione:
```
GET /c/{company_id}/entities/clients?page=1&per_page=50
```

Risposta include:
```json
{
  "current_page": 1,
  "last_page": 5,
  "per_page": 50,
  "total": 245
}
```

## Filtri

I filtri usano query string con operatori:
```
GET /c/{company_id}/issued_documents?type=invoice&q=date >= '2024-01-01'
```

## Rate Limits

- Piano Complete: nessun limite documentato
- Best practice: max 10 req/sec per evitare throttling

## Casi d'Uso per Vendiamonoi OS

### 1. Riconciliazione Ordini-Fatture
- Ordine completato su marketplace -> crea fattura su FIC
- Match ordine_id con numero fattura
- Automatizzare via n8n/Make

### 2. Gestione Fornitori
- Sync fornitori tra Supabase e FIC
- Import fatture ricevute da fornitori
- Riconciliazione pagamenti

### 3. Report Fiscali
- Export dati per commercialista
- Calcolo IVA per paese (OSS)
- Generazione report periodici

### 4. Automazione DDT
- Creazione DDT automatici per spedizioni
- Link con ordini marketplace

## Documentazione Ufficiale

- API Reference: https://developers.fattureincloud.it/api-reference
- Guida Auth: https://developers.fattureincloud.it/api-reference/#auth
- SDK disponibili: PHP, Python, C#, Java, TypeScript/JavaScript, Ruby

## Note Tecniche

- Il token ha prefisso `a/` (access token personale)
- Il Company ID (1303892) va usato in tutti gli endpoint come path parameter `{company_id}`
- La connessione è di tipo `master` (accesso completo)
- Il piano FIC è `complete` con scadenza 06/11/2026

---
*Ultimo aggiornamento: 2026-04-17*
*Configurato da: Cowork + Claude*
