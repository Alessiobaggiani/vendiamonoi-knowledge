# OpenAI/ChatGPT API vs Anthropic/Claude API - Technical Deep-Dive Part 1
## Knowledge Base per Vendiamonoi.it

**Data**: 7 Aprile 2026  
**Versione**: 1.0 Production-Ready  
**Target**: Architettura multi-provider, integrazione Make.com, ottimizzazione costi

---

## SEZIONE 1: Overview & Architettura delle Piattaforme AI

### 1.1 Panoramica Strategica

Le due piattaforme AI dominanti nel 2026 rappresentano approcci filosoficamente divergenti all'intelligenza artificiale. OpenAI, attraverso `api.openai.com`, mantiene il posizionamento di first-mover con il più ampio ecosistema di servizi e modelli. Anthropic, tramite `api.anthropic.com`, compensa la fondazione più recente (Claude lanciato nel 2023) con focus marcato su safety, ragionamento superiore e handling robusto di task complessi.

### 1.2 Architettura Tecnica OpenAI

La piattaforma OpenAI struttura l'accesso attraverso una gerarchia di entità:

```
┌─────────────────────────────────────────────────────┐
│ OpenAI Platform (api.openai.com)                    │
├─────────────────────────────────────────────────────┤
│ Organization (fatturazione principale)              │
│ ├── Project (isolamento risorse, accesso RBAC)     │
│ │   ├── Chat Completions (GPT-4o, GPT-4 Turbo)     │
│ │   ├── Embeddings (text-embedding-3-*)            │
│ │   ├── DALL-E (image generation)                  │
│ │   ├── Whisper (speech-to-text)                   │
│ │   ├── TTS (text-to-speech)                       │
│ │   ├── Assistants v2 (orchestration)              │
│ │   └── Batch API (async processing)               │
│ └── Project Keys (scoped API credentials)          │
└─────────────────────────────────────────────────────┘
```

Ogni **Project** è contenitore isolato con rate limits, RBAC e monitoraggio separato. Questo design permette segregazione per team, ambiente (prod/staging) o customer nel contesto SaaS.

**Endpoint Base**: `https://api.openai.com/v1`

**Header Autenticazione**:
```http
Authorization: Bearer sk-proj-XXXXX  # Project API Key
OpenAI-Organization: org-XXXXX       # Opzionale per multi-org
```

---

## SEZIONE 2: Modelli Disponibili e Posizionamento

### 2.1 Lineup OpenAI (Aprile 2026)

#### **GPT-4o (Omni)**
- **Capabilities**: Multimodal (vision + text), reasoning avanzato
- **Input**: 128K tokens
- **Output**: 4,096 tokens (standard), fino a 16K con Extended Thinking
- **Cost**: ~$15/1M input, ~$60/1M output (standard); 3x cost per extended thinking
- **Use Case**: Produzione generale, vision tasks, ragionamento complesso
- **Latency**: 500-1500ms (tipico)

#### **GPT-4 Turbo**
- **Capabilities**: Multimodal, JSON mode, function calling
- **Input**: 128K tokens
- **Output**: 4,096 tokens
- **Cost**: ~$10/1M input, ~$30/1M output
- **Use Case**: Legacy (deprecazione in corso), optimization budget-conscious
- **Status**: Deprecato a favore di GPT-4o

#### **GPT-4 Vision**
- **Deprecated**: Superato da GPT-4o
- **No longer recommended**: Usare GPT-4o per vision tasks

#### **GPT-3.5 Turbo**
- **Cost**: ~$0.50/1M input, ~$1.50/1M output
- **Use Case**: Prompt-heavy tasks, costo minimo, scarsa ragionamento
- **Status**: Supportato ma deprecato di fatto

#### **Fine-tuning Disponibile**:
- GPT-4o (limited availability)
- GPT-3.5 Turbo (maturo)
- Costo: ~$25/1M tokens training + storage

### 2.2 Lineup Anthropic (Aprile 2026)

#### **Claude 3.5 Sonnet** (Latest)
- **Capabilities**: Reasoning superiore, multimodal, extended thinking
- **Input**: 200K tokens standard (20K con extended thinking)
- **Output**: 4,096 tokens
- **Cost**: ~$3/1M input, ~$15/1M output
- **Extended Thinking Cost**: ~$15/1M input, ~$60/1M output
- **Use Case**: Reasoning-heavy, complex analysis, production-grade
- **Latency**: 800-2000ms (extended thinking adds significant delay)

#### **Claude 3.5 Haiku**
- **Capabilities**: Veloce, reasoning buono, multimodal
- **Input**: 200K tokens
- **Output**: 4,096 tokens
- **Cost**: ~$0.80/1M input, ~$4/1M output
- **Use Case**: Streaming, low-latency, cost optimization

#### **Claude 3 Opus** (Legacy)
- **Status**: Deprecated, replaced by 3.5 Sonnet
- **Cost**: ~$15/1M input, ~$75/1M output (3.5 Sonnet più cheap)

### 2.3 Confronto Rapido: Cost-Effective Analysis

```
┌──────────────────────┬──────────────┬──────────┬──────────────┐
│ Model                │ Input Cost   │ Output   │ Best For     │
├──────────────────────┼──────────────┼──────────┼──────────────┤
│ Claude 3.5 Haiku     │ $0.80/1M     │ $4/1M    │ Budget fast  │
│ GPT-3.5 Turbo        │ $0.50/1M     │ $1.5/1M  │ Ultra cheap  │
│ Claude 3.5 Sonnet    │ $3/1M        │ $15/1M   │ Reasoning    │
│ GPT-4o               │ $15/1M       │ $60/1M   │ Production   │
│ GPT-4o Extended      │ $15/1M       │ $60/1M   │ Deep thinking│
└──────────────────────┴──────────────┴──────────┴──────────────┘
```

---

## SEZIONE 3: Autenticazione e Gestione Credenziali

### 3.1 OpenAI: API Keys Organization vs Project

#### **Organization Keys** (Legacy)
```http
Authorization: Bearer sk-XXXXX
```
- Accesso a TUTTA l'organizzazione
- No scoping per progetto
- No quota/rate limit per key
- **Rischio**: Exposure = compromissione totale account
- **Deprecato di fatto**: Migrazione in corso verso Project Keys

#### **Project Keys** (Recommended)
```http
Authorization: Bearer sk-proj-XXXXX
OpenAI-Organization: org-XXXXX (opzionale)
```
- Scoped a singolo progetto
- Rate limits definibili per key
- RBAC + audit trail completo
- **Sicurezza**: Isolamento riduce blast radius
- **Best Practice**: 1 key per ambiente (dev/staging/prod)

#### **Implementazione Python**:
```python
from openai import AzureOpenAI, OpenAI

# Project Key (Recommended)
client = OpenAI(
    api_key="sk-proj-XXXXX",
    organization="org-XXXXX"  # Optional but recommended
)

# Env-based (Best Practice)
import os
client = OpenAI(
    api_key=os.getenv("OPENAI_PROJECT_KEY"),
    organization=os.getenv("OPENAI_ORG_ID")
)
```

### 3.2 Anthropic: API Keys Design

#### **Standard API Keys**
```http
Authorization: Bearer sk-ant-XXXXX
x-api-key: sk-ant-XXXXX
```
- Credential singola (no organization scoping)
- Rate limits a livello account
- No role-based access
- **Limitation**: Meno granulare di OpenAI
- **Roadmap**: Planned per futuro (Claude API v2)

#### **Implementazione Python**:
```python
from anthropic import Anthropic

# API Key
client = Anthropic(
    api_key="sk-ant-XXXXX"
)

# Env-based (Recommended)
import os
client = Anthropic(
    api_key=os.getenv("ANTHROPIC_API_KEY")
)
```

### 3.3 Secure Credential Management (Both)

#### **DO:**
- Store in environment variables (.env + git-ignored)
- Use secrets manager (AWS Secrets Manager, HashiCorp Vault)
- Rotate keys every 90 days minimum
- Use Project Keys (OpenAI) for isolation
- Create separate keys per environment

#### **DON'T:**
- Hardcode API keys in source code
- Commit .env files to Git
- Use same key across multiple services
- Share API keys between team members
- Log API keys in error messages/traces

### 3.4 Error Handling per Auth Failures

#### **OpenAI**:
```python
from openai import AuthenticationError, PermissionError

try:
    response = client.chat.completions.create(...)
except AuthenticationError as e:
    # Invalid/expired API key
    print(f"Auth failed: {e.status_code} - {e.message}")
except PermissionError as e:
    # Key lacks permissions for model/organization
    print(f"Permission denied: {e.status_code}")
```

#### **Anthropic**:
```python
from anthropic import APIError, APIStatusError

try:
    response = client.messages.create(...)
except APIStatusError as e:
    if e.status_code == 401:
        print("Authentication failed")
    elif e.status_code == 403:
        print("Forbidden")
```

---

## SEZIONE 4: Rate Limits, Quotas e Billing

### 4.1 OpenAI: Rate Limit Architecture

#### **Types of Limits**:
```
┌──────────────────┬──────────────────────────────────┐
│ Limit Type       │ Definition                       │
├──────────────────┼──────────────────────────────────┤
│ RPM              │ Requests per minute              │
│ TPM              │ Tokens per minute                │
│ Billing Quota    │ $ spend limit per billing cycle  │
│ Usage Quota      │ Unused balance expiration (3mo)  │
└──────────────────┴──────────────────────────────────┘
```

#### **Free Trial Limits** (Aprile 2026):
- $5 credits (expires 3 months)
- RPM: 3,500 (GPT-4o), 90,000 (GPT-3.5T)
- TPM: 200K total

#### **Paid Account Limits** (Post-verification):
| Model | RPM | TPM | Cost Limit |
|-------|-----|-----|------------|
| GPT-3.5 Turbo | 10,000 | 2,000,000 | Unlimited (quota-based) |
| GPT-4o | 5,000 | 1,000,000 | Unlimited (quota-based) |
| GPT-4o Extended | 2,000 | 150,000 | Unlimited (quota-based) |

#### **Request Increase Flow**:
1. Dashboard → Settings → Limits
2. Submit increase request (48h review)
3. Anthropic validates usage pattern
4. Approval typically within 5 business days

### 4.2 Anthropic: Rate Limit Architecture

#### **Limits per Account Tier**:

| Tier | Requests/min | Tokens/min | Burst |
|------|--------------|------------|-------|
| Free | 5 | 10,000 | 50 req/10min |
| Paid | 50 | 500,000 | 1,000 req/5min |
| Enterprise | Custom | Custom | Custom |

#### **Rate Limit Headers**:
```http
RateLimit-Limit-Requests: 50
RateLimit-Limit-Tokens: 500000
RateLimit-Remaining-Requests: 49
RateLimit-Remaining-Tokens: 499950
RateLimit-Reset-Requests: 2026-04-07T12:01:00Z
RateLimit-Reset-Tokens: 2026-04-07T12:05:00Z
```

#### **Handling Rate Limits**:
```python
import time
from anthropic import RateLimitError

max_retries = 3
for attempt in range(max_retries):
    try:
        response = client.messages.create(...)
        break
    except RateLimitError as e:
        if attempt < max_retries - 1:
            wait_time = 2 ** attempt  # Exponential backoff
            print(f"Rate limited, waiting {wait_time}s")
            time.sleep(wait_time)
        else:
            raise
```

### 4.3 Billing Mechanics

#### **OpenAI Billing** (Per Project):
- Usage-based (pay-as-you-go)
- Charged per 1M input/output tokens
- Billing cycle: Calendar month (1st-last day)
- Invoice: Net 30
- Multiple payment methods: Card, ACH, Invoice

#### **Anthropic Billing** (Organization-wide):
- Usage-based, charged per 1M tokens
- Monthly billing (subscription model)
- No per-project isolation
- Auto-renewal with usage-based surge
- Invoice available (Enterprise)

#### **Cost Optimization**:
```
OpenAI Strategy:
1. Use batch API for non-urgent (40% cheaper)
2. Fine-tune GPT-3.5T for repeated patterns
3. Implement prompt caching (25% cost reduction)
4. Use Vision selectively (tokens scale with image size)

Anthropic Strategy:
1. Use Haiku for streaming/low-latency (5x cheaper)
2. Implement prompt caching (25% reduction)
3. Use batch API (not yet available, Q2 2026)
4. Leverage 200K context window for fewer API calls
```

---

## SEZIONE 5: Chat Completions API (OpenAI)

### 5.1 Endpoint e Basic Request

#### **Endpoint**:
```
POST https://api.openai.com/v1/chat/completions
```

#### **Minimal Request**:
```json
{
  "model": "gpt-4o",
  "messages": [
    {
      "role": "user",
      "content": "What is Claude vs ChatGPT?"
    }
  ]
}
```

#### **Response Schema**:
```json
{
  "id": "chatcmpl-XXXXX",
  "object": "chat.completion",
  "created": 1712500000,
  "model": "gpt-4o-2024-05-13",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Claude and ChatGPT are both..."
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 15,
    "completion_tokens": 120,
    "total_tokens": 135
  }
}
```

### 5.2 Advanced Parameters

#### **Message Roles**:
```python
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "First user message"},
    {"role": "assistant", "content": "First assistant response"},
    {"role": "user", "content": "Follow-up question"}
]
```

#### **Temperature & Randomness**:
```python
client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    temperature=0.7,     # 0=deterministic, 1=creative
    top_p=0.9,          # Nucleus sampling
    frequency_penalty=0.5,  # Reduce repetition (-2 to 2)
    presence_penalty=0.5    # Increase topic diversity (-2 to 2)
)
```

#### **Structured Output (JSON Mode)**:
```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "user", "content": "Extract: John works at Google"}
    ],
    response_format={
        "type": "json_schema",
        "json_schema": {
            "name": "PersonInfo",
            "schema": {
                "type": "object",
                "properties": {
                    "name": {"type": "string"},
                    "company": {"type": "string"}
                },
                "required": ["name", "company"]
            },
            "strict": True
        }
    }
)

# Output: {"name": "John", "company": "Google"}
```

#### **Vision Capabilities**:
```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "What's in this image?"},
                {
                    "type": "image_url",
                    "image_url": {
                        "url": "https://example.com/image.jpg",
                        "detail": "high"  # low, auto, high
                    }
                }
            ]
        }
    ]
)
```

#### **Function Calling** (Deprecated in favor of tools):
```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "What's the weather?"}],
    tools=[
        {
            "type": "function",
            "function": {
                "name": "get_weather",
                "description": "Get weather for a city",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "city": {"type": "string"}
                    },
                    "required": ["city"]
                }
            }
        }
    ]
)
```

### 5.3 Extended Thinking (Preview)

#### **Use Case**: Deep analysis, complex reasoning

```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Prove the Collatz conjecture"}],
    thinking={
        "type": "enabled",
        "budget_tokens": 10000  # Max thinking tokens
    }
)

print(response.choices[0].message.thinking)  # Internal reasoning
print(response.choices[0].message.content)   # Final response
```

#### **Cost Impact**:
- Thinking tokens: 3x cost of normal input
- Budget: 1K-20K tokens typical
- Adds 1-3 seconds latency

### 5.4 Streaming

```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Write a poem"}],
    stream=True
)

for chunk in response:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")
```

---

## SEZIONE 6: Messages API (Anthropic)

### 6.1 Endpoint e Basic Request

#### **Endpoint**:
```
POST https://api.anthropic.com/v1/messages
```

#### **Minimal Request**:
```json
{
  "model": "claude-3-5-sonnet-20241022",
  "max_tokens": 1024,
  "messages": [
    {
      "role": "user",
      "content": "What is Claude vs ChatGPT?"
    }
  ]
}
```

#### **Response Schema**:
```json
{
  "id": "msg-XXXXX",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "Claude and ChatGPT are both..."
    }
  ],
  "model": "claude-3-5-sonnet-20241022",
  "stop_reason": "end_turn",
  "stop_sequence": null,
  "usage": {
    "input_tokens": 15,
    "output_tokens": 120
  }
}
```

### 6.2 Key Differences from OpenAI

#### **Required Field: max_tokens**
```python
# OpenAI: Optional (defaults to model max)
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[...]
)

# Anthropic: REQUIRED
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,  # MANDATORY
    messages=[...]
)
```

#### **No System Role in messages array**:
```python
# OpenAI: System in messages
messages = [
    {"role": "system", "content": "Be helpful"},
    {"role": "user", "content": "Hi"}
]

# Anthropic: Separate parameter
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    system="Be helpful",  # Separate parameter
    messages=[
        {"role": "user", "content": "Hi"}
    ]
)
```

#### **Messages Always Alternate**:
```python
# Anthropic enforces user-assistant alternation
# This FAILS:
messages = [
    {"role": "user", "content": "Hi"},
    {"role": "user", "content": "Are you there?"}
]

# Correct:
messages = [
    {"role": "user", "content": "Hi"},
    {"role": "assistant", "content": "Hello!"},
    {"role": "user", "content": "Are you there?"}
]
```

### 6.3 Vision Support

#### **Image Handling**:
```python
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "What's in this image?"},
                {
                    "type": "image",
                    "source": {
                        "type": "base64",
                        "media_type": "image/jpeg",
                        "data": base64_encoded_image
                    }
                }
            ]
        }
    ]
)
```

#### **Supported Media Types**:
- image/jpeg
- image/png
- image/gif
- image/webp

### 6.4 Tool Use (Function Calling)

```python
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    tools=[
        {
            "name": "get_weather",
            "description": "Get current weather for a city",
            "input_schema": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "City name"}
                },
                "required": ["city"]
            }
        }
    ],
    messages=[{"role": "user", "content": "What's the weather in Paris?"}]
)

# Check for tool use
if response.stop_reason == "tool_use":
    for block in response.content:
        if block.type == "tool_use":
            print(f"Call {block.name} with {block.input}")
```

### 6.5 Extended Thinking (Preview)

```python
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=16000,  # Much higher for thinking
    thinking={
        "type": "enabled",
        "budget_tokens": 10000
    },
    messages=[{"role": "user", "content": "Solve this complex problem..."}]
)

# Extract thinking and response
for block in response.content:
    if block.type == "thinking":
        print(f"Internal reasoning: {block.thinking}")
    elif block.type == "text":
        print(f"Response: {block.text}")
```

### 6.6 Streaming

```python
with client.messages.stream(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Write a poem"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

---

## SEZIONE 7: Prompt Caching

### 7.1 OpenAI Prompt Cache (Stable)

#### **How It Works**:
```
Request 1: "Analyze Shakespeare's works"
  → 10,000 tokens cached at 25% cost
  
Request 2: "Compare Hamlet with Macbeth"
  → Cache hit! Save 2,500 tokens
  → Pay only for new tokens + cache usage
```

#### **Cache Control Headers**:
```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": "\n" * 1000 + "Large context...",
                    "cache_control": {"type": "ephemeral"}
                },
                {
                    "type": "text",
                    "text": "New question based on context"
                }
            ]
        }
    ]
)

# Check cache stats
print(f"Cache creation: {response.usage.cache_creation_input_tokens}")
print(f"Cache read: {response.usage.cache_read_input_tokens}")
```

#### **Cache Types**:
- **ephemeral**: 5-minute TTL (no cost if not used)
- **permanent**: Until manually cleared (charged for storage)

### 7.2 Anthropic Prompt Cache (Preview)

#### **Implementation**:
```python
response = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    system=[
        {
            "type": "text",
            "text": "You are an expert analyst with knowledge of..."
        },
        {
            "type": "text",
            "text": large_documentation,
            "cache_control": {"type": "ephemeral"}
        }
    ],
    messages=[{"role": "user", "content": "Analyze this data..."}]
)

print(f"Cache creation: {response.usage.cache_creation_input_tokens}")
print(f"Cache read: {response.usage.cache_read_input_tokens}")
```

#### **Cost Savings**:
```
Cache creation: 10% of normal input cost
Cache read: 10% of normal input cost (vs 100% without cache)
Breakeven: ~10 queries with same context
```

### 7.3 Best Practices

```python
# Pattern 1: System-level caching
def cached_analysis(user_question: str):
    return client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=1024,
        system=[
            {
                "type": "text",
                "text": "You are an assistant",
                "cache_control": {"type": "ephemeral"}
            },
            {
                "type": "text",
                "text": shared_knowledge_base,  # Cached on each call
                "cache_control": {"type": "ephemeral"}
            }
        ],
        messages=[{"role": "user", "content": user_question}]
    )

# Pattern 2: Context-specific caching
def code_review(code: str, guidelines: str):
    return client.messages.create(
        model="gpt-4o",
        messages=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "text",
                        "text": f"Review guidelines:\n{guidelines}",
                        "cache_control": {"type": "ephemeral"}
                    },
                    {"type": "text", "text": f"Code to review:\n{code}"}
                ]
            }
        ]
    )
```

---

## SEZIONE 8: Batch API and Async Processing

### 8.1 OpenAI Batch API

#### **Use Cases**:
- Cost optimization (40% cheaper)
- Non-urgent data processing (overnight runs)
- Large volume processing (1000s of requests)
- No real-time latency requirements

#### **File Format (JSONL)**:
```jsonl
{"custom_id": "request-1", "params": {"model": "gpt-4o", "messages": [{"role": "user", "content": "Analyze this..."}]}}
{"custom_id": "request-2", "params": {"model": "gpt-4o", "messages": [{"role": "user", "content": "Analyze that..."}]}}
```

#### **Submission Flow**:
```python
# 1. Upload batch file
with open("batch_requests.jsonl", "rb") as f:
    batch_file = client.files.create(
        file=f,
        purpose="batch"
    )

# 2. Create batch job
batch = client.batches.create(
    input_file_id=batch_file.id,
    endpoint="/v1/chat/completions",
    timeout_minutes=24
)

print(f"Batch ID: {batch.id}")

# 3. Check status (poll or webhook)
while batch.status == "in_progress":
    batch = client.batches.retrieve(batch.id)
    time.sleep(10)

# 4. Retrieve results
if batch.status == "completed":
    results_file = client.files.content(batch.output_file_id)
    for line in results_file.text.split("\n"):
        if line:
            result = json.loads(line)
            print(f"Request {result['custom_id']}: {result['result']}")
```

#### **Pricing**:
- Input: 50% discount vs standard
- Output: 50% discount vs standard
- Min batch: 100K tokens
- Processing time: 24h SLA

### 8.2 Anthropic Batch Processing (Q2 2026 Roadmap)

#### **Current Status**: Not yet available
#### **Expected Features**:
- JSONL format similar to OpenAI
- Cost: 50% reduction expected
- Processing: 12-24h turnaround
- File size: Up to 100MB

### 8.3 Async Patterns (Current)

#### **Pattern 1: Queue-based Processing**
```python
import asyncio
from anthropic import AsyncAnthropic

async def process_batch(items: list[str]):
    client = AsyncAnthropic()
    tasks = [
        client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=1024,
            messages=[{"role": "user", "content": item}]
        )
        for item in items
    ]
    results = await asyncio.gather(*tasks)
    return results

# Run
results = asyncio.run(process_batch(large_item_list))
```

#### **Pattern 2: Webhook-based (Make.com Integration)**
```javascript
// Make.com scenario
// 1. Trigger: New CSV uploaded
// 2. Parse CSV → Create items
// 3. Call API with webhook response URL
// 4. Store job ID in data store
// 5. Webhook receives completion → Process results
```

#### **Pattern 3: Database-backed Queue**
```python
import time
from datetime import datetime

def async_api_call(item_id: int, prompt: str):
    # Store in DB with status=pending
    db.insert({
        "item_id": item_id,
        "status": "pending",
        "created_at": datetime.now()
    })
    
    # Background worker processes
    async def worker():
        try:
            result = client.messages.create(
                model="claude-3-5-sonnet-20241022",
                max_tokens=1024,
                messages=[{"role": "user", "content": prompt}]
            )
            db.update(item_id, {"status": "completed", "result": result.content})
        except Exception as e:
            db.update(item_id, {"status": "error", "error": str(e)})
    
    asyncio.create_task(worker())
```

---

## Summary: OpenAI vs Anthropic Selection Matrix

```
USE OPENAI (GPT-4o) WHEN:
  ✓ Production-grade reliability needed
  ✓ Vision tasks are critical
  ✓ Extended thinking (deep analysis)
  ✓ Batch API cost optimization required
  ✓ Largest ecosystem of integrations
  ✓ Project-scoped security critical

USE ANTHROPIC (Claude 3.5) WHEN:
  ✓ Reasoning quality is priority
  ✓ Long context (200K tokens) needed
  ✓ Cost optimization (3-5x cheaper)
  ✓ Safety/alignment critical
  ✓ Extended thinking (preview)
  ✓ Streaming performance matters

USE ANTHROPIC HAIKU WHEN:
  ✓ Budget is severely constrained
  ✓ Latency < 500ms required
  ✓ Simple classification/routing
  ✓ High-volume processing
  ✓ Streaming-only use case

MIXED APPROACH:
  → Haiku for preprocessing/routing
  → GPT-4o/Claude Sonnet for heavy lifting
  → Batch API for cost-sensitive volume
  → Caching for repeated contexts
```

---

**Prossimamente**: Sezione 9-16 (Fine-tuning, Embeddings, Vision API, Whisper, Tool Calling, Agentic Patterns, Multi-provider strategies, Make.com Integration)
