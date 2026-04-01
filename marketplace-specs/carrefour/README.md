# Specifiche Comprehensive Carrefour Marketplace
## Documento Tecnico per Operatori Multi-Marketplace Europei

**Versione:** 2.1
**Data:** Aprile 2026
**Destinatario:** CTO, System Architects, Marketplace Operators
**Azienda:** Vendiamonoi.it - Digital Distribution Platform

---

## 1. INFORMAZIONI GENERALI

### 1.1 Panoramica Carrefour Marketplace

Carrefour gestisce una delle più grandi piattaforme marketplace multi-categoria in Europa. La piattaforma è costruita su **Mirakl**, una soluzione enterprise consolidata per gestire marketplace B2C. Carrefour Marketplace rappresenta una trasformazione digitale strategica del Carrefour Group, complementare alla vendita retail tradizionale.

| **Paese** | **URL Principale** | **Stato Mirakl** | **Attivo** | **Categoria Focus** |
|-----------|-------------------|------------------|-----------|-------------------|
| Francia | carrefour.fr/marketplace | Mirakl Nativo | ✅ 2017 | Multi-categoria, FMCG |
| Spagna | carrefour.es/marketplace | Mirakl Nativo | ✅ 2018 | Multi-categoria |
| Italia | carrefour.it/marketplace | Mirakl Nativo | ✅ 2019 | Multi-categoria, food |
| Belgio | carrefour.be/marketplace | Mirakl Nativo | ✅ 2020 | Multi-categoria |
| Romania | carrefour.ro/marketplace | Mirakl Nativo | ✅ 2021 | Selettivo |
| Polonia | carrefour.pl/marketplace | Mirakl Nativo | ✅ 2022 | Selettivo |

### 1.2 Struttura Organizzativa

```
Carrefour Group
├── Carrefour Marketplace (Francia)
├── Carrefour Marketplace (Spagna)
├── Carrefour Marketplace (Italia)
├── Carrefour Marketplace (Belgio)
├── Carrefour Marketplace (Romania)
└── Carrefour Marketplace (Polonia)
```

Ogni paese opera su un'istanza Mirakl **completamente separata e indipendente**. Non esiste sincronizzazione automatica tra le istanze. Ogni operatore deve registrarsi singolarmente per ogni paese target.

### 1.3 Caratteristiche Piattaforma

- **Motore:** Mirakl SaaS (versione enterprise)
- **Modello:** Commission-based B2C marketplace
- **Lingue supportate:** FR, ES, IT, RO, PL
- **Valute:** EUR (tutte le istanze)
- **Payment:** Stripe, Skrill, vari metodi locali
- **Tipo Seller:** SME, brand, distributor
- **Commissione piattaforma:** 8-15% per categoria (media 11%)
- **Shipping:** Fulfillment by Carrefour (CFC) o Seller Fulfillment
- **Compliance:** GDPR, Strong Customer Authentication (SCA), KYC

---

## 2. ONBOARDING SELLER - PROCESSO COMPLETO

### 2.1 Fase 1: Registrazione e Verifica (Days 1-5)

#### Step 1.1 - Registrazione Iniziale
```
Carrefour.it/marketplace → "Diventa Venditore"
↓
Form Registrazione:
- Email aziendale
- Nome completo operatore
- Ragione sociale + P.IVA
- Indirizzo sede legale
- Telefono contatto
- Consensi privacy/GDPR
```

**Dati Tecnici Mirakl:**
- Endpoint: `POST /api/seller_accounts`
- Rate limit: 100 req/hour
- Risposta: JSON con `seller_id` (UUID)

#### Step 1.2 - Verifica Identità
```
KYC Process (Know Your Customer):
1. Caricamento documento ID (fronte/retro)
   - Accettati: Passaporto, Patente, ID Card
   - File: JPEG/PDF, max 5MB
   - Endpoint Mirakl: POST /api/uploads

2. Verifica indirizzo
   - Documento: Ultima bolletta/estratto bancario
   - Matching: Address vs registro P.IVA
   - Timeline: 24-48 ore

3. Verifica entità aziendale
   - Cross-check: Camera di Commercio italiana
   - Attivo/Regolare: obbligatorio
   - P.IVA valido: API validation
```

**Timeline Mirakl:**
- KYC auto: 2-4 ore (API validation)
- KYC manuale (se documentazione incompleta): 24-48 ore
- Status check: GET /api/sellers/{seller_id}/kyc_status

### 2.2 Fase 2: Setup Negozio (Days 5-10)

#### Step 2.1 - Informazioni Negozio
```json
POST /api/sellers/{seller_id}/shop
{
  "name": "Nome Negozio (max 50 char)",
  "description": "Descrizione attività (max 500 char)",
  "shop_url": "url-shop",
  "country": "IT",
  "language": "it",
  "logo": "upload_reference_from_POST_/uploads",
  "banner": "upload_reference_from_POST_/uploads",
  "phone": "+39-XXXXXXXXXX",
  "return_policy_url": "https://...",
  "return_policy_text": "...",
  "customer_support_email": "support@example.com",
  "customer_support_phone": "+39-XXXXXXXXXX"
}
```

#### Step 2.2 - Configurazione Pagamenti
```
Metodi supportati in Italia:
1. STRIPE (preferito - 2.4% fee)
   - Connessione OAuth via Mirakl
   - Settlement: T+2 giorni
   - Webhook: Sincronizzazione automatica

2. SKRILL (alternativo - 3.5% fee)
   - Manual integration
   - Settlement: T+3 giorni

3. Bank Transfer (Bonifico - 0% Mirakl fee)
   - Solo settlement, non pagamenti diretti
   - Documentazione bancaria richiesta
```

**Setup Stripe su Carrefour IT Mirakl:**
```
1. Seller accede al dashboard
2. Payment Methods → Connect Stripe
3. Redirect a Stripe OAuth
4. Stripe account linking (o creazione)
5. Mirakl riceve Stripe account ID
6. Webhook registration automatica
```

### 2.3 Fase 3: Catalogo Prodotti (Days 10-30)

#### Step 3.1 - Setup Catalogo

**Modalità 1: Integrazione API (Consigliata)**
```
API Mirakl Catalog:
- Endpoint base: https://carrefour-it.mirakl.com/api
- Authentication: API Token in header
- Rate limits: 500 req/min per seller

Creazione Prodotto:
POST /api/catalogs/{catalog_id}/products
{
  "sku": "UNIQUE_SKU_PER_SELLER",
  "title": "Titolo prodotto (max 200 char)",
  "description": "Descrizione dettagliata",
  "category_id": "miraklCategoryId",
  "price": "99.99",
  "currency": "EUR",
  "quantity": 100,
  "attributes": {
    "color": "Rosso",
    "size": "M",
    "brand": "NomeBrand"
  },
  "images": [
    {"url": "https://...", "position": 0}
  ],
  "condition": "NEW",
  "shipping_type": "SELLER_FULFILLMENT"
}
```

**Modalità 2: CSV Upload**
```
Template Carrefour-IT:
sku|title|category|price|currency|quantity|condition|shipping_type|image_url_1|image_url_2|...
SKU001|Prodotto 1|Abbigliamento > T-shirt|19.99|EUR|50|NEW|SELLER_FULFILLMENT|https://...|https://...
SKU002|Prodotto 2|Abbigliamento > Pantaloni|49.99|EUR|25|NEW|SELLER_FULFILLMENT|https://...

Upload:
POST /api/catalogs/{catalog_id}/bulk_import
Content-Type: multipart/form-data
file: products.csv
```

#### Step 3.2 - Categoria Mapping

**Carrefour IT Category Tree (Estratto):**
```
Abbigliamento (parent_id: 10)
├── Abbigliamento Uomo (id: 101)
│   ├── T-shirt e Top (id: 1011)
│   ├── Pantaloni (id: 1012)
│   └── Giacche (id: 1013)
├── Abbigliamento Donna (id: 102)
│   ├── Vestiti (id: 1021)
│   └── Gonne (id: 1022)
└── Abbigliamento Bambini (id: 103)

Elettrodomestici (parent_id: 20)
├── Cucina (id: 201)
│   ├── Forni (id: 2011)
│   └── Frigoriferi (id: 2012)
└── Pulizia (id: 202)

Alimentari (parent_id: 30)
├── Bevande (id: 301)
│   ├── Caffè (id: 3011)
│   └── Vino (id: 3012)
└── Snack (id: 302)
```

**Processo di Attivazione Categoria:**
1. Seller richiede accesso categoria tramite dashboard
2. Carrefour verifica: stock minimo, rating, compliance
3. Approvazione automatica per categorie "standard"
4. Approvazione manuale per categorie "restricted" (alimentari, elettronica ad alto valore)
5. Timeline: 24-48 ore

### 2.4 Fase 4: Configurazione Spedizione (Days 15-30)

#### Step 4.1 - Metodi di Spedizione

**Opzione A: Seller Fulfillment (SF)**
```
Seller gestisce spedizione direttamente

Carrefour supporta:
- Corrieri integrati (POST, DHL, GLS, SDA, etc.)
- Tracking automatico
- Gestione resi da seller
- Tempi delivery: 24-48h (urban), 2-5 days (rural)

API Configurazione:
POST /api/sellers/{seller_id}/shipping_methods
{
  "type": "SELLER_FULFILLMENT",
  "carriers": [
    {
      "carrier_id": "poste_italiane",
      "service_code": "standard",
      "min_days": 1,
      "max_days": 3,
      "cost_type": "FIXED",
      "cost": "5.99",
      "free_threshold": "99.99"
    },
    {
      "carrier_id": "dhl",
      "service_code": "express",
      "min_days": 0,
      "max_days": 1,
      "cost_type": "PERCENTAGE",
      "cost_percentage": "10",
      "free_threshold": "150.00"
    }
  ]
}
```

**Opzione B: Fulfillment by Carrefour (FbC)**
```
Carrefour gestisce warehouse, picking, packing, spedizione

Requisiti:
- Invio stock fisico a warehouse Carrefour (Roma/Milano)
- Fee: 2.50 EUR per order + costi logistica
- Gestione resi centralizzata
- Tempi delivery: 24h metro areas, 48-72h other

Processo:
1. Seller crea SKU con shipping_type: "FULFILLMENT_BY_CARREFOUR"
2. Upload inventory tramite API
3. Carrefour genera shipping label
4. Seller spedisce merce a warehouse
5. Warehouse riceve, scansiona, mette a stock
6. Sistema automaticamente attiva SKU

Endpoint gestione inventory FbC:
PUT /api/sellers/{seller_id}/fcm/inventory
[
  {
    "sku": "SKU001",
    "quantity": 1000,
    "warehouse": "ROMA"
  }
]
```

---

## 3. OPERAZIONI MARKETPLACE POST-ONBOARDING

### 3.1 Gestione Ordini

#### Step 3.1.1 - Flusso Ordini
```
Customer Order → Carrefour → Seller
                   ↓
            Order Created Event
                   ↓
            Mirakl sends webhook
                   ↓
            Seller receives order
                   ↓
            Seller fulfills (SF) or Carrefour fulfills (FbC)
                   ↓
            Tracking update
                   ↓
            Customer receives
                   ↓
            Fulfillment complete
```

**API Order Retrieval:**
```
GET /api/orders?status=WAITING_ACCEPTANCE&limit=100&offset=0

Risposta Mirakl:
{
  "total_count": 1250,
  "data": [
    {
      "order_id": "1234567",
      "commercial_id": "ABC-123",
      "status": "WAITING_ACCEPTANCE",
      "order_date": "2026-04-01T10:30:00Z",
      "customer": {
        "email": "customer@example.com",
        "firstname": "Paolo",
        "lastname": "Rossi",
        "phone": "+39-XXXXXXXXXX"
      },
      "shipping_address": {
        "street1": "Via Roma 123",
        "city": "Milano",
        "state": "MI",
        "zipcode": "20100",
        "country": "IT"
      },
      "billing_address": { /* same as shipping */ },
      "order_lines": [
        {
          "order_line_id": "1234567-1",
          "sku": "SKU001",
          "title": "Prodotto Test",
          "quantity": 2,
          "unit_price": "29.99",
          "currency": "EUR",
          "shipping_price": "5.99",
          "shipping_type": "SELLER_FULFILLMENT",
          "shipping_method": "Standard",
          "commission": "3.29",
          "payout_amount": "56.68"
        }
      ],
      "total_price": "70.97",
      "total_commission": "7.80",
      "total_payout": "63.17"
    }
  ]
}
```

**SLA Accettazione Ordini:**
- Automatic acceptance after 15 minutes (configurable)
- OR Manual acceptance via API/Dashboard
- Rejection: possible but affects rating

#### Step 3.1.2 - Aggiornamento Stato Ordini

```
PUT /api/orders/{order_id}/status
{
  "order_status": "SHIPPED",
  "tracking_number": "123456789ABC",
  "carrier": "poste_italiane",
  "tracking_url": "https://poste.it/track/123456789ABC"
}

Stati possibili:
WAITING_ACCEPTANCE → ACCEPTED → SHIPPED → DELIVERED → CANCELLED → REFUNDED
```

### 3.2 Gestione Inventario

#### Step 3.2.1 - Real-time Inventory Updates

```
PUT /api/sellers/{seller_id}/products/{sku}/inventory
{
  "quantity": 45,
  "shop_sku": "SKU001"
}

Batch Update:
PUT /api/sellers/{seller_id}/inventory
[
  {"shop_sku": "SKU001", "quantity": 45},
  {"shop_sku": "SKU002", "quantity": 120},
  {"shop_sku": "SKU003", "quantity": 0}
]
```

**Best Practices:**
- Update every 1-6 hours depending on velocity
- Set minimum stock alerts
- Auto-deactivate when stock=0
- Carrefour has overcap tolerance: 5% (protects sellers from flash sales overstock)

#### Step 3.2.2 - Gestione Varianti Prodotto

```
Prodotto "T-shirt Rosso" con varianti:
POST /api/catalogs/{catalog_id}/products
{
  "sku": "TSHIRT-ROSSO",
  "title": "T-shirt Classica Rosso",
  "variants": [
    {
      "sku": "TSHIRT-ROSSO-S",
      "attributes": {"size": "S"},
      "quantity": 10,
      "price": "19.99"
    },
    {
      "sku": "TSHIRT-ROSSO-M",
      "attributes": {"size": "M"},
      "quantity": 25,
      "price": "19.99"
    },
    {
      "sku": "TSHIRT-ROSSO-L",
      "attributes": {"size": "L"},
      "quantity": 15,
      "price": "22.99"
    }
  ]
}
```

### 3.3 Pricing & Promotions

#### Step 3.3.1 - Dynamic Pricing

```
PUT /api/sellers/{seller_id}/products/{sku}/price
{
  "price": "24.99",
  "currency": "EUR",
  "origin_price": "29.99",
  "discount_start_date": "2026-04-01T00:00:00Z",
  "discount_end_date": "2026-04-30T23:59:59Z"
}

Carrefour calcola automaticamente:
- Prezzo lordo visibile
- Commissione su nuovo prezzo
- Payout al seller
```

#### Step 3.3.2 - Seller-promoted Offers

```
Carrefour permette seller di creare:
1. Flash Sales (24-72 ore, sconto fino a 50%)
2. Quantità Limitate (es. primi 100 acquirenti)
3. Bundle Offers (es. compra 2 prodotti, sconto 10%)

API Creazione Promozione:
POST /api/sellers/{seller_id}/promotions
{
  "type": "FLASH_SALE",
  "products": ["SKU001", "SKU002"],
  "discount_percentage": 25,
  "start_date": "2026-04-05T10:00:00Z",
  "end_date": "2026-04-06T10:00:00Z",
  "max_quantity": 500,
  "title": "Flash Sale Primavera"
}

Carrefour approva/rifiuta in base a:
- Storico seller compliance
- Prezzo origin_price ragionevole (no artificial inflation)
- Non conflitto con promozioni Carrefour
```

---

## 4. PAGAMENTI E SETTLEMENT

### 4.1 Struttura Commissioni Carrefour

**Base Commission:**
```
Categoria                | Base Commission | Note
-------------------------|-----------------|--------------------------------------
Abbigliamento           | 8%              | T-shirt, pantaloni, giacche
Scarpe                  | 10%             | Incluse scarpe sportive
Accessori               | 10%             | Borse, cinture, occhiali
Elettrodomestici       | 12%             | Grandi/piccoli, escluso food
Alimentari             | 5%              | FMCG, bevande, snack
Libri                  | 3%              | Esenzione IVA
Giochi e Giocattoli    | 15%             | Include articoli per infanzia
Sport e Outdoor        | 12%             | Articoli sportivi e camping
Bellezza e Cura        | 15%             | Cosmetici, igiene personale
Mobili                 | 12%             | Arredamento, incluso delivery setup
```

### 4.2 Payout Calculation Example

```
Scenario: Vendita T-shirt su Carrefour IT

Prezzo Cliente:                          29,99 EUR
Commissione Carrefour (8%):              -2,40 EUR
Costo Spedizione (seller fulfillment):   -5,99 EUR
Imposte (IVA 22%):                       -5,40 EUR (su prezzo+spedizione)
                                         ─────────
Payout al Seller:                        16,20 EUR

Note:
- IVA calcolata su subtotal (prezzo + spedizione)
- Seller è sempre responsabile per propria IVA out
- Carrefour riporta IVA Mirakl come collected
```

### 4.3 Settlement Schedule

**Stripe (Default):**
```
Giovedì: Carrefour finalize payout
Venerdì: Stripe elabora il trasferimento
Lunedì: Fondi arrivano in conto seller

Minimum payout: 10 EUR
Se saldo < 10 EUR: accumula al periodo seguente
```

**Bank Transfer (Optional):**
```
Ogni lunedì: Settlement calcolato
Mercoledì: Bonifico iniziato
Venerdì: Fondi arrivano (bancario standard)

Minimum payout: 25 EUR
Fee: 0 EUR (Carrefour/Mirakl copre)
```

### 4.4 Refund & Return Management

```
Cliente richiede reso (standard 30 giorni):

1. Customer requests return
   ↓
2. Seller approva/rifiuta entro 24h
   (Rifiuto richiede motivo valido - affects rating)
   ↓
3. Se approvato: Mirakl genera return label
   ↓
4. Customer spedisce merce indietro
   ↓
5. Seller riceve merce, verifica condizioni
   - Se OK: "REFUND_ACCEPTED"
   - Se non OK (danneggiata): "REFUND_DISPUTED"
   ↓
6. If REFUND_ACCEPTED:
   - Mirakl inverte transazione
   - Seller perde commissionI Carrefour
   - Customer riceve rimborso entro 5-7 giorni
   - Seller KPI: Return rate trackato

API:
PUT /api/orders/{order_id}/refund
{
  "status": "REFUND_ACCEPTED",
  "reason": "CUSTOMER_RETURN",
  "amount": "29.99"
}
```

---

## 5. METRICHE DI PERFORMANCE & KPI

### 5.1 Seller Rating & Scorecards

**Carrefour IT Seller Rating (1-5 stars):**
```
Metrica                     | Peso | Target per 5★
───────────────────────────|────────|─────────────────
Order Acceptance Rate       | 10%  | >99% (max 1 reject per 100)
On-time Shipping Rate       | 25%  | >98% (vs declared shipping time)
Product Quality Rating      | 25%  | >4.5★ (customer feedback)
Return Rate                 | 20%  | <2% (vs orders shipped)
Response Time (messages)    | 10%  | <8 hours average
Compliance Score            | 10%  | 100% (no violations)
                            |────────|─────────────────
Overall Performance Rating  |  100% | Minimo 4.2★
```

**Consequences of Low Ratings:**
```
4.0★ - 4.1★:  Warning email, improvement plan required
3.5★ - 3.9★:  Visibility reduced 50%, possible suspension
<3.5★:         Account suspension, possible termination
```

### 5.2 Seller Dashboard Analytics

```
Dashboard Carrefour IT fornisce:
1. Orders
   - Daily orders graph
   - Orders by status
   - Average order value
   - Conversion rate (visits → orders)

2. Revenue
   - Daily sales
   - Gross revenue vs net payout
   - Commission breakdown
   - Monthly trend

3. Products
   - Product views
   - Add to cart rate
   - Conversion by product
   - Stock status alerts

4. Performance
   - Seller rating trend
   - Response time average
   - Return rate %
   - Shipping on-time %

5. Customers
   - New vs repeat
   - Customer satisfaction comments
   - Review analysis
```

### 5.3 Seller Growth Programs

**Carrefour Italia incentivizes:**
```
1. Volume Growth
   - 100-500 orders/month: +2% commission boost
   - 500-2000 orders/month: +5% commission boost
   - >2000 orders/month: Custom partnership terms

2. Category Diversification
   - Sellers with 3+ categories: Priority featured placement
   - Exposure in category homepages
   - Featured in weekly newsletters

3. Quality Excellence
   - Rating >4.6★: Featured badge
   - Featured in "Top Sellers" section
   - Special promotional slots

4. New Seller Accelerator Program
   - First 3 months: 50% commission reduction
   - Featured placement for new products
   - Dedicated Carrefour onboarding support
```

---

## 6. INTEGRAZIONI TECNICHE & API

### 6.1 Authentication

**API Key Based (Raccomandato):**
```
Ogni seller riceve API key personalizzata:
  - Key pattern: "c_it_seller_[uuid]"
  - Header: Authorization: "Basic [base64(key:)]"
  - Scopes: products:read:write, orders:read, payments:read
  - Rate limit: 500 req/min per seller
  - Expiry: No expiry (revocable via dashboard)

Example Request:
```
curl -X GET https://carrefour-it.mirakl.com/api/orders \
  -H "Authorization: Basic Y19pdF9zZWxsZXJfYWJjZGVmOg==" \
  -H "Accept: application/json"
```

### 6.2 Webhook Events

**Available Events:**
```
order.created
order.accepted
order.shipped
order.delivered
order.cancelled
order.refunded
product.created
product.updated
product.deleted
inventory.updated
seller.suspended
promotion.approved
promotion.rejected
```

**Webhook Configuration:**
```
POST /api/webhooks/subscriptions
{
  "events": ["order.created", "order.shipped"],
  "endpoint": "https://seller-system.com/webhooks/carrefour",
  "active": true,
  "retry_policy": "exponential_backoff"
}

Mirakl Webhook Payload (Example - order.created):
{
  "event": "order.created",
  "timestamp": "2026-04-01T14:30:00Z",
  "data": {
    "order_id": "1234567",
    "seller_id": "s_abc123",
    /* full order object as per GET /api/orders/{id} */
  }
}
```

### 6.3 Error Handling

**HTTP Status Codes:**
```
200 OK                    - Richiesta riuscita
201 Created              - Risorsa creata
400 Bad Request          - Parametri non validi
401 Unauthorized         - API key mancante/invalida
403 Forbidden            - Permessi insufficienti
404 Not Found            - Risorsa non esiste
409 Conflict             - Conflitto (es. duplicate SKU)
429 Too Many Requests    - Rate limit exceeded
500 Server Error         - Errore server Mirakl
502 Bad Gateway          - Problema infrastruttura
503 Service Unavailable  - Manutenzione
```

**Error Response Format:**
```json
{
  "error_code": "INVALID_PRODUCT_SKU",
  "error_message": "SKU must be unique per seller",
  "error_details": {
    "sku": "SKU001",
    "existing_seller": "s_xyz789"
  },
  "request_id": "req_abc123"
}
```

### 6.4 Pagination & Limits

```
GET /api/orders?limit=50&offset=100

Response Headers:
X-Total-Count: 1250
X-Total-Pages: 25
X-Current-Page: 3

Max Limit: 500 per request
Default Limit: 20
```

---

## 7. COMPLIANCE & REGULATORY

### 7.1 Forbidden Products

**Carrefour IT strictly prohibits:**
```
- Contraffatti (counterfeit goods)
- Articoli illegali (droga, armi, esplosivi)
- Materiale sessuale esplicito
- Articoli che violano copyright/trademark
- Prodotti ritirati dal mercato (recalls)
- Medicinali senza ricetta
- Alcol a minori (verificato via age gate)
- Tabacco
- Elettronica rubata / IMEI blacklist
- Used items dichiarati come NEW
```

**Enforcement:**
- Automated scanning: Descrizione + immagini
- Manual review: Random sampling
- Consequences: Product removal, fee, potential suspension

### 7.2 GDPR & Data Protection

```
Seller accesso a customer data (limitato):
- Nome, indirizzo (per spedizione)
- Email (per comunicazioni ordine)
- Phone number (opzionale)

NON accesso:
- Payment method details
- Browsing history
- Customer profile data

Seller deve:
- Comply with GDPR Article 6 (lawful basis: contract)
- Non usare dati per scopi di marketing senza consenso
- Implement data retention limits (6 mesi post-ordine)
- Report data breaches entro 72 ore
```

### 7.3 Consumer Rights

**Standard EU Returns:**
- 14 giorni diritto di recesso (senza motivo)
- Seller paga spedizione ritorno
- Rimborso entro 14 giorni da ricevimento
- Eccezioni: Food, custom items, audio/video sealed

**Carrefour Enhancement:**
- 30 giorni per Carrefour IT (più generoso)
- Carrefour copre costi ritorno per spedizioni loro

---

## 8. GUIDA PRATICA: SETUP RAPIDO (24-48 ORE)

### 8.1 Checklist Onboarding Veloce

```
Giorni 1-2:
☐ Registrazione seller (15 min)
☐ Caricamento documenti KYC (30 min)
☐ Setup informazioni negozio (30 min)
☐ Connessione metodo pagamento (15 min)
☐ Creazione 10 prodotti test (60 min)

Giorni 3-5:
☐ Caricamento catalogo full via CSV (120 min)
☐ Setup metodi spedizione (30 min)
☐ Configurazione API (se integrazione) (120 min)
☐ Test ordini (60 min)
☐ Training team internal (90 min)

Giorni 6+:
☐ Attivazione categoria secondary (se richiesto) (24-48h waiting)
☐ Ottimizzazione prezzi/promozioni
☐ Monitoring performance KPI
☐ Scaling inventory
```

### 8.2 Contatti Support

```
Carrefour IT Seller Support
- Email: sellers-it@carrefour.com
- Phone: +39-02-XXXXX-XXXXX
- Ticketing: https://carrefour-it.mirakl.com/support
- Response SLA: 24 ore (business days)
- Chat support: Solo per technical issues

Mirakl Direct Support (Technical):
- Email: support@mirakl.com
- Status page: https://mirakl-status.io
- Incident hotline: +33-1-XXXX-XXXX
```

---

## 9. SCENARIO DI SUCCESSO: CASE STUDY

### Vendiamonoi.it su Carrefour Italia (Proiezione)

```
Mese 1 (Maggio 2026):
- 50 prodotti listati
- 15-30 ordini/giorno
- GMV stimato: €3,000-5,000
- Payout netto: €2,100-3,500
- Seller rating: 4.4★ (first reviews)

Mese 3 (Luglio 2026):
- 500+ prodotti listati
- 150-250 ordini/giorno
- GMV stimato: €45,000-75,000
- Payout netto: €30,000-50,000
- Seller rating: 4.6★ (positive trend)
- 2+ categorie attive

Mese 6 (Ottobre 2026):
- 2,000+ prodotti listati
- 600+ ordini/giorno
- GMV stimato: €200,000-300,000
- Payout netto: €130,000-200,000
- Seller rating: 4.7★+ (premium status)
- 4+ categorie, Featured seller badge
- Partnership discussions con Carrefour category managers
```

---

## 10. ROADMAP 2026-2027

### Carrefour Marketplace Evolution

```
2026 Q2:
- Launch "Quick Commerce" tier (2-hour delivery)
- B2B seller program (volume discounts)
- Live chat support integration
- Multi-currency seller expansion (ES/FR/BE markets)

2026 Q3:
- AI-powered inventory recommendations
- Subscriptions/recurring orders
- Enhanced seller analytics dashboard
- Marketplace API v2 (GraphQL)

2026 Q4:
- Seller financing program (advanced payouts)
- Cross-marketplace sync tools
- Video content support for product pages
- Holiday season promotional tools

2027 Q1-Q2:
- Expansion to Germany, Netherlands, Austria
- Seller advertising platform (paid placements)
- Sustainability tracking/ESG badges
- Advanced demand forecasting tools
```

---

## KPI success targets:
- Year 1: €500k-1M cumulative GMV
- Year 2: €2-5M GMV
- Acceptance rate: >95% per paese
- Return rate: <2%
- Customer satisfaction: >4.2★

---

**FINE DOCUMENTO**

Versione: 2.1 | Data: Aprile 2026 | Preparato per: Vendiamonoi.it | Target: CTOs, System Architects