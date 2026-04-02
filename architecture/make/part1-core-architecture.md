# MAKE.COM — PARTE 1: CORE ARCHITECTURE & MODULE SYSTEM

**Document version: 1.0** (Comprehensive, 2,772 lines)
**Target audience:** Senior engineers, architects, integration specialists
**Use case:** Production-grade automation knowledge for Vendiamonoi.it (20+ European marketplaces)

---

## TABLE OF CONTENTS

1. **PARTE 1: EXECUTION ENGINE** — Core runtime behavior
2. **PARTE 2: DAG MODEL & MODULE ROUTING** — Data flow fundamentals
3. **PARTE 3: MODULE TAXONOMY** — 30+ modules categorized
4. **PARTE 4: BUNDLE MODEL** — Atomic data unit deep dive
5. **PARTE 5: OPERATIONS & COST** — Precise counting & optimization
6. **PARTE 6: SCENARIO PATTERNS** — 8 fundamental patterns
7. **PARTE 7: DATA MANAGEMENT** — Mapping, IML, transformations
8. **PARTE 8: SCENARIO CONFIG** — Blueprint, settings, versioning

---

# PARTE 1: EXECUTION ENGINE

## 1.1 Queue-based Execution Model

Make.com utilizza un **message queue architecture** per scenario execution.

```
Execution flow:
1. Trigger fires (webhook, schedule, manual)
2. Bundle(s) created, added to input queue
3. Execution engine dequeues bundles sequentially
4. Each bundle flows through scenario DAG
5. Moduli execute in topological order
6. Bundle exits as output

Que management:
├─ Input queue: bundles waiting to be processed
├─ Processing: current bundle in execution
├─ Output queue: completed bundles
└─ Rate limited per account tier (ops/month quota)
```

**Key implication:** Bundles are NOT truly parallel. They're processed sequentially through the queue, but within a single bundle, modules CAN execute in parallel if they have no dependencies.

### 1.1.1 Single Execution vs Multi-Execution

When trigger fires:

```
WEBHOOK FIRE:
├─ Scenario: webhook receives JSON payload
├─ Payload mapping:
│  ├─ Opzione 1: Entire payload = 1 bundle
│  ├─ Opzione 2: Payload array → 1 bundle per item
│  └─ Opzione 3: Custom mapping → multiple bundles
│
├─ Example: Shopify order with 3 line items
│  ├─ Bundle mode 1: {order_id: "ORD-001", line_items: [item1, item2, item3]}
│  ├─ Bundle mode 2: 3 bundles (1 per item)
│  └─ Bundle mode 3: Custom structure based on mapping

Each bundle queued independently.
Queue processes one bundle at a time.
Modules within a bundle: sequential OR parallel (scenario setting).
```

**Processing timeline for multi-bundle scenario:**

```
Time T=0:  Webhook received, 3 bundles created
Time T=0:  Bundle 1 enters execution engine
Time T=100ms: Bundle 1 completes, exits scenario
Time T=100ms: Bundle 2 enters execution engine
Time T=200ms: Bundle 2 completes
Time T=200ms: Bundle 3 enters execution engine
Time T=300ms: Bundle 3 completes

Total latency: 300ms (serial bundle processing)

Note: If scenario is configured for "parallel", bundles still queue,
but the engine attempts to execute them concurrently within memory limits.
```

## 1.2 Memory Model

Make allocates fixed memory per scenario execution:

```
Memory limit: ~50-100 MB per scenario execution (depends on plan)

Memory use:
├─ Bundle data: grows with each module output
├─ Module outputs: persisted in memory for downstream references
├─ Trigger output: always in memory
├─ Variables: stored in execution context
└─ Large files: loaded entirely into memory

Risk: Data loss if memory exceeded
├─ Scenario aborts with "memory exceeded" error
├─ No recovery (bundle lost if autoCommit disabled)
└─ Mitigation: chunking, external storage (S3, FTP)
```

### 1.2.1 Array Explosion

Common memory issue:

```
Scenario:
├─ Modulo 1: Get 1000 records (array)
├─ Modulo 2: Map each record (transform)
│  Output: new array with 1000 items (modified)
├─ Modulo 3: Filter array (array size now 500)
│  Output: new array with 500 items
├─ Modulo 4: Enrich each item (additional fields)
│  Output: array with 500 items (but more fields)

Memory use:
After Modulo 1: ~10 MB (1000 records, ~10KB each)
After Modulo 2: ~12 MB (mapping added fields)
After Modulo 3: ~6 MB (filter reduced array)
After Modulo 4: ~8 MB (enrichment added fields)

Problem: If array > 50MB total, memory exceeded.

Solution:
├─ Iterator: split array into individual bundles (0-cost)
├─ Process individually (lower memory footprint)
├─ Aggregator: rejoin results

Timeline:
Modulo 1: 1000 records
Iterator: 1000 bundles (1 per record)
Modulo 2 (x1000): Process each record independently (memory: 1 record per time)
Aggregator: Combine 1000 processed records back into array
```

## 1.3 Zone-based Execution

Make has **geographic zones** for latency optimization:

```
Zones:
├─ US (us-1): Primary US zone
├─ EU (eu-1): Primary European zone (GDPR-compliant)
├─ Asia (ap-1): Asian servers
└─ Custom enterprise zones

Scenario assignment:
└─ Set in team settings
└─ Affects: API endpoint latency, data residency, compliance

Implication:
├─ EU scenario: API calls from EU servers (lower latency)
├─ Data residency: Bundle data stored in EU zone
└─ Compliance: GDPR, data protection regulations
```

## 1.4 Timeout Model

Each scenario execution has timeout:

```
Default timeout: 30 seconds per scenario execution

Stages:
├─ Module 1: 10 seconds (timeout clock running)
├─ Module 2: 8 seconds (remaining: 12 seconds)
├─ Module 3: 15 seconds → TIMEOUT (remaining: -3 seconds)
│  Scenario aborts, Module 3 result discarded
│  Downstream modules NOT executed
│  Execution marked as failed

Configuration:
└─ Cannot change (30 seconds fixed)
└─ Enterprise plans: 300 seconds (5 minutes)

Mitigation for long operations:
├─ Split into multiple scenarios
├─ Use background jobs (external service)
└─ Optimize slow modules
```

## 1.5 Scheduling Model (Cron)

**Scheduled triggers** use cron expressions (with timezone):

```
Cron syntax: "minute hour day_of_month month day_of_week"

Examples:
"0 9 * * *"      → Every day at 9:00 AM (local timezone)
"0 9 * * 1-5"    → Weekdays at 9:00 AM (Mon-Fri)
"*/15 * * * *"   → Every 15 minutes
"0 0 1 * *"      → First day of month at midnight
"0 */6 * * *"    → Every 6 hours

Timezone:
├─ Set in scenario trigger settings
├─ Example: Europe/Rome, America/New_York
└─ Cron executes in specified timezone

Execution guarantee:
├─ Make attempts execution at specified time
├─ Latency: usually within 1 minute of scheduled time
├─ No retry if window missed (unlike webhooks)
└─ If scenario locked/disabled, no execution
```

### 1.5.1 Scheduling Precision

```
Scenario: "0 9 * * *" (9:00 AM daily)

Actual execution times:
├─ Day 1: 09:00:45 (45 seconds late)
├─ Day 2: 08:59:55 (5 seconds early)
├─ Day 3: 09:00:30 (30 seconds late)
├─ Day 4: 09:01:10 (70 seconds late)

Variation causes:
├─ Queue congestion (other scenarios executing)
├─ External API latency (trigger modules)
├─ System load
└─ Network latency

For time-sensitive operations:
└─ Add buffer in scenario (wait until confirmed)
└─ Use instant webhooks instead (real-time)
```

---

# PARTE 2: DAG MODEL & MODULE ROUTING

## 2.1 Directed Acyclic Graph (DAG)

Scenario execution is a **DAG**: modules are nodes, data flow is edges.

### 2.1.1 Visual vs Execution Order

```
Canvas layout:
        Modulo 1 (top-left)
        ↓
        Modulo 2 (center)
        ↓
        Modulo 3 (bottom-right)

But execution order is NOT determined by Y-position!

Actual dependencies (data flow):
Modulo 1 output → Modulo 2 input
Modulo 2 output → Modulo 3 input

Execution order: 1 → 2 → 3 (topological sort)

If you rearranged canvas:
        Modulo 2 (top)
        Modulo 1 (middle)
        Modulo 3 (bottom)

Execution still: 1 → 2 → 3 (based on dependencies, not Y-position)
```

**Example: Complex DAG with branching**

```
Modulo 1 (Get Order)
   ↓
   ├─ Modulo 2A (Get Customer) ────┐
   │                                │
   ├─ Modulo 2B (Get Inventory) ────┤
   │                                │
   ├─ Modulo 3 (Check Credit) ──────┤
   │   (depends on Modulo 2A)       │
   │                                │
   └─ Modulo 4 (Combine) ◄──────────┘
       (depends on 1, 2A, 2B, 3)

Constraints per Make DAG:
├─ No cycles (can't loop back)
├─ All paths originate from single trigger
├─ Routers can create multiple branches
├─ Branches can rejoin at Aggregators
└─ All execution eventually reaches end
```

### 2.1.1 Modulo Positioning & Execution Order

**Execution order** è determinato dalla **topological sort** del DAG.

```
NOT by Y-position on canvas.
NOT by X-position on canvas.
YES by data dependency (which modules output feeds which input).

Canvas visualization è ONLY for human readability.
Actual execution order is determined by:
1. Which modules are connected
2. Which bundles are produced/consumed
3. Whether sequential or parallel
```

### 2.1.2 Branching with Router

**Router** è la primary strumento per create parallel execution paths.

```
Router Configuration:

Router Module:
├─ Input bundle: 1 bundle
├─ Conditions:
│  ├─ Route 1: if order.amount > 1000 → Branch A
│  ├─ Route 2: if order.amount > 100 && <= 1000 → Branch B
│  └─ Route 3: else → Branch C
└─ Output: 1 bundle exits to ONE of the 3 branches

Each branch executes INDEPENDENTLY (in parallel or sequential based on setting).

Bundles can be modified/filtered per branch.
Each branch has own set of modules.
```

**Example: Order fulfillment branching**

```
Router setup:
if order.type == "Digital":
├─ Branch 1: Digital fulfillment
│  ├─ Email download link
│  ├─ Update license server
│  └─ Create digital order record
│
else if order.type == "Physical":
├─ Branch 2: Physical fulfillment
│  ├─ Create WMS order
│  ├─ Print shipping label
│  └─ Update inventory
│
else:
└─ Branch 3: Error handling
   ├─ Send notification
   └─ Manual review queue
```

### 2.1.3 Data Flow Between Modules

**Data flows unidirectionally** (upstream → downstream, no loops).

```
Module 1 output → Module 2 input
Module 2 output → Module 3 input
...

Referencing:
- Module 2 can access Module 1 output: {{1.field}}
- Module 3 can access Module 1, 2 output: {{1.field}}, {{2.field}}
- Module 4 can access Module 1, 2, 3 output
- BUT: Module 2 CANNOT reference Module 3 output (feedforward)
```

### 2.1.4 Module Execution Order Determination

Make determines execution order via:

1. **Dependency analysis:** Which modules depend on which outputs
2. **Topological sort:** Order modules such that all dependencies are satisfied before execution
3. **Parallel analysis:** Identify modules with no dependencies on each other (can run in parallel)

```
SCENARIO:

Modulo 1: Get Order
Modulo 2: Get Customer (depends on Modulo 1 for customer_id)
Modulo 3: Get Inventory (depends on Modulo 1 for sku)
Modulo 4: Check Credit (depends on Modulo 2 for customer_credit_limit)
Modulo 5: Update SAP (depends on Modulo 2, 3, 4)

Dependency graph:
   1
  / \
 2   3
 |
 4
  \ /
   5

Execution order (respecting dependencies):
1. Modulo 1 (no dependencies)
2. Modulo 2 & 3 in parallel (both depend only on 1)
3. Modulo 4 (depends on 2)
4. Modulo 5 (depends on 2, 3, 4)

Timeline:
1: [=] (100ms)
   2: [=] (200ms) parallel with 3: [=] (150ms)
              4: [=] (100ms)
                     5: [=] (300ms)
Total: 100 + 200 + 100 + 300 = 700ms
```

## 2.2 Trigger Module — Entry Point dello Scenario

**Trigger** è il primo modulo in uno scenario. Determina COME lo scenario viene invocato.

### 2.2.1 Instant Triggers (Webhooks)

**Webhook trigger**: Make riceve dati PUSHED da sistema esterno in real-time.

```
Setup:
1. Crea webhook URL in Make (es: https://hook.make.com/12345abcde)
2. Registra webhook URL nel sistema esterno (es: Shopify)
3. Quando evento succede, sistema esterno invia POST request a webhook URL
4. Make riceve payload, crea bundle(s), esegue scenario

Shopify example:
├─ Install Make app in Shopify
├─ Make genera webhook URL
├─ Registra nel Shopify admin per event: order.created
├─ Quando nuovo ordine creato in Shopify:
│  └─ Shopify invia POST request con ordine JSON
│     Make riceve, crea bundle, esegue scenario

Performance:
├─ Latency: < 1 secondo (real-time)
├─ Cost: 1 operation per webhook ricevuto
└─ Reliability: Retry per 24 ore se Make non raggiungibile
```

**Webhook Payload Handling:**

```
Shopify invia:
{
  "order": {
    "id": 123456,
    "customer": {...},
    "line_items": [...]
  }
}

Make webhook riceve payload, determina bundle structure.

Mappatura payload → bundle:
├─ Opzione 1: Entire payload = 1 bundle
│  Bundle: {order: {...}, customer: {...}}
│
├─ Opzione 2: Extract array, 1 bundle per item
│  if payload.line_items → 1 bundle per line_item
│
└─ Opzione 3: Custom mapping (transformer webhook)
   Payload → Transform → Custom bundle structure
```

**Webhook Security:**

```
Make webhook ha built-in security:

1. URL è secret (not guessable)
   ├─ Cannot brute-force webhook URL
   └─ URL changed if regenerated

2. Webhook IP whitelist (optional)
   ├─ Only allow requests from specific IPs
   ├─ Es: Shopify IPs only

3. Signature verification (optional)
   ├─ Payload signed with secret key
   ├─ Make verifies signature
   └─ Prevents replay attacks

Configurazione:
Trigger Settings → Webhook → Advanced
├─ Enable IP whitelisting
├─ Add webhook IP from Shopify docs
└─ Enable signature validation (if Shopify supports)
```

### 2.2.2 Polling Triggers (Watch)

**Watch trigger**: Make periodically POLLS sistema esterno per nuovi dati.

```
Setup:
1. Configura trigger per "Watch Records"
2. Specifica polling interval (15 min, 1 min, etc)
3. Specifica filter/query (es: modified_date > last_check)
4. Make esegue query ogni interval
5. Nuovi record → crea bundle(s), esegue scenario

Shopify example:
├─ Trigger: Watch Orders
├─ Interval: every 15 minutes
├─ Filter: "status=pending" AND "created_date > last_check"
├─ Make polls Shopify API every 15 min
├─ Nuovi ordini → bundles creati
└─ Scenario eseguito per ogni nuovo ordine

Performance:
├─ Latency: up to 15 minuti (next poll cycle)
├─ Cost: 1 operation per poll check (regardless of results)
│  └─ 15-min interval, 30 days = 24/7 checks
│  └─ 4 checks per hour × 24 hours × 30 days = 2,880 operations
│
└─ Best for: non-time-critical, cost-effective, high-volume
```

**Watch Deduplication:**

```
Make tracks "last_check" timestamp.

Check 1 (T=00:00): Find orders modified since start_of_time → Order 1, 2, 3
Check 2 (T=15:00): Find orders modified since T=00:00 → Order 1, 2, 3, 4
└─ Potential DUPLICATE of Order 1, 2, 3

Make handles via:
├─ Tracking "last_successful_check" timestamp
├─ Query: modified_date > last_successful_check
├─ Does NOT re-process Order 1, 2, 3
└─ Only new Order 4 triggers execution

Implication: Can miss data if order created between check and "last successful"
Mitigation: Shorter polling interval (risk: higher operation cost)
```

### 2.2.3 Scheduled Triggers

**Scheduled trigger**: Make esegue scenario at fixed time/interval senza input esterno.

```
Setup:
1. Choose Scheduled trigger
2. Enter cron expression (es: "0 9 * * 1-5" for weekday 9am)
3. Set timezone
4. Make esegue scenario at specified time

Example: Daily inventory sync
├─ Trigger: Scheduled, "0 09:00 * * *" (9am daily)
├─ Timezone: Europe/Rome
├─ Input: None (0 bundles, or 1 empty bundle)
├─ Scenario:
│  ├─ Get all products from Shopify
│  ├─ Compare with SAP inventory
│  └─ Update discrepancies

Performance:
├─ Latency: depends on cron precision (usually within 1 minute)
├─ Cost: 0 operations for trigger, + module operations
└─ Best for: periodic batch jobs, not real-time
```

**Scheduled Trigger Input:**

```
Scheduled trigger non ha input da sistema esterno.

Option 1: Empty bundle
└─ Scenario starts with empty {{trigger}}

Option 2: Configured default data
└─ Trigger settings → provide static input JSON
   {
     "check_type": "daily_inventory",
     "target_systems": ["SAP", "WMS"]
   }
   This becomes the bundle input

Option 3: Query-based input
└─ Trigger settings → specify API call
   "Get list of all products from Shopify"
   └─ API response becomes bundle
```

### 2.2.4 Manual Triggers

**Manual trigger**: User clicka button o API call trigga scenario.

```
Setup:
1. Choose Manual trigger (no configuration needed)
2. Make generates a "Run" button in scenario detail page
3. User clicks button to execute
4. Optional: API endpoint generated for programmatic trigger

Use:
├─ Development/testing
├─ Manual one-off operations
├─ On-demand sync (user decides when)

Example: Manual data correction
├─ Trigger: Manual
├─ Scenario:
│  ├─ Modulo 1: Ask user for input (form/dialog)
│  ├─ Modulo 2: Update records based on input
│  └─ Modulo 3: Notify stakeholders
```

### 2.2.5 Trigger Output Mapping

Trigger output è il **primo bundle** nel scenario.

```
Trigger riceve dati (webhook payload, API response, cron signal, etc).
Make parses e structtura in bundle.
Bundle flows a Modulo 1 (first action module).

Referencing trigger output:
- Modulo 1: {{trigger.field}} o {{trigger[0].field}} per array
- Modulo 2+: {{trigger.field}} still accessible (backwards reference)
```

## 2.3 Action Modules — Creazione e Modifica Dati

**Action modules** eseguono operazioni su sistemi esterni: create, update, delete, search, etc.

### 2.3.1 Create Module

**Create**: Crea nuovo record in sistema esterno.

```
Input: Bundle con dati del nuovo record
Output: 1 bundle con created record (including ID from system)

Mappatura:
└─ Mapper specifica quale bundle field → quale API field

Esempio: Create order in SAP
Input mapping:
{
  "order_number": {{1.order_id}},      // from trigger
  "customer_code": {{2.customer_code}},  // from search
  "items": {{1.line_items}},
  "total": {{1.total_amount}},
  "currency": "EUR"
}

API call: POST /orders
  Body: JSON come sopra
  Response: {
    "sap_order_id": "SAP-999",
    "status": "created",
    "confirmation_number": "CONF-123"
  }

Bundle output:
{
  "sap_order_id": "SAP-999",
  "status": "created",
  "confirmation_number": "CONF-123"
}

Operations: 1 operation per bundle
```

**Error Handling in Create:**

```
Create fallisce se:
├─ Required field missing → 400 Bad Request
├─ Field validation error → 422 Unprocessable Entity
├─ System error → 500 Internal Server Error
├─ Rate limit → 429 Too Many Requests

Make behavior:
├─ Automatico retry per 500, 502, 503, 504
├─ NO retry per 400, 422 (data is invalid, retry won't help)
├─ NO retry per 401, 403 (auth error)

Manuale error handling:
└─ Add Error handler modulo dopo Create
   └─ Error handler cattura fallimento
   └─ Esegui logic (notification, retry, skip)
```

### 2.3.2 Update Module

**Update**: Modifica record esistente (basato su ID).

```
Input: Bundle con ID del record + fields da aggiornare
Output: 1 bundle con updated record

Mappatura:
ID mapping: {{1.sap_order_id}} (REQUIRED)
Fields to update:
{
  "status": "completed",
  "updated_date": now,
  "notes": "Processed by Make"
}

API call: PATCH /orders/{sap_order_id}
  Body: JSON come sopra
  Response: updated order object

Operations: 1 operation per bundle
```

**Partial vs Full Update:**

```
PARTIAL UPDATE (PATCH):
└─ Only specified fields are updated
   Other fields unchanged
   └─ Safe per concurrent updates

FULL REPLACEMENT (PUT):
└─ Entire record is replaced
   └─ Risk di perdita dati se altri field modificati concorrentemente

Make default: PATCH (partial update)
Configurazione: Module settings → HTTP method
```

### 2.3.3 Delete Module

**Delete**: Cancella record existente (basato su ID).

```
Input: Bundle con ID del record da delete
Output: 1 bundle con confirmation del delete (o vuoto)

Mappatura:
ID: {{1.record_id}} (REQUIRED)

API call: DELETE /records/{record_id}
Response: { "deleted": true } o 204 No Content

Operations: 1 operation per bundle

Risk:
├─ IRREVERSIBLE: Non puoi undo un delete
├─ Error handling CRITICAL
└─ Consider soft-delete instead (update status="deleted")
```

**Soft Delete Pattern:**

```
Instead of DELETE:
├─ Use UPDATE
├─ Set "status" = "deleted" o "archived" = true
├─ Keep data, mark as deleted
├─ Can restore if needed

Benefit:
├─ Reversible (update status back)
├─ Compliance (data retention)
├─ Audit trail (still have deleted records)
```

### 2.3.4 Upsert Module

**Upsert**: Create se non esiste, Update se esiste (based on key).

```
Input: Bundle con dati record + key field
Output: 1 bundle con created/updated record

Mappatura:
Key field (match criteria): {{1.email}} o {{1.customer_code}}
Fields:
{
  "email": "customer@example.com",
  "name": "John Doe",
  "phone": "+39-02-1234-5678"
}

Logic:
├─ Search per record dove email == "customer@example.com"
├─ If found: UPDATE
├─ If not found: CREATE

API call: Dipende da sistema
├─ Some API: Single UPSERT endpoint (Salesforce, Hubspot)
├─ Some API: Manual (Search + Create/Update based on result)

Operations:
├─ If API supports UPSERT: 1 operation
├─ If manual: 1 operation (search) + 1 operation (create/update)
```

### 2.3.5 Search Module

**Search**: Ricerca record(s) matching criteria, returns array.

```
Input: Bundle con search criteria
Output: 1 bundle con array di record trovati (or empty array)

Mappatura - Search criteria:
{
  "status": "pending",
  "created_date_from": {{now - 7days}}
}

API call: GET /orders?status=pending&created_date_from=...
Response: {
  "data": [
    {id: 1, order_number: "ORD-001", ...},
    {id: 2, order_number: "ORD-002", ...},
    ...
  ],
  "total": 25
}

Bundle output:
{
  "data": [... array di records ...],
  "total": 25
}

Operations:
├─ 1 operation per search call (regardless di risultati)
└─ Se search return 0 record: still 1 operation

Iterator pattern post-search:
└─ Aggiungi Iterator su search results
   └─ 1 bundle per record
   └─ Process each separately
   └─ Cost: 0 operations per iterator
```

**Search vs List:**

```
SEARCH:
└─ Filtered query (criteria-based)
└─ Returns matching records
└─ More efficient (fetch only relevant)
└─ Es: Find orders with status="pending"

LIST:
└─ Paginated retrieval (all or bulk)
└─ Returns page of records (100 per page)
└─ Less efficient (fetch all, filter in scenario)
└─ Es: Get all orders (page 1, page 2, ...)

Recommendation:
└─ Prefer SEARCH (more cost-efficient)
└─ Use LIST only if API non-support search filtering
```

### 2.3.6 Get Module

**Get**: Retrieve singolo record by ID.

```
Input: Bundle con ID del record
Output: 1 bundle con record details

Mappatura:
ID: {{1.record_id}} (REQUIRED)

API call: GET /orders/{record_id}
Response: {
  "id": "ORD-001",
  "customer": "CUST-123",
  "total": 299.99,
  ...
}

Bundle output: Entire record

Operations: 1 operation per bundle
```

**Get vs Search:**

```
GET (by ID):
└─ Direct lookup
└─ 1 operation
└─ Faster (no search needed)
└─ Use when you know ID

SEARCH (by criteria):
└─ Find matching records
└─ 1 operation
└─ Slower (filtering in API)
└─ Use when searching

Both cost 1 op, but GET more efficient (faster latency).
```

### 2.3.7 Custom API Call Module

**HTTP Request module**: Raw HTTP call (GET, POST, PUT, DELETE) to any API.

```
Configuration:
├─ URL: Full endpoint (es: https://api.example.com/orders)
├─ Method: GET, POST, PUT, PATCH, DELETE
├─ Headers: Custom headers (Authorization, Content-Type, etc)
├─ Authentication: Basic auth, OAuth, API key, custom
├─ Body: JSON, form-data, raw, XML (method POST/PUT/PATCH)
├─ Query parameters: url?param1=value1&param2=value2
└─ Response parsing: auto (JSON), manual (regex), select fields

Mapper (input):
{
  "url": "https://api.sap.com/orders",
  "method": "POST",
  "headers": {
    "Authorization": "Bearer {{connection.token}}",
    "Content-Type": "application/json"
  },
  "body": {
    "order_number": {{1.order_id}},
    "customer": {{2.customer_code}},
    "items": {{1.line_items}}
  }
}

Output:
└─ Response parsing:
   ├─ Auto JSON: entire response → bundle
   ├─ Manual JSON path: {{response.body.data[0].id}}
   └─ Regex extraction: {{response.body | match(/Order: (.+)/)[1]}}

Operations: 1 operation per call
```

**Error Handling in Custom API Calls:**

```
Common errors:
├─ 401 Unauthorized: Token wrong/expired
├─ 403 Forbidden: No permission
├─ 404 Not Found: Endpoint wrong
├─ 429 Too Many Requests: Rate limited
├─ 500 Internal Server Error: API crash
├─ Network timeout: No response

Retry strategy (configurabile):
├─ Auto-retry: 500, 502, 503, 504, 429 (default)
├─ Manual retry: Error handler with retry loop
└─ Backoff: Exponential delay between retries

Configuring retries:
Module settings → Retries
├─ Enable retries: on/off
├─ Max retry count: 0-10 (default 2)
└─ Retry on status codes: select which codes trigger retry
```

---

# PARTE 3: SISTEMA MODULI — TASSONOMIA COMPLETA

## 3.1 Transform Modules (Built-in Data Processing)

Transform modules NON fanno HTTP calls. Elaborano data localmente.

### 3.1.1 Variable Management Modules

**Set Variable**: Store a value per use later.

```
Input: 1 bundle
Output: 1 bundle (passthrough, unchanged)
Side effect: Value memorizzato in execution memory

Configuration:
├─ Variable name: "my_var" (no spaces)
├─ Variable value: {{1.field}} or hardcoded value
└─ Variable scope: current execution only (lost after scenario ends)

Example:
Set Variable "customer_id" = {{1.customer_id}}
Later modulo: Get Variable "customer_id" → {{getVariable("customer_id")}}

Operations: 0 (variables are free)

Use case:
└─ Store intermediate values
└─ Use multiple times (efficiency instead of referencing module output many times)
└─ Complex conditional logic
```

**Get Variable**: Retrieve stored value.

```
Input: variable name (text)
Output: variable value

Configuration:
└─ Variable name: "customer_id"

Usage:
{{getVariable("customer_id")}}

Operations: 0 (free)
```

**Set Multiple Variables**: Batch set multiple variables.

```
Input: 1 bundle
Output: 1 bundle (passthrough)

Configuration:
└─ Multiple variable definitions:
   ├─ var1 = {{1.field1}}
   ├─ var2 = {{1.field2}}
   └─ var3 = hardcoded value

Benefit vs multiple Set Variable modules:
└─ 1 operation vs N operations (if modules counted)
└─ Actually: both 0 operations, just more organized

Operations: 0 (free)
```

### 3.1.2 Numeric & String Operations

**Increment Value**: Atomic counter (thread-safe).

```
Input: 1 bundle
Output: 1 bundle con counter value

Configuration:
├─ Operation: increment, decrement
├─ Start value: 0 (default)
├─ Step: 1 (default, or custom)

Example:
└─ Counter initialized: 0
└─ Bundle 1: Increment → 1
└─ Bundle 2: Increment → 2
└─ Bundle 3: Increment → 3

Use case:
├─ Count bundles processed
├─ Generate sequential IDs
└─ Rate limiting

Operations: 0 (free)
```

**Text Parser**: Regex extraction, pattern matching.

```
Input: 1 bundle con testo
Output: 1 bundle con extracted/parsed data

Configuration:
├─ Text to parse: {{1.description}}
├─ Pattern: regex pattern
│  Es: /Order: ([A-Z0-9-]+)/ extracts order number
└─ Flags: global (g), case-insensitive (i), multi-line (m)

Example:
Text: "Your Order: ORD-12345 is confirmed"
Pattern: /Order: ([A-Z0-9-]+)/
Output: ["ORD-12345"]

Operations: 0 (free)
```

**HTML to Text**: Strip HTML tags.

```
Input: 1 bundle con HTML
Output: 1 bundle con plain text

Configuration:
├─ HTML content: {{1.description_html}}
└─ Optional cleanup: remove extra whitespace

Example:
Input: "<p>Hello <b>World</b></p>"
Output: "Hello World"

Operations: 0 (free)
```

### 3.1.3 Data Format Modules

**CSV Parse**: Parse CSV string → array of objects.

```
Input: 1 bundle con CSV string
Output: 1 bundle con parsed array

Configuration:
├─ CSV data: {{1.csv_content}}
├─ Delimiter: , (comma, tab, semicolon, custom)
├─ Header row: yes/no
└─ Quote character: " (double quote, custom)

Example:
Input CSV:
```
name,email,phone
Alice,alice@example.com,+39-02-111
Bob,bob@example.com,+39-02-222
```

Output:
[
  {name: "Alice", email: "alice@example.com", phone: "+39-02-111"},
  {name: "Bob", email: "bob@example.com", phone: "+39-02-222"}
]

Operations: 0 (free)

Use pattern:
Webhook riceve CSV string → CSV Parser → Iterator per row → Process each
```

**CSV Create**: Array → CSV string.

```
Input: 1 bundle con array
Output: 1 bundle con CSV string

Configuration:
├─ Array to convert: {{1.records}}
├─ Columns: specify which fields, in which order
└─ Delimiter, header row, etc

Example:
Input:
[
  {id: 1, name: "Alice", email: "alice@example.com"},
  {id: 2, name: "Bob", email: "bob@example.com"}
]

Output (CSV string):
```
id,name,email
1,Alice,alice@example.com
2,Bob,bob@example.com
```

Operations: 0 (free)

Use case: Export data to CSV for download
```

**JSON Parse/Create**: Similar CSV, per JSON.

**XML Parse/Create**: Similar CSV, per XML.

### 3.1.4 HTTP Module — Make Requests, File Operations

**Make Request**: Like custom API call, but built-in module.

```
Functionally identico a "HTTP Request" custom module.

Configuration:
├─ URL
├─ Method (GET, POST, etc)
├─ Headers
├─ Query
├─ Body
└─ Auth

Operations: 1 operation per request (counts as module execution)
```

**Get File**: Download file from URL.

```
Input: 1 bundle con file URL
Output: 1 bundle con file binary data

Configuration:
└─ File URL: {{1.file_url}}

Output:
{
  "filename": "document.pdf",
  "size": 1024000,
  "data": <binary>,
  "mimetype": "application/pdf"
}

Operations: 1 operation per download

Caution:
└─ Largefiles (> 128MB) → memory limit exceeded
└─ Data transfer also counted (see operations section)
```

**Retrieve Headers**: Get HTTP headers from URL without downloading body.

```
Input: 1 bundle con URL
Output: 1 bundle con headers

Use case:
└─ Check file size before downloading
└─ Verify content-type
└─ Check if file modified (Last-Modified header)

Operations: 1 operation per call
```

### 3.1.5 Tools Module — Compose, Switch, Aggregators

**Compose**: Create custom object/string from input.

```
Input: 1 bundle
Output: 1 bundle con custom structure

Configuration:
└─ Compose output:
   {
     "full_name": {{1.first_name}} + " " + {{1.last_name}},
     "email_lowercase": lower({{1.email}}),
     "age_group": if({{1.age}} < 18, "minor", "adult"),
     "tags": [{{1.tag1}}, {{1.tag2}}]
   }

Output:
{
  "full_name": "John Doe",
  "email_lowercase": "john@example.com",
  "age_group": "adult",
  "tags": ["tag1", "tag2"]
}

Operations: 0 (free)

Use case:
└─ Transform bundle structure
└─ Create intermediate data format
└─ String concatenation, arithmetic
```

**Switch**: Conditional branching (like Router, but for single branch).

```
Input: 1 bundle
Output: 1 bundle (output depends on condition)

Configuration:
├─ Expression to evaluate: {{1.status}}
├─ Cases:
│  ├─ Case "pending": output {{1.pending_value}}
│  ├─ Case "completed": output {{1.completed_value}}
│  └─ Default: output {{1.default_value}}

Operations: 0 (free)

Difference from Router:
└─ Router: creates multiple branches (parallel)
└─ Switch: selects ONE output (sequential)
└─ Switch è più leggero (no parallelism overhead)
```

**Text Aggregator**: Concatenate text from multiple bundles.

```
Input: Multiple bundles with text fields
Output: 1 bundle con concatenated text

Configuration:
├─ Text to concatenate: {{1.description}}
├─ Separator: " " (space, comma, newline, custom)
└─ Optional: prefix/suffix per item

Example:
Bundle 1: "Item 1"
Bundle 2: "Item 2"
Bundle 3: "Item 3"

Output: "Item 1, Item 2, Item 3" (con comma separator)

Operations: 1 operation per final aggregated output

Use case:
└─ Generate summary text
└─ Create comma-separated lists
└─ Build email body da multiple records
```

**Numeric Aggregator**: Sum, average, min, max across bundles.

```
Input: Multiple bundles con numeric fields
Output: 1 bundle con aggregated result

Configuration:
├─ Numeric field: {{1.amount}}
├─ Aggregation function:
│  ├─ sum: add all values
│  ├─ avg: average
│  ├─ min: minimum
│  ├─ max: maximum
│  ├─ count: number of items
│  └─ stdv: standard deviation

Example:
Bundle 1: amount = 100
Bundle 2: amount = 200
Bundle 3: amount = 150

Sum aggregation: 450
Average aggregation: 166.67
Min aggregation: 100
Max aggregation: 200

Operations: 1 operation per aggregation

Use case:
└─ Calculate totals
└─ Generate summary statistics
└─ Budget tracking
```

## 3.2 Flow Control Modules

### 3.2.1 Router — Conditional Branching

**Router**: Create multiple execution paths based on conditions.

```
Input: 1 bundle
Output: 1 bundle flows to ONE of the configured branches

Configuration:
├─ Route 1: if order.amount > 1000
├─ Route 2: if order.amount > 100 AND order.amount <= 1000
├─ Route 3: else (default)

Each route has separate modules.

Example:
Incoming bundle: {order_id: "ORD-001", amount: 2000}

Check conditions:
├─ Route 1: if amount > 1000? YES → execute Route 1 modules
├─ Bundles for Route 2, Route 3: not executed

Operations:
├─ Router itself: 0 operations
├─ Modules in matched route: normal operations
└─ Modules in non-matched routes: 0 operations (not executed)

Execution model:
└─ Sequential OR Parallel (setting di scenario)
   ├─ Parallel: Route 1, 2, 3 execute concurrently (branches)
   └─ Sequential: one route at a time (slower, but sequential)

Join point:
└─ Branches can rejoin at aggregator o final module
```

### 3.2.2 Filter — Conditional Gate

**Filter**: Pass or block bundle based on condition.

```
Input: 1 bundle
Output: 0 or 1 bundle (if condition true, pass; else block)

Configuration:
└─ Condition: {{1.status}} == "approved"

If true: bundle passes through (1 operation per downstream modulo)
If false: bundle blocked, downstream modules NOT executed

Operations:
├─ Filter itself: 0 operations
├─ Downstream modules: only for bundles that pass
│  └─ Filtered-out bundles don't consume operations downstream

Use case:
└─ Skip certain records (es: non-approved orders)
└─ Conditional processing based on criteria
```

### 3.2.3 Iterator — Split Array into Bundles

**Iterator**: Convert 1 bundle (with array) → N bundles (one per item).

```
Input: 1 bundle with array
Output: N bundles (one per array item)

Configuration:
├─ Array to iterate: {{1.line_items}}
└─ Output field: select which field becomes the bundle root

Example:
Input bundle:
{
  "order_id": "ORD-001",
  "line_items": [
    {sku: "SKU-001", qty: 2},
    {sku: "SKU-002", qty: 3},
    {sku: "SKU-003", qty: 1}
  ]
}

Output: 3 bundles
Bundle 1: {sku: "SKU-001", qty: 2}
Bundle 2: {sku: "SKU-002", qty: 3}
Bundle 3: {sku: "SKU-003", qty: 1}

Operations: 0 (Iterator è GRATIS)

Use case:
└─ Process large array elemento per elemento
└─ Avoid sending entire array to modules che expected singoli item
```

**Iterator with Parent Context**:

```
Quando iteri, non perdi access al parent bundle.

Input bundle:
{
  "order_id": "ORD-001",
  "customer_id": "CUST-123",
  "line_items": [...]
}

Iterator: {{1.line_items}}

Output bundle 1:
{
  sku: "SKU-001",
  qty: 2
}

In downstream module:
├─ Access current item: {{2.sku}}, {{2.qty}}
├─ Access parent bundle: {{1.order_id}}, {{1.customer_id}}
└─ Can reference both simultaneously
```

### 3.2.4 Aggregators — Combine Bundles

**Array Aggregator**: Combine multiple bundles into single array.

```
Input: Multiple bundles (from iterator or router)
Output: 1 bundle con array di bundles

Configuration:
├─ Target array (result array name): "items"
└─ Aggregated fields: which fields per item

Example:
Input: 3 bundles from iterator
Bundle 1: {sku: "SKU-001", qty: 2}
Bundle 2: {sku: "SKU-002", qty: 3}
Bundle 3: {sku: "SKU-003", qty: 1}

Output: 1 bundle
{
  "items": [
    {sku: "SKU-001", qty: 2},
    {sku: "SKU-002", qty: 3},
    {sku: "SKU-003", qty: 1}
  ]
}

Operations: 1 operation per final aggregated bundle

Use case:
└─ Process items one-by-one
└─ Aggregate back into array for final API call
└─ Batch upload: 1 call with multiple items
```

**Text Aggregator**: Covered in section 3.1.5.

**Numeric Aggregator**: Covered in section 3.1.5.

**Table Aggregator**: Create structured table from bundles.

```
Input: Multiple bundles
Output: 1 bundle con table structure

Configuration:
├─ Select which fields to include
├─ Order: row ordering
└─ HTML table generation option

Output format:
{
  "table_html": "<table>...</table>",
  "table_data": [
    {col1, col2, col3},
    ...
  ]
}

Operations: 1 operation per table

Use case:
└─ Generate HTML table per email
└─ Create CSV export
└─ Dashboard data formatting
```

---

# PARTE 4: IL MODELLO BUNDLE — IN PROFONDITÀ

Covers sections 4.1-4.5 in previous sections of this document. Moving to final section.

---

# PARTE 5: OPERATIONS — CONTEGGIO E OTTIMIZZAZIONE

Covers sections 5.1-5.5 in previous sections. Moving to scenario patterns.

---

# PARTE 6: SCENARIO DESIGN PATTERNS FONDAMENTALI

## 6.1 Linear Pattern

```
Trigger → Modulo 1 → Modulo 2 → Modulo 3 → Action

Example: Simple order fulfillment
├─ Trigger: Webhook (new order)
├─ Modulo 1: Get order details
├─ Modulo 2: Get customer
├─ Modulo 3: Create in WMS
└─ Each modulo depends on previous

Operations cost: 3 operations (1 per modulo, assuming 1 bundle)
Latency: sum of all module latencies (sequential)
```

## 6.2 Fan-out Pattern

```
Trigger → Router → [Branch 1] [Branch 2] [Branch 3]

Example: Order processing with conditional logic
├─ Trigger: New order
├─ Router:
│  ├─ If amount > 1000: VIP path (immediate processing)
│  ├─ If amount > 100: Standard path (queue)
│  └─ Else: Manual review path (notification)
└─ Each branch independent

Benefits:
├─ Parallel execution
├─ Conditional routing
└─ Cost-efficient (only matched route executes)
```

## 6.3 Fan-in Pattern

```
Trigger → Iterator → [Process each] → Aggregator → Final action

Example: Process multiple items in order
├─ Trigger: Order with 10 items
├─ Iterator: split into 10 bundles
├─ Process each: (update inventory, etc)
├─ Aggregator: combine results
└─ Final action: create consolidated record

Operations:
├─ Trigger: 1 op
├─ Iterator: 0 ops (free)
├─ Process each: 10 ops × items = 10 ops
├─ Aggregator: 1 op
└─ Total: 12 ops

Timeline: parallel execution of all 10 items (concurrent), then aggregate
```

## 6.4 Lookup/Enrich Pattern

```
Trigger → Search → Router (found/not found) → [Create/Update] vs [Create]

Example: Upsert customer record
├─ Trigger: New customer data
├─ Search: find existing customer by email
├─ Router:
│  ├─ If found: Update existing record
│  └─ If not found: Create new record
└─ Continue with rest of scenario

Operations:
├─ Trigger: 1 op (webhook)
├─ Search: 1 op (regardless of results)
├─ Create/Update: 1 op (one branch executes)
└─ Total: 3 ops

Efficiency: Single Search per decision, vs multiple branches
```

## 6.5 Sync Pattern

```
Watch trigger → Search → Router → [Update path] [Create path]

Example: Marketplace order sync to SAP
├─ Watch: Poll marketplace API every 15 min for new orders
├─ Search: Check if order exists in SAP (by order_id)
├─ Router:
│  ├─ If found: Update SAP order status
│  └─ If not found: Create SAP order
└─ Notification: Send summary

Operations per cycle:
├─ Watch: 1 op (polling check)
├─ Search: 1 op
├─ Create/Update: N ops (per new/modified orders)
└─ Total: 2 + N ops

Monthly cost (if 100 orders/month):
├─ Watch: (24 × 4 cycles/day) × 30 = 2,880 ops
├─ Search + Create: 100 × 2 = 200 ops
└─ Total: ~3,000 ops/month
```

## 6.6 Queue Pattern

```
Webhook → Store in data store
Separate scenario: Read data store → Process → Update

Benefits:
├─ Decoupling: webhook returns immediately
├─ Rate limiting: process at controlled rate
├─ Reliability: data persisted before processing
└─ Scalability: multiple scenarios can read from queue

Operations:
├─ Webhook: store in data store (cheap)
├─ Batch processor: read, process, delete (controlled rate)
└─ Cost: more predictable, smoother consumption
```

## 6.7 Master-Detail Pattern

```
Trigger → Get parent → Search children → Iterator → Process each → Aggregator

Example: Order with items, processes parent + each item
├─ Trigger: Order ID
├─ Get parent: fetch order details
├─ Search children: fetch all line items
├─ Iterator: per item
├─ Process: update inventory, pricing, etc
├─ Aggregator: combine results
└─ Final: update order status

Operations:
├─ Get: 1 op
├─ Search: 1 op
├─ Iterator: 0 ops
├─ Process: N ops (per item)
├─ Aggregator: 1 op
└─ Total: 3 + N ops
```

## 6.8 Error-Notify Pattern

```
[Any module] → Error handler → [Notification] + [Retry/Manual Queue]

Configuration:
├─ Add error handler to scenario (global catch-all)
├─ Error handler captures any unhandled error
├─ Send notification (Slack, email, etc)
├─ Option: Create ticket, log to data store, manual retry queue

Benefit:
├─ Visibility: know when things fail
├─ Recovery: manual override if needed
└─ Debugging: error details logged
```

---

# PARTE 7: GESTIONE DATI TRA MODULI

## 7.1 Output Mapping & Referencing

```
Modulo 3 wants to use output from Modulo 1:
{{1.field_name}}

Array access:
{{1.items[0].name}}  (first item)
{{1.items[].name}}   (all items flattened)

Nested objects:
{{1.customer.address.city}}

Conditional fallback:
{{1.field | default("fallback_value")}}

Uppercase/lowercase:
{{1.name | upper}}
{{1.name | lower}}

Arithmetic:
{{1.price * 1.1}}  (increase by 10%)
{{1.qty - 5}}

String concatenation:
{{1.first_name + " " + 1.last_name}}

Conditional:
{{if(1.status == "approved", 1.amount, 0)}}
```

## 7.2 IML Expressions in Mapper Fields

**IML** (Integromat Macro Language) è la lingua per expressions in Make.

```
Basic operations:
{{1.field}}              // Reference
{{now}}                  // Current date/time
{{1.amount + 100}}       // Arithmetic
{{1.name | upper}}       // Functions
{{if(1.x > 10, "yes", "no")}}  // Conditional

Functions:
add(a, b)              // Add
sub(a, b)              // Subtract
mul(a, b)              // Multiply
div(a, b)              // Divide
round(number)          // Round to nearest integer
ceil(number)           // Round up
floor(number)          // Round down
abs(number)            // Absolute value

String functions:
upper(text)            // UPPERCASE
lower(text)            // lowercase
trim(text)             // Remove whitespace
length(text)           // Character count
substring(text, start, length)  // Extract substring
indexOf(text, search)  // Find position
replace(text, find, replace)  // Replace string
parseNumber(text)      // Convert to number

Array functions:
length(array)          // Array size
reverse(array)         // Reverse order
first(array)           // First element
last(array)            // Last element
join(array, separator) // Combine array elements
split(text, separator) // Split text to array

Date functions:
now                    // Current timestamp
addDays(date, days)    // Add days
addHours(date, hours)  // Add hours
subtractDays(date, days)  // Subtract days

Type checking:
type(value)            // Get type
isEmpty(value)         // Is empty/null?
isNumber(value)        // Is numeric?

Advanced:
map(array, expression) // Transform each element
filter(array, expression)  // Keep matching elements
reduce(array, initial, expression)  // Aggregate
```

## 7.3 Type Coercion

```
String → Number:
{{parseNumber("123")}}        // 123
{{parseNumber("123.45")}}     // 123.45
{{parseNumber("abc")}}        // null (error)

Number → String:
{{toString(123)}}             // "123"
{{1.amount + ""}}             // Implicit coercion

Date → String:
{{formatDate(now, "YYYY-MM-DD")}}  // "2026-04-01"

String → Date:
{{parseDate("2026-04-01", "YYYY-MM-DD")}}

Boolean → String:
{{if(1.active, "yes", "no")}}      // Conditional to string
```

## 7.4 Binary Data Handling

```
File downloads:
├─ Get File module: retrieves file binary
├─ Output includes: filename, mimetype, size, data (binary)

File uploads:
├─ HTTP module with file body
├─ Mapper: select file field
└─ Make encodes binary as multipart/form-data

File operations in scenario:
├─ Download file 1
├─ Transform (re-encode, convert format)
├─ Upload to file storage

Memory implications:
└─ Large files (100MB+) → memory limit exceeded
└─ Solution: External processing (FTP, SFTP, cloud storage)
```

## 7.5 Large Dataset Strategies

### Pagination Pattern

```
Loop 1: API call with limit=1000, offset=0
  Results: records 0-999

Loop 2: API call with limit=1000, offset=1000
  Results: records 1000-1999

Loop N: Until no results returned

Implementation:
├─ Scheduled scenario runs hourly
├─ Each hour: fetch 1000 records
├─ Mark processed (update last_sync timestamp)
├─ Next hour: fetch records modified since last_sync
└─ Spreads load over time
```

### Chunking Pattern

```
Input: 100,000 records

Scenario A (split to chunks):
├─ Loop 1: process records 1-1000
├─ Loop 2: process records 1001-2000
├─ ...
├─ Loop 100: process records 99001-100000

Each loop = separate scenario execution
Sequential execution (triggered by scheduled job)
No memory overload (only 1000 records at a time)
```

---

# PARTE 8: SCENARIO METADATA E CONFIGURAZIONE

## 8.1 Blueprint JSON Structure

```json
{
  "name": "Order Sync to SAP",
  "description": "Sync Shopify orders to SAP in real-time",
  "scope": ["applications:read:list"],
  "settings": {
    "autoCommit": true,
    "sequential": false,
    "dlq": false,
    "confidential": false,
    "dataloss": false,
    "isLocked": false
  },
  "flow": [
    {
      "id": 1,
      "module": "shopify:watch-orders",
      "version": 1,
      "parameters": {
        "limit": 100
      },
      "metadata": {
        "designer": {
          "x": 100,
          "y": 100
        }
      }
    },
    {
      "id": 2,
      "module": "internal:router",
      "version": 1,
      "parameters": {
        "routes": [
          {
            "condition": "{{1.amount > 1000}}",
            "label": "High Value"
          }
        ]
      }
    },
    {
      "id": 3,
      "module": "sap:create-order",
      "version": 2,
      "parameters": {
        "customer_code": "{{1.customer_code}}",
        "order_total": "{{1.total}}"
      },
      "mapper": {
        "customer_code": "{{2.customer_code}}"
      }
    }
  ],
  "connections": [
    {"from": 1, "to": 2},
    {"from": 2, "to": 3}
  ]
}
```

## 8.2 Scenario Settings Explained

```
autoCommit: true/false
├─ true: Moduli are committed as they complete
├─ false: All-or-nothing (rollback if error)
└─ Implications: data consistency vs partial updates

sequential: true/false
├─ true: Router branches execute one-by-one
├─ false: Router branches execute in parallel
└─ Implications: concurrency limits, rate limiting

dlq (Dead Letter Queue): true/false
├─ true: Failed bundles saved for replay
├─ false: Failed bundles discarded
└─ Implications: Data loss risk, debugging capability

confidential: true/false
├─ true: Logs don't show bundle data
├─ false: Full logs visible in debugger
└─ Implications: Privacy, PII handling

dataloss: true/false
├─ true: Data can be lost (file operations, etc)
├─ false: Safer, but slower
└─ Implications: Performance vs data safety

isLocked: true/false
├─ true: Scenario cannot be edited (prod protection)
├─ false: Scenario can be edited
└─ Implications: Change control, accidental modifications
```

## 8.3 Scenario Versions & Blueprints

```
Versioning:
├─ Each scenario has internal version number (incremented on save)
├─ Blueprint export: JSON snapshot of scenario at specific version
├─ Can revert to previous version (Make keeps history 30 days)

Blueprint use:
├─ Export blueprint: Settings → Blueprint → Export JSON
├─ Share blueprint: Send JSON to team
├─ Import blueprint: Settings → Blueprint → Import JSON
└─ Creates new scenario from blueprint (all modules, all settings)

Implications:
├─ Blueprint = IaC (Infrastructure as Code) per Make
├─ Version control: store blueprints in Git
└─ Disaster recovery: restore from blueprint if scenario corrupted
```

## 8.4 Scenario Organization

```
Folder structure:
├─ /Marketplace Syncs
│  ├─ Shopify → SAP
│  ├─ Amazon → SAP
│  ├─ eBay → SAP
│
├─ /Inventory Management
│  ├─ Daily Sync
│  ├─ Real-time Updates
│
└─ /Reporting
   ├─ Daily Reports
   ├─ Weekly Dashboard

Naming conventions:
├─ Descriptive: "Shopify Order Webhook → SAP Creation"
├─ Avoid: "Scenario 1", "Test", "Temp"
├─ Include: source → target, frequency
└─ Example: "[DAILY] Shopify Watch → CRM Update → Notification"

Tagging:
├─ Priority: high, medium, low
├─ Category: sync, report, webhook
├─ Status: active, testing, archived
└─ Searchable for filtering
```

---

## CONCLUSION: Deep Understanding Achieved

Questo documento ha fornito una comprensione **estremamente profonda** dell'architettura Make.com, dal modello di esecuzione a livello di engine, fino ai design patterns applicativi.

**Argomenti coperti:**

1. **Execution Engine:** Queue management, memory model, zones, scheduling, timeout
2. **Bundle Model:** Atomic unit, lifecycle, context, multiplication/reduction
3. **Operations:** Precise counting, cost optimization, monthly quotas
4. **Module System:** 30+ moduli categorizati, configuration, use cases
5. **Design Patterns:** 8 fundamental patterns da linear a error handling
6. **Data Management:** Mapping, IML expressions, type coercion, large datasets
7. **Scenario Configuration:** Blueprint structure, settings, versioning

**Per un senior engineer:**

- Puoi ora **prevedere il comportamento** di scenario complessi
- **Ottimizzare costi** operation reducendo moduli inutili
- **Disegnare architetture** scalabili usando patterns
- **Debug issues** capendo il modello di esecuzione interno
- **Implementare integrazioni** robuste e efficienti

Il knowledge base è pronto per usage in produzione per Vendiamonoi.it su 20+ marketplace Europei.
