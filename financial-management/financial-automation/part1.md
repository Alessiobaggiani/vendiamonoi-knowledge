# Domain 5.6 — Financial Automation & Reconciliation - PART 1
## Reconciliation Architecture, Marketplace APIs, Transaction Matching

(Lines 1-750 of comprehensive guide)

---

## 1. RECONCILIATION ARCHITECTURE

### 1.1 Multi-Source Data Flow

The financial reconciliation system processes data from three primary sources:

```
Marketplace APIs → Data Standardization → Transaction Matching → GL Posting
Bank Feeds (PSD2) → FX Conversion → Settlement Posting
Supplier Invoices → OCR/AI → Invoice Matching → AP Posting
```

**Key Components:**

1. **Data Ingestion Layer** — Hourly/daily syncs from marketplaces and banks via APIs
2. **Normalization Layer** — Map marketplace-specific fields to standard schema
3. **Deduplication** — Prevent double-posting of transactions
4. **Matching Engine** — Reconcile marketplace settlements with bank deposits
5. **Exception Queue** — Flag anomalies for manual review (duplicates, missing fees, timing issues)
6. **GL Posting** — Automated journal entries to accounting system

### 1.2 Reconciliation Process Flow

```
START
  ↓
[Daily] Extract marketplace settlement reports
[Daily] Extract bank transactions (PSD2 API)
[Daily] Extract supplier invoices
  ↓
Standardize all data to common schema
  ↓
Deduplication (check existing transactions)
  ↓
Auto-match: Direct 1:1 mapping
  ├─ Match by amount + date + reference
  ├─ Match by amount + marketplace ID
  └─ Mark as reconciled
  ↓
Auto-match: Fuzzy/complex scenarios
  ├─ 1:N settlement mapping (multiple orders → one deposit)
  ├─ Rounding tolerance checks
  ├─ FX gain/loss calculation
  └─ Exception queue if confidence < 95%
  ↓
Manual Review Queue (unmatched transactions)
  ├─ Review exceptions
  ├─ Manual assignment
  └─ Update matching rules
  ↓
Post to GL (reconciled transactions only)
  ↓
Generate daily reconciliation report
  ↓
END
```

**Timing:** Daily evening sync (7-11 PM), weekly reconciliation (Sunday 2-6 PM), monthly close (last day, 3-hour window)

---

## 2. MARKETPLACE DATA EXTRACTION

### 2.1 Amazon Seller Central (SP-API Settlement)

**Endpoint:** `https://sellingpartnerapi-eu.amazon.com/`

**Key Settlement Endpoints:**

```
GET /finances/v0/settlement-reports
  - Returns list of settlement reports
  - Params: maxResults=100, createdAfter, createdBefore
  - Rate limit: 10/sec
  
GET /finances/v0/settlement-reports/{settlementId}/statements
  - Detailed transactions for settlement
  - Includes orders, refunds, fees, adjustments
  - XML format (can be converted to JSON)
  
GET /finances/v0/transactions
  - Real-time transaction feed
  - Params: postingDate filter (15-30 days max)
  - Type: ORDER_ADJUSTMENT, REFUND, FEE, TRANSFER_OUT
```

**Mandatory Fields in Settlement:**
- `AmazonOrderId` (unique order reference)
- `TransactionAmount` (in EUR)
- `TransactionDate` (settlement date)
- `TransactionType` (order, refund, fee, chargeback)
- `OrderItemCode` (SKU level detail)
- `FulfillmentChannel` (FBA or MFN)

**Example Settlement Entry:**
```xml
<Transaction>
  <OrderId>111-1234567-1234567</OrderId>
  <OrderItemCode>ABC123XYZ</OrderItemCode>
  <TransactionType>Order</TransactionType>
  <TransactionAmount>29.99</TransactionAmount>
  <TransactionDate>2026-04-01T12:00:00Z</TransactionDate>
  <AmazonFeeComponent>
    <FeeType>Commission</FeeType>
    <FeeAmount>-4.49</FeeAmount>
  </AmazonFeeComponent>
  <AmazonFeeComponent>
    <FeeType>FBA Fulfillment</FeeType>
    <FeeAmount>-3.00</FeeAmount>
  </AmazonFeeComponent>
</Transaction>
```

**Authentication:** OAuth 2.0 (Restricted Data Token required)
**Refresh Cadence:** Daily at 6 AM, 12 PM, 6 PM (3 syncs/day)

---

### 2.2 eBay Financial API

**Endpoint:** `https://apiz.ebay.com/sell/finances/v1`

**Key Endpoints:**

```
GET /sell/finances/v1/seller_payouts
  - Payout summaries
  - Params: limit=200, offset, filter (by date/status)
  - Rate limit: 60/min
  
GET /sell/finances/v1/transactions
  - Detailed transaction ledger
  - Types: SALE, REFUND, SHIPPING_LABEL, FINAL_VALUE_FEE, etc.
  - Date range: 90 days max
  
GET /sell/finances/v1/seller_payouts/{payout_id}/payout_summary
  - Breakdown of single payout (included fees, adjustments)
```

**Mandatory Fields:**
- `referenceId` (unique transaction ID)
- `transactionDate` (when transaction occurred)
- `transactionAmount` (net or gross, specify)
- `transactionType` (SALE, REFUND, FEE)
- `orderLineItemReference` (links to marketplace order)
- `payoutReference` (payout batch ID)

**Example Payout Structure:**
```json
{
  "payoutId": "4028818675847234",
  "payoutStatus": "SUCCEEDED",
  "payoutDate": "2026-04-01",
  "payoutAmount": {
    "value": "1250.45",
    "currency": "EUR"
  },
  "transactionCount": 87,
  "transactions": [
    {
      "referenceId": "TX-2026-04-001",
      "transactionDate": "2026-03-31T23:59:59Z",
      "transactionType": "SALE",
      "transactionAmount": {
        "value": "45.99",
        "currency": "EUR"
      }
    }
  ]
}
```

**Authentication:** OAuth 2.0
**Refresh Cadence:** Daily at 8 AM (morning payout reports), ad-hoc checks at 4 PM

---

### 2.3 Bol.com Finance API

**Endpoint:** `https://api.bol.com/retailer/`

**Key Endpoints:**

```
GET /v10/orders
  - Order list with financial details
  - Params: status filter (OPEN, WAITING_FOR_SHIPMENT, PAID, PARTIALLY_PAID)
  - Pagination: pageNumber, pageSize=50
  
GET /v10/orders/{order-id}
  - Detailed order breakdown (price, commission, costs)
  
GET /v10/commissions
  - Commission rates and invoicing
  - Calculates what Bol.com takes
  
GET /v10/shipments/{fulfillment-order-id}
  - Shipping costs and tracking (affects net settlement)
```

**Mandatory Fields:**
- `id` (Bol.com order ID)
- `dateCreated` (order date)
- `offerReference` (seller's SKU)
- `priceInfo` (list price, discount, agreed price)
- `commissionInfo` (percentage, amount, refundable)
- `paymentStatus` (OPEN, PAID, CANCELLED, PENDING)

**Example Order Object:**
```json
{
  "id": "2026040100000001",
  "dateCreated": "2026-04-01T10:30:00+02:00",
  "paymentStatus": "PAID",
  "priceInfo": {
    "listPrice": 99.99,
    "discountAmount": 10.00,
    "agreedPrice": 89.99,
    "shippingCosts": 5.00
  },
  "commissionInfo": {
    "commissionPercentage": 15.0,
    "commissionAmount": -13.50,
    "isRefundableCommission": true
  },
  "netSettlement": 81.49
}
```

**Settlement Timing:** Weekly Thursdays to registered bank account (2-3 day bank transfer)
**Refresh Cadence:** Daily at 7 PM

---

### 2.4 Kaufland Finance Module

**Endpoint:** `https://api.kaufland.com/` (partner API)

**Key Endpoints:**

```
GET /v2/shops/{shop_id}/orders
  - Order list with status and financials
  - Filter: dateFrom, dateTo (30-day window)
  
GET /v2/shops/{shop_id}/invoices
  - Marketplace invoices (commission, fees)
  
GET /v2/shops/{shop_id}/settlements
  - Payout schedule and history
```

**Mandatory Fields:**
- `id_order` (unique Kaufland order ID)
- `id_product` (product ID)
- `created_at` (order timestamp)
- `item_price` (seller-set price)
- `commission` (Kaufland takes percentage)
- `payment_status` (pending, confirmed, cancelled)

**Settlement:** Weekly, variable day (Wed/Thu)
**Refresh Cadence:** Daily at 6 PM

---

### 2.5 Cdiscount Finance API

**Endpoint:** `https://api.cdiscount.com/` (partner account required)

**Key Endpoints:**

```
GET /orders
  - Order summary with financials
  
GET /settlements
  - Payout transactions (usually monthly)
  
GET /commissions
  - Commission calculations
```

**Mandatory Fields:**
- `order_id`
- `order_date`
- `net_amount` (after commission)
- `commission_amount`
- `status` (finalized, pending, disputed)

**Settlement Timing:** Monthly (variable), typically 5-10 days after month-end
**Refresh Cadence:** Daily at 8 PM

---

### 2.6 Allegro (Polish marketplace) API

**Endpoint:** `https://api.allegro.pl/` (OAuth required)

**Key Endpoints:**

```
GET /sale/offers/{offer-id}
  - Offer details with pricing
  
GET /order/transactions
  - Transaction ledger for financials
  - Params: date range, status
  
GET /user/me/billing
  - Billing summary (commissions, holds)
```

**Mandatory Fields:**
- `transactionId`
- `transactionDate`
- `amount` (gross)
- `commission` (Allegro's cut)
- `orderReference`
- `status`

**Settlement:** Weekly (usually Monday) to bank account
**Refresh Cadence:** Daily at 9 PM

---

## 3. TRANSACTION MATCHING ENGINE

### 3.1 One-to-One Matching (Direct Reconciliation)

**Matching Rule Priority (in order):**

1. **Exact Match (Hash-based)** — If `marketplace_id + amount + date` hash exists in GL, mark reconciled
2. **Amount + Date Match** — Match within 0 EUR tolerance, same day
3. **Amount + Marketplace Reference** — Match exact amount + settlement ID, date variance ±2 days
4. **Reference-Only Match** — Match by unique order ID regardless of amount

**Pseudocode:**

```python
def match_transactions(marketplace_tx, bank_tx):
    """
    Returns: (matched, confidence, additional_postings)
    confidence: 0-100 (100 = certain match, <95 = requires review)
    """
    
    # Rule 1: Hash-based exact match
    mp_hash = hash(f"{marketplace_tx.id}_{marketplace_tx.amount}_{marketplace_tx.date}")
    if mp_hash in existing_hashes:
        return (True, 100, [])
    
    # Rule 2: Amount exact + date match
    if (bank_tx.amount == marketplace_tx.amount and 
        abs((bank_tx.date - marketplace_tx.date).days) == 0):
        return (True, 98, [])
    
    # Rule 3: Amount + ref match, date tolerance
    if (bank_tx.amount == marketplace_tx.amount and 
        bank_tx.reference == marketplace_tx.settlement_id and 
        abs((bank_tx.date - marketplace_tx.date).days) <= 2):
        return (True, 95, [])
    
    # Rule 4: Reference-only (fuzzy)
    if extract_order_id(bank_tx.reference) == marketplace_tx.order_id:
        if abs((bank_tx.date - marketplace_tx.date).days) <= 5:
            return (True, 85, [])  # Needs manual review
    
    # No match found
    return (False, 0, [])
```

**Example Matching Scenario:**

```
Marketplace Transaction (Amazon):
  Order ID: 111-1234567-1234567
  Amount: €45.99
  Date: 2026-03-31
  Commission: -€4.50
  Net Settlement: €41.49

Bank Transaction (Qonto):
  Amount: €41.49
  Date: 2026-04-01 (one day later, expected)
  Description: "AMAZON EU SARL 111-1234567"
  
Matching Result: MATCH (confidence: 98%)
  - Amount matches net settlement
  - Date within tolerance (1 day)
  - Reference contains order ID
  → Post to GL: DR Bank 41.49 / CR Marketplace Revenue 45.99 / CR Commission Fee 4.50
```

---

### 3.2 One-to-Many Settlement Matching

**Scenario:** Multiple orders settle in single bank deposit

```
Marketplace Transactions (3 orders):
  Order 1: €15.00 (2026-03-28)
  Order 2: €22.50 (2026-03-29)
  Order 3: €18.75 (2026-03-30)
  Commission: -€12.25
  Total Net: €44.00

Bank Deposit:
  Amount: €44.00
  Date: 2026-04-01
  Reference: "EBAY SETTLEMENT 20260401"
  
Matching Result: GROUP MATCH (confidence: 97%)
  - Sum of orders matches deposit
  - Order dates all within settlement period
  - Settlement reference present
  → Create settlement posting record
  → Post all three orders + commission in GL
```

**Algorithm:**

```python
def match_settlement_batch(settlement_records, bank_deposits):
    """
    Matches multiple marketplace transactions to single bank deposit
    """
    unmatched_deposits = list(bank_deposits)
    
    for settlement in settlement_records:
        # Get all orders in this settlement batch
        orders = get_orders_in_settlement(settlement.id)
        total_net = sum(order.amount for order in orders) - settlement.commission
        
        # Find matching bank deposit
        for bank_tx in unmatched_deposits:
            if abs(bank_tx.amount - total_net) < 0.01:  # EUR tolerance
                if settlement.date <= bank_tx.date <= settlement.date + timedelta(days=3):
                    # Found match
                    create_settlement_record(settlement.id, bank_tx.id, orders)
                    unmatched_deposits.remove(bank_tx)
                    break
    
    return unmatched_deposits  # Remaining unmatched deposits → exception queue
```

---

### 3.3 Fuzzy Matching & Tolerance Thresholds

**Tolerance Rules for different scenarios:**

| Scenario | Amount Tolerance | Date Tolerance | Confidence Threshold |
|----------|-----------------|-----------------|---------------------|
| Direct 1:1 (same day) | ±€0.00 | 0 days | 100% |
| 1:1 with rounding | ±€0.01 | 1 day | 98% |
| Settlement batch | ±€0.05 | 3 days | 95% |
| Multi-currency (post-FX) | ±€1.00 | 2 days | 90% |
| Partial refund | ±€2.00 | 7 days | 80% |

**Rounding Example (EUR):**

```
Order Price: €24.99
Quantity: 3
Subtotal: €74.97
VAT (22%): €16.49
Total: €91.46

Bank charged: €91.45 (rounding difference: €0.01)
Match result: ACCEPT (within €0.01 tolerance)
```

**FX Example (GBP to EUR):**

```
Seller GBP Balance: £100.00
Exchange Rate (FX provider): 1 GBP = €1.1755
Converted to EUR: €117.55
Bank Deposit (actual): €117.54

FX Gain/Loss: -€0.01 (rounding)
Match result: ACCEPT (within €1.00 tolerance for multi-currency)
Posting: DR Bank €117.54 / CR Marketplace Revenue €117.55 / CR FX Loss €0.01
```

---

### 3.4 Exception Queue Management

**Unmatched transactions flow to exception queue:**

```
Exception Queue
├── Duplicates (same amount/date/ref within 24h)
│   └─ Flag for deletion from one source
├── Missing Fees (expected fee, not in settlement)
│   └─ Flag for manual investigation (fraud/glitch)
├── Rounding Errors (difference < €0.05)
│   └─ Auto-approve with note
├── Timing Issues (order dated 8+ days before bank tx)
│   └─ Flag for follow-up with marketplace
├── Chargeback/Dispute (negative amount, unexpected)
│   └─ Escalate to finance team
└── Unmatchable (orphaned transactions)
    └─ Manual review required
```

**Example Exception Processing:**

```
Transaction: Marketplace shows €50.00 refund, but bank deposit = €49.50
Variance: €0.50 (1% difference)
Rule: Refunds often incur reversal fees
Exception: Requires 95% confidence, currently 85%
Action: Queue for manual review
Reviewer: Confirms payment processor fee applied
Resolution: Accept match with note "refund fee €0.50"
```

---

**END OF PART 1**

Continue with Part 2: Bank Reconciliation, Invoice Automation, FatturaPA