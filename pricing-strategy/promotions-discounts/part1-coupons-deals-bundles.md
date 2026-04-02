# Part 1: Coupon Psychology, Lightning Deals & Bundles

**Sections 1-3: Tactical implementation of promotional mechanics across European marketplaces**

## Coupon Psychology & Mechanics

### The Rule of 100
The psychological impact of discounts shifts at €100 price point:

**Below €100**: Percentage discounts feel larger (€50 product with 20% off = "€10 savings" feels significant)
- **€10 product, 20% off**: "€2 savings" feels meaningful (20% is prominent)
- **€50 product, 20% off**: "€10 savings" feels substantial
- **€100 product, 20% off**: "€20 savings" still feels large

**Above €100**: Absolute euro discounts feel larger (€500 product with 10% off = "€50 savings" feels bigger)
- **€200 product, 10% off**: "€20 savings" feels significant (absolute amount visible)
- **€500 product, 10% off**: "€50 savings" feels substantial
- **€1000 product, 10% off**: "€100 savings" feels larger than percentage

**Implementation Across Price Points:**

| Price Range | Optimal Display | Example | Psychology |
|------------|-----------------|---------|------------|
| €5-€25 | % Discount | 25% OFF (€5-€6 savings) | Percentage amplifies perceived value |
| €25-€50 | Dual (% + €) | 20% OFF / €10 SAVINGS | Both impactful; customer chooses reference |
| €50-€100 | % Primary, € Secondary | 15% OFF (€8-€10 savings) | Percentage still primary above €100 boundary |
| €100-€250 | € Primary, % Secondary | SAVE €25 (10% OFF) | Absolute euro becomes primary anchor |
| €250-€500 | € Primary | SAVE €50-€100 | Pure absolute euro; percentage can feel small |
| €500+ | € Primary + Installment | SAVE €150 (0 interest financing) | Combine savings with financing for appeal |

### Coupon Types & Mechanics

#### Clip Coupons (Amazon, eBay, Bol.com, Kaufland, Cdiscount, Allegro)

**How It Works:**
- Customer visits product listing and clicks "Clip this coupon"
- Coupon automatically applied to cart at checkout
- Works across entire shopping session (not single-purchase)
- Visible on customer's coupon list for future purchases

**Psychological Triggers:**
- **Ownership effect**: Once "clipped," customer feels coupon is theirs (sunk psychology)
- **Limited availability perception**: "Clip coupon" language implies scarcity (even if unlimited)
- **Commitment escalation**: Clipping increases likelihood of purchase vs. passive discount viewing
- **Social proof**: Clip count visible on some platforms ("2,347 customers clipped")

**Implementation Strategy:**
- Clip coupons work best for products with high transaction frequency or subscription intent
- Position on high-ASP items (>€30) to justify per-coupon fee structure
- Pair with automated email campaign: "Your clipped coupon expires in 7 days"
- Use for products with seasonal peaks (e.g., sunscreen, heaters)

#### Promo Codes (Email, Newsletter, Social Media)

**How It Works:**
- Retailer generates unique alphanumeric code (e.g., SPRING20, NEWSLETTER15)
- Customer enters code at checkout
- Code must be manually typed or clicked from email/social link
- No prior registration required

**Psychological Triggers:**
- **Exclusivity**: "Code for email subscribers only" creates VIP perception
- **Scarcity**: "Code valid April 1-7 only" drives urgency
- **Reciprocity**: "We're giving you €10 off" after customer shared email feels like reward
- **Effort justification**: Typing code makes customer feel they "earned" discount

**Implementation Strategy:**
- Use in email sequences: Day 1 (welcome), Day 3 (browse abandonment), Day 7 (final push)
- Segment codes by customer type: New Customer (WELCOME15), Loyal (VIP20), Win-back (COMEBACK10)
- Create social-exclusive codes: #BlackFridayIG20 (Instagram only)
- Limit redemptions per code (e.g., 500 uses max) to create scarcity
- Track code performance by channel: Email newsletters vs. social media posts

#### Social Codes (TikTok, Instagram, Facebook, YouTube)

**How It Works:**
- Brand creates influencer-specific codes (e.g., @influencer_name20)
- Posted in influencer content (bio link, pinned comment, story)
- Customer sees code, types at checkout
- Tracking shows sales attribution to specific influencer

**Psychological Triggers:**
- **Parasocial relationship**: Code associated with trusted influencer feels like personal recommendation
- **FOMO**: Limited-time creator content (stories, live streams) drives urgency
- **Community identity**: Using creator's code signals alignment with community
- **Discovery**: Code in comments reveals product to broader audience

**Implementation Strategy:**
- Micro-influencers (50K-500K followers) deliver 8-15% conversion uplift (better ROI than macro)
- Rotating codes maintain attribution clarity (different code per influencer)
- Live stream exclusive codes (only valid during 30-min stream) drive engagement
- Post-purchase hashtag tracking: #SoldByInfluencer captures word-of-mouth

#### Subscribe & Save Codes (Amazon, Bol.com)

**How It Works:**
- Customer enrolls product in automatic recurring delivery (monthly, quarterly, semi-annual)
- Code applies ongoing discount (typically 5-20%)
- Subscription continues until customer manually cancels
- First order discount often higher (15-25%) than recurring discount (5-10%)

**Psychological Triggers:**
- **Default effect**: Subscription preset as default accelerates adoption
- **Switching cost**: Canceling subscription creates friction (prevents easy churn)
- **Habit formation**: Recurring delivery becomes "automatic" purchase
- **Lock-in perception**: "Subscription savings" feel like exclusive membership benefit

**Implementation Strategy:**
- Subscribe & Save fee structure is most favorable (€0.15 + 2.5% vs €2.50 base fee)
- Ideal for consumables: coffee, vitamins, pet food, beauty supplies
- Position as convenience play, not price reduction (cost-per-unit is lower)
- Offer "subscription pause" (temporary hold) vs. cancellation to reduce churn

### Coupon Fee Structure by Marketplace (Updated June 2, 2025)

#### Amazon EU Coupon Fees (New Performance-Based Model)

**Previous Model (Pre-June 2):** Per-unit fees (€0.10-€0.20 per redemption)

**New Model (June 2 onwards):**
- **Clip Coupon**: €2.50 fixed + 2.5% of discount amount
  - €2.50 discount: Total fee = €2.50 + €0.06 = €2.56
  - €5.00 discount: Total fee = €2.50 + €0.12 = €2.62
  - €10.00 discount: Total fee = €2.50 + €0.37 = €2.87
  - **Break-even**: €2.50 / 2.5% = €100 discount pool needed to match old per-unit model

- **Promo Code**: €2.50 fixed + 2.5% of discount amount
  - Identical fee structure to clip coupons
  - Higher volume typically seen vs. clip (newsletter reach vs. organic discoverability)

- **Subscribe & Save**: €0.15 fixed + 2.5% of discount amount
  - €2.00 discount: Total fee = €0.15 + €0.05 = €0.20
  - €5.00 discount: Total fee = €0.15 + €0.12 = €0.27
  - **Savings vs. clip**: €2.35 lower fixed fee per redemption

**Fee Impact on Margins:**

| Coupon Type | Discount | Qty | Total Discount € | Fee | Fee % | Margin (pre-fee) | Margin (post-fee) |
|------------|----------|-----|-------------------|-----|--------|------------------|------------------|
| Clip 5% | €50 product, 5% off | 100 | €250 | €256 (€2.50+€6.25) | 102% | €1500 (30%) | €1244 (25%) |
| Clip 10% | €50 product, 10% off | 100 | €500 | €262 (€2.50+€12.5) | 52% | €1500 (30%) | €1238 (25%) |
| Subscribe 5% | €50 product, 5% off | 100 | €250 | €20 (€0.15+€6.25) | 8% | €1500 (30%) | €1480 (30%) |

**Amazon EU Coupon Strategy Implications:**
- **New fixed fee heavily penalizes small discounts** (€2.50 on €2.50 discount = 100% fee!)
- **Subscribe & Save now clearly superior** for low-discount recurring products
- **Minimum viable coupon discount increased** to €5-€7.50 to justify per-coupon fees
- **Large bulk coupons now viable** (€10-€15 discounts absorb fixed fee better)

#### eBay Promotions Fees

| Promotion Type | Fee Structure | Example (€100 product, 10% discount) |
|--------------|-----------------|---------------------------------------|
| Markdown Promotion | 0% (free) | €10 discount cost = €0 Amazon fee |
| Coupon (Seller Created) | 0% (free) | €10 discount cost = €0 Amazon fee |
| Bulk Discount (Multi-buy) | 0% (free) | "Buy 2, €5 off" = €0 Amazon fee |
| Store Subscription Discount | 0% (free) | €10 recurring discount = €0 Amazon fee |

**eBay Advantage**: No platform fees on promotions. Cost is pure margin reduction.

#### Bol.com Promotion Fees

| Promotion Type | Fee Structure | Fee Calculation |
|--------------|-----------------|-----------------|
| Coupon (Customer Applicable) | 0% | €10 discount cost = €0 fee |
| Flash Deal (24-hour limit) | 0-2% | €10 discount, €0-€0.20 fee |
| Bundle Discount | 0% | €15 bundle discount = €0 fee |
| Subscribe & Save Discount | 0% | €5 recurring discount = €0 fee |

**Bol.com Advantage**: Free coupon coupons. Flash deals carry minimal platform fee.

#### Kaufland (Germany, Czech, Slovakia, Romania) Promotion Fees

| Promotion Type | Fee Structure |
|--------------|-----------------|
| Promotional Coupon | Free (Kaufland absorbs cost to drive traffic) |
| Limited-Time Deal | Free (Kaufland absorbs cost) |
| Multi-Buy Discount | Free |

**Kaufland Model**: Retailer sets discount; Kaufland promotes heavily. Margin reduction only cost.

#### Cdiscount (France) Promotion Fees

| Promotion Type | Fee Structure |
|--------------|-----------------|
| Coupon | Free |
| Flash Sale | Free (Cdiscount promotes) |
| Bundle Discount | Free |

**Cdiscount Model**: All promotional costs born by retailer (no platform fee).

#### Allegro (Poland, Central EU) Promotion Fees

| Promotion Type | Fee Structure | Notes |
|--------------|-----------------|-------|
| Allegro Coupon | Free (seller creates) | Coupon visible to all; automatic application |
| Limited-Time Promotion | Free | Seller controls timing |
| Multi-Item Promotion | Free | E.g., "Buy 2, get 20% off 2nd" |

**Allegro Advantage**: Zero promotion fees across all coupon types.

### Coupon Stacking Rules by Marketplace

**Amazon EU**: No stacking allowed
- One coupon per transaction (either clip coupon OR promo code, not both)
- Subscribe & Save discount + coupon = stacking NOT permitted
- Clip coupon + bulk discount (quantity-based) = stacking NOT permitted
- Impact: Customers must choose highest-value coupon

**eBay**: Limited stacking
- Markdown + seller coupon = stacking allowed in some categories
- Store subscription discount + promotion = stacking NOT allowed
- Impact: Design promotions to not overlap

**Bol.com**: Coupon stacking allowed (with conditions)
- Flash Deal + Coupon = can stack if both active and separate mechanics
- Subscribe & Save + Coupon = allowed (discount + coupon combine)
- Impact: Stacking encourages higher participation

**Kaufland**: No stacking
- One promotion per transaction
- Design campaigns with single highest-value offer

**Cdiscount**: Stacking not permitted
- Coupon + sale pricing = customer gets better offer, not both
- Impact: Customers receive highest discount (not cumulative)

**Allegro**: Limited stacking
- Coupon + multi-item promotion = some regional variation
- Best practice: design for single promotion per category

### Coupon Performance Benchmarks (European Marketplaces)

| Coupon Type | Redemption Rate | ASP Lift | Revenue Lift | Margin Impact | Use Case |
|------------|-----------------|----------|-------------|---------------|----------|
| Clip Coupon | 8-12% | +3-5% | +25-40% | -4% to -7% | Mature products, repeat buyers |
| Promo Code (Email) | 3-5% | +2-3% | +15-25% | -3% to -5% | Customer acquisition, retention |
| Social Code | 2-4% | +1-2% | +10-20% | -2% to -4% | Brand awareness, influencer |
| Subscribe & Save | 4-6% (initial) | +5-8% (recurring) | +35-50% (LTV) | -2% to -3% | Consumables, repeat purchase |
| Flash Deal (24h) | 15-25% | +10-20% | +150-300% | -8% to -15% | Inventory clearance, visibility |
| Bundle Coupon | 5-8% | +4-6% | +40-80% | -6% to -10% | Slow-moving SKUs, basket size |

---

## Lightning Deals & Flash Sales

### Amazon EU Best Deal Mechanics

**Eligibility Requirements:**
- Professional seller account (not FBA required but recommended)
- Product in stock (minimum 5 units required for some categories)
- Product must be ASP €5+ (lower ASP typically rejected)
- No hazardous materials restrictions
- Positive seller feedback (typically >95% rating)
- Not a Kindle book, restricted category, or gated product

**Deal Setup Process:**
1. Submit deal 7-14 days in advance (exact timeline varies by category)
2. Amazon reviews for feasibility (demand estimate, margin adequacy)
3. Deal scheduled into available 1-4 hour time slots
4. Amazon sets maximum quantity (typically 50-200 units depending on ASP)
5. Deal goes live; limited quantity sells out (or time expires)
6. Detailed performance report available post-deal

**Discount Depth & Duration:**
- **Discount**: 15-50% typical (Amazon suggests 20-35% for maximum appeal)
- **Duration**: 1, 2, 3, or 4-hour slots (4-hour slots get prime positioning)
- **Timing**: Peak hours (19:00-21:00 EU time) generate 3-5x volume vs. daytime

**Slot Availability by Season:**
| Period | Availability | Competition | Likelihood |
|--------|-------------|------------|-----------|
| Jan-Feb | Moderate | High (post-holiday) | 60-75% |
| March-May | High | Low | 85-95% |
| June | Moderate | Moderate | 70-80% |
| July-Aug | Moderate | Moderate | 70-80% |
| Sept-Oct | Moderate | High (holiday prep) | 65-75% |
| Nov (Black Friday prep) | Low | Very High | 25-40% |
| Dec (Christmas) | Low | Very High | 20-35% |

**Fee Structure:**
- **No platform fee** (Amazon absorbs cost to drive traffic)
- Cost is pure margin reduction (discount depth)
- Each unit sold absorbs the per-unit referral fee as usual

**Revenue & Visibility Impact:**
| Metric | Improvement |
|--------|------------|
| Units Sold (deal day) | +200-400% vs. baseline |
| Revenue (deal day) | +100-250% (due to price reduction + volume) |
| Search Visibility (post-deal) | +50-100% (30-60 day halo) |
| Reviews/Rating Reviews | +15-30 (visibility drives review generation) |
| Detail Page Traffic | +300-500% (deal day) |

### eBay Daily Deals

**Mechanics:**
- Seller nominates products for daily deal (up to 5 per week)
- eBay features selected deals on "Today's Deals" homepage
- 24-hour promotion window (expires at 23:59 local time)
- Quantity limit enforced (seller specifies maximum)
- Buyers see countdown timer and deal badge

**Requirements:**
- Product ASP €3+ minimum
- No reserve price (auction-style bids not eligible)
- Free or flat-rate shipping required
- 14-day money-back guarantee
- Seller feedback >94.7% (last 12 months)

**Performance:**
- **Visibility**: Featured on eBay homepage (potential reach 50M+ users)
- **Volume Lift**: +100-250% typical vs. standard listings
- **Margin**: -5-15% depending on discount depth

### Bol.com WinActie (Win Deal)

**Mechanics:**
- Limited inventory promotion (seller specifies quantity)
- Time-limited (24 hours typical, up to 72 hours)
- Significant badge/promotion on product tile
- Email notification sent to interested customers
- Real-time inventory counter ("Only 3 left at this price!")

**Requirements:**
- Product must be in Bol.com fulfilled warehouse (FBA-equivalent)
- Discount 15%+ required for approval
- Minimum €20 ASP typical (varies by category)
- No prior price reduction allowed in preceding 7 days

**Performance:**
- **Visibility**: Homepage feature + category pages + email blast
- **Volume Lift**: +150-300%
- **Target Margin**: Discount 20-35% depth

### Kaufland Flash Deals (Germany, Czech, Slovakia, Romania)

**Mechanics:**
- "Blitzangebote" (flash offers) run 24 or 48 hours
- Limited quantity (retailer specifies)
- Promotional badge and countdown timer
- Email notification optional (customer opt-in)

**Requirements:**
- Seller must have active account (6+ months history)
- Product €1+ minimum
- Discount 10%+ minimum
- Quantity limit 10+ units

**Timing & Seasons:**
- Available year-round
- Peak availability: March-May, September-October
- Limited slots during November-December

### Cdiscount Flash Sales (France)

**Mechanics:**
- "Ventes Flash" are 48-hour limited-quantity promotions
- Featured prominently on Cdiscount category pages
- Real-time countdown and inventory meter ("2,000 available; 1,847 sold")
- Email blast to subscribers (Cdiscount controls send)

**Requirements:**
- Seller account >6 months active
- Product €2+ minimum
- Discount 15%+ minimum
- Quantity 50+ units minimum

**Fee Impact:**
- **No Cdiscount fee** for flash sale setup
- Cost is pure margin reduction

### Allegro OkazjeAukcji (Deal Auctions - Poland & Central EU)

**Mechanics:**
- Limited-time deals (24 or 48 hours)
- Real-time counter: "1,000 available; 847 reserved"
- Special badge and placement on Allegro homepage
- Countdown timer visible on listing

**Requirements:**
- Seller account with positive feedback
- Product €1+ minimum
- Discount 15%+ minimum
- Quantity 50+ units minimum for homepage promotion

---

## Bundle Discounts & Multi-Buy

### Pure Bundling Strategy (Fixed Bundle Only)

**Definition**: Customer MUST purchase all items together; no individual purchase option available.

**Example**: "Office Starter Bundle €49.99 (Desk + Chair + Lamp individually €75)"

**Use Cases:**
- Clearing slow-moving SKUs together
- Creating unique SKU combinations to reduce marketplace competition
- Inventory clearance (pair fast-moving item with slow-moving item)

**Margin Structure:**

| Item | Price | Margin % | Bundle Price | Bundle % |
|------|-------|----------|--------------|----------|
| Item A | €20 | 35% | — | — |
| Item B | €30 | 25% | — | — |
| Item C | €25 | 30% | — | — |
| **Total** | **€75** | **30% avg** | **€49** | **28% avg** |

**Cannibalization Risk:**
- Pure bundles prevent individual purchases (eliminates choice)
- Best for inventory clearance only (time-limited bundles, not permanent)
- Customer acquisition cost may be higher (smaller addressable market)

**Effectiveness:**
- Redemption rate: 5-8% (lower than individual products)
- Velocity: +40-80% (compared to unsold slow-movers)
- AOV impact: High (+€15-30 vs. single-item purchase)

### Mixed Bundling Strategy (Choose-Your-Bundle)

**Definition**: Core item required + customer selects complementary items.

**Example**: 
- "Purchase €50+ = Choose FREE gift (Adapter OR Cable OR Case)"
- "Buy Coffee Maker + Choose 2 bags of beans at 50% off"

**Use Cases:**
- Clearing different slow-moving SKUs (customer picks which combo)
- Upselling complementary products to increase AOV
- Personalization without custom manufacturing

**Margin Structure:**

| Core Item | Price | Margin % | Upsell (3 options) | Margin | Bundle Total |
|-----------|-------|----------|-------------------|--------|--------------|
| Coffee Maker | €80 | 40% | +€0 (free item) | Negative (-€15) | €80 net margin |
| | | | Adapter €15 | 60% | €95 (€36 margin) |
| | | | Cable €12 | 70% | €92 (€32 margin) |
| | | | Case €20 | 50% | €100 (€40 margin) |

**Effectiveness:**
- Redemption rate: 8-15% (higher than pure bundling)
- Velocity: +50-100%
- AOV impact: +€10-25 (depends on upsell acceptance)
- Margin: -5% to +10% (depends on free vs. discounted upsells)

### Volume/Tiered Bundling

**Definition**: Increasing discount as customer purchases more units.

**Example**:
- "Buy 1 @ €10"
- "Buy 2 @ €9 each (€18 total, 10% savings)"
- "Buy 3+ @ €8 each (€24 total, 20% savings)"

**Mechanics:**

| Quantity | Unit Price | Total | Discount | Margin (€3 margin/unit) |
|----------|-----------|-------|----------|-------|
| 1 | €10 | €10 | 0% | €3 (30%) |
| 2 | €9 | €18 | 10% | €5 (28%) |
| 3 | €8 | €24 | 20% | €6 (25%) |
| 5 | €7.50 | €37.50 | 25% | €8.75 (23%) |

**Use Cases:**
- B2B or bulk purchases
- Consumables (increase customer replenishment quantity)
- Slow-moving inventory (incentivize larger purchase)

**Effectiveness:**
- Average transaction value increase: +35-60%
- Velocity: +45-75%
- Margin erosion: -3% to -7% (but offset by increased volume)

### Loss Leader Bundle (Strategic Sacrifice)

**Definition**: One item heavily discounted to drive traffic; other items at full/higher margin.

**Example**: "Coffee Maker €49.99 (€20 loss) + Beans €12.99 (€7 margin) + Grinder €79.99 (€20 margin) = Bundle €129.99 Total (€7 net margin)"

**Mechanics**:

| Item | Normal Price | Bundle Price | Margin (Normal) | Margin (Bundle) | Contribution |
|------|-------------|--------------|-----------------|-----------------|-------------|
| Coffee Maker | €80 | €49.99 | €32 (40%) | €2 (4%) | -€30 |
| Beans (12-pack) | €12.99 | €12.99 | €6 (46%) | €6 (46%) | €0 |
| Grinder | €80 | €79.99 | €32 (40%) | €30 (37%) | -€2 |
| **Bundle Total** | **€173** | **€143** | **€70 (40%)** | **€38 (27%)** | **-€32** |

**Psychology**:
- Loss leader drives traffic (€49.99 coffee maker is attractive)
- Bundle structure prevents customer from buying loss leader alone
- Cross-category purchase increases AOV dramatically
- Customer perception: "Great bundle deal, €30+ savings"
- Reality: €30+ net loss to drive consideration

**Use Cases:**
- New marketplace entry (establish seller reputation)
- Holiday season inventory clearance
- Compete against marketplace price wars
- Prime Day/Black Friday (traffic driver)

---

## Bundle Profitability Analysis

### Cannibalization Calculation

**Scenario**: Previously customer bought Coffee Maker alone (€80). Now offered Coffee Maker + Grinder bundle (€120 vs. €160 individual).

**Question**: Did bundle increase revenue or just shift purchase?

**Calculation**:

If 100 customers normally buy:
- 60 buy Coffee Maker only (€80 × 60 = €4,800)
- 30 buy both separately over time (€80 + €80 = €4,800)
- 10 buy neither (€0)
- **Total normal revenue**: €9,600

With bundle offer:
- 50 buy bundle (€120 × 50 = €6,000)
- 25 buy Coffee Maker only (€80 × 25 = €2,000)
- 15 buy Grinder only (€80 × 15 = €1,200)
- 10 buy neither (€0)
- **Total bundle revenue**: €9,200
- **Cannibalization**: €400 (€9,600 - €9,200) = 4% revenue loss

**Margin Impact**:
- Normal revenue margin: €9,600 × 35% = €3,360
- Bundle revenue margin: €9,200 × 30% = €2,760
- **Net margin loss**: €600 (€3,360 - €2,760) = 18% margin erosion

**Breakeven**: Bundle needs to drive 15-20% incremental unit volume to offset margin reduction.

### Cross-Category Bundle ROI

**Setup**: 
- Product A: €50, 40% margin (€20 profit)
- Product B: €30, 25% margin (€7.50 profit)
- Bundle: €70 (20% discount), 30% margin (€21 profit)

**Baseline (No Bundle)**:
- 1,000 A sold: €20,000 profit
- 600 B sold (lower demand): €4,500 profit
- **Total**: €24,500 profit

**With Bundle** (assuming 15% incremental adoption):
- 200 bundles sold: €4,200 profit (vs. €27,500 if sold individually)
- 1,100 A-only sold: €22,000 profit (bundled customers + 10% new)
- 700 B-only sold: €5,250 profit (bundled customers + 10% new)
- **Total**: €31,450 profit
- **Gain**: €6,950 (28% profit increase)

**Breakeven Volume**: Bundle needs 10-12% incremental adoption to exceed no-bundle scenario (accounting for margin erosion and cannibalization).

### Break-Even Discount Depth

**Formula**: 
```
Break-even discount % = (Normal margin % × Units needed for incremental lift) / Incremental units
```

**Example**:
- Product: €50, 35% margin (€17.50 profit/unit)
- Baseline units/month: 200 units = €3,500 profit
- Bundle targets 15% incremental (30 new units)

Break-even discount = (35% × 200) / 30 = 23% discount maximum

**Validation**:
- Bundle price: €50 × (1 - 0.23) = €38.50
- Margin: 35% × €38.50 = €13.48
- Bundle profit: €13.48 × 30 = €404
- Baseline profit: €17.50 × 200 = €3,500
- Total profit with bundle: €3,500 + €404 = €3,904
- **Gain**: €404 (11.5% profit increase) ✓

---

**Continue to**: [Part 2: Clearance, Seasonal Campaigns & ROI Measurement](part2-clearance-seasonal-measurement.md)
