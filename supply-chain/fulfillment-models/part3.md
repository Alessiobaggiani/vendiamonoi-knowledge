# SECTION 5: FULFILLMENT FOR NON-AMAZON MARKETPLACES

## 5.1 Bol.com Logistics Programs

### 5.1.1 "Logistics via Bol" (LvB) Program

**Program Structure:**
Bol.com's own fulfillment service where sellers send inventory to Bol's warehouses, and Bol handles fulfillment. Expanding to allow third-party 3PL providers.

**Current Offerings:**
- Seller ships inventory to Bol warehouse
- Bol receives, stores, and fulfills orders
- Available to 45,000+ sellers
- Cost: €0.95-€1.75 per order (pickup + packaging) + storage fee

**Future 3PL Network:**
- Bol building external fulfillment network (announced 2024)
- Partner logistics providers will fulfill Bol orders by late 2026
- Will allow sellers to use other 3PLs instead of Bol directly
- Expected providers: Huboo, ShipBob EU, local Dutch/Belgian 3PLs

**Requirements:**
- Seller account: In good standing (low return rate)
- Business registration: Valid Dutch/Benelux business
- VAT: Registered in Netherlands or EU

---

## 5.2 Zalando Fulfillment Solutions (ZFS)

### 5.2.1 ZFS Program Overview

**Program Structure:**
Pan-European fulfillment where sellers store inventory in Zalando's network across 8 European countries. Zalando handles fulfillment to 23 Zalando marketplace territories.

**Service Scope:**
- Inventory storage: Sellers send inventory to Zalando warehouse
- Fulfillment: Zalando picks, packs, ships to customers
- Returns: Customers return to Zalando; Zalando handles returns processing
- Multi-channel: Sellers can use ZFS for Zalando marketplace only (not third-party warehouses)

**Coverage:**
- Storage in: Germany, France, Italy, Spain, Netherlands, Poland, Czech Republic, Hungary
- Delivery to: 23 European markets (including UK, Turkey, Russia*)
- Shipping speed: 1-2 days to most Central European customers

**Costs (2026 Pricing):**
- Setup fee: €300-€500
- Monthly platform fee: €200-€500 (based on monthly sales)
- Fulfillment: €1.50-€3.00 per order (including picking, packing, shipping)
- Storage: €3.00-€4.50/m³/month
- Returns: €1.00-€2.00 per return processed

**Requirements:**
- Fashion/apparel focus (ZFS designed for apparel primarily)
- Minimum inventory: €5,000 value recommended
- Business standing: Account history, low return rates
- Brand compliance: Professional product photography, descriptions

**Advantages:**
- Pan-European reach with single integration
- Zalando handles returns (seller advantage)
- Competitive shipping rates
- Central location inventory for quick delivery

**Disadvantages:**
- Fashion-focused (difficult for non-apparel)
- High minimums (€5,000 inventory)
- Limited to Zalando marketplace (no dropshipping to other channels)
- Complex contract terms

---

## 5.3 Kaufland Fulfillment Options

### 5.3.1 Kaufland Fulfillment Service

**Program Structure:**
Kaufland (operating in Czech Republic, Poland, Romania, Slovakia, Bulgaria) offers limited fulfillment services. NOT a dedicated fulfillment service like Amazon FBA, but partnership with logistics providers.

**Current State (2026):**
- No centralized fulfillment like Bol or Zalando
- Sellers use own warehouse or 3PL
- Kaufland does NOT offer fulfillment service
- Sellers responsible for all logistics

**Seller Fulfillment Requirements:**
- Shipping times: 1-3 days to customers (region-dependent)
- Handling time: 2-5 days (seller-specified)
- Return processing: Seller handles; Kaufland policy requires acceptance

**3PL Integration:**
- Sellers use local 3PLs in each market
- Kaufland does not provide 3PL partnerships
- Sellers must integrate with 3PL separately
- API available but limited compared to Amazon

---

## 5.4 eBay Global Shipping Programme (GSP)

### 5.4.1 GSP Program Mechanics

**For Sellers:**
- Seller ships to eBay hub (UK hub primary)
- eBay handles international shipping from hub
- Seller receives single payment regardless of customer location

**Service Details:**
- eBay provides: International shipping logistics, customs, returns handling
- eBay charges: Seller pays international shipping fee to eBay
- Returns: eBay processes international returns (customer can return to UK hub)

**Cost Structure:**
- Shipping fee to eBay hub: Varies by weight/destination (€2-€8 typical UK)
- International shipping fee (charged to buyer): eBay calculates based on weight/destination
- eBay margin: Retains 10-15% of international shipping fees

**Advantages:**
- Seller only ships to UK hub (simple)
- eBay handles customs/duties
- Returns simplified (UK hub handles)
- Access to global buyers

**Disadvantages:**
- International customers pay high shipping (eBay margin reduces competitiveness)
- Longer delivery times (2-3 weeks typical to distant markets)
- Less control over customer experience (international carrier variability)
- Returns slower (international return times 20-30 days typical)

### 5.4.2 International Shipping Program (ISP)

**Newer Alternative to GSP:**
eBay introduced International Shipping Program (ISP) as alternative to GSP.

**ISP Mechanics:**
- Sellers manage own shipping to international customers
- Sellers use own carrier or eBay-negotiated carriers
- Rates: eBay negotiates carrier discounts (15-25% off retail)
- Returns: Sellers handle or use eBay managed returns

**ISP Advantages:**
- Lower shipping costs (seller retains negotiated discounts)
- Faster delivery (sellers choose carriers)
- Better customer experience (direct seller relationships)
- More margin control

---

## 5.5 Multi-Marketplace Fulfillment from Single Warehouse

### 5.5.1 Unified Fulfillment Strategy

**Challenge:**
Managing orders from multiple marketplaces (Amazon, eBay, Bol, Zalando) from single warehouse creates inventory/routing complexity.

**Solution Architecture:**

**Level 1: Inventory Planning**
- Total inventory = Amazon inventory + eBay inventory + Bol inventory
- Allocate per marketplace based on sales ratio
- Example: Amazon 60%, eBay 20%, Bol 20% of total stock

**Level 2: Warehouse Integration**
- Tool: Zentail, ChannelAI, or similar aggregator
- Syncs inventory with all marketplaces in real-time
- Shows available quantity across all channels
- Prevents overselling

**Level 3: Order Routing**
- Order received on eBay → Aggregator pulls order
- Routes to your warehouse (if sufficient inventory)
- Pick/pack/ship with eBay-approved carrier
- Return shipping labels: eBay-specific labels printed

**Level 4: Returns & Reconciliation**
- Returns received at warehouse
- Inspected and restocked (if condition acceptable)
- Return tracking updated in all marketplaces
- Refund processed per each marketplace's policy

**Example Operational Flow (200 orders/day, 60% Amazon / 20% eBay / 20% Bol):**

| Channel | Daily Orders | Inventory Allocation | Fulfillment |
|---------|-------------|---------------------|-------------|
| Amazon | 120 | 600 units (60%) | FBA or own warehouse |
| eBay | 40 | 200 units (20%) | Own warehouse (Hermes) |
| Bol | 40 | 200 units (20%) | Bol warehouse or own |
| **Total** | **200** | **1,000 units** | Varies by channel |

### 5.5.2 Multi-Marketplace Integration Platforms

**Top Platforms:**

**Zentail:**
- Integrates: 10+ fulfillment sources + 15+ marketplaces
- Cost: €100-€500/month
- Inventory sync: 15-30 min updates
- Order routing: Automatic to nearest warehouse
- Best for: Multi-3PL + multi-marketplace sellers

**ChannelAI:**
- Integrates: 15+ 3PLs + all major marketplaces
- Cost: €200-€600/month
- Reporting: Strong KPI dashboards
- Inventory forecasting: AI-powered
- Best for: Mid-market sellers (200-1000 orders/day)

**Veeqo:**
- Integrates: 20+ 3PLs + shipping carriers
- Cost: £200-£500/month
- Multi-channel: Amazon + eBay + own website focus
- Shipping cost comparison: Optimize carrier selection
- Best for: Multi-channel sellers with own website

---

# SECTION 6: FULFILLMENT KPIs & BENCHMARKS

## 6.1 Core Fulfillment KPIs

### 6.1.1 Order Accuracy (Fill Rate)

**Definition:**
Percentage of customer orders where all items shipped correctly, in correct quantities, undamaged.

**Calculation:**
(Correctly fulfilled orders / Total orders) × 100

**Example:**
- Orders this month: 1,000
- Correctly fulfilled: 994 (no errors, no damage)
- Order accuracy: 994/1000 = 99.4%

**Industry Benchmarks (2026):**
- Industry average: 99.0-99.2%
- Best-in-class (FBA, ShipBob): 99.5-99.9%
- Target for sellers: 99.5%+
- Minimum acceptable: 98%+

**Factors Affecting Accuracy:**
- Barcode scanning quality (prevents picking wrong item)
- Packing station organization (reduces item mixing)
- Quality control checks (sample verification)
- WMS accuracy (correct location data)
- Picker training (experience reduces errors)

**Cost of Errors:**
- Each order error: €5-€15 in replacement + shipping
- Customer dissatisfaction: 1 star review impact on future sales
- Return rate impact: Errors increase returns by 10-15%
- Account suspension risk: >1% error rate triggers account review

### 6.1.2 Ship-On-Time Rate (OTIF)

**Definition:**
Percentage of orders shipped within promised delivery window.

**Calculation:**
(Orders shipped by promised date / Total orders) × 100

**Example:**
- Orders this month: 1,000
- Promised delivery date average: 3 days from order
- Orders shipped within 3 days: 970
- OTIF: 970/1000 = 97%

**Industry Benchmarks (2026):**
- Industry standard: 95-97%
- Best-in-class (FBA, ShipBob): 98-99%+
- Target for sellers: 97%+
- Minimum for FBM: 95%+ (Amazon penalty if <95%)

**Impact on Search Rankings:**
- 99%+ OTIF: Boost in search visibility (+20-30% visibility)
- 95-98% OTIF: Standard visibility
- <95% OTIF: Penalty in search results (-15-25% visibility)
- Very late (7+ days after promise): Risk of A-to-Z claim

**Factors Affecting OTIF:**
- Fulfillment speed (pick/pack/ship time)
- Carrier reliability (shipping time accuracy)
- Inventory availability (stockouts delay shipping)
- Order volume fluctuations (peak seasons challenge)
- Returns processing (affects new inventory for resale)

### 6.1.3 Cost Per Order

**Definition:**
Total cost to pick, pack, and ship a single order.

**Components:**
- Fulfillment fee: €0.85-€3.80 (depends on size/weight)
- Packaging materials: €0.30-€1.00 per order
- Labor (picking/packing): €0.50-€2.00 per order
- Shipping: €3.50-€12.00 per order
- Returns processing (allocated): €0.20-€0.50 per order
- Storage (allocated): €0.05-€0.20 per order

**Calculation Example:**
- Fulfillment: €1.50
- Packaging: €0.50
- Labor: €1.00
- Shipping: €6.00
- Returns (allocated): €0.30
- Storage (allocated): €0.10
- **Total cost per order: €9.40**

**Cost Per Order by Fulfillment Model:**

| Model | Low | Mid | High | Best Practice |
|-------|-----|-----|------|----------------|
| FBA | €4.50 | €7.00 | €9.00 | €6-€8 range |
| 3PL | €7.50 | €10.00 | €13.00 | €8-€11 range |
| Own Warehouse | €5.00 | €8.00 | €12.00 | €7-€9 range |
| FBM | €6.00 | €10.00 | €16.00 | €8-€12 range |

**Margin Impact:**
- Product margin 40%: Can absorb €8-€10 CPOS
- Product margin 25%: Can absorb €4-€6 CPOS
- Product margin 15%: Can absorb €2-€3 CPOS (already struggling)

### 6.1.4 Pick Time & Pick Productivity

**Definition:**
Average time to pick all items for single order.

**Benchmarks:**
- Single-item order: 2-4 minutes (walk to location, scan, place in tote)
- Multi-item order (3 items): 6-12 minutes
- Multi-item order (5+ items): 12-20 minutes

**Picks Per Hour:**
- Single order pick: 120-175 picks/hour
- Batch pick (10 orders): 250-350 picks/hour
- Wave pick (parallel): 400-600 picks/hour
- Best-in-class: 300+ picks/hour

**Factors Improving Productivity:**
- Zone optimization (A-B-C SKU analysis): +15-20% improvement
- Barcode scanning vs. manual: +25-30% improvement
- Pick-to-light systems: +40-50% improvement
- Batch picking: +50-80% improvement over single-order
- Proper training: +20-30% improvement in new pickers

### 6.1.5 Pack Time

**Definition:**
Time from completed pick to sealed, labeled box ready for shipping.

**Benchmarks:**
- Simple order (1 item, small box): 2-3 minutes
- Standard order (2-3 items, medium box): 4-6 minutes
- Complex order (5+ items, multiple boxes): 10-15 minutes

**Average facility pack time:** 4-6 minutes per order

**Cost of pack time (labor):**
- At €15/hour wage: €1.00-€1.50 per order
- At €10/hour wage: €0.67-€1.00 per order

**Improving Pack Efficiency:**
- Pre-positioned packaging materials: -15% time
- Barcode label automation: -10% time
- Conveyor vs. manual transport: -20% time
- Quality control sampling vs. 100%: -25% time if reduced to 10% QC

### 6.1.6 Inventory Accuracy

**Definition:**
Actual physical inventory vs. system-recorded inventory accuracy.

**Calculation:**
(Total inventory units correct / Total units counted) × 100

**Benchmarks:**
- Best-in-class (FBA, ShipBob): 99.5%+
- Good (most 3PLs): 98-99%
- Acceptable: 97%+
- Problem: <96% (indicates WMS issues)

**Cost of Inaccuracy:**
- Each missing unit: €20-€100 in lost sales (opportunity cost)
- Overselling risk: €10-€50 per order (return/refund costs)
- Inventory write-off: €X value of missing items

**Example Loss from Poor Accuracy:**
- Inventory: 1,000 units
- Actual accuracy: 95% (50 units missing)
- Cost: 50 units × €40 avg product value = €2,000 in lost margin

### 6.1.7 Return Processing Time

**Definition:**
Time from return receipt at warehouse to item restocked or liquidated.

**Benchmarks:**
- Industry average: 10-15 business days
- Best-in-class: 5-7 business days
- Target: <10 business days

**Return Processing Steps:**
1. Receive return at warehouse (1 day)
2. Scan return barcode (0.5 days)
3. Inspect condition (1-2 days)
4. Determine disposition (restock, liquidate, scrap) (0.5 days)
5. Update inventory system (0.5 days)
6. Restock or prepare for liquidation (1-2 days)
- **Total: 5-7 days** best-in-class

**Return Rate Benchmarks:**
- Industry average: 5-8% of orders
- Electronics: 10-15% return rate
- Apparel: 20-35% return rate (much higher)
- FBA return average: 3-5% (lower due to quality control)

**Return Impact on Cash Flow:**
- 8% return rate = 1,000 orders/month = 80 returns
- Average processing: 10 days = €4,000-€8,000 in inventory value in limbo
- Restocking rate: 70% (30% not sellable) = €2,800-€5,600 returned to saleable inventory

---

## 6.2 Fulfillment KPI Benchmark Summary Table

| KPI | Target | Benchmark Range | Best-in-Class |
|-----|--------|-----------------|----------------|
| Order accuracy | 99.5%+ | 98-99.5% | 99.5-99.9% |
| Ship-on-time (OTIF) | 97%+ | 95-98% | 98-99%+ |
| Cost per order | Varies | €6-€12 | €6-€8 (FBA) |
| Pick time (avg) | <6 min | 4-8 min | 3-5 min |
| Pack time (avg) | 4-6 min | 4-8 min | 3-5 min |
| Inventory accuracy | 99%+ | 97-99% | 99.5%+ |
| Return processing | <10 days | 10-15 days | 5-7 days |
| Pick productivity | 200+/hr | 150-300/hr | 300-500/hr |
| Return rate | <8% | 5-15% | 3-8% |
| Order defect rate | <1% | 1-2% | <0.5% |

---

## CONCLUSION & KEY TAKEAWAYS

**Fulfillment Model Selection Framework:**
- FBA: Best for high-volume (500+ orders/month) Amazon sellers in EU
- FBA EFN: Best for mid-volume sellers (100-300/month) seeking VAT simplicity
- 3PL: Best for multi-channel sellers (Amazon + eBay + own) requiring control
- FBM: Best for made-to-order or low-volume sellers
- Hybrid: Optimal for scaling sellers balancing cost, control, and coverage

**European Tax Compliance Reality:**
- Pan-EU requires 8+ VAT registrations (compliance burden €3,600-€6,000/year)
- EFN reduces to 1 VAT + OSS (compliance burden €1,200-€2,000/year)
- 3PL strategies vary by provider and country of operation
- Professional tax support is NOT optional (penalty risk too high)

**Cost Hierarchy (Cost Per Order):**
1. FBA (lowest): €4.50-€8.00
2. 3PL: €7.50-€12.00
3. Own warehouse: €5.00-€12.00
4. FBM (highest): €6.00-€16.00

**Delivery Speed Advantage:**
- FBA Pan-EU: 2-3 days (all EU)
- FBA EFN: 3-6 days (periphery slower)
- 3PL multi-warehouse: 2-3 days (with proper network)
- Own warehouse single location: 3-7 days (geography dependent)

**Critical Success Metrics:**
- Order accuracy: 99.5%+ (non-negotiable)
- Ship-on-time: 97%+ (directly impacts search ranking)
- Cost control: Benchmark against model standard (FBA €6-8, 3PL €8-11)
- Inventory accuracy: 99%+ (prevents overselling)

---

## SOURCES & REFERENCES

Key sources for current 2026 data:

- [Amazon FBA Fees 2026: Complete Guide](https://amzprep.com/amazon-fba-fees/)
- [FBA Calculator 2026: Calculate Amazon Fees & Profit Margins](https://amzprep.com/amazon-fba-profit-margin-calculator/)
- [Update to European Referral and Fulfilment by Amazon fees for 2026](https://www.aboutamazon.eu/news/empowering-small-business/update-to-european-referral-and-fulfilment-by-amazon-fees-for-2026)
- [Rate Card Europe Fees Effective December 2025](https://m.media-amazon.com/images/G/02/sell/images/251221-FBA-Rate-Card-EN.pdf)
- [Amazon Pan-European FBA Program Guide](https://m.media-amazon.com/images/G/02/Pan_EU_FBA_handbook/Pan-EU_EN.pdf)
- [Amazon European Fulfillment Network (EFN) Guide](https://fulfillment-box.com/european-fulfillment-network-program-a-complete-guide/)
- [Best Fulfillment Centers in Europe 2026](https://rushorder.com/blog/best-fulfillment-centers-in-europe)
- [Huboo 3PL Europe Services](https://huboo.com/3pl-europe/)
- [ShipBob's Global 3PL Network](https://www.shipbob.com/3pl-network/)
- [Zalando Fulfillment Solutions (ZFS) Overview](https://partner.zalando.com/university/article/introduction-to-zalando-fulfillment-solutions)
- [Bol.com External Fulfillment Network](https://ecommercenews.eu/bol-builds-external-fulfillment-network/)
- [eBay Global Shipping Program Overview](https://www.pattern.com/blog/what-is-ebays-global-shipping-program)
- [Amazon FBA Packaging Requirements Guide](https://amzprep.com/amazon-fba-packaging-requirements-guidelines/)
- [Amazon FBA Inbound Requirements and Labeling](https://myfbaprep.com/blog/amazon/amazon-fba-prep-requirements-labeling-packaging-guide/)
- [Key Order Fulfillment KPIs Explained](https://www.netsuite.com/portal/resource/articles/erp/order-fulfillment-kpis-metrics.html)
- [Top Warehouse KPIs & Metrics to Track in 2026](https://www.hopstack.io/blog/warehouse-metrics-kpis)

---

**Document prepared for:** European Marketplace Distribution Professionals
**Level:** Expert / Reference Material
**Applicability:** Current as of April 2026

**Total Line Count:** 1,420+ lines

---

END OF DOCUMENT
