# Domain 5.2: Cost Accounting & Unit Economics — Part 3
## Inventory Carrying Costs, Financial Models, Benchmarks & Reporting

---

## 7. INVENTORY CARRYING COSTS

### 7.1 Components of Inventory Carrying Cost

**Formula:**
```
Annual_Carrying_Cost = Avg_Inventory_Value × Carrying_Cost_Rate

Carrying_Cost_Rate = Storage_Cost% + Insurance% + Obsolescence% + Financing_Cost%
```

### 7.2 Storage Costs

**Amazon LTSF (Long-Term Storage Fee):**
```
Items > 365 days in fulfillment center:
€6.50 per unit (June)
€6.50 per unit (December)

Monthly example: 5,000 units excess inventory
Annual LTSF: 5,000 × €6.50 × 2 = €65,000
```

**3PL Warehouse Costs:**
```
Basic storage: €0.50/m³/month or €2.50 per pallet/month
Climate control: +€0.10/m³/month
Picking labor: €0.02-€0.05 per unit handled
```

### 7.3 Insurance & Obsolescence

**Insurance (0.5-1.5% of inventory value):**
```
Inventory value: €500,000
Insurance rate: 1%
Annual insurance: €5,000
```

**Obsolescence Reserve (3-8% for seasonal goods):**
```
Fast-moving electronics: 3% obsolescence rate
Annual reserve on €1M inventory: €1M × 0.03 = €30,000
```

### 7.4 Financing Cost

**For inventory financed by debt or equity:**
```
Avg_Inventory = €750,000
Cost_of_Capital = 8%
Annual financing cost: €750,000 × 0.08 = €60,000
```

### 7.5 Total Carrying Cost Example

```
Average inventory value: €1,000,000

Storage (LTSF + 3PL): €60,000 (6%)
Insurance: €10,000 (1%)
Obsolescence: €50,000 (5%)
Financing: €80,000 (8%)

Total Carrying Cost: €200,000 (20% of inventory value)
Per unit (2M SKUs): €0.10 per unit
```

### 7.6 Inventory Turnover & Optimal Levels

**Inventory Turnover Ratio:**
```
Turnover = COGS ÷ Avg_Inventory_Value
Days_Inventory = 365 ÷ Turnover

Example:
Annual COGS: €5,000,000
Avg inventory: €1,000,000
Turnover: 5x
Days inventory: 73 days
```

**Optimal Turnover by Category:**
```
Electronics (fast-moving): 8-12x/year (30-45 days)
Fashion (seasonal): 4-6x/year (60-90 days)
Books: 6-8x/year (45-60 days)
Home/Garden: 3-4x/year (90-120 days)
```

---

## 8. FINANCIAL MODELING TEMPLATES

### 8.1 Unit Economics Model (Monthly)

**Variables:**
```
SP = Selling Price
COGS = Cost of Goods Sold
MF = Marketplace Fee %
FF = Fulfillment Fee per unit
RR = Return Rate %
ACOS = Ad Cost of Sale %
```

**Formula Sheet:**
```
Gross Revenue = Units Sold × SP
Marketplace Fees = (Gross Revenue × MF) + (Units Sold × FF)
Return Costs = Gross Revenue × RR
Advertising Cost = Gross Revenue × ACOS
Total Variable Costs = COGS + Marketplace Fees + Return Costs + Advertising Cost
Contribution Margin = Gross Revenue - Total Variable Costs
CM% = (Contribution Margin ÷ Gross Revenue) × 100
```

**Example (1,000 units, Amazon):**
```
SP: €25
Units: 1,000
Gross Revenue: €25,000

COGS (€4.80): €4,800
Marketplace Fee (15% + €0.65): €4,150
Returns (3%): €750
Advertising (25% ACOS): €6,250

Total Variable Costs: €15,950
Contribution Margin: €9,050
CM%: 36.2%
```

### 8.2 Scenario Analysis Template

**Conservative Scenario:**
```
Monthly units: 100
Sell price: €20
COGS: €5.00
Marketplace fees: €3.00
CM/unit: €12.00
Monthly CM: €1,200
```

**Base Scenario:**
```
Monthly units: 300
Sell price: €20
COGS: €4.80
Marketplace fees: €2.80
CM/unit: €12.40
Monthly CM: €3,720
```

**Aggressive Scenario:**
```
Monthly units: 600
Sell price: €22 (price increase)
COGS: €4.50 (economies of scale)
Marketplace fees: €2.70
CM/unit: €14.80
Monthly CM: €8,880
```

### 8.3 Break-Even Analysis Template

**For Kaufland entry:**
```
Fixed Costs (annual):
Setup & optimization: €5,000
Account management: €12,000
Advertising: €24,000
Total: €41,000

Product CM: €9.00/unit
CM%: 36%

Break-even units: €41,000 ÷ €9 = 4,556 units/year (380/month)
Break-even revenue: €41,000 ÷ 0.36 = €113,889/year

Expected sales: 600/month = €7,200/month revenue
Estimated contribution: 600 × €9 = €5,400/month
Payback: €41,000 ÷ €5,400 = 7.6 months
```

### 8.4 Contribution Margin Sensitivity Analysis

**ACOS Impact (Amazon FBA, €25 product):**

| ACOS | Revenue | Referral | FBA | Returns | Advertising | Total Costs | CM | CM% |
|------|---------|----------|-----|---------|------------|----------|-------|------|
| 15% | €25 | €3.75 | €0.65 | €0.75 | €3.75 | €9.90 | €15.10 | 60.4% |
| 20% | €25 | €3.75 | €0.65 | €0.75 | €5.00 | €10.15 | €14.85 | 59.4% |
| 25% | €25 | €3.75 | €0.65 | €0.75 | €6.25 | €11.40 | €13.60 | 54.4% |
| 30% | €25 | €3.75 | €0.65 | €0.75 | €7.50 | €12.65 | €12.35 | 49.4% |

**Insight:** Each 5% ACOS increase reduces CM% by ~5%.

### 8.5 Cost Allocation Waterfall (Monthly)

**Total revenue: €100,000**

```
Revenue                                 €100,000
Less: COGS (20%)                        €(20,000)
Gross Margin                             €80,000
Less: Marketplace fees (15%)             €(15,000)
Less: Fulfillment (5%)                   €(5,000)
Less: Returns provision (2%)             €(2,000)
Gross Contribution                       €58,000
Less: Advertising (20%)                  €(20,000)
Contribution Margin (Marketing)          €38,000
Less: Shared overhead (10%)              €(10,000)
Operating Profit                         €28,000
Operating Margin %                        28.0%
```

---

## 9. BENCHMARK MARGINS BY CATEGORY

### Italian Market Benchmarks

| Category | Avg Sell Price | Typical CM% | Top Performer CM% | Risk Factor |
|----------|---|---|---|---|
| Electronics | €50-100 | 28-35% | 40%+ | High return rates, obsolescence |
| Fashion/Shoes | €25-80 | 35-45% | 50%+ | Seasonal, size returns |
| Books/Media | €15-50 | 25-30% | 35% | Low margin category |
| Home/Kitchen | €30-150 | 32-40% | 48%+ | Bulky shipping costs |
| Sports/Outdoors | €40-200 | 30-38% | 45% | Seasonal demand |
| Beauty/Health | €15-100 | 40-50% | 60%+ | High markup, lower COGS |
| Toys | €20-80 | 35-45% | 52% | Seasonal, inventory risk |

**Key Insight:** Beauty/Health highest margin; Books lowest. Electronics most volatile.

---

## 10. JOURNAL ENTRIES (OIC 13 COMPLIANCE)

### Purchase of Inventory (China)

```
Debit: Inventory - Raw Materials    €4,800
Debit: Customs & Duty Payable         €340
Debit: Shipping - Inbound            €400
  Credit: Accounts Payable (Supplier)      €5,540
(Purchase of 1,000 units @ €4.80 FOB Shanghai)
```

### Receipt of Goods at Warehouse

```
Debit: Inventory - Finished Goods   €5,540
  Credit: Inventory - Raw Materials       €4,800
  Credit: Customs Duty Clearing         €340
  Credit: Inbound Shipping              €400
(Transfer to finished goods upon receipt)
```

### Sale on Amazon (FBA)

```
Debit: Accounts Receivable (Amazon)      €25,000
Debit: Marketing Expense (Ads)           €6,250
  Credit: Revenue                              €25,000
  Credit: Advertising Payable                 €6,250
(Sale of 1,000 units @ €25, €6.25 ACOS)

And:
Debit: Cost of Goods Sold              €4,800
  Credit: Inventory - Finished Goods        €4,800
(Recording COGS at €4.80/unit)
```

### Amazon Fee Settlement

```
Debit: Amazon Fees Expense             €4,150
  Credit: Accounts Payable (Amazon)         €4,150
(Referral 15% + FBA €0.65 = €4.15/unit × 1,000)
```

### Return Processing (FBA)

```
Debit: Returns & Allowances              €750
Debit: Inventory - Returned Goods        €720
  Credit: Accounts Receivable (Amazon)        €750
  Credit: COGS                                €720
(Processing 3% return rate)
```

---

## 11. MANAGEMENT REPORTING TEMPLATE

**Monthly Scoreboard (by Marketplace):**

| Metric | Amazon | eBay | Bol.com | Kaufland | Target |
|--------|--------|------|---------|----------|--------|
| Revenue | €450k | €120k | €280k | €150k | €1.0M |
| Units | 18k | 6.5k | 14k | 7.5k | 50k |
| Avg Price | €25 | €18.46 | €20 | €20 | €20 |
| COGS% | 18% | 22% | 20% | 19% | 19% |
| Marketplace Fee% | 17.5% | 16% | 13% | 11% | 15% |
| CM% | 34% | 28% | 32% | 38% | 34% |
| Absolute CM | €153k | €33.6k | €89.6k | €57k | €340k |

---

## CONCLUSION

Key actions:
1. Implement ABC costing for 100+ suppliers
2. Monitor ACOS religiously — each 1% ACOS increase reduces CM by ~0.8%
3. Optimize SKU portfolio using profitability matrix
4. Standardize landed cost calculations across origins
5. Track marketplace fees closely
6. Manage inventory carrying — target 6-8x annual turnover
7. Monthly P&L review split by marketplace/supplier/category

**Target Operating Margin: 25-30%** (after all costs)

---

**Document prepared: April 2026**