# SUPPLY CHAIN AUTOMATION & TECHNOLOGY FOR EUROPEAN MARKETPLACE DISTRIBUTION

**Version:** 1.0
**Date:** 2026
**Scope:** Complete technical specifications for omnichannel fulfillment across Amazon EU, eBay, Shopify, WooCommerce, and proprietary stores

---

## 1. ORDER MANAGEMENT SYSTEMS (OMS)

### 1.1 OMS Platform Landscape

| Platform | Founded | Target Market | Cloud Architecture | Multi-Region | Marketplace Focus |
|----------|---------|---------------|-------------------|---------------|--------------------||
| **Linnworks** | 2004 | SMB/Mid-Market | Multi-tenant SaaS | 3 data centers | Amazon, eBay, Shopify |
| **ChannelEngine** | 2010 | Enterprise | Managed Cloud (AWS) | EU/US/APAC | 100+ marketplace connectors |
| **Channable** | 2008 | Retail/Fashion | AWS-based | EU native | Feed optimization focus |
| **Brightpearl** | 2007 (Sage) | Mid-Market | Oracle Cloud | Global | Inventory-centric |
| **Cin7** | 2012 | SMB Global | AWS Multi-region | Yes | Lightweight integration |
| **Ordoro** | 2010 | SMB | US-based SaaS | Limited | Shipping-integrated |
| **Sellbrite** | 2012 | SMB/Channel | Cloud-based | US primarily | Multi-channel focus |

### 1.2 Linnworks Deep Specifications

**Licensing Model:**
- Starter: €99/month (500 orders/month)
- Professional: €199/month (5,000 orders/month)
- Enterprise: Custom (unlimited orders)
- Per-order overage: €0.02-0.04 per order above limit

**Integration Capabilities:**
- Amazon MWS API v5, eBay Trading API (XML), Shopify REST/GraphQL
- CSV import/export with field mapping templates
- Webhook support for real-time order sync
- FTP integration for legacy systems
- Database connectors: SQL Server, MySQL, PostgreSQL (enterprise)

**Order Processing Features:**
```
Routing Engine Rules:
├── Condition-based (warehouse, country, weight, value)
├── Automatic split orders >2 items to multiple warehouses
├── Backorder thresholds (auto-cancel >30 days)
├── Priority rules (FBA > FBM > eBay)
├── Inventory reserve allocation
└── Multi-language label generation (22 languages)
```

**API Rate Limits:**
- REST API: 5,000 requests/minute (bursts to 10,000)
- Webhook delivery: Guaranteed ≤1 second latency
- Batch operations: 50,000 items/request
- Order payload size: <15MB per request

**Fulfillment Network Integration:**
- Native: UPS, FedEx, DHL, Royal Mail, Hermes
- Via API: 50+ carrier integrations
- Rate shopping across 8 carriers simultaneously
- Label generation: PDF, ZPL (Zebra), EPL formats

**Multi-Warehouse Inventory Sync:**
```
Sync Frequency: Real-time (webhook-triggered)
Sync Method: Incremental or full inventory pull
Conflict Resolution: Last-write-wins with timestamp
Stock Allocation Algorithm:
├── Nearest warehouse by distance
├── Lowest cost carrier available
├── Warehouse capacity constraints
└── Stock availability buffer (safety stock)
```

### 1.3 ChannelEngine Platform Specifications

**Deployment:**
- Managed Cloud (AWS Frankfurt region for GDPR)
- 99.95% uptime SLA with load balancing
- Auto-scaling: 2-100 container instances
- Disaster recovery: RTO 4 hours, RPO 1 hour

**Marketplace Connectors (Current: 120+):**

| Marketplace | API Version | Sync Interval | Feed Types |
|-------------|-------------|---------------|----------|
| Amazon EU | Selling Partner API | Real-time | Inventory, Orders, Returns |
| eBay EU | REST API | 5-minute polling | Listings, Orders, Inventory |
| Shopify | REST (2024-04) | Webhook + polling | Products, Orders, Fulfillment |
| WooCommerce | REST v3 | Webhook | Products, Orders, Stock |
| Cdiscount | XML API | 10-minute | Products, Orders |
| Fnac/Darty | SFTP feed | Daily batch | Products, Inventory |
| Bol.com | REST API | Real-time | Inventory, Orders |
| ManoMano | API v2 | Real-time | Products, Orders |

**Order Processing Pipeline:**
```
1. Inbound (Marketplace → ChannelEngine)
   - Order ingestion: <200ms average
   - Duplicate detection: Order ID + timestamp hash
   - Data enrichment: Address validation, tax codes
   - Inventory deduction: Real-time stock update

2. Orchestration Engine
   - Rule evaluation: <50ms for 1,000+ rules
   - Warehouse selection algorithm (A/B tested)
   - Carrier rate shopping (parallel queries)
   - Exception management (manual intervention required)

3. Outbound (ChannelEngine → Fulfillment)
   - Dispatch to WMS via API or SFTP
   - Label generation (hosted PDF service)
   - Tracking sync back to marketplaces
   - Return authorization integration
```

**Pricing Structure:**
- Commission model: 0.35-0.99 EUR/order processed
- Transaction limits: Unlimited orders
- API calls: 1M included, €0.001 per additional call
- Custom integrations: €500-2,000 setup

### 1.4 Brightpearl OMS Specifications

**Architecture:**
- Built on Oracle NetSuite platform (backend)
- Sage ownership (acquired 2015)
- Multi-tenant SaaS with dedicated logging

**Core Features:**
- Order consolidation from 50+ channels
- Advanced backorder management:
  * Automatic PO creation from unfulfilled orders
  * Supplier lead-time integration
  * Partial fulfillment tracking
  * Backorder aging reports

**API Specifications:**
- REST API: 10,000 requests/day per user
- Webhook events: Order created, shipped, cancelled
- Bulk operations: 100 orders/request, max 100 requests/minute
- OAuth 2.0 + API key authentication

**Inventory Management:**
```
Stock Tracking:
├── Available stock (sellable quantity)
├── Reserved (allocated to unfulfilled orders)
├── In-transit (from suppliers)
├── Damaged/defective quantity
├── Warehouse-level visibility
└── Serial number tracking (luxury goods)

Safety Stock Model:
├── Min/max per warehouse
├── Lead-time demand calculation
├── Seasonal adjustment factors
└── ABC analysis integration
```

**Pricing:**
- Starter: £199/month (250 orders)
- Professional: £499/month (2,500 orders)
- Enterprise: Custom (unlimited)
- Setup fee: £1,000-3,000

### 1.5 Cin7 (Mid-Market Focus)

**Lightweight Integration Approach:**
- Purpose-built for Shopify-first retailers
- Rapid onboarding: 2-4 weeks
- Data warehouse: BigQuery-powered analytics
- Marketplace coverage: 20+ integrations

**Key Differentiators:**
- Supplier purchase order automation
- Customer invoice and shipment notifications
- Stock forecast dashboard (ML-powered)
- Financial sync: QuickBooks, Xero, SAP

**Pricing:**
- Professional: AUD $699/month (1,000 orders)
- Advanced: AUD $1,299/month (5,000 orders)
- Setup: AUD $1,500

### 1.6 OMS vs ERP vs Channel Manager: Technical Distinctions

| Aspect | OMS | ERP | Channel Manager |
|--------|-----|-----|------------------|
| **Primary Function** | Order-to-fulfillment orchestration | Enterprise-wide business management | Channel feed optimization |
| **Inventory Scope** | Real-time across channels | Central stock database | Feed-level stock visibility |
| **API Complexity** | Medium (order/fulfillment) | High (all business processes) | Low (product/price feeds) |
| **Setup Time** | 2-4 weeks | 3-12 months | 1-2 weeks |
| **Typical Cost** | €200-2,000/month | €5,000-50,000/month | €100-500/month |
| **Core Data Model** | Orders, shipments, carriers | GL, AR, inventory, manufacturing | Products, prices, attributes |
| **Multi-warehouse Native** | Yes | Yes | Feed-level only |
| **Returns Automation** | Yes (basic) | Yes (integrated) | No |
| **Supplier PO Integration** | Basic | Advanced | No |

**Integration Topology:**
```
Architecture Decision:
├── OMS-centric (e.g., Linnworks + WMS)
│   ├── Best for: High-velocity marketplace sellers
│   ├── Avoid: Complex B2B/manufacturing scenarios
│   └── Cost: €500-2,500/month

├── ERP-centric (e.g., SAP, Oracle)
│   ├── Best for: Multi-division enterprises
│   ├── Avoid: Rapid marketplace scaling
│   └── Cost: €10,000-30,000/month

└── Hybrid (OMS + ERP light)
    ├── Best for: Mid-market with growth trajectory
    ├── Tool: OMS handles marketplace, ERP handles accounting
    └── Cost: €3,000-8,000/month
```

### 1.7 OMS Feature Comparison Matrix

| Feature | Linnworks | ChannelEngine | Brightpearl | Cin7 |
|---------|-----------|---------------|-------------|------|
| Marketplaces | 35+ | 120+ | 50+ | 20+ |
| Warehouse Locations | Unlimited | Unlimited | 25 max | 5 max |
| Order Rules Engine | Basic | Advanced | Intermediate | Basic |
| API Rate Limit | 5K/min | 10K/day | 10K/day | 5K/day |
| Backorder Management | Yes | Yes | Advanced | Basic |
| Returns Workflow | Basic | Intermediate | Advanced | Basic |
| EDI Support | Limited | Via partners | Yes | No |
| Real-time Inventory | Yes | Yes | Yes | Yes |
| GDPR Compliance | Full | Full | Full | Full |

---

## 2. WAREHOUSE MANAGEMENT SYSTEMS (WMS)

### 2.1 Cloud WMS Platform Overview

| Platform | Founded | Target | Architecture | AWS Region | SKU Limit |
|----------|---------|--------|--------------|------------|----------|
| **ShipHero** | 2014 | SMB/E-commerce | Multi-tenant SaaS | US-East | 500K |
| **Deposco** | 2016 | 3PL/Fulfillment | Dedicated cloud | US | Unlimited |
| **Logiwa** | 2011 | European 3PL | AWS Frankfurt | EU | 1M+ |
| **3PL Central** | 2006 | 3PL-focused | Virtualized | Multi | 2M |
| **Extensiv (Skubana)** | 2017 | Marketplace sellers | Cloud-based | Global | 1M |

### 2.2 ShipHero Deep Specifications

**Core Functionality:**

```
Receiving Module:
├── PO matching (ASN validation)
├── GRN (Goods Received Note) generation
├── QC inspection workflow (sampling options)
├── Cycle counting (ABC method, random sampling)
├── Lot/serial number tracking (expiry dates)
├── Put-away automation (location suggestions)
└── Receiving API: /api/v1/inbounds

Pick/Pack/Ship Module:
├── Wave picking (time-based, order-based, batch)
├── Pick location optimization (nearest-pick algorithm)
├── Pack station integration (scale API, label printer)
├── SSCC/GS1 barcode generation
├── Multi-package order handling
├── Shipping integration (rate shopping)
├── Label generation: PDF, ZPL, ZPLII
└── API: /api/v1/picks, /api/v1/shipments

Inventory Management:
├── Real-time stock visibility (all locations)
├── Safety stock thresholds
├── Cycle counting schedules
├── Inventory adjustment workflows
├── Write-off (damage/expiry) management
├── Stock transfer between locations
└── API: /api/v1/inventory
```

**Warehouse Layout Configuration:**
```
Location Hierarchy:
Zone → Aisle → Rack → Shelf → Bin → Cell

Example: ZONE-A/AISLE-01/RACK-05/SHELF-03/BIN-02
├── Zone: A (Pick), B (Staging), C (Returns)
├── Aisle: Sequential numbering
├── Rack: Physical shelving unit
├── Shelf: Vertical level
├── Bin: Specific storage location
└── Cell: Optional sub-location
```

**Pricing:**
- Basic: $500/month (10K units/month)
- Professional: $1,200/month (50K units/month)
- Enterprise: Custom (unlimited)
- Per additional unit: $0.002-0.004

**Integration APIs:**

| Endpoint | Method | Rate Limit | Payload |
|----------|--------|-----------|----------|
| /inbounds | POST | 100/min | Create PO, ASN |
| /inventory | GET | 1000/min | Stock levels |
| /picks | POST | 100/min | Generate pick list |
| /shipments | POST | 50/min | Finalize shipment |
| /returns | POST | 100/min | Return authorization |

### 2.3 Logiwa WMS (European-Optimized)

**Deployment:**
- AWS Frankfurt (GDPR-compliant data center)
- Real-time cloud infrastructure
- 99.9% uptime SLA
- Auto-backup: Hourly snapshots

**Multi-Warehouse Management:**

```
Central Dashboard:
├── Real-time visibility across N warehouses
├── Consolidated inventory view
├── Cross-warehouse transfers
├── Capacity planning by location
├── Labor cost analysis by site
└── KPI dashboards (OEE, cycle times)

Warehouse-level Features:
├── Independent user roles
├── Location-specific settings
├── Separate receiving queues
├── Zone-based picking strategies
└── Warehouse-specific integrations
```

**Cycle Counting & Inventory Audit:**
```
Method 1: ABC Classification
├── A items (20% inventory, 80% value): Weekly
├── B items (30% inventory, 15% value): Monthly
├── C items (50% inventory, 5% value): Quarterly

Method 2: Random Sampling
├── Daily count: 100-500 random bins
├── Sample size: √(Total bins)
├── Variance tolerance: <2%
└── Auto-adjust: Recount if variance >5%

Method 3: Full Physical
├── Quarterly inventory closure
├── All bins counted simultaneously
├── Variance investigation: >€100 or >5%
└── Write-off approval workflow
```

**API Architecture:**
```
REST endpoints:
├── /api/v2/warehouses (GET, POST)
├── /api/v2/inbound/po (POST - create PO)
├── /api/v2/inbound/receive (POST - goods receipt)
├── /api/v2/inventory/current (GET - real-time stock)
├── /api/v2/picking/wave (POST - create wave)
├── /api/v2/shipment/create (POST - finalize shipment)
└── /api/v2/inventory/cycle-count (POST - cycle counting)

Rate Limits:
├── Standard: 1000 requests/minute
├── Batch operations: 100 requests/second
├── Webhook delivery: Async, guaranteed ≤2s
└── Data export: Max 100K records/call
```

**Lot Tracking & Expiry Management:**

```
Lot Entry on Receiving:
├── Lot number / batch code
├── Manufacturing date
├── Expiry date
├── QC status (pass/fail)
└── Serial numbers (if applicable)

Stock Aging Report:
├── Group by lot number
├── Show age in days/weeks
├── Flag items >50% of shelf life
├── Auto-hold policy (>75% of shelf life)
├── Write-off workflow for expired

Example:
Lot: BATCH-2024-001
├── Received: 2024-01-15
├── Expiry: 2026-01-15 (24 months)
├── Current age: 14 months (58%)
├── Status: AVAILABLE
└── Recommended action: Normal sale
```

**Pricing:**
- Starter: €800/month (50K SKUs, 1 warehouse)
- Professional: €1,500/month (500K SKUs, 3 warehouses)
- Enterprise: Custom (1M+ SKUs, unlimited warehouses)
- Setup: €2,000-5,000

### 2.4 3PL Central (Fulfillment-Optimized)

**3PL-specific Features:**

```
Multi-Client Inventory Segregation:
├── Separate SKU namespaces per customer
├── Inventory allocation per customer
├── Customer-specific receiving rules
├── Billing by customer/transaction
├── Client portal for stock visibility
└── Commission management engine
```

**Billing Integration:**
```
Charge Types:
├── Receiving: Per pallet, per unit, per weight
├── Storage: Per unit, per cubic foot, per month
├── Picking: Per pick line, per order, flat fee
├── Packing: Per order, per item, per weight
├── Shipping: Passthrough carrier rates
└── Handling: Per transaction type
```

**Pricing:**
- Cloud Service: $1,200/month (base)
- Per transaction: $0.001-0.05
- Implementation: $3,000-10,000
- Custom development: $100-200/hour

### 2.5 Extensiv (Skubana Successor)

**Marketplace-Seller Focus:**
- Designed for Amazon FBM, eBay, Shopify sellers
- Tight integration with OMS platforms
- Lightweight deployment

**Feature Set:**
```
Core Capabilities:
├── Real-time inventory sync from marketplaces
├── Automated pick/pack/ship workflows
├── Carrier rate shopping (UPS, FedEx, DHL)
├── Returns processing (RMA automation)
├── Label generation and thermal printer support
└── Mobile app for warehouse operations
```

**Pricing:**
- Professional: $300/month (10K units/month)
- Advanced: $800/month (50K units/month)
- Custom: Enterprise pricing available

### 2.6 Cloud vs On-Premise WMS Decision Matrix

| Factor | Cloud | On-Premise |
|--------|-------|----------|
| **Upfront Capital** | Low (SaaS model) | High (software + hardware) |
| **Maintenance** | Vendor-managed | Internal IT |
| **Uptime SLA** | 99.5-99.9% | 90-95% typical |
| **Scalability** | Automatic | Manual infrastructure |
| **Data Security** | Vendor responsibility | Internal responsibility |
| **Compliance (GDPR)** | Built-in (trusted vendors) | Custom implementation |
| **Customization** | Limited APIs | Extensive |
| **Integration Speed** | 2-4 weeks | 2-6 months |
| **Total Cost (5 years)** | €120K-300K | €200K-800K |

---

## 3. EDI & API INTEGRATION (Part 1)

### 3.1 EDI Standards Overview

**EDIFACT (UN/EDIFACT) vs ANSI X12 Comparison:**

| Aspect | EDIFACT | ANSI X12 |
|--------|---------|----------|
| **Origin** | UN (1986) | ASC (1979) |
| **Primary Region** | Europe, Asia | North America |
| **Segment Separator** | ' (apostrophe) | ~ (tilde) |
| **Element Separator** | + (plus) | * (asterisk) |
| **Message Types** | ORDERS, DESADV, INVOIC, etc. | 850, 856, 810, etc. |
| **Complexity** | Moderate | Moderate-High |
| **Adoption in EU** | Widespread (80%+) | Limited (10%) |

**EDI Document Types for European Distribution:**

```
Standard EDIFACT Messages:

ORDERS (D96A/D08B)
├── PO transmission format
├── Line items with quantities, dates
├── Delivery schedule (JIT orders)
├── Special instructions
└── Pricing/discounting terms

DESADV (Dispatch Advice, D96A/D08B)
├── ASN (Advanced Shipping Notice) equivalent
├── Shipment details (weight, dimensions)
├── Serial numbers / lot tracking
├── Package level details
└── Tracking reference

INVOIC (D96A/D08B)
├── Invoice transmission
├── Quantity, pricing, tax codes
├── Payment terms (2/10 NET 30)
├── Discounts/allowances
└── GL account coding for accounting

ORDRSP (Order Response, D96A)
├── PO acknowledgement
├── Accepted quantities, dates
├── Backorder information
├── Rejection reasons
└── Alternative schedule

INVRPT (Inventory Report, D96A)
├── Stock level reporting
├── Location-based inventory
├── Movement history
└── SKU-warehouse matrix

RECADV (Receiving Advice, D96A)
├── Goods receipt confirmation
├── Actual vs expected quantities
├── QC results (pass/fail)
└── Lot numbers, serial numbers
```

### 3.2 EDI Transmission Methods

**SFTP (SSH File Transfer Protocol):**
```
Connection Profile:
├── Host: edi.supplier.com
├── Port: 22 (SSH)
├── Auth: RSA public/private key (preferred) or password
├── Directory structure:
│   ├── /inbound (receive POs, payment files)
│   ├── /outbound (send ASNs, invoices)
│   └── /archive (30-day retention)
├── File naming: ORDERS_20240415_001.edi
├── Frequency: Real-time or batch (4x daily)
└── Verification: MD5 checksum or file count reporting
```

**AS2 (Applicability Statement 2):**
```
Protocol Details:
├── HTTP/HTTPS-based EDI transmission
├── Digital signing (S/MIME) and encryption
├── Non-repudiation (MDN - Message Disposition Notification)
├── Connection: Partner-to-partner, no intermediary
├── Throughput: 100+ messages/minute
├── Latency: <5 seconds typical
├── Certificate management: Self-signed or CA-issued
└── Compliance: HIPAA, PCI-DSS secure
```

**API-based EDI Gateway (Modern Approach):**
```
Provider Examples:
├── Coupa Connect (procurement-focused)
├── GXS (Infor-owned, legacy)
├── TrustCommerce (modern API-first)
├── EDI2X (cloud-native, REST API)
└── Basware (invoice automation)

Typical Flow:
1. Transform ERP data → JSON/XML
2. POST to EDI gateway API
3. Gateway converts → EDI X12/EDIFACT
4. Gateway routes to partner SFTP/AS2
5. Partner receives EDI message
6. Webhook callback: Delivery confirmation
```

### 3.3 REST API Integration Patterns for Marketplace ↔ ERP ↔ WMS ↔ Carrier

**Typical Integration Architecture:**

```
MARKETPLACE          OMS                WMS                CARRIER
(Amazon EU)      (ChannelEngine)   (Logiwa)            (DHL/UPS)
    ↓                 ↓                 ↓                  ↓
[Order placed]  [Order received]  [Receiving]         [Shipment]
    │                 │                 │                 │
    └─────── API ────→│                 │                 │
                      │                 │                 │
                  [Route order]         │                 │
                      │                 │                 │
                      └─────── API ────→│                 │
                                        │                 │
                                    [Pick/Pack]          │
                                        │                 │
                                        └─────── API ────→│
                                                          │
                                                   [Generate label]
                                                          │
                                        ←─────── API ─────┘
                                    [Tracking updated]
                                        │
                      ←─────── API ─────┘
                  [Tracking to marketplace]
                      │
        └─────── API ─────────→
                    [Fulfillment status]
```

**REST API Specifications:**

| Component | Endpoint | Method | Rate Limit | Payload Size |
|-----------|----------|--------|-----------|---------------|
| OMS Order Fetch | /api/v1/orders | GET | 1000/min | <5MB |
| OMS Order Update | /api/v1/orders/:id | PATCH | 500/min | <1MB |
| WMS Inbound Create | /api/v2/inbound | POST | 100/min | <10MB |
| WMS Shipment Ready | /api/v2/shipments | POST | 100/min | <5MB |
| Carrier Rate Quote | /api/v1/rates | POST | 50/min | <500KB |
| Carrier Label Gen | /api/v1/shipments/label | POST | 50/min | <1MB |
