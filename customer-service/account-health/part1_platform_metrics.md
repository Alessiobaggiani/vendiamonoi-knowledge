# Domain 6.4 — Part 1: Platform-Specific Health Metrics

**Vendiamonoi.it Multi-Marketplace Operations Guide**

---

## 1. Amazon Account Health — Comprehensive Metrics

### 1.1 Order Defect Rate (ODR) — THE CRITICAL METRIC

**Threshold:** < 1% (Risk: Suspension at 2-3%)

ODR measures complaint rate and includes:
- Buyer complaints (A-to-Z claims)
- Credit card chargebacks
- Item Not Received (INR) claims
- Negative feedback indicating defect

**Calculation:** (Complaints + Chargebacks) / (Shipped units) × 100 = ODR %

**Target for Vendiamonoi.it:**
- Green: 0.0 - 0.5% (Excellent)
- Yellow: 0.5 - 0.9% (Monitor)
- Red: 0.9 - 2.0% (High Risk)
- Suspension: 2.0%+ (Immediate Action)

**Mitigation Strategies:**
- Daily review of Complaints dashboard
- Proactive refunds for damaged goods
- Detailed product listings with photos
- Clear return policy communication
- Monitor shipping partner quality

### 1.2 Late Shipment Rate (LSR)

**Threshold:** < 4% (Risk: Suspension at 4%+)

LSR = (Late shipments) / (Total units shipped) × 100

**Target Bands:**
- Green: 0.0 - 2.0% (Excellent)
- Yellow: 2.0 - 3.5% (Monitor)
- Red: 3.5 - 4.0% (High Risk)
- Suspension: 4.0%+ (Immediate Action)

**Prevention Actions:**
- Automatic inventory reserve at order
- Set promise dates conservatively
- Daily shipment queue review
- Carrier performance tracking
- Batch processing during low-traffic windows

### 1.3 Pre-Fulfillment Cancel Rate (PFCR)

**Threshold:** < 2.5% (Risk: Suspension at 3%+)

PFCR = (Orders cancelled before shipment) / (Total orders) × 100

**Target Bands:**
- Green: 0.0 - 1.0%
- Yellow: 1.0 - 1.8%
- Red: 1.8 - 2.5%
- Suspension: 2.5%+

**Mitigation:**
- Real-time inventory synchronization (hourly)
- Buffer stock (+20% safety margin)
- Reserve inventory across channels
- Quality inspection before fulfillment
- Supplier SLA monitoring

### 1.4 Valid Tracking Rate (VTR)

**Threshold:** > 95% (Risk: Suspension below 90%)

VTR = (Shipments with valid tracking) / (Total shipments) × 100

**Target Bands:**
- Green: 95.0% - 100% (Excellent)
- Yellow: 92.0% - 94.9% (Monitor)
- Red: 90.0% - 91.9% (High Risk)
- Suspension: < 90% (Immediate Action)

**Valid Tracking Requirements:**
- Carrier-issued tracking (must match carrier database)
- Updated within 48 hours of shipment
- Active tracking link (FedEx/DHL/DPD/GLS APIs)
- Not generic/placeholder numbers

### 1.5 Invoice Defect Rate (IDR)

**Threshold:** < 0.5% (Risk: Suspension at 1%+)

**Common Defects:**
- Missing business registration/VAT number
- Incorrect seller name/address
- Undisclosed importer/distributor details
- VAT not charged correctly

### 1.6 Account Health Rating (AHR)

**Components:**
- ODR (35% weight): 0.0-1.0%
- LSR (25% weight): 0.0-4.0%
- PFCR (20% weight): 0.0-2.5%
- Customer Service (15% weight)
- Feedback/star rating (5% weight)

**AHR Target:** 95+ points (Green status)

**Status Thresholds:**
- Green: 95+ (Excellent standing)
- Yellow: 85-94 (Monitor closely)
- Red: < 85 (At risk)

### 1.7 Inventory Performance Index (IPI)

**Threshold:** > 400 points

IPI Components:
- Inventory turnover (15%)
- Fulfillment defect rate (25%): < 1% target
- Stranded inventory (20%): < 10% of total
- Sell-through rate (40%): 85%+ target

---

## 2. eBay Seller Standards — Performance Tiers

### 2.1 Seller Rating System

**Below Standard (Red Status)**
- Transaction Defect Rate: 4.0% or higher
- Final Value Fee: Increased 20-100%

**Standard (Yellow Status)**
- Transaction Defect Rate: 2.0% - 3.99%
- Final Value Fee: Baseline rates

**Top Rated (Green Status)**
- Transaction Defect Rate: Below 2.0%
- Final Value Fee: 10% reduction

**Top Rated Plus (Platinum Status)**
- Transaction Defect Rate: Below 2.0%
- Late Shipment Rate: Below 2.5%
- Returns Rate: Below 1.0%
- Final Value Fee: 20% reduction
- Must offer 30-day returns
- Must use tracked shipping

### 2.2 Transaction Defect Rate (TDR)

**Calculation:** (Defects) / (Transactions in rolling 12 months) × 100

**Defects Include:**
- Unpaid Item (INR)
- Item Not Received
- Significantly Not As Described (sNAD)
- Returns abuse

**Target for Vendiamonoi.it:**
- Green: 0.0 - 1.0% (Top Rated Plus achievable)
- Yellow: 1.0 - 1.8% (Top Rated)
- Red: 1.8 - 2.0% (Top Rated edge)
- Suspension: 4.0%+ TDR

### 2.3 Late Shipment Rate (eBay)

**Threshold:** Below 2.5% for Top Rated Plus

**Vendiamonoi.it Targets:**
- Green: 0.0 - 1.5% (Excellent)
- Yellow: 1.5 - 2.0% (Monitor)
- Red: 2.0 - 2.5% (High Risk)

### 2.4 Detailed Seller Ratings (DSR)

eBay collects DSR on 1-5 scale for:
1. Item accuracy
2. Communication responsiveness
3. Shipping speed
4. Shipping cost reasonableness

**Impact:** Below 4.5 average → Search visibility penalization

**Vendiamonoi.it Target:** 4.7+ average

---

## 3. Bol.com Performance Framework

### 3.1 Track & Trace Compliance

**Requirement:** Valid tracking on 100% of shipments within 6 hours

**Valid Tracking:**
- Carrier-issued tracking
- Real-time tracking updates
- ITM-compliant

**Bol.com Carriers:**
- PostNL (primary)
- DPD (secondary)
- GLS (alternative)
- DHL Express (premium)

### 3.2 Cancellation Rate Tracking

**Threshold:** Below 3% (monthly rolling)

Cancellations = cancelled before shipment / Total orders

**Prevention:**
- Real-time inventory sync (hourly)
- Conservative stock buffers
- Supplier SLA alignment
- Automated alerts (> 2% threshold)

### 3.3 On-Time Delivery Performance

**Threshold:** > 95% on-time delivery

**Handling Times:**
- 24-hour express: Next business day
- 2-3 day delivery: Within 3 business days
- 4-5 day delivery: Within 5 business days

**Vendiamonoi.it Target:** 97%+ on-time

### 3.4 Returns Processing & Logistics

**Timeline Requirements:**
- Accept returns up to 60 days
- Process within 24 hours
- Refund within 7 business days

**Returns Types:**
- Defect (seller pays): Damaged, wrong item, quality issue
- Non-Defect (buyer pays): Changed mind, wrong size

### 3.5 Response Time to Inquiries

**Threshold:** Average < 4 hours, max 24 hours

### 3.6 Bol.com Partner Charter

**Key Commitments:**
- Product quality and accuracy
- Pricing fairness
- Delivery reliability
- Customer service responsiveness
- Data security

---

## 4. Other Marketplaces

### 4.1 Kaufland Quality Score

**Regions:** Germany, Austria, Czech Republic, Slovakia, Romania, Bulgaria, Croatia

**Score Components:**
- Item Quality (30%)
- Seller Communication (25%): < 8 hours response
- Delivery Performance (25%): > 98% on-time
- Return Handling (20%): < 7 days refund

**Overall Score:** 1-5 stars (monthly)

**Vendiamonoi.it Target:** 4.5+ stars

### 4.2 Cdiscount Performance

**Key Metrics:**
- Customer Satisfaction Score
- Delivery Compliance
- Product Authenticity
- Return Processing

### 4.3 Allegro Rating

**System:** eBay-style feedback, 1-5 stars

**Key Metrics:**
- Average rating: Below 4.2 = reduced rank
- On-time delivery
- Communication

### 4.4 ManoMano Quality Metrics

**Categories:** Tools, hardware, building materials

**Requirements:**
- Professional photos
- Technical specs
- Seller verification

### 4.5 Fnac-Darty Dashboard

**Performance Indicators:**
- Product quality rating
- Delivery speed
- Returns experience

**Requirement:** €10,000+/month minimum order volume

---

## Metric Thresholds Summary

| Marketplace | Primary Metric | Green | Yellow | Red | Suspension |
|-------------|---|---|---|---|---|
| **Amazon** | ODR | 0-0.5% | 0.5-0.9% | 0.9-1.5% | 2.0%+ |
| | LSR | 0-2.0% | 2.0-3.5% | 3.5-4.0% | 4.0%+ |
| | PFCR | 0-0.8% | 0.8-1.5% | 1.5-2.0% | 2.5%+ |
| | VTR | 98-100% | 95-97% | 90-94% | <90% |
| **eBay** | TDR | 0-0.8% | 0.8-1.5% | 1.5-1.8% | 4.0%+ |
| | DSR | 4.7+ | 4.4-4.6 | <4.4 | N/A |
| **Bol.com** | Cancel Rate | 0-1.5% | 1.5-2.0% | 2.0-2.5% | 3.0%+ |
| | Response | <4h | 4-6h | 6-24h | >24h |
| | On-time | 95%+ | 92-94% | <92% | <90% |

---

**Version:** 1.0 | **Last Updated:** April 2, 2026