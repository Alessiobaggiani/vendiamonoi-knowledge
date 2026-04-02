# CAMPAIGN STRUCTURE & OPTIMIZATION FOR EUROPEAN MARKETPLACE DISTRIBUTION

**DEFINITIVE REFERENCE DOCUMENT**
*Last Updated: April 2, 2026*
*Scope: Amazon EU, eBay, Kaufland, Bol.com*

---

## SECTION 1: CAMPAIGN ARCHITECTURE FRAMEWORKS (300+ lines)

### 1.1 FOUNDATIONAL ARCHITECTURE PRINCIPLES

Campaign architecture is the skeletal structure that determines how your products, keywords, and budget allocate across the marketplace advertising system. Unlike opinions about strategy, architecture is measurable, replicable, and evidence-based.

#### 1.1.1 The Core Constraint: Amazon's ~25 Keyword Allocation Limit

**Structural Fact:** Amazon's algorithm allocates meaningful budget to approximately 25 keywords maximum per campaign, regardless of total budget assigned or keyword count added to the campaign.

**Implication:** Adding 100 keywords to a single campaign with a €5,000 budget produces identical results to adding 25 keywords with the same budget. The additional 75 keywords receive negligible impressions and no measurable conversion data.

**Practical Application:** Campaign architecture must be built around the 25-keyword maximum as the primary constraint. Campaigns with 1-5 high-intent keywords outperform campaigns with 50+ mixed-intent keywords.

#### 1.1.2 Campaign Hierarchy Structure

**Hierarchical Organization:**
```
Portfolio (Optional)
├── Campaign (Named: Category_Intent_MatchType_Margin)
    ├── Ad Group (Usually 1 per campaign for manual campaigns)
        ├── Keywords (1-25 maximum for effective allocation)
        └── Negative Keywords
    └── Budget Allocation
```

**Critical Specification:** For manual campaigns on Amazon, maintain one campaign = one ad group = one product or product group (SKU or ASIN cluster).

For auto campaigns, one campaign can contain multiple ASINs but should be logically related (same product category, similar margin tier, similar seasonality).

### 1.2 SINGLE-KEYWORD CAMPAIGNS VS MULTI-KEYWORD CAMPAIGNS

#### 1.2.1 Single-Keyword Campaign Architecture

**Definition:** Each campaign contains exactly one keyword matched to one or multiple products (ASINs).

**Structure:**
- Campaign: "Headphones_Search_Exact_25%Margin"
  - Ad Group: "Primary"
    - Keyword: "wireless headphones" (Exact match)
    - Negative Keywords: [used, broken, repair]
    - Budget: €50/day
    - Bid: €0.45

**Advantages:**
1. **Maximum Data Clarity**: Every impression, click, and conversion traces to a single keyword
2. **Precise Bid Control**: Adjust bids based on exact keyword performance without affecting other terms
3. **Rapid Scaling**: Proven keyword can be scaled independently to €500/day without keyword conflicts
4. **Clean Negative Keywords**: Negative list applies only to that keyword, preventing unintended blocks
5. **Performance Attribution**: Instantly identify which keywords drive profitability

**Disadvantages:**
1. **Campaign Proliferation**: 100 keywords = 100 campaigns (management overhead)
2. **Slower Launches**: Takes 2-3 weeks to gather sufficient data per keyword
3. **Fragmented Budget**: €3,000 total budget spread across 100 campaigns = €30/day per campaign (slow data accumulation)
4. **Platform Limits**: Amazon caps campaigns per account (1,000-5,000 depending on tier)
5. **Reporting Overhead**: Requires aggregation across hundreds of campaigns

**Profitability Profile:** High ACOS campaigns (15-20%) become highly profitable when scaled to €200+/day. Enables aggressive negative keyword strategies.

**When to Use:**
- Portfolio size: 20-100 keywords total
- Target ACOS: <15% (margin >35%)
- Team size: 1-2 people managing campaigns
- Platform: Amazon Advertising

#### 1.2.2 Multi-Keyword Campaign Architecture

**Definition:** One campaign contains 5-25 keywords grouped by intent or product similarity.

**Structure:**
- Campaign: "Headphones_Search_Broad_25%Margin"
  - Ad Group: "Primary"
    - Keywords:
      - "wireless headphones" (Broad)
      - "bluetooth headphones" (Broad)
      - "noise cancelling headphones" (Broad)
      - "over ear headphones" (Broad)
      - "premium headphones" (Broad)
    - Negative Keywords: [used, broken, repair]
    - Budget: €150/day
    - Bid: €0.35 (single bid applies to all)

**Advantages:**
1. **Consolidated Management**: 100 keywords = 10 campaigns (10x less overhead)
2. **Faster Budget Accumulation**: €150/day budget concentrates data vs €30/day per keyword
3. **Consistent Performance**: Algorithm distributes budget to top 3-5 keywords automatically
4. **Scalability**: Growth from €100/day to €500/day maintains keyword ratio
5. **Team Efficiency**: Single bid adjustment affects 5 keywords simultaneously

**Disadvantages:**
1. **Data Fragmentation**: Keyword-level performance becomes estimates, not certainties
2. **Bid Imprecision**: One bid for 5 keywords (some over-bid, some under-bid)
3. **Negative Keyword Conflicts**: Blocking for one keyword sometimes blocks others unintentionally
4. **Scaling Complexity**: Scaling from €100 to €500/day requires restructuring (splitting keywords)
5. **Keyword Cannibalization**: Similar keywords compete for same budget (e.g., "wireless" vs "bluetooth")

**Profitability Profile:** Requires TACOS >25% (3-month rolling target). More suited to discovery-phase campaigns.

**When to Use:**
- Portfolio size: 100-500 keywords total
- Target TACOS: <30% (margin 20-35%)
- Team size: 1-3 people managing campaigns
- Platform: eBay, Kaufland, Bol.com (where single-keyword overhead not justified)

#### 1.2.3 Comparison: Single vs Multi-Keyword

| Dimension | Single-Keyword | Multi-Keyword |
|-----------|----------------|---------------|
| Campaigns per 100 keywords | 100 | 10-15 |
| Data clarity per keyword | 100% (direct attribution) | 60-80% (algorithmic distribution) |
| Bid precision | Per-keyword | Per-campaign (averaged) |
| Time to significant data | 3-4 weeks (€30/day = 840 daily impressions) | 1-2 weeks (€150/day = 4,200 daily impressions) |
| Scaling complexity | Low (increase bid, increase budget) | Medium (requires restructuring at €500/day) |
| Negative keyword precision | High (blocks only target keyword) | Medium (may over-block related terms) |
| Team size to manage 100 keywords | 1-2 people | 1 person |
| Platform best suited | Amazon (high daily budgets, <1,000 keyword caps) | eBay, Kaufland (medium budgets, fewer platform constraints) |

---

### 1.3 SKU-LEVEL VS PRODUCT-GROUP CAMPAIGNS

#### 1.3.1 SKU-Level Architecture

**Definition:** Each product (ASIN) has dedicated campaigns at all match types (Auto, Broad, Phrase, Exact).

**Structure for Single Product (B00012345):**
```
Portfolio: "Headphones_25%Margin"
├── Campaign: "B00012345_Auto_Discovery"
│   └── ASINs: [B00012345]
├── Campaign: "B00012345_Broad_Search"
│   └── Keywords: [wireless headphones, bluetooth headphones]
├── Campaign: "B00012345_Phrase_Search"
│   └── Keywords: ["wireless headphones", "noise cancelling headphones"]
├── Campaign: "B00012345_Exact_Search"
│   └── Keywords: ["wireless headphones"]
└── Campaign: "B00012345_Product_Targeting"
    └── Targeting: [Competitors, Category]
```

**Advantages:**
1. **Complete Product Data**: Every keyword, targeting method, and match type tracked for single ASIN
2. **Lifecycle Visibility**: Identify when product enters decline phase (falling conversion rate)
3. **Seasonal Detection**: Automatic seasonal pattern recognition across all campaign types
4. **Highest Profitability Attribution**: Know exact profitability by product, not product group
5. **New Product Isolation**: Test new variants without affecting established product performance

**Disadvantages:**
1. **Campaign Explosion**: 50 products × 5 campaign types = 250 campaigns
2. **Budget Fragmentation**: €100/day split across 5 campaigns = €20/day per type (insufficient for data gathering)
3. **Scaling Complexity**: Adding 100 new products = 500 new campaigns to manage
4. **Negative Keyword Redundancy**: Same negative list replicated across 5 campaigns per product
5. **Team Scaling**: Managing 250+ campaigns requires dedicated 1-2 FTE

**Profitability Profile:** Enables portfolio-level profitability analysis. Can identify which SKUs are profitable, which drag ACOS.

**When to Use:**
- Portfolio size: 10-50 products total
- Margin variance: >20% difference between products
- Seasonal variance: Products with different seasonal patterns
- Team size: 2+ people managing campaigns
- Goal: Precise profitability per SKU

#### 1.3.2 Product-Group Architecture

**Definition:** Multiple related products (ASINs) share campaigns. For example, all sizes/colors of one headphone model grouped together.

**Structure for Product Group (4 colors of same model):**
```
Portfolio: "Headphones_25%Margin"
├── Campaign: "Wireless_Headphones_Auto_Discovery"
│   └── ASINs: [B00012345, B00012346, B00012347, B00012348]
├── Campaign: "Wireless_Headphones_Broad_Search"
│   └── Keywords: [wireless headphones, bluetooth headphones]
├── Campaign: "Wireless_Headphones_Phrase_Search"
│   └── Keywords: ["wireless headphones"]
└── Campaign: "Wireless_Headphones_Exact_Search"
    └── Keywords: ["wireless headphones"]
```

**Advantages:**
1. **Consolidated Budget**: €100/day per campaign type (vs €20/day split across 5 SKUs)
2. **Faster Data Gathering**: Algorithm distributes impressions across variants
3. **Simplified Management**: 50 products grouped = ~40 campaigns (vs 250 individual)
4. **Variant Testing**: Automatically identify which variant (color, size) converts best
5. **Efficient Scaling**: Adding 10 similar products = +8 campaigns (vs +50)

**Disadvantages:**
1. **Data Aggregation**: Cannot isolate one variant's performance (product-level attribution missing)
2. **Variant Cannibalization**: Budget may favor one color over others regardless of demand
3. **Lifecycle Blindness**: Cannot see when one variant enters decline phase
4. **Seasonal Averaging**: Different seasonal patterns within group average out
5. **Profitability Attribution**: Know group profitability, not individual SKU profitability

**Profitability Profile:** Requires TACOS <25% at group level (may hide unprofitable variants).

**When to Use:**
- Portfolio size: 50-200 products (variants within groups)
- Margin variance: <10% between variants
- Seasonal variance: Similar seasonal patterns
- Team size: 1-2 people managing campaigns
- Goal: Efficient scaling and variant testing

#### 1.3.3 Comparison: SKU-Level vs Product-Group

| Dimension | SKU-Level | Product-Group |
|-----------|-----------|---------------|
| Campaigns per 50 products | 250 (5 types × 50) | 40-50 (grouped variants) |
| Budget concentration per campaign | €25-50/day | €100-150/day |
| Data gathering timeline | 3-4 weeks | 1-2 weeks |
| Variant attribution | 100% (direct) | 80% (algorithmic) |
| Seasonal detection | Per-SKU | Per-group (averaged) |
| Lifecycle tracking | Per-SKU | Per-group |
| Management overhead | High (250+ campaigns) | Low (40-50 campaigns) |
| Portfolio scaling complexity | High (add product = add 5 campaigns) | Low (add variant = add 1 campaign) |
| Profitability attribution | Per-SKU | Per-group |
| Team size for 50-product portfolio | 2 FTE minimum | 1 FTE |

---

### 1.4 CAMPAIGN NAMING CONVENTIONS

#### 1.4.1 Standardized Naming System

**Format:** `Category_Intent_MatchType_MarginTier`

**Components:**
1. **Category** (Product Type):
   - Headphones
   - SmartSpeaker
   - PowerBank
   - etc.

2. **Intent** (Customer Purchase Intent):
   - Auto (Automatic/Discovery)
   - Broad (Broad Interest)
   - Phrase (Specific Intent)
   - Exact (High Intent)
   - Product (Product Targeting)

3. **MatchType** (Specificity Level):
   - For Search: Auto, Broad, Phrase, Exact
   - For Product: Category, Brand, ASIN, Competing

4. **MarginTier** (Product Margin):
   - 15% (Low margin, high volume)
   - 25% (Standard margin)
   - 40% (Premium margin)
   - etc.

**Examples:**
- `Headphones_Auto_Broad_25%` (Auto campaign for headphones, broad discovery, 25% margin)
- `SmartSpeaker_Exact_Exact_40%` (Exact match search for premium smart speakers)
- `PowerBank_Product_Category_15%` (Product targeting by category, low-margin power banks)
- `Headphones_Phrase_Phrase_25%` (Phrase match campaigns for mid-intent searches)

**Benefits:**
1. **Instant Identification**: Campaign purpose visible in name
2. **Hierarchical Organization**: Sort alphabetically produces logical portfolio structure
3. **Scaling Template**: New product uses same naming system
4. **Performance Comparison**: Similar campaigns (same intent + match type) comparable across categories
5. **Team Communication**: New team member understands structure without onboarding

#### 1.4.2 Portfolio Organization

**Hierarchical Structure:**
```
Portfolio: "Vendor_Name_2026_Q2"
├── Folder: "Margin_15%" (Volume products)
│   ├── Campaign: "Headphones_Auto_Broad_15%"
│   ├── Campaign: "Headphones_Broad_Broad_15%"
│   ├── Campaign: "PowerBank_Auto_Broad_15%"
│   └── Campaign: "PowerBank_Exact_Exact_15%"
├── Folder: "Margin_25%" (Standard products)
│   ├── Campaign: "SmartSpeaker_Auto_Broad_25%"
│   ├── Campaign: "SmartSpeaker_Exact_Exact_25%"
│   └── Campaign: "Headphones_Phrase_Phrase_25%"
└── Folder: "Margin_40%" (Premium products)
    ├── Campaign: "PremiumHeadphones_Auto_Broad_40%"
    └── Campaign: "PremiumHeadphones_Exact_Exact_40%"
```

**Benefits:**
1. **Margin-Based Optimization**: All 15% margin products grouped (adjust strategy systematically)
2. **Visual Organization**: Portfolio maps to business margin structure
3. **Batch Operations**: Adjust all high-margin products simultaneously
4. **Performance Comparison**: Compare like campaigns within margin tier

---

### 1.5 WATERFALL ARCHITECTURE (Sequential Structure)

#### 1.5.1 Waterfall Model Definition

**Concept:** Keywords start in Auto/Broad campaigns, graduate to Phrase, then to Exact match as they prove performance.

**Structure:**
```
Phase 1: Discovery
├── Campaign: "Headphones_Auto_Broad_25%"
│   └── ASINs: [B00012345, B00012346]
│   └── Budget: €100/day
│   └── Captures all search variation
│       └── Discovers keywords like "wireless", "noise cancelling", "waterproof"

Phase 2: Keyword Testing
├── Campaign: "Headphones_Broad_Broad_25%" 
│   └── Keywords: [wireless headphones, noise cancelling headphones, waterproof headphones]
│   └── Budget: €75/day (30% of auto)
│   └── Tests discovery keywords at controlled match type
│       └── Collects conversion data on each variation

Phase 3: Proven Keywords
├── Campaign: "Headphones_Phrase_Phrase_25%"
│   └── Keywords: ["wireless headphones", "noise cancelling headphones"]
│   └── Budget: €50/day (scaled from broad)
│   └── Narrows to proven keywords

Phase 4: Scaling High Performers
├── Campaign: "Headphones_Exact_Exact_25%"
│   └── Keywords: ["wireless headphones"]
│   └── Budget: €200/day (scaled from phrase)
│   └── Scales proven top performers
```

#### 1.5.2 Waterfall Advantages

1. **Progressive Data Gathering**: Auto campaign discovers keywords, Broad tests them, Phrase narrows, Exact scales
2. **Risk Mitigation**: Unknown keywords tested in Broad before scaling in Exact
3. **Budget Efficiency**: Low-performing keywords eliminated before scaling
4. **Performance Improvement**: Average ACOS improves as keywords progress (Auto 20% ACOS → Exact 12% ACOS)
5. **Lifecycle Alignment**: Matches product lifecycle (discovery → growth → maturity → scale)

#### 1.5.3 Waterfall Disadvantages

1. **Time Delay**: 8-12 weeks from auto discovery to exact scaling
2. **Complexity**: Requires 4 campaign types for one product group
3. **Budget Splitting**: €100/day total budget becomes €25/day per campaign (slower data gathering)
4. **Keyword Duplication**: "wireless headphones" exists in Auto, Broad, Phrase, and Exact (redundant impressions)
5. **Management Overhead**: Continuous keyword migration from one campaign to another

#### 1.5.4 Waterfall Workflow

**Week 1-2: Auto Campaign Launch**
- Launch auto campaign with €100/day budget
- Collect keyword discovery data
- Monitor for brand/competitor keywords (negative keyword additions)

**Week 3-4: Keyword Analysis**
- Extract keywords from auto campaign with >10 conversions
- Calculate conversion rate and ACOS per keyword
- Identify top 5-10 performers for broad campaign

**Week 5-6: Broad Campaign Launch**
- Create broad campaign with top-performing keywords
- Set budget at 30% of auto (€30/day)
- Monitor for intent mismatch (add negative keywords)

**Week 7-10: Broad Campaign Optimization**
- Collect conversion data on each broad keyword
- Identify keywords with >15 conversions and ACOS <15%
- Prepare phrase campaign setup

**Week 11-12: Phrase Campaign Launch**
- Migrate proven keywords to phrase campaign
- Set budget to 50% of broad (€15/day)
- Monitor for performance degradation

**Week 13-16: Exact Campaign Scaling**
- Migrate top phrase keywords to exact
- Scale budget aggressively (€150/day for proven high-performers)
- Monitor for budget ceiling (diminishing returns >€500/day)

---

### 1.6 MIRRORED ARCHITECTURE (Parallel Structure)

#### 1.6.1 Mirrored Model Definition

**Concept:** Create one identical campaign at each match type (Auto, Broad, Phrase, Exact) simultaneously for same product group. No sequential progression—campaigns run in parallel.

**Structure:**
```
Product Group: "Wireless Headphones"
├── Campaign: "Headphones_Auto_Discovery_25%"
│   └── ASINs: [B00012345, B00012346]
│   └── Budget: €100/day
│   └── Targets: All searches
│
├── Campaign: "Headphones_Broad_Search_25%"
│   └── Keywords: [wireless headphones, bluetooth headphones, noise cancelling headphones]
│   └── Budget: €50/day
│   └── Match Type: Broad
│
├── Campaign: "Headphones_Phrase_Search_25%"
│   └── Keywords: ["wireless headphones", "noise cancelling headphones"]
│   └── Budget: €50/day
│   └── Match Type: Phrase
│
└── Campaign: "Headphones_Exact_Search_25%"
    └── Keywords: ["wireless headphones"]
    └── Budget: €50/day
    └── Match Type: Exact
```

#### 1.6.2 Mirrored Architecture Advantages

1. **No Cannibalization**: Each match type operates independently (broad doesn't steal exact budget)
2. **Faster Data Gathering**: All campaigns launch simultaneously (4x faster data vs waterfall sequential launch)
3. **A/B Testing**: Compare match type performance side-by-side without time delay
4. **Simplified Migration**: No keyword shuffling between campaigns (static structure)
5. **Performance Clarity**: Broad ACOS vs Exact ACOS comparison immediate (not sequential)

#### 1.6.3 Mirrored Architecture Disadvantages

1. **Redundant Impressions**: Same search "wireless headphones" may match both Broad and Phrase campaign
2. **Budget Splitting**: €200/day split across 4 campaigns = €50/day each (slower data vs consolidated)
3. **Negative Keyword Fragmentation**: Broad campaign negative keywords don't prevent Exact campaign bids
4. **Higher Management Burden**: 4 campaigns to monitor vs 1 waterfall campaign
5. **Testing Complexity**: If negative keyword added to Exact campaign, must also add to Broad/Phrase to prevent redundant impressions

#### 1.6.4 Mirrored vs Waterfall

| Dimension | Mirrored | Waterfall |
|-----------|----------|----------|
| Campaigns for one product group | 4 (simultaneous) | 4 (sequential) |
| Launch speed | All at once (Week 0) | Staggered over 16 weeks |
| Time to compare match types | Immediate (Week 2) | 16 weeks |
| Redundant impressions | High (same search matches multiple campaigns) | None (keyword graduates, doesn't duplicate) |
| Budget concentration | Low (€50/day per campaign) | High (€100/day auto, graduates down) |
| Data gathering speed | Slower (split budget) | Fast (consolidated budget) |
| Negative keyword overhead | High (manage 4 lists independently) | Low (single list, inherited downstream) |
| Best use case | Fast A/B testing, performance comparison | Controlled rollout, risk mitigation |
| Team size | 2+ FTE | 1 FTE |

---

### 1.7 HYBRID ARCHITECTURE (Tiered Structure)

#### 1.7.1 Hybrid Model Definition

**Concept:** Combine SKU-level precision for top 10% products with product-group efficiency for remaining 90%.

**Tier 1: Top 10% Products (SKU-Level Architecture)**
```
Product: B00012345 (€5,000/month revenue)
├── Campaign: "B00012345_Auto_Discovery_25%"
└── Campaign: "B00012345_Exact_Search_25%"
```

**Tier 2: Remaining 90% Products (Product-Group Architecture)**
```
Product Group: "Standard_Headphones" (€1,000-2,000/month revenue each, 30 products)
├── Campaign: "Standard_Headphones_Auto_Discovery_25%"
│   └── ASINs: [B00023456, B00023457, ..., B00023485]
└── Campaign: "Standard_Headphones_Exact_Search_25%"
    └── Keywords: [wireless headphones, bluetooth headphones]
```

#### 1.7.2 Hybrid Architecture Advantages

1. **Precision + Efficiency**: Top products get SKU-level tracking, others get economies of scale
2. **Scalability**: Adding 50 new SKUs = 2-3 new product groups, not 250 new campaigns
3. **Budget Concentration**: Top 10% products receive focused budget; 90% pooled efficiently
4. **Lifecycle Clarity**: Monitor top performers' lifecycle; group other products
5. **Profitability Balance**: Know top products' unit economics; prioritize group optimization for remaining

#### 1.7.3 Hybrid Architecture Disadvantages

1. **Complexity**: Two different structures to manage (SKU vs group)
2. **Tier Transitions**: Moving product from group to SKU (or vice versa) requires restructuring
3. **Tier Definition**: Defining "top 10%" requires quarterly reviews
4. **Learning Curve**: Team must understand both models

#### 1.7.4 Hybrid Implementation Example: 200-SKU Portfolio

**Tier 1: Top 20 Products (10% of portfolio)**
- Average monthly revenue: €4,000-6,000 each
- Total revenue: €100,000/month
- Campaigns: 20 products × 2 campaign types = 40 campaigns
- Budget: €50/day per product (€1,000/month per product)
- Structure: SKU-level (Auto + Exact only, no intermediate match types)

**Tier 2: Standard 180 Products (90% of portfolio)**
- Average monthly revenue: €800-1,500 each
- Total revenue: €180,000/month
- Products grouped: 6 products per group (30 groups)
- Campaigns: 30 groups × 2 campaign types = 60 campaigns
- Budget: €30/day per group (€900/month, €150/product in group)
- Structure: Product-group (Auto + Exact)

**Portfolio Totals:**
- Campaigns: 100 total (40 tier 1 + 60 tier 2)
- Monthly budget: €50,000
- Management team: 1.5 FTE

---

## SECTION 2: AUTO CAMPAIGNS (300+ lines)

### 2.1 AUTO CAMPAIGN FUNDAMENTALS

#### 2.1.1 What is an Auto Campaign?

**Definition:** Amazon automatically matches advertised products to customer searches without keyword definition. The platform's algorithm determines which searches trigger your ads.

**Technical Operation:**
- Customer searches "wireless headphones"
- Amazon's algorithm analyzes:
  1. Product title, features, category, price
  2. Historical sales and conversion data
  3. Available competitor products
  4. Search intent interpretation
- Algorithm decides: "This seller's headphones product is relevant to this search"
- Ad is served (customer may click and convert, or scroll past)

#### 2.1.2 Why Auto Campaigns Matter

**Purpose 1: Keyword Discovery**
- Auto campaigns generate list of search terms your customers use
- These search terms become foundation for manual campaign keywords
- Without auto campaigns, manual campaign keywords are guesses

**Purpose 2: Niche Term Capture**
- Auto campaigns capture long-tail searches that no one thought to bid on manually
- "Wireless headphones for gym with sweat resistance" may be 5-10 searches/month
- Manual keyword list would never include this (too specific)
- Auto campaigns automatically bid on it

**Purpose 3: Demand Intelligence**
- Auto campaign search term report reveals what customers actually search for
- Keyword research tools show estimated volume; auto campaigns show actual volume (real customers)
- Inform product development (if "waterproof headphones" searches are high, develop waterproof variant)

**Purpose 4: Product Visibility**
- Auto campaigns catch searches where customer intent is high but keyword is obvious
- Customer searching for exact product name → auto campaign serves ad instantly
- Manual campaigns may have missed this (keyword lists often miss obvious terms)

#### 2.1.3 Auto Campaign Performance Expectations

**Typical Metrics (Average Portfolio):**
| Metric | Low Performer | Average | High Performer |
|--------|---|---|---|
| CTR (Click-Through Rate) | 0.3-0.5% | 0.8-1.2% | 1.5-2.5% |
| CPC (Cost Per Click) | €0.80-1.20 | €0.45-0.65 | €0.25-0.40 |
| Conversion Rate | 4-6% | 8-12% | 15-20% |
| ACOS | 35-45% | 18-25% | 12-16% |

**Interpretation:**
- CTR varies by product category (tech products: 0.9-1.2%; apparel: 0.4-0.6%)
- CPC reflects competitiveness (popular categories have higher CPC)
- Conversion rate depends on product quality and price competitiveness
- ACOS = (Ad Spend / Revenue) indicates profitability threshold

#### 2.1.4 Auto Campaign ACoS Explanation

**Why Auto ACOS is Often Higher Than Manual:**

1. **Less Precise Targeting**: Algorithm matches broad searches; manual campaigns target specific intent
   - Auto campaign serves ad for "headphones" (broad intent, may not want wireless)
   - Manual exact campaign serves ad only for "wireless headphones" (specific intent)
   - Broader intent = lower conversion rate = higher ACOS

2. **Volume vs Precision Trade-off**: Auto campaigns prioritize discovering all relevant searches
   - Manual campaigns prioritize converting high-intent searches
   - More searches = more impressions = more clicks = more cost
   - Higher cost with same conversion volume = higher ACOS

3. **Niche Term Inclusion**: Auto campaigns include long-tail searches with low volume but relevance
   - "Headphones for professional musicians" (100 searches/month, 5% conversion)
   - "Headphones with microphone for video recording" (50 searches/month, 8% conversion)
   - Manual campaigns wouldn't include these (too specific, too low volume)
   - Including low-volume niches brings down overall conversion rate

4. **Competitive Unavoidability**: Auto campaigns will match some competitor-adjacent searches
   - Customer searching "beats headphones" (competitor brand) gets auto campaign ad
   - May not convert (customer committed to Beats brand)
   - Manual campaigns can block via negative keywords; auto campaigns cannot pre-block
   - More impressions on low-intent searches = higher ACOS

**Target Metrics by Product Category:**
| Category | Typical Auto ACOS | Minimum Acceptable |
|----------|---|---|
| Electronics (high margin) | 18-25% | 30% |
| Apparel (medium margin) | 25-35% | 45% |
| Home & Kitchen (low margin) | 30-40% | 50% |

---

### 2.2 AUTO CAMPAIGN KEYWORD HARVESTING

#### 2.2.1 What is Keyword Harvesting?

**Definition:** Process of extracting search terms from auto campaign search term report and converting them into manual campaign keywords.

**Workflow:**
1. **Monitor Auto Campaign** (2-4 weeks)
   - Collect €500-1,000 in ad spend
   - Gather 1,000+ search term impressions
   - Record which searches led to clicks and conversions

2. **Generate Search Term Report**
   - Amazon Advertising → Campaigns → Auto Campaign → Search Term Report
   - Download CSV showing all searches that triggered ads
   - Data includes: Search Term, Impressions, Clicks, Spend, Conversions, ACOS

3. **Filter High-Performers**
   - Identify searches with:
     - ≥3 conversions AND ACOS <20%, OR
     - ≥10 clicks and CTR >1.5%
   - Example:
     - "wireless headphones" (25 conversions, 12% ACOS) → Harvest
     - "headphones battery life" (2 conversions, 8% ACOS) → Hold (too few conversions for reliability)
     - "beats headphones" (8 clicks, 0 conversions) → Don't harvest (competitor brand)

4. **Create Manual Campaign Keywords**
   - High-performer searches become exact match keywords in new manual campaign
   - Original search term "wireless headphones" (auto) → Exact keyword "wireless headphones" (manual exact campaign)

5. **Add Negative Keywords to Auto Campaign**
   - Searches that spent money but didn't convert → Add as negatives
   - Example: "beats headphones" → Add negative exact match -"beats headphones"
   - Prevents auto campaign from wasting budget on competitor brand searches

#### 2.2.2 Keyword Harvesting Decision Matrix

| Conversion Volume | ACOS | Action | Rationale |
|---|---|---|---|
| ≥5 conversions | <15% | Harvest → Exact campaign | High-intent, proven profitable |
| 3-4 conversions | <18% | Harvest → Exact campaign | Medium evidence, likely profitable |
| 1-2 conversions | <20% | Hold (retry in 4 weeks) | Insufficient data (1-2 conversions = luck, not pattern) |
| ≥5 conversions | 15-25% | Harvest → Broad campaign | Profitable but lower intent; test in broad |
| ≥5 conversions | >25% | Negative keyword | Unprofitable; prevent future spend |
| 0 conversions | Any | Negative keyword if >€10 spend | Wasted spend; prevent repetition |

#### 2.2.3 Keyword Harvesting Frequency

**Recommended Schedule:**
- **Weekly Review** (Quick scan): Identify obvious harvests (≥10 conversions) and obvious negatives (>€20 spend, 0 conversions)
- **Biweekly Harvesting** (Detailed): Run full search term analysis, harvest 5-10 new keywords for manual campaigns
- **Monthly Deep Dive** (Strategic): Analyze category trends, identify keywords representing >€200/month spend (should have dedicated manual campaigns)
- **Quarterly Review** (Portfolio): Evaluate whether auto campaign is still appropriate vs converting to full manual structure

**Week 1 Auto Campaign with €500 Budget:**
```
Search Term | Impressions | Clicks | Spend | Conversions | ACOS | Action
"wireless headphones" | 150 | 12 | €45 | 4 | 11% | HARVEST → Exact
"bluetooth headphones" | 120 | 10 | €38 | 3 | 13% | HARVEST → Exact
"noise cancelling headphones" | 100 | 9 | €35 | 2 | 18% | HOLD (only 2 conversions)
"waterproof headphones" | 80 | 4 | €25 | 0 | — | NEGATIVE keyword
"beats headphones" (competitor) | 90 | 8 | €50 | 0 | — | NEGATIVE keyword
Total | 540 | 43 | €193 | 9 | 21% | REPORT SUMMARY
```

---

### 2.3 AUTO CAMPAIGN TARGETING TYPES (Amazon Specific)

#### 2.3.1 Close Match Targeting

**Definition:** Algorithm matches searches that are semantically similar to product features.

**How It Works:**
- Product: Wireless Headphones with Noise Cancelling
- Product attributes (from title, description, category):
  - Type: Over-ear headphones
  - Features: Noise cancelling, wireless, 30-hour battery
  - Brand: [Your Brand]
  - Price: €150
- Customer search: "noise cancelling earbuds"
- Algorithm analysis: "Product has noise cancelling feature; search is for noise cancelling. Match probability: 60%"
- Outcome: Ad shown (algorithm considers 'close match')

**Characteristics:**
- Most restrictive auto campaign targeting type
- Lowest impression volume (but highest intent)
- Highest conversion rate (18-25%)
- Lowest ACOS (12-16%)
- Default targeting type on Amazon

**Use When:**
- High-margin products (>35% margin)
- Limited auto campaign budget (<€30/day)
- Prioritizing profitability over discovery

#### 2.3.2 Loose Match Targeting

**Definition:** Algorithm matches searches related to product category, even if not exact feature match.

**How It Works:**
- Product: Wireless Headphones with Noise Cancelling
- Customer searches matched:
  - "wireless headphones" (exact category match)
  - "headphones for running" (category + use case, product not running-specific)
  - "bluetooth speakers" (related category, different product type)
  - "audio equipment" (broad category)
- Algorithm shows ads to all four searches

**Characteristics:**
- Most permissive auto campaign targeting type
- Highest impression volume
- Moderate-to-low conversion rate (8-12%)
- Higher ACOS (18-25%)
- Captures discovery searches

**Use When:**
- Lower-margin products (20-30% margin) requiring volume
- Established brands (can tolerate wasted impressions)
- Goal is keyword discovery, not immediate profitability

#### 2.3.3 Substitutes Targeting

**Definition:** Algorithm matches searches for competitor products, assuming customer may consider your product as alternative.

**How It Works:**
- Your product: Mid-range wireless headphones (€150)
- Competitor products: Sony WH-1000XM5 (€400), Bose QuietComfort (€400)
- Customer searches: "sony headphones", "bose headphones", "best wireless headphones"
- Algorithm shows your ads (assuming customer may consider your cheaper alternative)

**Characteristics:**
- Moderate impression volume
- Lower conversion rate (5-8%, customer committed to specific brand)
- Higher ACOS (25-35%)
- Valuable for value-positioning
- Risk: Wasted spend on brand-loyal customers

**Use When:**
- Better value proposition (lower price, similar features)
- Sufficient budget to test ($30+/day)
- Brand recognition is secondary to value

#### 2.3.4 Complements Targeting

**Definition:** Algorithm matches searches for complementary products (not competitors).

**How It Works:**
- Your product: Wireless Headphones
- Complementary searches:
  - "headphone case" (customer looking for protection)
  - "portable phone charger" (customer on-the-go, may need additional power)
  - "audio cable" (customer building audio setup)
  - "desk headphone stand" (customer needs organization)
- Algorithm shows your headphone ads (assuming customer interested in audio/accessories)

**Characteristics:**
- Lower impression volume (complements are less frequent searches)
- Very low conversion rate (2-4%, search intent is different product)
- High ACOS (40-60%, essentially waste)
- Rarely profitable

**Use When:**
- Testing brand awareness (profitable ACOS not goal)
- Extremely low competitive pressure (excess budget)
- Rarely used in practice

#### 2.3.5 Auto Campaign Targeting Comparison

| Targeting Type | Impression Volume | CTR | Conversion Rate | ACOS | Best Use |
|---|---|---|---|---|---|
| Close Match | Low (100-300/day) | 1.5-2.0% | 18-25% | 12-16% | High-margin, profitability-first |
| Loose Match | High (500-1,500/day) | 0.8-1.2% | 8-12% | 18-25% | Discovery, volume-first |
| Substitutes | Medium (200-600/day) | 1.0-1.5% | 5-8% | 25-35% | Value positioning |
| Complements | Low (50-150/day) | 0.3-0.8% | 2-4% | 40-60% | Brand awareness only |

---

### 2.4 AUTO CAMPAIGN OPTIMIZATION

#### 2.4.1 Budget Allocation Strategy

**Principle:** Allocate auto campaign budget based on product margin tier, not equally across all products.

**Budget Distribution Model:**
```
Portfolio Annual Ad Spend: €50,000
Auto Campaign Allocation: 40% of total = €20,000/year

Product Tier Distribution:
├── High-Margin Products (>35%): 60% of auto budget = €12,000
│   ├── €100/day auto budget
│   ├── Generates €20,000/year revenue at 12% ACOS
│   └── Profit per product: €3,600 (after ad spend)
│
├── Standard-Margin Products (20-35%): 30% of auto budget = €6,000
│   ├── €30/day auto budget
│   ├── Generates €8,000/year revenue at 18% ACOS
│   └── Profit per product: €900 (after ad spend)
│
└── Low-Margin Products (<20%): 10% of auto budget = €2,000
    ├── €10/day auto budget
    ├── Generates €4,000/year revenue at 25% ACOS
    └── Profit per product: €300 (after ad spend)
```

**Rationale:**
- High-margin products can tolerate higher ACOS (12-16%) while remaining profitable
- Low-margin products must maintain strict ACOS targets (<20%) or become unprofitable
- Budget allocation reflects margin sustainability

#### 2.4.2 Negative Keyword Strategy for Auto Campaigns

**P1 Priority (Add within 24 hours):**
1. Competitor brand names (unless conducting competitor conquest)
   - Example: If selling your headphones, add -"beats", -"sony", -"bose"
2. Clear product mismatch
   - If selling headphones, add -"earbuds", -"speakers"
3. Used/Broken/Repair intent
   - Add: -"used", -"broken", -"repair", -"refurbished"

**P2 Priority (Add weekly):**
1. Accessory-only searches (not core product)
   - If selling headphones (not cases), add -"case", -"stand", -"cable"
2. Intent mismatches identified in search term report
   - Example: "cheapest headphones" (price-sensitive, low conversion) → -"cheap"
3. Volume-heavy, low-conversion searches
   - If "headphones" generates 50 clicks, 2 conversions, add -"headphones" (too broad)

**P3 Priority (Add monthly):**
1. Seasonal searches outside current season
   - If selling winter products, add -"summer" in June
2. Legal/regulatory blocks
   - If product not available in certain regions, add location-based negatives

#### 2.4.3 Auto Campaign Performance Review Cadence

**Daily Review (5 minutes):**
- Check current day's ACOS vs target
- If ACOS >30% by 2pm, pause campaign and investigate
- Monitor for unusual traffic patterns (suggests market event)

**Weekly Review (30 minutes):**
- Review search term report (all new searches that week)
- Harvest high-performing keywords for manual campaigns
- Add 3-5 new negative keywords from low-conversion searches
- Adjust budget up/down based on weekly ACOS trend

**Monthly Review (2 hours):**
- Calculate month ACOS vs prior month (trend analysis)
- Compare top 10 search terms to previous month (market shift detection)
- Evaluate if auto campaign role has changed (still discovery tool, or becoming main driver?)
- Plan quarterly strategy (continue, expand, pause, or convert to manual)

**Quarterly Review (4 hours):**
- Assess whether auto campaign is still appropriate for this product
- If auto campaign ACOS stable <15% for 3 months, may indicate keyword discovery complete; consider converting to full manual structure
- If auto ACOS trending up (was 15%, now 22%), market competitiveness increasing; add more negative keywords
- Review harvested keywords success rate (what % of harvested keywords remained profitable in manual campaigns?)

---

## SECTION 3: MANUAL CAMPAIGNS (400+ lines)

### 3.1 MANUAL CAMPAIGN FUNDAMENTALS

#### 3.1.1 What is a Manual Campaign?

**Definition:** Advertiser explicitly specifies keywords or product criteria; Amazon matches ads only to specified targets (not algorithmically inferred).

**Comparison to Auto:**
| Aspect | Auto Campaign | Manual Campaign |
|--------|---|---|
| Keyword Selection | Amazon's algorithm | Advertiser specifies |
| Search Matching | Broad interpretation | Exact match type rules |
| Control Level | Low (algorithmic) | High (keyword-specific) |
| Precision | Medium | High |
| Discovery | High (finds unexpected searches) | Low (only targets known keywords) |
| Setup Time | Fast (5 minutes) | Slower (hours to days) |
| Optimization | Broad (negative keywords) | Precise (per-keyword bid adjustment) |

#### 3.1.2 Why Manual Campaigns

**Reason 1: Precision Targeting**
- Auto campaign: "wireless headphones" search → show ad (might not have wireless)
- Manual exact campaign: Only show ad if search is exactly "wireless headphones"
- Eliminates wasted spend on non-matching intent

**Reason 2: Bid Control**
- Auto campaign: One bid for all searches (no per-keyword control)
- Manual campaign: Different bids for different keywords
  - "wireless headphones" = €0.50 bid (high conversion, higher bid OK)
  - "headphone sale" = €0.25 bid (lower conversion, lower bid needed)

**Reason 3: Performance Scaling**
- Auto campaign: When ACOS improves, Amazon doesn't allocate more budget (cap at ~25 keywords)
- Manual campaign: When keyword proves profitable, increase budget to €200+/day (unlimited scaling)

**Reason 4: Lifecycle Adaptation**
- Auto campaign: Cannot differentiate between product launch phase and decline phase
- Manual campaign: Can pause keywords in decline phase (old model), scale in launch phase (new model)

#### 3.1.3 Manual Campaign Match Types

**Broad Match**
- Symbol: None (default)
- Matching rule: Search contains keyword words in any order, with extra words allowed
- Example keyword: "wireless headphones"
  - Matches: "wireless headphones", "best wireless headphones", "wireless bluetooth headphones", "headphones wireless"
  - Doesn't match: "wired headphones"
- Typical ACOS: 18-25%
- Use for: Discovery within defined keyword scope

**Phrase Match**
- Symbol: "keyword in quotes"
- Matching rule: Search contains keyword phrase in order, with extra words allowed before/after
- Example keyword: "wireless headphones"
  - Matches: "wireless headphones", "best wireless headphones", "wireless headphones under €100"
  - Doesn't match: "headphones wireless" (wrong order), "wireless audio equipment" (different keyword)
- Typical ACOS: 14-18%
- Use for: Targeting specific intent with some flexibility

**Exact Match**
- Symbol: [keyword in brackets]
- Matching rule: Search must contain keyword (may have minor variations like plurals, synonyms)
- Example keyword: [wireless headphones]
  - Matches: "wireless headphones", "wireless headphones for running" (Amazon interprets as variant), "wireless earphones" (Amazon interprets "earphones" as synonym for "headphones")
  - Doesn't match: "bluetooth headphones" (different keyword)
- Typical ACOS: 12-16%
- Use for: Scaling proven keywords, highest profitability

#### 3.1.4 Match Type Selection Matrix

| Keyword Quality | Budget Available | Product Margin | Recommended Match Type |
|---|---|---|---|
| Proven (5+ conversions) | €50+/day | >30% | Exact match |
| Proven | €50+/day | 20-30% | Phrase match |
| Unproven (0-2 conversions) | <€30/day | Any | Broad match (test) |
| High-volume generic | €100+/day | >30% | Phrase match (capture volume) |
| High-volume generic | €100+/day | <30% | Broad match (risk mitigation) |
| Rare/niche keyword | <€30/day | Any | Exact match (focus budget) |

---

### 3.2 SINGLE KEYWORD AD GROUP (SKAG) METHODOLOGY

#### 3.2.1 What is SKAG?

**Definition:** One campaign = one ad group = one keyword = one landing page (product ASIN).

**Structure:**
```
Campaign: "Headphones_Exact_Search_25%"
└── Ad Group: "Primary"
    └── Keyword: ["wireless headphones"]
    └── Bid: €0.50
    └── Landing Page: ASIN B00012345 (specific headphone model)
```

#### 3.2.2 SKAG Advantages

1. **Maximum Attribution Clarity**: Every impression, click, conversion tied to one keyword
2. **Bid Precision**: Change €0.50 bid for "wireless headphones", no effect on "noise cancelling headphones"
3. **Scaling Confidence**: If "wireless headphones" converts 15%, confident scaling to €500/day
4. **Rapid Testing**: New product? New SKAG. Doesn't affect existing keywords.
5. **Negative Keyword Precision**: Block competitor search without over-blocking
   - Example: Only block -["beats wireless headphones"] in this campaign, not all "beats" searches across portfolio

#### 3.2.3 SKAG Disadvantages

1. **Campaign Proliferation**: 100 keywords = 100 campaigns (vs 5-10 with multi-keyword approach)
2. **Slower Data Gathering**: €3,000 budget / 100 campaigns = €30/day per campaign
   - 30/day budget → ~100 daily impressions → ~1 daily conversion
   - Takes 15-20 days to prove keyword profitability (vs 5-7 days with consolidated budget)
3. **Platform Limits**: Amazon caps campaigns per account (1,000-5,000 depending on tier)
   - If limit is 2,000 campaigns and 50% are SKAG, can only manage 1,000 keywords
4. **Redundant Infrastructure**: Each campaign has name, budget, bid, negative keywords, reporting
   - Managing 100 campaigns vs 10 is 10x work
5. **Consolidation Complexity**: Moving from SKAG to multi-keyword campaigns (for mature keyword) requires setting up new campaign and migrating budget

#### 3.2.4 SKAG Justified When

- Keyword monthly volume >100 (sufficient traffic to justify dedicated campaign)
- Per-keyword budget >€30/day (enough to gather data quickly)
- Product margin >25% (profitable threshold supports management overhead)
- Keywords have divergent performance (converting differently; justify separate bids)
- Portfolio size <100 keywords (platform limit not a constraint)
- Team size >2 FTE (campaign management workload distributed)

#### 3.2.5 SKAG Not Justified When

- Keyword monthly volume <30 (too little data)
- Per-keyword budget <€20 (too small to manage)
- Product margin <15% (management overhead unjustifiable)
- Keywords have similar conversion performance
- Small seller with <50 keywords total

### 3.5 NEGATIVE KEYWORD STRATEGY (ADVANCED)

#### 3.5.1 Negative Keyword Types and Matching Rules

**Negative Exact Match [-"keyword"]**
- Triggers: Search contains EXACTLY this keyword (no extra words)
- Use Case: Block specific competitor brands, specific product types
- Example: -["sony headphones"] blocks "sony headphones" but NOT "sony noise cancelling headphones"
- Risk: Often too narrow; easy to bypass with extra words

**Negative Phrase Match [-keyword] (Default)**
- Triggers: Search contains keyword in order (with or without extra words)
- Use Case: Block entire product categories, clear intent mismatches
- Example: -"used headphones" blocks "used headphones", "buy used headphones", "used wireless headphones"
- Benefit: Broad enough to catch variations; narrow enough to prevent over-blocking

**Negative Broad Match [-keyword without quotes]**
- Triggers: Search contains ANY of the keyword terms (different order OK)
- Use Case: Block entire categories; preventive blocking
- Example: -repair blocks "repair headphones", "headphones repair", "how to repair"
- Risk: Can over-block legitimate searches (e.g., -repair also blocks "repair shop near me" if selling headphones)

#### 3.5.2 Negative Keyword Priority Matrix

| Block Priority | Keyword Type | Match Type | Implementation Timing |
|---|---|---|---|
| **P1 (Immediate)** | Competitor brands (if not conquesting) | Exact | Within 24h of detection |
| **P1 (Immediate)** | Clear product mismatch (if selling headphones, -earbuds) | Exact | Within 24h |
| **P2 (Weekly)** | Intent misalignment (used, broken, repair) | Phrase | End of week batch |
| **P3 (Ongoing)** | Accessory-only terms (case, stand) | Broad | Monthly review |
