# OpenAI & Anthropic Claude API — Ricerca Tecnica Approfondita 2026

**Data della Ricerca:** 7 Aprile 2026
**Contesto:** Vendiamonoi.it — Distribuzione digitale multi-marketplace europea (20-30 piattaforme, 100+ fornitori, milioni SKU)

---

## EXECUTIVE SUMMARY

Questa ricerca approfondisce le due piattaforme API dominanti nel mercato LLM nel 2026:

| Aspetto | OpenAI | Anthropic/Claude |
|---------|--------|------------------|
| **Modello Flagship** | GPT-5.4 (1M context) | Claude Opus 4.6 (1M context) |
| **Modello Budget** | GPT-4o-mini ($0.15/$0.60) | Claude Haiku 4.5 ($1/$5) |
| **Prezzo Output** | $2.50/1M input (GPT-5.4) | $25.00/1M output (Opus) |
| **Batch Discount** | 50% off | 50% off |
| **Best for** | Coding, OSInt, reasoning | Writing, long context, nuance |
| **Deprecazione** | Assistants API (Aug 2026) | N/A |

**Per Vendiamonoi:** Claude Sonnet 4.6 + prompt caching (90% cost reduction) + vision/PDF per data extraction è la scelta ottimale per prodotto description + traduzione + customer service.

---

## 1. PIATTAFORMA OPENAI — ARCHITETTURA E MODELLI

### 1.1 Base URL e API Versions

```
Base URL: https://api.openai.com/v1
Versione corrente: v1 (stabile dal 2024)
Modalità di accesso: HTTP REST, WebSocket (Realtime), SIP
```

**Endpoint Principali:**
- `/v1/chat/completions` — Chat messages (principale)
- `/v1/responses` — Nuovo (sostituisce Assistants API)
- `/v1/batches` — Batch async processing
- `/v1/realtime` — Audio/speech streaming
- `/v1/files` — File upload & management
- `/v1/embeddings` — Text embedding

### 1.2 Linea Modelli OpenAI 2026

| Modello | Input | Output | Context | Specialità |
|---------|-------|--------|---------|----------|
| **GPT-5.4** | $2.50 | $10.00 | 1M | Coding (OSWorld 75%), reasoning, multi-step |
| **GPT-5.2** | $2.25 | $9.00 | 200K | Performance/cost balance |
| **GPT-4o** | $0.50 | $2.00 | 128K | Multimodal (vision + text) |
| **GPT-4o-mini** | $0.15 | $0.60 | 128K | Fast, cheap, simple tasks |
| **o1-preview** | $15.00 | $60.00 | 128K | Extended thinking (reasoning-optimized) |
| **o1-mini** | $3.00 | $12.00 | 128K | Reasoning, cost-effective |

**Deprecazioni Importanti (2026):**
- `gpt-4-turbo` — Ritirato a Febbraio 2026
- `Assistants API` — Fine supporto Agosto 2026 → migra a `/v1/responses`
- `text-embedding-3-small` — Consigliato passare a versioni future

---

## 2. OPENAI AUTHENTICATION & API KEYS

### 2.1 Tipi di Key

```bash
# Standard API Key (Progetto-scoped)
sk-proj-abcdef123456...
  - Creato da console.openai.com
  - Scoped a progetto specifico
  - Ideale per backend/business APIs
  - Permette fine-grained permissions

# Organization API Key (Legacy)
sk-org-abcdef123456...
  - Creato da organization settings
  - Accesso a tutti i progetti
  - Less recommended (security)

# User API Key
sk-user-abcdef123456...
  - Personale, legato a claude.ai subscription
  - NOT per business APIs
```

### 2.2 Setup Base

```bash
# Environment variable
export OPENAI_API_KEY="sk-proj-..."

# cURL example
curl https://api.openai.com/v1/chat/completions \
  -H "Authorization: Bearer sk-proj-..." \
  -H "Content-Type: application/json" \
  -d '{"model":"gpt-5.4","messages":[{"role":"user","content":"Hello"}]}'

# Python SDK
from openai import OpenAI
client = OpenAI(api_key="sk-proj-...")
response = client.chat.completions.create(
    model="gpt-5.4",
    messages=[{"role":"user","content":"Generate description"}]
)
```

### 2.3 Scopes & Permissions

```json
{
  "scopes": [
    "chat.completions",
    "chat.completions.create",
    "batch.create",
    "batch.retrieve",
    "files.upload",
    "files.delete",
    "models.list"
  ]
}
```

---

## 3. OPENAI PRICING 2026

### 3.1 Input/Output Token Pricing

| Modello | Input (/1M) | Output (/1M) | Context Window |
|---------|-------------|--------------|----------------|
| **GPT-5.4** | $2.50 | $10.00 | 1M |
| **GPT-5.2** | $2.25 | $9.00 | 200K |
| **GPT-4o** | $0.50 | $2.00 | 128K |
| **GPT-4o-mini** | $0.15 | $0.60 | 128K |
| **o1-preview** | $15.00 | $60.00 | 128K |
| **o1-mini** | $3.00 | $12.00 | 128K |

### 3.2 Batch API Pricing (50% Discount)

```
Batch API:
- Input tokens: 50% of standard price
- Output tokens: 50% of standard price
- Processing time: 24 hours
- Min request size: 100 requests

Esempio GPT-4o-mini batch:
- Standard: 100K input tokens = $0.15 × 100 / 1M = $0.015
- Batch: $0.015 × 0.5 = $0.0075 (50% off)
```

### 3.3 Vision API Pricing

```
Vision (image tokens):
- Low detail: 85 tokens per image
- High detail: 170 tokens per image + (170 × # tiles)

Esempio:
- GPT-4o: $0.50/1M input
- 1 low-detail image: 85 tokens = 85 × $0.50 / 1M = $0.0000425
- 100 images: $0.00425
```

---

## 4. OPENAI RATE LIMITS 2026

### 4.1 Tier System (TPM = Tokens Per Minute)

| Tier | Monthly Spend | Input TPM | Output TPM | RPM | Status |
|------|---------------|-----------|------------|-----|--------|
| **Free** | N/A | 90K | 30K | 3,500 | Deprecated |
| **Tier 1** | $0-5 | 200K | 40K | 3,500 | Starting |
| **Tier 2** | $5-50 | 500K | 100K | 3,500 | Growing |
| **Tier 3** | $50-200 | 2M | 400K | 10K | High |
| **Tier 4** | $200+ | 8M | 2M | 10K | Enterprise |
| **Tier 5** | Custom | 128M+ | 128M+ | Unlimited | On-demand |

### 4.2 Rate Limit Headers

```
Response headers:
  x-ratelimit-limit-tokens: 200000
  x-ratelimit-remaining-tokens: 199500
  x-ratelimit-reset-tokens: 60s
  
  x-ratelimit-limit-requests: 3500
  x-ratelimit-remaining-requests: 3499
  x-ratelimit-reset-requests: 60s
```

### 4.3 Best Practices per Rate Limiting

```python
import time
import random
from openai import OpenAI, RateLimitError

client = OpenAI(api_key="sk-proj-...")

def call_with_exponential_backoff(max_retries=3):
    for attempt in range(max_retries):
        try:
            response = client.chat.completions.create(
                model="gpt-4o-mini",
                messages=[{"role":"user","content":"Generate product description"}]
            )
            return response
        except RateLimitError as e:
            if attempt == max_retries - 1:
                raise
            wait_time = 2 ** attempt + random.uniform(0, 1)
            print(f"Rate limited. Waiting {wait_time:.1f}s...")
            time.sleep(wait_time)
```

---

## 5. OPENAI FUNCTION CALLING & STRUCTURED OUTPUTS

### 5.1 Function Calling (Tool Use)

```json
{
  "model": "gpt-5.4",
  "messages": [
    {
      "role": "user",
      "content": "Extract product information from this listing"
    }
  ],
  "tools": [
    {
      "type": "function",
      "function": {
        "name": "extract_product",
        "description": "Extract structured product information",
        "parameters": {
          "type": "object",
          "properties": {
            "product_name": {"type": "string"},
            "price": {"type": "number"},
            "category": {
              "type": "string",
              "enum": ["Electronics", "Home", "Fashion"]
            },
            "features": {
              "type": "array",
              "items": {"type": "string"},
              "maxItems": 5
            }
          },
          "required": ["product_name", "price"]
        }
      }
    }
  ]
}
```

### 5.2 Structured Outputs (Deterministic Schema)

NEW in 2026: Forza la risposta a matchare JSON schema rigido.

```json
{
  "model": "gpt-5.4",
  "response_format": {
    "type": "json_schema",
    "json_schema": {
      "type": "object",
      "name": "ProductDescription",
      "properties": {
        "title": {
          "type": "string",
          "maxLength": 60
        },
        "description": {
          "type": "string",
          "minLength": 100,
          "maxLength": 500
        },
        "seo_keywords": {
          "type": "array",
          "items": {"type": "string"},
          "minItems": 3,
          "maxItems": 8
        },
        "price_eur": {
          "type": "number",
          "minimum": 0.01
        }
      },
      "required": ["title", "description", "price_eur"],
      "additionalProperties": false
    }
  },
  "messages": [...]
}
```

### 5.3 Strict Mode

Strictly follow schema, no deviations allowed.

```json
{
  "response_format": {
    "type": "json_schema",
    "json_schema": {
      "strict": true,
      "schema": { ... }
    }
  }
}
```

---

## 6. OPENAI BATCH API

### 6.1 How Batch Works

```
Workflow:
1. Create JSONL file with up to 100K requests
2. Upload file to OpenAI
3. Submit batch job
4. Wait 24 hours (usually 1-2 hours)
5. Retrieve results

Discount: 50% off input + output tokens
Cost: ~$0.0075 per GPT-4o-mini request (vs $0.015 regular)
```

### 6.2 Batch Request Format

```jsonl
{"custom_id": "prod-001", "method": "POST", "url": "/v1/chat/completions", "body": {"model": "gpt-4o-mini", "messages": [{"role": "user", "content": "Generate description for product..."}]}}
{"custom_id": "prod-002", "method": "POST", "url": "/v1/chat/completions", "body": {"model": "gpt-4o-mini", "messages": [{"role": "user", "content": "Generate description for product..."}]}}
```

### 6.3 Python Implementation

```python
from openai import OpenAI
import jsonlines
import time

client = OpenAI(api_key="sk-proj-...")

# Prepare batch file
requests = []
for product in products:
    requests.append({
        "custom_id": product["id"],
        "method": "POST",
        "url": "/v1/chat/completions",
        "body": {
            "model": "gpt-4o-mini",
            "messages": [
                {"role": "user", "content": f"Generate SEO description for: {product['name']}"}
            ]
        }
    })

# Upload batch file
with open("batch_requests.jsonl", "w") as f:
    with jsonlines.Writer(f) as writer:
        writer.write_all(requests)

batch_file = client.files.create(
    file=open("batch_requests.jsonl", "rb"),
    purpose="batch"
)

# Submit batch
batch = client.batches.create(input_file_id=batch_file.id)
print(f"Batch ID: {batch.id}")

# Poll for completion
while batch.status not in ["completed", "failed"]:
    time.sleep(30)
    batch = client.batches.retrieve(batch.id)
    print(f"Status: {batch.status}")

# Retrieve results
if batch.status == "completed":
    result_file = client.files.content(batch.output_file_id)
    with jsonlines.Reader(result_file.text.split('\n')) as reader:
        for result in reader:
            product_id = result["custom_id"]
            content = result["response"]["body"]["choices"][0]["message"]["content"]
            print(f"Product {product_id}: {content}")
```

---

## 7. OPENAI VISION API

### 7.1 Supported Formats

```
Formats: JPEG, PNG, GIF, WebP
Max image resolution: 20MP (20 million pixels)
Input: base64 or URL
```

### 7.2 Example Vision Request

```json
{
  "model": "gpt-4o",
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "image_url",
          "image_url": {
            "url": "https://example.com/product.jpg"
          }
        },
        {
          "type": "text",
          "text": "Analyze this product image and extract: color, size, material, brand"
        }
      ]
    }
  ]
}
```

### 7.3 Low vs High Detail

```
Low detail (default):
- 85 image tokens per image
- Fast, cheaper
- Good for simple recognition

High detail:
- 170 + (170 × tile_count) tokens
- More expensive
- Better for fine details, text in images
```

---

## 8. OPENAI REALTIME API

### 8.1 Overview

```
NEW (2025): Audio streaming API
- Voice input/output
- Low latency (<500ms roundtrip)
- Supports multiple languages
- Protocol: WebSocket
```

### 8.2 Use Cases

```
NON ideale per Vendiamonoi (focus text):
- Customer service bots con voice
- Multilingual support centers
- Voice product queries
```

---

## 9. ANTHROPIC CLAUDE API — ARCHITETTURA

### 9.1 Base URL e Endpoints

```
Base URL: https://api.anthropic.com
Versione API: 2023-06-01 (stable)
Modalities: Text, Vision (Images + PDFs), Tool Use
```

### 9.2 Claude Model Lineup 2026

| Modello | Input | Output | Context | Specialità |
|---------|-------|--------|---------|----------|
| **Claude Opus 4.6** | $5.00 | $25.00 | 1M | Best reasoning, writing, complex tasks |
| **Claude Sonnet 4.6** | $3.00 | $15.00 | 200K | Balance speed/quality (RECOMMENDED) |
| **Claude Haiku 4.5** | $0.50 | $2.50 | 100K | Fast, cheap, simple tasks |
| **Claude 3.5 Sonnet** | $3.00 | $15.00 | 200K | Previous version (legacy) |

### 9.3 Key Advantages vs OpenAI

```
1. Prompt Caching — 90% cost reduction
2. PDF Support — Native (not just image)
3. Vision — Stronger for document understanding
4. Safety — Constitutional AI principles
5. Context Window — 1M available (not rate-limited)
6. No Function Calling — Has Tool Use (cleaner)
```

---

## 10. CLAUDE VISION & PDF SUPPORT

### 10.1 Image Support

```json
{
  "role": "user",
  "content": [
    {
      "type": "image",
      "source": {
        "type": "base64",
        "media_type": "image/jpeg",
        "data": "/9j/4AAQSkZJR..."
      }
    },
    {
      "type": "image",
      "source": {
        "type": "url",
        "url": "https://example.com/product.jpg"
      }
    },
    {
      "type": "text",
      "text": "Analyze these product images"
    }
  ]
}
```

**Supported Formats:** JPEG, PNG, GIF, WebP

**Limits:** 600 images per request (but 32 MB request size limit può essere raggiunto prima)

### 10.2 PDF Support (Beta)

Innovazione chiave: Claude processo ogni pagina PDF sia come **testo che come immagine**.

```json
{
  "role": "user",
  "content": [
    {
      "type": "document",
      "source": {
        "type": "url",
        "url": "https://example.com/supplier-pricelist.pdf"
      }
    },
    {
      "type": "text",
      "text": "Extract all product names, prices, and specifications"
    }
  ]
}
```

**Vantaggi rispetto OCR tradizionale:**
- Capisce layout + tabelle
- Estrae da immagini dentro PDF
- Mantiene context di intere pagine
- Gestisce multi-page documents

---

## 11. CLAUDE TOOL USE & STRUCTURED OUTPUTS

### 11.1 Tool Use Definizione

```json
{
  "tools": [
    {
      "name": "extract_product_info",
      "description": "Extract structured product information",
      "input_schema": {
        "type": "object",
        "properties": {
          "product_name": {
            "type": "string",
            "description": "Product name/title"
          },
          "category": {
            "type": "string",
            "enum": ["Electronics", "Home", "Fashion", "Sports", "Other"],
            "description": "Product category"
          },
          "price": {
            "type": "number",
            "description": "Product price in EUR"
          },
          "features": {
            "type": "array",
            "items": {"type": "string"},
            "description": "Key product features",
            "maxItems": 5
          }
        },
        "required": ["product_name", "price"]
      }
    }
  ],
  "messages": [
    {
      "role": "user",
      "content": "Extract product info from this PDF content..."
    }
  ]
}
```

### 11.2 Structured Outputs (Constraint Schema)

Forza la risposta a matchare uno schema JSON rigoroso:

```json
{
  "model": "claude-opus-4-6",
  "messages": [...],
  "extra_headers": {
    "anthropic-beta": "structured-outputs-2024-10-15"
  },
  "response_schema": {
    "type": "json_schema",
    "json_schema": {
      "type": "object",
      "title": "ProductDescription",
      "properties": {
        "title": {
          "type": "string",
          "description": "SEO-optimized product title (max 60 chars)",
          "maxLength": 60
        },
        "seo_keywords": {
          "type": "array",
          "items": {"type": "string"},
          "minItems": 3,
          "maxItems": 8,
          "description": "Target keywords for SEO"
        },
        "description": {
          "type": "string",
          "minLength": 100,
          "maxLength": 500,
          "description": "Product description"
        },
        "price_eur": {
          "type": "number",
          "minimum": 0.01,
          "description": "Price in EUR"
        }
      },
      "required": ["title", "description", "price_eur"]
    }
  }
}

// Response è GARANTITO matching schema — no parsing errors possible
```

### 11.3 Differenza: Tool Use vs Structured Outputs

| Aspetto | Tool Use | Structured Outputs |
|---------|----------|--------------------|
| **Cosa controlla** | Parametri quando Claude chiama una function | Risposta finale di Claude |
| **Quando usare** | Bridge a sistemi esterni (APIs, DB) | Garantire risposta valida JSON |
| **Error Handling** | Puoi rifiutare/ricalcolare | Sempre valido, sempre matching schema |
| **Latency** | Più alto (multi-turn con tool) | Leggermente più alto (grammar checking) |

---

## 12. CLAUDE EXTENDED THINKING & ADAPTIVE THINKING

### 12.1 Extended Thinking (Deprecated)

```json
{
  "model": "claude-opus-4-6",
  "messages": [...],
  "thinking": {
    "type": "enabled",
    "budget_tokens": 5000  // Min 1024
  }
}

// Response include thinking block:
{
  "content": [
    {
      "type": "thinking",
      "thinking": "<internal reasoning>"  // Not shown to users
    },
    {
      "type": "text",
      "text": "Final answer"
    }
  ]
}
```

### 12.2 Adaptive Thinking (Nuovo 2026)

Extended thinking è deprecato — rimpiazzato da Adaptive Thinking che:
- Claude **decide dinamicamente** quando e quanto pensare
- Calibra basato su `effort` parameter
- Più efficiente che budget statico

```json
{
  "model": "claude-opus-4-6",
  "messages": [...],
  "thinking": {
    "type": "adaptive",
    "effort": "high"  // "low", "medium", "high"
  }
}
```

### 12.3 Pricing — Thinking Tokens

Thinking tokens sono **output tokens a prezzo standard** (non separate tier).

```
Claude Opus 4.6:
- Input: $5.00 per 1M
- Output (incluso thinking): $25.00 per 1M
- Extended thinking min budget: 1,024 tokens

Esempio:
- Input prompt: 500 tokens
- Claude pensa: 2,000 tokens
- Claude risponde: 300 tokens
- Total charged: 500 input + 2,300 output = $5×500 + $25×2,300 = $59,500/1M usage
```

---

## 13. CLAUDE PROMPT CACHING — COST REDUCTION 90%

### 13.1 Come Funziona

Memorizza parti della prompt (system instructions, large documents, conversation history) e ricorda per richieste successive.

```json
{
  "model": "claude-opus-4-6",
  "system": [
    {
      "type": "text",
      "text": "You are a product description expert with 10 years experience..."
    },
    {
      "type": "text",
      "text": "Product database with 100,000 items: [LARGE DOCUMENT]",
      "cache_control": {"type": "ephemeral"}  // Cache per 5 min
    }
  ],
  "messages": [
    {"role": "user", "content": "Generate description for product ID 12345"}
  ]
}

// Prima request: cache WRITE (1.25x prezzo input per 5 min)
// Successive requests: cache READ (0.1x prezzo input)
// Payoff: dopo 2 letture recuperi il write cost
```

### 13.2 Pricing Structure

| Operazione | Prezzo |
|-----------|----------|
| **Cache Write (5-min duration)** | 1.25× base input |
| **Cache Write (1-hour duration)** | 2.00× base input |
| **Cache Read** | 0.10× base input |
| **Payoff point** | 1 lettura (5-min) o 2 letture (1-hour) |

### 13.3 Use Cases per Vendiamonoi

**IDEALE per prompt caching:**

1. **Product template instructions** — Stesse system prompt per 1000s descrizioni
2. **Large catalogs in context** — Referenza a database prodotti (supplier data)
3. **Conversational agents** — Conversation history reused
4. **Document processing** — Stessi guidelines per 100+ PDFs

**Esempio ROI:**

```
Scenario: Generare 5,000 product descriptions

Senza caching:
- 5,000 requests × 5,000 tokens input = 25M tokens
- 25M × $5.00/1M = $125

Con caching (una volta setup):
- First request: 5,000 tokens input @ 1.25x = $6.25
- Remaining 4,999: 5,000 tokens @ 0.10x = $2.50
- Total: $8.75

Savings: $125 - $8.75 = $116.25 (93% reduction!)
```

---

## 14. CLAUDE COMPUTER USE — BROWSER AUTOMATION

### 14.1 Overview

Beta feature che permette a Claude di:
- Veder schermi via screenshot
- Muovere mouse, cliccare buttons
- Digitare testo
- Navigare web, compilare forms

```
Diverso da Selenium/Playwright:
- Funziona VISUALMENTE (like humans)
- Non legge DOM/HTML selectors
- Ragiona su UI visualmente
```

### 14.2 Beta Headers

```
Per Opus 4.6/Sonnet 4.6:
  "computer-use-2025-11-24"

Per Sonnet 4.5, Haiku 4.5, Opus 4.5, etc.:
  "computer-use-2025-01-24"
```

### 14.3 Non Rilevante per Vendiamonoi (per ora)

Computer use è interessante per:
- Automation di task non-API
- Web scraping
- Browser-based workflows

Ma Vendiamonoi ha API dirette ai marketplace — meno necessità.

---

## 15. CLAUDE AUTHENTICATION & WORKSPACES

### 15.1 API Key Types

```
Standard API Key: sk-ant-api03-...
  - Generato da Anthropic Console
  - Scoped a workspace
  - Indipendente da Claude.ai subscription

OAuth Token: sk-ant-oat01-...
  - Legato a Claude.ai Pro/Max
  - Per utenti individuali
  - Less suitable per business APIs
```

### 15.2 Workspace Model

```
Organization (top-level)
  ├─ Default Workspace (cannot delete)
  ├─ Development Workspace
  ├─ Production Workspace
  └─ ... (up to 100 workspaces per org)

Cada workspace tiene:
  - API keys separate
  - Rate limits separate
  - Spend limits separate
  - Data residency settings
```

### 15.3 Setup

```bash
export ANTHROPIC_API_KEY="sk-ant-api03-..."

# Via Python SDK
from anthropic import Anthropic
client = Anthropic(api_key="sk-ant-api03-...")

# Via cURL
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: sk-ant-api03-..."
```

---

## 16. CLAUDE RATE LIMITS

### 16.1 Tier System

| Tier | Monthly Spend | Input TPM | Output TPM | Requests |
|------|---------------|-----------|-----------|----------|
| **Tier 1** | $0-5 | 40K | 10K | Limited |
| **Tier 2** | $5-50 | 100K | 25K | Moderate |
| **Tier 3** | $50-500 | 500K | 100K | High |
| **Tier 4** | $500+ | 2M | 500K | Very High |
| **Custom** | $10K+ | Per agreement | Per agreement | Unlimited |

### 16.2 Concurrent Requests

Numero di requests paralleli permitted (oltre TPM):

```
Tier 1: 5-10 concurrent
Tier 2: 10-25 concurrent
Tier 3: 25-50 concurrent
Tier 4: 100+ concurrent
```

### 16.3 Best Practices

Per Vendiamonoi (20-30 marketplace, 100+ suppliers):

```python
# Strategy 1: Use Batch API for non-urgent work
# (non esiste per Claude ma puoi usare job queue esterno)

# Strategy 2: Implement exponential backoff
import time
import random

def call_claude_with_retry(prompt, max_retries=3):
    for attempt in range(max_retries):
        try:
            response = client.messages.create(...)
            return response
        except RateLimitError:
            wait_time = 2 ** attempt + random.random()
            time.sleep(wait_time)

# Strategy 3: Queue management
# Usa job queue (Redis, Bull, Celery) per distribuire load
# durante peak hours

# Strategy 4: Model selection per TPM efficiency
# - Simple tasks: Haiku (ultra-cheap TPM)
# - Complex: Opus (conservare TPM per difficult tasks)
```

---

## 17. OPENAI vs CLAUDE — CONFRONTO TECNICO

### 17.1 Performance Benchmarks

| Benchmark | GPT-5.4 | Claude Opus 4.6 | Winner |
|-----------|---------|-----------------|--------|
| **SWE-Bench (coding)** | 80%+ | 80.8% | Claude |
| **SWE-Bench Pro (novel)** | 57.7% | ~45% | GPT-5.4 |
| **GPQA Diamond (science)** | Higher | Comparable | GPT-5.4 |
| **ARC-AGI-2 (reasoning)** | ~70% | ~85% | Claude |
| **MMMU-Pro (multimodal)** | Lower | 85.1% | Claude |
| **OSWorld (desktop tasks)** | 75% (first >72% human) | N/A | GPT-5.4 |
| **Code quality** | Generally good | Cleaner | Claude |

### 17.2 Feature Comparison

| Feature | OpenAI | Claude |
|---------|--------|--------|
| **Context Window (max)** | 1M | 1M |
| **Vision/Images** | Strong | Strong |
| **PDF Processing** | Weak | Strong (native) |
| **Token Counting** | API endpoint | Via Anthropic tokenizer |
| **Batch API** | Yes (50% off) | No (use external queue) |
| **Audio/Speech** | Realtime API | No |
| **Computer Use** | No | Yes (beta) |
| **Structured Outputs** | Yes | Yes |
| **Tool/Function Calling** | Yes | Yes |
| **Prompt Caching** | No | Yes (90% off) |
| **Extended Thinking** | o-series only | Adaptive thinking |

### 17.3 Scelta per Vendiamonoi

**Claude Sonnet 4.6 è la scelta OTTIMALE per:**
- Cost: $3 input, $15 output (equilibrato)
- Context: 1M tokens (Tier 4+) o 200K (standard)
- Vision: PDF extraction excellente
- Caching: Risparmia 90% su prompt ripetute

---

## 18. TOKEN COUNTING IMPLEMENTATION

### 18.1 OpenAI Tokenizer (Python)

```python
import tiktoken

# Carica tokenizer per modello specifico
encoding = tiktoken.encoding_for_model("gpt-5.4")

# Conta tokens in testo
text = "Generate a product description for wireless headphones"
tokens = encoding.encode(text)
print(f"Tokens: {len(tokens)}")  # Output: ~12 tokens

# Per chat messages, usa helper function
messages = [
    {"role": "user", "content": "Describe a product"}
]
tokens = tiktoken.encoding_for_model("gpt-5.4").encode_ordinary(
    messages[0]["content"]
)
```

### 18.2 OpenAI Token Counting API (Nuovo)

```bash
curl https://api.openai.com/v1/tokens/estimate \
  -X POST \
  -H "Authorization: Bearer sk-..." \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-5.4",
    "messages": [
      {"role": "user", "content": "Generate description"}
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "save_description",
          "parameters": {...}
        }
      }
    ]
  }'

# Response:
{
  "tokens": {
    "input": 250,
    "output": 150  // Estimate
  }
}
```

### 18.3 Claude Token Counting (Python)

```python
import anthropic

client = anthropic.Anthropic()

# Conta direttamente nella API call
response = client.messages.count_tokens(
    model="claude-opus-4-6",
    system="You are a product description expert",
    messages=[
        {
            "role": "user",
            "content": "Generate description for wireless headphones"
        }
    ]
)

print(f"Input tokens: {response.input_tokens}")
# Output: Input tokens: 45
```

---

## 19. COST OPTIMIZATION STRATEGIES

### 19.1 Matrice Decisione

```
Task: Product Description Generation (Vendiamonoi)
Volume: 5,000/mese
Urgency: Non-realtime (batch OK)

Decision Tree:
├─ Batch API + GPT-4o-mini?
│  └─ Input: 200 tok × $0.15/1M = $0.00003
│  └─ Output: 150 tok × $0.60/1M = $0.00009
│  └─ Batch discount: 50% = $0.00006/desc
│  └─ 5,000 × $0.00006 = $0.30/mese ✓ CHEAP
│
└─ Claude Sonnet + Caching + batch?
   └─ First: 5,000 tok @ 1.25× + 100 tok output = $0.019
   └─ Next 4,999: 100 tok @ 0.1× = $0.00003 each
   └─ Total: $0.019 + (4,999 × $0.00003) = $0.17/mese ✓ CHEAPER + better quality
```

### 19.2 Concrete Implementation

```python
import anthropic
import json
from datetime import datetime

client = anthropic.Anthropic()

def generate_descriptions_optimized(products: list[dict]) -> list[dict]:
    """
    Genera descrizioni con caching — first request setup cache,
    subsequent requests reuse cached system prompt.
    """

    # Static system prompt (reused via caching)
    system_instructions = [
        {
            "type": "text",
            "text": """You are an expert product description writer for European e-commerce.
Generate SEO-optimized product descriptions following these rules:
1. Title: 50-60 characters, include main keyword
2. Description: 150-300 words, engaging and informative
3. Keywords: 5-8 long-tail keywords for SEO
4. Features: Bullet list of 3-5 key benefits
Format output as JSON with keys: title, description, keywords, features"""
        },
        {
            "type": "text",
            "text": f"Product database reference:\n{json.dumps(get_product_categories(), indent=2)}",
            "cache_control": {"type": "ephemeral"}  # Cache this for 5 min
        }
    ]

    results = []

    for i, product in enumerate(products):
        # Prompt caching kicks in after first request
        response = client.messages.create(
            model="claude-opus-4-6",
            max_tokens=500,
            system=system_instructions,
            messages=[
                {
                    "role": "user",
                    "content": f"Generate description for: {product['name']}, Price: €{product['price']}, Category: {product['category']}"
                }
            ]
        )

        # Parse JSON response
        try:
            description_data = json.loads(response.content[0].text)
            results.append({
                "product_id": product["id"],
                "generated": description_data,
                "tokens_used": response.usage.input_tokens + response.usage.output_tokens
            })
        except json.JSONDecodeError:
            results.append({"product_id": product["id"], "error": "Parse failure"})

        # Logging for cost tracking
        if i == 0:
            print(f"Request {i+1}: Cache write (most expensive)")
        elif i % 100 == 0:
            print(f"Request {i+1}: Cache hit (cheap)")

    return results

# Execution
products = [
    {"id": "P001", "name": "Wireless Headphones", "price": 89.99, "category": "Electronics"},
    {"id": "P002", "name": "Phone Stand", "price": 19.99, "category": "Accessories"},
    # ... 4,998 more products
]

descriptions = generate_descriptions_optimized(products)
print(f"Generated {len(descriptions)} descriptions")
```

### 19.3 Multilingual Translation — Batch Optimization

```python
def translate_bulk(texts: list[str], target_langs: list[str]) -> dict:
    """
    Traduce 5,000 descrizioni in 5 lingue usando Batch API.
    OpenAI Batch = 50% discount, 24h turnaround.
    """

    import jsonlines
    import io

    # Create batch request file
    batch_requests = []
    for i, text in enumerate(texts):
        for lang in target_langs:
            batch_requests.append({
                "custom_id": f"{i}-{lang}",
                "method": "POST",
                "url": "/v1/chat/completions",
                "body": {
                    "model": "gpt-4o-mini",
                    "messages": [
                        {
                            "role": "user",
                            "content": f"Translate to {lang} (maintain SEO keywords):\n{text}"
                        }
                    ]
                }
            })

    # Submit batch
    with io.BytesIO() as buffer:
        with jsonlines.Writer(buffer) as writer:
            writer.write_all(batch_requests)

        batch_file = client.files.create(
            file=("batch.jsonl", buffer.getvalue()),
            purpose="batch"
        )

    batch = client.batches.create(input_file_id=batch_file.id)

    # Poll until complete
    import time
    while batch.status in ("queued", "in_progress"):
        time.sleep(5)
        batch = client.batches.retrieve(batch.id)

    # Retrieve results
    results = {}
    if batch.status == "completed":
        result_file = client.files.content(batch.output_file_id)
        for line in result_file.text.strip().split('\n'):
            result_obj = json.loads(line)
            custom_id = result_obj['custom_id']
            text_index, target_lang = custom_id.split('-')
            if text_index not in results:
                results[text_index] = {}
            results[text_index][target_lang] = result_obj['message']['content'][0]['text']

    return results
```

---

## 20. USE CASE: VENDIAMONOI PRODUCT DESCRIPTION PIPELINE

### 20.1 Architettura Consigliata

```
┌─────────────────────────────────────┐
│   Supplier Data (100+ suppliers)    │
│   Spreadsheets, PDFs, APIs          │
└──────────────┬──────────────────────┘
               │
        ┌──────▼──────┐
        │  Vendiamonoi│
        │  Data       │
        │  Extraction │ ← Claude API (vision for PDFs)
        │  Pipeline   │   Cost: $0.25 input/$1.25 output (Haiku)
        └──────┬──────┘   for fast extraction
               │
        ┌──────▼────────────────────────┐
        │ EN Description Generator       │ ← Claude Sonnet 4.6
        │ Cached system prompt           │   (main language)
        │ Reused across 5,000 SKUs       │   Cost: $3/$15 with caching
        └──────┬────────────────────────┘
               │
      ┌────────┴────────┐
      │                 │
   ┌──▼──────┐  ┌──────▼───┐
   │ Batch    │  │ Real-time │
   │ Translate│  │ QA Bot    │
   │ to FR/DE │  │ (customer │
   │ /ES/NL   │  │  support) │
   │ /PL      │  │           │
   │ (24h OK) │  │ (Opus 4.6)│
   └──────────┘  └───────────┘
        │              │
        └──────┬───────┘
               │
        ┌──────▼─────────────┐
        │ 20-30 Marketplaces │
        │ (Amazon, eBay,     │
        │  Conforama, etc.)  │
        └────────────────────┘
```

### 20.2 Component Costs (Monthly)

```
Scenario: 100,000 SKUs/month across 20 marketplaces
Languages: EN (primary) + FR/DE/ES/NL/PL

Component 1: Extraction (PDF/APIs → structured data)
  - 10,000 PDFs @ Haiku API
  - 500 tokens input, 100 output
  - Cost: (10K × 500 × $0.25 + 10K × 100 × $1.25) / 1M = $1.50

Component 2: EN Description Generation
  - 100,000 products
  - System prompt caching (shared across all)
  - First: 5K tokens @ 1.25× = $6.25
  - Remaining: 100K × 100 tokens @ 0.10× × $3 = $30
  - Output: 100K × 150 tokens × $15/1M = $225
  - Subtotal: $261.25

Component 3: Batch Translation
  - 100K descriptions × 5 languages
  - OpenAI Batch API (50% discount)
  - ~300 tokens input per translation
  - (500K translations × 300 tokens × $0.15/1M × 0.5) = $11.25
  - Output: (500K × 200 tokens × $0.60/1M × 0.5) = $30
  - Subtotal: $41.25

Component 4: Customer Service Bot
  - Realtime question-answering
  - ~500 conversations/day × 20 days = 10K/month
  - ~1000 tokens per conversation
  - (10K × 1000 × $3 input + 10K × 500 × $15 output) / 1M = $105
  - Subtotal: $105

TOTAL MONTHLY: $1.50 + $261.25 + $41.25 + $105 = $409/month

Per SKU: $409 / 100,000 = $0.004/SKU (4 millesimiEUR)
```

### 20.3 Implementation Checklist

```
□ Setup Anthropic workspace + API key
□ Setup OpenAI project + API key
□ Create prompt template for descriptions (cached)
□ Implement token counting for budget tracking
□ Setup batch processing pipeline for translations
□ Add error handling + retry logic
□ Implement logging/monitoring for costs
□ Create webhook for marketplace integration
□ Test on 100 SKUs before full scale
□ Setup customer service bot endpoint
□ Monitor rate limits, adjust Tier if needed
□ Weekly cost reports
□ A/B test descriptions (Claude vs GPT-4o)
```

---

## 21. RISK & COMPLIANCE

### 21.1 Data Privacy

**OpenAI:**
- Default: data usato per training models (opt-out con data residency)
- US-based (GDPR considerations)
- Consenti compliance per healthcare, finance

**Claude (Anthropic):**
- Default: data NON usato per training
- GDPR-compliant by default
- EU hosting available

Per Vendiamonoi: **Claude è preferibile** per privacy marketplace data.

### 21.2 Rate Limit & Overage Protection

```json
OpenAI:
- Set usage limits in dashboard
- Hard stop quando raggiunto

Claude:
- Workspace spend limits
- Graceful degradation
```

---

## 22. MIGRAZIONE DA OPENAI A CLAUDE

Se attualmente usi OpenAI, la migrazione a Claude per descrizioni prodotto:

### 22.1 Mapping API

```python
# OpenAI
from openai import OpenAI
client = OpenAI(api_key="sk-proj-...")
response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "..."}]
)

# Claude (simile, naming diverso)
from anthropic import Anthropic
client = Anthropic(api_key="sk-ant-...")
response = client.messages.create(
    model="claude-opus-4-6",
    messages=[{"role": "user", "content": "..."}]
)
```

### 22.2 Feature Parity

| Feature | OpenAI Migration | Claude Native |
|---------|------------------|---------------|
| Chat messages | ✓ Direct mapping | ✓ Standard |
| Tool calling | ✓ Minor syntax | ✓ Clean |
| Vision | ✓ Works | ✓ Better for PDFs |
| Streaming | ✓ Yes | ✓ Yes |
| Caching | ✗ Not available | ✓ NEW (save 90%) |
| Batch API | ✓ Native (50% off) | ✗ Use external queue |

---

## 23. FONTI & RIFERIMENTI

### OpenAI
- [Pricing | OpenAI API](https://developers.openai.com/api/docs/pricing)
- [Models | OpenAI API](https://developers.openai.com/api/docs/models)
- [Rate Limits | OpenAI API](https://developers.openai.com/api/docs/guides/rate-limits)
- [Batch API | OpenAI API](https://developers.openai.com/api/docs/guides/batch)
- [Function Calling | OpenAI API](https://developers.openai.com/api/docs/guides/function-calling)
- [Structured Outputs | OpenAI API](https://platform.openai.com/docs/guides/structured-outputs)
- [Vision | OpenAI API](https://platform.openai.com/docs/guides/images-vision)
- [Realtime API | OpenAI API](https://openai.com/index/introducing-the-realtime-api/)
- [Token Counting | OpenAI API](https://developers.openai.com/api/docs/guides/token-counting)

### Anthropic/Claude
- [Pricing - Claude API Docs](https://platform.claude.com/docs/en/about-claude/pricing)
- [Models Overview - Claude API Docs](https://platform.claude.com/docs/en/about-claude/models/overview)
- [Authentication - Claude API Docs](https://platform.claude.com/docs/en/api/overview)
- [Vision - Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/vision)
- [PDF Support - Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/pdf-support)
- [Tool Use - Claude API Docs](https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview)
- [Structured Outputs - Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/structured-outputs)
- [Extended Thinking - Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/extended-thinking)
- [Prompt Caching - Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)
- [Computer Use - Claude API Docs](https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool)

### Comparisons & Analysis
- [LLM API Pricing 2026: OpenAI vs Anthropic vs Gemini | Live Comparison](https://www.cloudidr.com/llm-pricing)
- [Claude Opus 4.6 vs GPT-5.4 Comprehensive Comparison](https://help.apiyi.com/en/claude-opus-4-6-vs-gpt-5-4-comparison-12-benchmarks-guide-en.html)
- [API Rate Limiting at Scale: Patterns, Failures, and Control Strategies](https://www.gravitee.io/blog/rate-limiting-apis-scale-patterns-strategies)
- [Token Counting Explained: tiktoken, Anthropic, and Gemini (2025 Guide)](https://www.propelcode.ai/blog/token-counting-tiktoken-anthropic-gemini-guide-2025)

### Use Case Applications
- [Product Description Generators and Optimizers for SEO](https://www.spocket.co/blogs/top-product-description-generators)
- [Batch Translation API - Translate Multiple Texts at Once](https://translateplus.io/solutions/batch-translate)
- [Chatbot Intent: Classification, Examples & Detection Strategies](https://www.tidio.com/blog/chatbot-intents/)
- [PDF Data Extraction and OCR: The Ultimate Guide](https://parsio.io/blog/pdf-parser/)
- [Product Catalog Enrichment: Make Your Catalog AI-Ready](https://stylitics.com/catalog-enrichment/)

---

**Fine Documento | 7 Aprile 2026**

*Ricerca condotta per Vendiamonoi.it — Distribuzione Digitale Multi-Marketplace Europa*