# INVENTORY MANAGEMENT & PLANNING FOR EUROPEAN MARKETPLACE DISTRIBUTION

**Version:** 2.0 | **Last Updated:** April 2026 | **Scope:** EU Marketplaces (Amazon, eBay, Shopify, Cdiscount, Zalando, Fruugo, etc.)

---

## 1. DEMAND FORECASTING (250+ lines)

### 1.1 Forecasting Methods Comparison

| Method | Formula | Lead Time | Accuracy | Data Req. | Complexity |
|--------|---------|-----------|----------|-----------|----------|
| **Simple Moving Average (SMA)** | F = (D₁ + D₂ + ... + Dₙ) / n | 1-2 weeks | 65-75% | 4-12 weeks | Low |
| **Weighted Moving Average** | F = (w₁D₁ + w₂D₂ + ... + wₙDₙ) / Σw | 1-2 weeks | 70-80% | 4-12 weeks | Low |
| **Exponential Smoothing (SES)** | F = αD₍ₜ₋₁₎ + (1-α)F₍ₜ₋₁₎ | 1-2 weeks | 75-85% | 6+ weeks | Medium |
| **Holt-Winters (Additive)** | F = (L + mT + S) | 2-4 weeks | 80-90% | 12+ weeks | High |
| **ARIMA (p,d,q)** | Φ(B)(1-B)ᵈY = θ(B)ε | 2-6 weeks | 80-88% | 24+ weeks | High |
| **Prophet (Facebook)** | Y = g(t) + s(t) + h(t) + ε | 1-4 weeks | 85-92% | 12+ weeks | High |
| **Machine Learning (XGBoost)** | Gradient boosting ensemble | 1-8 weeks | 88-95% | 100+ obs | Very High |
| **Regression with External** | Y = β₀ + β₁X₁ + β₂X₂ + ... | Variable | 75-90% | Market data | High |

### 1.2 Simple Moving Average (SMA)

**Formula:**
```
F(t) = (D(t-1) + D(t-2) + ... + D(t-n)) / n
```

**Example (4-week SMA for EU marketplace):**
- Week 1-4 sales: 250, 270, 285, 265 units
- F(Week 5) = (250 + 270 + 285 + 265) / 4 = **267.5 units**

**Advantages:**
- Simple calculation, immediate understanding
- Smooths minor fluctuations
- Baseline for comparison

**Disadvantages:**
- Ignores trends and seasonality
- All data points weighted equally
- Poor for fast-moving categories

---

### 1.3 Exponential Smoothing (SES)

**Formula:**
```
F(t) = αD(t-1) + (1-α)F(t-1)
Where: 0 < α < 1 (typically 0.1 to 0.3)
```

**Optimal α by category:**
- Stable categories (books, office supplies): α = 0.1-0.15
- Seasonal categories (fashion, sports): α = 0.2-0.3
- Volatile new launches: α = 0.3-0.5

**Example:**
- Previous forecast: 250 units
- Actual demand: 280 units
- α = 0.2
- F(new) = 0.2(280) + 0.8(250) = 56 + 200 = **256 units**

**Advantages:**
- Reacts faster to recent trends than SMA
- Requires minimal data storage
- Standard industrial method

---

### 1.4 Holt-Winters (Double Exponential Smoothing)

**Formulae:**
```
Level: L = α(D - S(t-s)) + (1-α)(L(t-1) + T(t-1))
Trend: T = β(L - L(t-1)) + (1-β)T(t-1)
Seasonal: S = γ(D - L) + (1-γ)S(t-s)
Forecast: F(t+m) = L + mT + S(t+m)

Where:
- α = level smoothing (0.1-0.3)
- β = trend smoothing (0.01-0.1)
- γ = seasonal smoothing (0.05-0.15)
- s = seasonal period (52 weeks, 12 months, etc.)
- m = forecast periods ahead
```

**Example (52-week seasonality, fashion category):**
- Current level (L) = 2,000 units/week
- Trend (T) = +50 units/week growth
- Seasonal index (S) = 1.2 (20% above baseline in summer)
- Forecast for 4 weeks ahead:
  - Week 1: 2,000 + 1(50) + 1.2S = ~2,340 units
  - Week 4: 2,000 + 4(50) + 1.2S = ~2,540 units

**Best for:**
- Categories with clear seasonal patterns (apparel, sports, toys)
- Established products with 12+ months history
- Accuracy: 80-92% MAPE

---

### 1.5 ARIMA (AutoRegressive Integrated Moving Average)

**Formula:**
```
ARIMA(p,d,q): Φ(B)(1-B)ᵈY = θ(B)ε

Where:
- p = AutoRegressive order (lag terms)
- d = Degree of differencing (1-2 for stationary)
- q = Moving Average order
- B = Backshift operator
- Φ(B) = AR coefficients
- θ(B) = MA coefficients
```

**Common configurations:**
- ARIMA(1,1,1): Simple trend + noise
- ARIMA(2,1,1): Moderate trend + seasonality
- SARIMA(1,1,1)(1,1,1,52): Seasonal (weekly pattern)

**Implementation steps:**
1. Check stationarity (ADF test, p < 0.05)
2. Difference if needed (d=1 or 2)
3. Plot ACF/PACF to identify p,q
4. Fit multiple models, compare AIC
5. Validate residuals (white noise test)

**Example Python (pseudocode):**
```python
from statsmodels.tsa.arima.model import ARIMA
model = ARIMA(sales_data, order=(1,1,1))
fit = model.fit()
forecast = fit.get_forecast(steps=12)
```

**Accuracy: 80-88% MAPE** (better for stable products)

---

### 1.6 Machine Learning Methods

#### A. Prophet (Facebook's Forecasting Library)

**Formula:**
```
Y(t) = g(t) + s(t) + h(t) + ε(t)

Where:
- g(t) = piecewise linear growth trend
- s(t) = seasonal component (Fourier series)
- h(t) = holiday effects
- ε(t) = error term
```

**Configuration for EU marketplace:**
```python
from fbprophet import Prophet
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=True,
    daily_seasonality=False,
    interval_width=0.95,
    changepoint_prior_scale=0.05
)
model.fit(df)  # df: columns 'ds' (date), 'y' (demand)
forecast = model.make_future_dataframe(periods=90)
```

**Advantages:**
- Handles missing data and outliers
- Captures multiple seasonalities
- Holiday effects (Easter, Black Friday, Christmas)
- Interpretable components

**Accuracy: 85-92% MAPE**

#### B. XGBoost (Gradient Boosting)

**Setup:**
```python
import xgboost as xgb
features = [
    'lag_7', 'lag_14', 'lag_30',  # Previous demand
    'day_of_week', 'week_of_year',  # Calendar features
    'is_promotion', 'price_index',  # Business features
    'competitor_price', 'stock_level'  # External
]
model = xgb.XGBRegressor(
    max_depth=6,
    learning_rate=0.1,
    n_estimators=500,
    subsample=0.8,
    objective='reg:squarederror'
)
```

**Feature engineering:**
- Lagged demand (7, 14, 30, 90 days)
- Rolling statistics (mean, std, min, max)
- Time-based features (weekday, month, quarter, holidays)
- Marketplace-specific (promo flags, price changes, reviews)
- Seasonal indices (Black Friday, Easter, summer, Christmas)

**Accuracy: 88-95% MAPE** (best overall for EU marketplaces)

---

### 1.7 Seasonality & Trend Decomposition

**Additive Model:**
```
D(t) = L(t) + T(t) + S(t) + R(t)

Where:
- L(t) = Level (baseline demand)
- T(t) = Trend component
- S(t) = Seasonal component
- R(t) = Residual/random
```

**Multiplicative Model:**
```
D(t) = L(t) × T(t) × S(t) × R(t)
```

**Seasonal indices by EU marketplace category:**

| Category | Peak Month | Peak Index | Low Month | Low Index |
|----------|-----------|-----------|-----------|----------|
| Apparel | August, December | 1.35-1.50 | February, July | 0.65-0.75 |
| Electronics | November, December | 1.60-1.80 | August, September | 0.55-0.70 |
| Home & Garden | April-May, September | 1.25-1.40 | January, August | 0.60-0.80 |
| Toys & Games | November-December | 2.00-2.50 | July, August | 0.40-0.60 |
| Sports & Outdoors | April-May, September-October | 1.30-1.50 | January, August | 0.65-0.85 |
| Beauty & Personal Care | November, December | 1.15-1.30 | July, August | 0.85-0.95 |
| Books | September, November-December | 1.20-1.35 | June, July | 0.75-0.90 |
| Furniture | January, August-September | 1.20-1.35 | May-July | 0.70-0.85 |

**Trend patterns (EU context):**
- Growth rate benchmarks: 15-30% YoY (mature), 50-150% YoY (new category)
- Decline rate benchmarks: 5-15% YoY (fashion), 20-40% YoY (tech obsolescence)

---

### 1.8 Forecast Accuracy Metrics

| Metric | Formula | Range | Interpretation |
|--------|---------|-------|----------------|
| **MAPE** (Mean Absolute % Error) | Σ|(D(t)-F(t))/D(t)|/n × 100 | 0-100% | <10% Excellent, <20% Good |
| **MAE** (Mean Absolute Error) | Σ|D(t)-F(t)|/n | Units | Absolute error in units |
| **RMSE** (Root Mean Sq Error) | √(Σ(D(t)-F(t))²/n) | Units | Penalizes large errors |
| **MASE** (Mean Abs Scaled Error) | MAE / SMA benchmark | Ratio | <1.0 beats naive forecast |
| **Bias** (Mean Error) | Σ(D(t)-F(t))/n | Units | >0 underforescast, <0 overforecast |
| **Theil's U** | √(Σ(D(t)-F(t))²) / √(Σ(D(t)-D(t-1))²) | 0-2 | <1 beats naive, >1 worse |

**Benchmark MAPE by product category (EU context):**

| Category | Best | Good | Acceptable | Poor |
|----------|------|------|-----------|------|
| Fast-moving staples | <5% | <8% | <12% | >15% |
| Established products | <8% | <12% | <18% | >25% |
| Seasonal fashion | <12% | <18% | <25% | >35% |
| Niche/specialty | <15% | <25% | <35% | >50% |
| New launches (0-3mo) | <30% | <50% | <70% | >80% |

**Forecast error tracking dashboard:**
```
Daily reconciliation:
- Actual vs Forecast (last 7, 14, 30 days)
- Cumulative error tracking
- By-SKU accuracy (top 20%, bottom 20%)
- By marketplace (Amazon DE, FR, IT, ES, PL, etc.)
- By category and brand
- Bias detection (systematic over/underforecast)
```

---

### 1.9 Demand Signals & External Factors

**Demand drivers monitored:**
- Price changes (own and competitor) → elasticity factor
- Promotional activities (banner, deal, Flash Sale) → lift factor
- Seasonal events (Black Friday, Easter, Summer) → calendar factor
- Marketing spend (PPC, influencer) → conversion correlation
- Review score/rating → trust/conversion impact
- Competitor inventory levels → share shift potential
- Supply disruptions → shortage risk
- Currency fluctuations → cross-border demand shift
- Holiday calendars (Easter varies 8 weeks, summer timing by country)

**Adjustment factors for European marketplaces:**

| Signal | Impact on Demand | Adjustment Method |
|--------|------------------|----------------|
| Price increase 10% | -8% to -15% | Multiply by elasticity coefficient |
| Price decrease 10% | +15% to +25% | Multiply by elasticity coefficient |
| Free shipping promo | +20% to +40% | Add lift percentage to baseline |
| Black Friday sale (Nov) | +150% to +300% | Seasonal index × promotion multiplier |
| New 5-star review surge | +5% to +15% | Rate of change in review score |
| Competitor stockout | +10% to +20% | Market share redistribution factor |
| Brexit/Import delays | -5% to -20% | Supply constraint multiplier |
| Currency devaluation 5% | +3% to +8% | Cross-border competitiveness factor |

---

### 1.10 Forecast Governance

**Forecast update cycle:**
- Weekly updates (every Monday) for top 100 SKUs
- Bi-weekly (every other Monday) for SKUs 101-500
- Monthly (1st of month) for all other SKUs
- Daily emergency updates if actual > forecast by 30%+ or < forecast by 50%+

**Variance thresholds triggering investigation:**
- MAPE > category benchmark + 5 percentage points
- Bias > 15% (systematic over/underforecast)
- MAE > 50% increase vs. 4-week trend
- Stockout incidents (forecast was 0 but demand occurred)

**Forecast committee meetings:**
- Monthly: review accuracy by marketplace, category, supplier
- Quarterly: rebaseline seasonality indices, update ML models
- Ad-hoc: major promotions, supply disruptions, competitive threats

---

## 2. SAFETY STOCK & REORDER POINTS (250+ lines)

### 2.1 Safety Stock Formula

**Standard formula:**
```
SS = Z × σ_d × √L

Where:
- SS = Safety stock (units)
- Z = Service level Z-score
- σ_d = Standard deviation of daily demand
- L = Lead time (in days)

Alternative (accounting for lead time variability):
SS = Z × √(L × σ_d² + D² × σ_L²)

Where:
- D = Average daily demand
- σ_L = Standard deviation of lead time
```

**Example calculation (electronics category):**
```
Given:
- Average daily demand = 50 units
- Demand std dev (σ_d) = 12 units
- Lead time = 21 days (supplier in Poland)
- Service level target = 95% (Z = 1.645)

SS = 1.645 × 12 × √21
SS = 1.645 × 12 × 4.583
SS = 1.645 × 55.0
SS = 90.4 units → round to 91 units
```

---

### 2.2 Service Level Z-Scores

| Service Level | Z-Score | Stockout Risk | Recommended For |
|--------------|---------|---------------|----------------|
| 80% | 0.842 | 1 in 5 orders | Clearance, slow-moving SKUs |
| 85% | 1.036 | 1 in 6-7 orders | Category managers discretion |
| 90% | 1.282 | 1 in 10 orders | Standard for most products |
| 95% | 1.645 | 1 in 20 orders | Fast-moving, high-margin items |
| 96% | 1.751 | 1 in 25 orders | Best-sellers, brand-critical |
| 97% | 1.881 | 1 in 33 orders | Premium, seasonal (holidays) |
| 98% | 2.054 | 1 in 50 orders | Critical items (rarely used) |
| 99% | 2.326 | 1 in 100 orders | Strategic/monopoly products |
| 99.5% | 2.576 | 1 in 200 orders | Not recommended (excess cost) |

**Service level selection matrix (EU marketplace context):**

| Category | Product Life | Margin | Demand Variability | Recommended SL |
|----------|-------------|--------|-------------------|----------------|
| Fashion (seasonal) | 3-6 months | 40-60% | High (σ_d/D = 0.3+) | 85-90% |
| Electronics | 6-24 months | 15-30% | Medium (σ_d/D = 0.2-0.3) | 92-95% |
| Home & Garden | 12+ months | 35-50% | Medium | 90-93% |
| Fast-moving staples | 12+ months | 10-25% | Low (σ_d/D = 0.1-0.2) | 95-98% |
| Toys (seasonal) | 3-6 months | 35-45% | Very high (σ_d/D = 0.4+) | 80-90% |
| Books | 12+ months | 20-35% | Low | 95-97% |

---

### 2.3 Reorder Point (ROP)

**Basic formula:**
```
ROP = D × L + SS

Where:
- D = Average daily demand
- L = Lead time (days)
- SS = Safety stock

Example:
- Daily demand = 50 units
- Lead time = 21 days
- Safety stock = 91 units (from previous example)
- ROP = 50 × 21 + 91 = 1,050 + 91 = 1,141 units
```

**Interpretation:**
- When inventory falls to 1,141 units, place replenishment order
- Order arrives after 21 days
- By arrival, inventory will be approximately: 1,141 - (50 × 21) = 91 units (=SS)
- This buffer prevents stockout if demand spikes

**ROP with lead time variability:**
```
ROP = D × (L_avg) + SS_dynamic

Where:
- L_avg = Average lead time
- SS_dynamic accounts for both demand AND lead time variability
```

---

### 2.4 Economic Order Quantity (EOQ)

**Classic EOQ formula (Wilson):**
```
EOQ = √(2DS/H)

Where:
- D = Annual demand (units)
- S = Ordering cost per order (€)
- H = Holding cost per unit per year (€)

EOQ derivation:
- Total Cost = (D/Q)×S + (Q/2)×H
- Minimize by taking derivative: Q = √(2DS/H)
```

**Example calculation:**
```
Given:
- Annual demand (D) = 12,000 units
- Ordering cost (S) = €50 per order (supplier fees, shipping, admin)
- Unit cost = €10
- Holding cost rate (h) = 25% per year
- H = €10 × 0.25 = €2.50 per unit per year

EOQ = √(2 × 12,000 × 50 / 2.50)
EOQ = √(1,200,000 / 2.50)
EOQ = √(480,000)
EOQ = 692.8 units → round to 700 units

Order frequency: 12,000 / 700 = 17.1 times/year ≈ every 3 weeks
Average inventory: 700 / 2 = 350 units
Total annual cost: (12,000/700)×50 + (700/2)×2.50 = €857.14 + €875 = €1,732.14
```

**Holding cost components:**
| Component | Rate | Notes |
|-----------|------|-------|
| Cost of capital | 8-12% | Opportunity cost of money tied up |
| Storage space | 3-5% | Warehouse rent, shelving, climate control |
| Insurance | 0.5-1% | Stock insurance |
| Shrinkage/obsolescence | 2-5% | Theft, damage, markdown, expiration |
| Handling & labor | 2-4% | Picking, packing, receiving, counting |
| **Total holding cost %** | **15-27%** | Typically 20-25% for EU marketplaces |

**EOQ adjustments for EU marketplace context:**

| Factor | EOQ Impact | Adjustment |
|--------|-----------|----------|
| Seasonal demand (high variability) | EOQ increases 20-30% | Use peak demand for D |
| Multiple suppliers available | EOQ decreases 10-15% | Lower S due to competition |
| Bulk discounts (MOQ tiers) | EOQ recalculation | Compare costs at each tier |
| Perishable goods (fashion, tech) | EOQ decreases 30-50% | Higher obsolescence in H |
| Dropshipping | EOQ = 0 | Supplier holds inventory |

**Bulk discount EOQ consideration:**

```
Price Tier Analysis:
Tier 1: 0-500 units @ €10/unit, EOQ = 700 units (outside tier)
Tier 2: 501-1,000 units @ €9.50/unit
Tier 3: 1,001+ units @ €9.00/unit

For Tier 2 (Q=700):
- Product cost: 700 × €9.50 = €6,650
- Order cost: (12,000/700) × €50 = €857.14
- Holding cost: (700/2) × €2.375 = €831.25 (H = €9.50 × 0.25)
- Total: €6,650 + €857.14 + €831.25 = €8,338.39

For Tier 3 (Q=1,001):
- Product cost: 1,001 × €9.00 = €9,009
- Order cost: (12,000/1,001) × €50 = €599.40
- Holding cost: (1,001/2) × €2.25 = €1,126.13 (H = €9.00 × 0.25)
- Total: €9,009 + €599.40 + €1,126.13 = €10,734.53

Decision: Tier 2 (Q=700) is optimal at €8,338.39
```

---

### 2.5 Lead Time Variability

**Lead time sources of variation (typical for EU suppliers):**

| Lead Time Source | Mean (days) | Std Dev (days) | Variability |
|------------------|-------------|----------------|----------|
| Sea freight (origin→EU port) | 35 | 8-12 | High (±30%) |
| Port clearing/customs | 5 | 2-4 | High (±50%) |
| Ground transport (port→warehouse) | 8 | 2-3 | Medium |
| **Total (sea+port+ground)** | **48** | **12-18** | **High** |
| **Air freight (door-to-door)** | **7** | **1-2** | **Low** |
| **Rail freight (Asia→EU)** | **21** | **3-5** | **Low** |
| Local EU suppliers | 3-7 | 0.5-1.5 | Very low |

**Dynamic safety stock formula (accounts for L variability):**

```
SS_dynamic = Z × √(L_avg × σ_d² + D² × σ_L²)

Example:
- L_avg = 21 days
- σ_d = 12 units/day
- D = 50 units/day
- σ_L = 3 days (lead time std dev)
- Z = 1.645 (95% SL)

SS = 1.645 × √(21 × 144 + 2500 × 9)
SS = 1.645 × √(3,024 + 22,500)
SS = 1.645 × √25,524
SS = 1.645 × 159.8
SS = 263 units (vs. 91 units with fixed lead time!)
```

**Lead time by supplier origin/mode (EU market data):**

| Route | Sea | Air | Rail | Road |
|-------|-----|-----|------|------|
| China → Germany | 35-45d | 5-8d | 20-25d | N/A |
| China → Netherlands | 33-42d | 5-7d | 18-22d | N/A |
| Vietnam → France | 38-48d | 6-9d | 22-28d | N/A |
| India → UK | 28-38d | 6-10d | 24-30d | N/A |
| Turkey → Poland | 20-28d | N/A | 15-18d | 8-12d |
| EU (intra) → Any | N/A | N/A | N/A | 2-5d |

---

### 2.6 Min/Max Inventory Policies

**Min/Max policy rules:**
```
When on-hand inventory ≤ Min level:
  → Place order for (Max - Current) units

Max = ROP + EOQ
Min = ROP - (D × review_period)

Where review_period = days between inventory checks
```

**Example (continuous review):**
```
- ROP = 1,141 units (calculated earlier)
- EOQ = 700 units
- Max = 1,141 + 700 = 1,841 units
- Min = 1,141 - (50 × 1) = 1,091 units (daily review)

Action triggers:
- At inventory = 1,091: Place order for 750 units (to reach ~1,841)
- At inventory = 1,500: No action (above min)
- At inventory = 800: CRITICAL - expedite order, consider safety stock failure
```

**Periodic review (monthly for EU marketplace):**
```
- Review period = 30 days
- Target level = ROP + (D × 30) + SS = 1,141 + 1,500 + 91 = 2,732 units
- On review day: If current < 2,732, order to bring to 2,732

Example:
- Current inventory on review day: 1,500 units
- Order quantity: 2,732 - 1,500 = 1,232 units
- Allows buffering against demand during lead time + review period
```

---

### 2.7 Two-Bin System (SKU-level control)

**Two-bin physical implementation:**
```
Bin 1: Standard order quantity (EOQ or fixed quantity)
Bin 2: Safety stock + demand during replenishment lead time

Workflow:
1. Pick from Bin 1 until empty
2. Remove Bin 1, move Bin 2 to active location
3. Empty Bin 2 signals order placement
4. New shipment fills Bin 1 when it arrives
5. Continue cycle
```

**Advantages for EU marketplace operations:**
- Visual control, no system dependency
- Works in high-velocity environments
- Immediate signal to reorder
- Reduces administrative errors

**Two-bin sizing:**
```
Bin 1: 500-1,000 units (EOQ or equivalent)
Bin 2: 250-350 units (SS + 7-day demand buffer)
Total: 750-1,350 units managed physically
```

---

### 2.8 Excess & Stranded Inventory

**Excess inventory definition:**
```
Excess units = On-hand - (ROP + 30 days demand forecast)
Excess value = Excess units × cost_per_unit
Excess inventory days: (Excess units / Daily demand)
```

**Example:**
```
- On-hand: 2,500 units
- ROP: 1,141 units
- 30-day forecast: 1,500 units
- Daily demand: 50 units
- Cost per unit: €10

Excess = 2,500 - (1,141 + 1,500) = -141 units
Status: Understocked (negative excess) → immediate reorder needed
```

**Stranded inventory (dead stock) policy:**

| Days on Hand | Status | Action | Write-down |
|--------------|--------|--------|----------|
| 0-30 days | Normal | Monitor | 0% |
| 31-60 days | At-risk | Promote, reduce price | 0% |
| 61-90 days | Slow-moving | Heavy discount (20-30%) | 5-10% |
| 91-180 days | Excess | Very heavy discount (40-60%) | 15-25% |
| 181-365 days | Dead stock | Clearance (cost-based pricing) | 40-60% |
| 365+ days | Obsolete | Removal from sale, scrap/donate | 80-100% |

**Amazon FBA inventory surcharges (as of 2026):**

| Metric | Benchmark | Fee | Impact |
|--------|-----------|-----|--------|
| Excess inventory ratio | <10% | 0% | Standard |
| Excess inventory ratio | 10-20% | €0.10/unit/day | ~€30/unit/year |
| Excess inventory ratio | 20-40% | €0.20/unit/day | ~€60/unit/year |
| Excess inventory ratio | >40% | €0.30/unit/day | ~€90/unit/year |
| Long-term stranded | 365+ days | Liquidation | Cost-based removal |
| Oversize surcharge | >141 cm longest | €0.15/unit/day | ~€45/unit/year |

**EU marketplace excess inventory management:**
- Amazon EU FBA: Monthly balance target ≤10% excess ratio
- eBay: Weekly inventory review to flag slow movers
- Cdiscount: 15-day clearance promotion for >60-day stock
- Zalando: 30-day return window increases stranding risk

---

## 3. INVENTORY PERFORMANCE METRICS (200+ lines)

### 3.1 Inventory Turnover Rate

**Formula:**
```
Inventory Turnover = COGS / Average Inventory
Or: Turnover = Units Sold / Average Units on Hand

Times per year = COGS / ((Beginning Inventory + Ending Inventory) / 2)
```

**Example (electronics category):**
```
- COGS (annual): €500,000
- Beginning inventory (Jan 1): €50,000
- Ending inventory (Dec 31): €60,000
- Average inventory: (€50,000 + €60,000) / 2 = €55,000

Turnover rate = €500,000 / €55,000 = 9.09 times/year
Average days in inventory: 365 / 9.09 = 40.2 days
```

**Turnover benchmarks by category (EU marketplace):**

| Category | Fast-moving | Average | Slow-moving |
|----------|-----------|---------|----------|
| Electronics | 8-15× | 5-8× | 2-5× |
| Fashion/Apparel | 4-8× | 2-4× | 1-2× |
| Home & Garden | 3-6× | 2-3× | 0.5-2× |
| Books | 6-12× | 3-6× | 1-3× |
| Toys/Games | 2-5× (annual) | 1-2× | 0.25-1× |
| Furniture | 1-3× | 0.5-1× | 0.1-0.5× |
| Sports & Outdoors | 2-5× | 1-3× | 0.5-1× |

**Industry targets (2024-2026):**
- High-margin resellers target: 6-10× turnover
- Integrated retailers (own+marketplace): 4-8× turnover
- Dropshippers (low inventory): N/A (just-in-time)

---

### 3.2 Days of Inventory

**Formula:**
```
Days of Inventory = 365 / Inventory Turnover
Or: DIH = Average Inventory / (COGS / 365)

DIH measures how many days of sales are carried in inventory
```

**Example:**
```
- Turnover = 9.09 times/year
- DIH = 365 / 9.09 = 40.2 days

Interpretation: Inventory is on-hand for ~40 days before sale
```

**Optimal DIH ranges (EU context):**

| Product Type | Optimal DIH | Min | Max |
|--------------|-------------|-----|-----|
| Fast-moving staples | 20-30 days | 15 | 45 |
| Standard products | 30-50 days | 20 | 75 |
| Seasonal (non-peak) | 40-70 days | 30 | 90 |
| Specialty/niche | 60-90 days | 45 | 120 |
| Fashion (in-season) | 15-30 days | 10 | 45 |
| Fashion (off-season) | 60-180 days | 45 | 365 |

**Time-based targets by marketplace:**
- Amazon FBA: 30-45 days optimal (balance inventory cost vs stockout risk)
- eBay MFN: 20-40 days (faster rotation rewards)
- Cdiscount: 25-45 days
- Zalando B2B: 35-60 days

---

### 3.3 Sell-Through Rate (STR)

**Formula:**
```
STR = (Units Sold / Units Received) × 100%
or: STR = (Units Sold / (Beginning Inventory + Units Received)) × 100%

Usually measured over 4-week or 13-week periods
```

**Example (13-week analysis):**
```
- Units received in period: 500 units
- Beginning inventory: 100 units
- Units sold: 450 units
- Ending inventory: 150 units

STR = 450 / 500 = 90% (first formula)
or STR = 450 / (100 + 500) = 450 / 600 = 75% (second formula)

Amazon uses second formula: STR = 75%
```

**STR benchmarks (Amazon FBA standard):**

| STR Range | Classification | Action |
|-----------|-----------------|--------|
| >150% | Exceptional (received less than sold) | Increase replenishment frequency |
| 100-150% | Excellent | Standard reorder |
| 75-100% | Very good | Monitor, consider smaller orders |
| 50-75% | Good | Reduce order size, extend replenishment cycle |
| 25-50% | Slow-moving | Discount aggressively or remove |
| <25% | Dead stock | Liquidate, discontinue |

**STR by category (13-week rolling):**

| Category | Excellent | Good | Acceptable | Poor |
|----------|-----------|------|-----------|------|
| Fast-moving essentials | >120% | 90-120% | 60-90% | <60% |
| Electronics | 80-120% | 60-80% | 40-60% | <40% |
| Fashion (in-season) | 70-110% | 50-70% | 30-50% | <30% |
| Home/Furniture | 50-75% | 35-50% | 20-35% | <20% |
| Toys (seasonal) | 40-80% (varies by season) | 25-60% | 15-40% | <15% |

**4-week vs. 13-week STR:**
- 4-week: Highly volatile, daily spikes affect % significantly
- 13-week: More stable, better for trend analysis
- Both monitored: 4-week for tactical decisions, 13-week for strategic

---

### 3.4 Inventory Accuracy & Shrinkage

**Formula:**
```
Inventory Accuracy = ((Counted Units - System Units) / System Units) × 100%
Or: % Accuracy = (Correct records / Total SKUs) × 100%

Shrinkage rate = (Book Inventory - Physical Count) / Book Inventory
Shrinkage €= Shrinkage units × Unit cost
```

**Example:**
```
- System shows: 5,000 units across 50 SKUs
- Physical count: 4,900 units (20 SKUs accurate, 30 with discrepancies)
- Accuracy: (20 / 50) × 100% = 40% SKU accuracy
- Or: Variance = 100 units / 5,000 = 2% quantity variance
- Shrinkage cost: 100 units × €25/unit = €2,500
```

**Accuracy targets by marketplace:**

| Standard | Accuracy | Frequency | Penalty |
|----------|----------|-----------|--------|
| Amazon FBA | ≥99% | Continuous | Account suspension if <95% |
| Cdiscount | ≥98% | Bi-weekly audit | Liquidation holds if <95% |
| eBay MFN | Self-reported | Monthly | Seller rating impact |
| Zalando B2B | ≥99.5% | Weekly | Return/chargeback limits |

**Shrinkage sources (EU warehouse context):**

| Source | % of Total | Mitigation |
|--------|-----------|----------|
| Damage/defect | 25-35% | Better packaging, damage prevention |
| Theft/loss | 20-30% | CCTV, access controls, cycle counts |
| Administrative error | 20-35% | Barcode scanning, system discipline |
| Obsolescence/expiration | 5-15% | FIFO rotation, hold dating |
| Supplier shipment short | 5-10% | Receiving verification, claim processing |
| **Total shrinkage rate** | **0.5-2%** | Continuous improvement program |

**Cycle count program (EU FBA-compliant):**
```
Frequency: Every 90 days for fast-movers, 180 days for slow-movers
Coverage: 100% of SKUs audited annually
Method: Count 20-30 SKUs per week, reconcile discrepancies same day
Variance tolerance: ±2% units or ±1% value (whichever is greater)
Exceptions: >5 units difference triggers full SKU recount
```

---

### 3.5 Amazon IPI (Inventory Performance Index)

**Amazon IPI calculation (2026 standard):**

```
IPI = (5 × excess_inventory_rate +
       20 × stranded_inventory_rate +
       15 × sell_through_rate +
       60 × in_stock_rate) / 100

Where each metric is weighted:
- Excess inventory rate: Units > 90 days demand forecast / Total units (5%)
- Stranded inventory rate: Units that won't sell / Total units (20%)
- Sell-through rate: Units sold / (Beginning + Received) (15%)
- In-stock rate: Days in stock / (Target inventory days) (60%)
```

**IPI scoring:**
```
IPI Score Range | Status | Impact
≥ 500          | Excellent | Full selling privileges
400-499        | Good | No restrictions
350-399        | Acceptable | Consider plan (warning level)
< 350          | Poor | Suspension likely if not improved
```