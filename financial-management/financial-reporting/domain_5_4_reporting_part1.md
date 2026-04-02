# Domain 5.4 — Financial Reporting & Analytics
## Italian S.R.L. Digital Marketplace Distribution

---

## 1. ITALIAN ACCOUNTING STANDARDS (OIC)

### OIC 12 — Leasing
- Capital vs operating lease classification
- Vendiamonoi.it warehouse equipment leases recorded as assets
- Monthly depreciation tracking per OIC guidelines

### OIC 13 — Revenues
- Marketplace revenue recognition at delivery completion (DDP terms)
- Net of returns, refunds, chargebacks within accrual period
- Revenue by marketplace: Amazon, eBay, Cdiscount, Zalando, Vinted, etc.
- Gross revenue → net revenue after deductions calculation

### OIC 15 — Inventories
- FIFO method for 100+ supplier products
- Monthly inventory valuation at lower of cost/net realizable value
- Obsolescence reserve for slow-moving items >6 months
- Work-in-progress on returns/damaged goods

### OIC 19 — Provisions & Contingencies
- Chargeback provisions (typically 1.5-2.5% of GMV)
- Return reserve (8-12% estimate for fashion marketplace)
- Legal contingencies for supplier disputes

### OIC 25 — Materiality & Estimates
- Materiality threshold: 2.5% of gross profit
- Key estimates: doubtful receivables (5%), stock obsolescence (3%), accrued returns (10%)

### Bilancio Abbreviato vs Ordinario
**Abbreviated Balance Sheet (Bilancio Abbreviato):**
- Available if: €1.6M revenue AND €800K assets AND ≤10 employees
- Simplified notes disclosure
- Reduced audit requirements
- VAT monthly filing still required (monthly or quarterly)

**Ordinary Balance Sheet (Bilancio Ordinario):**
- Vendiamonoi.it uses ordinario (exceeds €2M revenue threshold)
- Full detailed notes
- Auditor review required (Revisore Legale)
- Cash flow statement included
- Quarterly VAT reporting

---

## 2. CHART OF ACCOUNTS — PIANO DEI CONTI

### ASSETS (Attivo)

**1. Current Assets (Attivo Circolante)**
- 1100 Cash & Bank Accounts
  - 1110 Bank Account EUR (Primary)
  - 1120 PayPal Business Account
  - 1130 Stripe Settlement Account
  - 1140 Petty Cash
  
- 1200 Accounts Receivable
  - 1210 Receivables from Amazon (net of adjustments)
  - 1220 Receivables from eBay
  - 1230 Receivables from Cdiscount
  - 1240 Receivables from Zalando
  - 1250 Receivables from Other Marketplaces
  - 1260 Doubtful Receivables Reserve (-) [Negative]

- 1300 Inventory
  - 1310 Raw Materials & Finished Goods
  - 1320 Work-in-Progress (returns, refurbishment)
  - 1330 Inventory Obsolescence Reserve (-)

- 1400 Prepaid Expenses
  - 1410 Platform Subscription Prepaid
  - 1420 Insurance Prepaid
  - 1430 Software Licenses Prepaid

**2. Fixed Assets (Attivo Fisso)**
- 2100 Tangible Assets
  - 2110 Warehouse Equipment
  - 2120 Office Equipment
  - 2130 IT Hardware (Servers, Computers)
  - 2140 Accumulated Depreciation (-) [Negative]

- 2200 Intangible Assets
  - 2210 Software & IT Development
  - 2220 Brand & Trademark
  - 2230 Accumulated Amortization (-)

- 2300 Financial Assets
  - 2310 Deposits & Guarantees
  - 2320 Investments in Associates

---

### LIABILITIES (Passivo)

**3. Current Liabilities (Passivo Circolante)**
- 3100 Accounts Payable
  - 3110 Payables to Suppliers (100+ suppliers)
  - 3120 Payables to Logistics Partners
  - 3130 Payables to Marketplaces (chargebacks, fees)

- 3200 Tax Payables
  - 3210 VAT Payable (IVA - typically 22%)
  - 3220 Personal Income Tax Payable (Irpef)
  - 3230 Corporate Tax Payable (IRES)

- 3300 Accrued Liabilities
  - 3310 Accrued Returns Reserve (reverse value of expected returns)
  - 3320 Accrued Chargebacks Reserve
  - 3330 Accrued Wages & Bonuses
  - 3340 Accrued Utilities

- 3400 Short-term Debt
  - 3410 Bank Loans <12 months
  - 3420 Credit Lines

**4. Long-term Liabilities (Passivo Consolidato)**
- 4100 Long-term Debt
  - 4110 Bank Mortgages (warehouse lease financing)
  - 4120 Equipment Financing

- 4200 Deferred Tax Liabilities
  - 4210 Deferred Tax on Inventory Revaluation

---

### EQUITY (Patrimonio Netto)

**5. Equity**
- 5100 Share Capital
  - 5110 Authorized & Issued Capital (€50,000 typical for S.R.L.)

- 5200 Reserves
  - 5210 Legal Reserve (20% of profit, mandatory)
  - 5220 Statutory Reserves
  - 5230 Extraordinary Reserves
  - 5240 Revaluation Reserves

- 5300 Retained Earnings / Accumulated Losses
  - 5310 Prior Year Profit
  - 5320 Current Year Profit/Loss

---

### REVENUE (Ricavi)

**6. Operating Revenue (Ricavi Operativi)**
- 6000 Gross Revenue by Marketplace
  - 6100 Amazon.it Revenue
    - 6110 Amazon EU Revenue (cross-border)
    - 6111 Amazon Prime Revenue (qualified fulfillment)
  - 6200 eBay Revenue
  - 6300 Cdiscount Revenue
  - 6400 Zalando Revenue
  - 6500 Vinted Revenue
  - 6600 Other Marketplace Revenue (Etsy, Wish, Shopee, etc.)

- 6700 Returns & Refunds (-) [Negative]
  - 6710 Product Returns Deduction
  - 6720 Chargeback Deductions
  - 6730 Refund Processing Fees

- 6800 Other Income
  - 6810 Interest Income
  - 6820 Currency Gains
  - 6830 Miscellaneous Income

---

### COST OF GOODS SOLD (COGS)

**7. Costs (Costi)**
- 7000 Cost of Goods Sold
  - 7100 Purchase Cost of Inventory Sold (by supplier or category)
  - 7110 Product A Category COGS
  - 7120 Product B Category COGS
  - 7200 Inventory Obsolescence & Write-offs
  - 7300 Freight & Logistics Inbound
    - 7310 Inbound from Suppliers (EU warehousing costs)
    - 7320 Inbound Duties & Tariffs

- 7400 Marketplace Fees & Commissions
  - 7410 Amazon Referral Fees (8-45% by category)
  - 7411 Amazon Fulfillment Fees (FBA)
  - 7420 eBay Commission (12.9% standard)
  - 7430 Cdiscount Commission (7-15%)
  - 7440 Zalando Commission (15-25%)
  - 7450 Other Marketplace Commissions

- 7500 Payment Processing Fees
  - 7510 Stripe/PayPal fees (1.5-3%)
  - 7520 Chargeback/Refund Processing Fees

- 7600 Fulfillment & Logistics
  - 7610 Outbound Shipping (DHL, DPD, Poste)
  - 7620 Last-Mile Delivery Partners
  - 7630 Returns Processing & Reverse Logistics
  - 7640 Warehouse Storage (€0.10-0.30/unit/month)

- 7700 Product Compliance & Quality
  - 7710 Certification Costs
  - 7720 Product Testing & Inspection
  - 7730 Packaging Materials
  - 7740 Labeling & Barcode Costs

---

### OPERATING EXPENSES (OpEx)

**8. Personnel Costs (Costi del Personale)**
- 8100 Employee Salaries & Wages
  - 8110 Operations Manager
  - 8120 Buyer/Merchandise Manager
  - 8130 Fulfillment Staff
  - 8140 Customer Service Staff

- 8200 Social Charges & Contributions (INPS/INAIL)
  - 8210 Employer Contributions (~35% of salary)

- 8300 Bonuses & Incentives
  - 8310 Performance Bonuses

---

**9. Sales & Marketing (Commerciale)**
- 9100 Paid Advertising
  - 9110 Amazon Advertising (Sponsored Products, Stores)
  - 9120 eBay Promoted Listings
  - 9130 Google Ads / SEO
  - 9140 Facebook/Instagram Ads
  - 9150 Content Marketing

- 9200 Promotional Discounts
  - 9210 Marketplace Flash Sales
  - 9220 Coupon & Voucher Costs

- 9300 Affiliate & Influencer Marketing
  - 9310 Commission to Affiliates

---

**10. Technology & Digital (Tecnologia)**
- 10100 Software & Subscriptions
  - 10110 ERP/Accounting Software (Fatture in Cloud, Xero)
  - 10120 BI/Analytics Tools (Looker, Metabase, Data Studio)
  - 10130 Marketplace Management Tools (Sellerboard, A2X)
  - 10140 CRM & Customer Service Software
  - 10150 Website & E-commerce Platform

- 10200 IT Services & Support
  - 10210 Developer/IT Contractor Costs
  - 10220 Cloud Hosting & Server Costs
  - 10230 Data Security & Backup Services

- 10300 Telecommunications
  - 10310 Internet & Connectivity
  - 10320 Mobile Phones & Plans

---

**11. General & Administrative (G&A)**
- 11100 Professional Services
  - 11110 Accountant/Tax Advisor Fees
  - 11120 Legal Services
  - 11130 Audit Fees (Revisore Legale)
  - 11140 Consulting & Business Advisory

- 11200 Office Expenses
  - 11210 Office Rent/Lease
  - 11220 Utilities (Electricity, Water, Gas)
  - 11230 Office Supplies & Materials
  - 11240 Equipment Maintenance

- 11300 Insurance
  - 11310 Product Liability Insurance
  - 11320 General Liability Insurance
  - 11330 Director & Officer Insurance
  - 11340 Cyber/Data Breach Insurance

- 11400 Travel & Transportation
  - 11410 Business Travel
  - 11420 Vehicle Maintenance & Fuel
  - 11430 Vehicle Insurance

- 11500 Miscellaneous Expenses
  - 11510 Bank Fees & Charges
  - 11520 Currency Exchange Losses
  - 11530 Donations & Charitable Contributions
  - 11540 Training & Development

---

**12. Depreciation & Amortization**
- 12100 Depreciation Expense (Ammortamento)
  - 12110 Equipment Depreciation (10-15% per annum)
  - 12120 IT Hardware Depreciation (25-33%)
  - 12130 Warehouse Improvements Depreciation (10%)

- 12200 Amortization Expense
  - 12210 Software Amortization (3-5 years)
  - 12220 Intangible Assets Amortization

---

**13. Finance Costs**
- 13100 Interest Expense
  - 13110 Bank Loan Interest
  - 13120 Credit Line Interest

- 13200 Currency & Exchange Losses
  - 13210 FX Loss on Receivables
  - 13220 FX Loss on Payables

---

### TAX & FINAL ACCOUNTS

**14. Tax Expense (Imposte)**
- 14100 Corporate Income Tax (IRES) — 24% standard
  - 14110 Current Year IRES Expense
  - 14120 Prior Year Tax Adjustments

- 14200 Regional Tax (IRAP) — ~3.9% in most regions
  - 14210 IRAP Expense

- 14300 Deferred Tax (Imposte Differite)

---

**15. P&L Bottom Lines**
- 15100 Gross Profit (Utile Lordo)
- 15200 Operating Profit/EBIT (Utile Operativo)
- 15300 Profit Before Tax (Utile Prima delle Imposte)
- 15400 Net Profit/Loss (Utile Netto)

---

## 3. MONTHLY CLOSE — STEP-BY-STEP T+5

### Day 1-2: DATA IMPORT & RECONCILIATION

**Step 1: Export Data from Marketplaces**
```
1. Amazon Seller Central
   - Transaction reports (daily settlement)
   - Referral fees, FBA fees, promotional adjustments
   - Refund/chargeback data
   - Export to: ledger_amazon_[YYYY-MM].csv

2. eBay Seller Hub
   - Sales activity report
   - Fee breakdown
   - Payouts received
   - Export: ledger_ebay_[YYYY-MM].csv

3. Cdiscount/Zalando/Other
   - Order & revenue reports
   - Commission deductions
   - Export: ledger_marketplace_[YYYY-MM].csv

4. Bank Statements
   - Daily settlement files from each marketplace
   - Internal transfers
   - Export: bank_statement_[YYYY-MM].csv
```

**Step 2: Import into Accounting System**
```
Action: Upload CSVs to Fatture in Cloud or Xero
- Match transaction dates
- Verify total amounts vs. bank
- Tag by account (6100, 6200, etc.)
- Mark any discrepancies for investigation
```

**Step 3: Reconciliation Checklist**
```
□ Bank reconciliation: Compare GL cash balance vs. actual bank statement
□ Marketplace receivables: Calculate expected vs. actual payouts
□ Revenue reconciliation: Gross revenue (6000-6700 sum) matches marketplace reports
□ Commission verification: 7410-7450 match rate cards (e.g., 15% Amazon)
□ Returns match: Count of returns in system vs. marketplace return reports
```

### Day 2-3: ACCRUALS & ADJUSTMENTS

**Step 4: Record Accruals**
```
A. Returns Reserve Accrual
   Debit: 6710 Returns & Refunds (P&L)
   Credit: 3310 Accrued Returns Reserve (Balance Sheet)
   Amount: 10% × (Net Revenue last 30 days)
   Rationale: Per OIC 13, reverse expected returns not yet received

B. Chargeback Reserve Accrual
   Debit: 6720 Chargeback Deductions (P&L)
   Credit: 3320 Accrued Chargebacks Reserve (Balance Sheet)
   Amount: 2.0% × (Gross Revenue current month)
   Rationale: Historical chargeback rate

C. Doubtful Receivables Reserve
   Debit: OpEx line (G&A) or as contra-revenue
   Credit: 1260 Doubtful Receivables Reserve (Balance Sheet)
   Amount: 5% × (Total receivables >60 days)
   Rationale: Marketplace disputes, delayed settlements

D. Inventory Obsolescence
   Debit: 7200 Inventory Write-offs (P&L)
   Credit: 1330 Inventory Obsolescence Reserve (Balance Sheet)
   Amount: 3% × (Inventory units not sold >90 days)
   Rationale: Slow-moving SKUs from fashion/trending products

E. Accrued Wages (if monthly accrual)
   Debit: 8100/8200 Personnel Costs
   Credit: 3330 Accrued Wages
   Amount: Payroll due end of following month
```

**Step 5: Marketplace-Specific Adjustments**
```
Amazon Specific:
- Clawback reserve: 0.5% of FBA revenue (chargebacks post-settlement)
- Closing fees adjustment: Monthly reconciliation

eBay Specific:
- Final value fee reconciliation
- Managed payments payout variance

Other Marketplaces:
- Commission true-ups from prior months
- Currency adjustments (EUR settlements vary)
```

### Day 3-4: INVENTORY & FIXED ASSETS

**Step 6: Inventory Count & Revaluation**
```
Action: Physical or perpetual system count
- Reconcile system units vs. actual warehouse
- Record shrinkage/loss to 7200
- Update inventory GL value (1310/1320)
- Calculate days inventory on hand (DIO = Inventory / Daily COGS)
  Example: €120,000 inventory / €6,000 daily COGS = 20 days

Inventory Turnover = COGS / Avg Inventory
  Example: €180,000 COGS / €120,000 inventory = 1.5x per month = 18x per year
```

**Step 7: Fixed Asset Review**
```
- Depreciation calculation (monthly accrual):
  Depreciation = Asset Cost / Useful Life
  Example: Equipment €15,000 / 10 years = €125/month
  Debit: 12110 Depreciation Expense
  Credit: 2140 Accumulated Depreciation (contra-asset)

- Review for impairment:
  If FV < BV, record loss:
  Debit: G&A expense
  Credit: 2110/2120 Asset write-down
```

### Day 4: TRIAL BALANCE & RECONCILIATION

**Step 8: Trial Balance Preparation**
```
Extract trial balance from GL:
- All accounts (1100-15400) with debit/credit balances
- Total debits = Total credits (must balance)
- Typical trial balance template:

Account                      Debit (€)     Credit (€)
1100 Bank                    245,000
1200 AR - Amazon             180,000
1300 Inventory              120,000
2110 Equipment              80,000
2140 Accumulated Dep.                     (15,000)
3110 AP - Suppliers                       (95,000)
3210 VAT Payable                          (18,000)
5110 Share Capital                        (50,000)
6100 Amazon Revenue                       (850,000)
...
TOTAL                     €1,028,000    €1,028,000 ✓
```

**Step 9: GL Reconciliations**
```
□ Cash (1100) ↔ Bank statement
□ AR by marketplace (1210-1250) ↔ Marketplace dashboards
□ Inventory (1310) ↔ Warehouse system + accruals
□ AP by supplier (3110) ↔ Supplier statements
□ VAT payable (3210) ↔ Monthly VAT calculation (22% on net sales)
□ Revenue (6000-6700) ↔ Marketplace reports (gross - returns - commissions)
□ COGS (7100-7700) ↔ Inventory system + actual marketplace fees
```

### Day 4-5: FINANCIAL STATEMENTS

**Step 10: Generate P&L (Conto Economico)**
```
VENDIAMONOI.IT - P&L STATEMENT - MARCH 2026
==============================================

REVENUES:
Gross Revenue (6100-6600)                €850,000
Less: Returns (6710)                     (€85,000)
Less: Chargebacks (6720)                 (€15,000)
Net Revenue                              €750,000

COST OF GOODS SOLD:
COGS Inventory (7100)                    (€300,000)
Marketplace Commissions (7400)           (€112,500) [15% avg]
Fulfillment & Logistics (7600)           (€60,000)
Payment Processing (7500)                (€11,250) [1.5% avg]
Obsolescence (7200)                      (€3,000)
Gross Profit                             €263,250
Gross Margin %                           35.1%

OPERATING EXPENSES:
Personnel (8100-8300)                    (€35,000)
Sales & Marketing (9100-9300)            (€22,500)
Technology (10100-10300)                 (€18,000)
G&A (11100-11500)                        (€25,000)
Depreciation (12100-12200)               (€5,000)
Total OpEx                               (€105,500)

Operating Profit (EBIT)                  €157,750
Operating Margin %                       21.0%

FINANCE COSTS:
Interest Expense (13100)                 (€2,500)

Profit Before Tax                        €155,250
Profit Before Tax Margin %               20.7%

TAXES:
IRES (24%)                               (€37,260)
IRAP (3.9%)                              (€6,050)
Total Tax Expense                        (€43,310)

NET PROFIT                               €111,940
Net Profit Margin %                      14.9%
```

**Step 11: Generate Balance Sheet (Stato Patrimoniale)**
```
VENDIAMONOI.IT - BALANCE SHEET - MARCH 31, 2026
================================================

ASSETS
Current Assets:
  Cash & Bank (1100)                     €245,000
  AR - Marketplaces (1210-1250)          €180,000
  Inventory (1310-1330)                  €120,000
Total Current Assets                     €545,000

Fixed Assets:
  Equipment (2110)                       €80,000
  Accumulated Depreciation (2140)        (€15,000)
  Net Fixed Assets                       €65,000
  
TOTAL ASSETS                             €610,000

LIABILITIES
Current Liabilities:
  AP - Suppliers (3110)                  €95,000
  VAT Payable (3210)                     €18,000
  Accrued Returns (3310)                 €37,500
  Accrued Chargebacks (3320)             €15,000
Total Current Liabilities                €165,500

Long-term Debt:
  Equipment Financing (4110)             €30,000

TOTAL LIABILITIES                        €195,500

EQUITY
  Share Capital (5110)                   €50,000
  Reserves (5210-5240)                   €25,000
  Retained Earnings (5310)               €338,500
Net Profit - Current Year (5400)         €111,940
TOTAL EQUITY                             €414,500

TOTAL LIABILITIES + EQUITY               €610,000 ✓
```

**Step 12: Supporting Schedules & Notes**
```
Schedule A: Revenue by Marketplace
  Amazon.it:        €450,000 (60%)
  Amazon EU:        €200,000 (26.7%)
  eBay:             €80,000 (10.7%)
  Other:            €20,000 (2.6%)
  Net Deductions:   (€100,000)
  = Net Revenue:    €750,000

Schedule B: COGS Composition
  Product COGS:     €300,000 (40%)
  Marketplace Fees: €112,500 (15%)
  Logistics:        €60,000 (8%)
  Payment Proc:     €11,250 (1.5%)
  Obsolescence:     €3,000 (0.4%)
  = Total COGS:     €486,750 (65%)
  = Gross Profit:   €263,250 (35%)

Schedule C: Accrued Reserves
  Returns Reserve:      €37,500 (10% of NRR)
  Chargeback Reserve:   €15,000 (2% of GMV)
  Doubtful AR Reserve:  €3,000 (assumed write-offs)
  Inventory Reserve:    €3,000 (obsolescence)

Schedule D: Fixed Assets Register
  Equipment (€15K, 10yr):  €125/month depreciation
  IT Hardware (€5K, 3yr):  €139/month depreciation
  Software (€2K, 5yr):     €33/month depreciation
  Total monthly depr:      €297 (rounded to €5K annually)
```

---

## 4. P&L BY MARKETPLACE — TEMPLATE

### Monthly P&L Waterfall (Amazon.it Example)

```
AMAZON.IT - DETAILED P&L - MARCH 2026
=====================================

Gross Revenue:                           €450,000

Less: Direct Deductions:
  Amazon Referral Fees (15% avg)         (€67,500)
  FBA Fees (€3.50/unit × 8,000)          (€28,000)
  Promotional Adjustments                (€2,000)
  Currency Variance                      (€500)
Net Revenue (after marketplace)          €352,000

Less: COGS & Logistics:
  Product Cost (40% of GMV)              (€180,000)
  Inbound Shipping                       (€12,000)
  Outbound Shipping (FBA included)       (€8,000)
  Packaging & Labeling                   (€4,000)
  Damaged/Returned Units (2% reserve)    (€9,000)
Gross Profit                             €139,000
Gross Margin %                           39.5%

Less: COGS-Adjacent Costs:
  Payment Processing (1.5%)              (€5,250)
  Chargeback Reserve (0.8%)              (€3,600)
  Return Processing                      (€2,000)
  Fulfillment Fees Not in FBA            (€1,000)
Contribution Margin (CM)                 €127,150
CM %                                     36.1%

Less: Variable Marketing & Promo:
  Sponsored Product Ads (3% of NRR)      (€10,560)
  Promotions & Coupons                   (€5,000)
  Affiliate Commissions (1%)             (€3,520)
Adjusted CM                              €108,070
Adjusted CM %                            30.7%

Less: Fixed Channel Costs:
  Dedicated Channel Manager (allocated)  (€2,000)
  Marketplace Subscription/Tools         (€500)
  Reporting & Analytics                  (€300)
  Returns Processing Center              (€1,200)

CHANNEL OPERATING PROFIT                €104,070
Channel Operating Margin %               29.6%

Notes:
- Revenue mix: Electronics 45%, Fashion 35%, Home 20%
- FBA penetration: 65% of units
- Return rate: 8% (industry average for fashion)
- Chargeback rate: 0.8% (below platform average)
- TACoS (Total Advertising Cost of Sales): (€10,560 / €352,000) = 3.0%
  Industry benchmark: 3-5%, Vendiamonoi = EFFICIENT
```

### Marketplace Comparison Template (Full Month)

```
MARKETPLACE P&L COMPARISON - MARCH 2026
========================================

Metric                   Amazon    eBay    Cdiscount  Total
─────────────────────────────────────────────────────────────
Gross Revenue            €450K     €80K    €40K       €850K
Marketplace Commissions  (€95.5K)  (€10.3K) (€6.0K)   (€112.5K)
Net Revenue              €352K     €69.7K  €34K       €750K
Revenue %                46.9%     9.3%    4.5%       100%

COGS                     (€140K)   (€28K)  (€16K)     (€300K)
Logistics                (€20K)    (€5K)   (€3K)      (€60K)
Fulfillment              (€28K)    (€2K)   (€1K)      (€31K)
─────────────────────────────────────────────────────────────
Contribution Margin      €164K     €34.7K  €14K       €312.7K
CM %                     46.6%     49.8%   41.2%      41.7%

Operating Costs (allocated):
Personnel                (€17.5K)  (€3.5K) (€2K)      (€23K)
Marketing & Ads          (€10.6K)  (€1.5K) (€1K)      (€13.1K)
Tech & Tools             (€8K)     (€2K)   (€1K)      (€11K)
─────────────────────────────────────────────────────────────
Operating Profit         €127.9K   €27.7K  €10K       €265.6K
Operating Margin %       36.4%     39.7%   29.4%      35.4%

KPIs:
TACoS                    3.0%      2.2%    2.9%       2.9%
Return Rate              8%        5%      10%        8%
Chargeback Rate          0.8%      1.2%    1.5%       1.0%
Days to Settlement       3-5       1-2     7-10       

Inventory Allocation     €60K      €12K    €8K        €80K
Turnover (mo)            2.3x      2.3x    2.0x       2.2x
DIO (days)               13        13      15         
```