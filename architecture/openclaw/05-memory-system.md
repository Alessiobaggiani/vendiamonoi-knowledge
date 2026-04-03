# Settore 5 — Sistema Memory & Contesto

> Guida completa al sistema di memoria di OpenClaw: workspace files, architettura 3-tier, memory persistence, context assembly, RAG locale con SQLite, embedding providers, e best practice per gestione memoria enterprise.

---

## Indice

1. [Architettura 3-Tier](#1-architettura-3-tier)
2. [Workspace Files — I 7 File Fondamentali](#2-workspace-files--i-7-file-fondamentali)
3. [SOUL.md — Identità e Personalità](#3-soulmd--identità-e-personalità)
4. [AGENTS.md — Regole Operative](#4-agentsmd--regole-operative)
5. [MEMORY.md — Memoria a Lungo Termine](#5-memorymd--memoria-a-lungo-termine)
6. [Daily Notes — Memoria di Sessione](#6-daily-notes--memoria-di-sessione)
7. [SQLite RAG — Deep Knowledge Search](#7-sqlite-rag--deep-knowledge-search)
8. [Context Assembly](#8-context-assembly)
9. [ContextEngine Plugins (v2026.3.7)](#9-contextengine-plugins-v202637)
10. [Lossless Context Management](#10-lossless-context-management)
11. [Memory Commands & Tools](#11-memory-commands--tools)
12. [Embedding Providers](#12-embedding-providers)
13. [Memory Flush — Anti-Perdita Automatico](#13-memory-flush--anti-perdita-automatico)
14. [Best Practice Gestione Memoria](#14-best-practice-gestione-memoria)
15. [Strumenti Avanzati di Memoria](#15-strumenti-avanzati-di-memoria)
16. [Configurazione Memoria per Vendiamonoi](#16-configurazione-memoria-per-vendiamonoi)
17. [Fonti & Riferimenti](#17-fonti--riferimenti)

---

## 1. Architettura 3-Tier

Il sistema di memoria e contesto di OpenClaw è organizzato in **3 livelli distinti**:

```
┌───────────────────────────────────────────────────┐
│        TIER 1: IDENTITY (Chi sono)             │
│  SOUL.md │ IDENTITY.md │ USER.md               │
│  Personalità, valori, tono, limiti              │
│  → Caricato PRIMO nel context di ogni sessione │
├───────────────────────────────────────────────────┤
│        TIER 2: OPERATIONS (Cosa faccio)         │
│  AGENTS.md │ TOOLS.md │ HEARTBEAT.md           │
│  Regole, workflow, task schedulati              │
│  → Definisce il comportamento operativo        │
├───────────────────────────────────────────────────┤
│        TIER 3: KNOWLEDGE (Cosa so)              │
│  MEMORY.md │ memory/YYYY-MM-DD.md │ SQLite RAG │
│  Fatti duraturi, note giornaliere, deep search  │
│  → Persistenza e ricerca semantica             │
└───────────────────────────────────────────────────┘
```

### Principi fondamentali

- **Tutto è Markdown**: Nessun database nascosto, nessuno stato invisibile
- **Modificabile con qualsiasi editor**: Puoi aprire i file con VS Code, Vim, Obsidian
- **Versionabile con Git**: L'intero agente può essere sotto version control
- **Portabile**: Copia la cartella su un altro server = agente identico
- **Il modello ricorda solo ciò che è su disco**: Non c'è stato nascosto

---

## 2. Workspace Files — I 7 File Fondamentali

Quando OpenClaw avvia una sessione, legge questi file e assembla il contesto dell'agente.

| File | Layer | Funzione | Caricamento |
|---|---|---|---|
| **SOUL.md** | Identity | Personalità, valori, tono, limiti | Sempre, primo |
| **IDENTITY.md** | Identity | Nome, ID, ruolo, metadata | Sempre |
| **USER.md** | Identity | Chi è l'utente, preferenze, vincoli | Sempre |
| **AGENTS.md** | Operations | Regole procedurali, workflow, comportamento | Sempre |
| **TOOLS.md** | Operations | Convenzioni locali, ID canali, workflow preferiti | Sempre |
| **HEARTBEAT.md** | Operations | Checklist cron, task schedulati | Solo su heartbeat |
| **MEMORY.md** | Knowledge | Fatti duraturi, preferenze, decisioni | Sempre |

File opzionali:
- **SHIELD.md** — Policy di sicurezza caricabile (vedi Settore 3)
- **memory/YYYY-MM-DD.md** — Note giornaliere (oggi e ieri caricati automaticamente)
- **memory/project-name.md** — Memoria per progetto specifico
- **skills/\<skill\>/SKILL.md** — Istruzioni per skill specifiche

### Struttura directory

```
~/.openclaw/
├── config/
│   ├── models.yaml
│   ├── security.yaml
│   └── channels.yaml
└── agents/
    └── main/
        ├── SOUL.md              # Chi sono
        ├── IDENTITY.md          # La mia carta d'identità
        ├── USER.md              # Chi è il mio utente
        ├── AGENTS.md            # Le mie regole
        ├── TOOLS.md             # Le mie convenzioni
        ├── HEARTBEAT.md         # I miei task periodici
        ├── MEMORY.md            # La mia memoria a lungo termine
        ├── SHIELD.md            # Le mie regole di sicurezza
        ├── memory/
        │   ├── 2026-04-01.md    # Note di 2 giorni fa
        │   ├── 2026-04-02.md    # Note di ieri
        │   ├── 2026-04-03.md    # Note di oggi
        │   └── project-vendiamonoi.md
        ├── skills/
        │   ├── web-search/
        │   └── csv-processor/
        ├── sessions/
        │   └── *.jsonl          # History conversazioni
        └── memory.sqlite        # Indice RAG locale
```

---

## 3. SOUL.md — Identità e Personalità

SOUL.md è il **primo file** iniettato nel contesto dell'agente. Definisce:

- **Personalità**: Tono, stile di comunicazione
- **Valori**: Cosa è importante per l'agente
- **Limiti**: Cosa NON deve fare
- **Confini**: Come gestire situazioni ambigue

### Esempio SOUL.md per Vendiamonoi

```markdown
# Soul

Sono l'assistente AI di Vendiamonoi.it, un'azienda di distribuzione
digitale su marketplace europei.

## Personalità
- Professionale ma accessibile
- Orientato all'efficienza operativa
- Proattivo nel suggerire automazioni
- Preciso con i dati (mai inventare numeri)

## Valori
- Accuratezza prima di tutto
- Scalabilità delle soluzioni
- Rispetto della privacy dei dati aziendali
- Trasparenza nelle limitazioni

## Limiti
- Non condividere MAI API key, credenziali o dati sensibili
- Non eseguire azioni irreversibili senza conferma
- Non fare promesse su tempi di consegna
- Segnalare sempre quando un'informazione potrebbe non essere aggiornata

## Tono
- Italiano come lingua primaria
- Tecnico ma comprensibile
- Usa analogie per concetti complessi
- Struttura le risposte con heading e bullet point
```

### Regole SOUL.md

- Deve essere **conciso** (sotto 2000 token idealmente)
- Viene caricato in **ogni sessione** — ogni token conta
- Non mettere regole operative qui (quelle vanno in AGENTS.md)
- Non mettere fatti/conoscenze qui (quelle vanno in MEMORY.md)

---

## 4. AGENTS.md — Regole Operative

AGENTS.md risponde alla domanda "**Cosa fai e come?**". È il file più grande e importante per agenti con workflow complessi.

### Contenuto tipico

```markdown
# Agents

## Workflow Principali

### 1. Gestione Ordini Marketplace
Quando ricevo notifica di nuovo ordine:
1. Verificare disponibilità con fornitore
2. Confermare ordine su marketplace
3. Richiedere spedizione al fornitore
4. Aggiornare tracking appena disponibile
5. Notificare il cliente

### 2. Aggiornamento Catalogo
Quando un fornitore invia nuovo catalogo:
1. Validare formato (CSV/XML)
2. Controllare EAN e completezza dati
3. Arricchire titoli SEO
4. Generare feed per ogni marketplace
5. Pubblicare su Channable/ChannelEngine

## Regole Generali
- Cerca sempre in MEMORY prima di chiedere all'utente
- Salva decisioni importanti in MEMORY.md
- Per task complessi, crea un piano prima di agire
- Conferma prima di azioni irreversibili
- Logga errori in memory/errors.md

## Canali Attivi
- Telegram: Notifiche urgenti
- Slack: Comunicazione team
- Email: Fornitori e marketplace
- WhatsApp: Comunicazione rapida con Alessio
```

### Best practice AGENTS.md

- **Workflow numerati**: Step chiari e sequenziali
- **Condizioni specifiche**: "Quando X accade, fai Y"
- **Regole di fallback**: Cosa fare se qualcosa va storto
- **Priorità esplicite**: Quali task hanno precedenza

---

## 5. MEMORY.md — Memoria a Lungo Termine

MEMORY.md è la **memoria duratura** dell'agente. Caricato all'inizio di ogni sessione DM.

### Cosa mettere in MEMORY.md

- Fatti permanenti ("Il nostro fornitore principale è XYZ")
- Preferenze utente ("Alessio preferisce risposte in italiano")
- Decisioni prese ("Abbiamo scelto Caddy come reverse proxy")
- Configurazioni chiave ("Il nostro Shopify store è vendiamonoi.myshopify.com")

### Cosa NON mettere in MEMORY.md

- Task temporanei (usare daily notes)
- Log di sessione (usare sessions/)
- Regole di comportamento (usare AGENTS.md)
- Dati sensibili/credenziali (usare env vars)

### Regole critiche

- **Sotto 3000 token**: Qualità > quantità. Solo fatti veramente permanenti
- **Curare, non accumulare**: Se diventa un diario, perde efficacia
- **Strutturare per categoria**: Heading chiari per navigazione rapida

### Esempio MEMORY.md per Vendiamonoi

```markdown
# Memory

## Azienda
- Vendiamonoi.it S.R.L. Unipersonale
- Fondatore: Alessio Baggiani
- Business: distribuzione digitale su marketplace europei
- 20-30 marketplace attivi, 100+ fornitori, milioni di SKU

## Stack Tecnologico
- CRM: Bitrix24
- Automazione: Make (Integromat), Google Apps Script
- Marketplace: Channable, ChannelEngine, Shopify
- Pagamenti: Qonto
- Knowledge base: Notion, GitHub (vendiamonoi-knowledge), Obsidian

## Decisioni Architetturali
- Reverse proxy: Caddy (scelto per semplicità)
- Modello primario: Claude Sonnet 4
- Heartbeat: Gemini Flash-Lite (costo ottimizzato)
- Hosting: Hetzner CX22 ($6.50/mese)

## Fornitori Principali
- [Riservato — non includere dettagli sensibili]
```

---

## 6. Daily Notes — Memoria di Sessione

I file `memory/YYYY-MM-DD.md` sono le **note giornaliere**.

### Caricamento automatico

- **Oggi** (`2026-04-03.md`) → Sempre caricato
- **Ieri** (`2026-04-02.md`) → Sempre caricato
- **Giorni precedenti** → Accessibili via `memory_search` e `memory_get`

### Contenuto tipico

```markdown
# 2026-04-03

## Sessione mattina
- Completata ricerca sicurezza OpenClaw (Settore 3)
- Pubblicato su GitHub, Obsidian, Notion
- CVE-2026-25253 è la vulnerabilità più critica

## Task aperti
- Settore 5: Memory system (in corso)
- Settore 6-12: da iniziare

## Note
- La pagina Notion master è stata spostata sotto Software Aziendali
- ID pagina master: 337ab24b-3086-8142-8d53-d0e8f7d61b00
```

### Best practice daily notes

- Scrivere **durante la sessione**, non solo alla fine
- Includere **decisioni prese** e il **perché**
- Notare **errori e soluzioni** per riferimento futuro
- Alla fine della giornata, spostare fatti permanenti in MEMORY.md

---

## 7. SQLite RAG — Deep Knowledge Search

### Architettura

OpenClaw implementa un sistema **RAG-lite** interamente basato su SQLite:

```
Markdown files → Chunking → Embeddings → SQLite DB
                                            │
                                    ┌───────┴───────┐
                                    │               │
                              FTS5 (keyword)  sqlite-vec (vector)
                                    │               │
                                    └───────┬───────┘
                                            │
                                    Hybrid Search
                                    (keyword + semantic)
```

### Perché SQLite

| Caratteristica | Vantaggio |
|---|---|
| No server dependency | Nessun processo separato da gestire |
| Single-file | Tutto in `memory.sqlite`, facile da backuppare |
| FTS5 extension | Full-Text Search integrata |
| sqlite-vec extension | Vector Search per ricerca semantica |
| Portabilità | Copia il file = copia l'indice |

### Modalità di ricerca

| Modalità | Quando usata | Requisiti |
|---|---|---|
| **Hybrid** (keyword + vector) | Default con embedding provider | API key embedding |
| **Vector-only** | Ricerca semantica pura | API key embedding |
| **Keyword-only** (FTS) | Fallback senza embeddings | Nessuno |

### Configurazione

```yaml
# ~/.openclaw/config/memory.yaml
memory:
  index:
    enabled: true
    path: memory.sqlite
    chunk_size: 512                   # Token per chunk
    chunk_overlap: 64                 # Overlap tra chunk
    rebuild_on_change: true           # Re-index quando file cambiano
  search:
    mode: hybrid                      # hybrid | vector | keyword
    max_results: 10
    min_score: 0.5                    # Score minimo per risultati
  embedding:
    provider: openai                  # openai | gemini | voyage | mistral
    model: text-embedding-3-small
    apiKey: ${OPENAI_API_KEY}         # Riusa la stessa key
```

---

## 8. Context Assembly

Quando l'utente invia un messaggio, OpenClaw **assembla il contesto** che verrà inviato al modello LLM:

### Ordine di assemblaggio

```
1. System Prompt (OpenClaw built-in)         ~500 token
2. SOUL.md                                   ~1000 token
3. IDENTITY.md                               ~200 token
4. USER.md                                   ~300 token
5. AGENTS.md                                 ~2000 token
6. TOOLS.md                                  ~500 token
7. MEMORY.md                                 ~2000 token
8. Daily notes (oggi + ieri)                 ~1000 token
9. Skill context (skill attive)              ~500-2000 token
10. Conversation history                     ~5000-180000 token
11. User message (messaggio attuale)         ~100-1000 token
──────────────────────────────────────────────────
TOTALE BASE (senza history)                  ~8000-10000 token
TOTALE CON HISTORY (sessione lunga)          fino a 200K token
```

### Implicazioni sui costi

- I **file workspace** (SOUL, AGENTS, MEMORY, ecc.) consumano ~8-10K token **ad ogni messaggio**
- La **conversation history** cresce linearmente con ogni scambio
- Dopo 20-30 scambi, la history domina il consumo (vedi Settore 4)
- Tenere i file workspace concisi = risparmio su OGNI interazione

---

## 9. ContextEngine Plugins (v2026.3.7)

OpenClaw 2026.3.7 ha trasformato il context management da logica hardcoded a **sistema pluggabile**.

### Legacy Engine (default)

- **Sliding-window compaction**: quando il contesto raggiunge l'80% del context window, i messaggi vecchi vengono riassunti
- Il riassunto sostituisce i messaggi originali come messaggio di sistema
- Limiti: perde dettagli, riassunti possono distorcere informazioni

### Plugin Architecture

```yaml
# Configurazione context engine
context:
  engine: lossless-claw              # O: default, custom-engine
  compaction_threshold: 0.8          # 80% del context window
  preserve_recent: 10                # Mantieni ultimi 10 messaggi intatti
```

### Plugin disponibili

| Plugin | Strategia | Vantaggio |
|---|---|---|
| **default** | Sliding-window compaction | Semplice, built-in |
| **lossless-claw** | DAG-based summarization | Zero perdita informazioni |
| **custom** | Implementabile via API | Totale personalizzazione |

---

## 10. Lossless Context Management

Il plugin **lossless-claw** (Lossless Context Management) è il più avanzato:

### Come funziona

1. **Persiste ogni messaggio** in un database SQLite separato
2. **Riassume chunk** di messaggi vecchi usando il modello LLM configurato
3. **Condensa riassunti** in nodi di livello superiore, formando un **DAG** (Directed Acyclic Graph)
4. **Assembla il contesto** combinando riassunti + messaggi recenti raw

### Performance (OOLONG benchmark)

| Engine | Score | Note |
|---|---|---|
| **Lossless-claw** | **74.8** | Migliore, gap cresce con contesto lungo |
| Claude Code default | 70.3 | Baseline |
| OpenClaw legacy | 68.5 | Sliding-window standard |

### Installazione

```bash
openclaw plugin install lossless-claw

# Configurare in context engine
# context.engine: lossless-claw
```

---

## 11. Memory Commands & Tools

### Comandi in-chat (usati dall'agente)

| Comando | Funzione |
|---|---|
| `memory_search "query"` | Ricerca semantica/ibrida nelle note |
| `memory_get "path"` | Legge un file di memoria specifico |
| `memory_save "path" "content"` | Scrive/aggiorna un file di memoria |

### Comandi CLI (usati dall'utente)

```bash
# Stato dell'indice di memoria
openclaw memory status
# Output: provider, stato indice, numero chunks, dimensione DB

# Ricerca da terminale
openclaw memory search "configurazione reverse proxy"

# Ricostruire l'indice
openclaw memory index --force

# Controllare quali file sono indicizzati
openclaw memory list
```

### Comportamento memory_search

- **Con embedding provider**: Ricerca ibrida (semantic + keyword)
- **Senza embedding provider**: Solo keyword (FTS5)
- **Risultati**: Ordinati per score, con snippet del contenuto
- **Scope**: Tutti i file .md nella cartella memory/ + MEMORY.md

---

## 12. Embedding Providers

### Auto-detection

OpenClaw auto-rileva il provider di embedding dalle API key disponibili:

| Provider | Modello Default | Dimensioni | Costo |
|---|---|---|---|
| **OpenAI** | text-embedding-3-small | 1536 | $0.02/1M token |
| **Gemini** | gemini-embedding-001 | 768 | $0.0025/1M token |
| **Voyage** | voyage-3-lite | 512 | $0.02/1M token |
| **Mistral** | mistral-embed | 1024 | $0.01/1M token |

### Configurazione esplicita

```yaml
memory:
  embedding:
    provider: gemini                  # Più economico
    model: gemini-embedding-001
    apiKey: ${GOOGLE_AI_KEY}
```

### Raccomandazione

- **Gemini embedding**: 8x più economico di OpenAI, qualità comparabile
- Se hai già una key Google per i modelli LLM, riusala per gli embedding
- Il costo degli embedding è trascurabile (< $1/mese per uso tipico)

---

## 13. Memory Flush — Anti-Perdita Automatico

### Come funziona

Prima che la compaction riassuma la conversazione, OpenClaw esegue automaticamente un **memory flush**:

1. Rileva che la compaction sta per attivarsi (80% context window)
2. Esegue un "silent turn" invisibile all'utente
3. Chiede al modello: "Ci sono fatti importanti nella conversazione che non sono ancora salvati?"
4. Il modello salva automaticamente i fatti in MEMORY.md o daily notes
5. Solo DOPO il flush, la compaction procede

### Configurazione

```yaml
memory:
  flush:
    enabled: true                     # ON di default
    trigger: pre_compaction           # Prima della compaction
    buffer_tokens: 5000               # Spazio riservato per il flush
    save_to: daily_notes              # O: memory_md, both
```

**IMPORTANTE**: Il flush è attivo di default, ma serve buffer sufficiente. Se il contesto è al 99%, non c'è spazio per il flush. Mantenere un margine.

---

## 14. Best Practice Gestione Memoria

### Top 5 regole

1. **"Cerca prima di chiedere"**: Aggiungere in AGENTS.md la regola `search memory before acting`
2. **MEMORY.md sotto 3000 token**: Solo fatti permanenti, curati regolarmente
3. **Fatti duraturi in file, non in chat**: La conversazione viene compressa, i file sopravvivono
4. **Verificare che il memory flush funzioni**: `openclaw memory status` regolarmente
5. **Strutturare con heading**: Navigazione rapida per l'agente

### Anti-pattern da evitare

| Anti-pattern | Problema | Soluzione |
|---|---|---|
| MEMORY.md come diario | Rumore, token sprecati | Curare, spostare vecchio in daily notes |
| Non usare memory_search | Agente "dimentica" cose | Regola obbligatoria in AGENTS.md |
| File workspace troppo grandi | Costo alto su ogni messaggio | Refactoring regolare, limite token |
| Nessun embedding provider | Solo keyword search | Configurare Gemini embedding |
| Ignorare daily notes | Perdita contesto giornaliero | Scrivere durante la sessione |

---

## 15. Strumenti Avanzati di Memoria

Oltre al sistema nativo, esistono strumenti che estendono le capacità di memoria:

| Strumento | Tipo | Funzione |
|---|---|---|
| **memsearch** | Standalone (Milvus) | Estratto dal sistema OpenClaw, open-source |
| **ClawMem** | MCP Server + Hooks | Context engine on-device, hybrid RAG |
| **QMD** | Plugin | Hybrid retrieval, recall accuracy migliorata |
| **Cognee** | Plugin | Knowledge graph, capisce relazioni tra fatti |
| **Mem0** | Plugin (cloud/self-host) | Estrazione automatica fatti, storage lungo termine |
| **Hindsight** | Plugin | Memoria persistente con indexing automatico |

### Quando usare strumenti avanzati

- **Base sufficiente per Vendiamonoi**: Sistema nativo + Gemini embedding
- **Utile se**: Milioni di note, knowledge base enorme, necessità di knowledge graph
- **Da valutare**: Cognee per mappare relazioni tra fornitori, prodotti, marketplace

---

## 16. Configurazione Memoria per Vendiamonoi

### Setup raccomandato

```yaml
# ~/.openclaw/config/memory.yaml
memory:
  index:
    enabled: true
    chunk_size: 512
    rebuild_on_change: true
  search:
    mode: hybrid
    max_results: 10
  embedding:
    provider: gemini
    model: gemini-embedding-001
    apiKey: ${GOOGLE_AI_KEY}
  flush:
    enabled: true
    buffer_tokens: 5000
    save_to: both

# Context engine
context:
  engine: lossless-claw
  compaction_threshold: 0.8
  preserve_recent: 15
```

### AGENTS.md — Regole memoria obbligatorie

```markdown
## Regole Memoria
1. SEMPRE cercare in memoria prima di chiedere all'utente
2. Salvare decisioni importanti in MEMORY.md
3. Usare daily notes per contesto di sessione
4. Prima di task complessi, controllare memory/project-vendiamonoi.md
5. Dopo errori, salvare la soluzione in memory/troubleshooting.md
```

### Struttura file memoria per Vendiamonoi

```
memory/
├── 2026-04-03.md                # Note giornaliere
├── project-vendiamonoi.md       # Stato progetto generale
├── fornitori.md                 # Info fornitori attivi
├── marketplace.md               # Config e note marketplace
├── automazioni.md               # Stato automazioni Make
├── troubleshooting.md           # Errori risolti e soluzioni
└── decisioni.md                 # Log decisioni architetturali
```

### Costo embedding stimato

| Provider | Costo/mese (uso tipico) |
|---|---|
| OpenAI (text-embedding-3-small) | ~$0.50 |
| Gemini (gemini-embedding-001) | ~$0.06 |

Trascurabile. Usare Gemini per ulteriore risparmio.

---

## 17. Fonti & Riferimenti

### Documentazione ufficiale
- Memory Overview: https://docs.openclaw.ai/concepts/memory
- Context: https://docs.openclaw.ai/concepts/context
- Default AGENTS.md: https://docs.openclaw.ai/reference/AGENTS.default
- Memory source (GitHub): https://github.com/openclaw/openclaw/blob/main/docs/concepts/memory.md

### Guide e tutorial
- Workspace Files Explained (Roberto Capodieci): https://capodieci.medium.com/ai-agents-003
- Memory Masterclass (VelvetShark): https://velvetshark.com/openclaw-memory-masterclass
- Memory Explained (LumaDock): https://lumadock.com/tutorials/openclaw-memory-explained
- Workspace Architecture (DEV Community): https://dev.to/hex_agent/openclaw-workspace-architecture
- AWS Memory & Soul Guide: https://dev.to/aws-builders/mastering-openclaw-on-aws

### Strumenti avanzati
- memsearch (Milvus/open-source): https://milvus.io/blog/we-extracted-openclaws-memory-system-and-opensourced-it-memsearch.md
- Lossless-claw: https://github.com/martian-engineering/lossless-claw
- ClawMem: https://github.com/yoloshii/ClawMem
- Hindsight memory plugin: https://hindsight.vectorize.io/blog/2026/03/06/adding-memory-to-openclaw-with-hindsight

### Context Engine
- ContextEngine Deep Dive (v2026.3.7): https://openclaws.io/blog/openclaw-contextengine-deep-dive
- SQLite RAG (PingCAP): https://www.pingcap.com/blog/local-first-rag-using-sqlite-ai-agent-memory-openclaw/
- Memory is Broken (DailyDoseOfDS): https://blog.dailydoseofds.com/p/openclaws-memory-is-broken-heres
- Advanced Memory Management (LumaDock): https://lumadock.com/tutorials/openclaw-advanced-memory-management

---

> **Nota per Vendiamonoi**: La memoria è il cuore dell'agente. Investire tempo nella strutturazione di SOUL.md, AGENTS.md e MEMORY.md paga enormemente in efficacia operativa. Rivedere e curare MEMORY.md settimanalmente.

---

*Ultimo aggiornamento: aprile 2026*
*Documento parte della Master Class OpenClaw — vendiamonoi-knowledge*
