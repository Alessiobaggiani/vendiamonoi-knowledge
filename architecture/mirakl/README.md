# Mirakl — Documentazione Tecnica Completa della Piattaforma

**Versione:** 1.0
**Data:** Aprile 2026
**Destinatario:** CTO, Architetti di Sistema, Esperti Tecnici Marketplace
**Contesto:** Documentazione tecnica universale per aziende multi-marketplace europee (20-30 marketplace)

---

## Informazioni Generali su Mirakl

| Attributo | Valore |
|-----------|--------|
| **Nome Ufficiale** | Mirakl |
| **Tipo di Piattaforma** | SaaS Marketplace Platform |
| **Sito Web** | https://www.mirakl.com |
| **Documentazione Ufficiale** | https://developer.mirakl.com/ |
| **API Base URL Pattern** | `https://{marketplace-subdomain}.mirakl.com/api` |
| **Fondazione** | 2012 |
| **Co-Fondatori** | Philippe Corrot, Adrien Nussenbaum |
| **Sede Principale** | Parigi, Francia (con uffici a Boston, Massachusetts) |
| **Co-CEO Attuali** | Philippe Corrot, Adrien Nussenbaum |
| **Ultimo Finanziamento** | $555 milioni (Series E, 2021) |
| **Valutazione (2021)** | $3.5 miliardi+ |
| **Marketplace Powered Globalmente** | 350+ (47 solo tramite Lengow) |
| **Clienti Globali** | 300+ aziende enterprise |
| **Certificazioni** | SOC 2 Compliance, GDPR Compliant |
| **Principali Clienti** | Carrefour, Leroy Merlin, FNAC Darty, La Redoute, H&M, Best Buy, Decathlon, Boulanger |

---

## 1. Architettura della Piattaforma Mirakl

### 1.1 Panoramica Generale

Mirakl è una piattaforma SaaS (Software-as-a-Service) che permette alle aziende di costruire, gestire e scalare marketplace online. La piattaforma utilizza un'architettura multi-tenant che consente a centinaia di marketplace di operare in modo indipendente sulla stessa infrastruttura.

**Principi Architetturali:**
- **Isolamento dei Dati:** Ogni marketplace ha dati completamente isolati
- **Scalabilità Orizzontale:** La piattaforma scala automaticamente con il carico
- **API-First:** Tutti i servizi esposti tramite REST API
- **Microservizi:** Componenti indipendenti (Catalogo, Ordini, Pagamenti, Shipping)

### 1.2 I Tre Attori Principali

La piattaforma Mirakl si basa su un modello di business a tre attori:

1. **Marketplace Operator (Azienda Host)**
   - Proprietario della piattaforma marketplace
   - Gestisce brand, customer relationships e fulfillment
   - Fornisce il catalogo principale (catalogo dell'azienda host)
   - Controlla pricing, promotions e UX
   - Esempi: Carrefour.fr, Fnac.com, Leroy Merlin

2. **Sellers/Vendors (Venditori Terzi)**
   - Aziende che vendono prodotti tramite il marketplace
   - Forniscono i propri SKU e dati di inventario
   - Gestiscono le proprie descrizioni prodotto
   - Impostano i propri prezzi (in alcuni modelli)
   - Hanno accesso a un seller dashboard

3. **Customers (Clienti Finali)**
   - Acquistano sia dal catalogo dell'operator che dai sellers
   - Esperienza unificata di shopping
   - Un'unica esperienza di checkout
   - Un'unica esperienza di customer service

---

## 2. Componenti Principali della Piattaforma

### 2.1 Catalogo Prodotti

**Gestione del Catalogo:**
- **Catalogo Operator:** Prodotti gestiti direttamente dall'operator
- **Catalogo Sellers:** Prodotti forniti dai sellers terzi
- **Sincronizzazione:** Aggiornamenti in tempo reale via API
- **Attributi Prodotto:** SKU, nome, descrizione, prezzo, immagini, categoria

**API di Catalogo (Shop API):**
```
Endpoint: /api/shop/products
Metodi: GET, POST, PUT, DELETE
```

Caratteristiche:
- Supporto multi-lingua
- Gestione delle varianti prodotto (size, color, etc.)
- Attributi custom per marketplace
- SEO-friendly URLs

### 2.2 Gestione degli Ordini

**Flusso Ordini:**
1. Creazione ordine
2. Pagamento
3. Conferma ordine
4. Fulfillment (picking, packing, shipping)
5. Consegna
6. Post-delivery (resi, review)

**Stato degli Ordini:**
- WAITING_ACCEPTANCE: In attesa di accettazione dal seller
- ACCEPTED: Ordine accettato dal seller
- SHIPPED: Ordine spedito
- DELIVERED: Ordine consegnato
- CLOSED: Ordine completato
- REFUSED: Ordine rifiutato dal seller
- CANCELED: Ordine cancellato dal cliente

**API Ordini:**
```
Endpoint: /api/shop/orders
Metodi: GET, POST, PUT
```

### 2.3 Pagamenti

**Modello di Pagamento:**
- Il cliente paga l'operator (Marketplace)
- L'operator raccoglie i pagamenti per i sellers
- Settlement periodico ai sellers (solito mensile o settimanale)

**Gateway di Pagamento:**
- Stripe
- PayPal
- 2Checkout
- Local payment methods per paese

**API Pagamenti:**
- Non direttamente gestita da Mirakl (tokenizzazione esterna)
- Mirakl registra transazioni e settlement

### 2.4 Logistica e Shipping

**Opzioni di Spedizione:**

**1. Seller-Fulfillment (Seller gestisce spedizione)**
- Il seller pacca e spedisce direttamente
- Seller fornisce tracking number
- Mirakl integra con carrier API

**2. Operator-Fulfillment (Operator gestisce spedizione)**
- Operator centralizza logistica
- Tutti gli ordini spediti da magazzino operator
- Seller invia inventario a operator

**3. 3PL/Dropshipping**
- Third-party logistics provider
- Seller/Operator usa servizi logistici esterni
- Carrier API integration

**Carrier Integrations:**
- DPD, La Poste, UPS, DHL
- Real-time shipping rates
- Label generation
- Tracking integration

**API Shipping:**
```
Endpoint: /api/shop/shipments
Metodi: GET, POST, PUT
```

---

## 3. API Mirakl — Architettura Tecnica

### 3.1 Autenticazione

**Metodo: OAuth 2.0 + API Keys**

**API Key Authentication:**
```http
Authorization: Basic <base64(api-key)>
```

**OAuth 2.0 Flow:**
1. Registrazione dell'app
2. Richiesta di authorization code
3. Scambio per access token
4. Uso del token per API calls

**Scopes (Permessi):**
- `catalog:read` - Lettura catalogo
- `catalog:write` - Modifica catalogo
- `orders:read` - Lettura ordini
- `orders:write` - Modifica ordini
- `products:read` - Lettura prodotti
- `products:write` - Modifica prodotti
- `sellers:read` - Lettura dati sellers

### 3.2 Base URL e Endpoints

**Base URL Pattern:**
```
https://{marketplace-subdomain}.mirakl.com/api
```

**Esempi:**
- Carrefour: `https://carrefour.mirakl.com/api`
- FNAC Darty: `https://fnac-darty.mirakl.com/api`
- Leroy Merlin: `https://leroymerlin.mirakl.com/api`

**Versione API:**
- Attuale: v2.0
- Legacy support: v1.0 (deprecating)

### 3.3 Operazioni CRUD di Base

#### GET - Recuperare Risorse
```http
GET /api/shop/products?limit=10&offset=0
Authorization: Basic <api-key>

Risponse:
{
  "data": [
    {
      "product_id": "123456",
      "sku": "PROD-001",
      "title": "Laptop",
      "description": "High-performance laptop",
      "price": 999.99,
      "currency": "EUR",
      "status": "ACTIVE"
    }
  ],
  "paging": {
    "total_count": 5000,
    "limit": 10,
    "offset": 0
  }
}
```

#### POST - Creare Risorse
```http
POST /api/shop/products
Authorization: Basic <api-key>
Content-Type: application/json

{
  "sku": "PROD-002",
  "title": "Desktop Monitor",
  "description": "4K monitor",
  "price": 349.99,
  "currency": "EUR",
  "category_id": "electronics"
}

Risponse: 201 Created
{
  "product_id": "789012",
  "sku": "PROD-002",
  "status": "ACTIVE"
}
```

#### PUT - Aggiornare Risorse
```http
PUT /api/shop/products/123456
Authorization: Basic <api-key>
Content-Type: application/json

{
  "price": 899.99,
  "status": "INACTIVE"
}

Risponse: 200 OK
{
  "product_id": "123456",
  "status": "INACTIVE"
}
```

#### DELETE - Cancellare Risorse
```http
DELETE /api/shop/products/123456
Authorization: Basic <api-key>

Risponse: 204 No Content
```

### 3.4 Rate Limiting

**Limiti di Rate:**
- **Standard:** 1000 requests per minuto
- **Premium:** 5000 requests per minuto
- **Enterprise:** Custom limits

**Headers di Response:**
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 987
X-RateLimit-Reset: 1623456789
```

**Comportamento di Superamento:**
- HTTP 429 Too Many Requests
- Retry-After header indica secondi di attesa

### 3.5 Gestione Errori

**Formato Errore Standard:**
```json
{
  "error_code": "INVALID_REQUEST",
  "message": "Missing required field: sku",
  "details": {
    "field": "sku",
    "issue": "required"
  },
  "timestamp": "2026-04-01T10:30:00Z"
}
```

**Codici di Errore Comuni:**
- `400` - Bad Request (validation error)
- `401` - Unauthorized (auth failure)
- `403` - Forbidden (insufficient permissions)
- `404` - Not Found (resource not found)
- `409` - Conflict (duplicate SKU, etc.)
- `429` - Too Many Requests (rate limit)
- `500` - Internal Server Error

### 3.6 Pagination

**Parametri:**
```
GET /api/shop/products?limit=50&offset=100
```

**Response Paging Info:**
```json
{
  "data": [...],
  "paging": {
    "total_count": 10000,
    "limit": 50,
    "offset": 100,
    "has_more": true
  }
}
```

---

## 4. Integrazione dei Sellers

### 4.1 Seller Onboarding

**Processo di Onboarding:**
1. Registrazione seller
2. Verifica documenti (VIES, business license)
3. Approvazione manuale
4. Setup account
5. Accesso dashboard

**Requisiti Seller:**
- EU VAT number o business registration
- Bank account per payments
- Accepted terms of service
- Insurance (settore-specifico)

### 4.2 Seller Dashboard

**Funzionalità:**
- Gestione catalogo prodotti
- Visualizzazione ordini
- Tracking spedizioni
- Analytics e reporting
- Gestione rating e review
- Impostazioni account

### 4.3 Seller API

**Endpoint Seller:**
```
https://{marketplace-subdomain}.mirakl.com/api/seller
```

**Operazioni Principali:**
```
GET /api/seller/inventory - Visualizza inventario
POST /api/seller/products - Carica prodotti
PUT /api/seller/products/{id} - Aggiorna prodotto
GET /api/seller/orders - Visualizza ordini
PUT /api/seller/orders/{id} - Aggiorna stato ordine
```

---

## 5. Webhook e Notifiche

### 5.1 Tipi di Webhook

**Product Events:**
- `product.created`
- `product.updated`
- `product.deleted`

**Order Events:**
- `order.created`
- `order.accepted`
- `order.shipped`
- `order.delivered`
- `order.canceled`

**Inventory Events:**
- `inventory.updated`
- `stock.low` (alert)

### 5.2 Registrazione Webhook

**Endpoint Configurazione:**
```
POST /api/webhooks

{
  "url": "https://myapp.com/webhook",
  "events": ["order.created", "order.shipped"],
  "secret": "webhook_secret_key"
}
```

### 5.3 Payload Webhook

**Esempio Order Created:**
```json
{
  "event": "order.created",
  "timestamp": "2026-04-01T10:30:00Z",
  "data": {
    "order_id": "ORD-001234",
    "customer_email": "customer@example.com",
    "items": [
      {
        "product_id": "123456",
        "sku": "PROD-001",
        "quantity": 1,
        "price": 99.99
      }
    ],
    "total_amount": 99.99,
    "currency": "EUR",
    "status": "WAITING_ACCEPTANCE"
  }
}
```

### 5.4 Verifica Webhook

**Signature Verification (HMAC-SHA256):**
```python
import hmac
import hashlib
import json

def verify_webhook(payload, signature, secret):
    expected_signature = hmac.new(
        secret.encode(),
        payload.encode(),
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(signature, expected_signature)

# Nel header della richiesta:
# X-Mirakl-Signature: <HMAC-SHA256>
```

---

## 6. Batch Operations

### 6.1 Import Prodotti CSV

**Formato CSV:**
```csv
sku,title,description,price,currency,category_id,image_url
PROD-001,Laptop,High-performance,999.99,EUR,electronics,https://...
PROD-002,Monitor,4K Display,349.99,EUR,electronics,https://...
```

**Upload Batch:**
```http
POST /api/batch/products/import
Content-Type: multipart/form-data

file: [CSV file]

Risponse:
{
  "batch_id": "BATCH-123456",
  "status": "PROCESSING",
  "total_items": 5000
}
```

**Verifica Stato Batch:**
```http
GET /api/batch/BATCH-123456

Risponse:
{
  "batch_id": "BATCH-123456",
  "status": "COMPLETED",
  "total_items": 5000,
  "successful_items": 4990,
  "failed_items": 10
}
```

### 6.2 Batch Order Processing

**Processamento Bulk Ordini:**
```http
POST /api/batch/orders/accept

{
  "order_ids": ["ORD-001", "ORD-002", "ORD-003"]
}
```

---

## 7. Integrazioni Third-Party

### 7.1 ERP Integration

**Scenario Comune:**
```
ERP System → API Connector → Mirakl API → Marketplace
↓
Order Management
↓
Mirakl API → API Connector → ERP System
```

**Dati Sincronizzati:**
- Catalogo prodotti
- Inventario
- Ordini
- Prezzi

### 7.2 WMS Integration

**Workflow:**
1. Ordine creato in Mirakl
2. Notifica WMS via webhook
3. WMS picking/packing
4. WMS invia shipping info a Mirakl
5. Mirakl aggiorna cliente con tracking

### 7.3 Reporting Tool Integration

**Data Export APIs:**
```
GET /api/analytics/sales
GET /api/analytics/inventory
GET /api/analytics/sellers
GET /api/analytics/orders
```

---

## 8. Performance e Ottimizzazione

### 8.1 Caching Strategies

**Client-Side Caching:**
```http
Response Headers:
Cache-Control: max-age=300
ETag: "abc123def456"
```

**Conditional Requests:**
```http
GET /api/shop/products/123456
If-None-Match: "abc123def456"

Risponse: 304 Not Modified (if unchanged)
```

### 8.2 Optimized API Calls

**Bulk Requests:**
```http
POST /api/batch/products/get

{
  "product_ids": ["123456", "789012", "345678"]
}
```

**Field Filtering:**
```http
GET /api/shop/products/123456?fields=sku,title,price
```

### 8.3 Asynchronous Processing

**Long-Running Operations:**
```http
POST /api/batch/products/import (returns job_id)
GET /api/batch/jobs/{job_id} (check status)
```

---

## 9. Security Best Practices

### 9.1 API Key Management

**Raccomandazioni:**
- Ruotare API keys periodicamente (ogni 90 giorni)
- Usare separate keys per dev/test/prod
- Non committare keys in git
- Usare environment variables
- Tracciare usage per chiave

**Esempio Secure Storage:**
```python
import os
api_key = os.environ.get('MIRAKL_API_KEY')
```

### 9.2 HTTPS e TLS

**Tutti gli endpoint:** HTTPS only (TLS 1.2+)

**Certificate Pinning (optional):**
```python
# Pinning del certificato Mirakl
CA_CERT = "/path/to/mirakl-ca.pem"
requests.get(url, verify=CA_CERT)
```

### 9.3 Data Protection

**In Transit:**
- TLS 1.2+ encryption
- Authenticated encryption

**At Rest:**
- Database encryption
- Sensitive field masking (in logs)

**GDPR Compliance:**
- Data retention policies
- Right to deletion
- Data portability

---

## 10. Monitoraggio e Logging

### 10.1 Logging Best Practices

**Mai Loggare:**
- API keys
- Sensitive customer data
- Credit card information
- Passwords

**Da Loggare:**
- Request/response timestamps
- HTTP status codes
- Error details (non-sensitive)
- API endpoint accessed

**Formato Log Strutturato:**
```json
{
  "timestamp": "2026-04-01T10:30:00Z",
  "request_id": "req-123456",
  "method": "POST",
  "endpoint": "/api/shop/orders",
  "status_code": 201,
  "duration_ms": 245,
  "error": null
}
```

### 10.2 Monitoring Metriche

**KPIs da Monitorare:**
- API response time (p50, p95, p99)
- Error rate per endpoint
- Rate limit usage
- Webhook delivery success rate
- Database query performance

### 10.3 Alerting

**Alert Conditions:**
- Error rate > 5%
- Response time > 2s (p95)
- Rate limit approaching
- Webhook delivery failures > 10%

---

## 11. Caso di Studio: Integrazione Carrefour

### 11.1 Architettura Integrazione

```
[Carrefour ERP] ↔ [Integration Layer] ↔ [Mirakl API] ↔ [Carrefour Marketplace]
     ↓
  [Inventory]
  [Orders]
  [Prices]
```

### 11.2 Flusso Dati

**Catalogo:**
1. ERP genera feed prodotti
2. Connector legge feed
3. Connector chiama Mirakl API `/shop/products`
4. Mirakl indexa prodotti
5. Prodotti visibili su marketplace

**Ordini:**
1. Cliente ordina su marketplace
2. Mirakl crea ordine
3. Webhook notifica ERP
4. ERP fulfills ordine
5. ERP invia shipping info a Mirakl
6. Mirakl notifica cliente

**Inventory:**
1. Warehouse system aggiorna stock
2. ERP sincronizza stock
3. Connector chiama `/inventory/update`
4. Mirakl aggiorna disponibilità
5. Marketplace riflette nuovo stock

---

## 12. Troubleshooting Comune

### 12.1 Errori di Autenticazione

**Problema:** 401 Unauthorized

**Cause:**
- API key mancante o scaduta
- API key non valida
- Marketplace non trovato

**Soluzione:**
```
1. Verifica API key
2. Verifica marketplace subdomain
3. Verifica header Authorization
```

### 12.2 Errori di Validazione

**Problema:** 400 Bad Request

**Cause:**
- Campo obbligatorio mancante
- Formato dato non corretto
- Valore fuori dal range consentito

**Soluzione:**
```
1. Leggi messaggio di errore
2. Controlla documentazione campo
3. Valida dato prima di inviare
```

### 12.3 Rate Limiting

**Problema:** 429 Too Many Requests

**Soluzione:**
1. Aspetta Retry-After secondi
2. Implementa exponential backoff
3. Usa batch operations
4. Richiedi limite maggiore a Mirakl

### 12.4 Webhook Non Ricevuti

**Controlli:**
1. Verifica URL webhook è raggiungibile
2. Controlla firewall/security groups
3. Verifica secret signature
4. Controllo logs Mirakl webhook

---

## 13. Roadmap e Innovazioni Mirakl

### 13.1 Funzionalità in Arrivo

**2026:**
- Mirakl AI: Raccomandazioni prodotto
- Advanced Fulfillment Options
- Blockchain per supply chain transparency
- Real-time inventory analytics

### 13.2 Deprecazioni Previste

**API v1.0:** Deprecata a fine 2026
- Migrazione obbligatoria a v2.0
- Support fino a fine 2027

---

## 14. Risorse e Supporto

### 14.1 Documentazione Ufficiale
- https://developer.mirakl.com/ - API docs
- https://mirakl.force.com/ - Knowledge base
- https://www.mirakl.com/blog/ - Blog

### 14.2 Contatti Supporto

**Technical Support:**
- Email: support@mirakl.com
- Portal: https://support.mirakl.com
- Slack: Mirakl community

**Partner Success:**
- Partner portal
- Regular sync calls
- Dedicated account manager

### 14.3 Training Resources

**Corsi Online:**
- Mirakl Academy (gratuito)
- Certified partner training
- Webinar mensili

**Comunità:**
- Developer forum
- GitHub repositories
- User conferences

---

## Conclusioni

Mirakl è una piattaforma marketplace robusta e scalabile che supporta centinaia di marketplace europei. La sua architettura API-first facilita integrazioni seamless con sistemi legacy e moderni.

**Punti Chiave:**
- API standardizzate e ben documentate
- Supporto completo per multi-tenant marketplace
- Scalabilità fino a 600+ milioni SKU
- Integrazioni solide con ERP, WMS, payment gateways
- Conformità GDPR e SOC 2

**Consigli di Implementazione:**
1. Pianificare architettura integrazione early
2. Usare batch operations per performance
3. Implementare proper error handling
4. Monitorare API usage e performance
5. Mantenere documentazione updated
6. Formare team su best practices
7. Testare thoroughly prima di prod launch
8. Usare sandbox environment per sviluppo

**Contatti Supporto:**
Per supporto tecnico specifico o domande marketplace, contattare:
- Mirakl Support Team
- Partner success manager
- Community forum

---

**Fine Documentazione**

**Aggiornamenti:** La documentazione viene aggiornata quando Mirakl rilascia nuove API o marketplaces.

**Disclaimer:** Questa documentazione si basa su informazioni pubbliche e best practices industry. Per specifiche di marketplace, contattare il respectivo operator support.

---

**Documentazione Preparata Per:** Vendiamonoi.it
**Contesto:** 20-30 Marketplace Europei Mirakl-Powered
**Versione:** 1.0
**Data:** Aprile 2026