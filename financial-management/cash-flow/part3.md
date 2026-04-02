# Domain 5.5 Cash Flow Management - Part 3
## Treasury Management, Financing Options, Crisis Prevention & Reporting

---

## 1. Treasury Management

### 1.1 Bank Account Structure for Multi-Currency Operations

#### Qonto (Primary European Account)
- **Best for**: EU-based operations with EUR anchor
- **Features**:
  - Multi-currency IBAN (EUR, GBP, PLN, CZK primary)
  - Automated FX conversion with competitive rates (0.5-1.2% margin)
  - Per-account spend controls and approval workflows
  - Real-time transaction notifications via API
  - Integration with accounting software (Xero, NetSuite)
- **Account Structure**:
  - Main operating account (EUR) - daily operational expense
  - GBP collection account - UK marketplace revenue
  - PLN operating account - Poland supplier payments
  - Sweep master account - consolidates to EUR

**Cost**: EUR 29-99/month (Pro plan) + FX fees

#### N26 Business (Secondary Account)
- **Best for**: High-frequency business travel and subsidiary operations
- **Features**:
  - Debit card with expense controls
  - Category-based spending rules (supplier vs. operational)
  - Real-time expense tracking
  - Multi-user access with role-based controls
- **Limitations**:
  - Not primary for high-volume payments (ACH/SWIFT delays)
  - Better for ad-hoc business expenses than treasury management

**Cost**: EUR 0 (free) - EUR 16.90/month (premium)

#### Revolut Business (Liquidity Buffer)
- **Best for**: FX arbitrage and short-term working capital
- **Features**:
  - Inter-bank FX rates (minimal 0.1-0.3% margin)
  - Instant peer-to-peer transfers
  - Virtual card issuance for team members
  - API for automated transfers
- **Strategic Use**:
  - Receive supplier invoices in USD, convert via Revolut when rate favorable
  - Hold GBP/SEK/CZK in separate wallets for optimized conversion timing

**Cost**: EUR 7.99-59/month + minimal FX fees

### 1.2 Multi-Currency Management Framework

#### Currency Pairing Strategy (GBP/PLN/SEK/CZK)

| Currency Pair | Annual Volatility | Hedge Frequency | Position Size |
|---|---|---|---|
| GBP/EUR | 3.8% | Monthly | Up to 45 days sales |
| PLN/EUR | 6.2% | Quarterly | Up to 30 days payables |
| SEK/EUR | 4.1% | Monthly | Up to 35 days payables |
| CZK/EUR | 3.5% | Quarterly | Up to 40 days payables |

#### Monthly Currency Review Process
```
Week 1: Forecast GBP/PLN/SEK/CZK exposure for next 90 days
  - UK marketplace revenue (1-3% daily variance)
  - Polish supplier commitments (EUR/PLN locked)
  - Swedish/Czech vendor invoices (contracted rates)

Week 2: Compare forward rates vs. spot rates
  - If forward > spot by 1.2%+: Lock via forward contract
  - If volatility expected (>6% in next 30d): Consider collar option

Week 3: Execute contracts via bank dealer or fintech
  - Qonto FX desk (for <EUR 50k trades)
  - ICBC or Deutsche Bank (for >EUR 50k, quarterly reviews)

Week 4: Monitor daily P&L impact, document in Excel
```

### 1.3 Hedging Strategies

#### Strategy 1: Natural Hedging (Preferred)
- **Objective**: Match currency inflows with outflows
- **Example**:
  - UK revenue: GBP 150k/month (30-day average)
  - UK supplier costs: GBP 45k/month
  - Net unhedged exposure: GBP 105k
  - Action: Invoice UK customers in GBP 105k, pay back-to-back

#### Strategy 2: Forward Contracts (For Predictable Cash Flows)
- **Usage**: Lock GBP/EUR rate 30-90 days forward
- **Example**:
  - Today's rate: 1 GBP = 1.172 EUR (May 2026)
  - 90-day forward: 1 GBP = 1.165 EUR
  - Lock EUR 174,750 for GBP 150k receipt (saves EUR 1,050 vs. spot in 3mo)
- **Cost**: Minimal (embedded in forward rate spread, typically 0.3-0.8%)
- **When to use**: High certainty of cash flow + >EUR 30k at stake

#### Strategy 3: Currency Options (For Uncertain Flows)
- **Put Option Example** (Hedge GBP downside):
  - Strike price: 1 GBP = 1.160 EUR (floor)
  - Premium: 0.4% of notional (EUR 700 for EUR 175k hedge)
  - Benefit: Keep upside if GBP strengthens beyond 1.172
  - Breakeven: GBP must rise to 1.168 EUR to offset premium
- **When to use**: Seasonal revenue volatility or marketplace algorithm changes

#### Strategy 4: Multi-Leg Collar (For High-Volatility Pairs)
- **Structure** (PLN/EUR):
  - Buy put option: 1 PLN = 0.24 EUR (floor)
  - Sell call option: 1 PLN = 0.26 EUR (ceiling)
  - Net cost: 0% (sold call offsets bought put premium)
  - Protection: Lock range 0.24-0.26 EUR per PLN
- **Typical cost for EUR 50k notional**: EUR 0-150 net

### 1.4 Sweep Account Architecture

#### Daily Sweep Logic
```
6:00 AM CET: Automated process triggers
├── Check Qonto main account balance
├── Deduct minimum operating buffer (EUR 25k)
├── Deduct next 3 days payables
├── Excess → Sweep to Revolut savings wallet
└── Log transaction in treasury dashboard

Triggers:
- If balance > EUR 75k: Sweep EUR 50k → Revolut (1.2% interest rate)
- If balance < EUR 20k: Alert CFO, pause non-critical spending
- If upcoming payroll > EUR 15k in 2 days: Hold surplus
```

#### Account Hierarchy
```
Level 1: Operating Accounts (Qonto main)
  ├─ Daily inflows from marketplaces
  ├─ Daily outflows for suppliers
  └─ Maintains EUR 25-40k buffer

Level 2: Holding Accounts (Revolut, N26)
  ├─ 5-15 day working capital
  ├─ FX conversion staging area
  └─ Temporary payroll staging

Level 3: Reserve Account (Savings account at traditional bank)
  ├─ 30-45 day operating reserve
  ├─ Emergency liquidity (not daily access)
  └─ Higher interest rate (2.8-3.2% in 2026)
```

#### Implementation Requirements
- API access from both banks (automated sweep via Zapier + custom Python script)
- Daily reconciliation check (automated)
- Monthly sweep audit and rebalancing
- CFO approval for sweep thresholds

---

## 2. Financing Options & Cost of Capital

### 2.1 Revenue-Based Financing (RBF)

#### Provider Comparison: Clearco vs. Uncapped vs. Wayflyer

| Provider | Min. Revenue | Funding Amount | Repayment Rate | Time to Cash | Ideal Customer |
|---|---|---|---|---|---|
| **Clearco** | USD 10k/month | USD 25k-500k | 3-8% of daily revenue | 1-2 days | Growth-stage SaaS, 80%+ recurring |
| **Uncapped** | GBP 1.5k/month | GBP 2k-250k | 2.5-6% of revenue | 5-7 days | Ecommerce, variable monthly revenue |
| **Wayflyer** | USD 5k/month | USD 5k-2M | 4-9% of revenue | 2-3 days | High-growth commerce, 50%+ YoY |

#### Clearco Deep Dive (Recommended for Vend Marketplace Model)
- **Application Process**: 10 minutes online (instant decision engine)
- **Underwriting**: Analyzes Stripe/PayPal/Shopify data via API
- **Terms**:
  - No personal guarantee required
  - No fixed repayment date (flexible, based on sales)
  - Multiple draw-downs available
- **Example Scenario** (Marketplace with GBP 150k/month revenue):
  - Clearco funding: GBP 80k advance
  - Repayment: 5% of daily marketplace revenue
  - At GBP 150k/month: ~GBP 7,500 monthly repayment = 10-11 months payoff
  - Cost: GBP 42,500 total repayment (implied 12.3% APR equivalent)

**Pros**: 
- No monthly fee structure (pay-as-you-grow)
- Speed (same-day funding possible)
- Flexible repayment

**Cons**:
- High all-in cost (10-15% APR equivalent)
- Reduces cash flow available for working capital

#### Uncapped (Best for Seasonal/Variable Revenue)
- **Application**: Document review (3-5 days)
- **Underwriting**: Bank statements + business financials + owner background
- **Terms**:
  - Fixed repayment schedule (unlike Clearco)
  - GBP 2.5-6% weekly/daily repayment rate
  - Renewable draws available

**Pros**:
- Predictable repayment schedule
- Lower cost than Clearco (by 2-3% APR)
- Multi-currency support

**Cons**:
- Less flexible than Clearco if revenue drops
- Slower funding process

#### Wayflyer (For Hyper-Growth Amazon/Shopify Sellers)
- Specialized for FBA (Fulfilled by Amazon) and ecommerce
- Integration with Amazon Seller Central for data pull
- Largest single funding tranches (up to USD 2M)
- Less suitable for multi-marketplace model like Vend

### 2.2 Traditional Bank Loans

#### Secured Loan Structure (Recommended for >EUR 100k)
- **Collateral**: Marketplace receivables + inventory (if applicable)
- **Term**: 2-5 years
- **Interest Rate**: Prime + 2.5-4.5% (in 2026 environment: 6-8% typical)
- **Requirement**: Personal guarantee + financial statements

**Example**: EUR 150k facility at 7% APR over 3 years
- Monthly payment: EUR 4,573
- Total cost: EUR 14,628 (interest)
- All-in cost: 9.75% APR equivalent (lower than RBF)

**Advantages**:
- Lower cost than RBF
- Fixed repayment schedule (better forecasting)
- Available immediately if approved (3-10 days processing)

**Disadvantages**:
- Slower application (requires full underwriting)
- Personal guarantee exposure
- Covenant requirements (minimum DSCR of 1.2x)

#### Revolving Credit Facility (Working Capital Line)
- **Structure**: EUR 50-100k credit line, use only what needed
- **Cost**: 1% annual fee + 6-8% interest on drawn balance
- **Usage**: Bridge between payables and receivables (short-term only)
- **Example**: EUR 50k facility, average draw EUR 30k
  - Fee: EUR 500/year
  - Interest: EUR 1,800/year (est. at 7%)
  - Total annual cost: EUR 2,300 (4.6% on average draw)

### 2.3 Marketplace Lending

#### Amazon Lending (For Amazon-Seller Operations)
- **Eligibility**: Seller on Amazon for 12+ months, GBP 5k+ monthly revenue
- **Loan Amount**: GBP 5k-GBP 250k
- **Term**: 12 months
- **Interest Rate**: 8-12% APR (no origination fees)
- **Speed**: 2-3 days to funding
- **Advantage**: No personal guarantee, based solely on Amazon sales history
- **Limitation**: Only for FBA or A9-registered sellers

**Decision Tree**:
- If >50% sales from Amazon FBA: Consider Amazon Lending first (easiest approval)
- If multi-marketplace: Use Clearco or Uncapped (broader coverage)

### 2.4 Factoring Services

#### Invoice Factoring (For B2B Receivables)
- **Structure**: Sell unpaid invoices at discount for immediate cash
- **Discount Rate**: 2-5% per 30 days (24-60% APR equivalent)
- **When useful**:
  - Large B2B customer with 60+ day payment terms
  - Need immediate working capital without monthly repayment
  
**Example**:
- Unpaid invoice to corporate client: EUR 50k (Net-60)
- Factor offers: EUR 48,750 (2.5% discount) immediate
- Cost: EUR 1,250 (5% annualized if 60-day term)
- Break-even: If customer pays in 30 days instead of 60 (saves 30-day funding cost)

**NOT recommended** for Vend model (marketplace receivables are too short-term)

### 2.5 Financing Cost of Capital Comparison Table

| Financing Source | Amount | Term | All-in Cost | When to Use |
|---|---|---|---|---|
| **Bank Revolving Line** | EUR 50-100k | Flexible | 4.6-6.2% APR | Working capital bridge |
| **Traditional Bank Loan** | EUR 100-500k | 3-5 years | 7-8.5% APR | Longer-term expansion |
| **Clearco RBF** | USD 25-500k | Variable (9-14mo) | 10-15% APR | Quick growth funding |
| **Uncapped RBF** | GBP 2-250k | Fixed term | 8-12% APR | Stable-ish revenue |
| **Amazon Lending** | GBP 5-250k | 12 months | 8-12% APR | FBA sellers only |
| **Wayflyer RBF** | USD 5k-2M | Variable | 12-18% APR | Hyper-growth phase |
| **Invoice Factoring** | Per invoice | Per transaction | 24-60% APR | Distressed cash flow |

**Recommendation Priority** (for Vend model):
1. **First**: Negotiate longer payment terms with suppliers (0% cost)
2. **Second**: Bank revolving line (4.6-6.2% cost, flexible)
3. **Third**: Clearco RBF (10-15%, but faster access)
4. **Avoid**: Invoice factoring unless true crisis

---

## 3. Cash Flow Crisis Prevention

### 3.1 Early Warning Indicators

#### Daily Monitoring Dashboard (KPIs to Track)
```
1. Days Cash on Hand (DCoH)
   Formula: (Cash Balance) / (Daily Burn Rate)
   Alert Threshold: DCoH < 15 days
   
2. Cash Conversion Cycle (Days)
   Formula: DIO + DSO - DPO
   Alert Threshold: Increase of >5 days vs. rolling 90-day avg
   
3. Marketplace Payout Velocity (% of Sales)
   Metric: (Payouts Received) / (Marketplace Sales)
   Alert Threshold: <80% in any 7-day window
   
4. Supplier Payment Ratio
   Metric: (Payables Outstanding) / (COGS)
   Alert Threshold: >45 days (indicates lengthening terms)
   
5. Cash Burn Runway
   Formula: (Cash Balance - Minimum Reserve) / (Daily Net Burn)
   Alert Threshold: <30 days
   
6. Receivables Aging
   Metric: % of invoices >30 days old
   Alert Threshold: >15% of total AR
```

#### Weekly Review Checklist
- [ ] Confirm marketplace payouts received on schedule
- [ ] Verify no unexpected supplier payment hold-ups
- [ ] Check FX positions (mark-to-market vs. hedges)
- [ ] Review customer churn rate (early signal of demand drop)
- [ ] Monitor team attrition (may indicate future cash flow issues)
- [ ] Scan bank portal for unusual transactions/reversals

#### Red Flags (Immediate Investigation Required)
1. **Marketplace Suspension/Account Hold**
   - Even temporary hold (24-48 hours) creates liquidity crisis
   - Symptoms: Unexpected gap in daily payouts, communication from platform

2. **Large Customer Refunds**
   - 5%+ of daily revenue reversed
   - Indicates churn or quality issues cascading

3. **Supplier Demand for COD or Payment Upfront**
   - Indicates platform/margin deterioration
   - May signal changing credit environment (supplier stress)

4. **Revenue Decline >10% Week-over-Week**
   - Without clear seasonality explanation
   - Suggests marketplace algorithm change or competitive pressure

5. **Key Customer/Supplier Communication Breakdown**
   - Emails going unanswered
   - Relationship deterioration (payment disputes, late deliveries)

### 3.2 Emergency Measures (Graduated Response)

#### Tier 1: Yellow Alert (DCoH 15-20 days)
**Actions** (0-3 days):
- [ ] Call marketplace partner to confirm payout schedule
- [ ] Identify discretionary spending to cut (marketing, T&D)
- [ ] Reduce supplier orders by 20% for next 14 days
- [ ] Expedite collection of any outstanding B2B receivables
- [ ] Review payroll: Can any contractor hours be deferred?

**Expected impact**: Add 3-5 days DCoH

#### Tier 2: Orange Alert (DCoH 10-15 days)
**Actions** (1-7 days):
- [ ] **Activate Clearco/Uncapped** (apply immediately if not already set up)
- [ ] **Negotiate extended payment terms** with 3-5 largest suppliers (see templates)
- [ ] **Defer non-essential capex**: Pause any equipment purchases or software subscriptions
- [ ] **Reduce payroll**: Consider salary cuts (leadership first), not layoffs yet
- [ ] **Invoke supplier credit lines**: If available, move invoices from COD to Net-30

**Expected impact**: Add 5-10 days DCoH via financing + supplier terms

#### Tier 3: Red Alert (DCoH <10 days)
**Actions** (same day):
- [ ] **Activate bank revolving line** (if available)
- [ ] **Call board/investors**: Alert to potential capital injection need
- [ ] **Pause all hiring and contractor engagements** (effective immediately)
- [ ] **Negotiate with all creditors**: Payment plans, settlement offers
- [ ] **Evaluate asset sales**: Can any non-core business units be divested?
- [ ] **Invoke marketplace priority payout**: Contact Account Manager to accelerate (if available)

**Expected impact**: 10-30 days additional runway via credit lines + capital infusion planning

### 3.3 Supplier Communication Templates

#### Template 1: Extended Payment Terms Request (Standard)
```
Subject: Request for Extended Payment Terms (Net-45)

Dear [Supplier Name],

We have greatly valued our partnership over the past [X months/years] and our 
transaction volume has grown to [EUR X/month]. 

To support continued growth and optimize working capital management, we would 
like to request extended payment terms moving forward:

Current Terms: Net-30
Proposed Terms: Net-45

This aligns with industry standards for our business model and will strengthen 
our partnership. We remain committed to reliable, on-time payment and will 
continue to meet all other contractual obligations.

Could we schedule a brief call to discuss this? I'm available [suggest 2-3 times].

Thank you for considering this request.

Best regards,
[Your Name]
[Title]
[Company]
```

#### Template 2: Emergency Payment Delay Notice (Crisis Scenario)
```
Subject: Temporary Payment Schedule Adjustment

Dear [Supplier Name],

Due to temporary liquidity timing constraints related to [marketplace payout delays / 
seasonal demand shift / specific business event], we need to request a brief adjustment 
to our payment schedule for invoice [Invoice #, Amount, Due Date].

Proposed adjustment:
- Original due date: [Date]
- Revised due date: [Date, 10-15 days later]
- Amount: [EUR X]

We remain fully committed to this partnership and will resume standard payment terms 
immediately upon [specific resolution / timeline]. This is a temporary measure and we 
appreciate your understanding.

I will follow up by [specific date] with confirmation of payment.

Sincerely,
[Your Name]
```

#### Template 3: Volume Consolidation Proposal (Improve Terms)
```
Subject: Strategic Volume Consolidation Proposal

Dear [Supplier Name],

As our partnership scales, we are consolidating our supplier base to create deeper 
partnerships with key vendors. We would like to make [your company] our primary supplier 
for [product category], which would increase our monthly volume from [Current EUR X] 
to [Projected EUR Y].

In exchange for this volume commitment (12-month), we would like to discuss:

1. Pricing discount: [2-5% suggest based on volume]
2. Extended terms: Net-45 to Net-60
3. Dedicated account support
4. Priority allocation during supply constraints

This is a win-win: You gain stable, growing volume; we gain better costs and terms.

Can we schedule a call to discuss? I believe we can structure something mutually beneficial.

Best regards,
[Your Name]
```

#### Template 4: Settlement/Payment Plan Negotiation (Escalated Crisis)
```
Subject: Urgent: Payment Plan Discussion - Invoice [#]

Dear [Supplier Name],

We are currently experiencing temporary liquidity constraints and cannot meet the 
full payment of invoice [#, Amount] on the due date of [Date].

We propose the following payment plan:
- Payment 1: [Amount, Date]
- Payment 2: [Amount, Date]  
- Payment 3: [Amount, Date]
- Total completion: [Date within 60 days]

We are committed to fulfilling this obligation and have [credible reason: specific 
customer payment expected, marketplace payout confirmation, investor capital closing] 
that will enable full payment by [final date].

We value our relationship and trust this arrangement demonstrates our commitment. 
Please confirm your agreement so we can document this arrangement.

Thank you for your partnership during this period.

Sincerely,
[Your Name]
[CEO/CFO]
```

### 3.4 Marketplace Disbursement Acceleration

#### Techniques to Accelerate Payout Timing

**1. Amazon Seller Central Acceleration** (If 30%+ revenue from Amazon)
- Contact Amazon Account Manager for "expedited settlement" program
- Typical acceleration: 1-2 days faster (from 14-day standard to 12 days)
- Requirement: Account in good standing, >EUR 100k annual revenue
- Cost: None (prioritization benefit)

**2. Shopify Balance (Embedded Capital)** (If using Shopify)
- Shopify Balance offers 1-day payouts for qualified sellers
- Terms: 1.5-3% fee on advance taken
- Usage: For peak selling seasons or unexpected cash gaps
- Integration: Automatic in Shopify admin dashboard

**3. Stripe Capital** (If >70% payment volume via Stripe)
- 0% interest, flexible repayment based on % of daily Stripe sales
- Funding: 1-3 days
- Recommendation: Use for growth, not emergency liquidity

**4. eBay Dynamic Inventory Management** (If eBay seller)
- Lower inventory = faster inventory turnover = faster working capital recovery
- Request expedited settlement (eBay offers 5-7 day settlement for Premium sellers)
- Optimize: Reduce on-hand inventory by 15-20% during cash crunch

**5. Multi-Marketplace Load-Balancing**
- Shift more sales to marketplaces with faster payouts
- Payout timing comparison (standard):
  - Amazon FBA: 14 days
  - eBay: 5-7 days
  - Shopify (own store): 1-2 days
  - Vinted: 3-5 days
- Strategy: Increase Shopify direct sales by 10% -> adds 2-3 days liquidity

---

## 4. Cash Flow Reporting & Board-Level Treasury

### 4.1 Weekly Cash Position Report

#### Executive Summary (1 page, updated Friday EOD)
```
CASH POSITION REPORT
Week Ending: [Date]

Current Cash Balance:          EUR 145,230
vs. Prior Week:               EUR 128,560
vs. Budget:                   +EUR 12,340 (favorable)
vs. Year-Ago:                 EUR 91,400 (+59% growth)

Days Cash on Hand (DCoH):      18 days (vs. 15-day target: YELLOW)
Runway to Critical Level:      25 days (EUR 25k minimum)

Weekly Cash Inflow:            EUR 92,560 (marketplace revenue)
Weekly Cash Outflow:           EUR 75,890 (suppliers + payroll)
Net Weekly Change:             +EUR 16,670

Next 7-Day Outlook:
├─ High-confidence inflows:    EUR 95k (marketplace payouts scheduled)
├─ High-confidence outflows:   EUR 73k (payroll + committed suppliers)
└─ Projected balance (7d):     EUR 167,230 (GREEN - comfortable)

Risk Flags:
├─ Amazon UK payout delayed 1 day (monitor)
├─ PLN supplier requested payment acceleration (manageable via existing terms)
└─ No critical risks identified
```

#### Detailed Weekly Breakdown (2 pages)
```
A. INFLOWS BY SOURCE

Marketplace Revenue:
├─ Amazon FBA (GBP 145k @ 1.172):     EUR 169,940 (confirmed)
├─ eBay sellers commission:             EUR 8,230 (confirmed)
├─ Shopify own store (EUR):             EUR 12,450 (confirmed)
├─ B2B customer invoice payment:        EUR 5,000 (due in 2 days)
└─ TOTAL Inflows:                       EUR 195,620

Financing/Capital Inflows:
├─ Clearco drawdown (if active):        EUR 0 (not drawn this week)
├─ Bank facility drawdown:              EUR 0
└─ Other:                               EUR 0

TOTAL WEEKLY INFLOWS:                    EUR 195,620

---

B. OUTFLOWS BY CATEGORY

Payroll & Taxes:
├─ Salaries (10 FTE):                   EUR 42,000
├─ Employer taxes/benefits:             EUR 8,400
├─ Contractor fees:                     EUR 2,100
└─ Payroll subtotal:                    EUR 52,500

Supplier Payments (COGS):
├─ Poland suppliers (PLN):              EUR 18,500 (3 invoices confirmed)
├─ Czech supplier (CZK):                EUR 6,200 (1 invoice)
├─ Swedish vendor (SEK):                EUR 5,300 (confirmed)
├─ German supplier (EUR):               EUR 12,400 (confirmed)
└─ COGS subtotal:                       EUR 42,400

Operating Expenses:
├─ Rent/facilities:                     EUR 4,200
├─ Software subscriptions:              EUR 1,800
├─ Utilities:                           EUR 900
├─ Professional services:               EUR 2,100
├─ Marketplace fees/settlement charges: EUR 3,200
└─ OpEx subtotal:                       EUR 12,200

Capital Expenditure:
├─ Equipment (none this week)           EUR 0
├─ Software development:                EUR 5,600
└─ CapEx subtotal:                      EUR 5,600

Debt Service (if applicable):
├─ Clearco repayment (if active):       EUR 0
├─ Bank loan payment:                   EUR 0
└─ Subtotal:                            EUR 0

TOTAL WEEKLY OUTFLOWS:                   EUR 112,700

---

C. FX IMPACT SUMMARY

Conversion Activity:
├─ GBP/EUR conversions:                 EUR 15,240 (at 1.172, favorable +0.3%)
├─ PLN/EUR conversions:                 EUR 8,600 (at 0.255, -0.1% unfavorable)
├─ SEK/EUR conversions:                 EUR 5,300 (at 0.0965, neutral)
├─ CZK/EUR conversions:                 EUR 6,200 (at 0.041, favorable +0.2%)
└─ Total FX impact (realized):          +EUR 340 (favorable)

Unrealized FX on Holdings:
├─ GBP balance (GBP 50k @ 1.172):       Expected EUR 58,600
├─ PLN balance (PLN 25k @ 0.255):       Expected EUR 6,375
└─ Total unrealized (mark-to-market):   EUR 64,975

Active Hedges:
├─ GBP forward contract (GBP 100k @ 1.165): Locked EUR 116,500
├─ PLN collar (0.24-0.26 EUR/PLN):      Active, range protection
└─ Hedge portfolio impact this week:    +EUR 1,200 (favorable)
```

### 4.2 Monthly Cash Flow Statement (Direct Method)

#### Standard Format (Accounting Rules Compliant)
```
MONTHLY CASH FLOW STATEMENT - DIRECT METHOD
For the Month Ended: [Date]
Amounts in EUR

OPERATING ACTIVITIES
Cash Receipts from Customers:
├─ Marketplace revenue collection        EUR 425,300
├─ B2B customer payments                 EUR 18,500
└─ Total Cash Receipts:                  EUR 443,800

Cash Paid to Suppliers:
├─ COGS supplier payments:               EUR (187,400)
├─ Operating expense payments:           EUR (48,700)
└─ Total Cash Paid:                      EUR (236,100)

Cash from Operations:                    EUR 207,700

Interest and Taxes Paid:
├─ Interest on bank facility:            EUR (900)
├─ Tax payments:                         EUR (12,000)
└─ Subtotal:                             EUR (12,900)

OPERATING CASH FLOW (Net):                EUR 194,800

---

INVESTING ACTIVITIES
Capital Expenditures:
├─ Equipment purchases:                  EUR (8,200)
├─ Software development:                 EUR (22,400)
└─ Total CapEx:                          EUR (30,600)

Investments in Marketplaces (API):       EUR (1,500)

INVESTING CASH FLOW (Net):                EUR (32,100)

---

FINANCING ACTIVITIES
Debt Proceeds:
├─ Bank revolving facility drawdown:     EUR 0
├─ RBF financing drawn:                  EUR 0
└─ Subtotal:                             EUR 0

Debt Repayment:
├─ Bank loan repayment:                  EUR (13,500)
├─ RBF repayment:                        EUR 0
└─ Subtotal:                             EUR (13,500)

Owner Distributions:
├─ Dividends paid:                       EUR 0
├─ Owner withdrawals:                    EUR 0
└─ Subtotal:                             EUR 0

FINANCING CASH FLOW (Net):                EUR (13,500)

---

TOTAL MONTHLY CASH FLOW (Net):            EUR 149,200

CASH BALANCE
Beginning cash (1st of month):           EUR 128,560
Plus: Operating activities:              EUR 194,800
Less: Investing activities:              EUR (32,100)
Less: Financing activities:              EUR (13,500)
Ending cash (end of month):              EUR 277,760

---

SUPPLEMENTARY INFORMATION (Non-GAAP Metrics)

Free Cash Flow:
Operating Cash Flow:                     EUR 194,800
Less: Capital Expenditures:              EUR (30,600)
Free Cash Flow:                          EUR 164,200

Key Ratios:
└─ Operating Cash Flow / Revenue:        45.9% (healthy >30%)
└─ FCF / Revenue:                        38.7% (healthy >20%)
└─ Days Sales Outstanding (DSO):        12 days (marketplace model advantage)
└─ Days Payable Outstanding (DPO):      34 days (slight improvement from 32)
└─ Cash Conversion Cycle (CCC):          (22) days (negative = getting paid before paying)

Working Capital Changes:
├─ Accounts Receivable decrease:         EUR 5,200 (faster collection)
├─ Inventory increase:                   EUR (8,100) (seasonal build)
├─ Accounts Payable increase:            EUR 12,400 (extended terms negotiated)
└─ Net impact on working capital:        EUR 9,500 (favorable)
```

### 4.3 Rolling 13-Week Cash Flow Forecast

#### Updated Bi-weekly (Tuesday EOD)
```
13-WEEK ROLLING CASH FLOW FORECAST
Last Updated: [Date]
Confidence Levels: HIGH (confirmed), MEDIUM (probable), LOW (estimated)

Week    Inflows    Outflows   Net       Balance    DCoH   Risk Level
──────────────────────────────────────────────────────────────────
WK1    EUR 92.6k  EUR 75.9k  +EUR 16.7k EUR 277.8k  18d    GREEN
WK2    EUR 95.2k  EUR 73.5k  +EUR 21.7k EUR 299.5k  19d    GREEN
WK3    EUR 91.8k  EUR 76.2k  +EUR 15.6k EUR 315.1k  20d    GREEN
WK4    EUR 98.5k  EUR 78.0k  +EUR 20.5k EUR 335.6k  21d    GREEN
WK5    EUR 103.2k EUR 81.5k  +EUR 21.7k EUR 357.3k  23d    GREEN
WK6    EUR 88.4k  EUR 79.8k  +EUR 8.6k  EUR 365.9k  23d    GREEN (Seasonal dip)
WK7    EUR 92.1k  EUR 75.0k  +EUR 17.1k EUR 383.0k  24d    GREEN
WK8    EUR 96.8k  EUR 77.2k  +EUR 19.6k EUR 402.6k  25d    GREEN
WK9    EUR 94.3k  EUR 78.5k  +EUR 15.8k EUR 418.4k  26d    GREEN
WK10   EUR 99.7k  EUR 80.2k  +EUR 19.5k EUR 437.9k  27d    GREEN
WK11   EUR 102.1k EUR 82.0k  +EUR 20.1k EUR 458.0k  28d    GREEN
WK12   EUR 105.4k EUR 84.5k  +EUR 20.9k EUR 478.9k  29d    GREEN
WK13   EUR 101.6k EUR 83.0k  +EUR 18.6k EUR 497.5k  30d    GREEN

FORECAST ASSUMPTIONS:
✓ Marketplace payout timing: On schedule (14-day cycle Amazon, 5-7 day eBay)
✓ Supplier payment terms: Current (Net-30 average)
✓ Seasonal pattern: 5-10% variance typical for Q2
✓ FX hedges: GBP forward locked, PLN collar active
? Major assumption: No new marketplace suspensions or revenue shocks

RISK ADJUSTMENTS:
- 20% probability of Amazon payout delay (1-3 days): Impact -EUR 15-18k
- 10% probability of supplier payment hold (emergency): Impact -EUR 8-12k
- 15% probability of unexpected spending: Impact -EUR 5-10k
- Overall 13-week risk score: LOW (green zone maintained throughout)

RECOMMENDATIONS:
1. Maintain current DCoH target (18-25 days) - Forecast supports this
2. No urgent financing needed in next 13 weeks
3. Consider opportunistic supplier term improvements (padding available)
4. Monitor seasonal dips in Weeks 5-6; prepare contingency funding if needed
```

### 4.4 Board-Level Treasury Reporting

#### Monthly Board Dashboard (1-page executive view)
```
TREASURY & CASH FLOW DASHBOARD
[Company Name] | Month: [Date] | Prepared for: Board of Directors

LIQUIDITY POSITION
┌──────────────────────────────────────────────────────┐
│ Current Cash Balance:           EUR 277,760          │
│ Minimum Operating Reserve:      EUR 25,000           │
│ Available Working Capital:      EUR 252,760          │
│ Days Cash on Hand:              18 days (Target: 15-25) │
│ Status:                         GREEN - HEALTHY      │
└──────────────────────────────────────────────────────┘

FINANCING CAPACITY
┌──────────────────────────────────────────────────────┐
│ Bank Revolving Facility:        EUR 100,000         │
│ Amount Drawn:                   EUR 0               │
│ Available Borrowing Capacity:   EUR 100,000         │
│ RBF Capacity (Clearco):         EUR 200,000         │
│ Total Unused Financing:         EUR 300,000         │
│ Status:                         GREEN - AMPLE        │
└──────────────────────────────────────────────────────┘

CASH FLOW METRICS (YTD vs. Budget)
| Metric                 | YTD Actual | YTD Budget | Variance | Status |
|------------------------|-----------|-----------|----------|--------|
| Operating Cash Flow    | EUR 586k  | EUR 510k  | +€76k    | GREEN  |
| Free Cash Flow         | EUR 492k  | EUR 420k  | +€72k    | GREEN  |
| Working Capital Ratio  | 2.1x      | 2.0x      | +0.1x    | GREEN  |
| Days Cash on Hand      | 18 days   | 17 days   | +1 day   | GREEN  |
| Debt Service Coverage  | 8.2x      | 6.5x      | +1.7x    | GREEN  |

FX POSITION SUMMARY
┌──────────────────────────────────────────────────────┐
│ GBP Exposure (Net):    GBP 85,000  [Hedged 70%]    │
│ PLN Exposure (Net):    PLN 25,000  [Collar active]  │
│ SEK Exposure (Net):    SEK 45,000  [Unhedged]       │
│ CZK Exposure (Net):    CZK 150,000 [Unhedged]       │
│ Mark-to-Market P&L:    +EUR 2,100 (positive drift) │
└──────────────────────────────────────────────────────┘

UPCOMING MATERIAL EVENTS (Next 60 Days)
1. Marketplace payout acceleration opportunity (Week 2) - Could add €8-12k
2. Supplier payment term renewal cycle (Week 4) - Could extend by 5 days
3. Seasonal revenue dip (Week 5-6) - Expect €12-15k dip but within forecast
4. Q2 tax installment (Day 45) - EUR 18,500 payment scheduled

BOARD ACTIONS REQUIRED
☐ Approve new Clearco facility (EUR 200k, submitted last month)
☐ Confirm dividend policy for next quarter (if any)
☐ Review FX hedging strategy (currently 70% GBP hedged, recommend maintain)
☐ Approve capital budget for Q2 (currently planning EUR 35-40k)

TREASURER CERTIFICATION
This report accurately reflects the treasury position as of [Date]. All figures have 
been reconciled with bank statements and accounting records. No material misstatements 
or omissions.

Signed: [CFO/Treasurer Name]
Date: [Date]
```

#### Quarterly Treasury Committee Meeting Agenda
```
QUARTERLY TREASURY REVIEW (Board Finance Committee)
Duration: 60 minutes
Attendees: Board Chair, Finance Committee Members, CFO, CEO (optional)

AGENDA ITEMS:

1. Cash Position & Liquidity Review (10 min)
   ├─ Current liquidity adequacy
   ├─ Comparison to budget and prior year
   └─ Any stress-testing results

2. Working Capital Management (10 min)
   ├─ DPO/DSO/DIO trends
   ├─ CCC improvement progress
   └─ Supplier term negotiations update

3. Financing Review (15 min)
   ├─ Existing facility utilization
   ├─ Cost of capital analysis
   ├─ Refinancing opportunities (if any)
   └─ New financing needs assessment

4. FX Risk Management (10 min)
   ├─ Hedge portfolio performance
   ├─ Exposure limits compliance
   └─ Strategy adjustments (if needed)

5. Cash Flow Forecast & Risks (10 min)
   ├─ 13-week rolling forecast review
   ├─ Risk mitigation actions
   └─ Scenario planning (best/worst case)

6. Internal Controls & Compliance (5 min)
   ├─ Banking authorization review
   ├─ Separation of duties confirmation
   └─ Audit findings (if any)

Q&A / Board Discussion (10 min)
```

---

## Implementation Checklist

**Treasury Management Setup** (Week 1-2)
- [ ] Qonto business account open (EUR, GBP, PLN, CZK IBANs)
- [ ] N26 Business secondary account established
- [ ] Revolut Business API integration configured
- [ ] Daily sweep logic implemented (Python script or Zapier)
- [ ] Forex hedging guidelines documented
- [ ] Bank dealer relationship established (for >EUR 50k trades)

**Financing Options** (Week 2-4)
- [ ] Clearco application submitted
- [ ] Bank revolving facility application (if needed)
- [ ] Amazon Lending eligibility confirmed (if applicable)
- [ ] Wayflyer/Uncapped accounts established as backup
- [ ] Cost of capital comparison documented

**Crisis Prevention** (Ongoing)
- [ ] Early warning indicator dashboard built (weekly updates)
- [ ] Supplier communication templates saved & tested
- [ ] Marketplace acceleration contacts identified
- [ ] Emergency decision tree documented

**Reporting** (Week 3-4)
- [ ] Weekly cash position report template created
- [ ] Monthly direct-method cash flow statement automated
- [ ] 13-week rolling forecast model in Excel/Google Sheets
- [ ] Board reporting dashboard designed
- [ ] Treasury committee meeting schedule finalized

---

**Document Version**: 1.0
**Last Updated**: April 2026
**Next Review**: August 2026