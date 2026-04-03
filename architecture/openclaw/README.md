# OpenClaw — Panoramica Completa & Architettura Core

## Settore 1 della Guida Completa OpenClaw per Vendiamonoi.it

**Versione:** 1.0
**Data:** Aprile 2026
**Tipo:** Deep-Dive Tecnico
**Righe:** ~1.200+
**Stato:** ✅ Completato

---

## Indice dei Settori OpenClaw

| # | Settore | Stato |
|---|---------|-------|
| 1 | **Panoramica & Architettura Core** (questo documento) | ✅ |
| 2 | Installazione & Setup | ⏳ |
| 3 | Sicurezza & Hardening | ⏳ |
| 4 | Modelli AI & Costi | ⏳ |
| 5 | Sistema Memory & Contesto Persistente | ⏳ |
| 6 | Skills & ClawHub | ⏳ |
| 7 | MCP (Model Context Protocol) | ⏳ |
| 8 | Automazione (Cron, Webhooks, Gmail) | ⏳ |
| 9 | Browser Control & Web Automation | ⏳ |
| 10 | Voice Mode & Device Nodes | ⏳ |
| 11 | Multi-Agent & Orchestrazione | ⏳ |
| 12 | Deployment Production & Scalabilità | ⏳ |

---

## 1. Cos'è OpenClaw

### 1.1 Definizione

OpenClaw è un agente AI autonomo, open-source e self-hosted, che trasforma i Large Language Model (LLM) da interfacce conversazionali passive a lavoratori digitali capaci di eseguire azioni reali. Non è un chatbot: è un runtime che collega modelli AI a software, file system, browser, API e dispositivi fisici.

OpenClaw gira come servizio Node.js persistente sulla propria macchina (o server) e si interfaccia con l'utente attraverso le app di messaggistica già in uso: WhatsApp, Telegram, Slack, Discord, Teams, Signal, iMessage e oltre 20 altri canali.

### 1.2 Posizionamento Strategico per Vendiamonoi

Per un'azienda come Vendiamonoi.it che gestisce:
- 20-30+ marketplace europei
- 100+ fornitori
- Milioni di SKU
- Processi multi-reparto (fornitori, catalogo, marketplace, ordini, CS, amministrazione)

OpenClaw rappresenta la possibilità di avere un **lavoratore digitale autonomo** che può:
- Monitorare e agire sui marketplace 24/7
- Processare ordini automaticamente
- Gestire comunicazioni con fornitori
- Eseguire task ripetitivi senza intervento umano
- Orchestrare workflow complessi cross-piattaforma

### 1.3 Dati Chiave

| Parametro | Valore |
|-----------|--------|
| Licenza | Open Source (MIT) |
| Creatore | Peter Steinberger (Austria) |
| Primo rilascio | Novembre 2025 (come Clawdbot) |
| Nome attuale | OpenClaw (dal 30 Gennaio 2026) |
| GitHub Stars | 247.000+ (Marzo 2026) |
| Runtime | Node.js 24+ (raccomandato), Node.js 22.16+ (minimo) |
| Architettura | Gateway WebSocket + Agent Runtime |
| Canali supportati | 25+ (WhatsApp, Telegram, Slack, Discord, Teams, Signal, iMessage, ecc.) |
| Skills disponibili | 13.729+ su ClawHub (5.211 curate) |
| Provider LLM | 30+ (OpenAI, Claude, Gemini, Groq, Ollama, OpenRouter, ecc.) |
| Costo software | Gratuito (open-source) — paghi solo API LLM + hosting |
| Sito ufficiale | https://openclaw.ai |
| Documentazione | https://docs.openclaw.ai |
| Repository | https://github.com/openclaw/openclaw |

---

## 2. Storia e Timeline

### 2.1 Cronologia Completa

| Data | Evento |
|------|--------|
| Novembre 2025 | Peter Steinberger crea **Clawdbot** in 1 ora — idea iniziale: controllare il lavoro del PC da WhatsApp |
| 14 Nov 2025 | Pubblicazione su GitHub come Clawdbot |
| Gennaio 2026 | Esplosione virale — 60.000+ stelle GitHub in 72 ore |
| 27 Gen 2026 | Rinominato **Moltbot** dopo reclamo trademark di Anthropic (il nome era un gioco su "Claude") |
| 30 Gen 2026 | Rinominato **OpenClaw** — Steinberger: "Moltbot never quite rolled off the tongue" |
| Feb 2026 | Superamento 100.000 GitHub stars |
| 14 Feb 2026 | Steinberger annuncia ingresso in OpenAI; progetto trasferito a fondazione open-source |
| Mar 2026 | 247.000+ stars, 47.700+ forks — progetto AI open-source più veloce nella storia |

### 2.2 Filosofia del Progetto

OpenClaw nasce con una filosofia precisa:
- **Local-first**: i tuoi dati restano sulla tua macchina
- **Bring Your Own Key**: nessun vendor lock-in, scegli il modello LLM
- **Chat-native**: usi le app che già conosci come interfaccia
- **Skills-based**: estendibile con moduli come le app di uno smartphone
- **Open-source**: trasparenza totale, nessun costo di licenza

Jensen Huang (CEO NVIDIA) ha definito OpenClaw "il sistema operativo per l'AI personale".

---

## 3. Architettura Core

### 3.1 Schema Architetturale ad Alto Livello

OpenClaw segue un'architettura **hub-and-spoke** con un unico punto centrale (il Gateway) che coordina tutto:

```
┌─────────────────────────────────────────────────────────────────┐
│                        UTENTE / OPERATORE                       │
│  WhatsApp │ Telegram │ Slack │ Discord │ Teams │ CLI │ Web UI  │
└────────────────────────────┬────────────────────────────────────┘
                             │ WebSocket / HTTP
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                     🦞 GATEWAY (Control Plane)                  │
│                    ws://127.0.0.1:18789                         │
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────────┐   │
│  │ Channel  │  │ Session  │  │ Message  │  │   Device      │   │
│  │ Adapters │  │  Store   │  │ Router   │  │   Registry    │   │
│  └──────────┘  └──────────┘  └──────────┘  └───────────────┘   │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────────┐   │
│  │  Cron    │  │ Webhook  │  │  Auth &  │  │   Config      │   │
│  │ Scheduler│  │ Handler  │  │ Pairing  │  │   Manager     │   │
│  └──────────┘  └──────────┘  └──────────┘  └───────────────┘   │
└────────────────────────────┬────────────────────────────────────┘
                             │ RPC / Streaming
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                     🧠 AGENT RUNTIME (Pi)                       │
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────────┐   │
│  │  LLM     │  │  Tool    │  │  Memory  │  │   Skills      │   │
│  │ Provider │  │ Executor │  │  System  │  │   Engine      │   │
│  └──────────┘  └──────────┘  └──────────┘  └───────────────┘   │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────────┐   │
│  │  MCP     │  │ Browser  │  │ Sandbox  │  │   Context     │   │
│  │ Servers  │  │ Control  │  │ (Docker) │  │   Assembler   │   │
│  └──────────┘  └──────────┘  └──────────┘  └───────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                     📱 DEVICE NODES                             │
│         macOS App │ iOS Node │ Android Node                     │
│  (camera, screen, notifications, location, contacts, ecc.)     │
└─────────────────────────────────────────────────────────────────┘
```

### 3.2 I Tre Livelli dell'Architettura

**Livello 1 — Gateway (Il Centralino)**

Il Gateway è il cuore di OpenClaw. È un server WebSocket che gira sulla porta 18789 (di default) e svolge queste funzioni:

- **Message Routing**: riceve messaggi da tutti i canali e li instrada verso la sessione giusta dell'agente
- **Session Management**: mantiene lo stato delle conversazioni, la storia, e il contesto
- **Device Registry**: gestisce le connessioni dei dispositivi (Mac, iPhone, Android)
- **Authentication**: gestisce il pairing dei dispositivi e l'autenticazione
- **Cron & Automation**: esegue i job schedulati e gestisce i webhook
- **Control UI**: serve l'interfaccia web di amministrazione

Tecnicamente, il Gateway è un singolo processo Node.js long-running. Tutti i client (CLI, macOS app, iOS, Android, Web UI, automazioni) si connettono via WebSocket con frame di testo JSON. Il primo frame deve essere un messaggio `connect` con device identity.

**Livello 2 — Agent Runtime (Il Cervello)**

L'Agent Runtime è dove avviene l'intelligenza artificiale. OpenClaw usa internamente il framework **Pi** (una libreria di coding agent sviluppata da Armin Ronacher) per gestire le interazioni AI.

Il runtime fa 4 cose ad ogni turno:
1. **Resolve Session** — identifica la sessione e carica il contesto
2. **Assemble Context** — assembla memoria, istruzioni di sistema, storia conversazione
3. **Stream Model Response** — invia la richiesta al LLM e riceve la risposta in streaming, eseguendo tool call in tempo reale
4. **Persist State** — salva lo stato aggiornato su disco

Internamente, OpenClaw non spawna Pi come subprocess separato, ma lo importa direttamente via `createAgentSession()` dal pacchetto `@mariozechner/pi-agent-core`. Questo significa zero overhead di comunicazione inter-processo.

Quando il modello richiede l'uso di un tool (es. eseguire un comando bash, leggere un file, navigare un sito web), il runtime:
1. Intercetta la tool call
2. Esegue il tool (potenzialmente in un sandbox Docker)
3. Streama il risultato indietro nel flusso di generazione del modello
4. Il modello incorpora il risultato e continua

**Livello 3 — Device Nodes (Le Mani)**

I Device Nodes sono le estensioni fisiche di OpenClaw sui dispositivi dell'utente:

- **macOS Node**: menu bar app con Voice Wake/PTT, Talk Mode overlay, WebChat, debug tools, controllo remoto del gateway
- **iOS Node**: Canvas, Voice Wake, Talk Mode, camera, screen recording, Bonjour + device pairing
- **Android Node**: chat sessions, voice tab, Canvas, camera/screen recording, comandi dispositivo (notifiche, location, SMS, foto, contatti, calendario, motion, app updates)

I nodi si connettono al Gateway via WebSocket e pubblicano una mappa delle loro capabilities e permessi. Le azioni vengono invocate localmente tramite `node.invoke`:
- `system.run` — esecuzione comandi locali (con awareness dei permessi TCC su macOS)
- `system.notify` — notifiche utente
- `camera.*` — accesso fotocamera
- `screen.record` — registrazione schermo
- `location.get` — geolocalizzazione

---

## 4. Il Gateway in Dettaglio

### 4.1 WebSocket Control Plane

Il Gateway opera come un WebSocket server su `ws://127.0.0.1:18789`. Il protocollo è semplice:

- **Transport**: WebSocket con text frame contenenti JSON
- **Primo frame obbligatorio**: messaggio `connect` con device identity
- **Binding**: di default, bind su `127.0.0.1` (localhost only — sicurezza)
- **Pairing**: nuovi device ID richiedono approvazione; il Gateway emette un device token per connessioni successive

### 4.2 Componenti Interni del Gateway

**Channel Adapters**
Ogni piattaforma di messaggistica ha un adapter dedicato che implementa un'interfaccia standard. L'adapter normalizza i messaggi in entrata/uscita in modo che il resto di OpenClaw non debba preoccuparsi delle differenze tra piattaforme.

Adapters built-in:
- WhatsApp (via Baileys)
- Telegram (via grammY)
- Slack (via Bolt)
- Discord (via discord.js)
- Signal
- BlueBubbles (iMessage)
- iMessage legacy
- IRC
- Microsoft Teams
- Matrix
- Google Chat
- Feishu
- LINE
- Mattermost
- Nextcloud Talk
- Nostr
- Synology Chat
- Tlon
- Twitch
- Zalo (business + personal)
- WeChat
- WebChat (built-in)

**Session Store**
Le sessioni sono salvate in `~/.openclaw/agents/<agentId>/sessions` con chiave `agent:main:<mainKey>`. Ogni sessione mantiene:
- Storia della conversazione
- Task pendenti
- Contesto di routing
- Stato across channel interactions

**Message Router**
Il routing è deterministico:
- **DM (messaggi diretti)**: collassano nella sessione principale dell'agente
- **Gruppi e canali**: rimangono isolati per canale
- **Cross-channel identity**: se si bindano le identità allo stesso utente, la conversazione è condivisa tra WhatsApp, Telegram, ecc.

### 4.3 Configurazione del Gateway

Tutta la configurazione risiede in `~/.openclaw/openclaw.json` (formato JSON5, supporta commenti e trailing commas).

Sezioni principali:
- `agents` — configurazione agenti (workspace, modello, permessi elevati, heartbeat)
- `agents.list` — lista agenti multipli con workspace separati
- `bindings` — mapping canale → agente
- `channels` — configurazione dei canali di messaggistica
- `mcp` — configurazione MCP servers
- `models` — provider e modelli AI
- `memory` — configurazione sistema di memoria
- `cron` — job schedulati

Comandi CLI per configurazione:
```bash
openclaw config show                              # visualizza config attuale
openclaw config set agents.defaults.model.primary  # imposta valore specifico
openclaw doctor                                    # diagnostica configurazione
```

---

## 5. Sistema dei Canali (Channel Architecture)

### 5.1 Architettura a Plugin

I canali seguono un'architettura a plugin unificata. Ogni piattaforma implementa un'interfaccia provider standard che include:
- Connessione/disconnessione
- Ricezione messaggi
- Invio messaggi
- Gestione media (immagini, file, audio, video)
- Gestione gruppi
- Gestione stati (online, typing, ecc.)

### 5.2 Normalizzazione dei Messaggi

Ogni adapter converte i messaggi platform-specific in un formato normalizzato interno. Questo significa che:
- Un messaggio WhatsApp con immagine viene trattato identicamente a un messaggio Telegram con immagine
- Le risposte vengono ri-convertite nel formato della piattaforma di origine
- Il routing è trasparente rispetto alla piattaforma

### 5.3 Multi-Channel Setup

Un singolo Gateway può gestire contemporaneamente:
- Tutti i canali configurati
- Sessioni multiple per canale
- Routing messaggi cross-channel (stessa conversazione su più piattaforme)

Esempio pratico per Vendiamonoi: un fornitore scrive su WhatsApp, l'agente OpenClaw risponde. Lo stesso fornitore continua la conversazione su Telegram — OpenClaw mantiene il contesto se le identità sono bindate.

---

## 6. Agent Runtime in Dettaglio

### 6.1 Integrazione Pi

OpenClaw usa il Pi SDK per incorporare un coding agent nel suo gateway di messaggistica. L'implementazione è in `src/agents/piembeddedrunner.ts`.

Caratteristiche chiave:
- **Embedded, non RPC**: Pi viene importato direttamente (non come processo separato)
- **Streaming bidirezionale**: risposte e tool call vengono stremate in tempo reale
- **Tool interception**: il runtime intercetta le richieste di tool e le esegue
- **Sandbox policy**: i tool possono essere eseguiti in un Docker sandbox a seconda della policy della sessione

### 6.2 Tool Execution Pipeline

Quando il modello AI richiede un'azione:

```
Modello → Tool Call Request → Runtime Intercept → Sandbox Check → Tool Execution → Result → Model Stream
```

Tool built-in principali:
- **File System**: lettura/scrittura file, navigazione directory
- **Shell/Bash**: esecuzione comandi terminale
- **Browser**: navigazione web, scraping, form filling via Chrome DevTools Protocol
- **MCP Tools**: qualsiasi tool esposto da un MCP server configurato
- **Device Actions**: azioni sui nodi dispositivo (camera, notifiche, ecc.)
- **Agent-to-Agent**: comunicazione tra sessioni agente

### 6.3 Context Assembly

Ad ogni turno, il runtime assembla il contesto completo per il modello:
1. **System prompt** — istruzioni di base dell'agente
2. **Memory** — MEMORY.md (long-term) + daily notes (short-term)
3. **Session history** — storia della conversazione corrente
4. **Tool definitions** — lista dei tool disponibili
5. **Skill instructions** — istruzioni delle skills attive
6. **MCP resources** — risorse esposte dai server MCP

---

## 7. Provider LLM Supportati

### 7.1 Provider Built-in

OpenClaw supporta 30+ provider LLM nativamente:

**Tier 1 — Provider Principali:**
- OpenAI (GPT-5.2, GPT-5, GPT-4.5-preview, GPT-4o, GPT-4o-mini)
- Anthropic Claude (Opus 4.5, Sonnet 4.5, Haiku 4.5)
- Google Gemini (via GEMINI_API_KEY)
- Google Vertex AI

**Tier 2 — Provider Specializzati:**
- Groq (Llama models su hardware custom — velocità estrema)
- xAI (Grok)
- Mistral
- Deepseek
- Together AI

**Tier 3 — Aggregatori:**
- OpenRouter (accesso a centinaia di modelli con un singolo account)
- LiteLLM (proxy universale)
- Vercel AI Gateway

**Tier 4 — Modelli Locali:**
- Ollama (auto-detected su http://127.0.0.1:11434/v1)
- vLLM
- SGLang

**Tier 5 — Altri:**
- GitHub Copilot, Cloudflare, NVIDIA, Cerebras, Perplexity, Hugging Face, MiniMax, Moonshot AI, Qwen, Venice AI, Volcengine, Xiaomi MiMo, Z.AI

### 7.2 Configurazione Modelli

```json5
// ~/.openclaw/openclaw.json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-sonnet-4-5",  // modello principale
        "fallback": "openai/gpt-4o"                // fallback automatico
      }
    }
  }
}
```

Feature avanzate:
- **Model failover**: se il modello primario fallisce, switch automatico al fallback
- **API key rotation**: rotazione automatica tra chiavi API multiple
- **OAuth integration**: autenticazione OAuth per provider che lo supportano
- **Spending caps**: limite di spesa per API key (consigliato: $20/mese per uso personale)

---

## 8. Monorepo e Struttura del Codice

### 8.1 Organizzazione

OpenClaw è organizzato come monorepo gestito con pnpm:

```
openclaw/
├── packages/
│   ├── core/           # Framework core
│   ├── gateway/        # Control plane WebSocket server
│   ├── agent/          # Agent runtime (Pi integration)
│   ├── cli/            # Command-line interface
│   ├── sdk/            # SDK per sviluppatori
│   ├── ui/             # Web UI (Control Panel + WebChat)
│   └── channels/       # Channel adapters
├── skills/             # Built-in skills
├── docker/             # Dockerfile e docker-compose
├── docs/               # Documentazione
└── tests/              # Test suite
```

### 8.2 Stack Tecnologico

| Componente | Tecnologia |
|------------|------------|
| Runtime | Node.js 24+ |
| Package Manager | pnpm |
| Agent Core | Pi (@mariozechner/pi-agent-core) |
| WebSocket | ws (libreria Node.js) |
| WhatsApp | Baileys |
| Telegram | grammY |
| Slack | Bolt |
| Discord | discord.js |
| Browser | Chrome DevTools Protocol (CDP) |
| Database locale | SQLite (per memory/RAG) |
| Container | Docker / Docker Compose |
| Voice/TTS | ElevenLabs API |
| Wake Word | Porcupine engine |

---

## 9. Sponsor e Ecosystem

### 9.1 Sponsor Principali
- **OpenAI** — il creatore è entrato in OpenAI
- **NVIDIA** — ha annunciato NemoClaw per la community OpenClaw
- **Vercel** — hosting e AI Gateway
- **Blacksmith** — infrastruttura
- **Convex** — database

### 9.2 Community
- **Discord**: discord.gg/clawd
- **GitHub Discussions**: per RFC e proposte
- **ClawHub**: registry ufficiale delle skills (13.729+ skills)
- **Moltbook**: social network per agenti AI (lanciato da Matt Schlicht)

### 9.3 Release Channels

| Canale | Descrizione | Comando |
|--------|-------------|--------|
| stable | Release taggate (vYYYY.M.D) | `openclaw update --channel stable` |
| beta | Prerelease con npm dist-tag beta | `openclaw update --channel beta` |
| dev | Head del branch main | `openclaw update --channel dev` |

---

## 10. Rilevanza Strategica per Vendiamonoi

### 10.1 Mapping Architettura OpenClaw → Reparti Vendiamonoi

| Componente OpenClaw | Reparto Vendiamonoi | Use Case |
|--------------------|--------------------|----------|
| Channel Adapters (WhatsApp/Telegram) | Reparto Fornitori | Comunicazione automatica con 100+ fornitori |
| Cron Jobs + Webhooks | Reparto Marketplace | Monitoraggio prezzi, stock, KPI 24/7 |
| Browser Control | Reparto Catalogo | Scraping listini, verifica listing, screenshot |
| MCP Servers (Shopify, Notion) | Reparto Catalogo/Data | Sincronizzazione cataloghi cross-piattaforma |
| Skills Engine | Reparto Ordini | Automazione evasione ordini |
| Multi-Agent | Customer Service | Agenti specializzati per lingua/marketplace |
| Memory System | Tutti i reparti | Contesto persistente su fornitori, prodotti, policy |
| Gmail Pub/Sub | Amministrazione | Automazione fatturazione, riconciliazione |

### 10.2 Vantaggi Architetturali per Vendiamonoi

1. **Local-first = Dati sotto controllo**: i dati dei fornitori, cataloghi, e ordini non escono dalla tua infrastruttura
2. **Multi-channel = Un agente, tutti i canali**: un fornitore usa WhatsApp, un altro Telegram, un altro email — stesso agente
3. **Multi-agent = Specializzazione**: un agente per ordini, uno per CS, uno per pricing — coordinati dal Gateway
4. **Skills ecosystem = Estendibilità infinita**: 13.729+ skills pronte, possibilità di creare skills custom per i workflow Vendiamonoi
5. **MCP = Integrazione nativa**: connessione diretta a Shopify, Notion, database, API marketplace
6. **Cron = Operazioni autonome**: report giornalieri, monitoraggio stock, alert pricing — tutto automatico

---

## Fonti e Riferimenti

- [OpenClaw GitHub Repository](https://github.com/openclaw/openclaw)
- [OpenClaw Official Site](https://openclaw.ai)
- [OpenClaw Documentation](https://docs.openclaw.ai)
- [OpenClaw Wikipedia](https://en.wikipedia.org/wiki/OpenClaw)
- [OpenClaw Architecture Deep Dive — DeepWiki](https://deepwiki.com/openclaw/openclaw/15.1-architecture-deep-dive)
- [OpenClaw Channel Architecture — DeepWiki](https://deepwiki.com/openclaw/openclaw/4.1-channel-architecture)
- [Pi: The Minimal Agent — Armin Ronacher](https://lucumr.pocoo.org/2026/1/31/pi/)
- [OpenClaw Provider Directory](https://docs.openclaw.ai/providers)
- [OpenClaw Configuration Reference](https://docs.openclaw.ai/gateway/configuration)
- [NVIDIA NemoClaw Announcement](https://nvidianews.nvidia.com/news/nvidia-announces-nemoclaw)
- [OpenClaw Security Guide — Contabo](https://contabo.com/blog/openclaw-security-guide-2026/)
- [Awesome OpenClaw Skills](https://github.com/VoltAgent/awesome-openclaw-skills)
