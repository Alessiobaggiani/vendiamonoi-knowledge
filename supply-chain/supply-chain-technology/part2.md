# SUPPLY CHAIN AUTOMATION & TECHNOLOGY (CONTINUED)

## 3. EDI & API INTEGRATION (Part 2)

### 3.4 Webhook Architecture for Event-Driven Integration

**Webhook Event Types:**

```
Marketplace → OMS:
├── order.created (new order from marketplace)
├── order.cancelled (buyer cancellation)
├── order.return_requested (RMA initiation)
└── inventory.sync_request (sync signal)

OMS → WMS:
├── order.assigned_to_warehouse
├── order.ready_for_picking
├── order.packed (ready for carrier)
└── order.cancelled

WMS → Carrier:
├── shipment.ready_for_pickup
├── shipment.tracking_number_ready
└── parcel.delivered

Carrier → WMS:
├── shipment.picked_up
├── shipment.in_transit
├── shipment.exception (delivery issues)
├── shipment.delivered
└── shipment.failed

WMS → OMS:
├── shipment.created
├── shipment.tracking_updated
└── return.received
```

**Webhook Implementation Requirements:**

```
Receiver Configuration:
├── HTTPS endpoint (TLS 1.2+)
├── Timeout tolerance: 30 seconds
├── Retry policy: 5 attempts with exponential backoff
├── Backoff: 1s, 2s, 4s, 8s, 16s
├── Signature verification: HMAC-SHA256
└── Idempotency: Request ID header

Sender Requirements:
├── Deliver all events (at-least-once semantics)
├── Sequential ordering per order ID
├── Maximum 50K events/day per webhook
├── Queue with deadletter mechanism
└── Admin portal to manage endpoints

Example Webhook Payload:
{
  "event_id": "evt_abc123xyz",
  "timestamp": "2024-04-15T14:32:00Z",
  "event_type": "order.packed",
  "resource": "order",
  "resource_id": "ORD-2024-00542",
  "data": {
    "orderId": "ORD-2024-00542",
    "carrier": "DHL",
    "trackingNumber": "1234567890",
    "estimatedDelivery": "2024-04-17"
  },
  "signature": "sha256=abc123..." /* HMAC signature */
}
```

### 3.5 Data Mapping and Transformation

**EDI to ERP Mapping (ORDERS → PO):**

| EDI Element | EDIFACT Path | Target ERP Field | Type | Validation |
|-------------|--------------|------------------|------|----------|
| PO Number | BGM+220+PO12345 | PurchaseOrder.Number | String | 1-20 chars |
| Supplier | NAD+BY+abc123 | Vendor.VendorID | String | Exact match required |
| Item SKU | LIN+1+abc123' | LineItem.SKU | String | Must exist in product master |
| Quantity | QTY+21:100' | LineItem.Quantity | Numeric | >0, ≤999999 |
| Delivery Date | DTM+2:20240420' | LineItem.DeliveryDate | Date | YYYYMMDD format |
| Unit Price | PRI+AAA:24.99' | LineItem.UnitPrice | Decimal | 2 decimal places |
| Tax Code | TAX+7+VAT+21' | LineItem.TaxCode | String | EU rates: 0%, 19%, 21% |

**API JSON to EDI EDIFACT Transform:**

```javascript
// OMS JSON Output:
{
  "orderId": "AMZ-DE-2024-001",
  "supplier": "SUPPLIER_DE_001",
  "items": [
    {
      "sku": "WIDGET-001",
      "quantity": 2,
      "unitPrice": 24.99,
      "deliveryDate": "2024-04-20"
    }
  ]
}

// Maps to EDIFACT DESADV:
UNB+UNOC:3+SUPPLIER_DE_001+BUYER_DE_001+240415:1432+REF001
UNH+MSG001+DESADV:D:96A:UN'
BGM+350+AMZ-DE-2024-001+9'
DTM+137:20240415:102'
NAD+SHP+BUYER_DE_001++BUYER COMPANY'
NAD+STG+WAREHOUSE_DE_01++WAREHOUSE LOCATION'
LIN+1+WIDGET-001'
QTY+48:2'
MOA+203:49.98'
UNT+8+MSG001'
UNZ+1+REF001'
```

### 3.6 Error Handling & Data Quality

**Common Integration Failures:**

| Scenario | Root Cause | Detection | Recovery |
|----------|-----------|-----------|----------|
| Inventory Mismatch | OMS sees 10 units, WMS shows 5 | Periodic audit (hourly) | Manual intervention + investigation |
| Order Duplication | Webhook retry creates duplicate | Idempotency key hash | Deduplication logic in OMS |
| Lost Shipment Tracking | Carrier API timeout | Monitoring alert (5min) | Retry with exponential backoff |
| Address Validation Fail | Non-standard format | Pre-ship validation | Flag for manual review |
| Payment Failure (rare) | Marketplace escrow issue | Order status monitoring | Automatic retry or escalation |

---

## 4. SUPPLY CHAIN VISIBILITY & TRACKING

### 4.1 Real-Time Visibility Platforms

**Leading Platforms Comparison:**

| Platform | Founded | Specialty | Coverage | Integration |
|----------|---------|-----------|----------|----------|
| **FourKites** | 2014 | Logistics tracking | 2M+ shipments/day | 150+ carrier connections |
| **project44** | 2014 | Multimodal visibility | Ocean, air, ground | API-first architecture |
| **Shippeo** | 2014 | European focus | 100K+ vehicles | TMS, OMS, WMS connectors |
| **Fourkites** | 2011 | Supply chain | Global shipment visibility | Predictive ETA |

### 4.2 FourKites Platform Specifications

**Data Collection Methods:**

```
1. Carrier API Integration (Real-time)
   ├── DHL Tracking API (heartbeat every 5-10 min)
   ├── UPS OnTrack (polling every 5 min)
   ├── FedEx Track API (event-based)
   ├── Royal Mail (polling every 10 min)
   └── 3PL custom APIs (variable)

2. IoT Sensor Integration
   ├── GPS devices: Geotab, Samsara, Verizon Connect
   ├── Frequency: Every 5-30 minutes (GPS fix)
   ├── Accuracy: ±5-10 meters (urban), ±15-30 meters (rural)
   └── Battery life: 7-14 days (5-minute ping interval)

3. Manual Checkpoint Entry
   ├── Mobile app: Driver confirms pickup/delivery
   ├── Photo verification: Geotag + timestamp
   └── Webhook to control tower
```

**Predictive ETA Algorithm:**

```
Inputs:
├── Historical delivery data (3 years)
├── Current traffic conditions (real-time)
├── Weather data (rain delay impact)
├── Carrier performance baseline
├── Time of day (morning/afternoon/evening)
├── Distance remaining (via mapping API)
└── Vehicle type and load capacity

Processing:
1. Route analysis: Actual miles vs straight-line
2. Traffic adjustment: Current vs historical conditions
3. Weather impact: +15% for rain, +30% for snow
4. Driver behavior: Actual speed vs posted limit
5. Delivery window: Window time impact
6. Confidence scoring: Low/Medium/High

Output:
├── ETA: Timestamp with ±30 minute range
├── Confidence: Probability (80-95%)
├── Updates: Recalculated every 5-10 minutes
└── Alert thresholds: Off-route, delayed, exception

Accuracy Metrics:
├── On-time (within ±5 min): 85%
├── Early/Late (±15 min): 95%
└── Mean Absolute Error: ±8 minutes
```

### 4.3 IoT Sensor Integration for Temperature/Humidity/Shock

**Sensor Types & Specifications:**

| Sensor Type | Temperature Range | Humidity Range | Shock Sensitivity | Battery Life | Cost |
|-------------|-------------------|-----------------|-------------------|--------------|------|
| **Passive RFID** | -20 to 60°C | 0-100% (indirect) | N/A | Passive (unlimited) | €0.50-2 |
| **Active RFID/GPS** | -20 to 60°C | 0-100% | Accelerometer optional | 7-14 days | €15-40 |
| **Temperature/Humidity** | -40 to 85°C | 0-100% | N/A | 1-2 years (button cell) | €3-10 |
| **Shock/Tilt Indicator** | -20 to 60°C | N/A | >2G threshold | Mechanical (10+ years) | €0.30-1 |
| **Multi-sensor (IoT)** | -20 to 60°C | 0-100% | Full accelerometer | 30-90 days | €25-60 |

**Temperature-Controlled Shipment Monitoring:**

```
Pharma/Food Example:
├── Required range: 2-8°C (refrigerated)
├── Sensor: Multi-sensor IoT device
├── Logging interval: Every 15 minutes
├── Data storage: Cloud database (AWS S3)
├── Alert triggers:
│   ├── Below 2°C: Cold chain breach
│   ├── Above 8°C: Warm chain breach
│   ├── Time-temperature integral: Cumulative damage
│   └── Shock detection: >5G for >100ms
│
├── Corrective actions:
│   ├── Real-time: SMS/push notification to driver
│   ├── Exception: Divert to nearest distribution center
│   ├── Documentation: Report for regulatory compliance
│   └── Financial: Insurance claim + customer credit
```

**Shock Detection for Fragile Goods:**

```
Accelerometer Data Collection:
├── Frequency: 10-100 Hz (sampling rate)
├── Threshold: 2-5G depending on product
├── Duration: >200ms = shock event
├── Direction: X, Y, Z axes tracked separately
├── Data storage: Circular buffer (last 24 hours)

Example: Electronics Shipment
├── Threshold: 3G combined axes
├── Event: 4.2G shock for 350ms
├── Timestamp: 2024-04-15 14:32:15 UTC
├── Location: 52.5200° N, 13.4050° E (Berlin)
├── Action: Verify package integrity on delivery
└── Report: Damage liability investigation
```

### 4.4 Control Tower Architecture

**Control Tower Concept:**

```
Central Hub (Dashboard):
├── Real-time shipment visibility (map view)
├── KPI dashboard (OTD%, average delay, exceptions)
├── Alert management (100+ customizable rules)
├── Team collaboration (notes, task assignment)
└── Reporting (daily/weekly/monthly dashboards)

Data Integration:
├── Carrier feeds (real-time)
├── IoT sensors (temperature, GPS, shock)
├── Order management system (order status)
├── Warehouse management system (shipment readiness)
├── Customer feedback (delivery satisfaction)
└── Financial systems (cost, insurance claims)

Alert Rules Engine:
├── Delayed delivery: >2 hours behind ETA
├── Off-route: >5 miles from planned route
├── Stopped too long: Vehicle stationary >30 min
├── Temperature breach: Outside tolerance
├── Shock detected: >threshold G-force
├── Exception message: Carrier system alert
└── Custom rules: User-defined conditions
```

**Typical Control Tower Dashboard KPIs:**

| Metric | Target | Measurement | Alert Threshold |
|--------|--------|-------------|------------------|
| On-Time Delivery % | 95%+ | Arrive within 2-hour window | <90% daily |
| Average Delivery Time | 2-3 days | Pickup to delivery | >4 days |
| Shipment Visibility | 100% | Real-time location known | <95% |
| Exception Rate | <2% | Damaged, lost, delayed | >3% |
| Customer Satisfaction | 4.5/5 | Post-delivery survey | <4.0 |
| Cost per Shipment | €8-15 | Total logistics cost | >€16 |

### 4.5 Exception Management Workflows

**Exception Escalation Matrix:**

```
LEVEL 1: System Alert (Automated)
├── Trigger: Rule threshold exceeded
├── Action: Notify dispatcher via SMS/email
├── Timeframe: <5 minutes
└── Examples: Delayed >2 hours, off-route

LEVEL 2: Manual Investigation (5-30 min)
├── Trigger: Level 1 unresolved
├── Action: Dispatcher calls driver
├── Outcome: Confirm delivery time or divert
└── Documentation: Reason code + timestamp

LEVEL 3: Intervention (30+ min)
├── Trigger: Critical exception (lost shipment)
├── Action: Escalate to manager + customer
├── Outcome: Arrange recovery or replacement
└── Compensation: Refund or expedited reshipment

LEVEL 4: Post-Delivery (After delivery)
├── Trigger: Customer reports damage/loss
├── Action: Insurance claim investigation
├── Outcome: Customer credit + root cause analysis
└── Prevention: Carrier penalty assessment
```

**Exception Resolution Times (Target):**

| Exception Type | Detection Time | Resolution Time | Escalation % |
|----------------|---|---|---|
| Delayed delivery | 2-4 hours | 2-6 hours | 5% |
| Off-route/lost | 30-60 minutes | 1-2 hours | 15% |
| Temperature breach | Immediate (sensor) | 1-3 hours | 10% |
| Delivery failure | 24 hours | 24-48 hours | 20% |
| Damage reported | On delivery | 2-5 days | 8% |

---

## 5. AUTOMATION WITH MAKE.COM & N8N (Part 1)

### 5.1 Make.com for Supply Chain Automation

**Platform Architecture:**

```
Make.com Core Components:
├── Visual Scenario Builder (drag-and-drop)
├── Module Library (500+ apps)
├── Execution Engine (distributed, serverless)
├── Webhook receiver (public HTTPS endpoint)
├── Data store (key-value JSON storage)
├── Scheduling (cron-based or event-driven)
└── Pricing: €9.99-199/month (ops-based)

Supported Supply Chain Integrations:
├── OMS: ChannelEngine, Linnworks, Brightpearl
├── WMS: Logiwa, ShipHero, 3PL Central
├── Carriers: DHL, UPS, FedEx, Royal Mail
├── Marketplaces: Amazon, eBay, Shopify
├── Data: Google Sheets, Airtable, PostgreSQL
├── Communication: Slack, SendGrid, Twilio
└── Custom: HTTP request, XML/JSON parsing
```

**Scenario 1: Automated Order Sync (OMS → WMS)**

```
Trigger:
├── Webhook: ChannelEngine sends order.created event
├── Filter: Only orders >€100 value (cost optimization)
└── Schedule: Process every 5 minutes

Step 1: Receive Order from OMS
├── Module: ChannelEngine > Get Order
├── Input: Order ID from webhook
├── Output: {
     orderId, items[], shippingAddress, totalValue
   }

Step 2: Enrich with Product Data
├── Module: Google Sheets > Search
├── Input: Match SKU from items[]
├── Output: {
     sku, warehouseLocation, weight, dimensions
   }

Step 3: Determine Warehouse (Routing)
├── Module: Router (decision logic)
├── Rule 1: If weight >10kg → Warehouse B (has racking)
├── Rule 2: If delivery_country = DE → Warehouse A
├── Rule 3: Else → Warehouse C (default)
├── Output: warehouse_id

Step 4: Check Inventory
├── Module: HTTP Request
├── URL: https://api.logiwa.com/api/v2/inventory/sku/{sku}?warehouse={warehouse_id}
├── Method: GET
├── Headers: Authorization: Bearer <token>
├── Response: { available_qty, reserved_qty, in_stock: boolean }

Step 5: Conditional: Stock Available?
├── Route A (YES): Continue to Step 6
└── Route B (NO):
    ├── Module: Backorder workflow
    ├── Action: Create PO in ERP
    └── Action: Notify customer (Sendgrid email)

Step 6: Create Picking Wave in WMS
├── Module: HTTP Request (Logiwa API)
├── Method: POST
├── URL: https://api.logiwa.com/api/v2/picking/wave
├── Body: {
     wavedId: "WAVE-" + timestamp,
     orderId: order.id,
     items: [{
       skuCode: item.sku,
       quantity: item.qty,
       binLocation: product.binLocation
     }]
   }

Step 7: Update Order Status in OMS
├── Module: ChannelEngine > Update Order
├── Status: "ASSIGNED_TO_WAREHOUSE"
├── Additional: warehouse_id, estimated_ready_time
└── Notify: Send webhook to OMS

Step 8: Log Success
├── Module: Google Sheets > Create Row
├── Append: {
     timestamp, orderId, warehouse, status, items_count
   }

Error Handling:
├── If HTTP error (Step 4): Retry 3x with 5s delay
├── If all retries fail: Send Slack alert to ops team
└── If validation error: Log to error sheet + manual review
```

**Make.com Module Examples:**

```
HTTP Module Configuration:
├── URL: https://api.logiwa.com/api/v2/inbound
├── Method: POST
├── Headers: {
     "Authorization": "Bearer {{auth_token}}",
     "Content-Type": "application/json"
   }
├── Body: Raw JSON {
     "poNumber": "{{po_id}}",
     "supplierCode": "{{supplier_code}}",
     "items": {{items_array}},
     "expectedDeliveryDate": "{{delivery_date}}"
   }
├── Query String: timestamp={{now}}
└── Timeout: 30 seconds

Conditional Logic (If/Then):
├── If {{ order.totalValue }} > 100
│   ├── Then: Priority picking = YES
│   └── ElseIf {{ order.itemCount }} > 5
│       ├── Then: Wave picking = YES
│       └── Else: Single pick = YES

Google Sheets Integration:
├── Module: Google Sheets > Add Row
├── Configuration:
│   ├── Spreadsheet: supply_chain_log
│   ├── Sheet: daily_orders
│   └── Columns: {
│       timestamp: "{{now}}",
│       order_id: "{{order.id}}",
│       warehouse: "{{routing.warehouse}}",
│       status: "{{status}}",
│       items: "{{order.itemCount}}"
│     }
```

### 5.2 Scenario 2: Inventory Sync (WMS → OMS → Marketplaces)

```
Trigger:
├── Schedule: Every 1 hour (hourly batch)
├── Time window: 08:00-22:00 (business hours)
└── Frequency: Configurable via data store

Step 1: Fetch Inventory from WMS
├── Module: HTTP Request (Logiwa)
├── Endpoint: /api/v2/inventory/current
├── Query params: {
     warehouse_id: "ALL", last_sync: "{{last_sync_time}}"
   }
├── Response: Array[{
     sku, warehouse, available_qty, reserved_qty, location
   }]

Step 2: Aggregate by SKU (Remove warehouse dimension)
├── Module: Array Aggregator
├── Aggregate by: sku
├── Operation: SUM available_qty across warehouses
├── Result: {
     sku: "WIDGET-001",
     total_available: 45,
     reserved: 8,
     sellable: 37
   }

Step 3: Apply Safety Stock Rules
├── Module: JavaScript (custom logic)
├── Rules:
│   ├── If sellable < safety_stock → Subtract buffer
│   ├── If item is slow-moving → Apply damping factor
│   └── If item nearing expiry → Reduce advertised stock
│
├── Code:
  const updated_stock = Math.max(0,
    sellable
    - (slow_moving ? sellable * 0.2 : 0)
    - (nearing_expiry ? sellable * 0.3 : 0)
  )

Step 4: Update OMS Master (Linnworks)
├── Module: HTTP Request (Linnworks API)
├── For each SKU (loop):
│   ├── Endpoint: PUT /inventory/1.0/{inventoryId}
│   ├── Payload: {
│       supplieduserInventoryId: sku,
│       quantity: updated_stock,
│       lastModified: now()
│     }
│   └── Rate limit: 500 req/min (batch in groups of 50)

Step 5: Update Amazon EU (via SP API)
├── Module: HTTP Request
├── Endpoint: POST /feeds/2021-06-30/feeds
├── Feed type: INVENTORY
├── Payload: XML {
     <Message>
       <MessageID>1</MessageID>
       <OperationType>Update</OperationType>
       <Inventory>
         <SKU>WIDGET-001</SKU>
         <Quantity>37</Quantity>
       </Inventory>
     </Message>
   }

Step 6: Update eBay (via eBay API)
├── Module: HTTP Request
├── Endpoint: POST /sell/inventory/v1/inventory_items/{sku}
├── Payload: {
     quantity: { value: 37 }
   }
├── Rate limit: 60 req/min

Step 7: Update Shopify Store
├── Module: HTTP Request (Shopify REST)
├── Endpoint: POST /admin/api/2024-01/inventory_levels/adjust.json
├── Payload: {
     inventory_item_id: {{shopify_inv_id}},
     available_adjustment: {{delta}},
     locations_inventory_levels: [{ location_id, available: 37 }]
   }

Step 8: Record Sync in Data Store
├── Module: Make Data Store > Set
├── Key: "last_inventory_sync"
├── Value: {{now}}
├── Additional: { total_skus_updated, duration_ms }

Step 9: Report Results
├── Module: Slack > Send Message
├── Channel: #supply-chain-ops
├── Message: "✓ Inventory sync completed
   • Updated: 1,245 SKUs
   • Avg stock: 45 units
   • Duration: 3m 24s
   • Marketplaces: Amazon, eBay, Shopify"
```

### 5.3 Scenario 3: Shipping Label Generation & Tracking Update

```
Trigger:
├── Webhook from WMS: shipment.ready_for_pickup
└── Filters: status == "PACKED" AND carrier != null

Step 1: Fetch Shipment Details
├── Module: Logiwa API > Get Shipment
├── Input: shipment_id
├── Output: {
     orderId, items[], weight, dimensions,
     shippingAddress, carrier, serviceType
   }

Step 2: Get Carrier Account
├── Module: Google Sheets > Find
├── Search: carrier = "DHL"
├── Result: { api_key, account_number, base_url }

Step 3: Create Label via Carrier API
├── Module: HTTP Request (DHL API)
├── Endpoint: POST /shipments/v1/label-orders
├── Headers: { Authorization, Content-Type }
├── Body: {
     shipments: [{
       weight: weight_kg,
       dimensions: { length, width, height },
       recipient: { name, street, city, zip, country },
       sender: warehouse_address,
       productType: "express",
       plannedPickupDateAndTime: "2024-04-15T09:00:00"
     }]
   }
├── Response: {
     shipmentNumber: "1234567890",
     trackingNumber: "1234567890",
     documents: [{ documentType: "LABEL", contents: "pdf_base64" }]
   }

Step 4: Decode & Save Label
├── Module: HTTP Request (Google Drive API)
├── Action: Upload PDF
├── File: base64 decoded label PDF
├── Folder: labels/{date}/{carrier}/{shipment_id}.pdf
├── Make public: Via shared link

Step 5: Update Shipment in WMS
├── Module: Logiwa API > Update Shipment
├── Fields: {
     trackingNumber: "1234567890",
     carrier_tracking: "DHL",
     label_url: "https://drive.google.com/...",
     status: "LABEL_GENERATED"
   }

Step 6: Notify OMS
├── Module: HTTP Request (ChannelEngine)
├── Endpoint: PATCH /orders/{order_id}/shipment
├── Body: {
     trackingNumber: "1234567890",
     carrier: "DHL",
     carrierService: "EXPRESS",
     estimatedDeliveryDate: "2024-04-17"
   }

Step 7: Sync to Marketplace
├── Module: HTTP (Amazon SP API)
├── Endpoint: PUT /orders-v0/orders/{order_id}/shipment
├── Body: {
     shipments: [{
       items: [{ orderItemId, quantity }],
       trackingInfo: {
         trackingNumber: "1234567890",
         carrier: "DHL"
       }
     }]
   }

Step 8: Send Customer Notification
├── Module: SendGrid > Send Email
├── To: customer_email
├── Template: shipping_notification
├── Variables: {
     tracking_number: "1234567890",
     carrier_name: "DHL",
     estimated_delivery: "17. April 2024",
     tracking_url: "https://tracking.dhl.de/?..."
   }

Step 9: Log Results
├── Module: Data Store > Set
├── Key: "shipment_{shipment_id}"
├── Value: { trackingNumber, label_url, timestamp }
└── TTL: 90 days

Error Handling:
├── If label generation fails (HTTP 429): Retry after 60s
├── If shipment sync fails: Queue for retry (5 attempts)
└── If all retries fail: Alert via Slack + manual follow-up
```

### 5.4 Scenario 4: Return Processing & Reverse Logistics

```
Trigger:
├── Webhook: marketplace.return_requested
├── Filter: Return reason != "CUSTOMER_CHANGED_MIND" (exclude)
└── Only process if refund_applicable = true

Step 1: Validate Return Authorization
├── Module: HTTP Request (OMS)
├── Check: order_id, return_reason, item_condition
├── Verify: RMA eligibility (within 30 days, condition OK)
├── Outcome: Approve or Reject return

Step 2: Generate Return Label
├── Module: HTTP (Return carrier API)
├── Carrier: DHL, UPS, or reverse logistics partner
├── Label includes: RMA number, return address, QR code
├── Send to customer: Via email + marketplace message

Step 3: Update Order Status
├── Module: OMS > Update Order
├── Status: "RETURN_AUTHORIZED"
├── Fields: {
     rma_number: "RMA-20240415-001",
     return_label_url: "...",
     return_deadline: "2024-05-15"
   }

Step 4: Monitor Return Inbound
├── Schedule: Daily check
├── Query: return.status != "RECEIVED"
├── Wait time: >10 days → Send reminder email
├── Wait time: >21 days → Escalate (follow-up call)

Step 5: Goods Receipt at Warehouse
├── Trigger: Warehouse receives returned item
├── Step A: Scan RMA barcode
├── Step B: Verify condition (photos)
├── Step C: WMS marks as RECEIVED
├── Step D: QC inspection (2-3 business days)

Step 6: Process Return (Decision Tree)
├── Branch A: RESTOCK (Item pristine)
│   ├── Put back to inventory
│   ├── List on marketplaces
│   └── No refund delay
│
├── Branch B: PARTIAL REFUND (Minor wear)
│   ├── Restock at 70% value
│   ├── Note: Cosmetic damage
│   └── Refund: 85% (15% restocking fee)
│
└── Branch C: SCRAP/DISPOSE (Damaged)
    ├── Write-off in WMS
    ├── Schedule waste disposal
    └── Refund: 100% (under warranty)

Step 7: Process Refund
├── Module: Payment processor (Stripe/PayPal)
├── Endpoint: /refunds (or reverse charge)
├── Amount: Based on condition (100% or 85%)
├── Reason: Return reason code
├── Speed: Next-day processing

Step 8: Update Marketplaces
├── Module: Marketplace APIs (Amazon, eBay, Shopify)
├── Action: Mark order as "REFUNDED"
├── Message: "Return processed, refund issued"
├── Timeline: 3-5 business days to customer account

Step 9: Update Customer
├── Module: SendGrid Email
├── Message: Refund confirmation
├── Contents: {
     refund_amount, processing_time, tracking_info
   }

Step 10: Record Analytics
├── Module: Google Sheets > Add Row
├── Log: {
     date, order_id, refund_amount, reason,
     condition, restocking_cost, days_to_process
   }
└── KPI tracking: Return rate, average refund %
```
