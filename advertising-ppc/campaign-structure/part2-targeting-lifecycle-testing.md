**SKAG Not Justified When:**
- Keyword monthly volume <100 searches
- Product cost <€50 (can't sustain individual management)
- Margin <15% (ACOS tolerance too tight)
- Team capacity <5 hours/week for management
- Keywords have similar conversion rates

---

## SECTION 5: MANUAL CAMPAIGNS - EXACT, PHRASE, BROAD DEEP DIVE (250+ lines)

This section completes the manual campaign strategy with detailed implementation patterns.

### 5.1 EXACT MATCH CAMPAIGN OPTIMIZATION

**Campaign Structure Template:**
```
Campaign Name: [Category]_[Product]_Exact_[Margin]
Match Type: Exact Only
Budget: €200-500/month per keyword group
Bid Strategy: Manual or Auto based on volume
Daily Budget: €10-25
```

**Optimal Campaign Composition:**
- 3-5 tightly related exact-match keywords
- All keywords should target same customer intent
- Keywords share similar conversion rates (within 10% spread)
- Negative keywords: Category-wide negatives applied

**Example - Electronics Headphones Exact:**
```
Campaign: Electronics_Headphones_Exact_Premium
├── Keyword 1: [wireless headphones] - Bid: €0.95
├── Keyword 2: [noise cancelling headphones] - Bid: €1.15
├── Keyword 3: [bluetooth headphones] - Bid: €0.85
├── Keyword 4: [gaming headphones] - Bid: €0.75
└── Keyword 5: [wireless earbuds] - Bid: €0.80

Negative Keywords:
- [-stand] [-case] [-cable] [-review] [-cheap] [-used]
```

**Bid Adjustment Strategy:**
Monitor CTR and conversion rate weekly:
- If conversion rate declining: increase bid by €0.05-0.10
- If conversion rate high (>8%): can increase bid by €0.10-0.15
- If ACOS exceeding target: reduce bid by €0.05-0.10

**Expected Metrics (Wireless Headphones):**
- Average CPC: €0.80-1.20
- Average CTR: 2-4%
- Average Conversion Rate: 8-15%
- Typical ACOS: 15-25%
- Typical ROI: 4:1 to 6:1

### 5.2 PHRASE MATCH CAMPAIGN OPTIMIZATION

**Campaign Structure Template:**
```
Campaign Name: [Category]_[Product]_Phrase_[Margin]
Match Type: Phrase Only
Budget: €300-800/month per keyword group
Bid Strategy: Manual (phrase needs more control)
Daily Budget: €15-35
```

**Optimal Campaign Composition:**
- 8-15 phrase-match keywords
- Keywords related but not identical in intent
- Can support wider variation in search terms
- Negative keywords: Category-wide + competitor names

**Example - Fashion Shoes Phrase:**
```
Campaign: Fashion_Shoes_Phrase_Mid
├── Keyword 1: "running shoes" - Bid: €0.65
├── Keyword 2: "best running shoes" - Bid: €0.75
├── Keyword 3: "running shoes women" - Bid: €0.60
├── Keyword 4: "lightweight running shoes" - Bid: €0.70
├── Keyword 5: "cushioned running shoes" - Bid: €0.72
├── Keyword 6: "running shoes sale" - Bid: €0.45
├── Keyword 7: "marathon running shoes" - Bid: €0.80
├── Keyword 8: "trail running shoes" - Bid: €0.75
└── Keyword 9: "water resistant running shoes" - Bid: €0.65

Negative Keywords:
- [-review] [-cheap] [-used] [-orthopaedic] [-hiking] [-soccer]
- [-nike] [-adidas] [-asics] (competitor brands)
```

**Bid Optimization:**
Phrase match requires more active management:
- Analyze search term report every 2 weeks
- High-ACOS search variations: reduce bid by €0.10-0.15
- High-converting variations: increase bid by €0.05-0.10
- Poor-converting patterns: move to negative keywords

**Expected Metrics (Running Shoes):**
- Average CPC: €0.55-0.85
- Average CTR: 1.5-2.5%
- Average Conversion Rate: 4-7%
- Typical ACOS: 25-40%
- Typical ROI: 2.5:1 to 4:1

### 5.3 BROAD MATCH CAMPAIGN OPTIMIZATION

**Campaign Structure Template:**
```
Campaign Name: [Category]_[Product]_Broad_[Margin]
Match Type: Broad Only
Budget: €400-1500/month per keyword group
Bid Strategy: Manual (critical for broad match control)
Daily Budget: €20-50
Negative Keywords: AGGRESSIVE - 50+ negatives typical
```

**CRITICAL WARNING:** Broad match without aggressive negative keywords WILL waste budget.

**Optimal Campaign Composition:**
- 5-10 broad-match keywords only
- Aggressively use negative keywords (50-100+ negatives typical)
- Monitor search term report daily initially
- High tolerance for "irrelevant" searches (this is feature, not bug)

**Example - Home Coffee Maker Broad:**
```
Campaign: Home_CoffeeMaker_Broad_Mid
├── Keyword 1: coffee maker - Bid: €0.45
├── Keyword 2: espresso machine - Bid: €0.50
├── Keyword 3: coffee equipment - Bid: €0.35
├── Keyword 4: brewing coffee - Bid: €0.40
└── Keyword 5: morning coffee - Bid: €0.38

Negative Keywords (EXTENSIVE LIST):
- Competitor brands: [-nespresso] [-delonghi] [-philips] [-bosch] [-gaggia]
- Product types: [-grinder] [-beans] [-filters] [-pods] [-scales]
- Irrelevant searches: [-tea] [-cheap] [-free] [-reviews] [-diy]
- Use cases: [-office] [-shop] [-retail] [-commercial] [-automatic]
- Related services: [-repair] [-rental] [-lease] [-second-hand]

Typical total: 60-100 negative keywords
```

**Bid Optimization for Broad Match:**
More conservative approach due to volume:
- Bid lower than exact/phrase match (€0.35-0.50 typical)
- Adjust based on search term report (weekly analysis essential)
- Remove bottom 20% of search terms monthly
- Only scale up if ACOS remains acceptable

**Expected Metrics (Coffee Maker):**
- Average CPC: €0.35-0.60
- Average CTR: 1-2%
- Average Conversion Rate: 2-4%
- Typical ACOS: 35-60%
- Typical ROI: 1.5:1 to 2.5:1
- High variation (due to search term variety)

### 5.4 SINGLE KEYWORD AD GROUP (SKAG) METHODOLOGY

**Definition:** One campaign = one keyword. Extreme version of exact-match focusing.

**SKAG Structure:**
```
Campaign: Category_Product_Exact_Margin
├── Single Ad Group
├── Single Keyword: [specific keyword]
├── 3 Product Ads (same ASIN, different product images)
├── Negative Keywords: Category-wide list
└── Budget: €200-500/month
```

**Rationale for SKAG:**
- Perfect ACOS attribution (one keyword = one ACOS figure)
- Bid control at keyword level (can adjust €0.05 increments)
- A/B testing enabled (one keyword per campaign)
- Performance data unambiguous (no mixing)

**When SKAG Makes Sense:**
1. High-ticket products (COGS >€150)
2. Branded keywords (brand defense campaign)
3. Keywords with wildly different performance (need separate management)
4. Significant budget per keyword (€500+/month)
5. Team capacity for 100+ campaigns

**SKAG Implementation Example:**
```
Product: Premium Wireless Headphones (€250 retail, €100 COGS, 60% margin)

Campaign 1: Electronics_Headphones_Exact_Premium_Wireless
├── Keyword: [wireless headphones]
├── Bid: €1.10
├── Budget: €400/month
├── Negative: [-stand] [-case] [-review] ... (category-wide)
└── Conversion Rate: 12%, ACOS: 18%

Campaign 2: Electronics_Headphones_Exact_Premium_Noise-Cancelling
├── Keyword: [noise cancelling headphones]
├── Bid: €1.35
├── Budget: €500/month
├── Negative: [-stand] [-case] [-review] ... (category-wide)
└── Conversion Rate: 10%, ACOS: 22%

Campaign 3: Electronics_Headphones_Exact_Premium_Gaming
├── Keyword: [gaming headphones]
├── Bid: €0.90
├── Budget: €300/month
├── Negative: [-stand] [-case] [-review] ... (category-wide)
└── Conversion Rate: 8%, ACOS: 28%

Campaign 4: Electronics_Headphones_Exact_Premium_Bluetooth
├── Keyword: [bluetooth headphones]
├── Bid: €1.05
├── Budget: €400/month
├── Negative: [-stand] [-case] [-review] ... (category-wide)
└── Conversion Rate: 11%, ACOS: 19%

Total: 4 campaigns, €1,600/month
Blended ACOS: 21.75%
```

**SKAG Optimization Workflow:**
1. Week 1-2: Monitor bid effect on CTR/conversion
2. Week 3-4: Adjust bids based on data
3. Week 5-8: Collect 30+ conversions per keyword
4. Month 2: Evaluate performance vs. baseline
5. Month 3: Pause underperforming keywords
6. Month 4+: Scale winners, keep baseline performers

**Disadvantage of SKAG:**
- Requires 100+ campaigns to manage 100 keywords
- Management overhead: 1-2 hours per day
- Difficult to scale to 500+ keywords
- Only justified for very high-budget accounts

---

## SECTION 6: CAMPAIGN LIFECYCLE & SEASONAL MANAGEMENT (200+ lines)

### 6.1 CAMPAIGN LIFECYCLE PHASES

Every successful campaign passes through distinct phases:

#### 6.1.1 Launch Phase (Weeks 1-4)

**Objectives:**
1. Collect first conversion data
2. Identify search term patterns
3. Validate keyword quality
4. Establish baseline metrics

**Activities:**
- Set initial bids at €0.50-0.80 range (conservative)
- Run daily budget €10-20 to stay visible
- Review search term report daily
- Identify "accidental" high-ACOS searches for negating
- DO NOT optimize yet (need data)

**Success Metrics for Launch:**
- 20-30 clicks within first week
- At least 5-10 conversions by day 21
- ACOS <50% (acceptable for launch phase)

**Common Mistakes:**
- Setting bids too high initially (burns budget on unknown keywords)
- Pausing keywords after 0-2 clicks (need data)
- Adding 100+ keywords (should start with 5-10)
- Changing bids daily (need 7-10 days minimum between changes)

#### 6.1.2 Growth Phase (Weeks 4-12)

**Objectives:**
1. Increase volume while controlling ACOS
2. Identify top-performing keywords
3. Build negative keyword list
4. Scale budget

**Activities:**
- Increase bids on high-converting keywords (>5% conversion rate) by €0.10-0.20
- Add strong negative keywords from search term report
- Increase daily budget 10-20% weekly
- Test slight keyword variations (phrase match variations)
- Begin A/B testing (if resources available)

**Expected Growth Phase Metrics:**
- Conversions increasing 15-25% weekly
- ACOS trending toward 20-30% target
- 15+ conversions per keyword minimum
- Search term patterns becoming clear

**Success Criteria for Growth Phase:**
- ACOS within 30% of target
- Conversion volume doubling from launch
- Clear winners and losers identified

#### 6.1.3 Maturity Phase (Months 3-6)

**Objectives:**
1. Stabilize ACOS at target level
2. Maximize conversion volume
3. Harvest top keywords for expansion
4. Maintain profitability

**Activities:**
- Fine-tune bids monthly (€0.05 adjustments)
- Expand winning keyword themes into new campaigns
- Consolidate underperformers
- Review competitive landscape monthly
- Build lookalike campaigns from best performers

**Expected Maturity Phase Metrics:**
- ACOS stable month-to-month (±2%)
- Conversion volume stable
- 50-100+ conversions per keyword minimum
- Reliable ROI predictable

#### 6.1.4 Decline Phase (Months 6+)

**Warning Signs of Decline:**
- ACOS increasing month-over-month (>3% increase)
- Conversion volume declining 10%+ monthly
- CPC increasing faster than conversion rate
- Market saturation (all available customers reached)

**Typical Decline Causes:**
1. Keyword saturation (market fully penetrated)
2. Increased competition (competitors raising bids)
3. Seasonal decline (product demand drops)
4. Algorithm shifts (Amazon changes matching)
5. External factors (economic recession, supply issues)

**Actions During Decline:**
- Reduce bids by 10-15%
- Pause bottom-performing keywords
- Evaluate product positioning (price, reviews, competitors)
- Consider pause/restart vs. continued operation
- Test new keywords and audiences

**Decision Points:**
- If ACOS >40% and declining further: Consider pausing
- If volume dropped 50%+: Restructure campaign
- If profitable even at low volume: Maintain baseline spend
- If seasonal decline expected: Plan for return

### 6.2 SEASONAL CAMPAIGN MANAGEMENT

**Seasonal Adjustment Timeline:**
```
October:
- Pre-season: Prepare 2-3 months advance
- Create seasonal variants of campaigns
- Expand keyword list (30-50% more keywords)
- Build budget reserves

November-December:
- Peak season: 2-4x normal budget
- Daily monitoring essential
- Aggressive bidding on proven keywords
- Accept higher ACOS (volume compensates)

January:
- Decline phase: Volume drops 30-50%
- Reduce bids by 20-30%
- Consolidate underperformers
- Plan for off-season

February-September:
- Off-season: Baseline operations
- Maintain 1-2 core campaigns
- Low budget (€100-300/month)
- Focus on optimization, not volume
```

**Example: Holiday Season Campaign**
```
Base Campaign (Year-Round):
├── Electronics_Headphones_Exact_Premium
├── Budget: €200/month
├── 5 keywords
├── Bid: €0.90 average
└── Conversions: 50/month

Holiday Expansion (November-December):
├── Electronics_Headphones_Exact_Premium_Holiday
├── Budget: €600/month (3x base)
├── 15 keywords (includes gift-related)
├── Bid: €1.20 average (+30%)
└── Expected Conversions: 200/month (4x base)

Holiday Keywords (Added):
- [holiday gifts] - Bid: €1.50
- [christmas presents] - Bid: €1.45
- [stocking stuffers] - Bid: €1.00
- [wireless gifts] - Bid: €1.35
- [tech gifts] - Bid: €1.25

Off-Season (February-September):
├── Electronics_Headphones_Exact_Premium_Baseline
├── Budget: €100/month (1/2 base)
├── 3 core keywords only
├── Bid: €0.75 average (-20%)
└── Conversions: 25/month (1/2 base)
```

**Expected ACOS During Seasons:**
- Peak Season: 30-40% ACOS acceptable (volume high)
- Normal Season: 20-30% ACOS target
- Off-Season: 15-25% ACOS (lower volume acceptable)

---

## SECTION 7: A/B TESTING & OPTIMIZATION FRAMEWORKS (250+ lines)

### 7.1 A/B TESTING CAMPAIGN STRUCTURE

**Testing Principle:** Only change ONE variable at a time. Changing multiple variables simultaneously makes results ambiguous.

**Variables Available for Testing:**
1. Bid amount (€0.80 vs €1.00)
2. Bid strategy (Manual vs Auto)
3. Match type (Exact vs Phrase)
4. Product images/creative
5. Campaign name structure
6. Negative keyword set
7. Time scheduling
8. Geographic targeting

### 7.2 BID STRATEGY A/B TEST

**Test Structure:**
```
Campaign A (Control): Electronics_Headphones_Exact_Premium_Manual
├── Bid Strategy: Manual
├── Average Bid: €0.95
├── Budget: €500/month
├── Duration: 30 days
└── Keyword: [wireless headphones]

Campaign B (Test): Electronics_Headphones_Exact_Premium_Auto
├── Bid Strategy: Automatic (Target ACOS: 25%)
├── Initial Bid: €0.95
├── Budget: €500/month
├── Duration: 30 days
└── Keyword: [wireless headphones] (same)

Key: IDENTICAL except bid strategy
```

**Data Collection (30 days):**
| Metric | Manual | Auto | Winner |
|--------|--------|------|---------|
| Spend | €500 | €500 | Tie |
| Conversions | 45 | 48 | Auto |
| ACOS | 22.2% | 20.8% | Auto |
| CPC | €0.88 | €0.92 | Manual |
| CTR | 2.8% | 3.1% | Auto |
| Conv Rate | 9.2% | 10.1% | Auto |

**Result:** Auto bid strategy outperformed manual on ACOS and conversion rate.

**Action:**
1. Pause manual campaign
2. Increase budget on auto campaign to €700/month
3. Expand auto strategy to other keywords
4. Document finding in test results log

### 7.3 MATCH TYPE A/B TEST

**Test Structure:**
```
Campaign A (Control): Electronics_Headphones_Exact_Premium
├── Match Type: Exact
├── Keywords: [wireless headphones], [bluetooth headphones], [noise cancelling]
├── Budget: €400/month
├── Duration: 30 days
└── Target: [exact variations only]

Campaign B (Test): Electronics_Headphones_Phrase_Premium
├── Match Type: Phrase
├── Keywords: "wireless headphones", "bluetooth headphones", "noise cancelling"
├── Budget: €400/month
├── Duration: 30 days
└── Target: [variations + related phrases]

Key: IDENTICAL budget, similar keywords, different match type
```

**Data Collection (30 days):**
| Metric | Exact | Phrase | Winner |
|--------|--------|--------|----------|
| Spend | €400 | €400 | Tie |
| Conversions | 35 | 52 | Phrase |
| ACOS | 22.8% | 18.3% | Phrase |
| Clicks | 180 | 420 | Phrase |
| CPC | €2.20 | €0.95 | Phrase |
| Conv Rate | 19.4% | 12.4% | Exact |

**Analysis:**
- Phrase match gets 2.3x more clicks (good for volume)
- Phrase match has 1.5x more conversions (good for ROI)
- Phrase match has 20% lower ACOS (20% cost savings)
- Exact match has higher conversion rate (but fewer opportunities)

**Conclusion:** Phrase match delivers better overall value for this keyword group.

**Action:**
1. Increase phrase match budget to €600/month
2. Reduce exact match budget to €200/month
3. Use exact match for brand defense only
4. Monitor phrase conversion rate for quality

### 7.4 NEGATIVE KEYWORD A/B TEST

**Test Structure:**
```
Campaign A (Control): Fashion_Shoes_Phrase_Mid_Standard
├── Negative Keywords: Category-wide set (30 negatives)
│   [-review] [-cheap] [-rental] [-repair] [-fake]
├── Budget: €500/month
├── Duration: 30 days
└── Phrase keywords: [running shoes], [dress shoes], [walking shoes]

Campaign B (Test): Fashion_Shoes_Phrase_Mid_Aggressive
├── Negative Keywords: Category-wide + competitor brands (50+ negatives)
│   [-review] [-cheap] [-rental] [-repair] [-fake]
│   [-nike] [-adidas] [-asics] [-new balance] [-reebok]
│   [-children] [-kids] [-baby] [-infant]
├── Budget: €500/month
├── Duration: 30 days
└── Phrase keywords: [running shoes], [dress shoes], [walking shoes]

Key: IDENTICAL except negative keyword count
```

**Data Collection (30 days):**
| Metric | Standard | Aggressive | Winner |
|--------|----------|-----------|--------|
| Spend | €500 | €475 | Aggressive |
| Impressions | 8,500 | 7,200 | Standard |
| Clicks | 320 | 240 | Standard |
| Conversions | 28 | 24 | Standard |
| ACOS | 17.9% | 19.8% | Standard |
| CPC | €1.56 | €1.98 | Standard |

**Analysis:**
- Aggressive negatives reduce clicks by 25% (fewer irrelevant clicks)
- Aggressive negatives reduce conversions by 14% (losing some good traffic)
- Standard negative set has 18% lower ACOS
- Standard negative set is more cost-effective

**Conclusion:** Adding competitor negatives reduces volume without commensurate ACOS improvement. Standard negative set is better for this market.

**Action:**
1. Use standard negative set (30 negatives)
2. Skip competitor brand negatives
3. Keep cost-per-conversion low by accepting broader traffic

### 7.5 CREATIVE/PRODUCT IMAGE A/B TEST

**Test Structure:**
```
Campaign A (Control): Electronics_Headphones_Exact_Premium_Image1
├── Primary Product Image: Hero shot (clean background)
├── Ad Group Image: Main image with logo
├── Keywords: [wireless headphones], [noise cancelling headphones]
├── Budget: €400/month
└── Duration: 30 days

Campaign B (Test): Electronics_Headphones_Exact_Premium_Image2
├── Primary Product Image: Lifestyle photo (in-use scenario)
├── Ad Group Image: Lifestyle image with person using product
├── Keywords: [wireless headphones], [noise cancelling headphones] (same)
├── Budget: €400/month
└── Duration: 30 days

Key: IDENTICAL keywords, different creative
```

**Data Collection (30 days):**
| Metric | Hero Shot | Lifestyle | Winner |
|--------|----------|-----------|--------|
| Spend | €400 | €400 | Tie |
| Impressions | 2,100 | 2,850 | Lifestyle |
| Clicks | 95 | 157 | Lifestyle |
| CTR | 4.5% | 5.5% | Lifestyle |
| Conversions | 8 | 13 | Lifestyle |
| Conv Rate | 8.4% | 8.3% | Tie |
| ACOS | 50% | 30.8% | Lifestyle |

**Analysis:**
- Lifestyle image gets 35% more impressions (better visibility)
- Lifestyle image gets 65% more clicks (higher CTR)
- Lifestyle image converts at similar rate (both ~8%)
- Lifestyle image generates 38% better ACOS (20% cheaper)

**Conclusion:** Lifestyle creative outperforms hero shot significantly.

**Action:**
1. Use lifestyle images as primary creative
2. Update all hero shot campaigns with lifestyle alternatives
3. Test lifestyle variants (different scenarios, different people)

### 7.6 TESTING BEST PRACTICES

**Sample Size Requirements:**
- Minimum conversions per variant: 50 (for reliable data)
- Minimum duration: 14 days (captures weekly patterns)
- Minimum budget per variant: €300 (enough volume)
- Confidence level: 95% (standard for business decisions)

**Common Testing Mistakes:**
1. Ending test too early (before 50 conversions)
2. Changing variables mid-test (introduces confounds)
3. Testing during seasonal shifts (masks true effect)
4. Testing without budget parity (unfair comparison)
5. Overfitting to single test (ignoring other markets)

**Testing Prioritization:**
```
High Priority (Test First):
1. Bid strategy (Manual vs Auto) - 15-30% ACOS impact
2. Match type (Exact vs Phrase) - 20-40% volume impact
3. Negative keywords - 10-25% ACOS impact

Medium Priority (Test If Budget Available):
4. Creative/images - 5-20% CTR impact
5. Keyword variations - 10-30% volume impact

Low Priority (Test Only If Optimized):
6. Time scheduling - 3-8% ROI impact
7. Geographic targeting - 2-5% ACOS impact
```

---

## SECTION 8: MARKETPLACE-SPECIFIC IMPLEMENTATION (200+ lines)

### 8.1 AMAZON EU SPECIFIC TACTICS

Amazon EU (UK, DE, FR, IT, ES) has specific features and constraints:

#### 8.1.1 Multi-Country Campaign Strategy

**Approach 1: Unified EU Campaign**
```
Campaign: Electronics_Headphones_Exact_Premium_EU_All
├── Target Markets: UK, Germany, France, Italy, Spain
├── Language: English keywords (translated per country)
├── Budget: €1,000/month across all
├── Keywords: [wireless headphones] etc. (auto-translated)
└── Advantage: Single campaign to manage
   Disadvantage: Can't optimize per country
```

**Approach 2: Country-Specific Campaigns (RECOMMENDED)**
```
Campaign: Electronics_Headphones_Exact_Premium_UK
├── Target: UK only
├── Language: English (UK spelling)
├── Budget: €250/month
├── Keywords: [wireless headphones]
└── ACOS Target: 20%

Campaign: Electronics_Headphones_Exact_Premium_DE
├── Target: Germany only
├── Language: German
├── Budget: €400/month (larger market)
├── Keywords: [kabellose Kopfhörer] (German keyword)
└── ACOS Target: 22% (slightly different)

Campaign: Electronics_Headphones_Exact_Premium_FR
├── Target: France only
├── Language: French
├── Budget: €250/month
├── Keywords: [casques sans fil] (French keyword)
└── ACOS Target: 25% (lower conversion market)

[Similar for IT and ES...]

Total: 5 campaigns, €1,200/month
```

**Rationale for Country-Specific:**
- Conversion rates vary 30-40% between countries
- Search intent differs ("sale" vs "promotion" terms)
- CPC varies significantly (DE highest, ES lowest typically)
- Allows country-level optimization

#### 8.1.2 EU-Specific Keyword Adaptations

**UK Keywords:**
- Use British spelling: "colour", "favourite", "centre"
- Incorporate UK brands: "John Lewis", "Currys", "Amazon Fresh"
- Include "UK delivery", "Next day delivery" variations

**German Keywords:**
- Longer compound keywords: "hochwertige kabellose Kopfhörer"
- Quality indicators: "Premium", "Markenprodukt"
- Price terms: "unter€100", "günstig"

**French Keywords:**
- Formal language preferred: "écouteurs sans fil" vs "casques bluetooth"
- Luxury positioning: "haut de gamme", "prestige"
- Regional terms: "Livraison rapide", "Service client"

### 8.2 KAUFLAND SPECIFIC TACTICS

Kaufland (Germany, Czech Republic, Romania, Slovakia) has different algorithm:

#### 8.2.1 Kaufland Campaign Structure

**Key Differences from Amazon:**
- Keyword matching is broader (no exact match equivalent)
- Quality Score heavily weighted (review rating critical)
- CTR less important than conversion rate
- Daily budget management more volatile

**Recommended Structure:**
```
Campaign: Kaufland_Headphones_Standard
├── Keywords: Mix of 20-30 related terms
│   (Kaufland treats these as "medium intent")
├── Budget: Daily €10-15
├── Bid Strategy: Manual (Auto unreliable on Kaufland)
├── Focus: High-quality product (reviews essential)
└── Conversion Rate Target: 3-5%
```

#### 8.2.2 Kaufland Optimization Tips

1. **Product Reviews Critical:**
   - Don't advertise products with <4.0 average rating
   - Review count >30 essential (builds trust)
   - Negative reviews kill CTR

2. **Pricing Strategy:**
   - Kaufland customers price-sensitive
   - Competitive pricing essential (1-2% of Amazon)  
   - Bundle deals work well (can add 15-20% volume)

3. **Keyword Strategy:**
   - Broader keyword approach (50+ keywords per campaign)
   - Negative keywords less effective (system ignores some)
   - Test variations heavily

### 8.3 BOL.COM SPECIFIC TACTICS

Bol.com (Netherlands, Belgium) has marketplace-specific features:

#### 8.3.1 Bol.com Campaign Structure

**Key Differences:**
- Sponsored Products only (no brand campaigns)
- ACOS calculation includes VAT (20% in NL, 21% in BE)
- Attribution window only 14 days (vs 30 on Amazon)
- Algorithm heavily rewards shipping speed

**Recommended Structure:**
```
Campaign: Bolcom_Headphones_Standard
├── Keywords: 10-15 exact/phrase combinations
├── Budget: Daily €5-10 (smaller market)
├── Focus: Same-day shipping if possible
├── ACOS Target: 25% (adjusted for VAT)
└── Fulfillment: Fulfilment by Bol preferred
```

#### 8.3.2 Bol.com Specifics

1. **Fulfilment Strategy:**
   - FBB (Fulfilment by Bol) gets bid boost
   - Same-day option shows in search
   - Impacts CTR by 15-20%

2. **Pricing (Include VAT in ACOS Calculation):**
   - Netherlands: 21% VAT
   - Belgium: 21% VAT
   - When calculating ACOS: ACOS = (Ad Spend / (Sales × 0.79))
   - This adjusts for VAT removed from sales

3. **Shipping Terms:**
   - Express shipping (1-2 day) highly valued
   - Standard shipping (3-5 day) much lower CTR
   - International shipping limited impact

### 8.4 EBAY SPECIFIC TACTICS

eBay (global, varies by region) has different paid search platform (Promoted Listings):

#### 8.4.1 eBay Promoted Listings Strategy

**Key Differences:**
- Auction vs. Fixed Price (different strategy)
- Campaign level only (no keyword level)
- Percentage-based bidding (e.g., "1.5% of sale price")
- No negative keywords (system ignores)

**Recommended Structure:**
```
Campaign: eBay_Headphones_Electronics_Fixed
├── Listings: 20-50 fixed-price listings
├── Bid Rate: 1.5-2.5% of sale price
├── Budget: Daily €5-15
├── Focus: New and Trending category
└── ACOS Target: 8-12% (percentage-based)
```

#### 8.4.2 eBay Auction vs Fixed Price

**Auctions:**
- Bid lower (0.5-1%) - buyers already engaged
- Shorter campaign windows (7-10 days before auction ends)
- Higher competition near end-date

**Fixed Price:**
- Bid higher (1.5-3%) - need buyer attention
- Longer windows (campaigns run 30-90 days)
- More predictable volume

---

## SECTION 9: IMPLEMENTATION CHECKLIST & QUICK REFERENCE (150+ lines)

### 9.1 CAMPAIGN SETUP CHECKLIST

**Pre-Launch (Week -1):**
- [ ] Define target ACOS (profit margin / ideal ROI)
- [ ] Gather keyword list (start with 5-20 keywords)
- [ ] Collect negative keywords (start with category-wide list)
- [ ] Determine daily budget (ACOS target × expected conversions)
- [ ] Create naming convention (apply to all campaigns)
- [ ] Review product listing quality (title, images, description)
- [ ] Verify product review count (minimum 10 reviews)

**Campaign Creation (Week 0):**
- [ ] Create campaign with proper name format
- [ ] Set match type (select one: Exact/Phrase/Broad/Auto)
- [ ] Add keywords (start with 5-10 related keywords)
- [ ] Set initial bids (€0.50-1.00 depending on category)
- [ ] Apply category-wide negative keywords
- [ ] Set daily budget
- [ ] Enable automatic bid adjustment (if using Auto)
- [ ] Review all settings before launch

**Post-Launch Monitoring (Weeks 1-4):**
- [ ] Day 1: Check campaign is receiving impressions
- [ ] Day 3: Verify at least 10 clicks received
- [ ] Day 7: Review search term report for negatives
- [ ] Day 14: Collect first conversion data (target: 5-10)
- [ ] Day 21: Analyze ACOS vs. target
- [ ] Day 28: Make first bid adjustments based on data

### 9.2 QUICK REFERENCE: MATCH TYPES BY USE CASE

**Use EXACT MATCH if:**
- ✓ Budget limited (<€500/month)
- ✓ Keyword has proven conversion history
- ✓ High-value products (>€100)
- ✓ Brand keywords
- ✓ Want maximum ACOS control
- ✗ Don't use if you want high volume

**Use PHRASE MATCH if:**
- ✓ Budget moderate (€500-2000/month)
- ✓ Want to balance volume and ACOS
- ✓ Testing keyword variations
- ✓ Building awareness phase
- ✗ Don't use if ACOS tolerance is <15%

**Use BROAD MATCH if:**
- ✓ Budget high (>€1500/month)
- ✓ Accept higher ACOS (>35%) for volume
- ✓ Product low-cost (<€50)
- ✓ Discovery phase
- ✗ Never use without 50+ negative keywords

**Use AUTO CAMPAIGNS if:**
- ✓ Product new (need keyword discovery)
- ✓ Category unfamiliar (don't know search terms)
- ✓ Want to harvest keywords for manual campaigns
- ✓ Budget limited (auto more efficient initially)
- ✗ Don't use as primary profit driver

### 9.3 QUICK REFERENCE: ACOS TARGETS BY CATEGORY

**Premium Electronics (COGS €100+, Margin 40%+):**
- Exact Match Target: 15-20% ACOS
- Phrase Target: 25-30% ACOS  
- Broad Target: 35-45% ACOS
- Auto Target: 30-40% ACOS

**Mid-Tier Products (COGS €30-100, Margin 25-40%):**
- Exact Match Target: 20-25% ACOS
- Phrase Target: 30-40% ACOS
- Broad Target: 40-50% ACOS
- Auto Target: 35-45% ACOS

**Budget Products (COGS <€30, Margin <25%):**
- Exact Match Target: 25-35% ACOS
- Phrase Target: 35-45% ACOS
- Broad Target: 50-70% ACOS
- Auto Target: 45-60% ACOS

**Notes:**
- Add 5-10% to targets for new/unproven keywords
- Reduce targets by 5% for proven high-performers
- Seasonal peaks allow 10-15% higher ACOS
- Off-season requires 10% lower ACOS

### 9.4 CAMPAIGN OPTIMIZATION CHECKLIST (MONTHLY)

**Week 1 of Month:**
- [ ] Review monthly ACOS vs. target
- [ ] Identify top 3 converting keywords
- [ ] Identify bottom 3 performing keywords
- [ ] Analyze search term report
- [ ] Add 5-10 new negative keywords

**Week 2 of Month:**
- [ ] Increase bids on top performers by €0.10-0.20
- [ ] Reduce bids on underperformers by €0.05-0.15
- [ ] Pause keywords with 0 conversions after 100+ clicks
- [ ] Review budget allocation vs. performance
- [ ] Check for new competitor keywords

**Week 3 of Month:**
- [ ] Analyze conversion rate trends
- [ ] Test new keyword variations (5-10 new)
- [ ] Review negative keyword effectiveness
- [ ] Plan next month's budget based on ROI
- [ ] Document wins/losses in testing log

**Week 4 of Month:**
- [ ] Prepare monthly report (ACOS, ROI, volume)
- [ ] Schedule campaigns for next month
- [ ] Plan seasonal adjustments if needed
- [ ] Brief team on key findings
- [ ] Set targets for next month

### 9.5 TROUBLESHOOTING COMMON ISSUES

**Problem: High ACOS (>50%)**
- Check: Are bids too high? Reduce by €0.15-0.25
- Check: Wrong match type? Test exact match instead
- Check: Poor product quality? Improve reviews/images
- Check: Weak negative keywords? Add 20+ more
- Check: Seasonal decline? Reduce budget or pause

**Problem: Low conversion rate (<2%)**
- Check: Product image quality? Use lifestyle photos
- Check: Product title clarity? Add relevant keywords to title
- Check: Product reviews? Need minimum 20 positive
- Check: Price competitive? Check competitor pricing
- Check: Wrong keywords? Analyze search intent match

**Problem: Very high CPC (>€2.00)**
- Check: Competitive category? Use phrase/broad match
- Check: Branded keywords? Expect higher CPC
- Check: Peak season? Reduce bid, accept lower volume
- Check: Bid too aggressive? Reduce by €0.25-0.50
- Check: Poor CTR? Improve ad appeal

**Problem: No conversions (30+ days)**
- Check: Keyword intent match? Review search terms
- Check: Product new? Seed reviews (10-20 initial)
- Check: Wrong category? Verify product categorization
- Check: Budget too low? Increase to €15-20/day
- Check: Seasonal? Check category trends

---

## QUICK REFERENCE TABLES

### Campaign Types Summary

| Type | Keywords | Campaigns Needed | ACOS | Volume | Effort |
|------|----------|------------------|------|--------|--------|
| Exact Match | 1-5 | 20-100 | 15-25% | Low | High |
| Phrase Match | 5-15 | 7-20 | 25-40% | Medium | Medium |
| Broad Match | 5-10 | 2-10 | 35-60% | High | Low |
| SKAG | 1 | 100+ | 18-28% | Low | Very High |
| Auto (Close) | N/A | 1 | 20-35% | Medium | Low |
| Auto (Loose) | N/A | 1 | 30-50% | High | Low |

### Performance Benchmarks (Electronics)

| Metric | Excellent | Good | Acceptable | Poor |
|--------|-----------|------|-----------|------|
| ACOS | <15% | 15-25% | 25-35% | >35% |
| CTR | >5% | 3-5% | 2-3% | <2% |
| Conv Rate | >10% | 5-10% | 3-5% | <3% |
| CPC | <€0.80 | €0.80-1.20 | €1.20-1.50 | >€1.50 |
| ROI | >5:1 | 3:1-5:1 | 2:1-3:1 | <2:1 |

### Profit Calculation Reference

**Formula:**
```
Profit = (Sales × Profit Margin) - Ad Spend
ROI = (Sales × Profit Margin) / Ad Spend
Adjusted ACOS = Ad Spend / (Sales - Returns)
```

**Example:**
- Product Cost: €100
- Selling Price: €250  
- Profit Margin: (€250-€100)/€250 = 60%
- Ad Spend: €500
- Sales from Ads: €3,000
- Profit from Ads: (€3,000 × 0.60) - €500 = €1,300
- ROI: €1,300 / €500 = 2.6:1
- ACOS: €500 / €3,000 = 16.7%

---

**END OF DOCUMENT**

*This guide represents 2,429 lines of definitive guidance on PPC campaign structure and optimization for marketplace distribution across Amazon EU, Kaufland, Bol.com, and eBay.*

*Implement these frameworks systematically, test variations, document results, and adapt to your specific market conditions. Success comes from consistent application of proven architecture principles, not from random keyword additions or bid tweaking.*

*Last Updated: April 2, 2026*
*Next Review: October 2, 2026 (6-month cycle)*