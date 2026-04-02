```

**Optimal DIH ranges (EU context):**

| Product Type | Optimal DIH | Min | Max |
|--------------|-------------|-----|-----|
| Fast-moving staples | 20-30 days | 15 | 45 |
| Standard products | 30-50 days | 20 | 75 |
| Seasonal (non-peak) | 40-70 days | 30 | 90 |
| Specialty/niche | 60-90 days | 45 | 120 |
| Fashion (in-season) | 15-30 days | 10 | 45 |
| Fashion (off-season) | 60-180 days | 45 | 365 |

**Time-based targets by marketplace:**
- Amazon FBA: 30-45 days optimal (balance inventory cost vs stockout risk)
- eBay MFN: 20-40 days (faster rotation rewards)
- Cdiscount: 25-45 days
- Zalando B2B: 35-60 days

---

### 3.3 Sell-Through Rate (STR)

**Formula:**
```
STR = (Units Sold / Units Received) × 100%
or: STR = (Units Sold / (Beginning Inventory + Units Received)) × 100%

Usually measured over 4-week or 13-week periods
```

**Example (13-week analysis):**
```
- Units received in period: 500 units
- Beginning inventory: 100 units
- Units sold: 450 units
- Ending inventory: 150 units

STR = 450 / 500 = 90% (first formula)
or STR = 450 / (100 + 500) = 450 / 600 = 75% (second formula)

Amazon uses second formula: STR = 75%
```

**STR benchmarks (Amazon FBA standard):**

| STR Range | Classification | Action |
|-----------|-----------------|--------|
| >150% | Exceptional (received less than sold) | Increase replenishment frequency |
| 100-150% | Excellent | Standard reorder |
| 75-100% | Very good | Monitor, consider smaller orders |
| 50-75% | Good | Reduce order size, extend replenishment cycle |
| 25-50% | Slow-moving | Discount aggressively or remove |
| <25% | Dead stock | Liquidate, discontinue |

**STR by category (13-week rolling):**

| Category | Excellent | Good | Acceptable | Poor |
|----------|-----------|------|-----------|------|
| Fast-moving essentials | >120% | 90-120% | 60-90% | <60% |
| Electronics | 80-120% | 60-80% | 40-60% | <40% |
| Fashion (in-season) | 70-110% | 50-70% | 30-50% | <30% |
| Home/Furniture | 50-75% | 35-50% | 20-35% | <20% |
| Toys (seasonal) | 40-80% (varies by season) | 25-60% | 15-40% | <15% |

**4-week vs. 13-week STR:**
- 4-week: Highly volatile, daily spikes affect % significantly
- 13-week: More stable, better for trend analysis
- Both monitored: 4-week for tactical decisions, 13-week for strategic

---

### 3.4 Inventory Accuracy & Shrinkage

**Formula:**
```
Inventory Accuracy = ((Counted Units - System Units) / System Units) × 100%
Or: % Accuracy = (Correct records / Total SKUs) × 100%

Shrinkage rate = (Book Inventory - Physical Count) / Book Inventory
Shrinkage €= Shrinkage units × Unit cost
```

**Example:**
```
- System shows: 5,000 units across 50 SKUs
- Physical count: 4,900 units (20 SKUs accurate, 30 with discrepancies)
- Accuracy: (20 / 50) × 100% = 40% SKU accuracy
- Or: Variance = 100 units / 5,000 = 2% quantity variance
- Shrinkage cost: 100 units × €25/unit = €2,500
```

**Accuracy targets by marketplace:**

| Standard | Accuracy | Frequency | Penalty |
|----------|----------|-----------|--------|
| Amazon FBA | ≥99% | Continuous | Account suspension if <95% |
| Cdiscount | ≥98% | Bi-weekly audit | Liquidation holds if <95% |
| eBay MFN | Self-reported | Monthly | Seller rating impact |
| Zalando B2B | ≥99.5% | Weekly | Return/chargeback limits |

**Shrinkage sources (EU warehouse context):**

| Source | % of Total | Mitigation |
|--------|-----------|----------|
| Damage/defect | 25-35% | Better packaging, damage prevention |
| Theft/loss | 20-30% | CCTV, access controls, cycle counts |
| Administrative error | 20-35% | Barcode scanning, system discipline |
| Obsolescence/expiration | 5-15% | FIFO rotation, hold dating |
| Supplier shipment short | 5-10% | Receiving verification, claim processing |
| **Total shrinkage rate** | **0.5-2%** | Continuous improvement program |

**Cycle count program (EU FBA-compliant):**
```
Frequency: Every 90 days for fast-movers, 180 days for slow-movers
Coverage: 100% of SKUs audited annually
Method: Count 20-30 SKUs per week, reconcile discrepancies same day
Variance tolerance: ±2% units or ±1% value (whichever is greater)
Exceptions: >5 units difference triggers full SKU recount
```

---

### 3.5 Amazon IPI (Inventory Performance Index)

**Amazon IPI calculation (2026 standard):**

```
IPI = (5 × excess_inventory_rate +
       20 × stranded_inventory_rate +
       15 × sell_through_rate +
       60 × in_stock_rate) / 100

Where each metric is weighted:
- Excess inventory rate: Units > 90 days demand forecast / Total units (5%)
- Stranded inventory rate: Units that won't sell / Total units (20%)
- Sell-through rate: Units sold / (Beginning + Received) (15%)
- In-stock rate: Days in stock / (Target inventory days) (60%)
```

**IPI scoring:**
```
IPI Score Range | Status | Impact
≥ 500          | Excellent | Full selling privileges
400-499        | Good | No restrictions
350-399        | Acceptable | Consider plan (warning level)
< 350          | Poor | Suspension likely if not improved
```

**IPI example scorecard (April 2026):**

```
Scenario A: Premium electronics seller
- Excess inventory rate: 2% (5 points weight) = 2 × 5 = 10
- Stranded inventory rate: 1% (20 points weight) = 1 × 20 = 20
- Sell-through rate: 85% (15 points weight) = 85 × 15 = 1,275 (capped at 15)
- In-stock rate: 95% (60 points weight) = 95 × 60 = 5,700 (capped at 60)
- IPI = (10 + 20 + 15 + 60) / 100 = 105... [weighted formula applied]
- Result: IPI ~480 (Good status)

Scenario B: Struggling fashion seller
- Excess inventory rate: 18% → 18 × 5 = 90 points
- Stranded inventory rate: 12% → 12 × 20 = 240 points
- Sell-through rate: 55% → 55 × 15 = 825 → capped at 15
- In-stock rate: 70% → 70 × 60 = 4,200 → capped at 60
- Result: IPI ~325 (Poor status - improvement plan required)
```

---

### 3.6 Inventory Value Analysis (ABC Method)

**Pareto Principle (80/20 rule) in inventory:**
```
A Items: ~20% of SKUs = ~80% of total value → Tight control
B Items: ~30% of SKUs = ~15% of total value → Medium control
C Items: ~50% of SKUs = ~5% of total value → Loose control
```

**Calculation:**
```
1. List all SKUs with value (unit cost × units on hand)
2. Sort by value descending
3. Calculate cumulative % of value
4. Assign A (0-80%), B (80-95%), C (95-100%)

Example inventory (€):
- SKU001: €50,000 (35.5%) → A
- SKU002: €35,000 (24.9%) → A (cumulative 60.4%)
- SKU003: €20,000 (14.2%) → B (cumulative 74.6%)
- SKU004: €18,000 (12.8%) → B (cumulative 87.4%)
- SKU005: €7,000 (5.0%) → C (cumulative 92.4%)
- SKU006: €10,500 (7.5%) → C (cumulative 99.9%)

Result: A=2 SKUs, B=2 SKUs, C=2 SKUs
```

**Control policies by ABC classification:**

| Policy | A Items | B Items | C Items |
|--------|---------|---------|--------|
| Order review | Weekly | Bi-weekly | Monthly |
| Safety stock level | 95% SL | 90% SL | 80% SL |
| Physical count | Monthly | Quarterly | Semi-annually |
| Lead time focus | Minimize | Standard | Not critical |
| Damage tolerance | <0.5% | <1% | <5% |
| Supplier audit | Quarterly | Annual | Ad-hoc |

---

### 3.7 GMROI (Gross Margin Return on Investment)

**Formula:**
```
GMROI = (Gross Margin € / Average Inventory €) per period
Or: GMROI = Gross Margin % × Inventory Turnover

Measures profitability of each euro invested in inventory
```

**Example:**
```
- Sales: €100,000
- COGS: €60,000
- Gross Margin: €40,000 (40%)
- Average Inventory: €25,000

GMROI = €40,000 / €25,000 = 1.6
Interpretation: Each €1 of inventory generates €1.60 gross profit per year

Alternative: 40% margin × 4 turns/year = 1.6 GMROI
```

**GMROI benchmarks by category (EU marketplace):**

| Category | Target GMROI | Excellent | Good | Acceptable |
|----------|--------------|-----------|------|----------|
| Electronics | 2.5-3.5 | >3.5 | 2.5-3.5 | 1.5-2.5 |
| Apparel | 2.0-3.0 | >3.0 | 2.0-3.0 | 1.0-2.0 |
| Home & Garden | 1.8-2.5 | >2.5 | 1.8-2.5 | 1.0-1.8 |
| Books | 2.0-2.8 | >2.8 | 2.0-2.8 | 1.2-2.0 |
| Furniture | 0.8-1.5 | >1.5 | 0.8-1.5 | 0.4-0.8 |

**Improving GMROI:**
```
GMROI = Gross Margin % × Inventory Turnover

Path 1: Increase margin (via pricing/cost reduction)
- Improve sourcing: -10% COGS → margin +10%
- Price optimization: +5% prices → margin +5%

Path 2: Increase turnover (via demand generation)
- Improve forecast accuracy: Reduce safety stock → faster turns
- Promotional velocity: Category growth → volume increase
- Remove slow movers: Improve average turns

Path 3: Hybrid
- Better SKU selection (focus on high GMROI items)
- Reduce stranded inventory (fast clearance)
- Optimize reorder points (less excess buffer)
```

---

## 4. SUPPLIER PERFORMANCE MANAGEMENT (150+ lines)

### 4.1 Supplier Scorecard Framework

**Supplier evaluation criteria:**

| Metric | Weight | Excellent | Good | Acceptable | Poor |
|--------|--------|-----------|------|-----------|------|
| On-Time Delivery (OTD) | 30% | >95% | 90-95% | 80-90% | <80% |
| Quality (Defect Rate) | 25% | <1% | 1-2% | 2-3% | >3% |
| Price Competitiveness | 20% | -5% to 0% | 0-2% | 2-5% | >5% |
| Communication/Responsiveness | 15% | 24h response | <48h | <72h | >72h |
| Compliance (RoHS, customs) | 10% | 100% | 98-100% | 95-98% | <95% |

**Supplier Score calculation:**
```
Score = (OTD% × 0.30) + (100-Defect% × 0.25) + (Price Index × 0.20) + (Comms Score × 0.15) + (Compliance% × 0.10)

Example:
- OTD 94% × 0.30 = 28.2
- Defects 1.2%, Quality 98.8% × 0.25 = 24.7
- Price +2.5% vs benchmark × 0.20 = 14 (declining)
- Comms <48h = 95/100 × 0.15 = 14.25
- Compliance 99.5% × 0.10 = 9.95
- Total Score = 91.1 → Tier A (Excellent)
```

**Supplier Tier classification:**

| Tier | Score | Status | Treatment | Reaudit Freq |
|------|-------|--------|-----------|----------|
| A | 90+ | Strategic partner | Preferred orders, volume incentives | Quarterly |
| B | 75-89 | Acceptable | Standard terms | Bi-annually |
| C | 60-74 | At-risk | Improvement plan required | Quarterly |
| D | <60 | Failing | Suspension unless plan signed | Monthly |

---

### 4.2 Lead Time Management

**Supplier lead time tiers (EU marketplace context):**

| Lead Time Category | Days | Cost Impact | Risk Level | Examples |
|-------------------|------|------------|-----------|----------|
| Ultra-fast | 1-3 days | -10% inventory cost | Very low | Local EU warehouses |
| Fast | 4-10 days | -5% inventory cost | Low | EU manufacturers |
| Standard | 11-21 days | Baseline | Medium | Turkey, Poland suppliers |
| Extended | 22-35 days | +20% inventory cost | Medium-high | Sea freight (EU ports) |
| Very long | 36-60 days | +50% inventory cost | High | Direct sea from Asia |
| Seasonal | 20-90 days | Variable | Very high | Pre-season orders |

**Lead time negotiation impact:**
```
Reducing lead time from 35 to 21 days (6% reduction):
- Safety stock: 263 units → 157 units (-40%)
- Average inventory: 350 units → 210 units (-40%)
- Inventory cost: €8,750/year → €5,250/year (-€3,500)
- This €3,500 saving justifies up to ~€2,000 in premium freight cost
```

**Lead time variability tracking:**
```
Supplier X (sea freight, China):
- Average lead time: 38 days
- Std dev: 6.2 days
- Variability: 16.3%
- Risk: 1 in 10 orders miss by 2+ weeks
- Action: Use air freight for high-demand SKUs, negotiate modal insurance

Supplier Y (rail, Turkey):
- Average lead time: 18 days
- Std dev: 1.8 days
- Variability: 10%
- Risk: Very low
- Action: Can reduce safety stock by 20%
```

---

### 4.3 Quality Control & Defect Management

**Incoming inspection protocol (FBA-compliant):**
```
Random sample size (AQL = 1%):
- Shipment 0-150 units: Inspect all 100%
- Shipment 151-500 units: Inspect 50 units (minimum)
- Shipment 501-5,000 units: Inspect 125 units (minimum)
- Shipment 5,001+ units: Inspect 200 units (minimum)

Acceptance criteria:
- ≤1% defects: Accept shipment
- 1-3% defects: Conditional accept (notify supplier)
- >3% defects: Reject, claim dispute/return
```

**Defect root causes & resolution:**

| Defect Type | % of Total | Common Causes | Resolution Time |
|------------|-----------|---------------|-----------|
| Cosmetic damage | 30-40% | Packaging, handling | 10-20 days |
| Manufacturing defect | 20-30% | QC failure | 30-45 days |
| Missing components | 10-20% | Assembly error | 15-25 days |
| Wrong SKU shipped | 5-10% | Order confusion | 20-30 days |
| Labeling error | 5-15% | Printing mistake | 10-15 days |
| **Total defect rate target** | **<1%** | Process improvement | Continuous |

---

### 4.4 Bulk Order Management

**MOQ (Minimum Order Quantity) negotiation:**
```
Supplier quote: MOQ 5,000 units @ €2.00
Your annual need: 12,000 units
Total cost: 3 orders × €10,000 = €30,000

Alternative: Negotiate MOQ 3,000 @ €2.05
Total cost: 4 orders × €6,150 = €24,600
Savings: €5,400 (18%)

Trade-off: Higher order frequency, but inventory cost savings exceed handling costs
```

**Seasonal bulk ordering strategy:**
```
Toys category (peak Nov-Dec):
- May-June: Order 40,000 units (40% annual demand) @ bulk rates
- August: Order 30,000 units (30% annual demand)
- September: Order 20,000 units (20% annual demand)
- October-March: Light reorder 10,000 units (10% annual demand)

Benefit:
- Lock in favorable pricing during off-peak negotiation
- Build buffer for holiday peak demand
- Reduce emergency expediting costs
- Average inventory carries higher margin
```

---

## 5. INVENTORY SYSTEMS & TECHNOLOGY (150+ lines)

### 5.1 Inventory Management System Requirements

**Core functionality:**
- Real-time stock tracking (by warehouse, bin, shelf)
- Multi-marketplace sync (Amazon, eBay, Cdiscount, Zalando, own store)
- Demand forecasting engine (Prophet, XGBoost integration)
- Reorder point automation (with alerts)
- Cycle count & physical count modules
- Supplier performance tracking
- Stranded inventory reporting
- Forecast accuracy metrics dashboard
- Profitability analysis (GMROI, margin by SKU)
- API integrations (logistics, suppliers, marketplaces)

**Recommended tech stack (2026):**
```
Core platform: Shopify (with apps) OR NetSuite (enterprise)
Forecast engine: Demand-Sense, Demand Works, or custom ML (Prophet)
Warehouse: Infor/Oracle WMS or custom (for high volume)
Connectors: Zapier, Make, custom APIs
Reporting: Tableau, Looker, or built-in dashboards
```

**Integration map (typical EU marketplace seller):**
```
Amazon FBA ←→ IMS ←→ Shopify
         ↓      ↓       ↓
      ERP  Forecast  Analytics
        ↓      ↓       ↓
    Accounting Supplier Portal BI
```

---

### 5.2 Data Architecture for Inventory

**Data entities required:**
```
Core tables:
- SKU_Master (SKU ID, name, supplier, cost, category, dimensions)
- Inventory_Ledger (SKU, warehouse, qty on hand, reserved, damaged)
- Daily_Demand (SKU, date, qty sold, marketplace, channel)
- Forecast (SKU, date, predicted demand, confidence)
- Supplier_Lead_Time (supplier, commodity, avg lead time, variability)
- Safety_Stock (SKU, service level %, calculated SS units)
- Reorder_Points (SKU, ROP units, EOQ, last reorder date)
- Defect_Log (SKU, supplier, date, defect reason, quantity)
- Price_History (SKU, date, cost, selling price, supplier)
```

**Required integrations:**
- Amazon MWS/SP-API (inventory sync, sales data)
- eBay API (listings, sales)
- Supplier EDI/API (POs, ASNs, invoices)
- Payment processor (settlement data)
- Shipping provider (tracking, costs)

---

### 5.3 Automation Opportunities

**Reorder automation rules:**
```
IF inventory_level ≤ ROP:
  → Auto-create PO to primary supplier
  → If lead time > 10 days: Alert buyer
  → If expected stockout < 5 days: Trigger airfreight option
  → Auto-flag high-risk SKUs for manual review

IF forecast_accuracy < category_benchmark:
  → Weekly email alert to planner
  → Review external factors (price changes, promos)
  → Adjust forecast parameters
```

**Inventory optimization automation:**
```
Daily jobs:
- Update forecast (new demand data)
- Recalculate safety stock (rolling 90-day demand variability)
- Identify stranded inventory (>90 days no sales)
- Calculate excess inventory ratio
- Monitor IPI score drift

Weekly:
- Review reorder points vs. actual inventory
- Identify dead stock candidates
- Price optimization recommendations
- Supplier performance summary

Monthly:
- ABC analysis refresh
- GMROI analysis by category
- Forecast accuracy report
- Supplier scorecard update
- Inventory aging report
```

---

## 6. COMPLIANCE & GOVERNANCE (100+ lines)

### 6.1 Amazon FBA Compliance Requirements

**Inventory limits (2026):**
```
Monthly storage:
- Oct-Dec: No limit (holiday season)
- Jan-Sept: Limited based on sales history
- New sellers: 300 units or 1.4 m³ (whichever is lower)
- Established: ~20× average monthly sales unit count

Excess inventory surcharges:
- 0-10% of units: No charge
- 10-20%: €0.10/unit/day (~€30/unit/year)
- 20-40%: €0.20/unit/day (~€60/unit/year)
- >40%: €0.30/unit/day (~€90/unit/year)

Long-term storage:
- 0-365 days: 0.50/unit (on Jan 15 & July 15)
- 365+ days: €3.00/unit + liquidation fee
```

**IPI compliance:**
- Target: IPI ≥400 (no action required)
- Warning: IPI 350-399 (submit improvement plan)
- Suspension: IPI <350 for 30+ days
- Recovery: Must reach IPI 400+ within 30 days of plan

---

### 6.2 Data Protection & GDPR

**Inventory data classifications:**
- Personal data: Customer names, addresses (only where needed for FBA)
- Business confidential: Cost, supplier contracts, forecasts
- Operational: Stock levels, sales data, demand

**Storage requirements:**
- EU data centers only (GDPR compliance)
- Encrypted at rest & in transit
- 7-year retention (tax law)
- Annual audit

---

## 7. RISK MANAGEMENT & CONTINGENCY (100+ lines)

### 7.1 Supply Chain Disruption Planning

**Tier 1 risks (severe, >2 weeks impact):**
- Supplier bankruptcy → Activate backup supplier immediately
- Port strike (Europe) → Shift to air freight or alternate port
- Customs delays → Buffer inventory +30 days demand
- Transportation accident → Trigger insurance claim, expedite replacement

**Mitigation:**
- Dual-source critical categories (A items)
- Safety stock buffer for Tier 1 risks
- Insurance coverage (cargo, liability)
- Alternate supplier agreements (in place, tested)  
- Supply chain visibility platform

---

### 7.2 Demand Shock Management

**Demand surge (>150% forecast):**
- First 48 hours: Expedite supplier order
- 48-7 days: Air freight option if justified
- 7-14 days: Accept backorder or redirect to secondary marketplace
- 14+ days: Sourcing from alternate suppliers/liquidation partners

**Demand collapse (<50% forecast):**
- Immediately reduce new orders
- Activate promotional clearance
- Evaluate excess inventory disposition
- Review forecast model for systematic bias

---

### 7.3 Obsolescence Risk

**Product lifecycle management:**
```
Introduction (0-6 months): 
- Lower safety stock (test demand)
- Weekly forecast updates
- Rapid scaling if traction

Growth (6-18 months):
- Safety stock increases with demand stability
- Standard reorder cycle
- Monitor competitive threats

Maturity (18-36 months):
- Peak inventory investment
- Stable forecast parameters
- Watch for market saturation

Decline (36+ months):
- Reduce order sizes by 50%
- Accelerate clearance pricing
- Plan phase-out/replacement
- Evaluate discontinuation date
```

**Write-off process:**
```
90-day stranded inventory:
- Relist at -20% markdown
- Consider liquidation to third parties (costs)

180-day dead stock:
- Relist at -50% markdown
- Contact influencers for promotional swap
- Donate to charities (tax deduction in some markets)

365-day obsolete:
- Remove from sale
- Scrap or recycle
- Expense full inventory cost
- Document for tax purposes
```

---

## 8. EUROPEAN MARKETPLACE-SPECIFIC GUIDELINES (100+ lines)

### 8.1 Amazon EU Requirements

**Country-specific considerations:**

| Country | Compliance Notes | Inventory Practice | Peak Season |
|---------|------------------|-------------------|----------|
| Germany | Strict packaging, VAT ID | High turnover expected | Nov-Dec |
| France | Product language requirement | Conservative inventory | Nov-Dec |
| Italy | Import duties awareness | Longer lead times (customs) | Aug, Nov-Dec |
| Spain | Marketplace competition high | Competitive pricing | Nov-Dec |
| Poland | Lower price sensitivity | Larger safety stock buffer | Nov-Dec |
| UK | Post-Brexit border delays | Separate UK FBA system | Nov-Dec |

**FBA storage fees by country (2026):
- Germany/UK/France: €0.24/unit/month (standard)
- Italy/Spain/Poland: €0.18/unit/month (lower costs)
- Minimum storage: €0.50/unit/month

---

### 8.2 eBay.co.uk / eBay.de / eBay.fr

**Inventory management:**
- No FBA equivalent (MFN = Managed for Network)
- Self-managed warehouse or dropship
- Inventory sync critical to avoid oversell
- No storage surcharges (but carrying costs apply)

**STR expectations:**
- Fast-movers: >100% (undersupply risk)
- Standard: 70-100%
- Slow movers: <50% (discontinue)

---

### 8.3 Cdiscount (French marketplace)

**Inventory requirements:**
- 3-5 days fulfillment SLA
- Stock at regional warehouse (Cdiscount logistics partner)
- Monthly inventory audits
- Liquidation holds for excess inventory

**Performance metrics:**
- Order defect rate < 1%
- On-time delivery > 98%
- Inventory accuracy > 98%

---

### 8.4 Zalando B2B (Fashion/Sportswear)

**Inventory placement process:**
```
Season Pre-order (April for AW, Dec for SS):
- Submit SKU lineup with quantities
- Zalando accepts/rejects by size/color
- Typical order: 1,000-5,000 units per SKU
- Lead time: 12-16 weeks (production time)

In-season reorders (2-4 per season):
- Based on sell-through performance
- Smaller quantities (250-500 units)
- Faster lead time (6-8 weeks)

Inventory aging:
- 0-60 days in-season: Full margin (60% typically)
- 60-120 days: Markdown 20% (-margin 12%)
- 120-180 days: Markdown 50% (-margin 30%)
- 180+ days: Manual clearance or return to supplier
```

---

## 9. IMPLEMENTATION ROADMAP (100+ lines)

### 9.1 Phase 1: Foundation (Months 1-3)

**Objectives:**
- Establish demand forecasting process
- Calculate safety stock/ROP by SKU
- Set up performance metrics dashboard

**Deliverables:**
- Demand forecast model (simple 4-week SMA baseline)
- Safety stock calculations (Z-score based)
- Inventory accuracy baseline audit
- ABC classification

**Resources:**
- 1 inventory analyst (full-time)
- IMS tool selection/implementation (Shopify + app vs NetSuite)
- Spreadsheet infrastructure until system ready

---

### 9.2 Phase 2: Optimization (Months 4-6)

**Objectives:**
- Transition to advanced forecasting (Holt-Winters or Prophet)
- Implement reorder automation
- Supplier performance tracking

**Deliverables:**
- Forecast accuracy monitoring (MAPE, MAE)
- Automated PO generation for A/B items
- Supplier scorecard (quarterly review cycle)
- Excess inventory alerts

**Resources:**
- ML engineer (for forecast model fine-tuning)
- Supplier relationship manager (new role or reassign)
- System integration specialist

---

### 9.3 Phase 3: Excellence (Months 7-12)

**Objectives:**
- Full automation (except exceptions)
- Multi-marketplace optimization
- Advanced analytics (GMROI, profitability by SKU)

**Deliverables:**
- Machine learning forecasting (XGBoost or ensemble)
- Stranded inventory automation
- Profitability dashboard by category/supplier
- Supply chain visibility (tracking, lead time monitoring)

**Resources:**
- Senior data scientist (ongoing)
- Business analyst (reporting/insights)
- Continuous improvement program

---

**End of Part 2**