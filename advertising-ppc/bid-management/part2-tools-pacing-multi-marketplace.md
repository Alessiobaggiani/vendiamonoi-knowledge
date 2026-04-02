# SECTION 4: AUTOMATED BIDDING TOOLS (240 lines)

## 4.1 Amazon Advertising Console Built-In Rules

Amazon provides native **Bidding Rules** and **Budget Rules** within Seller Central.

### Bidding Rules Features

```
Rule Types Available:
├─ Schedule-based (time of day, day of week)
├─ Performance-based (ACoS, ROAS, conversion rate, CTR)
└─ Bid limit rules (cap max bid at specific amount)

Rule Creation Process:
1. Navigate: Campaigns → Select Campaign → Rules
2. Create New Rule
3. Condition: "When [metric] [operator] [threshold]"
4. Action: "Then [increase/decrease] bid by [%]"
5. Frequency: Daily evaluation (12:00 UTC)
6. Save and activate

Example Rule Setup:
Rule Name: Reduce High-ACoS Keywords
Condition: ACoS > 40%
Action: Reduce bid by 20%
Frequency: Daily
Applies to: All keywords in campaign

Limitations:
├─ Sponsored Products ONLY (not SB, SD, DSP)
├─ Cannot decrease bids below €0.02 minimum
├─ Cannot decrease bids at all without campaign segmentation
├─ No complex boolean logic (no AND/OR combinations)
├─ Limited condition metrics (no device type, no competitor data)
└─ Manual setup required per campaign (no bulk configuration)
```

### Budget Rules Features

```
Budget Rules allow automatic daily budget adjustments:

Rule Types:
├─ Increase budget when performance threshold met
├─ Decrease budget when performance threshold exceeded
└─ Set fixed daily budget for specific periods

Example Budget Rule:
Rule Name: Scale winning campaigns
Condition: ROAS > 4.0x AND Cost > €500/day
Action: Increase daily budget by 25%
Frequency: Daily evaluation
Maximum increase per day: 25%
Maximum total: €5,000/day (configurable)

Note: Boolean logic (AND) works for budget rules but NOT bid rules
```

## 4.2 Third-Party Automation Tools

### Perpetua

**Overview**: AI-driven bid management for Amazon Advertising

```
Pricing:
├─ Essential: €250/month + 0.5% of ad spend
├─ Professional: €400/month + 0.75% of ad spend
├─ Enterprise: €550/month + 1% of ad spend (€10K+ spend)

Features:
├─ AI keyword discovery and bid optimization
├─ Campaign creation automation
├─ ROAS-based bidding with ML prediction
├─ Budget allocation across campaigns
├─ Competitor intelligence and pricing analysis
├─ Integration: Amazon Ads API (native connection)
├─ Dashboards: Campaign performance, spend forecasts
└─ Reporting: Weekly email summaries, custom reports

Bid Optimization Methodology:
1. Historical data analysis (180+ days of campaign data)
2. Keyword profitability scoring (0-100 scale)
3. Algorithmic bid adjustment (updates every 4 hours)
4. A/B testing framework for bid changes
5. Anomaly detection (alerts on unusual patterns)

Results (user testimonials):
├─ ACoS reduction: 15-25%
├─ ROAS improvement: 20-35%
├─ Time saved: 8-12 hours per week
└─ Typical ROI: 3-4x on tool cost within 3 months
```

### Pacvue

**Overview**: Multi-channel marketplace management platform

```
Pricing:
├─ Growth: €1,000/month (up to €50K ad spend)
├─ Professional: €2,500/month (up to €500K ad spend)
├─ Enterprise: Custom (€500K+ spend)

Features:
├─ Marketplace management (Amazon, eBay, Walmart, TikTok Shop)
├─ Dayparting hourly adjustments (granular time-of-day bidding)
├─ Automated rules engine (complex boolean logic)
├─ Portfolio management (allocate budgets across 100+ campaigns)
├─ Organic rank correlation tracking
├─ Profitability dashboards (ROAS, ACoS, profit margin)
├─ Competitor pricing monitoring
├─ Search term analysis and negative keyword automation
└─ API access for custom integrations

Dayparting Capability:
- Adjust bids hourly (24 different time windows)
- Example: €1.00 bid → 6 AM €0.60, 12 PM €1.40, 8 PM €1.35, 2 AM €0.40
- Automation: Rules trigger bid changes at specified hours
- Reporting: Performance by hour-of-day heatmaps

User Results:
├─ ACoS optimization: 10-20% improvement
├─ Budget efficiency: 15-25% cost reduction
├─ Time savings: 6-10 hours per week
└─ Platform adoption: 2-3 months to mastery
```

### Teikametrics Flywheel

**Overview**: Real-time advertising optimization engine

```
Pricing:
├─ Starter: €600/month (up to €50K ad spend)
├─ Growth: €1,200/month (up to €300K spend)
├─ Professional: €2,000/month (up to €1M spend)
├─ Enterprise: Custom pricing

Features:
├─ Real-time bid adjustments (updates every 4 hours)
├─ Machine learning algorithm (learns from 90-day rolling history)
├─ Hourly bidding adjustments (time-of-day optimization)
├─ Keyword health scoring (0-100 scale)
├─ Campaign pause recommendations (unprofitable campaigns)
├─ Negative keyword suggestions (weekly updates)
├─ Multi-marketplace optimization (Amazon, eBay, Walmart)
├─ ROAS forecasting (predicts profitability 2 weeks ahead)
└─ Integration: API, CSV uploads, email reports

Key Differentiator - Hourly Bidding:
- Amazon's native rules: Daily evaluation (12:00 UTC once per day)
- Teikametrics: Up to 4-6 bid adjustments per day
- Result: 5-12% improvement vs. daily-only optimization
- Example: Catches 2 PM conversion spike, adjusts bid in real-time

User Results:
├─ ACoS improvement: 18-30%
├─ ROAS increase: 25-40%
├─ Operational time: Saves 8-15 hours per week
└─ Payback period: 6-8 weeks typical
```

### Quartile

**Overview**: Enterprise-grade Amazon Advertising optimization

```
Pricing (Tiered by Spend):
├─ Tier 1: €895/month (€10K-50K ad spend)
├─ Tier 2: €1,995/month (€50K-200K ad spend)
├─ Tier 3: €3,995/month (€200K-500K ad spend)
├─ Tier 4: €6,995/month (€500K-1M ad spend)
├─ Tier 5: €9,995/month (€1M+ ad spend)

Features:
├─ AI-powered bid optimization
├─ Device-level bidding (phone, tablet, desktop)
├─ Placement-level bid adjustments (TOS, ROS, Product Pages)
├─ Dynamic budget allocation
├─ Search term insights and negative keyword automation
├─ Competitive intelligence (price/placement monitoring)
├─ ROAS optimization with daily adjustments
├─ Multi-brand portfolio management
├─ Dedicated account manager (Tier 3+)
└─ White-glove onboarding and training

Device-Level Bidding Example:
```
Keyword: "wireless headphones"
Base bid: €1.00
Device adjustments:
├─ Desktop: €1.00 (baseline, 45% of impressions)
├─ Mobile: €0.70 (20% lower, 40% of impressions, lower conv rate)
└─ Tablet: €1.15 (15% higher, 15% of impressions, strong converters)

Weighted average: (€1.00 × 0.45) + (€0.70 × 0.40) + (€1.15 × 0.15)
               = €0.45 + €0.28 + €0.17
               = €0.90 average

Benefit: Optimize by device without platform constraints
```

User Results:
├─ ACoS reduction: 20-35%
├─ ROAS improvement: 30-50%
├─ Additional conversions: 15-25%
├─ Time savings: 10-20 hours per week
└─ Typical payback: 4-6 weeks
```

### Helium 10 Adtomic

**Overview**: Integrated advertising suite within Helium 10 ecosystem

```
Pricing:
├─ Starter: €39/month (basic features only)
├─ Accelerator: €99/month (campaign optimization)
├─ Elite: €199/month (advanced automation + 2% on ad spend >€10K)
├─ Platinum: Custom (enterprise features)

Features:
├─ Bid optimization (keyword-level auto-bidding)
├─ Campaign automation (rules engine for bid/budget changes)
├─ Keyword research integration (Cerebro tool connection)
├─ Negative keyword suggestions (from Search Term Reports)
├─ Profitability dashboards (ROAS, ACoS by keyword)
├─ Campaign cloning (duplicate winning campaigns)
├─ Competitor keyword tracking
├─ Integration: Amazon Ads API, email alerts
└─ Learning resources (guides, webinars, community)

Key Feature - Cerebro Integration:
- Link to Helium 10 keyword research data
- Add keywords directly to campaigns from research
- Bid recommendations based on historical performance
- Search volume and difficulty metrics imported

User Results:
├─ Time savings: 5-8 hours per week
├─ ACoS improvement: 10-15% (less aggressive than specialized tools)
├─ Ease of use: Beginner-friendly
└─ Best for: Sellers using other Helium 10 tools
```

## 4.3 Platform Comparison Matrix

```
Tool              Cost        Auto-Bidding   Device    Daypart.  Marketplaces  Ease
─────────────────────────────────────────────────────────────────────────────────
Amazon Console    Free        Limited        No        Daily     Amazon only   Easy
Perpetuaai        €250-550    Full           No        No        Amazon        Hard
Pacvue            €1-2.5K     Full           No        Hourly    Multi (5)     Hard
Teikametrics      €600-2K     Full           No        Hourly    Multi (3)     Medium
Quartile          €895-9.9K   Full           Yes       Daily     Amazon        Hard
Helium 10         €39-199     Partial        No        No        Amazon        Easy

Recommendations:
├─ Budget <€500: Use Amazon Console Rules + manual optimization
├─ Budget €500-5K: Perpetua (best AI) or Helium 10 (best UX)
├─ Budget €5K-50K: Teikametrics (hourly bidding) or Pacvue (multi-channel)
├─ Budget €50K+: Quartile (device-level) or Pacvue (enterprise)
└─ Enterprise: Custom solutions (Quartile Tier 5, custom APIs)
```

---

# SECTION 5: BUDGET PACING & DAYPARTING (210 lines)

## 5.1 Budget Exhaustion Analysis

Understanding how **daily budget limits impact impressions**.

### Peak Window Analysis

```
Scenario: Campaign with €500 daily budget
Campaign performance (Amazon.de, typical category)

Hour        Impressions   % of daily   Cumulative %   Budget consumed
─────────────────────────────────────────────────────────────────────
6 AM        320           4.5%         4.5%           €22.50
7 AM        450           6.3%         10.8%          €31.50
8 AM        580           8.1%         18.9%          €40.70
9 AM        680           9.5%         28.4%          €47.50
10 AM       750           10.5%        38.9%          €52.50
11 AM       810           11.3%        50.2%          €56.50
12 PM       920           12.8%        63.0%          €64.40  <-- EXHAUSTION BEGINS
1 PM        890           12.4%        75.4%          €62.30
2 PM        720           10.0%        85.4%          €50.40
3 PM        580           8.1%         93.5%          €40.70
4 PM        450           6.3%         99.8%          €31.50
After 4 PM  ~15           0.2%         100%           €1.05   <-- BUDGET EXHAUSTED
Evening     0             0%           100%           €0      <-- NO IMPRESSIONS

Daily total: 7,155 impressions
Peak exhaustion: 12 PM, 63% of daily budget used
Cost from 6 AM-12 PM: €315
Cost from 12 PM-end of day: €185
Impression ratio: 3,500 (morning) vs. 3,655 (afternoon/evening, but budget-limited)
Budget limit effect: Missing ~40% of evening impressions due to budget exhaustion
```

### Cost Impact from Budget Limitation

```
Without daily budget limit (hypothetical):
├─ Total potential impressions: 11,200
├─ Total spend: €785
├─ Average CPC: €0.07
└─ Daily conversions (2% rate): 224

With €500 daily budget limit:
├─ Actual impressions: 7,155 (36% fewer impressions)
├─ Spend: €500 (exactly budget)
├─ Average CPC: €0.07 (same)
└─ Daily conversions (2% rate): 143 (36% fewer conversions)

Improvement opportunity:
├─ Increase budget to €750/day: +€250/day = +€7,500/month
├─ Additional impressions gained: ~3,000/day
├─ Additional conversions: ~60/day at 2% CR
├─ Additional revenue: 60 × €50 AOV = €3,000/day = €90K/month
└─ Cost to capture: €750 additional spend/month
```

## 5.2 Weekend vs. Weekday Optimization

Performance variation between weekdays and weekends.

```
Weekday Performance (Monday-Friday):
├─ Average CTR: 1.2%
├─ Average conversion rate: 2.0%
├─ Average CPC: €0.68
├─ Peak hours: 12-1 PM (130% index), 7-9 PM (120% index)
└─ Recommended bid: Base bid (€1.00)

Weekend Performance (Saturday-Sunday):
├─ Average CTR: 1.4% (+16.7% vs. weekday)
├─ Average conversion rate: 2.3% (+15% vs. weekday)
├─ Average CPC: €0.72 (+5.9% vs. weekday)
├─ Peak hours: 10 AM-12 PM (140% index), 7-10 PM (135% index)
└─ Recommended bid: €1.18-1.25 (+18-25% adjustment)

Reasoning:
├─ Weekend shoppers: More leisure time, higher engagement
├─ Fewer competitors bidding: Potentially lower actual CPC despite higher bid
├─ Browsing behavior: More research-oriented, higher conversion likelihood
└─ Cart abandonment: Higher weekend cart recovery rates

Monthly Impact (€10,000 monthly budget):
Weekday spend (71% of month): €7,100 at 2.0% CR = 142 conversions
Weekend spend (29% of month): €2,900 at base bid = 87 conversions
Total: 229 conversions at €43.67 cost per conversion

With weekend optimization (+20% bid adjustment):
Weekday spend: €7,100 = 142 conversions
Weekend spend: €2,900 @ +20% cost = ~€3,480 spend, 120 conversions
Total additional investment: €580/month
Additional conversions: 33/month = 14% improvement
Return: 33 conversions × €15 profit = €495 additional profit
NET: -€85 loss (not recommended unless high-margin products)

Alternative: Increase weekend budget instead of bids
Weekend spend increase to €3,100: +€200/month
Additional impressions: +3,000
Additional conversions: 23 (at +15% conversion premium)
Additional profit: 23 × €15 = €345
NET: +€145 profit (recommended approach)
```

## 5.3 Holiday and Event-Based Adjustments

```
Major European Holidays and Shopping Events:

Black Friday (4th Friday of November):
├─ Start: Thursday before (early access)
├─ Peak: Friday 6 AM through Sunday midnight
├─ Search volume increase: +80-100%
├─ Average CPC increase: +50-80%
├─ Conversion rate increase: +40-80% (highly seasonal products)
├─ Budget adjustment: +50-80%
├─ Recommended bid adjustment: +60-100% above baseline

Example: Product selling €35 AOV, 45% margin, 2.5% baseline CR
Baseline daily budget: €200 (€6,000 monthly)
Black Friday week budget: €280 (€60K weekly with Friday peak)

Cyber Monday (Monday after Black Friday):
├─ Secondary peak to Black Friday
├─ Search volume: +60-70%
├─ CPC increase: +35-50%
├─ Budget adjustment: +40-60%

Christmas Season (December 1-24):
├─ Extended shopping window (6 weeks)
├─ Search volume: +25-50% (varies by product category)
├─ CPC increase: +20-40%
├─ Budget adjustment: +25-50%
├─ Day of delivery cutoff (Dec 15-20): +300% spike the week before

Mother's Day (2nd Sunday of May in most EU countries):
├─ High-converting event for gifts, beauty, jewelry
├─ Peak: 3 weeks before holiday (May 1-12)
├─ Search volume increase: +45-60%
├─ CPC increase: +25-35%
├─ Budget adjustment: +35-50%

Easter (Variable, late March-late April):
├─ Retail period: 6 weeks before through Easter Sunday
├─ Search volume increase: +30-40%
├─ CPC increase: +15-25%
├─ Budget adjustment: +25-35%
└─ Note: Varies year-to-year due to date variability

Annual Budget Plan with Holiday Adjustments:
├─ Base monthly budget: €5,000
├─ January (post-holiday): -20% = €4,000
├─ February: Normal = €5,000
├─ March/Easter: +30% = €6,500 (if early Easter)
├─ April/May (Mother's Day): +35% = €6,750
├─ June-September: Normal = €5,000 each
├─ October: +10% = €5,500 (prep for November)
├─ November (Black Friday): +80% = €9,000
├─ December (Christmas): +50% = €7,500
Annual total: €69,250 (vs. €60,000 flat, +15.4% uplift from smart allocation)
```

---

# SECTION 6: MULTI-MARKETPLACE BUDGET OPTIMIZATION (200 lines)

## 6.1 European Marketplace Share Analysis

```
Marketplace       Market Share   Typical Category   Avg AOV
──────────────────────────────────────────────────────────
Amazon.de         28%            All categories     €35-45
Amazon.fr         18%            All categories     €40-50
Amazon.uk         15%            All categories     €30-40
eBay.uk           12%            Vintage/collectibles €25-35
Bol.com           8%             Electronics/books  €28-38
Allegro.pl        6%             Electronics/fashion €15-25
Alternatives      13%            (Zalando, Otto, etc) €25-35

Total addressable market: €2.5M monthly revenue (multi-seller category)
Marketplace allocation example (€20,000 monthly budget):

Amazon.de (28%):  €5,600 (largest market, highest AOV)
Amazon.fr (18%):  €3,600
Amazon.uk (15%):  €3,000
eBay.uk (12%):    €2,400
Bol.com (8%):     €1,600
Allegro (6%):     €1,200
Other (13%):      €2,600
Total:            €20,000
```

## 6.2 ROI-Based Reallocation Strategy

```
Weekly performance review (Week 1 of campaign):

Marketplace    Spend     Conv   Revenue   ROAS    Recommendation
────────────────────────────────────────────────────────────────
Amazon.de      €1,400    42     €1,890    1.35x   UNDERPERFORMING
Amazon.fr      €900      35     €1,750    1.94x   STRONG
Amazon.uk      €750      18     €810      1.08x   WEAK
eBay.uk        €600      22     €1,100    1.83x   STRONG
Bol.com        €400      8      €360      0.90x   FAILING
Allegro        €300      7      €280      0.93x   FAILING

Total: €4,350 spend → €6,190 revenue → 1.42x ROAS

Month 1 Analysis & Reallocation:
Amazon.de: 1.35x ROAS, but largest market → increase to €1,600 (test)
Amazon.fr: 1.94x ROAS → increase to €1,100 (scale success)
Amazon.uk: 1.08x ROAS, low AOV → maintain at €750 (monitor)
eBay.uk: 1.83x ROAS → increase to €800 (strong performer)
Bol.com: 0.90x ROAS → decrease to €200 (reduce failing investment)
Allegro: 0.93x ROAS → decrease to €200 (reduce failing investment)

New allocation: €4,650 (slightly increased from learning)

Month 2 performance projection:
Amazon.de: €1,600 spend × 1.35 = €2,160 revenue
Amazon.fr: €1,100 spend × 1.94 = €2,134 revenue
Amazon.uk: €750 spend × 1.08 = €810 revenue
eBay.uk: €800 spend × 1.83 = €1,464 revenue
Bol.com: €200 spend × 0.90 = €180 revenue
Allegro: €200 spend × 0.93 = €186 revenue
Total projected: €4,650 spend → €6,934 revenue → 1.49x ROAS (improvement)

Gain from reallocation: €6,934 - €6,190 = +€744 additional revenue
Cost of increased spend: €4,650 - €4,350 = +€300
Net improvement: +€444 from optimization
Monthly benefit at monthly scale: €444 × 4.3 weeks = €1,909/month
```

## 6.3 Currency Exchange Impact

```
Challenges in multi-country marketing:

Example Campaign: €50,000 monthly budget allocation

Amazon.de: €15,000 (no conversion needed, €EUR)
Amazon.fr: €10,000 (no conversion needed, €EUR)
Amazon.uk: €12,500 (requires GBP conversion)
eBay.uk: €7,500 (requires GBP conversion)
Bol.com: €5,000 (€EUR, Netherlands)

GBP Conversion Impact (€/GBP rate = 1.18):
Amazon.uk budget: €12,500 ÷ 1.18 = £10,593
eBay.uk budget: €7,500 ÷ 1.18 = £6,356

Rate fluctuation scenario (rate moves 1.18 → 1.20):
Amazon.uk: £10,593 × 1.20 = €12,712 (+€212 cost increase)
eBay.uk: £6,356 × 1.20 = €7,627 (+€127 cost increase)
Total currency impact: +€339/month or 0.68% of budget

Rate fluctuation scenario (rate moves 1.18 → 1.15):
Amazon.uk: £10,593 × 1.15 = €12,182 (-€318 cost savings)
eBay.uk: £6,356 × 1.15 = €7,309 (-€191 cost savings)
Total currency impact: -€509/month or -1.02% of budget

Annual exposure: ±6,100€ from 1% GBP movement
Mitigation strategies:
├─ Set fixed GBP allocation (£16,949 = €20,000 at current rate)
├─ Rebalance monthly (move EUR spend to stable currencies)
├─ Hedge: Use forward contracts (requires treasury function)
└─ Monitor: Alert when rate moves >2% from baseline
```

## 6.4 Unified vs. Marketplace-Specific Management

```
UNIFIED APPROACH:
Single campaign spanning multiple marketplaces
├─ Pros:
│  ├─ Simpler management (one dashboard)
│  ├─ Single budget cap (no overspend risk)
│  ├─ Easier performance aggregation
│  └─ Less tool overhead
├─ Cons:
│  ├─ Cannot optimize by marketplace (lose ROI improvements)
│  ├─ Currency complexity (all conversions to home currency)
│  ├─ Different rules/regulations per marketplace (risk)
│  └─ Mixing high/low ROAS markets (blended mediocrity)
└─ Recommended: Only if <3 marketplaces or identical product strategy

MARKETPLACE-SPECIFIC APPROACH:
Separate campaigns per marketplace
├─ Pros:
│  ├─ Optimize bid by marketplace performance
│  ├─ Currency isolation (manage each separately)
│  ├─ Compliance flexibility (adapt to local rules)
│  ├─ Clear attribution (know which market performs best)
│  └─ Dynamic reallocation (move budget from failing to strong markets)
├─ Cons:
│  ├─ More complexity (6 dashboards vs. 1)
│  ├─ Tool overhead (higher subscription costs)
│  ├─ Setup time (duplicate campaign creation)
│  └─ Learning curve (manage independent strategies)
└─ Recommended: For 3+ marketplaces or mixed performance expectations

HYBRID APPROACH (RECOMMENDED for €50K+ budgets):
Unified budget allocation but marketplace-specific campaigns
├─ Structure:
│  ├─ Master budget: €50,000/month (single pool)
│  ├─ Marketplace campaigns: Separate (Amazon.de, Amazon.fr, etc.)
│  ├─ Allocation rules: Automated reallocation based on ROAS
│  └─ Reporting: Aggregated + marketplace breakdown
├─ Implementation:
│  ├─ Week 1: Allocate baseline (historical data or 50/50 split)
│  ├─ Week 2-4: Monitor performance, note ROAS by marketplace
│  ├─ Week 5: Reallocate (move 10-20% from weak to strong)
│  └─ Ongoing: Monthly reallocation based on rolling 30-day ROAS
├─ Example formula:
│  └─ New allocation = (Historical allocation × 0.7) + (ROAS ranking allocation × 0.3)
└─ Result: Balance between stability and optimization

Hybrid example (€20,000 monthly budget):
Month 1 baseline allocation (50/50 EUR/GBP markets):
├─ Amazon.de: €4,000
├─ Amazon.fr: €2,500
├─ Amazon.uk: €3,000
├─ eBay.uk: €2,500
├─ Bol.com: €2,000
├─ Allegro: €1,500
└─ Other: €4,500

Month 1 observed ROAS:
├─ Amazon.de: 1.35x
├─ Amazon.fr: 1.94x ← Strong
├─ Amazon.uk: 1.08x
├─ eBay.uk: 1.83x ← Strong
├─ Bol.com: 0.90x ← Weak
├─ Allegro: 0.93x ← Weak
└─ Other: 1.10x

Month 2 hybrid reallocation (70% history, 30% performance):
Amazon.de new: (€4,000 × 0.7) + (€3,000 × 0.3) = €3,700
Amazon.fr new: (€2,500 × 0.7) + (€4,500 × 0.3) = €3,100 ← Increased
Amazon.uk new: (€3,000 × 0.7) + (€2,500 × 0.3) = €2,850
eBay.uk new: (€2,500 × 0.7) + (€4,000 × 0.3) = €3,250 ← Increased
Bol.com new: (€2,000 × 0.7) + (€500 × 0.3) = €1,550 ← Decreased
Allegro new: (€1,500 × 0.7) + (€500 × 0.3) = €1,200 ← Decreased
Other new: (€4,500 × 0.7) + (€1,200 × 0.3) = €3,510
Total: €20,160 (rounding; adjust to €20,000 by reducing Other to €3,490)

Result: Strong performers gain +€600-750, weak performers lose €450-500
Expected improvement: +5-8% blended ROAS from optimization
```

End of section 6. Document concludes.