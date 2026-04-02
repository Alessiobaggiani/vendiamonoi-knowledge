# Domain 5.4 — Financial Reporting & Analytics

Complete financial reporting and analytics documentation for **Vendiamonoi.it** (Italian S.R.L., digital marketplace distribution).

## Contents

This domain covers the entire financial management lifecycle for multi-marketplace operations across 20-30+ EU platforms (Amazon, eBay, Cdiscount, Zalando, etc.) serving 100+ suppliers.

### Documentation Structure

- **domain_5_4_reporting_part1.md** (680 lines)
  - Italian Accounting Standards (OIC 12/13/15/19/25)
  - Complete Chart of Accounts (Piano dei Conti) — 50+ accounts organized by asset, liability, equity, revenue, cost
  - Monthly Close Procedures — Step-by-step T+5 (data import → accruals → trial balance → statements)
  - P&L by Marketplace templates and waterfall analysis

- **domain_5_4_reporting_part2.md** (681 lines)
  - KPI Dashboard — 20+ metrics with formulas and targets (Gross Margin, TACoS, CCC, DIO, DSO/DPO)
  - Management Reporting — Weekly flash, monthly pack, quarterly board deck, annual report
  - Budgeting & Forecasting — Annual budget, rolling 12-month, scenario planning
  - Analytics Tools — Excel, BI (Looker, Metabase, Data Studio), accounting (Fatture in Cloud, Xero)
  - Audit Preparation — Complete checklist, supporting schedules, VAT reconciliation

## Key Features

### Chart of Accounts (Conto 1-15)
```
1000s: ASSETS (Cash, AR, Inventory, Fixed Assets)
2000s: Liabilities (AP, Taxes, Accruals, Debt)
5000s: EQUITY (Capital, Reserves, Retained Earnings)
6000s: REVENUE (By marketplace: Amazon, eBay, Cdiscount, etc.)
7000s: COGS (Product, Commissions, Fulfillment, Payment Processing)
8-14: OpEx (Personnel, Marketing, Tech, G&A, Depreciation, Finance)
15000s: P&L Bottom Lines (Gross Profit, EBIT, Net Profit)
```

### Monthly Close (T+5 Process)
1. **Day 1-2:** Data import & reconciliation (marketplace → bank)
2. **Day 2-3:** Accruals (returns, chargebacks, obsolescence, doubtful AR)
3. **Day 3-4:** Inventory & fixed assets review
4. **Day 4:** Trial balance & GL reconciliation
5. **Day 4-5:** Financial statements (P&L, Balance Sheet, supporting schedules)

### P&L Templates
- **Full P&L:** Revenue → COGS → Gross Profit → OpEx → EBIT → EBT → Net Profit
- **By Marketplace:** Gross Revenue → Net Revenue → Contribution Margin → Operating Profit
- **Waterfall Analysis:** Breakdown of margin compression by cost category

### KPI Dashboard (20+ metrics)
- **Revenue:** Growth %, mix by marketplace, trend
- **Profitability:** Gross Margin %, CM %, Operating Margin %, Net Margin %
- **Efficiency:** TACoS, COGS %, Fulfillment Cost %, OpEx %, Unit Economics
- **Working Capital:** CCC, DIO, DSO, DPO, Quick Ratio
- **Operational:** Return rate, Chargeback rate, Inventory turnover, Sell-through rate

### Management Reporting Formats
- **Weekly Flash:** 5-minute snapshot (headline metrics + top 3 actions)
- **Monthly Pack:** 30-page detailed analysis (P&L, OpEx, KPIs, marketplace detail)
- **Quarterly Deck:** 50-slide board presentation (results, trends, outlook)
- **Annual Report:** 120+ page audited statements + notes

### Budgeting Framework
- **Annual Budget:** FY2026 template with monthly P&L, assumptions, drivers
- **Rolling 12-Month:** Monthly updates capturing seasonal trends & initiatives
- **Scenarios:** Optimistic (+20% rev), Base (+10% rev), Pessimistic (-5% rev)

### Analytics Tech Stack
| Tool | Purpose | Cost |
|------|---------|-------|
| Fatture in Cloud | Accounting GL, VAT compliance | €100-200/mo |
| Looker | BI dashboards, KPI monitoring | €2K/mo |
| Sellerboard | Amazon profitability (ASIN-level) | €50-100/mo |
| A2X | Marketplace accounting integration | €75-150/mo |

### Audit Preparation
- **Readiness Checklist:** 40+ items covering GL, revenue, inventory, fixed assets, liabilities, tax, compliance
- **Supporting Schedules:** Revenue reconciliation, COGS analysis, VAT reconciliation (detailed templates)
- **Documentation Standards:** Materials for Revisore Legale (external auditor)

## Usage

### For Monthly Close
1. Follow the 5-day close calendar (pages in Part 1)
2. Execute reconciliations from the checklist
3. Record accruals from template amounts
4. Generate trial balance and statements

### For Management Reporting
1. Extract KPIs from daily/weekly dashboards
2. Compare to targets and prior periods
3. Prepare variance analysis by marketplace
4. Package into appropriate format (flash/pack/deck)

### For Audits
1. Gather documentation per checklist
2. Prepare supporting schedules (3 samples included)
3. Ensure GL trial balance reconciles
4. Review management estimates & accruals

## Compliance

- **Italian Standards:** OIC 12, 13, 15, 19, 25 (Italian GAAP equivalent)
- **Form:** Bilancio Ordinario (ordinary balance sheet, required for >€2M revenue)
- **Audit:** External audit by Revisore Legale (required)
- **VAT:** Monthly calculation & filing (€822K annual liability at 22% rate)
- **Taxes:** IRES 24%, IRAP 3.9% (combined ~€426K on €1.28M profit)

## Key Metrics (FY2026 Forecast)

| Metric | Target | Status |
|--------|--------|--------|
| Revenue | €9.33M | +10% YoY |
| Gross Margin | 32.7% | Stable |
| Operating Margin | 19.4% | +1.5pp improvement |
| Net Profit Margin | 13.7% | On track |
| TACoS | <2.5% | 1.8% (efficient) |
| CCC | <30 days | -5 days (positive) |
| Inventory DIO | <20 days | 20.3 days (good) |

## Implementation

**Estimated Effort:**
- Manual monthly close: 160-200 hours/year
- With automation (APIs, GL integrations): 80-100 hours/year
- Audit preparation: 40-60 hours (annual)

**Quick Start:**
1. Load Chart of Accounts into accounting system (Fatture in Cloud or Xero)
2. Set up monthly close calendar (first 2 weeks of each month)
3. Configure KPI dashboard in Looker or Metabase
4. Establish marketplace data feeds (APIs or exports)
5. Schedule variance analysis meeting (monthly, 1 hour)

---

**Last Updated:** April 2, 2026  
**Owner:** Alessiobaggiani (Vendiamonoi.it Finance)  
**Repository:** vendiamonoi-knowledge  
**Branch:** main