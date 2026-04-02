# Listing Performance Metrics for European Marketplace Distribution
## A Comprehensive Reference Guide for Scale Operations

---

# TABLE OF CONTENTS

1. **CTR — Click-Through Rate (Comprehensive Analysis)**
2. **Conversion Rate (Comprehensive Analysis)**
3. **Session Metrics (Comprehensive Analysis)**
4. **Return Rate Analysis (Comprehensive)**
5. **Review & Rating Analytics (Comprehensive)**
6. **Share of Voice & Keyword Ranking (Comprehensive)**
7. **Buy Box Metrics (Comprehensive)**
8. **Profitability Metrics per Listing (Comprehensive)**
9. **KPI Dashboard Framework (Comprehensive)**
10. **Formulas & Calculations Reference**

---

## 1. CTR — CLICK-THROUGH RATE (COMPREHENSIVE ANALYSIS)

### 1.1 Mathematical Definitions & Variants

**Primary CTR Formula:**
```
CTR (%) = (Total Clicks on Listing / Total Impressions) × 100
```

**Alternative CTR Metrics:**

```
Adjusted CTR = (Clicks / Impressions) × (1 - Scroll_Through_Rate)
  [Accounts for passive scrolls without engagement intent]

Session-Based CTR = (Sessions with Click / Total Sessions) × 100
  [More granular; uses session-level data vs impression]

Position-Adjusted CTR = CTR / (1 + ln(Position))
  [Normalizes for ranking position; removes position bias]

Daily CTR Volatility = Std Dev of (Daily CTR values) / Mean CTR
  [Measures consistency; targets <20% for stable listings]
```

**Standard CTR Benchmarks by Category & Position:**

| Category | Pos 1-3 | Pos 4-8 | Pos 9-20 | Pos 21+ |
|----------|---------|---------|----------|----------|
| Electronics | 35-45% | 12-20% | 3-8% | <1% |
| Home & Garden | 28-38% | 10-18% | 2-6% | <0.5% |
| Fashion | 32-42% | 11-19% | 3-7% | <1% |
| Sports & Outdoors | 30-40% | 10-17% | 2-6% | <0.5% |
| Beauty & Personal Care | 25-35% | 8-15% | 2-5% | <0.5% |

**Traffic Quality Scoring:**
```
Traffic_Quality_Score = (Actual_CTR / Benchmark_CTR) × Engagement_Index
```
- Score >1.2: High-quality traffic, excellent keyword relevance
- Score 0.8-1.2: Normal traffic, expected performance
- Score <0.8: Low-quality traffic, keyword mismatch or saturation

### 1.2 CTR by Traffic Source & Channel

**Organic Search CTR Breakdown:**

| Source | Typical CTR | Trend | Notes |
|--------|------------|-------|-------|
| Branded Search | 65-85% | Stable | Lowest friction; repeat customers |
| Exact-Match Long-Tail | 35-55% | Variable | High intent; position-dependent |
| Broad Category Keywords | 8-18% | Declining | High competition; lower relevance |
| Related Product Keywords | 5-12% | Variable | Depends on content match |

**Paid Advertising CTR Benchmarks:**
- Amazon Sponsored Products: 0.8-2.5% (highly variable by category)
- Google Shopping: 1.2-3.5%
- Social Media (Facebook/Instagram): 0.5-2.0%

### 1.3 CTR Velocity & Trend Analysis

**Weekly CTR Growth Rate:**
```
CTR_Velocity = (CTR_Week_Current - CTR_Week_Previous) / CTR_Week_Previous × 100
```

**Alert Thresholds:**
- Positive velocity >8% per week: Excellent optimization progress
- Flat velocity 0-2% per week: Normal; steady state
- Negative velocity -3% to -5% per week: Ranking decline warning
- Negative velocity >5% per week: ranking decline alert

**CTR Elasticity to Position:**
```
Position_Elasticity = % Change in CTR / % Change in Position
```
- Typical range: 1.8-2.5 (1% position improvement = 1.8-2.5% CTR increase)
- High elasticity (>2.5): competitive keyword, easy wins possible
- Low elasticity (<1.5): saturated keyword, difficult to move

**CTR Quality Score:**
```
CTR_Quality = (Actual_CTR / Benchmark_CTR) × 100
```
- Score >110: Exceptional listing, optimization complete
- Score 90-110: Normal listing, targeted optimization recommended
- Score <90: Underperforming, immediate action needed

---

## 2. CONVERSION RATE (COMPREHENSIVE ANALYSIS)

### 2.1 Mathematical Definitions & Variants

**Primary Conversion Rate Formula:**
```
CR (%) = (Units Ordered / Sessions) × 100
```

**Alternative Definitions:**

```
Unit Session Percentage (USP) = Units Ordered / Sessions
  [Most commonly used metric; Amazon Business Reports metric]

Order Session Percentage (OSP) = Orders Placed / Sessions
  [Lower than USP due to multi-unit orders]
  Relationship: USP ≈ OSP × 1.15 to 1.35 (category dependent)

Revenue Conversion Rate = Revenue / Sessions
  [Used for profitability analysis; includes price per unit]

Visits-to-Purchase Rate = Orders Placed / Unique Visitors
  [Accounts for repeat visitors within session; typically 5-15% lower than CR]
```

**For standardization: Use USP (Unit Session Percentage) throughout this reference**

**Adjusted Conversion Rate (Accounting for Browser Friction):**
```