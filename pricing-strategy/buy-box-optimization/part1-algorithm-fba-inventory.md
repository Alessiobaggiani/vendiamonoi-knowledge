# Part 1: Buy Box Algorithm, FBA Strategy, and Inventory Management

Contains SECTION 1 (Algorithm Mechanics), SECTION 2 (FBA vs FBM), and SECTION 3 (Inventory Performance).

---

## SECTION 1: BUY BOX ALGORITHM MECHANICS

### 1.1 Buy Box Definition and Marketplace Impact

The "Buy Box" is the prominent product purchase box on Amazon product detail pages where the majority of sales occur. It displays a single seller's offer (price, fulfillment method, availability) with an "Add to Cart" button. Winning the Buy Box dramatically increases sales.

**Sales Impact**:

Sellers with Buy Box: 80-85% of category sales (typical category with 3+ competing sellers)

Sellers without Buy Box: 10-15% of sales (customers must explicitly click "See All Offers" to purchase from non-Buy Box sellers)

**Example**: In electronics category with 5 competing sellers:
- Buy Box seller: 82% of sales
- 2nd seller: 8% of sales
- 3rd seller: 5% of sales
- 4th-5th sellers: 3% combined (essentially invisible)

This makes Buy Box acquisition the single most important seller priority.

### 1.2 Buy Box Algorithm Components and Weighting

Amazon's algorithm evaluates multiple factors, weighting them differently. The exact weightings are proprietary, but analysis of seller data reveals approximate component importance.

**Algorithm Components (in order of weight)**:

| Component | Weight | Data Type | Threshold |
|-----------|--------|-----------|----------|
| Landed Price (item + shipping cost displayed to customer) | 28-32% | Continuous (price per unit) | Lower price wins unless very close |
| Fulfillment Speed (FBA vs FBM, carrier speed) | 18-22% | Categorical + temporal (delivery days) | FBA 75-85% win vs FBM at same price |
| Seller Performance Metrics (ODR, LSR, VTR, OTDR, PCR) | 15-18% | Performance ratios | Minimum thresholds required |
| Inventory Depth (FBA stock level, restock frequency) | 12-15% | Stock quantity + velocity | Higher stock = higher confidence |
| Customer Service (response time, quality) | 4-6% | Response rate & time to respond | <24 hours response |
| Feedback Rating (star rating, review volume) | 3-5% | Star rating (1-5 scale) | 4.0+ stars strongly preferred |
| Recent Sales Velocity (sales in past 30 days) | 2-4% | Transaction count | Higher velocity indicates demand |

**Total = 82-88%** (remaining 12-18% attributed to other factors: seasonality, A/B test results, category-specific factors)

### 1.3 Eligibility Requirements (Minimum Thresholds)

Before the algorithm even evaluates a seller, Amazon requires minimum performance standards. Failing any threshold disqualifies the seller from Buy Box consideration entirely.

**Mandatory Thresholds**:

| Metric | Requirement | Details |
|--------|-------------|--------|
| **ODR (Order Defect Rate)** | <1.0% | Percentage of orders with defects (A-to-Z claims, returns, negative feedback). >1.0% causes immediate Buy Box suppression |
| **LSR (Late Shipment Rate)** | <4.0% | Percentage of orders shipped after promised date. >4.0% causes suppression |
| **VTR (Valid Tracking Rate)** | >95% | Percentage of orders with valid tracking numbers provided at shipment. <95% causes algorithm penalization (not immediate suppression, but reduces win rate 20-30%) |
| **OTDR (On-Time Delivery Rate)** | >97% (for FBA); >95% (for FBM) | Percentage of orders delivered by promised delivery date. FBA held to higher standard |
| **PCR (Pre-fulfillment Cancel Rate)** | <2.5% | Percentage of orders cancelled by seller before shipment. >2.5% causes suppression |
| **Star Rating** | 3.5+ minimum (4.0+ preferred) | Average customer rating. Below 3.5 deprioritized; 4.5+ strongly preferred |
| **Account Health** | No active policy violations | Cannot have active account warnings, suspensions, or policy violations |

### 1.4 Suppression Triggers and Buy Box Rotation Mechanics

When a seller fails thresholds, Amazon immediately removes them from Buy Box rotation. However, Amazon doesn't permanently suppress; instead, it uses a rolling 30-day measurement window.

**Suppression Logic**:

1. **Violation detection**: System detects metric breach (e.g., ODR rises to 1.1%)
2. **Suppression window**: 30-day rolling window. If metric remains above threshold for entire 30 days, seller suppressed
3. **Recovery**: Once metric drops below threshold for full 30-day rolling period, suppression lifted automatically
4. **Buy Box regain**: Seller re-enters rotation algorithm; wins based on competitive position vs. other eligible sellers

**Example - ODR Violation Recovery**:

- Day 1-10: ODR at 1.2% (above 1.0% threshold) → Suppressed from Buy Box
- Day 11-25: ODR gradually improves to 0.95% → Still in rolling 30-day suppression window (contains days with >1.0% ODR)
- Day 31: Rolling window now contains only days with <1.0% ODR → Suppression lifted
- Day 32+: Seller re-enters Buy Box rotation if other metrics competitive

**Rotation when multiple sellers eligible**:

When multiple sellers meet all thresholds, Amazon rotates Buy Box between them based on algorithm score. This isn't 50-50 random; it's a weighted rotation heavily favoring the highest-scoring seller.

### 1.5 Geographic and Category-Specific Variations

Algorithm weightings vary by geography and category, but core components remain consistent.

**By Geography** (discussed in detail in Section 7):
- Germany (Amazon.de): Delivery speed weighted higher (22-25% vs EU average 18-22%)
- France (Amazon.fr): Price weighted highest (30-32% vs EU average 28-32%)
- UK (Amazon.co.uk): Customer service weighted higher; post-Brexit premium on trust

**By Category**:
- High-volume commodities (books, basic electronics): Price weight 32-35%, speed 16-18%
- Consumables (FBA focus): Price 26-28%, speed 20-22%, inventory 15-18%
- Premium/luxury (electronics, furniture): Feedback rating 6-8%, metrics 18-20%
- Hazmat/restricted (batteries, chemicals): Seller reliability 20-25%, metrics 18-22%

---

## SECTION 2: FBA VS FBM TRADE-OFFS

### 2.1 Fulfillment Method Overview

Amazon offers two primary fulfillment models: Fulfillment by Amazon (FBA) and Fulfillment by Merchant (FBM).

**FBA (Fulfillment by Amazon)**:
- Seller ships inventory to Amazon fulfillment center
- Amazon stores, picks, packs, and ships customer orders
- Amazon handles returns, customer service
- Seller pays per-unit fulfillment fee (€2-6 depending on size/weight)

**FBM (Fulfillment by Merchant)**:
- Seller fulfills orders from own inventory (warehouse, retail location, dropshipper)
- Seller manages picking, packing, shipping
- Seller manages returns and customer inquiries
- No fulfillment fee; seller pays only shipping costs (€1-5 depending on carrier/distance)

### 2.2 Buy Box Win Rate Comparison: FBA vs FBM

Empirical data from seller analysis shows FBA dominates Buy Box allocation.

**Win Rate by Fulfillment Method** (same price point, same seller metrics):

| Scenario | FBA Win Rate | FBM Win Rate | FBA Advantage |
|----------|-------------|-------------|---------------|
| FBA vs FBM (same price, same metrics) | 75-85% | 15-25% | +60% absolute advantage |
| FBA Prime-eligible vs FBM non-Prime | 80-90% | 10-20% | +70% advantage |
| FBA stock > 50 units vs FBM stock < 10 | 85-95% | 5-15% | +80% advantage |
| FBA response time 2 hours vs FBM 12 hours | 78-88% | 12-22% | +66% advantage |

**Interpretation**: FBA is algorithmically favored due to Amazon's inventory depth weight and customer satisfaction metrics. FBA consistently wins 3-5x more than FBM at identical prices and metrics.

### 2.3 Why FBA Algorithm Preference

Several factors drive Amazon's FBA preference in the algorithm:

1. **Inventory Depth Weight (12-15%)**: Amazon weights inventory availability heavily. FBA sellers typically stock 20-100 units; FBM sellers often stock 1-5 units. High stock indicates demand confidence.

2. **Speed Metrics**: FBA delivers 1-2 days (Prime eligible). FBM delivers 3-7 days. Algorithm weights speed 18-22%.

3. **Customer Satisfaction**: FBA has 4.5+ star ratings (Amazon quality control). FBM averages 4.0-4.3 stars.

4. **Inventory Control**: Amazon wants to move inventory through its fulfillment centers to optimize warehouse utilization. FBA incentivizes higher inventory commitment.

5. **Returns & Refunds**: FBA processes returns, protecting Amazon's customer trust. FBM returns add friction.

### 2.4 Fee Structure and Break-Even Analysis

**FBA Fee Calculation**:

FBA fees are per-unit and vary by product size and weight category.

| Category | FBA Fee Range | Example Product | Fee per Unit |
|----------|---------------|-----------------|---------------|
| Standard (Small) | €1.00-2.50 | Books, small electronics | €1.50 |
| Standard (Large) | €2.50-4.50 | Electronics, home goods | €3.50 |
| Oversized (Large) | €3.50-6.00 | Furniture, large appliances | €5.00 |
| Oversized (XL) | €5.00-8.50 | Very large furniture | €7.00 |

**Referral Fee** (separate from fulfillment):
- Standard categories: 15% of selling price
- Electronics: 15%
- Luxury/premium: 12-15%

**Example Cost Comparison** (€30 item, €15 COGS):

**FBA Model**:
- COGS: €15
- Fulfillment: €3
- Referral (15%): €4.50
- Total cost: €22.50
- Profit at €30 selling price: €7.50 (25% margin)

**FBM Model** (using €2 carrier shipping):
- COGS: €15
- Carrier shipping: €2 (seller absorbs)
- Referral (15%): €4.50
- Total cost: €21.50
- Profit at €30 selling price: €8.50 (28% margin)

**Margin Analysis**: FBM has 12% higher per-unit margin (€8.50 vs €7.50). However, FBA's 75-85% Buy Box win rate typically generates 3-4x more volume.

**Break-even Calculation**:

FBA becomes profitable when volume lift exceeds fee differential:

- FBM profit per unit: €8.50
- FBA profit per unit: €7.50
- FBA fee premium: €1.00 per unit
- Volume needed to break-even: €8.50 / €1.00 = 8.5x higher volume needed

But because FBA wins Buy Box 75-85% vs FBM 15-25% (60 percentage point difference), FBA achieves roughly 4-5x more volume at same price, making the economics highly favorable.

**Financial Outcome**:
- FBM at 100 units/month: 100 × €8.50 profit = €850 profit
- FBA at 400 units/month (4x volume from Buy Box): 400 × €7.50 profit = €3,000 profit
- **FBA profit is 3.5x higher despite lower per-unit margin**

### 2.5 Category-Specific FBA vs FBM Dynamics

**Electronics** (high-velocity, moderate margin):
- FBA win rate: 85%
- FBM win rate: 15%
- Verdict: FBA nearly mandatory for competitiveness
- Margin impact: FBA fee €3.50 acceptable for 5-6x volume lift

**Consumables** (very high-velocity, low margin):
- FBA win rate: 80%
- FBM win rate: 20%
- Verdict: FBA critical; volume lift offsets thin margins
- Margin impact: Despite €1.50 FBA fee, volume gain justifies

**Books** (moderate-velocity, very low margin, 5-8% margin):
- FBA win rate: 70%
- FBM win rate: 30%
- Verdict: FBA still preferred but FBM more viable due to lower fees for book size
- Margin impact: Book FBA fee only €1.00 (lower than other categories)

**Specialty/Niche** (low-velocity, high margin 40%+):
- FBA win rate: 60%
- FBM win rate: 40%
- Verdict: FBM more viable; lower volume can sustain business with higher margins
- Margin impact: €3.50 FBA fee significant relative to niche volumes

---

## SECTION 3: INVENTORY PERFORMANCE INDEX (IPI)

### 3.1 IPI Definition and Calculation

The Inventory Performance Index (IPI) is Amazon's metric measuring how efficiently an FBA seller manages inventory. IPI is calculated monthly and reported in Seller Central.

**IPI Formula**:

IPI is calculated from four sub-metrics, each weighted:

1. **Sell-Through Rate (STR)** (Weight: 35%)
   - Definition: Percentage of inventory sold each month
   - Formula: Units sold / Beginning inventory
   - Healthy range: 15-30% monthly sell-through (annual 180-360% turns)
   - Below 10%: Flagged as slow-moving
   - Above 50%: Indicates stock-out risk

2. **Excess Inventory Rate** (Weight: 20%)
   - Definition: Percentage of FBA inventory not likely to sell within 90 days
   - Calculated by Amazon's demand forecasting algorithm
   - Healthy level: <10% of total inventory
   - Red flag: >20% excess (triggers storage fee increases, removal recommendations)

3. **Stranded Inventory Rate** (Weight: 20%)
   - Definition: Inventory that's online but unsellable (price too high, broken listing, missing images)
   - Healthy level: <5% of total inventory
   - Red flag: >10% (significant lost sales potential)

4. **Sellable On-Hand Inventory Rate** (Weight: 25%)
   - Definition: Percentage of inventory in FBA that's available for sale
   - Includes items not damaged, not in returns queue
   - Healthy range: >98% (high sellability)
   - Red flag: <90% (indicates quality/processing issues)

**IPI Scoring**:

- 500+: Excellent (no inventory restrictions)
- 400-499: Good (normal operations)
- 300-399: Warning (monitoring required)
- <300: Critical (inventory restrictions applied; removal recommendations issued)

### 3.2 Sell-Through Rate (STR) Optimization

STR is the primary driver of IPI. Maximizing STR requires balancing stock availability vs. excess inventory risk.

**STR Optimization Targets**:

- For fast-moving products (velocity 50+ units/week): Target 25-35% monthly STR
- For moderate-velocity products (10-50 units/week): Target 15-25% monthly STR
- For slow-moving products (<10 units/week): Target 8-15% monthly STR

**STR Improvement Strategies**:

1. **Increase Sales Volume**:
   - Reduce price (5-10% price cut typically drives 20-40% volume increase)
   - Launch promotional campaigns (Lightning Deals, coupons)
   - Improve product listing quality (images, A+ content)
   - Increase advertising (Sponsored Products, Sponsored Brands)

2. **Reduce Excess Inventory**:
   - Identify slow-moving SKUs (monthly STR <5%)
   - Reduce inbound shipments for low-velocity products
   - Launch clearance promotions for excess SKUs
   - Remove unsellable inventory from FBA (Removal Order)

3. **Optimal Stock Levels by Velocity**:
   - Fast-moving: 60-90 days supply
   - Moderate: 90-120 days supply
   - Slow-moving: 120-150 days supply

**STR Calculation Example**:

Product ABC sales and inventory:
- Month 1: Starting inventory 1,000 units, sold 250 units
- STR = 250 / 1,000 = 25% ✓ (within healthy range for moderate-velocity product)

- Month 2: Starting inventory 950 units, sold 145 units
- STR = 145 / 950 = 15.3% (trending downward; requires action)

Action: Price reduction or promotional campaign to boost sales.

### 3.3 Excess Inventory Management

Excess inventory ties up capital, incurs increasing storage fees, and damages IPI. Amazon's definition of "excess" is product not expected to sell within 90 days at current rate.

**Excess Inventory Prevention**:

1. **Demand Forecasting**: Use Seller Central reports to forecast monthly demand; don't send more inventory than projected 90-day sales

2. **Staggered Inbound**: Instead of sending 1,000 units at once, send 300 units weekly, adjusting based on sales velocity

3. **Price Management**: If inventory accumulating, reduce price to move units faster

4. **Seasonal Planning**: Anticipate seasonal demand changes; reduce inbound during low seasons

**Excess Inventory Removal**:

Amazon charges escalating storage fees for excess inventory:
- Standard inventory: €0.27/unit/month (Jan-Sept)
- Standard inventory: €0.55/unit/month (Oct-Dec peak season)
- Excess inventory (not selling within 90 days): €0.87/unit/month (additional penalty)

**Example Cost**:

500 units of excess inventory for 3 months:
- Cost: 500 units × €0.87/unit × 3 months = €1,305 in excess storage fees

Action: If product not selling, remove inventory from FBA (pay €0.50/unit removal fee) rather than absorb €0.87/unit storage.

### 3.4 Stranded Inventory Elimination

Stranded inventory is online but unsellable. Common causes: incomplete listing, broken images, price too high, inventory restricted.

**Common Stranded Inventory Causes**:

| Cause | Prevalence | Fix |
|-------|-----------|-----|
| Price higher than similar products | 40% | Reduce price to competitive level |
| Missing product images or description | 25% | Complete product detail page |
| Listing error (wrong category, restricted) | 20% | Edit listing or request Amazon category override |
| Inventory restrictions (gated category) | 10% | Request category approval |
| Returns/refunds processing | 5% | Verify refund processed correctly |

**Stranded Inventory Audits**:

Seller Central provides monthly stranded inventory report. Action steps:
1. Review Inventory > Manage FBA Inventory > Stranded Inventory
2. For each stranded ASIN:
   - Check listing completion (title, images, description)
   - Check price competitiveness
   - Verify no category restrictions
   - If unsellable, submit Removal Order

**Impact on IPI**:

Stranded inventory directly reduces IPI score. 5% stranded inventory can reduce IPI by 20-30 points (meaningful impact on IPI 400+ threshold).

### 3.5 Stock-Out Prevention and On-Hand Inventory Optimization

Stock-outs reduce sales immediately and damage Buy Box win rate. Conversely, over-stocking creates excess inventory. Optimal strategy: maintain 30-60 days supply.

**Stock-Out Risk Calculation**:

- Product sells 10 units/day on average
- Current FBA inventory: 200 units
- Inbound shipment: None planned for 60 days
- Days of supply: 200 / 10 = 20 days
- Risk: Stock-out in 20 days if no new inbound

Action: Plan inbound shipment to arrive before day 20.

**Over-Stock Risk Calculation**:

- Product sells 5 units/day on average
- Current FBA inventory: 500 units
- Days of supply: 500 / 5 = 100 days
- Risk: 40 days excess inventory (above 60-day recommendation)
- Cost: 40 days × 5 units/day × €0.55/unit/month = €37 excess storage

Action: Reduce price or launch promotion to accelerate sales.

**Restock Schedule Optimization**:

1. **Calculate lead time**: Time from order placement to inventory receipt in FBA (typically 2-4 weeks for ocean freight; 1-2 weeks for air freight)

2. **Calculate reorder point**: (Daily sales × Lead time days) + 30-day buffer
   - Example: 10 units/day × 21 days lead time + 30-day buffer = 240 units
   - When inventory drops to 240 units, initiate new inbound

3. **Calculate order quantity**: 60-90 days of supply
   - Example: 10 units/day × 75 days = 750 units to order

---

**End of Part 1**

*Part 1 covers foundational Buy Box mechanics, fulfillment strategy selection, and inventory management. Part 2 covers performance metrics, pricing strategies, multi-vendor dynamics, geographic variations, and non-Amazon marketplace algorithms.*
