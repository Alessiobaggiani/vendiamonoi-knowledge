# Pattern di Design API — Guida Tecnica Universale

**Versione:** 1.0
**Data:** Aprile 2026
**Audience:** CTO, Systems Architect, Senior Backend Engineers
**Lingua:** Italiano

---

## Introduzione

Ogni integrazione API affrontata da un architetto tecnico, indipendentemente dal provider specifico, si basa su principi universali che trascendono piattaforme, linguaggi e tecnologie. Questo documento articola i principi dogmatici — le verità sempre valide — che ogni sistema di integrazione API deve rispettare.

Non si tratta di "best practices consigliate": queste sono **regole fisiche dell'integrazione distribuita**. Violarle introduce fragilità sistematica, bug impercettibili e incidenti di produzione che nessuno riesce a diagnosticare fino a quando non è troppo tardi.

Una comprensione profonda di questi pattern protegge da:
- **Degradazione silente delle prestazioni** (il 95° percentile latenza che improvvisamente esplode)
- **Perdita dati silente** (transazioni che si perdono in retry logic difettosa)
- **Cascate di errori** (un timeout in un servizio causa timeout a catena in quelli dipendenti)
- **Utilizzo inefficiente di risorse** (rate limiting implementato male che causa costi 10x)

Questi principi sono universali perché derivano da **proprietà inevitabili di sistemi distribuiti**:
- La rete non è affidabile (fault assumptions)
- I timeout sono necessari (non esiste "attesa infinita")
- Lo stato distribuito è fragile (idempotency è obbligatoria)
- La scalabilità richiede limite di risorse (rate limiting è inviolabile)

---

## 1. REST API — Principi Fondamentali

### 1.1 Semantica dei Metodi HTTP

La semantica HTTP è **completamente universale**. Non esiste una piattaforma dove GET modifica lo stato o POST è idempotente per default.

#### GET — Sicurezza e Idempotenza Assolute

```
GET /users/123
Host: api.example.com

HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "Alice",
  "email": "alice@example.com"
}
```

**Proprietà immutabili:**
- **Safe:** Nessun side effect sul server
- **Idempotent:** GET identici producono sempre lo stesso risultato
- **Cacheable:** Caching HTTP standard applicabile
- **Nessun body nella request:** I parametri vanno in query string

```bash
# Corretto
curl "https://api.example.com/users?status=active&limit=10"

# Sbagliato (impossibile garantire idempotenza e caching)
curl -X POST "https://api.example.com/users" \
  -d '{"status": "active", "limit": 10}'
```

**Quando NON usare GET:**
- Operazioni che cambiano stato
- Query con payload complesso (usa POST con accept semantica as query)
- Filtri sensibili (credenziali nei query parameters si vedono nei log)

#### POST — Creazione e Non-Idempotenza

```
POST /orders
Content-Type: application/json

{
  "customer_id": 42,
  "items": [
    {"product_id": 1, "quantity": 2}
  ]
}

HTTP/1.1 201 Created
Location: /orders/9999
Content-Type: application/json

{
  "id": 9999,
  "status": "pending",
  "created_at": "2026-04-01T14:32:00Z"
}
```

**Proprietà immutabili:**
- **Non-safe:** Cambia stato sul server (creazione risorsa)
- **Non-idempotent:** POST identici possono creare risorse duplicate
- **Non-cacheable:** Ogni POST è unica per definizione

**Protezione da duplicati — Idempotency Keys:**

```bash
# First request
curl -X POST "https://api.example.com/orders" \
  -H "Idempotency-Key: order-user42-2026-04-01-14:32" \
  -d '{"customer_id": 42, "items": [...]}'

# Response
# HTTP 201 Created
# {
#   "id": 9999,
#   "status": "pending"
# }

# Same request (network failure, client retries)
curl -X POST "https://api.example.com/orders" \
  -H "Idempotency-Key: order-user42-2026-04-01-14:32" \
  -d '{"customer_id": 42, "items": [...]}'

# Server riconosce la stessa Idempotency-Key, ritorna lo stesso risultato
# HTTP 201 Created (o 409 Conflict se stato cambiato)
# {
#   "id": 9999,  # STESSO ordine, non duplicato
#   "status": "pending"
# }
```

**Implementazione server (Node.js/Express):**

```javascript
const idempotencyStore = new Map(); // In produzione: Redis

app.post('/orders', async (req, res) => {
  const idempotencyKey = req.headers['idempotency-key'];

  // Ritorna risultato cached se esiste
  if (idempotencyStore.has(idempotencyKey)) {
    const cached = idempotencyStore.get(idempotencyKey);
    if (cached.processing) {
      return res.status(409).json({ error: 'Conflict: still processing' });
    }
    return res.status(cached.statusCode).json(cached.response);
  }

  // Marca come processing
  idempotencyStore.set(idempotencyKey, { processing: true });

  try {
    const order = await createOrder(req.body);
    const response = {
      id: order.id,
      status: order.status,
      created_at: order.created_at
    };

    // Cache il risultato (ACID: atomico con la creazione dell'ordine)
    idempotencyStore.set(idempotencyKey, {
      statusCode: 201,
      response,
      processing: false
    });

    res.status(201).json(response);
  } catch (error) {
    idempotencyStore.delete(idempotencyKey); // Retry
    res.status(500).json({ error: error.message });
  }
});
```

#### PUT — Sostituzione Completa e Idempotenza

```
PUT /users/123
Content-Type: application/json

{
  "name": "Alice Smith",
  "email": "alice.smith@example.com",
  "age": 30
}

HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "Alice Smith",
  "email": "alice.smith@example.com",
  "age": 30
}
```

**Proprietà critiche:**
- **Idempotent:** Stesso PUT ripetuto = stesso risultato
- **Non-safe:** Cambia stato
- **Sostituizione completa:** Il client invia lo stato INTERO della risorsa
- **Require versioning:** Etag per conflict detection (optimistic locking)

**Problema: Race Condition senza versioning:**

```
Stato iniziale: { name: "Alice", email: "alice@example.com", age: 25 }

Client A:                          Client B:
GET /users/123                     GET /users/123
← { name: "Alice", email: "alice@example.com", age: 25 }
                                   ← { name: "Alice", email: "alice@example.com", age: 25 }

PUT /users/123 { age: 30 }         PUT /users/123 { email: "new@example.com" }
← { name: "Alice", email: "alice@example.com", age: 30 }
                                   ← { name: "Alice", email: "new@example.com", age: 25 }

Risultato finale: PERSO il cambio email di Client A!
```

**Soluzione — Optimistic Locking con ETag:**

```
# Client A: Legge la risorsa con ETag
GET /users/123
← HTTP 200
← ETag: "abc123"
← { name: "Alice", age: 25 }

# Client A: Tenta PUT con ETag
PUT /users/123
If-Match: "abc123"
{ name: "Alice", age: 30 }

# Se nel frattempo Client B ha modificato, il server rifiuta:
← HTTP 412 Precondition Failed

# Client A deve ricominciare da GET per ottenere il nuovo ETag
```

#### PATCH — Aggiornamento Parziale

```
PATCH /users/123
Content-Type: application/json-patch+json

[
  { "op": "replace", "path": "/age", "value": 31 },
  { "op": "add", "path": "/phone", "value": "+39123456789" }
]

HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "Alice",
  "age": 31,
  "phone": "+39123456789"
}
```

**Quando usare PATCH vs PUT:**

| Scenario | Metodo | Ragione |
|----------|--------|----------|
| Client ha solo alcuni campi da modificare | PATCH | Bandwidth, chiarezza intenti |
| Client ha lo stato intero e vuole sostituire | PUT | Atomicità, versioning semplice |
| Update su larga scala (bande larga occupata) | PATCH | Efficienza |
| Operazioni complesse (atomic multi-field) | PATCH (JSON Merge) | Controllo granulare |

#### DELETE — Rimozione Idempotente

```bash
# First attempt
curl -X DELETE "https://api.example.com/users/123"
# HTTP 204 No Content

# Retry (network failure)
curl -X DELETE "https://api.example.com/users/123"
# HTTP 204 No Content (IDEMPOTENT - stesso risultato)

# Terzo tentativo (risorsa già eliminata in precedenza)
curl -X DELETE "https://api.example.com/users/123"
# HTTP 204 No Content (non 404! DELETE è idempotent)
```

**Perché 204 per DELETE idempotente?**

DELETE è idempotente per definizione: eliminare una risorsa già eliminata deve avere lo stesso effetto (niente). Restituire 404 viola questa proprietà perché il primo DELETE → 204, il secondo → 404.

```javascript
app.delete('/users/:id', async (req, res) => {
  const user = await User.findById(req.params.id);

  // È idempotente: se non esiste, il risultato è "utente non esiste"
  if (user) {
    await user.deleteOne();
  }

  // Stesso codice in entrambi i casi
  res.status(204).send();
});
```

### 1.2 HTTP Status Codes — Semantica Universale

Non è una questione di preferenza: gli status code hanno significati definiti dalle RFC e obbligano il client a comportamenti specifici.

#### 2xx Success

| Codice | Semantica | Quando usare | Cache? |
|--------|-----------|-------------|--------|
| **200 OK** | Richiesta riuscita, body contiene il risultato | GET, POST (con body in risposta), PUT, PATCH | Sì (con Cache-Control) |
| **201 Created** | Risorsa creata, Location header obbligatorio | POST di creazione risorsa | No |
| **202 Accepted** | Richiesta accettata, elaborazione asincrona | Job scheduling, batch operations | No |
| **204 No Content** | Successo, nessun body | DELETE, PUT/PATCH senza cambio | Sì |
| **206 Partial Content** | Risposta incompleta, Range-based | Download con range headers | Conditional |

**Errore comune: 200 con errori nel body**

```javascript
// SBAGLIATO - Viola HTTP semantics
app.post('/transfer', async (req, res) => {
  try {
    const result = await transferMoney(req.body);
    res.status(200).json({
      success: false,
      error: "Insufficient balance"
    });
    // Il client vede 200 OK, non sa che la transazione è fallita
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// CORRETTO
app.post('/transfer', async (req, res) => {
  try {
    const result = await transferMoney(req.body);
    res.status(200).json({ // Solo se transazione riuscita
      transaction_id: result.id,
      amount: result.amount,
      timestamp: result.timestamp
    });
  } catch (error) {
    if (error.code === 'INSUFFICIENT_BALANCE') {
      res.status(400).json({ error: error.message }); // Client error
    } else if (error.code === 'ACCOUNT_FROZEN') {
      res.status(403).json({ error: error.message }); // Forbidden
    } else {
      res.status(500).json({ error: error.message }); // Server error
    }
  }
});
```

#### 3xx Redirection

| Codice | Semantica | Quando usare |
|--------|-----------|-------------|
| **301 Moved Permanently** | Risorsa spostata definitivamente | Cambio URL endpoint (announce prima) |
| **302 Found** | Reindirizzamento temporaneo | Rate limiting, geolocation routing |
| **304 Not Modified** | Cache still valid (nessun body) | Quando ETag/Last-Modified match |
| **307 Temporary Redirect** | Mantieni metodo HTTP | POST redirect (form resubmission) |

**Redirect corretto con Location header:**

```bash
HTTP/1.1 301 Moved Permanently
Location: https://api.example.com/v2/users/123
Content-Length: 0

# Client automaticamente segue il redirect a /v2/users/123
```

#### 4xx Client Error

| Codice | Semantica | Quando usare |
|--------|-----------|-------------|
| **400 Bad Request** | Payload malformato, valori invalidi | JSON non parsabile, valori fuori range |
| **401 Unauthorized** | Autenticazione mancante o invalida | Token scaduto, credenziali sbagliate |
| **403 Forbidden** | Autenticato ma non autorizzato | Utente non ha permesso per questa risorsa |
| **404 Not Found** | Risorsa non esiste | La risorsa cercata non esiste |
| **409 Conflict** | Conflitto di stato (race condition) | PUT con ETag non matching, Idempotency-Key already processing |
| **429 Too Many Requests** | Rate limited | Client ha ecceduto limit, deve aspettare |

**Distinzione critica: 401 vs 403**

```javascript
app.get('/users/:id', (req, res) => {
  const token = req.headers.authorization;

  // Token mancante o invalido → 401
  if (!token || !isValidToken(token)) {
    return res.status(401).json({
      error: 'Unauthorized',
      message: 'Token required or expired'
    });
  }

  const user = User.findById(req.params.id);
  const requesterId = getUserIdFromToken(token);

  // Utente autenticato ma non ha permesso → 403
  if (user.owner_id !== requesterId && !isAdmin(requesterId)) {
    return res.status(403).json({
      error: 'Forbidden',
      message: 'You do not have permission to view this user'
    });
  }

  res.json(user);
});
```

#### 5xx Server Error

| Codice | Semantica | Quando usare |
|--------|-----------|-------------|
| **500 Internal Server Error** | Errore generico server | Eccezione imprevista, bug |
| **502 Bad Gateway** | Downstream service non risponde | Servizio dipendente offline |
| **503 Service Unavailable** | Server temporaneamente non disponibile | Overloaded, maintenance |
| **504 Gateway Timeout** | Timeout da downstream | Servizio dipendente lento/offline |

**Criterio universale per 5xx:**
> Usa 5xx SOLO per errori causati dal server. Se il client ha fatto qualcosa di sbagliato, è 4xx.

### 1.3 Naming di Resources

Convenzione universale (usata da REST API ovunque):

```
Plurali, minuscole, no verbi:

CORRETTO:
GET /users                          # List
POST /users                         # Create
GET /users/123                      # Retrieve
PUT /users/123                      # Replace
PATCH /users/123                    # Partial update
DELETE /users/123                   # Delete

SBAGLIATO:
GET /getUsers                       # Verbi nei nomi (il metodo HTTP è il verbo)
POST /createUser                    # Ridondante
GET /user/123                       # Singolare (ambiguo)
DELETE /deleteUser/123              # Ridondante
POST /users/getOrders               # Mixed naming
```

Gerarchie profonde:

```
# Massimo 2 livelli di nesting profondità
GET /users/123/orders               # Ok - user ha molti orders
GET /users/123/orders/456           # Boundary - ok se orders è la risorsa
GET /users/123/orders/456/items     # STOP - troppi livelli

# Alternative per deep nesting
GET /orders?user_id=123             # Query param per filtering
GET /orders?user_id=123&id=456      # Filtraggio multiplo
GET /order-items?order_id=456       # Flatten la gerarchia
```

### 1.4 Content Negotiation

HTTP definisce come il client richiede specifici formati di risposta:

```bash
# Client richiede JSON
curl -H "Accept: application/json" https://api.example.com/users/123

# Response
HTTP/1.1 200 OK
Content-Type: application/json

{ "id": 123, "name": "Alice" }

---

# Client richiede XML
curl -H "Accept: application/xml" https://api.example.com/users/123

# Response
HTTP/1.1 200 OK
Content-Type: application/xml

<?xml version="1.0"?>
<user><id>123</id><name>Alice</name></user>

---

# Client richiede format non supportato
curl -H "Accept: application/protobuf" https://api.example.com/users/123

# Response
HTTP/1.1 406 Not Acceptable
Content-Type: application/json

{ "error": "Supported formats: application/json, application/xml" }
```

**Server DEVE rispettare Accept header**:

```javascript
app.get('/users/:id', (req, res) => {
  const accept = req.headers.accept;
  const user = User.findById(req.params.id);

  if (accept.includes('application/json')) {
    res.setHeader('Content-Type', 'application/json');
    res.json(user);
  } else if (accept.includes('application/xml')) {
    res.setHeader('Content-Type', 'application/xml');
    res.send(user.toXML());
  } else {
    res.status(406).json({
      error: 'Not Acceptable',
      supported: ['application/json', 'application/xml']
    });
  }
});
```

### 1.5 HATEOAS — Hypermedia As The Engine Of Application State

HATEOAS è principio universale che rende l'API **self-discoverable**:

```json
{
  "id": 123,
  "name": "Alice",
  "email": "alice@example.com",
  "_links": {
    "self": {
      "href": "https://api.example.com/users/123"
    },
    "orders": {
      "href": "https://api.example.com/users/123/orders"
    },
    "update": {
      "href": "https://api.example.com/users/123",
      "method": "PUT"
    },
    "delete": {
      "href": "https://api.example.com/users/123",
      "method": "DELETE"
    }
  }
}
```

Client NON ha bisogno di hardcodare URL:

```javascript
// Senza HATEOAS (client brittele a URL changes)
const userId = 123;
const ordersUrl = `https://api.example.com/users/${userId}/orders`;

// Con HATEOAS (client resiliente)
const userResponse = await fetch(`https://api.example.com/users/123`);
const user = await userResponse.json();
const ordersUrl = user._links.orders.href; // Evita hardcoding

const ordersResponse = await fetch(ordersUrl);
const orders = await ordersResponse.json();
```

### 1.6 Idempotenza per Metodo HTTP

**Proprietà matematica:**

```
Idempotent: f(f(x)) = f(x)  (applicare 2 volte = applicare 1 volta)
```

| Metodo | Idempotent | Reason |
|--------|------------|--------|
| GET | ✅ Sì | Non cambia lo stato |
| HEAD | ✅ Sì | Non cambia lo stato |
| OPTIONS | ✅ Sì | Non cambia lo stato |
| PUT | ✅ Sì | Sostituisce la risorsa completamente |
| DELETE | ✅ Sì | Eliminare 2 volte = eliminare 1 volta |
| POST | ❌ No | Crea risorse nuove (duplicati) |
| PATCH | ⚠️ Dipende | Depends su implementazione operazioni |

**PATCH idempotente vs non-idempotente:**

```javascript
// NON idempotente - incremento
PATCH /account/123
[{ "op": "add", "path": "/balance", "value": 100 }]
// Applicate 2 volte: balance aumenta di 200 ❌

// Idempotente - sostituzione
PATCH /account/123
[{ "op": "replace", "path": "/balance", "value": 5000 }]
// Applicate 2 volte: balance = 5000 (idempotent) ✅
```

---

## 2. Autenticazione e Autorizzazione

### 2.1 API Key Patterns

Più semplice di OAuth, adatto a machine-to-machine se non c'è user-delegation.

#### API Key in Header (Raccomandato)

```bash
curl -H "X-API-Key: sk_live_abc123def456" https://api.example.com/users
```

**Server-side validation:**

```javascript
app.use((req, res, next) => {
  const apiKey = req.headers['x-api-key'];

  if (!apiKey) {
    return res.status(401).json({ error: 'API key required' });
  }

  const apiKeyHash = hashApiKey(apiKey); // Maiusolo store hashed
  const account = Account.findByApiKeyHash(apiKeyHash);

  if (!account) {
    return res.status(401).json({ error: 'Invalid API key' });
  }

  req.account = account;
  req.accountId = account.id;
  next();
});
```

#### API Key in Query (Sconsigliato, Ma A Volte Necessario)

```bash
# Appare nei server logs, browser history — NON usare per credenziali critiche
curl "https://api.example.com/users?api_key=sk_live_abc123"
```

#### Scoping e Rotazione

**Scoping — principio least privilege:**

```javascript
// Chiavi con scope specifico
{
  "api_key": "sk_live_abc123",
  "scopes": ["users.read", "orders.read"], // NON posso creare/cancellare
  "created_at": "2026-04-01",
  "expires_at": "2026-04-08"
}

// Validazione di scope
app.delete('/users/:id', (req, res) => {
  if (!req.account.scopes.includes('users.write')) {
    return res.status(403).json({
      error: 'Insufficient permissions',
      required_scope: 'users.write'
    });
  }

  // Procedi con DELETE
});
```

**Rotazione di chiavi (critical per security):**

```
Vecchia chiave (expiration: 2026-04-15)
↓
Cliente genera nuova chiave
↓
Client usa nuova chiave, vecchia continua a funzionare per 14 giorni
↓
Vecchia chiave expira
↓
Attacker non ha accesso
```

### 2.2 OAuth 2.0 — Autenticazione Delegata

OAuth 2.0 è il **protocollo universale per delegazione di accesso**. Non è sinonimo di autenticazione — è **autorizzazione**.

#### Authorization Code Flow con PKCE (Per App Native/SPA)

```
┌─────────────┐                                ┌──────────────────┐
│   Client    │                                │  Authorization   │
│  (Your App) │                                │     Server       │
└──────┬──────┘                                └────────┬─────────┘
       │                                                │
       │ 1. Genera code_verifier, code_challenge       │
       │    (PKCE random secret)                       │
       │                                                │
       │ 2. Redirects user to /authorize               │
       │    + code_challenge                           │
       │    + redirect_uri                             │
       ├──────────────────────────────────────────────→│
       │                                                │
       │                             3. User logs in,   │
       │                                grants consent  │
       │                                                │
       │ ← 4. Authorization Code (short-lived)         │
       │      + redirect to redirect_uri?code=ABC      │
       │                                                │
       │ 5. Exchange code for token (backend)          │
       │    + code                                     │
       │    + code_verifier (prova che client è legit) │
       ├──────────────────────────────────────────────→│
       │                                                │
       │ ← 6. Access Token + Refresh Token             │
       │                                                │
```

**Perché PKCE è obbligatorio (anche per app "confidenziali")?**

PKCE protegge da **Authorization Code Interception Attack**: se un attacker intercetta il code tramite malware/network sniffer, non può scambiarlo per token senza il `code_verifier` che solo il client legittimo conosce.

```javascript
// Client-side implementation (JavaScript)

// 1. Generate PKCE challenge
function generateCodeVerifier() {
  const array = new Uint8Array(32);
  crypto.getRandomValues(array);
  return btoa(String.fromCharCode(...array)).replace(/\+/g, '-').replace(/\//g, '_').replace(/=/g, '');
}

const codeVerifier = generateCodeVerifier();
const codeChallenge = btoa(
  String.fromCharCode(...new Uint8Array(
    await crypto.subtle.digest('SHA-256', new TextEncoder().encode(codeVerifier))
  ))
).replace(/\+/g, '-').replace(/\//g, '_').replace(/=/g, '');

// 2. Redirect user to authorization endpoint
const authUrl = new URL('https://auth.example.com/authorize');
authUrl.searchParams.set('client_id', 'my_app_client_id');
authUrl.searchParams.set('response_type', 'code');
authUrl.searchParams.set('redirect_uri', 'https://myapp.com/callback');
authUrl.searchParams.set('scope', 'openid profile email');
authUrl.searchParams.set('state', generateRandomState()); // CSRF protection
authUrl.searchParams.set('code_challenge', codeChallenge);
authUrl.searchParams.set('code_challenge_method', 'S256'); // SHA-256

window.location.href = authUrl.toString();

// 3. In callback (ricevi authorization code)
const params = new URLSearchParams(window.location.search);
const code = params.get('code');
const state = params.get('state');

// Verify state matches (CSRF protection)
if (state !== sessionStorage.getItem('oauth_state')) {
  throw new Error('State mismatch - CSRF attack suspected');
}

// 4. Exchange code for token (backend)
fetch('https://api.example.com/token', {
  method: 'POST',
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
  body: new URLSearchParams({
    grant_type: 'authorization_code',
    code: code,
    client_id: 'my_app_client_id',
    client_secret: 'my_app_client_secret', // Secure backend only
    code_verifier: codeVerifier, // PKCE proof
    redirect_uri: 'https://myapp.com/callback'
  })
}).then(r => r.json())
  .then(tokens => {
    localStorage.setItem('access_token', tokens.access_token);
    localStorage.setItem('refresh_token', tokens.refresh_token);
  });
```

#### Client Credentials Flow (Machine-to-Machine)

```
┌─────────────────┐
│ Service A       │
│ (no user)       │
└────────┬────────┘
         │
         │ POST /token
         │ - client_id
         │ - client_secret
         │ - grant_type: client_credentials
         │ - scope: api.write
         │
         ├────────────────────────────────────→ ┌──────────────────┐
         │                                       │  Token Server    │
         │                                       │  Validates       │
         │ ← Access Token (no expiration)        │  credentials     │
         │   or short-lived (15 min)             │                  │
         ←                                       └──────────────────┘
         │
         │ Usa token: Authorization: Bearer TOKEN
         │
         ├────────────────────────────────────→ Service B API
```

```javascript
// Node.js client
const axios = require('axios');

async function getAccessToken() {
  const response = await axios.post('https://auth.example.com/token', {
    grant_type: 'client_credentials',
    client_id: process.env.CLIENT_ID,
    client_secret: process.env.CLIENT_SECRET,
    scope: 'api.write orders.read'
  });

  return response.data.access_token;
}

async function callDownstreamAPI() {
  const token = await getAccessToken();
  const response = await axios.get('https://api.example.com/orders', {
    headers: { 'Authorization': `Bearer ${token}` }
  });

  return response.data;
}
```

#### Refresh Token Lifecycle

```
┌─────────────────────────────────────────┐
│ Initial Authorization                   │
├─────────────────────────────────────────┤
│ Access Token: Valid per 15 minuti       │
│ Refresh Token: Valid per 30 giorni      │
└─────────────────────────────────────────┘
                    │
                    ├→ [15 min] Access Token scade
                    │
                    ├→ Client usa Refresh Token
                    │  per ottenere nuovo Access Token
                    │
                    └→ [30 giorni] Refresh Token scade
                       Client deve re-authenticate
```

**Refresh Token Rotation (Best Practice):**

```
Primo refresh:
POST /token
{ grant_type: "refresh_token", refresh_token: "old_refresh_token" }

Response:
{
  "access_token": "new_access_token",
  "refresh_token": "new_refresh_token",  // NUOVO refresh token!
  "expires_in": 900
}

// Server invalida il vecchio refresh_token
// Ogni utilizzo genera nuovo refresh_token
// Previene token reuse attacks
```

### 2.3 JWT (JSON Web Token) — Token Self-Contained

```
JWT = Header.Payload.Signature

eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkFsaWNlIn0.
signature_here

Decodificato:
{
  "alg": "HS256",  // Algorithm
  "typ": "JWT"     // Token type
}
.
{
  "sub": "1234567890",    // Subject (user ID)
  "name": "Alice",
  "iat": 1704067200,      // Issued at (Unix timestamp)
  "exp": 1704070800       // Expiration (15 minuti dopo iat)
}
.
HMACSSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

**Validazione JWT (Server-side):**

```javascript
const jwt = require('jsonwebtoken');

const JWT_SECRET = process.env.JWT_SECRET; // Never hardcode!

// Middleware
function verifyJWT(req, res, next) {
  const authHeader = req.headers.authorization;

  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    return res.status(401).json({ error: 'Missing authorization header' });
  }

  const token = authHeader.substring(7); // Remove 'Bearer '

  try {
    const decoded = jwt.verify(token, JWT_SECRET, {
      algorithms: ['HS256'],
      issuer: 'https://auth.example.com',
      audience: 'api.example.com'
    });

    // Token è valido, exp è automaticamente verificata
    req.userId = decoded.sub;
    req.user = decoded;
    next();
  } catch (error) {
    if (error.name === 'TokenExpiredError') {
      return res.status(401).json({ error: 'Token expired', expires_at: error.expiredAt });
    }
    if (error.name === 'JsonWebTokenError') {
      return res.status(401).json({ error: 'Invalid token' });
    }
    res.status(500).json({ error: 'Token verification failed' });
  }
}

app.get('/protected', verifyJWT, (req, res) => {
  res.json({ userId: req.userId, name: req.user.name });
});
```

**Dove MEMORIZZARE JWT (Client-side)?**

```javascript
// ❌ localStorage - Vulnerabile a XSS (JavaScript può accedere)
localStorage.setItem('access_token', jwt);

// ✅ HttpOnly + Secure cookie - Non accessibile da JavaScript
// Server imposta:
res.cookie('access_token', jwt, {
  httpOnly: true,      // JavaScript non può accedere (protegge da XSS)
  secure: true,        // HTTPS only
  sameSite: 'strict',  // CSRF protection
  maxAge: 15 * 60 * 1000 // 15 minuti
});

// ✅ In-memory (session storage, richiede re-login al refresh)
let accessToken = jwt; // Perso al refresh pagina, ma sicuro

// ❌ SessionStorage - Stesso rischio di localStorage
sessionStorage.setItem('access_token', jwt);
```

### 2.4 HMAC per Webhooks — Firma Asimmetrica

Quando il server invia richieste al client (webhooks), il client DEVE verificare che il payload non sia stato manomesso:

```
Server genera HMAC:
HMAC = SHA256(
  body + timestamp,
  secret_key
)

Allega HMAC in header:
X-Webhook-Signature: sha256=abc123def456

---

Client riceve webhook:
1. Estrae X-Webhook-Signature
2. Calcola il proprio HMAC usando lo stesso secret
3. Compara i due HMAC (timing-safe!)
4. Verifica il timestamp (preventiva replay attack)
```

```javascript
// Server (genera webhook)
const crypto = require('crypto');

const secret = 'whsec_abc123'; // Shared secret
const payload = JSON.stringify({
  event: 'order.created',
  order_id: 12345,
  timestamp: Math.floor(Date.now() / 1000)
});

const hmac = crypto
  .createHmac('sha256', secret)
  .update(payload)
  .digest('hex');

// Invia webhook
await fetch('https://client.example.com/webhook', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-Webhook-Signature': `sha256=${hmac}`,
    'X-Webhook-Timestamp': Math.floor(Date.now() / 1000).toString()
  },
  body: payload
});

---

// Client (verifica webhook)
app.post('/webhook', (req, res) => {
  const signature = req.headers['x-webhook-signature'];
  const timestamp = req.headers['x-webhook-timestamp'];

  // Verifica timestamp (entro ultimi 5 minuti)
  const now = Math.floor(Date.now() / 1000);
  if (Math.abs(now - parseInt(timestamp)) > 300) {
    return res.status(401).json({ error: 'Timestamp too old - replay attack' });
  }

  // Calcola HMAC del payload ricevuto
  const secret = process.env.WEBHOOK_SECRET;
  const bodyString = JSON.stringify(req.body);
  const expectedHmac = crypto
    .createHmac('sha256', secret)
    .update(bodyString + timestamp)
    .digest('hex');

  // ⚠️ CRITICAL: Usa timing-safe comparison (protegge da timing attacks)
  if (!crypto.timingSafeEqual(
    Buffer.from(`sha256=${expectedHmac}`),
    Buffer.from(signature)
  )) {
    return res.status(401).json({ error: 'Signature verification failed' });
  }

  // Firma è valida, procedi
  console.log('Webhook verified:', req.body);
  res.json({ received: true });
});
```

---

## 3. Rate Limiting e Throttling

Rate limiting è **obbligatorio** in ogni API pubblica. Non è optional: è una difesa contro:
- Denial of Service (DoS)
- Brute force attacks
- Resource exhaustion
- Cascading failures

### 3.1 Token Bucket Algorithm

```
┌─────────────────────────────────┐
│   Bucket (capacity: 10)         │
│                                 │
│  ••••••••••  (10 tokens)        │
└─────────────────────────────────┘
    ↑ Refill rate: 2 tokens/sec
    ↑ Max capacity: 10 tokens

Time 0s:   [••••••••••] 10 tokens, request uses 1 → 9 tokens
Time 0.5s: [•••••••••] 9 tokens, request uses 1 → 8 tokens
Time 1s:   [••••••••••] 8 + 2 refill = 10 tokens
```

**Implementazione (Redis):**

```javascript
const redis = require('redis');
const client = redis.createClient();

async function rateLimit(clientId, limit = 100, windowSeconds = 60) {
  const key = `rate_limit:${clientId}`;

  // Pipeline per atomicità
  const pipeline = client.multi();

  // Decrementa i token disponibili
  pipeline.decrby(key, 1);

  // Set expiration se prima volta (TTL = finestra)
  pipeline.expire(key, windowSeconds);

  const results = await pipeline.exec();
  const tokensRemaining = results[0];

  // Se negativi, abbiamo ecceduto il limite
  if (tokensRemaining < 0) {
    return {
      allowed: false,
      tokensRemaining: 0,
      resetAt: await client.ttl(key)
    };
  }

  return {
    allowed: true,
    tokensRemaining,
    limit
  };
}

app.use(async (req, res, next) => {
  const clientId = req.ip || req.headers['x-forwarded-for'] || 'unknown';
  const result = await rateLimit(clientId, 100, 60);

  res.setHeader('X-RateLimit-Limit', '100');
  res.setHeader('X-RateLimit-Remaining', Math.max(0, result.tokensRemaining));
  res.setHeader('X-RateLimit-Reset', Math.floor(Date.now() / 1000) + result.resetAt);

  if (!result.allowed) {
    res.setHeader('Retry-After', result.resetAt);
    return res.status(429).json({
      error: 'Too Many Requests',
      message: `Rate limit exceeded. Reset in ${result.resetAt} seconds`
    });
  }

  next();
});
```

### 3.2 Sliding Window Algorithm

```
Request timeline:
  ▼ (12:00:00)
  ▼ (12:00:05)
           ▼ (12:00:30)  ← Sliding window all'elemento più recente
           ▼ (12:00:45)
                    ▼ (12:01:00) ← Limit 5 requests per minuto

Window 1 minuto:
[12:00:00 ← 12:01:00] (ultimo request)

Requests nel window: 4 (12:00:00, 12:00:05, 12:00:30, 12:00:45)
Prossimo request = 12:01:05: 12:00:05 esce dal window → 3 requests + 1 new = 4 (allowed)
```

```javascript
async function slidingWindowRateLimit(clientId, limit = 100, windowSeconds = 60) {
  const now = Math.floor(Date.now() / 1000);
  const windowStart = now - windowSeconds;

  const key = `sliding:${clientId}`;

  // Redis sorted set con timestamps come scores
  const pipeline = client.multi();

  // Rimuovi richieste fuori dalla finestra
  pipeline.zremrangebyscore(key, '-inf', windowStart);

  // Conta richieste nella finestra
  pipeline.zcard(key);

  // Aggiungi richiesta corrente se non eccediamo limite
  pipeline.zadd(key, now, `${now}-${Math.random()}`);

  // TTL = finestra + buffer
  pipeline.expire(key, windowSeconds + 1);

  const results = await pipeline.exec();
  const requestCount = results[1];

  if (requestCount >= limit) {
    return {
      allowed: false,
      requests: requestCount,
      resetAt: windowStart + windowSeconds - now
    };
  }

  return {
    allowed: true,
    requests: requestCount + 1,
    limit
  };
}
```

### 3.3 Exponential Backoff con Jitter

**Il problema: Thundering Herd**

```
Rate limit trigger: API server rate limits tutti i client
  ↓
Client 1 retry after 1s
Client 2 retry after 1s      ← TUTTI insieme
Client 3 retry after 1s
Client 4 retry after 1s
  ↓
Server riceve picco di 1000 requests nello stesso momento
  ↓
Rate limit triggerato di nuovo → fail
```

**Soluzione: Exponential Backoff + Jitter**

```
Retry 1: wait = 1 sec + random(0, 1 sec) = 1.23 sec
Retry 2: wait = 2 sec + random(0, 2 sec) = 3.87 sec  ← Spread out!
Retry 3: wait = 4 sec + random(0, 4 sec) = 6.42 sec
Retry 4: wait = 8 sec + random(0, 8 sec) = 10.15 sec
Cap max:   wait = min(64 sec, calculated)
```

```javascript
// Client-side retry logic
async function fetchWithRetry(url, options = {}, maxRetries = 5) {
  let lastError;

  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const response = await fetch(url, options);

      // Success
      if (response.ok) return response;

      // Rate limited - retry
      if (response.status === 429) {
        const retryAfter = response.headers.get('Retry-After');
        const waitMs = retryAfter
          ? parseInt(retryAfter) * 1000
          : exponentialBackoffWithJitter(attempt);

        await sleep(waitMs);
        continue;
      }

      // Non-retriable error
      throw new Error(`HTTP ${response.status}`);
    } catch (error) {
      lastError = error;

      // Transient error - retry with backoff
      if (isTransientError(error)) {
        const waitMs = exponentialBackoffWithJitter(attempt);
        await sleep(waitMs);
        continue;
      }

      // Non-transient - fail immediately
      throw error;
    }
  }

  throw new Error(`Max retries exceeded: ${lastError.message}`);
}

function exponentialBackoffWithJitter(attemptNumber) {
  const baseDelay = Math.pow(2, attemptNumber) * 1000; // 1s, 2s, 4s, 8s...
  const maxDelay = 64 * 1000; // 64 second cap
  const cappedDelay = Math.min(baseDelay, maxDelay);

  // Jitter: random delay tra 0 e cappedDelay
  const jitter = Math.random() * cappedDelay;

  return cappedDelay + jitter; // Equals: baseDelay + random(0, baseDelay)
}

async function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// Uso
const response = await fetchWithRetry('https://api.example.com/orders');
```

### 3.4 HTTP Rate Limiting Headers

Standard HTTP per comunicare i limiti al client:

```
HTTP/1.1 200 OK
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 89
X-RateLimit-Reset: 1680703200

---

HTTP/1.1 429 Too Many Requests
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1680703200
Retry-After: 3600  (client deve attendere 3600 secondi = 1 ora)
```

```javascript
app.use((req, res, next) => {
  const clientId = req.ip;
  const limit = 100;
  const remaining = getRemainingRequests(clientId);
  const resetTime = getResetTime(clientId);

  res.setHeader('X-RateLimit-Limit', limit.toString());
  res.setHeader('X-RateLimit-Remaining', Math.max(0, remaining).toString());
  res.setHeader('X-RateLimit-Reset', Math.floor(resetTime / 1000).toString());

  if (remaining <= 0) {
    const retryAfterSeconds = Math.ceil((resetTime - Date.now()) / 1000);
    res.setHeader('Retry-After', retryAfterSeconds.toString());
    return res.status(429).json({ error: 'Rate limited' });
  }

  next();
});
```

---

## 4. Retry e Error Handling

### 4.1 Classificazione Errori: Transient vs Permanent

**Fondamentale per decidere se fare retry:**

| Errore | Tipo | Azione |
|--------|------|--------|
| 429 Too Many Requests | Transient | Retry con exponential backoff |
| 500 Internal Server Error | Transient (di solito) | Retry con exponential backoff |
| 502 Bad Gateway | Transient | Retry con exponential backoff |
| 503 Service Unavailable | Transient | Retry con exponential backoff |
| 504 Gateway Timeout | Transient | Retry con exponential backoff |
| **400 Bad Request** | **Permanent** | **NON fare retry** |
| **401 Unauthorized** | **Permanent** | **NON fare retry** |
| **403 Forbidden** | **Permanent** | **NON fare retry** |
| **404 Not Found** | **Permanent** | **NON fare retry** |

```javascript
function isTransientError(statusCode) {
  // Errori server (5xx) sono generalmente transient
  if (statusCode >= 500 && statusCode < 600) return true;

  // Too Many Requests è transient
  if (statusCode === 429) return true;

  // Timeout (nel contesto HTTP via response)
  if (statusCode === 408) return true;

  // Tutto il resto (4xx, ecc) sono permanent
  return false;
}

// Uso
async function fetchWithRetry(url, maxRetries = 3) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const response = await fetch(url);

      if (isTransientError(response.status)) {
        const waitMs = exponentialBackoffWithJitter(attempt);
        await sleep(waitMs);
        continue;
      }

      // Non transient - non fare retry
      return response;
    } catch (error) {
      if (isTransientNetworkError(error)) {
        const waitMs = exponentialBackoffWithJitter(attempt);
        await sleep(waitMs);
        continue;
      }

      throw error; // Non transient
    }
  }
}
```

### 4.2 Circuit Breaker Pattern

Quando un servizio è costantemente down, continua a fare retry è controproducente:

```
STATE: CLOSED (Normale)
↓
3 richieste consecutive falliscono
↓
STATE: OPEN (Bloccato)
↓
Tutto le richieste falliscono immediatamente senza contattare il servizio
↓
[Timeout di 30 secondi]
↓
STATE: HALF-OPEN (Tentativo di recovery)
↓
1 richiesta di prova
  ✅ Successo → torna a CLOSED
  ❌ Fallisce → torna a OPEN + estendi timeout

```

```javascript
class CircuitBreaker {
  constructor(options = {}) {
    this.failureThreshold = options.failureThreshold || 5;
    this.resetTimeout = options.resetTimeout || 60000; // 60 secondi
    this.state = 'CLOSED'; // CLOSED, OPEN, HALF_OPEN
    this.failureCount = 0;
    this.nextAttemptTime = Date.now();
  }

  async call(fn) {
    if (this.state === 'OPEN') {
      if (Date.now() < this.nextAttemptTime) {
        throw new Error('Circuit breaker is OPEN');
      }
      // Prova transizione a HALF_OPEN
      this.state = 'HALF_OPEN';
    }

    try {
      const result = await fn();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }

  onSuccess() {
    this.failureCount = 0;
    this.state = 'CLOSED';
  }

  onFailure() {
    this.failureCount++;

    if (this.failureCount >= this.failureThreshold) {
      this.state = 'OPEN';
      this.nextAttemptTime = Date.now() + this.resetTimeout;
    }
  }
}

// Uso
const breaker = new CircuitBreaker({ failureThreshold: 5, resetTimeout: 30000 });

async function callDownstreamAPI() {
  return breaker.call(async () => {
    return fetch('https://downstream-api.example.com/data');
  });
}

// Se downstream è down:
// 5 tentativi falliscono → circuito si apre → fallisce immediatamente
// 30 secondi dopo → prova 1 tentativo → se fallisce, riapertura
```

### 4.3 Dead Letter Queue (DLQ)

Quando un messaggio (order, event, webhook) non può essere processato nemmeno dopo retry, va in una coda speciale per analisi:

```
┌──────────┐
│ Evento   │
└────┬─────┘
     │
     ├─→ [Tentativo 1] Fallisce
     │   Retry dopo 1s
     │
     ├─→ [Tentativo 2] Fallisce
     │   Retry dopo 4s
     │
     ├─→ [Tentativo 3] Fallisce
     │   Retry dopo 16s
     │
     ├─→ [Tentativo 4] Fallisce
     │
     ↓
  ┌─────────────────────┐
  │ Dead Letter Queue   │
  │                     │
  │ (Analytics, Alert)  │
  │ (Manual review)     │
  └─────────────────────┘
```

```javascript
class MessageQueue {
  constructor(redisClient) {
    this.redis = redisClient;
    this.mainQueue = 'messages:queue';
    this.dlq = 'messages:dlq';
    this.maxRetries = 4;
  }

  async enqueue(message) {
    await this.redis.lpush(this.mainQueue, JSON.stringify(message));
  }

  async processMessage(message, attemptNumber = 0) {
    try {
      // Processa il messaggio
      await this.handleMessage(message);
      return { success: true };
    } catch (error) {
      if (attemptNumber < this.maxRetries) {
        // Retry con backoff
        const delayMs = Math.pow(2, attemptNumber) * 1000;
        setTimeout(() => {
          message.attemptNumber = attemptNumber + 1;
          this.enqueue(message);
        }, delayMs);

        return { success: false, willRetry: true };
      } else {
        // Max retries raggiunto → DLQ
        await this.redis.lpush(
          this.dlq,
          JSON.stringify({
            ...message,
            failedAt: new Date().toISOString(),
            error: error.message,
            attempts: attemptNumber
          })
        );

        // Alert per team
        await this.alertTeam(`Message sent to DLQ after ${attemptNumber} retries`);

        return { success: false, dlq: true };
      }
    }
  }

  async handleMessage(message) {
    // Implementazione specifica
    if (message.type === 'order_created') {
      await OrderService.process(message.data);
    }
  }

  async alertTeam(message) {
    console.error('[DLQ Alert]', message);
    // Invia a Slack, PagerDuty, etc.
  }
}
```

### 4.4 Structured Error Responses (RFC 7807)

Standard universale per errori API:

```json
{
  "type": "https://api.example.com/errors/insufficient-balance",
  "title": "Insufficient Account Balance",
  "status": 400,
  "detail": "The account does not have sufficient funds to process the transfer. Required: $500, Available: $350",
  "instance": "/transfers/12345",
  "balance": 350,
  "required": 500
}
```

```javascript
app.post('/transfers', async (req, res) => {
  try {
    const { amount, from, to } = req.body;
    const account = await Account.findById(from);

    if (account.balance < amount) {
      return res.status(400).json({
        type: 'https://api.example.com/errors/insufficient-balance',
        title: 'Insufficient Balance',
        status: 400,
        detail: `Required: $${amount}, Available: $${account.balance}`,
        instance: req.path,
        balance: account.balance,
        required: amount
      });
    }

    const transfer = await createTransfer(amount, from, to);
    res.json({ transfer_id: transfer.id, status: 'pending' });
  } catch (error) {
    res.status(500).json({
      type: 'https://api.example.com/errors/internal-server-error',
      title: 'Internal Server Error',
      status: 500,
      detail: error.message,
      instance: req.path
    });
  }
});
```

### 4.5 Timeout Management

Ogni operazione HTTP necessita di timeout:

```javascript
// Senza timeout: richiesta può "stare sospesa" indefinitamente
fetch('https://api.example.com/data'); // ❌ Pericolo!

// Con timeout: fallisce dopo 10 secondi
const controller = new AbortController();
const timeoutId = setTimeout(() => controller.abort(), 10000);

try {
  const response = await fetch('https://api.example.com/data', {
    signal: controller.signal
  });
} catch (error) {
  if (error.name === 'AbortError') {
    console.log('Request timeout after 10 seconds');
  }
} finally {
  clearTimeout(timeoutId);
}
```

---

## 5. Pagination

### 5.1 Offset-Based Pagination

```
GET /users?limit=10&offset=0
GET /users?limit=10&offset=10
GET /users?limit=10&offset=20
```

**Problemi di performance:**

```sql
-- offset=0: molto veloce (first 10)
SELECT * FROM users ORDER BY created_at DESC LIMIT 10 OFFSET 0;

-- offset=100000: LENTISSIMO (reads first 100,010, discards 100,000)
SELECT * FROM users ORDER BY created_at DESC LIMIT 10 OFFSET 100000;
```

**Quando usare offset:**
- Dataset piccoli (< 100k records)
- Prototipi/MVP
- Quando cliente richiede "page 5"

```javascript
app.get('/users', async (req, res) => {
  const limit = Math.min(parseInt(req.query.limit) || 10, 100); // Max 100
  const offset = parseInt(req.query.offset) || 0;

  const users = await User.find()
    .limit(limit)
    .skip(offset)
    .sort({ created_at: -1 });

  const total = await User.countDocuments();

  res.json({
    users,
    pagination: {
      offset,
      limit,
      total,
      pages: Math.ceil(total / limit)
    }
  });
});
```

### 5.2 Cursor-Based Pagination

```
GET /users?limit=10&cursor=eyJpZCI6IDUwLCJjcmVhdGVkX2F0IjogIjIwMjYtMDMtMDEifQ==

Response:
{
  "users": [...],
  "pagination": {
    "cursor": "eyJpZCI6IDYwLCJjcmVhdGVkX2F0IjogIjIwMjYtMDMtMDIifQ==",
    "hasMore": true
  }
}
```

**Cursor = encoded position marker**

```javascript
// Encode cursor
function encodeCursor(lastId, lastCreatedAt) {
  const cursor = { id: lastId, created_at: lastCreatedAt };
  return Buffer.from(JSON.stringify(cursor)).toString('base64');
}

// Decode cursor
function decodeCursor(cursorString) {
  const json = Buffer.from(cursorString, 'base64').toString('utf-8');
  return JSON.parse(json);
}

app.get('/users', async (req, res) => {
  const limit = Math.min(parseInt(req.query.limit) || 10, 100);
  let cursor = null;

  if (req.query.cursor) {
    cursor = decodeCursor(req.query.cursor);
  }

  // Query: prendi record DOPO il cursor
  let query = User.find().sort({ created_at: -1, id: -1 });

  if (cursor) {
    // Keyset pagination: solo record dopo l'ultimo
    query = query.where('created_at').lt(cursor.created_at)
      .or([
        { created_at: cursor.created_at, id: { $lt: cursor.id } }
      ]);
  }

  const users = await query.limit(limit + 1); // +1 per sapere se ce ne sono altri

  const hasMore = users.length > limit;
  if (hasMore) users.pop(); // Rimuovi il +1

  const lastUser = users[users.length - 1];
  const nextCursor = lastUser
    ? encodeCursor(lastUser.id, lastUser.created_at.toISOString())
    : null;

  res.json({
    users,
    pagination: {
      cursor: nextCursor,
      hasMore
    }
  });
});
```

**Vantaggi cursor vs offset:**
- ✅ Costante O(1) indipendentemente dalla posizione
- ✅ Non soffre di duplicati/skip quando data cambia
- ❌ Meno intuitivo per l'utente ("page 5"non ha senso)
- ❌ Non puoi jumpare a posizione arbitraria

### 5.3 Keyset Pagination

Variante di cursor che usa un composite key (unique indexed):

```sql
-- Keyset pagination
SELECT * FROM orders
WHERE (created_at, id) > ('2026-04-01', 999)
ORDER BY created_at DESC, id DESC
LIMIT 10;
```

Performante come cursor-based, ma con query SQL più semplice.

### 5.4 Performance a Scala

| Metodo | Records | Latenza | Uso |
|--------|---------|---------|-----|
| Offset | 10k | 10ms | OK |
| Offset | 100k | 100ms | Borderline |
| Offset | 1M | 1s | NO |
| Cursor | 10k | 5ms | Perfetto |
| Cursor | 100k | 5ms | Perfetto |
| Cursor | 1M | 5ms | Perfetto |

**Scegli cursor per dataset > 100k records.**

---

## 6. Webhooks — Notifiche Asincrone

### 6.1 Principi di Design

```
┌────────────┐
│   Event    │
│ (order.    │
│ created)   │
└─────┬──────┘
      │
      ├→ Queue (garantisce consegna)
      │
      ├→ Delivery Worker (retry logic)
      │
      ├→ HTTP POST al webhook URL
      │
      │  Success (2xx) ✅
      │  ├→ Conclude, log success
      │
      │  Transient (5xx, timeout) ⏳
      │  ├→ Retry: 1s, 4s, 16s, 64s (exponential)
      │
      │  Permanent (4xx) ❌
      │  ├→ Stop retry, move to DLQ, alert
```

### 6.2 Signature Verification (HMAC-SHA256)

```javascript
// Server (invia webhook)
const crypto = require('crypto');
const secret = 'whsec_abc123def456';

async function sendWebhook(url, payload) {
  const timestamp = Math.floor(Date.now() / 1000);
  const body = JSON.stringify(payload);

  // Firma: HMAC(payload + timestamp, secret)
  const signature = crypto
    .createHmac('sha256', secret)
    .update(`${timestamp}.${body}`)
    .digest('hex');

  await fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-Webhook-Signature': `sha256=${signature}`,
      'X-Webhook-Timestamp': timestamp.toString(),
      'X-Webhook-ID': generateWebhookId() // Idempotency
    },
    body
  });
}

// Client (riceve e verifica webhook)
app.post('/webhook', (req, res) => {
  const signature = req.headers['x-webhook-signature'];
  const timestamp = req.headers['x-webhook-timestamp'];
  const webhookId = req.headers['x-webhook-id'];

  // 1. Verifica timestamp (max 5 minuti fa, previene replay)
  const now = Math.floor(Date.now() / 1000);
  if (Math.abs(now - parseInt(timestamp)) > 300) {
    return res.status(401).json({ error: 'Timestamp too old' });
  }

  // 2. Verifica idempotency (same webhookId = skip processing)
  if (isProcessed(webhookId)) {
    return res.json({ received: true }); // Idempotent
  }

  // 3. Verifica firma
  const body = JSON.stringify(req.body);
  const secret = process.env.WEBHOOK_SECRET;
  const expectedSig = crypto
    .createHmac('sha256', secret)
    .update(`${timestamp}.${body}`)
    .digest('hex');

  // ⚠️ TIMING-SAFE comparison
  if (!crypto.timingSafeEqual(
    Buffer.from(signature.replace('sha256=', '')),
    Buffer.from(expectedSig)
  )) {
    return res.status(401).json({ error: 'Signature mismatch' });
  }

  // 4. Processa evento
  try {
    markAsProcessed(webhookId);
    handleEvent(req.body);
    res.json({ received: true });
  } catch (error) {
    unmarkAsProcessed(webhookId); // Consenti retry
    res.status(500).json({ error: 'Processing failed' });
  }
});
```

### 6.3 Idempotency per Webhooks

Il cliente potrebbe ricevere lo stesso webhook 2 volte (retry). Deve essere idempotente:

```javascript
// Idempotency key = webhook ID (server assegna)
const processedWebhooks = new Set(); // In produzione: Redis

app.post('/webhook', (req, res) => {
  const webhookId = req.headers['x-webhook-id'];

  // Se già processato, ritorna lo stesso risultato
  if (processedWebhooks.has(webhookId)) {
    return res.json({ received: true });
  }

  try {
    // Processa (es: crea order)
    const order = createOrder(req.body.order);

    // Memorizza che l'ho processato
    processedWebhooks.add(webhookId);

    res.json({ received: true });
  } catch (error) {
    // Non memorizzare se fallisce
    res.status(500).json({ error: 'Failed' });
  }
});
```

### 6.4 Retry Policy

```
Tentativo 1: Immediato
Fallisce

Tentativo 2: 1 secondo dopo
Fallisce

Tentativo 3: 4 secondi dopo (1 * 4)
Fallisce

Tentativo 4: 16 secondi dopo (4 * 4)
Fallisce

Tentativo 5: 64 secondi dopo (16 * 4)
Fallisce

→ Sposta a DLQ (Dead Letter Queue)
  Manuale review / Alert team
```

```javascript
async function deliverWebhook(webhookUrl, payload, attempt = 1) {
  const maxAttempts = 5;
  const maxWait = 64000;

  try {
    const response = await fetch(webhookUrl, {
      method: 'POST',
      timeout: 10000,
      headers: {
        'Content-Type': 'application/json',
        'X-Webhook-Signature': generateSignature(payload)
      },
      body: JSON.stringify(payload)
    });

    if (response.ok) {
      return { success: true };
    }

    // 4xx: non fare retry
    if (response.status >= 400 && response.status < 500) {
      await moveToDeadLetterQueue(webhookUrl, payload, `HTTP ${response.status}`);
      return { success: false, dlq: true };
    }

    // 5xx: retry
    if (response.status >= 500) {
      throw new Error(`HTTP ${response.status}`);
    }
  } catch (error) {
    // Network error, timeout: retry
    if (attempt < maxAttempts) {
      const delay = Math.min(
        Math.pow(4, attempt - 1) * 1000 + Math.random() * 1000,
        maxWait
      );

      setTimeout(() => {
        deliverWebhook(webhookUrl, payload, attempt + 1);
      }, delay);

      return { success: false, willRetry: true, nextAttempt: attempt + 1 };
    } else {
      // Max retries reached
      await moveToDeadLetterQueue(webhookUrl, payload, error.message);
      return { success: false, dlq: true };
    }
  }
}
```

---

## 7. Data Formats e Serializzazione

### 7.1 JSON Best Practices

```json
{
  "id": 123,
  "name": "Alice",
  "email": "alice@example.com",
  "created_at": "2026-04-01T14:32:00Z",
  "is_active": true,
  "balance": 1500.50,
  "metadata": null,
  "tags": ["vip", "early-adopter"],
  "address": {
    "street": "123 Main St",
    "city": "Springfield",
    "zip": "12345"
  }
}
```

**Non codificare dati binari direttamente — usa base64:**

```json
{
  "file_content": "SGVsbG8gV29ybGQh" // "Hello World!" in base64
}

Decodifica: Buffer.from('SGVsbG8gV29ybGQh', 'base64').toString()
```

### 7.2 Date/Time — ISO 8601 Standard

```json
{
  "created_at": "2026-04-01T14:32:00Z",        // UTC con Z
  "updated_at": "2026-04-01T14:32:00+02:00",  // Con timezone offset
  "deleted_at": null                           // null = non eliminato
}
```

**Non usare Unix timestamps a meno che non sia specificamente richiesto** (consumano memoria, meno human-readable):

```javascript
// ❌ Non ideale
{ "created_at": 1680427920 }

// ✅ Meglio
{ "created_at": "2026-04-01T14:32:00Z" }

// In JavaScript
const date = new Date().toISOString(); // "2026-04-01T14:32:00.000Z"
```

### 7.3 Money/Currency

**Mai rappresentare soldi come float** (precision issues):

```javascript
// ❌ SBAGLIATO - causa errori di arrotondamento
{ "price": 19.99 }
0.1 + 0.2 === 0.3 // false! (floating point precision)

// ✅ CORRETTO - rappresenta in centesimi (integer)
{ "price_cents": 1999 } // 19.99 EUR

// Oppure string
{ "price": "19.99", "currency": "EUR" }
```

### 7.4 Null vs Absent Fields

```json
{
  "id": 123,
  "email": "alice@example.com",
  "phone": null,              // Esplicitamente assente/ignoto
  "middle_name": null         // Non fornito
}

vs

{
  "id": 123,
  "email": "alice@example.com"
  // phone non incluso = non fornito? non noto? diverso?
}
```

**Scegli una convenzione e mantenila consistente.**

Raccomandazione: **Includi sempre i campi, usa null per assente/ignoto**.

---

## 8. Caching — Ridurre Carico

### 8.1 HTTP Caching Headers

```
Cache-Control: max-age=3600, public
  ↓
1. Valido per 3600 secondi
2. Può essere cachato dai proxy pubblici (CDN)

Cache-Control: max-age=300, private
  ↓
1. Valido per 300 secondi
2. Solo browser, NON condividere (proxy)
```

```javascript
app.get('/users/:id', (req, res) => {
  const user = User.findById(req.params.id);

  res.set({
    'Cache-Control': 'max-age=300, private', // 5 minuti, browser only
    'Last-Modified': user.updated_at.toUTCString(),
    'ETag': `"${user.id}-${user.version}"`
  });

  res.json(user);
});
```

### 8.2 ETag — Validazione Cache Condizionale

```
Request 1:
GET /users/123
← 200 OK
← ETag: "abc123def456"
← { id: 123, name: "Alice" }

Request 2 (5 minuti dopo):
GET /users/123
If-None-Match: "abc123def456"
← 304 Not Modified (nessun body, usa cache locale)

Beneficio: Bandwidth zero se niente è cambiato
```

```javascript
app.get('/users/:id', (req, res) => {
  const user = User.findById(req.params.id);
  const etag = `"${user.id}-${user.updated_at.getTime()}"`;

  res.set('ETag', etag);

  // Client invia If-None-Match con ETag precedente
  if (req.headers['if-none-match'] === etag) {
    return res.status(304).send(); // Not Modified
  }

  res.json(user);
});
```

### 8.3 Application-Level Caching (Redis)

```javascript
const redis = require('redis');
const cache = redis.createClient();

app.get('/expensive-calculation/:id', async (req, res) => {
  const cacheKey = `calc:${req.params.id}`;

  // Try cache
  const cached = await cache.get(cacheKey);
  if (cached) {
    return res.json(JSON.parse(cached));
  }

  // Cache miss: compute
  const result = await expensiveCalculation(req.params.id);

  // Cache per 1 ora
  await cache.setex(cacheKey, 3600, JSON.stringify(result));

  res.json(result);
});
```

### 8.4 Cache Invalidation

Il più difficile nei sistemi distribuiti:

```
1. TTL-based: Cache scade dopo X secondi (semplice, stantio)
2. Evento-based: Invalidare quando dato cambia (complesso, ma fresco)
3. Manual purge: `/cache/purge` endpoint (fragile, umano-dependente)
```

**Pattern: Invalidate on Write**

```javascript
app.put('/users/:id', async (req, res) => {
  const user = await User.findByIdAndUpdate(req.params.id, req.body);

  // Invalida cache per questo utente
  const cacheKey = `user:${req.params.id}`;
  await cache.del(cacheKey);

  // Invalida anche liste che contengono questo utente
  const listCacheKey = `users:list:*`;
  await cache.delPattern(listCacheKey); // O pubblica evento

  res.json(user);
});
```

---

## 9. API Versioning

### 9.1 URI Versioning — Più Semplice

```
/v1/users
/v2/users    ← Nuova versione con breaking changes

Vantaggi: Esplicito, routing facile
Svantaggi: Moltiplica endpoint, deploy complesso
```

```javascript
// Express Router
const v1Routes = require('./routes/v1');
const v2Routes = require('./routes/v2');

app.use('/v1', v1Routes);
app.use('/v2', v2Routes);

// URLs:
// GET /v1/users        → v1 controller
// GET /v2/users        → v2 controller
```

### 9.2 Header Versioning

```bash
curl -H "Accept: application/vnd.example.v2+json" https://api.example.com/users
```

Vantaggi: URL puliti
Svantaggi: Non visible nella barra indirizzo, confuso nei log

### 9.3 Deprecation Policy

**Comunicazione chiara è essenziale:**

```javascript
app.use((req, res, next) => {
  if (req.path.startsWith('/v1')) {
    res.set(
      'Deprecation',
      'true'
    );
    res.set(
      'Sunset',
      new Date(Date.now() + 180 * 24 * 60 * 60 * 1000).toUTCString() // 180 days
    );
    res.set(
      'Link',
      '</docs/migration-v1-to-v2>; rel="deprecation"'
    );
  }
  next();
});

// Response
// HTTP/1.1 200 OK
// Deprecation: true
// Sunset: Mon, 01 Oct 2026 00:00:00 GMT
// Link: </docs/migration-v1-to-v2>; rel="deprecation"
```

### 9.4 Non-Breaking Changes

Se implementi correttamente, non hai bisogno di nuove versioni:

```json
// v1 response
{ "id": 1, "name": "Alice" }

// Aggiungi nuovi campi (backward compatible)
{ "id": 1, "name": "Alice", "email": "alice@example.com" }

// Client v1 ignora il nuovo campo "email", funziona lo stesso ✅
```

**Breaking changes richiedono nuova versione:**

```json
// v1
{ "user_id": 1, "user_name": "Alice" }

// v2 (rinominato)
{ "id": 1, "name": "Alice" }

// Client v1 cerca user_id e user_name → crash ❌
```

---

## 10. Sicurezza API

### 10.1 OWASP API Security Top 10

Delle [OWASP API Top 10](https://www.pynt.io/learning-hub/owasp-top-10-guide/owasp-api-top-10) ricordiamo i principali:

1. **Broken Authentication**: Token scaduti, credenziali deboli, nessuna MFA
2. **Broken Object Level Authorization (BOLA)**: Accedi a `GET /users/123` anche se non autorizzato
3. **Broken Function Level Authorization**: Accedi a endpoint amministratori senza permessi
4. **Unrestricted Resource Consumption**: No rate limiting → DoS
5. **Broken Business Logic**: Logica di autorizzazione difettosa

### 10.2 Input Validation

**Fondamentale: Whitelist, non blacklist.**

```javascript
// ❌ Blacklist (incompleto, facilmente bypassato)
if (input.includes('<script>')) {
  reject();
}

// ✅ Whitelist (esplicito, sicuro)
const allowedCharacters = /^[a-zA-Z0-9_\-\.@]+$/;
if (!allowedCharacters.test(input)) {
  return res.status(400).json({ error: 'Invalid characters' });
}
```

### 10.3 SQL Injection Prevention

```javascript
// ❌ Concatenazione (SQL injection!)
const query = `SELECT * FROM users WHERE email = '${req.query.email}'`;
db.query(query); // Se email = "' OR '1'='1", matcha tutto!

// ✅ Prepared statements
const query = 'SELECT * FROM users WHERE email = ?';
db.query(query, [req.query.email]); // Email viene escapato automaticamente
```

### 10.4 CORS Configuration

```javascript
// ❌ Permetti tutti
app.use(cors()); // = Access-Control-Allow-Origin: *

// ✅ Whitelist specifici
app.use(cors({
  origin: ['https://app.example.com', 'https://staging.example.com'],
  credentials: true, // Permetti cookies
  optionsSuccessStatus: 200
}));
```

### 10.5 API Key Rotation

```javascript
// Non mettere mai una API key statica nel codice
const apiKey = process.env.GITHUB_API_KEY; // Variabile ambiente

// Ruota le chiavi periodicamente (es: mensile)
// Vecchia chiave: valida fino al 30 Aprile
// Nuova chiave: valida dal 15 Aprile
// Overlap di 15 giorni per transizione
```

### 10.6 Secrets Management

```javascript
// ❌ Hardcoded (NEVER)
const SECRET = 'my-secret-key-12345';

// ✅ Environment variables
const SECRET = process.env.SECRET;

// ✅ Secrets manager (AWS Secrets Manager, HashiCorp Vault)
const secret = await secretsManager.getSecret('api-secret-key');
```

---

## 11. Monitoring e Observability

### 11.1 SLI, SLO, SLA — Definizioni Essenziali

```
SLI (Service Level Indicator)
  ↓
  Metrica quantitativa che misuri il comportamento
  Es: "Latenza p99 = 200ms", "Uptime = 99.9%"

SLO (Service Level Objective)
  ↓
  Target per SLI
  Es: "Latenza p99 deve essere ≤ 200ms"
      "Uptime deve essere ≥ 99.9%"

SLA (Service Level Agreement)
  ↓
  Contratto legale con conseguenze se SLO non raggiunto
  Es: "Se uptime < 99.9%, cliente riceve 10% sconto"
```

### 11.2 Metriche Critiche

```
Latency Percentiles:
- p50 (mediana): rappresenta esperienza utente "average"
- p95: 95% delle richieste sono più veloci di questo
- p99: 99% delle richieste sono più veloci di questo
  → Distribuisci risorse per p99, non p50!

Error Rate:
- Percentuale di richieste che falliscono (4xx, 5xx)
- Target: < 0.1% (99.9% success rate)

Throughput:
- Richieste processate per secondo (RPS)
- Capacità di handling

Available:
- Percentuale di tempo che servizio è raggiungibile
- Target: 99.9% = 43.2 minuti downtime per mese
```

### 11.3 Distributed Tracing

Segui una richiesta attraverso multiple servizi:

```
Client request:
GET /orders/123

┌─── Trace ID: abc123def456 ───────────────────────────┐
│                                                       │
├─ Span 1: API Gateway (2ms)                           │
│  └─ Authorization check (0.5ms)                      │
│  └─ Rate limit check (0.3ms)                         │
│                                                       │
├─ Span 2: Order Service (45ms)                        │
│  └─ Database query (30ms)                            │
│  └─ Cache lookup (8ms)                               │
│                                                       │
├─ Span 3: Inventory Service (35ms)                    │
│  └─ Stock check (30ms)                               │
│                                                       │
└─ Total latency: 82ms                                 │
```

```javascript
const tracer = require('opentelemetry-api').trace.getTracer('app');

app.get('/orders/:id', async (req, res) => {
  const span = tracer.startSpan('get_order');

  try {
    // Span child: database
    const dbSpan = tracer.startSpan('db_query', { parent: span });
    const order = await Order.findById(req.params.id);
    dbSpan.end();

    // Span child: inventory
    const invSpan = tracer.startSpan('check_inventory', { parent: span });
    const inventory = await checkInventory(order.items);
    invSpan.end();

    res.json({ order, inventory });
  } finally {
    span.end();
  }
});
```

---

## 12. Pattern Avanzati di Integrazione

### 12.1 Saga Pattern — Transazioni Distribuite

Problema: Come garantire consistenza su multiple servizi?

```
┌──────────┐    ┌──────────┐    ┌──────────┐
│ Order    │    │ Payment  │    │ Inventory│
│ Service  │    │ Service  │    │ Service  │
└─────┬────┘    └────┬─────┘    └────┬─────┘
      │              │               │
      │ Create order │               │
      ├─────────────→│               │
      │              │ Charge card   │
      │              ├──────────────→│ Reserve stock
      │              │               ├──────────────→
      │              │               │
      │ (success)    │ (success)     │ (success)
      │←─────────────┼───────────────┤
      │              │               │
      └──────────────────────────────┘
         Saga complete: All committed
```

Se uno step fallisce:

```
      ┌─── Payment failed! ───┐
      │                       │
      ├→ Rollback order      │
      ├→ Refund payment      │
      ├→ Release inventory   │
      │                       │
      └── Saga compensated ──┘
```

```javascript
// Choreography-based Saga
// Servizi ascoltano eventi e reagiscono

// OrderService crea ordine
app.post('/orders', async (req, res) => {
  const order = await Order.create(req.body);

  // Pubblica evento
  await eventBus.publish('order.created', { order_id: order.id });

  res.status(201).json(order);
});

// PaymentService ascolta order.created
eventBus.on('order.created', async (event) => {
  const { order_id } = event;

  try {
    const payment = await chargeCard(order_id);
    await eventBus.publish('payment.completed', { order_id });
  } catch (error) {
    await eventBus.publish('payment.failed', { order_id });
  }
});

// InventoryService ascolta payment.completed
eventBus.on('payment.completed', async (event) => {
  const { order_id } = event;

  try {
    await reserveInventory(order_id);
    await eventBus.publish('order.confirmed', { order_id });
  } catch (error) {
    await eventBus.publish('order.failed', { order_id });
  }
});

// Compensating transaction: payment.failed
eventBus.on('payment.failed', async (event) => {
  const { order_id } = event;

  // Rollback
  await Order.update(order_id, { status: 'cancelled' });
  await eventBus.publish('order.cancelled', { order_id });
});
```

### 12.2 Outbox Pattern — Garantire Consegna

Problema: Cosa se il servizio crasha dopo salvare il dato ma prima di pubblicare l'evento?

```
┌─────────────────────────────────────────┐
│ Order Service Database              │
├─────────────────────────────────────────┤
│ Table: orders                        │
│  id: 123, status: "pending"         │
│                                     │
│ Table: outbox (transactional)      │
│  event_id: abc, type: "order.     │
│  created", payload: {...}          │
└─────────────────────────────────────────┘
           ↓ [Separate process]
    Polling service legge outbox
           ↓
    Pubblica evento su message bus
           ↓
    Marca come published in outbox
           ↓
    [Se crash tra step 2 e 3, retry automatico]
```

```javascript
// Salva order E evento nello stesso database (ACID)
app.post('/orders', async (req, res) => {
  const transaction = db.beginTransaction();

  try {
    // Step 1: Create order
    const order = await Order.create(req.body, { transaction });

    // Step 2: Registra evento nel outbox (STESSO transaction)
    await Outbox.create({
      event_type: 'order.created',
      aggregate_id: order.id,
      payload: { order_id: order.id, ...order }
    }, { transaction });

    // ATOMICO: entrambi succedeono o entrambi falliscono
    await transaction.commit();

    res.status(201).json(order);
  } catch (error) {
    await transaction.rollback();
    res.status(500).json({ error: 'Failed to create order' });
  }
});

// Separate service: pubblica eventi dal outbox
async function publishOutboxEvents() {
  const unpublished = await Outbox.find({ published: false });

  for (const event of unpublished) {
    try {
      await eventBus.publish(event.event_type, event.payload);
      await event.update({ published: true, published_at: new Date() });
    } catch (error) {
      // Retry alla prossima iterazione
      console.error('Failed to publish event', event.id, error);
    }
  }
}

setInterval(publishOutboxEvents, 5000); // Ogni 5 secondi
```

---

## 13. Testing API

### 13.1 Contract Testing

Verificare che client e server concordino sul formato:

```javascript
// Provider test (server)
const { Pact } = require('@pact-foundation/pact');

const provider = new Pact({
  consumer: 'OrderClient',
  provider: 'OrderService'
});

describe('Order API Contract', () => {
  it('returns order details', () => {
    return provider
      .addInteraction({
        state: 'order 123 exists',
        uponReceiving: 'a request for order details',
        withRequest: {
          method: 'GET',
          path: '/orders/123'
        },
        willRespondWith: {
          status: 200,
          body: {
            id: 123,
            status: 'pending',
            total: Matchers.number()
          }
        }
      })
      .then(() => {
        return fetch('/orders/123')
          .then(res => res.json())
          .then(order => {
            expect(order.id).toEqual(123);
          });
      });
  });
});
```

### 13.2 Mock API Server

```javascript
const mockServer = require('json-server').create();
const router = require('json-server').router('./db.json');

// GET /users → returns all users
// GET /users/123 → returns user with id 123
// POST /users → creates new user
// PUT /users/123 → replaces user
// DELETE /users/123 → deletes user

mockServer.use(router);
mockServer.listen(3000);

// In tests
const response = await fetch('http://localhost:3000/users/123');
const user = await response.json();
```

---

## 14. Documentazione API

### 14.1 OpenAPI/Swagger Specification

Standard universale per documentare API:

```yaml
openapi: 3.0.0
info:
  title: Order API
  version: 1.0.0

paths:
  /orders:
    get:
      summary: List all orders
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
            default: 10
        - name: offset
          in: query
          schema:
            type: integer
            default: 0
      responses:
        200:
          description: List of orders
          content:
            application/json:
              schema:
                type: object
                properties:
                  orders:
                    type: array
                    items:
                      $ref: '#/components/schemas/Order'
    post:
      summary: Create new order
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateOrderRequest'
      responses:
        201:
          description: Order created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Order'

components:
  schemas:
    Order:
      type: object
      properties:
        id:
          type: integer
        status:
          type: string
          enum: [pending, confirmed, shipped, delivered]
        total:
          type: number
        created_at:
          type: string
          format: date-time
    CreateOrderRequest:
      type: object
      required:
        - customer_id
        - items
      properties:
        customer_id:
          type: integer
        items:
          type: array
          items:
            type: object
            properties:
              product_id:
                type: integer
              quantity:
                type: integer
```

### 14.2 API Documentation Best Practices

```markdown
# Orders API

## Authentication

All endpoints require API key in header:

```
X-API-Key: sk_live_abc123
```

## Rate Limiting

- 100 requests per minute per API key
- See `X-RateLimit-*` headers for usage

## Errors

All errors return standardized format:

```json
{
  "error": "invalid_request",
  "message": "User-friendly description",
  "code": "INVALID_EMAIL"
}
```

## Endpoints

### List Orders

```
GET /orders?limit=10&offset=0
```

#### Response

```json
{
  "orders": [...],
  "pagination": {
    "limit": 10,
    "offset": 0,
    "total": 500
  }
}
```

### Create Order

```
POST /orders
```

#### Request Body

```json
{
  "customer_id": 42,
  "items": [
    {"product_id": 1, "quantity": 2}
  ]
}
```

#### Response

```json
{
  "id": 9999,
  "status": "pending",
  "created_at": "2026-04-01T14:32:00Z"
}
```
```

---

## 15. Changelog

### Versione 1.0 (Aprile 2026)

- Pubblicazione iniziale
- Copertura: 15 pattern API universali
- Esempi in: JavaScript, Python, curl, SQL
- Focus: Principi dogmatici, mai platform-specific

---

## Fonti e Riferimenti

Questo documento si basa su standard e best practices universali:

- [REST API Best Practices](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)
- [OAuth 2.0 Security Best Practices](https://www.authgear.com/post/oauth2-security-best-practices-pkce-state)
- [PKCE - Proof Key for Code Exchange](https://auth0.com/docs/get-started/authentication-and-authorization-flow/authorization-code-flow-with-pkce)
- [API Rate Limiting Algorithms](https://api7.ai/blog/rate-limiting-guide-algorithms-best-practices)
- [Exponential Backoff and Jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/)
- [Webhook Delivery Best Practices](https://dev.to/young_gao/building-reliable-webhook-delivery-retries-signatures-and-failure-handling-40ff)
- [HMAC Webhook Signatures](https://docs.github.com/en/webhooks/using-webhooks/validating-webhook-deliveries)
- [API Pagination Strategies](https://www.stainless.com/sdk-api-best-practices/how-to-implement-rest-api-pagination-offset-cursor-keyset)
- [Idempotency in Distributed Systems](https://stripe.com/blog/idempotency)
- [Circuit Breaker Pattern](https://aws.amazon.com/prescriptive-guidance/cloud-design-patterns/circuit-breaker/)
- [API Versioning Best Practices](https://redocly.com/blog/api-versioning-best-practices)
- [OWASP API Security Top 10](https://www.pynt.io/learning-hub/owasp-top-10-guide/owasp-api-top-10)
- [SLI, SLO, SLA Definitions](https://signoz.io/guides/slo-vs-sla/)
- [Distributed Tracing and Observability](https://opentelemetry.io/docs/concepts/observability-primer/)
- [Semantic Versioning](https://semver.org/)

---

**Fine Documento**

Questo documento è una guida vivente. Gli aggiornamenti rifletteranno l'evoluzione dei principi universali — non le mode passeggere.