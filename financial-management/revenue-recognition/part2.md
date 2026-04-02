---

## 3. PAYOUT RECONCILIATION: STEP-BY-STEP PROCESS

### 3.1 Daily/Weekly Reconciliation Workflow

**Step 1: Extract Sales Data from Each Marketplace**
```
Automated via:
- Amazon Seller Central API → Excel/CSV
- eBay Trading API → CSV export
- Bol.com API → JSON feed
- Kaufland backend → Daily export
- Allegro API → XML/JSON
- Cdiscount SFTP → CSV
- Fnac-Darty FTP → XML
- ManoMano dashboard → Excel
- Leroy Merlin portal → XML

Export fields required:
- Order ID / Transaction ID
- Sale Date
- Item SKU
- Sale Price (gross)
- All fees deducted
- Net payout (calculated by marketplace)
- Currency
- Payout date (if settled)
```

**Step 2: Standardize Data Format**
Create unified data structure:
```
| Marketplace | Order_ID | Sale_Date | SKU | Gross | Commission | FBA_Fee | Other_Fees | Net | Currency | Status |
|-------------|----------|-----------|-----|-------|------------|---------|-----------|-----|----------|--------|
| Amazon | 123-4567-8901 | 2026-04-01 | SKU001 | 100.00 | 15.00 | 2.50 | 0.30 | 82.20 | EUR | Settled |
| eBay | 987654321 | 2026-04-01 | SKU002 | 50.00 | 6.40 | 0.00 | 0.30 | 43.30 | EUR | Pending |
| Bol.com | 555-666-777 | 2026-04-01 | SKU003 | 80.00 | 16.00 | 0.00 | 6.40 | 57.60 | EUR | Hold |
```

**Step 3: Validate Fee Calculations**
For each order, verify:
```
Net Payout = Gross - Commission - All_Fees - Returns - Chargebacks

Check by marketplace:
- Amazon: Verify referral % matches product category
- eBay: Confirm Final Value Fee calculated correctly
- Bol.com: Validate 30-day return reserve held correctly
- Kaufland: Ensure monthly service fee applied once per month
- Allegro: Verify transaction fee (1.5%) deducted
```

**Step 4: Currency Conversion (if needed)**
For multi-currency accounts:
```
USD/GBP/PLN/CZK sales → Convert to EUR at transaction date rate
- Source: ECB daily rates or marketplace-provided rates
- Treatment: Recorded as FX gain/loss
- Document: Keep rate used for audit

Example (GBP sale on Amazon.co.uk):
GBP 60.00 sale @ 1.1750 EUR/GBP = EUR 70.50
Commission @ 15% = EUR 10.58
Net to report: EUR 59.93 (converted)
FX rate variance: Track separately for reconciliation
```

**Step 5: Identify & Investigate Discrepancies**
Common issues:
```
Issue: Commission % doesn't match expected
Action: Check if promotional period active, or category misclassification

Issue: FBA fees higher than expected
Action: Verify product dimensions/weight; check if oversized

Issue: Chargebacks/Returns not visible in payout
Action: Review separate disputes section; Bol.com holds in reserve

Issue: Payout arrived late
Action: Verify settlement lag; check for holds/reserves

Issue: Currency conversion variance
Action: Confirm rate used; check if marketplace uses different provider

Issue: Monthly fee charged twice
Action: Verify if seller subscriptions or multi-store accounts
```

**Step 6: Reconcile to Bank Deposits**
```
Sum all marketplace payouts for period:
EUR 82.20 (Amazon)
+ EUR 43.30 (eBay)
+ EUR 57.60 (Bol.com)
+ EUR 380.00 (Kaufland - monthly)
+ EUR 165.80 (Allegro - weekly)
= EUR 729.00 expected

Cross-check against:
- Bank statement deposits (SEPA transfers)
- Timing: Expect deposits within settlement lag + 1-2 banking days
- Amounts: Should match exactly (less any bank fees)

If discrepancy:
1. Verify no pending/held amounts in marketplace dashboards
2. Check for pending refunds/chargebacks
3. Confirm currency conversions on bank side
4. Investigate if partial payout held
```

### 3.2 Monthly Reconciliation Template

| Marketplace | Week 1 | Week 2 | Week 3 | Week 4 | Monthly Total | Fees Total | Commission % | Net Revenue |
|-------------|--------|--------|--------|--------|----------------|------------|--------------|-------------|
| Amazon | EUR 450 | EUR 520 | EUR 480 | EUR 510 | EUR 1,960 | EUR 325 | 16.6% | EUR 1,635 |
| eBay | EUR 180 | EUR 200 | EUR 190 | EUR 210 | EUR 780 | EUR 105 | 13.5% | EUR 675 |
| Bol.com | EUR 320 | EUR 350 | EUR 340 | EUR 370 | EUR 1,380 | EUR 240 | 17.4% | EUR 1,140 |
| Kaufland | EUR 200 | - | - | - | EUR 200 | EUR 30 | 15.0% | EUR 170 |
| Allegro | EUR 280 | EUR 290 | EUR 310 | EUR 300 | EUR 1,180 | EUR 165 | 14.0% | EUR 1,015 |
| Cdiscount | EUR 150 | EUR 160 | EUR 155 | EUR 170 | EUR 635 | EUR 105 | 16.5% | EUR 530 |
| **Total** | **EUR 1,580** | **EUR 1,520** | **EUR 1,475** | **EUR 1,560** | **EUR 6,135** | **EUR 970** | **15.8%** | **EUR 5,165** |

---

## 4. MULTI-MARKETPLACE REVENUE TRACKING

### 4.1 Unified Dashboard Requirements

A centralized system should track:
```
Real-time data feeds from all 8+ marketplaces:
- Orders placed (volume, value)
- Orders shipped (fulfillment rate)
- Orders delivered (customer receipt)
- Returns & refunds (quantity, value)
- Chargebacks & disputes (count, value)
- Account balance (pending, settled)
- Fees applied (commission, service, fulfillment)
```

**Recommended Tools:**
1. **Qonto** (Banking + Aggregation)
   - SEPA settlements aggregated
   - Real-time balance view
   - EUR 50-200/month
   
2. **A2X** (Amazon-centric)
   - Amazon sales/fees/COGS tracking
   - Multi-marketplace support (partial)
   - EUR 50-150/month
   
3. **Link My Books** (Universal)
   - Connects 25+ marketplaces
   - Auto-reconciliation
   - EUR 80-200/month
   
4. **Xero** (Full ERP)
   - Marketplace integrations
   - Revenue recognition automation
   - EUR 150-300/month
   
5. **Custom API Integration**
   - Python/JavaScript scripts
   - Direct marketplace API connections
   - One-time setup, ongoing maintenance

### 4.2 Multi-Currency Consolidation

EUR, GBP, PLN, SEK, CZK, CHF sales require:
```
1. Daily conversion to base currency (EUR)
2. Track original currency for audit trail
3. Record FX gains/losses separately
4. Apply accounting policy (spot rate vs average rate)

Example 4-day period:
Date | Marketplace | Currency | Amount | EUR Rate | EUR Equivalent | FX Diff |
2026-04-01 | Amazon.co.uk | GBP | 100.00 | 1.1750 | 117.50 | - |
2026-04-02 | Allegro.pl | PLN | 500.00 | 0.2350 | 117.50 | +0.10 |
2026-04-03 | Amazon.se | SEK | 1,000.00 | 0.0890 | 89.00 | -0.50 |
2026-04-04 | Kaufland.cz | CZK | 2,000.00 | 0.0410 | 82.00 | -1.20 |

Total EUR equivalent: EUR 406.00
FX variance: EUR -1.60 (loss)
```

### 4.3 Standardized Reporting Structure

**Monthly P&L by Marketplace:**
```
                  Amazon    eBay     Bol.com   Kaufland  Allegro   Total
Sales Revenue     EUR 1,960 EUR 780  EUR 1,380 EUR 200   EUR 1,180 EUR 5,500
Less: Commission  (EUR 325) (EUR 105) (EUR 240) (EUR 30)  (EUR 165) (EUR 865)
Less: Fulfillment (EUR 85)  (EUR 0)  (EUR 0)   (EUR 0)   (EUR 0)   (EUR 85)
Less: Fees/Other  (EUR 45)  (EUR 0)  (EUR 85)  (EUR 0)   (EUR 0)   (EUR 130)
Net Payout        EUR 1,505 EUR 675  EUR 1,055 EUR 170   EUR 1,015 EUR 4,420

Payout %, excl COGS: 76.8%   86.5%    76.4%     85.0%    85.9%    80.4%
```

---

## 5. COMMISSION & FEE TRACKING

### 5.1 Fee Taxonomy by Marketplace

**Amazon:**
- Referral Fee: 5-45% (varies by category)
- FBA Fees: EUR 0.15-3.50 per unit (size/weight-based)
- Subscription: EUR 39.99/month or Professional
- PPC Advertising: Variable (pay-per-click)
- Storage Overage: EUR 10.50/m³ (Jan-Sep), EUR 52.50/m³ (Oct-Dec)

**eBay:**
- Insertion Fee: EUR 0.30-5.00 per item
- Final Value Fee: 12.80% (typical)
- Payment Processing: 0% (Managed Payments)
- International Site Visibility: EUR 3.00/item
- Gallery: EUR 0.15 per item

**Bol.com:**
- Commission: 6-20% (fulfillment-dependent)
- Return Reserve: 2-8% (held 30 days)
- Listing Delay Fee: EUR 0.05/item/day (if no stock)
- Premium Analytics: EUR 50/month

**Kaufland:**
- Commission: 8-18% (category-dependent)
- Monthly Service Fee: EUR 50 (AT/SK), CZK 1,200 (CZ), etc.
- FBA Equivalent: 3-5% additional
- Chargeback Fee: EUR 5-10 per dispute

**Allegro:**
- Commission: 5-15% (category-dependent)
- Transaction Fee: 1.5% (all sales)
- Item Fee: EUR 0.20-0.30 per item
- ProSeller Subscription: EUR 30-100/month (optional)

**Cdiscount:**
- Commission: 8-20% (category-dependent)
- Service Fee: EUR 0.10-0.15 per item
- Fulfillment Fee: EUR 0.80-3.50 per unit (if Cdiscount handles)
- Return Reserve: 15-30 days rolling

**Fnac-Darty:**
- Commission: 10-20% (category-dependent)
- Monthly Subscription: EUR 50
- Fulfillment Fee: EUR 1.00-4.00 per unit
- Return Reserve: 30 days (2-5% held)

**ManoMano:**
- Commission: 8-15% (category-dependent)
- Fulfillment: EUR 1.00-4.00 per order
- Listing Fee: EUR 0.50 per item
- Premium Account: EUR 100-500/month

**Leroy Merlin:**
- Commission: 8-18% (category-dependent)
- Monthly Service: EUR 100
- Return Reserve: 30 days (1-2%)
- Category Exclusivity: EUR 50-200/month

### 5.2 True Net Revenue Calculation Formula

```
True Net Revenue Per Order = 
  (Sale Price - Marketplace Commission - Payment Processing Fees 
   - Fulfillment Costs - Platform Fees - Chargebacks/Returns) 
   ÷ (Sales Volume)

Example Order: EUR 100 sale on Amazon Electronics (FBA)
  Sale Price:                           EUR 100.00
  Less: Referral Fee (15%):            (EUR 15.00)
  Less: FBA Fee (weight-based):        (EUR 2.50)
  Less: Payment Processing (0.3%+fee): (EUR 0.30)
  Less: Storage/Overhead allocation:   (EUR 1.00)
  = True Net Revenue:                   EUR 81.20
  
  Payout %: 81.2%
  Effective all-in cost: 18.8%
```

**Fee Calculator Matrix:**

| Marketplace | Category | Sale Price | Commission | FBA/Fulfillment | Other | Net Payout | % |
|-------------|----------|-----------|-----------|-----------------|-------|-----------|-----|
| Amazon | Electronics | EUR 100 | EUR 15 | EUR 2.50 | EUR 0.30 | EUR 82.20 | 82.2% |
| eBay | General | EUR 50 | EUR 6.40 | EUR 0 | EUR 0.30 | EUR 43.30 | 86.6% |
| Bol.com | Fashion | EUR 80 | EUR 16 | EUR 0 | EUR 6.40 | EUR 57.60 | 72.0% |
| Kaufland | Electronics | EUR 120 | EUR 14.40 | EUR 0 | EUR 5 | EUR 100.60 | 83.8% |
| Allegro | Home & Garden | EUR 200 | EUR 30 | EUR 0 | EUR 3 | EUR 167 | 83.5% |
| Cdiscount | Electronics | EUR 90 | EUR 9 | EUR 2 | EUR 1.35 | EUR 76.65 | 85.2% |
| Fnac-Darty | Books | EUR 25 | EUR 3 | EUR 1.50 | EUR 2.50 | EUR 18 | 72.0% |
| ManoMano | Power Tools | EUR 150 | EUR 15 | EUR 2 | EUR 0.50 | EUR 132.50 | 88.3% |

---

## 6. REVENUE ANALYTICS & FORECASTING

### 6.1 Revenue Models by Marketplace

**Amazon Model:**
```
Factors:
- Seasonality: +40% Q4, -20% Q1
- ACoS (Advertising Cost of Sale): 15-25% for PPC
- Conversion Rate: 3-8% average
- Average Order Value: EUR 45-80

Forecast:
Base Monthly: EUR 2,000
Q4 Multiplier: 1.40 = EUR 2,800
Minus ACoS impact (20%): EUR 2,240
= Estimated Q4 Month Revenue

Tracking:
- Daily sales volume & value
- Conversion rate by keyword
- ACoS trend
- Inventory turnover
```

**eBay Model:**
```
Factors:
- Category demand cycles
- Auction vs. Buy-It-Now mix (15% premium BIN)
- Average sale duration: 7 days
- Repeat buyer rate: 25-35%

Forecast:
Active listings × Conversion rate × Avg Price
= Monthly revenue

Current: 500 listings × 5% conversion × EUR 40
= EUR 1,000/month base
+ 30% from repeat buyers
= EUR 1,300 expected
```

**Bol.com Model:**
```
Factors:
- Customer acquisition cost: EUR 8-15 per customer
- Repeat rate: 40-50%
- Return rate: 15-25% (impacting net)
- Seasonal peaks: Black Friday (+200%), Christmas (+150%)

Cohort Analysis:
Customers acquired in Jan: 50% repeat by June
Customers acquired in May: 40% repeat by December
Lifetime value: EUR 120-180 per customer (3-4 orders)

Forecast for new customer month:
100 new customers × EUR 40 AOV = EUR 4,000 sales
Less 20% returns = EUR 3,200 net
Plus 45% repeat (next 6 months) = EUR 4,640 lifetime
```

**Multi-Marketplace Blended Model:**
```
Aggregate all platforms:
Month 1 baseline: EUR 5,000
Seasonal adjustment: ×1.15 (spring uptick)
= EUR 5,750

Competition factor: ×0.95 (new competitors in category)
= EUR 5,463 base forecast

Add/subtract for:
+ New product launches
+ Promotional campaigns (estimate lift)
+ Inventory availability
- Stockouts
- Returns/chargebacks

Final forecast: EUR 5,500 ±10%
```

### 6.2 Cohort Analysis: Customer Retention

Track customer LTV by acquisition source:

```
Cohort   Acquired  Month 1  Month 2  Month 3  Month 4  Month 5  Month 6  LTV
Jan      100 cust  EUR 4,000 EUR 1,600 EUR 800  EUR 400  EUR 300  EUR 200  EUR 7,300
Feb      120 cust  EUR 4,800 EUR 1,920 EUR 960 EUR 480  EUR 360  EUR 240  EUR 8,760
Mar      150 cust  EUR 6,000 EUR 2,400 EUR 1,200 EUR 600 EUR 450  EUR 300  EUR 10,950
Apr      130 cust  EUR 5,200 EUR 2,080 EUR 1,040 EUR 520 EUR 390  -        EUR 9,230
May      140 cust  EUR 5,600 EUR 2,240 EUR 1,120 EUR 560 -        -        EUR 9,520

Retention rate by month: 40%, 50%, 50%, 75% (varies by cohort)
Average LTV: EUR 9,132
Payback period (at EUR 10 CAC): 2-3 months
```

### 6.3 Seasonality Index

```
Month     Amazon  eBay   Bol.com  Kaufland  Allegro  Average  Adjusted
January   0.85    0.80   0.75     0.82      0.78     0.80     (EUR 5,200)
February  0.88    0.85   0.80     0.87      0.82     0.84     (EUR 5,460)
March     0.92    0.90   0.85     0.90      0.88     0.89     (EUR 5,785)
April     0.95    0.93   0.90     0.93      0.91     0.92     (EUR 5,980)
May       1.00    0.98   0.98     0.99      0.98     0.99     (EUR 6,435)
June      1.05    1.02   1.05     1.03      1.04     1.04     (EUR 6,760)
July      1.10    1.08   1.10     1.08      1.12     1.10     (EUR 7,150)
August    0.98    0.95   0.93     0.96      0.94     0.95     (EUR 6,175)
September 1.02    1.00   1.00     1.02      1.01     1.01     (EUR 6,565)
October   1.30    1.28   1.35     1.32      1.30     1.31     (EUR 8,515)
November  1.70    1.65   1.75     1.68      1.72     1.70     (EUR 11,050)
December  1.95    1.90   2.00     1.92      1.95     1.94     (EUR 12,610)

Base monthly assumption: EUR 6,500
Highest month (Dec): EUR 12,610
Lowest month (Jan): EUR 5,200
Seasonality range: 80%-194% of average
```

---

## 7. DEFERRED REVENUE & PREPAYMENTS

### 7.1 Gift Cards & Store Credits

**Recognition Principle:**
Gift cards represent future performance obligations. Revenue recognized when:
1. Customer redeems the gift card, OR
2. Refund obligation expires (typically 12-60 months)

**Accounting Treatment:**

```
When gift card sold for EUR 50:
Dr. Cash                EUR 50.00
    Cr. Deferred Revenue / Gift Card Liability  EUR 50.00

When customer uses gift card to buy EUR 50 product:
Dr. Deferred Revenue    EUR 50.00
    Cr. Revenue         EUR 50.00

Dr. COGS                EUR 25.00
    Cr. Inventory       EUR 25.00

If gift card expires (never redeemed after 60 months):
Dr. Deferred Revenue    EUR 50.00
    Cr. Revenue         EUR 50.00
(Typically: 2-5% of issued gift cards never redeemed)
```

**Balance Sheet Impact:**
```
Issued gift cards (lifetime): EUR 10,000
Redeemed to date: EUR 8,500
Expired/forfeited: EUR 800
Outstanding liability: EUR 700

Liability account: Deferred Gift Card Revenue
Recognition: As redeemed or expired
```

### 7.2 Customer Prepayments & Escrow

Prepayments on marketplace preorders:

```
Preorder received: EUR 100 prepayment for product launching Q2
Dr. Cash                EUR 100.00
    Cr. Deferred Revenue - Preorders  EUR 100.00

When product ships (Q2):
Dr. Deferred Revenue    EUR 100.00
    Cr. Revenue         EUR 100.00

Dr. COGS                EUR 60.00
    Cr. Inventory       EUR 60.00
```

**Disclosure Requirement:**
- Line item on balance sheet or note
- Typical values: EUR 3,000-15,000 (2-3 months forward)
- Review monthly; match to actual shipments

---

## 8. REVENUE REPORTING & MANAGEMENT TEMPLATES

### 8.1 Monthly P&L Statement (Multi-Marketplace)

```
VENDIAMONOI.IT — MONTHLY P&L STATEMENT
Period: April 2026

REVENUE:
  Amazon EU              EUR 1,960.00
  eBay International     EUR 780.00
  Bol.com (NL/BE/DE)     EUR 1,380.00
  Kaufland (AT/CZ/HU)    EUR 200.00
  Allegro (Poland)       EUR 1,180.00
  Cdiscount (France)     EUR 635.00
  Fnac-Darty            EUR 450.00
  ManoMano              EUR 950.00
  Leroy Merlin          EUR 300.00
  Other / Direct        EUR 165.00
  ─────────────────────────────────
  TOTAL GROSS REVENUE             EUR 8,000.00

COST OF GOODS SOLD:
  Inventory (beginning)          EUR 3,500.00
  + Purchases                    EUR 3,200.00
  - Inventory (ending)           (EUR 3,100.00)
  ─────────────────────────────────
  COGS                                       EUR 3,600.00
  ─────────────────────────────────
GROSS PROFIT                                  EUR 4,400.00
GROSS MARGIN %                               55.0%

MARKETPLACE FEES & COMMISSIONS:
  Amazon Referral Fees           (EUR 325.00)
  Amazon FBA Fees                (EUR 85.00)
  eBay Final Value Fees          (EUR 105.00)
  Bol.com Commission & Reserve   (EUR 240.00)
  Kaufland Commission            (EUR 30.00)
  Allegro Fees                   (EUR 165.00)
  Cdiscount Fees                 (EUR 105.00)
  Fnac-Darty Subscription        (EUR 50.00)
  Other Marketplace Fees         (EUR 120.00)
  ─────────────────────────────────
  TOTAL MARKETPLACE FEES                  (EUR 1,225.00)

OPERATING EXPENSES:
  Advertising (PPC, Sponsored)   (EUR 200.00)
  Packaging & Shipping Supplies  (EUR 150.00)
  Salaries & Contractors         (EUR 800.00)
  Office/Warehouse Rent          (EUR 400.00)
  Utilities                      (EUR 80.00)
  Software/Tools (A2X, Xero, etc)(EUR 100.00)
  Bank/Payment Processing Fees   (EUR 60.00)
  ─────────────────────────────────
  TOTAL OPERATING EXPENSES                (EUR 1,790.00)

EBITDA                                       EUR 1,385.00
EBITDA %                                     17.3%

DEPRECIATION & AMORTIZATION                 (EUR 50.00)

OPERATING PROFIT (EBIT)                      EUR 1,335.00
EBIT %                                       16.7%

OTHER INCOME/(EXPENSES):
  FX Gains/(Losses)              EUR 15.00
  Interest Income                EUR 5.00
  ─────────────────────────────────
  Total Other Income                         EUR 20.00

PROFIT BEFORE TAX (EBT)                      EUR 1,355.00

CORPORATE INCOME TAX (24% IT)               (EUR 325.20)

NET PROFIT                                   EUR 1,029.80
NET MARGIN %                                 12.9%
```

### 8.2 Revenue Waterfall Chart (Sample)

```
Starting point: EUR 8,000 Gross Revenue
├─ Amazon: EUR 1,960 (24.5%)
├─ eBay: EUR 780 (9.8%)
├─ Bol.com: EUR 1,380 (17.3%)
├─ Kaufland: EUR 200 (2.5%)
├─ Allegro: EUR 1,180 (14.8%)
├─ Cdiscount: EUR 635 (7.9%)
├─ Fnac-Darty: EUR 450 (5.6%)
├─ ManoMano: EUR 950 (11.9%)
├─ Leroy Merlin: EUR 300 (3.8%)
└─ Other: EUR 165 (2.1%)

Less: Marketplace Commissions (EUR 1,225)
= Net Revenue After Commissions: EUR 6,775

Less: COGS (EUR 3,600)
= Gross Profit: EUR 4,400

Less: Operating Expenses (EUR 1,790)
= EBITDA: EUR 1,385

Less: Depreciation (EUR 50)
= Operating Profit: EUR 1,335

Plus/Less: Other Income (EUR 20)
= EBT: EUR 1,355

Less: Tax (EUR 325)
= Net Profit: EUR 1,030
```

### 8.3 Journal Entry Templates

**Monthly Revenue Recognition (Principal):**
```
Apr 30, 2026
Dr. Accounts Receivable / Cash    EUR 8,000.00
    Cr. Revenue - Marketplace Sales             EUR 8,000.00
    (To record April gross revenue from 8 marketplaces)

Dr. Cost of Goods Sold             EUR 3,600.00
    Cr. Inventory                               EUR 3,600.00
    (To record COGS for April)

Dr. Marketplace Fees Expense      EUR 1,225.00
    Cr. Accounts Payable                       EUR 1,225.00
    (To accrue marketplace fees for April settlement)
```

**Reserve Holdbacks (Deferred Recognition):**
```
Apr 30, 2026 (Bol.com 30-day return reserve)
Dr. Revenue - Bol.com              EUR 240.00
    Cr. Deferred Revenue - Bol.com Return Reserve EUR 240.00
    (To defer 30-day return reserve estimate)

May 30, 2026 (Release April reserve)
Dr. Deferred Revenue - Bol.com    EUR 240.00
    Cr. Revenue - Bol.com                      EUR 240.00
    (To recognize April sales, reserve released)
```

**Currency Conversion (Weekly from USD/GBP/PLN):**
```
Apr 15, 2026
Dr. Cash                           EUR 1,575.00
Dr. Accounts Receivable            EUR 50.00
    Cr. Revenue - Amazon.com (GBP sales)       EUR 1,200.00
    Cr. FX Gain - Currency Adjustment          EUR 425.00
    (GBP 1,000 @ 1.1750, actual 1.1900 rate)
```

### 8.4 Payout Reconciliation Journal

```
Week of Apr 1-7, 2026

Payout received from Amazon: EUR 1,050.00
Dr. Cash                     EUR 1,050.00
    Cr. Accounts Receivable - Amazon            EUR 1,050.00
    (Amazon 14-day settlement received)

Payout received from eBay: EUR 240.00
Dr. Cash                     EUR 240.00
    Cr. Accounts Receivable - eBay              EUR 240.00
    (eBay daily payout received)

Payout received from Bol.com: EUR 890.00
Dr. Cash                     EUR 890.00
    Cr. Accounts Receivable - Bol.com           EUR 890.00
    (Bol.com weekly payout received; return reserve held)

Payout received from Allegro: EUR 580.00
Dr. Cash                     EUR 580.00
    Cr. Accounts Receivable - Allegro           EUR 580.00
    (Allegro weekly payout received)

TOTAL CASH RECEIVED:                            EUR 2,760.00
```

---

## CONCLUSION

Domain 5.1 Revenue Recognition & Marketplace Payouts requires integration of:
- **IFRS 15 compliance** for proper revenue timing
- **Principal vs agent classification** per marketplace
- **Multi-currency handling** across EU operations
- **Fee tracking** to calculate true net revenue
- **Reserve management** for return windows
- **Unified reporting** across 20+ marketplaces
- **Monthly reconciliation** to ensure accuracy

Recommended tools for Vendiamonoi.it:
1. **A2X** (Amazon + some marketplace support)
2. **Link My Books** (universal marketplace connector)
3. **Xero** (ERP backbone with integrations)
4. **Qonto** (SEPA settlement aggregation)
5. **Custom scripts** (for complex multi-currency conversion)

Regular quarterly review with accountant ensures compliance and identifies optimization opportunities for commission reduction and margin improvement.