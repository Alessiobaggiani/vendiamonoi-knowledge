# Specifica Tecnica Marketplace Leroy Merlin
## Documento di Architettura per Operatori Multi-Marketplace

**Versione:** 2.1
**Data:** Aprile 2026
**Target Audience:** CTO, System Architects, Marketplace Operators
**Operatore:** Vendiamonoi.it - Digital Distribution

---

## 1. Informazioni Generali

| Parametro | Valore |
|-----------|--------|
| **Marketplace Ufficiale** | Leroy Merlin Marketplace (marketplace.leroymerlin.com) |
| **Genitore Aziendale** | Adeo Group |
| **Paesi Operativi** | Francia, Italia, Spagna, Polonia, Portogallo, Germania, Belgio |
| **Piattaforma Tecnica** | Mirakl SaaS |
| **Modello API** | Mirakl Shop API v2+ |
| **Categorie Principali** | Bricolage, Edilizia, Bagno, Giardino, Illuminazione, Utensili, Ferramenta |
| **Settore** | Home Improvement & DIY (fai-da-te) |
| **Currency** | EUR (per tutti i paesi) |
| **Lingua Negozio** | Italiano (IT), Francese (FR), Spagnolo (ES), Polacco (PL) |
| **Integrazione Primaria** | Mirakl Shop API, ChannelEngine, Channable |

---

## 2. Modello di Business

### 2.1 Struttura Adeo Group
Leroy Merlin appartiene ad Adeo Group, holding multinazionale specializzato nel retail home improvement. La strategia marketplace rientra nell'espansione dell'ecosistema digitale B2B2C per fornitori di materiali edili e articoli per la casa.

### 2.2 Marketplace vs. Retail Tradizionale
- **Leroy Merlin Retail:** Negozi fisici, gestione diretta inventario, brand proprietario
- **Leroy Merlin Marketplace:** Piattaforma multi-vendor su tecnologia Mirakl, venditori terzi abilitati, catalogo esteso
- **Vantaggi per Venditori:** Accesso a customer base Leroy Merlin (milioni di utenti), logistica integrata, brand trust

### 2.3 Categorie Prodotto Principali
1. **Bricolage e Fai-da-Te** (12-15% commissione)
   - Attrezzi manuali, utensili elettrici, accessori
   - Vernici, solventi, prodotti chimici (richiede certificazione)
   - Materiali di consumo

2. **Edilizia e Costruzione** (12% commissione)
   - Materiali da costruzione, utensili professionali
   - Cemento, laterizi, malte (logistica specializzata)
   - Strutture metalliche, carpenteria

3. **Bagno e Cucina** (14-18% commissione)
   - Sanitari, rubinetteria, accessori
   - Arredamento bagno e cucina (alto valore)
   - Complementi d'arredo

4. **Giardino e Esterno** (13% commissione)
   - Piante, terricci, fertilizzanti
   - Attrezzi da giardino, macchinari
   - Arredamento esterno, sistemi di irrigazione

5. **Illuminazione** (15% commissione)
   - Lampade, faretti, sorgenti luminose LED
   - Apparecchiature di illuminazione smart

### 2.4 Commissioni e Fee Structure

| Categoria | Commissione | Costo Attivazione | Fee Logistica |
|-----------|------------|-------------------|---------------|
| Bricolage Standard | 12-15% | EUR 500 | Inclusa Mirakl |
| Edilizia | 12% | EUR 750 | +2-3% |
| Bagno/Cucina | 14-18% | EUR 600 | Inclusa Mirakl |
| Giardino | 13% | EUR 400 | Inclusa Mirakl |
| Illuminazione | 15% | EUR 500 | Inclusa Mirakl |
| Categoria Ristretta* | +5% | EUR 1000+ | +3% |

*Categorie ristrette richiedono certificazioni aggiuntive (chimici pericolosi, articoli regolamentati)

---

## 3. Architettura Tecnica

### 3.1 Stack Tecnologico

```
┌─────────────────────────────────────────────────────┐
│ Frontend: Leroy Merlin Web/Mobile Platform          │
│ (React, Vue.js) - Cliente Storeffront               │
└──────────────────────┬────────────────────────────┘
                       │ REST/GraphQL
┌──────────────────────v────────────────────────────┐
│ Mirakl Platform (SaaS)                              │
│ - Marketplace Core Engine                           │
│ - Multi-vendor Catalog Management                   │
│ - Order Management System (OMS)                      │
│ - Fulfillment & Logistics                           │
└──────────────────────┬────────────────────────────┘
                       │ API Gateway
        ┌──────────────┼──────────────┐
        │              │              │
        v              v              v
   ChannelEngine   Channable    Custom Integrations
   (Sync)          (Sync)       (Hooks/Webhooks)
        │              │              │
        └──────────────┼──────────────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        v              v              v
   ERP/WMS        Payment Gateway    Email/SMS
   (Vendor)       (PayPal, Stripe)   (Notifications)
```

### 3.2 API Endpoints Principali

#### Mirakl Shop API v2 (Autenticazione OAuth2)

**Base URL:** `https://api.mirakl.com/` (per istanza Leroy Merlin)

##### Product Catalog Management

```
GET    /mmp/shops/{shop_id}/products
POST   /mmp/shops/{shop_id}/products
PUT    /mmp/shops/{shop_id}/products/{product_sku}
DELETE /mmp/shops/{shop_id}/products/{product_sku}
PATCH  /mmp/shops/{shop_id}/products/{product_sku}
```

**Request Example (Product Creation):**
```json
{
  "sku": "VENDOR-SKU-001",
  "title": "Trapano Battente 13mm 800W",
  "description": "Trapano battente professionale con...",
  "category_code": "UTENSILI_ELETTRICI",
  "price": 125.99,
  "currency": "EUR",
  "quantity": 150,
  "attribute_values": {
    "brand": "Bosch",
    "power": "800W",
    "colore": "Nero"
  },
  "shop_sku": null,
  "shop_id": 12345
}
```

##### Order Management

```
GET    /mmp/shops/{shop_id}/orders
GET    /mmp/shops/{shop_id}/orders/{order_id}
PUT    /mmp/shops/{shop_id}/orders/{order_id}/status
POST   /mmp/shops/{shop_id}/shipments
PATCH  /mmp/shops/{shop_id}/shipments/{shipment_id}
```

**Order Status Flow:**
```
PENDING → ACCEPTED → SHIPPED → DELIVERED → CLOSED
                   ↓
              REJECTED/CANCELED
```

##### Inventory & Stock Management

```
PUT    /mmp/shops/{shop_id}/inventories
GET    /mmp/shops/{shop_id}/inventories
POST   /mmp/shops/{shop_id}/stock-levels
```

**Batch Inventory Update:**
```json
{
  "shop_id": "12345",
  "inventories": [
    {
      "sku": "VENDOR-SKU-001",
      "quantity": 150,
      "leadtime_to_ship": 2
    },
    {
      "sku": "VENDOR-SKU-002",
      "quantity": 0,
      "date_deactivated": "2026-04-01T10:00:00Z"
    }
  ]
}
```

#### Webhooks / Event-Driven Integration

**Webhooks Disponibili:**

| Evento | Payload | Frequenza |
|--------|---------|----------|
| `order.created` | Order ID, Status, Items | Real-time |
| `order.accepted` | Order ID, Vendor Ref | Real-time |
| `order.shipped` | Shipment ID, Tracking | Real-time |
| `order.canceled` | Order ID, Reason | Real-time |
| `product.rejected` | Product SKU, Errors | Real-time |
| `inventory.updated` | SKU, Qty Change | Real-time |
| `refund.requested` | Refund ID, Amount | Real-time |

**Webhook Signature Verification (HMAC-SHA256):**
```
Header: X-Mirakl-Signature
Format: sha256=base64(hmac_sha256(webhook_secret, body))
```

### 3.3 Flussi Dati Principali

#### A. Onboarding Vendor
```
1. Vendor Registration (Self-service portal)
2. Bank Account Verification (Stripe Connect)
3. Tax ID / VAT Validation
4. Category Application & Approval
5. API Credentials Generation (Shop ID + API Key)
6. First Product Import
7. Account Activation
```

#### B. Daily Catalog Sync (ChannelEngine/Channable)
```
1. Vendor → ERP/WMS: Product data export (JSON/XML)
2. ERP → ChannelEngine API: Batch product sync
3. ChannelEngine → Mirakl API: Product mapping & sync
4. Mirakl → Leroy Merlin Storefront: Catalog indexing
5. Daily Inventory Level Update (Pull-based, 4x/day)
6. Real-time Stock Alert (Webhooks, if <10 units)
```

#### C. Order Processing
```
1. Customer Order → Mirakl OMS
2. Mirakl → Vendor (Webhook: order.created)
3. Vendor Picks & Packs
4. Vendor → Mirakl API: Shipment creation (with tracking)
5. Mirakl → Customer: Shipping notification
6. Tracking Poll (Carrier API integration)
7. Delivery Confirmation → Mirakl
8. Auto-closure (3-5 days post-delivery)
```

#### D. Payment & Settlement
```
Weekly Settlement Cycle:
1. Orders shipped + delivered in previous week
2. Mirakl calculates net amount (Revenue - Commission - Returns)
3. Stripe Connect: Payment processing
4. Bank transfer to vendor (3-5 business days)
5. Settlement report generation (CSV export)
```

### 3.4 Authentication & Security

**OAuth2 Flow (Recommended):**
```
1. Vendor redirects to Mirakl Auth endpoint
2. User grants permission (Consent Screen)
3. Mirakl returns Authorization Code
4. Vendor backend exchanges code for Access Token
5. API requests include: Authorization: Bearer {token}
6. Token refresh before expiry (typically 1 hour)
```

**API Key Authentication (Legacy):**
```
Header: Authorization: ApiKey {api_key}
```

**Required Headers:**
```
Accept: application/json
Content-Type: application/json
User-Agent: VendorName/1.0 (contact@vendorname.com)
X-Mirakl-Api-Version: 2.0
```

**Rate Limiting:**
- Standard: 100 requests/second per vendor
- Burst: 500 req/sec (30 sec window)
- Batch operations: 1000 items/request max
- Response header: `X-RateLimit-Remaining`

---

## 4. Specifiche Prodotto & Catalogo

### 4.1 Attributi Prodotto Obbligatori

| Attributo | Tipo | Lunghezza | Validazione |
|-----------|------|-----------|----------|
| SKU | String | 1-50 | Unique per vendor |
| Titolo | String | 10-200 | No HTML tags |
| Descrizione | Text | 50-5000 | Unicode UTF-8 |
| Prezzo | Decimal | - | >= 0.01 EUR |
| Categoria | Enum | - | Mirakl taxonomy |
| Immagine | URL | - | Min 800x800px, <5MB |
| Disponibilità | Integer | - | >= 0 |
| Lead Time | Integer | 1-365 | Giorni |
| Brand | String | 1-100 | Name validation |

### 4.2 Categorie Mirakl Leroy Merlin

```
BRICOLAGE_FAI_DA_TE
├── UTENSILI_MANUALI
│   ├── CACCIAVITI
│   ├── MARTELLI
│   └── CHIAVI
├── UTENSILI_ELETTRICI
│   ├── TRAPANI
│   ├── SMERIGLIATRICI
│   └── SEGHE_CIRCOLARI
├── VERNICI_SOLVENTI
│   ├── PITTURE_MURALI
│   ├── SMALTI
│   └── DILUENTI (RESTRICTED)
└── MATERIALI_CONSUMO
    ├── CARTA_VETRATA
    ├── NASTRI_ADESIVI
    └── GUANTI_PROTEZIONE

EDILIZIA_COSTRUZIONE
├── MATERIALI_STRUTTURALI
│   ├── CEMENTO
│   ├── LATERIZI
│   └── LEGNAME
├── UTENSILI_PROFESSIONALI
├── SISTEMI_ISOLAMENTO
└── IMPIANTI (ELECTRICAL/IDRICO)

BAGNO_CUCINA
├── SANITARI
│   ├── WATER_CLOSET
│   ├── LAVABI
│   └── VASCHE
├── RUBINETTERIA
├── ARREDAMENTO_BAGNO
└── COMPLEMENTI_CUCINA

GIARDINO_ESTERNO
├── PIANTE_TERRICCI
├── ATTREZZI_GIARDINO
├── MACCHINARI
│   ├── TAGLIAERBA
│   ├── MOTOSEGHE
│   └── DECESPUGLIATORI
├── ARREDAMENTO_ESTERNO
└── SISTEMI_IRRIGAZIONE

ILLUMINAZIONE
├── LAMPADE
├── FARETTI
├── APPARECCHIATURE_LED
└── SMART_LIGHTING
```

### 4.3 Attributi Specifici per Categoria

**UTENSILI_ELETTRICI:**
- Potenza (Watt)
- Voltaggio (V)
- Tipo Batteria (Li-Ion, Ni-Cd)
- Velocità (RPM/min)
- Capacità Mandrino (mm)

**SANITARI:**
- Materiale (Ceramica, Acrilico)
- Dimensioni (Lunghezza x Larghezza x Altezza)
- Colore
- Finitura (Lucida, Satinata)
- Capacità (Litri)

**VERNICI (RESTRICTED):**
- Tipo (Acrilica, Alchidica, Epoxi)
- VOC Content (g/L) - Compliance REACH
- Resa (m²/L)
- Tenore Solidi (%) - per Etichettatura
- Simboli Pericolosità (GHS) - OBBLIGATORIO

### 4.4 Immagini Prodotto

**Requisiti:**
- **Formato:** JPG, PNG (no WEBP per backward compatibility)
- **Dimensioni Minime:** 800x800 px (per zoom)
- **Dimensioni Consigliate:** 1200x1200 px (responsive)
- **Dimensioni File:** Max 5 MB
- **Colore Sfondo:** Bianco (RGB 255,255,255) per consistenza storefront
- **Numero Immagini:** 1-10 per prodotto (prima è thumbnail)
- **Alt Text:** Obbligatorio (max 200 char) per SEO

**Politica di Approvazione:**
- Immagini esaminate da team QA Leroy Merlin
- Rigetto per: marchi competitors, watermark, persone, testo non conforme
- Timeline: 24-48 ore lavorative

---

## 5. Ordini e Fulfillment

### 5.1 Order Lifecycle

```
STATUS FLOW DETTAGLIATO:

┌─ PENDING (Ordine creato, in attesa accettazione vendor)
│  ├─→ ACCEPTED (Vendor ha accettato)
│  │   └─→ SHIPPED (Tracking number fornito)
│  │       └─→ DELIVERED (Consegna confermata)
│  │           └─→ CLOSED (Auto-chiuso dopo 5gg)
│  │
│  ├─→ REJECTED (Vendor rifiuta, out of stock)
│  │   └─→ CLOSED
│  │
│  └─→ PENDING_REFUND (Cliente richiede reso)
│      ├─→ REFUND_ACCEPTED (Vendor accetta reso)
│      │   └─→ REFUND_COMPLETED (Merci restituite)
│      │       └─→ CLOSED
│      │
│      └─→ REFUND_DENIED (Vendor nega reso)
│          └─→ CLOSED
```

### 5.2 Webhook: Order Lifecycle

**Event 1: order.created**
```json
{
  "timestamp": "2026-04-01T14:30:00Z",
  "event_type": "order.created",
  "order_id": "LM-2026-001234",
  "shop_id": "12345",
  "customer": {
    "first_name": "Mario",
    "last_name": "Rossi",
    "email": "mario.rossi@email.com"
  },
  "items": [
    {
      "order_line_id": "1",
      "product_sku": "VENDOR-SKU-001",
      "title": "Trapano Battente 13mm",
      "quantity": 1,
      "price": 125.99,
      "currency": "EUR"
    }
  ],
  "shipping_address": {
    "address_line1": "Via Roma 123",
    "city": "Milano",
    "postal_code": "20100",
    "country": "IT"
  },
  "order_total": 145.99,
  "shipping_type": "STANDARD",
  "estimated_delivery": "2026-04-05"
}
```

**Event 2: order.accepted (Vendor action)**
```json
{
  "timestamp": "2026-04-01T15:00:00Z",
  "event_type": "order.accepted",
  "order_id": "LM-2026-001234",
  "shop_id": "12345",
  "vendor_reference": "INT-2026-4567"
}
```

**Event 3: order.shipped (Vendor action)**
```json
{
  "timestamp": "2026-04-02T10:30:00Z",
  "event_type": "order.shipped",
  "order_id": "LM-2026-001234",
  "shop_id": "12345",
  "shipments": [
    {
      "shipment_id": "SHIP-001",
      "carrier": "DHL",
      "tracking_number": "1Z999AA10123456784",
      "estimated_delivery_date": "2026-04-05",
      "items": [
        {
          "order_line_id": "1",
          "product_sku": "VENDOR-SKU-001",
          "quantity_shipped": 1
        }
      ]
    }
  ]
}
```

### 5.3 Return & Refund Policy

**Return Window:**
- Standard: 30 giorni dalla consegna
- Categorie specifiche (Illuminazione, Sanitari): 14 giorni
- Articoli personalizzati: No return

**Refund Timeline:**
```
Day 0: Cliente richiede reso
Day 1-2: Vendor esamina e accetta/nega
Day 3-7: Cliente spedisce indietro (porta franchigia)
Day 7-14: Vendor riceve e valida
Day 14-21: Mirakl processa rimborso via Stripe Connect
Day 21+: Denaro disponibile in conto vendor
```

**Return Label:**
- Generato automaticamente da Mirakl
- DHL/GLS (selezionato da Leroy Merlin)
- Francchigia coperta da Mirakl per "restituzioni legittime"

---

## 6. Requisiti Logistici

### 6.1 Fulfillment Models

#### A. Vendor Fulfilled (Ship from Vendor)
- **Scenario:** Vendor gestisce inventory e spedizione
- **Lead Time:** 1-5 giorni (configurabile)
- **Costi:** Vendor paga spedizione
- **Supporto:** Vendor responsible per tracking & claims
- **Best For:** Grandi fornitori, stock elevato

#### B. MFN (Mirakl Fulfillment Network)
- **Scenario:** Vendor spedisce a hub Mirakl, Mirakl distribuisce
- **Lead Time:** +1 giorno (preparazione hub)
- **Costi:** Commissione Mirakl 5-8% su ordine
- **Supporto:** Mirakl gestisce logistica
- **Best For:** Piccoli vendor, drop-shipping
- **Nota:** Non disponibile per categorie pesanti (edilizia)

#### C. Click&Collect (Ritiro in Negozio)
- **Scenario:** Cliente ritira in store Leroy Merlin
- **Lead Time:** 2 giorni (preparazione in negozio)
- **Costi:** Commissione 3% su ordine
- **Coverage:** 80+ store Italia
- **Best For:** Prodotti leggeri, volumi bassi

### 6.2 Shipping Carriers

**Italia:**
- **DHL Express:** 1-2 giorni, parcelle <30kg
- **GLS:** 2-3 giorni, buon rapporto prezzo
- **SDA/Poste:** Economico ma lento (3-5gg)
- **Corriere Specializzato:** Edilizia/pesanti (Urgoto, Fercam)

**Shipping Rates (Leroy Merlin Approved):**

| Weight Range | DHL | GLS | SDA | Specializzato |
|-------------|-----|-----|-----|---------------|
| 0-5 kg | EUR 8-12 | EUR 6-10 | EUR 5-8 | N/A |
| 5-20 kg | EUR 12-18 | EUR 10-15 | EUR 8-12 | N/A |
| 20-50 kg | EUR 18-25 | EUR 15-22 | EUR 12-18 | EUR 20-30 |
| 50+ kg | N/A | N/A | N/A | EUR 30-60 |

### 6.3 Shipping Address Restrictions

**Aree Coperte:**
- Italia continentale (CAP 00000-99999)
- San Marino, Città del Vaticano

**Aree Escluse (No Shipping):**
- Isole (Sardegna, Sicilia): +3-5 giorni, +EUR 15-30
- Arcipelaghi minori: Case-by-case
- Zone di confine (Switzerland, Francia): Verificare

### 6.4 Inventory Management

**Real-Time Stock Sync:**

```
Vendor ERP → ChannelEngine API:
{
  "sku": "VENDOR-SKU-001",
  "quantity": 150,
  "leadtime_to_ship": 2,
  "restock_date": "2026-04-10T00:00:00Z"
}

ChannelEngine → Mirakl API (ogni 30 minuti):
Batch update max 10,000 SKU/request

Mirakl → Leroy Merlin Storefront (istantaneo)
Aggiornamento "Available Now" badge
```

**Low Stock Alert:**
- Quantity < 10 units: Webhook `inventory.alert`
- Email notification a vendor
- "Only X left" message su storefront

**Out of Stock Handling:**
```
1. Vendor imposta quantity = 0
2. Mirakl auto-disabilita prodotto (24h delay)
3. Prodotto nascosto da search/catalog
4. Ordini existing: Webhook order.rejected
5. Restock date: Prodotto ritorna disponibile
```

---

## 7. Commissioni e Pagamenti

### 7.1 Commission Structure Dettagliata

**Base Calculation:**
```
Order Value = (Quantity × Unit Price) + Shipping Fee
Commission Amount = Order Value × Commission %
Net Payout = Order Value - Commission - Returns Adjustment
```

**Commissioni per Categoria:**

| Categoria | % | Note |
|-----------|---|------|
| Bricolage Standard | 12-15% | Varia per sub-categoria |
| Edilizia Pesante | 12% | Min €50 ordine |
| Bagno Premium | 18% | Sanitari design, rubinetteria lusso |
| Giardino | 13% | Piante/terricci: 13%, Macchinari: 15% |
| Illuminazione Smart | 15-18% | Smart home: +3% premium |
| Articoli Ristretti | Base + 5% | Chimici, articoli regolamentati |
| Volumetric Pricing | -2% | >100 ordini/mese |

**Volume Discounts (Negoziabili):**
```
If Monthly Revenue > EUR 10,000: -0.5% commission
If Monthly Revenue > EUR 50,000: -1.0% commission
If Monthly Revenue > EUR 100,000: -1.5% commission (max)
```

### 7.2 Settlement Process

**Weekly Settlement Cycle (Every Monday):**

```
Week: Mon 01 Apr - Sun 07 Apr

1. Sunday 23:59 UTC: Period closes
2. Monday 00:00: Mirakl generates settlement report
3. Monday 06:00: Calculations validated
   - Orders shipped & delivered in period
   - Returns processed (full refund deductions)
   - Chargebacks/disputes (if any)
4. Monday 12:00: Payment initiated via Stripe Connect
5. Tuesday-Thursday: Bank transfer (3-5 days)
6. Settlement report available in vendor dashboard (CSV, PDF)
```

**Example Settlement Report:**

```
Period: Week 14 (Mar 31 - Apr 06, 2026)
Vendor Shop ID: 12345
Vendor Name: TechTools Italia SRL

REVENUE SUMMARY:
  Gross Revenue: EUR 12,450.50
  - Orders: 98
  - Avg Order Value: EUR 127.05
  - Returns/Refunds: -EUR 250.00

COMMISSIONS & FEES:
  Commission (12%): -EUR 1,494.06
  Returns Adjustment: +EUR 30.00
  Logistics Fee (included): EUR 0.00
  API Usage Fee: EUR 0.00

NET PAYOUT:
  Amount Due: EUR 10,736.44
  Payment Method: Stripe Connect → ING Bank
  Reference: LM-12345-W14-2026

TAX INFO:
  Gross Payout (before VAT): EUR 10,736.44
  Note: VAT handled separately (invoice system)
```

### 7.3 Payment Methods & Payouts

**Supported Payout Methods:**

1. **Bank Transfer (SEPA)**
   - Available for: EU vendors with IBAN
   - Timeline: 3-5 business days
   - Cost: EUR 0 (absorbed by Leroy Merlin)
   - Minimum: EUR 50

2. **Stripe Connect**
   - Integrated payment processor
   - Supports: Debit card top-up (if insufficient balance)
   - Timeline: 2-3 days (Stripe → bank)

3. **PayPal (Legacy)**
   - Deprecated (migrate to SEPA by Q4 2026)
   - Fee: EUR 0.35 per transfer + 1.5%

**Multi-Currency Support:**
- Base: EUR (all settlements)
- Vendor requests conversion to other currency at own expense
- No currency hedging provided

---

## 8. Compliance & Regulamenti

### 8.1 Data Protection (GDPR)

**Data Processing Agreement (DPA):**
- Signed during vendor onboarding
- Mirakl acts as Data Processor
- Vendor = Data Controller
- Personal data: Customer name, email, shipping address (for orders only)

**Data Retention Policy:**
- Order data: 7 years (Italian tax law)
- Customer info: Deleted after order completion + 180 days
- Logs/Webhooks: 30 days

**Customer Privacy Rights:**
- Vendor must honor "right to deletion" requests
- Leroy Merlin escalates to vendor
- Vendor response deadline: 5 business days

### 8.2 Product Safety Compliance

**Categories Requiring Certification:**

1. **Vernici e Solventi (Pericolosi)**
   - **Requirement:** REACH Certification, Safety Data Sheet (SDS)
   - **Standards:** EN 71 (toy paints), ISO 6603 (chemical safety)
   - **Documentation:** Scarico electronically, reviewed by Leroy Merlin
   - **Non-compliance:** Product rejection, account suspension risk

2. **Utensili Elettrici**
   - **Requirement:** CE Marking, EU Declaration of Conformity (DoC)
   - **Standards:** EN 60745 (portable power tools)
   - **Testing:** TÜV, Intertek, or equivalent notified body

3. **Sanitari**
   - **Requirement:** EN 997 (WC seats), EN 998 (sanitary ceramics)
   - **Standards:** Water pressure tolerance, material durability
   - **Documentation:** Factory inspection certificates

4. **Apparecchiature Elettriche**
   - **Requirement:** CE Mark, Low Voltage Directive (LVD)
   - **Standards:** EN 60950, IEC 61010
   - **EMC Testing:** Electromagnetic Compatibility

**Restricted Products (Pre-Approval Required):**
- Chemicalos (paint, solvents, pesticides): File SDS + labeling doc
- Electrical equipment (>1kW): CE cert + technical file
- Gas products: NOT ALLOWED on marketplace
- Biohazardous materials: Prohibited

### 8.3 VAT & Tax Compliance

**VAT Treatment:**
- **Standard Rate (Italy):** 22%
- **Reduced Rate:** 4% (certain building materials)
- **Reverse Charge:** Applied for B2B transactions (if registered)

**Vendor Responsibilities:**
1. Register for VAT if EU-based and >EUR 40,000 annual revenue
2. Provide VAT ID number during onboarding
3. Submit VAT returns quarterly (Italian Authority)
4. Leroy Merlin withholds 4% (advance tax, reconciled annually)

**Invoice Requirements:**
- Auto-generated by Mirakl (vendor dashboard)
- Must include: VAT ID, item breakdown, payment terms
- Retention: Vendor must keep for 6 years

### 8.4 Consumer Rights & Warranty

**Statutory Warranty (Italy):**
- **Duration:** 24 months from delivery (EU law)
- **Vendor Obligation:** Repair or replace defective products
- **Exception:** Misuse, normal wear
- **Cost:** Vendor pays (Leroy Merlin doesn't subsidize)

**Warranty Claim Process:**
```
1. Customer files claim in Mirakl dashboard
2. Leroy Merlin escalates to vendor (72-hour SLA)
3. Vendor must respond within 7 days
4. Options: Repair (on-site or return), Replace, Refund
5. If vendor non-responsive: Mirakl initiates chargeback
```

---

## 9. Technical Integration Guides

### 9.1 Quickstart: OAuth2 Flow

**Step 1: Register Application**

Contact Leroy Merlin Integration Team:
```
Email: partners-integration@leroymerlin.fr
Application Name: "YourVendor SYN"
Callback URL: "https://yourdomain.com/auth/callback"
Required Scopes: "catalog:write inventory:write orders:read refunds:read"
```

**Step 2: Request Authorization**

```
GET https://auth.mirakl.com/oauth2/authorize?
  client_id=YOUR_CLIENT_ID&
  redirect_uri=https://yourdomain.com/auth/callback&
  response_type=code&
  scope=catalog:write+inventory:write+orders:read&
  state=RANDOM_STATE_TOKEN
```

**Step 3: Exchange Code for Token**

```bash
curl -X POST https://auth.mirakl.com/oauth2/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorization_code" \
  -d "code=AUTH_CODE_FROM_CALLBACK" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET" \
  -d "redirect_uri=https://yourdomain.com/auth/callback"
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "refresh_token_value"
}
```

**Step 4: Use Access Token**

```bash
curl -X GET https://api.mirakl.com/mmp/shops/12345/products \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Accept: application/json"
```

### 9.2 Product Sync Implementation (ChannelEngine)

**Setup:**

1. Register ChannelEngine account
2. Connect Leroy Merlin channel (via API key)
3. Map categories (your ERP categories → Leroy Merlin categories)

**Configuration JSON (ChannelEngine):**

```json
{
  "channel": "leroy-merlin-it",
  "enabled": true,
  "settings": {
    "auto_sync_enabled": true,
    "sync_interval_minutes": 30,
    "product_filter": {
      "status": "active",
      "inventory_gt": 0
    },
    "category_mapping": {
      "STRUMENTI_MANUALI": "UTENSILI_MANUALI",
      "TRAPANI_ELETTRICI": "UTENSILI_ELETTRICI",
      "PITTURE": "VERNICI_SOLVENTI"
    },
    "attribute_mapping": {
      "our_brand_field": "brand",
      "our_color_field": "colore",
      "our_power_field": "power"
    },
    "pricing_rules": {
      "margin_percentage": 25,
      "currency": "EUR",
      "rounding": "up"
    },
    "image_settings": {
      "max_images": 10,
      "min_width_px": 800,
      "accepted_formats": ["jpg", "png"]
    }
  }
}
```

**Product Mapping (Sample):**

```
Your ERP:
  ID: PROD-001
  Name: Professional Drill 13mm 800W
  Category: POWER_TOOLS
  Price: 99.99 EUR
  Stock: 150
  Brand: Bosch
  Power: 800W
  Images: [img1.jpg, img2.jpg]

ChannelEngine Transform:
  SKU: VENDOR-SKU-001
  Title: Trapano Professionale 13mm 800W Bosch
  Description: (auto-generated or template)
  Category: UTENSILI_ELETTRICI
  Price: 124.99 EUR (99.99 + 25% margin)
  Quantity: 150
  Brand: Bosch
  Power: 800W
  Lead Time: 2 days

Mirakl API POST:
  POST /mmp/shops/12345/products
  Content: [Transformed data]
```

### 9.3 Webhook Implementation (Order Processing)

**Webhook Listener Setup (Node.js Example):**

```javascript
const express = require('express');
const crypto = require('crypto');
const app = express();

const WEBHOOK_SECRET = 'your_webhook_secret_from_mirakl';

app.post('/webhooks/mirakl', express.json(), (req, res) => {
  // 1. Verify signature
  const signature = req.headers['x-mirakl-signature'];
  const body = JSON.stringify(req.body);
  
  const hmac = crypto
    .createHmac('sha256', WEBHOOK_SECRET)
    .update(body)
    .digest('base64');
  
  if (signature !== `sha256=${hmac}`) {
    console.error('Signature verification failed');
    return res.status(401).send('Unauthorized');
  }
  
  // 2. Process webhook
  const event = req.body;
  console.log(`Received event: ${event.event_type}`);
  
  switch (event.event_type) {
    case 'order.created':
      handleOrderCreated(event);
      break;
    
    case 'order.shipped':
      handleOrderShipped(event);
      break;
    
    case 'refund.requested':
      handleRefundRequest(event);
      break;
    
    default:
      console.warn(`Unknown event: ${event.event_type}`);
  }
  
  // 3. Acknowledge receipt
  res.status(200).json({ status: 'ok' });
});

function handleOrderCreated(event) {
  const { order_id, items, customer, shipping_address } = event;
  
  // 1. Fetch from Mirakl API (optional - webhook already has details)
  // 2. Validate inventory
  // 3. Create order in your ERP/WMS
  // 4. Update status (ACCEPTED or REJECTED) via API PUT
  
  console.log(`Order ${order_id} created for ${customer.email}`);
  
  // Simulate inventory check
  const hasStock = items.every(item => checkInventory(item.product_sku));
  
  if (hasStock) {
    acceptOrder(order_id);
  } else {
    rejectOrder(order_id, 'Out of stock');
  }
}

function handleOrderShipped(event) {
  const { order_id, shipments } = event;
  console.log(`Order ${order_id} shipped`);
  
  shipments.forEach(shipment => {
    console.log(`  Carrier: ${shipment.carrier}`);
    console.log(`  Tracking: ${shipment.tracking_number}`);
  });
  
  // Update your WMS
}

function handleRefundRequest(event) {
  const { refund_id, order_id, amount } = event;
  console.log(`Refund request: ${refund_id} for order ${order_id}`);
  
  // Process refund (initiate RMA, etc.)
}

function checkInventory(sku) {
  // Query your WMS/ERP
  return true; // placeholder
}

function acceptOrder(orderId) {
  // Call Mirakl API:
  // PUT /mmp/shops/{shop_id}/orders/{order_id}/status
  // Body: { status: "ACCEPTED" }
}

function rejectOrder(orderId, reason) {
  // PUT with status: "REJECTED", rejection_reason
}

app.listen(3000, () => {
  console.log('Webhook listener running on port 3000');
});
```

**Webhook Registration (Manual):**

Contact Leroy Merlin Integration Team or use vendor dashboard:
```
URL: https://yourdomain.com/webhooks/mirakl
Secret: [auto-generated by Mirakl]
Events: order.created, order.shipped, refund.requested, inventory.alert
```

### 9.4 Batch Import via CSV/Excel

**CSV Format (Mirakl Accepted):**

```csv
SKU,TITLE,DESCRIPTION,CATEGORY_CODE,PRICE,QUANTITY,BRAND,COLORE,POWER,LEADTIME_DAYS,IMAGE_URL
VENDOR-SKU-001,Trapano Battente 13mm 800W,Trapano professionale con...,UTENSILI_ELETTRICI,125.99,150,Bosch,Nero,800W,2,https://yourdomain.com/img1.jpg
VENDOR-SKU-002,Martello 500g,Martello in acciaio temperato,UTENSILI_MANUALI,15.50,300,Stanley,Giallo,N/A,1,https://yourdomain.com/img2.jpg
VENDOR-SKU-003,Vernice Bianca 5L,Vernice acrilica esterna,VERNICI_SOLVENTI,45.99,75,Dulux,Bianco,N/A,3,https://yourdomain.com/img3.jpg
```

**Upload via Mirakl Dashboard:**
1. Seller Center → Catalog → Bulk Import
2. Select CSV file
3. Validate mappings
4. Submit
5. Monitoring: Dashboard shows import status (5-30 min for 10k products)

---

## 10. Support & Escalation

### 10.1 Support Channels

| Channel | Response Time | Best For |
|---------|---------------|---------|
| **Integration Portal** | 24-48h | Technical issues, API errors |
| **Email: partners-it@leroymerlin.fr** | 24h | Account issues, onboarding |
| **Slack Community** | 4-8h | Quick questions, peer support |
| **Phone: +39 02 XXXX XXXX** | 2-4h | Critical production issues |
| **Quarterly Business Review** | Scheduled | Strategy, volume growth |

### 10.2 Common Issues & Troubleshooting

**Issue 1: Products not appearing on storefront**
```
Cause: Category not approved OR image validation failure
Solution:
  1. Check Mirakl dashboard: Catalog > Products > Status
  2. Review rejection reason (e.g., "Image too small")
  3. Correct issue and re-upload
  4. Verify category assignment
```

**Issue 2: Inventory out of sync**
```
Cause: ChannelEngine sync failed OR manual stock mismatch
Solution:
  1. Verify ChannelEngine connection (dashboard)
  2. Check API logs for errors
  3. Force manual sync: ChannelEngine > Actions > Sync Now
  4. If persists: Contact integration support
```

**Issue 3: Orders not received (Webhook failure)**
```
Cause: Webhook URL unreachable OR signature mismatch
Solution:
  1. Test webhook endpoint: https://yourdomain.com/webhooks/mirakl
  2. Verify SSL certificate (HTTPS required)
  3. Check firewall rules (allow Mirakl IPs)
  4. Validate signature algorithm (HMAC-SHA256)
  5. Re-register webhook in Mirakl dashboard
```

**Issue 4: High commission disputes**
```
Solution:
  1. Review settlement report (line-by-line)
  2. Verify commission % in contract
  3. Check for return adjustments
  4. Submit dispute with evidence (within 30 days)
```

### 10.3 Performance Metrics & KPIs

**Vendor Dashboard Metrics:**

| KPI | Target | Warning |
|-----|--------|----------|
| Order Acceptance Rate | >95% | <90% = account review |
| Shipment On-Time | >98% | <95% = notice |
| Return Rate | <2% | >5% = category risk |
| Product Approval Rate | >95% | <80% = rejection risk |
| Response Time to Support | <24h | >48h = escalation |

---

## 11. Appendix: API Rate Limits & Performance

### 11.1 Rate Limiting

```
Standard Tier:
  - 100 requests/second per vendor
  - Burst allowance: 500 req/sec for 30 seconds
  - Header: X-RateLimit-Remaining
  - Response when exceeded: 429 Too Many Requests

Batch Operations:
  - Max 1000 items per request
  - Recommended: 500 items for stability
  - Retry logic: Exponential backoff (1s, 2s, 4s...)
```

### 11.2 Recommended Sync Intervals

```
Product Catalog: Every 30-60 minutes
Inventory Levels: Every 30 minutes (or real-time if ERP supports)
Orders: Real-time via webhooks
Returns/Refunds: Real-time via webhooks
```

### 11.3 Data Validation Rules

| Field | Format | Example | Error |
|-------|--------|---------|-------|
| SKU | Alphanumeric + dash/underscore | VENDOR-SKU-001 | Duplicate = 409 Conflict |
| Price | Decimal, 2 decimals | 125.99 | <0.01 = 400 Bad Request |
| Quantity | Integer >= 0 | 150 | Negative = 400 Bad Request |
| Category | Enum (predefined) | UTENSILI_ELETTRICI | Invalid = 400 Bad Request |
| Image URL | HTTPS URL | https://example.com/img.jpg | HTTP = rejected |

---

## 12. Glossary & Abbreviations

**Technical Terms:**
- **OAuth2:** Open authorization standard for secure API access
- **HMAC-SHA256:** Cryptographic signature verification algorithm
- **Webhook:** Server-to-server HTTP POST callback
- **API Rate Limiting:** Throttling requests to prevent server overload
- **MFN:** Mirakl Fulfillment Network (logistics service)
- **SLA:** Service Level Agreement (response time commitment)
- **RMA:** Return Merchandise Authorization
- **DPA:** Data Processing Agreement (GDPR compliance)
- **DoC:** Declaration of Conformity (CE marking requirement)

**Marketplace Terms:**
- **Shop ID:** Vendor identifier in Mirakl platform
- **Shop SKU:** Leroy Merlin's internal product code
- **Lead Time:** Days from order acceptance to shipment
- **Settlement:** Weekly payout calculation and transfer
- **Commission:** % fee deducted from order value
- **Chargeback:** Disputed transaction (customer initiated)
- **Click&Collect:** In-store pickup option

---

## 13. Document History & Versioning

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | Feb 2026 | Initial release | Leroy Merlin Tech Team |
| 1.5 | Mar 2026 | Added webhook examples, expanded logistics | Integration Team |
| 2.0 | Mar 2026 | Major revision: OAuth2, batch import, compliance | CTO |
| 2.1 | Apr 2026 | Current: Fixed API examples, updated rates | Tech Documentation |

---

## 14. Legal Disclaimer & Support

**Confidentiality:**
This document contains proprietary information. Intended for authorized Leroy Merlin marketplace vendors only.

**No Warranty:**
Information provided "as-is" without warranty. Leroy Merlin reserves the right to modify terms, APIs, and policies with 30-day notice.

**Questions?**
Contact: partners-integration@leroymerlin.fr

**Document Classification:**
- Visibility: Partners (vendors) only
- Distribution: Internal Use - Marketplace Operators
- Audience: CTO, System Architects, Marketplace Operations Teams
