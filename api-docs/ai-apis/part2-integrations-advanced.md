# Token cost example
# "detail": "high"  → ~750 tokens per 512x512
# "detail": "low"   → ~85 tokens per image (cheaper!)
```

### 8.3 Claude Vision Implementation

```python
import base64
from anthropic import Anthropic

client = Anthropic(api_key="sk-ant-...")

# Image analysis
with open("product_photo.jpg", "rb") as f:
    image_base64 = base64.b64encode(f.read()).decode('utf-8')

response = client.messages.create(
    model="claude-opus-4-20250514",
    max_tokens=1024,
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": "Analyze this product image for quality issues"
                },
                {
                    "type": "image",
                    "source": {
                        "type": "base64",
                        "media_type": "image/jpeg",
                        "data": image_base64
                    }
                }
            ]
        }
    ]
)

print(response.content[0].text)

# Token cost: ~67 tokens per image regardless of detail
```

### 8.4 PDF Document Analysis (Anthropic Advantage)

```python
import base64
from anthropic import Anthropic

client = Anthropic(api_key="sk-ant-...")

# Native PDF support
with open("supplier_contract.pdf", "rb") as f:
    pdf_data = base64.b64encode(f.read()).decode('utf-8')

response = client.messages.create(
    model="claude-opus-4-20250514",
    max_tokens=2048,
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": """Extract from this contract:
1. Company name
2. Contract duration
3. Payment terms
4. Termination clauses
5. GDPR/data processing obligations

Return as structured JSON."""
                },
                {
                    "type": "document",
                    "source": {
                        "type": "base64",
                        "media_type": "application/pdf",
                        "data": pdf_data
                    }
                }
            ]
        }
    ]
)

print(response.content[0].text)
```

### 8.5 Token Cost Comparison Summary

| Input Type | Provider | Cost | Notes |
|---|---|---|---|
| Text | Anthropic | $3/$15 per M tokens | Input/output |
| Text | OpenAI | $5/$15 per M tokens | For GPT-4 |
| Image | Anthropic | ~67 tokens fixed | Regardless of size/detail |
| Image | OpenAI | 85–$2,550 tokens | Size + detail dependent |
| PDF | Anthropic | ~0.1 tokens per char | Native support |
| PDF | OpenAI | N/A | No native support |

**Key insight**: For document-heavy workflows (contracts, PDFs, e-books), Anthropic is significantly cheaper. For image-heavy workflows, Anthropic's fixed token cost is a major advantage.

---

## Section 9: Batch Processing API

### 9.1 Batch API Overview

The **Batch Processing API** is designed for non-time-sensitive tasks where you want to:

1. **Reduce costs**: 50% discount on input tokens
2. **Process large volumes**: Thousands or millions of requests
3. **Flexible scheduling**: Processed during off-peak hours
4. **Cost predictability**: Fixed pricing, no rate limit surprises

**Typical use cases**:
- Email classification for thousands of customer messages
- Document summarization (annual reports, contracts)
- Large-scale sentiment analysis
- Generating product descriptions from catalogs
- Training data generation for fine-tuning
- Bulk data extraction and transformation

**Pricing**: 50% discount on input tokens (output tokens are same price)

**Availability**: Anthropic Batch API (available now)

### 9.2 Batch API Request Format

Batch requests are sent as a JSONL file (one JSON object per line):

```jsonl
{"custom_id": "request-1", "params": {"model": "claude-opus-4-20250514", "max_tokens": 1024, "messages": [{"role": "user", "content": "Classify this text as positive/negative: Great product!"}]}}
{"custom_id": "request-2", "params": {"model": "claude-opus-4-20250514", "max_tokens": 1024, "messages": [{"role": "user", "content": "Classify this text as positive/negative: Terrible experience."}]}}
{"custom_id": "request-3", "params": {"model": "claude-opus-4-20250514", "max_tokens": 1024, "messages": [{"role": "user", "content": "Classify this text as positive/negative: It's okay."}]}}
```

### 9.3 Python Batch Processing Example

```python
import anthropic
import json

client = anthropic.Anthropic(api_key="sk-ant-...")

# Step 1: Create batch requests
requests = []
for idx, text in enumerate([
    "Great product, highly recommended!",
    "Terrible experience, will not buy again.",
    "It's average, nothing special."
]):
    requests.append({
        "custom_id": f"sentiment-{idx}",
        "params": {
            "model": "claude-opus-4-20250514",
            "max_tokens": 256,
            "messages": [{
                "role": "user",
                "content": f"Classify sentiment (positive/negative/neutral): {text}"
            }]
        }
    })

# Step 2: Write requests to JSONL file
with open('requests.jsonl', 'w') as f:
    for request in requests:
        f.write(json.dumps(request) + '\n')

# Step 3: Submit batch
with open('requests.jsonl', 'rb') as f:
    batch_response = client.beta.messages.batch.create_from_file(
        request_file=f,
        betas=["interleaved-thinking-2025-05-14"]  # Include beta features if needed
    )

print(f"Batch ID: {batch_response.id}")
print(f"Status: {batch_response.processing_status}")

# Step 4: Poll for completion
import time
while True:
    batch_status = client.beta.messages.batch.retrieve(batch_response.id)
    print(f"Status: {batch_status.processing_status}")
    
    if batch_status.processing_status == "completed":
        break
    elif batch_status.processing_status == "failed":
        print("Batch failed!")
        break
    
    time.sleep(5)  # Check every 5 seconds

# Step 5: Retrieve results
results = client.beta.messages.batch.retrieve(batch_response.id)
for result in results.results:
    print(f"{result.custom_id}: {result.result.message.content[0].text}")
```

### 9.4 Cost Savings Example

For 1,000 requests with 500 input tokens each:

```
Regular API:
- Cost: 1,000 × 500 tokens × $3/M = $1.50

Batch API:
- Cost: 1,000 × 500 tokens × $1.50/M = $0.75
- Savings: 50% ($0.75 saved per 1,000 requests)

For large-scale operations:
- 1M requests: $750 saved
- 10M requests: $7,500 saved
```

**Important**: Batch API is designed for throughput, not speed. Batches can take hours to process. Use for non-urgent tasks only.

---

## Section 10: SDKs and Libraries

### 10.1 Official Anthropic SDK

**Python SDK**:
```bash
pip install anthropic
```

**Key features**:
- Full API support (messages, vision, PDFs, batch)
- Async support for concurrent requests
- Built-in retry logic and error handling
- Type hints for IDE support

**Initialization**:
```python
from anthropic import Anthropic, AsyncAnthropic

# Sync client
client = Anthropic(api_key="sk-ant-...")

# Async client
async_client = AsyncAnthropic(api_key="sk-ant-...")
```

**TypeScript/JavaScript SDK**:
```bash
npm install @anthropic-ai/sdk
```

**Key features**:
- Full API support
- Streaming responses
- TypeScript definitions
- Edge runtime compatibility

### 10.2 Using SDKs with Vision

```python
from anthropic import Anthropic
import base64

client = Anthropic()

# From file
with open("image.jpg", "rb") as f:
    image_data = base64.standard_b64encode(f.read()).decode("utf-8")

response = client.messages.create(
    model="claude-opus-4-20250514",
    max_tokens=1024,
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "Describe this image"},
                {
                    "type": "image",
                    "source": {
                        "type": "base64",
                        "media_type": "image/jpeg",
                        "data": image_data,
                    },
                },
            ],
        }
    ],
)

print(response.content[0].text)
```

### 10.3 Streaming Responses

```python
from anthropic import Anthropic

client = Anthropic()

with client.messages.stream(
    model="claude-opus-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Write a product description for a coffee mug"}],
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

### 10.4 Error Handling

```python
from anthropic import Anthropic, APIError, RateLimitError

client = Anthropic()

try:
    response = client.messages.create(
        model="claude-opus-4-20250514",
        max_tokens=1024,
        messages=[{"role": "user", "content": "Hello"}],
    )
except RateLimitError:
    print("Rate limited, retrying...")
except APIError as e:
    print(f"API error: {e.status_code} - {e.message}")
```

---

## Section 11: Make.com Integration

### 11.1 Make.com Overview

**Make.com** (formerly Integromat) is a low-code automation platform that connects thousands of apps. It allows non-technical users to build AI workflows without coding.

**Key benefits**:
1. **Visual workflow builder**: Drag-and-drop interface
2. **1000+ integrations**: Connect any app
3. **Conditional logic**: If/then branching
4. **Webhooks**: Trigger automations from external events
5. **Scheduling**: Run automations on a schedule

### 11.2 Setting Up Anthropic in Make.com

**Step 1**: Log into Make.com and create a new scenario

**Step 2**: Add the "HTTP > Make a request" module

**Step 3**: Configure the Anthropic API call:

```
URL: https://api.anthropic.com/v1/messages
Method: POST
Headers:
  - Content-Type: application/json
  - x-api-key: YOUR_ANTHROPIC_KEY
  - anthropic-version: 2023-06-01

Body (JSON):
{
  "model": "claude-opus-4-20250514",
  "max_tokens": 1024,
  "messages": [
    {
      "role": "user",
      "content": "{{variable_from_previous_module}}"
    }
  ]
}
```

### 11.3 Common Make.com Workflows

**Example 1: Email Classification**

1. **Gmail trigger**: "New email received"
2. **Anthropic module**: "Classify this email sentiment"
3. **Gmail action**: "Label email" (based on classification)

```json
{
  "model": "claude-opus-4-20250514",
  "max_tokens": 256,
  "messages": [
    {
      "role": "user",
      "content": "Classify the sentiment of this email and respond with ONLY the label (positive/negative/neutral):\n\n{{Gmail.email_body}}"
    }
  ]
}
```

**Example 2: Slack Message Summarization**

1. **Slack trigger**: "New message in #sales"
2. **Anthropic module**: "Summarize message"
3. **Slack action**: "Post summarized message to #executive-brief"

```json
{
  "model": "claude-opus-4-20250514",
  "max_tokens": 512,
  "messages": [
    {
      "role": "user",
      "content": "Summarize this Slack message in 2-3 bullets for an executive brief:\n\n{{Slack.message_text}}"
    }
  ]
}
```

**Example 3: Google Forms → AI Response**

1. **Google Forms trigger**: "New form submission"
2. **Anthropic module**: "Generate personalized response"
3. **Gmail action**: "Send email with AI response"

```json
{
  "model": "claude-opus-4-20250514",
  "max_tokens": 1024,
  "messages": [
    {
      "role": "user",
      "content": "A customer filled out our inquiry form with this question:\n{{GForms.question}}\n\nPlease write a professional, helpful response."
    }
  ]
}
```

### 11.4 Error Handling in Make.com

```
1. Add "Error handler" router
2. Configure for HTTP errors (4xx, 5xx)
3. Add retry logic:
   - Wait 30 seconds
   - Retry up to 3 times
   - Then send alert via Slack
```

---

## Section 12: Supabase Integration

### 12.1 Supabase Overview

**Supabase** is an open-source Firebase alternative providing:
- PostgreSQL database
- Authentication
- Real-time subscriptions
- Vector storage (pgvector)
- Edge Functions

**Why integrate with Anthropic?**
1. **Store conversation history**: Keep track of user interactions
2. **Vector embeddings**: Use pgvector for semantic search
3. **User management**: Track which user called the API
4. **Rate limiting**: Implement per-user API limits
5. **Audit logging**: Track all API calls

### 12.2 Setting Up Supabase

**Step 1**: Create a Supabase project at https://supabase.com

**Step 2**: Create a table for conversation history:

```sql
CREATE TABLE conversations (
  id BIGSERIAL PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES auth.users(id),
  messages JSONB NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  model VARCHAR(50),
  total_tokens INT,
  cost_cents INT  -- Store cost in cents
);

CREATE INDEX idx_conversations_user_id ON conversations(user_id);
CREATE INDEX idx_conversations_created_at ON conversations(created_at);
```

**Step 3**: Create embeddings table:

```sql
CREATE TABLE embeddings (
  id BIGSERIAL PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES auth.users(id),
  content TEXT NOT NULL,
  embedding vector(1536),  -- 1536 dimensions for Anthropic embeddings
  metadata JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_embeddings_user_id ON embeddings(user_id);
CREATE INDEX idx_embeddings_embedding ON embeddings USING ivfflat(embedding vector_cosine_ops);
```

### 12.3 Python Code for Supabase Integration

```python
import anthropic
from supabase import create_client, Client
from datetime import datetime
import json

# Initialize clients
supabase: Client = create_client(
    "https://your-project.supabase.co",
    "your-anon-key"
)
client = anthropic.Anthropic(api_key="sk-ant-...")

def store_conversation(user_id: str, messages: list, response: str, model: str):
    """Store conversation in Supabase"""
    
    conversation_data = {
        "user_id": user_id,
        "messages": messages,
        "model": model,
        "created_at": datetime.utcnow().isoformat(),
        "updated_at": datetime.utcnow().isoformat()
    }
    
    result = supabase.table("conversations").insert(conversation_data).execute()
    return result

def get_conversation_history(user_id: str, limit: int = 10):
    """Retrieve conversation history for a user"""
    
    data = supabase.table("conversations")\
        .select("messages, model, created_at")\
        .eq("user_id", user_id)\
        .order("created_at", desc=True)\
        .limit(limit)\
        .execute()
    
    return data.data

def chat_with_memory(user_id: str, new_message: str):
    """Chat with conversation history loaded from Supabase"""
    
    # Get recent conversation history
    history = get_conversation_history(user_id, limit=5)
    
    # Build messages array
    messages = []
    for conv in reversed(history):
        messages.extend(conv["messages"])
    
    # Add new message
    messages.append({
        "role": "user",
        "content": new_message
    })
    
    # Call Anthropic
    response = client.messages.create(
        model="claude-opus-4-20250514",
        max_tokens=2048,
        messages=messages
    )
    
    # Store in Supabase
    store_conversation(
        user_id=user_id,
        messages=messages,
        response=response.content[0].text,
        model="claude-opus-4-20250514"
    )
    
    return response.content[0].text

# Usage
response = chat_with_memory(
    user_id="user123",
    new_message="What did I ask you last time?"
)
print(response)
```

### 12.4 Supabase Edge Functions

Create a Supabase Edge Function that calls Anthropic:

```typescript
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";

const anthropicApiKey = Deno.env.get("ANTHROPIC_API_KEY");

serve(async (req) => {
  if (req.method !== "POST") return new Response("Method not allowed", { status: 405 });

  const { message, user_id } = await req.json();

  // Call Anthropic API
  const response = await fetch("https://api.anthropic.com/v1/messages", {
    method: "POST",
    headers: {
      "x-api-key": anthropicApiKey,
      "content-type": "application/json",
      "anthropic-version": "2023-06-01",
    },
    body: JSON.stringify({
      model: "claude-opus-4-20250514",
      max_tokens: 1024,
      messages: [{ role: "user", content: message }],
    }),
  });

  const data = await response.json();

  return new Response(
    JSON.stringify({
      response: data.content[0].text,
      user_id: user_id,
      timestamp: new Date().toISOString(),
    }),
    { headers: { "Content-Type": "application/json" } }
  );
});
```

Deploy:
```bash
supabase functions deploy anthropic-chat
```

---

## Section 13: Prompt Engineering with AI APIs

### 13.1 Principles of Effective Prompts

**1. Be specific and clear**

Bad:
```
Summarize this article.
```

Good:
```
Summarize this article in 3 bullet points, focusing on business implications and cost savings.
```

**2. Provide context and examples**

Bad:
```
Classify the sentiment.
```

Good:
```
Classify the sentiment of customer reviews as positive, negative, or neutral. Here are examples:
- "Great product, highly recommend!" → positive
- "Terrible quality, wouldn't buy again" → negative
- "It's okay, nothing special" → neutral

Now classify: "The delivery was fast but the product was damaged"
```

**3. Use structured output formats**

Bad:
```
Extract the key information.
```

Good:
```
Extract the following information from the contract and return as JSON:
{
  "company_name": "...",
  "contract_duration": "...",
  "payment_terms": "...",
  "renewal_date": "...."
}
```

**4. Use role-playing for better outputs**

Bad:
```
Write a product description.
```

Good:
```
You are an experienced marketing copywriter for a luxury brand. Write a compelling product description for our new collection that emphasizes quality, craftsmanship, and sustainability. Use vivid imagery and emotional language.
```

### 13.2 Prompt Templates for Common Tasks

**Customer Support Response**:
```
You are a customer support specialist for [COMPANY]. A customer has written the following message:

[CUSTOMER_MESSAGE]

Respond with empathy and professionalism. Offer a solution or escalation path if needed. Keep the response under 150 words.
```

**Content Moderation**:
```
Review the following content for policy violations:
- Hate speech or harassment
- Adult content
- Spam or misleading information
- Violent or harmful content

Content to review: [USER_GENERATED_CONTENT]

Respond with ONLY: "APPROVED" or "REJECTED: [reason]"
```

**Data Extraction**:
```
Extract the following fields from the document below and return as JSON:
1. Invoice number
2. Invoice date
3. Vendor name
4. Total amount
5. Due date

Document:
[DOCUMENT_TEXT]

Return ONLY valid JSON, no other text.
```

**Comparative Analysis**:
```
Compare the following two products across these dimensions:
- Price
- Features
- Quality
- Customer reviews
- Value for money

Product A: [PRODUCT_A_DESCRIPTION]
Product B: [PRODUCT_B_DESCRIPTION]

Provide a structured analysis and recommend one product with justification.
```

### 13.3 Chain-of-Thought Prompting

For complex reasoning tasks, ask Claude to "think step by step":

```python
def solve_with_reasoning(problem: str):
    response = client.messages.create(
        model="claude-opus-4-20250514",
        max_tokens=2048,
        messages=[{
            "role": "user",
            "content": f"""Solve this problem step by step:

{problem}

First, break down the problem. Then, work through each step. Finally, provide the answer."""
        }]
    )
    return response.content[0].text
```

### 13.4 Few-Shot Prompting

Provide examples before asking for the actual task:

```python
def classify_with_examples(text: str):
    response = client.messages.create(
        model="claude-opus-4-20250514",
        max_tokens=256,
        messages=[{
            "role": "user",
            "content": f"""Classify the sentiment. Here are examples:

Example 1: "I love this product!" → Positive
Example 2: "Worst purchase ever" → Negative
Example 3: "It's alright" → Neutral

Now classify: "{text}"""
        }]
    )
    return response.content[0].text
```

### 13.5 System Prompts

Use the `system` parameter to define Claude's behavior:

```python
response = client.messages.create(
    model="claude-opus-4-20250514",
    max_tokens=1024,
    system="""You are a helpful assistant for an Italian e-commerce company called Vendiamonoi. 
You specialize in:
- Product recommendations
- Pricing and discounts
- Shipping and returns
- Italian market expertise

Always respond in Italian unless requested otherwise.
Be friendly, professional, and solution-focused.""",
    messages=[{
        "role": "user",
        "content": "Quale prodotto mi consigliate per..."
    }]
)
```

### 13.6 Avoiding Common Pitfalls

**Pitfall 1: Ambiguous instructions**
```python
# Bad
"Summarize this"

# Good
"Summarize this article in 3 bullet points, max 10 words each, focusing on business impact"
```

**Pitfall 2: Forgetting context**
```python
# Bad
messages = [{"role": "user", "content": "What's the answer?"}]

# Good
messages = [
    {"role": "user", "content": "Here's the background: [CONTEXT]"},
    {"role": "assistant", "content": "I understand. [SUMMARY]"},
    {"role": "user", "content": "Now, what's the answer?"}
]
```

**Pitfall 3: Asking for too much**
```python
# Bad - trying to do everything at once
"Extract data, classify sentiment, summarize, and predict next action"

# Good - break into steps
step1 = extract_data(text)
step2 = classify_sentiment(text)
step3 = summarize(text)
step4 = predict_action(step1, step2, step3)
```

---

## Section 14: Advanced Integration Patterns

### 14.1 Multi-Model Orchestration

Combine Anthropic with other AI models:

```python
import anthropic
import openai

anthropc = anthropic.Anthropic(api_key="sk-ant-...")
openai.api_key = "sk-..."

def analyze_with_multiple_models(text: str):
    """Get perspectives from multiple AI models"""
    
    # Anthropic analysis
    anthropic_response = anthropc.messages.create(
        model="claude-opus-4-20250514",
        max_tokens=1024,
        messages=[{"role": "user", "content": f"Analyze: {text}"}]
    )
    
    # OpenAI analysis
    openai_response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[{"role": "user", "content": f"Analyze: {text}"}]
    )
    
    # Compare perspectives
    comparison = anthropc.messages.create(
        model="claude-opus-4-20250514",
        max_tokens=1024,
        messages=[{
            "role": "user",
            "content": f"""Compare these two analyses:
            
Anthropic: {anthropic_response.content[0].text}

OpenAI: {openai_response['choices'][0]['message']['content']}

Which is more accurate and why?"""
        }]
    )
    
    return {
        "anthropic": anthropic_response.content[0].text,
        "openai": openai_response['choices'][0]['message']['content'],
        "comparison": comparison.content[0].text
    }
```

### 14.2 Function Calling Workflows

Orchestate multi-step workflows using function calling:

```python
import anthropic
import json

def execute_workflow(user_request: str):
    client = anthropic.Anthropic(api_key="sk-ant-...")
    
    tools = [
        {
            "name": "search_products",
            "description": "Search for products in the catalog",
            "input_schema": {
                "type": "object",
                "properties": {
                    "query": {"type": "string"},
                    "category": {"type": "string"}
                }
            }
        },
        {
            "name": "get_price",
            "description": "Get the price of a product",
            "input_schema": {
                "type": "object",
                "properties": {
                    "product_id": {"type": "string"}
                }
            }
        },
        {
            "name": "add_to_cart",
            "description": "Add a product to the user's cart",
            "input_schema": {
                "type": "object",
                "properties": {
                    "product_id": {"type": "string"},
                    "quantity": {"type": "integer"}
                }
            }
        }
    ]
    
    messages = [{"role": "user", "content": user_request}]
    
    while True:
        response = client.messages.create(
            model="claude-opus-4-20250514",
            max_tokens=1024,
            tools=tools,
            messages=messages
        )
        
        # Check if Claude wants to use a tool
        if response.stop_reason == "tool_use":
            # Find the tool use block
            tool_use = next(b for b in response.content if b.type == "tool_use")
            
            # Execute the tool (pseudo-code)
            if tool_use.name == "search_products":
                result = search_products(tool_use.input["query"])
            elif tool_use.name == "get_price":
                result = get_price(tool_use.input["product_id"])
            elif tool_use.name == "add_to_cart":
                result = add_to_cart(tool_use.input["product_id"])
            
            # Add assistant response and tool result to messages
            messages.append({"role": "assistant", "content": response.content})
            messages.append({
                "role": "user",
                "content": [{
                    "type": "tool_result",
                    "tool_use_id": tool_use.id,
                    "content": json.dumps(result)
                }]
            })
        else:
            # Claude has finished, return final response
            return response.content[0].text
```

### 14.3 RAG (Retrieval Augmented Generation)

Combine Claude with vector search:

```python
import anthropic
from sentence_transformers import SentenceTransformer
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

class RAGSystem:
    def __init__(self, api_key: str):
        self.client = anthropic.Anthropic(api_key=api_key)
        self.embedder = SentenceTransformer('all-MiniLM-L6-v2')
        self.documents = []
        self.embeddings = []
    
    def add_document(self, doc: str):
        """Add a document to the knowledge base"""
        embedding = self.embedder.encode([doc])[0]
        self.documents.append(doc)
        self.embeddings.append(embedding)
    
    def retrieve(self, query: str, top_k: int = 3) -> list:
        """Retrieve relevant documents for a query"""
        query_embedding = self.embedder.encode([query])[0]
        
        # Calculate similarity
        similarities = cosine_similarity(
            [query_embedding],
            self.embeddings
        )[0]
        
        # Get top k
        top_indices = np.argsort(similarities)[-top_k:][::-1]
        return [self.documents[i] for i in top_indices]
    
    def answer(self, question: str) -> str:
        """Answer a question using retrieved documents"""
        documents = self.retrieve(question)
        context = "\n\n".join(documents)
        
        response = self.client.messages.create(
            model="claude-opus-4-20250514",
            max_tokens=1024,
            messages=[{
                "role": "user",
                "content": f"""Based on the following documents:

{context}

Answer this question: {question}

If the answer is not in the documents, say so."""
            }]
        )
        
        return response.content[0].text

# Usage
rag = RAGSystem(api_key="sk-ant-...")
rag.add_document("Vendiamonoi is an Italian e-commerce company...")
rag.add_document("Our return policy is 30 days for most items...")
print(rag.answer("What is Vendiamonoi's return policy?"))
```

---

## Section 15: Monitoring and Observability

### 15.1 Token Tracking

```python
def track_tokens(response):
    """Track token usage and costs"""
    input_tokens = response.usage.input_tokens
    output_tokens = response.usage.output_tokens
    
    # Costs (as of early 2025)
    input_cost = input_tokens * (3 / 1000000)  # $3 per M input tokens
    output_cost = output_tokens * (15 / 1000000)  # $15 per M output tokens
    total_cost = input_cost + output_cost
    
    return {
        "input_tokens": input_tokens,
        "output_tokens": output_tokens,
        "total_tokens": input_tokens + output_tokens,
        "input_cost_usd": round(input_cost, 6),
        "output_cost_usd": round(output_cost, 6),
        "total_cost_usd": round(total_cost, 6)
    }

# Usage
response = client.messages.create(
    model="claude-opus-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Hello"}]
)
metrics = track_tokens(response)
print(f"Cost: ${metrics['total_cost_usd']:.6f}")
```

### 15.2 Logging and Debugging

```python
import logging
import json
from datetime import datetime

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def log_api_call(user_id: str, prompt: str, response: str, tokens: dict):
    """Log API call for debugging and auditing"""
    log_entry = {
        "timestamp": datetime.utcnow().isoformat(),
        "user_id": user_id,
        "prompt_length": len(prompt),
        "response_length": len(response),
        "tokens": tokens,
        "cost": tokens["total_cost_usd"]
    }
    logger.info(json.dumps(log_entry))
    
    # Optional: Send to monitoring service
    # send_to_datadog(log_entry)
```

### 15.3 Error Handling and Retries

```python
import time
from anthropic import APIError, RateLimitError

def call_with_retry(messages: list, max_retries: int = 3):
    """Call Claude API with exponential backoff"""
    
    for attempt in range(max_retries):
        try:
            response = client.messages.create(
                model="claude-opus-4-20250514",
                max_tokens=1024,
                messages=messages
            )
            return response
        
        except RateLimitError:
            if attempt < max_retries - 1:
                wait_time = 2 ** attempt  # 1s, 2s, 4s
                logger.warning(f"Rate limited, waiting {wait_time}s before retry")
                time.sleep(wait_time)
            else:
                raise
        
        except APIError as e:
            if e.status_code == 500 and attempt < max_retries - 1:
                logger.warning(f"Server error, retrying attempt {attempt + 1}")
                time.sleep(2 ** attempt)
            else:
                raise
    
    raise RuntimeError("Failed after max retries")
```

### 15.4 Performance Metrics

```python
import time
from statistics import mean, stdev

class PerformanceTracker:
    def __init__(self):
        self.latencies = []
        self.token_counts = []
        self.costs = []
    
    def track(self, response, duration: float):
        """Track a single API call"""
        self.latencies.append(duration)
        self.token_counts.append(
            response.usage.input_tokens + response.usage.output_tokens
        )
        cost = (
            response.usage.input_tokens * (3 / 1000000) +
            response.usage.output_tokens * (15 / 1000000)
        )
        self.costs.append(cost)
    
    def get_metrics(self) -> dict:
        """Get summary metrics"""
        return {
            "total_calls": len(self.latencies),
            "avg_latency_ms": mean(self.latencies) * 1000 if self.latencies else 0,
            "p95_latency_ms": sorted(self.latencies)[int(len(self.latencies) * 0.95)] * 1000 if self.latencies else 0,
            "total_tokens": sum(self.token_counts),
            "avg_tokens_per_call": mean(self.token_counts) if self.token_counts else 0,
            "total_cost": sum(self.costs),
            "avg_cost_per_call": mean(self.costs) if self.costs else 0
        }

# Usage
tracker = PerformanceTracker()

for i in range(10):
    start = time.time()
    response = client.messages.create(
        model="claude-opus-4-20250514",
        max_tokens=1024,
        messages=[{"role": "user", "content": f"Task {i}"}]
    )
    duration = time.time() - start
    tracker.track(response, duration)

metrics = tracker.get_metrics()
print(f"Metrics: {metrics}")
```

---

## Section 16: Production Deployment

### 16.1 Environment Configuration

```python
import os
from dotenv import load_dotenv

load_dotenv()

CONFIG = {
    "anthropic_api_key": os.getenv("ANTHROPIC_API_KEY"),
    "environment": os.getenv("ENVIRONMENT", "development"),
    "log_level": os.getenv("LOG_LEVEL", "INFO"),
    "max_retries": int(os.getenv("MAX_RETRIES", "3")),
    "timeout_seconds": int(os.getenv("TIMEOUT_SECONDS", "60")),
    "rate_limit_per_minute": int(os.getenv("RATE_LIMIT_PER_MINUTE", "60")),
}

assert CONFIG["anthropic_api_key"], "ANTHROPIC_API_KEY not set"
```

### 16.2 Rate Limiting

```python
from collections import deque
import time
import threading

class RateLimiter:
    def __init__(self, max_per_minute: int):
        self.max_per_minute = max_per_minute
        self.requests = deque()
        self.lock = threading.Lock()
    
    def is_allowed(self) -> bool:
        """Check if request is allowed"""
        with self.lock:
            now = time.time()
            # Remove requests older than 1 minute
            while self.requests and self.requests[0] < now - 60:
                self.requests.popleft()
            
            if len(self.requests) < self.max_per_minute:
                self.requests.append(now)
                return True
            return False
    
    def wait_if_needed(self):
        """Wait until a request is allowed"""
        while not self.is_allowed():
            time.sleep(0.1)

# Usage
limiter = RateLimiter(max_per_minute=60)

def safe_call():
    limiter.wait_if_needed()
    return client.messages.create(
        model="claude-opus-4-20250514",
        max_tokens=1024,
        messages=[{"role": "user", "content": "Hello"}]
    )
```

### 16.3 Caching Responses

```python
import hashlib
import json
from functools import lru_cache

class ResponseCache:
    def __init__(self, ttl_seconds: int = 3600):
        self.cache = {}
        self.ttl = ttl_seconds
        self.timestamps = {}
    
    def get_key(self, messages: list) -> str:
        """Generate cache key from messages"""
        text = json.dumps(messages, sort_keys=True)
        return hashlib.sha256(text.encode()).hexdigest()
    
    def get(self, messages: list):
        """Get cached response if available"""
        key = self.get_key(messages)
        if key in self.cache:
            age = time.time() - self.timestamps[key]
            if age < self.ttl:
                return self.cache[key]
            else:
                del self.cache[key]
                del self.timestamps[key]
        return None
    
    def set(self, messages: list, response):
        """Cache a response"""
        key = self.get_key(messages)
        self.cache[key] = response
        self.timestamps[key] = time.time()

# Usage
cache = ResponseCache(ttl_seconds=3600)

def chat_with_cache(messages: list):
    # Check cache first
    cached = cache.get(messages)
    if cached:
        return cached
    
    # Call API
    response = client.messages.create(
        model="claude-opus-4-20250514",
        max_tokens=1024,
        messages=messages
    )
    
    # Store in cache
    cache.set(messages, response)
    return response
```

### 16.4 Health Checks

```python
from flask import Flask, jsonify
import time

app = Flask(__name__)

class HealthCheck:
    def __init__(self):
        self.last_api_call = time.time()
        self.api_healthy = True
        self.error_count = 0
    
    def check(self) -> dict:
        """Perform health check"""
        try:
            response = client.messages.create(
                model="claude-opus-4-20250514",
                max_tokens=10,
                messages=[{"role": "user", "content": "ok"}]
            )
            self.api_healthy = True
            self.error_count = 0
            self.last_api_call = time.time()
            return {"status": "healthy", "api": "ok"}
        except Exception as e:
            self.api_healthy = False
            self.error_count += 1
            return {"status": "unhealthy", "error": str(e)}

health = HealthCheck()

@app.route("/health")
def health_check():
    result = health.check()
    status_code = 200 if result["status"] == "healthy" else 503
    return jsonify(result), status_code
```

### 16.5 Docker Deployment

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PYTHONUNBUFFERED=1
ENV ENVIRONMENT=production

EXPOSE 8000

CMD ["gunicorn", "--workers=4", "--bind=0.0.0.0:8000", "app:app"]
```

```bash
# Build
docker build -t anthropic-api-server .

# Run
docker run -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
           -p 8000:8000 \
           anthropic-api-server
```

---

## Summary

This guide covered:

1. **Vision APIs** - Image and document analysis
2. **Batch Processing** - Cost-effective bulk operations
3. **SDKs** - Official Python and TypeScript libraries
4. **Make.com** - No-code automation platform
5. **Supabase** - Database and serverless functions
6. **Prompt Engineering** - Best practices for effective prompts
7. **Advanced Patterns** - RAG, multi-model orchestration, function calling
8. **Monitoring** - Logging, tracking, error handling
9. **Production** - Deployment, caching, health checks

For more information:
- **Anthropic Docs**: https://docs.anthropic.com
- **API Reference**: https://docs.anthropic.com/en/api/messages
- **GitHub Examples**: https://github.com/anthropics/anthropic-sdk-python