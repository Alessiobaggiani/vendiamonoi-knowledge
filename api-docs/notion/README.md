# Notion — Documentazione Tecnica

> **Vendiamonoi.it S.R.L.** — Documentazione interna
> **Ultimo aggiornamento:** 31/03/2026
> **Autore:** Claude (Cowork)
> **Pagina Notion:** [Notion](https://www.notion.so/287ab24b308680538e38f7deb158da5a)

---

## Panoramica

Notion è il sistema centrale di knowledge management di Vendiamonoi.it. L'API REST permette di automatizzare: gestione cataloghi prodotti, tracking fornitori, procedure operative, reporting KPI, sincronizzazione con Make.com/Supabase, e integrazione AI via MCP con Claude.

### Note Critiche

- Notion API v2025-09-03 introduce **data sources** al posto di databases — breaking change
- La versione più recente è **2026-03-11** (nuovo default in SDK JS)
- MCP Server ufficiale (hosted) è prioritario — il local potrebbe essere deprecato
- Rate limit: **3 richieste/secondo** per integrazione — pianificare batch con backoff
- Webhook disponibili — payload sparsi (richiedono fetch API per dati completi)
- Piani supportati per MCP: Pro, Max, Team, Enterprise

---

## 📡 API Documentation

### Autenticazione

#### 1. Internal Integration Token (consigliato per automazioni interne)

- Creare integrazione su https://www.notion.so/profile/integrations
- Token formato: `ntn_****` (secret)
- Header: `Authorization: Bearer ntn_****`
- Header obbligatorio: `Notion-Version: 2025-09-03`

#### 2. OAuth 2.0 (per app pubbliche)

- Redirect flow standard OAuth
- Scopes granulari per lettura/scrittura
- Token di accesso per utente

#### Setup Integration

1. Vai su https://www.notion.so/profile/integrations
2. Crea nuova integrazione interna
3. Seleziona capabilities (Read content, Update content, Insert content, Read comments)
4. Copia il token secret
5. Su ogni pagina/database da collegare: ⋯ → Connect to → [nome integrazione]

### Base URL

```
https://api.notion.com/v1/
```

### Endpoint — Pages

| Metodo | Endpoint | Descrizione |
|--------|----------|-------------|
| POST | `/pages` | Crea nuova pagina |
| GET | `/pages/{page_id}` | Recupera proprietà pagina |
| PATCH | `/pages/{page_id}` | Aggiorna proprietà pagina |
| POST | `/pages/{page_id}/move` | Sposta pagina |

### Endpoint — Blocks (Contenuto pagine)

| Metodo | Endpoint | Descrizione |
|--------|----------|-------------|
| GET | `/blocks/{block_id}` | Recupera blocco |
| GET | `/blocks/{block_id}/children` | Lista blocchi figli (paginato) |
| PATCH | `/blocks/{block_id}/children` | Appendi blocchi figli |
| PATCH | `/blocks/{block_id}` | Aggiorna blocco |
| DELETE | `/blocks/{block_id}` | Archivia blocco |

### Endpoint — Data Sources (ex Databases — API v2025-09-03+)

| Metodo | Endpoint | Descrizione |
|--------|----------|-------------|
| POST | `/data_sources` | Crea data source |
| GET | `/data_sources/{data_source_id}` | Recupera schema |
| PATCH | `/data_sources/{data_source_id}` | Aggiorna proprietà |
| POST | `/data_sources/{data_source_id}/query` | Query con filtri/sort |
| GET | `/data_sources/{data_source_id}/templates` | Lista template |

### Endpoint — Databases (Legacy — ancora supportati)

| Metodo | Endpoint | Descrizione |
|--------|----------|-------------|
| POST | `/databases` | Crea database |
| GET | `/databases/{database_id}` | Recupera database |
| PATCH | `/databases/{database_id}` | Aggiorna database |
| POST | `/databases/{database_id}/query` | Query con filtri |

### Endpoint — Users, Search, Comments

| Metodo | Endpoint | Descrizione |
|--------|----------|-------------|
| GET | `/users` | Lista utenti workspace |
| GET | `/users/{user_id}` | Dettagli utente |
| GET | `/users/me` | Bot user corrente |
| POST | `/search` | Cerca pagine e database |
| POST | `/comments` | Crea commento |
| GET | `/comments?block_id={id}` | Lista commenti blocco |
| GET | `/comments/{comment_id}` | Recupera commento |

### Esempio: Creare una Pagina

```bash
curl -X POST 'https://api.notion.com/v1/pages' \
  -H 'Authorization: Bearer ntn_****' \
  -H 'Notion-Version: 2025-09-03' \
  -H 'Content-Type: application/json' \
  -d '{
    "parent": {"database_id": "DB_ID"},
    "properties": {
      "Name": {"title": [{"text": {"content": "Nuovo Fornitore"}}]},
      "Status": {"select": {"name": "Attivo"}}
    }
  }'
```

### Esempio: Query Database con Filtri

```bash
curl -X POST 'https://api.notion.com/v1/databases/DB_ID/query' \
  -H 'Authorization: Bearer ntn_****' \
  -H 'Notion-Version: 2025-09-03' \
  -H 'Content-Type: application/json' \
  -d '{
    "filter": {
      "property": "Status",
      "select": {"equals": "Attivo"}
    },
    "sorts": [{"property": "Name", "direction": "ascending"}],
    "page_size": 100
  }'
```

### Rate Limits

- **3 richieste/secondo** per integrazione (media)
- HTTP 429 → `rate_limited` error code
- Header `Retry-After` indica secondi di attesa
- **Best practice:** implementare exponential backoff con jitter
- Paginazione: max 100 risultati per richiesta (`start_cursor` per pagina successiva)

### Error Codes

| HTTP | Codice | Significato | Soluzione |
|------|--------|-------------|-----------|
| 400 | `invalid_json` | JSON malformato | Verificare body richiesta |
| 400 | `validation_error` | Parametri non validi | Controllare schema proprietà |
| 401 | `unauthorized` | Token mancante/invalido | Verificare header Authorization |
| 403 | `restricted_resource` | Integrazione non connessa | Connettere integrazione alla pagina |
| 404 | `object_not_found` | Risorsa non trovata | Verificare ID e permessi |
| 409 | `conflict_error` | Conflitto di scrittura | Retry con backoff |
| 429 | `rate_limited` | Troppe richieste | Rispettare Retry-After header |
| 500 | `internal_server_error` | Errore Notion | Retry con backoff |
| 502 | `bad_gateway` | Gateway error | Retry dopo qualche secondo |
| 503 | `service_unavailable` | Servizio non disponibile | Retry con backoff crescente |

---

## 🔔 Webhook

### Disponibilità

✅ **Webhook nativi disponibili** (introdotti 2025)

### Setup

1. Creare subscription via API con URL endpoint
2. Notion invia POST di verifica con `verification_token`
3. Rispondere con 200 per confermare
4. Webhook attivo — ricevi eventi in real-time

### Eventi Disponibili

| Evento | Descrizione | Capability richiesta |
|--------|-------------|----------------------|
| `page.created` | Nuova pagina creata | Read content |
| `page.content_updated` | Contenuto pagina modificato | Read content |
| `page.properties_updated` | Proprietà pagina cambiate | Read content |
| `page.deleted` | Pagina eliminata | Read content |
| `page.undeleted` | Pagina ripristinata | Read content |
| `data_source.schema_updated` | Schema data source cambiato | Read content |
| `comment.created` | Nuovo commento | Read comments |
| `comment.updated` | Commento modificato | Read comments |

### Payload Structure (Sparse)

I payload webhook sono **sparsi**: contengono solo metadata (event type, entity ID, timestamp). Per i dati completi, fare fetch via API.

### Verifica Firma

- Header: `X-Notion-Signature`
- Algoritmo: **HMAC-SHA256**
- Secret: `verification_token` ricevuto al setup
- Calcolare: `HMAC-SHA256(raw_body, verification_token)`
- Confrontare con header per validare autenticità

### Use Case Vendiamonoi

- **Nuovo fornitore aggiunto** → webhook trigger → Make.com crea record su Supabase
- **Status ordine cambiato** → webhook → notifica team su Bitrix24
- **Nuova procedura pubblicata** → webhook → sync su Obsidian

---

## 🔌 MCP Server

### Opzione 1: MCP Hosted (Raccomandato — Ufficiale Notion)

**URL:** `https://mcp.notion.com/mcp`

**Configurazione Claude Desktop:**
Settings → Connectors → Notion MCP (OAuth flow automatico)

**Configurazione Claude Code:**
```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

**Autenticazione:** OAuth user-based — l'utente autorizza via browser. NON supporta bearer token diretto.

**Piani Supportati:** Pro, Max, Team, Enterprise

> ⚠️ Notion ha dichiarato priorità al MCP hosted — il local potrebbe essere deprecato in futuro.

---

### Opzione 2: MCP Server Local (npm — Self-hosted)

**Repository:** [github.com/makenotion/notion-mcp-server](https://github.com/makenotion/notion-mcp-server)
**Versione:** 2.0.0

#### Installazione

```json
{
  "mcpServers": {
    "notionApi": {
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "env": {
        "NOTION_TOKEN": "ntn_****"
      }
    }
  }
}
```

#### Transport HTTP

```bash
npx @notionhq/notion-mcp-server --transport http --port 3000
# Endpoint: http://0.0.0.0:3000/mcp
```

#### Tools Disponibili (22 totali — v2.0.0)

**Pages:**

| Tool | Descrizione |
|------|-------------|
| `create-a-page` | Crea pagina in database o sotto pagina |
| `retrieve-a-page` | Recupera proprietà pagina |
| `update-page-properties` | Aggiorna proprietà |
| `move-page` | Sposta pagina nella gerarchia |
| `archive-a-page` | Archivia/ripristina pagina |

**Blocks:**

| Tool | Descrizione |
|------|-------------|
| `retrieve-block-children` | Lista blocchi figli |
| `append-block-children` | Aggiungi blocchi |
| `retrieve-a-block` | Dettagli singolo blocco |
| `update-a-block` | Modifica blocco |
| `delete-a-block` | Archivia blocco |

**Data Sources (nuovo v2.0.0):**

| Tool | Descrizione |
|------|-------------|
| `query-data-source` | Query con filtri e sort |
| `retrieve-a-data-source` | Schema e metadata |
| `update-a-data-source` | Modifica proprietà |
| `create-a-data-source` | Crea data source |
| `list-data-source-templates` | Template disponibili |
| `retrieve-a-database` | Metadata con data source IDs |

**Search, Users, Comments:**

| Tool | Descrizione |
|------|-------------|
| `search` | Cerca pagine e database |
| `list-all-users` | Lista utenti workspace |
| `retrieve-a-user` | Dettagli utente |
| `create-a-comment` | Crea commento |
| `retrieve-comments` | Lista commenti pagina |

#### Breaking Changes v2.0.0

- `post-database-query` → `query-data-source`
- `update-a-database` → `update-a-data-source`
- `create-a-database` → `create-a-data-source`
- Tutti usano `data_source_id` invece di `database_id`

---

## 📦 SDK e Librerie

### JavaScript/TypeScript (Ufficiale)

```bash
npm install @notionhq/client
```

```javascript
const { Client } = require('@notionhq/client');
const notion = new Client({ auth: 'ntn_****' });

// Query database
const response = await notion.databases.query({
  database_id: 'DB_ID',
  filter: { property: 'Status', select: { equals: 'Attivo' } }
});

// Creare pagina
await notion.pages.create({
  parent: { database_id: 'DB_ID' },
  properties: {
    Name: { title: [{ text: { content: 'Nuovo Fornitore' } }] }
  }
});
```

**Versioni API supportate:** `2025-09-03` (default), `2026-03-11`

### Python (Community — notion-client)

```bash
pip install notion-client
```

```python
from notion_client import Client
notion = Client(auth="ntn_****")

# Query database
results = notion.databases.query(
    database_id="DB_ID",
    filter={"property": "Status", "select": {"equals": "Attivo"}}
)

# Creare pagina
notion.pages.create(
    parent={"database_id": "DB_ID"},
    properties={
        "Name": {"title": [{"text": {"content": "Nuovo Fornitore"}}]}
    }
)
```

**Supporto:** sync + async (`AsyncClient`)

### Python Alternativo (notion-sdk)

```bash
pip install notion-sdk
```

Typed, asyncio-native, usa httpx + pydantic.

---

## 🚀 Padronanza Avanzata

### Use Cases per Vendiamonoi.it

#### 1. Catalogo Fornitori Automatizzato
Database Notion con proprietà: Nome Fornitore, Categoria, N. SKU, Status, Contratto, Margine %. Make.com popola automaticamente da CSV/Excel → Notion API crea pagine.

#### 2. Dashboard KPI Marketplace
Database con metriche per marketplace: ordini/giorno, fatturato, tasso reso, recensioni. Aggiornamento automatico via Make.com → Notion API `PATCH /pages`.

#### 3. Gestione Ordini con Webhook
Webhook su database ordini → `page.properties_updated` → Make.com → aggiorna Supabase + notifica Bitrix24.

#### 4. Knowledge Base Team (MCP)
Claude interroga direttamente Notion via MCP per: cercare procedure, trovare info fornitori, recuperare policy marketplace. Zero switching tra app.

#### 5. Sync Notion ↔ Obsidian
Export trimestrale Notion → Obsidian per backup locale e accesso offline. Webhook Notion + Make.com per sync incrementale.

#### 6. Onboarding Automatizzato
Nuovo membro team → template pagina Notion con checklist → webhook → Make.com → crea utente Bitrix24 + assegna task ClickUp.

### Integrazioni con Stack Vendiamonoi

#### Notion + Make.com
**Trigger Make:** New Database Item, Updated Database Item, Watched Events (webhook)
**Azioni Make:** Create a Page, Update a Page, Query Database, Create Database Item, Append Block, Search
**Pattern:** Webhook Notion → Make → trasforma dati → Supabase/Bitrix24/Email

#### Notion + Supabase
Sincronia bidirezionale: Notion come frontend user-friendly, Supabase come backend scalabile. Make.com come ponte.

#### Notion + Claude (MCP)
Claude accede a tutto il workspace Notion via MCP. Può cercare, leggere, creare, aggiornare pagine direttamente dalla conversazione.

#### Notion + Bitrix24
Via Make.com: cambio status su Notion → crea task Bitrix24, o viceversa.

#### Notion + Obsidian
Sync via export Markdown. Guida disponibile nella pagina Notion dedicata.

### Limitazioni Rilevanti

- **Rate limit 3 req/s** — problematico per bulk operations su milioni di SKU
- **Paginazione max 100 risultati** — servono chiamate multiple per dataset grandi
- **Nessun bulk create/update** — ogni pagina richiede una richiesta separata
- **Webhook payload sparsi** — richiedono fetch API aggiuntivo per dati completi
- **Contenuto blocchi non incluso in page retrieve** — serve chiamata separata a /blocks/{id}/children
- **Formule 2.0 non esposte via API** — solo il risultato, non la formula
- **File/media upload non supportato** — solo URL esterni
- **Max 100 blocchi per append** — batch di max 100 per operazione
- **Relazioni cross-workspace non possibili**
- **MCP hosted richiede piano Pro+** — non disponibile su Free/Personal

### Risorse Ufficiali

- [Notion API Reference](https://developers.notion.com/reference/intro)
- [Notion API — Getting Started](https://developers.notion.com/docs/getting-started)
- [Notion Webhooks](https://developers.notion.com/reference/webhooks)
- [Notion MCP (Official)](https://developers.notion.com/docs/mcp)
- [MCP Server GitHub](https://github.com/makenotion/notion-mcp-server)
- [SDK JavaScript — npm](https://www.npmjs.com/package/@notionhq/client)
- [SDK Python — PyPI](https://pypi.org/project/notion-client/)
- [Notion API Changelog](https://developers.notion.com/changelog)
- [API Upgrade Guide 2025-09-03](https://developers.notion.com/docs/upgrade-guide-2025-09-03)
- [Notion Help Center — Integrations](https://www.notion.com/help/create-integrations-with-the-notion-api)
- [Notion Help Center — Webhook Actions](https://www.notion.com/help/webhook-actions)

---

## Changelog

| Data | Versione | Modifiche | Autore |
|------|----------|-----------|--------|
| 31/03/2026 | 1.0 | Creazione documentazione completa: API REST (tutti endpoint), Webhook (8 eventi), MCP Server (hosted + local, 22 tools), SDK (JS + Python), Use Cases, Integrazioni Stack, Limitazioni | Claude (Cowork) |
