# Domain 5.1 — Revenue Recognition & Marketplace Payouts
## Vendiamonoi.it Financial Management Framework

---

## 1. REVENUE RECOGNITION STANDARDS: IFRS 15 & OIC 15

### 1.1 The Five-Step Revenue Recognition Model

IFRS 15 (International Financial Reporting Standards) and OIC 15 (Organismo Italiano di Contabilità) establish a unified framework for recognizing revenue when control of goods transfers to customers. For Vendiamonoi.it operating across 20-30+ EU marketplaces, this model is critical.

**Step 1: Identify the Contract with a Customer**
A contract exists when Vendiamonoi.it lists products on marketplace platforms (Amazon, eBay, Kaufland, etc.). The marketplace operator acts as either principal or agent, fundamentally changing revenue recognition treatment. The contract specificity includes delivery terms, pricing, payment conditions, and returns policies unique to each marketplace.

**Step 2: Identify the Performance Obligations**
The performance obligation is the delivery of goods to the end customer. For each marketplace:
- Amazon: Delivery of physical product or FBA fulfillment
- eBay: Product shipment and delivery confirmation
- Bol.com: Goods delivery to customer's address
- Kaufland: Stock delivery to Kaufland warehouse or direct-to-consumer
- Allegro: Sale and shipment of items

Each performance obligation must be separately identified and valued at transaction price.

**Step 3: Determine the Transaction Price**
Transaction price = List price - Marketplace discounts - Promotional allowances + Shipping revenue

For multi-currency operations:
- EUR transactions: Direct recognition
- GBP (UK marketplace): Spot rate at transaction date
- PLN (Polish): Spot rate conversion
- SEK (Sweden), CZK (Czech), CHF (Switzerland): Spot rates

**Step 4: Allocate the Transaction Price to Performance Obligations**
If bundled sales occur (product + extended warranty, product + gift wrapping):
- Allocate based on standalone selling prices
- Use market assessment or cost-plus methods if standalone prices unavailable
- Document allocation methodology for audit trail

**Step 5: Recognize Revenue When (or As) the Entity Satisfies Performance Obligations**
Critical timing determination:

| Scenario | Recognition Point | Documentation |
|----------|-------------------|----------------|
| FBA (Amazon) | Upon delivery to customer | Amazon delivery confirmation |
| Seller-fulfilled | Upon shipment | Carrier tracking number |
| Bol.com | Upon delivery | Bol.com fulfillment status |
| Kaufland | Upon warehouse delivery OR customer receipt | Invoice/delivery confirmation |
| eBay | Upon delivery confirmation | eBay transaction record |

### 1.2 Principal vs Agent Analysis

This is the most complex determination for marketplace sellers. IFRS 15 (paragraphs 35-38) provides guidance:

**Principal Characteristics:**
- Controls goods before transfer to customer
- Has primary responsibility to fulfill
- Bears inventory/fulfillment risk
- Sets price (within marketplace constraints)
- Revenue recognized at gross amount (list price)

**Agent Characteristics:**
- Facilitates transaction between buyer and seller
- No inventory control
- No fulfillment risk
- Marketplace sets pricing or commission terms
- Revenue recognized at net amount (commission only)

**Vendiamonoi.it Principal vs Agent Matrix:**

| Marketplace | Model | Reasoning | Revenue Recognition |
|-------------|-------|-----------|--------------------|
| Amazon FBA | Principal* | V controls listing, pricing decision | Gross (list price) |
| Amazon MFN | Principal* | V controls shipment, accepts returns | Gross (list price) |
| eBay (own inventory) | Principal | V owns inventory, controls terms | Gross (sale price) |
| Bol.com | Principal* | V controls pricing, fulfillment | Gross (list price) |
| Kaufland | Principal* | V supplies goods, controls pricing | Gross (list price) |
| Allegro (own inventory) | Principal | V owns goods, manages shipment | Gross (sale price) |
| Cdiscount | Principal* | V provides inventory, prices goods | Gross (list price) |
| ManoMano (marketplace) | Agent** | ManoMano controls pricing | Net (commission fee only) |
| Leroy Merlin (B2B) | Agent | Leroy Merlin controls pricing | Net (commission only) |

*FBA/Platform fulfillment doesn't change principal classification; Vendiamonoi still controls the goods.
**Some marketplaces function as agents; requires individual assessment based on contract terms.

### 1.3 Timing Considerations: Shipped vs Delivered

Italian law and IFRS 15 recognize that shipment (transfer of physical control) differs from delivery (customer receipt). The critical timing question: When is performance satisfied?

**Guidance:**
- For goods with high transportation risk: Revenue at delivery confirmation
- For goods in warehouses: Revenue at shipment (marketplace assumes delivery risk)
- For FBA: Revenue at delivery to customer (Amazon's responsibility)

**Practical Timing Policy:**

```
Revenue Recognition Timing by Marketplace:

Amazon FBA/MFN:     Delivery confirmed (customer marked received)
eBay:               Delivery confirmation via carrier (tracking)
Bol.com:            Delivery date (Bol.com backend date)
Kaufland:           Warehouse delivery OR customer delivery (per contract)
Allegro:            Delivery confirmation from customer/courier
Cdiscount:          Delivery to Cdiscount warehouse OR customer
```

### 1.4 Journal Entries for Principal vs Agent

**Principal Revenue (Gross Recognition):**
```
Dr. Accounts Receivable / Cash         EUR 100.00
    Cr. Revenue                                      EUR 100.00
    (To recognize sale on Amazon at list price)

Dr. Cost of Goods Sold                EUR 60.00
    Cr. Inventory                                    EUR 60.00
    (To record COGS)

Dr. Operating Expense - Marketplace Fees EUR 15.00
    Cr. Accounts Payable / Cash                      EUR 15.00
    (To record Amazon commission; separate from revenue)
```

**Agent Revenue (Net Recognition):**
```
Dr. Cash / Accounts Receivable         EUR 5.00
    Cr. Revenue                                      EUR 5.00
    (To recognize commission revenue only; ManoMano controls goods)

No COGS entry; no inventory movement.
```

---

## 2. MARKETPLACE PAYOUT STRUCTURES: EU MARKETPLACES

### 2.1 Amazon (EU)

**Payout Cycles:**
- Payment interval: Every 14 days
- Payment method: Bank transfer, Amazon Business account, or Amazon gift card
- Reserve policy: 14-day reserve on first EUR 100K cumulative sales (adjustable)
- Settlement lag: T+14 (sale date to payment date minimum)

**Commission Structure:**
| Product Category | Referral Fee | Fulfillment (FBA) | Subscription (Monthly) |
|------------------|--------------|-------------------|------------------------|
| Electronics | 8-15% | EUR 0.15-3.50 per unit | EUR 39.99 |
| Clothing | 17% | EUR 0.50-2.50 per unit | EUR 39.99 |
| Books | 15% | EUR 0.35-1.50 per unit | EUR 39.99 |
| Sports | 8-15% | EUR 0.25-3.00 per unit | EUR 39.99 |
| Home & Garden | 15% | EUR 0.25-2.50 per unit | EUR 39.99 |
| Toys | 10% | EUR 0.50-2.50 per unit | EUR 39.99 |

**Payout Formula:**
```
Gross Proceeds = Sale Price × Quantity
Less: Referral Fee (% of sale price)
Less: FBA/Fulfillment Fees
Less: Payment Processing Fees (0.3% + EUR 0.30 for credit cards)
Less: Advertising Spend (if PPC active)
Less: Returns/Chargebacks
= Net Payout
```

**Example:** EUR 100 sale, Electronics, FBA
```
Gross Proceeds:             EUR 100.00
Referral Fee (15%):        (EUR 15.00)
FBA Fee (per unit):        (EUR 2.50)
Payment Processor (0.3%):   (EUR 0.30)
= Net to Seller:            EUR 82.20
(Paid 14 days later)
```

**Currencies:** EUR for all EU operations; settlements in EUR via SEPA transfer.

---

### 2.2 eBay (EU)

**Payout Cycles:**
- Payment interval: Daily or weekly (seller choice)
- Payment method: Direct bank transfer, PayPal, Managed Payments
- Reserve policy: None (direct payouts)
- Settlement lag: T+1 to T+7 depending on payment method

**Commission Structure:**
| Category | Insertion Fee | Final Value Fee | Payment Fee |
|----------|---------------|-----------------|-------------|
| General | EUR 0.40-5.00 | 12.80% | 0% (Managed) |
| Motors | EUR 0.50 | 6.50% | 0% (Managed) |
| Collectibles | EUR 0.50-15.00 | 12.80% | 0% (Managed) |
| Electronics | EUR 0.30 | 12.80% | 0% (Managed) |

**Payout Formula (Managed Payments):**
```
Sale Price
Less: Final Value Fee (12.80% typical)
Less: Shipping Cost (if discounted)
Less: Returns/Disputes
Less: Listing Fees
= Net Payout (within 1-7 days)
```

**Example:** EUR 50 sale, Electronics
```
Sale Price:                 EUR 50.00
Final Value Fee (12.80%):  (EUR 6.40)
Insertion Fee:             (EUR 0.30)
= Net Payout:               EUR 43.30
(Usually within 2-3 days)
```

**Currencies:** EUR, GBP, PLN on respective country marketplaces; separate accounts.

---

### 2.3 Bol.com (Netherlands, Belgium, Germany)

**Payout Cycles:**
- Payment interval: Weekly (every Monday)
- Payment method: SEPA bank transfer only
- Reserve policy: Rolling 30-day return reserve (percentage varies by category)
- Settlement lag: T+7 to T+37 (depends on return window)

**Commission Structure:**
| Category | Fulfillment (Bol) | Fulfillment (Own) | Return Window |
|----------|-------------------|-------------------|---------------|
| Electronics | 10-20% | 6-12% | 30 days |
| Books | 15% | 8% | 30 days |
| Fashion | 20% | 12% | 30 days |
| Home & Garden | 18% | 10% | 30 days |

**Payout Formula:**
```
Sale Price
Less: Bol Commission (per category)
Less: Return Reserve (held for 30 days post-delivery)
Less: Chargebacks/Disputes
Less: Shipping (if subsidized by Bol)
= Weekly Net Payout (minus reserve)
```

**Example:** EUR 80 fashion item, 30-day return window
```
Sale Price:                 EUR 80.00
Bol Commission (20%):      (EUR 16.00)
Return Reserve (8% = 2.4d): (EUR 6.40)
= Weekly Payout:            EUR 57.60
(Return reserve released after 30 days)
```

**Currencies:** EUR for NL/BE/DE; separate settlement per country.

---

### 2.4 Kaufland (Austria, Czech, Hungary, Romania, Slovakia)

**Payout Cycles:**
- Payment interval: Monthly (usually mid-month)
- Payment method: Bank transfer (SEPA or local)
- Reserve policy: 30-day fulfillment reserve; percentage varies
- Settlement lag: T+30 to T+60

**Commission Structure:**
| Country | Commission Range | Service Fee | Return Days |
|---------|------------------|------------|-------------|
| Austria (AT) | 8-18% | EUR 50/month | 30 |
| Czech (CZ) | 8-18% | CZK 1,200/month | 30 |
| Hungary (HU) | 8-18% | HUF 12,000/month | 30 |
| Romania (RO) | 8-18% | RON 200/month | 14 |
| Slovakia (SK) | 8-18% | EUR 50/month | 30 |

**Payout Formula:**
```
Total Sales Revenue
Less: Commission (8-18% by category)
Less: Service Fee (fixed monthly)
Less: FBA/Fulfillment fees (if applicable)
Less: Return Reserve (30 days)
Less: Chargebacks
= Monthly Net Payout
```

**Example:** EUR 500 sales in Austria (Electronics, 12%)
```
Total Sales:                EUR 500.00
Commission (12%):          (EUR 60.00)
Service Fee (monthly):      (EUR 50.00)
Return Reserve (2%):        (EUR 10.00)
= Monthly Payout:           EUR 380.00
```

**Currencies:** Local currencies per country (CZK, HUF, RON, SKK, EUR).

---

### 2.5 Allegro (Poland)

**Payout Cycles:**
- Payment interval: Weekly (Friday payouts for sales through Sunday)
- Payment method: SEPA transfer or Alior Bank direct
- Reserve policy: None (immediate payout)
- Settlement lag: T+5 to T+10

**Commission Structure:**
| Category | Commission | Transaction Fee | Platform Fee |
|----------|------------|-----------------|---------------|
| Electronics | 5-15% | 1.5% | EUR 0.30/item |
| Fashion | 10-20% | 1.5% | EUR 0.30/item |
| Home & Garden | 8-15% | 1.5% | EUR 0.30/item |
| Books | 5% | 1.5% | EUR 0.20/item |

**Payout Formula:**
```
Sale Price (in PLN or EUR)
Less: Commission (category-dependent)
Less: Transaction Fee (1.5%)
Less: Platform Fee (fixed per item)
Less: Returns/Disputes
= Weekly Net Payout (PLN or EUR)
```

**Example:** PLN 200 fashion item
```
Sale Price:                 PLN 200.00
Commission (15%):          (PLN 30.00)
Transaction Fee (1.5%):    (PLN 3.00)
Platform Fee:              (PLN 1.20)
= Weekly Payout:            PLN 165.80
```

**Currencies:** PLN (primary) or EUR (optional); weekly settlement.

---

### 2.6 Cdiscount (France)

**Payout Cycles:**
- Payment interval: Weekly (Mondays for prior week sales)
- Payment method: SEPA transfer only
- Reserve policy: 15-30 day rolling reserve
- Settlement lag: T+7 to T+37

**Commission Structure:**
| Category | Commission | Service Fee | Fulfillment |
|----------|------------|------------|------------||
| Electronics | 8-12% | EUR 0.15/item | EUR 1.50-3.50 |
| Books | 10% | EUR 0.10/item | EUR 0.80-1.50 |
| Fashion | 15-20% | EUR 0.15/item | EUR 1.50-2.50 |
| Sports | 12% | EUR 0.15/item | EUR 2.00-3.00 |

**Payout Formula:**
```
Sale Price (EUR)
Less: Commission (8-20%)
Less: Service Fee (per item)
Less: Fulfillment Fee (if Cdiscount handles)
Less: Reserve (15-30 days for returns)
= Weekly Payout
```

**Example:** EUR 90 electronics item, self-fulfilled
```
Sale Price:                 EUR 90.00
Commission (10%):          (EUR 9.00)
Service Fee:               (EUR 0.15)
Reserve (2 weeks @ 2%):    (EUR 1.80)
= Weekly Payout:            EUR 79.05
```

**Currencies:** EUR only; weekly settlement.

---

### 2.7 Fnac-Darty (France)

**Payout Cycles:**
- Payment interval: Monthly (mid-month)
- Payment method: Bank transfer (SEPA)
- Reserve policy: 30-day return reserve
- Settlement lag: T+30 to T+60

**Commission Structure:**
| Category | Commission | Fulfillment | Monthly Fee |
|----------|------------|------------|------------||
| Electronics | 10-15% | EUR 2.00-4.00 | EUR 50 |
| Books | 12% | EUR 1.00-2.00 | EUR 50 |
| Fashion | 18-20% | EUR 2.00-3.00 | EUR 50 |
| Toys | 15% | EUR 2.50-3.50 | EUR 50 |

**Payout Formula:**
```
Monthly Sales Total
Less: Commission (10-20%)
Less: Fulfillment Fees
Less: Monthly Service Fee (EUR 50)
Less: Returns/Chargebacks
= Monthly Net Payout (typically mid-month)
```

**Example:** EUR 1,200 monthly sales (Electronics, avg 12% + fulfillment)
```
Monthly Sales:              EUR 1,200.00
Commission (12%):          (EUR 144.00)
Avg Fulfillment (2.5%):    (EUR 30.00)
Monthly Fee:               (EUR 50.00)
Return Reserve (2%):       (EUR 24.00)
= Monthly Payout:           EUR 952.00
```

**Currencies:** EUR; monthly settlement.

---

### 2.8 ManoMano (France, Spain, Germany, Benelux)

**Payout Cycles:**
- Payment interval: Monthly (5th business day of month)
- Payment method: Bank transfer
- Reserve policy: None
- Settlement lag: T+35 average

**Commission Structure (Variable):**
| Category | Commission | Fulfillment | Listing Fee |
|----------|------------|------------|------------||
| Power Tools | 10% | EUR 1.00-3.00 | EUR 0.50 |
| Gardening | 12% | EUR 1.50-3.50 | EUR 0.50 |
| Home Improvement | 15% | EUR 2.00-4.00 | EUR 0.50 |
| Hardware | 8% | EUR 0.80-2.00 | EUR 0.50 |

**Payout Formula:**
```
Total Monthly Sales
Less: Commission (8-15%)
Less: Fulfillment (if ManoMano manages)
Less: Listing Fees
Less: Chargebacks/Returns
= Monthly Payout (5th business day)
```

**Example:** EUR 2,000 Power Tools, self-fulfilled
```
Total Sales:                EUR 2,000.00
Commission (10%):          (EUR 200.00)
Listing Fee (25 items):    (EUR 12.50)
Returns (est 2%):          (EUR 40.00)
= Monthly Payout:           EUR 1,747.50
```

**Currencies:** EUR, USD (some categories); monthly settlement.

---

### 2.9 Leroy Merlin (France, Spain, Portugal, Italy)

**Payout Cycles:**
- Payment interval: Monthly (15th-20th)
- Payment method: Bank transfer
- Reserve policy: 30-day return reserve
- Settlement lag: T+30 to T+60

**Commission Structure:**
| Category | Commission | Service Charge | Returns |
|----------|------------|----------------|----------|
| DIY & Tools | 8-12% | EUR 100/month | 30 days |
| Garden | 10-15% | EUR 100/month | 30 days |
| Home Decoration | 12-18% | EUR 100/month | 30 days |

**Payout Formula:**
```
Monthly Sales Total
Less: Commission (8-18%)
Less: Monthly Service Charge (EUR 100)
Less: Return Reserve (1-2%)
= Net Monthly Payout (15th-20th)
```

**Example:** EUR 3,000 monthly sales (avg 12%)
```
Monthly Sales:              EUR 3,000.00
Commission (12%):          (EUR 360.00)
Service Charge:            (EUR 100.00)
Return Reserve (1%):       (EUR 30.00)
= Monthly Payout:           EUR 2,510.00
```

**Currencies:** EUR, ESP (Spain), PTE (Portugal); monthly settlement.