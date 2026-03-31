# ChatGPT / OpenAI — Documentazione Tecnica Completa

> **Vendiamonoi.it S.R.L.** — Knowledge Base Tecnica
> Versione: 1.0 | Data: 31/03/2026 | Autore: Claude (Cowork)

---

## 1. Panoramica

ChatGPT è il prodotto consumer di OpenAI basato sui modelli GPT. OpenAI fornisce sia l'interfaccia ChatGPT (web/app/desktop) sia una piattaforma API completa per integrare capacità AI in applicazioni custom.

### Rilevanza per Vendiamonoi.it

- **Generazione schede prodotto** — Titoli SEO, descrizioni, bullet points per milioni di SKU su 30+ marketplace
- **Traduzione e localizzazione** — Adattamento cataloghi per marketplace multilingue (FR, DE, ES, NL, PL)
- **Customer service automatizzato** — Risposte automatiche a messaggi marketplace
- **Classificazione prodotti** — Mapping categorie fornitore → categorie marketplace
- **Parsing cataloghi fornitori** — Pulizia e normalizzazione dati
- **Automazioni Make.com** — Integrazione OpenAI nei workflow di distribuzione digitale

---

## 2. Piani e Limiti

### Piani ChatGPT

| Piano | Prezzo | Modelli | Note |
|-------|--------|---------|------|
| Free | $0/mese | GPT-4.1-mini limitato | Messaggi limitati |
| Plus | $20/mese | GPT-5, GPT-4.1, DALL-E | Accesso anticipato |
| Pro | $200/mese | Tutti + o1-pro, Deep Research | Utilizzo illimitato |
| Team | $25/utente/mese | Come Plus + workspace | Admin console |
| Enterprise | Custom | Tutti + SSO, SCIM | SLA dedicato |

### Pricing API (Marzo 2026)

| Modello | Input $/1M | Output $/1M | Context |
|---------|-----------|-------------|---------|
| gpt-5.4 | ~$2.50 | ~$10.00 | 1M tokens |
| gpt-5.4-mini | ~$0.40 | ~$1.60 | 1M tokens |
| gpt-4.1 | ~$2.00 | ~$8.00 | 1M tokens |
| gpt-4.1-mini | ~$0.40 | ~$1.60 | 1M tokens |
| gpt-4.1-nano | ~$0.10 | ~$0.40 | 128K tokens |
| o3-pro | ~$150 | ~$600 | 200K tokens |
| o4-mini | ~$1.10 | ~$4.40 | 200K tokens |
| text-embedding-3-small | $0.02 | — | 8.191 tokens |
| text-embedding-3-large | $0.13 | — | 8.191 tokens |

### Usage Tiers

| Tier | Spesa | RPM | TPM |
|------|-------|-----|-----|
| Free | $0 | 3 | 40K |
| Tier 1 | $5 | ~1.000 | ~500K |
| Tier 2 | $50 | ~2.000 | ~2M |
| Tier 3 | $100 | ~5.000 | ~5M |
| Tier 4 | $250 | ~10.000 | ~10M |
| Tier 5 | $1.000 | ~10.000+ | ~30M |

---

## 3. API Documentation

### Autenticazione

**Tipo:** Bearer Token (API Key)

```
Authorization: Bearer sk-proj-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

**Headers opzionali:**
- `OpenAI-Organization: org-XXXX`
- `OpenAI-Project: proj_XXXX`

**Base URL:** `https://api.openai.com/v1/`

### Endpoint Principali

#### Responses API (Raccomandato — Nuovo)

**POST** `/v1/responses`

Nuovo primitivo API, evoluzione di Chat Completions. Raccomandato per tutti i nuovi progetti.

**Vantaggi:**
- Built-in tools (web search, file search, code interpreter, MCP)
- Cache utilization 40-80% migliore
- +3% su benchmark (SWE-bench) con reasoning models
- Conversation state automatico con `store: true`

#### Chat Completions API (Legacy — Supportato indefinitamente)

**POST** `/v1/chat/completions`

Standard industriale. Parametri: model, messages[] (system/user/assistant), temperature, max_tokens, top_p, frequency_penalty, presence_penalty, stream.

#### Embeddings API

**POST** `/v1/embeddings` — Ricerca semantica, deduplicazione prodotti, clustering.
Modelli: text-embedding-3-small (1536 dim), text-embedding-3-large (3072 dim).

#### Image Generation API

**POST** `/v1/images/generations` — gpt-image-1.5, gpt-image-1, gpt-image-1-mini.
> ⚠️ DALL·E 2/3 deprecati, rimozione maggio 2026.

#### Audio API

- **STT:** `POST /v1/audio/transcriptions` — gpt-4o-transcribe, whisper-1
- **TTS:** `POST /v1/audio/speech` — gpt-4o-mini-tts

#### Fine-tuning API

**POST** `/v1/fine_tuning/jobs` — Modelli custom (gpt-4.1-mini, gpt-4.1-nano supportati).

#### Batch API

**POST** `/v1/batches` — Elaborazione asincrona, **50% sconto**.
Supporta: /v1/chat/completions, /v1/embeddings, /v1/completions.

#### Moderations API

**POST** `/v1/moderations` — Verifica contenuti, **gratuito**.

### Rate Limits

Misurati in 5 dimensioni: RPM, RPD, TPM, TPD, IPM.

**Gestione:** exponential backoff, header retry-after, circuit breaker, Batch API per alto volume.

### Error Codes

| Code | Tipo | Azione |
|------|------|--------|
| 400 | Bad Request | Verificare payload |
| 401 | Unauthorized | Verificare API key |
| 429 | Rate Limited | Backoff + retry |
| 500 | Server Error | Retry |
| 503 | Unavailable | Exponential backoff |

**Regola:** 4xx (eccetto 429/408) = fix codice. 5xx e 429 = retry.

---

## 4. Webhook

OpenAI supporta webhook nativi (Standard Webhooks specification).

### Eventi Disponibili

| Evento | Trigger |
|--------|---------|
| response.completed | Background response completata |
| response.cancelled | Response cancellata |
| batch.completed | Batch job completato |
| batch.failed | Batch job fallito |
| batch.expired | Batch scaduto |
| fine_tuning.job.succeeded | Fine-tuning completato |
| fine_tuning.job.failed | Fine-tuning fallito |

### Sicurezza

- Firma webhook-signature per verifica
- webhook-id come idempotency key (possibili duplicati)
- Rispondere con 2xx entro 30 secondi

---

## 5. MCP Server (Model Context Protocol)

### OpenAI come MCP Client

ChatGPT Developer Mode funziona come MCP client, connettendosi a server MCP esterni.

**Configurazione via Responses API:** tool type "mcp" con server_url e server_label.

### MCP Server Community

| Server | Linguaggio | Funzione |
|--------|-----------|----------|
| automateyournetwork/chatGPT_MCP | Python | Chat Completions |
| thadius83/mcp-server-openai | TypeScript | Chat Completions + o3-mini |
| Apps SDK (ufficiale) | Python/TS | Framework per creare server |

### Built-in Tools (Responses API)

| Tool | Funzione |
|------|----------|
| web_search | Ricerca web real-time |
| file_search | Ricerca in file caricati |
| code_interpreter | Esecuzione Python |
| computer_use | Controllo computer (beta) |

---

## 6. SDK e Librerie

### Python SDK (Ufficiale)

```bash
pip install openai
```

Supporta: Chat Completions, Responses API, Embeddings, Batch, Images, Audio, Fine-tuning, Streaming, Async.

**Best practice:** Un solo client riutilizzato, AsyncOpenAI per throughput, response_format json_object per output strutturato.

### Node.js SDK (Ufficiale)

```bash
npm install openai
```

TypeScript support nativo. Stesse funzionalità del Python SDK.

### Agents SDK (Python)

```bash
pip install openai-agents
```

Framework multi-agent. Python 3.10+, 100+ LLM, MCP nativo, guardrails, tracing, sessions.

### Altre Librerie Ufficiali

.NET (openai-dotnet), Java (openai-java), Go (openai-go).

---

## 7. Use Case Vendiamonoi.it

1. **Generazione Bulk Schede Prodotto** — Batch API + prompt marketplace-specifico per migliaia di SKU
2. **Traduzione Cataloghi** — Batch API + gpt-4.1-mini per FR, DE, ES, NL, PL
3. **Classificazione Categorie** — Embeddings + Chat Completion per mapping automatico
4. **Customer Service Automatizzato** — Webhook marketplace → Make.com → OpenAI → risposta
5. **Parsing Cataloghi Fornitori** — File API + Code Interpreter → normalizzazione dati
6. **Analisi KPI Marketplace** — Supabase + Code Interpreter → report con insight

---

## 8. Integrazioni Stack

- **OpenAI + Make.com** — Modulo nativo, pattern Trigger → OpenAI → Router → Pubblicazione
- **OpenAI + Supabase** — pgvector per embeddings, caching risposte, logging usage
- **OpenAI + Notion** — Generazione contenuti, analisi documenti, automazioni
- **OpenAI + Channable/ChannelEngine** — Feed optimization, A/B testing titoli, localizzazione
- **OpenAI + Shopify** — Descrizioni, SEO meta tags, chatbot customer service

---

## 9. Limitazioni

| Limitazione | Workaround |
|-------------|------------|
| Rate limit bassi ai primi tier | Batch API, queue system |
| Costo per alto volume | nano/mini, caching, batch (50% sconto) |
| Output inconsistente | seed, temperature 0, few-shot examples |
| Hallucination | Validazione post-gen, dati nel prompt |
| Assistants API deprecated (ago 2026) | Migrare a Responses API |
| DALL-E 2/3 deprecated (mag 2026) | Usare gpt-image-1 |

---

## 10. Risorse

- API Reference: developers.openai.com/api/docs
- Rate Limits: platform.openai.com/docs/guides/rate-limits
- Webhooks: developers.openai.com/api/docs/guides/webhooks
- MCP: developers.openai.com/api/docs/guides/tools-connectors-mcp
- Python SDK: github.com/openai/openai-python
- Agents SDK: github.com/openai/openai-agents-python
- Cookbook: github.com/openai/openai-cookbook

---

## 11. Changelog

| Data | Versione | Modifiche | Autore |
|------|----------|-----------|--------|
| 31/03/2026 | 1.0 | Documentazione completa — API REST, Webhook, MCP, SDK, Use Cases, Integrazioni | Claude (Cowork) |
