# 11 — Multi-Agent & Orchestrazione

> Guida tecnica completa al sistema multi-agent di OpenClaw: subagent, agent teams, orchestrazione, Mission Control e pattern enterprise per Vendiamonoi.it

---

## Indice

1. [Panoramica Multi-Agent](#1-panoramica-multi-agent)
2. [Architettura Multi-Agent](#2-architettura-multi-agent)
3. [Subagent: Concetti Fondamentali](#3-subagent-concetti-fondamentali)
4. [Configurazione Subagent](#4-configurazione-subagent)
5. [Spawn e Delegation](#5-spawn-e-delegation)
6. [maxSpawnDepth e Nesting](#6-maxspawndepth-e-nesting)
7. [Agent Teams](#7-agent-teams)
8. [Orchestration Patterns](#8-orchestration-patterns)
9. [Mission Control](#9-mission-control)
10. [YAML Multi-Agent Configuration](#10-yaml-multi-agent-configuration)
11. [Routing e Channel Dispatch](#11-routing-e-channel-dispatch)
12. [Cost Optimization Multi-Agent](#12-cost-optimization-multi-agent)
13. [Sicurezza e Governance](#13-sicurezza-e-governance)
14. [Configurazione Vendiamonoi](#14-configurazione-vendiamonoi)

---

## 1. Panoramica Multi-Agent

OpenClaw non è limitato a un singolo agente. Il sistema supporta **architetture multi-agent** dove più agenti AI collaborano, si delegano task e lavorano in parallelo.

### Tre livelli di complessità

```
┌─────────────────────────────────────────────────────┐
│  Livello 1: Single Agent                            │
│  Un agente → un task → un risultato                 │
├─────────────────────────────────────────────────────┤
│  Livello 2: Subagent (Hub-and-Spoke)                │
│  Agente principale → spawna subagent → raccoglie    │
├─────────────────────────────────────────────────────┤
│  Livello 3: Agent Teams (Swarm)                     │
│  Più agenti → comunicano tra loro → coordinamento   │
│  autonomo o orchestrato                             │
└─────────────────────────────────────────────────────┘
```

### Perché multi-agent?

| Problema Single Agent | Soluzione Multi-Agent |
|---|---|
| Context window limitata | Ogni agente ha il suo context |
| Task troppo complesso | Decomposizione in subtask |
| Costo alto (tutto su Opus) | Worker su modelli economici |
| Nessun parallelismo | Subagent eseguono in parallelo |
| Single point of failure | Agenti indipendenti, resilienza |

---

## 2. Architettura Multi-Agent

### Schema architetturale

```
                    ┌──────────────────┐
                    │   Gateway :18789 │
                    │   (WebSocket)    │
                    └────────┬─────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
     ┌────────▼───┐  ┌──────▼─────┐  ┌────▼────────┐
     │  Agent A   │  │  Agent B   │  │  Agent C    │
     │  (Primary) │  │  (Worker)  │  │  (Worker)   │
     │  Opus 4    │  │  Sonnet 4  │  │  Ollama     │
     │  Orchestr. │  │  Code/Data │  │  Local LLM  │
     └──┬────┬────┘  └────────────┘  └─────────────┘
        │    │
   ┌────▼┐ ┌▼────┐
   │Sub-1│ │Sub-2│    (Subagent figli di Agent A)
   │Haiku│ │Flash│
   └─────┘ └─────┘
```

### Componenti chiave

| Componente | Ruolo | Descrizione |
|---|---|---|
| **Gateway** | Router centrale | Riceve richieste, le instrada all'agente corretto |
| **Primary Agent** | Orchestratore | Riceve input utente, decompone task, delega |
| **Worker Agent** | Esecutore specializzato | Esegue task specifici (code, data, search) |
| **Subagent** | Figlio temporaneo | Spawnato on-demand, termina e riporta risultato |
| **Shared Tool Pool** | Risorse condivise | Tool MCP accessibili da più agenti |
| **Mission Control** | Dashboard governance | UI per monitorare, approvare, gestire agenti |

---

## 3. Subagent: Concetti Fondamentali

I **subagent** sono sessioni figlio spawnate da un agente esistente. Ogni subagent:

- Ha il proprio **context window** (separato dal parent)
- Può usare un **modello diverso** (es. parent su Opus, subagent su Haiku)
- Esegue in **background** nella propria sessione
- **Annuncia il risultato** al parent quando termina
- Viene **archiviato automaticamente** dopo un timeout

### Ciclo di vita subagent

```
Parent Agent                    Subagent
     │                              │
     │── spawn ────────────────────►│
     │                              │ (lavora nel suo context)
     │                              │ (usa i suoi tool)
     │                              │ (può avere modello diverso)
     │◄── result ──────────────────│
     │                              │ (sessione archiviata)
     ▼                              ▼
```

### Tipi di subagent built-in

| Tipo | Scopo | Tool disponibili | Quando viene usato |
|---|---|---|---|
| **Explore** | Ricerca codebase, read-only | Read, Glob, Grep, WebSearch | Analisi, comprensione codice |
| **General-purpose** | Task complessi multi-step | Tutti (Read, Write, Edit, Bash) | Modifica codice, task articolati |
| **Custom** | Definito dall'utente | Configurabili | Specializzazioni specifiche |

---

## 4. Configurazione Subagent

### File di configurazione

In `~/.openclaw/openclaw.json`:

```json
{
  "agents": {
    "defaults": {
      "subagents": {
        "model": "anthropic/claude-haiku-4-5",
        "maxSpawnDepth": 1,
        "maxChildrenPerAgent": 5,
        "archiveAfterMinutes": 60,
        "allowedTools": ["read", "glob", "grep", "web_search"],
        "blockedTools": ["exec", "write"]
      }
    }
  }
}
```

### Parametri chiave

| Parametro | Default | Descrizione |
|---|---|---|
| `model` | Eredita dal parent | Modello AI per i subagent |
| `maxSpawnDepth` | `1` | Profondità massima di nesting (1 = no sub-sub) |
| `maxChildrenPerAgent` | `5` | Max figli attivi per agente contemporaneamente |
| `archiveAfterMinutes` | `60` | Minuti prima dell'archiviazione automatica |
| `allowedTools` | Tutti | Tool consentiti ai subagent |
| `blockedTools` | Nessuno | Tool bloccati per i subagent |

### Configurazione per-agente

```json
{
  "agents": [
    {
      "id": "orchestrator",
      "model": { "primary": "anthropic/claude-opus-4" },
      "subagents": {
        "model": "anthropic/claude-haiku-4-5",
        "maxSpawnDepth": 2,
        "maxChildrenPerAgent": 8
      }
    }
  ]
}
```

---

## 5. Spawn e Delegation

### Metodi di spawn

#### 1. One-shot spawn (CLI)

```bash
# Spawna un subagent per task singolo
/subagents spawn "Analizza tutti i file CSV nella cartella /data e genera un report"

# Con modello specifico
/subagents spawn --model claude-haiku-4-5 "Conta le righe di ogni file .py"
```

#### 2. Programmatic spawn (API)

```javascript
// Spawn via sessions_spawn
const result = await gateway.sessions_spawn({
  parentSessionId: "session-123",
  prompt: "Analizza il catalogo prodotti e trova duplicati",
  model: "anthropic/claude-sonnet-4",
  tools: ["read", "glob", "grep"],
  thread: false  // one-shot (true = persistente)
});
```

#### 3. Spawn persistente (thread-bound)

```javascript
// Sessione persistente - rimane attiva per interazioni multiple
const worker = await gateway.sessions_spawn({
  parentSessionId: "session-123",
  prompt: "Sei un esperto di marketplace pricing",
  thread: true,
  mode: "session"  // mantiene stato tra messaggi
});

// Invia messaggi successivi al worker
await gateway.sessions_message(worker.sessionId, "Analizza i prezzi Amazon IT");
await gateway.sessions_message(worker.sessionId, "Ora confronta con eBay DE");
```

### Pattern di delegation

```
┌─────────────────────────────────────────────────┐
│  Pattern 1: Fan-out / Fan-in                    │
│                                                 │
│  Orchestrator ──┬── Worker A (Amazon)           │
│                 ├── Worker B (eBay)       ──► Merge Results
│                 └── Worker C (Kaufland)          │
│                                                 │
├─────────────────────────────────────────────────┤
│  Pattern 2: Pipeline (Sequenziale)              │
│                                                 │
│  Fetch Data ──► Clean Data ──► Analyze ──► Report│
│  (Worker A)    (Worker B)    (Worker C)  (Parent)│
│                                                 │
├─────────────────────────────────────────────────┤
│  Pattern 3: Specialist Routing                  │
│                                                 │
│  Orchestrator ──► if(code) → Code Worker        │
│                 ► if(data) → Data Worker         │
│                 ► if(web)  → Search Worker       │
│                                                 │
├─────────────────────────────────────────────────┤
│  Pattern 4: Hierarchical                        │
│                                                 │
│  CEO Agent ──► Manager A ──┬── Worker A1        │
│              ► Manager B   ├── Worker A2        │
│                            └── Worker A3        │
└─────────────────────────────────────────────────┘
```

---

## 6. maxSpawnDepth e Nesting

### Livelli di profondità

```
maxSpawnDepth: 1 (Default)
─────────────────────────
Main Agent ──► Subagent (leaf)
               └── NON può spawnare figli

maxSpawnDepth: 2
─────────────────────────
Main Agent ──► Orchestrator Sub ──► Worker Sub-Sub
               (depth 1)           (depth 2, leaf)
               Ha sessions_spawn   NON ha sessions_spawn

maxSpawnDepth: 3 (raro, sconsigliato)
─────────────────────────
Main ──► Orch ──► Sub-Orch ──► Worker
          (1)      (2)          (3, leaf)
```

### Tool disponibili per depth

| Depth | Tool di sessione | Ruolo tipico |
|---|---|---|
| 0 (Main) | Tutti | Orchestratore principale |
| 1 (quando maxSpawnDepth ≥ 2) | `sessions_spawn`, `subagents`, `sessions_list`, `sessions_history` | Sub-orchestratore |
| 1 (quando maxSpawnDepth == 1) | Nessun tool sessione | Leaf worker |
| 2+ | Nessun tool sessione | Sempre leaf worker |

### Perché limitare la profondità

- **Costo**: ogni livello moltiplica i token consumati
- **Complessità**: debug diventa esponenzialmente difficile
- **Latenza**: ogni spawn aggiunge overhead
- **Runaway risk**: senza limiti, un agent potrebbe spawnare infiniti figli

> **Best practice**: maxSpawnDepth: 2 è il massimo consigliato per produzione.

---

## 7. Agent Teams

Gli **Agent Teams** vanno oltre i subagent: sono agenti **indipendenti** che comunicano tra loro tramite il Gateway.

### Differenze Subagent vs Agent Teams

| Caratteristica | Subagent | Agent Teams |
|---|---|---|
| Relazione | Parent-child | Peer-to-peer |
| Sessione | Figlia del parent | Indipendente |
| Comunicazione | Result-back | Bidirezionale |
| Persistenza | Temporanea | Permanente |
| Modello | Può differire | Ognuno il suo |
| Workspace | Condiviso col parent | Separato |
| Use case | Task decomposition | Collaborazione continua |

### Configurazione Agent Team

```yaml
# ~/.openclaw/teams/marketplace-team.yaml
team:
  name: "Marketplace Operations"
  description: "Team per gestione marketplace Vendiamonoi"
  
  agents:
    - id: coordinator
      name: "Coordinatore"
      model: "anthropic/claude-opus-4"
      persona:
        role: "Orchestratore operazioni marketplace"
        rules:
          - "Decomponi task complessi in subtask"
          - "Delega ai worker specializzati"
          - "Sintetizza risultati"
      permissions:
        allowedCommands: ["*"]
    
    - id: catalog-worker
      name: "Catalog Manager"
      model: "anthropic/claude-sonnet-4"
      persona:
        role: "Esperto gestione cataloghi prodotti"
        rules:
          - "Pulisci e arricchisci dati prodotto"
          - "Genera titoli SEO ottimizzati"
          - "Valida EAN e attributi"
      permissions:
        allowedCommands: ["read", "write", "glob"]
    
    - id: pricing-worker
      name: "Pricing Analyst"
      model: "anthropic/claude-haiku-4-5"
      persona:
        role: "Analista prezzi e margini"
        rules:
          - "Analizza prezzi competitor"
          - "Calcola margini"
          - "Suggerisci repricing"
      permissions:
        allowedCommands: ["read", "web_search"]
    
    - id: listing-worker
      name: "Listing Optimizer"
      model: "anthropic/claude-sonnet-4"
      persona:
        role: "Ottimizzatore listing marketplace"
        rules:
          - "Genera descrizioni per marketplace target"
          - "Adatta contenuto per lingua/cultura"
      permissions:
        allowedCommands: ["read", "write"]

  routing:
    default: coordinator
    keywords:
      catalogo: catalog-worker
      prezzo: pricing-worker
      listing: listing-worker
```

### Gestione Team via CLI

```bash
# Avvia il team
openclaw teams start marketplace-team

# Lista agenti attivi nel team
openclaw teams list marketplace-team

# Invia messaggio a un agente specifico
openclaw teams message marketplace-team coordinator "Nuovo catalogo da processare"

# Ferma il team
openclaw teams stop marketplace-team
```

---

## 8. Orchestration Patterns

### Pattern 1: Sequential Pipeline

```
Input ──► Agent A ──► Agent B ──► Agent C ──► Output
          (fetch)     (clean)     (publish)
```

**Use case Vendiamonoi**: Catalogo fornitore → pulizia dati → generazione listing → pubblicazione marketplace.

```yaml
pipeline:
  name: "Catalog Pipeline"
  steps:
    - agent: fetcher
      input: "csv_url"
      output: "raw_data"
    - agent: cleaner
      input: "raw_data"
      output: "clean_data"
    - agent: publisher
      input: "clean_data"
      output: "marketplace_listings"
```

### Pattern 2: Fan-out / Fan-in (Parallel)

```
              ┌── Worker A ──┐
Input ──► Orch├── Worker B ──┤──► Merge ──► Output
              └── Worker C ──┘
```

**Use case Vendiamonoi**: Analisi prezzi simultanea su Amazon IT, eBay DE, Kaufland DE.

### Pattern 3: Router (Specialist Dispatch)

```
Input ──► Router ──► if(type == "catalog") → Catalog Agent
                  ──► if(type == "order") → Order Agent
                  ──► if(type == "support") → CS Agent
```

**Use case Vendiamonoi**: Smistamento automatico richieste per reparto.

### Pattern 4: Supervisor (Human-in-the-Loop)

```
Input ──► Agent ──► Proposta ──► [Approvazione Umana] ──► Esecuzione
                                       │
                                       └── Rifiuto → Revisione
```

**Use case Vendiamonoi**: Pubblicazione listing su marketplace premium con approvazione.

### Pattern 5: Swarm (Autonomo)

```
┌─────────────────────────────────┐
│  Agent A ◄──► Agent B           │
│     ▲           ▲               │
│     │           │               │
│     ▼           ▼               │
│  Agent C ◄──► Agent D           │
│                                 │
│  Comunicazione peer-to-peer     │
│  Nessun orchestratore centrale  │
└─────────────────────────────────┘
```

**Use case**: Monitoraggio distribuito di marketplace multipli con auto-coordinamento.

### Pattern 6: Hierarchical (Enterprise)

```
         CEO Agent (Opus)
        ┌──────┼──────┐
   Manager A    Manager B    Manager C
   (Sonnet)    (Sonnet)     (Sonnet)
   ┌──┼──┐    ┌──┼──┐      ┌──┼──┐
   W1 W2 W3   W4 W5 W6     W7 W8 W9
   (Haiku)    (Haiku)      (Local)
```

**Use case Vendiamonoi**: Gestione enterprise con CEO agent che coordina manager per reparto (catalogo, ordini, customer service), ciascuno con worker specializzati.

---

## 9. Mission Control

**Mission Control** è la piattaforma centralizzata per governance e monitoraggio degli agenti OpenClaw.

### Funzionalità principali

| Area | Funzionalità | Descrizione |
|---|---|---|
| **Work Orchestration** | Board, Task, Tag | Gestione task come un project manager |
| **Agent Operations** | Create, Inspect, Manage | Ciclo di vita completo degli agenti |
| **Governance** | Approval Workflows | Azioni sensibili richiedono approvazione |
| **Gateway Management** | Multi-gateway | Gestione ambienti distribuiti |
| **Monitoring** | Dashboard real-time | Stato agenti, token usage, errori |
| **Security** | RBAC, Audit Log | Role-based access, tracciabilità |

### Architettura Mission Control

```
┌──────────────────────────────────────────────┐
│              Mission Control UI              │
│  (Web Dashboard / React)                     │
├──────────────────────────────────────────────┤
│            Mission Control API               │
│  (REST + WebSocket)                          │
├──────────┬──────────┬──────────┬─────────────┤
│ Gateway  │ Gateway  │ Gateway  │ Gateway     │
│ (Dev)    │ (Stage)  │ (Prod)   │ (EU-West)   │
├──────────┴──────────┴──────────┴─────────────┤
│            Agent Fleet                       │
│  Agent A │ Agent B │ Agent C │ ... │ Agent N │
└──────────────────────────────────────────────┘
```

### Mission Control - Open Source Ecosystem

Non esiste un singolo prodotto "Mission Control" ufficiale, ma un ecosistema di dashboard open-source:

| Progetto | Stars | Focus |
|---|---|---|
| **openclaw-mission-control** (abhi1693) | ~2K | Dashboard orchestrazione agenti |
| **OpenClaw Command Center** (jontsai) | ~1K | Workforce management |
| **Navi** | ~500 | Fino a 7 agenti concorrenti |
| **agent-orchestrator** (Composio) | ~3K | Orchestratore per coding agent paralleli |

### Governance Enterprise

```
Richiesta Agente ──► Policy Check ──► Approval Queue ──► Esecuzione
                         │                   │
                    Blocco automatico    Review umana
                    (comandi pericolosi)  (azioni sensibili)
```

Features governance:
- **Approval workflows**: azioni sensibili richiedono approvazione esplicita
- **Security scanners**: NemoClaw di Nvidia per sandboxing
- **Role-based access**: permessi per ruolo
- **Audit trail**: log completo di ogni azione agente
- **Policy engine**: regole dichiarative per limitare comportamenti

---

## 10. YAML Multi-Agent Configuration

### Struttura completa

```yaml
# ~/.openclaw/agents.yaml
agents:
  defaults:
    model:
      primary: "anthropic/claude-sonnet-4"
      fallbacks:
        - "anthropic/claude-haiku-4-5"
        - "ollama/llama3.1-70b"
    subagents:
      model: "anthropic/claude-haiku-4-5"
      maxSpawnDepth: 2
      maxChildrenPerAgent: 5
      archiveAfterMinutes: 60

  definitions:
    - id: "main"
      name: "Agente Principale"
      description: "Orchestratore centrale Vendiamonoi"
      workspace: "/vendiamonoi"
      agentDir: "~/.openclaw/agents/main"
      model:
        primary: "anthropic/claude-opus-4"
        fallbacks:
          - "anthropic/claude-sonnet-4"
      persona:
        role: "Operations Manager Vendiamonoi.it"
        rules:
          - "Coordina i worker per task complessi"
          - "Usa subagent per analisi parallele"
          - "Mantieni focus su efficienza e scalabilità"
      permissions:
        allowedCommands: ["*"]
        blockedCommands: ["rm -rf /", "DROP DATABASE"]
      memory:
        vectorStore: true
        proactive: true
      tools:
        - read
        - write
        - exec
        - web_search
        - mcp_*

    - id: "catalog-worker"
      name: "Catalog Worker"
      description: "Gestione cataloghi e dati prodotto"
      model:
        primary: "anthropic/claude-sonnet-4"
      persona:
        role: "Data specialist per cataloghi prodotto"
      permissions:
        allowedCommands: ["read", "write", "glob", "grep"]
        blockedCommands: ["exec", "bash"]
      tools:
        - read
        - write
        - glob
        - grep

  routing:
    channels:
      telegram-ops: "main"
      telegram-catalog: "catalog-worker"
      discord-general: "main"
    keywords:
      catalogo: "catalog-worker"
      ordine: "order-worker"
      prezzo: "pricing-worker"
    users:
      alessio: "main"
      operatore1: "catalog-worker"
```

### Routing deterministico

L'array `routing` permette di instradare automaticamente le richieste:

| Criterio | Esempio | Agente Target |
|---|---|---|
| **Channel** | `telegram-ops` | main |
| **Keyword** | messaggio contiene "catalogo" | catalog-worker |
| **User** | `alessio` | main (sempre orchestratore) |
| **Fallback** | nessun match | default agent |

---

## 11. Routing e Channel Dispatch

### Come funziona il routing

```
Messaggio in arrivo
       │
       ▼
┌─────────────────┐
│ 1. Check Channel│──► Match? → Agente assegnato al channel
└────────┬────────┘
         │ No match
         ▼
┌─────────────────┐
│ 2. Check User   │──► Match? → Agente assegnato all'utente
└────────┬────────┘
         │ No match
         ▼
┌─────────────────┐
│ 3. Check Keywords│──► Match? → Agente specializzato
└────────┬────────┘
         │ No match
         ▼
┌─────────────────┐
│ 4. Default Agent│──► Agente principale
└─────────────────┘
```

### Multi-channel setup per Vendiamonoi

```yaml
routing:
  channels:
    # Telegram
    telegram-direzione: "orchestrator"
    telegram-fornitori: "supplier-agent"
    telegram-ordini: "order-agent"
    
    # Discord
    discord-dev: "tech-agent"
    discord-ops: "orchestrator"
    
    # WhatsApp
    whatsapp-alessio: "orchestrator"
    
    # Webhook
    webhook-marketplace: "order-agent"
    webhook-shopify: "catalog-agent"
```

---

## 12. Cost Optimization Multi-Agent

### Strategia: Modello a piramide

```
          ┌─────────┐
          │  Opus 4  │  ← Orchestratore (planning, sintesi)
          │  $15/1M  │     ~20% dei token totali
          ├─────────┤
          │ Sonnet 4 │  ← Worker specializzati
          │  $3/1M   │     ~30% dei token
          ├─────────────┤
          │   Haiku 4.5  │  ← Task semplici, bulk
          │   $0.80/1M   │     ~30% dei token
          ├───────────────────┤
          │   Ollama (Local)  │  ← Task ripetitivi, no cloud
          │     $0/1M         │     ~20% dei token
          └───────────────────┘
```

### Stima costi multi-agent Vendiamonoi

| Componente | Modello | Token/mese stimati | Costo/mese |
|---|---|---|---|
| Orchestratore principale | Opus 4 | ~2M input + 500K output | ~$37.50 |
| Worker catalogo (x2) | Sonnet 4 | ~5M input + 2M output | ~$21.00 |
| Worker pricing | Haiku 4.5 | ~3M input + 1M output | ~$3.40 |
| Worker listing | Sonnet 4 | ~4M input + 2M output | ~$18.00 |
| Worker ordini | Haiku 4.5 | ~2M input + 500K output | ~$2.00 |
| Subagent explore | Haiku 4.5 | ~5M input + 500K output | ~$4.40 |
| **Totale stimato** | | | **~$86/mese** |

### Risparmio vs Single Agent

| Approccio | Costo stimato/mese | Note |
|---|---|---|
| Tutto su Opus 4 | ~$250-400 | Ogni task usa modello premium |
| Multi-agent ottimizzato | ~$86 | 60-70% risparmio |
| Con Ollama per bulk | ~$55-65 | Task ripetitivi gratis |

### Tecniche di ottimizzazione

1. **Model routing**: Opus solo per planning/sintesi, Haiku per task semplici
2. **Context pruning**: ogni subagent riceve solo il contesto necessario
3. **Caching risultati**: evita re-computation di analisi identiche
4. **Batch processing**: raggruppa task simili per lo stesso worker
5. **Early termination**: interrompi subagent se il risultato è sufficiente
6. **Archivio aggressivo**: `archiveAfterMinutes: 30` per liberare risorse

---

## 13. Sicurezza e Governance

### Principi di sicurezza multi-agent

| Principio | Implementazione |
|---|---|
| **Least Privilege** | Ogni agente ha solo i tool necessari |
| **Isolation** | Subagent in sandbox (NemoClaw/OpenShell) |
| **Depth Limit** | `maxSpawnDepth: 2` massimo |
| **Fan-out Limit** | `maxChildrenPerAgent: 5` |
| **Approval Gates** | Azioni sensibili richiedono human approval |
| **Audit Trail** | Ogni azione loggata con agent ID e timestamp |
| **Network Policy** | Agenti con accesso rete limitato per policy |

### NemoClaw (Nvidia Security Add-on)

Rilasciato il 16 marzo 2026, NemoClaw aggiunge:

- **OpenShell**: ogni azione agente in container sicuro
- **File system**: accesso solo a directory whitelisted
- **Network filtering**: richieste filtrate per policy rules
- **Command approval**: comandi di sistema richiedono approvazione esplicita

### Configurazione sicurezza per team

```yaml
security:
  # Sandbox per tutti gli agenti
  sandbox:
    enabled: true
    provider: "nemoclaw"  # o "docker", "firejail"
  
  # Limiti globali
  limits:
    maxSpawnDepth: 2
    maxChildrenPerAgent: 5
    maxTotalAgents: 20
    maxTokensPerMinute: 100000
    maxCostPerDay: 10.00  # USD
  
  # Approval workflow
  approvals:
    required_for:
      - "exec:rm"
      - "exec:curl"
      - "write:*.json"
      - "publish:*"
    approvers:
      - "alessio"
    timeout_minutes: 30
    default_action: "deny"  # deny se nessuna approvazione
  
  # Audit
  audit:
    enabled: true
    destination: "file"  # o "webhook", "database"
    path: "/var/log/openclaw/audit.jsonl"
    retention_days: 90
```

---

## 14. Configurazione Vendiamonoi

### Architettura multi-agent raccomandata

```
┌─────────────────────────────────────────────────────┐
│                  VENDIAMONOI AGENT TEAM              │
│                                                     │
│  ┌─────────────────────────────────────┐            │
│  │  🧠 Orchestratore (Opus 4)         │            │
│  │  - Riceve richieste da Alessio      │            │
│  │  - Decompone in subtask             │            │
│  │  - Sintetizza risultati             │            │
│  └────────┬──────────┬─────────────────┘            │
│           │          │                              │
│  ┌────────▼──┐ ┌─────▼──────┐ ┌──────────────┐     │
│  │📦 Catalog │ │💰 Pricing  │ │📝 Listing    │     │
│  │  Worker   │ │  Worker    │ │  Worker      │     │
│  │ (Sonnet)  │ │ (Haiku)    │ │ (Sonnet)     │     │
│  │           │ │            │ │              │     │
│  │- Pulizia  │ │- Analisi   │ │- Generazione │     │
│  │- Validaz. │ │- Repricing │ │- Traduzione  │     │
│  │- Arricch. │ │- Margini   │ │- SEO         │     │
│  └───────────┘ └────────────┘ └──────────────┘     │
│                                                     │
│  ┌───────────┐ ┌────────────┐ ┌──────────────┐     │
│  │📋 Order   │ │🎧 CS       │ │🔍 Research   │     │
│  │  Worker   │ │  Worker    │ │  Worker      │     │
│  │ (Haiku)   │ │ (Sonnet)   │ │ (Haiku)      │     │
│  │           │ │            │ │              │     │
│  │- Tracking │ │- Risposte  │ │- Competitor  │     │
│  │- Fornitori│ │- Resi      │ │- Normative   │     │
│  │- Spediz.  │ │- Reclami   │ │- Marketplace │     │
│  └───────────┘ └────────────┘ └──────────────┘     │
└─────────────────────────────────────────────────────┘
```

### Costi stimati team completo

| Componente | Costo/mese |
|---|---|
| Orchestratore (Opus 4) | ~$37 |
| 2x Worker Sonnet (Catalog + Listing) | ~$39 |
| 3x Worker Haiku (Pricing + Order + Research) | ~$10 |
| CS Worker (Sonnet) | ~$15 |
| Subagent on-demand | ~$5 |
| **Totale** | **~$106/mese** |

### Fasi di implementazione

| Fase | Cosa | Quando | Costo aggiuntivo |
|---|---|---|---|
| **Fase 1** | Single agent Opus con subagent Haiku | Subito | ~$50/mese |
| **Fase 2** | +Catalog Worker + Pricing Worker | Mese 2 | +$30/mese |
| **Fase 3** | +Listing Worker + Order Worker | Mese 3 | +$25/mese |
| **Fase 4** | Full team + Mission Control | Mese 4+ | +$20/mese |

### Priorità implementazione

1. **ALTA** — Configurare orchestratore con subagent (maxSpawnDepth: 2)
2. **ALTA** — Catalog Worker per automazione pulizia cataloghi (task più ripetitivo)
3. **MEDIA** — Pricing Worker per analisi prezzi competitor
4. **MEDIA** — Listing Worker per generazione listing multilingua
5. **BASSA** — Order Worker (integrabile con Make/n8n per ora)
6. **BASSA** — Mission Control dashboard (quando team > 5 agenti)

### Pattern ibrido OpenClaw + Make/n8n

```
                OpenClaw (Cervello)
                       │
          ┌────────────┼────────────┐
          │            │            │
    Analisi AI    Decisioni     Generazione
    (subagent)    (orchestr.)   (worker)
          │            │            │
          └────────────┼────────────┘
                       │
                  API/Webhook
                       │
               Make/n8n (Mani)
          ┌────────────┼────────────┐
          │            │            │
   Push su MPK    Aggiorna DB    Notifica
   (ChannelEngine) (Shopify)     (Telegram)
```

> **Regola d'oro**: OpenClaw pensa e decide, Make/n8n esegue azioni ripetitive sui sistemi esterni. I subagent di OpenClaw fanno analisi e generazione, Make gestisce le API call verso marketplace e sistemi.

---

## Fonti

- [OpenClaw Docs — Sub-Agents](https://docs.openclaw.ai/tools/subagents)
- [OpenClaw Multi-Agent Orchestration Guide](https://zenvanriel.com/ai-engineer-blog/openclaw-multi-agent-orchestration-guide/)
- [Meta Intelligence — Multi-Agent, Subagents & Orchestration](https://www.meta-intelligence.tech/en/insight-openclaw-multi-agent)
- [Mission Control for OpenClaw](https://github.com/abhi1693/openclaw-mission-control)
- [OpenClaw Multi-Agent YAML Configuration](https://open-claw.online/docs/multi-agent-yaml)
- [Azure AI Agent Orchestration Patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)
- [OpenClaw Enterprise Agentic AI Architecture 2026](https://kollox.com/openclaw-2026-enterprise-agentic-ai-orchestration-architecture/)
- [DigitalOcean — Run Multiple OpenClaw Agents](https://www.digitalocean.com/blog/openclaw-digitalocean-app-platform)
