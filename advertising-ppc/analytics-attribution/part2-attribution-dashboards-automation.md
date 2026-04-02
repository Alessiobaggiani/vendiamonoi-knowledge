# Part 2: Attribution Models, Dashboards, Advanced Analytics & Automation

## Section 3: Attribution Models

### Last-Click Attribution

**Definition:**
Last-click attribution assigns 100% of the conversion credit to the final (most recent) touchpoint before purchase. This is the default model on most advertising platforms.

**How It Works:**
```
Customer Journey:
- Day 1: Sees brand on Facebook (ignored)
- Day 3: Clicks Amazon Sponsored Products ad
- Day 5: Purchases product

Attribution Result: Amazon Sponsored Products receives 100% credit
```

**Advantages:**
- Simplicity: easy to understand and calculate
- Performance clarity: directly measures final conversion driver
- Paid platform alignment: matches how most platforms report attribution
- Implementation: standard across all advertising platforms

**Disadvantages:**
- Ignores upper-funnel marketing: brand awareness, consideration phases
- Overvalues bottom-funnel: overestimates final-click channel value
- Undervalues awareness: Facebook, display, influencer efforts invisible
- Portfolio blindness: doesn't see cross-channel effects

**When to Use:**
- Performance-focused optimization (focus on direct converters)
- Bottom-funnel campaigns (retargeting, final-click optimization)
- New market launch (measuring direct response)
- Cash-constrained budgets (focus ROI on highest performers)

### First-Click Attribution

**Definition:**
First-click attribution assigns 100% of conversion credit to the initial (first) touchpoint in the customer journey.

**How It Works:**
```
Customer Journey:
- Day 1: Sees brand on Facebook (initial touchpoint)
- Day 3: Clicks Amazon Sponsored Products ad
- Day 5: Purchases product

Attribution Result: Facebook receives 100% credit for awareness
```

**Advantages:**
- Awareness focus: values top-funnel awareness building
- New audience discovery: measures effectiveness of brand introduction
- Strategy foundation: identifies where customer journey starts
- Complementary to last-click: combination reveals full journey

**Disadvantages:**
- Ignores conversion drivers: undervalues bottom-funnel efforts
- Overvalues initial exposure: awareness doesn't guarantee purchase
- Journey diversity: different customers have different path lengths
- Platform limitation: most platforms optimize for last-click

**When to Use:**
- Brand awareness campaigns (Facebook, display, influencer)
- New product launches (measuring initial awareness)
- Market expansion (moving customers from unaware to aware)
- Macro strategy decisions (portfolio-level awareness impact)

### Multi-Touch Attribution (MTA)

**Definition:**
Multi-touch attribution distributes conversion credit across multiple touchpoints in the customer journey. Various models allocate credit differently.

**Common Multi-Touch Models:**

**1. Linear Attribution**
- Distributes credit equally across all touches
- Example: 4 touchpoints each receive 25% credit
- Assumption: all touches equally important
- Use case: when unable to identify critical touch importance

**2. Time-Decay Attribution**
- Weights recent touches heavier than early touches
- Exponential formula: recent touches 40-50%, early touches 10-20%
- Assumption: proximity to purchase = influence
- Use case: measuring mid-to-bottom funnel influence

**3. Position-Based Attribution (U-Shaped)**
- Allocates 40% first-touch, 40% last-touch, 20% middle touches
- Recognition: first and last touches are critical
- Assumption: awareness and conversion equally important
- Use case: balanced view of journey endpoints

**4. Custom Attribution**
- Define custom weights based on business logic
- Example: awareness=20%, consideration=30%, conversion=50%
- Flexibility: matches business model and customer behavior
- Use case: industry-specific attribution (high-consideration categories)

**Multi-Touch Implementation Challenges:**

1. **Cross-Device Tracking:**
   - Customer sees ad on phone, purchases on desktop
   - Tracking pixel must follow across devices
   - Browser privacy: limited cross-device data (ITP 2.3+, FLEDGE)
   - Solution: logged-in user ID tracking (requires sign-in)

2. **Third-Party Cookie Deprecation:**
   - Chrome phasing out third-party cookies (Q4 2024+)
   - Attribution pixel tracking becomes unreliable
   - Solution: first-party data, consent-based tracking, Universal Analytics 4 (GA4)
   - Impact: increased attribution model uncertainty

3. **Data Integration:**
   - Combining data from multiple platforms (Facebook, Google, Amazon, eBay)
   - Timestamp synchronization across systems
   - User ID matching across platforms
   - Solution: CDP (Customer Data Platform), ETL pipelines

4. **Attribution Window:**
   - How long to track post-click (28-day window standard)
   - Offline conversions: tracking lag time
   - Repeat purchases: assigning credit for repeat vs. new purchase

### View-Through Attribution (VTA)

**Definition:**
View-through attribution assigns conversion credit to ads that were displayed (viewed) but not clicked. Measures impression impact beyond immediate clicks.

**How It Works:**
```
Customer Journey:
- Day 1: Sees display ad (no click)
- Day 2: Returns to marketplace search (organic)
- Day 3: Purchases product

Attribution with VTA: Display ad receives credit for view-through conversion
Without VTA: Organic search receives all credit (incomplete picture)
```

**VTA Implementation:**
- Conversion pixel on product page or thank-you page
- Pixel matched to display impression (if customer ID available)
- VTA window: typical 1-30 days post-impression
- Cookieless VTA: experimental methods (contextual factors, cohort analysis)

**VTA Advantages:**
- Awareness measurement: captures impression impact
- Brand lift calculation: non-clickers influenced by exposure
- Display ROI: vindicates display and upper-funnel spending
- Complete picture: click-through + view-through = full impact

**VTA Challenges:**
- Correlation vs. causation: hard to prove ad caused purchase
- Baseline issue: would customer have purchased anyway?
- Brand safe attribution: untargeted exposed customers included
- Cross-device limitation: pixel must match user across devices

**VTA Benchmarks:**
- Display advertising view-through rates: 0.5-2.0% (varies by placement)
- View-through CVR: 0.5-3.0% (lower than click-through CVR)
- View-through ROI: typically positive but lower than click-based
- Incremental impact: VTA reveals 10-30% additional conversions

### Cross-Device Attribution

**Definition:**
Cross-device attribution tracks customer interactions across multiple devices (phone, tablet, desktop, smart TV) and attributes conversions properly when journey spans devices.

**Common Cross-Device Scenarios:**
1. Phone: sees mobile ad during commute (no click)
2. Desktop: clicks retargeting ad at work
3. Mobile: adds to cart but doesn't checkout
4. Desktop: later completes purchase

**Cross-Device Tracking Methods:**

**1. Login-Based (Most Reliable)**
- Require customer to sign in to account
- User ID consistent across devices when logged in
- Limitations: not all users sign in immediately
- Accuracy: 85-95% (high confidence)

**2. Probabilistic (Cookie-Based)**
- Match users by IP address, device characteristics
- Historical patterns and behavior matching
- Limitations: approximation, not definitive
- Accuracy: 60-80% (moderate confidence)

**3. Deterministic (Enhanced)**
- Combination of login + hashed email matching
- Cross-device graph (Facebook, Google, Apple)
- Most accurate method available
- Accuracy: 90-98% (high confidence)

**Cross-Device Attribution Platforms:**
- Google Analytics 4 (GA4): device and cross-device reporting
- Meta Pixel: cross-device through Facebook login
- Amazon: cross-device within ecosystem (Prime members)
- Third-party: Neustar, Conversant, Innovid

**Privacy Challenges:**
- GDPR: explicit consent required for cross-device tracking
- CCPA: California residents must be able to opt-out
- Apple ITP 2.3+: limits cookie-based cross-device tracking
- Chrome Third-Party Cookie Phase-Out: impacts probabilistic methods

**Strategic Importance:**
- Mobile-first world: customers switch devices frequently
- Complete journey: understanding full device path
- Budget allocation: proper credit across device-specific campaigns
- Retargeting effectiveness: measuring cross-device impact

---

## Section 4: Dashboards & BI Integration

### Dashboard Design Principles

**Core Design Goals:**
1. **Clarity:** Information immediately understandable
2. **Hierarchy:** Important metrics prominent, details accessible
3. **Actionability:** Metrics drive decisions, not just informational
4. **Performance:** Dashboard loads <3 seconds
5. **Currency:** Data updated frequency matches decision velocity

**KPI Selection Framework:**

**Tier 1: Strategic KPIs (Executive Level)**
- Total revenue from advertising
- Overall ROAS (Return on Ad Spend)
- Market share in category
- Customer acquisition cost (CAC)
- Lifetime value (LTV) ratio

**Tier 2: Tactical KPIs (Manager Level)**
- Platform-specific ROAS (Amazon, eBay, Bol.com)
- ACoS by campaign type
- CTR and conversion rate trends
- Budget utilization and pacing
- Top-performing products and keywords

**Tier 3: Operational KPIs (Team Level)**
- Campaign-specific metrics
- Keyword performance
- Placement analysis
- Bid strategy effectiveness
- A/B test results

### Looker Studio Dashboard Setup

**Data Connection:**
- Native Amazon Advertising connector (real-time)
- Google Sheets for manual data import
- BigQuery for custom queries on historical data
- Third-party: Supermetrics, Data Studio Gallery templates

**Common Report Structure:**
```
Page 1: Executive Overview
- Total spend and revenue (current month)
- Overall ROAS and ACoS
- Platform breakdown (pie chart: Amazon, eBay, etc.)
- Trend line: ROAS over last 3 months

Page 2: Platform Deep Dive
- Amazon Advertising: campaign, category performance
- eBay Promoted Listings: campaign ROAS
- Bol.com: spend by product
- Allegro: performance by category

Page 3: Product Performance
- Top 10 performing products (by revenue)
- Bottom 10 products (by ACoS)
- New product ramp-up tracking
- Inventory-constrained products

Page 4: Keyword Analysis
- Top keywords by volume
- Top keywords by ROAS
- Keywords to pause (low ROAS)
- Seasonal keyword trends

Page 5: Drill-Down Details
- Campaign-level metrics
- Ad group analysis
- Placement breakdown
- Time-of-day performance
```

**Looker Studio Visualizations:**
- Scorecards: single metric, highlighted
- Tables: detailed data, sortable
- Line charts: trends over time
- Bar charts: comparisons (campaigns, products)
- Pie charts: distribution (budget by platform)
- Geo maps: geographic performance
- Heatmaps: day-of-week, hour-of-day performance

**Update Frequency:**
- Real-time (1-4 hour delay): critical operational metrics
- Daily: most campaign and product metrics
- Weekly: strategic analysis and review
- Monthly: detailed deep-dives, long-term trends

### Tableau Integration

**Data Source Options:**
- AWS Athena: query S3 data from API exports
- Excel/CSV files: manual upload
- Salesforce: CRM integration
- Database: direct connection to data warehouse

**Advanced Tableau Features:**
- Calculated fields: custom metrics (ACoS, CAC, LTV)
- Parameters: user inputs (date range, product category filter)
- Dashboards: multi-sheet interactive views
- Actions: click-through to detailed reports
- Alerts: automated notifications on KPI thresholds

**Typical Tableau Report Structure:**
```
Dashboard 1: Performance Overview
- KPI cards (spend, revenue, ROAS, ACoS)
- Trend analysis (month-over-month growth)
- Platform comparison (bar chart)
- Campaign ranking (sorted by ROAS)

Dashboard 2: Financial Analysis
- Profitability by product
- Margin analysis (contribution margin vs. ACoS)
- Budget utilization (actual vs. allocated)
- Cost allocation (by platform, campaign, product)

Dashboard 3: Customer Insights
- Customer acquisition cost by channel
- Repeat customer rate by advertising source
- Lifetime value by acquisition channel
- Cohort analysis (retention by acquisition date)

Dashboard 4: Operational Efficiency
- Campaign optimization recommendations
- Quality score trends
- Bid strategy effectiveness
- Automation impact analysis
```

### Power BI Configuration

**Data Import Process:**
1. Get Data: Connect to CSV, Excel, or web sources
2. Power Query: Transform and clean data
3. Load: Import into Power BI data model
4. Relationships: Define table relationships (campaign > keywords > products)
5. Measures: Create calculated columns and DAX formulas

**Common DAX Calculations:**
```
// ACoS Calculation
ACoS = SUM(Spend) / SUM(Revenue)

// ROAS Calculation
ROAS = SUM(Revenue) / SUM(Spend)

// CTR Calculation
CTR = SUM(Clicks) / SUM(Impressions)

// Month-over-Month Growth
MoM Growth = (Current Month - Prior Month) / Prior Month

// Cumulative ROAS
Cumulative ROAS = CALCULATE(ROAS, ALL(Calendar))
```

**Refresh Schedule:**
- Data refresh: daily (scheduled)  
- Dashboard interaction: real-time (within Power BI service)
- Email delivery: daily/weekly digests
- SharePoint integration: embedded dashboard access

### Qlik Sense Implementation

**In-Memory Architecture:**
- Data loaded into memory for fast querying
- Aggregation on-the-fly (no pre-calculated tables)
- Complex calculations instantly available
- Scalability: handles millions of rows

**Associative Analysis:**
- Multi-dimensional exploration
- Click on metric to filter all others
- Drill-down capability: campaign > ad group > keyword
- Visual indication of associated data

**Mobile-First Design:**
- Responsive layouts for phone/tablet
- Gesture support (swipe, tap)
- Touch-optimized controls
- Native mobile app (iOS, Android)

**Real-Time Dashboards:**
- WebSocket connection: live data updates
- Streaming connector: continuous data ingestion
- Alert trigger: automatic notifications on KPI changes
- Drill-through: interactive exploration

---

## Section 5: Advanced Analytics

### Incrementality Testing

**Definition:**
Incrementality testing measures the true incremental impact of advertising by isolating the effect of campaigns using controlled test/holdout groups.

**Core Concept:**
```
Test Group: Exposed to advertising (measurement group)
Control Group: No exposure to advertising (baseline)

Incremental Lift = Revenue(Test) - Revenue(Control)
Percentage Lift = (Incremental / Revenue(Control)) × 100
```

**Implementation Methods:**

**1. Geographic Test:**
- Test markets: Run ads in select regions
- Control markets: No advertising in matched regions
- Statistical control: Market selection based on similarity
- Duration: 4-12 weeks for reliable results

**2. Time-Based Test:**
- Test period: Run campaign during specific dates
- Control period: Compare to similar period without campaign
- Seasonal adjustment: control for holiday effects
- Limitation: requires historical baseline for comparison

**3. Customer Cohort Test:**
- New customer cohort: expose 50% to ads
- Control: 50% holdout, no ad exposure
- Measurement: Compare purchase rates between groups
- Duration: 30-60 days (adequate observation period)

**Statistical Requirements:**
- Sample size: sufficient volume for significance
- Statistical power: 80% (detect true effect 80% of the time)
- Confidence level: 95% (only 5% chance of false positive)
- Minimum detectable effect: 5-10% lift

**Results Interpretation:**
```
Test Group Revenue: $100,000 (10,000 customers, $10 AOV)
Control Group Revenue: $90,000 (10,000 customers, $9 AOV)

Incremental Lift = $100,000 - $90,000 = $10,000
Percentage Lift = ($10,000 / $90,000) × 100 = 11.1%

True ROAS = (Incremental Revenue) / (Ad Spend)
         = $10,000 / $2,000 = 5.0x

(vs. attributed ROAS of 12x based on last-click attribution)
```

### Market Mix Modeling (MMM)

**Definition:**
Market Mix Modeling uses statistical regression to quantify the contribution of each marketing channel (advertising, pricing, seasonality, promotions) to overall revenue.

**Data Requirements:**
- Historical revenue data (weekly or monthly)
- Spend data: by channel (Amazon, eBay, Bol.com, Facebook)
- Promotional data: discounts, seasonal factors
- External factors: competitor activity, macroeconomic indicators
- Time period: minimum 2 years (104 weeks) for reliable modeling

**Modeling Approach:**
```
Revenue = Base + (Channel1 Spend × Coeff1) + (Channel2 Spend × Coeff2) + 
          (Seasonality Adjustment) + (Promotion Effect) + Error

Example Model:
Revenue = $500K + (1.5 × Amazon Spend) + (1.2 × eBay Spend) + 
          (2.0 × Seasonal Factor) - (0.3 × Competitor Discount)
```

**Key Metrics Produced:**
- **Coefficient (elasticity):** revenue impact per dollar spent
  - 1.5 coefficient = $1 spend = $1.50 incremental revenue
- **Budget contribution:** estimated revenue from each channel
- **Diminishing returns:** as spend increases, effectiveness decreases
- **Optimal spend:** mathematical optimization for maximum efficiency

**Advantages:**
- Holistic view: accounts for all marketing activities
- Competitive insight: measures competitor impact
- Budget optimization: identifies over/under-invested channels
- Long-term strategy: reveals strategic value vs. tactical

**Limitations:**
- Correlation vs. causation: external factors create confounds
- Collinearity: correlated variables inflate uncertainty
- Lag effects: promotional impact not immediate
- External shocks: COVID, supply chain, economic recession

### Cannibalization Analysis

**Definition:**
Cannibalization analysis measures how new advertising initiatives reduce sales from existing products or channels (organic sales loss).

**Common Cannibalization Scenarios:**

**1. Product Cannibalization:**
```
Scenario: Launch paid ads for Product A
Result: Sales increase for Product A by 100 units
       But organic (non-paid) sales decline by 40 units
       Net gain: 60 units (60% cannibalization rate)
```

**2. Channel Cannibalization:**
```
Scenario: Launch Amazon Sponsored Products campaign
Result: Amazon advertising sales increase by $50,000
       But organic Amazon sales decline by $15,000
       Net gain: $35,000 (30% cannibalization rate)
```

**3. Platform Cannibalization:**
```
Scenario: Increase eBay advertising spend
Result: eBay sales increase $20,000
       Allegro sales decline $5,000 (customer shift)
       Net gain: $15,000 (25% cannibalization rate)
```

**Measurement Methodology:**

**Control Group Approach (Preferred):**
- Control group: customers not exposed to new campaign
- Test group: exposed to campaign
- Comparison: organic sales in test vs. control
- Result: organic sales decline in test group = cannibalization

**Baseline Trending:**
- Establish historical organic sales trend
- Predict expected sales without campaign
- Compare actual to expected
- Variance = incremental gain or cannibalization

**Customer Journey Analysis:**
- Track customer before/after campaign exposure
- Analyze product substitution patterns
- Measure channel switching behavior
- Quantify product mix changes

### Cohort Analysis

**Definition:**
Cohort analysis segments customers into groups (cohorts) based on shared characteristics or experiences and tracks their behavior over time.

**Common Cohort Types:**

**1. Acquisition Cohort (Date-Based):**
```
Cohort: Customers acquired in January 2024
Tracking: Monthly retention, repeat purchase rate, LTV

Month 1: 1,000 customers acquired
Month 2: 650 customers repeat purchase (65% retention)
Month 3: 480 customers (48% cumulative retention)
Month 4: 380 customers (38% cumulative retention)
Month 5: 310 customers (31% cumulative retention)
```

**2. Acquisition Source Cohort:**
```
Cohort A: Customers acquired from Amazon Sponsored Products
Cohort B: Customers acquired from eBay Promoted Listings  
Cohort C: Customers acquired from Organic Search

Comparison: Retention, LTV, product preferences by acquisition source
```

**3. Behavioral Cohort:**
```
Cohort: High-value customers (LTV > $500)
Tracking: Repeat purchase frequency, average order value, churn rate
Comparison: vs. low-value customers (LTV < $100)
```

**Cohort Retention Analysis:**
```
Retention Table by Acquisition Date:

             Month 1  Month 2  Month 3  Month 4  Month 5
Jan 2024     100%     65%      48%      38%      31%
Feb 2024     100%     68%      50%      39%      32%
Mar 2024     100%     62%      45%      36%      29%
Apr 2024     100%     70%      52%      42%      34%

Insights:
- Improving retention trend (Feb > Jan > Mar)
- Seasonal pattern (April cohort performs well)
- Month 2-3: critical retention period (highest drop-off)
```

**Strategic Applications:**
- Product quality impact: cohorts from high-return periods show lower retention
- Advertising quality: high-quality traffic = higher retention
- Pricing changes: price increase may impact retention
- Feature updates: new features may improve retention
- Seasonal patterns: holiday customers vs. non-holiday customers

---

## Section 6: Automation & Optimization

### Automation Framework

**Tier 1: Rules-Based Automation (Simple)**
- Budget pacing: pause when daily budget hit
- Bid adjustment: increase bids for high-performing keywords
- Negative keyword addition: auto-add based on rules
- Campaign pause: disable underperforming campaigns

**Example Rules:**
```
IF ACoS > 50% THEN reduce bid by 10%
IF CTR < 0.3% THEN review ad creative and consider replacement
IF daily spend > budget THEN pause campaign
IF no conversions in 30 days THEN pause campaign
```

**Tier 2: Algorithmic Optimization (Intermediate)**
- Bid optimization: machine learning predicts optimal bid
- Budget allocation: algorithm recommends spend reallocation
- Keyword expansion: automated keyword suggestion based on performance
- Audience targeting: ML identifies best-performing audience segments

**Tier 3: AI-Driven Automation (Advanced)**
- Deep learning: neural networks optimize across dozens of variables
- Real-time personalization: adapt creative by audience segment
- Predictive analytics: forecast sales and recommend spend levels
- Autonomous optimization: system manages campaigns with minimal oversight

### Machine Learning Optimization

**Applications:**

1. **Bid Optimization:**
   - Input: product, keyword, user, time, location, device
   - Output: optimal bid amount
   - Model: gradient boosting (XGBoost, LightGBM)
   - Improvement: 10-30% better ROAS than manual bidding

2. **Audience Targeting:**
   - Input: product, customer behavior, demographics
   - Output: audience propensity score (likelihood to purchase)
   - Model: logistic regression, random forest
   - Improvement: 15-25% higher conversion rate

3. **Budget Allocation:**
   - Input: campaign historical performance, seasonality
   - Output: recommended daily budget distribution
   - Model: reinforcement learning
   - Improvement: 20-40% better overall ROAS

4. **Creative Optimization:**
   - Input: product attributes, historical creative performance
   - Output: recommended image, headline, copy variants
   - Model: classification + neural network
   - Improvement: 5-15% higher CTR

### Automation Tools & Platforms

**Amazon Native Automation:**
- Automatic bidding: Dynamic bids (down only), Target ACoS, Maximize conversions
- Sponsored Ads Manager: campaign automation features
- Rules: create custom rules for bid, budget management
- Bulk operations: schedule operations, set recurring changes

**Third-Party Automation:**
- Skai: unified management platform for Amazon, Google, Facebook
- Pattern: AI-driven Amazon advertising optimization
- Cortexica: computer vision for creative optimization
- Advertising Agency Tools: Efficient Frontier, Marin Software

**ETL & Data Pipeline Automation:**
- Apache Airflow: workflow orchestration
- Dagster: modern data orchestration platform
- Great Expectations: data quality monitoring
- dbt: data transformation automation

---

## Appendix A: Metric Formulas Quick Reference

| Metric | Formula | Interpretation |
|--------|---------|----------------|
| CTR | (Clicks / Impressions) × 100 | % of impressions resulting in clicks |
| CPC | Total Spend / Total Clicks | Average cost per click |
| CVR | (Conversions / Clicks) × 100 | % of clicks resulting in purchase |
| CPA | Total Spend / Total Conversions | Average cost per purchase |
| ACoS | (Advertising Spend / Sales Revenue) × 100 | Spend as % of attributed sales |
| ROAS | Sales Revenue / Advertising Spend | Revenue generated per dollar spent |
| TACoS | (Total Ad Spend / Total Revenue) × 100 | All ad spend as % of total revenue |
| Gross Margin | (Revenue - COGS) / Revenue | Profit margin before operating costs |
| Net Margin | Net Profit / Revenue | Profit margin after all costs |
| CAC | Marketing Spend / New Customers | Cost to acquire one new customer |
| LTV | (Average Revenue × Lifetime Weeks) | Total value of customer relationship |

## Appendix B: Marketplace Reporting Comparison

| Feature | Amazon | eBay | Bol.com | Allegro |
|---------|--------|------|---------|----------|
| Real-time reporting | Yes | Yes | Yes | Yes |
| API access | Yes (v3.0) | Yes | Limited | Limited |
| Search term reporting | Yes | No | Limited | Limited |
| Placement reporting | Yes | No | No | Limited |
| Cross-device attribution | Yes | Limited | No | No |
| Video performance tracking | Yes | Limited | No | No |
| Multi-touch attribution | No (basic) | No | No | No |
| Data export frequency | Daily | Daily | Daily | Daily |
| Historical lookback | 3 years | 2 years | 90 days | 180 days |

---

**End of Domain 3.6: Analytics & Attribution**
