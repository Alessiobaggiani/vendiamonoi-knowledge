# Settore 7 — MCP (Model Context Protocol)

> Guida completa al Model Context Protocol: cos'è, come funziona l'architettura client-server, come configurare MCP server su OpenClaw, i server più popolari, sicurezza, e configurazione raccomandata per Vendiamonoi.

---

## Indice

1. [Cos'è MCP](#1-cosè-mcp)
2. [Architettura MCP](#2-architettura-mcp)
3. [MCP in OpenClaw](#3-mcp-in-openclaw)
4. [Configurazione — openclaw.json](#4-configurazione--openclawjson)
5. [Transport: stdio, HTTP, SSE](#5-transport-stdio-http-sse)
6. [Comandi CLI MCP](#6-comandi-cli-mcp)
7. [Server MCP Popolari](#7-server-mcp-popolari)
8. [Server MCP per E-commerce](#8-server-mcp-per-e-commerce)
9. [Per-Agent MCP Routing](#9-per-agent-mcp-routing)
10. [Sicurezza MCP](#10-sicurezza-mcp)
11. [Creare un MCP Server Custom](#11-creare-un-mcp-server-custom)
12. [MCP Roadmap 2026](#12-mcp-roadmap-2026)
13. [Configurazione Vendiamonoi](#13-configurazione-vendiamonoi)
14. [Fonti & Riferimenti](#14-fonti--riferimenti)

---

## 1. Cos'è MCP

**Model Context Protocol (MCP)** è un protocollo aperto creato da Anthropic (novembre 2024) che standardizza come le applicazioni AI si connettono a strumenti e fonti dati esterne.

### L'analogia USB-C

MCP è come **USB-C per l'AI**: un singolo standard che sostituisce decine di integrazioni custom. Prima di MCP, ogni tool richiedeva un'integrazione dedicata. Con MCP, un unico protocollo connette qualsiasi AI a qualsiasi servizio.

### Numeri chiave (marzo 2026)

| Dato | Valore |
|---|---|
| Server MCP disponibili | 5.000+ community, 500+ su npm |
| Server su ClawHub | 3.200+ |
| Protocollo comunicazione | JSON-RPC 2.0 |
| Specifica corrente | 2025-11-25 |
| Governance | Agentic AI Foundation (Linux Foundation) |
| Co-fondatori AAIF | Anthropic, Block, OpenAI |

### MCP vs API tradizionale

| Aspetto | API tradizionale | MCP |
|---|---|---|
| Integrazione | Custom per ogni servizio | Standard unico |
| Discovery | Documentazione manuale | Auto-discovery tool |
| Schema | Varia per API | Uniforme JSON-RPC |
| Bidirezionale | Raramente | Sì (client ↔ server) |
| Context-aware | No | Sì (resources + prompts) |

---

## 2. Architettura MCP

### Tre primitivi fondamentali

MCP standardizza tre tipi di oggetti che si muovono tra client e server:

| Primitivo | Direzione | Funzione | Esempio |
|---|---|---|---|
| **Resources** | Server → Client | Dati strutturati per contesto | File, record DB, documenti |
| **Tools** | Client → Server | Azioni eseguibili | `query_database`, `send_email` |
| **Prompts** | Server → Client | Template di workflow | "Analizza questo dataset" |

### Architettura Client-Server

```
┌─────────────────┐     JSON-RPC 2.0      ┌─────────────────┐
│                  │ ◄──────────────────► │                  │
│   MCP Client     │                      │   MCP Server     │
│   (OpenClaw)     │   Transport Layer    │   (Notion, DB,   │
│                  │   stdio / HTTP / SSE │    Shopify...)    │
└─────────────────┘                      └─────────────────┘
        │                                         │
        ▼                                         ▼
   AI Agent                              External Service
   (LLM + Logic)                         (API, Database,
                                          File System)
```

### Tre componenti core

| Componente | Funzione |
|---|---|
| **MCP Server Instance** | Orchestrazione: gestisce richieste, routing, connessioni persistenti |
| **Protocol Handler** | Comunicazione: formati messaggi standardizzati, gestione sessione, sicurezza |
| **Resource Managers** | Accesso: interfaccia unificata verso API esterne, DB, servizi |

### Ciclo di vita connessione

```
1. Initialize  → Client invia capabilities
2. Negotiate    → Server risponde con tool/resource list
3. Operate      → Scambio messaggi JSON-RPC
4. Terminate    → Chiusura pulita
```

---

## 3. MCP in OpenClaw

OpenClaw ha **supporto MCP nativo** dalla versione 2026.1. Può agire sia come:

- **MCP Client**: connette l'agente a server MCP esterni (Notion, Shopify, PostgreSQL...)
- **MCP Server**: espone le capacità di OpenClaw ad altri client MCP (es. Claude Desktop)

### Come OpenClaw usa MCP

| Modalità | Descrizione | Comando/Config |
|---|---|---|
| **Client** (il più comune) | OpenClaw si connette a server MCP per usare tool esterni | `mcpServers` in openclaw.json |
| **Server** | OpenClaw espone i suoi tool via MCP | `openclaw mcp serve` |
| **Bridge** | OpenClaw fa da ponte tra Claude.ai e i suoi tool | openclaw-mcp (npm package) |

### Skill vs MCP — Chiarimento

| Aspetto | Skill | MCP Server |
|---|---|---|
| Dove vive | Locale (`~/.openclaw/skills/`) | Processo esterno o remoto |
| Formato | SKILL.md (markdown + YAML) | Codice (Node.js, Python, Go) |
| Comunicazione | Letta dall'agente all'avvio | JSON-RPC runtime |
| Quando usare | Logica, workflow, regole | Accesso a servizi esterni |
| Esempio | "Come gestire un ordine" | Connessione API Shopify |

Le **skill orchestrano**, i **MCP server connettono**. Spesso una skill usa un MCP server come suo strumento.

---

## 4. Configurazione — openclaw.json

### File di configurazione principale

```json
// ~/.openclaw/openclaw.json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["@notionhq/mcp-server"],
      "env": {
        "NOTION_API_KEY": "ntn_xxx"
      }
    },
    "shopify": {
      "command": "npx",
      "args": ["@anthropic/shopify-mcp-server"],
      "env": {
        "SHOPIFY_STORE_URL": "https://vendiamonoi.myshopify.com",
        "SHOPIFY_ACCESS_TOKEN": "shpat_xxx"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@host:5432/db"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-filesystem", "/home/openclaw/workspace"]
    },
    "brave-search": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-brave-search"],
      "env": {
        "BRAVE_API_KEY": "BSAxx"
      }
    }
  }
}
```

### Struttura di un server entry

| Campo | Obbligatorio | Funzione |
|---|---|---|
| `command` | Sì | Comando per avviare il server (`npx`, `python`, `node`) |
| `args` | Sì | Argomenti del comando (package name, flags) |
| `env` | No | Variabili d'ambiente (API key, token) |
| `transport` | No | Tipo di trasporto (default: `stdio`) |
| `url` | Per remoti | URL del server remoto (HTTP/SSE) |

### Server remoto (Streamable HTTP)

```json
{
  "mcpServers": {
    "remote-db": {
      "transport": "streamable-http",
      "url": "https://mcp.vendiamonoi.it/db",
      "headers": {
        "Authorization": "Bearer xxx"
      }
    }
  }
}
```

---

## 5. Transport: stdio, HTTP, SSE

### Tre modalità di trasporto

| Transport | Come funziona | Quando usarlo | Latenza |
|---|---|---|---|
| **stdio** | OpenClaw spawna il server come processo figlio, comunica via stdin/stdout | Server locali, setup semplice | Minima |
| **Streamable HTTP** | Server come servizio HTTP, connessione stateless | Server remoti, multi-client, infrastruttura condivisa | Media |
| **SSE** (legacy) | Server-Sent Events, connessione persistente | Vecchi server, backward compatibility | Media |

### stdio (default e raccomandato per locale)

```
OpenClaw ──stdin──► MCP Server Process
         ◄─stdout──
```

- **Pro**: Zero configurazione rete, sicuro (nessuna porta aperta), semplice
- **Contro**: Solo locale, un client per server
- **Protocollo**: JSON-RPC 2.0 su stdin/stdout

### Streamable HTTP (nuovo standard per remoto)

```
OpenClaw ──HTTP POST──► MCP Server (porta 8080)
         ◄──Response────
```

- **Pro**: Multi-client, remoto, scalabile, load balancing
- **Contro**: Richiede configurazione rete, TLS obbligatorio in production
- **Sostituisce**: SSE per nuove implementazioni

### SSE (Server-Sent Events — legacy)

- Supportato per backward compatibility
- Nuovi progetti dovrebbero usare Streamable HTTP
- Connessione persistente unidirezionale (server → client)

---

## 6. Comandi CLI MCP

### Gestione configurazione

```bash
# Elencare tutti i server MCP configurati
openclaw mcp list

# Mostrare configurazione di un server specifico
openclaw mcp show notion

# Aggiungere/modificare un server
openclaw mcp set notion --command npx --args '@notionhq/mcp-server'

# Rimuovere un server
openclaw mcp unset notion

# Impostare variabile d'ambiente per un server
openclaw mcp set notion --env NOTION_API_KEY=ntn_xxx
```

### Modalità server (esporre OpenClaw come MCP server)

```bash
# Serve OpenClaw via MCP (per Claude Desktop o altri client)
openclaw mcp serve --url wss://gateway-host:18789 --token-file ~/.openclaw/gateway.token

# Con password
openclaw mcp serve --url wss://gateway-host:18789 --password-file ~/.openclaw/gateway.password
```

### Diagnostica

```bash
# Verificare che tutti i server siano raggiungibili
openclaw mcp check

# Log dettagliati per un server
openclaw mcp logs notion

# Restart di un server specifico
openclaw gateway restart
```

### Note importanti

- I comandi `mcp set/unset/show` **modificano solo la configurazione** (openclaw.json)
- **Non** avviano, fermano o testano la connessione al server
- Per applicare le modifiche: `openclaw gateway restart`

---

## 7. Server MCP Popolari

### Top 15 server per categoria

| # | Server | Categoria | Package | Funzione |
|---|---|---|---|---|
| 1 | **Filesystem** | Storage | `@modelcontextprotocol/server-filesystem` | Accesso file locali, zero config |
| 2 | **PostgreSQL** | Database | `@modelcontextprotocol/server-postgres` | Query SQL, schema, migrazioni |
| 3 | **Brave Search** | Search | `@modelcontextprotocol/server-brave-search` | Ricerca web (free tier disponibile) |
| 4 | **GitHub** | DevOps | `@modelcontextprotocol/server-github` | Repo, issue, PR, code search |
| 5 | **Notion** | Productivity | `@notionhq/mcp-server` | Ricerca semantica workspace |
| 6 | **Slack** | Communication | `@modelcontextprotocol/server-slack` | Messaggi, canali, ricerca |
| 7 | **Google Drive** | Storage | `@anthropic/google-drive-mcp-server` | Docs, Sheets, Slides |
| 8 | **Memory** | AI | `@modelcontextprotocol/server-memory` | Recall persistente tra sessioni |
| 9 | **Playwright** | Automation | `@anthropic/playwright-mcp-server` | Browser automation, screenshot |
| 10 | **Supabase** | Database | `@supabase/mcp-server` | DB + Auth + Storage + Edge Functions |
| 11 | **Prisma** | Database | `prisma mcp` (built-in) | TypeScript ORM, migrazioni |
| 12 | **Sentry** | Monitoring | `@sentry/mcp-server` | Error tracking, performance |
| 13 | **Docker** | DevOps | `@docker/mcp-server` | Container management |
| 14 | **Stripe** | Payments | `@stripe/mcp-server` | Pagamenti, fatture, subscriptions |
| 15 | **Apify** | Scraping | `@apify/mcp-server` | Web scraping, data extraction |

### Server raccomandati "First 5" per iniziare

1. **Filesystem** — accesso file, zero config
2. **Brave Search** — ricerca web gratuita
3. **Memory** — recall tra conversazioni
4. **PostgreSQL** — accesso database
5. **GitHub** — gestione repository

---

## 8. Server MCP per E-commerce

### Server rilevanti per Vendiamonoi

| Server | Funzione | Scopes/Permessi | Package |
|---|---|---|---|
| **Shopify** | Prodotti, ordini, inventario, clienti | `read_products`, `write_orders`, `read_inventory` | `@anthropic/shopify-mcp-server` |
| **Notion** | Knowledge base, procedure, cataloghi | Full access workspace | `@notionhq/mcp-server` |
| **Google Sheets** | Cataloghi fornitori, pricing, reportistica | Sheets API scopes | `@anthropic/google-sheets-mcp-server` |
| **PostgreSQL/Supabase** | Database operativo centrale | Tabelle specifiche (RLS) | `@supabase/mcp-server` |
| **Gmail** | Comunicazioni fornitori, notifiche | `gmail.send`, `gmail.readonly` | `@anthropic/gmail-mcp-server` |
| **Slack** | Comunicazione team interna | Canali specifici | `@modelcontextprotocol/server-slack` |
| **Apify** | Scraping prezzi competitor, monitoring listing | API key | `@apify/mcp-server` |
| **Brave Search** | Ricerca prodotti, trend, competitor | Free tier OK | `@modelcontextprotocol/server-brave-search` |

### Esempio: Shopify MCP per Vendiamonoi

```json
{
  "mcpServers": {
    "shopify": {
      "command": "npx",
      "args": ["@anthropic/shopify-mcp-server"],
      "env": {
        "SHOPIFY_STORE_URL": "https://vendiamonoi.myshopify.com",
        "SHOPIFY_ACCESS_TOKEN": "shpat_xxx"
      }
    }
  }
}
```

Scopes necessari per custom app Shopify:
- `read_products`, `write_products` — gestione catalogo
- `read_orders`, `write_orders` — gestione ordini
- `read_inventory`, `write_inventory` — sincronizzazione stock
- `read_customers` — dati cliente per customer service

---

## 9. Per-Agent MCP Routing

### Routing specifico per agente

OpenClaw supporta l'assegnazione di server MCP diversi a diversi agenti. Questo è fondamentale per il principio del **least privilege**.

```json
// ~/.openclaw/openclaw.json
{
  "mcpServers": {
    "shopify": { "command": "npx", "args": ["@anthropic/shopify-mcp-server"], "env": {...} },
    "notion": { "command": "npx", "args": ["@notionhq/mcp-server"], "env": {...} },
    "gmail": { "command": "npx", "args": ["@anthropic/gmail-mcp-server"], "env": {...} },
    "postgres": { "command": "npx", "args": ["@modelcontextprotocol/server-postgres"], "env": {...} }
  },
  "agents": {
    "order-manager": {
      "mcpServers": ["shopify", "postgres", "gmail"]
    },
    "catalog-enricher": {
      "mcpServers": ["shopify", "notion", "postgres"]
    },
    "customer-service": {
      "mcpServers": ["shopify", "gmail"]
    }
  }
}
```

### Regole di routing

- Agenti **senza** override in `agents` ereditano la lista globale `mcpServers`
- Agenti **con** override vedono **solo** i server elencati
- Permette isolamento: l'agente customer service non accede al database

---

## 10. Sicurezza MCP

### Panorama vulnerabilità (gennaio 2026)

Una scansione di 1.808 server MCP ha rivelato:

| Vulnerabilità | % dei server | Rischio |
|---|---|---|
| Shell/command injection | 43% | Critico |
| Tooling infrastructure | 20% | Alto |
| Authentication bypass | 13% | Critico |
| Path traversal | 10% | Alto |
| **Almeno 1 finding** | **66%** | — |

### Incidenti reali

| Incidente | Descrizione | Impatto |
|---|---|---|
| **Fake Postmark MCP** | Server npm con naming legittimo che esfiltra API key | Supply chain attack |
| **mcp-remote CVE** | CVSS 9.6, command injection via OAuth flow, 437K download | RCE critico |
| **Tool poisoning** | Tool definition manipolate che ingannano l'agente | Esecuzione non autorizzata |

### 5 livelli di rischio MCP

| Livello | Rischio | Mitigazione |
|---|---|---|
| 1. **Transport** | Intercettazione dati senza TLS | TLS/mTLS obbligatorio |
| 2. **Auth** | Token statici, credential leak | OAuth 2.1, rotazione automatica |
| 3. **Context** | Prompt injection via resource | Validazione input, schema check |
| 4. **Authorization** | Permessi troppo ampi | Least privilege, per-agent routing |
| 5. **Supply Chain** | Server malevoli su npm | Audit codice, pin versioni |

### Hardening checklist MCP

```yaml
# 10 regole di sicurezza MCP
1. LEGGERE IL CODICE SORGENTE prima di installare
   # La maggior parte dei server MCP è 200-500 righe
   # Cercare: child_process.exec, subprocess.run, HTTP calls non documentate

2. PIN delle versioni
   # "@notionhq/mcp-server@1.2.3" non "@notionhq/mcp-server@latest"

3. Verificare postinstall scripts
   # In package.json, cercare script postinstall sospetti

4. TLS obbligatorio per server remoti
   # Mai HTTP in chiaro per server con dati sensibili

5. Least privilege per API key
   # Shopify: solo scopes necessari, mai admin access

6. Per-agent routing
   # Ogni agente vede solo i server che gli servono

7. Monitoraggio runtime
   # Log delle chiamate MCP, alert su pattern anomali

8. Sandbox per server non fidati
   # Container Docker, chroot, namespace isolation

9. Variabili d'ambiente per credenziali
   # Mai hardcodare token in openclaw.json
   # Usare env: {} o file .env separato

10. Audit periodico
    # Revisione mensile dei server installati
    # Rimuovere quelli non più utilizzati
```

---

## 11. Creare un MCP Server Custom

### Quando creare un server custom

- Nessun server community per il tuo servizio (es. ChannelEngine API)
- Necessità di logica business specifica
- Requisiti di sicurezza particolari

### Struttura minima (Node.js)

```javascript
// server.js
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';

const server = new McpServer({
  name: 'vendiamonoi-channelengine',
  version: '1.0.0'
});

// Definire un tool
server.tool(
  'get_orders',
  'Recupera ordini da ChannelEngine',
  {
    status: { type: 'string', enum: ['NEW', 'IN_PROGRESS', 'SHIPPED'] },
    limit: { type: 'number', default: 50 }
  },
  async ({ status, limit }) => {
    const response = await fetch(
      `https://api.channelengine.com/v2/orders?statuses=${status}&limit=${limit}`,
      { headers: { 'X-CE-KEY': process.env.CHANNELENGINE_API_KEY } }
    );
    const data = await response.json();
    return { content: [{ type: 'text', text: JSON.stringify(data, null, 2) }] };
  }
);

// Definire una resource
server.resource(
  'order_stats',
  'channelengine://orders/stats',
  async () => ({
    contents: [{
      uri: 'channelengine://orders/stats',
      text: JSON.stringify(await getOrderStats())
    }]
  })
);

// Avviare con transport stdio
const transport = new StdioServerTransport();
await server.connect(transport);
```

### Registrare in openclaw.json

```json
{
  "mcpServers": {
    "channelengine": {
      "command": "node",
      "args": ["/home/openclaw/mcp-servers/channelengine/server.js"],
      "env": {
        "CHANNELENGINE_API_KEY": "ce_xxx"
      }
    }
  }
}
```

### SDK disponibili

| Linguaggio | Package | Note |
|---|---|---|
| **TypeScript/Node** | `@modelcontextprotocol/sdk` | Più maturo, raccomandato |
| **Python** | `mcp` (PyPI) | Secondo più usato |
| **Go** | `github.com/mark3labs/mcp-go` | Community |
| **Rust** | `mcp-rust-sdk` | Community, performance |
| **Java/Kotlin** | `mcp-kotlin-sdk` | Community |

---

## 12. MCP Roadmap 2026

### Governance

Da dicembre 2025, MCP è sotto la **Agentic AI Foundation (AAIF)** della Linux Foundation, co-fondata da Anthropic, Block e OpenAI.

### Roadmap ufficiale

| Timeline | Feature | Impatto |
|---|---|---|
| **Q2 2026** | Enterprise Auth (OAuth 2.1 + PKCE + SAML/OIDC) | Deployment in settori regolamentati |
| **Q3 2026** | Agent-to-Agent Communication | Un agente può chiamare un altro via MCP |
| **Q3 2026** | Transport Scalability | HTTP streaming migliorato, load balancing nativo |
| **Q4 2026** | Registry Standardization | Registry federato di server MCP |
| **2026+** | Governance Maturation | Processo RFC aperto, working groups |

### Agent-to-Agent (Q3 2026)

Permette a un agente di chiamare un altro agente come se fosse un server MCP. Crea architetture gerarchiche dove un orchestrator delega a sub-agenti specializzati.

```
Orchestrator Agent
    ├── Order Agent (via MCP)
    ├── Catalog Agent (via MCP)
    └── Customer Service Agent (via MCP)
```

Questo è particolarmente rilevante per Vendiamonoi: ogni reparto potrebbe avere il suo agente specializzato, coordinato da un orchestrator centrale.

---

## 13. Configurazione Vendiamonoi

### openclaw.json raccomandato

```json
{
  "mcpServers": {
    "shopify": {
      "command": "npx",
      "args": ["@anthropic/shopify-mcp-server"],
      "env": {
        "SHOPIFY_STORE_URL": "https://vendiamonoi.myshopify.com",
        "SHOPIFY_ACCESS_TOKEN": "${SHOPIFY_TOKEN}"
      }
    },
    "supabase": {
      "command": "npx",
      "args": ["@supabase/mcp-server"],
      "env": {
        "SUPABASE_URL": "${SUPABASE_URL}",
        "SUPABASE_SERVICE_KEY": "${SUPABASE_KEY}"
      }
    },
    "notion": {
      "command": "npx",
      "args": ["@notionhq/mcp-server"],
      "env": {
        "NOTION_API_KEY": "${NOTION_KEY}"
      }
    },
    "google-sheets": {
      "command": "npx",
      "args": ["@anthropic/google-sheets-mcp-server"],
      "env": {
        "GOOGLE_SERVICE_ACCOUNT_KEY": "${GOOGLE_SA_KEY}"
      }
    },
    "gmail": {
      "command": "npx",
      "args": ["@anthropic/gmail-mcp-server"],
      "env": {
        "GMAIL_CREDENTIALS": "${GMAIL_CREDS}"
      }
    },
    "brave-search": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-brave-search"],
      "env": {
        "BRAVE_API_KEY": "${BRAVE_KEY}"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-filesystem", "/home/openclaw/workspace"]
    }
  },
  "agents": {
    "order-manager": {
      "mcpServers": ["shopify", "supabase", "gmail"]
    },
    "catalog-enricher": {
      "mcpServers": ["shopify", "supabase", "notion", "google-sheets"]
    },
    "customer-service": {
      "mcpServers": ["shopify", "gmail"]
    },
    "analytics": {
      "mcpServers": ["supabase", "google-sheets"]
    },
    "researcher": {
      "mcpServers": ["brave-search", "filesystem"]
    }
  }
}
```

### Server MCP custom da creare per Vendiamonoi

| Server | Funzione | Priorità |
|---|---|---|
| `vendiamonoi-channelengine` | Ordini, listing, spedizioni ChannelEngine | Alta |
| `vendiamonoi-channable` | Feed prodotti, regole, mappatura | Alta |
| `vendiamonoi-marketplace-api` | API dirette marketplace (dove non c'è aggregator) | Media |
| `vendiamonoi-qonto` | Accesso bancario per riconciliazione | Bassa |

### Costi stimati server MCP

| Server | Costo | Note |
|---|---|---|
| Filesystem | $0 | Zero config |
| Brave Search | $0 | Free tier (2000 query/mese) |
| Notion | $0 | Solo API key Notion |
| Google Sheets | $0 | Service account gratuito |
| Shopify | $0 | Custom app, nessun costo aggiuntivo |
| Supabase | $0-25/mese | Free tier generoso |
| Gmail | $0 | Google Workspace già in uso |
| **Totale** | **$0-25/mese** | Solo infrastruttura MCP |

### Sicurezza: regole Vendiamonoi

```yaml
# Regole sicurezza MCP per Vendiamonoi
1. Solo server da npm con publisher verificato
2. Tutte le versioni pinnate
3. Variabili d'ambiente via .env file (mai in openclaw.json)
4. Per-agent routing obbligatorio
5. Audit trimestrale dei server installati
6. TLS per qualsiasi server remoto
7. Shopify: scopes minimi per operazione
8. Supabase: Row Level Security attivo
9. Log MCP centralizzati per audit
10. Nessun server custom senza code review
```

---

## 14. Fonti & Riferimenti

### Specifica ufficiale MCP
- Specifica: https://modelcontextprotocol.io/specification/2025-11-25
- GitHub MCP: https://github.com/modelcontextprotocol/modelcontextprotocol
- Server ufficiali: https://github.com/modelcontextprotocol/servers
- Wikipedia: https://en.wikipedia.org/wiki/Model_Context_Protocol

### Documentazione OpenClaw MCP
- CLI MCP: https://docs.openclaw.ai/cli/mcp
- OpenClaw MCP Bridge: https://github.com/freema/openclaw-mcp
- Helms OpenClaw MCP Server: https://github.com/Helms-AI/openclaw-mcp-server

### Guide e tutorial
- LaunchMyOpenClaw — MCP Guide 2026: https://launchmyopenclaw.com/openclaw-mcp-guide/
- SkyWork — Ultimate Guide MCP: https://skywork.ai/skypage/en/ultimate-guide-openclaw-mcp/
- Fast.io — MCP Setup Guide: https://fast.io/resources/openclaw-mcp-setup/
- AI Insider — Setup Tutorial 2026: https://aiinsider.in/ai-learning/openclaw-setup-tutorial-2026/
- SafeClaw — MCP Integration: https://safeclaw.io/blog/openclaw-mcp
- OpenClawVPS — My Setup for 12 Servers: https://openclawvps.io/blog/add-mcp-openclaw
- Composio MCP with OpenClaw: https://composio.dev/content/how-to-use-composio-mcp-with-openclaw

### Server MCP popolari
- Builder.io — Best MCP Servers 2026: https://www.builder.io/blog/best-mcp-servers-2026
- Skyvia — Top 12 MCP Servers: https://blog.skyvia.com/best-mcp-servers/
- K2view — Awesome MCP Servers: https://www.k2view.com/blog/awesome-mcp-servers
- MCP Market: https://mcpmarket.com/
- LobeHub MCP Marketplace: https://lobehub.com/mcp
- OpenClaw Launch — Best MCP Servers: https://openclawlaunch.com/guides/best-mcp-servers
- Nventory — E-commerce MCP Config: https://nventory.io/us/blog/openclaw-mcp-server-configuration-ecommerce-2026

### Sicurezza MCP
- Practical DevSecOps — MCP Vulnerabilities: https://www.practical-devsecops.com/mcp-security-vulnerabilities/
- Aembit — MCP Security Guide: https://aembit.io/blog/the-ultimate-guide-to-mcp-security-vulnerabilities/
- Red Hat — MCP Security Risks: https://www.redhat.com/en/blog/model-context-protocol-mcp-understanding-security-risks-and-controls
- MCP Security Best Practices (official): https://modelcontextprotocol.io/specification/draft/basic/security_best_practices
- Security Boulevard — MCP Risks: https://securityboulevard.com/2026/02/mcp-security-risks-and-best-practices-explained/
- Network Intelligence — Security Checklist: https://www.networkintelligence.ai/blogs/model-context-protocol-mcp-security-checklist/

### Roadmap
- MCP Roadmap 2026: https://blog.modelcontextprotocol.io/posts/2026-mcp-roadmap/
- OneReach — MCP Architecture: https://onereach.ai/blog/what-to-know-about-model-context-protocol/

---

> **Nota per Vendiamonoi**: MCP è il modo più potente per connettere OpenClaw ai servizi aziendali. Iniziare con Shopify + Supabase + Notion come primi 3 server. Creare un server custom per ChannelEngine come prima priorità. Usare per-agent routing dal giorno 1.

---

*Ultimo aggiornamento: aprile 2026*
*Documento parte della Master Class OpenClaw — vendiamonoi-knowledge*
