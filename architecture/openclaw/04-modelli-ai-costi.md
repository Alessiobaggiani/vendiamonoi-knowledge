# Settore 4 — Modelli AI & Costi

> Guida completa ai provider LLM supportati da OpenClaw, pricing, failover, key rotation, ottimizzazione costi e configurazione raccomandata per deployment aziendale.

---

## Indice

1. [Provider Supportati](#1-provider-supportati)
2. [Priorità Provider e Auto-Select](#2-priorità-provider-e-auto-select)
3. [Configurazione Provider](#3-configurazione-provider)
4. [Modelli per Tier](#4-modelli-per-tier)
5. [Pricing Comparison Dettagliato](#5-pricing-comparison-dettagliato)
6. [Failover Multi-Modello](#6-failover-multi-modello)
7. [Key Rotation](#7-key-rotation)
8. [Heartbeat e Costi Idle](#8-heartbeat-e-costi-idle)
9. [Context Window e Token Accumulation](#9-context-window-e-token-accumulation)
10. [ClawRouter — Smart Multi-Model Routing](#10-clawrouter--smart-multi-model-routing)
11. [10 Strategie Ottimizzazione Costi](#11-10-strategie-ottimizzazione-costi)
12. [Costi Reali di Deployment](#12-costi-reali-di-deployment)
13. [Configurazione Raccomandata per Vendiamonoi](#13-configurazione-raccomandata-per-vendiamonoi)
14. [Fonti & Riferimenti](#14-fonti--riferimenti)

---

## 1. Provider Supportati

OpenClaw supporta **30+ provider LLM**, suddivisi in 5 categorie.

### Tier 1 — Provider Premium (Cloud)

| Provider | Modelli principali | API Type | Note |
|---|---|---|---|
| **Anthropic** | Claude Opus 4.5, Sonnet 4.6, Haiku 4.5 | anthropic-messages | Default primario, gold standard |
| **OpenAI** | GPT-5.2, GPT-5, GPT-4.1 | openai-completions | Secondo in priorità |
| **Google (Gemini)** | Gemini 2.5 Pro, 2.5 Flash, Flash-Lite | openai-completions | Context window fino a 1M token |
| **xAI** | Grok 4.1, Grok 4.1 mini | openai-completions | Economico, buone performance |

### Tier 2 — Provider Specializzati

| Provider | Modelli principali | Specialità |
|---|---|---|
| **DeepSeek** | DeepSeek V3, R1 | Coding, ragionamento, prezzo aggressivo |
| **Mistral** | Mistral Large, Codestral | Modelli europei, GDPR-friendly |
| **Groq** | Llama 3.3 70B (su LPU) | Velocità inferenza estrema |
| **Perplexity** | Sonar | Ricerca web integrata |
| **Together AI** | Vari open-source | Fine-tuning, modelli custom |

### Tier 3 — Gateway & Aggregatori

| Provider | Modelli | Vantaggio |
|---|---|---|
| **OpenRouter** | 100+ modelli | Singola API key, accesso a tutto |
| **LiteLLM** | Tutti i provider | Gateway unificato self-hosted |
| **Vercel AI Gateway** | Vari | Integrato con Vercel stack |
| **Cloudflare AI Gateway** | Vari | Edge inference, caching |
| **NVIDIA** | NIM models | GPU inference ottimizzata |

### Tier 4 — Modelli Locali

| Provider | Setup | Vantaggio |
|---|---|---|
| **Ollama** | `http://localhost:11434` | Zero costo, privacy totale |
| **vLLM** | Server locale | Alta performance, batching |
| **SGLang** | Server locale | Ottimizzato per structured generation |

### Tier 5 — Provider Regionali/Emergenti

| Provider | Regione | Modelli |
|---|---|---|
| **Qwen / Model Studio** | Alibaba Cloud | Qwen 2.5 family |
| **MiniMax** | Cina | M2.1, economici |
| **Moonshot AI (Kimi)** | Cina | Kimi, Kimi Coding |
| **GLM Models** | Cina | ChatGLM |
| **Volcengine (Doubao)** | Cina/ByteDance | Doubao |
| **Z.AI** | Vario | Modelli budget |
| **Xiaomi** | Cina | MiLM |
| **Venice** | Privacy-focused | Modelli senza log |
| **Synthetic** | Vario | Modelli sintetici |

---

## 2. Priorità Provider e Auto-Select

Quando OpenClaw ha più provider configurati, seleziona automaticamente il modello primario con questa priorità:

```
Anthropic > OpenAI > OpenRouter > Gemini > OpenCode > GitHub Copilot > 
xAI > Groq > Mistral > Cerebras > Venice > Moonshot > Kimi > 
MiniMax > Synthetic > ZAI > AI Gateway > Xiaomi > Bedrock > Ollama
```

**Implicazioni**:
- Se hai sia Anthropic che OpenAI configurati, Claude sarà sempre il primario
- Ollama è sempre l'ultimo fallback (usato per heartbeat e task semplici)
- OpenRouter è alto in priorità perché dà accesso a 100+ modelli

---

## 3. Configurazione Provider

### Struttura configurazione

```yaml
# ~/.openclaw/config/models.yaml
models:
  providers:
    anthropic:
      apiKey: ${OPENCLAW_ANTHROPIC_KEY}  # Da variabile ambiente
      models:
        - id: claude-sonnet-4-20260514
          contextWindow: 200000
          maxTokens: 8192
        - id: claude-opus-4-5-20260220
          contextWindow: 200000
          maxTokens: 32768
        - id: claude-haiku-4-5-20251022
          contextWindow: 200000
          maxTokens: 8192
    
    openai:
      apiKey: ${OPENCLAW_OPENAI_KEY}
      models:
        - id: gpt-5.2
          contextWindow: 128000
          maxTokens: 16384
    
    google:
      apiKey: ${OPENCLAW_GOOGLE_KEY}
      models:
        - id: gemini-2.5-flash
          contextWindow: 1000000
          maxTokens: 8192
    
    ollama:
      baseUrl: http://localhost:11434
      models:
        - id: qwen2.5-coder:7b
          contextWindow: 32768
          maxTokens: 4096

    openrouter:
      apiKey: ${OPENCLAW_OPENROUTER_KEY}
      models:
        - id: anthropic/claude-sonnet-4
          contextWindow: 200000
        - id: google/gemini-2.5-flash
          contextWindow: 1000000
```

### Allowlist e selezione modello

```yaml
# AGENTS.md o agents.defaults
agents:
  defaults:
    model:
      primary: anthropic/claude-sonnet-4-20260514
      fallbacks:
        - openai/gpt-5.2
        - google/gemini-2.5-flash
        - ollama/qwen2.5-coder:7b
    models:
      allowlist:
        - anthropic/claude-sonnet-4-20260514
        - anthropic/claude-opus-4-5-20260220
        - anthropic/claude-haiku-4-5-20251022
        - openai/gpt-5.2
        - google/gemini-2.5-flash
        - ollama/qwen2.5-coder:7b
```

### Provider custom (OpenAI-compatible)

```yaml
# Qualsiasi API compatibile OpenAI
models:
  providers:
    custom-provider:
      baseUrl: https://my-custom-api.example.com/v1
      apiKey: ${CUSTOM_API_KEY}
      apiType: openai-completions
      models:
        - id: my-model
          contextWindow: 128000
          maxTokens: 8192
```

---

## 4. Modelli per Tier

### Classificazione per uso in OpenClaw

| Tier | Uso | Modelli consigliati | Costo relativo |
|---|---|---|---|
| **Flagship** | Task complessi, coding, ragionamento lungo | Claude Opus 4.5, GPT-5.2 | $$$$$ |
| **Workhorse** | Uso quotidiano, buon rapporto qualità/prezzo | Claude Sonnet 4, Gemini 2.5 Pro | $$$ |
| **Budget** | Task semplici, alto volume | Gemini 2.5 Flash, GPT-4.1-mini, Haiku 4.5 | $ |
| **Free/Local** | Heartbeat, classificazione, task offline | Ollama (Qwen, Llama), Gemini Flash-Lite | Gratis |

### Ranking community (marzo 2026)

Secondo PricePerToken.com e community OpenClaw:

| # | Modello | Score | Punto di forza |
|---|---|---|---|
| 1 | Claude Sonnet 4.6 | 9.2/10 | Miglior bilanciamento complessivo |
| 2 | Claude Opus 4.5 | 9.5/10 | Massima qualità, costoso |
| 3 | GPT-5.2 | 8.8/10 | Buon coding, veloce |
| 4 | Gemini 2.5 Pro | 8.7/10 | Context window enorme (1M) |
| 5 | DeepSeek V3 | 8.5/10 | Coding eccellente, economico |
| 6 | Gemini 2.5 Flash | 8.3/10 | Ultra-economico, veloce |
| 7 | Grok 4.1 mini | 8.0/10 | Prezzo aggressivo |

---

## 5. Pricing Comparison Dettagliato

### Prezzi per 1M token (aprile 2026)

| Modello | Input $/1M | Output $/1M | Totale (1M in+out) | Context Window |
|---|---|---|---|---|
| **Claude Opus 4.5** | $15.00 | $75.00 | $90.00 | 200K |
| **Claude Sonnet 4** | $3.00 | $15.00 | $18.00 | 200K |
| **Claude Haiku 4.5** | $0.80 | $4.00 | $4.80 | 200K |
| **GPT-5.2** | $1.75 | $14.00 | $15.75 | 128K |
| **GPT-4.1-mini** | $0.40 | $1.60 | $2.00 | 128K |
| **Gemini 2.5 Pro** | $1.25 | $10.00 | $11.25 | 1M |
| **Gemini 2.5 Flash** | $0.30 | $2.50 | $2.80 | 1M |
| **Gemini Flash-Lite** | ~$0.075 | ~$0.30 | ~$0.375 | 1M |
| **Grok 4.1 mini** | $0.20 | $0.50 | $0.70 | 128K |
| **DeepSeek V3** | $0.27 | $1.10 | $1.37 | 128K |
| **Ollama (locale)** | $0.00 | $0.00 | $0.00 | Varia |

### Rapporto costo: quanto costa in più usare Opus vs Budget?

| Confronto | Differenza |
|---|---|
| Opus 4.5 vs Gemini Flash | **240x** più costoso |
| Opus 4.5 vs Grok mini | **128x** più costoso |
| Opus 4.5 vs Sonnet 4 | **5x** più costoso |
| Sonnet 4 vs Gemini Flash | **6.4x** più costoso |
| Sonnet 4 vs DeepSeek V3 | **13x** più costoso |

---

## 6. Failover Multi-Modello

### Come funziona

OpenClaw implementa un sistema di failover a cascata:

1. **Richiesta** → Modello primario (es. Claude Sonnet 4)
2. **Se errore 429 (rate limit)** → Rotazione auth profile dentro stesso provider
3. **Se tutti i profili falliscono** → Prossimo modello in `fallbacks`
4. **Se errore non-rate-limit** → Fallisce immediatamente (no rotation)

```yaml
# Configurazione failover
agents:
  defaults:
    model:
      primary: anthropic/claude-sonnet-4-20260514
      fallbacks:
        - openai/gpt-5.2               # Primo fallback
        - google/gemini-2.5-flash       # Secondo fallback (economico)
        - ollama/qwen2.5-coder:7b       # Ultimo resort (locale, gratis)
      failover:
        retry_on: [429, rate_limit, quota, resource_exhausted]
        max_retries: 3
        cooldown: 60                    # Secondi prima di riprovare provider fallito
```

### Tipi di errore e comportamento

| Errore | Azione |
|---|---|
| 429 (Rate Limit) | Ruota auth profile → poi fallback |
| 500 (Server Error) | Fallisce immediatamente |
| 503 (Service Unavailable) | Fallback al prossimo modello |
| Timeout | Fallback al prossimo modello |
| Auth Error (401/403) | Fallisce immediatamente (config errata) |

---

## 7. Key Rotation

Introdotta in OpenClaw 2026.3.13, la **multi-key rotation** cicla automaticamente tra più API key per lo stesso provider.

### Configurazione

```yaml
# Multi-key per singolo provider
models:
  providers:
    anthropic:
      auth_profiles:
        - apiKey: ${ANTHROPIC_KEY_1}    # Key principale
        - apiKey: ${ANTHROPIC_KEY_2}    # Key secondaria
        - apiKey: ${ANTHROPIC_KEY_3}    # Key terziaria
      rotation:
        strategy: round_robin           # O: on_rate_limit
        cooldown: 300                   # Secondi di cooldown per key in rate limit
```

### Comportamento

1. Richiesta inviata con Key 1
2. Se Key 1 riceve 429 → passa a Key 2
3. Se Key 2 riceve 429 → passa a Key 3
4. Se tutte le key sono in cooldown → fallback al modello successivo
5. Le key escono dal cooldown dopo il tempo configurato

### Best practice

- **3+ key per provider ad alto utilizzo** (Anthropic, OpenAI)
- **Key con billing account separati** per evitare quota condivisa
- **Monitorare utilizzo per key** per bilanciare consumo
- **Rotazione periodica** delle key stesse (sicurezza, vedi Settore 3)

---

## 8. Heartbeat e Costi Idle

### Cos'è il Heartbeat

L'heartbeat è il meccanismo con cui OpenClaw mantiene l'agente "vivo" — periodicamente invia il contesto completo al modello LLM per verificare se ci sono task da eseguire.

### Il problema dei costi

**Ogni heartbeat è una chiamata API completa** che include:
- AGENTS.md completo
- SOUL.md
- MEMORY.md
- HEARTBEAT.md (istruzioni cron)
- Context delle skill attive
- History recente

Con un modello costoso come Opus, questo diventa un killer di budget:

| Configurazione | Costo heartbeat/giorno | Costo/mese |
|---|---|---|
| Opus 4.5, ogni 30 min, 2 canali | ~$10/giorno | **~$300/mese** |
| Sonnet 4, ogni 30 min, 2 canali | ~$2/giorno | ~$60/mese |
| Gemini Flash, ogni 30 min | ~$0.15/giorno | ~$4.5/mese |
| Ollama locale, ogni 30 min | $0/giorno | **$0/mese** |

### Ottimizzazione heartbeat

```yaml
# Configurazione heartbeat ottimizzata
heartbeat:
  interval: 55m                       # Poco sotto cache TTL (1h)
  model: ollama/qwen2.5-coder:7b      # Modello locale gratuito
  # O se non hai Ollama:
  model: google/gemini-flash-lite      # Ultra-economico (~$0.50/M)
  context_trim: true                   # Invia solo heartbeat.md, non tutto
  channels_active_only: true           # Solo canali con attività recente
```

**Risparmio**: Da $300/mese a $0-5/mese solo cambiando il modello heartbeat.

### Caso reale: $50 in un giorno

Un utente ha configurato il check email automatico ogni 5 minuti con Claude Opus come modello. 288 chiamate/giorno × contesto pieno = $50/giorno. Soluzione: spostare i task cron su un modello budget.

---

## 9. Context Window e Token Accumulation

### Il problema dell'accumulo

Il **context accumulation** è il killer di costi #1, responsabile del 40-50% del consumo token tipico:

1. Ogni messaggio viene salvato in file JSONL (`~/.openclaw/agents.main/sessions/`)
2. Ad ogni nuova richiesta, OpenClaw invia l'**intera history** della sessione al modello
3. Dopo 20-30 scambi, il contesto può superare 50K-100K token
4. **Ogni messaggio successivo costa di più** perché include tutto il precedente

### Crescita dei costi in una sessione

| Messaggio # | Token contesto | Costo input (Sonnet) | Costo cumulativo |
|---|---|---|---|
| 1 | ~5K | $0.015 | $0.015 |
| 10 | ~25K | $0.075 | $0.45 |
| 20 | ~60K | $0.18 | $1.80 |
| 30 | ~100K | $0.30 | $4.50 |
| 50 | ~180K (limit!) | $0.54 | $13.50 |

### Strategie di gestione

```yaml
# Gestione context window
context:
  max_history_messages: 20            # Limita messaggi in context
  prune_strategy: sliding_window      # Finestra scorrevole
  cache_ttl: 3600                     # Cache TTL 1h
  cache_ttl_pruning: true             # Prune dopo scadenza cache
  summarize_old_messages: true        # Riassumi messaggi vecchi
```

**Tecnica del "New Session"**: Creare un comando che salva il contesto corrente e ne apre uno nuovo prima di ogni task importante. Riduce il contesto da 100K a 5K token.

---

## 10. ClawRouter — Smart Multi-Model Routing

### Cos'è

**ClawRouter** è un router LLM open-source che analizza ogni richiesta e la inoltra automaticamente al modello più economico in grado di gestirla.

### Come funziona

1. Ogni richiesta viene analizzata su **15 dimensioni**
2. Classificata in uno dei 4 tier:
   - **SIMPLE** (40% del traffico) → Gemini Flash, Grok mini
   - **MEDIUM** (30%) → DeepSeek Chat, Haiku
   - **COMPLEX** (20%) → Claude Sonnet, GPT-5.2
   - **REASONING** (10%) → Claude Opus, o3
3. Routing eseguito in **< 1ms** localmente
4. 80% del traffico gestito da regole veloci, 20% da classificatore leggero

### Impatto sui costi

| Scenario | Costo senza router | Costo con router | Risparmio |
|---|---|---|---|
| Tutto su Opus | $300/mese | $45/mese | **85%** |
| Tutto su Sonnet | $60/mese | $18/mese | **70%** |
| Mix manuale | $40/mese | $12/mese | **70%** |

### Configurazione

```yaml
# Installazione ClawRouter
# openclaw skill install clawrouter

clawrouter:
  enabled: true
  tiers:
    simple:
      models: [google/gemini-2.5-flash, xai/grok-4.1-mini]
      criteria: [heartbeat, classification, simple_lookup, translation]
    medium:
      models: [deepseek/deepseek-v3, anthropic/claude-haiku-4.5]
      criteria: [summarization, data_extraction, simple_coding]
    complex:
      models: [anthropic/claude-sonnet-4, openai/gpt-5.2]
      criteria: [coding, analysis, multi_step, writing]
    reasoning:
      models: [anthropic/claude-opus-4.5, openai/o3]
      criteria: [architecture, debugging_complex, mathematical_proof]
  fallback_tier: medium
```

### 55+ modelli supportati

ClawRouter supporta tutti i provider di OpenClaw, inclusi OpenAI, Anthropic, Google, xAI, DeepSeek, Groq, Mistral, e altri.

---

## 11. 10 Strategie Ottimizzazione Costi

### 1. Heartbeat su modello locale/budget
**Risparmio: 90-100%** sui costi idle
```yaml
heartbeat.model: ollama/qwen2.5-coder:7b
```

### 2. ClawRouter per routing automatico
**Risparmio: 70-92%** complessivo

### 3. New Session prima di ogni task
**Risparmio: 60-80%** su input token
Resetta il contesto accumulato.

### 4. Cache del provider
**Risparmio: 50-90%** su richieste ripetitive
Anthropic e OpenAI offrono caching a livello API.

### 5. Limitare history in context
**Risparmio: 30-50%** su sessioni lunghe
```yaml
context.max_history_messages: 15
```

### 6. Disabilitare canali non attivi
**Risparmio: proporzionale** al numero di canali
Ogni canale attivo = potenziali heartbeat e context aggiuntivi.

### 7. Usare OpenRouter per accesso multi-modello
**Vantaggio: una sola API key**, pricing competitivo, accesso a 100+ modelli.

### 8. Multi-key rotation per evitare rate limit
**Risparmio indiretto**: evita downtime e failback su modelli costosi.

### 9. Ollama per task privacy-sensitive
**Costo: $0** + elettricità. Nessun dato esce dal server. Ideale per task con dati aziendali sensibili.

### 10. Monitorare attivamente i costi
```yaml
monitoring:
  token_budget:
    daily_limit: 500000
    monthly_limit: 10000000
    alert_at: 80%
    action_at_limit: fallback_to_budget
```

---

## 12. Costi Reali di Deployment

### Breakdown costo totale mensile

| Componente | Self-hosted (Hetzner) | Self-hosted (DigitalOcean) | Managed (Blink Claw) |
|---|---|---|---|
| **Server/VPS** | $6.50/mese | $24/mese | Incluso |
| **LLM API (ottimizzato)** | $15-40/mese | $15-40/mese | $15-40/mese |
| **LLM API (non ottimizzato)** | $150-600/mese | $150-600/mese | $150-600/mese |
| **Dominio + TLS** | $1/mese | $1/mese | Incluso |
| **Backup** | $2/mese | $2/mese | Incluso |
| **Totale ottimizzato** | **$25-50/mese** | **$42-67/mese** | **~$45/mese** |
| **Totale non ottimizzato** | **$160-610/mese** | **$177-627/mese** | **$165-640/mese** |

### Profili di utilizzo tipici

| Profilo | Descrizione | Costo LLM stimato |
|---|---|---|
| **Light** | 1-2h/giorno, task semplici | $5-15/mese |
| **Medium** | 4-6h/giorno, coding + automazioni | $30-60/mese |
| **Heavy** | 8h+/giorno, multi-canale, 24/7 cron | $100-200/mese |
| **Enterprise (no ottimizzazione)** | 24/7, Opus, multi-agent | $500-1000+/mese |
| **Enterprise (ottimizzato)** | 24/7, router, multi-agent | $50-150/mese |

---

## 13. Configurazione Raccomandata per Vendiamonoi

### Setup modelli

```yaml
# Configurazione Vendiamonoi — bilanciamento qualità/costo
agents:
  defaults:
    model:
      primary: anthropic/claude-sonnet-4-20260514     # Workhorse principale
      fallbacks:
        - google/gemini-2.5-flash                      # Budget fallback
        - ollama/qwen2.5-coder:7b                      # Ultimo resort locale

heartbeat:
  interval: 55m
  model: google/gemini-flash-lite                      # Ultra-budget per heartbeat

# Provider configurati
models:
  providers:
    anthropic:
      auth_profiles:
        - apiKey: ${ANTHROPIC_KEY_1}
        - apiKey: ${ANTHROPIC_KEY_2}
      models:
        - id: claude-sonnet-4-20260514
        - id: claude-opus-4-5-20260220                 # Solo per task critici
        - id: claude-haiku-4-5-20251022                # Per classificazione
    google:
      apiKey: ${GOOGLE_AI_KEY}
      models:
        - id: gemini-2.5-flash
        - id: gemini-flash-lite
    ollama:
      baseUrl: http://localhost:11434
      models:
        - id: qwen2.5-coder:7b
```

### ClawRouter per Vendiamonoi

```yaml
clawrouter:
  enabled: true
  tiers:
    simple:                                            # Heartbeat, status check
      models: [google/gemini-flash-lite]
    medium:                                            # Catalogazione, traduzioni
      models: [anthropic/claude-haiku-4.5, google/gemini-2.5-flash]
    complex:                                           # Listing, analisi, coding
      models: [anthropic/claude-sonnet-4]
    reasoning:                                         # Strategia, architettura
      models: [anthropic/claude-opus-4.5]
```

### Budget stimato Vendiamonoi

| Componente | Costo mensile stimato |
|---|---|
| VPS (Hetzner CX22) | $6.50 |
| Claude Sonnet (primario) | $25-40 |
| Gemini Flash (budget/heartbeat) | $3-5 |
| Ollama (locale, backup) | $0 |
| Opus (occasionale, task critici) | $5-10 |
| **Totale stimato** | **$40-62/mese** |

### Confronto con alternativa non ottimizzata

| Setup | Costo/mese |
|---|---|
| Solo Opus, no router, heartbeat 30min | ~$500-800 |
| Vendiamonoi setup ottimizzato | ~$40-62 |
| **Risparmio** | **90-92%** |

---

## 14. Fonti & Riferimenti

### Documentazione ufficiale
- Provider Directory: https://docs.openclaw.ai/providers
- Models CLI: https://docs.openclaw.ai/concepts/models
- Model Providers: https://github.com/openclaw/openclaw/blob/main/docs/concepts/model-providers.md
- Model Failover: https://docs.openclaw.ai/concepts/model-failover
- Token Use and Costs: https://docs.openclaw.ai/reference/token-use

### Pricing e comparazioni
- PricePerToken LLM Rankings: https://pricepertoken.com/leaderboards/openclaw
- AICost Token Pricing Breakdown: https://aicost.org/blog/openclaw-ai-token-costs-2026-pricing-breakdown-optimization
- Blink Blog — Total Cost Analysis: https://blink.new/blog/openclaw-total-cost-self-host-vs-managed-2026

### Ottimizzazione
- ClawRouter GitHub: https://github.com/BlockRunAI/ClawRouter
- Cost Optimization Guide (LaoZhang): https://blog.laozhang.ai/en/posts/openclaw-save-money-practical-guide
- DailyDoseOfDS — Cut Costs 95%: https://blog.dailydoseofds.com/p/cut-openclaw-costs-by-95
- VelvetShark — Multi-Model Routing: https://velvetshark.com/openclaw-multi-model-routing

### Heartbeat e Session Management
- Heartbeat Session Tuning (Roberto Capodieci): https://capodieci.medium.com/ai-agents-020
- Token Management Guide: https://blog.laozhang.ai/en/posts/openclaw-cost-optimization-token-management
- OpenClaw Token Usage & Cost Control: https://www.getopenclaw.ai/help/token-usage-cost-management

### Provider specifici
- OpenRouter Integration: https://openrouter.ai/docs/guides/openclaw-integration
- Key Rotation (ClawCloud): https://www.clawcloud.sh/blog/openclaw-2026-3-13-upgrade-guide

---

> **Nota per Vendiamonoi**: Monitorare i costi settimanalmente. Impostare alert al 80% del budget mensile. Rivedere la configurazione del router ogni trimestre in base ai nuovi modelli disponibili.

---

*Ultimo aggiornamento: aprile 2026*
*Documento parte della Master Class OpenClaw — vendiamonoi-knowledge*
