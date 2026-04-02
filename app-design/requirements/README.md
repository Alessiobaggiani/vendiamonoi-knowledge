# Vendiamonoi Platform — Requisiti Funzionali e Specifiche

**Versione**: 1.0
**Data**: 2026-04-02
**Audience**: Technical Team, Product Managers, Stakeholders
**Status**: Design Phase

---

## 1. Vision e Obiettivi

### 1.1 Dichiarazione di Vision
Vendiamonoi.it aims to build a unified, software-first platform that consolidates digital distribution operations currently fragmented across Google Sheets, Notion, Make.com, and 20+ marketplace portals. The platform will serve as the centralized hub for managing 100+ suppliers, 1M+ product SKUs, and millions of annual transactions across European marketplaces.

### 1.2 Obiettivi Strategici

#### Primary Objectives:
1. **Automazione Operativa (80%+ automation target)**
   - Current state: 40% of operations automated, 60% manual in spreadsheets/portal switching
   - Target: 80%+ automated with human-in-the-loop for exceptions
   - Impact: reduce operational overhead by €50K+/year in labor costs

2. **Centralizzazione Dati**
   - Eliminate data silos across Google Sheets, Notion, marketplace portals
   - Single source of truth for products, orders, suppliers, financials
   - Enable real-time visibility across all 20+ marketplace integrations

3. **Self-Service per Fornitori (Supplier Portal)**
   - Empower 100+ suppliers with self-service capabilities
   - Reduce supplier communication overhead by 60%
   - Enable automated order confirmation and invoice generation
   - Provide supplier dashboard: orders, stock levels, performance metrics

4. **Real-Time Visibility & Analytics**
   - Live KPI monitoring: orders/hour, revenue/day, pending actions
   - Marketplace health dashboard with alerts
   - Supplier performance scorecards
   - Financial reporting with VAT/OSS compliance

5. **Scalabilità e Crescita**
   - Support growth from current 50K SKUs to 1M+ SKUs
   - Handle 10K+ daily orders without performance degradation
   - Add new marketplaces/suppliers with minimal manual configuration
   - Sustainable infrastructure for multi-year operation

---

## 2. User Personas e Stakeholders

### 2.1 Primary Personas

**Persona 1: Operations Manager (Marco - Age 35)**
- Role: Manages daily order fulfillment, supplier coordination, exception handling
- Pain Points: Fragmented systems (Google Sheets + Notion + 20 marketplace portals), manual data entry (currently 60% of time), late detection of issues
- Goals: Reduce manual work to <20% of time, get real-time visibility, enable data-driven decisions
- Success Metrics: >80% automation, <1 hour to resolve exceptions, accurate forecasting

**Persona 2: Supplier Manager (Sara - Age 42)**
- Role: Onboards suppliers, manages supplier performance, negotiates contracts
- Pain Points: Repetitive communication, no self-service platform, delayed order confirmations
- Goals: Scale to 500+ suppliers without additional headcount, reduce communication overhead by 70%
- Success Metrics: Supplier activation time <48h, <2% of orders require manual intervention

**Persona 3: Finance Controller (Luca - Age 48)**
- Role: VAT/OSS compliance, invoicing, financial reporting, reconciliation
- Pain Points: Manual reconciliation across marketplaces, complicated VAT compliance, no real-time P&L
- Goals: Automated compliance reporting, real-time financial dashboards, zero reconciliation errors
- Success Metrics: Audit-ready reports in <1h, 99.9% invoice accuracy

**Persona 4: Tech Lead / CTO (Elena - Age 32)**
- Role: Oversees platform architecture, integrations, technical debt
- Pain Points: Brittle integrations with Make.com, limited scalability, no real API strategy
- Goals: Modern, scalable architecture; API-first approach; clear tech roadmap
- Success Metrics: <100ms p99 API latency, support 10K+ orders/day, <30min deployment

---

## 3. Functional Requirements

### 3.1 Core Platform Capabilities

#### A. Product Catalog Management
**Req 1.1: Multi-Source Catalog Ingestion**
- Enable bulk import from suppliers (CSV, XLSX, EDI)
- Support 50+ product attribute types
- Automatic deduplication and variant matching
- SKU generation with configurable templates
- Category auto-mapping (10+ European taxonomies)
- Status: MVP

**Req 1.2: Dynamic Inventory Synchronization**
- Real-time stock level updates from suppliers
- Threshold-based alerts (minimum stock, overstock)
- Marketplace inventory reservation and allocation
- Backorder management with automatic fulfillment
- Status: MVP

**Req 1.3: Price Management & Currency Conversion**
- Multi-currency pricing (EUR, GBP, SEK, CZK, RON, PLN, etc.)
- Dynamic pricing rules (tiered discounts, seasonal adjustments)
- Competitor price tracking and margin optimization
- Automated price sync to all 20+ marketplaces
- Status: MVP

**Req 1.4: Product Attribute Mapping**
- Map supplier attributes to marketplace attributes
- Automatic translation (google Translate + custom dictionaries)
- Validation rules per marketplace
- Bulk edit capabilities
- Status: MVP

---

#### B. Order Management & Fulfillment

**Req 2.1: Unified Order Ingestion**
- Aggregate orders from 20+ marketplaces (real-time webhooks)
- Order parsing and normalization
- Duplicate detection and prevention
- Split orders by supplier for optimization
- Status: MVP

**Req 2.2: Order Routing & Fulfillment Optimization**
- Intelligent supplier selection based on:
  - Stock availability
  - Lead time
  - Shipping cost
  - Performance history
  - Margin
- Batch optimization for cost reduction
- Multi-drop order handling
- Status: MVP

**Req 2.3: Supplier Communication**
- Automated order confirmation to suppliers
- EDI/API integration with tech-enabled suppliers
- Web portal for manual suppliers
- Delivery tracking and proof-of-delivery
- Exception escalation workflows
- Status: MVP

**Req 2.4: Marketplace Integration & Fulfillment Updates**
- Real-time shipment updates to marketplaces
- Tracking number insertion
- Delivery confirmation
- Return/RMA handling
- Automated refunds/credits
- Status: MVP

**Req 2.5: Customer Communication (Omnichannel)**
- Order confirmations (email + SMS + push)
- Tracking notifications
- Delivery confirmations
- Automated dispute resolution
- Multilingual support (10+ languages)
- Status: Phase 2

---

#### C. Supplier Management & Portal

**Req 3.1: Supplier Onboarding**
- Automated supplier registration forms
- Document verification (VAT ID, bank details)
- Performance threshold setup
- Contract template management
- SLA tracking
- Status: MVP

**Req 3.2: Supplier Self-Service Portal**
- Order dashboard (pending, shipped, delivered)
- Inventory upload interface
- Performance metrics & scorecards
- Payment statements
- Dispute resolution interface
- Support ticket system
- Status: MVP

**Req 3.3: Supplier Performance Tracking**
- Real-time KPIs:
  - On-time delivery %
  - Order accuracy %
  - Return rate %
  - Average response time
- Historical trends and forecasts
- Automated alerts for threshold violations
- Tier-based benefits (gold/silver/bronze suppliers)
- Status: MVP

**Req 3.4: Payment Management**
- Automatic payment scheduling
- Payment reconciliation
- Partial invoice support
- Currency conversion & FX tracking
- Refund management
- Tax form management (W-9, etc for non-EU)
- Status: Phase 2

---

#### D. Financial & Compliance

**Req 4.1: VAT & OSS Compliance**
- Automated VAT calculation per marketplace rules
- OSS (One-Stop-Shop) reporting in IT, DE, FR, ES
- Digital Services Act compliance tracking
- Audit trail for all transactions
- Status: MVP

**Req 4.2: Financial Reporting**
- Real-time revenue dashboards
- Margin analysis by product/supplier/marketplace
- Cash flow forecasting
- Profitability reports
- Expense tracking
- Tax-ready export formats
- Status: MVP

**Req 4.3: Reconciliation Automation**
- Automatic matching of marketplace payouts to orders
- Bank reconciliation
- Invoice matching (PO ↔ Invoice ↔ Payment)
- Variance analysis and alerts
- Status: Phase 2

---

#### E. Analytics & Reporting

**Req 5.1: Executive Dashboard**
- KPI cards: Orders/day, revenue, pending actions, top suppliers
- Marketplace health scorecard
- Supplier performance ranking
- Financial P&L summary
- Trend indicators (7-day, 30-day, 90-day)
- Status: MVP

**Req 5.2: Marketplace Analytics**
- Orders/revenue by marketplace
- Category mix analysis
- Shipping cost analysis
- Refund/return trends
- Competitive pricing insights
- Status: MVP

**Req 5.3: Supplier Analytics**
- Supplier contribution to revenue/orders
- Performance trends
- Cost per order analysis
- Reliability metrics
- Growth/churn trends
- Status: MVP

**Req 5.4: Custom Reports**
- Drag-and-drop report builder
- Scheduled report delivery (email/Slack)
- Export to Excel/PDF
- Historical comparison
- Status: Phase 2

---

#### F. System Integration & Data Exchange

**Req 6.1: Marketplace Integrations (20+ platforms)**
- Amazon (IT, DE, FR, ES, UK)
- eBay (EU)
- Kaufland (DE, RO, CZ)
- Bol.com (NL, BE)
- Cdiscount (FR)
- Carrefour, Leroy Merlin, ManoMano, METRO, and 11 more
- Real-time order sync
- Inventory/pricing updates
- Fulfillment updates
- Status: MVP (phased rollout)

**Req 6.2: Supplier Integrations**
- EDI/API for tech suppliers
- CSV/XLSX for traditional suppliers
- Direct database connections
- Webhook inbound for real-time updates
- Status: MVP

**Req 6.3: Accounting Software Integrations**
- QuickBooks Online
- Xero
- Payhawk (for expenses)
- Invoicing automation
- Status: Phase 2

**Req 6.4: Communication Integrations**
- Slack notifications
- Webhook outbound for external systems
- Email (SMTP)
- SMS (Twilio)
- Status: MVP

---

#### G. Authentication & Access Control

**Req 7.1: User Management**
- Email-based authentication
- Role-based access control (Admin, Manager, Supplier, Analyst)
- SSO support (future)
- Audit logging of all user actions
- IP whitelisting for supplier portals
- Status: MVP

**Req 7.2: Organization Management**
- Multi-tenant architecture
- Custom organization branding
- Team structure and permissions
- API key management
- Status: MVP

---

#### H. Mobile & UX

**Req 8.1: Mobile Interface (Phase 2)**
- Responsive web interface
- Mobile app for iOS/Android
- Push notifications
- Offline capabilities for supplier portal
- Status: Phase 2

**Req 8.2: User Experience**
- Intuitive dashboard design
- Onboarding flow for new users
- Help contextual help and documentation
- Search functionality across orders/suppliers/products
- Status: MVP

---

### 3.2 Non-Functional Requirements

#### A. Performance

| Metric | Target | Notes |
|--------|--------|-------|
| API Response Time (p99) | <100ms | For most endpoints |
| Page Load Time | <2s | Including assets |
| Order Processing Latency | <1s | From marketplace to internal system |
| Concurrent Users | 500+ | Simultaneous users |
| Orders/Second Throughput | 100+ | Peak capacity |
| Uptime | 99.9% | Monthly SLA |

#### B. Scalability

- Support growth from 50K to 1M+ SKUs
- Handle 10K+ orders/day without degradation
- Automatic infrastructure scaling
- Database optimization for large datasets

#### C. Security

- End-to-end encryption for sensitive data
- GDPR compliance (data privacy, right to be forgotten)
- SOC 2 readiness
- Regular security audits
- API rate limiting and DDoS protection
- PCI DSS compliance for payments (future)

#### D. Reliability

- Automated backups (daily + point-in-time recovery)
- Disaster recovery (RTO <1h, RPO <15min)
- Graceful degradation during outages
- Retry logic for transient failures

#### E. Maintainability

- Infrastructure as Code (IaC)
- Automated testing (unit, integration, e2e)
- Clear documentation
- Modular architecture for easy updates

---

## 4. Data Model Overview

### Core Entities

1. **Organizations** - Tenants of the platform
2. **Users** - Platform and supplier users
3. **Suppliers** - Partner vendors
4. **Products** - Catalog items with attributes
5. **Inventories** - Stock levels per supplier
6. **Marketplaces** - Sell-side channels (Amazon, eBay, etc.)
7. **Orders** - Customer orders from marketplaces
8. **Order Lines** - Individual items within orders
9. **Invoices** - Supplier invoices
10. **Payments** - Outbound payments to suppliers
11. **Financial Transactions** - Revenue and expenses
12. **Audit Logs** - All system actions

---

## 5. Architecture Overview

### High-Level Architecture

```
┌─────────────────────────────────────────┐
│   20+ Marketplace APIs (webhooks)      │
└──────────────┬──────────────────────────┘
               │
         ┌─────▼─────────────────┐
         │   API Gateway         │
         │   (Authentication)    │
         └─────┬─────────────────┘
               │
    ┌──────────┼──────────┐
    │          │          │
┌──▼───┐  ┌───▼────┐  ┌──▼────┐
│ REST │  │GraphQL │  │Webhooks
│ API  │  │  API   │  │
└──┬───┘  └───┬────┘  └──┬─────┘
   │          │          │
   └──────┬───┴────┬─────┘
          │        │
    ┌─────▼──────┐ │
    │ Make.com   │─┘  (Orchestration)
    │ Workflows  │
    └─────┬──────┘
          │
   ┌──────▼───────────────┐
   │  PostgreSQL Database │
   │  (Supabase)          │
   └──────┬───────────────┘
          │
   ┌──────▼───────────────┐
   │  UI (Next.js/React)  │
   └────────────────────  ┘
```

### Key Components

1. **API Gateway** - Authentication, rate limiting, routing
2. **REST API** - Main application APIs (100+ endpoints)
3. **Business Logic** - Order processing, supplier management, financials
4. **Database** - PostgreSQL with Row-Level Security (RLS)
5. **Background Jobs** - Order sync, reconciliation, reporting
6. **UI Layer** - React-based dashboard and supplier portal

---

## 6. Implementation Roadmap

### Phase 1: MVP (0-3 months)
- Core order management (marketplace → supplier)
- Basic supplier portal
- Financial tracking & VAT compliance
- 8 major marketplaces integration
- Real-time dashboards

### Phase 2: Enhancement (3-6 months)
- Payment automation
- Advanced reporting & custom reports
- Mobile app
- Accounting software integrations
- 12 additional marketplace integrations

### Phase 3: Scale (6-12 months)
- AI-powered price optimization
- Predictive inventory management
- Global expansion (non-EU marketplaces)
- Advanced reconciliation automation
- Supplier portal mobile app

---

## 7. Success Criteria

### Quantitative Metrics
- 80%+ operational automation (from current 40%)
- Support 100+ suppliers (from 50)
- Handle 1M+ SKUs (from 50K)
- Process 10K+ orders/day
- 99.9% system uptime
- Reduce manual labor by €50K+/year

### Qualitative Metrics
- Operations team satisfaction with system
- Supplier adoption and engagement
- Data accuracy and compliance
- Flexibility to adapt to new marketplaces

---

## 8. Glossary

- **SKU**: Stock Keeping Unit - unique product identifier
- **GMV**: Gross Merchandise Value - total order value
- **OSS**: One-Stop-Shop - EU VAT regulation
- **RLS**: Row-Level Security - database-level access control
- **SLA**: Service Level Agreement
- **EDI**: Electronic Data Interchange - B2B communication standard
- **Webhook**: Real-time event notifications
- **Refund/RMA**: Return Merchandise Authorization

---

## 9. Appendix: Market Research Summary

### 20 Marketplace Analysis

| Marketplace | Commission | Shipping Model | Integration Type | Priority |
|-------------|-----------|-----------------|------------------|----------|
| Amazon EU | 8-45% | Fulfilled by Amazon (FBA) or seller (FBM) | API + Webhooks | Phase 1 |
| eBay EU | 9.5-16% | Seller fulfillment | API | Phase 1 |
| Kaufland | 3-12% | Mirakl platform | Mirakl API | Phase 1 |
| Bol.com | 5-20% | Seller fulfillment | API | Phase 1 |
| Cdiscount | 4-14% | Mirakl platform | Mirakl API | Phase 1 |
| Carrefour Marketplace | 5-15% | Mirakl platform | Mirakl API | Phase 2 |
| Leroy Merlin | 3-10% | Mirakl platform | Mirakl API | Phase 2 |
| ManoMano | 4-12% | Mirakl platform | Mirakl API | Phase 2 |
| MediaWorld | 6-18% | Custom API | Custom API | Phase 2 |
| METRO | 2-8% | Mirakl B2B | Mirakl API | Phase 2 |
| Others (10 more) | Varies | Varies | Mix | Phase 2-3 |

---

## Change Log

| Version | Date | Notes |
|---------|------|-------|
| 1.0 | 2026-04-02 | Initial document |

---

**End of Document**
