# Domain 5.2: Cost Accounting & Unit Economics — Part 2
## Landed Cost, Profitability Analysis, Cost Allocation

---

## 4. LANDED COST CALCULATION

### 4.1 Landed Cost Formula

```
Landed_Cost_per_Unit = (Product_Cost + Inbound_Shipping + Customs_Duty + VAT + Handling + Packaging) ÷ Units

LC = PC + IS + CD + VAT + HC + PKG
```

### 4.2 Origin-Specific Calculations

#### A. China Origin (High-volume, slower)

**Example: 1,000 units of electronics**
```
Product Cost (FOB Shanghai): €3,000 (€3/unit)
Sea Freight (LCL, 20 days): €400
Documentation/Customs broker: €100
Customs Duty (10%): €300
VAT (22%): €814
Warehouse Handling (€0.05/unit): €50
Packaging/Labeling (€0.20/unit): €200

Total Landed Cost: €4,864
Per Unit: €4.86
```

**Duty Calculation:**
```
Duty_payable = (Product_Cost + Shipping) × Duty_Rate
= (€3,000 + €400) × 10% = €340
```

**VAT Calculation:**
```
VAT_base = Product_Cost + Shipping + Duty
= €3,000 + €400 + €340 = €3,740
VAT_22% = €3,740 × 0.22 = €822.80
```

#### B. Turkey Origin (Moderate cost, GSP benefits)

**Example: 500 units of textiles**
```
Product Cost (FOB Istanbul): €1,500 (€3/unit)
Air Freight (3 days): €750
Documentation: €50
Customs Duty (12%, GSP may reduce): €90 (after GSP benefit)
VAT (22%): €330
Warehouse Handling (€0.10/unit): €50
Packaging (€0.15/unit): €75

Total: €2,935
Per Unit: €5.87 (vs €6.47 without GSP benefit)
```

#### C. EU Origin (Intra-EU, no customs)

**Example: 2,000 units from Germany supplier**
```
Product Cost (EXW Frankfurt): €8,000 (€4/unit)
Ground Freight (2 days): €300
Documentation: €0
Customs Duty: €0 (intra-EU)
VAT: Deductible (not in cost)
Warehouse Handling (€0.03/unit): €60
Packaging (€0.10/unit): €200

Total Landed Cost: €8,560
Per Unit: €4.28
```

**VAT Treatment (Intra-EU):**
```
Intra-EU invoices are zero-rated (0% VAT).
VAT is only on final sale to customer.
No customs duty payable.
```

### 4.3 Total Landed Cost Per Unit by Origin

| Origin | Product Cost | Freight | Customs | VAT | Handling | Packaging | **Total/Unit** |
|--------|------------|---------|---------|-----|----------|-----------|----------|
| China | €3.00 | €0.40 | €0.34 | €0.81 | €0.05 | €0.20 | €4.80 |
| Turkey | €3.00 | €1.50 | €0.18 | €0.37 | €0.10 | €0.15 | €5.30 |
| EU (Germany) | €4.00 | €0.15 | €0.00 | €0.00* | €0.03 | €0.10 | €4.28 |

*VAT deductible; not part of inventory cost under IFRS.

---

## 5. PROFITABILITY ANALYSIS

### 5.1 Contribution Margin Waterfall

**For iPhone 15 Case (€25 selling price, Amazon FBA)**

```
Selling Price                                €25.00  (100.0%)
  Less: COGS (€4.80)                       -€4.80   (-19.2%)
Gross Margin                                €20.20   (80.8%)
  Less: Amazon Referral (15%)              -€3.75   (-15.0%)
  Less: FBA Fulfillment (€0.65)            -€0.65   (-2.6%)
  Less: Return provision (3%)              -€0.75   (-3.0%)
Contribution Margin (Fulfillment)           €14.65   (58.6%)
  Less: Advertising/Marketing (ACOS 25%)  -€6.25   (-25.0%)
Contribution Margin (After Marketing)       €8.40    (33.6%)
  Less: Admin/Overhead allocation (€1)    -€1.00   (-4.0%)
Net Profit per Unit                         €7.40    (29.6%)
```

### 5.2 Break-Even Analysis

**Formula:**
```
Break_Even_Units = Fixed_Costs ÷ Contribution_Margin_per_Unit
Break_Even_Revenue = Fixed_Costs ÷ Contribution_Margin_%
```

**Example: Kaufland Launch**
```
Monthly Fixed Costs: €2,000
Product CM: €8.50 per unit
CM%: 34%

Break_Even_Units = €2,000 ÷ €8.50 = 235 units/month
Break_Even_Revenue = €2,000 ÷ 0.34 = €5,882/month

At €25 selling price: 235 units × €25 = €5,875
```

### 5.3 SKU/Marketplace/Supplier Profitability Matrix

**Example Dashboard:**

| SKU ID | Supplier | Marketplace | Units/Month | Avg Price | COGS | CM/Unit | Total CM | Rank |
|--------|----------|------------|------------|-----------|------|---------|----------|------|
| SK-001 | Supplier A | Amazon | 500 | €25 | €4.80 | €8.40 | €4,200 | 1 |
| SK-002 | Supplier B | eBay | 120 | €18 | €5.00 | €4.80 | €576 | 5 |
| SK-003 | Supplier A | Bol.com | 350 | €22 | €4.80 | €6.90 | €2,415 | 3 |
| SK-004 | Supplier C | Amazon | 200 | €15 | €6.00 | €2.50 | €500 | 8 |
| SK-005 | Supplier B | Kaufland | 450 | €20 | €5.00 | €6.50 | €2,925 | 2 |

**Analysis:** SK-001 on Amazon most profitable; SK-004 least profitable (high COGS).

---

## 6. COST ALLOCATION MODELS

### 6.1 Shared Costs Allocation Methods

**Scenario: €100,000 monthly overhead**

#### Method 1: Allocation by Units Handled
```
Total units: 2M/month
Overhead per unit: €100,000 ÷ 2M = €0.05/unit

SKU-A (100k units): €100,000 × (100k ÷ 2M) = €5,000
SKU-B (400k units): €100,000 × (400k ÷ 2M) = €20,000
SKU-C (1.5M units): €100,000 × (1.5M ÷ 2M) = €75,000
```

#### Method 2: Allocation by Revenue
```
Total revenue: €2,000,000/month
Overhead per €1 of sales: €100,000 ÷ €2M = €0.05

SKU-A revenue (€500k): €500k × €0.05 = €25,000
SKU-B revenue (€300k): €300k × €0.05 = €15,000
SKU-C revenue (€1,200k): €1,200k × €0.05 = €60,000
```

#### Method 3: Allocation by Storage Space (ABC)
```
Warehouse capacity: 10,000 m³
Overhead per m³: €100,000 ÷ 10,000 = €10

SKU-A (500 m³): 500 × €10 = €5,000
SKU-B (2,000 m³): 2,000 × €10 = €20,000
SKU-C (7,500 m³): 7,500 × €10 = €75,000
```

**Recommendation:** Use ABC method (Method 3) for Vendiamonoi.it to reflect true resource consumption.

### 6.2 Transfer Pricing (Multi-entity structure)

**Arm's Length Principle (EU/OECD):**
```
Transfer_Price = Market_Price for similar goods ± adjustments

Or: Cost-Plus Method
Transfer_Price = Cost + Markup%

Example:
Cost of logistics service: €500k
Reasonable markup: 15-25%
Transfer price: €500k × 1.20 = €600k
```

**Journal Entry (Intercompany sale):**
```
Debit: Accounts Receivable (Subsidiary)  €600,000
  Credit: Revenue (Parent)                     €600,000
(Recording transfer of logistics services)
```

---

**Part 2 Complete**