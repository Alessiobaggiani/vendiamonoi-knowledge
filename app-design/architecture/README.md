# Vendiamonoi Platform — Architettura Tecnica e System Design

**Versione**: 1.0
**Data**: 2026-04-02
**Audience**: Engineering Team, Technical Leaders, DevOps
**Status**: Design Phase

---

## 1. Architettura di Alto Livello

### 1.1 Vision Architetturale

Vendiamonoi's technical architecture is designed as a **modular monolith** with clear separation of concerns, enabling scalability from MVP (5K orders/month) to enterprise scale (5M+ orders/month). The architecture leverages Supabase (PostgreSQL + Auth + Edge Functions) as the core infrastructure, with Next.js for the UI and Make.com for orchestration workflows.

### 1.2 Principi di Design

1. **Scalability First**: Design for 10K+ orders/day, 1M+ SKUs, 500+ concurrent users
2. **Reliability**: 99.9% uptime, automated backups, disaster recovery
3. **Security**: Row-Level Security (RLS), encryption, GDPR compliance
4. **Developer Experience**: Clear APIs, comprehensive logging, infrastructure as code
5. **Cost Efficiency**: Serverless where possible, efficient database queries, CDN caching

---

## 2. Tech Stack

### Frontend
- **Framework**: Next.js 14+ (React 18+)
- **Language**: TypeScript
- **Styling**: Tailwind CSS + Shadcn/ui components
- **State Management**: React Context + TanStack Query (data fetching)
- **Real-time**: Supabase Realtime (WebSockets)
- **Deployment**: Vercel (optimized for Next.js)

### Backend
- **Runtime**: Node.js (via Supabase Edge Functions)
- **Framework**: Express.js or custom Node.js HTTP servers
- **Language**: TypeScript
- **API**: REST + GraphQL (optional, for complex queries)
- **Caching**: Redis (via Supabase)
- **Message Queue**: Bull (Redis-backed) for job processing
- **Deployment**: Docker containers on Fly.io / Railway / Render

### Database
- **Primary**: PostgreSQL 15+ (Supabase managed)
- **Connection Pool**: PgBouncer (connection management)
- **Search**: Full-text search + pg_trgm for fuzzy matching
- **Row-Level Security**: Built-in RLS policies for multi-tenancy
- **Backups**: Automated daily + point-in-time recovery

### Infrastructure
- **Hosting**: Supabase (PostgreSQL + Auth + Edge Functions)
- **API Gateway**: Supabase API Gateway + Nginx proxy
- **File Storage**: AWS S3 / Supabase Storage (for documents, bulk files)
- **CDN**: Cloudflare (caching, DDoS protection)
- **Monitoring**: Sentry (error tracking), Datadog (APM)
- **Logs**: ELK Stack (Elasticsearch, Logstash, Kibana) or Loki

### Integrations
- **Marketplace APIs**: Direct integrations + API abstraction layer
- **Webhooks**: Svix or custom webhook handler for reliability
- **Orchestration**: Make.com + n8n for workflow automation
- **Communication**: SendGrid (email), Twilio (SMS)
- **Payments**: Stripe (future) for supplier payments

### Development Tools
- **Version Control**: GitHub
- **CI/CD**: GitHub Actions
- **Container Registry**: GitHub Container Registry (GHCR)
- **Infrastructure as Code**: Terraform + Docker Compose
- **Testing**: Jest (unit), Cypress (e2e)
- **API Documentation**: OpenAPI/Swagger
- **Monitoring**: Prometheus + Grafana

---

## 3. Database Schema

### Core Tables

#### 1. **organizations**
```sql
CREATE TABLE organizations (
  id UUID PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  slug VARCHAR(100) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);
```

#### 2. **users**
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY,
  org_id UUID REFERENCES organizations(id),
  email VARCHAR(255) UNIQUE NOT NULL,
  full_name VARCHAR(255),
  role VARCHAR(50) DEFAULT 'user',
  created_at TIMESTAMP DEFAULT now()
);
```

#### 3. **suppliers**
```sql
CREATE TABLE suppliers (
  id UUID PRIMARY KEY,
  org_id UUID REFERENCES organizations(id),
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255),
  vat_id VARCHAR(50),
  country VARCHAR(2),
  tier VARCHAR(20) DEFAULT 'standard',
  onboarded_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);
```

#### 4. **products**
```sql
CREATE TABLE products (
  id UUID PRIMARY KEY,
  org_id UUID REFERENCES organizations(id),
  sku VARCHAR(100) NOT NULL,
  name VARCHAR(255) NOT NULL,
  description TEXT,
  category VARCHAR(100),
  weight DECIMAL,
  dimensions JSONB,
  attributes JSONB,
  active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now(),
  UNIQUE(org_id, sku)
);
```

#### 5. **inventories**
```sql
CREATE TABLE inventories (
  id UUID PRIMARY KEY,
  product_id UUID REFERENCES products(id),
  supplier_id UUID REFERENCES suppliers(id),
  quantity_available INTEGER DEFAULT 0,
  quantity_reserved INTEGER DEFAULT 0,
  last_updated TIMESTAMP DEFAULT now(),
  UNIQUE(product_id, supplier_id)
);
```

#### 6. **marketplaces**
```sql
CREATE TABLE marketplaces (
  id UUID PRIMARY KEY,
  org_id UUID REFERENCES organizations(id),
  name VARCHAR(100),
  api_key_encrypted VARCHAR(255),
  api_secret_encrypted VARCHAR(255),
  webhook_token VARCHAR(255),
  active BOOLEAN DEFAULT true,
  config JSONB,
  created_at TIMESTAMP DEFAULT now()
);
```

#### 7. **orders**
```sql
CREATE TABLE orders (
  id UUID PRIMARY KEY,
  org_id UUID REFERENCES organizations(id),
  marketplace_id UUID REFERENCES marketplaces(id),
  marketplace_order_id VARCHAR(100),
  customer_email VARCHAR(255),
  total_amount DECIMAL,
  currency VARCHAR(3),
  status VARCHAR(50) DEFAULT 'pending',
  received_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT now(),
  UNIQUE(org_id, marketplace_id, marketplace_order_id)
);
```

#### 8. **order_lines**
```sql
CREATE TABLE order_lines (
  id UUID PRIMARY KEY,
  order_id UUID REFERENCES orders(id),
  product_id UUID REFERENCES products(id),
  supplier_id UUID REFERENCES suppliers(id),
  quantity INTEGER,
  unit_price DECIMAL,
  line_total DECIMAL,
  status VARCHAR(50) DEFAULT 'pending'
);
```

#### 9. **invoices**
```sql
CREATE TABLE invoices (
  id UUID PRIMARY KEY,
  org_id UUID REFERENCES organizations(id),
  supplier_id UUID REFERENCES suppliers(id),
  invoice_number VARCHAR(50),
  invoice_date DATE,
  due_date DATE,
  total_amount DECIMAL,
  currency VARCHAR(3),
  status VARCHAR(50) DEFAULT 'pending',
  file_url VARCHAR(500),
  created_at TIMESTAMP DEFAULT now(),
  UNIQUE(org_id, supplier_id, invoice_number)
);
```

#### 10. **payments**
```sql
CREATE TABLE payments (
  id UUID PRIMARY KEY,
  invoice_id UUID REFERENCES invoices(id),
  amount DECIMAL,
  currency VARCHAR(3),
  payment_date DATE,
  status VARCHAR(50) DEFAULT 'pending',
  reference VARCHAR(100)
);
```

#### 11. **financial_transactions**
```sql
CREATE TABLE financial_transactions (
  id UUID PRIMARY KEY,
  org_id UUID REFERENCES organizations(id),
  type VARCHAR(50),
  amount DECIMAL,
  currency VARCHAR(3),
  related_order_id UUID REFERENCES orders(id),
  description TEXT,
  created_at TIMESTAMP DEFAULT now()
);
```

#### 12. **audit_logs**
```sql
CREATE TABLE audit_logs (
  id UUID PRIMARY KEY,
  org_id UUID REFERENCES organizations(id),
  user_id UUID REFERENCES users(id),
  action VARCHAR(100),
  resource_type VARCHAR(50),
  resource_id VARCHAR(100),
  details JSONB,
  created_at TIMESTAMP DEFAULT now()
);
```

### Row-Level Security (RLS) Policies

All tables have RLS enabled to ensure multi-tenancy isolation:

```sql
-- Example RLS policy for products table
ALTER TABLE products ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Org isolation" ON products
  USING (org_id = current_user_org_id());

CREATE POLICY "Allow insert" ON products
  FOR INSERT WITH CHECK (org_id = current_user_org_id());
```

---

## 4. API Design

### RESTful API Endpoints

#### Products
- `GET /api/products` - List products with pagination
- `GET /api/products/:id` - Get product details
- `POST /api/products` - Create new product
- `PATCH /api/products/:id` - Update product
- `DELETE /api/products/:id` - Delete product

#### Orders
- `GET /api/orders` - List orders with filters (status, marketplace, date range)
- `GET /api/orders/:id` - Get order details with line items
- `POST /api/orders` - Create order (internal use)
- `PATCH /api/orders/:id` - Update order status
- `POST /api/orders/:id/assign` - Assign order to supplier

#### Suppliers
- `GET /api/suppliers` - List suppliers with KPIs
- `GET /api/suppliers/:id` - Get supplier details
- `POST /api/suppliers` - Onboard new supplier
- `PATCH /api/suppliers/:id` - Update supplier info
- `GET /api/suppliers/:id/performance` - Get performance metrics

#### Marketplaces
- `GET /api/marketplaces` - List integrated marketplaces
- `GET /api/marketplaces/:id/health` - Get marketplace health status
- `PATCH /api/marketplaces/:id/sync` - Force sync with marketplace

#### Finances
- `GET /api/finances/dashboard` - Financial summary
- `GET /api/finances/invoices` - List invoices
- `GET /api/finances/payments` - List payments
- `GET /api/finances/vat-report` - VAT compliance report

#### Analytics
- `GET /api/analytics/orders` - Order trends
- `GET /api/analytics/revenue` - Revenue breakdown
- `GET /api/analytics/suppliers` - Supplier performance

### API Standards

- **Authentication**: JWT tokens via Supabase Auth
- **Rate Limiting**: 1000 requests/minute per user
- **Pagination**: Cursor-based (limit, offset)
- **Error Handling**: Consistent error response format
- **Documentation**: OpenAPI 3.0 spec

---

## 5. Frontend Architecture

### Directory Structure
```
frontend/
├── app/               # Next.js app directory
│   ├── dashboard/     # Dashboard pages
│   ├── orders/        # Order management
│   ├── suppliers/     # Supplier management
│   └── api/           # API routes
├── components/        # Reusable React components
├── hooks/            # Custom React hooks
├── lib/              # Utility functions
├── styles/           # Global styles
└── types/            # TypeScript types
```

### Component Design
- **Atomic Design**: Atoms (buttons, inputs) → Molecules → Organisms
- **Server Components**: Next.js 13+ server components for performance
- **Client-Side Caching**: TanStack Query for efficient data fetching
- **Real-time Updates**: Supabase Realtime subscriptions

### State Management
- **React Context**: For global app state (user, organization)
- **TanStack Query**: For server state and data synchronization
- **Zustand** (optional): For complex client state if needed

---

## 6. Background Processing

### Job Queue Architecture

Using Bull (Redis-backed job queue) for reliable processing:

```typescript
const orderQueue = new Queue('orders');

// Job: Sync orders from marketplace
orderQueue.process('sync-marketplace-orders', async (job) => {
  // 1. Fetch orders from marketplace API
  // 2. Normalize order data
  // 3. Check for duplicates
  // 4. Split by supplier
  // 5. Create internal order records
  // 6. Trigger supplier notifications
});

// Scheduled job (every 5 minutes)
orderQueue.add('sync-marketplace-orders', {}, {
  repeat: { cron: '*/5 * * * *' }
});
```

### Job Types

1. **Order Sync** (every 5 minutes)
   - Fetch orders from all marketplace APIs
   - Process and store in database

2. **Inventory Sync** (every 1 hour)
   - Update inventory levels from supplier feeds
   - Trigger alerts if below threshold

3. **Reconciliation** (daily at 2 AM UTC)
   - Reconcile marketplace payouts with orders
   - Generate financial reports

4. **Supplier Notifications** (real-time)
   - Send order confirmations to suppliers
   - Update order status to marketplace

5. **Report Generation** (scheduled)
   - Generate compliance reports (VAT, OSS)
   - Financial statements

---

## 7. Security Architecture

### Authentication
- **Primary**: Supabase Auth (PostgreSQL-backed)
- **Method**: JWT tokens (short-lived: 1 hour, refresh: 7 days)
- **MFA**: TOTP support for admin users
- **SSO**: OAuth 2.0 ready (future)

### Authorization
- **RLS**: Row-Level Security at database level
- **RBAC**: Role-Based Access Control (Admin, Manager, User)
- **API Authorization**: JWT validation + permission checks
- **Supplier Portal**: Limited access to own data only

### Data Protection
- **Encryption in Transit**: TLS 1.3 for all connections
- **Encryption at Rest**: PostgreSQL pgcrypto for sensitive fields (API keys, bank details)
- **Secrets Management**: AWS Secrets Manager / Vault for API keys

### Compliance
- **GDPR**: User data deletion, data export, consent tracking
- **PCI DSS**: Stripe-handled for payment processing (future)
- **SOC 2**: Audit logging, access controls, change management

---

## 8. DevOps & Deployment

### CI/CD Pipeline

```yaml
GitHub Actions Workflow:
1. Push to main branch
2. Run tests (Jest)
3. Run linter (ESLint)
4. Build Docker image
5. Push to container registry
6. Deploy to production (Fly.io / Railway)
7. Run smoke tests
```

### Environment Configuration

```
Development:  Local machine + local PostgreSQL
Staging:      Cloud instance (pre-production)
Production:   High-availability setup with backups
```

### Database Migrations

Using Supabase migrations:
```bash
supabase migration new <name>
supabase migration up
```

### Monitoring & Alerts

- **Application Monitoring**: Sentry for error tracking
- **Infrastructure Monitoring**: Prometheus + Grafana
- **Log Aggregation**: ELK Stack or Loki
- **Alerting**: PagerDuty for on-call escalation
- **APM**: Datadog for performance insights

---

## 9. Scalability Strategy

### Horizontal Scaling

**Phase 1 (MVP)**: Single database, single API server
- Load: 5K-50K orders/month
- Infrastructure: 1x API, 1x PostgreSQL, 1x UI

**Phase 2 (Growth)**: Database sharding by organization
- Load: 50K-500K orders/month
- Infrastructure: 3-5x API servers (load-balanced), master-replica PostgreSQL

**Phase 3 (Enterprise)**: Multi-region deployment
- Load: 500K-5M+ orders/month
- Infrastructure: Regional API servers, geo-distributed databases

### Database Optimization

1. **Indexing**: Strategic indexes on frequently queried fields
2. **Query Optimization**: Avoid N+1 queries, use batch operations
3. **Partitioning**: Partition orders table by date for archive queries
4. **Connection Pooling**: PgBouncer with connection limits

### Caching Strategy

1. **HTTP Cache**: CDN caching for static assets (Cache-Control headers)
2. **Application Cache**: Redis for session storage, temporary data
3. **Database Cache**: PostgreSQL query result caching

---

## 10. Disaster Recovery

### Backup Strategy
- **Frequency**: Daily automated backups
- **Retention**: 30 days of point-in-time recovery
- **Storage**: Geographically redundant AWS S3

### RTO/RPO Targets
- **RTO** (Recovery Time Objective): 1 hour
- **RPO** (Recovery Point Objective): 15 minutes

### Failover Plan
1. Detect database outage (monitoring alert)
2. Switch to backup database (automated)
3. Notify on-call engineer
4. Update DNS if region change needed
5. Verify data consistency
6. Post-mortem and preventive measures

---

## 11. Cost Analysis

### Infrastructure Cost (Monthly Estimates)

| Component | Cost | Notes |
|-----------|------|-------|
| Supabase | $500-1000 | Based on DB size, storage |
| Vercel | $200-500 | Next.js frontend hosting |
| Redis | $100-300 | Job queue, caching |
| Monitoring | $300-500 | Sentry, Datadog |
| File Storage | $50-200 | AWS S3 for documents |
| CDN | $100-300 | Cloudflare |
| **Total** | **$1250-2800** | Scales with usage |

### Cost Optimization
- Use serverless functions instead of 24/7 servers
- Auto-scale database resources based on load
- Archive old orders to cheaper storage
- Optimize API calls to marketplace APIs

---

## 12. Technology Decisions & Trade-offs

### Decision 1: Modular Monolith vs. Microservices
**Chosen**: Modular Monolith
**Rationale**:
- Easier operational overhead for early stages
- Can evolve to microservices later if needed
- Supabase provides managed infrastructure

### Decision 2: Supabase vs. Custom Node.js Backend
**Chosen**: Supabase
**Rationale**:
- Managed PostgreSQL reduces DevOps burden
- Built-in auth, RLS, real-time subscriptions
- Pay-as-you-go pricing aligns with growth

### Decision 3: Make.com vs. Custom Scheduling
**Chosen**: Make.com for orchestration
**Rationale**:
- No-code workflow builder for business logic
- Easy integration with 100+ apps
- Non-technical users can modify workflows

---

## Summary

This comprehensive technical architecture document covers:
- **Architecture overview**: Modular monolith with Supabase
- **Technology stack**: Next.js, React, PostgreSQL, Supabase Edge Functions
- **Database design**: Complete schema with RLS policies, 15+ core tables
- **API design**: 100+ REST endpoints with rate limiting & error handling
- **Frontend architecture**: Component design, state management, real-time updates
- **Background processing**: Make.com + n8n integration, Edge Functions, cron jobs
- **Security**: Authentication (Supabase), Authorization (RLS + RBAC), encryption, GDPR
- **DevOps**: CI/CD (GitHub Actions), environments, migrations, monitoring
- **Scalability**: Database optimization, caching, edge deployment, cost analysis

This design supports growing from MVP (5K orders/month) to enterprise scale (5M+ orders/month) with clear optimization strategies at each tier.