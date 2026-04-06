# Fatture in Cloud — Architettura Completa, API v2, Fatturazione Marketplace

## Overview

Fatture in Cloud è il software di fatturazione elettronica e gestione contabile cloud-based più diffuso in Italia, con oltre 500.000 utenti. Sviluppato da TeamSystem S.p.A., è il sistema contabile principale di Vendiamonoi.it S.R.L. Unipersonale per gestire l'intera catena fiscale della distribuzione digitale multi-marketplace.

Per Vendiamonoi, Fatture in Cloud gestisce: fatturazione elettronica B2B/B2C/PA, note di credito, autofatture per commissioni marketplace (TD17/TD18/TD19), regime OSS per vendite cross-border UE, riconciliazione contabile con settlement report marketplace, registrazione fatture fornitori, e prima nota automatizzata.

---

## Informazioni Generali

| Campo | Dettaglio |
|---|---|
| **Nome** | Fatture in Cloud |
| **Tipo** | Software fatturazione, contabilità e gestione aziendale cloud-based |
| **Sito** | https://www.fattureincloud.it |
| **Developer Portal** | https://developers.fattureincloud.it |
| **API Reference** | https://developers.fattureincloud.it/api-reference/ |
| **Help Center** | https://help.fattureincloud.it |
| **Azienda** | TeamSystem S.p.A. |
| **Sede** | Italia |
| **Utenti** | 500.000+ |
| **Licenza** | Proprietaria (SaaS) |
| **Compliance** | SDI (Sistema di Interscambio), Agenzia delle Entrate, GDPR |
| **OpenAPI Spec** | https://github.com/fattureincloud/openapi-fattureincloud |
| **SDK Ufficiali** | C#, Go, Java, JavaScript, PHP, Python, Ruby, TypeScript |
| **Licenza SDK** | MIT |

---

## Piani e Limiti

### Piani Disponibili (Prezzi 2026)

| Piano | Prezzo/mese | Prezzo/anno | Documenti | Anagrafiche | Note |
|---|---|---|---|---|---|
| **Trial** | Gratuito | — | 100 | 500 | Funzionalità limitate |
| **Standard Forfettari** | €4 | €48 | 400 | — | Solo regime forfettario |
| **Standard** | €12 | €144 | — | — | Fatturazione base |
| **Premium** | €21 | €252 | Illimitati | — | + solleciti, Google Calendar |
| **Premium Plus** | €27 | €324 | Illimitati | — | + multi-magazzino, team |
| **Complete** | €51 | €612 | 3.000 | 5.000 | Tutte le funzionalità |

### Funzionalità Incluse in Tutti i Piani

- Fatturazione elettronica SDI
- Conservazione sostitutiva a norma (10 anni)
- App mobile iOS/Android
- Invio e ricezione fatture tramite Sistema di Interscambio
- Dashboard e reportistica base

### Funzionalità Avanzate (Premium+)

- Documenti illimitati
- Solleciti automatici di pagamento
- Sincronizzazione Google Calendar
- Multi-magazzino
- Gestione team multi-utente
- Riconciliazione bancaria con AI
- API access (OAuth 2.0)

### Raccomandazione Vendiamonoi

Per un'operazione con 100+ fornitori e 20+ marketplace, il piano **Complete** o **Premium Plus** è necessario per:
- Volume documenti elevato (fatture + NC + autofatture + registrazioni fornitori)
- Multi-utente (reparto amministrazione)
- API access per automazioni Make.com
- Riconciliazione bancaria avanzata

---

## Architettura API v2

### Fondamentali

| Parametro | Valore |
|---|---|
| **Base URL** | `https://api-v2.fattureincloud.it` |
| **Protocollo** | REST over HTTPS |
| **Formato** | JSON |
| **Autenticazione** | OAuth 2.0 (Authorization Code + Device Code) |
| **Authorization Endpoint** | `https://api-v2.fattureincloud.it/oauth/authorize` |
| **Token Endpoint** | `https://api-v2.fattureincloud.it/oauth/token` |
| **Versioning** | v2 (corrente, v1 deprecata) |
| **OpenAPI Spec** | Disponibile su GitHub |
| **Postman Collection** | Disponibile |

### Autenticazione OAuth 2.0

#### Flow Supportati

**1. Authorization Code Flow (Raccomandato)**
- Per applicazioni che possono conservare un client_secret in sicurezza
- Flow standard OAuth 2.0: authorize → callback → token exchange → access_token + refresh_token
- Ideale per: server-side apps, Make.com, integrazioni backend

**2. Device Code Flow**
- Per applicazioni che non possono conservare un secret in sicurezza
- Flow: device authorization request → user code display → polling per token
- Ideale per: CLI tools, dispositivi IoT, smart TV

**3. Manual Authentication**
- Token generato manualmente dal pannello sviluppatore
- Ideale per: test rapidi, integrazioni semplici, script one-off

#### Processo Authorization Code Flow

```
1. Redirect utente → https://api-v2.fattureincloud.it/oauth/authorize
   ?response_type=code
   &client_id={CLIENT_ID}
   &redirect_uri={REDIRECT_URI}
   &state={RANDOM_STATE}
   &scope={SCOPES}

2. Utente autorizza → callback su redirect_uri con ?code={AUTH_CODE}

3. Exchange code → POST https://api-v2.fattureincloud.it/oauth/token
   {
     "grant_type": "authorization_code",
     "client_id": "{CLIENT_ID}",
     "client_secret": "{CLIENT_SECRET}",
     "code": "{AUTH_CODE}",
     "redirect_uri": "{REDIRECT_URI}"
   }

4. Risposta → { "access_token": "...", "refresh_token": "...", "token_type": "bearer", "expires_in": ... }

5. Usa access_token come header:
   Authorization: Bearer {ACCESS_TOKEN}
```

#### SDK OAuth Helper

Gli SDK ufficiali includono helper per semplificare il flow OAuth:
- Generazione URL di autorizzazione
- Exchange del codice
- Gestione automatica del refresh token
- Disponibile in tutti gli 8 linguaggi supportati

### Scopes (Permessi)

Gli scopes definiscono i permessi dell'access token. Formato: `RESOURCE:LEVEL` dove `r` = read, `a` = all (read + write).

| Scope | Descrizione | Livelli |
|---|---|---|
| `entity.clients` | Anagrafica clienti | `:r` (read), `:a` (all) |
| `entity.suppliers` | Anagrafica fornitori | `:r`, `:a` |
| `products` | Prodotti/servizi | `:r`, `:a` |
| `issued_documents.invoices` | Fatture emesse | `:r`, `:a` |
| `issued_documents.credit_notes` | Note di credito | `:r`, `:a` |
| `issued_documents.receipts` | Ricevute | `:r`, `:a` |
| `issued_documents.orders` | Ordini | `:r`, `:a` |
| `issued_documents.quotes` | Preventivi | `:r`, `:a` |
| `issued_documents.proformas` | Fatture proforma | `:r`, `:a` |
| `issued_documents.delivery_notes` | DDT | `:r`, `:a` |
| `received_documents` | Documenti ricevuti (fatture passive) | `:r`, `:a` |
| `taxes` | F24 e tasse | `:r`, `:a` |
| `archive` | Archivio documenti | `:r`, `:a` |
| `cashbook` | Prima nota | `:r`, `:a` |
| `settings` | Impostazioni azienda | `:r`, `:a` |
| `situation` | Situazione economica | `:r` |

#### Scopes Raccomandati per Vendiamonoi

```
entity.clients:a
entity.suppliers:a
products:r
issued_documents.invoices:a
issued_documents.credit_notes:a
received_documents:a
taxes:r
cashbook:a
settings:r
situation:r
```

Questo set copre: creazione fatture/NC automatiche, registrazione fatture fornitori, prima nota, lettura impostazioni per validazione.

### Rate Limits e Quote

| Tipo | Limite | Finestra | Conseguenza |
|---|---|---|---|
| **Short-term** | Non documentato pubblicamente | Per-request | Throttling |
| **Long-term (orario)** | Quota oraria | 1 ora (reset a inizio ora) | HTTP 403 Forbidden |
| **Long-term (mensile)** | Quota mensile | 1 mese (reset a inizio mese) | HTTP 403 Forbidden |

**Best Practice:**
- Implementare exponential backoff su 403
- Cachare le risposte dove possibile (anagrafiche, impostazioni)
- Usare fieldsets per ridurre il payload
- Batch operations dove disponibili
- Monitorare i rate limit headers nella risposta

### Paginazione

Tutti i metodi `list_*` sono paginati. Non è possibile disabilitare la paginazione.

```json
// Request
GET /c/{company_id}/entities/clients?page=1&per_page=50

// Response
{
  "current_page": 1,
  "data": [...],
  "first_page_url": "...",
  "last_page": 5,
  "last_page_url": "...",
  "next_page_url": "...",
  "per_page": 50,
  "prev_page_url": null,
  "total": 243
}
```

### Fieldsets (Ottimizzazione Response)

Due parametri per personalizzare i campi restituiti:

- **fieldset**: Set predefiniti di campi (`basic`, `detailed`)
- **fields**: Selezione specifica di campi singoli

Questo riduce significativamente il payload, fondamentale per operazioni batch con milioni di SKU.

### Filtri e Query

I metodi `list_*` supportano filtri avanzati tramite query string:

```
// Esempio: fatture emesse dell'ultimo mese
GET /c/{company_id}/issued_documents?type=invoice&q=date >= '2026-03-01' and date <= '2026-03-31'
```

### Gestione Errori

| HTTP Status | Significato |
|---|---|
| `200` | Successo |
| `401 Unauthorized` | Problema con Access Token o Company ID |
| `403 Forbidden` | Permessi insufficienti o rate limit superato |
| `404 Not Found` | Risorsa non trovata |
| `422 Unprocessable Entity` | Dati di input non validi |
| `5xx` | Errore server |

Formato errore:
```json
{
  "error": {
    "message": "Descrizione errore",
    "code": "ERROR_CODE",
    "validation_result": { ... }
  }
}
```

---

## Mappa Completa Endpoint API v2

Tutti gli endpoint usano il prefisso `/c/{company_id}/` (company-scoped). Base URL: `https://api-v2.fattureincloud.it`

### 1. User & Companies (CompaniesApi)

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `get_company_info` | `GET /c/{company_id}/company/info` | Info azienda (ragione sociale, P.IVA, impostazioni) |
| `get_company_plan_usage` | `GET /c/{company_id}/company/plan_usage` | Utilizzo piano corrente (documenti usati, limiti) |

### 2. Clienti (ClientsApi) — 6 metodi

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_clients` | `GET /c/{cid}/entities/clients` | Lista clienti paginata con filtri |
| `get_client` | `GET /c/{cid}/entities/clients/{id}` | Dettaglio singolo cliente |
| `create_client` | `POST /c/{cid}/entities/clients` | Crea nuovo cliente |
| `modify_client` | `PUT /c/{cid}/entities/clients/{id}` | Modifica cliente esistente |
| `delete_client` | `DELETE /c/{cid}/entities/clients/{id}` | Elimina cliente |
| `get_client_info` | `GET /c/{cid}/entities/clients/info` | Info aggregate clienti |

### 3. Fornitori (SuppliersApi) — 5 metodi

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_suppliers` | `GET /c/{cid}/entities/suppliers` | Lista fornitori paginata |
| `get_supplier` | `GET /c/{cid}/entities/suppliers/{id}` | Dettaglio fornitore |
| `create_supplier` | `POST /c/{cid}/entities/suppliers` | Crea fornitore |
| `modify_supplier` | `PUT /c/{cid}/entities/suppliers/{id}` | Modifica fornitore |
| `delete_supplier` | `DELETE /c/{cid}/entities/suppliers/{id}` | Elimina fornitore |

### 4. Prodotti (ProductsApi) — 5 metodi

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_products` | `GET /c/{cid}/products` | Lista prodotti paginata |
| `get_product` | `GET /c/{cid}/products/{id}` | Dettaglio prodotto |
| `create_product` | `POST /c/{cid}/products` | Crea prodotto |
| `modify_product` | `PUT /c/{cid}/products/{id}` | Modifica prodotto |
| `delete_product` | `DELETE /c/{cid}/products/{id}` | Elimina prodotto |

### 5. Documenti Emessi (IssuedDocumentsApi) — 18 metodi ⭐

Questo è il gruppo più importante per Vendiamonoi (fatture, NC, DDT, ordini).

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_issued_documents` | `GET /c/{cid}/issued_documents?type={type}` | Lista documenti emessi per tipo |
| `get_issued_document` | `GET /c/{cid}/issued_documents/{id}` | Dettaglio documento |
| `create_issued_document` | `POST /c/{cid}/issued_documents` | **Crea fattura/NC/preventivo/ordine/DDT** |
| `modify_issued_document` | `PUT /c/{cid}/issued_documents/{id}` | Modifica documento |
| `delete_issued_document` | `DELETE /c/{cid}/issued_documents/{id}` | Elimina documento |
| `get_issued_document_pre_create_info` | `GET /c/{cid}/issued_documents/info` | Info pre-creazione (numerazione, defaults) |
| `get_new_issued_document_totals` | `POST /c/{cid}/issued_documents/totals` | Calcola totali per nuovo documento |
| `get_existing_issued_document_totals` | `POST /c/{cid}/issued_documents/{id}/totals` | Ricalcola totali documento esistente |
| `upload_issued_document_attachment` | `POST /c/{cid}/issued_documents/attachment` | Upload allegato |
| `delete_issued_document_attachment` | `DELETE /c/{cid}/issued_documents/{id}/attachment` | Elimina allegato |
| `get_email_data` | `GET /c/{cid}/issued_documents/{id}/email` | Dati per invio email |
| `schedule_email` | `POST /c/{cid}/issued_documents/{id}/email` | Programma invio email |
| `join_issued_documents` | `POST /c/{cid}/issued_documents/join` | Unisci più documenti |
| `transform_issued_document` | `POST /c/{cid}/issued_documents/transform` | Trasforma documento (es. preventivo→fattura) |
| `list_bin_issued_documents` | `GET /c/{cid}/issued_documents/bin` | Lista documenti nel cestino |
| `get_bin_issued_document` | `GET /c/{cid}/issued_documents/bin/{id}` | Dettaglio documento nel cestino |
| `delete_bin_issued_document` | `DELETE /c/{cid}/issued_documents/bin/{id}` | Elimina definitivamente dal cestino |
| `recover_bin_issued_document` | `POST /c/{cid}/issued_documents/bin/{id}/recover` | Ripristina dal cestino |

#### Tipi di Documento Emesso (type parameter)

| Tipo | Valore API | Uso Vendiamonoi |
|---|---|---|
| Fattura | `invoice` | ⭐ Fatture a clienti B2B/B2C |
| Nota di credito | `credit_note` | ⭐ Resi, rimborsi marketplace |
| Ricevuta | `receipt` | Vendite B2C senza fattura |
| Ordine | `order` | Ordini a fornitori |
| Preventivo | `quote` | Proposte commerciali |
| Proforma | `proforma` | Fatture proforma |
| DDT | `delivery_note` | Documenti di trasporto |
| Autofattura propria | `self_own_invoice` | ⭐ Autofatture (TD17/18/19) |
| Autofattura fornitore | `self_supplier_invoice` | ⭐ Autofatture per fornitori UE |

#### Join & Transform

- **Join**: Unisce più DDT, ordini, preventivi o report di lavoro in un unico documento
- **Transform**: Converte un tipo di documento in un altro (es. preventivo → fattura, DDT → fattura)
- Fondamentale per workflow: ordine → DDT → fattura automatico

### 6. Fattura Elettronica SDI (IssuedEInvoicesApi) — 4 metodi ⭐

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `verify_e_invoice_xml` | `POST /c/{cid}/issued_documents/{id}/e_invoice/verify` | Verifica validità XML prima dell'invio |
| `get_e_invoice_xml` | `GET /c/{cid}/issued_documents/{id}/e_invoice/xml` | Scarica XML FatturaPA |
| `send_e_invoice` | `POST /c/{cid}/issued_documents/{id}/e_invoice/send` | **Invia fattura al SDI** |
| `get_e_invoice_rejection_reason` | `GET /c/{cid}/issued_documents/{id}/e_invoice/rejection_reason` | Motivo scarto SDI |

#### Flow Invio Fattura Elettronica

```
1. create_issued_document (type=invoice) → ID documento
2. verify_e_invoice_xml → validazione XML
3. send_e_invoice → invio al SDI
4. (webhook) → notifica stato (accettata/scartata)
5. [se scartata] get_e_invoice_rejection_reason → motivo
6. [se scartata] modify_issued_document → correggi
7. [se scartata] send_e_invoice → re-invio
```

#### Dry-Run Flag

Il metodo `send_e_invoice` supporta un flag dry-run per testare l'invio senza effettivamente trasmettere al SDI. Fondamentale per testing in ambiente di sviluppo.

**IMPORTANTE**: FiC invia al SDI solo documenti creati tramite FiC (web o API). Non è possibile uploadare un XML esterno e inviarlo.

### 7. Documenti Ricevuti (ReceivedDocumentsApi) — 13 metodi ⭐

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_received_documents` | `GET /c/{cid}/received_documents` | Lista fatture passive |
| `get_received_document` | `GET /c/{cid}/received_documents/{id}` | Dettaglio fattura passiva |
| `create_received_document` | `POST /c/{cid}/received_documents` | **Registra fattura fornitore** |
| `modify_received_document` | `PUT /c/{cid}/received_documents/{id}` | Modifica registrazione |
| `delete_received_document` | `DELETE /c/{cid}/received_documents/{id}` | Elimina |
| `get_received_document_pre_create_info` | `GET /c/{cid}/received_documents/info` | Info pre-creazione |
| `get_new_received_document_totals` | `POST /c/{cid}/received_documents/totals` | Calcola totali |
| `get_existing_received_document_totals` | `POST /c/{cid}/received_documents/{id}/totals` | Ricalcola totali |
| `upload_received_document_attachment` | `POST /c/{cid}/received_documents/attachment` | Upload allegato |
| `delete_received_document_attachment` | `DELETE /c/{cid}/received_documents/{id}/attachment` | Elimina allegato |
| `list_bin_received_documents` | `GET /c/{cid}/received_documents/bin` | Cestino |
| `get_bin_received_document` | `GET /c/{cid}/received_documents/bin/{id}` | Dettaglio cestino |
| `delete_bin_received_document` | `DELETE /c/{cid}/received_documents/bin/{id}` | Elimina definitivo |
| `recover_bin_received_document` | `POST /c/{cid}/received_documents/bin/{id}/recover` | Ripristina |

#### Pending Received Documents

FiC supporta anche i "Pending Received Documents" — documenti ricevuti via SDI in attesa di registrazione contabile. L'API permette di listarli e gestirli programmaticamente.

### 8. Ricevute (ReceiptsApi) — 7 metodi

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_receipts` | `GET /c/{cid}/receipts` | Lista ricevute |
| `get_receipt` | `GET /c/{cid}/receipts/{id}` | Dettaglio ricevuta |
| `create_receipt` | `POST /c/{cid}/receipts` | Crea ricevuta |
| `modify_receipt` | `PUT /c/{cid}/receipts/{id}` | Modifica ricevuta |
| `delete_receipt` | `DELETE /c/{cid}/receipts/{id}` | Elimina ricevuta |
| `get_receipt_pre_create_info` | `GET /c/{cid}/receipts/info` | Info pre-creazione |
| `get_receipts_monthly_totals` | `GET /c/{cid}/receipts/monthly_totals` | Totali mensili ricevute |

### 9. Prima Nota (CashbookApi) — 5 metodi ⭐

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_cashbook_entries` | `GET /c/{cid}/cashbook` | Lista movimenti prima nota |
| `get_cashbook_entry` | `GET /c/{cid}/cashbook/{id}` | Dettaglio movimento |
| `create_cashbook_entry` | `POST /c/{cid}/cashbook` | **Crea registrazione prima nota** |
| `modify_cashbook_entry` | `PUT /c/{cid}/cashbook/{id}` | Modifica |
| `delete_cashbook_entry` | `DELETE /c/{cid}/cashbook/{id}` | Elimina |

### 10. Archivio (ArchiveApi) — 6 metodi

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_archive_documents` | `GET /c/{cid}/archive` | Lista documenti archiviati |
| `get_archive_document` | `GET /c/{cid}/archive/{id}` | Dettaglio |
| `create_archive_document` | `POST /c/{cid}/archive` | Archivia documento |
| `modify_archive_document` | `PUT /c/{cid}/archive/{id}` | Modifica |
| `delete_archive_document` | `DELETE /c/{cid}/archive/{id}` | Elimina |
| `upload_archive_document_attachment` | `POST /c/{cid}/archive/attachment` | Upload allegato |

### 11. Tasse/F24 (TaxesApi) — 7 metodi

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_f24` | `GET /c/{cid}/taxes` | Lista F24 |
| `get_f24` | `GET /c/{cid}/taxes/{id}` | Dettaglio F24 |
| `create_f24` | `POST /c/{cid}/taxes` | Crea F24 |
| `modify_f24` | `PUT /c/{cid}/taxes/{id}` | Modifica F24 |
| `delete_f24` | `DELETE /c/{cid}/taxes/{id}` | Elimina F24 |
| `upload_f24_attachment` | `POST /c/{cid}/taxes/attachment` | Upload allegato |
| `delete_f24_attachment` | `DELETE /c/{cid}/taxes/{id}/attachment` | Elimina allegato |

### 12. Email (EmailsApi) — 1 metodo

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `list_emails` | `GET /c/{cid}/emails` | Lista email inviate |

### 13. Listini Prezzo (PriceListsApi) — 2 metodi

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `get_price_lists` | `GET /c/{cid}/info/price_lists` | Lista listini |
| `get_price_list_items` | `GET /c/{cid}/info/price_lists/{id}/items` | Items di un listino |

### 14. Impostazioni (SettingsApi) — 12 metodi

| Metodo | Endpoint | Descrizione |
|---|---|---|
| `get_tax_profile` | `GET /c/{cid}/settings/tax_profile` | Profilo fiscale azienda |
| `create_payment_account` | `POST /c/{cid}/settings/payment_accounts` | Crea conto pagamento |
| `get_payment_account` | `GET /c/{cid}/settings/payment_accounts/{id}` | Dettaglio conto |
| `modify_payment_account` | `PUT /c/{cid}/settings/payment_accounts/{id}` | Modifica conto |
| `delete_payment_account` | `DELETE /c/{cid}/settings/payment_accounts/{id}` | Elimina conto |
| `create_payment_method` | `POST /c/{cid}/settings/payment_methods` | Crea metodo pagamento |
| `get_payment_method` | `GET /c/{cid}/settings/payment_methods/{id}` | Dettaglio metodo |
| `modify_payment_method` | `PUT /c/{cid}/settings/payment_methods/{id}` | Modifica metodo |
| `delete_payment_method` | `DELETE /c/{cid}/settings/payment_methods/{id}` | Elimina metodo |
| `create_vat_type` | `POST /c/{cid}/settings/vat_types` | Crea tipo IVA |
| `get_vat_type` | `GET /c/{cid}/settings/vat_types/{id}` | Dettaglio tipo IVA |
| `modify_vat_type` | `PUT /c/{cid}/settings/vat_types/{id}` | Modifica tipo IVA |
| `delete_vat_type` | `DELETE /c/{cid}/settings/vat_types/{id}` | Elimina tipo IVA |

### 15. Info (InfoApi) — 17 metodi

Metodi di lookup per valori di riferimento:

| Metodo | Descrizione |
|---|---|
| `list_archive_categories` | Categorie archivio |
| `list_cities` | Comuni italiani |
| `list_cost_centers` | Centri di costo |
| `list_countries` | Paesi |
| `list_currencies` | Valute |
| `list_delivery_notes_default_causals` | Causali DDT predefinite |
| `list_detailed_countries` | Paesi con dettagli (codice ISO, etc.) |
| `list_languages` | Lingue |
| `list_payment_accounts` | Conti di pagamento |
| `list_payment_methods` | Metodi di pagamento |
| `list_product_categories` | Categorie prodotti |
| `list_received_document_categories` | Categorie documenti ricevuti |
| `list_revenue_centers` | Centri di ricavo |
| `list_templates` | Template documenti |
| `list_units_of_measure` | Unità di misura |
| `list_vat_types` | Tipi IVA configurati |

### Riepilogo: 14 API Classes, 131 Metodi Totali

| Classe | Metodi | Priorità Vendiamonoi |
|---|---|---|
| IssuedDocumentsApi | 18 | ⭐⭐⭐ Critico |
| ReceivedDocumentsApi | 13 | ⭐⭐⭐ Critico |
| InfoApi | 17 | ⭐⭐ Importante |
| SettingsApi | 12 | ⭐⭐ Importante |
| ReceiptsApi | 7 | ⭐ Utile |
| TaxesApi | 7 | ⭐ Utile |
| ClientsApi | 6 | ⭐⭐⭐ Critico |
| ArchiveApi | 6 | ⭐ Utile |
| SuppliersApi | 5 | ⭐⭐⭐ Critico |
| CashbookApi | 5 | ⭐⭐⭐ Critico |
| ProductsApi | 5 | ⭐ Utile |
| IssuedEInvoicesApi | 4 | ⭐⭐⭐ Critico |
| CompaniesApi | 2 | ⭐⭐ Importante |
| PriceListsApi | 2 | ⭐ Utile |
| EmailsApi | 1 | ⭐ Utile |

---

## Webhooks

### Architettura

Fatture in Cloud supporta webhooks per notifiche real-time sugli eventi. I webhook seguono lo standard **CloudEvents Core Specification** usando il **Binary Content Mode** (attributi CloudEvents come HTTP headers).

### Come Funzionano

1. **Subscription**: La tua app si registra per ricevere notifiche su specifici eventi
2. **Evento**: Quando accade qualcosa in FiC (es. nuova fattura creata), il sistema genera un evento
3. **Notifica**: FiC invia un POST al tuo endpoint con i dati dell'evento
4. **Risposta**: Il tuo endpoint deve rispondere rapidamente (timeout breve)
5. **Processing**: Elabora la notifica in modo asincrono

### Formato Notifica

```json
{
  "data": {
    "ids": [12345, 67890]
  }
}
```

**IMPORTANTE**: Il body contiene SOLO gli ID delle risorse modificate, NON i dati sensibili. La tua app deve fare una chiamata GET per recuperare i dettagli.

### Tipi di Notifica

| Tipo | Evento | Scope Richiesto |
|---|---|---|
| `it.fattureincloud.webhooks.issued_documents.invoices.create` | Nuova fattura creata | `issued_documents.invoices:r` |
| `it.fattureincloud.webhooks.issued_documents.invoices.update` | Fattura modificata | `issued_documents.invoices:r` |
| `it.fattureincloud.webhooks.issued_documents.invoices.delete` | Fattura eliminata | `issued_documents.invoices:r` |
| `it.fattureincloud.webhooks.issued_documents.credit_notes.*` | Eventi note di credito | `issued_documents.credit_notes:r` |
| `it.fattureincloud.webhooks.issued_documents.e_invoices.status_update` | ⭐ Cambio stato e-fattura SDI | `issued_documents.invoices:r` |
| `it.fattureincloud.webhooks.received_documents.e_invoices.receive` | ⭐ Nuova fattura ricevuta via SDI | `received_documents:r` |
| `it.fattureincloud.webhooks.received_documents.*.create/update/delete` | CRUD documenti ricevuti | `received_documents:r` |
| `it.fattureincloud.webhooks.entities.clients.*` | CRUD clienti | `entity.clients:r` |
| `it.fattureincloud.webhooks.entities.suppliers.*` | CRUD fornitori | `entity.suppliers:r` |
| `it.fattureincloud.webhooks.products.*` | CRUD prodotti | `products:r` |
| `it.fattureincloud.webhooks.cashbook.*` | CRUD prima nota | `cashbook:r` |
| `it.fattureincloud.webhooks.subscriptions.welcome` | Conferma sottoscrizione | — |

### Webhook Critici per Vendiamonoi

1. **`e_invoices.status_update`** — per monitorare accettazione/scarto SDI in tempo reale
2. **`received_documents.e_invoices.receive`** — per processare automaticamente fatture fornitori ricevute via SDI
3. **`issued_documents.invoices.create`** — per sincronizzare con Supabase
4. **`cashbook.create`** — per audit trail movimenti contabili

### Best Practice Webhook

- Rispondere al POST entro pochi secondi (timeout breve)
- Processare il payload in modo asincrono (queue/worker)
- Implementare retry logic per chiamate GET successive
- Verificare gli headers CloudEvents per autenticità
- Loggare tutti gli eventi per debugging

---

## SDK Ufficiali

### Linguaggi Supportati

| Linguaggio | Repository | Package |
|---|---|---|
| **Python** | `fattureincloud/fattureincloud-python-sdk` | `fattureincloud-python-sdk` |
| **PHP** | `fattureincloud/fattureincloud-php-sdk` | `fattureincloud/fattureincloud-php-sdk` |
| **JavaScript** | `fattureincloud/fattureincloud-js-sdk` | `@fattureincloud/fattureincloud-js-sdk` |
| **TypeScript** | `fattureincloud/fattureincloud-ts-sdk` | `@fattureincloud/fattureincloud-ts-sdk` |
| **Java** | `fattureincloud/fattureincloud-java-sdk` | Maven |
| **C#** | `fattureincloud/fattureincloud-csharp-sdk` | NuGet |
| **Go** | `fattureincloud/fattureincloud-go-sdk` | Go module |
| **Ruby** | `fattureincloud/fattureincloud-ruby-sdk` | Gem |

### Caratteristiche SDK

- Generati automaticamente dall'OpenAPI Specification tramite OpenAPI Generator
- Modelli tipizzati per tutte le entità
- OAuth helper integrato
- Gestione automatica paginazione
- Tutti rilasciati con licenza MIT
- Documentazione inclusa con esempi

### Esempio Creazione Fattura (Python)

```python
import fattureincloud_python_sdk as fic
from fattureincloud_python_sdk.api import issued_documents_api
from fattureincloud_python_sdk.model.create_issued_document_request import CreateIssuedDocumentRequest
from fattureincloud_python_sdk.model.issued_document import IssuedDocument
from fattureincloud_python_sdk.model.issued_document_type import IssuedDocumentType

# Configurazione
configuration = fic.Configuration()
configuration.access_token = "YOUR_ACCESS_TOKEN"

with fic.ApiClient(configuration) as api_client:
    api = issued_documents_api.IssuedDocumentsApi(api_client)
    
    company_id = 12345
    
    document = IssuedDocument(
        type=IssuedDocumentType("invoice"),
        entity=IssuedDocumentEntity(
            name="Cliente SRL",
            vat_number="IT12345678901"
        ),
        items_list=[
            IssuedDocumentItemsListItem(
                product_id=1,
                name="Servizio distribuzione digitale",
                net_price=100.00,
                qty=1,
                vat=IssuedDocumentItemsListItemVat(id=0)  # 22%
            )
        ],
        payment_method=PaymentMethod(id=1)
    )
    
    request = CreateIssuedDocumentRequest(data=document)
    response = api.create_issued_document(company_id, request)
    
    # Invia al SDI
    e_invoice_api = issued_e_invoices_api.IssuedEInvoicesApi(api_client)
    e_invoice_api.send_e_invoice(company_id, response.data.id)
```

---

## Integrazione Make.com

### Connessione

Fatture in Cloud ha un'app nativa su Make.com che si collega tramite OAuth 2.0. La connessione richiede:
1. Client ID e Client Secret dall'app FiC Developer Portal
2. Autorizzazione OAuth dell'utente
3. Selezione dell'azienda (company_id)

### Trigger Disponibili

| Trigger | Descrizione | Uso Vendiamonoi |
|---|---|---|
| **Watch New Clients** | Si attiva quando viene creato un nuovo cliente | Sync clienti → Supabase |
| **Watch New Issued Documents** | Si attiva quando viene creato un nuovo documento emesso | ⭐ Sync fatture → Supabase, notifiche |

### Azioni Disponibili

| Azione | Descrizione | Uso Vendiamonoi |
|---|---|---|
| **Create Document** | Crea un nuovo documento emesso (fattura, NC, etc.) | ⭐⭐⭐ Fatturazione automatica |
| **Create Client** | Crea un nuovo cliente | ⭐⭐ Onboarding clienti da marketplace |
| **Create Supplier** | Crea un nuovo fornitore | ⭐⭐ Onboarding fornitori |
| **Create Product** | Crea un nuovo prodotto | Sync prodotti |
| **Create Receipt** | Crea una nuova ricevuta | Vendite B2C |
| **Create Received Document** | Registra una fattura passiva | ⭐⭐⭐ Registrazione fatture fornitori |
| **Create Cashbook Entry** | Crea un movimento di prima nota | ⭐⭐ Riconciliazione |
| **Create F24** | Crea un modello F24 | Scadenze fiscali |

### Scenari Make.com per Vendiamonoi

#### Scenario 1: Fatturazione Automatica Marketplace

```
Trigger: Webhook SellRapido (nuovo ordine evaso)
→ Router: tipo marketplace
  → Amazon: crea fattura con specifiche Amazon
  → eBay: crea fattura con specifiche eBay
  → Kaufland: crea fattura con specifiche Kaufland
→ FiC: Create Document (type=invoice)
→ FiC: Send E-Invoice (via HTTP module → API v2)
→ Supabase: registra stato fattura
```

#### Scenario 2: Registrazione Automatica Fatture Fornitori

```
Trigger: FiC Watch → nuova fattura ricevuta via SDI
→ Filtro: fornitore nel nostro DB?
→ FiC: Get Received Document (dettagli completi)
→ Supabase: registra fattura fornitore
→ Supabase: aggiorna saldo fornitore
→ [Opzionale] Slack/Email: notifica reparto acquisti
```

#### Scenario 3: Riconciliazione Settlement Marketplace

```
Trigger: Schedulato (settimanale)
→ Supabase: query ordini periodo
→ FiC: list_issued_documents (fatture periodo)
→ Confronto: ordini vs fatture
→ Supabase: registra riconciliazione
→ [Se discrepanze] Alert: notifica amministrazione
```

#### Scenario 4: Autofatture Commissioni Marketplace

```
Trigger: Schedulato (mensile, dopo settlement)
→ Supabase: query commissioni marketplace del mese
→ Router: tipo commissione
  → Amazon LU (servizi): crea autofattura TD17
  → Amazon DE (logistica FBA): crea autofattura TD17
  → eBay IE (servizi): crea autofattura TD17
→ FiC: Create Document (type=self_own_invoice)
→ FiC: Send E-Invoice
→ Supabase: registra autofattura
```

### Limitazioni Make.com vs API Diretta

| Funzione | Make.com App | API v2 Diretta |
|---|---|---|
| Crea documenti | ✅ | ✅ |
| Invio SDI | ❌ (serve HTTP module) | ✅ |
| Webhook ricezione | ❌ (solo polling triggers) | ✅ |
| Verifica XML | ❌ | ✅ |
| Fieldsets | ❌ | ✅ |
| Filtri avanzati | Limitati | ✅ Completi |
| Bin (cestino) | ❌ | ✅ |
| Join/Transform | ❌ | ✅ |

**Raccomandazione**: Usare l'app Make nativa per operazioni CRUD semplici. Per operazioni avanzate (invio SDI, webhook, filtri complessi), usare il modulo HTTP di Make con chiamate dirette all'API v2.

---

## Fatturazione Marketplace — Guida Operativa

### Principi Fondamentali

#### 1. Obbligo di Fatturazione

- **B2B** (verso soggetti con P.IVA): fattura SEMPRE obbligatoria, anche se non richiesta
- **B2C** (verso consumatori privati): fattura obbligatoria solo se richiesta dal cliente
- **Marketplace come intermediari**: il marketplace NON emette fattura per il venditore — il venditore deve emettere la fattura per l'acquirente
- **Eccezione Amazon**: per vendite B2C in certi paesi, Amazon può emettere fattura per conto del venditore (VCS - VAT Calculation Service)

#### 2. Formato FatturaPA

- Standard XML obbligatorio per fatture elettroniche in Italia
- Invio tramite Sistema di Interscambio (SDI)
- Codice destinatario: per marketplace, spesso "0000000" + PEC
- Conservazione digitale obbligatoria per 10 anni

### Autofatture per Commissioni Marketplace (TD17/TD18/TD19)

Quando ricevi fatture da sedi UE dei marketplace (Lussemburgo, Irlanda, etc.) senza IVA italiana, devi emettere autofattura.

#### Tipi di Autofattura

| Codice | Tipo | Quando Usare | Esempio |
|---|---|---|---|
| **TD17** | Integrazione/autofattura per acquisto servizi dall'estero | Commissioni marketplace (servizi) | Commissioni Amazon LU, eBay IE, Kaufland DE |
| **TD18** | Integrazione per acquisto beni intracomunitari | Acquisto beni da fornitore UE | Acquisto merce da produttore DE/FR |
| **TD19** | Integrazione/autofattura per acquisto beni art. 17 c.2 DPR 633/72 | Beni già in Italia da fornitore estero | Merce in magazzino IT da fornitore CN via marketplace |

#### Processo Autofattura in Fatture in Cloud

```
1. Ricevi fattura marketplace (es. Amazon LU, senza IVA)
2. In FiC: Crea Documento → tipo "Autofattura"
3. Seleziona codice TD17 (servizi) o TD18 (beni UE) o TD19 (beni in IT)
4. Inserisci dati fattura originale:
   - Fornitore: Amazon Europe Core S.à r.l. (LU)
   - Importo: come da fattura originale
   - IVA: integra con aliquota italiana 22%
5. Invia al SDI
6. Il sistema genera automaticamente:
   - IVA a debito (22% sull'importo)
   - IVA a credito (22% sull'importo) → effetto neutro
```

#### Scadenze Autofatture

- **TD17** (servizi): entro il 15 del mese successivo alla ricezione fattura
- **TD18** (beni UE): entro il 15 del mese successivo alla ricezione fattura
- **TD19** (beni art.17): entro il 15 del mese successivo alla ricezione fattura

**ATTENZIONE**: Ritardo = sanzione. Automatizzare con Make.com è fondamentale per non perdere scadenze con 20+ marketplace.

#### Automazione via API

```python
# Esempio: crea autofattura TD17 per commissioni Amazon
autofattura = IssuedDocument(
    type=IssuedDocumentType("self_own_invoice"),
    ei_raw={
        "TipoDocumento": "TD17"  # Servizi dall'estero
    },
    entity=IssuedDocumentEntity(
        name="Amazon Europe Core S.à r.l.",
        vat_number="LU26375245",
        country="LU"
    ),
    items_list=[
        IssuedDocumentItemsListItem(
            name="Commissioni marketplace Marzo 2026",
            net_price=1500.00,
            qty=1,
            vat=IssuedDocumentItemsListItemVat(id=VAT_22_ID)
        )
    ]
)
```

### Regime OSS (One Stop Shop)

#### Cos'è

Il regime OSS permette ai venditori e-commerce di dichiarare e versare l'IVA dovuta su vendite B2C a consumatori in altri paesi UE attraverso un unico sportello nel proprio paese di stabilimento (Italia per Vendiamonoi).

#### Quando è Obbligatorio

- Vendite B2C transfrontaliere a consumatori finali in altri paesi UE
- Soglia complessiva: **€10.000/anno** di vendite B2C cross-border in tutta l'UE
- Superata la soglia, si applica l'IVA del paese di destinazione

#### Aliquote IVA per Paese (Principali Mercati Vendiamonoi)

| Paese | Aliquota Standard | Aliquota Ridotta |
|---|---|---|
| Italia | 22% | 4%, 5%, 10% |
| Germania | 19% | 7% |
| Francia | 20% | 5.5%, 10% |
| Spagna | 21% | 10% |
| Paesi Bassi | 21% | 9% |
| Belgio | 21% | 6%, 12% |
| Austria | 20% | 10%, 13% |
| Polonia | 23% | 5%, 8% |
| Svezia | 25% | 6%, 12% |
| Portogallo | 23% | 6%, 13% |

#### Configurazione OSS in Fatture in Cloud

1. **Creare numerazione separata** per fatture OSS
2. **Creare codici IVA per paese**: es. "IVA DE 19%", "IVA FR 20%", "IVA ES 21%"
3. **Creare conto IVA vendite OSS dedicato** per distinguere da IVA nazionale
4. **Emettere fatture** con il codice IVA del paese di destinazione
5. **Dichiarazione trimestrale OSS** all'Agenzia delle Entrate

#### Dichiarazione Trimestrale OSS

| Trimestre | Scadenza Dichiarazione | Scadenza Pagamento |
|---|---|---|
| Q1 (Gen-Mar) | 30 Aprile | 30 Aprile |
| Q2 (Apr-Giu) | 31 Luglio | 31 Luglio |
| Q3 (Lug-Set) | 31 Ottobre | 31 Ottobre |
| Q4 (Ott-Dic) | 31 Gennaio | 31 Gennaio |

#### OSS + Marketplace: Chi Paga l'IVA?

**Regola fondamentale**: Se il marketplace è un "deemed supplier" (facilitatore), è il MARKETPLACE a dichiarare e versare l'IVA, NON il venditore.

Amazon, eBay e la maggior parte dei grandi marketplace sono deemed supplier per vendite B2C. Questo significa:
- Il marketplace applica e incassa l'IVA dal consumatore
- Il marketplace versa l'IVA all'autorità fiscale del paese di destinazione
- Il venditore fattura al marketplace a IVA 0% (cessione al facilitatore)
- Il venditore NON deve includere queste vendite nella dichiarazione OSS

**ATTENZIONE**: Questa regola si applica SOLO per vendite B2C facilitate dal marketplace. Per vendite B2B, il venditore resta responsabile della fatturazione IVA.

### Riconciliazione Contabile Marketplace

#### Il Problema

I marketplace (Amazon, eBay, etc.) accreditano i pagamenti in modo cumulativo e al netto delle commissioni. Un singolo settlement di Amazon può contenere centinaia di transazioni:
- Ricavi vendite
- Rimborsi
- Commissioni referral
- Commissioni FBA
- Commissioni storage
- Advertising costs
- Adjustment

#### Processo di Riconciliazione

```
1. SETTLEMENT REPORT (dal marketplace)
   → Esporta report periodo da Seller Central / Seller Hub
   → Contiene: ordini, rimborsi, commissioni, importo netto

2. REGISTRAZIONE IN FATTURE IN CLOUD
   a) Fatture attive: già registrate (emesse per ogni vendita B2B)
   b) Commissioni: registrate come fatture passive (ricevute dal marketplace)
   c) Rimborsi: registrate come note di credito
   d) Settlement netto: confronto con accredito bancario

3. CONTO TRANSITORIO "SOSPESI MARKETPLACE"
   → Registra ogni vendita in contropartita a "Sospesi Amazon/eBay"
   → Quando arriva il settlement, chiudi il sospeso
   → La differenza = commissioni (già registrate come fattura passiva)

4. RICONCILIAZIONE BANCARIA
   → Importa estratto conto in FiC
   → AI di FiC analizza e propone matching
   → Conferma e registra in prima nota
```

#### Automazione Riconciliazione con Make.com

```
Scenario: Riconciliazione Settlement Amazon (settimanale)

1. Trigger: Schedulato (ogni lunedì)
2. Amazon SP-API: scarica settlement report ultimi 7 giorni
3. Parser: estrai transazioni (vendite, rimborsi, commissioni)
4. Per ogni transazione:
   a. Supabase: cerca ordine corrispondente
   b. FiC API: cerca fattura/NC corrispondente
   c. Confronta importi
   d. Supabase: registra stato riconciliazione
5. FiC API: crea movimenti prima nota per commissioni
6. Report: genera riepilogo discrepanze
7. Alert: notifica se discrepanze > soglia
```

#### Metriche Riconciliazione

| KPI | Target | Frequenza |
|---|---|---|
| Tasso riconciliazione automatica | >95% | Settimanale |
| Tempo medio riconciliazione | <24h dal settlement | Per settlement |
| Discrepanze irrisolte | <1% | Mensile |
| Autofatture emesse entro scadenza | 100% | Mensile |

---

## Sincronizzazione con Supabase

### Modello Dati Consigliato

#### Tabella `fic_issued_documents`

```sql
CREATE TABLE fic_issued_documents (
  id BIGINT PRIMARY KEY,
  fic_id BIGINT UNIQUE NOT NULL,  -- ID in Fatture in Cloud
  company_id BIGINT NOT NULL,
  type TEXT NOT NULL,  -- invoice, credit_note, self_own_invoice
  number INTEGER,
  numeration TEXT,
  date DATE NOT NULL,
  year INTEGER NOT NULL,
  entity_name TEXT,
  entity_vat_number TEXT,
  amount_net DECIMAL(12,2),
  amount_vat DECIMAL(12,2),
  amount_gross DECIMAL(12,2),
  currency TEXT DEFAULT 'EUR',
  e_invoice_status TEXT,  -- sent, accepted, rejected, pending
  sdi_message_id TEXT,
  payment_status TEXT,  -- paid, unpaid, partial
  marketplace_order_id TEXT,  -- link a ordine marketplace
  marketplace TEXT,  -- amazon, ebay, kaufland, etc.
  oss_country TEXT,  -- paese OSS se applicabile
  td_code TEXT,  -- TD17, TD18, TD19 per autofatture
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  raw_data JSONB  -- dati completi FiC per riferimento
);

CREATE INDEX idx_fic_issued_docs_type ON fic_issued_documents(type);
CREATE INDEX idx_fic_issued_docs_marketplace ON fic_issued_documents(marketplace);
CREATE INDEX idx_fic_issued_docs_date ON fic_issued_documents(date);
CREATE INDEX idx_fic_issued_docs_e_invoice_status ON fic_issued_documents(e_invoice_status);
```

#### Tabella `fic_received_documents`

```sql
CREATE TABLE fic_received_documents (
  id BIGINT PRIMARY KEY,
  fic_id BIGINT UNIQUE NOT NULL,
  company_id BIGINT NOT NULL,
  type TEXT NOT NULL,
  date DATE NOT NULL,
  supplier_name TEXT,
  supplier_vat_number TEXT,
  amount_net DECIMAL(12,2),
  amount_vat DECIMAL(12,2),
  amount_gross DECIMAL(12,2),
  currency TEXT DEFAULT 'EUR',
  payment_status TEXT,
  payment_due_date DATE,
  category TEXT,  -- commissioni, logistica, advertising, merce
  marketplace TEXT,
  supplier_id BIGINT REFERENCES suppliers(id),
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  raw_data JSONB
);
```

#### Tabella `fic_reconciliation`

```sql
CREATE TABLE fic_reconciliation (
  id BIGINT PRIMARY KEY,
  marketplace TEXT NOT NULL,
  period_start DATE NOT NULL,
  period_end DATE NOT NULL,
  settlement_id TEXT,
  total_sales DECIMAL(12,2),
  total_refunds DECIMAL(12,2),
  total_commissions DECIMAL(12,2),
  total_net DECIMAL(12,2),
  bank_amount DECIMAL(12,2),
  discrepancy DECIMAL(12,2),
  status TEXT DEFAULT 'pending',  -- pending, matched, discrepancy, resolved
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## Errori Comuni da Evitare

### 1. Non Emettere Autofatture

**Errore**: Ignorare le fatture marketplace estere (Amazon LU, eBay IE) pensando che non servano autofatture.
**Conseguenza**: Sanzione dal 100% al 200% dell'IVA non integrata.
**Soluzione**: Automatizzare con Make.com → per ogni fattura estera ricevuta, creare autofattura TD17/18/19.

### 2. Confondere OSS con Autofatture

**Errore**: Pensare che il regime OSS copra anche le commissioni marketplace.
**Chiarimento**: OSS riguarda le VENDITE B2C cross-border. Le commissioni marketplace sono ACQUISTI di servizi da integrare con autofattura.

### 3. Non Riconciliare i Settlement

**Errore**: Registrare l'accredito bancario del marketplace come un'unica entrata.
**Conseguenza**: Impossibile verificare correttezza importi, commissioni, rimborsi.
**Soluzione**: Scomporre ogni settlement nelle sue componenti e riconciliare con fatture emesse.

### 4. Fatturare Vendite B2C Facilitate

**Errore**: Emettere fattura al consumatore finale per vendite B2C su marketplace che agisce come deemed supplier.
**Chiarimento**: Se Amazon/eBay è deemed supplier, il venditore fattura al marketplace, non al consumatore.

### 5. Usare un Solo Conto IVA per Tutto

**Errore**: Registrare IVA nazionale, IVA OSS e IVA autofatture nello stesso conto.
**Conseguenza**: Impossibile generare dichiarazione OSS corretta.
**Soluzione**: Conti IVA separati per: nazionale, OSS per paese, reverse charge.

### 6. Non Testare l'Invio SDI

**Errore**: Andare in produzione senza testare il flow di invio e-fattura.
**Soluzione**: Usare il flag dry-run dell'API `send_e_invoice` per testare senza invio reale.

### 7. Ignorare i Rate Limits

**Errore**: Fare chiamate API massive senza rispettare i limiti.
**Conseguenza**: HTTP 403 e blocco temporaneo.
**Soluzione**: Implementare exponential backoff, caching, e batch operations.

---

## Specifiche per Vendiamonoi

### Stack Integrato

```
Marketplace (20+)
    ↓ ordini
SellRapido (channel manager)
    ↓ ordini processati
Make.com (automazione)
    ↓ crea fatture/NC/autofatture
Fatture in Cloud (contabilità)
    ↓ webhook stato SDI
Make.com
    ↓ sync dati
Supabase (database centrale)
    ↓ analytics
Dashboard (reporting)
```

### Volume Stimato Mensile

| Tipo Documento | Volume Stimato | Automazione |
|---|---|---|
| Fatture B2B | 50-200/mese | Make → FiC API |
| Fatture B2C (su richiesta) | 10-50/mese | Make → FiC API |
| Note di credito | 20-80/mese | Make → FiC API |
| Autofatture TD17 (commissioni) | 20-30/mese (1 per marketplace/mese) | Make → FiC API |
| Fatture fornitori ricevute | 100-300/mese | SDI → FiC → webhook → Make → Supabase |
| Movimenti prima nota | 200-500/mese | Make → FiC API |

### Configurazione Raccomandata

#### 1. Numerazioni Separate

| Numerazione | Uso | Formato Esempio |
|---|---|---|
| FE | Fatture emesse standard | FE/1, FE/2, ... |
| NC | Note di credito | NC/1, NC/2, ... |
| AF | Autofatture TD17/18/19 | AF/1, AF/2, ... |
| OSS | Fatture regime OSS | OSS/1, OSS/2, ... |

#### 2. Conti IVA

| Conto | Uso |
|---|---|
| IVA c/vendite 22% | Vendite nazionali |
| IVA c/vendite OSS DE 19% | Vendite B2C Germania |
| IVA c/vendite OSS FR 20% | Vendite B2C Francia |
| IVA c/vendite OSS ES 21% | Vendite B2C Spagna |
| IVA c/acquisti reverse charge | Autofatture commissioni |

#### 3. Centri di Costo/Ricavo per Marketplace

| Centro | Marketplace |
|---|---|
| MKP-AMZ | Amazon (tutti i mercati) |
| MKP-EBY | eBay |
| MKP-KAU | Kaufland |
| MKP-CAR | Carrefour |
| MKP-LRM | Leroy Merlin |
| MKP-BOL | Bol.com |
| MKP-FNC | Fnac-Darty |
| MKP-OTH | Altri marketplace |

#### 4. Categorie Documenti Ricevuti

| Categoria | Tipo |
|---|---|
| Commissioni Marketplace | Fatture commissioni da Amazon, eBay, etc. |
| Logistica FBA | Costi fulfillment Amazon |
| Advertising | Costi advertising marketplace |
| Acquisto Merce | Fatture fornitori prodotti |
| Software/Servizi | SellRapido, Make, Supabase, etc. |
| Spedizioni | Corrieri, packaging |

---

## Metriche e KPI

| KPI | Target | Come Misurare |
|---|---|---|
| Fatture emesse entro 24h dall'ordine | >98% | Supabase: ordine.created_at vs fattura.date |
| Autofatture emesse entro scadenza | 100% | FiC: autofatture per data vs deadline |
| Tasso scarto SDI | <2% | FiC: e_invoice_status rejected / total |
| Riconciliazione settlement | >95% automatica | Supabase: fic_reconciliation.status |
| Tempo medio riconciliazione | <48h | Supabase: settlement_date vs reconciled_date |
| Fatture fornitori registrate entro 24h | >90% | Webhook receive → FiC registration |
| Dichiarazione OSS puntuale | 100% | Calendar: scadenze trimestrali |

---

## Fonti e Riferimenti

### Documentazione Ufficiale

- [Fatture in Cloud Developer Portal](https://developers.fattureincloud.it/)
- [API Reference](https://developers.fattureincloud.it/api-reference/)
- [Webhooks Documentation](https://developers.fattureincloud.it/docs/webhooks/)
- [OAuth 2.0 Authentication](https://developers.fattureincloud.it/docs/basics/authentication/)
- [Scopes Reference](https://developers.fattureincloud.it/docs/basics/scopes/)
- [E-Invoice Management Guide](https://developers.fattureincloud.it/docs/guides/e-invoice-management/)
- [Invoice Creation Guide](https://developers.fattureincloud.it/docs/guides/invoice-creation/)
- [Pending Received Documents](https://developers.fattureincloud.it/docs/guides/pending-documents/)
- [OpenAPI Specification](https://github.com/fattureincloud/openapi-fattureincloud)
- [Help Center](https://help.fattureincloud.it/)
- [Autofattura TD17/TD18/TD19](https://help.fattureincloud.it/help/articolo/647-crea-autofattura-elettronica-td17-td18-td19)
- [Operazioni OSS/IOSS](https://help.fattureincloud.it/help/articolo/653-operazioni-oss-ioss)

### SDK Ufficiali

- [Python SDK](https://github.com/fattureincloud/fattureincloud-python-sdk)
- [PHP SDK](https://github.com/fattureincloud/fattureincloud-php-sdk)
- [JavaScript SDK](https://github.com/fattureincloud/fattureincloud-js-sdk)
- [TypeScript SDK](https://github.com/fattureincloud/fattureincloud-ts-sdk)
- [Java SDK](https://github.com/fattureincloud/fattureincloud-java-sdk)
- [C# SDK](https://github.com/fattureincloud/fattureincloud-csharp-sdk)
- [Go SDK](https://github.com/fattureincloud/fattureincloud-go-sdk)
- [Ruby SDK](https://github.com/fattureincloud/fattureincloud-ruby-sdk)

### Make.com Integration

- [Fatture in Cloud su Make.com](https://www.make.com/en/integrations/fatture-in-cloud)
- [App Documentation](https://apps.make.com/fatture-in-cloud)

### Normativa e Fiscalità

- [Agenzia delle Entrate — FAQ OSS](https://www.agenziaentrate.gov.it/portale/risposte-alle-domande-piu-frequenti-oss)
- [Guida Autofattura Amazon](https://www.autofattura.io/blog/autofattura-amazon)
- [TD17/TD18/TD19 Guida Definitiva](https://www.autofattura.io/blog/autofattura-td17-td18-td19)
- [Fatturazione Marketplace](https://aliasdigital.it/blog/fatturare-su-amazon-ebay-altri-marketplace-come-fare/)
- [IVA Amazon Italia ed Europa](https://consol.srl/iva-amazon/)
- [Fatturazione E-commerce](https://consol.srl/fatturazione-elettronica-ecommerce/)
- [SellRapido Guida Fatturazione Amazon](https://www.sellrapido.com/it/blog/271-guida-alla-fatturazione-per-chi-vende-su-amazon/)
- [Riconciliazione Bancaria FiC](https://www.fattureincloud.it/software-fatturazione/riconciliazione/)
- [Registrazione Vendite Amazon in Prima Nota](https://marcovergani.com/vendite-su-amazon-come-registrare-le-operazioni-in-prima-nota/)
