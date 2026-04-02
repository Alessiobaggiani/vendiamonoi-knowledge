# Part 1: Amazon DSP and Sponsored Display

## Section 1: Amazon DSP Comprehensive Guide

### 1.1 Platform Overview

Amazon's Demand-Side Platform (DSP) enables advertisers to programmatically purchase display, video, and audio advertising across Amazon-owned properties and premium third-party publisher networks. The platform serves both marketplace sellers and external brands, offering self-service and managed service options.

**Key Features**:
- First-party audience access through Amazon customer data
- Programmatic guaranteed and real-time bidding (RTB) capabilities
- Cross-device targeting and frequency management
- Advanced attribution and cross-device measurement
- Integration with Amazon Advertising ecosystem

### 1.2 Audience Types and Targeting

#### In-Market Audiences
Users actively researching or considering purchase categories. Amazon identifies intent through:
- Recent search activity on Amazon.com
- Shopping category exploration
- Wishlist additions
- Product page engagement
- Similar user behavior patterns

**Reach**: 50-70% of Amazon's user base across categories
**Performance**: 2-4x baseline CTR for relevant categories
**Best for**: Products with clear research phase (electronics, appliances, furniture)

#### Lifestyle Audiences
Users grouped by interests, demographics, and purchasing behaviors across all product categories.

**Segments Available**:
- Sports enthusiasts (fitness, outdoor recreation)
- Tech adopters (early adopters, gadget enthusiasts)
- Home improvement (DIY, interior design, gardening)
- Beauty and wellness (cosmetics, supplements, health)
- Entertainment (movies, gaming, music)
- Travel and leisure (vacation planning, travel gear)

**Reach**: 70-85% of user base
**Performance**: 1-2x baseline CTR
**Best for**: Awareness and consideration campaigns

#### Interest and Category-Based Audiences
Refined targeting based on specific categories and subcategories.

**Examples**:
- Books and media interests
- Kitchen and dining
- Fashion and accessories
- Pet supplies and care
- Office and school supplies

#### Customer Remarketing Lists
First-party audience segments:
- Website visitors (pixel-based retargeting)
- Past buyers of similar products
- Cart abandoners
- High-value customers (RFM segmentation)

#### Lookalike Audiences
Amazon's algorithm identifies users similar to:
- Your past customers
- Website visitors
- Engagement audiences
- Custom lists (email, phone, postal address)

**Performance Lift**: 20-40% improvement over generic targeting
**Scale**: 3-5x larger than seed audience

### 1.3 Ad Format Specifications

#### Standard Display Formats

**Leaderboard (728x90)**
- File size: Max 150KB
- Format: JPG, PNG, GIF
- Animation: Max 15 seconds, max 3 loops
- Use case: Top-of-page placements
- CTR: 0.3-0.5%

**Medium Rectangle (300x250)**
- File size: Max 150KB
- Format: JPG, PNG, GIF
- Animation: Max 15 seconds, max 3 loops
- Use case: In-content placements
- CTR: 0.5-0.8%

**Half Page (300x600)**
- File size: Max 150KB
- Format: JPG, PNG, GIF, HTML5
- Animation: Max 15 seconds, max 3 loops
- Use case: Sidebar and vertical placements
- CTR: 0.6-0.9%

**Mobile Leaderboard (320x50)**
- File size: Max 150KB
- Format: JPG, PNG, GIF
- Animation: Max 15 seconds, max 3 loops
- Use case: Mobile top-of-page
- CTR: 0.2-0.4%

**Mobile Medium Rectangle (300x250)**
- File size: Max 150KB
- Format: JPG, PNG, GIF, HTML5
- Animation: Max 15 seconds, max 3 loops
- Use case: Mobile in-content
- CTR: 0.4-0.7%

#### HTML5 Rich Media

**Specifications**:
- File size: Max 500KB (including all assets)
- Max initial load: 40KB
- Animation: Unlimited duration
- Expandable dimensions:
  - Standard expand: 728x90 → 728x410
  - Mobile expand: 300x250 → 480x360
- Supported features:
  - Video playback (H.264, WebM)
  - Animation and interactive elements
  - Form inputs and surveys
  - Click tracking and measurement

**Common Rich Media Types**:
- Video-based ads (30-60 second auto-play)
- Carousel/slider ads
- Interactive product showcases
- Countdown timers and promotions
- Expandable formats with rich content

#### Video Specifications

**In-stream Video (Display Network)**
- Dimensions: 640x360 minimum, up to 1920x1080
- Duration: 6-30 seconds recommended (up to 60 seconds)
- File size: Max 2GB
- Format: MP4, WebM, MOV
- Codec: H.264, VP8, VP9
- Audio: Required
- Frame rate: 24-60fps

**Outstream Video**
- Dimensions: 640x360 minimum
- Duration: 15-60 seconds
- Auto-play: Muted required
- File size: Max 2GB
- Format: MP4, WebM, MOV

### 1.4 Bidding Models

#### CPM (Cost Per Thousand Impressions)
- **Use case**: Brand awareness, reach maximization
- **Typical range**: €0.50-€3.00 per 1,000 impressions
- **Optimization**: System optimizes for viewability and engagement
- **Best for**: Budget-conscious campaigns, top-of-funnel

#### vCPM (Viewable CPM)
- **Billing trigger**: Only for impressions meeting viewability standards (50% visible for 1 second)
- **Typical range**: €0.70-€4.00 per 1,000 viewable impressions
- **Advantage**: Only pay for viewed impressions
- **Best for**: Quality-focused campaigns

#### CPV (Cost Per View)
- **Billing trigger**: Video plays for 3+ seconds or to completion (if under 3 seconds)
- **Typical range**: €0.10-€0.50 per view
- **For video campaigns only**
- **Best for**: Video awareness and engagement

#### CPC (Cost Per Click)
- **Billing trigger**: User clicks on ad
- **Typical range**: €0.20-€1.50 per click
- **Optimization**: System optimizes for high-quality clicks
- **Best for**: Traffic and conversion-focused campaigns

### 1.5 Targeting and Campaign Setup

#### Device and Placement Targeting

**Device Types**:
- Desktop (75% of impressions)
- Tablet (15% of impressions)
- Mobile phone (10% of impressions)

**Publisher Network**:
- Amazon.com properties (Amazon.com, Prime Video, Alexa, Kindle)
- Premium third-party publishers (90+% contextual relevance)
- Programmatic guaranteed inventory
- Real-time bidding inventory

#### Contextual Targeting

**Shopping Context**:
- Product categories users browsing
- Search keywords (when available)
- Category affinity
- Seasonal shopping intent

**Content Context**:
- Contextually relevant pages on publisher sites
- Brand-safe content categories
- Contextual keyword matching

#### Frequency Management

**Default Settings**:
- 5 impressions per user per day (marketplace campaigns)
- 3 impressions per user per day (brand awareness)
- 7 impressions per user per day (promotional)

**Optimization Strategies**:
- Increase frequency for high-engagement audiences
- Decrease frequency for remarketing (2-3 impressions)
- Vary by audience segment and campaign objective
- Monitor for frequency-related fatigue

### 1.6 Publisher Services and Network

#### Amazon Publisher Network

**First-Party Properties**:
- Amazon.com product pages and category pages (40% of impressions)
- Amazon Prime Video (25% of impressions)
- Amazon Alexa-enabled device content (5% of impressions)
- Amazon Kindle and reading apps (5% of impressions)

**Performance by Property**:
- Product pages: Highest intent, 2-3x CTR vs baseline
- Prime Video: High engagement, 1.5-2x CTR
- Homepage: Broad reach, baseline CTR

#### Third-Party Premium Publishers

**Top Categories**:
- News and media (40% of third-party inventory)
- Lifestyle and entertainment (30%)
- Shopping and commerce (15%)
- Technology and automotive (15%)

**Quality Standards**:
- 100% brand-safe content (verified)
- 70%+ viewability rates guaranteed
- Ad fraud detection and prevention
- No malware or suspicious content

**Examples of Partners**:
- Major news sites and publishers
- Entertainment and lifestyle platforms
- Technology review sites
- Sports and fitness websites

### 1.7 Reporting and Attribution

#### Standard Metrics

**Performance Metrics**:
- Impressions (number of times ad displayed)
- Clicks (user clicks on ad)
- CTR (click-through rate: clicks ÷ impressions)
- Cost (total spend)
- CPM (cost per 1,000 impressions)
- CPC (cost per click)

**Engagement Metrics**:
- Video views (for video ads)
- Video completion rate (% completing video)
- CPV (cost per view)
- Page scrolls (user scrolls to see ad)
- Hover rate (mouse hovers over ad)

#### Conversion Tracking

**Amazon Pixel Installation**:
- JavaScript tag for website conversion tracking
- Tracks: Product purchases, sign-ups, downloads, add-to-cart
- Requires HTTPS and proper implementation
- 30-day conversion window (default)

**Conversion Types**:
- Purchase conversion (transaction value tracked)
- Add-to-cart conversion
- Page view conversion
- Custom conversion (signup, download, etc.)

#### Attribution Models

**Last-Click Attribution**:
- Credit given to final touchpoint
- Default Amazon DSP model
- Limitations: Ignores awareness and consideration

**First-Click Attribution**:
- Credit to initial touchpoint
- Useful for awareness campaigns
- Shows early-funnel contribution

**Multi-Touch Attribution**:
- Amazon's linear model: Equal credit across touchpoints
- Time-decay model: More credit to recent interactions
- Position-based: 40-20-40 split (first, middle, last)

**Cross-Device Attribution**:
- Same user tracked across devices (Amazon customer ID)
- Matches users by email, phone, postal address
- Requires opt-in and privacy compliance

### 1.8 Cross-Device Tracking

#### Deterministic Matching

**Method**: Amazon customer login
- When user signs into Amazon account
- Email address and unique identifier matched
- 95%+ accuracy in matching
- Device graph includes:
  - Smartphones
  - Tablets
  - Desktop computers
  - Smart TV devices

**Reach**:
- 150+ million identified users across Europe
- 60-70% of Amazon customer base
- Higher in mature markets (UK, DE, FR)

#### Probabilistic Matching

**Method**: IP address, device ID, behavioral similarity
- Lower accuracy (80-85%) than deterministic
- Broader reach but less reliable
- Used to fill gaps in deterministic graph

#### Cross-Device Reporting

**Metrics Available**:
- Cross-device conversions
- Device path to conversion
- Sequential device touchpoints
- Same-day vs multi-day cross-device journeys

**Insights Example**:
- User sees mobile display ad (Day 1)
- Clicks desktop search ad (Day 2)
- Converts on tablet (Day 3)
- Full journey tracked and attributed

### 1.9 Managed Services vs Self-Service

#### Self-Service Platform

**Characteristics**:
- Direct platform access and control
- Real-time bidding and campaign management
- Audience selection and customization
- Budget and bidding control
- Reporting dashboard
- Minimum spend: None

**Best for**:
- Experienced media buyers
- Agencies and in-house teams
- High-frequency campaign optimization
- Technical implementations

**Support**: Help center, documentation, email support

#### Managed Service

**Characteristics**:
- Dedicated Amazon advertising specialist
- Campaign strategy and consulting
- Audience recommendations
- Optimization and reporting
- Minimum spend: €5,000-€25,000 per month
- Service fee: 10-20% of media spend

**Best for**:
- Enterprise brands
- First-time DSP users
- Complex multi-market campaigns
- Strategic partnerships

**Support**: Dedicated account manager, regular reviews, strategic planning

## Section 2: Sponsored Display Deep Dive

### 2.1 Platform Overview and Capabilities

Sponsored Display is Amazon's self-service display advertising solution, enabling advertisers to reach customers on Amazon.com and across external publisher networks. It differs from Amazon DSP in automation level, audience access, and pricing transparency.

**Key Differences from DSP**:

| Attribute | Sponsored Display | Amazon DSP |
|-----------|-------------------|------------|
| Setup | Simple, 10-minute setup | Complex, 2-4 week setup |
| Audiences | Predefined + custom audiences | Custom + first-party data |
| Pricing | CPM, vCPM, fixed budgets | CPM, vCPM, CPV, CPC |
| Reporting | Basic engagement metrics | Advanced attribution |
| Minimum spend | None | €5,000-€25,000 (managed) |
| Best for | SMBs, single-product ads | Enterprise, multi-product |

### 2.2 Audience Types and Segmentation

#### Targeting Audience

**All Shoppers**:
- Default audience (Amazon.com visitors)
- Reach: 100+ million unique monthly users in EU
- Performance: Baseline CTR 0.3-0.5%
- Use case: Broad awareness campaigns

**In-Market Audiences**:
- Users actively researching product categories
- Shopping signals: searches, views, additions to wishlist
- Performance: 2-4x baseline CTR
- Availability: Top 50 product categories
- Granularity: Category-level only (not specific products)

**Interest-Based Audiences**:
- Lifestyle and demographic segments
- Examples: "Tech enthusiasts", "Outdoor adventure", "Home cooking"
- Performance: 1.5-2.5x baseline CTR
- Number of audiences: 50+ available

**Custom Audiences**:
- First-party data from advertiser
- Email lists (uploaded and matched)
- Website visitors (pixel-based retargeting)
- Mobile app users (if applicable)
- Customer ID matching (Unified ID)

**Similar Audience (Lookalike)**:
- Amazon identifies similar shoppers
- Seed audience: Customers, website visitors, email list
- Reach: 2-5x seed audience size
- Performance: 15-30% lift vs untargeted

#### Retargeting Audiences

**Product Retargeting**:
- Users who viewed specific ASIN
- Time window: Up to 540 days
- Performance: 5-10x baseline CTR
- Creative requirement: Show product image

**Category Retargeting**:
- Users browsing specific categories
- Time window: Up to 540 days
- Performance: 2-3x baseline CTR
- Use case: Similar product recommendations

**Shopping Cart Abandoners**:
- Users who added product to cart but didn't purchase
- Time window: Up to 30 days
- Performance: 8-15x baseline CTR
- Use case: Conversion-focused

**Past Purchasers**:
- Users who bought product (same ASIN)
- Time window: Up to 540 days
- Performance: 3-5x baseline CTR
- Use case: Upsell, cross-sell, repeat purchase

### 2.3 Bidding Strategies and Models

#### Dynamic Bid Adjustment

**Automated Bidding**:
- Amazon's algorithm adjusts bids in real-time
- Goal: Achieve maximum conversions at target ACoS
- Learning period: 7-14 days for optimization
- Adjustment range: -50% to +100% of base bid

**Manual Bidding**:
- Advertiser sets fixed bid
- No automatic adjustment
- More control, less optimization
- Recommended for: Testing, specific price points

#### Pricing Models

**Cost Per Mille (CPM)**:
- Billing: Per 1,000 impressions
- Typical range: €0.50-€2.50
- Best for: Awareness, reach maximization
- No conversion guarantee
- Performance depends on audience quality

**Viewable CPM (vCPM)**:
- Billing: Per 1,000 viewable impressions
- Viewability standard: 50% visible for 1 second (IAB standard)
- Typical range: €0.75-€3.50
- Premium over CPM: 20-40%
- Best for: Quality-focused campaigns
- Only pay for viewed ads

**Cost Per Click (CPC) - New**:
- Billing: Per click on ad
- Typical range: €0.15-€1.00 per click
- Best for: Traffic, click-through optimization
- Automatic bid optimization available

### 2.4 Creative Specifications and Best Practices

#### Ad Format Requirements

**Product Ads**
- Format: JPG, PNG, or GIF
- Dimensions: 300x250, 728x90, 1200x628 (preferred)
- File size: Max 150KB
- Image: Must show product from catalog
- Requirement: ASIN (Amazon identifier) required
- Headline: Optional, auto-generated if blank
- Approval time: 1-2 business days

**Headline Requirements**:
- Length: Max 80 characters
- Content: Product benefit, offer, brand name
- Best practices:
  - Lead with benefit (e.g., "Up to 50% off")
  - Include key differentiator
  - Use action verbs ("Discover", "Get", "Shop")

**Brand or Collection Ads**
- Format: JPG, PNG, GIF, or HTML5
- Dimensions: 300x250, 728x90, custom sizes
- File size: Max 150KB
- Multiple headlines: Up to 3 options (A/B testing)
- Landing page: Custom URL required
- Approval time: 2-3 business days

#### Creative Best Practices

**Image Design**:
- Clean, minimal background (white or light colors)
- Product 50-70% of image area
- High-contrast graphics
- Avoid competitor logos
- Mobile-friendly design
- A/B test variations (color, layout, messaging)

**Text Messaging**:
- Lead with value proposition
- 3-5 words for headline (optimal)
- Avoid superlatives ("best", "greatest")
- Use promotional language (limited time, exclusive)
- Include numbers and specifics ("30% off", "Free shipping")
- Call-to-action ("Shop now", "Learn more")

**Technical Quality**:
- High-resolution images (min 72 DPI)
- Color space: RGB (no CMYK)
- No animation required but can improve engagement
- Ensure text is legible on all devices
- Test on mobile (smaller text becomes unreadable)

#### Creative Performance Benchmarks

**CTR by Industry**:
- Electronics: 0.6-1.2%
- Apparel and shoes: 0.4-0.8%
- Home and garden: 0.5-1.0%
- Sports and outdoors: 0.7-1.3%
- Beauty: 0.5-0.9%

**Headline Impact**:
- With promotional message: +30-50% CTR
- With limited-time offer: +40-60% CTR
- Generic/bland: -20-30% vs optimized

**Image Impact**:
- High-quality photography: +20-40% CTR
- Lifestyle context: +15-30% CTR
- Simple product shot: Baseline
- Cluttered backgrounds: -20-40% CTR

### 2.5 Placement Types and Channel Targeting

#### On-Amazon Placements

**Amazon.com Product Pages**
- Location: Sponsored Display section (right sidebar)
- Inventory: 1 billion+ daily page views
- Audience: High intent (shopping)
- Performance: 1.5-2x baseline CTR
- Best for: ASIN-based targeting
- Frequency: 1-3 per user per day

**Amazon.com Search Results**
- Location: Sidebar and below organic results
- Inventory: 500 million+ daily search sessions
- Audience: Very high intent
- Performance: 1-1.5x baseline CTR
- Best for: Keyword-related products
- Frequency: 1-2 per user per session

**Amazon.com Home Page**
- Location: Top banner, sidebar areas
- Inventory: 100+ million daily visitors
- Audience: Low intent (browsing)
- Performance: 0.3-0.5x baseline CTR
- Best for: Awareness and reach
- Frequency: 1 per user per day

#### Off-Amazon (Publisher Network)

**Premium Publisher Sites**
- Partner network: 200+ premium publishers
- Categories: News, sports, entertainment, lifestyle
- Inventory: 5 billion+ daily impressions
- Audience: Medium intent (content consumption)
- Performance: 0.2-0.4% CTR
- Best for: Awareness and reach extension
- Brand safety: 100% verified, contextually relevant

**YouTube and Video Sites** (Limited availability)
- In-stream placements
- Performance: Lower CTR but higher engagement
- Best for: Brand awareness, video products

### 2.6 Retargeting Strategy and Mechanics

#### Website Visitor Retargeting

**Pixel Installation**:
1. Generate Sponsored Display pixel code
2. Add to website header (before </head> tag)
3. Verify pixel firing on all pages
4. Exclude conversion page (avoid retargeting converters)
5. Testing: Check pixel in browser developer console

**Tracking Events**:
- Page view (default)
- Add-to-cart
- Purchase (exclude if want to avoid retargeting)
- Custom events (view specific category, spend time on page)

**Audience Building Timeline**:
- Minimum 100 users for targeting (safety threshold)
- Typical reach: 1,000-5,000 users after 30 days
- Stabilized after 90 days
- Recommended list size: 10,000+ for scale

#### Campaign Setup for Retargeting

**Time Windows**:
- 14-day window: Recent visitors (hot)
- 30-day window: Active researchers
- 90-day window: Casual browsers
- 180-day window: Past visitors (brand recall)

**Bid Strategy for Retargeting**:
- Increase bids 25-50% vs new visitor campaigns
- Retargeting has 5-10x higher conversion rate
- Higher bid justified by conversion lift
- Monitor cost per conversion (should be 40-60% lower)

**Frequency Capping for Retargeting**:
- Default: 5 impressions per user per day
- Retargeting: 2-3 impressions per user per day (to avoid annoyance)
- Monitor: If frequency increases CTR without increasing spend, frequency is not limiting

#### Segmentation by Behavior

**Recent Viewers (Last 14 days)**:
- Highest intent
- Highest conversion rate (5-10%)
- Bid: +40-50% premium
- Message: Reminder, overcome objections

**Frequent Visitors (15-30 days)**:
- Medium intent
- Conversion rate: 1-3%
- Bid: +25-35% premium
- Message: Building trust, social proof, reviews

**Inactive Visitors (30-90 days)**:
- Lower intent (but still interested)
- Conversion rate: 0.3-0.5%
- Bid: +10-20% premium
- Message: Promotional offer, new features, "Come back" messaging

### 2.7 Campaign Performance and Benchmarking

#### Expected Performance Metrics by Channel

**On-Amazon Sponsored Display Performance**

| Metric | Target Range | Variance |
|--------|-------------|----------|
| CTR | 0.5-1.0% | ±0.2% by audience |
| CPM | €0.75-€2.00 | ±€0.50 by segment |
| CPC | €0.15-€0.40 | ±€0.10 by audience |
| Conversion rate | 1.5-3.5% | ±1% by product |
| ROAS | 2.0-4.0x | ±0.5x by vertical |
| ACoS | 25-50% | ±10% by category |

**Publisher Network Performance**

| Metric | Target Range | Variance |
|--------|-------------|----------|
| CTR | 0.2-0.5% | ±0.15% by context |
| CPM | €0.50-€1.50 | ±€0.40 by publisher |
| CPC | €0.25-€0.60 | ±€0.20 by site |
| Conversion rate | 0.5-1.5% | ±0.5% by audience |
| ROAS | 1.0-2.0x | ±0.5x by vertical |
| Cost per conversion | €3-€8 | ±€2 by category |

**Retargeting Performance**

| Metric | Benchmark | High Performer | Notes |
|--------|-----------|-----------------|-------|
| CTR | 2-3% | 3-5% | 5-10x baseline |
| Conversion rate | 3-5% | 5-8% | Target repeat buyers |
| ROAS | 3-5x | 5-8x | Highest ROI channel |
| Cost per conversion | €0.80-€2.00 | €0.50-€1.00 | Lowest CPA channel |

#### Performance Optimization Tactics

**Bid Optimization**:
- Start at 50% of average CPC
- Increase bids by 10-15% if CPM below target
- Decrease bids if ROAS below 1.5x
- Monitor daily for first 2 weeks
- Then optimize weekly

**Audience Optimization**:
- Start with in-market audience (highest performance)
- Add similar audiences (scale phase)
- Add interest audiences (awareness phase)
- Exclude low-performing segments (after 200+ conversions)
- Test new audiences (2-week minimum)

**Creative Optimization**:
- Test 2-3 creative variations (A/B test)
- Run for minimum 2 weeks (7-14 days for quick test)
- Winner: 15%+ higher CTR
- Pause loser: Under-performing by 20%+
- Rotate winners (avoid fatigue after 4-6 weeks)

**Campaign Structure**:
- Separate by audience (in-market vs interest)
- Separate by product (ASIN)
- Separate by placement (on-amazon vs off-amazon)
- Allows granular optimization
- Easier troubleshooting if one underperforms

### 2.8 EU Availability and Regional Considerations

#### Geographic Coverage

**Available Markets** (EU):
- Germany (largest market)
- France (second largest)
- United Kingdom (post-Brexit)
- Italy
- Spain
- Netherlands
- Belgium
- Sweden
- Poland
- Austria

**Market Penetration**:
- Mature markets (UK, DE, FR): 90%+ inventory
- Growth markets (IT, ES, PL): 60-70% inventory
- Emerging markets (AT, BE): 40-50% inventory

#### Currency and Pricing

**Supported Currencies**:
- EUR (primary for EU campaigns)
- GBP (for UK-only campaigns)
- SEK (for Sweden)
- PLN (for Poland)

**Pricing Transparency**:
- CPM/vCPM bids shown in local currency
- No cross-currency hidden fees
- Monthly billing and statements

#### Regional Campaign Optimization

**Language Targeting**:
- Headlines and copy translated to local language
- German: Requires umlauts and formal tone
- French: Accents critical for professionalism
- Spanish: Formal vs informal (Castilian vs other regions)

**Cultural Considerations**:
- Avoid US-centric imagery
- Regional product preferences vary
- Seasonal campaigns aligned to local holidays
- Pricing displayed in local format (€1,99 not €1.99)

**Advertising Regulations**:
- No misleading claims (EU directive)
- Price comparison: Must show before/after clearly
- Disclaimers for health claims
- Accessibility: WCAG 2.1 AA standard for rich media
- GDPR: Cookie consent required for pixel tracking

#### Cross-Border Considerations

**Multi-Country Campaigns**:
- Separate campaigns per country (recommended)
- Single campaign with geographic targeting (simpler)
- Performance varies 30-50% between countries
- Germany typically 15-25% higher CPM than UK

**Content Localization**:
- Images: Check for cultural relevance
- Text: Full translation (not machine translation)
- Currency: Show local prices
- Measurement: GDPR affects cross-border tracking

**Tax and Compliance**:
- VAT handled by Amazon
- Ad spend subject to local regulations
- Data residency: Must comply with GDPR
- Right to erasure: Can delete audience data on request
