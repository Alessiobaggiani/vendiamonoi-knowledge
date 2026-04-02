# Part 1: Advertising Performance Reporting & Key Metrics

## Section 1: Advertising Performance Reporting

### Amazon Advertising Reports Architecture

Amazon provides a comprehensive reporting suite across sponsored ads, display ads, video ads, and brand advertising. The reporting system is built on three core layers:

#### Layer 1: Dashboard Reports
- Sponsor Products Performance Dashboard (real-time, aggregated)
- Sponsored Brands Performance Dashboard
- Sponsored Display Performance Dashboard
- Video Ads Performance Dashboard
- Brand Metrics Dashboard (brand awareness and consideration)
- Cross-channel Attribution Dashboard

#### Layer 2: Downloadable Reports
- Search Term Report (keyword performance, search frequency)
- Placement Report (product page placements, top of search, sidebar performance)
- Targeting Report (audience targeting, keyword targeting effectiveness)
- Budget Report (daily spend, budget utilization, remaining budget)
- Advertised Product Report (by-product performance metrics)
- Purchased Product Report (customer purchase behavior post-ad exposure)

#### Layer 3: API-Based Reporting
- Amazon Advertising API (v3.0) for programmatic access
- Report generation with custom parameters
- Scheduled report generation and delivery
- Real-time data streaming for automation

### Search Term Report Deep Dive

The Search Term Report reveals actual customer search queries that triggered impressions:

**Available Metrics:**
- Query (customer search term)
- Impressions (how many times the ad appeared for that query)
- Clicks (click-throughs from that query)
- Cost (total advertising spend for that query)
- Conversions (attributed sales from that query)
- Advertised SKU (ASIN being advertised)
- Customer Search Term (exact match to customer input)
- Placement (where the ad appeared: Top, Sidebar, Product Pages)

**Optimization Actions:**
- Add high-performing search terms as keywords (auto to manual campaign migration)
- Add high-cost, low-converting terms as negative keywords
- Identify long-tail opportunities with low competition
- Analyze intent shifts: same keyword, different placement performance
- Seasonal patterns: winter holidays, back-to-school, gift-giving occasions

### Placement Report Analysis

Placement reports show where advertisements appeared on Amazon:

**Placement Types:**
- Top of Search (TOS): Premium placement above organic results
- Sidebar: Right-hand sidebar on search results page
- Product Pages: Placements on competing product detail pages
- Rest of Search: Below the fold, organic search results area

**Performance Patterns:**
- Top of Search typically highest CTR (0.8-1.2%), highest CPC
- Product pages moderate CTR (0.4-0.7%), lower CPC
- Sidebar lower CTR (0.2-0.4%), lowest CPC
- Bid adjustment by placement: TOS bids typically 50-100% higher

### Targeting Report Mechanics

Targeting reports reveal audience segment performance:

**Report Contents:**
- Targeting type (automatic, keyword, targeting expression, audience)
- Target expression (specific keyword, ASIN, interest, demographic)
- Impressions and clicks by target
- Cost per target
- Conversion performance by audience segment

**Analysis Framework:**
- Identify underperforming audience segments for exclusion
- Find high-intent audiences for budget increase
- Seasonal audience changes: gift-buyers, seasonal products
- Cross-platform audience analysis: video, display, search performance

### Budget Report Structure

Daily budget reports track spend against allocated budget:

**Key Metrics:**
- Daily spend (actual cost per day)
- Budget allocated (daily or campaign-level budget)
- Remaining budget (budget available for campaign)
- Days remaining in campaign
- Projected spend (forecasted based on daily burn rate)

**Budget Management:**
- Real-time budget monitoring for campaign pacing
- Prevention of early budget exhaustion
- Identification of under-spending campaigns (bid too high, low volume)
- Budget reallocation to high-performing campaigns

### Advertised Product Report

Product-level performance breakdown:

**Metrics by Product:**
- ASIN (Amazon Standard Identification Number)
- Product title and category
- Impressions and clicks per product
- CTR by product
- Cost per product
- Conversions attributed to product advertising
- Units sold and revenue generated

**Product-Level Optimization:**
- Identify low-performing products (high spend, low conversions)
- Scale high-performing products (increase bid, expand to new campaigns)
- Portfolio analysis: hero products vs. long-tail
- Product refresh impact: pre/post launch performance

### Purchased Product Report

Customer purchase behavior post-ad exposure:

**Report Structure:**
- Customer journey: ad exposure > click > purchase
- Advertised ASIN (product shown in ad)
- Purchased ASIN (actual product customer bought)
- Purchase latency (days between click and purchase)
- Order value and profit margin

**Strategic Insights:**
- Halo effect: customers see Product A ad, buy Product B
- Complementary products: frequently co-purchased items
- Category penetration: ads drive cross-category purchases
- Profit optimization: measure profitability, not just revenue

### Report Scheduling & Automation

**Manual Report Generation:**
- Dashboard view: real-time, limited customization
- Downloadable reports: CSV/TSV format, 3-month historical data
- Scheduled delivery: daily, weekly, monthly email delivery
- Custom date ranges and metric selection

**Automated Report Delivery:**
- Amazon Advertising API scheduled reports
- Set up once, recurring delivery on schedule
- Automatic file storage in S3 or email delivery
- Integration with ETL pipelines and BI tools

### Amazon Advertising API v3.0

**Core API Endpoints:**
```
/v3/reports - Report generation and management
/v3/portfolios - Portfolio management
/v3/campaigns - Campaign CRUD operations
/v3/keywords - Keyword management
/v3/productAds - Ad management
/v3/targets - Audience targeting
```

**Report Types:**
- `spSearchTermReport`: Search term performance
- `spPlacementReport`: Placement performance
- `spPurchasedProductReport`: Customer purchase behavior
- `spAdvertisedProductReport`: Product-level performance
- `sbReport`: Sponsored Brands performance
- `sdReport`: Sponsored Display performance

**API Authentication:**
- OAuth 2.0 flow for seller account access
- Refresh token rotation every 30 days
- Rate limiting: 10 requests per second, 50,000 requests per day
- Exponential backoff for rate limit handling

### eBay Promoted Listings Reporting

**Report Types:**
- Campaign Performance Report: aggregate metrics by campaign
- Listing Performance Report: item-level results
- Promotion Impact Report: organic vs. promoted performance
- Traffic and Conversion Report: visitor journey analysis

**Key Metrics:**
- Impressions (promoted listing views)
- Clicks (click-throughs to item page)
- Conversion Rate (promoted sales / clicks)
- ACoS equivalent (promotion spend / revenue from promoted sales)
- Organic impact (lift in organic sales due to visibility)

**Data Access:**
- eBay Seller Center dashboard: real-time metrics
- CSV export: daily performance files
- eBay API: Inventory, Promotion, Analytics endpoints
- Report refresh: hourly updates for real-time monitoring

### Bol.com Advertising Reports

**Available Reports:**
- Sponsored Products Performance: daily aggregated metrics
- Campaign Manager: real-time dashboard and export
- Advertising Statistics: CTR, CPC, conversion data
- Product Performance: ASIN-level results

**Metrics:**
- Impressions and clicks
- Average CPC and CTR
- Conversions and conversion rate
- Spend by campaign and product
- ROI calculation (Bol.com specific)

**Data Architecture:**
- Real-time dashboard updates every 15 minutes
- Historical data: 2-year lookback
- CSV export: daily, weekly, monthly options
- API access: limited, primarily dashboard-based

### Kaufland Advertising Reports

**Report Structure:**
- Campaign Dashboard: real-time performance overview
- Daily Reports: downloadable CSV files
- Product Reports: item-level performance
- Channel Analysis: organic vs. sponsored visibility

**Key Metrics:**
- Impressions and clicks by campaign
- Spend and cost per click
- Conversion data (Kaufland tracks purchase events)
- Sales attributed to advertising
- ROAS calculation

**Report Features:**
- 90-day historical data available
- Customizable date ranges
- Campaign-level and product-level granularity
- Automatic daily email delivery option

### Allegro Advertising Platform Reporting

**Report Types:**
- Campaign Performance Report: aggregated by campaign
- Promoted Item Report: item-level metrics
- Daily Traffic and Sales: visitor and conversion tracking
- Audience Insights: demographic and behavioral data

**Available Metrics:**
- Impressions and clicks
- Average CPC and CTR
- Sales and revenue attributed to advertising
- ACoS-equivalent metric (advertising spend vs. revenue)
- Conversion rate by item

**Data Management:**
- Real-time dashboard with 1-hour delay
- Historical data: 180-day lookback
- CSV and Excel export functionality
- Automated daily email reports

### Cdiscount Advertising Reports

**Report Structure:**
- Advertising Dashboard: real-time metrics overview
- Campaign Reports: daily performance by campaign
- Product Performance: item-level analytics
- Advertiser Statistics: cross-campaign aggregation

**Key Metrics:**
- Impressions, clicks, and CTR
- Cost per click and total spend
- Conversion rate and total sales
- Average order value and profit margin
- Budget utilization and pacing

**Data Export Options:**
- CSV files via dashboard download
- Scheduled email delivery
- Real-time API access (limited)
- 60-day historical data available

### Data Export Formats

**Standard Formats:**
- CSV (Comma-Separated Values): universal import to spreadsheets and BI tools
- TSV (Tab-Separated Values): Amazon native format
- JSON: API responses, programmatic integration
- Excel (.xlsx): formatted reports with formulas
- Parquet: big data processing and storage

**Export Specifications:**
- Column headers: included in first row
- Character encoding: UTF-8 standard
- Date format: YYYY-MM-DD or customizable
- Number format: decimal point (.) vs. comma (,) regional variations
- Timezone handling: UTC standard, regional conversion on request

### BI Tool Integration (Part of Section 4)

**Looker Studio Setup:**
- Native Amazon Advertising connector
- Real-time data refresh (hourly automated)
- Drag-and-drop dashboard creation
- Pre-built templates for common KPIs

**Tableau Integration:**
- AWS/Amazon Athena data source connection
- Custom SQL queries for complex analysis
- Live vs. extracted data modes
- Advanced visualization capabilities

**Power BI Configuration:**
- Data import from CSV/Excel files
- Query Editor for data transformation
- Scheduled refresh every 4-24 hours
- RLS (Row-Level Security) for team access

**Qlik Sense Implementation:**
- In-memory processing for fast analytics
- Associative analysis for multi-dimensional exploration
- Mobile-first dashboard design
- Real-time alerts and KPI monitoring

---

## Section 2: Key Advertising Metrics

### Impressions: Foundation Metric for Reach

**Definition:**
An impression is counted each time an advertisement is displayed to a user, regardless of whether they interact with it. In the context of marketplace advertising, an impression represents an ad served and displayed on a product page, search results page, or sponsored content placement.

**Calculation Formula:**
```
Impressions = Total ad displays
```

No mathematical calculation; impressions are recorded at the moment of display by the advertising platform's server.

**Platform-Specific Tracking:**

- **Amazon:** Impressions counted when sponsored products appear in search results or on product pages, even if the user doesn't scroll to view the ad
- **eBay:** Impressions counted for promoted listings that appear in search results
- **Bol.com:** Sponsored products impressions when displayed in search and recommendation placements
- **Allegro:** Ad impressions tracked on search results and category pages
- **Kaufland:** Impressions counted on product page placements and search results
- **Cdiscount:** Ad impressions on marketplace search and category pages

**Viewability Standards:**
- Standard definition: ad appears on page (even if below fold)
- Enhanced viewability: ad in viewport for 50% of time, 1+ second minimum
- Premium viewability: ad in viewport for 70% of time, 3+ second minimum
- Non-viewable: user never scrolls to ad location, ad blocked by browser extensions

**Impression Volume Drivers:**

1. **Keyword Competitiveness:** High-volume keywords = more impressions
   - "iPhone" generates 1M+ monthly impressions
   - "iPhone 15 Pro Max screen protector" generates 10K monthly impressions
   - Long-tail keywords have lower but more qualified impression volume

2. **Bid Strategy Impact:** Higher bids = more placements = more impressions
   - Increasing bid from $0.50 to $1.00 can double impressions
   - Placement preference settings influence impression distribution
   - Manual vs. automatic bidding changes impression frequency

3. **Budget Allocation:** Daily budget limits total impressions possible
   - $10/day budget at $1 CPC = 10 clicks/day maximum
   - $100/day budget allows 100 clicks/day with same CPC
   - Budget depletion earlier in day = fewer daily impressions

4. **Negative Keywords:** Exclusions reduce irrelevant impressions
   - Adding "free" as negative keyword eliminates 15-30% of impressions
   - Negative phrase match prevents related variations
   - Category-specific negatives reduce wasted spend

5. **Audience Targeting:** Audience selection filters impression delivery
   - In-market audiences receive ads more frequently
   - Custom audiences limited to specific customer list size
   - Lookalike audiences = larger impression pool

**Impression Quality Assessment:**

- **High-quality impressions:** Low-intent traffic that doesn't convert wastes budget
- **Qualified impressions:** Target customer within market window, product relevance high
- **Brand-safe impressions:** Ad appears in appropriate content context (no controversial content, fake websites)
- **Fraud impressions:** Bot traffic, auto-refreshing ads, hidden ad placements

**Impression Fraud Detection:**
- Click-through rate anomalies (CTR > 5% suggests bot traffic)
- Conversion rate collapse (traffic with zero purchases)
- Geographic anomalies (clicks from countries where product doesn't ship)
- Time-of-day patterns (clicks at 3 AM in unusual volumes)

**Benchmarking Impressions by Vertical:**

| Product Category | Monthly Impressions (per ASIN) | Impression Growth Rate |
|---|---|---|
| Electronics | 50K-500K | +15-30% YoY |
| Home & Kitchen | 30K-300K | +10-20% YoY |
| Fashion & Apparel | 40K-400K | +20-35% YoY |
| Health & Beauty | 25K-250K | +15-25% YoY |
| Sports & Outdoors | 20K-200K | +10-15% YoY |
| Pet Supplies | 15K-150K | +20-30% YoY |

### Clicks and Click-Through Rate (CTR)

**Click Definition:**
A click occurs when a user actively selects an advertisement by clicking on the ad unit, product image, headline, or call-to-action button. One click = one user interaction with the ad, recorded by the advertising platform's pixel tracking.

**Click Counting Rules:**
- Single click per user interaction (double-click = 1 click)
- Click requires navigation to landing page or product page
- Page views without clicks are impressions only
- Back button navigation still counts as original click
- Click fraud: multiple clicks from same IP in short timeframe (filtered by platform)

**Click-Through Rate (CTR) Calculation:**
```
CTR (%) = (Clicks / Impressions) × 100

Example:
- Impressions: 10,000
- Clicks: 50
- CTR = (50 / 10,000) × 100 = 0.5%
```

**CTR Interpretation:**
- 0.1-0.3%: Low engagement (check ad relevance and creative)
- 0.3-0.8%: Average performance (good baseline)
- 0.8-1.5%: Strong engagement (proven ad relevance)
- 1.5%+: Exceptional performance (organic/brand demand, optimized messaging)

**Platform CTR Benchmarks:**

| Platform | Average CTR | Top Performers | Bottom Quartile |
|---|---|---|---|
| Amazon Sponsored Products | 0.5-0.8% | 1.2-1.8% | 0.1-0.3% |
| Amazon Sponsored Brands | 0.3-0.6% | 0.8-1.2% | 0.1-0.2% |
| Amazon Sponsored Display | 0.2-0.4% | 0.6-1.0% | 0.05-0.1% |
| eBay Promoted Listings | 0.3-0.6% | 0.9-1.3% | 0.1-0.2% |
| Bol.com Sponsored | 0.25-0.5% | 0.7-1.0% | 0.1-0.15% |
| Allegro Promoted | 0.3-0.6% | 0.8-1.2% | 0.1-0.2% |
| Kaufland Sponsored | 0.2-0.5% | 0.6-1.0% | 0.05-0.15% |
| Cdiscount Advertising | 0.25-0.5% | 0.7-1.2% | 0.05-0.15% |

**Factors Affecting CTR:**

1. **Ad Creative Quality:**
   - Product image quality: professional photos = +0.2-0.4% CTR
   - Headline clarity: specific benefits = +0.1-0.3% CTR
   - Call-to-action clarity: "Learn More", "Shop Now" = +0.1-0.2% CTR
   - Price visibility: competitive prices = +0.1-0.3% CTR

2. **Placement Position:**
   - Top of search: CTR 0.8-1.2% (premium placement)
   - Product page: CTR 0.4-0.7% (secondary placement)
   - Sidebar: CTR 0.2-0.4% (tertiary placement)
   - Below-the-fold: CTR 0.05-0.2% (limited visibility)

3. **Keyword Relevance:**
   - Exact match: CTR 0.7-1.2% (high intent)
   - Phrase match: CTR 0.4-0.7% (medium intent)
   - Broad match: CTR 0.2-0.5% (low relevance)
   - Negative keywords used: CTR improves 10-20% by filtering unqualified traffic

4. **Product Factors:**
   - New products: CTR 0.2-0.4% (discovery phase)
   - Established products: CTR 0.5-1.0% (demand established)
   - Best-sellers: CTR 0.8-1.5% (high demand)
   - Clearance/sale items: CTR 1.0-2.0% (urgency driver)

5. **Seasonality:**
   - Off-season: CTR 0.3-0.5% (low demand)
   - Pre-season: CTR 0.5-0.8% (emerging demand)
   - Peak season: CTR 1.0-1.5% (high demand)
   - Post-season: CTR 0.4-0.6% (declining demand)

**CTR Fraud Indicators:**
- CTR > 5%: Likely invalid traffic (bots, accidental clicks)
- CTR with 0% conversion: Clicks not resulting in page engagement
- Clicks from single IP: Traffic concentration anomaly
- Clicks outside expected geographic region: Geo-targeting failure

**CTR Optimization Strategies:**

1. **Ad Copy Testing:**
   - A/B test headlines: quantify impact of different messages
   - Test price visibility: "From $9.99" vs. "Starting at"
   - Test benefit-focused copy: "Waterproof" vs. "Water-resistant for up to 6 hours"
   - Test urgency: "Limited stock" vs. "Fast shipping"

2. **Image Optimization:**
   - Professional photography: studio lighting, clean background = +0.2-0.3% CTR
   - Lifestyle images: product in use = +0.1-0.2% CTR
   - Multiple images: carousel format = +0.15-0.25% CTR
   - Background color contrast: white background = +0.1% CTR vs. busy backgrounds

3. **Negative Keyword Additions:**
   - Add "free" if product is premium (filters bargain hunters)
   - Add product variant names for non-matching variants
   - Add competitor brand names to reduce off-target traffic
   - Add delivery method negatives: "overnight" if not offered

4. **Bid and Placement Optimization:**
   - Increase bids for top placements: secure top-of-search positions
   - Adjust bids by placement type: higher bids for high-CTR placements
   - Placement bid adjustments: +50% for top-of-search, -30% for sidebar
   - Daypart bidding: higher bids during peak shopping hours (6-9 PM)

### Cost Per Click (CPC)

**Definition:**
Cost Per Click (CPC) is the average amount an advertiser pays for each click on their advertisement. Calculated by dividing total ad spend by total clicks.

**CPC Calculation:**
```
CPC = Total Advertising Spend / Total Clicks

Example:
- Total Spend: $500
- Total Clicks: 250
- CPC = $500 / 250 = $2.00 per click
```

**CPC Components:**

1. **Bid Amount:** Advertiser's maximum willingness to pay
   - Manual bidding: advertiser sets exact bid
   - Automatic bidding: system sets bid based on conversion probability
   - Average bid != average CPC (actual CPC often lower due to second-price auctions)

2. **Auction Competition:** Competing advertisers' bids
   - High competition keywords: CPC increases 20-50%
   - Low competition keywords: CPC decreases 10-30%
   - Seasonal competition: holiday CPCs 2-3x higher than off-season

3. **Quality Score/Relevance:** Platform-weighted relevance metric
   - Amazon Relevance Score (1-5): lower score = higher CPC
   - eBay Quality Score: affects placement and CPC
   - Bol.com Quality Rating: impacts advertising eligibility and costs
   - Higher relevance = lower CPC required for same placement

**Platform CPC Benchmarks by Category:**

| Category | Amazon CPC | eBay CPC | Bol.com CPC | Allegro CPC |
|---|---|---|---|---|
| Electronics | $0.75-$2.50 | $0.40-$1.50 | $0.25-$1.00 | $0.30-$1.20 |
| Fashion | $0.50-$1.50 | $0.30-$1.00 | $0.20-$0.80 | $0.25-$1.00 |
| Home & Garden | $0.40-$1.20 | $0.25-$0.75 | $0.15-$0.60 | $0.20-$0.80 |
| Health & Beauty | $0.60-$1.80 | $0.35-$1.20 | $0.20-$0.70 | $0.25-$0.90 |
| Sports | $0.45-$1.50 | $0.25-$0.90 | $0.15-$0.65 | $0.20-$0.85 |

**CPC Variation Factors:**

1. **Keyword Type:**
   - Branded keywords: CPC $0.30-$0.80 (low competition, high intent)
   - Category keywords: CPC $0.60-$1.50 (medium competition)
   - Competitor keywords: CPC $1.00-$3.00 (high competition)
   - Long-tail keywords: CPC $0.20-$0.60 (low competition, low volume)

2. **Daypart (Time of Day):**
   - Early morning (5-7 AM): CPC -20% to -30% (lower competition)
   - Business hours (9 AM-5 PM): CPC baseline (standard competition)
   - Evening (6-10 PM): CPC +15% to +25% (higher competition, peak shopping)
   - Night (10 PM-5 AM): CPC -10% to -20% (lower competition)

3. **Seasonality:**
   - Off-season: CPC baseline
   - Pre-season: CPC +10-30%
   - Peak season (holidays): CPC +100-200% (significantly higher)
   - Post-season/clearance: CPC +0-50% (variable based on urgency)

4. **Geographic Variation:**
   - Major metropolitan areas: CPC +20-40% (higher competition, higher customer value)
   - Secondary markets: CPC baseline
   - Rural areas: CPC -10-20% (lower competition, lower customer density)
   - International marketplaces: currency conversion, local competition

**CPC Optimization Strategies:**

1. **Bid Management:**
   - Manual bidding: set bids to match target ACoS
   - Automatic bidding: let platform optimize based on conversion data
   - Bid adjustments: by placement (+50% top-of-search, -30% sidebar)
   - Daypart bidding: -20% bids during low-traffic hours, +15% during peak

2. **Negative Keywords:**
   - Add competitor brand names as negatives (reduce wasted spend)
   - Add product variant negatives: "blue" if selling red only
   - Add intent-mismatched terms: "free", "discount", "cheap"
   - Add misspelled variations that won't convert

3. **Quality Score Improvement:**
   - Improve landing page relevance: product page matches keyword exactly
   - Increase ad relevance score: match ad copy to search intent
   - Improve click-through rate: better ad creative and copy
   - Lower bounce rate: ensure product page loads fast and matches expectation

4. **Campaign Structure:**
   - Separate high-volume from long-tail keywords (different strategies)
   - Single-ASIN campaigns: laser-focused relevance
   - Separate branded from non-branded: different CPC targets
   - Test new keywords in dedicated low-spend campaigns

### Conversion Rate (CVR)

**Definition:**
Conversion Rate is the percentage of clicks that result in a completed purchase (or desired action). It measures how effectively traffic is converted into customers.

**CVR Calculation:**
```
CVR (%) = (Conversions / Clicks) × 100

Example:
- Clicks: 200
- Conversions (purchases): 8
- CVR = (8 / 200) × 100 = 4.0%
```

**CVR Benchmarks by Marketplace:**

| Marketplace | Average CVR | High Performers | Low Performers |
|---|---|---|---|
| Amazon (All) | 5-12% | 15-25% | 1-3% |
| Amazon (Sponsored Products) | 6-10% | 12-20% | 2-4% |
| Amazon (Sponsored Brands) | 4-8% | 10-15% | 1-3% |
| eBay | 2-6% | 8-12% | 0.5-1.5% |
| Bol.com | 3-7% | 9-14% | 1-2% |
| Allegro | 3-8% | 10-15% | 1-2% |
| Kaufland | 2-6% | 7-12% | 0.5-1.5% |
| Cdiscount | 2-5% | 6-10% | 0.5-1% |

**Factors Affecting CVR:**

1. **Product Factors:**
   - Price point: budget items (CVR 8-15%) vs. premium (CVR 2-5%)
   - Availability: in-stock items (CVR +2-3%) vs. limited stock
   - Reviews/ratings: 4.5+ stars (CVR +5-8%) vs. 3.0-3.5 stars (CVR -2-4%)
   - Product images: high-quality (CVR +2-4%) vs. low-quality (-1-2%)

2. **Search Intent Quality:**
   - Exact brand match: CVR 15-25% (high intent)
   - Category/feature match: CVR 4-8% (medium intent)
   - Broad match: CVR 1-3% (low intent, exploratory)
   - Long-tail keywords: CVR 6-12% (specific intent)

3. **Keyword Relevance:**
   - Perfect match (keyword = product): CVR 10-15%
   - Close match (keyword = product category): CVR 5-8%
   - Loose match (keyword = general interest): CVR 2-4%
   - Competitor keywords: CVR 2-5% (lower intent)

4. **Seasonality & Timing:**
   - Peak season (holidays): CVR +5-10% (strong demand)
   - Pre-season: CVR baseline to +3%
   - Off-season: CVR -2-5% (lower demand)
   - Day-of-week: weekends CVR +2-5% higher than weekdays

5. **Geographic Factors:**
   - Major metros: CVR +1-2% (higher purchasing power)
   - Secondary markets: CVR baseline
   - Rural areas: CVR -0.5-1% (lower population density)
   - International: varies by market maturity and brand recognition

**CVR Analysis by Product Category:**

| Category | Primary CVR Driver | Typical Range |
|---|---|---|
| Electronics | Brand recognition, reviews | 8-15% |
| Fashion | Size availability, return policy | 4-10% |
| Home & Garden | Seasonal demand, aesthetic appeal | 3-7% |
| Health & Beauty | Brand trust, ingredient focus | 5-12% |
| Sports & Outdoors | Activity popularity, season | 4-9% |
| Pet Supplies | Pet type specificity, reviews | 6-11% |

**CVR Optimization Strategies:**

1. **Keyword Optimization:**
   - Remove low-intent keywords (searchers not ready to buy)
   - Increase bids on high-converting keywords
   - Add negative keywords that drive low-converting traffic
   - Analyze search term report for intent mismatches

2. **Product Listing Optimization:**
   - Improve product title: include key features (CVR +1-2%)
   - Add high-quality images: multiple angles, lifestyle shots (CVR +1-3%)
   - Enhance description: detailed specifications, benefit statements (CVR +0.5-1.5%)
   - Optimize bullet points: scannable benefits, key features (CVR +0.5-1%)
   - Increase star rating: encourage positive reviews (CVR +2-4% per 0.5-star increase)
   - Reduce price: competitive pricing increases CVR (CVR +0.5-2% per $5 reduction)

3. **Campaign Structure:**
   - Separate high-CVR from low-CVR keywords (different strategies)
   - Dedicated campaigns for top-converting keywords (aggressive bids)
   - Test new keywords in small campaigns first (validate before scaling)
   - Monitor underperforming keywords (reduce bids, add negatives)

4. **Inventory & Availability:**
   - Maintain inventory: out-of-stock kills CVR (missing customers)
   - Ensure fast shipping: competitive delivery = +1-2% CVR
   - Offer returns policy: reduces purchase anxiety = +0.5-1.5% CVR
   - Flash sales/urgency: limited-time offers = +2-4% CVR

### Advertising Cost of Sale (ACoS)

**Definition:**
Advertising Cost of Sale (ACoS) is an Amazon-specific metric that represents the ratio of advertising spend to attributed sales revenue. It measures the profitability of advertising efforts on a 0-100% scale.

**ACoS Formula:**
```
ACoS (%) = (Total Advertising Spend / Attributed Sales Revenue) × 100

Example:
- Advertising Spend: $500
- Attributed Sales Revenue: $2,000
- ACoS = ($500 / $2,000) × 100 = 25%
```

**ACoS Interpretation:**
- ACoS < 20%: Highly profitable (excellent ROAS)
- ACoS 20-30%: Profitable (good performance, sustainable)
- ACoS 30-40%: Moderate (acceptable for market penetration)
- ACoS 40-50%: Break-even or marginal (typically not sustainable)
- ACoS > 50%: Unprofitable (ad spend exceeds revenue)

**ACoS Relationship to Profit:**

```
Target Profit Margin: 40%
Target ACoS: 20-30% (leaves margin for other costs)

Calculation:
- Selling Price: $100
- COGS: $30
- Gross Margin: $70 (70%)
- Operating Costs: $25 (utilities, labor, overhead)
- Available for ACoS: $45 (45%)
- Target ACoS: 25% (example conservative target)
- Net Profit: $20 per sale (20% net margin)
```

**ACoS by Product Category Benchmarks:**

| Category | Typical ACoS Range | High Competitors | Low Performers |
|---|---|---|---|
| Electronics | 15-35% | 8-15% | 40-60% |
| Fashion & Apparel | 20-40% | 12-20% | 45-65% |
| Home & Kitchen | 18-35% | 10-18% | 40-55% |
| Health & Beauty | 15-30% | 8-15% | 35-50% |
| Sports & Outdoors | 20-38% | 12-20% | 42-60% |
| Pet Supplies | 18-32% | 10-18% | 38-52% |

**Key Performance Levers for ACoS:**

1. **Revenue Increase (Denominator):**
   - Improve product price: higher price = lower ACoS (25% of $100 sale vs. $50 sale)
   - Increase conversion rate: more sales from same clicks
   - Encourage add-on purchases: bundling, cross-sells
   - Leverage seasonality: time campaigns during peak demand

2. **Cost Reduction (Numerator):**
   - Lower bid amounts: reduce CPC
   - Optimize negative keywords: reduce wasted spend
   - Improve quality score/relevance: same placement at lower bid
   - Reduce bid on low-converting keywords

3. **Efficiency Combination:**
   - High-converting campaigns: accept higher ACoS (20-35%)
   - Low-converting campaigns: target lower ACoS (10-20%) or pause
   - New product ramp: higher ACoS acceptable during launch phase
   - Established products: optimize for profitability, lower ACoS target

**ACoS Tracking Over Time:**

**Monthly ACoS Trend Analysis:**
```
January: 28% (acceptable)
February: 31% (creeping up, monitor)
March: 35% (investigation needed)
April: 38% (corrective action required)
```

**Root Cause Analysis for Rising ACoS:**
1. Seasonality decline (off-season demand)
2. Competitive bids increasing (market saturation)
3. Quality score declining (ad relevance issues)
4. Product-level changes (inventory, reviews, pricing)
5. Keyword expansion too broad (low-quality traffic)

**ACoS Optimization by Campaign Stage:**

| Stage | ACoS Strategy | Typical Target |
|---|---|---|
| Launch/Discovery | Higher acceptable ACoS | 40-60% (growth phase) |
| Growth | Moderate ACoS target | 25-40% (expansion) |
| Mature/Stable | Low ACoS focus | 15-30% (profitability) |
| Decline/Seasonal | Variable by circumstance | 10-50% (tactical) |

---

**End of Part 1: Reporting and Metrics**
