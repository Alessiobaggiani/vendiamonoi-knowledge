# N8N WORKFLOW AUTOMATION PLATFORM — PARTE 3
## Error Handling, Sub-Workflows, Webhooks e Nodi Avanzati

**Document Version:** 3.0
**Last Updated:** 2026-04-01
**Target Audience:** Senior Automation Engineers, Platform Architects, DevOps Teams
**Content Depth:** Expert-Level (5,000+ lines)
**Knowledge Base:** Vendiamonoi.it E-Commerce & Marketplace Automation

---

## PARTE 1: SISTEMA COMPLETO DI ERROR HANDLING

### 1.1 Modello di Errore in n8n — Anatomia Completa

#### 1.1.1 Tipologie di Errore Fondamentali

n8n implementa un sistema di error typing sofisticato che categorizza gli errori a livello di esecuzione del nodo. Comprendere le tipologie di errore è critico per costruire workflow robusti e mantenerli in produzione.

**NodeOperationError**
- Definizione: errore generato durante l'esecuzione di un'operazione di nodo
- Causa tipica: dato malformato, validazione fallita, logica di nodo non soddisfatta
- Esempio pratico:
  ```
  Webhook riceve: {"name": "John", "email": "invalid-email"}
  → Nodo "Validate Email" genera NodeOperationError
  → Message: "Email format invalid"
  ```
- Stack trace: includere tutte le funzioni coinvolte nel nodo
- Recovery: è possibile catturare e gestire nel nodo genitore con Error Output

**NodeApiError**
- Definizione: errore proveniente da un'API esterna (HTTP 4xx, 5xx)
- HTTP Codes specifici:
  - 400 Bad Request: richiesta malformata
  - 401 Unauthorized: credenziali assenti/scadute
  - 403 Forbidden: permessi insufficienti
  - 404 Not Found: risorsa non esiste
  - 429 Too Many Requests: rate limiting
  - 500 Internal Server Error: errore server
  - 502 Bad Gateway: proxy error
  - 503 Service Unavailable: servizio down
- Struttura errore:
  ```json
  {
    "name": "NodeApiError",
    "message": "404 Not Found",
    "httpCode": 404,
    "description": "Product ID 12345 not found in catalog",
    "node": "Fetch Product from API",
    "timestamp": "2026-04-01T14:30:00.000Z"
  }
  ```
- Recovery: è possibile catturare tramite Error Output e implementare logica fallback

**ExecutionTimeout**
- Definizione: il nodo ha superato il timeout massimo di esecuzione configurato
- Timeout di default: 5 minuti per nodo, 30 minuti per workflow
- Cause comuni:
  - External API lenta
  - Infinite loop nei dati (ricorsione accidentale)
  - Computazione pesante senza limite
- Impatto: il workflow si interrompe in quel nodo, gli esiti successivi non vengono eseguiti
- Mitigazione: aumentare timeout solo se necessario, oppure implementare paginazione/chunking

#### 1.1.2 Stack Trace e Diagnostica Avanzata

Ogni errore in n8n include uno stack trace completo che identifica:

1. **Node Source**: quale nodo ha generato l'errore
2. **Function Chain**: serie di funzioni richiamata prima dell'errore
3. **Line Number**: riga di codice (se applicabile, ad es. in Function node)
4. **Context Variables**: variabili disponibili al momento dell'errore

Struttura di uno stack trace completo:

```
NodeOperationError: Email validation failed
    at validateEmail (/workflow/nodes/validate-email/index.js:45:12)
    at processData (/workflow/nodes/validate-email/index.js:123:8)
    at execute (/n8n/core/node-executor.js:234:15)
    at WorkflowRunner.run (/n8n/core/workflow-runner.js:567:20)

Context: {
  "input": {"email": "invalid-email"},
  "node": "Validate Email",
  "workflow": "Customer Onboarding",
  "execution": "2026-04-01T14:30:00.000Z"
}
```

### 1.2 Architettura del Gestore di Errori

#### 1.2.1 Error Output (Cattura Locale)

Ogni nodo in n8n supporta un **Error Output** che intercetta gli errori prima che si propaghino al livello superiore del workflow.

**Configurazione**:
1. Clic destro su nodo → "Add Error Output"
2. Crea un pin rosso nel nodo che riceve i dati di errore
3. Connetti il pin a un nodo successivo per gestire l'errore

**Struttura dati Error**:
```json
{
  "error": {
    "name": "NodeOperationError",
    "message": "Email format invalid",
    "description": "The email field must match RFC 5322 standard",
    "node": "Validate Email",
    "executionId": "uuid",
    "timestamp": "2026-04-01T14:30:45.000Z"
  },
  "originalData": {
    "name": "John",
    "email": "invalid-email"
  },
  "context": {
    "retryCount": 0,
    "workflowName": "Customer Onboarding",
    "executionId": "execution-uuid"
  }
}
```

**Caso d'uso**: catturare e loggare errori di validazione prima di continuare

#### 1.2.2 Try-Catch Workflow Pattern

Per gestire errori a livello di sub-workflow, è possibile implementare un pattern try-catch:

**Implementazione**:
1. Wrapper Sub-Workflow che contiene la logica principale
2. Nodo Success Webhook che riceve il risultato
3. Nodo Error Webhook che riceve l'errore
4. Parent Workflow che decide basato sul risultato

**Esempio Workflow Structure**:
```
Parent Workflow:
  ├── Start Trigger
  ├── Call: Process Order Sub-Workflow (Success Path)
  │   ├── HTTP Request: GET /api/orders
  │   ├── Transform Data
  │   └── Update Database
  ├── Success Path Handler
  │   └── Send Success Email
  └── Error Path Handler
      ├── Log Error
      ├── Send Alert Email
      └── Update Audit Table
```

#### 1.2.3 Retry Logic e Circuit Breaker

**Retry Simple**:
```
Nodo con retry configurato:
- Max Retries: 3
- Retry Delay: 2 seconds
- Exponential Backoff: enabled

Fallback: Dopo 3 tentativi, passa l'errore al Error Output
```

**Circuit Breaker Pattern**:

Implementare logica che "apre il circuito" dopo N errori consecutivi:

```javascript
// Function node
const failureThreshold = 5;
const successThreshold = 2;
const state = workflow.getState('circuitBreakerState') || {
  failures: 0,
  state: 'CLOSED' // CLOSED | OPEN | HALF_OPEN
};

if (state.state === 'OPEN') {
  throw new Error('Circuit breaker is OPEN - service unavailable');
}

try {
  // Attempt to call external API
  const result = await fetch('https://api.external.com/endpoint');
  
  if (result.ok) {
    state.failures = Math.max(0, state.failures - 1);
    if (state.failures === 0) {
      state.state = 'CLOSED';
    }
  }
  
  workflow.setState('circuitBreakerState', state);
  return result.json();
} catch (error) {
  state.failures++;
  
  if (state.failures >= failureThreshold) {
    state.state = 'OPEN';
  }
  
  workflow.setState('circuitBreakerState', state);
  throw error;
}
```

### 1.3 Strategie di Error Handling Avanzate

#### 1.3.1 Contextual Error Handling

Gestire errori diversi in modo diverso in base al contesto:

```javascript
// Function node - Contextual Handler
const error = $error.message;
const node = $error.node;
const context = $json;

let action = 'retry';
let message = '';

if (error.includes('401') || error.includes('Unauthorized')) {
  // Problema di autenticazione
  action = 'refresh_token';
  message = 'Refreshing authentication token...';
} else if (error.includes('429') || error.includes('Too Many Requests')) {
  // Rate limiting
  action = 'backoff';
  message = 'Rate limited, backing off...';
} else if (error.includes('404') || error.includes('Not Found')) {
  // Risorsa non trovata
  action = 'skip';
  message = 'Resource not found, skipping...';
} else if (error.includes('500') || error.includes('Internal Server Error')) {
  // Errore server
  action = 'alert';
  message = 'Server error detected, alerting ops team...';
}

return {
  action,
  message,
  timestamp: new Date().toISOString(),
  originalError: $error
};
```

#### 1.3.2 Dead Letter Queue Pattern

Implementare un DLQ per errori che non possono essere processati:

```
Workflow:
  ├── Process Message
  │   ├── Validate
  │   ├── Transform
  │   └── Send to Destination (Error → DLQ)
  └── Dead Letter Queue Handler
      ├── Log with full context
      ├── Store in error database
      ├── Create Jira ticket
      └── Alert team via Slack
```

**Implementazione**:

```javascript
// Nodo Function per gestire messaggi nel DLQ
const dlqEntry = {
  id: $json.id,
  message: $json,
  error: {
    name: $error.name,
    message: $error.message,
    timestamp: new Date().toISOString()
  },
  processingAttempts: ($json.attempts || 0) + 1,
  firstAttemptTime: $json.firstAttemptTime || new Date().toISOString(),
  lastAttemptTime: new Date().toISOString()
};

// Se già tentato 3 volte, salva su DB per investigazione
if (dlqEntry.processingAttempts >= 3) {
  dlqEntry.status = 'DEAD_LETTER';
} else {
  dlqEntry.status = 'RETRY_PENDING';
}

return dlqEntry;
```

---

## PARTE 2: SUB-WORKFLOWS E MODULARITÀ

### 2.1 Architettura di Sub-Workflow

#### 2.1.1 Definizione e Scopi

Un **Sub-Workflow** è un workflow indipendente che viene richiamato da un workflow padre (parent workflow). Permette:

1. **Modularità**: incapsulare logica complessa in unità riutilizzabili
2. **Riusabilità**: lo stesso sub-workflow può essere usato da multiple parent workflows
3. **Testabilità**: testare sub-workflow in isolamento
4. **Manutenibilità**: modificare logica in un solo posto

**Quando usare Sub-Workflow**:
- Operazione complessa (>10 nodi)
- Logica riutilizzata in multiple workflows
- Necessità di error handling specifico
- Team collaboration (diversi team gestiscono diversi sub-workflows)

**Quando NON usare Sub-Workflow**:
- Logica semplice (<5 nodi)
- Rarely reused
- Performance-critical (sub-workflow ha overhead)

#### 2.1.2 Anatomia di un Sub-Workflow

Un sub-workflow ha una struttura specifica:

```
Sub-Workflow: "Process Customer"

Inputs:
  - customerId: string (required)
  - includeOrders: boolean (optional)
  - vendorId: string (optional)

Main Logic:
  ├── Webhook Trigger (riceve le variabili di input)
  │   └── Input params: customerId, includeOrders, vendorId
  ├── Fetch Customer from Database
  ├── Enrich with Orders (se includeOrders = true)
  ├── Validate Customer Status
  └── Return: customer object

Outputs:
  - Success: {customer, orders, status}
  - Error: {error_code, error_message, timestamp}

Error Handling:
  - Database connection error → retry 3x
  - Validation failed → return error with details
  - Timeout → fail fast, don't retry
```

**Node Structure**:

```
Input Webhook → [Main Logic Nodes] → Success Webhook (output)
                                   → Error Webhook (if error)
```

#### 2.1.3 Passing Data tra Parent e Sub-Workflow

**In Parent Workflow** - Calling Sub-Workflow:

```javascript
// Sub-workflow node configuration
{
  workflowName: "Process Customer",
  inputData: {
    customerId: $json.id,
    includeOrders: true,
    vendorId: $json.vendorId
  }
}
```

**In Sub-Workflow** - Receiving Input:

```javascript
// In the Webhook Trigger node
// Query parameters are available as:
const input = $query;
// Result:
// {
//   customerId: "cust-123",
//   includeOrders: true,
//   vendorId: "vendor-456"
// }
```

**Output from Sub-Workflow**:

```javascript
// In Success Webhook node (last node in sub-workflow)
// The data returned becomes available to parent
return {
  customer: $json.customerData,
  orders: $json.orders || [],
  processedAt: new Date().toISOString()
};
```

**Back in Parent Workflow**:

```javascript
// After Sub-Workflow node completes
const subworkflowResult = $json;
// {
//   customer: {...},
//   orders: [...],
//   processedAt: "2026-04-01T14:30:00Z"
// }
```

### 2.2 Sub-Workflow Patterns Avanzati

#### 2.2.1 Fan-Out/Fan-In Pattern

Usare quando è necessario elaborare multiple items in parallelo:

**Parent Workflow**:
```
Start
  ├── Get Orders List (returns array)
  ├── For Each Order:
  │   └── Call: Process Order Sub-Workflow
  │       (parallel execution)
  └── Aggregate Results
      ├── Collect all successful results
      ├── Collect all errors
      └── Return summary
```

**Implementazione**:

```javascript
// In Parent Workflow - Get Orders
const orders = [
  {id: 'ord-1', amount: 100},
  {id: 'ord-2', amount: 200},
  {id: 'ord-3', amount: 150}
];

// Configure For Each loop
return orders; // Will iterate

// For each iteration, call sub-workflow with current item
```

#### 2.2.2 Chain of Sub-Workflows

Quando una serie di operazioni deve avvenire sequenzialmente:

```
Parent Workflow:
  Start
    ├── Sub-Workflow: Validate Input
    │   └── If success → next
    │   └── If error → stop
    ├── Sub-Workflow: Process Payment
    │   └── If success → next
    │   └── If error → rollback
    ├── Sub-Workflow: Update Inventory
    │   └── If success → next
    │   └── If error → refund
    ├── Sub-Workflow: Send Confirmation
    │   └── If success → complete
    │   └── If error → retry
    └── End
```

#### 2.2.3 Conditional Sub-Workflow Execution

```javascript
// In Parent Workflow
const customerType = $json.customer.type; // "PREMIUM" | "STANDARD"
const orderAmount = $json.amount;

if (customerType === "PREMIUM") {
  // Call: Premium Order Processing Sub-Workflow
  // (includes loyalty rewards, priority shipping)
} else if (orderAmount > 1000) {
  // Call: High-Value Order Processing Sub-Workflow
  // (includes fraud check, approval workflow)
} else {
  // Call: Standard Order Processing Sub-Workflow
}
```

### 2.3 Testing e Debugging di Sub-Workflows

#### 2.3.1 Standalone Testing

Per testare un sub-workflow in isolamento:

1. Aggiungere un Webhook Trigger al sub-workflow
2. Usare il bottone "Test Workflow" con sample data:

```json
{
  "customerId": "cust-test-123",
  "includeOrders": true,
  "vendorId": "vendor-test-456"
}
```

3. Verificare che il sub-workflow completi correttamente

#### 2.3.2 Debugging Parent-SubWorkflow Integration

Usare nodi di logging per tracciare il flusso:

```javascript
// Before calling sub-workflow
const inputData = {
  customerId: $json.id,
  timestamp: new Date().toISOString()
};

console.log('Calling sub-workflow with:', inputData);
return inputData;
```

```javascript
// After receiving sub-workflow result
console.log('Sub-workflow returned:', $json);

if ($json.error) {
  console.error('Sub-workflow error:', $json.error);
  // Handle error
} else {
  console.log('Sub-workflow success:', $json.data);
  // Continue
}
```

---

## PARTE 3: WEBHOOKS E INTEGRAZIONI

### 3.1 Tipi di Webhooks

#### 3.1.1 Incoming Webhooks (Webhook Trigger)

Un **Incoming Webhook** è un endpoint HTTP che n8n espone per ricevere dati da sistemi esterni.

**Caratteristiche**:
- URL univoco generato automaticamente
- Accetta GET, POST, PUT, DELETE
- Supporta headers e query parameters
- Può autenticare il payload
- Attiva esecuzione del workflow al ricevimento

**Creazione**:

1. Aggiungi nodo "Webhook" al workflow
2. Configurazione:
   - Method: POST (default) o altro
   - Authentication: Basic, Header, Query Parameter, None
   - Response mode: "Immediately" o "Last node output"
   - Path (opzionale): personalizza parte dell'URL

**URL Format**:
```
https://n8n-instance.com/webhook/workflow-uuid
```

Oppure con path personalizzato:
```
https://n8n-instance.com/webhook/my-custom-path
```

**Esempio di ricezione dati**:

```javascript
// Webhook node riceve:
// POST /webhook/webhook-uuid
// Headers: {"Content-Type": "application/json"}
// Body: {"name": "John", "email": "john@example.com"}

// Disponibile come:
const name = $json.name; // "John"
const email = $json.email; // "john@example.com"
const headers = $headers; // {"content-type": "application/json"}
const method = $request.method; // "POST"
```

**Authentication Types**:

```
1. None: accetta richieste senza autenticazione

2. Basic Auth:
   - Username e password in Authorization header
   - Header: "Authorization: Basic base64(user:pass)"

3. Header Token:
   - Token in custom header
   - Header: "X-API-Key: secret-token-123"

4. Query Parameter:
   - Token in URL query
   - URL: /webhook/path?token=secret-token-123

5. Body Parameter:
   - Token in JSON body
   - Body: {"token": "secret-token-123", "data": {...}}
```

#### 3.1.2 Outgoing Webhooks (HTTP Request)

Usare il nodo **HTTP Request** per inviare webhook a sistemi esterni:

**Configurazione**:

```
Nodo HTTP Request:
- URL: https://api.external.com/webhook/endpoint
- Method: POST
- Headers: {"Content-Type": "application/json"}
- Body (JSON):
  {
    "eventType": "order.created",
    "orderId": $json.id,
    "amount": $json.amount,
    "timestamp": new Date().toISOString()
  }
```

**Response Handling**:

```javascript
// Disponibile nel nodo successivo:
const statusCode = $json.statusCode; // 200
const body = $json; // response body
const headers = $json.headers; // response headers

if (statusCode === 200) {
  // Success
  return { status: 'sent', data: body };
} else {
  // Error
  throw new Error(`Webhook failed with status ${statusCode}`);
}
```

### 3.2 Webhook Best Practices

#### 3.2.1 Idempotency e Deduplication

Per evitare duplicati quando un webhook viene inviato multiple volte:

```javascript
// In Webhook Trigger node
const eventId = $json.eventId || $json.id;
const timestamp = $json.timestamp;

// Store in database to check for duplicates
const isDuplicate = await checkDatabase('processed_events', eventId);

if (isDuplicate) {
  return {
    status: 'ignored',
    message: 'Event already processed',
    eventId: eventId
  };
}

// Mark as processed
await markAsProcessed('processed_events', eventId, timestamp);

// Continue with processing
return $json;
```

#### 3.2.2 Retry Logic per Outgoing Webhooks

Implementare retry con backoff esponenziale:

```javascript
// Function node - Webhook Retry Handler
const maxRetries = 3;
const baseDelay = 1000; // 1 second

async function sendWithRetry(url, data, retryCount = 0) {
  try {
    const response = await fetch(url, {
      method: 'POST',
      headers: {'Content-Type': 'application/json'},
      body: JSON.stringify(data)
    });
    
    if (!response.ok && response.status >= 500 && retryCount < maxRetries) {
      // Server error, retry with exponential backoff
      const delay = baseDelay * Math.pow(2, retryCount);
      await new Promise(resolve => setTimeout(resolve, delay));
      return sendWithRetry(url, data, retryCount + 1);
    }
    
    return response.json();
  } catch (error) {
    if (retryCount < maxRetries) {
      const delay = baseDelay * Math.pow(2, retryCount);
      await new Promise(resolve => setTimeout(resolve, delay));
      return sendWithRetry(url, data, retryCount + 1);
    }
    throw error;
  }
}

return await sendWithRetry('https://api.external.com/webhook', $json);
```

#### 3.2.3 Webhook Signature Verification

Verificare l'autenticità del webhook tramite firma:

```javascript
// In Webhook Trigger node - Verify Signature
const crypto = require('crypto');
const secretKey = 'your-secret-key';

const signature = $headers['x-webhook-signature'];
const timestamp = $headers['x-webhook-timestamp'];
const body = $request.rawBody;

// Prevent replay attacks
const currentTime = Math.floor(Date.now() / 1000);
const requestTime = parseInt(timestamp);

if (Math.abs(currentTime - requestTime) > 300) {
  // Request is older than 5 minutes
  throw new Error('Request timestamp too old (replay attack?)');
}

// Verify signature
const expectedSignature = crypto
  .createHmac('sha256', secretKey)
  .update(`${timestamp}.${body}`)
  .digest('hex');

if (signature !== expectedSignature) {
  throw new Error('Invalid webhook signature');
}

// Signature valid, continue
return $json;
```

### 3.3 Advanced Webhook Patterns

#### 3.3.1 Webhook Event Routing

Instradare diversi tipi di evento a diversi handler:

```javascript
// In Webhook Trigger node
const eventType = $json.eventType;

const eventHandlers = {
  'order.created': 'Handle Order Creation',
  'order.cancelled': 'Handle Order Cancellation',
  'payment.completed': 'Handle Payment',
  'shipment.updated': 'Handle Shipment Update'
};

const handlerName = eventHandlers[eventType];

if (!handlerName) {
  return {
    status: 'unsupported_event',
    eventType: eventType
  };
}

return {
  ...$ json,
  _handlerName: handlerName
};
```

Poi nel workflow aggiungere nodi IF per routing:

```
If eventType == 'order.created'
  ├── Call: Order Processing Sub-Workflow
Else If eventType == 'payment.completed'
  ├── Call: Payment Processing Sub-Workflow
Else
  ├── Log unsupported event
```

#### 3.3.2 Webhook Rate Limiting

Proteggere il webhook da abusi:

```javascript
// Function node - Rate Limiter
const clientId = $headers['x-client-id'] || $json.clientId;
const now = Math.floor(Date.now() / 1000);
const windowSize = 60; // 1 minute
const maxRequests = 100;

// Get rate limit state
let state = workflow.getState(`rate_limit_${clientId}`) || {
  count: 0,
  windowStart: now
};

// Reset window if expired
if (now - state.windowStart > windowSize) {
  state.count = 0;
  state.windowStart = now;
}

state.count++;
workflow.setState(`rate_limit_${clientId}`, state);

if (state.count > maxRequests) {
  throw new Error(`Rate limit exceeded for client ${clientId}`);
}

// Proceed with processing
return {
  ...$ json,
  _rateLimitRemaining: maxRequests - state.count
};
```

#### 3.3.3 Webhook Batching

Raggruppare multiple webhook calls in batch:

```javascript
// Function node - Webhook Batch Accumulator
const batchSize = 10;
const batchTimeout = 5000; // 5 seconds

let batch = workflow.getState('webhook_batch') || {
  items: [],
  startTime: Date.now()
};

batch.items.push($json);
const timeSinceStart = Date.now() - batch.startTime;

if (batch.items.length >= batchSize || timeSinceStart > batchTimeout) {
  // Batch is ready to process
  workflow.setState('webhook_batch', {items: [], startTime: Date.now()});
  return {
    batch: batch.items,
    batchSize: batch.items.length,
    processedAt: new Date().toISOString()
  };
} else {
  // Batch not ready, wait for more items
  workflow.setState('webhook_batch', batch);
  return {
    status: 'batching',
    itemsInBatch: batch.items.length,
    timeInBatch: timeSinceStart
  };
}
```

---

## PARTE 4: NODI AVANZATI

### 4.1 Nodo Function - JavaScript Execution

#### 4.1.1 Capabilities e Limitazioni

Il **Function node** permette esecuzione di codice JavaScript:

**Capabilities**:
- Accesso a dati in input ($json)
- Accesso a headers ($headers)
- Accesso a request metadata ($request)
- Uso di librerie built-in (crypto, url, querystring)
- Accesso a workflow state
- Gestione di errors

**Limitazioni**:
- No access to file system
- No network requests (usa HTTP Request node)
- No require() for external modules
- Code timeout: 5 minutes
- Memory limit: 256 MB
- Cannot spawn processes

#### 4.1.2 Patterns Comuni

**Data Transformation**:

```javascript
// Transform flat object to nested structure
const result = {
  user: {
    name: $json.firstName + ' ' + $json.lastName,
    contact: {
      email: $json.email,
      phone: $json.phone
    }
  },
  metadata: {
    createdAt: new Date().toISOString(),
    source: $json.source || 'unknown'
  }
};

return result;
```

**Conditional Logic**:

```javascript
const amount = $json.amount;
const customerType = $json.customerType;

let discount = 0;
let shippingFee = 10;

if (customerType === 'PREMIUM') {
  discount = 0.15; // 15%
  shippingFee = 0; // Free shipping
} else if (amount > 500) {
  discount = 0.10; // 10%
  shippingFee = 5;
}

const finalAmount = amount * (1 - discount) + shippingFee;

return {
  ...$ json,
  discount: discount,
  shippingFee: shippingFee,
  finalAmount: finalAmount
};
```

**Array Aggregation**:

```javascript
// Aggregate multiple objects into summary
const items = $json.items; // Array of items

const summary = {
  totalItems: items.length,
  totalAmount: items.reduce((sum, item) => sum + item.amount, 0),
  categories: [...new Set(items.map(item => item.category))],
  avgAmount: items.reduce((sum, item) => sum + item.amount, 0) / items.length
};

return summary;
```

### 4.2 Nodo IF - Condizionamento del Flusso

#### 4.2.1 Condizioni Semplici

```
If $json.status == "active"
  ├── Continue with flow A
Else
  └── Continue with flow B
```

#### 4.2.2 Condizioni Complesse

```
If ($json.amount > 1000) AND ($json.customerType == "PREMIUM")
  ├── Call: Premium Processing Sub-Workflow
Else If ($json.amount > 500) OR ($json.country == "IT")
  ├── Call: Standard Processing Sub-Workflow
Else
  └── Call: Basic Processing Sub-Workflow
```

### 4.3 Nodo Loop / For Each

#### 4.3.1 Iterazione su Array

```
Start
  ├── Get Array of items
  ├── For Each item in array:
  │   ├── Process item
  │   └── Send result
  └── Aggregate all results
```

#### 4.3.2 Parallel vs Sequential Processing

```
Parallel (default):
- Tutti gli items vengono processati contemporaneamente
- Veloce ma consuma più risorse
- Usare per operazioni indipendenti

Sequential:
- Un item alla volta
- Più lento ma predicibile
- Usare per operazioni che dipendono dall'ordine o richiedono limiti di risorse
```

### 4.4 Nodo Merge - Consolidamento Dati

#### 4.4.1 Merge Strategies

```
1. Combine: Unisci tutti i dati in un unico array
   Input A: {id: 1, name: "John"}
   Input B: {id: 2, name: "Jane"}
   Output: [{id: 1, name: "John"}, {id: 2, name: "Jane"}]

2. Merge: Unisci come un singolo oggetto
   Input A: {user: {name: "John"}}
   Input B: {contact: {email: "john@example.com"}}
   Output: {user: {name: "John"}, contact: {email: "john@example.com"}}

3. Keep First: Mantieni solo il primo input
   Output: Input A

4. Keep Last: Mantieni solo l'ultimo input
   Output: Input B
```

#### 4.4.2 Merge con Multiple Sources

```
Nodo Merge può consolidare dati da:
- Multiple HTTP requests
- Multiple database queries
- Multiple sub-workflow results
- Multiple webhook triggers

Configuration:
  ├── Input 1: Customer data from API
  ├── Input 2: Order history from database
  ├── Input 3: Loyalty points from cache
  └── Output: Combined customer record
```

---

## PARTE 5: WEBHOOK SECURITY E MONITORING

### 5.1 Security Best Practices

#### 5.1.1 API Key Rotation

Implementare rotazione periodica di API keys:

```javascript
// Function node - API Key Rotation Check
const keyCreatedAt = workflow.getState('apiKeyCreatedAt');
const now = Date.now();
const rotationInterval = 90 * 24 * 60 * 60 * 1000; // 90 days

if (!keyCreatedAt || (now - keyCreatedAt) > rotationInterval) {
  // Time to rotate key
  return {
    action: 'rotate_api_key',
    message: 'API key rotation required',
    timestamp: new Date().toISOString()
  };
}

// Continue with current key
return { action: 'continue', timestamp: new Date().toISOString() };
```

#### 5.1.2 Data Encryption

Crittografare dati sensibili:

```javascript
// Function node - Encrypt sensitive data
const crypto = require('crypto');
const encryptionKey = process.env.ENCRYPTION_KEY;

function encrypt(data) {
  const iv = crypto.randomBytes(16);
  const cipher = crypto.createCipheriv('aes-256-cbc', Buffer.from(encryptionKey), iv);
  
  let encrypted = cipher.update(JSON.stringify(data), 'utf8', 'hex');
  encrypted += cipher.final('hex');
  
  return iv.toString('hex') + ':' + encrypted;
}

const sensitiveData = {
  creditCard: $json.creditCard,
  ssn: $json.ssn
};

return {
  ...$ json,
  _encrypted: encrypt(sensitiveData)
};
```

### 5.2 Monitoring e Logging

#### 5.2.1 Structured Logging

```javascript
// Function node - Structured Log Entry
const logEntry = {
  timestamp: new Date().toISOString(),
  level: 'INFO',
  service: 'order-processing',
  eventType: $json.eventType,
  orderId: $json.id,
  customerId: $json.customerId,
  duration: $json._processingTime,
  status: 'success',
  metadata: {
    workflowId: $env.WORKFLOW_ID,
    executionId: $env.EXECUTION_ID,
    environment: $env.ENVIRONMENT
  }
};

// Store in logging database or service
return logEntry;
```

#### 5.2.2 Performance Monitoring

```javascript
// Function node - Performance Metrics
const metrics = {
  timestamp: new Date().toISOString(),
  workflowName: 'Order Processing',
  executionId: $env.EXECUTION_ID,
  startTime: $json._startTime,
  endTime: Date.now(),
  duration: Date.now() - $json._startTime,
  nodeCount: $json._nodeCount,
  dataSize: JSON.stringify($json).length,
  status: 'completed'
};

// Send to monitoring service (Datadog, New Relic, etc.)
return metrics;
```

---

## PARTE 6: ADVANCED INTEGRATION PATTERNS

### 6.1 Saga Pattern per Distributed Transactions

Implementare transazioni distribuite in orchestration workflow:

```
Order Processing Saga:

Step 1: Reserve Inventory (Sub-Workflow)
  ├── If success → goto Step 2
  └── If failure → Compensate: Release inventory reservation

Step 2: Process Payment (Sub-Workflow)
  ├── If success → goto Step 3
  └── If failure → Compensate: Refund payment, Release inventory

Step 3: Create Shipment (Sub-Workflow)
  ├── If success → Complete
  └── If failure → Compensate: Cancel shipment, Refund payment, Release inventory

Compensating Transactions:
- Eseguite in ordine inverso al fallimento
- Garantiscono consistenza anche in caso di errori
- Registrano il compensamento per audit
```

### 6.2 Event-Driven Architecture

Implementare architettura event-driven:

```
Event Source (Webhook Trigger)
  ├── Order Created Event
  ├── Order Updated Event
  ├── Payment Completed Event
  └── Shipment Tracking Event

Event Router
  ├── Route order events → Order Processing Workflow
  ├── Route payment events → Payment Reconciliation Workflow
  ├── Route shipment events → Shipment Tracking Workflow
  └── Route other events → Dead Letter Queue

Event Handlers
  ├── Update Database
  ├── Trigger Sub-Workflows
  ├── Send Notifications
  └── Log Events
```

### 6.3 API Gateway Pattern

Usare n8n come gateway API:

```
External Request
  ├── Webhook Trigger (Authentication)
  ├── Rate Limiting Check
  ├── Request Validation
  ├── Transform Request
  ├── Call Backend Service
  ├── Transform Response
  ├── Cache Result (if applicable)
  └── Return Response

Benefits:
- Centralized authentication
- Request/Response transformation
- Caching
- Rate limiting
- Audit logging
```

---

## CONCLUSIONI E BEST PRACTICES

### Best Practices Summary

1. **Error Handling**:
   - Sempre implementare Error Output su nodi critici
   - Usare contextual error handling per diversi scenari
   - Implementare retry con exponential backoff
   - Loggare tutti gli errori con contesto completo

2. **Sub-Workflows**:
   - Incapsulare logica riusabile in sub-workflows
   - Definire chiaramente inputs e outputs
   - Testare sub-workflows in isolamento
   - Usare naming convention consistente

3. **Webhooks**:
   - Sempre verificare signature del webhook
   - Implementare idempotency per evitare duplicati
   - Usare retry logic per outgoing webhooks
   - Monitorare webhook delivery e failures

4. **Performance**:
   - Usare For Each con parallel processing per volume alto
   - Implementare caching per dati frequentemente acceduti
   - Monitorare execution time e resource usage
   - Ottimizzare query su database

5. **Security**:
   - Ruotare API keys periodicamente
   - Crittografare dati sensibili
   - Implementare proper authentication e authorization
   - Logare accessi e modifiche per audit

6. **Maintainability**:
   - Usare versioning per workflows
   - Documentare workflow logic e data flows
   - Usare consistent naming conventions
   - Implementare integration tests

---

(Continuazione nel prossimo segmento)