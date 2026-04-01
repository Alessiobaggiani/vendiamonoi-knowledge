# Supabase — Documentazione Tecnica Completa

## Informazioni Generali

| Campo | Dettaglio |
|---|---|
| **Nome** | Supabase |
| **Tipo** | Backend-as-a-Service (BaaS) — alternativa open source a Firebase |
| **Sito** | [https://supabase.com](https://supabase.com) |
| **Documentazione** | [https://supabase.com/docs](https://supabase.com/docs) |
| **Dashboard** | [https://supabase.com/dashboard](https://supabase.com/dashboard) |
| **GitHub** | [https://github.com/supabase/supabase](https://github.com/supabase/supabase) |
| **Status Page** | [https://status.supabase.com](https://status.supabase.com) |
| **Licenza** | Apache 2.0 (core), MIT (client libraries) |
| **Fondazione** | 2020 — Paul Copplestone, Ant Wilson |
| **Sede** | Singapore (remoto globale) |
| **Funding** | $116M+ (Series B, 2022) |

---

## Contesto Vendiamonoi.it

### Progetti Supabase Attivi

| Progetto | ID | Regione | Stato | Creato | PostgreSQL |
|---|---|---|---|---|---|
| **Prodotti** | `biaazljxmybdklrtbjbx` | eu-west-3 (Parigi) | INACTIVE | 19 Nov 2025 | v17.6.1 |
| **Vendiamonoi.it Orders fullfillment** | `smozehzbhoeceioqxodx` | eu-west-1 (Irlanda) | INACTIVE | 10 Mar 2026 | v17.6.1 |

### Edge Functions Deployate

| Funzione | Progetto | Slug | Versione | Status | JWT Verify |
|---|---|---|---|---|---|
| **hyper-task** | Prodotti | `hyper-task` | v33 | ACTIVE | Sì |
| **text-task-html** | Prodotti | `text-task-html` | v11 | ACTIVE | Sì |

### Utilizzo in Vendiamonoi.it

Supabase è utilizzato come backend database e API layer per due casi d'uso principali:

1. **Catalogo Prodotti** — database relazionale per la gestione di milioni di SKU con query avanzate, filtri compositi e ricerca full-text. Il progetto "Prodotti" gestisce il catalogo centralizzato dei prodotti distribuiti sui marketplace.

2. **Order Fulfillment** — sistema di gestione ordini che riceve dati dai marketplace tramite webhook/API, li processa e coordina l'evasione con i fornitori. Il progetto "Orders fullfillment" è il backend per l'automazione del ciclo di vita dell'ordine.

3. **Edge Functions** — logica serverless per task di elaborazione dati (`hyper-task` per operazioni batch ad alta intensità, `text-task-html` per generazione/parsing di contenuti testuali e HTML).

---

## 1. Architettura della Piattaforma

### 1.1 Stack Tecnologico

Supabase è costruito interamente su tecnologie open source mature e battle-tested:

```
┌─────────────────────────────────────────────────────────┐
│                    CLIENT LAYER                          │
│  JS/TS SDK · Python SDK · Dart SDK · Swift · Kotlin    │
│  C# · REST API · GraphQL · Realtime WebSocket          │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                   API GATEWAY                            │
│  Kong / Envoy · JWT Verification · Rate Limiting        │
│  API Keys (anon / service_role) · Custom Domains        │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│               SUPABASE SERVICES                          │
│                                                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐ │
│  │ PostgREST│  │GoTrue    │  │ Storage  │  │Realtime│ │
│  │ REST API │  │Auth      │  │ S3-compat│  │ CDC    │ │
│  │ auto-gen │  │ JWT/OAuth│  │ CDN 285+ │  │ WS     │ │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └───┬────┘ │
│       │              │              │             │      │
│  ┌────▼──────────────▼──────────────▼─────────────▼──┐  │
│  │           PostgreSQL 17 (Core Database)            │  │
│  │  RLS · Functions · Triggers · Extensions           │  │
│  │  pg_cron · pg_net · pgvector · PostGIS             │  │
│  │  Supavisor (Connection Pooling)                    │  │
│  └───────────────────────────────────────────────────┘  │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │ Edge Functions│  │ pg_graphql   │  │ Vault        │  │
│  │ Deno Runtime  │  │ GraphQL API  │  │ Secrets Mgmt │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### 1.2 Componenti Principali

| Componente | Tecnologia | Funzione |
|---|---|---|
| **Database** | PostgreSQL 17 | Database relazionale core con RLS, funzioni, trigger |
| **REST API** | PostgREST | API REST auto-generata dallo schema del database |
| **Auth** | GoTrue | Autenticazione e autorizzazione con JWT |
| **Storage** | Custom (S3-compatible) | Object storage con CDN e image transforms |
| **Realtime** | Elixir/Phoenix | CDC, Broadcast, Presence via WebSocket |
| **Edge Functions** | Deno | Funzioni serverless TypeScript/JavaScript |
| **GraphQL** | pg_graphql | API GraphQL auto-generata |
| **Connection Pooling** | Supavisor | Pool di connessioni Elixir-based |
| **Vault** | Supabase Vault | Gestione sicura di segreti nel database |
| **Vector** | pgvector | Embeddings e ricerca vettoriale per AI/ML |
| **Cron** | pg_cron | Scheduling di job SQL ricorrenti |
| **Networking** | pg_net | HTTP requests dal database |

### 1.3 Differenze Chiave rispetto a Firebase

| Aspetto | Supabase | Firebase |
|---|---|---|
| **Database** | PostgreSQL relazionale | Firestore (NoSQL document) |
| **Query** | SQL completo, JOIN, aggregazioni | Query limitate, no JOIN nativi |
| **Lock-in** | Open source, portabile | Proprietario Google |
| **Self-hosting** | Sì (Docker Compose) | No |
| **Pricing** | Prevedibile, basato su risorse | Pay-per-read/write (imprevedibile) |
| **Realtime** | CDC + Broadcast + Presence | Realtime listeners nativi |
| **Auth** | GoTrue (open source) | Firebase Auth (proprietario) |
| **Storage** | S3-compatible, CDN globale | Cloud Storage (Google) |
| **Edge Functions** | Deno (standard web) | Cloud Functions (Node.js) |
| **Scalabilità catalogo** | Eccellente per milioni di righe | Costoso per grandi dataset |

---

## 2. Database PostgreSQL

### 2.1 Fondamentali

Supabase offre un'istanza PostgreSQL 17 dedicata per ogni progetto, con accesso completo a tutte le funzionalità del database.

**Caratteristiche principali:**

- PostgreSQL 17 con tutte le feature native (JSONB, array, range types, full-text search, CTE, window functions, lateral joins, generated columns)
- Accesso diretto via connection string (psql, pgAdmin, DBeaver, DataGrip)
- SQL Editor integrato nella dashboard con salvataggio query
- Supporto per schemi multipli (public, auth, storage, + custom)
- Backup automatici giornalieri (PITR su piani Pro+)
- Read replicas per distribuzione del carico di lettura

### 2.2 Row Level Security (RLS)

RLS è il meccanismo fondamentale di sicurezza in Supabase. Ogni tabella esposta via API **deve** avere RLS abilitato.

**Come funziona:**

```sql
-- 1. Abilitare RLS sulla tabella
ALTER TABLE products ENABLE ROW LEVEL SECURITY;

-- 2. Policy: tutti possono leggere i prodotti pubblicati
CREATE POLICY "Prodotti pubblici leggibili da tutti"
ON products FOR SELECT
USING (published = true);

-- 3. Policy: solo il proprietario può modificare
CREATE POLICY "Solo il proprietario modifica"
ON products FOR UPDATE
USING (auth.uid() = owner_id)
WITH CHECK (auth.uid() = owner_id);

-- 4. Policy: service_role bypassa RLS (per automazioni server-side)
-- Il service_role key bypassa automaticamente tutte le RLS policies

-- 5. Policy con ruolo personalizzato
CREATE POLICY "Admin può tutto"
ON products FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM user_roles
    WHERE user_id = auth.uid()
    AND role = 'admin'
  )
);
```

**Pattern RLS per Vendiamonoi.it:**

```sql
-- Pattern multi-tenant per fornitori
CREATE POLICY "Fornitore vede solo i propri prodotti"
ON products FOR SELECT
USING (supplier_id = auth.jwt() ->> 'supplier_id');

-- Pattern per ordini marketplace
CREATE POLICY "Ordini visibili al fornitore assegnato"
ON orders FOR SELECT
USING (
  supplier_id = auth.jwt() ->> 'supplier_id'
  OR auth.jwt() ->> 'role' = 'admin'
);

-- Pattern per accesso API da Make/automazioni (service_role)
-- Usare sempre service_role key per operazioni server-to-server
-- Il service_role bypassa tutte le RLS policies
```

**Funzioni helper per RLS:**

| Funzione | Descrizione |
|---|---|
| `auth.uid()` | UUID dell'utente autenticato corrente |
| `auth.jwt()` | Oggetto JWT completo (claims, metadata) |
| `auth.role()` | Ruolo corrente (anon, authenticated, service_role) |
| `auth.email()` | Email dell'utente autenticato |

### 2.3 Schemi e Tabelle

**Schemi predefiniti:**

| Schema | Scopo | Accessibile via API |
|---|---|---|
| `public` | Tabelle dell'applicazione | Sì (PostgREST) |
| `auth` | Tabelle di autenticazione (users, sessions) | No (solo GoTrue) |
| `storage` | Metadati oggetti storage | No (solo Storage API) |
| `extensions` | Estensioni PostgreSQL | No |
| `supabase_migrations` | Tracking delle migrazioni | No |
| `realtime` | Configurazione Realtime | No |
| `vault` | Segreti criptati | No |

**Best practice per schemi custom:**

```sql
-- Creare uno schema per logica interna non esposta via API
CREATE SCHEMA internal;

-- Tabelle nello schema internal NON sono esposte via PostgREST
CREATE TABLE internal.supplier_credentials (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  supplier_id UUID REFERENCES public.suppliers(id),
  api_key TEXT ENCRYPTED,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Esporre lo schema custom via PostgREST (opzionale)
-- Richiede configurazione nel dashboard: Settings > API > Exposed Schemas
```

### 2.4 Tipi di Dati Avanzati

```sql
-- JSONB per dati semi-strutturati (attributi prodotto variabili)
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  ean VARCHAR(13) UNIQUE NOT NULL,
  title JSONB NOT NULL,           -- {"it": "Titolo IT", "fr": "Titre FR", "de": "Titel DE"}
  description JSONB,              -- Descrizioni multilingua
  attributes JSONB,               -- Attributi variabili per categoria
  technical_specs JSONB,          -- Schede tecniche
  images TEXT[],                  -- Array di URL immagini
  price_ranges NUMRANGE[],        -- Range di prezzo per marketplace
  categories TEXT[],              -- Array di categorie
  tags TEXT[],                    -- Tag per ricerca
  weight_kg NUMERIC(10,3),
  dimensions JSONB,               -- {"length": 30, "width": 20, "height": 10, "unit": "cm"}
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

-- Indice GIN per ricerca su JSONB
CREATE INDEX idx_products_attributes ON products USING GIN (attributes);
CREATE INDEX idx_products_title ON products USING GIN (title);
CREATE INDEX idx_products_tags ON products USING GIN (tags);

-- Full-Text Search nativo
ALTER TABLE products ADD COLUMN fts tsvector
  GENERATED ALWAYS AS (
    to_tsvector('italian', coalesce(title->>'it', '') || ' ' || coalesce(description->>'it', ''))
  ) STORED;
CREATE INDEX idx_products_fts ON products USING GIN (fts);

-- Query full-text
SELECT * FROM products
WHERE fts @@ to_tsquery('italian', 'aspirapolvere & cordless');
```

### 2.5 Foreign Keys e Relazioni

```sql
-- Schema relazionale per Vendiamonoi.it
CREATE TABLE suppliers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  code VARCHAR(20) UNIQUE NOT NULL,
  contact_email TEXT,
  contact_phone TEXT,
  country VARCHAR(2) DEFAULT 'IT',
  vat_number VARCHAR(20),
  payment_terms INTEGER DEFAULT 30,    -- giorni
  status TEXT DEFAULT 'active' CHECK (status IN ('active', 'inactive', 'pending')),
  metadata JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE marketplaces (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  code VARCHAR(30) UNIQUE NOT NULL,    -- 'amazon_it', 'ebay_fr', 'kaufland_de'
  country VARCHAR(2) NOT NULL,
  platform TEXT NOT NULL,               -- 'amazon', 'mirakl', 'channelengine'
  api_endpoint TEXT,
  commission_rate NUMERIC(5,2),
  status TEXT DEFAULT 'active',
  settings JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  marketplace_id UUID REFERENCES marketplaces(id),
  supplier_id UUID REFERENCES suppliers(id),
  marketplace_order_id TEXT NOT NULL,
  status TEXT DEFAULT 'pending' CHECK (status IN (
    'pending', 'confirmed', 'processing', 'shipped',
    'delivered', 'cancelled', 'returned', 'refunded'
  )),
  customer_name TEXT,
  shipping_address JSONB,
  line_items JSONB NOT NULL,            -- [{sku, qty, price, ...}]
  total_amount NUMERIC(12,2),
  currency VARCHAR(3) DEFAULT 'EUR',
  tracking_number TEXT,
  carrier TEXT,
  shipped_at TIMESTAMPTZ,
  delivered_at TIMESTAMPTZ,
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

-- Indici per query frequenti
CREATE INDEX idx_orders_marketplace ON orders(marketplace_id);
CREATE INDEX idx_orders_supplier ON orders(supplier_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created ON orders(created_at DESC);
CREATE INDEX idx_orders_marketplace_order ON orders(marketplace_order_id);
```

### 2.6 Funzioni PostgreSQL (Stored Procedures)

```sql
-- Funzione per calcolo margine ordine
CREATE OR REPLACE FUNCTION calculate_order_margin(order_id UUID)
RETURNS NUMERIC AS $$
DECLARE
  selling_price NUMERIC;
  purchase_price NUMERIC;
  commission NUMERIC;
  shipping_cost NUMERIC;
BEGIN
  SELECT
    o.total_amount,
    COALESCE(SUM((item->>'purchase_price')::NUMERIC * (item->>'quantity')::INTEGER), 0),
    COALESCE(m.commission_rate * o.total_amount / 100, 0),
    COALESCE((o.shipping_address->>'shipping_cost')::NUMERIC, 0)
  INTO selling_price, purchase_price, commission, shipping_cost
  FROM orders o
  JOIN marketplaces m ON o.marketplace_id = m.id,
  LATERAL jsonb_array_elements(o.line_items) AS item
  WHERE o.id = order_id
  GROUP BY o.total_amount, m.commission_rate, o.shipping_address;

  RETURN selling_price - purchase_price - commission - shipping_cost;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Funzione RPC chiamabile via API
CREATE OR REPLACE FUNCTION get_supplier_dashboard(p_supplier_id UUID)
RETURNS JSON AS $$
SELECT json_build_object(
  'total_orders', COUNT(*),
  'pending_orders', COUNT(*) FILTER (WHERE status = 'pending'),
  'shipped_orders', COUNT(*) FILTER (WHERE status = 'shipped'),
  'total_revenue', COALESCE(SUM(total_amount), 0),
  'avg_order_value', COALESCE(AVG(total_amount), 0),
  'orders_today', COUNT(*) FILTER (WHERE created_at >= CURRENT_DATE),
  'orders_this_month', COUNT(*) FILTER (WHERE created_at >= date_trunc('month', CURRENT_DATE))
)
FROM orders
WHERE supplier_id = p_supplier_id;
$$ LANGUAGE sql SECURITY DEFINER;

-- Chiamata via API REST:
-- POST /rest/v1/rpc/get_supplier_dashboard
-- Body: {"p_supplier_id": "uuid-here"}
```

### 2.7 Trigger

```sql
-- Trigger per aggiornamento automatico updated_at
CREATE OR REPLACE FUNCTION update_modified_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = now();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER set_updated_at
  BEFORE UPDATE ON products
  FOR EACH ROW
  EXECUTE FUNCTION update_modified_column();

CREATE TRIGGER set_updated_at
  BEFORE UPDATE ON orders
  FOR EACH ROW
  EXECUTE FUNCTION update_modified_column();

-- Trigger per notifica nuovo ordine (via pg_net + webhook)
CREATE OR REPLACE FUNCTION notify_new_order()
RETURNS TRIGGER AS $$
BEGIN
  -- Invia webhook a Make.com per processamento
  PERFORM net.http_post(
    url := 'https://hook.eu2.make.com/your-webhook-id',
    headers := jsonb_build_object(
      'Content-Type', 'application/json',
      'Authorization', 'Bearer your-secret-token'
    ),
    body := jsonb_build_object(
      'event', 'new_order',
      'order_id', NEW.id,
      'marketplace_order_id', NEW.marketplace_order_id,
      'marketplace_id', NEW.marketplace_id,
      'total_amount', NEW.total_amount,
      'created_at', NEW.created_at
    )
  );
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_new_order
  AFTER INSERT ON orders
  FOR EACH ROW
  EXECUTE FUNCTION notify_new_order();

-- Trigger per log audit trail
CREATE TABLE audit_log (
  id BIGSERIAL PRIMARY KEY,
  table_name TEXT NOT NULL,
  record_id UUID NOT NULL,
  action TEXT NOT NULL,  -- INSERT, UPDATE, DELETE
  old_data JSONB,
  new_data JSONB,
  changed_by UUID,
  changed_at TIMESTAMPTZ DEFAULT now()
);

CREATE OR REPLACE FUNCTION audit_trigger()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO audit_log (table_name, record_id, action, old_data, new_data, changed_by)
  VALUES (
    TG_TABLE_NAME,
    COALESCE(NEW.id, OLD.id),
    TG_OP,
    CASE WHEN TG_OP IN ('UPDATE', 'DELETE') THEN to_jsonb(OLD) END,
    CASE WHEN TG_OP IN ('INSERT', 'UPDATE') THEN to_jsonb(NEW) END,
    auth.uid()
  );
  RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### 2.8 Estensioni PostgreSQL

Supabase supporta 50+ estensioni. Le più rilevanti per Vendiamonoi.it:

| Estensione | Scopo | Caso d'uso Vendiamonoi |
|---|---|---|
| **pg_cron** | Scheduling job SQL | Sincronizzazione cataloghi periodica, cleanup dati |
| **pg_net** | HTTP requests dal DB | Webhook a Make.com, notifiche API marketplace |
| **pgvector** | Ricerca vettoriale AI | Ricerca semantica prodotti, matching cataloghi |
| **pg_trgm** | Trigram matching | Ricerca fuzzy su nomi prodotti/SKU |
| **unaccent** | Rimozione accenti | Normalizzazione testi multilingua |
| **uuid-ossp** | Generazione UUID | Primary keys universali |
| **PostGIS** | Dati geospaziali | Mapping magazzini/fornitori su territorio |
| **pg_stat_statements** | Query performance | Monitoraggio query lente |
| **pgjwt** | JWT nel database | Generazione token custom |
| **http** | HTTP client | Chiamate API esterne |
| **pg_hashids** | Hashids encoding | ID pubblici offuscati per ordini |
| **plv8** | JavaScript nel DB | Trasformazioni dati complesse |

**Abilitazione estensioni:**

```sql
-- Abilitare pg_cron
CREATE EXTENSION IF NOT EXISTS pg_cron;

-- Abilitare pgvector
CREATE EXTENSION IF NOT EXISTS vector;

-- Abilitare pg_net
CREATE EXTENSION IF NOT EXISTS pg_net;

-- Abilitare pg_trgm per ricerca fuzzy
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

### 2.9 pg_cron — Job Scheduling

```sql
-- Sincronizzazione prezzi ogni ora
SELECT cron.schedule(
  'sync-prices-hourly',
  '0 * * * *',  -- ogni ora
  $$
    SELECT net.http_post(
      url := 'https://hook.eu2.make.com/price-sync-webhook',
      headers := '{"Content-Type": "application/json"}'::jsonb,
      body := jsonb_build_object('trigger', 'hourly_price_sync', 'timestamp', now())
    );
  $$
);

-- Cleanup ordini vecchi ogni giorno a mezzanotte
SELECT cron.schedule(
  'cleanup-old-data',
  '0 0 * * *',
  $$
    DELETE FROM audit_log WHERE changed_at < now() - INTERVAL '90 days';
    DELETE FROM temp_imports WHERE created_at < now() - INTERVAL '7 days';
  $$
);

-- Report giornaliero ordini
SELECT cron.schedule(
  'daily-order-report',
  '0 8 * * *',  -- ogni giorno alle 8:00
  $$
    SELECT net.http_post(
      url := 'https://hook.eu2.make.com/daily-report-webhook',
      headers := '{"Content-Type": "application/json"}'::jsonb,
      body := (
        SELECT jsonb_build_object(
          'date', CURRENT_DATE - 1,
          'total_orders', COUNT(*),
          'total_revenue', SUM(total_amount),
          'by_marketplace', jsonb_agg(
            jsonb_build_object('marketplace_id', marketplace_id, 'count', cnt, 'revenue', rev)
          )
        )
        FROM (
          SELECT marketplace_id, COUNT(*) as cnt, SUM(total_amount) as rev
          FROM orders
          WHERE created_at >= CURRENT_DATE - 1 AND created_at < CURRENT_DATE
          GROUP BY marketplace_id
        ) sub
      )
    );
  $$
);

-- Gestione cron jobs
SELECT * FROM cron.job;                    -- Lista tutti i job
SELECT cron.unschedule('sync-prices-hourly');  -- Rimuovi job
```

**Limiti pg_cron:** massimo 8 job concorrenti, timeout 10 minuti per job.

### 2.10 pg_net — HTTP dal Database

```sql
-- Chiamata GET a API esterna
SELECT net.http_get(
  url := 'https://api.marketplace.com/orders/new',
  headers := jsonb_build_object(
    'Authorization', 'Bearer ' || (SELECT decrypted_secret FROM vault.decrypted_secrets WHERE name = 'marketplace_api_key'),
    'Accept', 'application/json'
  )
);

-- Chiamata POST con body JSON
SELECT net.http_post(
  url := 'https://hook.eu2.make.com/webhook-id',
  headers := '{"Content-Type": "application/json"}'::jsonb,
  body := jsonb_build_object(
    'event', 'product_updated',
    'product_id', '12345',
    'changes', '{"price": 29.99}'::jsonb
  )
);

-- Risultati delle chiamate (asincroni)
SELECT * FROM net._http_response ORDER BY created DESC LIMIT 10;
```

### 2.11 Migrazioni

Supabase supporta migrazioni SQL versionabili:

```bash
# CLI: creare una nuova migrazione
supabase migration new create_products_table

# Questo crea: supabase/migrations/20240101000000_create_products_table.sql

# Applicare migrazioni
supabase db push

# Verificare stato migrazioni
supabase migration list

# Reset database (ATTENZIONE: distruttivo)
supabase db reset
```

**Workflow migrazioni consigliato:**

1. Sviluppo locale con `supabase start` (Docker)
2. Creazione migrazioni con `supabase migration new`
3. Test locale
4. Push su branch Supabase (`supabase db push --linked`)
5. Review e merge su produzione

### 2.12 Database Branching

Supabase supporta branching del database (simile a Git):

```bash
# Creare un branch
supabase branches create feature/new-catalog-schema

# Il branch crea una copia isolata del database
# Perfetto per testare migrazioni schema senza rischiare produzione

# Merge branch
supabase branches merge feature/new-catalog-schema

# Ogni branch ha il proprio URL di connessione
```

### 2.13 Read Replicas

Disponibili su Piano Pro+ per distribuire il carico di lettura:

- Repliche in regioni diverse per bassa latenza
- Connessione separata per letture vs scritture
- Utile per dashboard analytics pesanti senza impattare le API

### 2.14 Connection Pooling (Supavisor)

Supavisor gestisce il pool di connessioni per ottimizzare l'uso delle risorse:

| Compute Size | Connessioni Dirette | Pool Connessioni (Supavisor) |
|---|---|---|
| Nano (Free) | 60 | 200 |
| Micro | 60 | 200 |
| Small | 90 | 400 |
| Medium | 120 | 600 |
| Large | 160 | 800 |
| XL | 240 | 1200 |
| 2XL | 380 | 1500 |
| 4XL | 480 | 3000 |
| 8XL | 490 | 3000 |

**Modalità di connessione:**

```
# Connessione diretta (per migrazioni, admin)
postgresql://postgres.[ref]:[password]@aws-0-[region].pooler.supabase.com:5432/postgres

# Session mode (per transazioni lunghe)
postgresql://postgres.[ref]:[password]@aws-0-[region].pooler.supabase.com:5432/postgres?pgbouncer=true

# Transaction mode (per API/automazioni — raccomandato)
postgresql://postgres.[ref]:[password]@aws-0-[region].pooler.supabase.com:6543/postgres
```

### 2.15 Point-in-Time Recovery (PITR)

Disponibile su Piano Pro+:

- Recovery a qualsiasi punto negli ultimi 7 giorni (Pro) o 28 giorni (Enterprise)
- Granularità al secondo
- Essenziale per recovery da errori umani (DELETE/UPDATE senza WHERE)

### 2.16 Supabase Vault

Gestione sicura di segreti direttamente nel database:

```sql
-- Inserire un segreto
SELECT vault.create_secret('my_marketplace_api_key', 'sk-live-xxxxx', 'API key per Amazon SP-API');

-- Leggere un segreto (decriptato)
SELECT * FROM vault.decrypted_secrets WHERE name = 'my_marketplace_api_key';

-- Usare un segreto in una funzione
CREATE OR REPLACE FUNCTION call_marketplace_api()
RETURNS JSON AS $$
DECLARE
  api_key TEXT;
BEGIN
  SELECT decrypted_secret INTO api_key
  FROM vault.decrypted_secrets
  WHERE name = 'marketplace_api_key';

  RETURN (
    SELECT content::json FROM net.http_get(
      'https://api.marketplace.com/orders',
      headers := jsonb_build_object('Authorization', 'Bearer ' || api_key)
    )
  );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## 3. Autenticazione (GoTrue)

### 3.1 Panoramica

GoTrue è il servizio di autenticazione open source di Supabase basato su JWT.

**Metodi di autenticazione supportati:**

| Metodo | Tipo | Dettaglio |
|---|---|---|
| **Email + Password** | Nativo | Signup, login, reset password, confirm email |
| **Magic Link** | Nativo | Login via link email (passwordless) |
| **Phone OTP** | Nativo | SMS o WhatsApp via Twilio/MessageBird/Vonage |
| **Google** | OAuth | Google Sign-In |
| **Apple** | OAuth | Sign in with Apple |
| **Facebook** | OAuth | Facebook Login |
| **GitHub** | OAuth | GitHub OAuth |
| **Azure AD** | OAuth/SAML | Microsoft Azure Active Directory |
| **Slack** | OAuth | Slack Sign-In |
| **Twitter/X** | OAuth | Twitter OAuth |
| **Discord** | OAuth | Discord OAuth |
| **Spotify** | OAuth | Spotify OAuth |
| **LinkedIn** | OAuth | LinkedIn OIDC |
| **Notion** | OAuth | Notion OAuth |
| **Figma** | OAuth | Figma OAuth |
| **Bitbucket** | OAuth | Bitbucket OAuth |
| **GitLab** | OAuth | GitLab OAuth |
| **Twitch** | OAuth | Twitch OAuth |
| **Keycloak** | OIDC | Self-hosted identity provider |
| **SAML SSO** | Enterprise | Any SAML 2.0 provider (Okta, OneLogin, etc.) |
| **Anonymous** | Nativo | Sessioni anonime convertibili |

### 3.2 JWT e Chiavi API

Supabase utilizza due tipi di chiavi principali:

| Chiave | Scopo | RLS | Utilizzo |
|---|---|---|---|
| **anon key** | Client-side, pubblico | Sì, rispetta RLS | Frontend, app mobile |
| **service_role key** | Server-side, segreto | No, bypassa RLS | Backend, Make, automazioni |

**Struttura JWT:**

```json
{
  "aud": "authenticated",
  "exp": 1234567890,
  "sub": "user-uuid-here",
  "email": "user@example.com",
  "phone": "",
  "app_metadata": {
    "provider": "email",
    "providers": ["email"]
  },
  "user_metadata": {
    "name": "Mario Rossi",
    "role": "supplier",
    "supplier_id": "supplier-uuid"
  },
  "role": "authenticated"
}
```

**Custom Claims per Vendiamonoi.it:**

```sql
-- Hook per aggiungere custom claims al JWT
CREATE OR REPLACE FUNCTION custom_access_token_hook(event JSONB)
RETURNS JSONB AS $$
DECLARE
  claims JSONB;
  user_role TEXT;
  user_supplier_id UUID;
BEGIN
  claims := event->'claims';

  -- Recupera ruolo e supplier_id dal profilo utente
  SELECT role, supplier_id INTO user_role, user_supplier_id
  FROM user_profiles
  WHERE user_id = (event->>'user_id')::UUID;

  -- Aggiungi claims custom
  claims := jsonb_set(claims, '{user_role}', to_jsonb(user_role));
  IF user_supplier_id IS NOT NULL THEN
    claims := jsonb_set(claims, '{supplier_id}', to_jsonb(user_supplier_id));
  END IF;

  event := jsonb_set(event, '{claims}', claims);
  RETURN event;
END;
$$ LANGUAGE plpgsql;
```

### 3.3 Multi-Factor Authentication (MFA)

```javascript
// Enrollment TOTP
const { data, error } = await supabase.auth.mfa.enroll({
  factorType: 'totp',
  friendlyName: 'Vendiamonoi App'
});

// Verifica MFA
const { data, error } = await supabase.auth.mfa.challengeAndVerify({
  factorId: factor.id,
  code: '123456'
});
```

### 3.4 Auth Hooks

| Hook | Trigger | Uso |
|---|---|---|
| **Custom Access Token** | Generazione JWT | Aggiungere claims custom |
| **MFA Verification Attempt** | Tentativo MFA | Logging, rate limiting custom |
| **Password Verification Attempt** | Login password | Audit, blocco brute force |
| **Send Email** | Invio email auth | Custom email provider |
| **Send SMS** | Invio SMS OTP | Custom SMS provider |

### 3.5 User Management

```javascript
// Admin: lista utenti (richiede service_role)
const { data, error } = await supabase.auth.admin.listUsers();

// Admin: crea utente
const { data, error } = await supabase.auth.admin.createUser({
  email: 'fornitore@example.com',
  password: 'securepassword',
  user_metadata: { role: 'supplier', supplier_id: 'uuid' },
  email_confirm: true
});

// Admin: aggiorna utente
const { data, error } = await supabase.auth.admin.updateUserById(userId, {
  user_metadata: { role: 'admin' }
});

// Admin: elimina utente
const { data, error } = await supabase.auth.admin.deleteUser(userId);
```

---

## 4. Storage

### 4.1 Panoramica

Supabase Storage è un sistema di object storage S3-compatible con CDN globale integrato.

**Caratteristiche:**

- Bucket pubblici e privati
- Upload fino a 5GB per file (50MB su piano Free)
- Resumable uploads (TUS protocol) per file grandi
- Image transforms (resize, crop, format) via CDN
- RLS policies sui bucket
- CDN con 285+ PoP (Points of Presence) globali
- Compatibilità S3 (per strumenti come `aws-cli`, `boto3`)

### 4.2 Struttura Bucket

```javascript
// Creare un bucket per immagini prodotti
const { data, error } = await supabase.storage.createBucket('product-images', {
  public: true,                    // Accessibile pubblicamente
  fileSizeLimit: 10485760,         // 10MB max
  allowedMimeTypes: ['image/jpeg', 'image/png', 'image/webp']
});

// Bucket privati per documenti fornitori
const { data, error } = await supabase.storage.createBucket('supplier-docs', {
  public: false,
  fileSizeLimit: 52428800,         // 50MB max
  allowedMimeTypes: ['application/pdf', 'application/vnd.ms-excel',
                     'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet']
});
```

**Bucket consigliati per Vendiamonoi.it:**

| Bucket | Visibilità | Scopo | Limite File |
|---|---|---|---|
| `product-images` | Pubblico | Foto prodotti per marketplace | 10MB |
| `product-docs` | Privato | Schede tecniche, manuali | 50MB |
| `supplier-docs` | Privato | Contratti, listini, certificati | 50MB |
| `order-attachments` | Privato | DDT, etichette spedizione, fatture | 20MB |
| `import-files` | Privato | File CSV/XML di importazione cataloghi | 100MB |
| `export-files` | Privato | Feed generati per marketplace | 100MB |

### 4.3 RLS su Storage

```sql
-- Policy: chiunque può vedere immagini prodotti pubblici
CREATE POLICY "Immagini prodotti pubbliche"
ON storage.objects FOR SELECT
USING (bucket_id = 'product-images');

-- Policy: solo admin può caricare immagini
CREATE POLICY "Solo admin carica immagini"
ON storage.objects FOR INSERT
WITH CHECK (
  bucket_id = 'product-images'
  AND auth.jwt() ->> 'user_role' = 'admin'
);

-- Policy: fornitori vedono solo i propri documenti
CREATE POLICY "Fornitore vede propri documenti"
ON storage.objects FOR SELECT
USING (
  bucket_id = 'supplier-docs'
  AND (storage.foldername(name))[1] = auth.jwt() ->> 'supplier_id'
);
```

### 4.4 Image Transforms

```
# URL di trasformazione immagine (via CDN)
https://[project-ref].supabase.co/storage/v1/render/image/public/product-images/sku-12345.jpg
  ?width=800
  &height=600
  &resize=cover
  &quality=80
  &format=webp

# Parametri disponibili:
# width: larghezza in pixel
# height: altezza in pixel
# resize: contain | cover | fill
# quality: 1-100 (solo JPEG/WebP)
# format: origin | webp | avif
```

### 4.5 Presigned URLs

```javascript
// Generare URL temporaneo per download privato (60 secondi)
const { data, error } = await supabase.storage
  .from('supplier-docs')
  .createSignedUrl('supplier-123/contratto.pdf', 60);

// Generare URL temporaneo per upload
const { data, error } = await supabase.storage
  .from('import-files')
  .createSignedUploadUrl('catalogo-2024.csv');

// Upload con URL firmato
const { data, error } = await supabase.storage
  .from('import-files')
  .uploadToSignedUrl('catalogo-2024.csv', signedUrl.token, file);
```

### 4.6 Compatibilità S3

```bash
# Accesso via aws-cli
aws s3 ls s3://product-images/ \\
  --endpoint-url https://[project-ref].supabase.co/storage/v1/s3 \\
  --region eu-west-3

# Upload via aws-cli
aws s3 cp local-image.jpg s3://product-images/sku-12345.jpg \\
  --endpoint-url https://[project-ref].supabase.co/storage/v1/s3

# Utile per bulk upload da script o tool di terze parti
```

---

## 5. Edge Functions

### 5.1 Panoramica

Le Edge Functions sono funzioni serverless basate su Deno Deploy, eseguite globalmente in regioni distribuite.

**Caratteristiche:**

- Runtime Deno (TypeScript/JavaScript nativo)
- Cold start < 200ms
- Supporto Web APIs standard (fetch, Request, Response)
- Import via URL o npm (via `npm:` specifier)
- Accesso a Supabase client preconfigurato
- Secrets management integrato
- CORS configurabile
- JWT verification automatica (opzionale)

### 5.2 Edge Functions Vendiamonoi.it

| Funzione | Versione | Scopo |
|---|---|---|
| **hyper-task** | v33 | Operazioni batch ad alta intensità — processamento bulk di ordini, sincronizzazione cataloghi, elaborazione dati massiva |
| **text-task-html** | v11 | Generazione e parsing di contenuti testuali e HTML — creazione schede prodotto, parsing feed XML, generazione template |

### 5.3 Struttura di una Edge Function

```typescript
// supabase/functions/process-order/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

const corsHeaders = {
  "Access-Control-Allow-Origin": "*",
  "Access-Control-Allow-Headers": "authorization, x-client-info, apikey, content-type",
};

serve(async (req: Request) => {
  // CORS preflight
  if (req.method === "OPTIONS") {
    return new Response("ok", { headers: corsHeaders });
  }

  try {
    // Client con service_role per operazioni admin
    const supabase = createClient(
      Deno.env.get("SUPABASE_URL")!,
      Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!
    );

    const { order_id, action } = await req.json();

    // Recupera ordine
    const { data: order, error } = await supabase
      .from("orders")
      .select("*, suppliers(*), marketplaces(*)")
      .eq("id", order_id)
      .single();

    if (error) throw error;

    // Processa ordine in base all'azione
    switch (action) {
      case "confirm":
        // Invia conferma al fornitore
        await notifySupplier(order);
        await supabase
          .from("orders")
          .update({ status: "confirmed" })
          .eq("id", order_id);
        break;

      case "ship":
        // Genera etichetta spedizione
        const label = await generateShippingLabel(order);
        await supabase
          .from("orders")
          .update({
            status: "shipped",
            tracking_number: label.tracking,
            shipped_at: new Date().toISOString()
          })
          .eq("id", order_id);
        break;
    }

    return new Response(
      JSON.stringify({ success: true, order_id }),
      { headers: { ...corsHeaders, "Content-Type": "application/json" } }
    );
  } catch (err) {
    return new Response(
      JSON.stringify({ error: err.message }),
      { status: 400, headers: { ...corsHeaders, "Content-Type": "application/json" } }
    );
  }
});
```

### 5.4 Deploy e Gestione

```bash
# Creare nuova funzione
supabase functions new process-order

# Deploy singola funzione
supabase functions deploy process-order

# Deploy tutte le funzioni
supabase functions deploy

# Gestione secrets
supabase secrets set MARKETPLACE_API_KEY=sk-live-xxxxx
supabase secrets set MAKE_WEBHOOK_URL=https://hook.eu2.make.com/xxx
supabase secrets list

# Logs
supabase functions logs process-order --tail

# Invocazione da CLI (test)
supabase functions invoke process-order --body '{"order_id": "uuid", "action": "confirm"}'
```

### 5.5 Invocazione via API

```bash
# Via curl
curl -X POST 'https://[ref].supabase.co/functions/v1/process-order' \\
  -H 'Authorization: Bearer [anon-key-or-user-jwt]' \\
  -H 'Content-Type: application/json' \\
  -d '{"order_id": "uuid", "action": "confirm"}'

# Via JavaScript SDK
const { data, error } = await supabase.functions.invoke('process-order', {
  body: { order_id: 'uuid', action: 'confirm' }
});
```

### 5.6 Pattern Avanzati

```typescript
// Pattern: Webhook receiver per marketplace
serve(async (req: Request) => {
  const signature = req.headers.get("x-marketplace-signature");
  const body = await req.text();

  // Verifica firma webhook
  if (!verifySignature(body, signature, Deno.env.get("WEBHOOK_SECRET")!)) {
    return new Response("Invalid signature", { status: 401 });
  }

  const payload = JSON.parse(body);

  // Processa evento marketplace
  switch (payload.event_type) {
    case "order.created":
      await handleNewOrder(payload.data);
      break;
    case "order.cancelled":
      await handleCancellation(payload.data);
      break;
    case "refund.requested":
      await handleRefund(payload.data);
      break;
  }

  return new Response("OK", { status: 200 });
});

// Pattern: Batch processing con chunking
async function processCatalogBatch(items: any[], batchSize = 100) {
  const supabase = createClient(
    Deno.env.get("SUPABASE_URL")!,
    Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!
  );

  for (let i = 0; i < items.length; i += batchSize) {
    const chunk = items.slice(i, i + batchSize);
    const { error } = await supabase
      .from("products")
      .upsert(chunk, { onConflict: "ean" });

    if (error) console.error(`Batch ${i / batchSize} failed:`, error);
  }
}

// Pattern: Scheduled function via pg_cron
// pg_cron chiama la Edge Function periodicamente:
// SELECT cron.schedule('catalog-sync', '*/30 * * * *', $$
//   SELECT net.http_post(
//     url := 'https://[ref].supabase.co/functions/v1/sync-catalog',
//     headers := '{"Authorization": "Bearer [service_role_key]"}'::jsonb,
//     body := '{}'::jsonb
//   );
// $$);
```

---

## 6. Realtime

### 6.1 Panoramica

Il servizio Realtime di Supabase offre tre modalità di comunicazione in tempo reale via WebSocket.

### 6.2 Database Changes (CDC)

Ascolta INSERT, UPDATE, DELETE su tabelle specifiche:

```javascript
// Ascolta nuovi ordini in tempo reale
const channel = supabase
  .channel('new-orders')
  .on(
    'postgres_changes',
    {
      event: 'INSERT',
      schema: 'public',
      table: 'orders'
    },
    (payload) => {
      console.log('Nuovo ordine:', payload.new);
      // Aggiorna dashboard, invia notifica
    }
  )
  .on(
    'postgres_changes',
    {
      event: 'UPDATE',
      schema: 'public',
      table: 'orders',
      filter: 'status=eq.shipped'
    },
    (payload) => {
      console.log('Ordine spedito:', payload.new);
    }
  )
  .subscribe();
```

**Nota importante:** RLS viene rispettato anche su Realtime. Solo le righe che l'utente è autorizzato a vedere verranno inviate via CDC.

### 6.3 Broadcast

Messaggi client-to-client senza persistenza (in-memory):

```javascript
// Dashboard: invia aggiornamento stato
const channel = supabase.channel('dashboard-updates');

// Sender
channel.send({
  type: 'broadcast',
  event: 'order-status',
  payload: { order_id: 'uuid', new_status: 'shipped' }
});

// Receiver
channel.on('broadcast', { event: 'order-status' }, (payload) => {
  updateDashboard(payload);
}).subscribe();
```

### 6.4 Presence

Traccia utenti connessi in tempo reale:

```javascript
const channel = supabase.channel('online-users');

channel.on('presence', { event: 'sync' }, () => {
  const state = channel.presenceState();
  console.log('Utenti online:', Object.keys(state).length);
}).subscribe(async (status) => {
  if (status === 'SUBSCRIBED') {
    await channel.track({
      user_id: currentUser.id,
      name: currentUser.name,
      online_at: new Date().toISOString()
    });
  }
});
```

### 6.5 Limiti Realtime

| Piano | Connessioni Concorrenti | Messaggi/sec | Canali | Joins/sec |
|---|---|---|---|---|
| Free | 200 | 100 | Illimitati | 100 |
| Pro | 500 | 500 | Illimitati | 500 |
| Team | 500 | 500 | Illimitati | 500 |
| Enterprise | Custom | Custom | Illimitati | Custom |

---

## 7. REST API (PostgREST)

### 7.1 Panoramica

PostgREST genera automaticamente un'API REST completa dallo schema del database. Ogni tabella nello schema `public` diventa un endpoint.

### 7.2 Endpoint Base

```
Base URL: https://[project-ref].supabase.co/rest/v1/

Headers richiesti:
  apikey: [anon-key o service_role-key]
  Authorization: Bearer [jwt-token]
  Content-Type: application/json  (per POST/PATCH/PUT)
  Prefer: return=representation   (per ricevere i dati modificati)
```

### 7.3 Operatori di Filtro

| Operatore | Abbreviazione | Esempio |
|---|---|---|
| Uguale | `eq` | `?status=eq.active` |
| Non uguale | `neq` | `?status=neq.cancelled` |
| Maggiore di | `gt` | `?price=gt.10` |
| Maggiore o uguale | `gte` | `?price=gte.10` |
| Minore di | `lt` | `?price=lt.100` |
| Minore o uguale | `lte` | `?price=lte.100` |
| Pattern matching | `like` | `?name=like.*aspirapolvere*` |
| Pattern case-insensitive | `ilike` | `?name=ilike.*ASPIRA*` |
| In lista | `in` | `?status=in.(active,pending)` |
| Is null | `is` | `?tracking_number=is.null` |
| Is not null | `not.is` | `?tracking_number=not.is.null` |
| Contains (array) | `cs` | `?tags=cs.{electronics,home}` |
| Contained by (array) | `cd` | `?tags=cd.{electronics,home,garden}` |
| Overlap (array) | `ov` | `?tags=ov.{electronics,home}` |
| Full-text search | `fts` | `?fts=fts(italian).aspirapolvere` |
| Phrase search | `phfts` | `?fts=phfts(italian).aspirapolvere cordless` |
| JSON contains | `cs` | `?attributes=cs.{"color":"red"}` |
| Range contains | `cs` | `?price_range=cs.[10,50]` |
| AND logico | `,` | `?status=eq.active&price=gt.10` |
| OR logico | `or` | `?or=(status.eq.active,status.eq.pending)` |
| NOT | `not` | `?not.status=eq.cancelled` |
| Nested OR/AND | `and/or` | `?and=(price.gt.10,or(status.eq.active,status.eq.pending))` |

### 7.4 Select, Ordering, Pagination

```bash
# Select colonne specifiche
GET /rest/v1/products?select=id,ean,title,price

# Select con relazioni (JOIN automatico via FK)
GET /rest/v1/orders?select=*,suppliers(name,email),marketplaces(name,code)

# Select nested
GET /rest/v1/suppliers?select=name,orders(id,status,total_amount,marketplaces(name))

# Ordinamento
GET /rest/v1/orders?order=created_at.desc

# Ordinamento multiplo
GET /rest/v1/products?order=category.asc,price.desc

# Ordinamento nulls
GET /rest/v1/orders?order=shipped_at.desc.nullslast

# Paginazione (offset-based)
GET /rest/v1/products?limit=50&offset=100

# Paginazione con Range header
GET /rest/v1/products
  Range: 0-49
  # Response header: Content-Range: 0-49/12345

# Count totale
GET /rest/v1/products?select=count
  Prefer: count=exact
```

### 7.5 Operazioni CRUD

```bash
# INSERT singolo
POST /rest/v1/products
{
  "ean": "8001234567890",
  "title": {"it": "Aspirapolvere Cordless"},
  "price": 199.99
}

# INSERT bulk
POST /rest/v1/products
[
  {"ean": "8001234567890", "title": {"it": "Prodotto 1"}},
  {"ean": "8001234567891", "title": {"it": "Prodotto 2"}}
]

# UPSERT (insert or update su conflitto)
POST /rest/v1/products
Prefer: resolution=merge-duplicates
{"ean": "8001234567890", "price": 179.99}
# Upsert su colonna specifica:
# POST /rest/v1/products?on_conflict=ean

# UPDATE
PATCH /rest/v1/orders?id=eq.uuid-here
{"status": "shipped", "tracking_number": "IT1234567890"}

# UPDATE bulk con filtro
PATCH /rest/v1/orders?status=eq.pending&created_at=lt.2024-01-01
{"status": "cancelled"}

# DELETE
DELETE /rest/v1/products?id=eq.uuid-here

# DELETE con filtro
DELETE /rest/v1/temp_imports?created_at=lt.2024-01-01
```

### 7.6 RPC (Remote Procedure Call)

```bash
# Chiamata a funzione PostgreSQL
POST /rest/v1/rpc/get_supplier_dashboard
{"p_supplier_id": "uuid-here"}

# Funzione con risultati complessi
POST /rest/v1/rpc/calculate_order_margin
{"order_id": "uuid-here"}

# Funzione che ritorna set di righe (si comporta come una tabella)
POST /rest/v1/rpc/search_products
{"search_term": "aspirapolvere", "category": "elettrodomestici"}
# Può essere filtrata: /rpc/search_products?price=gt.50&order=relevance.desc
```

### 7.7 Rate Limits

| Piano | Richieste/sec (per progetto) |
|---|---|
| Free | 100 |
| Pro | 1000 |
| Team | 1000 |
| Enterprise | Custom |

---

## 8. GraphQL API

### 8.1 Panoramica

Supabase offre un'API GraphQL auto-generata tramite l'estensione `pg_graphql`.

```graphql
# Query prodotti con relazioni
query {
  productsCollection(
    filter: { status: { eq: "active" } }
    orderBy: [{ created_at: DescNullsLast }]
    first: 50
  ) {
    edges {
      node {
        id
        ean
        title
        price
        supplier {
          name
          code
        }
      }
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}

# Mutation
mutation {
  insertIntoOrdersCollection(objects: [{
    marketplace_order_id: "AMZ-12345"
    marketplace_id: "uuid"
    supplier_id: "uuid"
    total_amount: 59.99
    line_items: [{"sku": "SKU001", "qty": 1, "price": 59.99}]
  }]) {
    records {
      id
      status
    }
  }
}
```

**Endpoint:** `https://[ref].supabase.co/graphql/v1`

---

## 9. Client SDK

### 9.1 SDK Disponibili

| Linguaggio | Package | Stato |
|---|---|---|
| **JavaScript/TypeScript** | `@supabase/supabase-js` | Stabile (v2) |
| **Python** | `supabase-py` | Stabile |
| **Dart/Flutter** | `supabase_flutter` | Stabile |
| **Swift** | `supabase-swift` | Stabile |
| **Kotlin** | `supabase-kt` | Stabile |
| **C#** | `supabase-csharp` | Community |

### 9.2 JavaScript SDK (Principale)

```javascript
import { createClient } from '@supabase/supabase-js';

// Client per frontend (anon key)
const supabase = createClient(
  'https://[ref].supabase.co',
  'eyJhbGciOi...' // anon key
);

// Client per backend/automazioni (service_role)
const supabaseAdmin = createClient(
  'https://[ref].supabase.co',
  'eyJhbGciOi...',  // service_role key
  { auth: { autoRefreshToken: false, persistSession: false } }
);
```

**Operazioni Database:**

```javascript
// SELECT con filtri
const { data, error } = await supabase
  .from('products')
  .select('id, ean, title, price, suppliers(name)')
  .eq('status', 'active')
  .gte('price', 10)
  .order('created_at', { ascending: false })
  .range(0, 49);

// INSERT
const { data, error } = await supabase
  .from('products')
  .insert({ ean: '8001234567890', title: { it: 'Prodotto' }, price: 29.99 })
  .select();

// UPSERT
const { data, error } = await supabase
  .from('products')
  .upsert({ ean: '8001234567890', price: 24.99 }, { onConflict: 'ean' })
  .select();

// UPDATE
const { data, error } = await supabase
  .from('orders')
  .update({ status: 'shipped', tracking_number: 'IT123' })
  .eq('id', orderId)
  .select();

// DELETE
const { data, error } = await supabase
  .from('temp_imports')
  .delete()
  .lt('created_at', '2024-01-01');

// RPC
const { data, error } = await supabase
  .rpc('get_supplier_dashboard', { p_supplier_id: supplierId });

// COUNT
const { count, error } = await supabase
  .from('products')
  .select('*', { count: 'exact', head: true })
  .eq('status', 'active');
```

### 9.3 Python SDK

```python
from supabase import create_client

supabase = create_client(
    "https://[ref].supabase.co",
    "service_role_key"
)

# Query
response = supabase.table("products") \\
    .select("*") \\
    .eq("status", "active") \\
    .order("created_at", desc=True) \\
    .limit(100) \\
    .execute()

# Insert
response = supabase.table("products").insert({
    "ean": "8001234567890",
    "title": {"it": "Prodotto"},
    "price": 29.99
}).execute()

# Upsert bulk
response = supabase.table("products").upsert(
    [{"ean": "8001234567890", "price": 24.99},
     {"ean": "8001234567891", "price": 34.99}],
    on_conflict="ean"
).execute()

# RPC
response = supabase.rpc("get_supplier_dashboard", {
    "p_supplier_id": "uuid"
}).execute()

# Storage
response = supabase.storage.from_("product-images").upload(
    "sku-12345.jpg",
    open("local-image.jpg", "rb"),
    {"content-type": "image/jpeg"}
)
```

---

## 10. CLI (Command Line Interface)

### 10.1 Installazione e Setup

```bash
# Installazione via npm
npm install -g supabase

# Oppure via Homebrew (macOS)
brew install supabase/tap/supabase

# Login
supabase login

# Inizializzare progetto
supabase init

# Collegare a progetto esistente
supabase link --project-ref [project-ref]
```

### 10.2 Sviluppo Locale

```bash
# Avviare ambiente locale (richiede Docker)
supabase start

# Output: URL locale per ogni servizio
#   API URL: http://localhost:54321
#   DB URL: postgresql://postgres:postgres@localhost:54322/postgres
#   Studio URL: http://localhost:54323
#   Anon key: eyJ...
#   Service role key: eyJ...

# Stato ambiente locale
supabase status

# Fermare ambiente locale
supabase stop

# Reset database locale
supabase db reset
```

### 10.3 Comandi Principali

| Comando | Descrizione |
|---|---|
| `supabase init` | Inizializza progetto |
| `supabase start` | Avvia ambiente locale Docker |
| `supabase stop` | Ferma ambiente locale |
| `supabase status` | Stato servizi locali |
| `supabase db push` | Applica migrazioni su remoto |
| `supabase db pull` | Scarica schema remoto come migrazione |
| `supabase db reset` | Reset database locale |
| `supabase db diff` | Genera diff tra locale e remoto |
| `supabase db lint` | Lint dello schema |
| `supabase migration new [name]` | Crea nuova migrazione |
| `supabase migration list` | Lista migrazioni |
| `supabase migration repair` | Ripara stato migrazioni |
| `supabase functions new [name]` | Crea nuova Edge Function |
| `supabase functions deploy [name]` | Deploy Edge Function |
| `supabase functions serve` | Serve funzioni localmente |
| `supabase functions logs [name]` | Visualizza log funzione |
| `supabase secrets set KEY=VALUE` | Imposta secret |
| `supabase secrets list` | Lista secrets |
| `supabase gen types typescript` | Genera tipi TypeScript |
| `supabase inspect db` | Ispeziona database |
| `supabase projects list` | Lista progetti |
| `supabase orgs list` | Lista organizzazioni |

### 10.4 Type Generation

```bash
# Genera tipi TypeScript dallo schema
supabase gen types typescript --linked > types/supabase.ts

# Uso nel codice
import { Database } from './types/supabase';

const supabase = createClient<Database>(url, key);

// Ora il client è completamente tipizzato
const { data } = await supabase
  .from('products')  // autocomplete tabelle
  .select('id, ean, title')  // autocomplete colonne
  .eq('status', 'active');  // type-safe filters

// data è tipizzato come { id: string, ean: string, title: Json }[]
```

---

## 11. Integrazioni Esterne

### 11.1 Make (Integromat)

Make ha un connettore ufficiale Supabase con i seguenti moduli:

**Moduli Storage:**

| Modulo | Azione |
|---|---|
| Upload a File | Carica file in un bucket |
| Delete a File | Elimina file da bucket |
| Get a Signed URL | Genera URL firmato per accesso temporaneo |
| List Files | Lista file in un bucket/cartella |
| Move a File | Sposta file tra cartelle/bucket |

**Moduli Database:**

| Modulo | Azione |
|---|---|
| Create a Row | INSERT singola riga |
| Update a Row | UPDATE riga per ID |
| Delete a Row | DELETE riga per ID |
| Get a Row | SELECT singola riga per ID |
| Search Rows | SELECT con filtri (query builder) |

**Moduli API:**

| Modulo | Azione |
|---|---|
| Make an API Call | Chiamata REST personalizzata |
| Call an RPC Function | Chiamata a funzione PostgreSQL |

**Pattern Make + Supabase per Vendiamonoi.it:**

```
Scenario 1: Importazione Catalogo Fornitore
Webhook (CSV dal fornitore)
  → Parse CSV
  → Iterator
  → Supabase: Upsert Row (products, on_conflict: ean)
  → Error Handler: Log errori su tabella import_errors

Scenario 2: Sincronizzazione Ordini Marketplace
Scheduled (ogni 15 min)
  → HTTP: GET nuovi ordini da marketplace API
  → Iterator
  → Supabase: Create Row (orders)
  → Supabase: Call RPC (notify_supplier)
  → Router:
    → Route A (ordine urgente): Email al fornitore
    → Route B (ordine standard): Aggiungi a coda evasione

Scenario 3: Aggiornamento Prezzi
pg_cron trigger → Edge Function → Make Webhook
  → Supabase: Search Rows (products with price_update_needed)
  → Iterator
  → HTTP: Update price on marketplace API
  → Supabase: Update Row (products, last_price_sync)

Scenario 4: Report Giornaliero
Schedule (ogni giorno 8:00)
  → Supabase: Call RPC (daily_report_data)
  → Google Sheets: Aggiungi riga a report
  → Email: Invia riepilogo
```

**Configurazione connessione Make:**

```
URL: https://[project-ref].supabase.co
API Key: [service_role key]  ← IMPORTANTE: usare service_role per bypassare RLS
```

### 11.2 Channable / ChannelEngine

Pattern di integrazione per distribuire prodotti su marketplace:

```
Supabase (catalogo centralizzato)
  → Edge Function (genera feed XML/CSV)
  → Storage bucket (export-files)
  → URL pubblica del feed
  → Channable/ChannelEngine importa il feed
  → Distribuisce su 20+ marketplace
```

```typescript
// Edge Function: genera feed per Channable
serve(async (req) => {
  const supabase = createClient(
    Deno.env.get("SUPABASE_URL")!,
    Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!
  );

  const { data: products } = await supabase
    .from("products")
    .select("*")
    .eq("status", "active")
    .eq("feed_enabled", true);

  // Genera CSV
  const csv = generateCSV(products, {
    columns: ["ean", "title_it", "description_it", "price", "stock", "category", "brand", "image_url"]
  });

  // Salva su Storage
  await supabase.storage
    .from("export-files")
    .upload("feeds/channable-feed.csv", csv, {
      contentType: "text/csv",
      upsert: true
    });

  return new Response(JSON.stringify({ success: true, products: products.length }));
});
```

### 11.3 Shopify

```javascript
// Sync prodotti Shopify → Supabase
// Via Make webhook o Edge Function

async function syncShopifyProducts(shopifyProducts) {
  const formatted = shopifyProducts.map(p => ({
    shopify_id: p.id,
    title: { it: p.title },
    ean: p.variants[0]?.barcode,
    price: parseFloat(p.variants[0]?.price),
    stock: p.variants[0]?.inventory_quantity,
    images: p.images.map(img => img.src),
    shopify_data: p  // salva dati raw come JSONB
  }));

  const { data, error } = await supabase
    .from("products")
    .upsert(formatted, { onConflict: "shopify_id" });
}
```

### 11.4 Google Sheets

Pattern di sincronizzazione bidirezionale:

```
Google Sheets → Make → Supabase
  Trigger: Google Sheets "Watch Rows"
  → Transform data
  → Supabase: Upsert Row

Supabase → Make → Google Sheets
  Trigger: Scheduled o Supabase webhook
  → Supabase: Search Rows
  → Google Sheets: Add/Update Row
```

### 11.5 GitHub Actions (CI/CD)

```yaml
# .github/workflows/supabase-deploy.yml
name: Deploy Supabase

on:
  push:
    branches: [main]
    paths:
      - 'supabase/**'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: supabase/setup-cli@v1
        with:
          version: latest
      - run: supabase link --project-ref ${{ secrets.SUPABASE_PROJECT_REF }}
        env:
          SUPABASE_ACCESS_TOKEN: ${{ secrets.SUPABASE_ACCESS_TOKEN }}
      - run: supabase db push
        env:
          SUPABASE_ACCESS_TOKEN: ${{ secrets.SUPABASE_ACCESS_TOKEN }}
      - run: supabase functions deploy
        env:
          SUPABASE_ACCESS_TOKEN: ${{ secrets.SUPABASE_ACCESS_TOKEN }}
```

### 11.6 Webhooks (Database → Esterno)

Pattern usando pg_net + trigger:

```sql
-- Webhook generico riutilizzabile
CREATE OR REPLACE FUNCTION send_webhook(
  webhook_url TEXT,
  payload JSONB,
  secret TEXT DEFAULT NULL
)
RETURNS VOID AS $$
DECLARE
  headers JSONB;
BEGIN
  headers := jsonb_build_object('Content-Type', 'application/json');

  IF secret IS NOT NULL THEN
    headers := headers || jsonb_build_object(
      'X-Webhook-Secret', secret,
      'X-Timestamp', extract(epoch from now())::text
    );
  END IF;

  PERFORM net.http_post(
    url := webhook_url,
    headers := headers,
    body := payload
  );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Uso nei trigger
CREATE OR REPLACE FUNCTION webhook_on_order_status_change()
RETURNS TRIGGER AS $$
BEGIN
  IF OLD.status IS DISTINCT FROM NEW.status THEN
    PERFORM send_webhook(
      'https://hook.eu2.make.com/order-status-webhook',
      jsonb_build_object(
        'event', 'order_status_changed',
        'order_id', NEW.id,
        'old_status', OLD.status,
        'new_status', NEW.status,
        'marketplace_order_id', NEW.marketplace_order_id,
        'changed_at', now()
      ),
      (SELECT decrypted_secret FROM vault.decrypted_secrets WHERE name = 'webhook_secret')
    );
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_order_status_change
  AFTER UPDATE ON orders
  FOR EACH ROW
  EXECUTE FUNCTION webhook_on_order_status_change();
```

---

## 12. Monitoraggio e Logging

### 12.1 Log Types

| Tipo Log | Fonte | Informazione |
|---|---|---|
| **API Logs** | PostgREST | Richieste REST, latenza, errori |
| **Auth Logs** | GoTrue | Login, signup, errori auth |
| **Database Logs** | PostgreSQL | Query lente, errori, warnings |
| **Storage Logs** | Storage | Upload, download, errori |
| **Edge Function Logs** | Deno | Console output, errori runtime |
| **Realtime Logs** | Elixir | Connessioni WS, messaggi, errori |

### 12.2 Dashboard Monitoring

La dashboard Supabase include 200+ grafici e metriche:

- **Database:** CPU, memoria, I/O, connessioni attive, query/sec, cache hit ratio
- **API:** Richieste/sec, latenza p50/p95/p99, errori per codice HTTP
- **Auth:** Signup/login rate, provider distribution, errori
- **Storage:** Bandwidth, upload/download rate, spazio utilizzato
- **Realtime:** Connessioni attive, messaggi/sec
- **Edge Functions:** Invocazioni, latenza, errori, cold starts

### 12.3 pg_stat_statements

```sql
-- Abilitare monitoraggio query
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;

-- Top 10 query più lente
SELECT
  calls,
  mean_exec_time::numeric(10,2) as avg_ms,
  total_exec_time::numeric(10,2) as total_ms,
  rows,
  query
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 10;

-- Query con più I/O
SELECT
  calls,
  shared_blks_hit + shared_blks_read as total_blocks,
  shared_blks_hit::float / NULLIF(shared_blks_hit + shared_blks_read, 0) as cache_hit_ratio,
  query
FROM pg_stat_statements
ORDER BY shared_blks_read DESC
LIMIT 10;
```

### 12.4 Database Advisors

Supabase offre advisor automatici che analizzano il database e suggeriscono ottimizzazioni:

- **Security Advisor:** RLS mancante, policy vulnerabili
- **Performance Advisor:** Indici mancanti, query lente, bloat
- **Schema Advisor:** Colonne non utilizzate, FK mancanti

Accessibili via Dashboard → Database → Advisors o via MCP tool `get_advisors`.

---

## 13. Sicurezza

### 13.1 Livelli di Sicurezza

| Livello | Meccanismo | Configurazione |
|---|---|---|
| **Network** | SSL/TLS obbligatorio, IP allowlist | Dashboard → Settings → Network |
| **Autenticazione** | JWT, OAuth, MFA, SAML | Dashboard → Auth |
| **Autorizzazione** | RLS Policies, Roles | SQL / Dashboard → Database |
| **Encryption** | AES-256 at rest, TLS in transit | Automatico |
| **Secrets** | Supabase Vault (encrypted) | SQL / Dashboard |
| **API** | Rate limiting, CORS, API keys | Dashboard → Settings → API |
| **Audit** | Log completi, pg_audit | Estensione opzionale |

### 13.2 Best Practice Sicurezza

```sql
-- 1. SEMPRE abilitare RLS su tabelle esposte
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
ALTER TABLE suppliers ENABLE ROW LEVEL SECURITY;

-- 2. Policy di default deny (nessuna policy = nessun accesso)
-- Le tabelle con RLS abilitato senza policy negano tutto

-- 3. Mai esporre service_role key nel frontend
-- Usare SOLO anon key lato client

-- 4. Validare input con CHECK constraints
ALTER TABLE products ADD CONSTRAINT valid_ean
  CHECK (ean ~ '^\\d{8,13}$');
ALTER TABLE orders ADD CONSTRAINT valid_status
  CHECK (status IN ('pending','confirmed','processing','shipped','delivered','cancelled','returned','refunded'));

-- 5. Usare SECURITY DEFINER con cautela
-- Le funzioni SECURITY DEFINER eseguono con i privilegi del creatore
-- Utili per operazioni privilegiate, ma validare sempre l'input

-- 6. IP Allowlist per connessioni dirette al database
-- Dashboard → Settings → Network → IP Allow List
```

### 13.3 Compliance

| Standard | Supporto |
|---|---|
| **SOC 2 Type 2** | Sì (tutti i piani) |
| **HIPAA** | Sì (Enterprise, con BAA) |
| **GDPR** | Sì (regioni EU disponibili) |
| **ISO 27001** | In corso |

---

## 14. Pricing e Piani

### 14.1 Piani

| Feature | Free | Pro ($25/mese) | Team ($599/mese) | Enterprise |
|---|---|---|---|---|
| **Database** | 500 MB | 8 GB (poi $0.125/GB) | 8 GB (poi $0.125/GB) | Custom |
| **Storage** | 1 GB | 100 GB (poi $0.021/GB) | 100 GB (poi $0.021/GB) | Custom |
| **Bandwidth** | 5 GB | 250 GB (poi $0.09/GB) | 250 GB (poi $0.09/GB) | Custom |
| **MAU Auth** | 50.000 | 100.000 (poi $0.00325/MAU) | 100.000 | Custom |
| **Edge Functions** | 500K invocazioni | 2M (poi $2/M) | 2M | Custom |
| **Realtime** | 200 connessioni | 500 connessioni | 500 connessioni | Custom |
| **Progetti** | 2 | Illimitati | Illimitati | Illimitati |
| **Organizzazione** | 1 | Illimitata | Illimitata | Illimitata |
| **Backup** | Giornaliero (7gg) | Giornaliero + PITR (7gg) | PITR (28gg) | Custom |
| **Support** | Community | Email | Priority | Dedicated |
| **SLA** | No | No | 99.9% | Custom |
| **SSO/SAML** | No | No | Sì | Sì |
| **Read Replicas** | No | Sì (add-on) | Sì | Sì |
| **Custom Domain** | No | Sì ($10/mese) | Sì | Sì |
| **Branching** | No | Sì ($0.32/ora) | Sì | Sì |
| **Log Retention** | 1 giorno | 7 giorni | 28 giorni | 90+ giorni |

### 14.2 Compute Add-ons

| Size | CPU | RAM | Prezzo/mese | Connessioni Dirette | Pool |
|---|---|---|---|---|---|
| Nano (Free) | Shared | 512 MB | $0 | 60 | 200 |
| Micro | Shared | 1 GB | $7 | 60 | 200 |
| Small | 2-core ARM | 2 GB | $25 | 90 | 400 |
| Medium | 2-core ARM | 4 GB | $50 | 120 | 600 |
| Large | 2-core ARM | 8 GB | $100 | 160 | 800 |
| XL | 4-core ARM | 16 GB | $200 | 240 | 1200 |
| 2XL | 8-core ARM | 32 GB | $400 | 380 | 1500 |
| 4XL | 16-core ARM | 64 GB | $950 | 480 | 3000 |
| 8XL | 32-core ARM | 128 GB | $3,200 | 490 | 3000 |

### 14.3 Stima Costi per Vendiamonoi.it

**Scenario: Piano Pro + Medium Compute**

| Voce | Stima Mensile |
|---|---|
| Piano Pro base | $25 |
| Medium Compute (4GB RAM) | $50 |
| Database extra 20GB | $2.50 |
| Storage extra 50GB | $1.05 |
| Bandwidth extra 100GB | $9.00 |
| Edge Functions extra 1M | $2.00 |
| Custom Domain | $10 |
| **Totale stimato** | **~$100/mese** |

Per la scala di Vendiamonoi.it (milioni di SKU, 100+ fornitori, 20+ marketplace), il piano Pro con compute Medium/Large è il punto di partenza consigliato, con possibilità di scaling verticale on-demand.

---

## 15. Infrastruttura e Regioni

### 15.1 Regioni AWS Disponibili

| Regione | Codice | Località |
|---|---|---|
| US East (N. Virginia) | us-east-1 | Virginia, USA |
| US East (Ohio) | us-east-2 | Ohio, USA |
| US West (Oregon) | us-west-1 | Oregon, USA |
| US West (N. California) | us-west-2 | California, USA |
| Canada (Central) | ca-central-1 | Montreal, Canada |
| EU West (Ireland) | eu-west-1 | Irlanda |
| EU West (London) | eu-west-2 | Londra, UK |
| EU West (Paris) | eu-west-3 | Parigi, Francia |
| EU Central (Frankfurt) | eu-central-1 | Francoforte, Germania |
| EU Central (Zurich) | eu-central-2 | Zurigo, Svizzera |
| EU North (Stockholm) | eu-north-1 | Stoccolma, Svezia |
| AP Southeast (Singapore) | ap-southeast-1 | Singapore |
| AP Southeast (Sydney) | ap-southeast-2 | Sydney, Australia |
| AP Northeast (Tokyo) | ap-northeast-1 | Tokyo, Giappone |
| AP Northeast (Seoul) | ap-northeast-2 | Seoul, Corea |
| AP South (Mumbai) | ap-south-1 | Mumbai, India |
| South America (São Paulo) | sa-east-1 | São Paolo, Brasile |

**Progetti Vendiamonoi.it:**
- Prodotti: **eu-west-3** (Parigi) — vicino all'Italia, compliance GDPR
- Orders: **eu-west-1** (Irlanda) — datacenter principale AWS EU

### 15.2 Self-Hosting

Supabase può essere self-hosted via Docker Compose:

```bash
# Clone repository
git clone --depth 1 https://github.com/supabase/supabase
cd supabase/docker

# Copiare configurazione
cp .env.example .env
# Editare .env con le proprie chiavi

# Avviare
docker compose up -d

# Servizi inclusi:
# - PostgreSQL 17
# - PostgREST
# - GoTrue (Auth)
# - Storage
# - Realtime
# - Kong (API Gateway)
# - Studio (Dashboard)
# - pg_meta
# - Logflare (Logs)
```

---

## 16. pgvector — Ricerca Vettoriale AI

### 16.1 Setup

```sql
-- Abilitare pgvector
CREATE EXTENSION IF NOT EXISTS vector;

-- Tabella con embedding
CREATE TABLE product_embeddings (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID REFERENCES products(id) ON DELETE CASCADE,
  content TEXT NOT NULL,
  embedding vector(1536),  -- OpenAI text-embedding-3-small
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Indice per ricerca approssimata (IVFFlat)
CREATE INDEX idx_product_embeddings ON product_embeddings
USING ivfflat (embedding vector_cosine_ops)
WITH (lists = 100);

-- Oppure indice HNSW (più veloce per recall elevato)
CREATE INDEX idx_product_embeddings_hnsw ON product_embeddings
USING hnsw (embedding vector_cosine_ops)
WITH (m = 16, ef_construction = 64);
```

### 16.2 Ricerca Semantica

```sql
-- Funzione di ricerca semantica
CREATE OR REPLACE FUNCTION search_products_semantic(
  query_embedding vector(1536),
  match_threshold FLOAT DEFAULT 0.7,
  match_count INT DEFAULT 10
)
RETURNS TABLE (
  product_id UUID,
  content TEXT,
  similarity FLOAT
) AS $$
SELECT
  pe.product_id,
  pe.content,
  1 - (pe.embedding <=> query_embedding) as similarity
FROM product_embeddings pe
WHERE 1 - (pe.embedding <=> query_embedding) > match_threshold
ORDER BY pe.embedding <=> query_embedding
LIMIT match_count;
$$ LANGUAGE sql;

-- Uso via RPC:
-- POST /rest/v1/rpc/search_products_semantic
-- Body: {"query_embedding": [0.1, 0.2, ...], "match_threshold": 0.7, "match_count": 20}
```

**Caso d'uso Vendiamonoi.it:** matching automatico tra prodotti di fornitori diversi basato su similarità semantica delle descrizioni, identificazione duplicati nel catalogo, ricerca prodotti in linguaggio naturale.

---

## 17. Foreign Data Wrappers (FDW)

Supabase supporta FDW per connettere database esterni direttamente da PostgreSQL:

```sql
-- Connettere un database MySQL esterno (es. vecchio sistema ERP)
CREATE EXTENSION IF NOT EXISTS mysql_fdw;

CREATE SERVER erp_server
FOREIGN DATA WRAPPER mysql_fdw
OPTIONS (host 'erp.example.com', port '3306');

CREATE USER MAPPING FOR postgres
SERVER erp_server
OPTIONS (username 'readonly', password 'secret');

CREATE FOREIGN TABLE erp_products (
  id INTEGER,
  sku VARCHAR(50),
  name VARCHAR(200),
  price DECIMAL(10,2)
)
SERVER erp_server
OPTIONS (dbname 'erp', table_name 'products');

-- Ora puoi fare JOIN tra tabelle locali e remote
SELECT p.ean, e.name, e.price
FROM products p
JOIN erp_products e ON p.erp_sku = e.sku;
```

**FDW disponibili:** PostgreSQL, MySQL, Firebase, Stripe, S3, BigQuery, ClickHouse, Airtable.

---

## 18. Migrazione da Altri Sistemi

### 18.1 Da Firebase

```bash
# Tool di migrazione ufficiale
npx supabase-migrate firebase \\
  --firebase-project my-firebase-project \\
  --supabase-project-ref [ref] \\
  --supabase-access-token [token]

# Migra: Auth users, Firestore → PostgreSQL, Storage files
```

### 18.2 Da MySQL/PostgreSQL Esterno

```bash
# Via pg_dump/pg_restore (PostgreSQL)
pg_dump -h old-host -d old-db -F c -f backup.dump
pg_restore -h db.[ref].supabase.co -d postgres -U postgres backup.dump

# Via pgloader (MySQL → PostgreSQL)
pgloader mysql://user:pass@old-host/db \\
  postgresql://postgres:pass@db.[ref].supabase.co:5432/postgres
```

### 18.3 Da Google Sheets

Pattern con Make:

```
Google Sheets (sorgente)
  → Make: Watch Rows
  → Transform (mapping colonne)
  → Supabase: Upsert Row
  → Ogni modifica al foglio viene sincronizzata
```

---

## 19. Pattern Architetturali per Vendiamonoi.it

### 19.1 Architettura Complessiva

```
                    ┌──────────────────┐
                    │  MARKETPLACE API  │
                    │  (Amazon, eBay,   │
                    │   Kaufland, etc.) │
                    └────────┬─────────┘
                             │
                    ┌────────▼─────────┐
                    │    Make.com       │
                    │  (Orchestrator)   │
                    └────────┬─────────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
┌───────▼───────┐   ┌───────▼───────┐   ┌───────▼───────┐
│   Supabase    │   │   Channable/  │   │   Google      │
│  (Database +  │   │ ChannelEngine │   │   Sheets      │
│   API + Auth) │   │  (Feed Mgmt)  │   │  (Reporting)  │
└───────┬───────┘   └───────────────┘   └───────────────┘
        │
        ├── PostgreSQL (catalogo, ordini, fornitori)
        ├── Storage (immagini, documenti, feed)
        ├── Edge Functions (logica business)
        ├── Realtime (dashboard live)
        └── Auth (accesso fornitori/team)
```

### 19.2 Flusso Ordine Completo

```
1. Marketplace riceve ordine
   └─→ Webhook/API → Make scenario "New Order"
       └─→ Supabase INSERT orders (via service_role)
           └─→ Trigger: notify_new_order()
               └─→ pg_net → Make webhook "Process Order"
                   ├─→ Identifica fornitore
                   ├─→ Supabase UPDATE orders (status: confirmed)
                   ├─→ Email/notifica al fornitore
                   └─→ Crea DDT/etichetta spedizione

2. Fornitore spedisce
   └─→ Make scenario "Tracking Update"
       └─→ Supabase UPDATE orders (tracking, shipped_at)
           └─→ Trigger: webhook_on_order_status_change()
               └─→ Make → Marketplace API (upload tracking)

3. Consegna completata
   └─→ Marketplace webhook → Make
       └─→ Supabase UPDATE orders (status: delivered)
           └─→ Trigger: genera fattura, aggiorna KPI
```

### 19.3 Gestione Catalogo Scalabile

```sql
-- Struttura ottimizzata per milioni di prodotti
CREATE TABLE product_catalog (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  ean VARCHAR(13) UNIQUE NOT NULL,
  supplier_id UUID REFERENCES suppliers(id),
  brand TEXT,
  category_path TEXT[],          -- ['Elettronica', 'Aspirapolvere', 'Cordless']
  title JSONB NOT NULL,          -- Multilingua
  description JSONB,
  bullet_points JSONB,           -- {"it": ["punto1", "punto2"], "fr": [...]}
  attributes JSONB,              -- Attributi dinamici per categoria
  images TEXT[],
  main_image TEXT,
  price_purchase NUMERIC(12,2),
  price_recommended NUMERIC(12,2),
  weight_kg NUMERIC(10,3),
  dimensions JSONB,
  stock_quantity INTEGER DEFAULT 0,
  stock_status TEXT DEFAULT 'in_stock',
  status TEXT DEFAULT 'draft',
  feed_enabled BOOLEAN DEFAULT false,
  marketplace_data JSONB DEFAULT '{}',  -- Dati specifici per marketplace
  seo_title JSONB,
  seo_keywords JSONB,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  fts tsvector GENERATED ALWAYS AS (
    setweight(to_tsvector('simple', coalesce(ean, '')), 'A') ||
    setweight(to_tsvector('italian', coalesce(title->>'it', '')), 'B') ||
    setweight(to_tsvector('italian', coalesce(description->>'it', '')), 'C')
  ) STORED
);

-- Indici per performance su milioni di righe
CREATE INDEX idx_catalog_ean ON product_catalog(ean);
CREATE INDEX idx_catalog_supplier ON product_catalog(supplier_id);
CREATE INDEX idx_catalog_brand ON product_catalog(brand);
CREATE INDEX idx_catalog_status ON product_catalog(status);
CREATE INDEX idx_catalog_category ON product_catalog USING GIN(category_path);
CREATE INDEX idx_catalog_attributes ON product_catalog USING GIN(attributes);
CREATE INDEX idx_catalog_fts ON product_catalog USING GIN(fts);
CREATE INDEX idx_catalog_created ON product_catalog(created_at DESC);
CREATE INDEX idx_catalog_stock ON product_catalog(stock_status) WHERE feed_enabled = true;

-- Partitioning per tabella ordini (per mese)
CREATE TABLE orders_partitioned (
  id UUID DEFAULT gen_random_uuid(),
  marketplace_id UUID,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  -- ... altre colonne
  PRIMARY KEY (id, created_at)
) PARTITION BY RANGE (created_at);

CREATE TABLE orders_2024_01 PARTITION OF orders_partitioned
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
CREATE TABLE orders_2024_02 PARTITION OF orders_partitioned
  FOR VALUES FROM ('2024-02-01') TO ('2024-03-01');
-- etc.
```

### 19.4 Multi-Tenancy per Fornitori

```sql
-- Pattern RLS per accesso fornitore isolato
CREATE POLICY "supplier_isolation_select"
ON product_catalog FOR SELECT
USING (
  supplier_id = (auth.jwt() -> 'user_metadata' ->> 'supplier_id')::UUID
  OR (auth.jwt() -> 'user_metadata' ->> 'role') = 'admin'
);

CREATE POLICY "supplier_isolation_insert"
ON product_catalog FOR INSERT
WITH CHECK (
  supplier_id = (auth.jwt() -> 'user_metadata' ->> 'supplier_id')::UUID
  OR (auth.jwt() -> 'user_metadata' ->> 'role') = 'admin'
);

CREATE POLICY "supplier_isolation_update"
ON product_catalog FOR UPDATE
USING (
  supplier_id = (auth.jwt() -> 'user_metadata' ->> 'supplier_id')::UUID
  OR (auth.jwt() -> 'user_metadata' ->> 'role') = 'admin'
);

-- Vista fornitore: dashboard personalizzata
CREATE OR REPLACE FUNCTION get_my_products_summary()
RETURNS JSON AS $$
SELECT json_build_object(
  'total_products', COUNT(*),
  'active', COUNT(*) FILTER (WHERE status = 'active'),
  'draft', COUNT(*) FILTER (WHERE status = 'draft'),
  'out_of_stock', COUNT(*) FILTER (WHERE stock_quantity = 0),
  'avg_price', ROUND(AVG(price_recommended), 2),
  'total_value', ROUND(SUM(price_purchase * stock_quantity), 2)
)
FROM product_catalog
WHERE supplier_id = (auth.jwt() -> 'user_metadata' ->> 'supplier_id')::UUID;
$$ LANGUAGE sql SECURITY INVOKER;
```

---

## 20. Troubleshooting e Limiti

### 20.1 Limiti Noti

| Risorsa | Limite Free | Limite Pro |
|---|---|---|
| Database size | 500 MB | 8 GB + espandibile |
| File upload singolo | 50 MB | 5 GB |
| Storage totale | 1 GB | 100 GB + espandibile |
| Edge Function timeout | 2 sec (inbound), 150 sec (background) | 150 sec |
| Edge Function memory | 256 MB | 256 MB (512 MB su richiesta) |
| Realtime connessioni | 200 | 500 |
| Realtime msg/sec | 100 | 500 |
| API requests/sec | 100 | 1000 |
| pg_cron job concorrenti | 8 | 8 |
| pg_cron timeout per job | 10 min | 10 min |
| Progetti | 2 | Illimitati |
| Branching | No | Sì |

### 20.2 Errori Comuni e Soluzioni

| Errore | Causa | Soluzione |
|---|---|---|
| `JWT expired` | Token scaduto | Refresh token automatico via SDK, o generare nuovo token |
| `Permission denied for table` | RLS blocca accesso | Verificare RLS policies, usare service_role per admin |
| `Connection timeout` | Progetto in pausa o rete | Riattivare progetto, verificare IP allowlist |
| `Too many connections` | Pool esaurito | Usare Supavisor (connection pooling), scalare compute |
| `Row size exceeds maximum` | Riga troppo grande (8KB) | Usare TOAST, normalizzare dati, Storage per file |
| `Relation does not exist` | Tabella non nello schema esposto | Verificare schema, esporre via API settings |
| `Could not find function` | Funzione non esposta | Verificare SECURITY DEFINER/INVOKER, schema |
| `Rate limit exceeded` | Troppe richieste | Implementare backoff, upgrade piano |
| `Storage quota exceeded` | Spazio esaurito | Cleanup file, upgrade piano |

### 20.3 Performance Tips

```sql
-- 1. Analizzare query lente
EXPLAIN ANALYZE SELECT * FROM products WHERE attributes @> '{"color": "red"}';

-- 2. Verificare utilizzo indici
SELECT schemaname, tablename, indexname, idx_scan, idx_tup_read, idx_tup_fetch
FROM pg_stat_user_indexes
ORDER BY idx_scan DESC;

-- 3. Identificare tabelle con bloat
SELECT schemaname, tablename, pg_size_pretty(pg_total_relation_size(schemaname || '.' || tablename))
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname || '.' || tablename) DESC;

-- 4. Vacuum e analyze
VACUUM ANALYZE products;

-- 5. Query optimization per grandi dataset
-- Usare cursor-based pagination invece di OFFSET
SELECT * FROM products
WHERE created_at < '2024-06-15T10:30:00Z'  -- cursor dell'ultimo risultato
ORDER BY created_at DESC
LIMIT 50;
```

---

## 21. Risorse e Riferimenti

### 21.1 Documentazione Ufficiale

| Risorsa | URL |
|---|---|
| Documentazione principale | https://supabase.com/docs |
| Guida database | https://supabase.com/docs/guides/database |
| Guida auth | https://supabase.com/docs/guides/auth |
| Guida storage | https://supabase.com/docs/guides/storage |
| Guida edge functions | https://supabase.com/docs/guides/functions |
| Guida realtime | https://supabase.com/docs/guides/realtime |
| API reference JS | https://supabase.com/docs/reference/javascript |
| API reference Python | https://supabase.com/docs/reference/python |
| CLI reference | https://supabase.com/docs/reference/cli |
| Self-hosting | https://supabase.com/docs/guides/self-hosting |
| Management API | https://supabase.com/docs/reference/api |

### 21.2 Community e Supporto

| Canale | URL |
|---|---|
| GitHub Discussions | https://github.com/supabase/supabase/discussions |
| Discord | https://discord.supabase.com |
| Twitter/X | https://twitter.com/supabase |
| YouTube | https://youtube.com/@supabase |
| Blog | https://supabase.com/blog |

### 21.3 Tool Complementari

| Tool | Integrazione |
|---|---|
| **Prisma** | ORM con supporto Supabase |
| **Drizzle** | ORM TypeScript leggero |
| **Terraform** | Provider per infrastruttura as code |
| **Pulumi** | IaC alternativo a Terraform |
| **Grafana** | Dashboard monitoring custom |
| **Stripe** | Sync Engine per dati pagamento |

---

## Changelog

| Data | Versione | Modifica |
|---|---|---|
| 2026-04-01 | 1.0 | Creazione documentazione completa |