# Supabase — Architettura Tecnica Completa

**Versione:** 1.0
**Data:** Aprile 2026
**Target:** Architetti di sistemi, CTO, Engineering Leaders
**Ambito:** Architettura interna, pattern di design, concetti avanzati

---

## Informazioni Generali

| Aspetto | Dettagli |
|---------|----------|
| **Nome Ufficiale** | Supabase — The Postgres Development Platform |
| **Stack Open-Source** | PostgreSQL + PostgREST + GoTrue + Realtime + Storage + Edge Functions |
| **Modello di Licensing** | Open-source (Apache 2.0) + SaaS managed + self-hosting |
| **Repository Principale** | [github.com/supabase/supabase](https://github.com/supabase/supabase) |
| **Database Core** | PostgreSQL 15+ (managed), 14+ (self-hosted) |
| **Disponibilità Geografica** | US, EU, Asia-Pacific (SaaS managed) |
| **SLA Managed** | 99.9% (Pro+Team+Enterprise) |
| **Supporto Multilinguaggio SDK** | JavaScript/TypeScript, Python, Dart, Swift, Kotlin, C# |

---

## 1. Architettura Core

### 1.1 Componenti Principali

Supabase non è un singolo prodotto, ma un'orchestrazione di componenti open-source battaglia-testati:

```
┌─────────────────────────────────────────────────────────┐
│              Kong API Gateway (Router)                  │
├─────────────────────────────────────────────────────────┤
│  ┌──────────┬──────────┬──────────┬──────────┬──────┬───┤
│  │ PostgREST│ GoTrue   │ Realtime │ Storage  │ Func │...│
│  │ (REST)   │ (Auth)   │ (WS)     │ (S3)     │ (Edge) │
│  └──────────┴──────────┴──────────┴──────────┴──────┴───┤
│                                                         │
│                PostgreSQL (Core DB)                    │
│                                                         │
│      ├─ pgBouncer / Supavisor (Connection Pool)       │
│      ├─ Extensions (pgvector, pg_cron, pg_net, etc.)  │
│      ├─ Logical Replication (WAL2JSON)                │
│      └─ Row Level Security (RLS) Enforcement          │
└─────────────────────────────────────────────────────────┘
```

**Flusso di una richiesta API tipica:**

1. Client invia HTTPS GET/POST a `https://project.supabase.co/rest/v1/users`
2. Kong Router introspetta l'Authorization header (JWT)
3. Kong indirizza verso PostgREST
4. PostgREST introspects schema PostgreSQL
5. PostgREST applica RLS policies via auth.uid() / auth.jwt()
6. Query eseguita direttamente su PostgreSQL
7. Risultato serializzato a JSON ritornato al client

### 1.2 Stack Componenti Dettagliato

#### **PostgreSQL**
- **Versione:** 15.x (SaaS), 14+ (self-hosted)
- **Ruolo:** Source of truth, business logic, security enforcement
- **Caratteristiche chiave:** ACID transazioni, type system ricco, JSON/JSONB native, FDW, extensions
- **Extensions critiche:** pgvector, pg_cron, pg_net, pgaudit, uuid-ossp, unaccent, pg_trgm

#### **PostgREST**
- **Linguaggio:** Haskell
- **Ruolo:** Auto-generated REST API sopra schema SQL
- **Caratteristiche:** Introspection, filtering, embedding, bulk operations, stored procedure support
- **Latenza tipica:** 10-50ms (escludendo latenza di rete)

#### **GoTrue**
- **Linguaggio:** Go
- **Ruolo:** JWT-based authentication service
- **Supporta:** Email/password, phone/OTP, OAuth (Google, GitHub, Microsoft, Apple, Twitch, Discord, etc.), SAML, SSO
- **Output:** JWT con custom claims integrabili con RLS

#### **Realtime**
- **Linguaggio:** Elixir/Phoenix
- **Ruolo:** WebSocket server per Presence, Broadcast, PostgreSQL change streaming
- **CDC Technology:** Logical replication + WAL2JSON + WALRUS PostgreSQL function
- **Concorrenze supportate:** Milioni di connessioni simultanee per istanza

#### **Storage**
- **Lingua:** TypeScript/Node.js
- **Ruolo:** S3-compatible object storage con metadata PostgreSQL
- **Features:** RLS-based access control, image transformation (width/height/quality), signed URLs, CDN

#### **Edge Functions**
- **Runtime:** Deno (TypeScript/JavaScript)
- **Deployment:** Distributed edge locations (Cloudflare, Vercel, etc.)
- **Limiti:** 2s CPU time, 150s idle timeout, 20MB bundled size, 512MB runtime memory

### 1.3 Managed vs Self-Hosted Comparison

| Feature | Managed (SaaS) | Self-Hosted |
|---------|---|---|
| **Infrastructure** | Supabase-managed | Your servers/K8s |
| **Backup & DR** | Automated, point-in-time recovery | Your responsibility |
| **Security patching** | Automatic | Manual |
| **Scaling** | Transparent | Manual / K8s-based |
| **Monitoring** | Supabase Dashboard | Your observability stack |
| **Networking** | Public IP (optional private endpoint) | Full network control |
| **Cost model** | Usage-based billing | CapEx + OpEx |
| **Database size limit** | Plan-dependent (500MB-4TB+) | Infrastructure-dependent |
| **Custom extensions** | Limited pre-approved set | Full PostgreSQL ecosystem |
| **Compliance** | SOC2, HIPAA (Enterprise) | Your compliance burden |

---

## 2. PostgreSQL — Il Cuore

### 2.1 Perché PostgreSQL e Non Altre DB?

**vs MySQL/MariaDB:**
- PostgreSQL ha type system più ricco (composite types, enums, ranges, JSON, arrays)
- JSON/JSONB è first-class (native, indexable, queryable)
- Full-text search integrato (non necessita Elasticsearch per use case base)
- Trigger e stored procedures (PL/pgSQL, PL/Python, PL/Perl) per business logic nel DB
- RLS integrato (MySQL ha solo view-based security, non policy-driven)
- Window functions, recursive CTEs, lateral joins (pattern modeling avanzati)
- LISTEN/NOTIFY per pub/sub primitivo (fondamentale per Realtime di Supabase)

**vs MongoDB:**
- ACID transazioni multi-document garantite
- Schema enforcement opzionale (JSONB + CHECK constraints)
- Join efficiency (MongoDB richiede data denormalization)
- Query optimizer maturo
- Full-text search nativo
- Ecosystem di tools (pg_dump, barman, pgBackRest per backup/PITR)

**vs DynamoDB/Firebase:**
- Transazioni complesse (non solo item-level)
- Complex queries senza scan di intere tabelle
- Costo prevedibile (billing per compute + storage, non per read/write ops)
- SQL standard compliance (portabilità)
- Zero vendor lock-in

### 2.2 Ecosystem Extensions

Supabase pre-installa e mantiene:

**Dati & Query:**
```sql
-- pgvector: vector similarity search per embeddings/AI
CREATE EXTENSION IF NOT EXISTS vector;
CREATE TABLE documents (
  id SERIAL PRIMARY KEY,
  embedding vector(1536)  -- OpenAI embedding size
);
CREATE INDEX ON documents USING ivfflat (embedding vector_cosine_ops);

-- pg_trgm: trigram-based full-text search
CREATE EXTENSION IF NOT EXISTS pg_trgm;
CREATE INDEX ON users USING GIN (username gin_trgm_ops);

-- ltree: hierarchical tree data
CREATE EXTENSION IF NOT EXISTS ltree;
CREATE TABLE org_hierarchy (
  id SERIAL,
  path ltree,
  name TEXT
);

-- PostGIS: geospatial queries
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE TABLE locations (
  id SERIAL,
  geom geometry(Point, 4326)
);
```

**Automazione & Network:**
```sql
-- pg_cron: job scheduler (background jobs)
CREATE EXTENSION IF NOT EXISTS pg_cron;
SELECT cron.schedule('vacuum-job', '0 2 * * *', 'VACUUM ANALYZE;');

-- pg_net: HTTP calls directly from DB
CREATE EXTENSION IF NOT EXISTS pg_net;
SELECT net.http_post(
  'https://webhook.example.com/events',
  jsonb_build_object('user_id', auth.uid(), 'action', 'signup')
);
```

**Audit & Monitoring:**
```sql
-- pgaudit: detailed audit logging
CREATE EXTENSION IF NOT EXISTS pgaudit;

-- pg_stat_statements: query performance analysis
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;

-- uuid-ossp: UUID generation
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```

**Ricerca & Pattern Matching:**
```sql
-- unaccent: remove accents per ricerca case-insensitive
CREATE EXTENSION IF NOT EXISTS unaccent;

-- fuzzystrmatch: Levenshtein distance
CREATE EXTENSION IF NOT EXISTS fuzzystrmatch;
```

### 2.3 Connection Pooling: pgBouncer vs Supavisor

**pgBouncer** (legacy, still widely used):
- Single-process, single-threaded (max 1 CPU core utilization)
- ~2MB RAM per 1,000 client connections
- Pool modes:
  - **Session**: 1 app conn = 1 DB conn (preservato per sessione HTTP)
  - **Transaction**: 1 app conn multiplexed su pool DB conn (efficiència máxima)
- Latency tipico: < 1ms per connection handshake
- Limiti: ~50 client connections prima che bottleneck CPU

**Supavisor** (nuovo, cloud-native):
- Multi-process Elixir/Erlang, leverages multiple CPU cores
- Multi-tenant architecture (ogni tenant ha own pool dinamico)
- Connection distribution across cluster nodes
- Latency tipico: 80-160% rispetto pgBouncer (higher ma still acceptable)
- Vantaggi: Massively scalable (millions of connections), serverless-friendly

**Configuration Example:**
```ini
# pgbouncer.ini
[databases]
mydb = host=localhost port=5432 dbname=postgres

[pgbouncer]
pool_mode = transaction       # multiplexing per transaction
max_client_conn = 1000        # connections da app
default_pool_size = 25        # connections verso PostgreSQL
reserve_pool_size = 5
reserve_pool_timeout = 3
```

### 2.4 Row Level Security (RLS) — Deep Dive

RLS è il meccanismo fondamentale per security in Supabase. **Non è opzionale per multi-tenant—è obbligatorio**.

#### **Anatomia di una RLS Policy:**

```sql
-- Enable RLS su tabella
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;

-- Policy 1: Utenti possono leggere solo i loro post
CREATE POLICY "Users can view own posts" ON posts
  FOR SELECT
  USING (auth.uid() = user_id);

-- Policy 2: Utenti possono solo scrivere come se stessi
CREATE POLICY "Users can insert own posts" ON posts
  FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- Policy 3: Utenti possono editare solo i loro post
CREATE POLICY "Users can update own posts" ON posts
  FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

-- Policy 4: Utenti possono deletare solo i loro post
CREATE POLICY "Users can delete own posts" ON posts
  FOR DELETE
  USING (auth.uid() = user_id);
```

**Componenti:**
- **USING**: Filtra rows per SELECT, DELETE
- **WITH CHECK**: Valida rows per INSERT, UPDATE (deve passare check per INSERT/UPDATE succeeding)
- **auth.uid()**: Function che ritorna current user ID da JWT claims
- **auth.jwt()**: Accesso full JWT payload

#### **Pattern Avanzati:**

**1. Tenant-aware RLS (Multi-tenant SaaS):**
```sql
-- Tabella con tenant_id
CREATE TABLE organizations (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL
);

CREATE TABLE organization_members (
  id SERIAL PRIMARY KEY,
  org_id UUID REFERENCES organizations(id),
  user_id UUID,
  role TEXT
);

-- RLS che permette accesso solo se membro
ALTER TABLE organizations ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Org members can view" ON organizations
  FOR SELECT
  USING (
    id IN (
      SELECT org_id FROM organization_members
      WHERE user_id = auth.uid()
    )
  );
```

**2. Role-based RLS:**
```sql
-- JWT contiene custom claim: claims ->> 'role' = 'admin' | 'user'
CREATE POLICY "Admins can see everything, users see own data" ON users
  FOR SELECT
  USING (
    (auth.jwt() ->> 'role' = 'admin') OR
    (auth.uid() = id)
  );
```

**3. Organization + Role (SaaS Standard):**
```sql
CREATE POLICY "Access based on org membership + role" ON posts
  FOR SELECT
  USING (
    org_id IN (
      SELECT org_id FROM organization_members
      WHERE user_id = auth.uid()
    )
    AND
    (
      auth.jwt() ->> 'org_role' = 'admin'
      OR user_id = auth.uid()
    )
  );
```

#### **Performance Implications:**

**Problemi Comuni:**
1. **Missing indexes**: RLS policies su colonne non-indexed = full table scans
   ```sql
   -- SBAGLIATO: user_id non indexed
   CREATE POLICY "..." USING (org_id = 42);  -- se org_id non indexed, scan completo

   -- CORRETTO:
   CREATE INDEX idx_posts_org_id ON posts(org_id);
   ```

2. **Function execution per-row**: `auth.uid()` called per ogni row
   ```sql
   -- Postgres optimizer CACHES scalar funzioni se wrapped in subquery
   -- MIGLIORE:
   CREATE POLICY "..." USING (
     user_id = (SELECT auth.uid())  -- initPlan, eseguito una sola volta
   );
   ```

3. **Nested subqueries**: O(n) complexity
   ```sql
   -- LENTO: per ogni row nella main table, esegui subquery su join table
   CREATE POLICY "..." USING (
     id IN (SELECT post_id FROM post_collaborators WHERE user_id = auth.uid())
   );

   -- PIÙ VELOCE: flip il join direction
   CREATE POLICY "..." USING (
     user_id IN (SELECT user_id FROM post_collaborators WHERE post_id = id)
   );
   -- ...ma ancora meglio: move la logica a security definer function
   ```

**Best Practices Performance:**
- Indizza **sempre** colonne in RLS USING/WITH CHECK
- Usa security definer functions per logica complessa (evita RLS ricorsiva)
- Cache `auth.uid()` se usato multipli volte
- Monitora query plans con `EXPLAIN ANALYZE`
- Testing: `SELECT * FROM table WHERE <policy_condition>` deve essere fast

#### **RLS Debugging:**

```sql
-- Visualizza active RLS policies
SELECT schemaname, tablename, policyname, permissive, cmd, qual, with_check
FROM pg_policies
WHERE tablename = 'posts';

-- Test policy (come utente X)
SET ROLE authenticated;
SET request.jwt.claim.sub = 'user-uuid-here';
SELECT * FROM posts;  -- só vedi rows dove user_id = 'user-uuid-here'

-- Esegui EXPLAIN per vedere se query usa indici
EXPLAIN (ANALYZE, BUFFERS)
SELECT * FROM posts WHERE org_id = 1;
```

### 2.5 Triggers e Stored Procedures

Supabase supporta automazione database-level via triggers PL/pgSQL:

```sql
-- Audit trail trigger
CREATE TABLE audit_log (
  id SERIAL PRIMARY KEY,
  table_name TEXT,
  operation TEXT,
  old_data JSONB,
  new_data JSONB,
  changed_at TIMESTAMP DEFAULT NOW(),
  changed_by UUID
);

CREATE OR REPLACE FUNCTION audit_trigger()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO audit_log (table_name, operation, old_data, new_data, changed_by)
  VALUES (
    TG_TABLE_NAME,
    TG_OP,
    CASE WHEN TG_OP = 'DELETE' THEN row_to_json(OLD) ELSE NULL END,
    CASE WHEN TG_OP IN ('INSERT', 'UPDATE') THEN row_to_json(NEW) ELSE NULL END,
    auth.uid()
  );
  RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Attach su users table
CREATE TRIGGER users_audit_trigger
AFTER INSERT OR UPDATE OR DELETE ON users
FOR EACH ROW EXECUTE FUNCTION audit_trigger();
```

**Funzioni come API endpoints:**
```sql
CREATE OR REPLACE FUNCTION get_user_stats(p_user_id UUID)
RETURNS TABLE (post_count INT, follower_count INT)
AS $$
BEGIN
  RETURN QUERY
  SELECT
    COUNT(*)::INT,
    (SELECT COUNT(*) FROM followers WHERE user_id = p_user_id)::INT
  FROM posts
  WHERE user_id = p_user_id;
END;
$$ LANGUAGE plpgsql STABLE;

-- PostgREST espone automaticamente come:
-- GET /rest/v1/rpc/get_user_stats?p_user_id=uuid
```

### 2.6 Partitioning Strategies

PostgreSQL partitioning è essenziale per tabelle grandi (>10M rows):

```sql
-- Range partitioning (time-series data)
CREATE TABLE events (
  id BIGSERIAL,
  event_type TEXT,
  created_at TIMESTAMP,
  data JSONB
) PARTITION BY RANGE (created_at);

CREATE TABLE events_2026_q1 PARTITION OF events
  FOR VALUES FROM ('2026-01-01') TO ('2026-04-01');
CREATE TABLE events_2026_q2 PARTITION OF events
  FOR VALUES FROM ('2026-04-01') TO ('2026-07-01');
-- etc.

-- Hash partitioning (distribute by user_id)
CREATE TABLE user_events (
  id SERIAL,
  user_id UUID,
  event_type TEXT
) PARTITION BY HASH (user_id);

CREATE TABLE user_events_0 PARTITION OF user_events
  FOR VALUES WITH (MODULUS 4, REMAINDER 0);
CREATE TABLE user_events_1 PARTITION OF user_events
  FOR VALUES WITH (MODULUS 4, REMAINDER 1);
-- etc.

-- List partitioning (categorical)
CREATE TABLE orders (
  id SERIAL,
  status TEXT,
  amount NUMERIC
) PARTITION BY LIST (status);

CREATE TABLE orders_pending PARTITION OF orders
  FOR VALUES IN ('pending');
CREATE TABLE orders_completed PARTITION OF orders
  FOR VALUES IN ('completed', 'shipped');
```

### 2.7 Index Types e Use Cases

```sql
-- B-tree (default, per equality + range)
CREATE INDEX ON products (price);
EXPLAIN SELECT * FROM products WHERE price > 100;

-- GIN (Generalized Inverted Index, per JSONB + full-text)
CREATE INDEX ON documents USING GIN (content);
SELECT * FROM documents WHERE content @> '{"status": "active"}';

-- GiST (Generalized Search Tree, per geometrie, range types)
CREATE EXTENSION postgis;
CREATE INDEX ON locations USING GIST (geom);
SELECT * FROM locations WHERE geom && ST_MakeEnvelope(-10, -10, 10, 10, 4326);

-- BRIN (Block Range Index, per tabelle very large + sequential order)
CREATE TABLE large_timeseries (id SERIAL, ts TIMESTAMP, value NUMERIC);
CREATE INDEX ON large_timeseries USING BRIN (ts);

-- Hash (per equality only, rare use)
CREATE INDEX ON users USING HASH (email);
```

### 2.8 VACUUM e Maintenance

PostgreSQL ha necessità di manutenzione proattiva:

```sql
-- VACUUM: reclaim storage, aggiorna visibility info
VACUUM ANALYZE users;  -- full rewrite, blocks table

-- VACUUM ANALYZE: aggiorna table statistics per query planner
VACUUM ANALYZE;  -- entire database

-- AUTOVACUUM (default enabled)
-- Configurable per-table:
ALTER TABLE users SET (autovacuum_vacuum_scale_factor = 0.05);

-- BLOAT monitoring
SELECT schemaname, tablename,
  ROUND(100 * (CASE WHEN otta > 0 THEN sml.relpages::float/otta
    ELSE 0.0 END), 2) AS ratio
FROM pg_class c
LEFT JOIN pg_stat_user_tables AS sml ON sml.relid = c.oid
WHERE c.relkind = 't';

-- REINDEX se indici frammentati
REINDEX TABLE CONCURRENTLY users;
```

### 2.9 Performance Tuning Parameters

Parametri PostgreSQL critici per Supabase:

```sql
-- Connection pool size (total connections che PostgreSQL supporta)
max_connections = 200  -- default, adjust per workload

-- Memory settings
shared_buffers = 256MB           -- 25% system RAM, max 40%
effective_cache_size = 1GB       -- 50-75% system RAM
work_mem = 4MB                   -- per operation, total = work_mem * max_connections
maintenance_work_mem = 64MB      -- VACUUM, CREATE INDEX

-- Planner settings
random_page_cost = 1.1           -- SSD: 1.1, HDD: 4.0
effective_io_concurrency = 200   -- SSDs: 200, HDD: 1-4

-- Query planning
join_collapse_limit = 8          -- default, allow more complex plans
geqo_threshold = 12

-- Logging (per debugging)
log_min_duration_statement = 1000  -- log queries > 1s
log_line_prefix = '%t [%p]: [%l-1] user=%u,db=%d,app=%a,client=%h '
```

---

## 3. PostgREST — Auto-generated REST API

### 3.1 Come PostgREST Funziona Internamente

PostgREST non interroga il database via application logic. **Introspects lo schema PostgreSQL e traduce HTTP requests direttamente in SQL queries.**

```
HTTP GET /rest/v1/users?id=eq.42
    ↓
PostgREST introspects schema: SELECT * FROM information_schema.tables
    ↓
Schema knows: users table esiste, colonne id, name, email, etc.
    ↓
Parses query: ?id=eq.42 → WHERE id = 42
    ↓
Genera SQL: SELECT * FROM users WHERE id = 42
    ↓
Applica RLS policies (auth context from Authorization header)
    ↓
Esegui query
    ↓
Serializza result a JSON
    ↓
HTTP 200 + JSON response
```

**Cache Schema:**
PostgREST caches schema introspection result (~0.5-2s). Quando modifichi schema (CREATE TABLE, ALTER), il cache invalidates automaticamente.

### 3.2 Filtering Operators

PostgREST supporta operatori di filtering via query string:

```
Base URL: /rest/v1/users?<filter>

Operatori:
- eq.value              →  =
- neq.value            →  !=
- gt.value             →  >
- gte.value            →  >=
- lt.value             →  <
- lte.value            →  <=
- like.pattern         →  LIKE
- ilike.pattern        →  ILIKE (case-insensitive)
- in.(val1,val2,val3)  →  IN
- is.null              →  IS NULL
- is.true              →  IS TRUE
- is.false             →  IS FALSE
- contains.array       →  && (array overlap)
- contained.array      →  <@ (array contained)
- @.relation           →  @> (JSONB contains)
- ps.polygon           →  && (spatial overlap, PostGIS)
```

**Esempi:**
```
GET /rest/v1/posts?created_at=gte.2026-01-01&status=eq.published
→ SELECT * FROM posts WHERE created_at >= '2026-01-01' AND status = 'published'

GET /rest/v1/users?email=like.%@gmail.com
→ SELECT * FROM users WHERE email LIKE '%@gmail.com'

GET /rest/v1/comments?content=ilike.%postgres%
→ SELECT * FROM comments WHERE content ILIKE '%postgres%'

GET /rest/v1/documents?tags=contains.{python,database}
→ SELECT * FROM documents WHERE tags && ARRAY['python', 'database']

GET /rest/v1/config?settings=@.{"env":"production"}
→ SELECT * FROM config WHERE settings @> '{"env":"production"}'
```

### 3.3 Resource Embedding (Joins via Foreign Keys)

PostgREST auto-detects foreign keys e permette embedding:

```sql
CREATE TABLE authors (id SERIAL PRIMARY KEY, name TEXT);
CREATE TABLE books (
  id SERIAL PRIMARY KEY,
  title TEXT,
  author_id INT REFERENCES authors(id)
);
CREATE TABLE reviews (
  id SERIAL PRIMARY KEY,
  book_id INT REFERENCES books(id),
  rating INT,
  comment TEXT
);
```

```
GET /rest/v1/books?select=*,authors(name),reviews(rating,comment)
→ SELECT books.*, authors.name, reviews.rating, reviews.comment
  FROM books
  LEFT JOIN authors ON books.author_id = authors.id
  LEFT JOIN reviews ON reviews.book_id = books.id

Response JSON:
{
  "id": 1,
  "title": "ACID Explained",
  "author_id": 5,
  "authors": { "name": "Alice" },
  "reviews": [
    { "rating": 5, "comment": "Excellent" },
    { "rating": 4, "comment": "Good" }
  ]
}
```

**Embedding Depth:**
- Default limit: 20 colonne
- Configurable via `--db-max-rows` flag
- Circular references: PostgREST detects + breaks cycles automaticamente
- Anti-join via `is.null`:
  ```
  GET /rest/v1/books?reviews=is.null
  → Books senza alcuna review
  ```

### 3.4 Bulk Operations

```
INSERT multipli:
POST /rest/v1/users
Body: [
  { "name": "Alice", "email": "alice@example.com" },
  { "name": "Bob", "email": "bob@example.com" }
]

UPDATE multipli:
PATCH /rest/v1/users?role=eq.user
Body: { "role": "admin" }
→ Tutti gli users con role='user' ora hanno role='admin'

DELETE multipli:
DELETE /rest/v1/users?status=eq.inactive
→ Cancella tutti gli users con status='inactive'
```

### 3.5 Stored Procedures as API Endpoints

Qualsiasi PostgreSQL function diventa automaticamente REST endpoint:

```sql
CREATE FUNCTION add_numbers(a INT, b INT)
RETURNS INT AS $$
  SELECT a + b;
$$ LANGUAGE SQL;

CREATE FUNCTION create_team(p_name TEXT, p_creator_id UUID)
RETURNS TABLE (id UUID, name TEXT, created_at TIMESTAMP)
AS $$
  INSERT INTO teams (name, creator_id)
  VALUES (p_name, p_creator_id)
  RETURNING id, name, created_at;
$$ LANGUAGE SQL;
```

Accessibile via:
```
POST /rest/v1/rpc/add_numbers
Body: { "a": 5, "b": 3 }
Response: { "add_numbers": 8 }

POST /rest/v1/rpc/create_team
Body: { "p_name": "Engineering", "p_creator_id": "uuid..." }
Response: [{ "id": "uuid", "name": "Engineering", "created_at": "..." }]
```

### 3.6 Performance Characteristics

- **Latency tipico:** 10-50ms (DB query time)
- **Throughput:** 1,000-10,000 req/sec per container (dipende da query complexity)
- **Bottleneck comune:** RLS policies su tabelle grandi senza indici
- **Connection pooling:** PostgREST usa PgBouncer/Supavisor, connection reuse per efficiency
- **Caching:** HTTP caching via ETag headers (client-side can cache 304 Not Modified responses)

**Query Optimization:**
```
Lento:
GET /rest/v1/posts?select=*,comments(*)&comments.author_id=eq.42
→ FULL OUTER JOIN con filter su tabella embedded

Migliore:
GET /rest/v1/comments?author_id=eq.42&select=*,posts(id,title)
→ Start da comments, join con posts
```

---

## 4. Auth (GoTrue) — Architettura Autenticazione

### 4.1 GoTrue Architecture Overview

GoTrue è JWT-based auth service scritto in Go (fork di Netlify GoTrue). Non memorizza sessioni—ogni request include JWT nel header.

```
┌─────────────┐
│ Client App  │
└──────┬──────┘
       │ POST /auth/v1/signup
       │ { email, password }
       ↓
┌──────────────────────┐
│ GoTrue Service       │
│                      │
│ - Hash password      │
│ - Store in DB        │
│ - Generate JWT       │
│ - Return token       │
└────────┬─────────────┘
         │
         ↓
┌──────────────────────┐
│ PostgreSQL           │
│ auth.users table     │
│ (encrypted password) │
└──────────────────────┘

Subsequent requests:
Client includes: Authorization: Bearer <JWT>
↓
PostgREST extracts JWT, calls auth.uid() from claims
↓
RLS policies use auth.uid() to filter rows
```

### 4.2 JWT Structure e Claims

JWT standard Supabase format:
```json
{
  "aud": "authenticated",
  "exp": 1750000000,
  "iat": 1740000000,
  "iss": "https://project-id.supabase.co/auth/v1",
  "sub": "user-uuid-here",
  "email": "user@example.com",
  "email_confirmed_at": "2026-01-15T10:30:00Z",
  "phone": "+1234567890",
  "phone_confirmed_at": null,
  "app_metadata": {
    "provider": "email",
    "providers": ["email", "google"]
  },
  "user_metadata": {
    "full_name": "Alice Smith",
    "avatar_url": "https://example.com/avatar.jpg"
  },
  "role": "authenticated"
}
```

**Custom Claims via Auth Hooks:**
```javascript
// Edge Function che modifica JWT
export default async (req, context) => {
  const { user } = context;

  // Fetch user's organization
  const { data } = await supabase
    .from('organization_members')
    .select('org_id')
    .eq('user_id', user.id)
    .single();

  return {
    claims: {
      org_id: data.org_id,
      org_role: data.role  // 'admin', 'member', 'viewer'
    }
  };
};
```

Poi RLS policies usano:
```sql
CREATE POLICY "..." USING (
  org_id = auth.jwt() ->> 'org_id'::uuid
);
```

### 4.3 Token Refresh Flow

```
Initial Login:
POST /auth/v1/token
{ "email": "user@example.com", "password": "secret" }
↓ Response:
{
  "access_token": "eyJhbGc...",      // expires in 1 hour
  "refresh_token": "your-refresh-...", // expires in 7 days
  "user": { ... }
}

After access_token expires:
POST /auth/v1/token
{
  "grant_type": "refresh_token",
  "refresh_token": "your-refresh-..."
}
↓ Response:
{
  "access_token": "eyJhbGc...",  // new token
  "refresh_token": "your-refresh-...",  // new refresh token (rotated)
  "user": { ... }
}
```

**Best Practice:** Client libraries (supabase-js) automaticamente refreshano tokens via interceptor.

### 4.4 OAuth Providers

Supabase supporta:
- Google, GitHub, Microsoft, Apple
- Twitch, Discord, GitLab, BitBucket, Slack
- Okta, Auth0 (SAML)
- Custom SAML providers

```javascript
// Client-side OAuth flow
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: 'google',
  options: {
    redirectTo: 'https://myapp.com/auth/callback'
  }
});

// Redirect user to Google consent screen
// After consent, Google redirects to callback URL con authorization code
// GoTrue exchanges code per access_token
// GoTrue issues JWT con google provider info
// App stores JWT, user autenticato
```

### 4.5 Multi-Factor Authentication (MFA)

```sql
-- MFA metadata stored in auth.users
ALTER TABLE auth.users ADD COLUMN totp_enabled BOOLEAN DEFAULT FALSE;

-- User enrolls in TOTP
POST /auth/v1/factors
{
  "friendly_name": "Authenticator App",
  "factor_type": "totp"
}
→ Returns QR code per scan in Google Authenticator / Authy

-- User verifies TOTP
POST /auth/v1/factors/{factor_id}/verify
{
  "code": "123456"  -- 6-digit code from authenticator app
}
→ Returns challenge token

-- Login con MFA
POST /auth/v1/token
{
  "email": "user@example.com",
  "password": "secret",
  "gotrue_metadata": { "challenge_id": "..." }  // if MFA required
}
→ Response: { "requires_mfa": true, "challenge_id": "..." }

-- Complete MFA
POST /auth/v1/factors/{factor_id}/challenge
{
  "challenge_id": "...",
  "code": "123456"  -- TOTP code from app
}
→ Returns access_token, user logged in
```

### 4.6 Hook System

Supabase Auth triggers webhooks su events specifici:

```
POST /webhook/custom-auth
Event: signup
Payload:
{
  "type": "user.created",
  "data": {
    "id": "user-uuid",
    "email": "newuser@example.com",
    "created_at": "2026-04-01T10:00:00Z"
  }
}

Events:
- user.created
- user.updated
- user.deleted
- user.signed_in
- user.password_changed
```

Usi comuni:
- Creare record automatico in public `profiles` table
- Inviare welcome email
- Log auth events per audit trail
- Sync con external auth systems

---

## 5. Realtime — WebSocket Server per Live Updates

### 5.1 Realtime Architecture (Elixir/Phoenix)

Realtime è scritto in Elixir (compiles a Erlang bytecode). Phoenix framework permette milioni di concurrent WebSocket connections su singola istanza.

```
┌──────────────┐
│ Client       │
│ WebSocket    │
└──────┬───────┘
       │ ws://project.supabase.co/realtime/v1
       │ (Handshake upgrade)
       ↓
┌──────────────────────────────────────┐
│ Realtime Server (Elixir/Phoenix)     │
│                                      │
│ - Manages WebSocket connections      │
│ - Maintains in-memory user presence  │
│ - Listens to PostgreSQL replication  │
│ - Broadcasts DB changes              │
│ - Handles PubSub (channels)          │
└────────┬─────────────────────────────┘
         │
         ├─→ PostgreSQL (Logical Replication Stream)
         │   WAL2JSON output plugin → WALRUS function
         │
         └─→ Redis (for multi-instance sync)
```

**Lightweight Erlang processes:** Erlang non usa OS threads—ogni connection è lightweight Erlang process. Quindi 1 million concurrent connections = 1 million Erlang processes (feasible su singola server).

### 5.2 Three Channel Types

#### **1. PostgreSQL Changes (Database CDC)**

```javascript
supabase
  .channel('db-changes')
  .on('postgres_changes',
    { event: '*', schema: 'public', table: 'posts' },
    (payload) => {
      console.log('Change received!', payload);
      // payload.eventType: 'INSERT', 'UPDATE', 'DELETE'
      // payload.new: new row data
      // payload.old: old row data (for UPDATE/DELETE)
    }
  )
  .subscribe();
```

Come funziona:
1. Client sottoscrive `channel('db-changes')`
2. Realtime WebSocket connection established
3. Realtime starts listening to PostgreSQL logical replication stream
4. PostgreSQL publishes change via WAL (Write Ahead Log)
5. WAL2JSON plugin converte WAL entry a JSON
6. Realtime calls WALRUS function per autorizzazione (RLS checks)
7. Se passato, broadcast JSON a tutti i client sottoscritti
8. Client riceve in real-time, aggiorna UI

**RLS Integration:** Realtime RLS verification è server-side, quindi client non ricevono rows a cui non hanno accesso.

#### **2. Broadcast (Pub/Sub)**

```javascript
// Sender
supabase.channel('live-notifications')
  .send('broadcast', {
    event: 'new-comment',
    payload: { comment_id: 123, text: 'Great post!' }
  });

// Listener
supabase.channel('live-notifications')
  .on('broadcast', { event: 'new-comment' }, (payload) => {
    console.log('New comment:', payload.payload);
  })
  .subscribe();
```

**Low-latency messaging** (non passa per database). Usado per:
- Live notifications
- Typing indicators
- Collaborative cursors
- Live reaction counts

#### **3. Presence (User Presence Tracking)**

```javascript
// User joins channel + tracks presence
const status = supabase.channel('users-online', {
  config: { broadcast: { self: true } }
})
  .on('presence', { event: 'sync' }, () => {
    const state = status.presenceState();
    console.log('Users online:', state);
    // state[user_id] = { online_at: timestamp, user_id, ... }
  })
  .subscribe(async (status) => {
    if (status === 'SUBSCRIBED') {
      await status.track({
        user_id: auth.user().id,
        user_name: auth.user().email
      });
    }
  });
```

Usi comuni:
- Mostra quanti utenti online
- Collaborative editing (show cursors di altri utenti)
- Live user activity feed

### 5.3 CDC Deep Dive (Change Data Capture)

PostgreSQL logical replication è il fondamento di Realtime:

```sql
-- PostgreSQL server configuration per CDC
wal_level = logical        -- Enable logical decoding

-- Realtime crea replication slot
SELECT * FROM pg_create_logical_replication_slot(
  'supabase_realtime_slot',
  'wal2json'
);

-- Whenever a change happens (INSERT/UPDATE/DELETE)
-- PostgreSQL writes to WAL
-- WAL2JSON plugin decodes WAL → JSON:
{
  "action": "I",  -- Insert
  "schema": "public",
  "table": "posts",
  "columns": ["id", "title", "user_id"],
  "columnvalues": [42, "New Post", "user-uuid"],
  "timestamp": "2026-04-01T10:30:00Z"
}

-- WALRUS PostgreSQL function decides if broadcast allowed
-- (checks RLS policies)
-- If allowed, Realtime broadcasts JSON to subscribed WebSocket clients
```

**Importantly:** Changes can originate from **anywhere**—API, direct DB access, pg_net webhooks—Realtime still broadcasts because it listens to WAL, not application code.

### 5.4 Performance at Scale

- **Concurrent connections per instance:** 100,000+
- **Messages per second:** 10,000+ (depends on message size + server specs)
- **Latency:** 50-200ms (typical, network + processing)
- **Memory per connection:** ~10-50KB (Erlang processes are lightweight)

**Scaling:** Multi-instance Realtime deployments use Redis for cross-instance sync:
```
Instance A receives PostgreSQL change
↓
Publishes to Redis channel "db-changes-posts"
↓
Instance B, C, D all subscribe to Redis, receive change
↓
Each instance broadcasts to its own WebSocket connections
```

---

## 6. Storage — S3-Compatible Object Storage

### 6.1 Storage Architecture

```
┌──────────────┐
│ Client Upload│
└──────┬───────┘
       │ PUT /storage/v1/b/my-bucket/o/file.txt
       ↓
┌──────────────────────┐      ┌──────────────────┐
│ Storage Service      │──RLS→│ PostgreSQL       │
│ (TypeScript/Node)    │      │ storage.objects  │
│                      │      │ (metadata)       │
│ - Validates JWT      │      └──────────────────┘
│ - Applies RLS checks │
│ - Routes to S3       │
│ - Transforms images  │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────────┐
│ S3 Compatible Storage    │
│ (File bytes)             │
└──────────────────────────┘
```

**Key Distinction:** File metadata (permissions, sizes, timestamps) in PostgreSQL, actual bytes in S3-compatible backend.

### 6.2 Bucket Policies & RLS

```sql
-- Storage RLS works on storage.objects table
ALTER TABLE storage.objects ENABLE ROW LEVEL SECURITY;

-- Example: Users can only access their own uploads
CREATE POLICY "Users can access own uploads" ON storage.objects
  FOR SELECT
  USING (
    bucket_id = 'user-uploads'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );

-- File path structure: <user_id>/<filename>
-- E.g., "550e8400-e29b-41d4-a716-446655440000/avatar.jpg"

CREATE POLICY "Users can upload to own folder" ON storage.objects
  FOR INSERT
  WITH CHECK (
    bucket_id = 'user-uploads'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );
```

**Helper functions:**
```sql
-- Extract folder from path
SELECT storage.foldername('path/to/file.txt')
→ '["path", "to"]'

-- Check authorization
SELECT storage.can_access_bucket('user-uploads', 'public', 'authenticated');
```

### 6.3 Image Transformations

```
GET /storage/v1/b/avatars/o/user-123/avatar.jpg?
  transform=image&width=200&height=200&quality=80

→ Storage service:
  1. Fetch avatar.jpg from S3
  2. Resize to 200x200
  3. Compress to quality 80
  4. Cache result (CDN)
  5. Return optimized image
```

**Supported transforms:**
- `width`, `height`: dimensions
- `quality`: 0-100
- `format`: webp, png, jpg
- `resize`: cover, contain, fill, inside, outside

### 6.4 Signed URLs

```javascript
// Generate time-limited download link
const { data } = await supabase.storage
  .from('user-uploads')
  .createSignedUrl('550e8400/avatar.jpg', 3600);  // expires in 1 hour

// URL:
// https://project.supabase.co/storage/v1/b/user-uploads/o/550e8400%2Favatar.jpg?token=eyJhbGc...

// Anyone with URL can download, even if not logged in
// Token expires after 3600 seconds
```

### 6.5 Resumable Uploads (TUS Protocol)

```
POST /storage/v1/b/bucket-name/upload?
  uploadType=resumable&name=large-video.mp4

→ Response: Location: /upload?upload_id=abc123

PUT /upload?upload_id=abc123 Content-Range: bytes 0-1023999/10485760000
[10MB chunk]

→ Response: 308 Resume Incomplete, Range: bytes=0-1023999

... continue uploading chunks ...

PUT /upload?upload_id=abc123 Content-Range: bytes 9475309440-10485759999/10485760000
[final chunk]

→ Response: 200 OK, file complete
```

TUS consente resume se connection drops—client può riprendere dal byte dove era interrotto.

---

## 7. Edge Functions (Deno Runtime)

### 7.1 Runtime Architecture

Edge Functions runno su Deno (V8 engine, TypeScript-first):

```
┌──────────────┐
│ Client HTTPS │
│ POST /fn/    │
└──────┬───────┘
       ↓
┌──────────────────────────────┐
│ Kong API Gateway             │
│ Routes /functions/* to Deno   │
└──────┬───────────────────────┘
       ↓
┌───────────────────────────────┐
│ Deno Runtime (Distributed)    │
│ - V8 JavaScript engine         │
│ - TypeScript support           │
│ - Isolated context             │
│ - 2s CPU time limit            │
│ - 512MB memory limit           │
│ - Cold start: ~100-500ms       │
│ - Warm start: ~10-50ms         │
└──────┬────────────────────────┘
       │
       ├→ PostgreSQL access (via Supabase client)
       ├→ HTTP calls (fetch API)
       ├→ Secrets (via Deno.env.get)
       └→ File system (read-only /tmp)
```

### 7.2 Function Code Example

```typescript
// supabase/functions/hello/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

serve(async (req) => {
  const authHeader = req.headers.get("Authorization");

  if (!authHeader) {
    return new Response(
      JSON.stringify({ error: "Missing authorization" }),
      { status: 401, headers: { "Content-Type": "application/json" } }
    );
  }

  // Initialize Supabase client (uses SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY)
  const supabase = createClient(
    Deno.env.get("SUPABASE_URL")!,
    Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!
  );

  // Call PostgreSQL function or query
  const { data, error } = await supabase
    .from("users")
    .select("id, email")
    .limit(10);

  if (error) {
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 500, headers: { "Content-Type": "application/json" } }
    );
  }

  return new Response(
    JSON.stringify({ users: data }),
    { headers: { "Content-Type": "application/json" } }
  );
});
```

**Deploy:**
```bash
supabase functions deploy hello
```

### 7.3 Cold Starts & Performance

**Cold start:** First invocation après deployment, Deno runtime spins up
- ~100-500ms (depends on dependencies, network latency)

**Warm start:** Subsequent invocations within minutes
- ~10-50ms (function already loaded)

**Optimization:**
```typescript
// Good: Minimal dependencies
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";

// Not great: Heavy npm packages bundled
import lodash from "npm:lodash@4.17.21";  // Large package

// ESZip bundling: CLI bundles function + deps into efficient format
// supabase functions deploy --bundle-only
```

### 7.4 Secrets Management

```typescript
// Access secrets via environment variables
const API_KEY = Deno.env.get("EXTERNAL_API_KEY");
const DB_PASSWORD = Deno.env.get("DATABASE_PASSWORD");

// Define in .env.local (dev) or Supabase Secrets dashboard (prod)
serve(async (req) => {
  const response = await fetch("https://api.external.com/data", {
    headers: { "Authorization": `Bearer ${API_KEY}` }
  });
  // ...
});
```

**Best Practice:** Service role key Supabase sa allowed dentro functions. Use sparingly—prefer user JWT + RLS.

### 7.5 Limitations

| Limit | Value |
|-------|-------|
| CPU time | 2 seconds |
| Request idle timeout | 150 seconds |
| Memory | 512MB runtime |
| Bundle size | 20MB (after compression) |
| Max output size | 6MB |
| Concurrent invocations | Limited per plan |
| Outbound ports | 80, 443 only (25, 587 blocked) |

### 7.6 Database Access from Functions

```typescript
// Two approaches:

// 1. Use service_role key (full database access, bypasses RLS)
const supabase = createClient(
  Deno.env.get("SUPABASE_URL")!,
  Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!
);

// 2. Use user JWT (respects RLS)
const authHeader = req.headers.get("Authorization");
const supabase = createClient(
  Deno.env.get("SUPABASE_URL")!,
  Deno.env.get("SUPABASE_ANON_KEY")!,
  {
    global: {
      headers: { Authorization: authHeader }
    }
  }
);
```

---

## 8. Database Design Patterns

### 8.1 Soft Deletes

Instead of `DELETE`, mark rows as deleted:

```sql
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  deleted_at TIMESTAMP DEFAULT NULL
);

-- Soft delete
UPDATE posts SET deleted_at = NOW() WHERE id = 42;

-- Hard delete (rare)
DELETE FROM posts WHERE id = 42;

-- Query active posts only
SELECT * FROM posts WHERE deleted_at IS NULL;

-- Optional: View hiding deleted rows
CREATE VIEW posts_active AS
  SELECT * FROM posts WHERE deleted_at IS NULL;
```

**Pros:** Recoverability, audit trail, referential integrity (can soft-delete then recover)

**Cons:** Every query must filter `deleted_at IS NULL`, slower if many deleted rows

### 8.2 Audit Trails (Trigger-based)

```sql
CREATE TABLE audit_log (
  id BIGSERIAL PRIMARY KEY,
  table_name TEXT NOT NULL,
  record_id UUID NOT NULL,
  operation TEXT NOT NULL,  -- INSERT, UPDATE, DELETE
  old_data JSONB,
  new_data JSONB,
  changed_at TIMESTAMP DEFAULT NOW(),
  changed_by UUID  -- from auth.uid()
);

CREATE OR REPLACE FUNCTION audit_changes()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO audit_log (
    table_name, record_id, operation, old_data, new_data, changed_by
  ) VALUES (
    TG_TABLE_NAME,
    (CASE WHEN TG_OP = 'DELETE' THEN OLD.id ELSE NEW.id END)::UUID,
    TG_OP,
    CASE WHEN TG_OP = 'DELETE' THEN row_to_json(OLD) ELSE NULL END,
    CASE WHEN TG_OP IN ('INSERT', 'UPDATE') THEN row_to_json(NEW) ELSE NULL END,
    auth.uid()
  );
  RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Attach to all tables needing audit
CREATE TRIGGER users_audit AFTER INSERT OR UPDATE OR DELETE ON users
  FOR EACH ROW EXECUTE FUNCTION audit_changes();
```

### 8.3 Multi-Tenancy: RLS-based vs Schema-based

#### **RLS-based (Shared Tables + Tenant ID)**

```sql
-- Single users table, tenant_id column
CREATE TABLE organizations (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL
);

CREATE TABLE organization_members (
  id SERIAL,
  org_id UUID NOT NULL REFERENCES organizations(id),
  user_id UUID NOT NULL REFERENCES auth.users(id),
  role TEXT NOT NULL,  -- admin, member, viewer
  UNIQUE(org_id, user_id)
);

CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  org_id UUID NOT NULL REFERENCES organizations(id),
  title TEXT NOT NULL,
  content TEXT
);

ALTER TABLE posts ENABLE ROW LEVEL SECURITY;

-- User can see posts from organizations they belong to
CREATE POLICY "Read org posts" ON posts
  FOR SELECT
  USING (
    org_id IN (
      SELECT org_id FROM organization_members
      WHERE user_id = auth.uid()
    )
  );

-- User can create posts only in owned orgs (if admin)
CREATE POLICY "Create org posts" ON posts
  FOR INSERT
  WITH CHECK (
    org_id IN (
      SELECT org_id FROM organization_members
      WHERE user_id = auth.uid() AND role = 'admin'
    )
  );
```

**Pros:** Simpler admin operations, cross-tenant analytics, single table backup

**Cons:** RLS overhead per query, potential data leaks if policy buggy, larger tables

#### **Schema-based (Per-Tenant Schemas)**

```sql
-- Separate schema per tenant
CREATE SCHEMA tenant_550e8400;  -- UUID of tenant

-- All tables under tenant schema
CREATE TABLE tenant_550e8400.posts (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL
);

CREATE TABLE tenant_550e8400.users (
  id UUID PRIMARY KEY,
  email TEXT
);

-- Connection routing logic in application
-- When user from org 550e8400 connects:
SET search_path = 'tenant_550e8400, public, pg_catalog';

-- Now SELECT * FROM posts = SELECT * FROM tenant_550e8400.posts
```

**Pros:** Strong isolation, simpler queries (no RLS), per-tenant backups, compliance easier

**Cons:** Schema management overhead, many schemas = more connections, harder analytics

### 8.4 Hierarchical Data (ltree / Recursive CTEs)

```sql
-- ltree approach (simpler, faster)
CREATE TABLE categories (
  id SERIAL PRIMARY KEY,
  path ltree UNIQUE,
  name TEXT
);

INSERT INTO categories VALUES
  (1, '1', 'Electronics'),
  (2, '1.1', 'Phones'),
  (3, '1.1.1', 'Smartphones'),
  (4, '1.2', 'Laptops');

-- Query all descendants
SELECT * FROM categories WHERE path <@ '1.1'::ltree;
→ Phones, Smartphones

-- Query all ancestors
SELECT * FROM categories WHERE '1.1.1'::ltree <@ path;
→ Electronics, Phones

-- Recursive CTE approach (for non-ltree, e.g., parent_id column)
WITH RECURSIVE category_tree AS (
  SELECT id, name, parent_id, 1 AS depth
  FROM categories
  WHERE parent_id IS NULL

  UNION ALL

  SELECT c.id, c.name, c.parent_id, ct.depth + 1
  FROM categories c
  JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree WHERE depth <= 3;
```

### 8.5 Full-Text Search

```sql
CREATE TABLE documents (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  search_vector tsvector GENERATED ALWAYS AS (
    setweight(to_tsvector('english', coalesce(title, '')), 'A') ||
    setweight(to_tsvector('english', coalesce(content, '')), 'B')
  ) STORED
);

CREATE INDEX ON documents USING GIN (search_vector);

-- Query
SELECT id, title, ts_rank(search_vector, query) AS rank
FROM documents, plainto_tsquery('english', 'postgres database') AS query
WHERE search_vector @@ query
ORDER BY rank DESC;
```

### 8.6 JSONB Patterns

```sql
-- Store semi-structured data without schema
CREATE TABLE user_settings (
  user_id UUID PRIMARY KEY,
  preferences JSONB DEFAULT '{}'
);

INSERT INTO user_settings VALUES (
  'user-uuid',
  '{
    "theme": "dark",
    "notifications": {"email": true, "sms": false},
    "language": "en"
  }'::JSONB
);

-- Query JSONB
SELECT preferences -> 'notifications' ->> 'email' AS email_notifications
FROM user_settings
WHERE user_id = 'user-uuid';

-- Index JSONB (GIN)
CREATE INDEX ON user_settings USING GIN (preferences);

-- Update JSONB
UPDATE user_settings
SET preferences = preferences || '{"theme": "light"}'::JSONB
WHERE user_id = 'user-uuid';

-- Delete key
UPDATE user_settings
SET preferences = preferences - 'language'
WHERE user_id = 'user-uuid';
```

---

## 9. Performance e Scalabilità

### 9.1 Connection Limits per Plan

| Plan | Max Connections | Max Database Size | Compute |
|------|---|---|---|
| Free | 20 | 500 MB | Micro (pauses after 7 days inactivity) |
| Pro | 100 | 8 GB | Micro to Small (scalable) |
| Team | 100 | 50 GB+ | Configurable |
| Enterprise | 500+ | Unlimited | Dedicated, custom |

**Compute Instances:**
- Micro: 0.5 GB RAM, 0.1 CPU share ($0/month, included in Pro $25)
- Small: 2 GB RAM, 0.25 CPU ($10/month)
- Medium: 4 GB RAM, 0.5 CPU ($25/month)
- Large: 8 GB RAM, 1 CPU ($50/month)
- Larger: 16 GB RAM, 2 CPU ($100/month)

### 9.2 Read Replicas

```sql
-- Create read replica (Pro+ only)
-- Via Supabase Dashboard or:
SELECT create_replica('replica-region-us-east-1');

-- Replicas are read-only, cannot write
-- Use for:
-- - Geographical distribution (lower latency)
-- - Heavy read workloads
-- - Report generation without impacting main DB
-- - Primary region failover backup
```

### 9.3 Database Branching

```bash
# Create development branch from production
supabase branches create --parent prod

# Deploy changes on branch, test, then merge
supabase branches merge my-branch --parent prod
```

Branche = separate database instance con snapshot da parent schema + data.

### 9.4 N+1 Query Prevention

**Bad:**
```javascript
// N+1: fetch users, then for each user fetch posts
const users = await supabase.from('users').select();
for (const user of users) {
  const posts = await supabase
    .from('posts')
    .select()
    .eq('user_id', user.id);
  user.posts = posts;
}
// 1 + N queries
```

**Good (use embedding):**
```javascript
// 1 query: fetch users + posts in single call
const { data } = await supabase
  .from('users')
  .select('*, posts(id, title)')  // embedding via foreign key
  .limit(10);

// data[0] = { id: ..., name: ..., posts: [...] }
```

### 9.5 Query Optimization

```sql
-- EXPLAIN ANALYZE to find slow queries
EXPLAIN ANALYZE
SELECT u.id, u.email, COUNT(p.id) AS post_count
FROM users u
LEFT JOIN posts p ON u.id = p.user_id
WHERE u.created_at > NOW() - INTERVAL '30 days'
GROUP BY u.id
ORDER BY post_count DESC
LIMIT 10;

-- Check: does it use indexes?
-- Check: are there full table scans?
-- Check: is sort expensive?

-- Create missing indexes if needed
CREATE INDEX ON users(created_at);
CREATE INDEX ON posts(user_id, created_at);
```

---

## 10. Sicurezza Avanzata

### 10.1 Advanced RLS Patterns

**Tenant-scoped data + role-based access:**
```sql
CREATE TABLE projects (
  id UUID PRIMARY KEY,
  org_id UUID NOT NULL,
  name TEXT,
  owner_id UUID
);

ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- Admin can see all org projects
-- Owner can see all their projects
-- Members can see only projects they collaborate on
CREATE POLICY "Complex project access" ON projects
  FOR SELECT
  USING (
    -- User is admin of org
    org_id IN (
      SELECT org_id FROM organization_members
      WHERE user_id = auth.uid() AND role = 'admin'
    )
    OR
    -- User is project owner
    owner_id = auth.uid()
    OR
    -- User is project collaborator
    id IN (
      SELECT project_id FROM project_members
      WHERE user_id = auth.uid()
    )
  );
```

### 10.2 Service Role vs Anon Key

- **anon_key:** Public, allows unauthorized access, RLS policies always applied
  ```
  GET /rest/v1/posts  // uses anon_key if no auth header
  // RLS policies applied, only authorized rows returned
  ```

- **service_role_key:** Private, admin-level, bypasses RLS
  ```
  // In Edge Function, backend:
  const supabase = createClient(url, SERVICE_ROLE_KEY);
  // Can access all data, RLS ignored
  ```

**Best Practice:** Use anon_key in public clients, service_role_key only in secure backend.

### 10.3 Network Restrictions

Supabase supports IP whitelisting (Team+ plans):

```
Dashboard → Database Settings → Network Restrictions
Add allowed IPs: 203.0.113.5, 198.51.100.0/24
```

Direct database connections from unlisted IPs rejected.

### 10.4 Encryption & Vault

```sql
-- Supabase Vault: encrypted secrets in database
CREATE TABLE secrets (
  id SERIAL PRIMARY KEY,
  secret BYTEA,  -- encrypted
  description TEXT
);

-- Store encrypted secret
INSERT INTO vault.secrets (name, secret)
VALUES ('stripe_api_key', 'sk_live_...');

-- Retrieve secret (must have permission)
SELECT decrypted_secret FROM vault.secrets
WHERE name = 'stripe_api_key';

-- RLS can restrict who sees secrets
ALTER TABLE vault.secrets ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Only admins see secrets" ON vault.secrets
  FOR SELECT
  USING (auth.jwt() ->> 'role' = 'admin');
```

### 10.5 Audit Logging

PostgreSQL's pgaudit extension:

```sql
CREATE EXTENSION pgaudit;

-- Log all DDL
ALTER SYSTEM SET pgaudit.log = 'DDL';
SELECT pg_reload_conf();

-- Query audit log
SELECT auditlog FROM pg_audit_log WHERE audit_type = 'TABLE';
```

---

## 11. Integrazioni

### 11.1 Client Libraries

| Linguaggio | Library | Features |
|---|---|---|
| JavaScript/TS | @supabase/supabase-js | REST, Realtime, Auth, Storage |
| Python | supabase-py | REST, Async support |
| Dart | supabase-flutter | Flutter, mobile optimized |
| Swift | supabase-swift | iOS/macOS |
| Kotlin | supabase-kt | Android |
| C# | supabase-csharp | .NET, Unity |

### 11.2 REST vs GraphQL vs Direct Connection

```javascript
// REST (via PostgREST)
const { data } = await supabase
  .from('posts')
  .select('id, title, author:user_id(email)')
  .eq('status', 'published');

// GraphQL (via pg_graphql)
const { data } = await supabase.graphql(`
  query {
    postCollection {
      edges {
        node { id title author { email } }
      }
    }
  }
`);

// Direct SQL (not recommended, but possible)
const { data } = await supabase.rpc('custom_function', {
  param1: 'value'
});
```

### 11.3 Webhooks via pg_net

```sql
-- PostgreSQL function triggering webhook
CREATE OR REPLACE FUNCTION notify_on_new_user()
RETURNS TRIGGER AS $$
BEGIN
  SELECT net.http_post(
    'https://webhook.example.com/new-user',
    jsonb_build_object(
      'user_id', NEW.id,
      'email', NEW.email,
      'created_at', NEW.created_at
    )
  );
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER user_webhook AFTER INSERT ON auth.users
  FOR EACH ROW EXECUTE FUNCTION notify_on_new_user();
```

### 11.4 Cron Jobs (pg_cron)

```sql
CREATE EXTENSION pg_cron;

-- Send weekly digest emails every Monday 9 AM
SELECT cron.schedule('send-digest', '0 9 * * 1', $$
  SELECT send_weekly_digest();
$$);

-- Clean up old records every day at 2 AM
SELECT cron.schedule('cleanup', '0 2 * * *', $$
  DELETE FROM sessions WHERE expires_at < NOW();
$$);

-- Unschedule
SELECT cron.unschedule('cleanup');
```

---

## 12. Pricing e Limiti

### 12.1 Pricing Model

**Free Plan:**
- 2 projects
- 500 MB database storage
- 5 GB egress
- 50,000 MAU (monthly active users)
- 1 GB file storage
- 500,000 edge function invocations/month
- Projects pause after 7 days inactivity

**Pro Plan ($25/month):**
- Unlimited projects
- 8 GB database storage + $0.125/GB overage
- $10 compute credit (≈ 1 Micro instance)
- 100,000 MAU + $0.00325/MAU overage
- 100 GB file storage + $0.021/GB overage
- 2M edge function invocations/month
- $0.09/GB egress overage

**Team Plan ($25/month per project, usage-based):**
- Higher MAU, priority support, SSO, audit logs

**Enterprise:**
- Custom pricing, dedicated infrastructure, SLA

### 12.2 Hard Limits

| Resource | Limit |
|---|---|
| Database size | 4 TB (managed), unlimited (self-hosted) |
| Connection pool size | 100-500 depending on compute |
| Single query size | 6 MB result |
| API request payload | 6 MB |
| Row count per query | Unlimited (but practical ~100k rows) |
| Column count per table | 1,664 |
| Table count per database | Unlimited (practical: 10,000+) |

### 12.3 Soft Limits & Rate Limiting

- Realtime connections: 100,000 per-project
- API requests: No per-second limit (but abuse throttled)
- Concurrent database connections: Pool-dependent
- Edge function execution: 2s timeout, 512MB memory

---

## 13. Self-Hosting

### 13.1 Docker Compose Setup

```bash
# Clone Supabase repo
git clone https://github.com/supabase/supabase.git
cd supabase/docker

# Configure .env with secrets
cp .env.example .env
# Edit .env: set POSTGRES_PASSWORD, JWT_SECRET, etc.

# Start all services
docker-compose up -d

# Available on localhost:
# - API: http://localhost:3000
# - Auth: http://localhost:9999
# - Realtime: ws://localhost:8000
# - Storage: http://localhost:5000
# - PostgreSQL: localhost:5432
```

### 13.2 Required Services

```yaml
services:
  postgres:          # Core database
  kong:              # API gateway
  auth:              # GoTrue
  rest:              # PostgREST
  realtime:          # Realtime server
  storage:           # Object storage (minio or S3)
  edge-runtime:      # Deno functions
  pg_meta:           # Schema introspection
  logflare:          # Analytics (optional)
  imgproxy:          # Image transformation (optional)
  vector:            # Vector storage (optional)
```

### 13.3 Configuration

Key environment variables:
```bash
POSTGRES_PASSWORD=secretpassword
JWT_SECRET=your-jwt-secret-min-32-chars
ANON_KEY=generated-jwt-token
SERVICE_ROLE_KEY=generated-service-token
SITE_URL=https://yourdomain.com
ADDITIONAL_REDIRECT_URLS=...

# Storage backend
STORAGE_BACKEND=s3  # or local, gcs, azure
AWS_S3_BUCKET=mybucket
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...

# SMTP (for emails)
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=...
SMTP_PASS=...
```

### 13.4 Maintenance Responsibilities

Self-hosting requires:
- PostgreSQL backup strategy (pg_dump, WAL archiving)
- SSL certificate renewal (Let's Encrypt)
- Security patching (Supabase updates monthly)
- Monitoring & alerting (Prometheus, Grafana)
- Log aggregation (ELK stack, Loki)
- Disaster recovery plan
- Capacity planning

---

## 14. Confronto con Alternative

| Aspetto | Supabase | Firebase | PlanetScale | Neon | Hasura |
|---------|----------|----------|---|---|---|
| **Database** | PostgreSQL | Firestore/Realtime DB | MySQL | PostgreSQL | PostgreSQL + GraphQL |
| **Query Language** | SQL/REST/GraphQL | SDK-specific | SQL | SQL | GraphQL |
| **Type System** | Rich (enums, ranges, JSON) | Basic | Rich | Rich | Rich |
| **RLS** | Built-in, policy-driven | Firestore rules | No | Via policies | Via GraphQL |
| **Transazioni** | ACID multi-row | Per-document | ACID | ACID | ACID |
| **Self-hosting** | Docker Compose | No | No | No | Docker + orchestration |
| **Pricing** | Usage-based | Usage-based (generous free) | Usage-based | Usage-based | Varies |
| **Lock-in** | Low (standard SQL) | High (vendor-specific) | Medium | Low | Low-Medium |

---

## 15. Anti-Pattern e Errori Comuni

| Anti-Pattern | Problema | Soluzione |
|---|---|---|
| Oversized JWT claims | Large JWT payload = more bandwidth | Keep JWT minimal, store extra data in DB |
| Missing RLS indexes | Full table scan per query | `CREATE INDEX` su colonne RLS USING/WITH CHECK |
| Storing secrets in env | Secrets in version control | Use Vault, environment-specific .env.local |
| No pagination | OOM on large result sets | `LIMIT` + `OFFSET` or cursor pagination |
| SELECT * in RLS | Unnecessary columns fetched | Specify columns, let PostgREST optimize |
| Recursive RLS queries | Expensive nested subqueries | Use security definer functions, flatten logic |
| No audit trail | Can't trace changes | Implement trigger-based audit log |
| Direct DB access without RLS | Bypasses client-side checks | Always use RLS, never bypass in production |
| Soft deletes without filtering | Queries return "deleted" data | Always add `WHERE deleted_at IS NULL` |
| Single compute instance | No failover | Use read replicas, configure backup |

---

## 16. Risorse Ufficiali

| Risorsa | URL |
|---------|-----|
| Documentazione Ufficiale | https://supabase.com/docs |
| Architettura | https://supabase.com/docs/guides/getting-started/architecture |
| Auth Docs | https://supabase.com/docs/guides/auth/architecture |
| Realtime Docs | https://supabase.com/docs/guides/realtime/architecture |
| Storage Docs | https://supabase.com/docs/guides/storage |
| Edge Functions | https://supabase.com/docs/guides/functions |
| Self-Hosting | https://supabase.com/docs/guides/self-hosting |
| RLS Best Practices | https://supabase.com/docs/guides/troubleshooting/rls-performance-and-best-practices-Z5Jjwv |
| GitHub Repo | https://github.com/supabase/supabase |
| Community Discord | https://discord.supabase.io |
| Pricing | https://supabase.com/pricing |

---

## Changelog

**v1.0** (Aprile 2026)
- Documentazione architettura iniziale coprendo PostgreSQL, PostgREST, GoTrue, Realtime, Storage, Edge Functions
- Sezioni su design patterns, performance, sicurezza avanzata
- Confronto con alternative (Firebase, PlanetScale, Neon, Hasura)
- Self-hosting configuration e maintenance guide
- 1000+ righe contenuto tecnico

---

**Note di Chiusura**

Questa documentazione copre architettura Supabase a livello CTO. Non è guida per principianti—presuppone familiarità con PostgreSQL, REST APIs, autenticazione, e concetti database.

Supabase è "The Postgres Development Platform" perché espone il potere completo di PostgreSQL tramite interfacce moderne (REST, GraphQL, WebSocket, Edge Functions) senza astrazione vendor-specific. La sicurezza è enforced a livello database via RLS, non applicazione. Scaling è percepito come seamless—stessa API, stesso schema, ma con compute instances più grandi.

Per domande tecniche approfondite, consultare repository ufficiale GitHub e community Discord.