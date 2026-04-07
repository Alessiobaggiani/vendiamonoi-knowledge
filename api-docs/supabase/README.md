# Supabase — Documentazione Tecnica Approfondita

> **Documentazione tecnico-operativa per Vendiamonoi.it S.R.L.**
> Supabase come database primario dello stack aziendale di distribuzione digitale

---

## Metadata

- **Ultimo aggiornamento**: 2026-04-07
- **Versione documento**: 1.0
- **Autore**: Knowledge Base Team Vendiamonoi
- **Priorità**: P3.2
- **Target audience**: Sviluppatori backend, DevOps engineers, system architects
- **Scope**: Supabase come hub dati centralizzato per Vendiamonoi.it

---

## Indice Generale

1. [Executive Summary](#1-executive-summary)
2. [Architettura della Piattaforma Supabase](#2-architettura-della-piattaforma-supabase)
3. [Autenticazione e Autorizzazione (GoTrue)](#3-autenticazione-e-autorizzazione-gotrue)
4. [PostgREST API — CRUD Auto-Generato](#4-postgrest-api--crud-auto-generato)
5. [Client Libraries e SDK](#5-client-libraries-e-sdk)
6. [Supabase Realtime](#6-supabase-realtime)
7. [Storage API per Media](#7-storage-api-per-media)
8. [Edge Functions (Serverless Deno)](#8-edge-functions-serverless-deno)
9. [Database Functions e RPC](#9-database-functions-e-rpc)
10. [Row Level Security (RLS)](#10-row-level-security-rls)
11. [Supabase CLI e Local Development](#11-supabase-cli-e-local-development)
12. [Database Webhooks e pg_net](#12-database-webhooks-e-pgnet)
13. [Integrazione Make.com + Supabase](#13-integrazione-makecom--supabase)
14. [Integrazione Shopify + Supabase](#14-integrazione-shopify--supabase)
15. [Integrazione Notion + Supabase (Bidirectional)](#15-integrazione-notion--supabase-bidirectional)
16. [Integrazione Stack Completo Vendiamonoi](#16-integrazione-stack-completo-vendiamonoi)
17. [Schema Database per E-Commerce Marketplace](#17-schema-database-per-e-commerce-marketplace)
18. [Performance e Scaling per Marketplace](#18-performance-e-scaling-per-marketplace)
19. [Full-Text Search Multilingue](#19-full-text-search-multilingue)
20. [Backup, Sicurezza e Monitoring](#20-backup-sicurezza-e-monitoring)

---

# 1. Executive Summary

Supabase è una piattaforma **backend-as-a-service (BaaS)** costruita su **PostgreSQL**, che offre:
- Database relazionale gestito con scalabilità orizzontale
- API REST auto-generata (PostgREST) per CRUD operations
- Autenticazione integrata (GoTrue) con provider OAuth/SAML
- Storage per media files (basato su S3-compatible)
- Realtime subscriptions via WebSocket
- Edge Functions per logica serverless (Deno runtime)
- Row Level Security per autorizzazione a livello database

Nell'architettura di **Vendiamonoi.it**, Supabase funge da **hub dati centralizzato** che integra:
- E-commerce marketplace (Shopify)
- Workflow automation (Make.com)
- Knowledge management (Notion)
- Custom business logic (Edge Functions e Database Functions)

**Obiettivo**: Fornire una base dati robusta, scalabile e sicura che supporti la crescita del marketplace digitale.

---

# 2. Architettura della Piattaforma Supabase

## 2.1 Componenti Core

### PostgreSQL Database
- Versione: 15.x+
- Estensioni attive: `uuid-ossp`, `pgjwt`, `pgcrypto`, `http`, `pg_net`
- Scalabilità: Read replicas + connection pooling (pgBouncer)
- Backup: Automated daily + point-in-time recovery (PITR)

### PostgREST API Layer
- Converte schema PostgreSQL in API REST
- Supporta:
  - CRUD operations standard (GET, POST, PATCH, DELETE)
  - Filtering, sorting, pagination
  - RLS enforcement (Row Level Security)
  - Custom headers e content negotiation

### GoTrue Authentication
- OAuth providers: Google, GitHub, Azure AD, Apple
- SAML 2.0 support
- Magic link authentication
- Custom JWT claims e refresh token logic

### Supabase Realtime
- WebSocket connection per subscriptions
- Presenze online tracking
- Broadcast channels per messaggistica
- Automatic reconnection con exponential backoff

### Storage API
- S3-compatible storage per media
- Integrazione con CDN (Cloudflare)
- Signed URLs per accesso temporaneo
- Server-side encryption at rest

### Edge Functions
- Deno runtime serverless
- Deploy con versioning semantico
- Environment variables per config
- Logging centralizzato e monitoring

## 2.2 Flusso Dati Architetturale

```
┌─────────────────────────────────────────────────────────────┐
│                    Client Applications                        │
│  (Web, Mobile, Desktop, Make.com, External APIs)             │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
        ┌──────────────────────────┐
        │  Supabase API Gateway    │
        │   (PostgREST + Auth)     │
        └──────┬───────────────────┘
               │
         ┌─────┴──────────────────────┐
         │                            │
         ▼                            ▼
    ┌─────────────┐          ┌──────────────────┐
    │ PostgreSQL  │          │ Edge Functions   │
    │  Database   │◄────────►│   (Deno)         │
    └─────────────┘          └──────────────────┘
         │                            │
         │                            │
    ┌────┴──────┐              ┌──────┴────────┐
    │   Storage │              │ Webhooks &    │
    │  (S3 CDN) │              │ pg_net        │
    └───────────┘              └───────────────┘
         │                            │
         └────────────┬───────────────┘
                      │
                      ▼
         ┌─────────────────────────┐
         │  External Integrations  │
         │ (Make, Shopify, Notion) │
         └─────────────────────────┘
```

---

# 3. Autenticazione e Autorizzazione (GoTrue)

## 3.1 Flusso di Autenticazione

### 1. Sign-up con Email
```javascript
const { user, session } = await supabase.auth.signUp({
  email: 'user@example.com',
  password: 'securePassword123',
  options: {
    emailRedirectTo: 'https://vendiamonoi.it/auth/callback',
    data: {
      first_name: 'John',
      last_name: 'Doe',
      role: 'customer' // Custom claim
    }
  }
});
```

### 2. Sign-in
```javascript
const { user, session } = await supabase.auth.signInWithPassword({
  email: 'user@example.com',
  password: 'securePassword123'
});

// Session include JWT access token + refresh token
console.log(session.access_token); // Valido per 1 ora
console.log(session.refresh_token); // Valido per 7 giorni
```

### 3. OAuth (Google)
```javascript
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: 'google',
  options: {
    redirectTo: 'https://vendiamonoi.it/auth/callback'
  }
});
```

## 3.2 JWT Structure

```json
{
  "aud": "authenticated",
  "exp": 1704067200,
  "iat": 1704063600,
  "iss": "https://project.supabase.co",
  "sub": "user-uuid-here",
  "email": "user@example.com",
  "email_confirmed_at": "2024-01-01T10:00:00.000Z",
  "phone": "",
  "phone_confirmed_at": null,
  "user_metadata": {
    "first_name": "John",
    "last_name": "Doe"
  },
  "app_metadata": {
    "provider": "email",
    "providers": ["email"]
  }
}
```

## 3.3 Session Management

### Token Refresh
```javascript
// Refresh token scaduto > 7 giorni
const { data, error } = await supabase.auth.refreshSession();

if (error) {
  // Redirect to login
  window.location.href = '/login';
}
```

### Sign Out
```javascript
await supabase.auth.signOut();
// Chiude la sessione, invalida i token
```

## 3.4 Custom Claims e User Metadata

```sql
-- Nel database: aggiungere custom claims
UPDATE auth.users
SET user_metadata = jsonb_set(
  user_metadata,
  '{role}',
  '"seller"'::jsonb
)
WHERE id = 'user-uuid';
```

```javascript
// Nel JWT saranno disponibili come:
const { data: { user } } = await supabase.auth.getUser();
console.log(user.user_metadata.role); // 'seller'
```

---

# 4. PostgREST API — CRUD Auto-Generato

## 4.1 Configurazione Automatica

Ogni tabella nel database PostgreSQL viene automaticamente esposta come endpoint REST:

```
GET     /rest/v1/products
GET     /rest/v1/products/:id
POST    /rest/v1/products
PATCH   /rest/v1/products/:id
DELETE  /rest/v1/products/:id
```

## 4.2 CRUD Operations

### CREATE (POST)
```javascript
const { data, error } = await supabase
  .from('products')
  .insert([
    {
      name: 'Laptop Dell',
      price: 899.99,
      category: 'electronics',
      stock: 15
    }
  ])
  .select();

if (error) {
  console.error('Error:', error.message);
} else {
  console.log('Created:', data[0].id);
}
```

### READ (GET)
```javascript
// Fetch singolo record
const { data, error } = await supabase
  .from('products')
  .select()
  .eq('id', 1)
  .single();

// Fetch lista con filtri
const { data, error } = await supabase
  .from('products')
  .select()
  .gte('price', 100)
  .lt('price', 1000)
  .order('price', { ascending: true })
  .limit(10);
```

### UPDATE (PATCH)
```javascript
const { data, error } = await supabase
  .from('products')
  .update({
    price: 799.99,
    stock: 20
  })
  .eq('id', 1)
  .select();
```

### DELETE
```javascript
const { data, error } = await supabase
  .from('products')
  .delete()
  .eq('id', 1);
```

## 4.3 Query Avanzate

### Filtering
```javascript
// Multiple conditions (AND logic)
const { data } = await supabase
  .from('products')
  .select()
  .gte('price', 100)          // price >= 100
  .lte('price', 1000)         // AND price <= 1000
  .eq('category', 'electronics'); // AND category = 'electronics'

// OR logic via filter function
const { data } = await supabase
  .from('products')
  .select()
  .or(`category.eq.electronics,category.eq.software`);

// Text search
const { data } = await supabase
  .from('products')
  .select()
  .ilike('name', '%laptop%'); // Case-insensitive LIKE
```

### Sorting & Pagination
```javascript
const { data } = await supabase
  .from('products')
  .select()
  .order('price', { ascending: false })
  .range(0, 9); // Pagina 1 (items 0-9)

// Pagina 2
const { data } = await supabase
  .from('products')
  .select()
  .order('price', { ascending: false })
  .range(10, 19); // Items 10-19
```

### Relationships (JOIN)
```javascript
const { data } = await supabase
  .from('orders')
  .select(`
    id,
    created_at,
    customer:customer_id(name, email),
    order_items(
      id,
      quantity,
      product:product_id(name, price)
    )
  `)
  .eq('id', 123);

// Result:
// {
//   id: 123,
//   created_at: '2024-01-01T10:00:00',
//   customer: { name: 'John', email: 'john@example.com' },
//   order_items: [
//     {
//       id: 1,
//       quantity: 2,
//       product: { name: 'Laptop', price: 899.99 }
//     }
//   ]
// }
```

## 4.4 Count e Aggregazioni

```javascript
// Count records
const { count, error } = await supabase
  .from('products')
  .select('*', { count: 'exact', head: true })
  .gte('price', 100);

console.log(`Total products >= $100: ${count}`);

// Aggregates tramite Database Functions (vedi sezione 9)
```

---

# 5. Client Libraries e SDK

## 5.1 JavaScript/TypeScript (@supabase/supabase-js)

### Installation
```bash
npm install @supabase/supabase-js
```

### Initialization
```typescript
import { createClient } from '@supabase/supabase-js';

const supabase = createClient(
  'https://project-ref.supabase.co',
  'public-anon-key'
);

// Per server-side (con service role key):
const supabaseAdmin = createClient(
  'https://project-ref.supabase.co',
  'service-role-key'
);
```

### Autenticazione
```typescript
// Sign Up
const { data, error } = await supabase.auth.signUp({
  email: 'user@example.com',
  password: 'password123'
});

// Sign In
const { data, error } = await supabase.auth.signInWithPassword({
  email: 'user@example.com',
  password: 'password123'
});

// Get current user
const { data: { user } } = await supabase.auth.getUser();

// Sign Out
await supabase.auth.signOut();
```

## 5.2 Python (supabase-py)

### Installation
```bash
pip install supabase
```

### Usage
```python
from supabase import create_client, Client

url = "https://project-ref.supabase.co"
key = "public-anon-key"

supabase: Client = create_client(url, key)

# Select
response = supabase.table("products").select("*").execute()
print(response.data)

# Insert
data = {
    "name": "Laptop",
    "price": 899.99,
    "category": "electronics"
}
response = supabase.table("products").insert([data]).execute()

# Update
response = supabase.table("products").update({"price": 799.99}).eq("id", 1).execute()

# Delete
response = supabase.table("products").delete().eq("id", 1).execute()
```

## 5.3 React (@supabase/auth-helpers-react)

### Setup
```bash
npm install @supabase/auth-helpers-react @supabase/supabase-js
```

### Provider
```jsx
import { SessionContextProvider } from '@supabase/auth-helpers-react';
import { supabase } from './supabaseClient';

export default function App() {
  return (
    <SessionContextProvider supabaseClient={supabase}>
      {/* Your app */}
    </SessionContextProvider>
  );
}
```

### Hook Usage
```jsx
import { useSession } from '@supabase/auth-helpers-react';
import { useEffect, useState } from 'react';

export function MyComponent() {
  const session = useSession();
  const [products, setProducts] = useState([]);

  useEffect(() => {
    if (session) {
      // Fetch products
    }
  }, [session]);

  return (
    <div>
      {session ? (
        <p>Logged in as {session.user.email}</p>
      ) : (
        <p>Not logged in</p>
      )}
    </div>
  );
}
```

## 5.4 Dart (supabase-flutter)

### Installation
```pubspec.yaml
dependencies:
  supabase_flutter: ^1.x
```

### Usage
```dart
import 'package:supabase_flutter/supabase_flutter.dart';

final supabase = Supabase.instance.client;

// Select
final data = await supabase.from('products').select();

// Insert
await supabase.from('products').insert({
  'name': 'Laptop',
  'price': 899.99,
  'category': 'electronics'
});

// Update
await supabase.from('products').update({
  'price': 799.99
}).eq('id', 1);

// Delete
await supabase.from('products').delete().eq('id', 1);
```

---

# 6. Supabase Realtime

## 6.1 Realtime Subscriptions

### Broadcast Channels
```javascript
const channel = supabase.channel('my-channel');

channel
  .on('broadcast', { event: 'inventory-updated' }, (payload) => {
    console.log('Inventory updated:', payload.new_stock);
  })
  .subscribe((status) => {
    console.log(`Channel status: ${status}`);
  });

// Publish message
supabase.channel('my-channel').send({
  type: 'broadcast',
  event: 'inventory-updated',
  payload: { product_id: 1, new_stock: 25 }
});
```

### Presence (User Status)
```javascript
const channel = supabase.channel('seller-activity');

channel.on('presence', { event: 'sync' }, () => {
  const presenceState = channel.presenceState();
  console.log('Active sellers:', presenceState);
}).on('presence', { event: 'join' }, ({ key, newPresences }) => {
  console.log('Seller joined:', newPresences[0]);
}).on('presence', { event: 'leave' }, ({ key, leftPresences }) => {
  console.log('Seller left:', leftPresences[0]);
}).subscribe(async (status) => {
  if (status === 'SUBSCRIBED') {
    await channel.track({
      online_at: new Date(),
      user_id: session.user.id,
      status: 'online'
    });
  }
});
```

## 6.2 Database Changes (Realtime)

```javascript
// Subscribe to INSERT, UPDATE, DELETE events
const subscription = supabase
  .channel('schema-db-changes')
  .on(
    'postgres_changes',
    {
      event: '*', // all events: INSERT, UPDATE, DELETE
      schema: 'public',
      table: 'products'
    },
    (payload) => {
      if (payload.eventType === 'INSERT') {
        console.log('New product:', payload.new);
      } else if (payload.eventType === 'UPDATE') {
        console.log('Product updated:', payload.new);
      } else if (payload.eventType === 'DELETE') {
        console.log('Product deleted:', payload.old);
      }
    }
  )
  .subscribe();

// Unsubscribe
supabase.removeChannel(subscription);
```

## 6.3 Realtime Performance Tuning

```javascript
// Limit subscriptions with WHERE clause
const subscription = supabase
  .channel('seller-inventory')
  .on(
    'postgres_changes',
    {
      event: 'UPDATE',
      schema: 'public',
      table: 'products',
      filter: `seller_id=eq.${userId}` // Only this seller
    },
    (payload) => {
      console.log('Your product updated:', payload.new);
    }
  )
  .subscribe();
```

---

# 7. Storage API per Media

## 7.1 Setup Storage Bucket

```sql
-- Create bucket for product images
insert into storage.buckets (id, name, public)
values ('product-images', 'product-images', true);

-- Create bucket for private user documents
insert into storage.buckets (id, name, public)
values ('user-documents', 'user-documents', false);
```

## 7.2 Upload Files

```javascript
const file = new File(['hello'], 'hello.txt', { type: 'text/plain' });

// Upload public file
const { data, error } = await supabase.storage
  .from('product-images')
  .upload(`products/123/image.jpg`, file, {
    cacheControl: '3600',
    upsert: false // Skip if exists
  });

if (error) {
  console.error('Upload failed:', error.message);
} else {
  console.log('File uploaded:', data.path);
}
```

## 7.3 Download Files

```javascript
// Download private file (requires auth)
const { data, error } = await supabase.storage
  .from('user-documents')
  .download('documents/invoice-2024.pdf');

if (data) {
  const url = URL.createObjectURL(data);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'invoice.pdf';
  a.click();
}
```

## 7.4 Generate Public URLs

```javascript
// Public URL for product images
const { data } = supabase.storage
  .from('product-images')
  .getPublicUrl('products/123/image.jpg');

console.log('Public URL:', data.publicUrl);
// https://project-ref.supabase.co/storage/v1/object/public/product-images/products/123/image.jpg

// Signed URL for private files (expires in 1 hour)
const { data, error } = await supabase.storage
  .from('user-documents')
  .createSignedUrl('documents/invoice-2024.pdf', 3600);

if (data) {
  console.log('Signed URL:', data.signedUrl);
  // https://project-ref.supabase.co/storage/v1/object/sign/user-documents/...?token=xyz
}
```

## 7.5 Delete Files

```javascript
const { error } = await supabase.storage
  .from('product-images')
  .remove(['products/123/image.jpg', 'products/456/image.jpg']);

if (error) {
  console.error('Deletion failed:', error.message);
}
```

## 7.6 Storage RLS Policies

```sql
-- Allow users to upload their own product images
create policy "Users can upload product images"
  on storage.objects for insert
  with check (
    bucket_id = 'product-images'
    and auth.uid() is not null
  );

-- Allow users to download private documents only if owner
create policy "Users can download own documents"
  on storage.objects for select
  using (
    bucket_id = 'user-documents'
    and owner_id = auth.uid()
  );
```

---

# 8. Edge Functions (Serverless Deno)

## 8.1 Create Edge Function

```bash
# Login to Supabase
supabase login

# Create new function
supabase functions new send-email

# Deploy
supabase functions deploy send-email
```

## 8.2 Basic Function Structure

```typescript
// supabase/functions/send-email/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";

serve(async (req) => {
  // CORS
  if (req.method === "OPTIONS") {
    return new Response("ok", { headers: { "Access-Control-Allow-Origin": "*" } });
  }

  try {
    const { email, subject, message } = await req.json();

    // Validate input
    if (!email || !subject || !message) {
      return new Response(
        JSON.stringify({ error: "Missing required fields" }),
        { status: 400, headers: { "Content-Type": "application/json" } }
      );
    }

    // Send email (use service like SendGrid)
    const response = await fetch("https://api.sendgrid.com/v3/mail/send", {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${Deno.env.get("SENDGRID_API_KEY")}`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        personalizations: [{ to: [{ email }] }],
        from: { email: "noreply@vendiamonoi.it" },
        subject,
        content: [{ type: "text/html", value: message }]
      })
    });

    if (!response.ok) {
      throw new Error(`SendGrid error: ${response.statusText}`);
    }

    return new Response(
      JSON.stringify({ success: true, message: "Email sent" }),
      { status: 200, headers: { "Content-Type": "application/json" } }
    );
  } catch (error) {
    console.error(error);
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 500, headers: { "Content-Type": "application/json" } }
    );
  }
});
```

## 8.3 Call Edge Function from Client

```javascript
const { data, error } = await supabase.functions.invoke('send-email', {
  body: {
    email: 'customer@example.com',
    subject: 'Order Confirmation',
    message: '<h1>Your order has been placed!</h1>'
  }
});

if (error) {
  console.error('Function error:', error);
} else {
  console.log('Email sent:', data);
}
```

## 8.4 Edge Function with Auth

```typescript
// supabase/functions/user-profile/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

const SUPABASE_URL = Deno.env.get("SUPABASE_URL");
const SUPABASE_ANON_KEY = Deno.env.get("SUPABASE_ANON_KEY");

serve(async (req) => {
  if (req.method === "OPTIONS") {
    return new Response("ok", { headers: { "Access-Control-Allow-Origin": "*" } });
  }

  try {
    // Get JWT token from Authorization header
    const authHeader = req.headers.get("Authorization");
    if (!authHeader) {
      return new Response(
        JSON.stringify({ error: "No authorization header" }),
        { status: 401 }
      );
    }

    // Create Supabase client with user token
    const token = authHeader.replace("Bearer ", "");
    const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY, {
      global: {
        headers: { Authorization: `Bearer ${token}` }
      }
    });

    // Get current user
    const { data: { user }, error: authError } = await supabase.auth.getUser();

    if (authError || !user) {
      return new Response(
        JSON.stringify({ error: "Unauthorized" }),
        { status: 401 }
      );
    }

    // Fetch user profile
    const { data: profile, error: dbError } = await supabase
      .from('profiles')
      .select()
      .eq('id', user.id)
      .single();

    if (dbError) throw dbError;

    return new Response(
      JSON.stringify({ profile }),
      { status: 200, headers: { "Content-Type": "application/json" } }
    );
  } catch (error) {
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 500 }
    );
  }
});
```

## 8.5 Environment Variables

```bash
# Set environment variables
supabase secrets set SENDGRID_API_KEY sk_live_xxx
supabase secrets set STRIPE_SECRET_KEY sk_live_yyy

# List secrets
supabase secrets list

# Access in function
const apiKey = Deno.env.get("SENDGRID_API_KEY");
```

## 8.6 Logging & Debugging

```typescript
// Logs are visible in supabase functions logs
console.log("Info message");
console.error("Error message");
console.debug("Debug message");

// View logs
// supabase functions logs send-email
```

---

# 9. Database Functions e RPC

## 9.1 Create Database Function

```sql
-- Function to calculate product popularity
CREATE OR REPLACE FUNCTION get_product_popularity(product_id UUID)
RETURNS TABLE (
  product_name TEXT,
  total_views BIGINT,
  total_purchases BIGINT,
  avg_rating NUMERIC,
  popularity_score NUMERIC
) AS $$
BEGIN
  RETURN QUERY
  SELECT
    p.name,
    COALESCE(COUNT(DISTINCT pv.id), 0) as total_views,
    COALESCE(COUNT(DISTINCT oi.id), 0) as total_purchases,
    COALESCE(AVG(r.rating)::NUMERIC, 0) as avg_rating,
    (COALESCE(AVG(r.rating)::NUMERIC, 0) * COALESCE(COUNT(DISTINCT oi.id), 0)) as popularity_score
  FROM products p
  LEFT JOIN product_views pv ON p.id = pv.product_id
  LEFT JOIN order_items oi ON p.id = oi.product_id
  LEFT JOIN reviews r ON p.id = r.product_id
  WHERE p.id = product_id
  GROUP BY p.id, p.name;
END;
$$ LANGUAGE plpgsql;
```

## 9.2 Call Database Function from Client

```javascript
const { data, error } = await supabase.rpc('get_product_popularity', {
  product_id: '123e4567-e89b-12d3-a456-426614174000'
});

if (error) {
  console.error('RPC error:', error);
} else {
  console.log('Product popularity:', data[0]);
  // {
  //   product_name: 'Laptop Dell',
  //   total_views: 1500,
  //   total_purchases: 45,
  //   avg_rating: 4.8,
  //   popularity_score: 216
  // }
}
```

## 9.3 Complex Function Example

```sql
-- Function to process new order and update inventory
CREATE OR REPLACE FUNCTION process_order(
  p_customer_id UUID,
  p_items JSONB -- [{"product_id": "...", "quantity": 2}, ...]
)
RETURNS TABLE (
  order_id UUID,
  total_amount NUMERIC,
  status TEXT
) AS $$
DECLARE
  v_order_id UUID;
  v_item RECORD;
  v_total NUMERIC := 0;
BEGIN
  -- Start transaction
  BEGIN
    -- Create order
    INSERT INTO orders (customer_id, status, created_at)
    VALUES (p_customer_id, 'pending', NOW())
    RETURNING id INTO v_order_id;

    -- Process each item
    FOR v_item IN
      SELECT
        (item->>'product_id')::UUID as product_id,
        (item->>'quantity')::INT as quantity
      FROM jsonb_array_elements(p_items) as item
    LOOP
      -- Check stock
      IF (SELECT stock FROM products WHERE id = v_item.product_id) < v_item.quantity THEN
        RAISE EXCEPTION 'Insufficient stock for product %', v_item.product_id;
      END IF;

      -- Insert order item
      INSERT INTO order_items (order_id, product_id, quantity, unit_price)
      SELECT
        v_order_id,
        v_item.product_id,
        v_item.quantity,
        price
      FROM products
      WHERE id = v_item.product_id
      RETURNING (quantity * unit_price) INTO v_total;

      -- Update inventory
      UPDATE products
      SET stock = stock - v_item.quantity
      WHERE id = v_item.product_id;
    END LOOP;

    -- Update order total
    UPDATE orders
    SET total_amount = v_total
    WHERE id = v_order_id;

    -- Return result
    RETURN QUERY SELECT v_order_id, v_total, 'completed'::TEXT;

  EXCEPTION WHEN OTHERS THEN
    -- Rollback if error
    RAISE;
  END;
END;
$$ LANGUAGE plpgsql;
```

## 9.4 Call Complex Function

```javascript
const { data, error } = await supabase.rpc('process_order', {
  p_customer_id: userId,
  p_items: JSON.stringify([
    { product_id: 'uuid-1', quantity: 2 },
    { product_id: 'uuid-2', quantity: 1 }
  ])
});

if (error) {
  console.error('Order processing failed:', error.message);
  // Handle error (e.g., insufficient stock)
} else {
  const { order_id, total_amount, status } = data[0];
  console.log(`Order ${order_id} created for $${total_amount}`);
}
```

---

# 10. Row Level Security (RLS)

## 10.1 Enable RLS on Tables

```sql
-- Enable RLS
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
ALTER TABLE user_profiles ENABLE ROW LEVEL SECURITY;

-- Create policies

-- 1. Products: Everyone can read published products
CREATE POLICY "Select published products"
  ON products FOR SELECT
  USING (is_published = true);

-- 2. Products: Sellers can update their own products
CREATE POLICY "Sellers can update their products"
  ON products FOR UPDATE
  USING (seller_id = auth.uid())
  WITH CHECK (seller_id = auth.uid());

-- 3. Orders: Users can only see their own orders
CREATE POLICY "Users can view own orders"
  ON orders FOR SELECT
  USING (customer_id = auth.uid());

-- 4. User profiles: Users can only edit their own profile
CREATE POLICY "Users can view own profile"
  ON user_profiles FOR SELECT
  USING (id = auth.uid());

CREATE POLICY "Users can edit own profile"
  ON user_profiles FOR UPDATE
  USING (id = auth.uid())
  WITH CHECK (id = auth.uid());
```

## 10.2 RLS with Custom Claims

```sql
-- Admin can see all orders
CREATE POLICY "Admins can see all orders"
  ON orders FOR SELECT
  USING (
    auth.jwt() ->> 'role' = 'admin'
  );

-- Sellers can see orders for their products
CREATE POLICY "Sellers can see orders for their products"
  ON orders FOR SELECT
  USING (
    EXISTS (
      SELECT 1 FROM order_items oi
      JOIN products p ON oi.product_id = p.id
      WHERE oi.order_id = orders.id
      AND p.seller_id = auth.uid()
    )
  );
```

## 10.3 RLS with Relationships

```sql
-- Members can only see their own team's data
CREATE POLICY "Team members can view team data"
  ON team_projects FOR SELECT
  USING (
    team_id IN (
      SELECT team_id FROM team_members
      WHERE user_id = auth.uid()
    )
  );
```

## 10.4 Testing RLS Policies

```javascript
// Test as authenticated user
const { data, error } = await supabase
  .from('orders')
  .select()
  .eq('customer_id', userId);
// Should work - user can see their own orders

// Test accessing another user's order
const { data: otherOrder, error: deniedError } = await supabase
  .from('orders')
  .select()
  .eq('id', 'other-users-order-id');
// Should return error or empty result

// Test as unauthenticated user
const publicSupabase = createClient(URL, ANON_KEY);
const { data: publicData } = await publicSupabase
  .from('products')
  .select()
  .eq('is_published', true);
// Should work - can see published products
```

---

# 11. Supabase CLI e Local Development

## 11.1 Installation

```bash
# Install Supabase CLI
npm install -g supabase

# Or via Homebrew (macOS)
brew install supabase/tap/supabase
```

## 11.2 Initialize Project

```bash
# Login
supabase login

# Link to remote project
supabase link --project-ref your-project-ref

# Initialize local development
supabase init

# Start local development stack
supabase start

# Stop local stack
supabase stop
```

## 11.3 Database Migrations

```bash
# Create new migration
supabase migration new create_products_table

# Edit the file: supabase/migrations/[timestamp]_create_products_table.sql

# Apply local migrations
supabase db push

# Push to production
supabase db push --remote

# Reset local database
supabase db reset
```

## 11.4 Example Migration File

```sql
-- supabase/migrations/20240101120000_create_products_table.sql

-- Create products table
CREATE TABLE IF NOT EXISTS products (
  id UUID NOT NULL PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  description TEXT,
  price NUMERIC(10, 2) NOT NULL,
  stock INT NOT NULL DEFAULT 0,
  category TEXT,
  seller_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  is_published BOOLEAN DEFAULT false,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);

-- Create indexes
CREATE INDEX idx_products_seller_id ON products(seller_id);
CREATE INDEX idx_products_category ON products(category);
CREATE INDEX idx_products_is_published ON products(is_published);

-- Enable RLS
ALTER TABLE products ENABLE ROW LEVEL SECURITY;

-- Create policies
CREATE POLICY "Products are viewable by everyone if published"
  ON products FOR SELECT
  USING (is_published = true OR seller_id = auth.uid());

CREATE POLICY "Users can insert their own products"
  ON products FOR INSERT
  WITH CHECK (seller_id = auth.uid());

CREATE POLICY "Users can update their own products"
  ON products FOR UPDATE
  USING (seller_id = auth.uid())
  WITH CHECK (seller_id = auth.uid());

CREATE POLICY "Users can delete their own products"
  ON products FOR DELETE
  USING (seller_id = auth.uid());
```

## 11.5 Seed Data

```bash
# Create seed file
supabase seed add seed_data

# Edit: supabase/seed.sql
```

```sql
-- supabase/seed.sql
-- Seed initial data

INSERT INTO products (name, description, price, stock, category, seller_id, is_published)
VALUES
  (
    'Laptop Dell',
    'High-performance laptop for development',
    899.99,
    15,
    'electronics',
    '00000000-0000-0000-0000-000000000001',
    true
  ),
  (
    'Mechanical Keyboard',
    'RGB mechanical keyboard',
    149.99,
    50,
    'electronics',
    '00000000-0000-0000-0000-000000000002',
    true
  );
```

## 11.6 Pull Schema Changes

```bash
# Pull remote schema to local migrations
supabase db pull

# This creates a new migration file with any remote changes
```

---

# 12. Database Webhooks e pg_net

## 12.1 Create Webhook

```sql
-- Create function that triggers webhook
CREATE OR REPLACE FUNCTION notify_order_webhook()
RETURNS TRIGGER AS $$
BEGIN
  -- Send HTTP request when order is created
  PERFORM pg_net.http_post(
    url := 'https://webhook.site/your-unique-id',
    body := jsonb_build_object(
      'event', 'order.created',
      'order_id', NEW.id,
      'customer_id', NEW.customer_id,
      'total_amount', NEW.total_amount,
      'created_at', NEW.created_at
    )
  );
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Create trigger
CREATE TRIGGER order_webhook_trigger
AFTER INSERT ON orders
FOR EACH ROW
EXECUTE FUNCTION notify_order_webhook();
```

## 12.2 Webhook with Authentication

```sql
-- Include signature for webhook verification
CREATE OR REPLACE FUNCTION notify_order_secure()
RETURNS TRIGGER AS $$
DECLARE
  v_secret TEXT := 'your-webhook-secret';
  v_signature TEXT;
  v_payload JSONB;
BEGIN
  v_payload := jsonb_build_object(
    'event', 'order.created',
    'data', NEW
  );

  -- Create HMAC signature
  v_signature := encode(
    hmac(v_payload::TEXT, v_secret, 'sha256'),
    'hex'
  );

  PERFORM pg_net.http_post(
    url := 'https://api.vendiamonoi.it/webhooks/orders',
    body := v_payload,
    headers := jsonb_build_object(
      'Content-Type', 'application/json',
      'X-Signature', v_signature
    )
  );

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

## 12.3 Multiple Event Webhooks

```sql
-- Track inventory changes
CREATE OR REPLACE FUNCTION notify_inventory_webhook()
RETURNS TRIGGER AS $$
BEGIN
  IF OLD.stock != NEW.stock THEN
    PERFORM pg_net.http_post(
      url := 'https://webhook.site/inventory',
      body := jsonb_build_object(
        'event', 'inventory.updated',
        'product_id', NEW.id,
        'old_stock', OLD.stock,
        'new_stock', NEW.stock
      )
    );
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER inventory_webhook_trigger
AFTER UPDATE ON products
FOR EACH ROW
EXECUTE FUNCTION notify_inventory_webhook();
```

---

# 13. Integrazione Make.com + Supabase

## 13.1 Setup Make.com Webhook

1. In Supabase: Create webhook per new orders
2. In Make.com: Create scenario con HTTP trigger
3. Configure Make.com per ascoltare webhook da Supabase

```sql
-- Webhook endpoint in Make.com
-- Example: https://hook.make.com/xyz123abc456

CREATE OR REPLACE FUNCTION send_to_make()
RETURNS TRIGGER AS $$
BEGIN
  PERFORM pg_net.http_post(
    url := 'https://hook.make.com/xyz123abc456',
    body := jsonb_build_object(
      'table', TG_TABLE_NAME,
      'event', TG_OP,
      'data', NEW
    )
  );
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

## 13.2 Make.com Scenarios

### Scenario 1: New Order -> Send Email
1. Trigger: Webhook (Supabase)
2. Action: Email module (SendGrid)
3. Map order data to email template

### Scenario 2: New Customer -> Add to CRM
1. Trigger: Webhook (Supabase - new user)
2. Action: Create contact in CRM
3. Map user metadata to CRM fields

### Scenario 3: Inventory Low -> Alert
1. Trigger: Webhook (product stock update)
2. Condition: IF stock < 5
3. Action: Send Slack notification

## 13.3 Make.com API Module

```
Module: HTTP -> Make a Request

URL: https://project.supabase.co/rest/v1/products
Method: POST
Headers:
  - Authorization: Bearer [YOUR-ANON-KEY]
  - Content-Type: application/json

Body:
{
  "name": "New Product",
  "price": 99.99,
  "stock": 10,
  "seller_id": "seller-uuid"
}
```

---

# 14. Integrazione Shopify + Supabase

## 14.1 Setup Shopify App

```bash
# Create custom Shopify app
# 1. Shopify Admin -> Apps -> App and sales channel settings
# 2. Develop apps -> Create an app
# 3. Set scopes: products, orders, customers
# 4. Generate access token
```

## 14.2 Sync Products Shopify -> Supabase

```typescript
// supabase/functions/shopify-sync/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

const SHOPIFY_STORE = Deno.env.get("SHOPIFY_STORE");
const SHOPIFY_ACCESS_TOKEN = Deno.env.get("SHOPIFY_ACCESS_TOKEN");
const SUPABASE_URL = Deno.env.get("SUPABASE_URL");
const SUPABASE_KEY = Deno.env.get("SUPABASE_ANON_KEY");

const supabase = createClient(SUPABASE_URL, SUPABASE_KEY);

serve(async (req) => {
  if (req.method === "OPTIONS") {
    return new Response("ok", { headers: { "Access-Control-Allow-Origin": "*" } });
  }

  try {
    // Fetch products from Shopify
    const shopifyResponse = await fetch(
      `https://${SHOPIFY_STORE}/admin/api/2024-01/products.json`,
      {
        headers: {
          "X-Shopify-Access-Token": SHOPIFY_ACCESS_TOKEN
        }
      }
    );

    const { products } = await shopifyResponse.json();

    // Sync to Supabase
    const productsToSync = products.map((product: any) => ({
      shopify_id: product.id.toString(),
      name: product.title,
      description: product.body_html,
      price: product.variants[0].price,
      stock: product.variants[0].inventory_quantity,
      shopify_handle: product.handle
    }));

    const { error } = await supabase
      .from('shopify_products')
      .upsert(productsToSync, { onConflict: 'shopify_id' });

    if (error) throw error;

    return new Response(
      JSON.stringify({ success: true, synced: products.length }),
      { status: 200, headers: { "Content-Type": "application/json" } }
    );
  } catch (error) {
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 500 }
    );
  }
});
```

## 14.3 Sync Orders Shopify -> Supabase

```typescript
// supabase/functions/shopify-orders/index.ts
// Similar structure to products
// Fetch orders from Shopify API
// Store in Supabase orders table with shopify_order_id reference
```

## 14.4 Update Inventory on Order

```sql
-- Trigger when order is created
CREATE TRIGGER update_shopify_inventory
AFTER INSERT ON orders
FOR EACH ROW
EXECUTE FUNCTION sync_inventory_to_shopify();

-- Function that calls Edge Function
CREATE OR REPLACE FUNCTION sync_inventory_to_shopify()
RETURNS TRIGGER AS $$
BEGIN
  -- Call Edge Function that updates Shopify inventory
  PERFORM pg_net.http_post(
    url := 'https://project.supabase.co/functions/v1/update-shopify-inventory',
    headers := jsonb_build_object(
      'Authorization', 'Bearer ' || Deno.env.get('SERVICE_ROLE_KEY')
    ),
    body := jsonb_build_object(
      'order_id', NEW.id,
      'items', (
        SELECT jsonb_agg(
          jsonb_build_object(
            'product_id', product_id,
            'quantity', quantity
          )
        )
        FROM order_items
        WHERE order_id = NEW.id
      )
    )
  );
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

---

# 15. Integrazione Notion + Supabase (Bidirectional)

## 15.1 Notion Database Schema

Create a Notion database with these properties:
- Title: Product Name
- Price: Number
- Stock: Number
- Seller: Relation to Sellers database
- Status: Select (Draft, Published, Archived)
- URL: Supabase Product ID

## 15.2 Sync Supabase -> Notion

```typescript
// supabase/functions/sync-to-notion/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";

const NOTION_API_KEY = Deno.env.get("NOTION_API_KEY");
const NOTION_DATABASE_ID = Deno.env.get("NOTION_DATABASE_ID");

serve(async (req) => {
  try {
    const { product_id, product_name, price, stock } = await req.json();

    // Create page in Notion
    const response = await fetch("https://api.notion.com/v1/pages", {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${NOTION_API_KEY}`,
        "Content-Type": "application/json",
        "Notion-Version": "2022-06-28"
      },
      body: JSON.stringify({
        parent: { database_id: NOTION_DATABASE_ID },
        properties: {
          "Title": {
            title: [{ text: { content: product_name } }]
          },
          "Price": {
            number: price
          },
          "Stock": {
            number: stock
          },
          "URL": {
            rich_text: [{ text: { content: product_id } }]
          }
        }
      })
    });

    const notion_page = await response.json();

    return new Response(
      JSON.stringify({ notion_page_id: notion_page.id }),
      { status: 200, headers: { "Content-Type": "application/json" } }
    );
  } catch (error) {
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 500 }
    );
  }
});
```

## 15.3 Sync Notion -> Supabase

```typescript
// supabase/functions/sync-from-notion/index.ts
// Triggered by Notion webhook
// Parse Notion page changes and update Supabase

const handleNotionUpdate = async (notionPage: any) => {
  const properties = notionPage.properties;

  const { error } = await supabase
    .from('products')
    .update({
      name: properties.Title.title[0]?.text.content,
      price: properties.Price.number,
      stock: properties.Stock.number,
      notion_page_id: notionPage.id
    })
    .eq('id', properties.URL.rich_text[0]?.text.content);

  return { success: !error };
};
```

## 15.4 Real-time Sync with Triggers

```sql
-- Trigger when product is updated
CREATE TRIGGER sync_product_to_notion
AFTER UPDATE ON products
FOR EACH ROW
WHEN (OLD.name != NEW.name OR OLD.price != NEW.price OR OLD.stock != NEW.stock)
EXECUTE FUNCTION call_notion_sync();

CREATE OR REPLACE FUNCTION call_notion_sync()
RETURNS TRIGGER AS $$
BEGIN
  PERFORM pg_net.http_post(
    url := 'https://project.supabase.co/functions/v1/sync-to-notion',
    headers := jsonb_build_object(
      'Content-Type', 'application/json'
    ),
    body := jsonb_build_object(
      'product_id', NEW.id,
      'product_name', NEW.name,
      'price', NEW.price,
      'stock', NEW.stock
    )
  );
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

---

# 16. Integrazione Stack Completo Vendiamonoi

## 16.1 Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                   VENDIAMONOI MARKETPLACE                    │
└─────────────────────────────────────────────────────────────┘
                              │
           ┌──────────────────┼──────────────────┐
           │                  │                  │
           ▼                  ▼                  ▼
    ┌────────────┐    ┌────────────┐    ┌────────────┐
    │   SHOPIFY  │    │  MAKE.COM  │    │   NOTION   │
    │            │    │            │    │            │
    │ - Products │    │ - Workflow │    │- Knowledge │
    │ - Orders   │    │ - Automation   │ - Planning  │
    │ - Customers    │ - Integration   │            │
    └─────┬──────┘    └──────┬─────┘    └────┬───────┘
          │                  │               │
          └──────────────────┼───────────────┘
                             │
                             ▼
               ┌─────────────────────────┐
               │     SUPABASE HUB        │
               │   (Central Database)    │
               │                         │
               │ - Core tables           │
               │ - Real-time updates     │
               │ - Storage API           │
               │ - Edge Functions        │
               │ - Webhooks              │
               │ - RLS policies          │
               └──────────┬──────────────┘
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
        ▼                 ▼                 ▼
  ┌──────────┐    ┌──────────┐    ┌──────────┐
  │   API    │    │  REALTIME│    │ STORAGE  │
  │  REST    │    │WebSocket │    │   CDN    │
  │  POST/GET    │  Broadcast     │          │
  └──────────┘    └──────────┘    └──────────┘
        │                 │                 │
        └─────────────────┼─────────────────┘
                          │
                          ▼
               ┌─────────────────────────┐
               │  Client Applications    │
               │                         │
               │ - Web (React/Vue)       │
               │ - Mobile (React Native) │
               │ - Admin Dashboard       │
               │ - Vendor Portal         │
               └─────────────────────────┘
```

## 16.2 Integration Points

### Shopify Integration
- **Data Flow**: Shopify products ↔ Supabase products table
- **Events**: Product created/updated → Sync via Edge Function
- **Orders**: Shopify orders → Supabase orders + webhook to Make.com
- **Inventory**: Stock changes → Shopify API update

### Make.com Automation
- **Trigger**: Supabase webhooks (new order, inventory change)
- **Actions**: 
  - Send email to customer
  - Update Notion database
  - Create Slack notifications
  - Add to CRM
- **Bidirectional**: Notion changes → Supabase via webhook

### Notion Integration
- **Knowledge Base**: Product specs, seller guidelines
- **Planning**: Sprint planning, roadmap
- **Sync**: Product metadata ↔ Supabase

## 16.3 Complete Workflow Example

### New Product Flow
1. **Seller** creates product in Shopify
2. **Supabase Edge Function** syncs product to database
3. **Database Webhook** triggers Make.com scenario
4. **Make.com** creates Notion page for product spec
5. **Real-time** updates in all connected apps

```sql
-- Event sequence
INSERT INTO shopify_products -- Shopify API
→ Supabase Webhook
→ Make.com Scenario
→ Notion API
→ Dashboard Real-time Update
```

### Order Processing Flow
1. **Customer** places order on Shopify
2. **Shopify** creates order webhook
3. **Supabase Edge Function** imports order
4. **Database Webhook** triggers Make.com
5. **Make.com** sends:
   - Confirmation email (SendGrid)
   - Alert to seller (Slack)
   - Update inventory (Shopify API)
6. **Real-time** order status updates to customer

---

# 17. Schema Database per E-Commerce Marketplace

## 17.1 Core Tables

```sql
-- Users Table
CREATE TABLE user_profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id),
  email TEXT UNIQUE,
  first_name TEXT,
  last_name TEXT,
  phone TEXT,
  avatar_url TEXT,
  role TEXT CHECK (role IN ('customer', 'seller', 'admin')),
  seller_verified BOOLEAN DEFAULT false,
  seller_category TEXT,
  bio TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Products Table
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  seller_id UUID NOT NULL REFERENCES user_profiles(id),
  name TEXT NOT NULL,
  description TEXT,
  category TEXT NOT NULL,
  price NUMERIC(10, 2) NOT NULL,
  stock INT NOT NULL DEFAULT 0,
  sku TEXT UNIQUE,
  is_published BOOLEAN DEFAULT false,
  views INT DEFAULT 0,
  rating NUMERIC(3, 2),
  reviews_count INT DEFAULT 0,
  image_url TEXT,
  shopify_id TEXT,
  notion_page_id TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Product Images
CREATE TABLE product_images (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  image_url TEXT NOT NULL,
  display_order INT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Orders Table
CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  customer_id UUID NOT NULL REFERENCES user_profiles(id),
  seller_id UUID REFERENCES user_profiles(id),
  total_amount NUMERIC(10, 2) NOT NULL,
  status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'confirmed', 'shipped', 'delivered', 'cancelled')),
  payment_method TEXT,
  shipping_address JSONB,
  notes TEXT,
  shopify_order_id TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Order Items
CREATE TABLE order_items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  order_id UUID NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
  product_id UUID NOT NULL REFERENCES products(id),
  quantity INT NOT NULL,
  unit_price NUMERIC(10, 2) NOT NULL,
  subtotal NUMERIC(10, 2) GENERATED ALWAYS AS (quantity * unit_price) STORED,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Reviews Table
CREATE TABLE reviews (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  customer_id UUID NOT NULL REFERENCES user_profiles(id),
  rating INT CHECK (rating >= 1 AND rating <= 5),
  title TEXT,
  comment TEXT,
  helpful_count INT DEFAULT 0,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

## 17.2 Analytics Tables

```sql
-- Product Views
CREATE TABLE product_views (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  visitor_id UUID,
  device_type TEXT,
  source TEXT,
  viewed_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Sales Analytics (Denormalized for performance)
CREATE TABLE sales_daily (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  date DATE NOT NULL,
  seller_id UUID REFERENCES user_profiles(id),
  total_orders INT,
  total_revenue NUMERIC(10, 2),
  total_items_sold INT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Create materialized view for aggregations
CREATE MATERIALIZED VIEW product_stats AS
SELECT
  p.id,
  p.name,
  COUNT(DISTINCT pv.id) as total_views,
  COUNT(DISTINCT oi.id) as total_purchases,
  AVG(r.rating) as avg_rating,
  COUNT(DISTINCT r.id) as review_count,
  SUM(oi.quantity) as total_sold,
  MAX(oi.order_id) as last_sale
FROM products p
LEFT JOIN product_views pv ON p.id = pv.product_id
LEFT JOIN order_items oi ON p.id = oi.product_id
LEFT JOIN reviews r ON p.id = r.product_id
GROUP BY p.id, p.name;

-- Index for performance
CREATE INDEX idx_product_stats ON product_stats(id);
```

## 17.3 Indexes for Performance

```sql
-- Frequently queried columns
CREATE INDEX idx_products_seller ON products(seller_id);
CREATE INDEX idx_products_category ON products(category);
CREATE INDEX idx_products_published ON products(is_published);
CREATE INDEX idx_products_created ON products(created_at DESC);

CREATE INDEX idx_orders_customer ON orders(customer_id);
CREATE INDEX idx_orders_seller ON orders(seller_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created ON orders(created_at DESC);

CREATE INDEX idx_reviews_product ON reviews(product_id);
CREATE INDEX idx_reviews_customer ON reviews(customer_id);

-- Full-text search
CREATE INDEX idx_products_search ON products USING GIN(to_tsvector('italian', name || ' ' || COALESCE(description, '')));
```

---

# 18. Performance e Scaling per Marketplace

## 18.1 Query Optimization

### Before (Slow)
```javascript
// N+1 problem
const orders = await supabase
  .from('orders')
  .select('*')
  .limit(10);

for (const order of orders.data) {
  const items = await supabase
    .from('order_items')
    .select('*')
    .eq('order_id', order.id); // N queries!
}
```

### After (Optimized)
```javascript
// Single query with relationships
const { data: orders } = await supabase
  .select(`
    *,
    order_items (
      *,
      product:product_id(name, price, image_url)
    ),
    customer:customer_id(first_name, last_name, email)
  `)
  .from('orders')
  .limit(10);
```

## 18.2 Pagination Strategy

```javascript
// Cursor-based pagination (better for large datasets)
let cursor = null;
let allResults = [];

while (true) {
  const { data, error } = await supabase
    .from('products')
    .select('*')
    .order('id')
    .range(cursor ? cursor : 0, cursor ? cursor + 99 : 99);

  if (error || data.length === 0) break;

  allResults = [...allResults, ...data];
  cursor = (cursor || 0) + 100;
}
```

## 18.3 Caching Strategy

```javascript
// Client-side cache with React Query
import { useQuery } from '@tanstack/react-query';

function useProducts(category) {
  return useQuery({
    queryKey: ['products', category],
    queryFn: async () => {
      const { data } = await supabase
        .from('products')
        .select()
        .eq('category', category)
        .eq('is_published', true);
      return data;
    },
    staleTime: 1000 * 60 * 5, // 5 minutes
    cacheTime: 1000 * 60 * 10  // 10 minutes
  });
}
```

## 18.4 Database Connection Pooling

```
-- In Supabase settings:
Pool Mode: Transaction (recommended for serverless)
Pool Size: 10 (adjust based on traffic)
Connection Timeout: 5 seconds
```

## 18.5 CDN Configuration

```
-- Enable CDN for static assets
- All images in Storage bucket
- Cloudflare CDN integration
- Cache-Control: max-age=31536000 (1 year for versioned assets)
- Gzip compression enabled
```

## 18.6 Database Replication

```
-- For read-heavy workloads:
- Primary: Read + Write
- Replica 1: Read-only (Asia region)
- Replica 2: Read-only (EU region)

-- Route queries appropriately
const readReplica = createClient(REPLICA_URL, KEY);
const { data } = await readReplica
  .from('products')
  .select();
```

---

# 19. Full-Text Search Multilingue

## 19.1 Setup PostgreSQL Full-Text Search

```sql
-- Add tsvector column for Italian search
ALTER TABLE products ADD COLUMN search_text TSVECTOR;

-- Create function to update tsvector
CREATE OR REPLACE FUNCTION update_product_search_text()
RETURNS TRIGGER AS $$
BEGIN
  NEW.search_text := to_tsvector('italian', COALESCE(NEW.name, '') || ' ' || COALESCE(NEW.description, ''));
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Create trigger
CREATE TRIGGER product_search_update
BEFORE INSERT OR UPDATE ON products
FOR EACH ROW
EXECUTE FUNCTION update_product_search_text();

-- Create GIN index for fast search
CREATE INDEX idx_products_search ON products USING GIN(search_text);
```

## 19.2 Search Queries

```javascript
// Simple search
const { data } = await supabase
  .from('products')
  .select('*')
  .or('name.ilike.%laptop%,description.ilike.%laptop%')
  .limit(10);

// Full-text search (Italian)
const { data } = await supabase.rpc('search_products', {
  query: 'laptop gaming',
  language: 'italian'
});
```

## 19.3 Search Function with Ranking

```sql
CREATE OR REPLACE FUNCTION search_products(
  p_query TEXT,
  p_language TEXT DEFAULT 'italian'
)
RETURNS TABLE (
  id UUID,
  name TEXT,
  description TEXT,
  price NUMERIC,
  rank NUMERIC
) AS $$
BEGIN
  RETURN QUERY
  SELECT
    p.id,
    p.name,
    p.description,
    p.price,
    ts_rank_cd(p.search_text, to_tsquery(p_language, p_query)) as rank
  FROM products p
  WHERE p.search_text @@ to_tsquery(p_language, p_query)
  AND p.is_published = true
  ORDER BY rank DESC
  LIMIT 50;
END;
$$ LANGUAGE plpgsql;
```

## 19.4 Multilingual Support

```sql
-- Support multiple languages
ALTER TABLE products ADD COLUMN language TEXT DEFAULT 'italian';

-- Language-specific search
CREATE OR REPLACE FUNCTION search_products_lang(
  p_query TEXT,
  p_language TEXT
)
RETURNS TABLE (
  id UUID,
  name TEXT,
  rank NUMERIC
) AS $$
BEGIN
  RETURN QUERY
  SELECT
    p.id,
    p.name,
    ts_rank_cd(
      to_tsvector(p_language, p.name || ' ' || COALESCE(p.description, '')),
      to_tsquery(p_language, p_query)
    ) as rank
  FROM products p
  WHERE p.language = p_language
  AND p.is_published = true
  ORDER BY rank DESC;
END;
$$ LANGUAGE plpgsql;
```

---

# 20. Backup, Sicurezza e Monitoring

## 20.1 Backup Strategy

### Automatic Backups
```
- Daily backup (automated by Supabase)
- Point-in-time recovery (PITR) for 7 days
- Geographic redundancy (multi-region)
- Backup retention: 30 days (configurable)
```

### Manual Backups
```bash
# Export database with pg_dump
pg_dump -h db.project.supabase.co -U postgres -d postgres > backup.sql

# Compress backup
gzip backup.sql

# Store in S3 or secure location
aws s3 cp backup.sql.gz s3://vendiamonoi-backups/2024-01-01.sql.gz
```

## 20.2 Row Level Security Best Practices

```sql
-- Always enable RLS on sensitive tables
ALTER TABLE user_profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
ALTER TABLE order_items ENABLE ROW LEVEL SECURITY;

-- Default deny policy
CREATE POLICY "Deny all by default" ON user_profiles AS (
  USING (false)
);

-- Specific allow policies
CREATE POLICY "Users can read own profile" ON user_profiles
  FOR SELECT USING (id = auth.uid());

-- Admin override
CREATE POLICY "Admins can access all" ON user_profiles
  FOR ALL USING (auth.jwt() ->> 'role' = 'admin');
```

## 20.3 Environment Variables and Secrets

```bash
# Store sensitive data in environment variables
SUPABASE_URL=https://project.supabase.co
SUPABASE_ANON_KEY=eyJhbGc...
SUPABASE_SERVICE_ROLE_KEY=eyJhbGc... # Never expose client-side
SHOPIFY_ACCESS_TOKEN=shpat_...
NOTION_API_KEY=secret_...
MAKE_WEBHOOK_URL=https://hook.make.com/...
```

## 20.4 Monitoring and Logging

### PostgreSQL Logging
```sql
-- Enable query logging
ALTER SYSTEM SET log_min_duration_statement = 1000; -- Log queries > 1 second
ALTER SYSTEM SET log_statement = 'all';

-- Analyze slow queries
EXPLAIN ANALYZE
SELECT * FROM products WHERE category = 'electronics' AND price > 100;
```

### Application Monitoring
```typescript
// Sentry integration for error tracking
import * as Sentry from "@sentry/deno";

Sentry.init({
  dsn: Deno.env.get("SENTRY_DSN"),
  environment: Deno.env.get("ENVIRONMENT")
});

try {
  // Code
} catch (error) {
  Sentry.captureException(error);
}
```

## 20.5 Database Audit Trail

```sql
-- Create audit log table
CREATE TABLE audit_log (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  table_name TEXT,
  operation TEXT,
  user_id UUID REFERENCES auth.users(id),
  old_values JSONB,
  new_values JSONB,
  timestamp TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Function to log changes
CREATE OR REPLACE FUNCTION audit_trigger()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO audit_log (table_name, operation, user_id, old_values, new_values)
  VALUES (
    TG_TABLE_NAME,
    TG_OP,
    auth.uid(),
    CASE WHEN TG_OP = 'DELETE' THEN to_jsonb(OLD) ELSE NULL END,
    CASE WHEN TG_OP IN ('INSERT', 'UPDATE') THEN to_jsonb(NEW) ELSE NULL END
  );
  RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Attach trigger to sensitive tables
CREATE TRIGGER audit_products AFTER INSERT OR UPDATE OR DELETE ON products
FOR EACH ROW EXECUTE FUNCTION audit_trigger();
```

## 20.6 SSL/TLS Configuration

```
- All connections require SSL (enforced by Supabase)
- Certificate: Let's Encrypt (auto-renewed)
- TLS 1.2+
- HSTS enabled (6 months)
```

## 20.7 Rate Limiting

```javascript
// Implement rate limiting in Edge Functions
const rateLimit = new Map();

function checkRateLimit(userId: string, limit: number = 100, window: number = 60000) {
  const key = userId;
  const now = Date.now();
  const userData = rateLimit.get(key) || { count: 0, resetTime: now + window };

  if (now > userData.resetTime) {
    userData.count = 0;
    userData.resetTime = now + window;
  }

  userData.count++;

  rateLimit.set(key, userData);

  return userData.count <= limit;
}
```

---

## Conclusioni

Supabase rappresenta una soluzione robusta e scalabile per il backend di Vendiamonoi.it. Grazie alle sue capacità di integrazione con Shopify, Make.com, e Notion, permette un ecosistema di dati connesso e automatizzato.

I pilastri della strategia:
1. **Database PostgreSQL** come fonte unica di verità
2. **PostgREST API** per CRUD rapido
3. **Real-time Subscriptions** per aggiornamenti istantanei
4. **Edge Functions** per logica serverless
5. **Row Level Security** per autorizzazione sicura
6. **Webhooks** per automazione e integrazione

Con questa architettura, Vendiamonoi.it può scalare efficacemente mantenendo la coerenza dei dati e la sicurezza.
