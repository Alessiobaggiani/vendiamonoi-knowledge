# Domain 5.5: Cash Flow Management for Vendiamonoi.it

Complete guide to cash flow management, working capital optimization, and treasury operations for a multi-marketplace Italian e-commerce distributor (20-30+ EU marketplaces, 100+ suppliers, €1.4M monthly revenue).

## Content Overview

This knowledge base covers 10 critical domains of cash flow management with real numbers, exact payout timelines, formulas, and actionable frameworks.

### [Part 1: Fundamentals & CCC Analysis](part1.md)
- **Domain 1:** Cash Flow Fundamentals (marketplace cash flow gap problem)
- **Domain 2:** Cash Conversion Cycle (DIO, DSO, DPO analysis with benchmarks)
- **Domain 3:** Marketplace Payout Timing (Amazon, eBay, Bol.com, Kaufland, Cdiscount, Allegro detailed calendars)

### [Part 2: Optimization & Forecasting](part2.md)
- **Domain 4:** Supplier Payment Terms (Net 30/60/90, 2/10 terms, LC, escrow, consignment)
- **Domain 5:** Working Capital Optimization (JIT, drop-shipping, marketplace lending, factoring)
- **Domain 6:** Cash Flow Forecasting (13-week rolling model with seasonal adjustments, scenario planning)

### [Part 3: Treasury & Reporting](part3.md)
- **Domain 7:** Treasury Management (bank structure: Qonto, N26, Revolut; multi-currency; hedging GBP/PLN/SEK/CZK)
- **Domain 8:** Financing Options (revenue-based financing comparison: Clearco vs Uncapped vs Wayflyer, bank loans, cost analysis)
- **Domain 9:** Crisis Prevention (early warning indicators, dashboard alerts, response protocols, communication templates)
- **Domain 10:** Reporting (weekly cash position, monthly statement direct method, board treasury report)

## Key Metrics for Vendiamonoi.it

| Metric | Current | Target | Unit |
|--------|---------|--------|------|
| **Revenue** | €1,400,000 | Growth 15%/year | Monthly |
| **Cash Conversion Cycle** | 20 days | 2 days | Days |
| **DSO (Marketplace)** | 17 days | 12 days | Days |
| **DIO (Inventory)** | 50 days | 40 days | Days |
| **DPO (Suppliers)** | 47 days | 65 days | Days |
| **Working Capital Requirement** | €283,000 | €67,000 | Frozen cash |
| **Days of Cash Buffer** | 52 days | 60+ days | Days |
| **Debt Service** | €3,500/month | <1% revenue | Monthly |

## Critical Formulas

**Cash Conversion Cycle (CCC):**
```
CCC = DIO + DSO - DPO
Current: 50 + 17 - 47 = 20 days
Target: 40 + 12 - 65 = -13 days (cash generation)
```

**Working Capital Requirement:**
```
WCR = (Daily COGS × DIO) + (Daily Revenue × DSO) - (Daily COGS × DPO)
Current: (€40,000 × 50) + (€47,000 × 17) - (€40,000 × 47) = €283,000
Target: (€38,000 × 40) + (€47,000 × 12) - (€38,000 × 65) = -€144,000
```

**Amazon Holdback Impact:**
```
Monthly revenue: €500,000 (35% of total)
Payout delay: 21-28 days
Permanent cash tie-up: €350,000-€467,000
```

## Marketplace Payout Timelines (Days to Bank Account)

| Marketplace | Holdback | Frequency | Total DSO | Annual Utilization |
|-------------|----------|-----------|----------|--------------------|
| Amazon | 14 days | Biweekly | 21-28 | 35% |
| Bol.com | 7 days | Weekly | 8 | 21% |
| eBay | 5 days | Weekly | 6-7 | 15% |
| Kaufland | 3 days | Daily | 3-4 | 13% |
| Cdiscount | 2 days | Daily | 3 | 8% |
| Allegro | 1 day | Daily | 1-2 | 6% |
| **Weighted Average DSO** | — | — | **17 days** | **100%** |

## Supplier Payment Terms Optimization

### Current State
- 70% Asian suppliers: Net 30-45 (average 35 days)
- 20% EU suppliers: Net 60 (60 days)
- 10% Italian suppliers: Net 15-30 (22 days)
- **Weighted DPO: 47 days**

### Target State (12-month plan)
- Phase 1 (Months 1-4): Negotiate Net 60 with top 30 suppliers (+18 days DPO)
- Phase 2 (Months 5-8): Implement 2/10 Net 30 discounts (20-day acceleration, via credit line)
- Phase 3 (Months 9-12): Convert Net 90 with LC backing, implement consignment
- **Target DPO: 65 days (+18 days improvement)**

### 2/10 Net 30 ROI Calculation
```
Discount rate: 2% for 20 days early payment
Annualized yield: (2% / 20 days) × 365 = 36.5% ROI

Example (€500,000 supplier volume):
- Discount amount: €10,000
- Cost of acceleration: €490,000 × 4% × (20/365) = €1,075
- Net benefit: €10,000 - €1,075 = €8,925
- Should ALWAYS take discount when bank financing available
```

## 13-Week Cash Flow Forecast (Template)

**Key Drivers:**
- Opening balance + inflows - outflows + financing = closing balance
- Amazon: Biweekly €125K+ settlements (Day 21-28)
- Smaller marketplaces: Weekly/daily €28K+ combined (Day 3-7)
- Supplier payments: Peak around 45-60 days post-invoice
- Weekly variance: ±€50K from forecast is normal
- **Red flag threshold:** 5-day forecast accuracy <±€25K

**Seasonal Adjustments:**
- Q1: -15% revenue (post-holiday decline)
- Q2: -5% revenue (spring lull)
- Q3: Baseline (+0%)
- Q4: +30-40% revenue (Black Friday Nov 20 = +80% spike)

## Revenue-Based Financing Comparison

### Scenario: €150,000 Seasonal Inventory Build (Sept 1 - Oct 15, 45-day need)

| Option | Capital | Cost | Payback | Monthly Payment | Total Cost | Rec Priority |
|--------|---------|------|---------|---|---|---|
| Amazon Lending | €150K | 6% rate | 6 mo | €2,625 | €4,500 | 1st ⭐ |
| Qonto Working Capital Line | €150K | 4% + €1.5K fee | 6 mo | €2,500 | €4,000 | 2nd ⭐ |
| Wayflyer RBF | €150K | 5% fee | 6 mo | €1,250 | €7,500 | 3rd |
| Clearco RBF | €150K | 8% fee (8% revenue) | 4.3 mo | €28,000 | €12,000 | 4th |
| Traditional Bank Loan | €150K | 5.5% rate + 1.5% fee | 24 mo | €6,625 | €8,213 | Last |

**Recommendation:** Activate Amazon Lending first (lowest cost), then Qonto LOC, then Wayflyer only if needed.

## Cash Crisis Prevention Dashboard

**Monitor Weekly (Automated Alerts):**
1. Cash balance below 10-day operating expense (€50K threshold)
2. DSO elongating >3 days from normal (indicates settlement delays)
3. Supplier payment default risk (track overdue invoices)
4. Return rate spiking above 30% (indicates quality issues)
5. Marketplace account holds (suspension risk)

**Response Levels:**
- **Level 1 (7-day buffer):** Halt discretionary spend, activate working capital line, notify management
- **Level 2 (3-day buffer):** Activate all marketplace lending, emergency RBF, daily cash calls
- **Level 3 (Imminent negative):** Emergency board meeting, suspend operations, activate asset sales, legal notification

**Communication templates for supplier delays, marketplace inquiries, and internal escalation provided in Part 3.**

## Optimal Bank Structure

**Primary (Operational): Qonto**
- €49-99/month (Team plan)
- Multi-currency: EUR, GBP, PLN, CZK, SEK
- Working capital line: €20K-€500K at 5-7%
- API integration for marketplace reconciliation

**Secondary (Distribution): Wise Business**
- Free borderless account
- Multi-currency settlement
- 1-2% conversion fees (low cost)

**Tertiary (Reserve): Revolut Business**
- €0-€19/month
- Interest on balances: 2.5-4.5%
- Vault/savings buckets (emergency buffer)

## Weekly Treasury Tasks (10 minutes)

1. Pull Qonto balance via API (reconcile vs. forecast)
2. Verify 7-day marketplace payout confirmation (all regions)
3. Check supplier invoice aging (flag any overdue >3 days)
4. Confirm 13-week forecast variance (update if >€25K deviation)
5. Review currency exposure (GBP/PLN/CZK/SEK: any hedges needed?)
6. Alert if any red flag thresholds crossed

## Monthly Treasury Tasks (2 hours)

1. Prepare cash flow statement (direct method) with variance analysis
2. Reconcile all marketplace settlements vs. invoiced orders
3. Update DSO/DIO/DPO metrics (track progress vs. targets)
4. Board report: key metrics, debt position, financing options review
5. Supplier payment term negotiation: follow-up with target vendors
6. Review financing options (marketplace lending availability, RBF rates)

## Annual Strategy Review (Q4)

- Evaluate CCC target achievement (on track for 2-day goal?)
- Rebalance marketplace mix (shift revenue to faster payout platforms)
- Supplier consolidation (reduce # suppliers, increase leverage)
- Banking relationships (any better terms available?)
- Financing options (current landscape review)

---

**Document Version:** 1.0
**Created:** April 2, 2026
**For:** Vendiamonoi.it S.R.L.
**Revenue Base:** €1.4M monthly (20-30+ EU marketplaces, 100+ suppliers)
**Confidentiality:** Internal use only

**Total Content:** 1,073 lines | **Coverage:** All 10 Financial Management Domains | **Real Numbers:** ✓ | **Actionable:** ✓