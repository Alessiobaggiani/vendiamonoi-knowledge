# Bol.com Marketplace — Specifiche Tecniche Complete

**Versione:** 1.0
**Data:** Aprile 2026
**Lingua:** Italiano
**Target:** Gestori tecnici di operazioni multi-marketplace a livello enterprise
**Status:** Documento di riferimento universale per Vendiamonoi.it

---

## 1. Informazioni Generali

### Bol.com: Dati Essenziali

| Parametro | Dettaglio |
|-----------|----------|
| **Fondazione** | 1999 (Utrecht, Paesi Bassi) |
| **Modello di Business** | Marketplace + Retailer (Hybrid C2B2C) |
| **Sedi Operative** | Paesi Bassi (NL), Belgio (BE) |
| **Valuta Principale** | EUR |
| **Linguaggı Supportate** | Olandese (NL), Francese (BE), Inglese (BE) |
| **Certificazioni** | ISO 27001, GDPR-compliant, GS1 Member |
| **API Versione Attuale** | v10 (REST, deprecato v3 Trading API) |
| **Traffic Mensile** | ~30M visitors (NL+BE combinati) |
| **Categories** | +40M SKU attivi (NL+BE) |
| **Payment Methods** | iDEAL (NL), Bancontact (BE), Credit Card, PayPal, Klarna |

### Marketplace Geografici

| Paese | Dominio | Lingua | Valuta | Market Size | Launch |
|-------|---------|--------|--------|-------------|--------|
| **Paesi Bassi** | bol.com | Olandese/Inglese | EUR | Principale, ~€1.2B GMV 2025 | 1999 |
| **Belgio** | bol.com/be | Francese/Olandese/Inglese | EUR | Secondario, ~€200M GMV 2025 | 2013 |

**NOTA:** Bol.com ha interrotto operazioni in Francia (bol.fr) nel 2021, Germania (bol.de) nel 2019. Attualmente focalizzato su NL+BE.

### Requisiti di Registrazione Base

- **Account Type:** Business Seller (consigliato) o Personal
- **Sottoscrizione:** Nessun fee fisso (commissione variabile per transazione)
- **Numero IVA/BTW:** Obbligatorio per aziende EU
- **KYC (Know Your Customer):** Verifica identità, documento valido, indirizzo registrazione
- **Informazioni bancarie:** Coordinate IBAN per settlement (pagamenti settimanali)
- **Certificati:** GS1 membership (consigliato per EAN management)
- **SLA Onboarding:** 3-7 giorni per approvazione account

---

## 2. Modello Commissionale e Fee Structure

### Commissioni Struttura Generale (2025/2026)

| Categoria | Commissione Base | Note |
|-----------|-----------------|------|
| Elettronica | 18-22% | Varia per subcategory |
| Libri | 8% | Minore tra tutte |
| Abbigliamento | 20% | Fisso per quasi tutta categoria |
| Casa/Giardino | 18-20% | Varia per item tipo |
| Giocattoli | 18% | Relativamente fisso |
| Sport | 18% | Relativamente fisso |
| Salute/Bellezza | 18% | Incluso igiene |
| Alimentari | 15% | Ridotto (perishable considerations) |

**NOTA:** Commissioni riferite su **prezzo di vendita netto (esclusa VAT)**

---

## 3. Seller API v10 — Architettura e Autenticazione

### Endpoint Base

**Produzione:**
```
https://api.bol.com/seller-api/v10
```

**Sandbox (Testing):**
```
https://api-sandbox.bol.com/seller-api/v10
```

### Autenticazione (OAuth 2.0 - Client Credentials)

#### Step 1: Registra Applicazione

Accedi a **Bol Seller Center > Settings > API** e registra una nuova applicazione.

**Ricevi:**
- `client_id` (es: `test_client_12345`)
- `client_secret` (conserva in **vault sicuro**, non in VCS)

#### Step 2: Richiedi Access Token

```bash
curl -X POST https://login.bol.com/oauth2/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET"
```

**Risposta:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

**Token Validity:** 1 ora (3.600 secondi)

#### Step 3: Usa Token per API Calls

```bash
curl -X GET https://api.bol.com/seller-api/v10/orders \
  -H "Authorization: Bearer {access_token}"
```

### Refresh Token Strategy

- **Automatic refresh:** Refresha token 5 minuti prima della scadenza
- **Library approach:** Usa `requests-oauthlib` (Python) per auto-refresh
- **Error handling:** Se 401, esegui nuovo token request

---

## 4. Core API Endpoints — Operazioni Principali

### 4.1 Orders — Gestione Ordini

**GET** `/orders`

```json
{
  "status": "PENDING",
  "limit": 50,
  "offset": 0,
  "dateFrom": "2026-03-01T00:00:00Z",
  "dateTo": "2026-04-01T23:59:59Z"
}
```

**Response:**
```json
{
  "orders": [
    {
      "orderId": "1000000001",
      "dateTimeOrderPlaced": "2026-03-15T10:30:00Z",
      "orderStatus": "PENDING",
      "orderItems": [
        {
          "orderItemId": "1000000001001",
          "ean": "1234567890123",
          "title": "Wireless Headphones Pro",
          "quantity": 1,
          "unitPrice": 79.99,
          "commission": 18.0
        }
      ],
      "shipmentDetails": {
        "shippingLabelPresent": false,
        "giftwrap": false,
        "gift": {
          "message": null,
          "wrap": false
        }
      },
      "billingDetails": {
        "salutation": "MALE",
        "firstName": "John",
        "surname": "Doe",
        "email": "john.doe@example.com",
        "phone": "+31612345678",
        "address": "Main Street 123",
        "postalCode": "1011AB",
        "city": "Amsterdam",
        "countryCode": "NL"
      }
    }
  ],
  "listOfStatusPerStatus": {
    "PENDING": 5,
    "SHIPPED": 10,
    "CANCELLED": 2
  }
}
```

**Query Parameters:**
- `status` — Filtra per status (PENDING, SHIPPED, CANCELLED, CLOSED, ARCHIVED)
- `limit` — Numero risultati per pagina (max 50, default 50)
- `offset` — Offset per paginazione
- `dateFrom` — ISO 8601 date (es: 2026-03-01T00:00:00Z)
- `dateTo` — ISO 8601 date

**Order Status Flow:**
```
PENDING → SHIPPED → (RECEIVED, APPROVED) → CLOSED
↓
CANCELLED (anytime)
```

### 4.2 Shipments — Tracciamento Spedizioni

**GET** `/shipments/{shipmentId}`

```json
Response:
{
  "shipmentId": "5000000001",
  "shipmentItems": [
    {
      "orderItemId": "1000000001001",
      "title": "Wireless Headphones Pro",
      "quantity": 1,
      "unitPrice": 79.99
    }
  ],
  "shipmentStatus": "IN_TRANSIT",
  "trackAndTrace": "3S00000000123456",
  "shipmentReference": "OUR-REF-001",
  "transport": {
    "transportId": "DHL-123456",
    "transportCode": "DHL",
    "labelNumber": null
  },
  "estimatedDeliveryDate": "2026-03-17"
}
```

**Delivery Status Types:**
- `DELIVERED` — Consegna completata
- `IN_TRANSIT` — In transito
- `DELIVERY_TODAY` — Consegna oggi
- `DELIVERY_NEXT_DAY` — Consegna domani
- `DELIVERY_2_DAYS` — 2 giorni
- `DELIVERY_3_TO_5_DAYS` — 3-5 giorni
- `DELIVERY_6_TO_8_DAYS` — 6-8 giorni
- `DELIVERY_UNKNOWN` — Sconosciuto

### 4.3 Offers — Gestione Catalogo Prodotti

**GET** `/offers`

**POST** `/offers`

```json
{
  "sku": "SKU-12345",
  "ean": "1234567890123",
  "condition": "NEW",
  "offerPrice": 79.99,
  "quantityInStock": 15,
  "fulfilmentType": "LVB",
  "reference": "INT-REF-001",
  "onHoldByBol": false,
  "unknownProductTitle": "Wireless Headphones Pro Deluxe"
}
```

**Condition Types:**
- `NEW` — Nuovo
- `AS_GOOD_AS_NEW` — Praticamente nuovo
- `GOOD` — Buono
- `REASONABLE` — Ragionevole
- `REFURBISHED` — Ricondizionato

**Fulfillment Types:**
- `LVB` — Logistica Bol (leveraging Bol's warehouse)
- `FBR` — Fulfillment by Retailer (seller handles)

**Price Update:**
```bash
PUT /offers/{offerId}
{
  "offerPrice": 74.99
}
```

### 4.4 Inventory — Gestione Stock

**PUT** `/inventory/{offerId}`

```json
{
  "quantity": 25,
  "reservedQuantity": 5
}
```

**Total Available = quantity - reservedQuantity = 20 units**

### 4.5 Returns — Gestione Resi

**GET** `/returns`

```json
Response:
{
  "returns": [
    {
      "returnId": "2000000001",
      "orderId": "1000000001",
      "rmaNumber": "RMA-2026-001",
      "returnStatus": "OPEN",
      "returnDate": "2026-03-18T14:22:00Z",
      "returnReason": "NOT_AS_DESCRIBED",
      "items": [
        {
          "orderItemId": "1000000001001",
          "title": "Wireless Headphones Pro",
          "quantity": 1,
          "reasonCode": "NOT_AS_DESCRIBED"
        }
      ]
    }
  ]
}
```

**Return Status:** OPEN, IN_TRANSIT, RECEIVED, APPROVED, REJECTED, CLOSED

**Return Reason Codes:**
- `ITEM_DEFECTIVE` — Articolo difettoso
- `NOT_AS_DESCRIBED` — Non conforme alla descrizione
- `ITEM_DAMAGED` — Articolo danneggiato
- `UNWANTED_ITEM` — Articolo indesiderato
- `WRONG_ITEM` — Articolo sbagliato
- `MISSING_PARTS` — Parti mancanti

### 4.6 Commissions — Visualizza Commissioni Applicate

**GET** `/commissions?period=2026-03&countryCode=NL`

```json
Response:
{
  "commissions": [
    {
      "offerId": "5000000001",
      "sku": "SKU-12345",
      "title": "Wireless Headphones Pro",
      "category": "Electronics",
      "commissionPercentage": 18.0,
      "fixedAmount": 0.0,
      "totalCommission": 14.40,
      "salesAmount": 80.00
    }
  ],
  "totalCommission": 150.75,
  "totalSalesAmount": 837.50
}
```

### 4.7 Insights — Dashboard Dati Vendite

**GET** `/insights/sales?period=WEEKLY`

```json
Response:
{
  "period": "WEEK_OF_2026_03_16",
  "salesData": {
    "totalOrders": 42,
    "totalRevenue": 3245.50,
    "averageOrderValue": 77.27,
    "topProduct": {
      "sku": "SKU-12345",
      "sales": 15,
      "revenue": 1199.85
    }
  }
}
```

---

## 5. Rate Limiting e Quota API

### Global Rate Limit

**1.000 requests/ora** per API token (distributed across endpoints)

### Distribution per Endpoint

```
├── /orders: 250 req/h (25% della quota)
├── /offers: 200 req/h (20% della quota)
├── /inventory: 300 req/h (30% della quota)
├── /shipments: 150 req/h (15% della quota)
├── /returns: 100 req/h (10% della quota)
└── /commissions & /insights: 0 (illimitati)
```

### Headers Response

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 987
X-RateLimit-Reset: 1710604800
```

### Best Practices

```python
import time

def api_call_with_retry(endpoint, method="GET", max_retries=3):
    for attempt in range(max_retries):
        response = requests.request(method, endpoint)

        if response.status_code == 429:  # Rate limit exceeded
            retry_after = int(response.headers.get("Retry-After", 3600))
            wait_time = min(retry_after, 2 ** attempt)  # exponential backoff
            time.sleep(wait_time)
            continue

        return response

    raise Exception("Max retries exceeded")
```

---

## 6. Webhook Integration — Real-time Events

### Webhook Security

**Signature Header:** `X-Bol-Signature: sha256=xxxxx`

### Validazione Firma

```json
Webhook Header: X-Bol-Signature: sha256=xxxxx

Implementazione:
  1. Ricevi webhook + firma
  2. Calcola HMAC-SHA256(webhook_body, client_secret)
  3. Confronta con X-Bol-Signature
  4. Accetta se coincide
  5. Rifiuta/log se mismatch (potenziale spoofing)
```

### Webhook Events Supportati

- `order.created` — Nuovo ordine
- `order.shipped` — Ordine spedito
- `order.cancelled` — Ordine annullato
- `return.initiated` — Reso iniziato
- `return.approved` — Reso approvato
- `shipment.delivered` — Consegna completata

---

## 7. Buy Box Algorithm — Strategia Ottimale per Visibilità

### Fattori Influenzanti (Priorità Ranking)

#### 1. PRICE OPTIMIZATION
- Monitora prezzi competitor quotidianamente
- Usa strumenti di dynamic pricing
- Non sottotaggiare mai il margine sostenibile
- Target: margine 10-15% zona ideale

#### 2. DELIVERY SPEED
- Priorità 1: Ottieni badge DELIVERY_NEXT_DAY
- Usa LVB se volume >500 unit/mese
- FBR: Negozia accordi carrier next-day
- Impatto: +200% conversion vs opzione 3-5 giorni

#### 3. STOCK MANAGEMENT
- Mantieni >10 unit minimo (segnale fiducia)
- Evita zero-stock (perdi ranking)
- Sincronizzazione inventory real-time (mai stale >4h)

#### 4. SELLER SCORE
- Target: 90+ score (Buy Box requirement)
- <1% cancellation rate assoluto
- <3% return rate accettabile
- <24h response time critico

#### 5. FULFILLMENT METHOD
- LVB preferito da algoritmo (+5-10% visibility)
- FBR accettabile se veloce
- Ibrido (alcuni LVB, alcuni FBR) funziona

### Buy Box Score Formula

```
Buy Box Score =
  (Price Competitiveness × 0.35) +
  (Delivery Speed × 0.25) +
  (Seller Score Norm × 0.20) +
  (Stock Level Norm × 0.10) +
  (Fulfillment Type Bonus × 0.10)

Where 0.0 = worst, 1.0 = best possible

Example:
  Price Comp:  0.95 × 0.35 = 0.3325
  Delivery:    1.00 × 0.25 = 0.2500 (DELIVERY_NEXT_DAY)
  Seller:      0.90 × 0.20 = 0.1800
  Stock:       0.85 × 0.10 = 0.0850
  Fulfill:     1.00 × 0.10 = 0.1000 (LVB)
  ──────────────────────────
  TOTAL:                    0.9475
```

**Interpretation:**
- **0.95+** → Buy Box winner likely
- **0.80-0.95** → Competitive, monitor pricing
- **<0.80** → Improve seller metrics or speed

---

## 8. Esempi Pratici — Use Cases Reali

### Use Case 1: Wireless Headphones Listing

**[Brand]:** Sony, Bose, Apple

**[Unique Selling Proposition]:**
- Riduzione rumore leader del settore (40dB riduzione)
- Batteria 30 ore, carica veloce 10min = 5h playtime
- Costruzione premium (alluminio, pelle genuina earcup)
- Connettività seamless: Bluetooth 5.3, multi-device pairing
- Include hard case + cavo 3.5mm per modalità wired

**[Title Format]:**
```
[Brand] Wireless Headphones [Model] - Active Noise Cancellation, 30h Battery, Bluetooth 5.3
```

**[Description Structure]:**
1. Opening (problem solved)
2. Key specs (short form)
3. Use cases (buyer scenarios)
4. Warranty/support details
5. Optional: Content score notice

### Use Case 2: LVB vs FBR — Financial Analysis

#### LVB (Logistica Bol)

```
Cost per unit: €2-4 (DHL/GLS agreement)
Delivery: Next business day (NL metro), 2-3d (NL rural)
Cash flow: Monthly payment terms with carrier
Scalability: Manual picking/packing until 100+ daily orders
```

**Monthly Impact (100 units/day):**
- Fulfillment cost: €300-400
- Margin retained: 15-18%
- Time investment: ~5h/week (packing)

#### FBR (Fulfillment by Retailer)

```
Cost per unit: €3-6 total (inbound €0.50, storage €0.10/d, fulfillment €3/order)
Delivery: Same-day (metro) or next-day (EU)
Cash flow: Weekly settlement, Bol covers logistics
Scalability: Unlimited (automated warehouse)
```

**Monthly Impact (100 units/day):**
- Fulfillment cost: €300-600
- Margin retained: 12-15%
- Time investment: ~0.5h/week (monitoring)

**Decision Criteria:**
- **Choose LVB if:** Volume <50 units/day, high margin items (>€30), dedicated team
- **Choose FBR if:** Volume >100 units/day, rapid growth, limited logistics
- **Choose Hybrid if:** Mixed SKU portfolio with different velocities

---

## 9. Errori Comuni e Soluzioni

### Errore: "401 Unauthorized"

**Causa:** Token scaduto o non valido

**Soluzione:**
1. Verifica expiry time (X-RateLimit-Reset header)
2. Refresha token: POST /oauth2/token
3. Verifica client_secret non scaduto (scade ogni 12 mesi)

### Errore: "429 Too Many Requests"

**Causa:** Rate limit superato

**Soluzione:**
1. Implementa exponential backoff (2^attempt max 3600s)
2. Distribuisci load across time (non batch 300 requests in 1 min)
3. Contatta support per rate limit increase (per enterprise accounts)

### Errore: "ACTIVE_OFFERS_LIMIT_EXCEEDED"

**Causa:** Troppe offerte attive per categoria

**Soluzione:**
1. Verifica limits per categoria in Seller Center
2. Archivia offerte a zero stock
3. Contatta support per aumentare limite

---

## 10. Spostamento da Altre Piattaforme

### Amazon → Bol.com

**Differenze Chiave:**
- FBA cost structure diverso (non c'è per-unit fee base, solo fulfillment)
- Buy Box algorithm meno aggressivo su prezzo (data più alta importanza)
- Customer base: 30M visitors/mese NL+BE (~50% volume di Amazon EU)
- Commission structure: Flat per categoria, no additional fees

**Strategia Migrazione:**
1. Importa top 20% SKU per volume (Pareto)
2. Testa LVB con subset (100-200 units)
3. Scala gradualmente basandoti su conversion metrics
4. Mantieni Amazon durante ramp-up (3-6 mesi overlap)

### eBay → Bol.com

**Vantaggi Bol:**
- Logistica integrata (no need for 3PL)
- Seller protection più forte (Bol covers authenticity)
- Customer base younger, higher LTV
- No auction model (fissi prices only)

**Mapping di Strategie:**
- eBay "Best Offer" → Bol dynamic pricing
- eBay "Auction" → Bol promotions/flash sales
- eBay FBA → Bol LVB (direct equivalent)

---

## 11. Contacts e Supporto

### Bol.com Resources

- **Seller Center:** https://partnerweb.bol.com
- **Developer Portal:** https://developer.bol.com
- **API Documentation:** https://developer.bol.com/documentation/seller-api/v10
- **Support Email:** seller-support@bol.com
- **Response SLA:** <24h (business days)

### Technical Support

- **API Issues:** developer-support@bol.com
- **Integration:** Dedicated technical account manager (volume >€500K/year GMV)
- **Webhook Debugging:** Test tool available in Seller Center > API > Webhooks

---

**Documento Version:** 1.0
**Last Updated:** Aprile 2026
**Maintained by:** Vendiamonoi.it Technical Operations