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

#### 1.2.1 Single-Keyword Campaign (SKC) Architecture

**Definition:** One campaign contains one primary keyword in exact match form, with related exact match variations as supporting keywords (never exceeding 5 total).

**Structure Example:**
```
Campaign: Electronics_Headphones_Exact_Premium
├── Keyword 1: "wireless headphones" [Exact]
├── Keyword 2: "wireless earbuds" [Exact]
├── Keyword 3: "bluetooth headphones" [Exact]
└── Budget: €500/month
```

**Performance Characteristics:**
- Bid Control: Extremely precise—single keyword bid control
- Data Accuracy: Conversion data attributed to single keyword
- Scaling: Requires 100+ campaigns for 100 keywords
- Management Overhead: High (requires daily monitoring of 100+ campaigns)
- Conversion Tracking: Highest accuracy (no attribution dilution)
- ACOS Optimization: Easiest to identify and pause underperformers

**When to Deploy SKC:**
- High-ticket items (COGS >€100)
- Branded keywords (brand defense)
- Keyword groups with wildly different conversion rates
- Testing phase (first 30 days of new keyword)
- Highly competitive categories (>€2 CPC)

#### 1.2.2 Multi-Keyword Campaign (MKC) Architecture

**Definition:** One campaign contains 5-25 related keywords (same intent, same match type), with unified budget and bid strategy.

**Structure Example:**
```
Campaign: Electronics_Headphones_Exact_Mid-Tier
├── Keyword 1: "wireless headphones" [Exact] → Bid: €0.85
├── Keyword 2: "best headphones" [Exact] → Bid: €0.72
├── Keyword 3: "over ear headphones" [Exact] → Bid: €0.68
├── Keyword 4: "noise cancelling headphones" [Exact] → Bid: €0.95
├── Keyword 5: "gaming headphones" [Exact] → Bid: €0.61
└── Budget: €2,500/month
```

**Performance Characteristics:**
- Bid Control: Keyword-level precision maintained
- Data Accuracy: Good (keywords share same intent)
- Scaling: Requires 20 campaigns for 100 keywords
- Management Overhead: Medium
- Conversion Tracking: Good (within-intent keywords similar behavior)
- ACOS Optimization: Moderate complexity (multiple variables)

**When to Deploy MKC:**
- 50-250 keywords total to manage
- Keywords with similar search intent
- Products with consistent margin (within 5%)
- Seasonal campaigns (ramp-up/wind-down phases)
- Low-to-medium competition categories

**Performance Data:** MKC campaigns maintain 85-92% efficiency versus SKC when keywords share intent. Scaling to 50+ keywords per campaign reduces efficiency to 65-75%.

### 1.3 SKU-LEVEL VS PRODUCT GROUP CAMPAIGNS

#### 1.3.1 SKU-Level Campaign Architecture

**Definition:** One campaign = one product (one ASIN). All keywords in the campaign promote only that single SKU.

**Structure Specification:**
```
Campaign: B08XK2V3M4_Wireless_Headphones_Exact
├── Ad Group (Single ASIN target)
│   └── ASIN: B08XK2V3M4
├── Keywords (5-15, all related to this specific product)
│   ├── "wireless headphones"
│   ├── "bluetooth headphones"
│   └── "noise cancelling earbuds"
└── Budget: €500-1,000/month
```

**Advantages:**
- Conversion tracking specific to single product
- Bid optimization targets single SKU ACOS
- Clear product-level ROI attribution
- Easier to identify if product is unprofitable
- Scales linearly with SKU count

**Disadvantages:**
- Requires many campaigns (one per SKU)
- Management complexity for large catalogs (100+ SKUs)
- Cannot leverage keyword synergies across variants
- Budget allocation per SKU may be suboptimal

**Implementation:** Use SKU-level architecture for:
- High-margin products (>30% margin)
- Branded products where SKU distinction matters
- Products with distinct targeting needs
- Small-to-medium catalogs (<50 SKUs)

#### 1.3.2 Product Group Campaign Architecture

**Definition:** One campaign targets multiple related ASINs (product group). Keywords apply to all products in the group.

**Structure Specification:**
```
Campaign: Electronics_Headphones_All_Variants_Exact
├── Ad Group (Multiple ASIN targets)
│   ├── ASIN: B08XK2V3M4 (Wireless model)
│   ├── ASIN: B08XK2V3M5 (Wired model)
│   ├── ASIN: B08XK2V3M6 (Gaming variant)
│   └── ASIN: B08XK2V3M7 (Budget variant)
├── Keywords (5-15, applies to all products)
│   ├── "wireless headphones"
│   ├── "best headphones"
│   └── "noise cancelling"
└── Budget: €2,000-5,000/month
```

**Advantages:**
- Single campaign manages multiple SKUs
- Keyword performance aggregated across variants
- Budget can flow to best-performing SKUs
- Reduced management overhead
- Good for product families

**Disadvantages:**
- Less precise ACOS per SKU
- Cannot target specific product variants with keywords
- Attribution mixing between products
- Harder to identify underperforming SKUs

**Implementation:** Use Product Group architecture for:
- Product families with similar profit margins
- Multiple color/size variants of same product
- Catalog size >50 SKUs
- Products where variants cannibalize each other anyway
- Cross-selling situations

### 1.4 NAMING CONVENTIONS FOR CAMPAIGNS

**Critical Importance:** Campaign names are the primary metadata signal for later reporting, analysis, and optimization. Inconsistent naming makes analysis impossible at scale.

#### 1.4.1 Campaign Naming Formula

**Standard Format:**
```
[Category]_[Product/Intent]_[MatchType]_[Margin/Tier]
```

**Real Examples:**
- `Electronics_Headphones_Exact_Premium` → Targeted exact-match headphones campaign for high-margin products
- `Fashion_Shoes_Phrase_Mid` → Phrase-match shoes for mid-margin products
- `Auto_CarBatteries_Broad_Budget` → Broad-match car batteries for budget tier
- `Home_CoffeeMaker_ASIN_Product` → ASIN-targeting (product ads) coffee maker

#### 1.4.2 Field Specifications

**[Category]** - Product category (max 15 chars)
- Electronics, Fashion, Home, Beauty, Sports, Auto, Books
- Use single word or hyphenated (Electronics_Smart not Electronics_Smart_Home_Devices)

**[Product/Intent]** - Specific product or search intent (max 20 chars)
- Headphones, Shoes, CoffeeMaker, RunningGels, DogFood
- Use keyword primary term that matches customer search intent

**[MatchType]** - Keyword match type or targeting method (Required)
- Auto, Exact, Phrase, Broad, ASIN, Category, Brand
- Use exact match type names

**[Margin/Tier]** - Profit margin or product tier (max 10 chars)
- Premium (>35% margin), Mid (20-35% margin), Budget (<20% margin)
- Or: Tier1, Tier2, Tier3 if margins vary within product group

**Rationale:** This formula ensures:
- Consistent structure across all campaigns
- Quick visual scanning for campaign type
- Easy filtering/sorting in reports
- ACOS benchmarking by category and margin

#### 1.4.3 Examples of CORRECT and INCORRECT Naming

**CORRECT:**
```
Electronics_Headphones_Exact_Premium
Electronics_Headphones_Phrase_Mid
Electronics_Headphones_Broad_Budget
Electronics_Headphones_Auto_All
Fashion_Shoes_ASIN_Premium
Home_Coffee_Category_Mid
```

**INCORRECT (Avoid These):**
```
Headphones Campaign #1  ← No structure, no match type, no margin
Headphones - Exact  ← Missing category and margin
Electronics 2024 Q2  ← No product/intent, date-based (bad practice)
Headphones_All_Types_ACOS_Optimized  ← Vague, uses ACOS (not a category)
Winter_Promo_Special  ← Seasonal only (campaigns live year-round)
Test_Campaign_v3  ← No real-world meaning
```

**Anti-Pattern:** Never include:
- Dates (campaigns live beyond single quarter)
- ACOS/RoAS figures (these change daily)
- Manager names (campaigns outlive employees)
- A/B test versions (v1, v2, v3) without clear parameter
- Promotional terms ("sale", "deal", "promo") that imply temporary status

### 1.5 WATERFALL CAMPAIGN ARCHITECTURE

**Definition:** Sequential campaign structure where high-intent keywords (exact match) feed into progressively broader targeting (phrase, broad, auto).

**Visual Structure:**
```
Exact Match (Highest Intent)
        ↓
    (Most Conversions)
Phrase Match (Medium Intent)
        ↓
   (Fewer Conversions)
 Broad Match (Low Intent)
        ↓
    (Rare Conversions)
 Auto Campaigns (Maximum Coverage)
        ↓
(Keyword Discovery)
```

**Campaign Specification:**
```
Campaign Level 1: Electronics_Headphones_Exact_Premium
├── Match Type: Exact only
├── Keywords: ~5-10 exact variations
├── Budget Allocation: 40%
├── Expected ACOS: 10-20%
└── Purpose: Highest-intent traffic

Campaign Level 2: Electronics_Headphones_Phrase_Premium
├── Match Type: Phrase only
├── Keywords: ~10-15 phrase variations
├── Budget Allocation: 30%
├── Expected ACOS: 20-35%
└── Purpose: Medium-intent traffic

Campaign Level 3: Electronics_Headphones_Broad_Premium
├── Match Type: Broad only
├── Keywords: ~5-10 broad match keywords
├── Budget Allocation: 20%
├── Expected ACOS: 35-50%
└── Purpose: Volume and discovery

Campaign Level 4: Electronics_Headphones_Auto_Premium
├── Match Type: Auto campaigns
├── Targeting: ASIN, Category, or Loose Match
├── Budget Allocation: 10%
├── Expected ACOS: 30-60%
└── Purpose: Keyword discovery for future campaigns
```

**Budget Allocation Rationale:**
- Exact match gets 40% because conversion rate is 3-5x better than broad
- Phrase match gets 30% (medium efficiency)
- Broad/Auto get 30% combined (discovery and volume)
- Exact match often produces 60-70% of total conversions despite being 40% of budget

**Advantage:**
- Clean separation of match types
- Budget naturally flows to highest-converting campaigns
- Easy to identify keyword quality (exact conversion > phrase > broad)
- Clear optimization path (promote good phrases to exact, demote poor phrases)

**Disadvantage:**
- Requires 3-4 campaigns per product instead of 1
- Higher management overhead
- Negative keyword maintenance across all levels

**When to Deploy Waterfall:**
- New product categories where you're discovering keywords
- High-volume categories (>1,000 monthly searches)
- Margin >20% (enough budget to support 4 campaigns)
- Team capacity >10 hours/week for management

### 1.6 MIRRORED CAMPAIGN ARCHITECTURE

**Definition:** Identical keyword/ASIN structure duplicated across multiple campaigns with different optimization targets.

**Visual Structure:**
```
Base Campaign: Headphones_Exact_ACOS_Optimized
├── Keywords: [wireless, bluetooth, noise-cancelling, gaming, over-ear]
├── Optimization: Target ACOS = 25%
└── Bid Adjustment: Automatic (Amazon)

   ↓ MIRROR ↓

Mirror Campaign: Headphones_Exact_ROAS_Optimized
├── Keywords: [wireless, bluetooth, noise-cancelling, gaming, over-ear]
├── Optimization: Target ROAS = 3.5:1
└── Bid Adjustment: Automatic (Amazon)

   ↓ MIRROR ↓

Mirror Campaign: Headphones_Exact_Sales_Optimized
├── Keywords: [wireless, bluetooth, noise-cancelling, gaming, over-ear]
├── Optimization: Target Sales = €500/day
└── Bid Adjustment: Manual (daily adjustment)
```

**Technical Specification:**
- Same keywords across all mirror campaigns
- Same ASIN targets
- Different bidding strategy (ACOS, ROAS, Manual)
- Separate daily negative keyword lists

**Advantage:**
- A/B test different bid optimization strategies simultaneously
- Identify which bidding strategy works best for category
- Can phase out underperforming strategies
- Requires minimal extra work (keyword duplication only)

**Disadvantage:**
- Budget split across 2-3 campaigns (less concentrated)
- Management complexity (3x negative keyword updates)
- Requires Amazon Ads API or manual duplicate creation

**Implementation Example:**
You want to test ACOS vs. ROAS optimization. Create two identical campaigns with same keywords, but one targets 25% ACOS and the other targets 3.5:1 ROAS. After 30 days, compare which strategy produced better profit.

**When to Deploy Mirrored:**
- Testing different bid optimization strategies
- Campaign budget >€1,000/month (large enough for meaningful splits)
- Want data on strategy effectiveness
- Team capacity for 2-3x management overhead

### 1.7 HYBRID CAMPAIGN ARCHITECTURE

**Definition:** Combination of waterfall, mirrored, and SKU/product-group strategies optimized for specific business constraints.

**Real-World Example: Electronics Retailer with 8 SKUs, €5,000/month budget**

```
Strategy: Hybrid Waterfall + SKU-Level

Tier 1: High-Margin SKUs (>30% margin) - SKU-Level + Exact Match
├── Campaign: Electronics_Headphones_B08XK_Exact_Premium
│   ├── ASIN: B08XK2V3M4
│   ├── Match: Exact
│   ├── Keywords: 5 exact variations
│   └── Budget: €1,200/month
│
└── Campaign: Electronics_Headphones_B08XK_Phrase_Premium
    ├── ASIN: B08XK2V3M4
    ├── Match: Phrase
    ├── Keywords: 8 phrase variations
    └── Budget: €400/month

Tier 2: Mid-Margin SKUs (20-30% margin) - Product Group + Exact/Phrase Mix
├── Campaign: Electronics_EarBuds_MidTier_Exact
│   ├── ASINs: [B08XK2V3M5, B08XK2V3M6]
│   ├── Match: Exact
│   ├── Keywords: 10 exact (shared across 2 SKUs)
│   └── Budget: €1,200/month
│
└── Campaign: Electronics_EarBuds_MidTier_Phrase
    ├── ASINs: [B08XK2V3M5, B08XK2V3M6]
    ├── Match: Phrase
    ├── Keywords: 12 phrase (shared across 2 SKUs)
    └── Budget: €600/month

Tier 3: Budget/Discovery - Auto Campaigns
├── Campaign: Electronics_Headphones_Auto_Discovery
│   ├── Type: Close Match Auto
│   ├── Category: Electronics > Audio > Headphones
│   └── Budget: €800/month
│
└── Campaign: Electronics_EarBuds_Auto_Discovery
    ├── Type: Loose Match Auto
    ├── Category: Electronics > Audio > Earbuds
    └── Budget: €600/month

Total: 6 campaigns, €5,000/month
- 40% (€2,000) to high-margin exact-match
- 30% (€1,500) to high-margin phrase-match
- 20% (€1,000) to mid-margin campaigns
- 10% (€500) to discovery/auto
```

**Key Decisions in Hybrid Architecture:**
1. SKU-level for high-margin (>30%) where individual ACOS matters
2. Product-group for mid-margin (20-30%) where volume justifies grouping
3. Waterfall structure (exact → phrase) within each tier
4. Auto campaigns only for discovery, not volume
5. Budget allocation by margin tier, not equal split

**Optimization Workflow:**
- Month 1-2: Run all campaigns, collect data
- Month 3: Harvest high-intent keywords from auto/broad campaigns
- Month 4: Create new exact-match campaigns from harvested keywords
- Month 5: Pause underperforming phrase campaigns
- Month 6: Start new testing on budget tier

**When to Deploy Hybrid:**
- Complex product catalog (>5 SKUs)
- Mixed profit margins across SKUs
- Budget >€3,000/month
- Team capacity >15 hours/week
- Want sophisticated optimization but can't commit to full-time PPC management

---

## SECTION 2: AUTO CAMPAIGN FUNDAMENTALS (200+ lines)

### 2.1 AUTO CAMPAIGN OVERVIEW

**Definition:** Amazon's automatic campaign type where the platform matches customer searches to your product listing without explicit keyword input. The algorithm determines which search queries trigger your ads.

**Core Mechanism:**
Amazon's system analyzes:
- Your product title, description, bullet points, backend keywords
- Customer search behavior in your category
- Historical conversion patterns for similar products
- Competitor listings in the same category

...then serves your ads to searches it predicts will convert.

### 2.2 AUTO CAMPAIGN MATCH TYPES

Amazon offers three auto campaign targeting types:

#### 2.2.1 Close Match Auto Campaigns

**Definition:** Most restrictive auto targeting. Matches customer searches to your product when the search is closely related to your product title, description, and indexed keywords.

**Matching Logic:**
- Searches must share 70%+ semantic similarity with your listing
- Product category must be exact or closely related
- Search intent must closely align with your product positioning

**Typical ACOS:** 15-30% (best performing auto type)
**Conversion Rate Multiplier:** 2-3x better than loose match

**Example Triggers:**
For product "Wireless Noise-Cancelling Headphones", Close Match might trigger on:
- "wireless headphones"
- "noise cancelling earbuds"
- "bluetooth headphones"
- "headphones with microphone"

But NOT on:
- "phone cases" (different product category)
- "headphone stands" (complementary, not substitute)
- "audio cables" (related but different intent)

**When to Use Close Match:**
- Budget-limited (best ROI of auto types)
- New products (need immediate conversion data)
- High-margin products (can afford 15-30% ACOS)
- Primary auto campaign (should be your first auto campaign)

#### 2.2.2 Loose Match Auto Campaigns

**Definition:** Most permissive auto targeting. Matches customer searches to your product even if the search is only moderately related to your product.

**Matching Logic:**
- Searches can be 40-60% semantically similar
- Related product categories accepted
- Complementary and substitute products included
- Customer interest signals primary factor

**Typical ACOS:** 30-60% (moderate to poor conversion)
**Conversion Rate Multiplier:** 0.5-1.5x vs. close match

**Example Triggers:**
For "Wireless Noise-Cancelling Headphones", Loose Match might trigger on:
- All close match triggers
- "bluetooth speakers" (substitute)
- "wireless earbuds" (variant)
- "phone volume control"
- "audio quality test"
- "listening to music"... (very broad)

**When to Use Loose Match:**
- High-volume demand (need maximum visibility)
- Budget sufficient for 30-60% ACOS
- Product available for low-margin markets (€< 50 product)
- Secondary discovery campaign (after close match)

#### 2.2.3 Substitutes Match Auto Campaigns

**Definition:** Mid-range auto targeting (Amazon's latest addition). Matches searches for products that customers view as substitutes or alternatives to your product.

**Matching Logic:**
- Searches include competitor products and alternatives
- Customer viewed your product as substitute option
- Cross-category product comparisons (e.g., headphones vs. earbuds)
- Intent: "If not your product, here's an alternative"

**Typical ACOS:** 25-45% (moderate conversion)
**Conversion Rate Multiplier:** 1.2-2x vs. loose match, but lower than close match

**Example Triggers:**
For "Wireless Noise-Cancelling Headphones", Substitutes might trigger on:
- "sony wh-1000xm4 headphones" (competitor direct)
- "best noise cancelling headphones under €200" (comparison search)
- "wireless earbuds vs headphones" (category alternative)
- "gaming headphones" (use-case alternative)

**When to Use Substitutes:**
- Competitive categories (many alternatives available)
- Want to bid on competitor searches
- Product price-point competitive (€100-500 range)
- Looking for market-share shifts

#### 2.2.4 Complements Match Auto Campaigns

**Definition:** Auto targeting that serves your ads when customers search for complementary products (products used together with yours).

**Matching Logic:**
- Searches for products that complement your offering
- Items commonly purchased together with your product type
- Secondary products in a larger solution
- Intent: "Your main product + our complement = better solution"

**Typical ACOS:** 40-100% (poor to terrible conversion)
**Conversion Rate Multiplier:** 0.2-0.8x vs. close match

**Example Triggers:**
For "Wireless Noise-Cancelling Headphones", Complements might trigger on:
- "headphone stands" (storage)
- "audio cables" (connectivity)
- "headphone cleaning kit" (maintenance)
- "microphone boom arm" (if recording headphones)

**When to Use Complements:**
- Extreme budget availability
- Product low-cost (<€20) so high ACOS acceptable
- Want maximum volume regardless of conversion
- Testing phase only

**Recommendation:** Most sellers should NOT use Complements. The conversion rates are typically too poor to justify the ad spend.

### 2.3 AUTO CAMPAIGN STRUCTURE FOR KEYWORD HARVESTING

**Purpose:** Auto campaigns serve as keyword research tools. Valuable searches that convert are"harvested" and moved to manual campaigns for optimization.

**Harvesting Workflow:**
```
Month 1: Run Auto Campaigns (all match types)
    ↓
Month 2: Analyze search term reports
    ↓
Month 3: Identify high-converting searches (>25% conversion rate)
    ↓
Month 4: Create manual exact-match campaigns from top searches
    ↓
Month 5: Add top searches to negative keywords in auto campaigns
    ↓
Month 6: Run refined auto campaigns for NEW keyword discovery
```

**Identification Criteria for Harvesting:**
- Searches with >10 clicks
- Conversion rate >25% (for your category average ACOS)
- Cost per conversion <€15 (adjust per margin)
- Search term length 2-4 words (not long-tail)

**Example Harvest:**
```
Search Terms Report:
1. "wireless headphones" - 45 clicks, 12 conversions, ACOS 18% ← HARVEST
2. "noise cancelling headphones" - 38 clicks, 8 conversions, ACOS 22% ← HARVEST
3. "gaming headphones" - 22 clicks, 2 conversions, ACOS 65% ← DO NOT HARVEST
4. "headphones with microphone" - 15 clicks, 4 conversions, ACOS 28% ← MAYBE HARVEST
5. "headphone stand" - 8 clicks, 0 conversions, ACOS ∞ ← DO NOT HARVEST

Action:
- Create manual exact-match campaign: "wireless headphones"
- Create manual exact-match campaign: "noise cancelling headphones"
- Add "gaming headphones" to auto campaign negative keywords
- Add "headphone stand" to auto campaign negative keywords
- Keep monitoring "headphones with microphone"
```

---

## SECTION 3: MANUAL CAMPAIGN FUNDAMENTALS (250+ lines)

### 3.1 MANUAL CAMPAIGN OVERVIEW

**Definition:** Manual campaigns where you explicitly select keywords to target. You control which searches trigger your ads.

**Core Advantage vs. Auto:** Precision. You can:
- Bid higher on keywords you know convert
- Bid lower on keywords with poor ACOS
- Exclude keywords that don't match your product
- Build campaigns around specific customer intents

### 3.2 MATCH TYPES IN MANUAL CAMPAIGNS

Three match types control how broadly your keyword matches customer searches:

#### 3.2.1 Exact Match [exact]

**Definition:** Your ad shows only when a customer searches the EXACT keyword you're bidding on (or very close variations).

**Matching Behavior:**
- "wireless headphones" [exact] matches:
  - "wireless headphones"
  - "wireless headphones uk"
  - "headphones wireless"
  - "headphones, wireless"

- But NOT:
  - "wireless bluetooth headphones" (added word)
  - "wireless" (partial)
  - "gaming headphones" (different keyword)

**ACOS Characteristics:** Lowest (best) ACOS because intent alignment is highest.
- Typical ACOS: 15-25%
- Conversion Rate: Highest among match types
- Search Volume: 100% of exact searches
- Management: Requires one campaign per keyword (or 5-15 related keywords)

**Syntax:** Use brackets [keyword]

**When to Use Exact Match:**
- High-intent keywords
- Keywords with conversion history
- Limited budget (must maximize ROAS)
- Protecting brand keywords
- SKU-specific keywords

#### 3.2.2 Phrase Match "keyword"

**Definition:** Your ad shows when a customer searches your keyword as a phrase, with other words before or after.

**Matching Behavior:**
- "wireless headphones" [phrase] matches:
  - "wireless headphones"
  - "best wireless headphones"
  - "wireless headphones uk"
  - "wireless headphones under €200"
  - "good quality wireless headphones"

- But NOT:
  - "headphones wireless" (word order reversed)
  - "noise cancelling wireless headphones" - may or may not match, depends on Amazon algorithm
  - "wireless" (missing word)
  - "bluetooth headphones" (different phrase)

**ACOS Characteristics:** Medium ACOS (2-3x worse than exact match).
- Typical ACOS: 25-35%
- Conversion Rate: 50-70% of exact match conversion rate
- Search Volume: 3-5x more searches than exact match
- Management: 5-15 phrase keywords per campaign is reasonable

**Syntax:** Use quotes "keyword"

**When to Use Phrase Match:**
- Medium-intent keywords
- Keywords with some conversion history but not proven
- Budget sufficient for moderate ACOS
- Want more volume than exact match
- Building awareness around keyword themes

#### 3.2.3 Broad Match keyword (no symbol)

**Definition:** Your ad shows for your keyword AND related/similar keywords that the Amazon algorithm determines are relevant.

**Matching Behavior:**
- wireless headphones [broad] matches:
  - "wireless headphones"
  - "bluetooth headphones"
  - "gaming headphones"
  - "wireless earbuds"
  - "headphones"
  - "audio equipment"
  - "listening to music"... basically anything vaguely related

**ACOS Characteristics:** Highest (worst) ACOS among match types.
- Typical ACOS: 35-60%
- Conversion Rate: 20-40% of exact match conversion rate
- Search Volume: 5-10x more searches than exact match
- Management: Should use negative keywords aggressively

**Syntax:** Type keyword without symbols

**When to Use Broad Match:**
- Discovery and volume (you're willing to accept poor ACOS)
- Very high-budget campaigns (€1,000+/month)
- Products where ACOS tolerance is high (low-margin or high-volume)
- Testing phase when you want to see full range of related searches

**Critical Warning:** Broad match without negative keywords is a money-wasting machine. Always use negative keywords with broad match.

### 3.3 MATCH TYPE COMPARISON TABLE

| Metric | Exact | Phrase | Broad |
|--------|-------|--------|-------|
| **Typical ACOS** | 15-25% | 25-35% | 35-60% |
| **Search Volume Relative to Exact** | 1x | 3-5x | 5-10x |
| **Conversion Rate Relative to Exact** | 100% | 50-70% | 20-40% |
| **Cost per Conversion** | Lowest | Medium | Highest |
| **Campaigns Needed for 100 Keywords** | ~100 | ~20 | ~10 |
| **Management Overhead** | High | Medium | Low |
| **Best For** | High-intent | Discovery | Volume |
| **Requires Negative Keywords** | Rarely | Regularly | Always |

**Conclusion:** Exact match is almost always better than broad/phrase IF you have sufficient budget. The lower ACOS more than compensates for lower volume.

---

## SECTION 4: KEYWORD STRATEGY AND NEGATIVE KEYWORDS (200+ lines)

### 4.1 KEYWORD SELECTION FRAMEWORK

When building manual campaigns, keyword selection determines 80% of success.

#### 4.1.1 Keyword Classification by Intent

**Navigational Keywords** ("your brand")
- Customer searching for your specific brand/product
- Example: "sony wh-1000xm4", "apple airpods"
- ACOS: 5-15% (best converting)
- Purpose: Capture brand-aware customers
- Strategy: Create separate "Brand Defense" campaigns

**Informational Keywords** ("how to", "best", "reviews")
- Customer researching the product category
- Example: "best headphones under €200", "how to choose headphones"
- ACOS: 25-40% (moderate converting)
- Purpose: Reach researching customers
- Strategy: Use phrase match for these

**Transactional Keywords** ("buy", "order", "shop")
- Customer ready to purchase
- Example: "buy wireless headphones", "order headphones online"
- ACOS: 20-35% (good converting)
- Purpose: Capture purchase-intent traffic
- Strategy: Exact match these aggressively

**Comparison Keywords** (competitor names, "vs")
- Customer comparing alternatives
- Example: "sony vs bose headphones", "wireless vs wired headphones"
- ACOS: 30-45% (depends on position)
- Purpose: Steal market share
- Strategy: Use only if budget permits and product competitive

### 4.2 NEGATIVE KEYWORD STRATEGY

**Definition:** Keywords you tell Amazon NOT to show your ads for.

**Syntax:**
- Negative Exact: [-keyword]
- Negative Phrase: [-"keyword"]
- Negative Broad: [-keyword]

**Critical Importance:** Negative keywords prevent wasted ad spend on non-converting searches.

#### 4.2.1 Common Negative Keyword Categories

**1. Competitor Names** (unless bidding on them intentionally)
```
[-sony] [-bose] [-sennheiser] [-shure]
```

**2. Irrelevant Product Types**
```
[-headphone stand] [-headphone case] [-audio cable] [-headphone amplifier]
```

**3. Low-Intent Modifiers**
```
[-review] [-tutorial] [-guide] [-cheap] [-used] [-broken]
```

**4. Incompatible Use Cases**
```
[-swimming] [-underwater] [-airport] (if your headphones aren't suitable)
```

**5. High-Volume, Low-Converting Terms**
```
[-headphones for sleep] (if product not suitable)
[-noise cancelling earplugs] (different product)
[-vintage headphones] (different market)
```

#### 4.2.2 Negative Keyword Building Workflow

```
Month 1: Run campaign with minimal negatives
    ↓
Month 2: Analyze search term report
    ↓
1. Identify searches with 5+ clicks but 0 conversions → Add as negative
2. Identify searches for competitor products → Add as negative
3. Identify searches for incompatible products → Add as negative
    ↓
Month 3: Implement negative keywords
    ↓
Month 4: Re-analyze with refined negatives
    ↓
Months 5+: Continuous refinement
```

---

CONTINUES IN PART 2: Manual Campaigns Deep Dive, Campaign Lifecycle, Testing & Optimization, Implementation Checklist