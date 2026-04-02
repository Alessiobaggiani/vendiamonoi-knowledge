# Part 2: Criteo, Retail Media Networks, Video, and Programmatic Strategy

## Section 3: Criteo Retail Media Platform

### 3.1 Platform Overview

Criteo is the leading independent retail media network specializing in digital advertising solutions for retailers and brands. Unlike Amazon, which primarily serves Amazon.com sellers, Criteo operates a neutral platform serving 600+ retail and publisher partners globally with focus on EU retail networks.

**Key Differentiators**:
- Agnostic platform: Works with any retailer (not owned by retailer)
- EU-centric: Strong coverage of European retailers (Carrefour, FNAC, Cdiscount, Bol.com)
- First-party data: Partners share customer transaction data (privacy-compliant)
- Advanced attribution: Multi-touch attribution across retail ecosystem
- Omnichannel: Desktop, mobile, video, in-store integration

**Core Services**:
1. **Sponsored Products**: Keyword-based ads on partner retailer sites
2. **Display Ads**: Retargeting across publisher networks
3. **Native Ads**: Sponsored content within editorial
4. **Email Marketing**: Promotional emails to retailer customer lists
5. **Onsite Personalization**: Dynamic product recommendations

### 3.2 Sponsored Products on Criteo Network

#### Campaign Setup

**Product Feed Integration**:
- XML or CSV feed of product catalog
- Required fields: Product ID, title, price, category, image URL
- Feed updates: Daily or real-time via API
- Criteo matches products with retailer inventory
- Feed validation: Automatic quality checks

**Catalog Match Rate**:
- Typical: 70-85% of products matched to retailer
- Issues: Brand/product mismatch, pricing format, missing data
- Resolution: Manual product mapping or feed correction

#### Campaign Configuration

**Campaign Structure**:
- By retailer (e.g., one campaign per Criteo partner)
- By product category (e.g., Electronics, Apparel)
- By audience segment (e.g., High-value customers)

**Bidding Strategy**:
- Dynamic bids based on predicted conversion rate
- Bid adjustments: +/- percentage by audience segment
- Daily budget caps: Per campaign or total account
- Bid type: CPC (cost per click) typical, CPM available

#### Audience Targeting

**Retailer First-Party Data**:
- Purchase history (if brand owns on-site data)
- Email engagement (click-through rate, open rate)
- Recency (time since last purchase)
- Frequency (number of purchases)
- Monetary value (customer LTV)

**Criteo Audiences**:
- Lookalike audiences (similar to high-value customers)
- Product interest audiences (browsed category on any retailer)
- In-market audiences (recent retail purchase in category)
- Competitor customer audiences (purchased from competitor)

#### Bidding Models

**CPC (Cost Per Click)**:
- Billing trigger: User clicks product link
- Typical range: €0.15-€0.50
- Criteo optimizes for clicks with highest conversion probability
- Default bidding model on Criteo

**CPM (Cost Per Thousand Impressions)**:
- Alternative to CPC
- Typical range: €1.00-€5.00
- Less common (CPC preferred by most advertisers)

**Dynamic Bid Adjustment**:
- Algorithm adjusts bids based on:
  - Time of day (evenings typically higher conversion)
  - User device (mobile vs desktop)
  - Product category (electronics higher bids)
  - Audience segment (high-value customers higher bids)
- Adjustment range: -50% to +100%

### 3.3 Display Advertising on Criteo Network

#### Campaign Types

**Retargeting Display**
- Audience: Users who visited advertiser's website
- Creative: Dynamic (pulls products they viewed)
- Placement: Publisher network across EU
- Duration: Up to 30 days lookback
- Goal: Drive return to retailer

**Awareness Display**
- Audience: Broad targeting (all users or interest-based)
- Creative: Static brand or product
- Placement: Premium publisher inventory
- Duration: Ongoing
- Goal: Build brand awareness

#### Dynamic Display Creative

**Personalization**:
- Shows products user recently viewed
- Recommends similar products
- Shows best-sellers in category
- Includes discount if applicable
- Real-time updates based on current prices

**Format Variations**:
- Single product (300x250, 728x90)
- Multi-product carousel (728x90 minimum)
- Vertical carousel (300x600)
- Video carousel (landscape format)

**Performance Benefits**:
- Dynamic CTR: 0.4-0.8%
- Static CTR: 0.1-0.3%
- Lift: 3-5x higher engagement
- Conversion rate: 0.5-1.5% (vs 0.1-0.3% static)

### 3.4 Audience Targeting and Segmentation

#### First-Party Data Utilization

**Upload Methods**:
1. **Email list matching**
   - Upload email addresses
   - Criteo matches to retail networks
   - Match rate: 50-70% typical
   - Timeline: 24-48 hours

2. **Retailer transaction data**
   - Partner retailers share customer purchase data
   - Transaction history, recency, frequency, value
   - Privacy-compliant (anonymized, consented)
   - Updates: Daily or weekly

3. **Website pixel**
   - JavaScript pixel tracks visitor behavior
   - Events: Page view, add-to-cart, purchase
   - Historical data: Up to 540 days
   - Real-time updates

#### Segmentation Strategies

**RFM Segmentation**:
- **R**ecency: Time since last purchase
  - 0-30 days: "Hot" customers
  - 31-90 days: "Warm" customers
  - 90+ days: "Cold" customers
- **F**requency: Purchase count
  - 1 purchase: One-time buyers
  - 2-5 purchases: Repeat customers
  - 5+ purchases: Loyal customers
- **M**onetary: Customer lifetime value
  - Low: <€50
  - Medium: €50-€200
  - High: >€200

**Performance Impact**:
- Hot + High-value: 3-5x baseline conversion
- Warm + Medium: 1.5-2x baseline
- Cold + Low: 0.5x baseline

**Lookalike Audiences**:
- Criteo identifies users similar to best customers
- Scale: 5-10x seed audience
- Performance: 20-30% vs seed audience
- Timing: 2-3 weeks to build sufficient scale

### 3.5 Campaign Setup and Reporting

#### Performance Metrics

**Standard KPIs**:
- Impressions: Number of ad impressions
- Clicks: Users clicking ad
- CTR: Click-through rate
- Spend: Total cost
- CPC: Cost per click
- Conversions: Purchase transactions
- Conversion rate: Conversions/clicks
- ROAS: Revenue generated / ad spend
- CAC: Cost per acquisition

#### Attribution Reporting

**Click Attribution**:
- Last-click attribution (default)
- 30-day attribution window
- Cross-device matching (email-based)

**View-Through Attribution**:
- Conversion credit for viewed ads
- Even if user doesn't click
- 30-day attribution window
- Used for awareness campaign measurement

#### Advanced Reporting

**Audience Performance**:
- Breakdown by customer segment
- Show which audiences most profitable
- Enable/disable by performance

**Product Performance**:
- Which products drive conversions
- Which have highest ROI
- Identify slow movers

**Publisher Network Performance**:
- Which publisher sites drive conversions
- CPM and CPC by partner
- Viewability and engagement rates

## Section 4: Retail Media Networks in the European Market

### 4.1 RMN Definition and Strategic Importance

#### What is a Retail Media Network?

A Retail Media Network (RMN) is an advertising platform operated by retailers, enabling brands to purchase advertising space on that retailer's digital properties and external publisher networks using retailer-supplied first-party data.

**Key Components**:
1. **Owned media**: Ads on retailer website and mobile app
2. **Earned media**: Ratings, reviews, sponsored content
3. **Paid media**: Advertising inventory for brands
4. **First-party data**: Customer purchase history, behavior
5. **Measurement**: Attribution, ROI, customer analytics

**Strategic Value for Retailers**:
- New revenue stream (20-30% of digital ad spend)
- Customer data monetization
- Reduced dependency on Google/Facebook
- Direct brand relationship
- Competitive advantage vs pure e-commerce

**Strategic Value for Brands**:
- Direct access to purchase-intent audience
- First-party data (more privacy-compliant than third-party)
- Attribution to retail sales
- High conversion rates (2-5% vs 0.5% for display)
- Cross-retailer audience insights (if multiple RMN partners)

### 4.2 Major Retail Media Networks in Europe

#### Amazon Retail Media

**Coverage**:
- Active in: UK, Germany, France, Italy, Spain, Netherlands, Sweden, Poland
- Inventory: 10+ billion daily impressions across marketplaces
- Audience size: 150+ million unique users monthly

**Advertising Options**:
- Sponsored Products (keyword-based search ads)
- Sponsored Brands (multi-product ads)
- Sponsored Display (audience-targeted, retargeting)
- Amazon DSP (premium programmatic buying)
- Video advertising (Sponsored Brands Video)

**Publisher Partners**:
- Amazon.com properties (product pages, search, homepage)
- Prime Video (video inventory)
- Third-party publishers (Criteo network partners)

**Performance Benchmarks**:
- Sponsored Products: 2-4% conversion rate
- ROAS: 2.5-5x for mature campaigns
- ACoS: 20-40% (average cost of sale)
- Average order value: €40-€100

#### Criteo Retail Media

**Coverage**:
- Active in: 12+ European countries
- Unique feature: Agnostic, works with all retailers
- Partnerships: 600+ retail and publisher partners
- Inventory: 5+ billion daily impressions

**Retailers Using Criteo**:
- Carrefour (France, Spain, Belgium, Italy)
- FNAC (France, Belgium, Spain)
- Cdiscount (France)
- Bol.com (Netherlands)
- DPDgroup (logistics, EU-wide)
- Darty (electronics, France, Belgium)

**Performance Benchmarks**:
- Average CPC: €0.25-€0.40
- Conversion rate: 1-2%
- ROAS: 1.5-3x for products
- CAC: €1.00-€3.00

#### Marketplace-Specific Retail Media Networks

**Bol.com (Netherlands, Belgium)**
- Audience: 20+ million monthly users
- Ad formats: Sponsored Products, Display, Email
- Performance: 2-3% conversion rate
- Market share: 50%+ of Netherlands e-commerce
- Best for: FMCG, electronics, apparel

**Allegro (Poland)**
- Audience: 15+ million monthly users
- Ad formats: Sponsored products, banner ads
- Performance: 1.5-2.5% conversion rate
- Pricing: CPC average €0.10-€0.20 (lower than Amazon)
- Best for: Polish-speaking brands, cross-border sellers

**Cdiscount (France)**
- Audience: 8+ million monthly users
- Ad formats: Sponsored products, display
- Performance: 2-3% conversion rate
- Best for: Consumer electronics, appliances

**Other Platforms**:
- Carrefour Direct (France)
- Media Markt (electronics, Germany, Austria)
- Douglas (beauty, Germany, EU-wide)
- Decathlon (sports, EU-wide)

### 4.3 Retail Media Network Comparison Matrix

**Performance Metrics**

| Network | Avg CPC | Conversion | ROAS | ACoS | Min Spend | Setup Time |
|---------|---------|----------|------|------|-----------|------------|
| Amazon RMN | €0.30-€0.50 | 2-4% | 2.5-5x | 20-40% | None | 1-2 days |
| Criteo | €0.25-€0.40 | 1-2% | 1.5-3x | 30-50% | None | 1-2 days |
| Bol.com | €0.20-€0.35 | 2-3% | 2-4x | 25-40% | None | 1-2 days |
| Allegro | €0.10-€0.20 | 1.5-2.5% | 2-3.5x | 30-45% | None | 2-3 days |
| Cdiscount | €0.15-€0.30 | 2-3% | 2-3.5x | 25-40% | None | 2-3 days |

**Audience and Inventory**

| Network | Monthly Users | Inventory | Data Quality | Attribution |
|---------|---------|---------|---------|----------|
| Amazon | 150M+ EU | 10B+ daily | 1st-party purchase | 30-day cookie |
| Criteo | 200M+ EU | 5B+ daily | 1st-party transaction | 30-day cookie |
| Bol.com | 20M NL/BE | 500M daily | 1st-party transaction | 30-day cookie |
| Allegro | 15M Poland | 300M daily | 1st-party transaction | 30-day cookie |
| Cdiscount | 8M France | 200M daily | 1st-party transaction | 30-day cookie |

**Targeting Capabilities**

| Network | In-Market | Audience | Retargeting | Lookalike | Custom | Email |
|---------|---------|---------|---------|---------|---------|----------|
| Amazon | Yes | Yes | Yes | Yes | Limited | No |
| Criteo | Yes | Yes | Yes | Yes | Yes | Yes |
| Bol.com | Limited | Limited | Yes | Yes | No | No |
| Allegro | Limited | Limited | Limited | No | No | No |
| Cdiscount | Limited | Limited | Limited | No | No | No |

### 4.4 Self-Serve vs Managed Service Models

#### Self-Serve Model

**Characteristics**:
- Direct platform access
- Real-time campaign management
- Self-service reporting
- No minimum spend requirement
- Minimal support (help center, email)

**Advantages**:
- Full control over strategy
- Immediate changes and optimizations
- No service fees
- Transparent bidding and pricing
- Fast iteration on creative

**Disadvantages**:
- Requires in-house expertise
- Time-intensive (5-10 hours/week per campaign)
- Higher learning curve
- Limited platform-specific best practices
- No strategic guidance

**Best for**:
- Experienced media buyers
- High-volume advertisers
- Technical teams
- Agencies managing multiple clients

**Platforms Offering Self-Serve**:
- Amazon (all solutions)
- Criteo (all solutions)
- Bol.com (limited)
- Allegro (limited)

#### Managed Service Model

**Characteristics**:
- Dedicated account manager
- Strategic consulting
- Campaign optimization by platform expert
- Minimum spend: €5,000-€25,000/month
- Service fee: 10-20% of media spend

**Advantages**:
- Expert platform knowledge
- Strategic recommendations
- Campaign optimization by specialists
- Access to best practices
- Regular reporting and reviews
- Quick ramp-up time

**Disadvantages**:
- Higher cost (10-20% service fee)
- Less direct control
- Slower decision-making
- Limited customization
- Potential conflict of interest (drive more spend)

**Best for**:
- Brands new to RMN advertising
- Enterprise with large budgets
- Complex multi-market campaigns
- Limited in-house expertise
- Strategic partnerships

**Platforms Offering Managed Service**:
- Amazon (DSP managed service)
- Criteo (managed service available)
- Bol.com (agency partnerships only)

### 4.5 Retail Media Network Spending and ROI Trends

#### Market Growth

**European RMN Spending**:
- 2023: €3.5 billion total market
- 2024: €4.8 billion (estimated +37% YoY)
- 2025: €6.2 billion (estimated +30% YoY)
- CAGR 2023-2025: +32%

**By Market**:
- Germany: €1.2B (30% of EU)
- France: €0.9B (22% of EU)
- UK: €0.8B (18% of EU)
- Other EU: €1.1B (30% of EU)

**By Platform**:
- Amazon RMN: 45% market share
- Criteo: 20% market share
- Marketplace-specific (Bol, Allegro, etc.): 20% market share
- Other platforms: 15% market share

#### ROI Performance by Segment

**Brand vs Product Sellers**:
- Brand-owned products: ROAS 2.5-4x (higher margins)
- Resellers: ROAS 1.5-2.5x (lower margins)
- Difference: Brand authorization and pricing control

**High-Margin vs Low-Margin Categories**:
- High-margin (electronics, luxury): ROAS 3-5x
- Medium-margin (apparel, home): ROAS 2-3x
- Low-margin (FMCG, food): ROAS 1-2x

**Campaign Stage**:
- New product launch: ROAS 1.5-2x (testing phase)
- Scaling: ROAS 2-3x (optimization in progress)
- Mature: ROAS 2.5-4x (fully optimized)

#### Attribution and Incrementality

**Challenged Metrics**:
- Last-click attribution: Overstates impact (customer already intent)
- View-through attribution: Inflates upper-funnel value
- True incrementality: Only 40-60% of RMN sales are incremental

**Incrementality Testing Methods**:
1. **Geographic holdouts**: Run campaign in 8 regions, pause in 2
   - Measure sales lift in active regions vs holdouts
   - True incremental effect: Difference

2. **Time-based tests**: Pause campaign for 1 week
   - Compare week-off sales to week-on
   - True incremental impact

3. **Audience holdouts**: Exclude random sample
   - Compare excluded vs included groups
   - Most statistically rigorous

**Expected Incrementality**:
- New customer acquisition: 60-80% incremental
- Repeat purchase (existing): 30-50% incremental
- Conversion acceleration: 20-40% incremental

### 4.6 RMN Selection Framework and Evaluation Criteria

#### Priority Decision Matrix

**Evaluation Factors**:

1. **Audience Reach**
   - How many customers can reach on platform
   - Market coverage in target countries
   - Audience growth trends
   - Score: Percentage of target market covered

2. **Audience Quality**
   - First-party data depth (purchase history, frequency)
   - Targeting capabilities (in-market, lookalike, etc.)
   - Data freshness (real-time vs daily updates)
   - Score: Conversion rate achievable

3. **Cost Efficiency**
   - Average CPC or CPM
   - Conversion rate (cost per conversion)
   - ROI potential given margins
   - Score: ROAS potential

4. **Ease of Use**
   - Platform usability
   - Setup time
   - Reporting quality
   - Support availability
   - Score: Internal resource requirement

5. **Strategic Fit**
   - Category focus of platform
   - Competitive environment
   - Brand alignment
   - Growth trajectory
   - Score: Long-term partnership potential

#### Prioritization by Business Model

**For Amazon/Marketplace Sellers**:
1. Amazon RMN (primary channel)
2. Criteo (scaling)
3. Marketplace-specific (secondary)

**For Pure-Play E-commerce Brands**:
1. Criteo (neutral platform)
2. Amazon RMN (if selling on Amazon)
3. Marketplace platforms (geographic expansion)

**For Omnichannel Retailers**:
1. Own retail media network (if available)
2. Criteo (partner with all retailers)
3. Marketplace platforms (extended reach)

**For Agencies**:
1. Criteo (agnostic, supports all clients)
2. Amazon RMN (largest opportunity)
3. Marketplace platforms (geographic specialists)

## Section 5: Video Advertising on Marketplaces

### 5.1 Video Advertising Formats and Platforms

#### Sponsored Brands Video on Amazon

**Campaign Overview**:
- Format: Video ads in Amazon search results and product pages
- Inventory: High-intent shopping audience
- Duration: 6-60 seconds
- Creative: Uploaded MP4 or via Advertising Studio template

**Placement Locations**:
1. **Amazon search results**: Above and beside organic listings
   - Position: Premium, high-visibility
   - Reach: 500M+ monthly searches
   - Performance: 1.5-2x higher CTR vs static

2. **Product pages**: In Sponsored Display section
   - Position: Right sidebar, below title
   - Reach: 1B+ monthly product page views
   - Performance: High engagement from interested shoppers

3. **Amazon.com homepage**: Top banner placement
   - Position: Premium visibility
   - Reach: 100M+ daily visitors
   - Performance: Awareness-focused

4. **Mobile app**: In-app video placements
   - Position: Native within feed
   - Reach: 60M+ monthly mobile app users
   - Performance: Higher engagement on mobile

**Audience Targeting**:
- In-market audiences (research phase)
- Lifestyle audiences (interest-based)
- Competitor audiences (brand switching)
- Custom audiences (email, website pixel)
- Lookalike audiences (similar to best customers)

**Bidding Models**:
- CPC (Cost Per Click) - primary
- CPM (Cost Per Thousand Impressions) - alternative
- Dynamic bidding optimization available

#### OTT and Streaming TV Advertising

**What is OTT?**
Over-The-Top video advertising: Video ads served on streaming platforms (not traditional TV) such as Netflix, Prime Video, Disney+, Hulu, YouTube.

**Amazon Prime Video**
- Audience: 150M+ global subscribers
- Ad placement: Mid-roll ads within content (if included in plan)
- Format: 15, 30, 60 second video
- Targeting: Contextual (show category), audience (Criteo, Epsilon)
- Performance: High completion rate (70%+)
- Cost: Premium pricing (€5-€15 CPM)

**Third-Party OTT Platforms**
- YouTube (product ads, TrueView)
- Hulu (in-stream ads)
- Peacock (NBC platform)
- RTBF Auvio (Belgian public broadcaster)
- ITV Hub (UK streaming)

**OTT Advantages**:
- Premium content environment (brand safety)
- High engagement (70%+ watch rate)
- Viewability guaranteed (100% in-view)
- Cross-device reaching (TV, mobile, tablet)
- Frequency capping (avoid over-exposure)

**OTT Disadvantages**:
- High cost (premium pricing)
- Limited targeting options (content-based)
- Longer sales cycle (brand awareness, not immediate conversion)
- Inventory limited and seasonal

#### Twitch Advertising

**Platform Overview**:
- Live streaming platform for gaming and entertainment
- Audience: 140M+ monthly viewers globally
- EU audience: 25M+ monthly viewers
- Demographics: 13-40 years old, 70% male, high disposable income

**Ad Types**:
1. **Pre-roll ads** (before stream starts)
   - Duration: 15 or 30 seconds
   - Skipable after 5 seconds
   - CPM: €2-€8
   - Completion rate: 50-70%

2. **Mid-roll ads** (during stream)
   - Duration: 15 or 30 seconds
   - Non-skippable on most streams
   - CPM: €3-€12
   - Completion rate: 70-90%

3. **Takeover ads** (sponsorship of streamer)
   - Brand sponsorship of popular streamer
   - Streamer mentions product
   - Cost: Flat fee (€500-€5,000+)
   - Reach: 5,000-50,000 concurrent viewers

4. **In-stream brand integration**
   - Streamer uses product during broadcast
   - Organic integration (higher credibility)
   - Cost: Flat fee negotiated with streamer

**Audience Targeting**:
- Channel targeting (specific gaming category)
- Interest targeting (game genres)
- Content targeting (specific streamers)
- Contextual (type of content)
- Limited audience data (privacy-focused)

**Performance Benchmarks**:
- CTR: 0.3-0.8%
- Completion rate: 60-75%
- CPM: €3-€10
- Conversion: Low direct (brand awareness focus)

**Best for**:
- Gaming and tech products
- Esports sponsorships
- Youth audience targeting (18-35)
- Brand awareness in younger demographics
- Entertainment and lifestyle brands

### 5.2 Video Specifications and Technical Requirements

#### Sponsored Brands Video Specs

**File Format**:
- Container: MP4 (H.264 codec)
- Resolution: 640x360 minimum, 1280x720 recommended
- Aspect ratio: 16:9 (widescreen)
- Frame rate: 24-60 FPS
- Bitrate: 2-8 Mbps (depends on resolution)
- Audio: Required (stereo preferred)
- File size: Max 2GB (typically 50-500MB)

**Duration and Editing**:
- Minimum: 6 seconds
- Maximum: 60 seconds
- Recommended: 15-30 seconds (optimal engagement)
- Intro: 0-3 seconds (brand reveal)
- Product demo: 5-20 seconds
- Call-to-action: 2-5 seconds (end)

**Text and Graphics**:
- Logo: Appear for minimum 2 seconds
- Product: Visible for minimum 3 seconds
- CTA: Clear call-to-action ("Shop now")
- Legibility: Text readable at 360p resolution
- Subtitles: Recommended (15% increase in engagement)

**Audio**:
- Codec: AAC, MP3
- Sample rate: 44.1kHz minimum
- Volume: Normalized (-23 LUFS target)
- Music: Royalty-free or licensed
- Voice-over: Clear, professional

#### OTT Video Specifications

**OTT Standard Specs**:
- Resolution: 1920x1080 (Full HD)
- Aspect ratio: 16:9
- Frame rate: 23.976 or 25 FPS (varies by platform)
- Codec: H.264 or H.265
- Bitrate: 5-12 Mbps
- Audio: 2-channel stereo, AAC codec
- File size: Up to 5GB

**Variations by Platform**:
- **Prime Video**: 1920x1080, H.264, 8-12 Mbps
- **YouTube**: 1920x1080, VP9/H.264, any bitrate
- **Hulu**: 1920x1080, H.264, 6-10 Mbps
- **Peacock**: 1280x720 or 1920x1080

**Vertical Video (Mobile)**:
- Resolution: 1080x1920
- Aspect ratio: 9:16
- Frame rate: 25 or 30 FPS
- Bitrate: 3-6 Mbps
- Used on mobile apps, social media

#### Twitch Video Specifications

**Pre-roll/Mid-roll Ads**:
- Resolution: 1280x720 minimum
- Aspect ratio: 16:9
- Frame rate: 30 FPS
- Codec: H.264
- Audio: AAC, stereo
- Max file size: 2GB
- Duration: 15 or 30 seconds

**Quality Standards**:
- No pixelation or distortion
- Audio-visual sync (within 200ms)
- No excessive brightness changes (avoids viewer eye strain)
- No strobe/flickering (accessibility)

### 5.3 Video Creative Best Practices

#### Sponsored Brands Video

**Story Structure**:
1. **Hook (0-3 seconds)**
   - Grab attention immediately
   - Problem statement or emotion
   - Ask question to viewer
   - Examples:
     - "Is your kitchen outdated?"
     - "Tired of low-quality headphones?"
     - Show customer problem, frustrated face

2. **Product Introduction (3-8 seconds)**
   - Show product clearly
   - Highlight unique features
   - Demonstrate benefit
   - Use text to reinforce message
   - Example: "Introducing XYZ - the solution you need"

3. **Demonstration (8-20 seconds)**
   - Show product in use
   - Demonstrate benefit
   - Before/after comparison
   - Customer testimonial or use case
   - Multiple angles or features

4. **Value Proposition (20-25 seconds)**
   - Summarize key benefit
   - Price or discount (if applicable)
   - Warranty or guarantee
   - Comparison to alternatives
   - Example: "Save 40% vs competitors"

5. **Call-to-Action (25-30 seconds)**
   - Clear action: "Shop now", "Learn more"
   - Display Amazon product page link
   - Create urgency: "Limited time offer"
   - Brand logo and final frame

**Creative Best Practices**:
- **Pacing**: Not too fast, not too slow (match viewing context)
- **Audio**: Use voice-over, music, or both
- **Music**: Match brand personality and product
- **Subtitles**: Add for context and accessibility
- **Text overlay**: Large, readable, 2-3 words maximum per line
- **Color**: Brand colors, high contrast
- **Lighting**: Professional production quality
- **Multiple variations**: Test 2-3 creatives per campaign

#### OTT and Streaming Video

**High Production Quality**:
- Professional cinematography
- Color grading and post-production
- High-resolution assets (1080p minimum)
- Broadcast-quality audio
- Licensed music and voiceover

**Storytelling for Premium Audiences**:
- Longer narratives (30-60 seconds)
- Emotional connection
- Brand story, not just product
- Premium production values
- Target affluent audience (higher purchase intent)

**CTR Optimization**:
- Strong opening (first 3 seconds critical)
- Visual variety (cuts every 2-3 seconds)
- High contrast (watch on smaller screens)
- Text hierarchy (primary message largest)

#### Twitch Video

**Gamer-Focused Messaging**:
- Gaming/esports language and culture
- Streamer credibility and endorsements
- Community engagement
- Authentic, non-polished (authentic better than corporate)

**Performance Optimization**:
- Fast cuts and high energy
- Quick scene transitions
- High engagement opening
- Relevant to gaming content
- Community memes/references (cultural relevance)

**Authenticity**:
- Streamer testimonials (more trusted)
- Gaming footage (relevant to audience)
- Real-world use cases
- Avoid corporate/polished feel
- Engage with streamer community

### 5.4 Video Performance Benchmarks and Optimization

#### Performance Metrics by Video Type

**Sponsored Brands Video**

| Metric | Benchmark | Good | Excellent |
|--------|-----------|------|----------|
| View rate | 40-60% | 60-70% | 70%+ |
| CTR | 0.5-1.0% | 1-1.5% | 1.5%+ |
| CPV | €0.15-€0.30 | €0.12-€0.18 | €0.10 or less |
| Conversion rate | 1-2% | 2-3% | 3%+ |
| ROAS | 1.5-2.5x | 2.5-3.5x | 3.5x+ |

**OTT/Prime Video**

| Metric | Benchmark | Good | Excellent |
|--------|-----------|------|----------|
| Completion rate | 60-75% | 75-85% | 85%+ |
| View-through rate | 50-70% | 70-80% | 80%+ |
| CPM | €5-€12 | €4-€8 | €2-€4 |
| Brand lift | +15-30% | +30-50% | +50%+ |

**Twitch**

| Metric | Benchmark | Good | Excellent |
|--------|-----------|------|----------|
| Completion rate | 50-70% | 70-80% | 80%+ |
| CTR | 0.3-0.8% | 0.8-1.5% | 1.5%+ |
| CPM | €3-€10 | €2-€6 | €1-€3 |
| Engagement | Video comments/reactions | 50-200 per stream | 200+ per stream |

#### Optimization Tactics

**Hook Optimization**:
- Test different openings (question, statement, visual)
- Measure 3-second drop-off rate
- Target: Less than 30% drop-off at 3-second mark
- Iterate: New hook if >40% drop-off

**Duration Testing**:
- Test 15 vs 30 second versions
- Longer not always better (shorter = lower cost)
- 15-second: Lower cost, faster to completion
- 30-second: More storytelling, higher recall

**Creative Rotation**:
- Update creative every 4-6 weeks (avoid fatigue)
- Maintain winning creative (if still performing)
- Test 20% of budget on new variations
- Monitor creative fatigue metrics (declining CTR)

**Frequency Capping**:
- Limit impressions per user per day
- Video ads: 2-3 impressions per user per day (higher than display)
- Monitor: If frequency cap reached, increase if CTR maintained
- Avoid: >5 impressions per user (brand fatigue, negative perception)

**Audience Targeting Optimization**:
- Start broad (all in-market audience)
- Identify best-performing segments (after 500+ conversions)
- Allocate more budget to high-performing audiences
- Test lookalike audiences (2-3 week test, minimum)
- Exclude audiences: No strong performance (after 1000+ impressions)

## Section 6: Programmatic Display Strategy

### 6.1 Full-Funnel Campaign Framework

#### Awareness Stage (Top of Funnel)

**Objective**: Build brand awareness among target audience

**Audience**:
- Broad interest-based (lifestyle, hobby)
- Geographic (new market entry)
- Demographic (age, gender, income)
- No purchase behavior required

**Channels and Tactics**:
- OTT/Streaming TV (70% budget)
  - Brand storytelling (not product focus)
  - Premium inventory (high viewability)
  - Long-form (30-60 second)
  - High completion rates
  
- Twitch sponsorships (15% budget)
  - Target younger demographics
  - Cultural relevance
  - Streamer endorsements
  - Community engagement

- Display awareness (15% budget)
  - Broad publisher network
  - CPM bidding (not conversion focused)
  - Rich media formats (expandable, video)
  - Frequency capping (3-4 per user per week)

**Success Metrics**:
- Reach (% of target market exposed)
- Frequency (average impressions per user)
- Viewability (% of ad in-view)
- Brand lift (aided/unaided awareness survey)
- Share of voice (% of market impressions vs competitors)

**Budget Allocation**:
- Brand awareness campaigns: 20-30% of total advertising budget
- ROI expectation: Brand lift measurement, not direct conversion
- Long-term impact: Customer lifetime value increase

#### Consideration Stage (Middle of Funnel)

**Objective**: Educate interested prospects about product/brand

**Audience**:
- In-market audiences (active research)
- Website visitors (shown product interest)
- Category interest audiences (high intent)
- Competitor customer audiences (brand switching)

**Channels and Tactics**:
- Sponsored Brands Video (40% budget)
  - Product demonstration
  - Feature/benefit focus
  - Call-to-action (learn more)
  - Amazon search/product pages (high intent)
  
- Amazon DSP (25% budget)
  - In-market audiences
  - Lifestyle audiences
  - Dynamic creative (show product)
  - Retargeting (website visitors)
  
- Criteo Display (20% budget)
  - Retargeting (product viewers)
  - Dynamic creative (show viewed products)
  - Audience lookalikes
  
- Programmatic Display (15% budget)
  - Contextual targeting (relevant content)
  - Publisher network (educational content)
  - Native ads (less disruptive)

**Success Metrics**:
- Engagement rate (CTR, video watch rate)
- Cost per engagement
- Product page views
- Add-to-cart rate
- Email newsletter signups

**Budget Allocation**:
- Consideration campaigns: 40-50% of advertising budget
- ROI focus: Cost per engagement, conversion indicator
- Optimization: Bid up for high-engagement audiences

#### Conversion Stage (Bottom of Funnel)

**Objective**: Drive purchase conversion from warm audiences

**Audience**:
- Cart abandoners (highest intent)
- Past website visitors (purchase-ready)
- Email engaged (opened/clicked)
- Similar audiences (lookalikes of buyers)
- Competitors' customers (brand switching)

**Channels and Tactics**:
- Sponsored Products on Amazon (50% budget)
  - Keyword targeting (purchase intent)
  - In-market audiences
  - Automatic targeting (Amazon algorithm)
  - Lowest cost per conversion
  
- Amazon Sponsored Display (20% budget)
  - Cart abandoners (dynamic creative)
  - Product retargeting
  - High-frequency targeting
  - CPM optimized for conversion
  
- Criteo (15% budget)
  - Retailer partner retargeting
  - Dynamic product ads
  - Email retargeting
  
- Email retargeting (15% budget)
  - Newsletter subscribers
  - Cart abandoners
  - Promotional offers
  - High-frequency messages

**Success Metrics**:
- Conversion rate (% clicking ads that purchase)
- Cost per acquisition (CPA)
- ROAS (return on ad spend)
- ACoS (average cost of sale)
- Customer lifetime value

**Budget Allocation**:
- Conversion campaigns: 30-40% of advertising budget
- ROI focus: ROAS, ACoS, CPA
- Optimization: Bid aggressively for high-converting audiences

#### Loyalty Stage (Post-Purchase)

**Objective**: Drive repeat purchases and customer lifetime value

**Audience**:
- Past customers (repeat buyers)
- High-value customers (top 20% by spend)
- Email subscribers (engaged)
- Email non-openers (at-risk)

**Channels and Tactics**:
- Email marketing (50% budget)
  - Promotional offers
  - New product notifications
  - Loyalty program engagement
  - Highest ROI channel
  
- Amazon Sponsored Products (30% budget)
  - Upsell/cross-sell
  - Complementary product targeting
  - New arrivals
  
- Display retargeting (15% budget)
  - Frequency lower (avoid annoyance)
  - Offer-focused creative
  - Seasonal promotions
  
- SMS/Push notifications (5% budget)
  - Time-limited offers
  - Back-in-stock notifications
  - VIP exclusive access

**Success Metrics**:
- Repeat purchase rate
- Customer lifetime value (LTV)
- Order frequency
- Average order value increase
- Churn rate

**Budget Allocation**:
- Loyalty campaigns: 10-15% of advertising budget
- ROI expectation: 5-15x (mature customers cheaper to convert)
- Optimization: Focus on highest-value customer segments

### 6.2 Campaign Structure and Organization

#### Recommended Campaign Hierarchy

**Level 1: Account**
- Single platform (Amazon DSP, Criteo, etc.)
- One P&L responsibility
- Shared budget (can reallocate between campaigns)

**Level 2: Campaign**
- Group by funnel stage (awareness, consideration, conversion, loyalty)
- Separate budget allocation
- Different objectives and KPIs
- Allows independent optimization

**Level 3: Ad Group**
- Group by targeting (audience, keyword, placement)
- Separate bids
- Shared creative across group
- Granular performance tracking

**Example Structure for E-Commerce Brand**:
```
Account: MyBrand_Amazon_DSP
├─ Campaign: Awareness_Lifestyle
│  ├─ Ad Group: Tech_Enthusiasts_Display
│  ├─ Ad Group: Home_Improvement_Video
│  └─ Ad Group: Design_Professionals_Display
├─ Campaign: Consideration_InMarket
│  ├─ Ad Group: Category_Researchers_Display
│  ├─ Ad Group: Product_Page_Visitors_Display
│  └─ Ad Group: Competitor_Customers_Display
└─ Campaign: Conversion_Retargeting
   ├─ Ad Group: Cart_Abandoners
   ├─ Ad Group: 7Day_Visitors
   └─ Ad Group: 30Day_Visitors
```

#### Organizational Best Practices

**Audience Segmentation**:
- Separate campaigns for different audiences
- Allows different bid strategies
- Independent performance tracking
- Easier troubleshooting if one underperforms

**Product Grouping**:
- Separate campaigns for product lines (if >5 products)
- Different margins require different bid strategies
- Category-specific messaging
- Avoid budget conflicts

**Geographic Separation**:
- Separate by country (UK, Germany, France)
- Different CPM rates by market
- Language-specific creative
- Currency management

**Device Targeting**:
- Consider separate campaigns for mobile vs desktop
- Mobile: 30-50% lower CTR, lower CPC
- Different bid strategies justified
- Mobile-specific creative optimization

### 6.3 Attribution Models and Cross-Device Tracking

#### Attribution Model Types

**Last-Click Attribution**
- Credit: 100% to final touchpoint (converting click)
- Amazon DSP default
- Window: 30 days
- Limitation: Ignores awareness and consideration
- Use case: Sales reporting, ROAS calculation
- Bias: Overvalues lower-funnel channels

**First-Click Attribution**
- Credit: 100% to initial touchpoint (awareness ad)
- Opposite of last-click
- Window: 30 days (or longer, depends on platform)
- Limitation: Undervalues middle-funnel efforts
- Use case: Brand awareness ROI
- Bias: Overvalues upper-funnel channels

**Linear Attribution**
- Credit: Equal to all touchpoints
- Example: 3 touchpoints = 33% each
- Window: Full customer journey
- Limitation: Assumes all touchpoints equally important
- Use case: Multi-touch campaign analysis
- Bias: None (treats all equally)

**Time-Decay Attribution**
- Credit: More to recent touchpoints, less to older
- Formula: Recent touch = 40%, middle = 35%, first = 25% (example)
- Window: Full journey
- Limitation: Assumes recency = importance
- Use case: Mid-funnel evaluation
- Bias: Overvalues direct conversion driver

**Position-Based (Bathtub) Attribution**
- Credit: 40% first, 20% middle, 40% last
- Importance: First and last touches most valuable
- Window: Full journey
- Limitation: Ignores middle touches
- Use case: Balanced view
- Bias: Highlights upper and lower funnel

#### Cross-Device Attribution

**Amazon's Cross-Device Matching**:
- Method: Deterministic (customer login)
- Accuracy: 95%+ (if user signed in)
- Coverage: 150M+ identified users EU
- Device tracking: Mobile, desktop, tablet, TV
- Conversion window: 30 days

**Example Cross-Device Journey**:
```
Day 1 (Mobile): User sees Sponsored Display ad
↓
Day 2 (Desktop): User clicks Amazon search ad
↓
Day 3 (Tablet): User views product page
↓
Day 4 (Desktop): User completes purchase

Attribution: All touchpoints tracked and reported
Credit allocation depends on model (last-click, linear, etc.)
```

**Challenges**:
- Not all users signed in (reduces matching)
- Privacy regulations (GDPR) limit tracking
- Safari and Firefox limit tracking (ITP/ETP)
- Cross-origin limitations

### 6.4 Frequency Capping Strategy

#### Frequency Definition

**Frequency**: Number of times same user sees ad within time period
- Example: "3 impressions per user per day"
- Typical time window: Per day, per week, per month
- Platform default: Often 5 impressions per day

#### Frequency Capping by Channel

**Display Awareness Campaigns**:
- Recommended: 3-5 impressions per user per week
- Rationale: Build awareness without annoying
- Too low (<3): Insufficient frequency, wasted reach
- Too high (>7): Brand fatigue, negative perception
- Monitor: If increasing frequency increases conversions, still room to increase

**Retargeting Campaigns**:
- Recommended: 2-3 impressions per user per day
- Rationale: Hot audience (recent visitor), don't annoy
- Too low (<1): Not enough opportunity to convert
- Too high (>4): Cart abandonment fatigue
- Monitor: Avoid "stalking" perception

**Video Campaigns**:
- Recommended: 2-4 impressions per user per week
- Rationale: Video completion needs frequency
- Too low: Insufficient reach
- Too high: Video auto-play mutes perception of ads
- Monitor: Viewability and completion rates

**Email Campaigns**:
- Recommended: 1-2 per week for subscribers
- Rationale: Email fatigue real, but higher intent
- Too low: Forgotten
- Too high: Unsubscribe rate increases
- Monitor: Unsubscribe rate, open rate decline

#### Frequency Optimization

**Testing Frequency Levels**:
1. Set low frequency (2 impressions per day)
2. Monitor CTR and conversion rate
3. Gradually increase (add 1 impression)
4. Measure if performance improves or declines
5. Find optimal level (max performance)

**Signs Frequency is Too High**:
- Declining CTR despite same audience
- Negative sentiment in comments
- High unsubscribe/bounce rate
- Brand safety concerns

**Signs Frequency is Too Low**:
- Audience not fully exposed
- Leaving reach on table
- Low repeat exposure
- Worse for upper-funnel awareness

### 6.5 Viewability and Brand Safety

#### Viewability Standards

**IAB Standard Definition**:
- Desktop: 50% of pixels in-view for 1+ consecutive second
- Mobile: 50% of pixels in-view for 1+ consecutive second
- Video: 50% of pixels in-view for 2+ consecutive seconds

**Target Viewability Rates**:
- Benchmark: 70%+ viewability
- Good: 75%+ viewability
- Excellent: 80%+ viewability
- Poor: <50% viewability (avoid)

**Factors Affecting Viewability**:
1. **Placement**: Above-the-fold placements higher viewability
   - Above fold: 80%+
   - Below fold: 40-60%
   - Sidebar: 50-70%

2. **Format**: Different formats have different viewability
   - Video: 75%+ (auto-play advantage)
   - Rich media: 70%+ (interactive engages)
   - Standard display: 60-70% (depends on placement)

3. **Publisher**: Premium publishers have higher viewability
   - Premium publishers: 75%+ 
   - Mid-tier: 60-70%
   - Long-tail: 40-60%

4. **Timing**: Time of day affects engagement
   - Business hours: Higher (users at computer)
   - Evening: Lower (passive viewing)
   - Weekend: Lower (casual browsing)

#### Brand Safety Configurations

**Content Categories to Avoid**:
- Violence or gore
- Adult/NSFW content
- Political extremism
- Misinformation
- Hate speech
- Conspiracy theories

**Keyword Blacklisting**:
- Create negative keywords
- Prevent ads next to sensitive content
- Examples: "controversial news", "celebrity scandal"

**Publisher Whitelisting**:
- Specify approved publishers only
- Premium publishers for brand-safe inventory
- Exclude long-tail publishers
- Higher CPM but guaranteed quality

**Domain Category Exclusions**:
- Exclude categories (news, politics, etc.)
- Platform-level setting
- Can be too restrictive (may limit reach)
- Use for conservative brands

**Page-Level Monitoring**:
- Monitor after campaign launch
- Check sample of placements
- Review any concerning placements
- Pause if brand safety concerns

#### Measurement

**Metrics**:
- Viewable impressions (tracked separately)
- Viewability rate (viewable ÷ total impressions)
- Cost per viewable impression (vCPM model)

**Tools**:
- Amazon DSP reports (built-in)
- Third-party measurement (IAS, Moat, Integral Ad Science)
- Tag-based (Moat) vs Pixel-based (IAS)

### 6.6 Cross-Channel Programmatic Integration

#### Unified Reporting and Analytics

**Data Consolidation**:
- Aggregate metrics across platforms (DSP, Sponsored Ads, Criteo)
- Normalize for comparison (different metrics, definitions)
- Attribution across channels
- Customer journey view

**Platforms and Tools**:
- Amazon Advertising console (in-platform reporting)
- Criteo dashboard (platform-specific)
- Data warehouse (Snowflake, Redshift)
- BI tools (Tableau, Looker)
- Marketing analytics (Mixpanel, Amplitude)

**Key Metrics to Track**:
1. **Spend by channel**
   - Amazon: Sponsored Products, Sponsored Display, DSP
   - Criteo: CPC ads, Display, Email
   - Total: All channels combined

2. **Performance by channel**
   - Conversion rate
   - ROAS
   - CAC (cost per acquisition)
   - CPA (cost per action)

3. **Customer journey**
   - First-touch channel
   - Last-touch channel
   - Multi-touch path
   - Device progression

#### Budget Allocation Optimization

**Current State Analysis**:
- Current spend distribution
- Current ROAS by channel
- Current CAC by channel
- Benchmark vs industry

**Optimization Approach**:
1. **Identify high-performing channels** (ROAS >2x)
2. **Increase budget allocation** to winners
3. **Test underperforming channels** (confirm poor performance)
4. **Reallocate from losers** to winners
5. **Monitor for saturation** (increased bids when scaling)

**Example Reallocation**:
```
Current Allocation:
- Sponsored Products: €10,000 (40%) → ROAS 3x
- Criteo: €7,500 (30%) → ROAS 1.5x
- DSP: €5,000 (20%) → ROAS 2x
- Other: €2,500 (10%) → ROAS 0.8x

Optimized Allocation (increase high performers):
- Sponsored Products: €12,000 (43%) → ROAS 2.8x (slightly lower due to saturation)
- DSP: €7,000 (25%) → ROAS 2x (maintained)
- Criteo: €5,000 (18%) → ROAS 1.5x (maintained)
- Other: €1,000 (4%) → Discontinued
Result: Total ROAS improves from 2.23x to 2.35x
```

#### Seasonal Campaign Planning

**Peak Seasons**:
1. **Black Friday/Cyber Monday** (Nov-Dec)
   - Budget: 30-50% increase
   - Duration: 4-6 weeks (prep + event)
   - Focus: Email, retargeting, Sponsored Products
   - Expected ROAS: 1.5-2x (higher volume, lower margin)

2. **Back-to-School** (Aug-Sep)
   - Budget: 20-30% increase
   - Duration: 3-4 weeks
   - Focus: Awareness + conversion
   - Expected ROAS: 2-3x

3. **Christmas/Holiday** (Nov-Dec)
   - Budget: 40-60% increase
   - Duration: 6-8 weeks
   - Focus: Gift-giving, emotional messaging
   - Expected ROAS: 1.5-2.5x (gift economy higher volume)

4. **New Year's Resolution** (Jan-Feb)
   - Budget: 15-25% increase
   - Duration: 4-6 weeks
   - Focus: Fitness, wellness, self-improvement
   - Expected ROAS: 2-3x (high intent)

**Off-Season Optimization**:
- Lower budgets (no need to max out)
- Focus on audience building (pixel, email)
- Test new creative and positioning
- Optimize operations
- Build customer relationships
