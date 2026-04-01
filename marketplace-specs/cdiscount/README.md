# Cdiscount Marketplace - Specifiche Tecniche Complete

> **Documento di riferimento per CTO, System Architects, Marketplace Operators**
>
> **Versione:** 2.1.1 | **Data:** Aprile 2026 | **Audience:** Vendiamonoi.it Engineering
>
> **Sottosistema:** Cdiscount Pro, Cdiscount Marketplace (powered by Octopia/Mirakl), C le Marchè
>
> **Regione:** Francia + DOM-TOM | **Valuta primaria:** EUR

---

## 1. Informazioni Generali

| Attributo | Valore |
|-----------|--------|
| **Nome Ufficiale** | Cdiscount.com / Cdiscount Marketplace |
| **Fondazione** | 1998 |
| **Sede Legale** | Bordeaux, Francia |
| **Società Madre** | Groupe Sofina (Société Générale Surveillance) / Capital affiliation depuis 2020 |
| **País Operativo Primario** | Francia (FRA) + DOM-TOM (Réunion, Guadeloupe, Martinique, Guyane) |
| **URL Ufficiale Seller** | https://www.cdiscount.com/sellers |
| **URL API Documentazione** | https://api-docs.octopia.io / https://developers.cdiscount.com |
| **URL Portal Venditori** | https://sellers.cdiscount.com |
| **Categoria GMV Globale** | Top 10 marketplace Francia; €3.2B GMV annuale (2024) |
| **Tipologia Modello** | B2C Marketplace + B2B Fulfillment |
| **Numero Seller Attivi** | 7000+ (Francia); 2500+ internazionali |
| **SKU Totali Catalogo** | 80+ milioni (incluse varianti) |
| **Copertura Geografica Spedizioni** | Francia, Benelux, Spagna, Italia, Germania, Svizzera, Austria, Polonia, República Checa |

---

## 2. Modello di Business — Tre Pilastri

### 2.1 Cdiscount Marketplace (Powered by Octopia/Mirakl Hybrid)

**Architettura tecnica:** Sistema ibrido proprietario Octopia basato su codebase Mirakl 2.x.
- **Visibilità Prodotti:** Integrazione search engine dedicato (Algolia + Elasticsearch proprietario)
- **Featured Placement:** Algoritmo A/B testing per posizionamento bestseller
- **Commissioni:** Tabella variabile per categoria (vedi sezione 9)
- **Pagamento Seller:** Ciclo settimanale (lunedì)
- **Onboarding:** 5-7 giorni lavorativi post-KYC KYSC
- **Seller Support:** Ticketing 24/7 in FR, EN; SLA 24h L-V
- **Dispute Resolution:** Sistema integrato Octopia (buyer-driven payback guarantee)
- **Cancellazioni Permesse:** Fino a 7 giorni post-acquisto (customer-initiated); seller cancellations penalized

### 2.2 Cdiscount Pro (B2B + Dropshipping)

**Modello:** White-label B2B + dropshipping da fornitori certificati
- **Accesso:** Solo su invito; listing automatici da catalogo fornitori
- **Commissioni:** 15-22% categoria-based
- **Fulfillment:** Tercerizzato (partner logistici)
- **Min. Order Value:** €150 EUR
- **SLA Consegna:** 3-5 giorni lavorativi (fra entrega express disponibile)

### 2.3 C le Marché (Legacy B2C + Outlet)

**Status:** Sistema legacy integrato in Cdiscount (2021). Transizione in corso verso Octopia Core.
- **Focus:** Liquidazione stock, overstock, brand outlet
- **Commissioni:** Ridotte (8-12%)
- **Visibilità:** Segregata su https://marche.cdiscount.com
- **EOL Roadmap:** Full consolidation in main Cdiscount Marketplace (Q4 2026)

---

## 3. API Ecosystem & Integration Landscape

### 3.1 Octopia API (Primary Integration Layer)

**Endpoint Base:** `https://api.octopia.io/v3` | `https://api-marketplace.cdiscount.com/v2`

**Authentication:**
- OAuth 2.0 + API Key (legacy JWT supported until Q3 2026)
- Scopes: `products:read`, `products:write`, `orders:read`, `fulfillment:write`, `inventory:read`, `returns:manage`, `analytics:read`
- Rate Limit: 1000 req/min per seller (burst: 5000)
- Timeout: 30s

**Core Modules:**

#### Product Management
```
POST   /sellers/{sellerId}/products
GET    /sellers/{sellerId}/products/{sku}
PUT    /sellers/{sellerId}/products/{sku}
DELETE /sellers/{sellerId}/products/{sku}
GET    /sellers/{sellerId}/products (paginated, 100 per page max)
POST   /sellers/{sellerId}/products/bulk (max 500 per request)
GET    /sellers/{sellerId}/products/{sku}/audit-log (v3.5+)
```

**Product Schema (Required Fields):**
- `sku`: Unique identifier (alphanumeric, max 64 chars)
- `title`: Product name (max 200 chars; Italian preferred)
- `description`: Full description (HTML allowed; max 5000 chars)
- `price`: Selling price in EUR (decimal, 2 places)
- `originalPrice`: Recommended retail price (optional)
- `barcode`: EAN/UPC (13-14 digits; validation strict)
- `stock`: Current inventory (integer, ≥0)
- `category`: Cdiscount category ID (hierarchical; see section 4)
- `brand`: Brand name (max 100 chars)
- `images`: Array of URLs (max 20; JPG/PNG; min 500x500px)
- `weight`: Shipping weight in kg (decimal)
- `dimensions`: L x W x H in cm (object with length, width, height)
- `shipping`: Object {price_EUR, leadtime_days, zones}
- `attributes`: Free-form key-value (used for search filters)
- `active`: Boolean (toggles visibility)

**Product Validation Rules:**
- Title length: 20-200 chars (auto-truncated at 200)
- Price range: €0.01 - €999,999.99
- Stock must be integer; no float values
- Images: max 5MB each; auto-optimized to 2000x2000px
- Barcode validation: EAN-13 checksum required
- Category: Must exist in Cdiscount taxonomy (see section 4)
- Attributes: No reserved keywords (price, stock, category)
- Description: Forbidden HTML tags: <script>, <iframe>, <embed>, <object>

#### Order Management
```
GET    /sellers/{sellerId}/orders
GET    /sellers/{sellerId}/orders/{orderId}
PUT    /sellers/{sellerId}/orders/{orderId}/accept-deny
GET    /sellers/{sellerId}/orders/{orderId}/shipments
POST   /sellers/{sellerId}/orders/{orderId}/shipment
GET    /sellers/{sellerId}/orders/{orderId}/messages
POST   /sellers/{sellerId}/orders/{orderId}/messages
```

**Order Object Structure:**
```json
{
  "orderId": "CD-2026-04-000001",
  "sellerId": "SELLER_ID_123",
  "createdAt": "2026-04-01T10:30:00Z",
  "status": "PENDING",
  "buyer": {
    "email": "buyer@example.fr",
    "firstName": "Jean",
    "lastName": "Dupont",
    "phone": "+33612345678"
  },
  "items": [
    {
      "sku": "ITEM-001",
      "title": "Product Name",
      "quantity": 2,
      "price": 29.99,
      "status": "PENDING_SHIPMENT"
    }
  ],
  "shippingAddress": {
    "line1": "123 Rue de la Paix",
    "city": "Paris",
    "postalCode": "75001",
    "country": "FR"
  },
  "totalAmount": 69.98,
  "currency": "EUR",
  "paymentStatus": "PAID"
}
```

**Order Lifecycle:**
1. **PENDING** (0-24h) — Seller must accept/deny within 24h
2. **ACCEPTED** — Seller confirmed, waiting shipment preparation
3. **PENDING_SHIPMENT** — Ready to ship; seller provides tracking
4. **SHIPPED** — Tracking number sent to buyer
5. **DELIVERED** — Buyer received; dispute window opens (30 days)
6. **CLOSED** — Transaction complete; no further disputes
7. **CANCELLED** — Order cancelled (seller or buyer)
8. **RETURNED** — Item returned (see section 6)

**Auto-Accept Rule:** If seller doesn't accept/deny within 24h, Cdiscount system auto-denies the order (seller reputation penalty: -2 points per incident).

#### Inventory & Warehouse
```
GET    /sellers/{sellerId}/inventory
GET    /sellers/{sellerId}/inventory/{sku}
PUT    /sellers/{sellerId}/inventory/{sku}/stock
GET    /sellers/{sellerId}/inventory/sync-status
POST   /sellers/{sellerId}/inventory/batch-update (max 1000 SKUs)
```

**Stock Sync Rules:**
- Real-time push (preferred): `PUT /inventory/{sku}/stock` with quantity delta
- Scheduled pull: Cdiscount polls seller inventory endpoint daily 2:00-4:00 UTC
- Overselling: If stock goes negative, Cdiscount auto-pauses product (notification sent)
- Threshold Alert: System alerts if stock <5 units (configurable)

#### Returns & Refunds
```
GET    /sellers/{sellerId}/returns
GET    /sellers/{sellerId}/returns/{returnId}
PUT    /sellers/{sellerId}/returns/{returnId}/approve-refuse
GET    /sellers/{sellerId}/returns/{returnId}/messages
POST   /sellers/{sellerId}/refunds (manual processing)
```

**Return Window:** 30 days from delivery (standard). Extended to 60 days for electronics (TVA rule).

**Refund Processing:**
- Buyer-initiated: Money returned to buyer by Cdiscount; seller debited
- Seller-approved return: Restock credit applied to seller account
- Dispute: Escalates to Cdiscount arbitration team

### 3.2 Search & Catalog API

**Search Endpoint:** `GET /catalog/search`

**Parameters:**
- `q`: Query string (Elasticsearch syntax supported)
- `filters`: Category, brand, price range, attributes
- `sort`: Relevance (default), price_asc, price_desc, popularity, newest
- `limit`: 1-100 (default 20)
- `offset`: For pagination

**Example Request:**
```
GET /catalog/search?q=smartphone&filters[category]=Telefonia&filters[price][min]=200&filters[price][max]=800&sort=price_asc&limit=50
```

**Response:**
```json
{
  "results": [
    {
      "sku": "IPHONE-13-128GB",
      "title": "Apple iPhone 13 128GB",
      "price": 789.99,
      "seller": {"id": "SELLER_123", "name": "Tech Store Pro"},
      "rating": 4.8,
      "reviews": 245,
      "image": "https://cdn.cdiscount.com/..."
    }
  ],
  "total": 1234,
  "facets": {"brands": [...], "price_ranges": [...]}
}
```

### 3.3 Analytics & Reporting API

**Endpoints:**
- `GET /sellers/{sellerId}/analytics/dashboard` — KPI overview
- `GET /sellers/{sellerId}/analytics/sales` — Revenue breakdown
- `GET /sellers/{sellerId}/analytics/inventory` — Stock turnover
- `GET /sellers/{sellerId}/analytics/performance` — Ratings, disputes, cancellation rate
- `GET /sellers/{sellerId}/analytics/traffic` — Product page views, impressions

**Metrics Available:**
- Conversion Rate (%)
- AOV (Average Order Value) in EUR
- Cancellation Rate (%)
- Return Rate (%)
- Seller Rating (1-5 stars)
- Dispute Resolution Time (days)
- Inventory Turnover Ratio

---

## 4. Cdiscount Category Taxonomy

**Root Categories (L0):**

| ID | Name (FR) | ID | Name (IT) | Avg Commission |
|----|-----------|----|-----------|-----------------|
| 1 | Électronique | 1 | Elettronica | 12% |
| 2 | Informatique | 2 | Informatica | 10% |
| 3 | Téléphonie | 3 | Telefonia | 8% |
| 4 | Maison & Jardin | 4 | Casa & Giardino | 15% |
| 5 | Sports & Loisirs | 5 | Sport & Tempo Libero | 16% |
| 6 | Mode & Chaussures | 6 | Moda & Scarpe | 18% |
| 7 | Beauté & Santé | 7 | Bellezza & Salute | 14% |
| 8 | Livres & Media | 8 | Libri & Media | 6% |
| 9 | Jouets & Jeux | 9 | Giocattoli & Giochi | 16% |
| 10 | Auto & Moto | 10 | Auto & Moto | 12% |

**Subcategories (L1-L3):** Each root category has 2-5 levels. Example:
- 1 → Electronics
  - 1.1 → Computers
    - 1.1.1 → Laptops
    - 1.1.2 → Desktops
  - 1.2 → Audio-Video
    - 1.2.1 → Speakers
    - 1.2.2 → Headphones

**Full Taxonomy:** Available via `GET /catalog/categories` (paginated, 500 categories per page).

---

## 5. Commission Structure & Fee Schedule

### 5.1 Seller Commission Tiers (2026)

| Category | Base Rate | Volume Bonus (if GMV > €50k/month) | Margin (after FEE) |
|----------|-----------|-------------------------------------|--------------------|
| Électronique | 12% | -1% | ~87% |
| Informatique | 10% | -0.5% | ~89.5% |
| Téléphonie | 8% | -0.5% | ~91.5% |
| Maison & Jardin | 15% | -1% | ~84% |
| Sports & Loisirs | 16% | -1.5% | ~82.5% |
| Mode | 18% | -2% | ~80% |
| Beauté & Santé | 14% | -1% | ~85% |
| Livres & Media | 6% | 0% | ~94% |
| Jouets & Jeux | 16% | -1.5% | ~82.5% |
| Auto & Moto | 12% | -1% | ~87% |

### 5.2 Additional Fees

**Fulfillment (Optional):** €0.50 - €3.00 per order (depends on item category & weight)

**Shipping Label Subsidy:** Cdiscount refunds €2-5 per label if seller uses Cdiscount-approved carrier

**Return Processing:** €1.50 per return (paid from seller refund if return approved)

**Payment Processing:** 1.5% + €0.25 per transaction (deducted at payout)

**Advertising (Sponsored Listing):** €0.10 - €2.00 per click (CPC model; optional)

**Subscription Premium (Pro Seller Badge):** €99/month (optional; increases visibility)

### 5.3 Payment & Payout Schedule

**Payout Day:** Every Monday (weekly)
**Payout Currency:** EUR
**Settlement Lag:** T+5 days (order placed Monday → settled following Monday)
**Minimum Payout:** €10 EUR (if < €10, rolls to next week)
**Bank Fee:** Cdiscount covers transfer fees (SEPA)
**Payment Method:** IBAN direct bank transfer

**Deductions (Applied at Payout):**
- Commissions
- Refunds & chargebacks
- Return processing fees
- Disputed items (held in escrow; released after 30 days if no chargeback)

---

## 6. Returns & Disputes Management

### 6.1 Return Policy (Default)

**Standard Return Window:** 30 days from delivery
**Electronics Exception:** 60 days (French consumer law)
**No Return Categories:** Food, cosmetics (opened), custom items, clearance stock

**Return Reasons (Buyer-Selectable):**
- Defect/Quality
- Not as described
- Wrong item
- Damaged in transit
- Changed mind
- Size/Fit (apparel only)

### 6.2 Return Workflow

1. **Buyer Initiates Return** — Cdiscount generates prepaid return shipping label
2. **Return in Transit** — Buyer ships item back (Cdiscount pays shipping)
3. **Item Received** — Cdiscount warehouse inspects item (2-3 days)
4. **Inspection Result:**
   - **Approved** → Refund issued to buyer; seller debited; item restocked or liquidated
   - **Refused** → Item returned to seller at seller's cost; buyer disputes
5. **Dispute Escalation** — If seller refuses or disputes, goes to Cdiscount arbitration

**Seller Dispute Window:** 7 days to respond to return reason

### 6.3 Chargeback & Dispute Management

**Buyer Disputes:** "Item not received" or "Item not as described"
- **Initial Deadline:** Buyer has 45 days from order date
- **Seller Response Time:** 7 days
- **Arbitration:** Cdiscount investigates; final decision within 30 days
- **Appeal:** Either party can appeal once; final decision binding

**High Dispute Rate:** If seller exceeds 5% dispute rate in 30 days, account flagged for review. >10% → temporary suspension.

---

## 7. Seller Account Suspension & Penalties

### 7.1 Suspension Triggers

| Violation | Threshold | Action | Appeal? |
|-----------|-----------|--------|--------|
| Order Cancellation | >10% / month | Temp. Suspension (7 days) | Yes |
| Missing Shipments | >5% / month | Temp. Suspension (14 days) | Yes |
| Return/Dispute Rate | >5% / month | Account Review | Yes |
| Late Shipments | >15% / month | Performance Warning → Suspension | Yes |
| Policy Violations (duplicates, fake reviews) | 1+ incidents | Immediate Suspension (30 days) | Limited |
| Intellectual Property (counterfeits) | 1+ report | Immediate Suspension (180 days) | Court appeal only |
| Fraud/Chargebacks | >3 / month | Permanent Ban | No |

### 7.2 Seller Rating & Reputation

**Seller Rating Scale:** 1.0 - 5.0 stars

**Rating Factors:**
- **On-Time Shipment (40%)** — Percentage of orders shipped within promised timeframe
- **Item Quality (30%)** — Based on buyer reviews & return rate
- **Customer Service (20%)** — Response time to messages, disputes
- **Accuracy (10%)** — Product description accuracy, item condition

**Reputation Penalties:**
- Each order cancellation: -2 rating points
- Each late shipment: -1 rating point
- Each negative review: -0.5 rating points
- Each resolved dispute: -0.25 rating points

**Minimum Required Rating:** 3.0 stars (below → account review)

---

## 8. Shipping & Fulfillment

### 8.1 Shipping Options

**Seller-Fulfilled (SFM):**
- Seller ships items directly from own warehouse
- Shipping cost: Seller-determined (or Cdiscount subsidy)
- Delivery SLA: 5-7 days (standard), 2-3 days (express)
- Tracking: Seller provides carrier tracking number
- Supported Carriers: Colissimo, UPS, DPD, Mondial Relay, Amazon Logistics FR

**Cdiscount Fulfillment (FBD):** [Not yet available for new sellers; legacy only]

**Hybrid:** Seller fulfills some categories; Cdiscount for others (negotiated per account)

### 8.2 Shipping Zones

| Zone | Destinations | Standard Delay | Carrier Options |
|------|---------------|----------------|-----------------|
| Zone 1 | France (mainland) | 3-5 days | Colissimo, UPS, DPD |
| Zone 2 | Benelux, Spain, Italy | 5-7 days | UPS, DPD, Mondial Relay |
| Zone 3 | Germany, Austria, Poland | 7-10 days | DPD, Mondial Relay |
| Zone 4 | DOM-TOM | 7-14 days | Colissimo (specialized) |
| Zone 5 | Switzerland | 5-7 days | UPS, DPD |
| Zone 6 | Czech Republic | 10-14 days | DPD (limited) |

**Shipping Cost Structure (Seller-Set):**
- Base cost for Zone 1: €0.00 - €30.00 (recommended: €3-8 for standard items)
- Zone surcharges: +€2-5 per zone
- Weight-based: Can add €0.50 per 500g excess
- Free shipping offers boost CTR by 15-25%

### 8.3 Forbidden Items & Restricted Categories

**Prohibited:**
- Weapons, explosives, hazardous materials
- Counterfeits, pirated content
- Recalls (any item on CPSC or EU safety recall list)
- Alcohol (restricted; requires license)
- Tobacco, vaping products
- Live animals (except live fish for aquariums)
- Prescription drugs

**Restricted (License/Declaration Required):**
- Food items (must pass food safety cert.)
- Cosmetics (CPNP registration)
- Used electronics (must be tested & certified working)
- Jewelry, precious metals (authentication certificate)

---

## 9. Marketplace Algorithms & Visibility

### 9.1 Search Ranking Algorithm (Algolia + Proprietary ML)

**Ranking Factors (Weighted):**
1. **Text Relevance (25%)** — Title, description match to search query
2. **Sales Velocity (20%)** — Units sold per day (trending boost)
3. **Price Competitiveness (15%)** — Lowest price for item (with quality threshold)
4. **Seller Rating (15%)** — 4-star+ sellers ranked higher
5. **Click-Through Rate (10%)** — Historical CTR from search results
6. **Review Rating (10%)** — Product star rating (aggregated buyer reviews)
7. **Stock Availability (5%)** — In-stock items ranked above out-of-stock

**Boost Factors:**
- New product (0-7 days): +30% boost
- Free shipping: +15% boost
- Seller subscription (Pro Badge): +10% boost
- Sponsored ad: Top placement (paid)

### 9.2 Homepage & Featured Placement

**Homepage Algorithm:**
- **Trending** (top-left): Top 20 products by sales velocity (recalc. daily)
- **Personalized** (logged-in users): ML-based on browsing history
- **Category Best-Sellers** (each section): Top 10 per category
- **Flash Deals** (rotating hourly): Seller-offered discounts; auto-populated

**Featured Placement Cost:** €0.15 - €1.00 per day per product (optional)

### 9.3 Visibility Score & Quality Index

**Visibility Score (0-100):**
- Calculated per product based on: completeness, images, reviews, sales history
- Threshold: >60 required for homepage eligibility
- Updates: Daily (based on last 30 days data)

**Quality Index Penalization:**
- Incomplete description: -5 points
- Missing images (< 3): -10 points
- No shipping weight: -5 points
- High return rate (>10%): -15 points
- Low seller rating (<3 stars): Auto-delisted

---

## 10. Seller Onboarding & KYC

### 10.1 Account Creation Flow

1. **Registration** — Email verification, password setup
2. **Store Information** — Business name, VAT number (EU), contact info
3. **KYC/KYSC** — Identity & address verification (via Onfido API)
4. **Banking Details** — IBAN for payout (SEPA validation)
5. **Product Catalog** — Upload initial products (10+ required)
6. **Policy Acceptance** — Terms of service, commission agreement, returns policy
7. **Account Approval** — Cdiscount team review (2-7 days)
8. **Go Live** — Seller can list products; receives API credentials

**Processing Time:** 5-7 business days (KYC approval is bottleneck)

### 10.2 KYC Requirements (EU Sellers)

**Individual Seller:**
- Government ID (passport or driver's license)
- Proof of residence (utility bill, bank statement, <3 months old)
- Tax ID (SIRET/SIREN for France; VAT number for others)

**Business Seller:**
- Business registration document (Kbis for France)
- Proof of address (official business license)
- Tax ID & VAT number
- Beneficial owner declaration (if applicable)
- Bank statement showing business account

**Verification Tools:** Onfido API, manual document review (for high-risk)
**Rejection Rate:** ~5% (usually incomplete docs; can resubmit)

### 10.3 First Sale Restrictions

**New Sellers (First 30 Days):**
- Max 100 orders/day
- Max 5000 EUR GMV/month
- All items require manual approval before going live
- Cannot use Cdiscount Fulfillment

**Restrictions Lift:** After 30 days + 50+ completed orders + 4.0+ rating

---

## 11. Data & Compliance

### 11.1 Data Privacy & GDPR

**Data Processor Agreement:** Seller acts as data processor; Cdiscount is controller
- Buyer personal data (email, address, phone) subject to GDPR
- Seller must not contact buyer outside Cdiscount messaging system (prohibited)
- Seller email scraping: Violation → account suspension
- Data retention: Seller account data retained 3 years post-deletion

**Audit Rights:** Cdiscount can audit seller compliance; annual data protection review

### 11.2 Intellectual Property

**Seller Warranty:** Seller guarantees all products are authentic & legally sourced
- Counterfeits: Automatic suspension (180 days) + possible legal action
- Trademark issues: Cdiscount removes listing; seller can contest
- Copyright (images): Seller must own or license; third-party images prohibited

**IP Reporting:** Cdiscount has IP team monitoring for violations

### 11.3 Tax Compliance

**Seller Responsibility:** Seller must comply with local tax laws (VAT, income tax)
- Cdiscount does NOT file taxes on seller behalf
- Cdiscount reports seller to local tax authority (for transparency)
- Seller VAT registration: Some EU countries require if seller based outside country

**VAT-Free Threshold (EU):** Sellers not registered in-country can sell up to €10k/year before VAT registration requirement

---

## 12. Technical Integrations & ERP/Inventory Systems

### 12.1 Supported Integrations

**Native Integrations (Pre-built):**
- Shopify (via Octopia Connector)
- WooCommerce (via Octopia Connector)
- Prestashop (via custom module)
- Magento (via extension; maintained by Octopia)
- SAP (B2B; requires enterprise support)

**API-Based Integrations:**
- Custom ERP via Octopia REST API
- Inventory sync via webhook (real-time or scheduled)
- Order auto-import to fulfillment systems
- Invoice/payment sync to accounting software

**Third-Party Connectors:**
- ChannelEngine (European multi-marketplace aggregator)
- Sellr (inventory management SaaS)
- Stocky (inventory optimization)
- Trackio (unified shipping management)

### 12.2 Inventory Sync Best Practices

**Real-Time Sync (Recommended):**
- Set up webhook: `POST https://your-server.com/webhook/cdiscount-inventory`
- Cdiscount sends stock update on each sale (millisecond delay)
- Your server updates local inventory; responds with 200 OK
- Fallback: If webhook fails 3x, Cdiscount switches to polling

**Scheduled Sync (Daily):**
- Cron job: Run every 2 hours
- Pull latest inventory from Cdiscount API
- Compare with local DB; sync differences
- Log all changes for audit trail

**Overselling Prevention:**
- Keep local inventory slightly lower than actual (buffer: -5%)
- Disable product on Cdiscount if local stock <0
- Use inventory reservations in local DB (hold for 15 min per Cdiscount sale)

---

## 13. Support & Escalation

### 13.1 Seller Support Channels

| Channel | Hours | Response Time | Use Case |
|---------|-------|----------------|-----------|
| Help Center | 24/7 | Self-service | General questions |
| Email Support | 24/7 | 24h (L-V), 48h (WE) | Account issues, disputes |
| Live Chat | 9:00-20:00 CET (L-V) | 5-30 min | Quick troubleshooting |
| Phone | 9:00-18:00 CET (L-V) | Immediate | Urgent issues, escalations |
| Community Forum | 24/7 (peer support) | Variable | Best practices, tips |

**SLA (Service Level Agreement):**
- Critical issues (account suspended): Response within 2 hours
- High priority (payment problem): Response within 4 hours
- Medium priority (product issue): Response within 24 hours
- Low priority (general question): Response within 48 hours

### 13.2 Common Issues & Troubleshooting

**Products Not Syncing:**
- Check API credentials (may have expired after 90 days)
- Verify category ID exists in Cdiscount taxonomy
- Confirm product fields pass validation (see section 3.1)
- Check webhook logs for failed requests

**Orders Not Importing:**
- Verify seller account has `orders:read` scope
- Check if orders are in PENDING status (must be manually accepted)
- Confirm account not flagged for suspension

**Payment Issues:**
- Bank account validation takes 1-2 business days
- SEPA transfers can take 2-3 business days
- If not received: Open ticket with seller support + proof of IBAN

**Low Sales/Visibility:**
- Check Visibility Score (target >60)
- Improve product description (minimum 500 chars recommended)
- Add more high-quality images (target: 8+)
- Monitor & lower price vs. competitors (especially if >€500)
- Enroll in Sponsored Listings for high-margin items

---

## 14. Cdiscount Seller Performance Monitoring Dashboard

### 14.1 Key Metrics Dashboard

**Real-Time KPIs:**
- **Daily Sales Count:** Orders placed in last 24h
- **Daily Revenue:** EUR amount in last 24h
- **Conversion Rate:** (Orders / Sessions) %
- **Average Order Value (AOV):** Avg EUR per transaction
- **Traffic:** Unique sessions (from Cdiscount search/browse)
- **Visibility Score:** Product quality index (0-100)
- **Seller Rating:** Current star rating (1.0-5.0)

**Weekly/Monthly Views:**
- **Sales Trend:** Graph of revenue over time (7d, 30d, 90d)
- **Inventory Turnover:** SKU velocity, slow-movers flagged
- **Return Rate:** % of orders returned
- **Cancellation Rate:** % of orders cancelled by seller
- **Dispute Rate:** % of orders with buyer disputes

### 14.2 Performance Targets (Green Zone)

| Metric | Target | Warning Threshold |
|--------|--------|-------------------|
| On-Time Shipment | >95% | <85% (review) |
| Return Rate | <5% | >10% (suspension risk) |
| Cancellation Rate | <2% | >5% (suspension) |
| Dispute Rate | <2% | >5% (review) |
| Seller Rating | >4.0 | <3.0 (delisting) |
| Inventory Velocity | >1 unit/month | <1 (risk of delisting) |

---

## 15. Roadmap & Future Features (2026 H2)

**Q3 2026:**
- Live Seller Analytics API (custom metrics export)
- Sponsored Listings Dashboard (A/B testing for campaigns)
- C le Marché migration to unified Octopia platform

**Q4 2026:**
- Cdiscount Fulfillment expansion (open to selected sellers)
- Subscription Box feature (recurring orders)
- White-label mobile app for sellers
- Advanced inventory forecasting (ML-based)

**2027 Plans:**
- EU expansion (Germany, Italy, Spain native Cdiscount marketplaces)
- B2B Portal consolidation (Cdiscount Pro → full enterprise API)
- Supply chain financing (seller loans based on performance)

---

## Appendix A: Error Codes & API Responses

**HTTP Status Codes:**
- `200 OK` — Success
- `201 Created` — Resource created
- `400 Bad Request` — Invalid input (see error_message field)
- `401 Unauthorized` — API key invalid or expired
- `403 Forbidden` — Insufficient permissions (scope issue)
- `404 Not Found` — Resource not found (product SKU doesn't exist)
- `409 Conflict` — Duplicate entry (e.g., SKU already exists)
- `429 Too Many Requests` — Rate limit exceeded; retry after X seconds
- `500 Internal Server Error` — Cdiscount server error; contact support
- `503 Service Unavailable` — Maintenance window; check status page

**Error Response Format:**
```json
{
  "error": "INVALID_SKU_FORMAT",
  "message": "SKU must be alphanumeric, max 64 characters",
  "code": 4001,
  "timestamp": "2026-04-01T10:30:00Z",
  "request_id": "req_123456789"
}
```

---

## Appendix B: Sample API Requests & Responses

**Create Product:**
```bash
curl -X POST https://api.octopia.io/v3/sellers/SELLER_123/products \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "sku": "PROD-001",
    "title": "Smartphone Android 13",
    "description": "Dernière génération, écran 6.5 pouces OLED...",
    "price": 299.99,
    "originalPrice": 399.99,
    "barcode": "1234567890123",
    "stock": 50,
    "category": "3",
    "brand": "TechBrand",
    "images": [
      "https://cdn.example.com/image1.jpg",
      "https://cdn.example.com/image2.jpg"
    ],
    "weight": 0.18,
    "dimensions": {"length": 15.5, "width": 7.5, "height": 0.9},
    "shipping": {
      "price_EUR": 5.99,
      "leadtime_days": 3,
      "zones": ["FR", "BE", "NL"]
    },
    "attributes": {
      "Color": "Black",
      "Storage": "128GB",
      "RAM": "6GB"
    },
    "active": true
  }'
```

**Response (201 Created):**
```json
{
  "sku": "PROD-001",
  "id": "prod_5f8d9e9c1a2b3c4d",
  "title": "Smartphone Android 13",
  "status": "PENDING_APPROVAL",
  "createdAt": "2026-04-01T10:30:00Z",
  "message": "Product created successfully; awaiting visibility review"
}
```

**Retrieve Order:**
```bash
curl -X GET https://api.octopia.io/v3/sellers/SELLER_123/orders/CD-2026-04-000001 \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

**Response (200 OK):**
```json
{
  "orderId": "CD-2026-04-000001",
  "createdAt": "2026-04-01T10:15:00Z",
  "status": "PENDING",
  "buyer": {"email": "buyer@example.fr", "firstName": "Jean"},
  "items": [{"sku": "PROD-001", "quantity": 1, "price": 299.99}],
  "totalAmount": 299.99,
  "currency": "EUR",
  "paymentStatus": "PAID",
  "deadline_accept_until": "2026-04-02T10:15:00Z"
}
```

---

## Document Metadata

**Responsabile del Documento:** Vendiamonoi.it S.R.L.
**Distributore digitale:** 30+ marketplace europei
**Stack:** Make.com, Supabase, Claude API, ChannelEngine, Cdiscount Octopia/Mirakl
**Last Updated:** 01 Aprile 2026