# ChannelEngine — Documentazione Tecnica Completa

## Informazioni Generali

| Parametro | Valore |
|-----------|--------|
| **Nome Ufficiale** | ChannelEngine B.V. |
| **Tipo di Piattaforma** | SaaS Multi-tenant, Middleware Multichannel |
| **Sito Ufficiale** | https://www.channelengine.com |
| **Hub Sviluppatori** | https://www.channelengine.com/developer-hub |
| **Documentazione Ufficiale** | https://support.channelengine.com/hc/en-us |
| **Repository GitHub** | https://github.com/channelengine/api-docs |
| **API Base URL (Esempio)** | `https://{tenant}.channelengine.net/api` |
| **Fondazione** | 2013 |
| **Sede Principale** | Leiden, Paesi Bassi (Vondellaan 47) |
| **Uffici Globali** | Leiden, Monaco, Dubai, Singapore, Melbourne, New York |
| **Dipendenti** | ~190 |
| **Marketplace Supportati** | 1,300+ canali |
| **Certificazioni** | SOC 2 Type II (conforme a standard enterprise) |
| **Modello Aziendale** | SaaS con pricing basato su GMV |

---

## 1. Architettura della Piattaforma

### 1.1 Componenti Core

ChannelEngine è un sistema integrato multi-tenant costruito su architettura REST/API-first. I componenti principali includono:

**Merchant Portal:** Interfaccia web centrale per la gestione di:
- Prodotti e cataloghi
- Ordini e spedizioni
- Inventory/stock
- Pricing e repricing rules
- Channel management
- Reporting e analytics

**Merchant API (v2):** API REST per integrazione merchant-side:
- Sincronizzazione ordini (import/export)
- Product feed management
- Gestione stock real-time
- Returns e cancellazioni
- Shipment tracking
- Pricing dinamico

**Channel API:** API REST per l'integrazione di nuovi canali (marketplace, piattaforme ecommerce):
- Product data synchronization
- Order submission
- Shipment updates
- Cancellations e returns handling

**Webhooks:** Sistema di notifiche real-time per:
- Nuovi ordini
- Cambio stato ordini
- Aggiornamenti spedizioni
- Cambio inventario

**Multi-Tenant SaaS Infrastructure:**
- Isolamento dati per tenant
- Autoscaling automatico
- Data residency per regione
- 99.9% uptime SLA

### 1.2 Data Flow Architecture

```
[Marketplace/Canale] → [Channel API] → [ChannelEngine Core] → [Merchant API] → [Merchant ERP/WMS]
     ↓                                          ↓
 Sincronizza: Prodotti,                  Processa:
 Ordini, Prezzi                          - Inventory sync
                                         - Order orchestration
                                         - Pricing rules
                                         ↓
                                    [Webhooks] → Event notifications real-time
```

Il flusso è **bidrezionale e asincrono**:
1. I merchant caricano prodotti via API Merchant
2. ChannelEngine distribuisce ai canali via Channel API
3. I canali inviano ordini via Channel API
4. ChannelEngine notifica via Webhook e rende disponibile via API Merchant
5. Il merchant processa l'ordine e aggiorna lo stato via API Merchant
6. ChannelEngine aggiorna i canali via Channel API

---

## 2. API — Merchant API

### 2.1 Autenticazione e Base URL

**Endpoint Base:** `https://{tenant}.channelengine.net/api`

Dove `{tenant}` è il sottodominio del proprio account ChannelEngine (es: `demo.channelengine.net/api`).

**Autenticazione:** Header `X-CE-Auth-Token` o `Authorization: Bearer {api-key}`

```bash
# Esempio con cURL
curl -X GET "https://demo.channelengine.net/api/v2/orders" \
  -H "X-CE-Auth-Token: your-api-key-here"
```

**Generazione API Key:**
1. Login al Merchant Portal
2. Settings → Merchant API Keys → Add
3. Inserire nome descrittivo
4. Copiare il token generato

### 2.2 Rate Limits

ChannelEngine implementa rate limiting per endpoint per evitare abusi:

| Endpoint | Limite | Unità | Note |
|----------|--------|-------|------|
| POST /v2/supportorder | 3 | per minuto | Critical path |
| GET /v2/orders | 60 | per minuto | Standard read |
| POST /v2/orders | 30 | per minuto | Order creation |
| GET /v2/products/data | 40 | per minuto | Product feed |
| POST /v2/products/data | 20 | per minuto | Product update |
| GET /v2/products/offers | 60 | per minuto | Price/stock feed |
| POST /v2/products/offers | 30 | per minuto | Price/stock update |
| PUT /v2/shipments | 40 | per minuto | Shipment updates |
| General bucket | 1,000 | per ora | Per tenant |

**Response Headers Rate Limit:**
```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 55
X-RateLimit-Reset: 1617189660
```

**Gestione Errore 429:** Quando si raggiunge il limite, l'API ritorna `HTTP 429 Too Many Requests`. Implementare backoff esponenziale:

```javascript
async function apiCallWithRetry(url, options, maxRetries = 3) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    const response = await fetch(url, options);

    if (response.status === 429) {
      const retryAfter = response.headers.get('Retry-After') || Math.pow(2, attempt);
      console.log(`Rate limited. Retry after ${retryAfter}s`);
      await new Promise(r => setTimeout(r, retryAfter * 1000));
      continue;
    }

    return response;
  }
}
```

### 2.3 Endpoints Principali

#### 2.3.1 Orders (Ordini)

**GET /v2/orders** - Lista ordini con filtri
```bash
curl -X GET "https://demo.channelengine.net/api/v2/orders?statuses=NEW,IN_PROGRESS&pageSize=100&pageNumber=1" \
  -H "X-CE-Auth-Token: api-key"
```

**Parametri Query:**
- `statuses[]`: NEW, IN_PROGRESS, SHIPPED, DELIVERED, RETURNED (filtro multiplo)
- `pageSize`: 1-1000 (default 100)
- `pageNumber`: 1-based indexing
- `createdAfter`: ISO 8601 timestamp
- `createdBefore`: ISO 8601 timestamp
- `orderNos[]`: Filtro per numero ordine

**Response Body (Esempio):**
```json
{
  "content": [
    {
      "id": 123456,
      "channelOrderNo": "123-ABC-456",
      "merchantOrderNo": "ORD-2024-001",
      "orderDate": "2024-03-15T10:30:00Z",
      "channelId": 1,
      "channelName": "Amazon",
      "status": "NEW",
      "currencyCode": "EUR",
      "totalAmount": 149.99,
      "totalOrderValue": 149.99,
      "totalCost": 0,
      "lines": [
        {
          "id": 789,
          "sku": "PROD-SKU-001",
          "title": "Prodotto Esempio",
          "quantity": 2,
          "unitPrice": 74.99,
          "commissionAmount": 10.00,
          "description": "Descrizione variante"
        }
      ],
      "shipmentDetails": {
        "firstName": "Mario",
        "lastName": "Rossi",
        "email": "mario@example.com",
        "phone": "+39123456789",
        "addressLine1": "Via Roma 10",
        "addressLine2": null,
        "city": "Milano",
        "state": "MI",
        "postalCode": "20100",
        "countryCode": "IT"
      }
    }
  ],
  "pageNumber": 1,
  "pageSize": 100,
  "totalCount": 5432
}
```

**PATCH /v2/orders/{id}/acknowledge** - Conferma ricezione ordine
```bash
curl -X PATCH "https://demo.channelengine.net/api/v2/orders/123456/acknowledge" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "merchantOrderNo": "ORD-2024-001",
    "lines": [
      {
        "id": 789,
        "merchantLineNo": "LINE-001"
      }
    ]
  }'
```

**PATCH /v2/orders/{id}/state** - Cambio stato ordine
```bash
curl -X PATCH "https://demo.channelengine.net/api/v2/orders/123456/state" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "status": "IN_PROGRESS"
  }'
```

#### 2.3.2 Products (Prodotti)

**GET /v2/products/data** - Lista completa prodotti
```bash
curl -X GET "https://demo.channelengine.net/api/v2/products/data?pageSize=100&pageNumber=1" \
  -H "X-CE-Auth-Token: api-key"
```

**Response Body (Esempio):**
```json
{
  "content": [
    {
      "id": 999,
      "sku": "PROD-SKU-001",
      "title": "Prodotto Esempio Premium",
      "description": "Descrizione completa del prodotto",
      "brandCode": "BRAND001",
      "categoryTitles": ["Elettronica", "Smartphone", "Flagship"],
      "images": [
        "https://cdn.example.com/prod1-main.jpg",
        "https://cdn.example.com/prod1-side.jpg"
      ],
      "attributes": {
        "Color": "Nero",
        "Storage": "256GB",
        "RAM": "8GB"
      },
      "ean": "4006381333931",
      "gtin": "4006381333931",
      "offerData": [
        {
          "channelId": 1,
          "channelName": "Amazon",
          "price": 899.99,
          "originalPrice": 1099.99,
          "stock": 150,
          "condition": "NEW"
        }
      ]
    }
  ],
  "pageNumber": 1,
  "pageSize": 100,
  "totalCount": 45678
}
```

**GET /v2/products/offers** - Solo prezzo e stock (update leggeri)
```bash
curl -X GET "https://demo.channelengine.net/api/v2/products/offers?pageSize=500&pageNumber=1" \
  -H "X-CE-Auth-Token: api-key"
```

**POST /v2/products/data** - Carica/aggiorna prodotti
```bash
curl -X POST "https://demo.channelengine.net/api/v2/products/data" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Content-Type: application/json" \
  -d '[
    {
      "sku": "PROD-SKU-001",
      "title": "Prodotto Esempio",
      "description": "Descrizione completa",
      "brandCode": "BRAND001",
      "ean": "4006381333931",
      "images": [
        "https://cdn.example.com/image1.jpg"
      ],
      "categoryTitles": ["Elettronica", "Smartphone"]
    }
  ]'
```

**POST /v2/products/offers** - Aggiorna solo prezzo e stock
```bash
curl -X POST "https://demo.channelengine.net/api/v2/products/offers" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Content-Type: application/json" \
  -d '[
    {
      "sku": "PROD-SKU-001",
      "offerData": [
        {
          "channelId": 1,
          "price": 899.99,
          "stock": 150
        }
      ]
    }
  ]'
```

#### 2.3.3 Shipments (Spedizioni)

**GET /v2/shipments** - Lista spedizioni
```bash
curl -X GET "https://demo.channelengine.net/api/v2/shipments?pageSize=100&pageNumber=1" \
  -H "X-CE-Auth-Token: api-key"
```

**POST /v2/shipments** - Crea spedizione (finale o bozza)
```bash
curl -X POST "https://demo.channelengine.net/api/v2/shipments" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "merchantShipmentNo": "SHIP-2024-001",
    "orderLines": [
      {
        "id": 789,
        "quantity": 2
      }
    ],
    "trackingNumber": "1Z999AA10123456784",
    "trackingUrl": "https://track.fedex.com/?tracknumbers=1Z999AA10123456784",
    "carrierCode": "FEDEX",
    "shipDate": "2024-03-16T14:00:00Z",
    "expectedDeliveryDate": "2024-03-20T00:00:00Z"
  }'
```

**PUT /v2/shipments/{merchantShipmentNo}** - Aggiorna spedizione
```bash
curl -X PUT "https://demo.channelengine.net/api/v2/shipments/SHIP-2024-001" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "trackingNumber": "1Z999AA10123456784",
    "carrierCode": "FEDEX",
    "expectedDeliveryDate": "2024-03-20T00:00:00Z"
  }'
```

#### 2.3.4 Returns (Resi)

**GET /v2/returns** - Lista resi
```bash
curl -X GET "https://demo.channelengine.net/api/v2/returns?statuses=RETURN_RECEIVED&pageSize=100" \
  -H "X-CE-Auth-Token: api-key"
```

**PUT /v2/returns/{id}** - Accetta/rifiuta reso
```bash
curl -X PUT "https://demo.channelengine.net/api/v2/returns/54321" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "merchantReturnNo": "RET-2024-001",
    "status": "RETURN_ACCEPTED",
    "refundAmount": 149.99
  }'
```

#### 2.3.5 Cancellations (Cancellazioni)

**GET /v2/cancellations** - Lista cancellazioni richieste
```bash
curl -X GET "https://demo.channelengine.net/api/v2/cancellations?statuses=REQUESTED&pageSize=100" \
  -H "X-CE-Auth-Token: api-key"
```

**PUT /v2/cancellations/{id}** - Accetta/rifiuta cancellazione
```bash
curl -X PUT "https://demo.channelengine.net/api/v2/cancellations/99999" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "status": "CANCELLATION_ACCEPTED"
  }'
```

#### 2.3.6 Stock Management

**GET /v2/inventory** - Visualizza livelli stock
```bash
curl -X GET "https://demo.channelengine.net/api/v2/inventory?pageSize=100" \
  -H "X-CE-Auth-Token: api-key"
```

**POST /v2/inventory** - Aggiorna stock per warehouse
```bash
curl -X POST "https://demo.channelengine.net/api/v2/inventory" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Content-Type: application/json" \
  -d '[
    {
      "sku": "PROD-SKU-001",
      "warehouse": "WAREHOUSE-MAIN",
      "stock": 500,
      "reserved": 50
    }
  ]'
```

### 2.4 Pagination

Tutti gli endpoint che ritornano liste usano pagination:

```json
{
  "content": [...],
  "pageNumber": 1,
  "pageSize": 100,
  "totalCount": 5432
}
```

**Parametri:**
- `pageSize`: 1-1000 (default 100)
- `pageNumber`: 1-based

**Best Practice:** Per sync bulk, processare max 100-500 items per request e implementare batching parallelo.

### 2.5 Error Codes and Handling

| Codice HTTP | Descrizione | Azione Consigliata |
|------------|-------------|-------------------|
| 200 | OK | Elabora response |
| 201 | Created | Risorsa creata con successo |
| 204 | No Content | Operazione riuscita (nessun body) |
| 400 | Bad Request | Valida parametri e payload |
| 401 | Unauthorized | Verifica API key |
| 403 | Forbidden | Controlla permessi tenant |
| 404 | Not Found | Verifica ID/SKU esiste |
| 409 | Conflict | Stato inconsistente (retry con backoff) |
| 429 | Too Many Requests | Implementa rate limiting client-side |
| 500 | Internal Server Error | Retry con backoff esponenziale |
| 503 | Service Unavailable | Retry dopo delay |

**Response Error (Esempio):**
```json
{
  "statusCode": 400,
  "errorCode": "INVALID_PRODUCT_DATA",
  "message": "Product SKU is required",
  "timestamp": "2024-03-15T10:30:00Z",
  "path": "/api/v2/products/data",
  "details": [
    {
      "field": "sku",
      "message": "SKU cannot be null"
    }
  ]
}
```

---

## 3. API — Channel API

La Channel API è complementare alla Merchant API. Mentre la Merchant API è usata dai merchant per inviare prodotti e gestire ordini, la Channel API è usata dai **canali** (marketplace, piattaforme ecommerce) per:

### 3.1 Ruolo della Channel API

- Fornire prodotti dal merchant al canale
- Sincronizzare ordini dal canale al merchant
- Gestire spedizioni in tempo reale
- Tracciare cancellazioni e resi

### 3.2 Endpoint Principali

**GET /v2/products/data** - Riceve catalogo prodotti completo
```
Usato dal canale per scaricare il catalogo del merchant
Response: Same structure come Merchant API
```

**GET /v2/products/offers** - Riceve aggiornamenti prezzo/stock
```
Usato dal canale per sync price/inventory frequent
```

**POST /v2/products/orders** - Invia ordini al merchant
```json
{
  "channelOrderNo": "CH-123456",
  "orderDate": "2024-03-15T10:30:00Z",
  "lines": [
    {
      "sku": "PROD-SKU-001",
      "quantity": 2,
      "price": 74.99
    }
  ],
  "shipmentDetails": {...}
}
```

**PATCH /v2/shipments/{merchantShipmentNo}** - Notifica spedizioni al merchant

### 3.3 Authentication

La Channel API usa **API key per channel**, reperibile in: Channel Settings → Setup → API Key

```bash
curl -X GET "https://channelengine.net/api/v2/products/data" \
  -H "X-CE-Channel-Id: {channel-api-key}"
```

---

## 4. Webhooks

### 4.1 Overview

I webhook abilitano la comunicazione **push real-time** da ChannelEngine ai sistemi merchant. Invece di faire polling frequente, ChannelEngine notifica istantaneamente quando accadono eventi importanti.

**Importante:** I webhook non devono essere l'unica fonte di dati. L'applicazione deve funzionare anche senza webhooks in caso di network issues.

### 4.2 Eventi Disponibili

| Evento | Trigger | Payload |
|--------|---------|----------|
| `orders.created` | Nuovo ordine importato | Order ID, Channel, Dettagli ordine |
| `orders.updated` | Cambio stato ordine | Order ID, Nuovo stato |
| `orders.acknowledged` | Ordine confermato | Order ID, Merchant Order No |
| `shipments.created` | Spedizione creata | Shipment details, Tracking |
| `shipments.updated` | Spedizione aggiornata | Shipment ID, Tracking changes |
| `cancellations.created` | Cancellazione richiesta | Cancellation ID, Order ID |
| `cancellations.updated` | Cambio stato cancellazione | Status update |
| `returns.created` | Reso ricevuto | Return ID, Order ID |
| `returns.updated` | Cambio stato reso | Status update |
| `inventory.changed` | Stock sync richiesto | SKU, Stock changes |

### 4.3 Configurazione Webhook

**Dashboard:** Settings → Webhooks → Add Webhook
- URL di destinazione: `https://merchant.example.com/webhooks/channelengine`
- Selezionare eventi da sottoscrivere
- ChannelEngine aggiunge automaticamente parametri: `?type={event}&tenant={id}&updatedSince={timestamp}`

### 4.4 Payload Webhook (Esempio)

```json
{
  "type": "orders.created",
  "tenant": "demo",
  "timestamp": "2024-03-15T10:30:00Z",
  "data": {
    "orderId": 123456,
    "channelOrderNo": "123-ABC-456",
    "status": "NEW",
    "channelId": 1,
    "totalAmount": 149.99
  }
}
```

### 4.5 Retry Policy

ChannelEngine ritenta il webhook con backoff esponenziale:
- 1 tentativo immediato
- Retry dopo 1 minuto
- Retry dopo 5 minuti
- Retry dopo 30 minuti
- Retry dopo 2 ore
- Max 24 ore di retry

**HTTP Status Codes validi:** 200-299. Qualsiasi altro status causa retry.

### 4.6 Webhook Security

**Header di firma:** `X-CE-Webhook-Signature`

```javascript
// Validazione firma webhook (HMAC-SHA256)
const crypto = require('crypto');

function verifyWebhookSignature(payload, signature, secret) {
  const computed = crypto
    .createHmac('sha256', secret)
    .update(JSON.stringify(payload))
    .digest('hex');

  return computed === signature;
}
```

---

## 5. Order Lifecycle

### 5.1 State Machine Completo

```
NEW → IN_PROGRESS → SHIPPED → DELIVERED → RETURNED
 ↓         ↓            ↓
CANCELLED  (any) → CANCELLED
```

### 5.2 Descrizione Stati

| Stato | Descrizione | Transizioni Possibili | Note |
|-------|-------------|----------------------|------|
| **NEW** | Ordine appena creato, non confermato | IN_PROGRESS, CANCELLED | Non può avere shipment |
| **IN_PROGRESS** | Ordine confermato, in elaborazione | SHIPPED, CANCELLED | Confermato con acknowledge |
| **SHIPPED** | Spedizione creata con tracking | DELIVERED, RETURNED | Shipment obbligatorio |
| **DELIVERED** | Consegnato al cliente | RETURNED, CLOSED | Fine ciclo normale |
| **RETURNED** | Reso ricevuto dal merchant | CLOSED | Reso accettato |
| **CANCELLED** | Cancellato dal merchant/canale | CLOSED | Terminal state |

### 5.3 Workflow Fulfillment Completo

```
1. [NEW] Ordine arriva da canale
   ↓
   POST /v2/orders/{id}/acknowledge
   { "merchantOrderNo": "ORD-001", "lines": [...] }
   ↓
2. [IN_PROGRESS] Sistema merchant processa
   ↓
   POST /v2/shipments
   { "merchantShipmentNo": "SHIP-001", "trackingNumber": "1Z..." }
   ↓
3. [SHIPPED] Monitoraggio spedizione
   ↓
4. [DELIVERED] Cliente riceve (automatico da carrier)
   ↓
   [RETURNED] Se cliente chiede reso
   PUT /v2/returns/{id}
   { "status": "RETURN_ACCEPTED", "refundAmount": 149.99 }
```

### 5.4 Cancellation Flow

**Prima di shipment:**
```
[NEW] → Cancellazione richiesta → PUT /v2/cancellations/{id}
        { "status": "CANCELLATION_ACCEPTED" } → [CANCELLED]
```

**Dopo shipment:**
```
[SHIPPED] → Client chiede return → [RETURNED]
```

---

## 6. Product Data Model

### 6.1 Struttura Completa Prodotto

```json
{
  "id": 999,
  "sku": "PROD-SKU-001",
  "title": "Prodotto Esempio Premium",
  "description": "Descrizione lunga e dettagliata",
  "brandCode": "BRAND001",
  "manufacturerCode": "MFR001",
  "ean": "4006381333931",
  "gtin": "4006381333931",
  "upc": "123456789012",
  "categoryTitles": [
    "Elettronica",
    "Smartphone",
    "Flagship"
  ],
  "images": [
    "https://cdn.example.com/main.jpg",
    "https://cdn.example.com/side.jpg"
  ],
  "attributes": {
    "Color": "Nero",
    "Storage": "256GB",
    "RAM": "8GB",
    "Warranty": "24 mesi",
    "CustomField1": "Valore custom"
  },
  "offerData": [
    {
      "channelId": 1,
      "channelName": "Amazon",
      "price": 899.99,
      "originalPrice": 1099.99,
      "cost": 450.00,
      "stock": 150,
      "reserved": 10,
      "condition": "NEW"
    }
  ],
  "extraData": {
    "shippingWeight": "250g",
    "shippingDimensions": "15x10x5cm",
    "isFragile": false,
    "leadTime": "2-3 days"
  }
}
```

### 6.2 Campi Obbligatori vs Opzionali

**Campi Obbligatori (globali per tutti i canali):**
- `sku`: Identificativo univoco prodotto
- `title`: Titolo prodotto (min 5, max 200 caratteri)
- `description`: Descrizione (min 20 caratteri)
- `images`: Almeno 1 immagine URL
- `categoryTitles`: Almeno 1 categoria

**Campi Globali Consigliati:**
- `ean`: European Article Number (13 cifre)
- `gtin`: Global Trade Item Number
- `brandCode`: Brand/Marca
- `price` (in offerData): Prezzo per canale

**Campi Opzionali:**
- `upc`: Codice Universal Product Code
- `manufacturerCode`: Codice produttore
- `customAttributes`: Attributi definiti dal merchant
- `extraData`: Dati shipping, lead time, ecc.

### 6.3 Custom Attributes

Ogni merchant può definire attributi custom per esigenze specifiche:

```json
{
  "sku": "PROD-SKU-001",
  "attributes": {
    "Material": "Acciaio inox",
    "IPRating": "IP67",
    "BatteryLife": "48 ore",
    "Weight": "185g",
    "Warranty": "24 mesi",
    "InternalCode": "INTERNAL-123"
  }
}
```

Questi attributi sono **mappabili** ai requisiti specifici di ogni canale.

### 6.4 Category Mapping

ChannelEngine supporta **3 modalità di mapping categorie:**

1. **Manual Mapping:** Collegare manualmente categorie merchant a categorie canale
2. **Rule-based:** Creare regole IF/THEN basate su attributi prodotto
3. **AI-powered:** ChannelEngine usa ML per match automatico categorie

**Best Practice:** Essere specifici: "Electronics > Smartphones > Flagship" è migliore di "Electronics > Phones"

### 6.5 Content Grading

ChannelEngine valuta la qualità dati del prodotto:

| Score | Requisiti | Impatto |
|-------|-----------|----------|
| A | Titolo, descrizione completa, 3+ immagini, EAN, prezzo | Best visibility |
| B | Titolo, descrizione, 1-2 immagini, prezzo | Good visibility |
| C | Titolo minimo, descrizione, prezzo | Limited visibility |
| D | Dati insufficienti | Poor visibility |

---

## 7. Marketplace Connectors

### 7.1 Panorama Marketplace Supportati

ChannelEngine supporta integrazione con **1,300+ canali** (marketplace, piattaforme ecommerce, social commerce).

**Top Marketplace per Volume:**

| Marketplace | Regione | Tipo Integrazione | FBA/FBM | Returns |
|-----------|---------|------------------|---------|----------|
| **Amazon** | Global | Native (Seller/Vendor) | Entrambi | Auto |
| **eBay** | Global | Native | Partial | Manual |
| **bol.com** | BeNeLux | Native | No | Auto |
| **Kaufland** | EU (DE, AT, RO, CZ) | Native | No | Auto |
| **Carrefour** | EU (FR, IT, ES) | Native | No | Manual |
| **Fnac-Darty** | EU (FR, ES) | Native | No | Auto |
| **MediaMarkt** | EU | Native | No | Manual |
| **Coolblue** | BeNeLux | Native | No | Auto |
| **Zalando** | EU | API | No | Auto |
| **ASOS** | Global | API | No | Auto |
| **Wish** | Global | API | No | Manual |
| **Google Shopping** | Global | Feed | N/A | N/A |
| **Shopify** | SaaS | App/API | Dipende store | Dipende store |
| **WooCommerce** | Self-hosted | Plugin/API | No | Manual |
| **Adobe Commerce** | Self-hosted | Plugin/API | No | Manual |

### 7.2 Connector Types

**Native Integration:** ChannelEngine ha API diretto con marketplace
- Sync real-time
- Full feature support
- Webhook support

**Feed-based Integration:** Tramite file XML/CSV/JSON
- Sincronizzazione batch giornaliera
- Features limitate
- Usato per Google Shopping, Criteo, ecc.

**Custom API:** Merchant integra via REST API con Merchant API
- Usato per ecommerce proprietari
- Full control
- Richiede sviluppo custom

### 7.3 Marketplace-Specific Features

**Amazon:**
- Dual fulfillment (FBA + FBM + MCF)
- Repricing automatico
- Content quality A+
- Brand registry

**eBay:**
- Auction + Fixed price
- Pre-built & Custom listings
- Inventory sync
- Limited repricing

**bol.com (Olanda/Belgio):**
- Full inventory sync
- Automatic fulfillment routing
- Best seller metrics
- Real-time returns

---

## 8. Stock & Fulfillment

### 8.1 Stock Management Model

ChannelEngine usa un modello di **stock aggregato multi-warehouse:**

```
Total Physical Stock = WH1_Available + WH2_Available + WH3_Available
Reserved Stock = Ordini confermati non ancora shipped
Net Stock = Total - Reserved
Channelable Stock = Net - Buffer (rules-based)
```

### 8.2 Multi-Warehouse Configuration

```json
{
  "warehouse": "WAREHOUSE-MAIN",
  "location": "Milano",
  "priority": 1,
  "sku": "PROD-SKU-001",
  "physical": 500,
  "reserved": 50,
  "available": 450,
  "buffer": 20,
  "leadTime": "1 day"
}
```

**Lead Time:** Tempo necessario per preparare shipment da warehouse

### 8.3 Fulfillment Methods

**FBM (Fulfillment By Merchant):**
- Merchant prepara e spedisce
- Stock su warehouse merchant
- Full control su shipment

**FBA (Fulfillment By Amazon):**
- Amazon riceve inventory
- Amazon prepara e spedisce
- Real-time stock sync con FBA
- Higher fees, faster delivery

**Hybrid Fulfillment:**
ChannelEngine cambia automaticamente tra FBA e FBM basato su disponibilità stock:

```
IF FBA_stock > 0 THEN
  Use FBA (faster delivery, higher visibility)
ELSE IF FBM_stock > 0 THEN
  Use FBM (fallback)
ELSE
  Backorder or cancel
```

Switchover time: ~15 minuti

### 8.4 MCF (Amazon Multi-Channel Fulfillment)

Esporta ordini non-Amazon ad Amazon per fulfillment:

```
[bol.com] → [Order] → [ChannelEngine] → [Amazon MCF]
                                    ↓
                            [Amazon fulfills]
                                    ↓
                            [Shipment tracking]
```

### 8.5 Overselling Prevention

**Meccanismi anti-oversell:**

1. **Real-time inventory lock:** Ordine reservation immediata
2. **Buffer stock:** X% di stock riservato per safety
3. **Sync frequency:** Check inventory ogni 5-15 minuti
4. **Backorder handling:** Auto-cancel se stock non disponibile entro timeframe

---

## 9. Pricing & Repricing

### 9.1 Dynamic Pricing Engine

ChannelEngine supporta pricing rules complesso con **repricing automatico:**

```
Base Price → Apply Rules → Per-Channel Pricing → Repricing → Final Price
                ↓
            - Commission
            - VAT/Tax
            - Markup
            - Competition rules
            - Margin protection
```

### 9.2 Price Rules Configuration

```json
{
  "sku": "PROD-SKU-001",
  "basePrice": 100.00,
  "priceRules": [
    {
      "channel": "Amazon",
      "basePrice": 100.00,
      "commission": -15.00,
      "markup": 10.00,
      "vat": 19,
      "finalPrice": 113.90
    },
    {
      "channel": "bol.com",
      "basePrice": 100.00,
      "commission": -12.00,
      "markup": 5.00,
      "vat": 21,
      "finalPrice": 114.33
    }
  ]
}
```

### 9.3 Repricing Rules

```json
{
  "sku": "PROD-SKU-001",
  "repricingRule": {
    "enabled": true,
    "rule": "UNDERCUT_COMPETITORS",
    "discount": 2.00,
    "minPrice": 80.00,
    "maxPrice": 150.00,
    "recalcInterval": 15
  }
}
```

**Repricing Strategies:**
- `MATCH_LOWEST`: Eguaglia prezzo più basso competitor
- `UNDERCUT_COMPETITORS`: Sconta sotto competitors
- `ABOVE_MINIMUM`: Sempre >= minPrice
- `MARGIN_BASED`: Basato su margine desiderato

### 9.4 VAT & Tax Management

ChannelEngine calcola automaticamente VAT:

```
Base Price: 100.00 EUR
Commission: -15.00 EUR
Subtotal: 85.00 EUR
VAT (19%): 16.15 EUR
Final Price: 101.15 EUR
```

**Configurazione VAT:**
- Per canale/paese
- Automatica per EU
- Supporta reverse charge

### 9.5 Commission & Margin Calculation

```
Gross Revenue = Final Price (inc VAT)
Commission (platform) = -15%
Your Revenue = Gross - Commission
Cost = Product Cost + Shipping Cost
Net Margin = (Your Revenue - Cost) / Your Revenue
```

ChannelEngine mostra profittabilità per SKU/canale per ottimizzare pricing.

---

## 10. Reporting & Analytics

### 10.1 Reports Disponibili

**Insights Module (add-on):**

| Report | Metrica | Frequenza | Export |
|--------|---------|-----------|--------|
| Summary | Revenue, Orders, Products | Real-time | CSV, XLSX |
| Order Analytics | Vendite per canale, AOV | Daily | CSV, XLSX |
| Product Performance | Conversion, Stock turns | Daily | CSV, XLSX |
| Returns & Cancellations | Return rate, RMA ratio | Daily | CSV, XLSX |
| Inventory | Stock levels, turnover | Real-time | CSV, XLSX |
| Competition | Price positioning, Buy Box | Daily | N/A |
| Financial | Margin, Profitability | Daily | CSV, XLSX |

### 10.2 Key Performance Indicators

```json
{
  "period": "2024-03-01 to 2024-03-31",
  "metrics": {
    "totalRevenue": 45000.00,
    "totalOrders": 320,
    "averageOrderValue": 140.63,
    "conversionRate": 2.3,
    "returnRate": 3.2,
    "profitMargin": 18.5,
    "buyBoxWins": 78.4,
    "averageRating": 4.6
  },
  "byChannel": [
    {
      "channel": "Amazon",
      "revenue": 25000.00,
      "orders": 180,
      "margin": 20.1
    }
  ]
}
```

### 10.3 Export & API Analytics

**Export Formati:**
- XLSX: Con formattazione, tabelle pivot
- CSV: Raw data
- JSON: API calls programmatici

```bash
# Export via API
curl -X GET "https://demo.channelengine.net/api/v2/reports/summary?startDate=2024-03-01&endDate=2024-03-31" \
  -H "X-CE-Auth-Token: api-key" \
  -H "Accept: application/json"
```

---

## 11. Integrazioni

### 11.1 ERP Connectors

ChannelEngine ha integrazioni native con major ERP systems:

**SAP:**
- SAP Order Management Services (OMS)
- Real-time inventory sync
- Order acknowledgment bi-directional
- Link: https://www.channelengine.com/en/integrations/sap

**Microsoft Dynamics 365:**
- Business Central
- Finance & Operations
- Real-time data sync
- Custom field mapping

**NetSuite:**
- Full inventory sync
- Order import/export
- Automatic GL posting
- Link: https://www.channelengine.com/en/integrations/netsuite

**Oracle EBS:**
- Order import
- Inventory update
- Customer sync

### 11.2 WMS Connectors

ChannelEngine integra con sistemi warehouse:

- **Mecalux/Easy WMS:** Inventory sync, pick/pack optimization
- **3PL Partners:** Shipment visibility, carrier integration
- **Proprietary WMS:** Via REST API custom

### 11.3 PIM Systems

**Native Integrations:**
- Akeneo
- Salsify
- Syndigo
- Plytix

**Sync Trigger:** Cambio prodotto in PIM → Auto-sync a ChannelEngine → Distribuzione canali

### 11.4 Accounting Integrations

- **Xero:** Auto GL posting di vendite/costi
- **QuickBooks:** Sync vendite, expense tracking
- **Wave:** Free accounting sync

### 11.5 Middleware & iPaaS

ChannelEngine supporta connessioni via middleware automation:

**Make (formerly Integromat):**
```
[Shopify] → [Make] → [ChannelEngine] → [Amazon, eBay, bol.com]
```

**Zapier:**
- Trigger su ordini nuovi
- Create custom workflows
- Connect con 5000+ apps

**Elastic.io / AVA:**
- Enterprise integration platform
- Custom transformation logic

**Native REST API:**
- Qualsiasi piattaforma custom
- Full control su mapping
- Retry logic implementato nel client

---

## 12. Sicurezza e Compliance

### 12.1 Data Security

**Encryption:**
- **In Transit:** TLS 1.2+ per tutti gli endpoint
- **At Rest:** AES-256 per dati sensibili
- **API Keys:** Never logged, hashed in database

**Access Control:**
- Role-Based Access Control (RBAC)
- Ruoli: Admin, Manager, Seller, Reader
- IP whitelisting per API access

### 12.2 Compliance Standards

**SOC 2 Type II:** Certificato
- Security controls audited
- Availability: 99.9% uptime
- Confidentiality & integrity controls

**GDPR (EU):**
- Data processing agreements (DPA)
- Right to deletion compliant
- Data residency options (EU)

**PCI DSS:** Non applica (ChannelEngine non gestisce pagamenti)

**CCPA (California):** Compliant

### 12.3 Data Retention

| Dato | Retention | Note |
|------|-----------|------|
| Order data | 7 anni | Per compliance fiscal |
| Customer PII | 3 anni | Post-delivery |
| API logs | 90 giorni | Per audit |
| Deleted products | 30 giorni | Soft delete, poi hard delete |
| Backups | 30 giorni rolling | Disaster recovery |

### 12.4 Webhook Security

**Signature Verification:** Ogni webhook include header `X-CE-Webhook-Signature` (HMAC-SHA256)

```javascript
const secret = "your-webhook-secret";
const signature = req.headers['x-ce-webhook-signature'];
const computed = crypto
  .createHmac('sha256', secret)
  .update(JSON.stringify(req.body))
  .digest('hex');

if (computed !== signature) {
  return res.status(401).send('Invalid signature');
}
```

---

## 13. Pricing e Piani

### 13.1 Pricing Models

ChannelEngine usa **transaction-based pricing** basato su GMV (Gross Merchandise Value):

**Piano Starter:** €0 - €175K/mese GMV
- Commissione: 1.5% su GMV
- Max 3 marketplace
- API & plugin access
- Insights lite
- Cost: €49 - €262/mese

**Piano Growth:** €175K - €750K/mese GMV
- Commissione: 1.2% su GMV
- Unlimited marketplace
- Advanced Insights
- Priority support
- Cost: €262 - €900/mese

**Piano Scale:** >€750K/mese GMV
- Commissione: negotiated (0.8-1%)
- Dedicated account manager
- Custom integrations
- SLA guarantee
- Custom pricing

### 13.2 Add-ons

| Add-on | Cost | Descrizione |
|--------|------|-------------|
| Insights (advanced) | +€299/mese | Advanced reporting & KPI |
| Repricing Engine | +€199/mese | Dynamic repricing rules |
| Content Grading | +€99/mese | AI product content optimization |
| Compliance Pro | +€149/mese | Tax/VAT compliance monitoring |
| Priority Support | +€199/mese | 24/7 dedicated support |

### 13.3 No Setup Fees

- Onboarding gratuito
- Data migration included
- Training sessions included

---

## 14. Limitazioni Tecniche

### 14.1 Rate Limits per Endpoint

Vedi sezione 2.2 per dettagli completi. Riassunto:
- Rate limiting per endpoint specifico
- Global bucket 1,000 req/ora
- Retry con backoff esponenziale consigliato

### 14.2 Product Limits

| Limite | Valore | Note |
|--------|--------|------|
| Max SKU per merchant | Unlimited | ~150K in produzione |
| Max caratteri title | 200 | Per canale può variare |
| Max caratteri description | 5,000 | HTML supportato |
| Max immagini per prodotto | 10 | Min 1, recommended 3+ |
| Max custom attributes | 50 | Per prodotto |
| Max category depth | 5 | Levels di categoria |

### 14.3 Order Processing Limits

| Limite | Valore | Note |
|--------|--------|------|
| Max ordini per richiesta | 1,000 | Per GET /v2/orders |
| Max order lines per ordine | 500 | Per order |
| Order data retention | 7 anni | Per compliance |
| Max shipments per ordine | 5 | Partial shipments |
| Webhook retry window | 24 ore | Poi abbandona |

### 14.4 Known Constraints

**Performance:**
- Large product catalogs (>100K SKU) richiedono API polling batch (ogni 12-24 ore)
- Real-time sync è meglio per <50K SKU
- Peak load: 500 req/sec per tenant

**Marketplace Limitations:**
- Alcuni marketplace non supportano repricing real-time
- eBay ha restrizioni su auction sync
- Google Shopping è feed-only (niente ordini in real-time)

**Regional:**
- Some marketplace available solo in regioni specifiche
- VAT compliance richiede proper setup per EU

---

## 15. Confronto con Alternative

### 15.1 ChannelEngine vs Linnworks

| Aspetto | ChannelEngine | Linnworks |
|--------|---|---|
| **Marketplace Coverage** | 1,300+ | 70+ (limited) |
| **EU Focus** | Excellent | Good |
| **Pricing Model** | % GMV | Fixed monthly |
| **API Maturity** | v2 (REST) | Legacy |
| **Repricing** | Advanced | Basic |
| **Ease of Setup** | Quick (7 days) | Medium (14 days) |
| **Support** | Responsive | Standard |
| **Cost <€500K GMV/mo** | Better | Comparable |
| **Cost >€1M GMV/mo** | Better | Comparable |

### 15.2 ChannelEngine vs ChannelAdvisor

| Aspetto | ChannelEngine | ChannelAdvisor |
|--------|---|---|
| **Global Marketplace** | 1,300+ | 500+ |
| **US Market** | Limited | Strong |
| **Advertising Tools** | Basic | Advanced |
| **Analytics** | Good | Excellent |
| **Pricing** | GMV-based | Fixed + transaction |
| **Lead Time** | 1-2 settimane | 2-4 settimane |
| **Market Position** | EU-first | US-first |

### 15.3 ChannelEngine vs Lengow

| Aspetto | ChannelEngine | Lengow |
|--------|---|---|
| **Core Focus** | Marketplace aggregation | Feed management |
| **Marketplace Sync** | Full (orders) | Limited (feeds) |
| **Product Feed** | Native | Via Lengow cloud |
| **Order Management** | Yes | Limited |
| **Repricing** | Advanced | Basic |
| **Integration Depth** | Deep | Shallow |
| **Target Segment** | Enterprise marketplace sellers | E-commerce generalists |

### 15.4 ChannelEngine vs Channable

Nota: Channable è spesso nello stack insieme a ChannelEngine per complementare:

| Aspetto | Ruolo | Quando usare |
|--------|-------|---|
| **ChannelEngine** | Marketplace aggregation & orders | Primary marketplace strategy |
| **Channable** | Feed optimization & shopping channels | Google Shopping, Affiliate, Marketplace feeds secondary |
| **Combo** | Best of both worlds | When need both precise order mgmt + advanced feed optimization |

**Typical Stack:** ChannelEngine (primaria) + Channable (feed optimization) + Zapier (custom integrations)

---

## 16. Troubleshooting

### 16.1 Common Errors Reference

| Errore | Causa | Soluzione |
|--------|-------|----------|
| 401 Unauthorized | API key invalida/scaduta | Regenerate API key in Settings |
| 400 Bad Request | Payload JSON malformato | Validate JSON syntax |
| 404 Not Found | SKU/Order ID non esiste | Verify ID exists, check spelling |
| 409 Conflict | Stato ordine inconsistente | Retry dopo 5 sec, ChannelEngine sincronizzerà |
| 429 Too Many Requests | Rate limit raggiunto | Implementa backoff esponenziale, batch richieste |
| 500 Internal Server Error | Bug ChannelEngine | Contatta support, include request ID |
| 503 Service Unavailable | Maintenance | Retry dopo 1 minuto |

### 16.2 Best Practices

**API Integration:**
1. **Batching:** Max 100-500 items per request, non loop singoli items
2. **Caching:** Cache prodotti per 24 ore, stock per 15 minuti
3. **Error Handling:** Retry con backoff per 5xx, abort per 4xx
4. **Monitoring:** Log API latency, volume, error rates
5. **Rate Limiting:** Implementa client-side rate limiting per rimanere sotto limiti

**Order Processing:**
1. **Idempotency:** Include `merchantOrderNo` in acknowledge per evitare duplicati
2. **Shipment:** Invia tracking entro 24h da shipment per better buyer experience
3. **Returns:** Processa return entro 7 giorni per refund tempestivo
4. **Webhooks:** Sempre poll API come backup, non trustare soli webhooks

**Product Management:**
1. **Feed Quality:** Completa tutti campi global attributes, use high-res images
2. **Category Mapping:** Usa AI-mapping come starting point, refine manualmente per categorie critiche
3. **EAN/GTIN:** Sempre includi, critical per deduplicazione marketplace
4. **Updates:** Batch product updates durante off-peak hours, not during peak sales

---

## 17. Risorse e Riferimenti

### 17.1 Official Documentation & Support

| Risorsa | URL | Utilizzo |
|---------|-----|----------|
| **Sito Principale** | https://www.channelengine.com | Overview aziendale |
| **Developer Hub** | https://www.channelengine.com/developer-hub | Getting started API |
| **Help Center** | https://support.channelengine.com/hc/en-us | FAQ e guide |
| **GitHub API Docs** | https://github.com/channelengine/api-docs | OpenAPI specs |
| **API Swagger** | https://demo.channelengine.net/api/swagger-ui.html | Interactive documentation |
| **Integrations Page** | https://www.channelengine.com/en/integrations | Pre-built connectors |
| **Status Page** | https://status.channelengine.com | Service status |

### 17.2 Learning Resources

- **Onboarding:** 7-14 days standard implementation
- **Training:** Free sessions per merchant
- **Community:** Slack channel per enterprise customers
- **Certifications:** Partner certifications available

### 17.3 Support Channels

- **Email:** support@channelengine.com (standard: 24h response)
- **Chat:** In-app help center
- **Phone:** Priority support tier
- **Slack:** Enterprise customers

---

## Changelog

### Release History

| Data | Versione | Modifica |
|------|----------|----------|
| 2024-03-15 | 2.0 | Merchant API v2 full stabilization |
| 2024-02-10 | 1.9 | Hybrid fulfillment for Amazon (FBA/FBM auto-switch) |
| 2024-01-20 | 1.8 | AI-powered category mapping launch |
| 2023-12-15 | 1.7 | Webhook signature verification (HMAC-SHA256) |
| 2023-11-01 | 1.6 | Advanced Insights module add-on |
| 2023-09-30 | 1.5 | Channel API improvements, rate limit increase |
| 2023-08-15 | 1.4 | Multi-warehouse routing optimization |
| 2023-07-01 | 1.3 | Dynamic repricing rules engine |
| 2023-06-01 | 1.2 | GDPR compliance updates |

---

## Glossario Tecnico

**API Key:** Token univoco per autenticazione API, revocabile in Settings
**Batch:** Richiesta singola per multiple items (es: 100 SKU in 1 POST)
**Backoff:** Delay esponenziale tra tentativi (1s, 2s, 4s, 8s...)
**Channel:** Marketplace o piattaforma ecommerce integrata
**Dual Fulfillment:** Supporto simultaneo FBA + FBM per Amazon
**FBA:** Fulfillment By Amazon (Amazon prepara e spedisce)
**FBM:** Fulfillment By Merchant (Merchant prepara e spedisce)
**GMV:** Gross Merchandise Value (valore lordo ordini)
**GTIN:** Global Trade Item Number (codice internazionale prodotto)
**Hybrid Fulfillment:** Automatic switch tra FBA/FBM basato su stock
**MCF:** Multi-Channel Fulfillment (Amazon fulfills per altri canali)
**Merchant:** Venditore/seller che usa ChannelEngine
**Middleware:** Piattaforma (Make, Zapier) che connette sistemi
**HMAC:** Keyed-Hash Message Authentication Code (webhook signature)
**Pagination:** Division risultati in pagine (pageNumber, pageSize)
**Rate Limit:** Max richieste per time unit (es: 60 req/min)
**Real-time:** Sincronizzazione immediate (< 1 minuto)
**Reprice/Repricing:** Aggiornamento prezzo automatico basato rules
**Return:** Reso ordine dopo shipment completato
**SKU:** Stock Keeping Unit (codice prodotto interno)
**State:** Stato attuale ordine (NEW, IN_PROGRESS, SHIPPED, ecc)
**Tenant:** Istanza separata ChannelEngine per merchant
**Webhook:** HTTP callback per notifiche real-time

---

**Documento Versione:** 2.0
**Data Ultimo Aggiornamento:** 15 Marzo 2024
**Target Audience:** CTO, Systems Architect, Technical Leads
**Lingua:** Italiano
**Repository:** vendiamonoi-knowledge (Knowledge Base)
