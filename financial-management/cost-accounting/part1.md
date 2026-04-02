# Domain 5.2: Cost Accounting & Unit Economics — Part 1
## Cost Methods, Unit Economics, Marketplace Fees

**For: Vendiamonoi.it (Italian S.R.L.)**

---

## 1. COST ACCOUNTING METHODS

### 1.1 Standard Costing vs Actual Costing

**Standard Costing** sets predetermined costs for materials, labor, overhead based on historical data and budgets.

**Formula (Standard Cost per Unit):**
```
SC_unit = SM_std + SL_std + SO_std

Where:
SC_unit = Standard Cost per Unit
SM_std = Standard Material Cost per unit
SL_std = Standard Labor Cost per unit
SO_std = Standard Overhead Cost per unit
```

**Actual Costing** records real acquisition costs at time of purchase.

**For Vendiamonoi.it:** Actual costing preferred due to volatile supplier pricing and currency fluctuations across 100+ suppliers.

### 1.2 Activity-Based Costing (ABC)

**ABC Cost Formula:**
```
Total_Product_Cost = Direct Materials + Direct Labor + (Cost Pool 1 × Activity Driver 1) + (Cost Pool 2 × Activity Driver 2)
```

**Cost Pools for Vendiamonoi.it:**
1. **Procurement Pool:** €80,000/year ÷ 3,000 POs = €26.67 per PO
2. **Warehouse/Fulfillment:** €150,000/year ÷ 2M units = €0.075 per unit
3. **Marketplace Management:** €60,000/year ÷ 50,000 SKUs = €1.20 per SKU
4. **Returns Processing:** €40,000/year ÷ 8,000 returns = €5 per return

### 1.3 Inventory Valuation Methods (OIC 13)

**FIFO (First-In, First-Out):**
```
COGS = (100 × €10) + (150 × €11) = €2,650
Ending Inventory: 200 units @ €12 = €2,400
```

**LIFO (Last-In, First-Out):**
```
COGS = (150 × €12) + (100 × €11) = €2,900
Ending Inventory = €2,100
```

**Weighted Average:**
```
Weighted_Avg_Cost = €5,000 ÷ 450 = €11.11 per unit
COGS (250 units) = 250 × €11.11 = €2,777.75
```

**OIC 13 Requirement:** Italian companies must use FIFO or Weighted Average. LIFO discouraged for tax purposes.
**Recommendation for Vendiamonoi.it:** FIFO preferred.

---

## 2. UNIT ECONOMICS FRAMEWORK

### 2.1 Core Unit Economics Formula

```
Contribution_Margin_per_Unit = Selling_Price - COGS - Direct_Marketplace_Fees - Direct_Shipping - Direct_Advertising

CM% = (Contribution_Margin ÷ Selling_Price) × 100
Break_Even_Units = Fixed_Costs ÷ CM_per_unit
```

### 2.2 Cost Components Per Unit - Example

**Electronics Category (Smartphone Case)**

| Component | Amount | % of Sale |
|-----------|--------|----------|
| Selling Price | €25.00 | 100.0% |
| COGS (ex-VAT) | €5.00 | 20.0% |
| Amazon Referral Fee (15%) | €3.75 | 15.0% |
| FBA Fulfillment | €2.50 | 10.0% |
| Shipping to Amazon (€0.80/unit) | €0.80 | 3.2% |
| Return costs (3% loss) | €0.75 | 3.0% |
| Advertising (ACOS 25%) | €6.25 | 25.0% |
| **Contribution Margin** | **€5.95** | **23.8%** |

---

## 3. MARKETPLACE FEE DEEP-DIVE (EU MARKETPLACES)

### 3.1 Amazon EU Fee Structure

**Referral Fees (Variable by Category):**
- Electronics: 8%
- Clothing/Shoes: 17%
- Books: 15%
- Beauty: 8%
- Jewelry/Watches: 20%
- Maximum: 45% (luxury categories)

**FBA Fulfillment Fees (Standard-size):**
- Jan-Sep: €0.65 per unit
- Oct-Dec (holiday): €1.25 per unit
- Oversize: €0.70 surcharge

**Storage Fees:**
- Jan-Sep: €0.50 per unit per month
- Oct-Dec: €2.70 per unit per month
- Long-Term Storage (>365 days): €6.50/unit (June & Dec)

**Formula (Amazon Monthly Cost):**
```
Amazon_Cost_monthly = (Units_sold × Referral%) + (Units_sold × FBA_fee) + (Avg_inventory × Storage_fee)
Example: 1,000 units @ €25
= (1,000 × 0.15 × €25) + (1,000 × €0.65) + (500 × €0.50)
= €3,750 + €650 + €250 = €4,650
Effective fee: 18.6%
```

### 3.2 eBay EU Fee Structure

**Final Value Fee (FVF):**
- Electronics: 12-15%
- Clothing: 12%
- Books/Media: 10%
- Collectibles: 12%
- Minimum: €0.10 per item

**Insertion Fees:**
- Free: First 1,000 items/month
- Beyond: €0.03 per listing

**Payment Processing:**
- PayPal: 3.5% + €0.35 per transaction
- Integrated processor: 2.8% + €0.34

**Example (500 items @ €20):**
```
Sales: €10,000
FVF (12.5%): €1,250
Payment fee (3.5%): €350
Insertion: €30
Total: €1,630 (16.3% effective)
```

### 3.3 Bol.com (Netherlands/Belgium Leader)

**Commission Structure:**
- Electronics: 8-10%
- Fashion: 10-15%
- Home/Garden: 8-12%
- Books: 5-7%
- Beauty: 12-17%
- Toys: 12-17%

**Fulfillment by Bol (FBB):**
- Small (<100g): €0.80/unit
- Medium (100g-5kg): €1.50/unit
- Large (5-30kg): €2.50/unit

**Additional Fees:**
- Payment processing: 2.5%
- Returns handling (FBB): Included
- Listing: Free

**Example (300 units @ €30, Electronics):**
```
Commission (9%): €810
FBB (€1.50/unit): €450
Payment (2.5%): €225
Total: €1,485 (16.5% effective)
```

### 3.4 Other Major EU Marketplaces

**Kaufland (Germany/Romania/Slovakia/Czech)**
- Commission: 6.5-14.5%
- Payment processing: 2%
- Advertising: 0-5% optional
- Effective: 8.5-21.5%

**Cdiscount (France)**
- Commission: 8-12%
- Delivery fee: €0.99/item (if Cdiscount fulfills)
- Payment: 3.5%
- Effective: 12-19%

**Allegro (Poland)**
- Commission: 5-13%
- Payment: 1.99% + €0.20
- Smart! subscription: €6.99/month (20% fee reduction)
- Effective: 7.99-14.99%

**ManoMano (France/Germany/Spain/UK)**
- Commission: 8-15%
- Fulfillment (optional): €1.50-€4.00/item
- Payment: 3%
- Effective: 12-22%

**Fnac-Darty (France/Belgium/Luxembourg)**
- Commission: 10-18%
- Seller fee: €10/month
- Fulfillment (optional): €1.50-€3/item
- Payment: 2.5%
- Effective: 13-23.5%

**Leroy Merlin (France/Spain/Italy)**
- Commission: 8-12%
- Seller account fee: €15/month
- Payment: 3%
- Effective: 11-18%

### 3.5 Fee Comparison Table (Per €100 Sale)

| Marketplace | Commission | Fulfillment | Processing | Total Cost | Effective % |
|-------------|-----------|-------------|-----------|-----------|----------|
| Amazon (Std) | €15 | €6.50 | - | €21.50 | 21.5% |
| Amazon (Premium) | €15 | €2.50 | - | €17.50 | 17.5% |
| eBay | €12.50 | - | €3.50 | €16.00 | 16.0% |
| Bol.com | €9 | €1.50 | €2.50 | €13.00 | 13.0% |
| Kaufland | €10 | - | €2 | €12.00 | 12.0% |
| Allegro | €7 | - | €2 | €9.00 | 9.0% |
| ManoMano | €12 | €2 | €3 | €17.00 | 17.0% |

---

**Part 1 Complete**