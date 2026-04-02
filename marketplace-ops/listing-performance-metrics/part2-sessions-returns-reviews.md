## 3. SESSION METRICS (COMPREHENSIVE ANALYSIS)

### 3.1 Definitions: Sessions vs Page Views vs Unique Visitors

**Session Definition:**
```
Session = Continuous series of pageviews by a single user
          (typically 30-minute timeout on Amazon and eBay)
```

**Key Distinctions:**

| Metric | Definition | Use Case |
|--------|-----------|----------|
| **Sessions** | User browsing activity within 30-min window | Overall traffic volume; visitor behavior |
| **Pageviews** | Individual page loads; multiple per session | Content engagement; navigation depth |
| **Unique Visitors** | Deduplicated users (daily/monthly basis) | Reach measurement; audience size |
| **Returning Visitors** | Users with 2+ sessions in period | Loyalty; repeat purchase intent |
| **New Visitors** | First-time visitors to listing | Acquisition effectiveness; top-of-funnel |

**Session Quality Metrics:**
```
Session_Quality_Index = (Session_Duration / Bounce_Rate) × Conversion_Rate
```
- Index >1.5: High-quality sessions, strong engagement
- Index 0.8-1.5: Normal session quality
- Index <0.8: Poor session quality, optimization needed

### 3.2 Session Duration & Depth Analysis

**Key Session Depth Metrics:**

```
Average Session Length = Total Pageviews / Total Sessions
  [Benchmark: 2.5-4.0 pages per session for product listings]

Session Depth Percentile Distribution:
  - 30% of sessions: 1 page (quick bounces)
  - 40% of sessions: 2-3 pages (moderate engagement)
  - 20% of sessions: 4-6 pages (detailed review)
  - 10% of sessions: 7+ pages (extensive research)
```

**Optimal Session Depth by Category:**

| Category | Target Pages/Session | Interpretation |
|----------|----------------------|-----------------|
| Electronics | 3.5-4.5 | Detailed specs review needed |
| Fashion | 2.5-3.5 | Visual browsing; color/size checking |
| Home & Garden | 3.0-4.0 | Moderate research phase |
| Beauty | 2.0-3.0 | Quick decision; trust-based |
| Sports & Outdoors | 3.5-4.5 | Technical details important |

**Time-on-Page Elasticity:**
```
PageView_Impact_on_CR = (Avg_Time_Page_4to6 - Avg_Time_Page_1to3) / Avg_Time_Page_1to3
```
- Elasticity 1.2-1.5: Strong correlation between engagement depth and conversion
- Elasticity 0.8-1.2: Moderate impact
- Elasticity <0.8: Time spent has minimal impact on conversion (price-driven)

### 3.3 Bounce Rate & Session Escape Patterns

**Bounce Rate Definition:**
```
Bounce_Rate (%) = (Single-Page Sessions / Total Sessions) × 100
```

**Benchmark Bounce Rates by Category:**

| Category | Healthy Range | Warning Level | Critical Level |
|----------|--------------|----------------|----------------|
| Electronics | 35-50% | >55% | >65% |
| Fashion | 40-55% | >60% | >70% |
| Home & Garden | 35-48% | >55% | >65% |
| Beauty | 45-60% | >65% | >75% |
| Sports & Outdoors | 40-52% | >58% | >68% |

**Bounce Rate by Traffic Source:**

```
Organic Search: 40-50% (intent-driven; lower bounce expected)
Direct Traffic: 25-35% (repeat customers; lowest bounce)
Paid Ads: 50-65% (broader targeting; higher bounce acceptable)
Social Media: 60-75% (lowest intent; highest bounce expected)
```

**Escape Rate vs Bounce Rate:**
```
Escape_Rate = (Sessions Exiting on Page / Sessions Viewing Page) × 100
  [More granular than bounce rate; isolates exit patterns]

Comparison:
- Bounce rate: Exit from first page only
- Escape rate: Exit from any page (better for multi-page analysis)
```

---

## 4. RETURN RATE ANALYSIS (COMPREHENSIVE)

### 4.1 Return Rate Definitions & Measurement

**Primary Return Rate Formula:**
```
Return_Rate (%) = (Units Returned / Units Ordered) × 100
```

**Return Rate Variants:**

```
Monetary Return Rate = (Revenue from Returns / Total Revenue) × 100
  [Accounts for price variance; typically 2-3% lower than unit RR]

Order-Level Return Rate = (Orders with ≥1 Return / Total Orders) × 100
  [Higher than unit RR; multiple units per order common]

Return_Velocity = (Units Returned in Week_1 / Units Ordered) × 100
  [Measures speed of returns; defects return faster than preference]
```

**Typical Return Rate Benchmarks by Category:**

| Category | Range | Notes |
|----------|-------|-------|
| Electronics | 3-7% | Defect-driven; higher velocity returns |
| Fashion | 15-35% | Fit/preference-driven; slower returns |
| Home & Garden | 4-10% | Mixed defect + fit issues |
| Beauty | 2-5% | Strict return policies; lower rates |
| Sports & Outdoors | 5-12% | Quality/durability-driven |

**Return Velocity Pattern:**
```
Week 1: 30-40% of total returns (defects; rapid identification)
Week 2-3: 40-50% of total returns (fit/preference issues)
Week 4+: 10-20% of total returns (delayed discovery; rare)

[Higher Week 1% = quality issues; Higher Week 2-3% = expectation mismatch]
```

### 4.2 Return Drivers & Root Cause Analysis

**Primary Return Drivers by Category:**

Electronics:
- Defective unit: 40-50%
- Wrong item received: 20-30%
- Functionality mismatch: 20-30%

Fashion:
- Fit/sizing: 60-70%
- Color mismatch vs photos: 15-20%
- Quality below expectations: 10-15%

Home & Garden:
- Defects: 35-45%
- Sizing/dimension mismatch: 30-40%
- Quality issues: 15-25%

**Return Impact on Profitability:**
```
Net_Profitability = Gross_Profit - (Return_Rate × COGS) - (Return_Rate × Logistics_Cost)

Example: $100 COGS product, 10% return rate, $8 return logistics:
Net Impact = -($100 × 0.10) - ($8 × 0.10) = -$10.80 per 10 units ordered
```

### 4.3 Return Rate Segmentation

**Return Rate by Customer Type:**

```
First-Time Buyers: 18-25% (high uncertainty; higher return rate)
Repeating Customers: 5-8% (lower return rate; higher confidence)
Brand/Category Familiar: 3-6% (lowest return rate; high confidence)
```

**Return Rate by Traffic Source:**

| Source | Typical RR | Reason |
|--------|-----------|--------|
| Organic Search | 6-10% | Moderate intent; mixed buyer types |
| Paid Ads | 12-18% | Broad targeting; lower intent match |
| Direct Traffic | 3-5% | Repeat customers; highest confidence |
| Social Media | 15-25% | Impulse purchases; high uncertainty |

**Return Rate Tracking Over Time:**
```
Monthly Return Rate Trend = (Current Month RR - Previous Month RR) / Previous Month RR × 100
```
- Trend >10% increase month-over-month: Quality issue investigation needed
- Trend 2-10% increase: Normal seasonal variation
- Trend <2%: Stable performance
- Trend negative: Improvement in quality or customer satisfaction

---

## 5. REVIEW & RATING ANALYTICS (COMPREHENSIVE)

### 5.1 Review Metrics Hierarchy

**Tier 1: Star Rating (1-5 Scale)**

```
Weighted_Average_Rating = Σ(Rating × Count) / Total_Reviews

Example: 100 reviews (50×5★, 30×4★, 15×3★, 3×2★, 2×1★)
Weighted Avg = (250 + 120 + 45 + 6 + 2) / 100 = 4.23 stars
```

**Rating Distribution Impact:**

| Metric | 5-Star | 4-Star | 3-Star | 2-Star | 1-Star | Impact on CR |
|--------|--------|--------|--------|--------|--------|---------------|
| Benchmark | 65-75% | 15-20% | 5-10% | 2-4% | 1-3% | Baseline (100%) |
| Excellent | >75% | 15-18% | 3-6% | 1-2% | <1% | +15-25% CR boost |
| Average | 60-70% | 18-25% | 5-10% | 3-5% | 1-3% | -10-15% CR penalty |
| Poor | <50% | >25% | >10% | >5% | >3% | -40-60% CR penalty |

**Average Rating Benchmarks by Category:**

| Category | Healthy Avg | Warning Level | Critical Level |
|----------|-------------|----------------|----------------|
| Electronics | 4.2-4.5 | <4.0 | <3.5 |
| Fashion | 4.0-4.3 | <3.8 | <3.3 |
| Home & Garden | 4.1-4.4 | <3.9 | <3.4 |
| Beauty | 4.3-4.6 | <4.1 | <3.6 |
| Sports & Outdoors | 4.1-4.4 | <3.9 | <3.4 |

**Tier 2: Review Velocity**

```
Review_Velocity = Reviews in Period / Units Sold in Period × 100
  [Typical range: 2-8% of units purchased leave reviews]

Review_Conversion_Rate = Reviews / Total Possible Reviews
  [Influenced by email quality, timing, incentive structure]
```

**Healthy Review Velocity by Volume:**

| Monthly Units | Target Reviews | Target Velocity |
|----------------|-----------------|------------------|
| <100 | 2-5 | 2-5% |
| 100-500 | 8-25 | 3-6% |
| 500-2000 | 25-100 | 4-7% |
| 2000+ | 100-200 | 5-8% |

**Tier 3: Review Sentiment Scoring**

```
Positive_Sentiment_Score = (5★ + 4★ - 2★ - 1★) / Total_Reviews × 100
  [Accounts for strength of positive vs negative reviews]

Negative_Sentiment_Score = (2★ + 1★) / Total_Reviews × 100
```

### 5.2 Review Content Analysis

**Common Positive Keywords & Frequency:**

| Keyword | Frequency | Impact |
|---------|-----------|--------|
| "arrived quickly" | 15-25% | +8-12% CR boost |
| "exactly as described" | 20-30% | +10-15% CR boost |
| "high quality" | 25-35% | +12-18% CR boost |
| "great value" | 15-25% | +8-12% CR boost |
| "recommend" | 30-40% | +15-20% CR boost |

**Common Negative Keywords & Frequency:**

| Keyword | Frequency | Impact |
|---------|-----------|--------|
| "defective/broken" | 20-30% | -15-25% CR reduction |
| "not as described" | 15-25% | -12-20% CR reduction |
| "poor quality" | 20-30% | -15-25% CR reduction |
| "arrived damaged" | 10-20% | -10-18% CR reduction |
| "cheap/flimsy" | 15-25% | -12-20% CR reduction |

### 5.3 Review Metrics by Product Tier

**Premium Products (High Price, >$500):**
- Typical Rating: 4.3-4.6 stars
- Review Velocity: 4-6%
- Positive Keywords Focus: quality, durability, precision
- Conversion Impact: Strong (±20-30% swings)

**Mid-Market Products ($50-500):**
- Typical Rating: 4.0-4.3 stars
- Review Velocity: 4-7%
- Positive Keywords Focus: value, reliability, satisfaction
- Conversion Impact: Moderate-to-Strong (±15-25% swings)

**Budget Products (<$50):**
- Typical Rating: 3.8-4.1 stars
- Review Velocity: 2-4%
- Positive Keywords Focus: usable, works fine, price
- Conversion Impact: Moderate (±10-15% swings)
