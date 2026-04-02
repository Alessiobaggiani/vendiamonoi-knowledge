# Cross-Platform Automation — Make + n8n + Architettura Ibrida
## Operational Playbook per Vendiamonoi.it

**Document Version**: 2.0
**Last Updated**: 2026-04-02
**Audience**: DevOps, Automation Engineers, Product Operations
**Scope**: Digital Distribution Platform — 20+ Marketplaces, 100+ Suppliers

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Platform Comparison Matrix](#platform-comparison-matrix)
3. [Hybrid Architecture Patterns](#hybrid-architecture-patterns)
4. [Operational Playbooks](#operational-playbooks)
5. [Data Store Schemas](#data-store-schemas)
6. [Monitoring & Alerting](#monitoring--alerting)
7. [Scaling & Performance](#scaling--performance)
8. [Disaster Recovery](#disaster-recovery)

---

## Executive Summary

Vendiamonoi.it's automation infrastructure spans two primary platforms:

- **Make (Integromat)**: Webhooks, rapid prototyping, external API integrations
- **n8n**: Complex data transformations, internal workflows, long-running processes

This document provides a comprehensive operational guide for managing both platforms as a unified system.

---

## Platform Comparison Matrix

### Latency & Performance

| Scenario | Make | n8n | Winner | Notes |
|----------|------|-----|--------|-------|
| Webhook to API call | 200-400ms | 100-300ms | n8n | n8n self-hosted = lower latency |
| Complex transformation (100K items) | 45-60s | 8-12s | n8n | n8n handles bulk processing better |
| Real-time order sync (1 item) | 500ms | 250ms | n8n | But Make sufficient for orders |
| Report generation (1M rows) | Not recommended | 2-4min | n8n | Make would timeout |
| Webhook reliability | 99.2% | 99.8% | n8n | Self-hosted = more stable |

### Feature Comparison

#### Make Strengths

1. **Quick Integration Setup**
   - 1000+ pre-built integrations
   - Visual UI requires minimal JSON/code
   - Fast prototyping (hours vs. days)

2. **Specific Use Cases**
   - Customer support ticket routing
   - Marketing automation workflows
   - Simple data synchronization
   - Notification chains

3. **Ecosystem**
   - Strong community templates
   - GPT integration (AI 50 app)
   - Zapier competitor parity

#### Make Weaknesses

- CPU/memory limits for transformations
- Timeout at 10 minutes
- Limited custom code (no direct database access)
- Queue depth visibility limited
- Pricing scales with operations count
- Webhook v3 requires maintenance

#### n8n Strengths

1. **Complex Data Processing**
   - Handle 100K+ item transformations
   - Custom JavaScript/Python logic
   - Direct database access
   - Long-running workflows (hours)

2. **Performance**
   - Bulk operations (database, spreadsheets)
   - XML parsing + transformation
   - Financial reconciliation with precision

3. **Scalability**
   - Horizontal scaling (worker nodes)
   - Queue depth monitoring
   - Resource allocation per workflow

4. **Cost**
   - Fixed infrastructure cost
   - Unlimited executions
   - No per-operation pricing

#### n8n Weaknesses

- Steeper learning curve
- Requires deployment expertise
- Fewer pre-built integrations
- Community smaller than Make
- Debugging more complex

---

## Hybrid Architecture Patterns

### Pattern 1: Fast Path (Make)

**When to use**: Customer-facing operations requiring <1s latency

```
Customer Event
    ↓
Make Webhook
    ↓
[Decision Logic]
    ├→ Update CRM
    ├→ Send Email
    └→ Notify Slack
    ↓
Response (~500ms)
```

**Examples**:
- Order confirmation emails
- Customer support ticket creation
- Marketing automation triggers

### Pattern 2: Batch Processing (n8n)

**When to use**: High-volume data transformation, 1-100+ minutes acceptable

```
Data Source (SFTP/API/DB)
    ↓
n8n Bulk Import
    ├→ Parse/Validate (per item)
    ├→ Transform/Enrich
    ├→ Deduplicate
    └→ Normalize
    ↓
Load to Primary DB
    ↓
Sync to Mirrors
```

**Examples**:
- Supplier catalog imports (100K+ items)
- Daily reconciliation reports
- Historical data migration

### Pattern 3: Complex Integration (n8n + Custom Code)

**When to use**: Domain-specific logic requiring custom algorithms

```
Make Webhook (Trigger)
    ↓
n8n Workflow
    ├→ Module 1: Fetch supplier data
    ├→ Module 2: Calculate optimal carrier
    ├→ Module 3: Get real-time rates
    ├→ Module 4: Denormalize for display
    ├→ Module 5: Store in Supabase
    ├→ Module 6: Log to Supabase audit table
    └→ Module 7: Notify team su Slack (order summary)
```

Make handles steps 1-7 senza codice. Latency totale: ~2-5 secondi.

### 1.3 Strengths di n8n per Vendiamonoi

**n8n eccelle in:**

1. **Complex Data Transformation Pipelines**
   - ETL multistep per supplier catalogs (100K+ items)
   - Normalizzazione formati diversi (CSV, JSON, XML, custom formats)
   - Merging dati da multiple sources
   - Deduplication + fuzzy matching

2. **Custom Code Logic**
   - JavaScript/Python nodes per algoritmi custom
   - Direct access a Supabase, Postgres, Google Sheets
   - No limitations on processing time (vs Make's 10-minute timeout)

3. **Operational Observability**
   - Queue monitoring (depth, age)
   - Execution history con detailed logs
   - Error alerting con retry logic
   - Integration con Slack, email

4. **Performance per Euro**
   - Fixed infrastructure cost
   - Unlimited executions
   - Economies of scale (vs Make's per-operation pricing)

---

## Operational Playbooks

### Playbook 1: Daily Supplier Catalog Sync (n8n)

**Schedule**: 02:00 UTC, every day  
**Participants**: 100+ suppliers, 10M+ SKU variations  
**Duration**: 15-45 minutes (depends on changes)

**High-Level Flow**:

```
┌─────────────────────────────────┐
│ 1. Fetch from supplier SFTPs    │ (parallel, 5 at a time)
├─────────────────────────────────┤
│ 2. Validate schemas             │
├─────────────────────────────────┤
│ 3. Enrich with marketplace data │
├─────────────────────────────────┤
│ 4. Denormalize + price optimize │
├─────────────────────────────────┤
│ 5. Upsert to primary DB         │
├─────────────────────────────────┤
│ 6. Sync to read replicas        │
├─────────────────────────────────┤
│ 7. Alert on errors/warnings     │
└─────────────────────────────────┘
```

**Detailed Steps**:

1. **Fetch Phase** (5-10 min)
   - Loop: supplier_ids
   - SFTP connect (credentials from n8n vault)
   - List files matching pattern: `catalog_*.csv` or `*.json`
   - Download latest version
   - Validate file integrity (checksum if provided)
   - Log: fetch status, file size, record count

2. **Validation Phase** (2-5 min)
   - Per supplier:
     - Check required fields (sku, name, price)
     - Validate data types (price = number, sku = string)
     - Check for duplicates within file
     - Detect schema changes (new fields, removed fields)
   - Alert: if schema changed, escalate to supplier contact

3. **Enrichment Phase** (3-8 min)
   - For each SKU:
     - Lookup current marketplace presence
     - Fetch live inventory from supplier API (if available)
     - Get shipping dimensions from supplier master data
     - Lookup VAT/tax codes by country
     - Calculate potential profit margin

4. **Denormalization + Optimization** (5-15 min)
   - For each marketplace + SKU combination:
     - Create variant (color, size, etc.)
     - Calculate optimal price (supply, demand, competitor data)
     - Denormalize shipping cost per marketplace
     - Set marketplace-specific fulfillment method
   - Output: denormalized table (SKU × Marketplace × Variant)

5. **Upsert Phase** (2-5 min)
   - Batch insert into `catalog_active`
   - Update `catalog_versions` history
   - Soft-delete old variants
   - Update `last_sync_at` timestamp
   - Transaction: all-or-nothing per supplier

6. **Sync to Read Replicas** (1-3 min)
   - Trigger Supabase → Google Sheets sync
   - Trigger Supabase → Elasticsearch indexing
   - Update Redis cache (ttl: 6 hours)

7. **Alert Phase**
   - If any step fails:
     - Log error + context
     - Notify ops-team Slack channel
     - Increment failure counter
     - If 3+ consecutive failures: page on-call engineer

**Success Criteria**:
- 100% of suppliers synced within 45 minutes
- 0 schema validation errors
- >95% of SKUs successfully upserted
- <100ms latency for GET /catalog endpoints (post-sync)

**Rollback Plan**:
- If critical errors: restore from previous night's snapshot
- If partial supplier failure: re-sync just that supplier
- If data quality issue: toggle `catalog_active` to read-only, investigate

---

### Playbook 2: Real-Time Order Processing (Make)

**Trigger**: Webhook from marketplace (orders API)  
**Latency SLA**: <2 seconds end-to-end  
**Concurrency**: 100-500 concurrent orders

**Flow**:

```
Marketplace Order
    ↓ (Webhook)
Make Module 1: Parse order JSON
    ↓
Make Module 2: Validate address + payment
    ↓
Make Module 3: Lookup SKU details (n8n call)
    ↓
Make Module 4: Calculate carrier + cost
    ↓
Make Module 5: Create shipment label
    ↓
Make Module 6: Update order status in marketplace
    ↓
Make Module 7: Notify customer + supplier
    ↓
Response 200 OK (~1.5 seconds)
```

**Modules**:

**Module 1: Parse Order**
```json
{
  "marketplace_id": "marketplace_sku",
  "order_id": "unique_id",
  "items": [
    {
      "marketplace_sku": "...",
      "quantity": 2,
      "price_per_unit": 49.99
    }
  ],
  "shipping_address": {...},
  "total_price": 99.98
}
```

**Module 2: Validate**
- Address: zip code format, country code
- Payment: stripe/PayPal receipt exists
- Inventory: check availability (query n8n)

**Module 3: Lookup SKU Details** (call to n8n endpoint)
```
POST /webhook/lookup-sku
{
  "marketplace_sku": "...",
  "quantity_ordered": 2
}
```

Response:
```json
{
  "internal_sku": "PROD-123",
  "supplier_id": "SUP-456",
  "weight_kg": 0.5,
  "dimensions": "10x10x10cm",
  "available_qty": 100
}
```

**Module 4: Calculate Carrier**
- Dimensions + weight → carrier matrix
- Preferred carrier (DPD, DHL, GLS)
- Get real-time rate
- Check cost vs. margin (abort if unprofitable)

**Module 5: Create Shipment Label**
- Call carrier API (DPD, DHL, GLS)
- Generate tracking number
- Store label PDF

**Module 6: Update Marketplace**
- Call marketplace API: `POST /orders/{id}/fulfill`
- Send tracking number
- Status: "shipped"

**Module 7: Notify**
- Email customer (template with tracking)
- Slack: #orders channel (summary)
- Supplier: send via SFTP or API

**Error Handling**:
- If Module 3 fails: Use cached SKU data (6-hour TTL)
- If Module 4 fails: Default carrier (usually cheapest)
- If Module 5 fails: Manual label creation (queue task)
- If Module 6 fails: Retry with exponential backoff
- If Module 7 fails: Log error, non-blocking

**Scaling Considerations**:
- Make: handles 100-500 concurrent webhooks
- If >500/sec: buffer in n8n queue, process in batches
- Monitor Make response latency; alert if >3 seconds

---

### Playbook 3: Financial Reconciliation (n8n)

**Schedule**: Weekly (Sundays, 23:00 UTC)  
**Duration**: 10-30 minutes  
**Participants**: Accounting team, Finance Manager

**Goal**: Reconcile marketplace payouts, supplier invoices, and platform costs

**Flow**:

```
Marketplace APIs
    ↓
n8n Module 1: Fetch payout records
    ↓
n8n Module 2: Fetch supplier invoices (SFTP)
    ↓
n8n Module 3: Fetch platform charges (Stripe, AWS, etc.)
    ↓
n8n Module 4: Reconciliation logic
    ├→ Match transactions by date, amount, ID
    ├→ Identify discrepancies
    └→ Calculate net P&L
    ↓
n8n Module 5: Generate report
    ↓
Google Sheets (read-only for accounting team)
    ↓
Slack alert (summary + discrepancies)
```

**Module 1: Fetch Marketplace Payouts**
- For each marketplace (Amazon, eBay, etc.):
  - Call API: GET /payouts (date range: last 7 days)
  - Extract: payout_id, amount, date, status
  - Store in temp table

**Module 2: Fetch Supplier Invoices**
- SFTP to supplier folder: `/invoices/{current_week}/`
- Parse invoices (PDF → OCR or CSV)
- Extract: supplier_id, amount, date, items
- Store in temp table

**Module 3: Fetch Platform Charges**
- Stripe: GET /invoices (date range)
- AWS: describe costs (date range)
- Google Sheets (manual entry): operational costs
- Store in temp table

**Module 4: Reconciliation Logic**
```
For each payout:
  - Lookup corresponding invoices (supplier_id, date match)
  - Calculate: payout - invoices - platform_costs = profit
  - If discrepancy > 5%: flag for manual review

Output:
  - reconciled_transactions: list of matched, profit calculated
  - discrepancies: list of unmatched or large variances
  - summary: total_payouts, total_costs, net_profit
```

**Module 5: Generate Report**
- Write to Google Sheets: `Finance > Weekly Reconciliation`
- Format: table with columns
  - Marketplace
  - Payout Amount
  - Supplier Costs
  - Platform Costs
  - Net Profit
  - Status (Reconciled / Flag for Review)

**Module 6: Alert**
- Slack: #finance channel
  ```
  Weekly Reconciliation Summary
  Total Payouts: €50,000
  Total Costs: €35,000
  Net Profit: €15,000
  
  Discrepancies (1): [Details]
  ```

---

## Data Store Schemas

### Schema 1: Catalog Management

**Table**: `catalog_active`

| Column | Type | Notes |
|--------|------|-------|
| id | UUID | Primary key |
| internal_sku | String | System identifier |
| supplier_id | UUID | Foreign key to suppliers |
| marketplace_id | String | External marketplace ID |
| title | String | Product name |
| price | Decimal(10,2) | Current price |
| inventory | Integer | Available quantity |
| dimensions | JSON | {length, width, height, weight} |
| last_sync_at | Timestamp | From supplier sync |
| created_at | Timestamp | Record creation |
| updated_at | Timestamp | Last modification |

**Indexes**:
- `internal_sku` (unique)
- `marketplace_id` (unique per marketplace)
- `supplier_id` (for bulk operations)
- `last_sync_at` (for debugging)

### Schema 2: Orders

**Table**: `orders`

| Column | Type | Notes |
|--------|------|-------|
| id | UUID | Primary key |
| marketplace_id | String | E.g., "amazon_de" |
| marketplace_order_id | String | External order ID |
| status | Enum | pending, processing, shipped, delivered, cancelled |
| customer_email | String | For notifications |
| total_price | Decimal(10,2) | Total revenue |
| items | JSON Array | [{sku, quantity, unit_price}] |
| shipping_address | JSON | {street, city, zip, country} |
| tracking_number | String | Carrier tracking (if shipped) |
| created_at | Timestamp | Order placement |
| shipped_at | Timestamp | Shipment time |

**Indexes**:
- `marketplace_order_id` (unique per marketplace)
- `status` (for reporting)
- `created_at` (for time-based queries)

---

## Monitoring & Alerting

### Key Metrics

| Metric | Threshold | Alert | Action |
|--------|-----------|-------|--------|
| Webhook latency (p99) | >3s | Critical | Check Make → n8n communication |
| Supplier catalog sync time | >1 hour | Warning | Review data volume, optimize queries |
| Order fulfillment SLA | >24 hours | Warning | Check carrier delays, supplier issues |
| Reconciliation discrepancy | >5% | Critical | Manual audit, supplier investigation |
| Queue depth (n8n) | >10K items | Medium | Scale workers, review bottlenecks |
| API error rate | >1% | Warning | Check external service status |

### Alerting Setup

**Tool**: n8n (email/Slack alerts)

**Examples**:

1. **Supplier Sync Failure**
   ```
   IF supplier_sync_status = 'failed'
   THEN notify_slack('#ops', 'critical', "Supplier {name} sync failed")
   ```

2. **Order Processing Delay**
   ```
   IF order_created_at > NOW - 2 hours AND status = 'pending'
   THEN notify_slack('#orders', 'warning', "Order {id} stuck in pending")
   ```

3. **Queue Backlog**
   ```
   IF queue_depth > 10000
   THEN notify_slack('#devops', 'medium', "n8n queue depth {count}")
   ```

---

## Scaling & Performance

### n8n Worker Scaling

**Setup**: Kubernetes deployment, 3-5 worker nodes

**Auto-Scaling Rules**:
- If CPU > 70% for 5 minutes → add worker
- If CPU < 30% for 10 minutes → remove worker
- Min: 3 nodes, Max: 10 nodes

**Queue Tuning**:
- Batch size: 100 items (reduces context switching)
- Worker concurrency: 4 (per node)
- Memory per worker: 2GB

### Make Optimization

**Limits**: 
- Max 100 concurrent executions
- If exceeding: buffer in n8n queue, dequeue to Make in batches
- Consider webhook v3 migration for reliability

### Database Optimization

**Indexes**:
- On `order.created_at`, `order.status` (for reporting)
- On `catalog.supplier_id`, `catalog.marketplace_id` (for bulk operations)

**Connection Pooling**:
- Max pool size: 20 (n8n → database)
- Idle timeout: 30 seconds

---

## Disaster Recovery

### Backup Strategy

| Component | Frequency | Retention | Location |
|-----------|-----------|-----------|----------|
| n8n workflows | Daily | 30 days | GitHub |
| Database | Hourly | 7 days | Supabase backups + S3 |
| Supplier files | Daily | 90 days | S3 glacier |

### Recovery Procedures

**If n8n is down**:
1. Failover to Make-only mode (orders only, no bulk sync)
2. Queue catalog updates for later
3. ETA: 1-2 hours

**If database is down**:
1. Restore from latest backup (max 1 hour data loss)
2. Re-sync catalog + orders from sources
3. ETA: 2-4 hours

**If Make is down**:
1. Pause webhooks, queue in n8n
2. Resume once Make recovers
3. Replay queued items

---

## Summary

This playbook provides a comprehensive guide for managing Vendiamonoi.it's multi-platform automation ecosystem. Key principles:

- **Make**: Fast path, webhooks, external integrations
- **n8n**: Complex logic, bulk processing, long-running workflows
- **Hybrid**: Leverage strengths of both platforms

For detailed troubleshooting, monitoring, or scaling questions, see internal wiki or contact DevOps team.

---

**Document Maintenance**: 
- Review quarterly
- Update on major architecture changes
- Assign owner: [DevOps Lead]
- Last Reviewed: [Date]