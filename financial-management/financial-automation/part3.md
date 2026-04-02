# Domain 5.6 — Financial Automation & Reconciliation - PART 3
## Workflows, Accounting Integration, Error Handling, ROI Analysis

(Lines 1401-2078 of comprehensive guide)

---

## 7. AUTOMATION WORKFLOWS (Make.com/n8n)

### 7.1 Daily Marketplace Sync Workflow (Make.com)

**Frequency:** Daily at 6 AM, 12 PM, 6 PM
**Duration:** 15-45 minutes (depends on transaction volume)

**Scenario Blueprint:**

```
TRIGGER: Schedule (Daily, 3x per day)
  ↓
STEP 1: Fetch Amazon SP-API settlements
  - Module: HTTP Request (GET)
  - URL: https://sellingpartnerapi-eu.amazon.com/finances/v0/settlement-reports
  - Auth: OAuth 2.0 (SP-API credentials)
  - Parse XML response → JSON
  
STEP 2: Fetch eBay Payouts API
  - Module: HTTP Request (GET)
  - URL: https://apiz.ebay.com/sell/finances/v1/seller_payouts
  - Auth: OAuth 2.0 (eBay token)
  - Limit: 200 per request, paginate if needed
  
STEP 3: Fetch Bol.com Orders
  - Module: HTTP Request (GET)
  - URL: https://api.bol.com/retailer/v10/orders
  - Auth: API Key header
  - Filter: status=PAID, createdAfter=48h ago
  
STEP 4: Standardize Data
  - Module: JavaScript (custom function)
  - Input: Array of all marketplace transactions
  - Output: Normalized schema
    {
      "source": "amazon|ebay|bol",
      "transaction_id": string,
      "amount_eur": decimal,
      "date": ISO date,
      "type": "order|refund|fee|settlement",
      "reference": string
    }
  
STEP 5: Deduplicate
  - Module: Postgres Database Query
  - Query: Check if transaction_id exists in posting_log
  - Filter: Keep only new transactions
  
STEP 6: Create Posting Records
  - Module: Postgres Insert
  - Table: marketplace_postings
  - Insert: All deduplicated transactions
  
STEP 7: Send Summary Email
  - Module: Gmail
  - To: finance@vendiamonoi.it
  - Body: "Sync complete: {X} Amazon, {Y} eBay, {Z} Bol transactions imported"
  
ERROR HANDLER:
  - If Step 1 fails (Amazon API error) → Retry 3x with 5min delay
  - If Step 4 fails (malformed data) → Send alert email, skip transaction
  - If Step 6 fails (DB error) → Pause scenario, escalate to DevOps
```

**Make.com Estimated Cost:**
- 3 syncs/day × 365 days = 1,095 scenario executions/year
- ~€200-300/month for Make Pro plan

---

### 7.2 Weekly Reconciliation Workflow

**Frequency:** Sundays 2 PM
**Duration:** 2-4 hours

**Scenario:**

```
TRIGGER: Schedule (Weekly, Sunday 2 PM)
  ↓
STEP 1: Fetch Bank Transactions (Qonto PSD2)
  - Module: HTTP Request (GET)
  - URL: https://api.qonto.eu/v2/transactions
  - Date range: Last 7 days
  - Filter: All transactions (debit + credit)
  
STEP 2: Fetch GL Posting Records
  - Module: Postgres Query
  - Query: SELECT * FROM postings WHERE posted_date >= TODAY-7
  
STEP 3: Match Transactions
  - Module: JavaScript (matching algorithm)
  - Input: Bank txns + GL postings
  - Output: 
    {
      "matches": [...],
      "exceptions": [...]
    }
  
STEP 4: Auto-Approve 99%+ Confidence Matches
  - Module: Postgres Update
  - Table: posting_reconciliation
  - Set: matched=true, reconciled_date=NOW, confidence_score=99
  
STEP 5: Create Exception Tasks
  - For each unmatched transaction:
    - Create Notion database entry (Exception Queue)
    - Fields: bank amount, GL amount, variance, date diff
  
STEP 6: Generate Report
  - Module: Create PDF
  - Template: Weekly reconciliation summary
  - Data: Stats, exception list, matched pairs
  - Recipient: finance@vendiamonoi.it
  
STEP 7: Slack Notification
  - Module: Slack
  - Message: "Weekly reconciliation complete: {match_rate}% matched"
  - Channel: #finance
```

---

### 7.3 Monthly Close Automation Workflow

**Frequency:** Last day of month, 6 PM
**Duration:** 3 hours

**Scenario:**

```
TRIGGER: Schedule (Monthly, Last day 6 PM)
  ↓
STEP 1: Final Reconciliation Run
  - Run Steps 1-7 from Weekly Workflow (above)
  - Stricter matching rules: 100% confidence only
  
STEP 2: Calculate VAT Totals
  - Module: Postgres Query
  - Group all transactions by VAT rate (22%, 10%, 4%, 0%)
  - Calculate: Total sales, Total VAT, Total net by rate
  
STEP 3: Validate VAT Calculation
  - Module: JavaScript
  - Logic: Sum of (line_amount × rate%) = total_vat
  - If mismatch > €1 → Flag for review
  
STEP 4: Generate FatturaPA Invoices
  - For each customer with orders shipped that month:
    - Module: Execute Python script
    - Input: Order data, customer data, VAT summary
    - Output: FatturaPA XML files (one per customer)
  
STEP 5: Digitally Sign Invoices
  - Module: Custom API call (to Aruba PKI)
  - Sign all FatturaPA files with qualified cert
  - Output: .xml.p7m files
  
STEP 6: Submit to SDI
  - Module: HTTP Request (POST)
  - Endpoint: Aruba intermediary API
  - Upload: All signed invoices
  - Log: Submission IDs for tracking
  
STEP 7: Create GL Close Journal Entry
  - Posting:
    DR: Marketplace Revenue €XXX
    CR: Accrued Marketplace Settlement €XXX
  
STEP 8: Generate Close Reports
  - Module: Create PDF documents:
    1. Monthly P&L summary
    2. Reconciliation report
    3. VAT summary (by rate)
    4. FatturaPA submission log
  
STEP 9: Lock Period & Archive
  - Module: Postgres Update
  - Set: accounting_period_status = 'CLOSED'
  - Archive all detail records to cold storage
  
STEP 10: Send Close Confirmation
  - Module: Email (CFO + Accountant)
  - Attachments: All close reports
  - Body: "Month closed: {month}/{year}, {X} invoices, {Y}% matched"
```

---

## 8. ACCOUNTING INTEGRATION (A2X, Link My Books, Synder, Custom API)

### 8.1 Platform Comparison

| Platform | Setup Time | Monthly Cost | Strengths | Weaknesses |
|----------|-----------|------------|----------|----------|
| **A2X** | 2-3 weeks | €99-299 | Amazon specialist, auto-reconcile, FBA handling | Limited to Amazon, eBay add-on expensive |
| **Link My Books** | 1-2 weeks | €79-199 | Multi-channel (8+ platforms), simple setup | Less sophisticated matching, UK-focused |
| **Synder** | 1-2 weeks | €99-499 | Real-time sync, good reporting, customer support | Can be slow with large volumes |
| **Custom API** | 4-8 weeks | $0 recurring | Full control, no vendor lock-in, optimized for Vendiamonoi | Requires dev resources, ongoing maintenance |

**Vendiamonoi Recommendation:** Custom API (long-term ROI justified by scale)

---

### 8.2 Chart of Accounts Mapping

**Standard e-commerce CoA structure:**

```
ASSETS (1000-1999)
├─ 1100 Cash & Bank Accounts
│  ├─ 1110 Qonto EUR Account
│  ├─ 1120 Qonto GBP Account
│  └─ 1130 Marketplace Settlement Account
├─ 1200 Accounts Receivable
│  ├─ 1210 Customer AR
│  └─ 1220 Marketplace AR (pending settlements)
├─ 1300 Inventory
│  ├─ 1310 Finished Goods
│  └─ 1320 Inventory Reserve
└─ 1400 Prepaid Expenses

REVENUE (4000-4999)
├─ 4100 Product Sales Revenue
│  ├─ 4110 Amazon Sales
│  ├─ 4120 eBay Sales
│  ├─ 4130 Bol.com Sales
│  └─ 4140-4160 (Other marketplaces)
├─ 4200 Shipping Revenue
├─ 4300 Marketplace Fees (negative revenue)
│  ├─ 4310 Amazon Commission
│  ├─ 4320 eBay Commission
│  └─ 4340 FBA Fees
└─ 4400 Returns & Allowances

COST OF GOODS SOLD (5000-5999)
├─ 5100 Inventory Purchases
└─ 5200 Inventory Adjustments

OPERATING EXPENSES (6000-6999)
├─ 6100 Personnel
├─ 6200 Marketing & Advertising
├─ 6300 Technology & Software
│  ├─ 6310 Accounting Software
│  ├─ 6320 Make.com/n8n Workflows
│  ├─ 6330 API costs
│  └─ 6340 Cloud Storage
├─ 6400 Professional Services
├─ 6500 Facilities & Admin
├─ 6600 Bank & Financial Charges
│  ├─ 6610 Qonto Bank Fees
│  ├─ 6620 Wire Transfer Fees
│  └─ 6630 FX Fees
└─ 6700 Depreciation & Amortization

OTHER INCOME/EXPENSE (7000-7999)
├─ 7300 Foreign Exchange Gain/Loss
│  ├─ 7310 FX Gain
│  └─ 7320 FX Loss
└─ 7400 Miscellaneous Income
```

---

### 8.3 Custom GL Integration (Python Example)

**Pseudo-code for posting marketplace transactions:**

```python
class AccountingGateway:
    def post_marketplace_transaction(self, transaction):
        """
        Posts a single marketplace transaction to GL
        """
        # Map marketplace to GL code
        revenue_account = {
            "amazon": "4110",
            "ebay": "4120",
            "bol": "4130"
        }[transaction["source"]]
        
        commission_account = {
            "amazon": "4310",
            "ebay": "4320"
        }[transaction["source"]]
        
        # Determine account if refund
        if transaction["txn_type"] == "REFUND":
            revenue_account = "4410"  # Sales Returns
        
        # Build GL entry (double-entry bookkeeping)
        if transaction["txn_type"] == "ORDER":
            lines = [
                {
                    "account": "1200",  # AR - Marketplace
                    "debit": transaction["gross_amount"],
                    "credit": 0,
                    "description": f"Order {transaction['txn_id']}"
                },
                {
                    "account": revenue_account,
                    "debit": 0,
                    "credit": transaction["gross_amount"],
                    "description": f"Sales revenue {transaction['source']}"
                },
                {
                    "account": commission_account,
                    "debit": abs(transaction["commission"]),
                    "credit": 0,
                    "description": f"Marketplace commission"
                }
            ]
        
        # Create journal entry
        journal_entry = {
            "date": transaction["txn_date"],
            "reference": transaction["txn_id"],
            "description": f"{transaction['source']} {transaction['txn_type']}",
            "lines": lines
        }
        
        # POST to GL
        response = requests.post(
            f"{self.base_url}/journal-entries",
            json=journal_entry,
            headers=self.headers
        )
        
        return {"success": response.status_code == 201, "je_id": response.json().get("id")}
```

---

## 9. ERROR HANDLING & EXCEPTION RESOLUTION

### 9.1 Common Errors & Workflows

**Error Type 1: Duplicate Transactions**

```
Detection:
  Same marketplace_id + amount + date within 24 hours
  Occurrence: ~2-3% of transactions (API retry, webhook duplicate)

Resolution:
  1. Flag first occurrence as "canonical"
  2. Flag second as "duplicate"
  3. If both posted to GL → reverse duplicate entry
  4. Create note: "Duplicate removed, kept ID: XXX"
```

**Error Type 2: Missing Fees**

```
Detection:
  Expected commission not in marketplace settlement
  Occurrence: ~0.5%

Resolution:
  1. Check marketplace report directly (API call)
  2. Check if fee was waived (promotional, new seller discount)
  3. If fee confirmed missing:
    a) Create GL adjustment entry (record missing fee)
    b) Create task: "Follow up with {marketplace} support"
    c) Set threshold: If >€50 missing, escalate to finance
```

**Error Type 3: Rounding Errors**

```
Detection:
  Bank amount ≠ sum of settled orders (difference < €0.05)
  Occurrence: ~15%

Resolution:
  If difference ≤ €0.01:
    → Auto-accept, post rounding difference to 'Misc Income/Expense'
    
  If €0.01 < difference ≤ €0.05:
    → Flag for 24-hour review window
    → If still unmatched, post rounding difference
    
  If difference > €0.05:
    → Escalate to exception queue
```

**Error Type 4: Chargebacks & Disputes**

```
Detection:
  Negative transaction from marketplace
  Occurrence: ~1-2%

Resolution:
  1. Get chargeback details from marketplace
  2. Check if customer dispute is open
  3. Create credit memo (reverse original sale)
  4. Escalate to Finance Manager
```

**Error Type 5: Currency Fluctuation (FX)**

```
Detection:
  Multi-currency settlement with FX variance > 1%
  Occurrence: Daily

Calculation:
  eBay GBP settlement: £1,000.00
  FX Rate expected: 1.175
  Expected EUR: €1,175.00
  Bank actually received: €1,172.50
  Loss: €2.50

Resolution:
  1. Compare actual rate to ECB daily rate (official)
  2. If variance within 0.5%, accept (market accepted)
  3. If variance > 0.5%, check with bank
  4. Post FX gain/loss to GL: "7310 FX Gain/Loss"
```

---

### 9.2 Exception Queue Management (Notion)

**Database Schema:**

```
Table: Exception Queue
Columns:
  ├─ ID (unique)
  ├─ Error Type (dropdown: Duplicate, Missing Fee, Rounding, Chargeback, Currency, Other)
  ├─ Source Marketplace (amazon, ebay, bol, etc.)
  ├─ Amount (€)
  ├─ Date Detected
  ├─ Description (text)
  ├─ Status (New, Investigating, Resolved, Escalated)
  ├─ Assigned To (person)
  ├─ Due Date
  ├─ Related GLEntry (link to GL posting)
  └─ Resolution Notes (text)
```

---

## 10. AUTOMATION ROI ANALYSIS

### 10.1 Cost-Benefit Analysis

**Setup Costs (One-time):**

| Component | Cost | Notes |
|-----------|------|-------|
| Make.com Scenarios (setup) | €2,000-3,000 | Consultant setup + tuning |
| Custom API Development (3-4 weeks) | €5,000-8,000 | For GL integration |
| FatturaPA/SDI Integration | €1,500-2,000 | Via Aruba or In-house |
| Qonto API Integration | €500 | Setup + testing |
| Training (Finance team) | €1,000 | 2-3 days onboarding |
| **Total One-Time** | **€10,000-16,000** | ~1-2 months developer time |

**Recurring Costs (Annual):**

| Component | Monthly | Annual | Notes |
|-----------|---------|--------|-------|
| Make.com Pro | €200-300 | €2,400-3,600 | For scenario automation |
| Aruba PKI/SDI | €100 | €1,200 | FatturaPA signing + transmission |
| Qonto API | Included | Included | Bundled in Qonto account |
| Cloud Storage (archives) | €50 | €600 | AWS S3 or similar |
| Ongoing Support (0.5 FTE) | €1,500 | €18,000 | Maintenance + improvements |
| **Total Annual** | **~€1,850-2,350** | **€22,200-28,200** | Scales with volume |

---

### 10.2 Manual Effort Saved

**Before Automation:**
```
Daily Activities:
  • Download marketplace reports: 5 hours/week
  • Manual data entry: 8 hours/week
  • Bank reconciliation (manual match): 6 hours/week
  • Invoice OCR + entry: 10 hours/week
  
Weekly Activities:
  • Exception resolution: 8 hours
  • FatturaPA invoice creation: 6 hours
  • FatturaPA SDI submission: 2 hours
  
Monthly Activities:
  • Month-end reconciliation: 16 hours
  • Financial close: 20 hours
  • VAT calculation & compliance: 8 hours
  
Annual Effort: ~650 hours/year (1 FTE @ €30,000 salary)
```

**After Automation:**
```
Daily Activities:
  • Monitor automated syncs (review alerts only): 2.5 hours/week
  • Handle exception queue (5-10 items): 2 hours/week
  
Weekly Activities:
  • Weekly reconciliation review: 2 hours
  • Exception escalation: 2 hours
  
Monthly Activities:
  • Month-end close review: 4 hours
  • VAT compliance check: 2 hours
  
Annual Effort: ~180 hours/year (0.25 FTE)

Hours Saved: 650 - 180 = 470 hours/year
Cost Savings: 470 hours × €40/hour (blended rate) = €18,800/year
```

---

### 10.3 ROI Summary

**Year 1:**
- Setup Cost: €10,000-16,000
- Annual Recurring: €22,620-28,620
- Labor Savings: €18,800
- Compliance Savings: €5,000-10,000
- **Net Year 1 Cost:** ~€19,000-27,000 (investment year)

**Year 2+:**
- Annual Recurring: €22,620-28,620
- Labor Savings: €18,800
- Compliance Savings: €5,000-10,000
- **Net Annual Benefit:** €17,000+ (break-even + profitability)

**Payback Period:** ~18 months

**Verdict:** ROI is positive by Year 2-3, with increasing benefits as scale grows.

---

## CONCLUSION

Domain 5.6 Financial Automation provides Vendiamonoi with:

1. **Reconciliation at Scale** — 20-30+ marketplaces, 100+ suppliers, fully automated matching
2. **Compliance** — FatturaPA/SDI, VAT, EU regulations, audit trail
3. **Visibility** — Real-time GL postings, daily close capability
4. **Scalability** — Grows with business without proportional cost increase
5. **Risk Mitigation** — Error handling, exception queues, human oversight

**Implementation Timeline:**
- Phase 1 (Weeks 1-4): Data extraction
- Phase 2 (Weeks 5-8): Matching engine + GL integration
- Phase 3 (Weeks 9-12): FatturaPA + SDI setup
- Phase 4 (Weeks 13+): Optimization & monitoring

**Next Steps:**
1. Validate with finance team (spreadsheet pilot)
2. Set up Make.com Pro account
3. Build initial Amazon → GL pipeline
4. Expand to other marketplaces incrementally
5. Monitor exception queue, refine rules
6. Roll out to full monthly close (Month 4-5)

---

**Document End — Version 1.0 | April 2026**