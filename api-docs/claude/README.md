# Claude.ai / Anthropic — Documentazione Tecnica Completa

> **Vendiamonoi.it S.R.L.** — Knowledge Base Tecnica
> Versione: 1.0 | Data: 31/03/2026 | Autore: Claude (Cowork)

---

## 1. Panoramica

### Cos'è

Claude è la famiglia di modelli AI di Anthropic, disponibile come piattaforma consumer (claude.ai), API per sviluppatori, e strumenti agentic (Claude Code, Cowork, Agent SDK). Anthropic è focalizzata sulla sicurezza AI (AI Safety) e offre modelli con capacità avanzate di ragionamento, coding, analisi documenti e computer use.

### Rilevanza per Vendiamonoi.it

Claude/Anthropic rappresenta un componente strategico dell'infrastruttura AI di Vendiamonoi.it:

- **Claude Code + Cowork** — Automazione sviluppo infrastruttura, gestione file, task operativi aziendali
- **API Messages** — Generazione schede prodotto, traduzioni, classificazione categorie per milioni di SKU
- **Tool Use** — Integrazione diretta con database, marketplace, sistemi aziendali via function calling
- **Computer Use** — Automazione UI per marketplace senza API (screen scraping intelligente)
- **Batch API** — Elaborazione bulk cataloghi con 50% sconto (prodotti, traduzioni, descrizioni)
- **MCP (Model Context Protocol)** — Standard aperto creato da Anthropic per connettere AI a tool esterni (Notion, Supabase, Make, GitHub, Obsidian)
- **Agent SDK** — Costruzione agenti autonomi per workflow complessi di distribuzione digitale
- **Prompt Caching** — Risparmio fino al 90% su prompt ripetitivi (template schede prodotto)

### Prodotti Anthropic

| Prodotto | Tipo | Uso |
|----------|------|-----|
| Claude.ai (Free/Pro/Max) | Consumer Web/Desktop/Mobile | Task manuali, analisi, chat |
| Claude Code | CLI Agent (Terminal/IDE) | Sviluppo, automazione, agentic coding |
| Cowork | Desktop Agent (Research Preview) | Automazione file, task, documenti |
| Claude API | REST API | Integrazione programmatica |
| Claude Agent SDK | SDK Python/TypeScript | Costruzione agenti custom |
| Claude in Chrome | Browser Extension | Automazione web |
| AWS Bedrock | Cloud Deployment | Enterprise deployment |
| Google Vertex AI | Cloud Deployment | Enterprise deployment |

---

## 2. Piani e Pricing

### Piani Consumer Claude.ai

| Piano | Prezzo | Caratteristiche | Note |
|-------|--------|----------------|------|
| Free | $0/mese | Accesso base a Sonnet, limiti messaggi | Limitato |
| Pro | $20/mese | 5x usage, model selector, progetti, Claude Code | Consigliato |
| Max | $100-200/mese | 20x-40x usage, Claude Code avanzato | Power users |
| Team | $25/utente/mese (standard), $150/utente/mese (premium) | Workspace, admin, analytics, Claude Code premium | Team |
| Enterprise | Custom | SSO, SCIM, audit logs, HIPAA, usage-based billing | Min 50 seats |

### Pricing API (Marzo 2026)

| Modello | Input $/1M | Output $/1M | Context Window | Max Output |
|---------|-----------|-------------|----------------|------------|
| claude-opus-4-6 | $5.00 | $25.00 | 1M tokens | 32K tokens |
| claude-sonnet-4-6 | $3.00 | $15.00 | 1M tokens | 16K tokens |
| claude-haiku-4-5 | $1.00 | $5.00 | 200K tokens | 8K tokens |
| claude-opus-4-5 | $5.00 | $25.00 | 1M tokens | 32K tokens |
| claude-sonnet-4-5 | $3.00 | $15.00 | 200K tokens | 16K tokens |

**Ottimizzazioni costi:**

| Funzione | Risparmio | Dettaglio |
|----------|-----------|-----------|
| Prompt Caching (write) | 25% più caro | Scrittura iniziale in cache |
| Prompt Caching (read) | 90% risparmio | Lettura da cache |
| Batch API | 50% risparmio | Elaborazione asincrona (24h) |
| US-only inference | +10% | Data residency US (modelli post feb 2026) |

**Consiglio Vendiamonoi.it:** Per generazione bulk schede prodotto usare **claude-haiku-4-5** (più cost-effective) con **Batch API** (50% sconto) e **Prompt Caching** (90% sconto su template ripetitivi). Costo effettivo: ~$0.05/1M input tokens con cache hit + batch.

### Usage Tiers API

| Tier | Deposito Cumulativo | RPM | ITPM (Sonnet) | OTPM | Spend Mensile |
|------|---------------------|-----|---------------|------|---------------|
| Tier 1 | $5 | 50 | ~200K | ~40K | $100 |
| Tier 2 | $40 | 1.000 | ~400K | ~80K | $500 |
| Tier 3 | $200 | 2.000 | ~800K | ~160K | $1.000 |
| Tier 4 | $400 | 4.000 | ~2M | ~400K | $5.000 |

L'upgrade avviene automaticamente al raggiungimento della soglia di deposito. Token bucket algorithm per rate limiting (capacità continua, non reset a intervalli fissi). I token cached non contano verso ITPM — throughput effettivo 5-10x superiore.

---

## 3. API Documentation

### Autenticazione

**Tipo:** API Key (Bearer Token)

```
x-api-key: sk-ant-api03-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
anthropic-version: 2023-06-01
```

**Headers richiesti:**

| Header | Valore | Obbligatorio |
|--------|--------|-------------|
| `x-api-key` | La tua API key | Sì |
| `anthropic-version` | `2023-06-01` | Sì |
| `content-type` | `application/json` | Sì |
| `anthropic-beta` | Feature-specific | Solo per beta |

**Base URL:** `https://api.anthropic.com`

**Nota:** A differenza di OpenAI che usa `Authorization: Bearer`, Anthropic usa l'header custom `x-api-key`.

### Endpoint Principali

#### Messages API (Principale)

**`POST /v1/messages`**

L'endpoint principale per interagire con Claude. Supporta conversazioni multi-turn, tool use, vision, extended thinking, citations, streaming.

```json
{
  "model": "claude-sonnet-4-6-20250514",
  "max_tokens": 1024,
  "system": "Sei un esperto SEO per marketplace europei. Genera titoli ottimizzati.",
  "messages": [
    {
      "role": "user",
      "content": "Genera titolo SEO per: Lavatrice Samsung EcoBubble 9kg A+++ bianca"
    }
  ],
  "temperature": 0.5
}
```

**Risposta:**
```json
{
  "id": "msg_01XFDUDYJgAACzvnptvVoYEL",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "Samsung Lavatrice EcoBubble 9kg Classe A+++ | WW90T554DAW Bianca | Risparmio Energetico | Lavaggio a Vapore"
    }
  ],
  "model": "claude-sonnet-4-6-20250514",
  "stop_reason": "end_turn",
  "usage": {
    "input_tokens": 42,
    "output_tokens": 38
  }
}
```

#### Messages con Tool Use

**`POST /v1/messages`** con `tools` array

```json
{
  "model": "claude-sonnet-4-6-20250514",
  "max_tokens": 1024,
  "tools": [
    {
      "name": "cerca_prodotto_supabase",
      "description": "Cerca prodotto nel database Supabase per EAN o nome",
      "input_schema": {
        "type": "object",
        "properties": {
          "ean": { "type": "string", "description": "Codice EAN del prodotto" },
          "nome": { "type": "string", "description": "Nome prodotto" }
        },
        "required": ["ean"]
      }
    }
  ],
  "messages": [
    {
      "role": "user",
      "content": "Trova info per EAN 8001234567890 e genera scheda Amazon"
    }
  ]
}
```

#### Server-Side Tools (Built-in)

| Tool | Tipo | Funzione | Uso Vendiamonoi.it |
|------|------|----------|-------------------|
| `web_search` | server | Ricerca web real-time | Prezzi competitor, info prodotti |
| `code_execution` | server | Esecuzione Python sandboxed | Analisi CSV, elaborazione dati |
| `web_fetch` | server | Fetch pagine web | Scraping info prodotto |
| `tool_search` | server | Lazy loading MCP tools | Ottimizzazione contesto |

```json
{
  "model": "claude-sonnet-4-6-20250514",
  "max_tokens": 1024,
  "tools": [
    { "type": "web_search_20250305", "name": "web_search" }
  ],
  "messages": [
    {
      "role": "user",
      "content": "Cerca prezzo medio TV Samsung QLED 55 pollici su Amazon.it"
    }
  ]
}
```

#### Computer Use (Beta)

**`POST /v1/messages`** con tool `computer_20250124`

Permette a Claude di controllare mouse, tastiera e screenshot per automazione UI.

```json
{
  "model": "claude-sonnet-4-6-20250514",
  "max_tokens": 1024,
  "anthropic-beta": "computer-use-2025-11-24",
  "tools": [
    {
      "type": "computer_20250124",
      "name": "computer",
      "display_width_px": 1920,
      "display_height_px": 1080
    }
  ],
  "messages": [
    {
      "role": "user",
      "content": "Vai su eBay.it e cerca il prezzo medio per 'Nike Air Max 90'"
    }
  ]
}
```

**Uso Vendiamonoi.it:** Automazione marketplace senza API (es. caricamento manuale prodotti su marketplace minori).

#### Message Batches API

**`POST /v1/messages/batches`** — Elaborazione asincrona, 50% sconto

```json
{
  "requests": [
    {
      "custom_id": "sku-001",
      "params": {
        "model": "claude-haiku-4-5-20251001",
        "max_tokens": 500,
        "messages": [
          { "role": "user", "content": "Titolo SEO per: Aspirapolvere Dyson V15 Detect" }
        ]
      }
    },
    {
      "custom_id": "sku-002",
      "params": {
        "model": "claude-haiku-4-5-20251001",
        "max_tokens": 500,
        "messages": [
          { "role": "user", "content": "Titolo SEO per: Robot Aspirapolvere iRobot Roomba j7+" }
        ]
      }
    }
  ]
}
```

Fino a 10.000 richieste per batch. Elaborazione entro 24h. **Ideale per generazione bulk schede prodotto.**

**Endpoint correlati:**
- `GET /v1/messages/batches/{batch_id}` — Stato batch
- `GET /v1/messages/batches/{batch_id}/results` — Risultati
- `POST /v1/messages/batches/{batch_id}/cancel` — Cancellazione
- `GET /v1/messages/batches` — Lista batch

#### Streaming Messages

**`POST /v1/messages`** con `stream: true`

Server-Sent Events (SSE) per risposte in tempo reale.

Eventi SSE:
- `message_start` — Inizio messaggio
- `content_block_start` — Inizio blocco contenuto
- `content_block_delta` — Delta incrementale
- `content_block_stop` — Fine blocco
- `message_delta` — Delta messaggio (stop_reason, usage)
- `message_stop` — Fine messaggio
- `ping` — Keep-alive

#### Extended Thinking

**`POST /v1/messages`** con `thinking` parameter

Abilita il ragionamento step-by-step interno di Claude prima di rispondere.

```json
{
  "model": "claude-opus-4-6-20250514",
  "max_tokens": 16000,
  "thinking": {
    "type": "enabled",
    "budget_tokens": 10000
  },
  "messages": [
    {
      "role": "user",
      "content": "Analizza la strategia pricing ottimale per 50 SKU su 5 marketplace"
    }
  ]
}
```

#### Citations

Supporto nativo per citazioni tracciabili con riferimento ai documenti sorgente.

```json
{
  "model": "claude-sonnet-4-6-20250514",
  "max_tokens": 1024,
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "document",
          "source": { "type": "text", "media_type": "text/plain", "data": "Catalogo prodotti..." },
          "title": "Catalogo Fornitore XYZ",
          "citations": { "enabled": true }
        },
        { "type": "text", "text": "Elenca i prodotti con prezzo > 100€" }
      ]
    }
  ]
}
```

#### Prompt Caching

Riduce costi e latenza riutilizzando porzioni di prompt già processate.

```json
{
  "model": "claude-sonnet-4-6-20250514",
  "max_tokens": 1024,
  "system": [
    {
      "type": "text",
      "text": "Sei un esperto di marketplace europei. Template scheda prodotto: [lungo template con istruzioni dettagliate per 30+ marketplace...]",
      "cache_control": { "type": "ephemeral" }
    }
  ],
  "messages": [
    { "role": "user", "content": "Genera scheda per EAN 8001234567890" }
  ]
}
```

**Uso Vendiamonoi.it:** Cachare il system prompt con template schede prodotto (identico per migliaia di SKU) → 90% risparmio su input tokens.

#### Models API

- `GET /v1/models` — Lista modelli disponibili
- `GET /v1/models/{model_id}` — Dettagli modello (capabilities, limits)

#### Usage & Cost API

- `GET /v1/organizations/{org_id}/usage_report/messages` — Report usage
- `GET /v1/organizations/{org_id}/cost_report` — Report costi

### Parametri Chiave

| Parametro | Range | Uso Vendiamonoi.it |
|-----------|-------|-------------------|
| `temperature` | 0.0–1.0 | 0.2 traduzioni, 0.4 titoli SEO, 0.7 descrizioni creative |
| `max_tokens` | 1–limit modello | 200 titoli, 1000 descrizioni, 4000 traduzioni complete |
| `top_p` | 0.0–1.0 | 0.9 default, non modificare con temperature |
| `top_k` | int | Sampling top-k (default: nessuno) |
| `stop_sequences` | string[] | Sequenze di stop custom |
| `stream` | boolean | true per risposte realtime |

### Rate Limits

Misurati in RPM (requests/min), ITPM (input tokens/min), OTPM (output tokens/min).

**Rate limit algorithm:** Token bucket (capacità continua, non reset fisso).

**Header di risposta:**
- `retry-after` — Secondi di attesa
- `anthropic-ratelimit-requests-limit` — Limite RPM
- `anthropic-ratelimit-requests-remaining` — RPM rimanenti
- `anthropic-ratelimit-tokens-limit` — Limite TPM
- `anthropic-ratelimit-tokens-remaining` — TPM rimanenti

**Gestione:**
- Exponential backoff per 429
- Circuit breaker per produzione
- Batch API per alto volume
- Prompt caching: token cached non contano verso ITPM

### Error Codes

| Code | Tipo | Descrizione | Azione |
|------|------|-------------|--------|
| 400 | `invalid_request_error` | Formato/contenuto request errato | Fix payload |
| 401 | `authentication_error` | API key invalida | Verificare chiave |
| 403 | `permission_error` | Permessi insufficienti | Verificare accesso |
| 404 | `not_found_error` | Risorsa non trovata | Verificare URL/ID |
| 429 | `rate_limit_error` | Rate limit superato | Backoff + retry |
| 500 | `api_error` | Errore interno | Retry |
| 529 | `overloaded_error` | API sovraccarica | Exponential backoff |

**Regola:** 4xx (eccetto 429) = fix codice. 429 e 5xx = retry con backoff.

---

## 4. Webhook / Channels

### Webhook Nativi

Anthropic **non offre** un sistema webhook tradizionale come OpenAI. Le notifiche per batch completati e eventi asincroni si ottengono tramite polling dell'endpoint batch status.

### Claude Channels (Research Preview — Marzo 2026)

Nuovo sistema per push di eventi real-time in sessioni Claude Code:

| Source | Tipo | Uso |
|--------|------|-----|
| Telegram | Bot messages | Notifiche da team/clienti |
| Discord | Channel messages | Alert community |
| Custom Webhooks | HTTP POST | CI/CD builds, monitoring alerts |

I Channels sono MCP server che pushano eventi in Claude Code, non tool che Claude chiama. Utili per reagire a eventi esterni (build CI completate, alert monitoring, messaggi customer service).

### Pattern Alternativo: Make.com

Per workflow event-driven con Claude API, usare Make.com:
- Webhook trigger (ordine marketplace) → HTTP Module (Claude API) → Router → Azioni

---

## 5. MCP Server (Model Context Protocol)

### Cos'è MCP

MCP (Model Context Protocol) è uno **standard aperto creato da Anthropic** (novembre 2024) per connettere AI a tool e data source esterni. È il protocollo nativo di Claude per estendere le sue capacità.

### Architettura MCP

```
Host (Claude Desktop/Code/API)
  └── MCP Client (built-in nel host)
        └── MCP Server (locale o remoto)
              ├── Tools (funzioni che Claude può chiamare)
              ├── Resources (dati che Claude può leggere)
              └── Prompts (template pre-configurati)
```

### MCP Client: Dove Claude si Connette

| Client | Supporto MCP | Note |
|--------|-------------|------|
| Claude Desktop (macOS/Windows) | ✅ Nativo | Configurazione via JSON |
| Claude Code (CLI) | ✅ Nativo | Configurazione via `.mcp.json` |
| Cowork | ✅ Nativo | Plugin system |
| Claude.ai (Pro) | ✅ | Integrations marketplace |
| Claude API (Responses) | ✅ | `type: "mcp"` tool |

### MCP Server in Uso a Vendiamonoi.it

| Server | Funzione | Configurazione |
|--------|----------|---------------|
| Notion MCP | Lettura/scrittura pagine Notion | notion-mcp-server |
| Supabase MCP | Query database, gestione tabelle | Supabase official MCP |
| Make MCP | Gestione scenari, esecuzioni, webhook | Make official MCP |
| GitHub MCP | Repo, issues, PR | GitHub official MCP |
| Obsidian MCP | Lettura/scrittura vault | obsidian-mcp |
| Google Drive MCP | Accesso file Drive | Google Drive MCP |
| Gmail MCP | Gestione email | Gmail MCP |
| ClickUp MCP | Task management | ClickUp MCP |
| Miro MCP | Board e diagrammi | Miro MCP |

### Reference Servers (Ufficiali Anthropic)

| Server | Linguaggio | Funzione |
|--------|-----------|----------|
| Filesystem | TypeScript | Accesso file system locale |
| PostgreSQL | TypeScript | Query database PostgreSQL |
| Brave Search | TypeScript | Ricerca web |
| GitHub | TypeScript | Gestione repository |
| Puppeteer | TypeScript | Browser automation |
| Google Maps | TypeScript | Geocoding e mappe |

### Costruire un MCP Server Custom

SDK ufficiale: `@modelcontextprotocol/sdk` (TypeScript), `mcp` (Python)

Esempio server minimo (TypeScript):
```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new McpServer({ name: "vendiamonoi-products", version: "1.0.0" });

server.tool("search_product", { ean: { type: "string" } }, async ({ ean }) => {
  // Query Supabase per prodotto
  const product = await supabase.from('products').select().eq('ean', ean).single();
  return { content: [{ type: "text", text: JSON.stringify(product) }] };
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

### Configurazione MCP in Claude Code

File `.mcp.json` nella root del progetto:
```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server"],
      "env": { "SUPABASE_ACCESS_TOKEN": "${SUPABASE_TOKEN}" }
    },
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server"],
      "env": { "NOTION_TOKEN": "${NOTION_TOKEN}" }
    }
  }
}
```

### Ecosistema MCP (Marzo 2026)

- **1.000+ server community** nel registry ufficiale
- Tool descriptions capped a 2KB per prevenire bloat del contesto
- **MCP Tool Search** per lazy loading — riduce uso contesto del 95%
- Desktop Extensions: installazione one-click di MCP server per Claude Desktop

---

## 6. SDK e Librerie

### Python SDK (Ufficiale)

```bash
pip install anthropic
```

```python
import anthropic

client = anthropic.Anthropic()  # usa ANTHROPIC_API_KEY env var

# Messages API
message = client.messages.create(
    model="claude-sonnet-4-6-20250514",
    max_tokens=1024,
    system="Genera titoli SEO per marketplace europei.",
    messages=[
        {"role": "user", "content": "Titolo per: Aspirapolvere Dyson V15 Detect Absolute"}
    ],
    temperature=0.5
)
print(message.content[0].text)

# Streaming
with client.messages.stream(
    model="claude-sonnet-4-6-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Descrizione lunga per TV Samsung QLED 55"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)

# Batch API
batch = client.messages.batches.create(
    requests=[
        {
            "custom_id": f"sku-{i}",
            "params": {
                "model": "claude-haiku-4-5-20251001",
                "max_tokens": 500,
                "messages": [{"role": "user", "content": f"Titolo SEO per: {product}"}]
            }
        }
        for i, product in enumerate(products)
    ]
)
```

### TypeScript/Node.js SDK (Ufficiale)

```bash
npm install @anthropic-ai/sdk
```

```typescript
import Anthropic from '@anthropic-ai/sdk';
const client = new Anthropic();

const message = await client.messages.create({
  model: "claude-sonnet-4-6-20250514",
  max_tokens: 1024,
  messages: [{ role: "user", content: "Genera 5 bullet points per Dyson V15" }]
});

console.log(message.content[0].text);
```

### Claude Agent SDK (Python)

```bash
pip install claude-agent-sdk
```

Stessi tool, agent loop e context management di Claude Code, programmabili.

```python
from claude_agent_sdk import Agent, Tool

# Definire tool custom
search_tool = Tool(
    name="search_marketplace",
    description="Cerca prodotti su marketplace specifico",
    input_schema={...},
    handler=search_marketplace_fn
)

# Creare agente
agent = Agent(
    model="claude-sonnet-4-6-20250514",
    tools=[search_tool],
    instructions="Sei un agente per distribuzione digitale su marketplace europei."
)

# Eseguire
result = await agent.run("Analizza i top 10 prodotti su Amazon.fr categoria elettrodomestici")
```

**Concetti chiave Agent SDK:**
- **Tools** — Funzioni che l'agente può chiamare
- **Hooks** — Lifecycle callbacks (before think, after tool choice, before execute, on finish)
- **MCP Servers** — Connessione a server MCP esterni
- **Subagents** — Agenti specializzati delegabili

### Claude Agent SDK (TypeScript)

```bash
npm install @anthropic-ai/claude-agent-sdk
```

Versione TypeScript (v0.2.71 a marzo 2026) con le stesse funzionalità.

### OpenAI SDK Compatibility

Claude API supporta la compatibilità con OpenAI SDK — permette di migrare facilmente da OpenAI a Claude cambiando solo base URL e API key:

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.anthropic.com/v1/",
    api_key="sk-ant-..."
)
```

### Altre Librerie

| Linguaggio | Libreria | Tipo |
|-----------|----------|------|
| Python | `anthropic` | Ufficiale |
| TypeScript | `@anthropic-ai/sdk` | Ufficiale |
| Python | `claude-agent-sdk` | Ufficiale (Agent) |
| TypeScript | `@anthropic-ai/claude-agent-sdk` | Ufficiale (Agent) |
| Python/TS | `@modelcontextprotocol/sdk` | Ufficiale (MCP) |
| Python | `litellm` | Community — proxy 100+ LLM |
| Node.js | `ai` (Vercel) | Community — multi-provider |

---

## 7. Use Cases Vendiamonoi.it

### UC1 — Generazione Bulk Schede Prodotto

**Stack:** Batch API + Prompt Caching + claude-haiku-4-5

1. Template system prompt con istruzioni per 30+ marketplace (cached)
2. Batch di 10.000 SKU con Batch API (50% sconto)
3. Cache hit su template → 90% risparmio input
4. Output: titoli SEO, descrizioni, bullet points per marketplace

**Costo stimato:** ~$0.50 per 10.000 schede prodotto (con cache + batch)

### UC2 — Traduzione e Localizzazione Cataloghi

**Stack:** Batch API + claude-haiku-4-5

Traduzione in FR, DE, ES, NL, PL con adattamento culturale e SEO locale per ogni marketplace target. Batch processing per volumi alti.

### UC3 — Classificazione Automatica Categorie

**Stack:** Messages API + Tool Use + Supabase

Claude riceve categoria fornitore → consulta mapping esistenti in Supabase → propone mapping per ogni marketplace. Tool use per query diretta al database.

### UC4 — Customer Service Automatizzato

**Stack:** Make.com → Claude API → Supabase

Webhook marketplace (messaggio cliente) → Make scenario → Claude API con contesto ordine da Supabase → risposta automatica o draft per review.

### UC5 — Agente Autonomo Distribuzione

**Stack:** Claude Agent SDK + MCP (Supabase + Channable + Notion)

Agente che monitora nuovi prodotti fornitori → genera schede → pubblica su marketplace → traccia performance. Workflow end-to-end automatizzato.

### UC6 — Automazione UI Marketplace

**Stack:** Computer Use (Beta) + Claude Code

Per marketplace senza API: Claude controlla browser, naviga interfaccia, carica prodotti, aggiorna prezzi via screen interaction.

### UC7 — Analisi Cataloghi Fornitori

**Stack:** Messages API + Code Execution + Vision

Upload CSV/PDF fornitore → Claude analizza struttura → normalizza dati → mappa a schema Vendiamonoi → output CSV/JSON pulito.

---

## 8. Integrazioni Stack

### Claude + Make.com

Modulo HTTP con Claude API (non esiste modulo nativo Make per Anthropic).

**Pattern:** Trigger (webhook/schedule) → HTTP Module (POST a /v1/messages) → Router → Azioni

**Headers Make.com:**
```
x-api-key: {{anthropic_api_key}}
anthropic-version: 2023-06-01
Content-Type: application/json
```

### Claude + Supabase

- **Vector store:** pgvector per embeddings (text-embedding non nativo, usare altro provider o fine-tuning)
- **Caching risposte:** Tabella cache per risposte frequenti
- **Logging:** Tracciamento usage, costi, performance per SKU/marketplace
- **MCP Server:** Connessione diretta Claude → Supabase via MCP

### Claude + Notion

- **MCP Server Notion** — Claude legge/scrive direttamente su Notion workspace
- **Generazione contenuti** — Schede prodotto → pagine Notion automatiche
- **Knowledge base** — Notion come sorgente dati per prompt context

### Claude + Channable/ChannelEngine

- Claude genera feed ottimizzati per marketplace specifici
- A/B testing titoli AI-generated vs manuali
- Localizzazione automatica feed per paese

### Claude + Obsidian

- **MCP Server Obsidian** — Claude legge/scrive nel vault Obsidian
- Documentazione tecnica automatica
- Knowledge graph aziendale

---

## 9. Limitazioni

| Limitazione | Dettaglio | Workaround |
|-------------|-----------|------------|
| No embeddings nativo | Claude non ha API embeddings propria | Usare OpenAI text-embedding-3 o Cohere |
| Rate limit ai primi tier | Tier 1 = solo 50 RPM | Batch API, upgrade tier, caching |
| Computer Use in beta | Non production-ready | Solo per POC, non per workflow critici |
| No webhook nativi | Polling per batch status | Make.com per event-driven workflow |
| Costo Opus elevato | $5/$25 per 1M tokens | Usare Haiku/Sonnet per task bulk |
| Context window usage | 1M tokens ma costo proporzionale | Prompt caching, chunking |
| Streaming + extended thinking | Thinking tokens aumentano costo | Usare solo quando necessario |
| Image generation | Claude non genera immagini | Usare DALL-E, Midjourney, Flux |
| Channels in preview | Non GA, potrebbe cambiare | Usare Make.com per produzione |

---

## 10. Risorse

- [Claude API Documentation](https://docs.anthropic.com/)
- [Claude Platform Console](https://console.anthropic.com/)
- [Claude API Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- [Messages API Reference](https://docs.anthropic.com/en/api/messages)
- [Tool Use Guide](https://docs.anthropic.com/en/docs/agents-and-tools/tool-use/overview)
- [Computer Use Guide](https://docs.anthropic.com/en/docs/build-with-claude/computer-use)
- [Prompt Caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)
- [MCP Documentation](https://modelcontextprotocol.io/)
- [Agent SDK Overview](https://platform.claude.com/docs/en/agent-sdk/overview)
- [Python SDK — GitHub](https://github.com/anthropics/anthropic-sdk-python)
- [TypeScript SDK — GitHub](https://github.com/anthropics/anthropic-sdk-typescript)
- [Agent SDK Python — GitHub](https://github.com/anthropics/claude-agent-sdk-python)
- [Agent SDK TypeScript — GitHub](https://github.com/anthropics/claude-agent-sdk-typescript)
- [Claude Code Docs](https://code.claude.com/docs/en/)
- [OpenAI SDK Compatibility](https://docs.anthropic.com/en/api/openai-sdk)

---

## 11. Changelog

| Data | Versione | Modifiche | Autore |
|------|----------|-----------|--------|
| 31/03/2026 | 1.0 | Documentazione completa v1.0 — API Messages (tool use, computer use, batch, streaming, extended thinking, citations, prompt caching), MCP (architettura, ecosystem, 9+ server attivi), SDK (Python, TypeScript, Agent SDK Python/TS, MCP SDK), 7 Use Cases, 5 Integrazioni Stack | Claude (Cowork) |
