# Make.com — Architettura Tecnica Completa

## Informazioni Generali

| Attributo | Dettagli |
|-----------|----------|
| **Nome Piattaforma** | Make.com (precedentemente Integromat) |
| **Sito Ufficiale** | https://www.make.com |
| **Documentazione Ufficiale** | https://help.make.com |
| **Developer Hub** | https://developers.make.com |
| **API Documentation** | https://developers.make.com/api-documentation |
| **Integrations** | https://www.make.com/en/integrations |
| **Fondazione** | 2012 (come Integromat) |
| **Acquisizione Celonis** | Ottobre 2020 |
| **Rebranding Make** | Febbraio 2022 |
| **Headquarter** | Repubblica Ceca (sviluppo), Berlino (Celonis) |
| **Società Madre** | Celonis (2020-presente) |
| **Modelli di Prezzo** | Free, Core, Pro, Teams, Enterprise |
| **Integrations Disponibili** | 3.000+ app pre-built, HTTP/API universale |
| **Certificazioni** | SOC 2 Type II, SOC 3, ISO 27001, GDPR |
| **Lingue Supportate** | 30+ lingue |

---

## 1. Architettura della Piattaforma

### 1.1 Concetti Fondamentali

Make è una piattaforma di automazione di processi basata sul cloud che opera secondo tre concetti architetturali principali:

**Bundle (Unità di Dati)**
Il bundle rappresenta una singola unità di dati elaborata da uno scenario. Esempi di bundle includono:
- Una riga di un foglio Google Sheets
- Un singolo record di un database
- Un'email ricevuta
- Un oggetto JSON da un webhook

**Operation (Operazione)**
Un'operazione rappresenta una singola esecuzione completata di un modulo. Per ogni modulo eseguito, viene conteggiata almeno 1 operazione. Se un modulo elabora 5 bundle, consuma 5 operazioni (con conseguenti crediti di fatturazione dopo il cambio di agosto 2025).

**Execution (Esecuzione)**
Un'esecuzione è un'intera elaborazione di uno scenario, dal trigger iniziale fino al completamento finale. Una singola esecuzione può generare multiple operazioni se i moduli elaborano array di dati.

### 1.2 Modello di Esecuzione Sequenziale

Make segue un modello **sequenziale puro** con supporto per parallelizzazione tramite router:

```
Trigger → [Init] → Module 1 → [Process] → Module 2 → [Commit] → Fine
           └─ Validation ─┘                              └─ Cleanup ─┘
```

Ogni modulo passa attraverso 4 fasi per ogni bundle:
1. **Initialization (Init)**: Setup connessioni e validazione
2. **Operation (Process)**: Elaborazione effettiva del bundle
3. **Commit/Rollback**: Conferma transazionale
4. **Finalization**: Cleanup risorse

### 1.3 Cicli e Moltiplicazione di Bundle

Quando un modulo restituisce un array, ciascun elemento del bundle successivo crea un "ciclo":

```
Modulo 1: [1 bundle input]
  ↓ (restituisce array di 3 elementi)
Modulo 2: [3 bundle - 1 per elemento]
  ↓ (ogni bundle moltiplicato × 2)
Modulo 3: [6 bundle totali]
```

**Impatto su Operazioni:**
Operazioni = Σ(moduli × bundle che elaborano)

### 1.4 Struttura Organizzativa

Make implementa una gerarchia a **due livelli**:

**Organization (Organizzazione)**
- Contenitore top-level
- Rappresenta tipicamente una azienda/cliente
- Piano di prezzo separato
- Fatturazione centralizzata
- Limite di team per organizzazione

**Team (Team)**
- Sub-livello per isolamento e accesso
- Ogni team ha:
  - Elenco di scenari
  - Connessioni condivise
  - Data stores
  - Webhook
  - API keys
  - Funzioni custom
  - Permessi granulari

Un utente può appartenere a multiple team all'interno della stessa organizzazione con ruoli differenti.

### 1.5 Flusso di Dati Architetturale

```
┌─────────────┐
│   Trigger   │ (Instant/Polling/Scheduled)
└──────┬──────┘
       │
       ▼
┌─────────────────┐
│   Webhook Queue │ (se trigger istantaneo)
│   (30 webhook/s)│
└──────┬──────────┘
       │
       ▼
┌──────────────────────┐
│  Scenario Execution  │
│  (Sequential Flow)   │
└──────┬───────────────┘
       │
       ├─→ [Router] ──→ Parallel Paths
       │
       ├─→ [Iterator] ──→ Array to Bundles
       │
       ├─→ [Modules] ──→ Transformations
       │
       └─→ [Aggregator] ──→ Bundles to Array
       │
       ▼
┌──────────────────────┐
│  Error Handler       │
│  (Resume/Commit/etc) │
└──────┬───────────────┘
       │
       ▼
┌──────────────────────┐
│  Incomplete Queue    │
│  (30 giorni Free)    │
└──────────────────────┘
```

### 1.6 Infrastruttura e Regioni

Make offre hosting in **multiple regioni geografiche**:
- **US East** (us-east-1)
- **EU West** (eu-west-1)
- **Asia Pacific** (ap-southeast-1)
- **Personalizzato per Enterprise** (AWS dedicato)

La latenza media di elaborazione è < 100ms per operazioni semplici.

---

## 2. Architettura degli Scenario

### 2.1 Componenti di uno Scenario

Un scenario Make è composto da:

```
┌─────────────────────────────────────┐
│         SCENARIO MAIN FLOW          │
├─────────────────────────────────────┤
│ Trigger Module                      │
│ (Instant/Polling/Scheduled)         │
└────────────┬────────────────────────┘
             │
┌────────────▼────────────────────────┐
│ Router (Optional)                   │
│ (Branching logico con filtri)       │
├─┬──────────────────┬──────────────┬─┤
│ │ Route 1 (If X)   │ Route 2 (If Y)│ │
│ │ ...modules...    │ ...modules... │ │
│ └──────────────────┴──────────────┘ │
└────────────┬────────────────────────┘
             │
┌────────────▼────────────────────────┐
│ Iterator (Optional)                 │
│ (Array → Multiple Bundles)          │
└────────────┬────────────────────────┘
             │
┌────────────▼────────────────────────┐
│ Action/Transform Modules            │
│ (Sequential: 1, 2, 3, ...)         │
└────────────┬────────────────────────┘
             │
┌────────────▼────────────────────────┐
│ Aggregator (Optional)               │
│ (Multiple Bundles → Array)          │
└────────────┬────────────────────────┘
             │
┌────────────▼────────────────────────┐
│ Final Action/Response               │
│ (Output o Webhook Response)         │
└─────────────────────────────────────┘
```

### 2.2 Tipi di Moduli

Make supporta **6 tipi architetturali di moduli**:

| Tipo | Funzione | Esempi | Bundle Output |
|------|----------|--------|---------------|
| **Trigger** | Inizio scenario | Webhook, Schedule, Watch Emails | 1+ per evento |
| **Action** | Esecuzione azione | Send Email, Create Record | 1 per bundle |
| **Search** | Query dati | Find User, List Records | 1 per bundle |
| **Iterator** | Esplosione array | Loop Items | 1 per elemento |
| **Aggregator** | Implosione bundles | Combine to Array | 1 finale |
| **Transformer** | Riformattazione | Text, JSON, Map | 1 per bundle |

### 2.3 Ordine di Esecuzione

Make segue **ordine di esecuzione rigorosamente sequenziale** (left-to-right, top-to-bottom):

```
Scenario Start
  ↓
Trigger Execution
  ↓
Module 1 (per ogni bundle)
  ↓
Module 2 (per ogni bundle in uscita da M1)
  ↓
Module 3 (per ogni bundle in uscita da M2)
  ↓
Error Handler (se presente errore)
  ↓
Scenario End
```

**Eccezione:** Router crea percorsi paralleli logici, ma l'esecuzione rimane sequenziale per singolo bundle.

### 2.4 Concetto di Bundle e Cicli

**Ciclo Semplice:**
```
Input: 1 bundle
Module A: Process → 1 bundle
Module B: Process → 1 bundle
Output: 1 bundle
Operazioni: 2
```

**Ciclo con Array:**
```
Input: 1 bundle {items: [a, b, c]}
Iterator: Explode array → 3 bundle
Module A: Process × 3 → 3 bundle
Aggregator: Combine → 1 bundle
Output: 1 bundle {items: [x, y, z]}
Operazioni: 3 (Iterator + 3×A)
```

### 2.5 Pannello di Mapping e Data Flow

Il **mapping panel** è il core della trasformazione dati:

```
┌─ Module Configuration
│
├─ Input Mapping
│  ├─ {{trigger.data.id}}
│  ├─ {{previousModule.output.name}}
│  └─ {{variable.myVar}}
│
├─ IML Expressions
│  ├─ {{toLower(input)}}
│  ├─ {{formatDate(now, 'YYYY-MM-DD')}}
│  └─ {{splitText(text, ',')}}
│
└─ Output Schema
   ├─ Field 1: {{input.field1}}
   ├─ Field 2: {{calculateValue}}
   └─ Array: {{input.items}}
```

Ogni modulo output diventa disponibile per mapping in moduli downstream:
```
trigger. → Module1. → Module2. → ...
```

### 2.6 Filtri tra Moduli

I filtri permettono **filtraggio bundle** senza consumare operazioni nel modulo successivo:

```
Module A
  ↓
[FILTER: 1 = 1]
  ├─ TRUE → Module B (Consuma 1 op)
  └─ FALSE → Skip (0 operazioni)
```

**Condizioni disponibili:**
- `equals`, `does not equal`
- `contains`, `does not contain`
- `greater than`, `less than`
- `between`, `is empty`
- Operatori logici: `AND`, `OR`, `NOT`

---

## 3. Trigger e Webhook

### 3.1 Tipologie di Trigger

Make supporta **3 categorie architetturali di trigger**:

#### A) Instant Triggers (Webhook-based)

**Caratteristiche:**
- Event-driven: esecuzione istantanea al ricevimento webhook
- Latenza: < 100ms tipicamente
- Elaborazione: parallela (ogni webhook non aspetta fine precedente)
- Coda: gestita internamente da Make
- Payload: fino a 100MB per webhook
- Rate limit: 30 webhook/secondo per default

**Tipi:**
- App-specific webhooks (es: Stripe payment_intent.succeeded)
- Custom webhooks (URL personalizzato creato in Make)
- Response webhooks (webhook con response HTTP)

**Architettura:**
```
Client/App
  │
  └─→ Make Webhook URL: https://hook.make.com/xxxxx
       │
       └─→ Webhook Queue (thread-safe, FIFO)
            │
            └─→ Scenario Execution (parallel)
                 │
                 └─→ Complete & Store Result
```

#### B) Polling Triggers

**Caratteristiche:**
- Time-based: interrogazione periodica del servizio esterno
- Intervallo minimo: 15 minuti (Free), 1 minuto (Core+)
- Latenza: 15-60 secondi (dipende intervallo)
- Overhead: 1 operazione per polling anche se no nuovi dati
- Stato: Make traccia timestamp ultimo fetch

**Implementazione:**
```
Scheduler (Make)
  │
  ├─ Every 15 min: Check Slack for new messages
  │   └─ Call API: GET /conversations.history?oldest=X
  │       ├─ Se 3 nuovi messaggi → Trigger 3 volte
  │       └─ Se no nuovi → 1 operazione "wasted"
```

#### C) Scheduled Triggers

**Caratteristiche:**
- Time-based: esecuzione a orario specifico o ricorrente
- Intervallo minimo: come polling trigger
- Comportamento: sempre esecuzione anche senza nuovi dati
- Use case: report giornalieri, pulizia dati periodica

**Formati supportati:**
- Every X minutes/hours/days
- Specific time (HH:MM)
- Cron expression (Pro+): `0 9 * * 1-5` (9am weekdays)

### 3.2 Webhook Architecture

**Webhook URL Anatomy:**
```
https://hook.make.com/ABC123XYZ?name=value&override=param

Componenti:
├─ hook.make.com: endpoint webhook globale
├─ ABC123XYZ: hook ID (unique per webhook)
└─ Query params: dati aggiuntivi (opzionali)
```

**Webhook Queue Internals:**
```
┌─────────────────────┐
│ Webhook Received    │
│ Timestamp: T0       │
└────────┬────────────┘
         │
┌────────▼────────────┐
│ Queue Storage       │
│ (FIFO, in-memory)   │
│ Max: 50 pending     │
└────────┬────────────┘
         │
    ┌────┴────┐
    │ Processing│
    │ (Parallel)│
    └────┬────┘
         │
┌────────▼────────────┐
│ Execution Result    │
│ Stored 30 days (Free)│
│ Stored longer (Paid) │
└─────────────────────┘
```

Se coda raggiunge 50 elementi: `HTTP 400 - Queue is full`

### 3.3 Webhook Response

**Response Webhook Pattern:**
```
Client
  │
  └─→ POST /hook/make/url
       │
       ├─ Scenario starts executing
       │
       ├─ Response Webhook Module
       │  └─ Sends HTTP response immediately
       │
       └─ Scenario continues (asynchronously)
```

Utile per: REST APIs, form submissions, external confirmations.

### 3.4 Confronto Trigger - Tabella

| Caratteristica | Instant | Polling | Scheduled |
|---|---|---|---|
| Latenza | < 100ms | 15-60sec | A orario |
| Operazioni "wasted" | No | Sì (se no nuovi dati) | Sempre 1 |
| Rate limit | 30/sec | Intervallo min | Intervallo min |
| Setup | Complex | Simple | Very simple |
| Costo operazioni | Basso | Alto (polling) | Predittibile |
| Ideal for | Real-time | Compatibility | Batch ops |

---

## 4. Router, Iterator, Aggregator — Flussi Avanzati

### 4.1 Router — Branching Condizionale

Il **Router** è modulo di flow control che crea **percorsi paralleli condizionali**:

```
Router Input (1 bundle)
  │
  ├─ Route 1 [IF condition1] → Module A → Module B
  │                             ↓
  │   Output: 1 bundle
  │
  ├─ Route 2 [IF condition2] → Module C → Module D
  │                             ↓
  │   Output: 1 bundle
  │
  └─ Route 3 [ELSE] → Module E
                       ↓
      Output: 1 bundle

Total Output: 1-3 bundle (a seconda route matching)
```

**Architettura Interna:**
```
Configurazione Router:
├─ Route 1
│  ├─ Condition: {{bundleData.status}} equals "active"
│  ├─ Modules: [M1, M2, M3]
│  └─ Fallback: None
│
├─ Route 2
│  ├─ Condition: {{bundleData.status}} contains "pending"
│  ├─ Modules: [M4, M5]
│  └─ Fallback: None
│
└─ Route 3 (Fallback)
   ├─ Condition: Always True
   ├─ Modules: [M6]
   └─ Execution: Se nessuna route precedente matched
```

**Filtering behavior:**
- Se un bundle non soddisfa **nessuna** route: viene scartato
- Se un bundle soddisfa **multiple** route: esegue tutti i match sequenzialmente
- Operazioni contate: 1 per ogni modulo in ogni route eseguita

**Operazioni example:**
```
Router + 3 route (A=2mod, B=3mod, C=1mod)
Input: 1 bundle

Scenario: Matches route A e C
Output bundles: 2 (1 da A, 1 da C)
Operazioni: 2 (from A) + 1 (from C) = 3 ops
```

### 4.2 Iterator — Array Explosion

L'**Iterator** converte un **array in multiple bundle**, uno per elemento:

```
Input Bundle:
{
  id: 1,
  items: ["apple", "banana", "cherry"]
}

Iterator Configuration:
├─ Array to iterate: {{inputBundle.items}}
└─ Process each item

Output:
├─ Bundle 1: {item: "apple"}
├─ Bundle 2: {item: "banana"}
└─ Bundle 3: {item: "cherry"}
```

**Operazioni:**
```
Iterator (1 op per bundle input)
  ↓
Module A (1 op per bundle output da iterator)
  ↓
Module B (1 op per bundle output da A)

Total: 1 (iter) + 3 (A) + 3 (B) = 7 ops
```

**Caso avanzato - Nested arrays:**
```
{
  orders: [
    {id: 1, items: [a, b]},
    {id: 2, items: [c, d, e]}
  ]
}

Iterator su orders: 2 bundle
  ↓
Iterator su items: 2 + 3 = 5 bundle

Output: 5 bundle separati
```

### 4.3 Aggregator — Array Implosion

L'**Aggregator** combina **multiple bundle in un array unico**:

#### Array Aggregator

```
Input: 3 bundle
├─ Bundle 1: {id: 1, name: "Alice", amount: 100}
├─ Bundle 2: {id: 2, name: "Bob", amount: 200}
└─ Bundle 3: {id: 3, name: "Charlie", amount: 300}

Array Aggregator Configuration:
├─ Source module: Module X
├─ Mapping:
│  ├─ id: {{id}}
│  ├─ name: {{name}}
│  └─ amount: {{amount}}

Output: 1 bundle
{
  "array": [
    {id: 1, name: "Alice", amount: 100},
    {id: 2, name: "Bob", amount: 200},
    {id: 3, name: "Charlie", amount: 300}
  ]
}
```

**Operazioni:**
```
Iterator (3 bundle output): 1 op
Module A (×3 bundle): 3 ops
Aggregator: 1 op (attende tutti i bundle)
Downstream module: 1 op (su array)

Total: 1 + 3 + 1 + 1 = 6 ops
```

#### Text Aggregator

Concatena stringhe da multiple bundle:
```
Input bundles: ["hello", "world", "!"]
Output: "hello world !"
```

#### Table Aggregator

Crea tabella (array of objects) con ordinamento:
```
Input: 3 bundle con {name, score}
Output:
[
  {name: "Alice", score: 95},
  {name: "Bob", score: 87},
  {name: "Charlie", score: 92}
]
Ordinabile per: name, score ASC/DESC
```

#### Numeric Aggregator

Aggregazione numerica:
```
Funzioni: SUM, AVERAGE, MIN, MAX, COUNT, PRODUCT
Input: 3 bundle {amount: [100, 200, 300]}
SUM: 600
AVERAGE: 200
MIN: 100
MAX: 300
```

### 4.4 Pattern Avanzati: Fan-Out / Fan-In

**Fan-Out (1 → Many):**
```
Single input
  │
  ├─→ Router/Iterator
  │   ├─ Path A: Module1 → Module2
  │   ├─ Path B: Module3 → Module4
  │   └─ Path C: Module5
  │
Multiple outputs/branches
```

**Fan-In (Many → 1):**
```
Multiple inputs/branches
  │
  ├─→ Aggregator
  │   (Combines into array)
  │
Single consolidated output
```

**Esempio combinato:**
```
Trigger: Ricevi lista order IDs

Iterator su IDs
  ↓ (3 bundle)

Router per status:
├─ Route "pending": Send Email
└─ Route "shipped": Send SMS

Aggregator: Raccogli risposte

Final: Log all notifications
```

---

## 5. Gestione dei Dati

### 5.1 Tipi di Dati Supportati

Make supporta i seguenti tipi di dato **nativamente**:

| Tipo | Esempio | Note |
|------|---------|------|
| **Text** | "hello", "ciao123" | String illimitata |
| **Number** | 42, 3.14, -100 | Integer, Float, Big Integer |
| **Boolean** | true, false | Logico |
| **Date** | 2024-01-15 | ISO 8601 |
| **DateTime** | 2024-01-15T09:30:00Z | ISO 8601 con timezone |
| **Array** | [1, 2, 3], ["a", "b"] | Collezione omogenea |
| **Collection** | {key: value, ...} | Oggetto eterogeneo |
| **Buffer** | Binary file data | File binari, immagini, PDF |
| **Any** | Tipo dinamico | Any JSON structure |

### 5.2 IML — Integromat Markup Language

IML è il **linguaggio di espressione** di Make per trasformazioni dati:

```
Sintassi base:
{{expression}}

Esempi:
├─ {{variable}}                    // Reference semplice
├─ {{module.output.field}}        // Nested property
├─ {{toLower(text)}}              // Function call
├─ {{add(a, b)}}                  // Math operation
└─ {{if(condition; true; false)}} // Ternary
```

#### 5.2.1 Funzioni String

```javascript
{{toLowerCase(text)}}              // "HELLO" → "hello"
{{toUpperCase(text)}}              // "hello" → "HELLO"
{{capitalize(text)}}               // "hello" → "Hello"
{{contains(text; "substring")}}    // true/false
{{startsWith(text; "prefix")}}     // true/false
{{endsWith(text; "suffix")}}       // true/false
{{split(text; ",")}}               // "a,b,c" → ["a", "b", "c"]
{{join(array; ",")}}               // ["a", "b"] → "a,b"
{{substring(text; 0; 3)}}          // text[0:3]
{{replace(text; "old"; "new")}}    // String replace
{{trim(text)}}                     // Remove whitespace
{{length(text)}}                   // String length
{{concat(str1; str2; str3)}}       // String concatenation
{{md5(text)}}                      // MD5 hash
{{sha1(text)}}                     // SHA1 hash
{{sha256(text)}}                   // SHA256 hash
{{base64(text)}}                   // Base64 encoding
{{base64Decode(encoded)}}          // Base64 decoding
{{urlEncode(text)}}                // URL encoding
{{htmlEntityEncode(text)}}         // HTML entity encoding
```

#### 5.2.2 Funzioni Math

```javascript
{{add(a; b)}}                      // Addition: 2 + 3 = 5
{{subtract(a; b)}}                 // Subtraction: 5 - 2 = 3
{{multiply(a; b)}}                 // Multiplication: 3 × 4 = 12
{{divide(a; b)}}                   // Division: 12 ÷ 3 = 4
{{modulo(a; b)}}                   // Modulo: 7 % 3 = 1
{{round(number)}}                  // Round to nearest int
{{ceil(number)}}                   // Round up
{{floor(number)}}                  // Round down
{{min(a; b; c)}}                   // Minimum value
{{max(a; b; c)}}                   // Maximum value
{{average(a; b; c)}}               // Average
{{abs(number)}}                    // Absolute value
{{sqrt(number)}}                   // Square root
{{pow(base; exponent)}}            // Power: 2^3 = 8
```

#### 5.2.3 Funzioni Date

```javascript
{{now}}                            // Current timestamp: 1708012345
{{formatDate(date; "YYYY-MM-DD")}} // Format: "2024-02-15"
{{parseDate(text; "YYYY-MM-DD")}}  // Parse string to date
{{addDays(date; 5)}}               // Date + 5 days
{{addMonths(date; 1)}}             // Date + 1 month
{{addHours(date; 2)}}              // Date + 2 hours
{{datedif(date1; date2; "days")}}  // Difference in days
{{getDayOfWeek(date)}}             // 0=Sun, 6=Sat
{{getDateOfISOWeek(year; week)}}   // ISO week to date
{{timestamp}}                      // Current Unix timestamp
{{addSeconds(date; 30)}}           // Date + 30 seconds
{{betweenDates(date1; date2)}}     // Milliseconds between
```

#### 5.2.4 Funzioni Array

```javascript
{{length(array)}}                  // Array length
{{first(array)}}                   // First element
{{last(array)}}                    // Last element
{{nth(array; index)}}              // Element at index
{{indexOf(array; value)}}          // Index of value
{{join(array; ",")}}               // Convert to string
{{reverse(array)}}                 // Reverse array
{{unique(array)}}                  // Remove duplicates
{{flatten(array)}}                 // Flatten nested arrays
{{compact(array)}}                 // Remove null/empty
{{map(array; expression)}}         // NOT native - use iterator
{{filter(array; condition)}}       // NOT native - use router
{{sort(array; "field")}}           // Sort by field
{{range(1; 10)}}                   // [1, 2, 3, ..., 10]
{{isEmpty(array)}}                 // Check if empty
```

#### 5.2.5 Funzioni Generali

```javascript
{{if(condition; trueValue; falseValue)}}  // Ternary operator
{{empty(value)}}                          // Check if null/empty
{{coalesce(v1; v2; v3)}}                  // First non-null
{{default(value; defaultVal)}}            // Fallback value
{{equals(a; b)}}                          // Equality
{{notEquals(a; b)}}                       // Inequality
{{gt(a; b)}}                              // Greater than
{{gte(a; b)}}                             // Greater or equal
{{lt(a; b)}}                              // Less than
{{lte(a; b)}}                             // Less or equal
{{and(cond1; cond2)}}                     // Logical AND
{{or(cond1; cond2)}}                      // Logical OR
{{not(condition)}}                        // Logical NOT
{{type(value)}}                           // Type of value
{{keys(object)}}                          // Object keys array
{{values(object)}}                        // Object values array
```

### 5.3 Mapping Avanzato

**Pattern 1: Conditional Mapping**
```
{{if(status = "active";
     emailAddress;
     defaultEmail)}}
```

**Pattern 2: Array Transformation** (via setter variable)
```
Mapping in Array Aggregator:
└─ Field: id = {{id}}
└─ Field: upper_name = {{toUpperCase(name)}}
└─ Field: full_date = {{formatDate(createdAt; "DD/MM/YYYY")}}
```

**Pattern 3: Data Type Conversion**
```
{{toString(number)}}               // 42 → "42"
{{toNumber(text)}}                 // "42" → 42
{{parseJson(jsonString)}}          // JSON parsing
{{toString(jsonObject)}}           // JSON stringification
```

### 5.4 JSON, XML, CSV Handling

#### JSON Processing

**JSON Module - Parse:**
```
Input: {raw_json: '{"name":"Alice","age":30}'}
↓
JSON Parser Module
├─ Set JSON to parse: {{raw_json}}
└─ Output:
   ├─ name: "Alice"
   └─ age: 30
```

**JSON Module - Create:**
```
Create JSON:
├─ Object structure:
│  ├─ name: {{firstName}}
│  ├─ email: {{emailAddress}}
│  ├─ metadata:
│  │  ├─ created: {{now}}
│  │  └─ tags: [{{tag1}}, {{tag2}}]
│  └─ active: true
│
Output: {"name":"...", "email":"...", "metadata":{...}}
```

#### XML Handling

```
// Non è nativo - usa HTTP module con parser XML:
1. Fetch XML via HTTP GET
2. Parse response as XML
3. Map XML properties: {{xmlResponse.root.element.value}}
4. Create response XML:
   {{replace(baseXML; "{{PLACEHOLDER}}"; value)}}
```

#### CSV Processing

```
CSV Module - Parse:
├─ CSV data: {{httpResponse}}
├─ Column headers: Auto-detect
└─ Output: Array of objects
   [{col1: "val1", col2: "val2"}, ...]

CSV Module - Create:
├─ Source array: {{arrayData}}
├─ Column mapping:
│  ├─ ID: {{id}}
│  ├─ Name: {{name}}
│  └─ Email: {{email}}
└─ Output: CSV string
   "ID,Name,Email\n1,Alice,alice@..."
```

---

## 6. Connessioni e Autenticazione

### 6.1 Tipi di Connessione

Make supporta **7 architetture di autenticazione**:

| Tipo | Meccanismo | Refresh | Use Case |
|------|-----------|---------|----------|
| **OAuth 2.0** | Token + Refresh | Auto | SaaS (Slack, Google) |
| **API Key** | Static token | Manual | Stripe, SendGrid |
| **Basic Auth** | Username:Password | No | Legacy APIs |
| **Bearer Token** | Static JWT | Manual | Custom APIs |
| **Custom Headers** | X-API-Key, ecc | Varies | Proprietary |
| **Session/Cookie** | Session ID | No | Web apps |
| **mTLS** | Client certificate | No | Enterprise |

### 6.2 Gestione delle Connessioni

**Architecture:**
```
Team
├─ Connections (Shared across scenarios)
│  ├─ Slack (OAuth2) → token_slack_xxx
│  ├─ Stripe (API Key) → sk_live_xxx (encrypted)
│  ├─ Gmail (OAuth2) → token_gmail_yyy
│  └─ Custom API (Bearer) → bearer_token_zzz
│
└─ Scenarios can reference:
   └─ Use connection "Slack" in 500 scenarios
```

**Token Lifecycle:**
```
OAuth2 Flow:
1. User authorizes app
2. Make receives refresh_token + access_token
3. Tokens stored encrypted at rest
4. Token expiry monitored
5. Auto-refresh before expiry
6. If refresh fails → Connection errors

API Key Flow:
1. User provides key
2. Encrypted storage
3. No auto-refresh
4. Manual rotation required
```

### 6.3 Condivisione e Gestione Connessioni

**Team-level sharing:**
```
Team A
├─ Connection: Salesforce CRM
│  └─ Authorized to: 20 scenarios
│  └─ Users: Can reference but not modify
│
Team B
├─ Different Salesforce account
│  └─ Authorized independently
```

**Revoking connections:**
- Remove authorization in app settings
- Make invalidates immediately
- Existing scenarios fail with auth error
- Error handler catches e notifica

### 6.4 Multi-account Management

Per account multipli dello stesso servizio:
```
Team Setup:
├─ Connection: Gmail Account 1 (alice@...)
├─ Connection: Gmail Account 2 (bob@...)
├─ Connection: Gmail Account 3 (support@...)
│
Scenario usage:
└─ Module "Send Email"
   ├─ Use connection: Gmail Account 1
   └─ Or use: Gmail Account 2 (per routing)
```

---

## 7. Data Stores — Persistenza Dati

### 7.1 Architettura NoSQL Key-Value

Make Data Stores sono implementati come **NoSQL key-value store** con caratteristiche:

**Struttura:**
```
Data Store: "users_cache"
├─ Key: "user_123" → Value: {name: "Alice", email: "alice@...", updated: 1708012345}
├─ Key: "user_456" → Value: {name: "Bob", email: "bob@...", updated: 1708012346}
├─ Key: "config_main" → Value: {version: "1.2", stage: "production"}
└─ Key: "session_xyz" → Value: {token: "...", expires: 1708099999}
```

**Caratteristiche:**
- O(1) lookup per chiave (constant time)
- Storage: JSON objects come valori
- Size limit per team:
  - Free: 5MB
  - Core/Pro: 100MB
  - Teams/Enterprise: 500MB+
- Retention: Unlimited (fino a limite size)

### 7.2 Operazioni CRUD

**CREATE/UPSERT:**
```
Data Store Module: Set a value
├─ Data store: "users_cache"
├─ Key: {{userId}}
├─ Value:
│  {
│    "name": {{firstName}} {{lastName}},
│    "email": {{email}},
│    "status": "active",
│    "updated": {{now}}
│  }
└─ Result: {success: true}
```

**READ:**
```
Data Store Module: Get a value
├─ Data store: "users_cache"
├─ Key: {{userId}}
└─ Output:
   {
     "name": "Alice Smith",
     "email": "alice@...",
     "status": "active",
     "updated": 1708012345
   }
```

**UPDATE (Partial):**
```
// Non esiste "partial update" nativo
// Workaround:
1. Read value: {{oldValue}}
2. Modify in mapper: {{merge(oldValue; {status: "inactive"})}}
3. Write complete value: Set a value
```

**DELETE:**
```
Data Store Module: Delete a value
├─ Data store: "users_cache"
├─ Key: {{userId}}
└─ Result: {success: true}
```

**LIST (Con filtri):**
```
Data Store Module: Get all values
├─ Data store: "users_cache"
├─ Filter condition: {{value.status = "active"}}
├─ Sort: By key DESC
└─ Output: Array
   [{key: "user_123", value: {...}}, ...]
```

### 7.3 Locking e Concorrenza

**Race condition scenario:**
```
Thread 1                          Thread 2
└─ Read: value=10                 └─ (simultaneous)
   └─ +5 = 15                         └─ Read: value=10
      └─ Write: 15                       └─ +3 = 13
                                           └─ Write: 13
                                              (Overwrites 15)

Result: Lost update! Should be 18, actually 13
```

**Solution - Lock mechanism:**
```
// Make does NOT have native pessimistic locking
// Workaround: Version-based optimistic locking

Read with version:
├─ Key: "user_123"
├─ Value: {count: 10, version: 1}

Update:
├─ IF current version = 1:
│  └─ Write {count: 15, version: 2}
└─ ELSE:
   └─ Retry (version mismatch)
```

### 7.4 Use Cases Comuni

**1. State Management:**
```
Scenario for order processing:
├─ Trigger: New order from Shopify
├─ Data Store Read: "order_state_{{orderId}}"
│  └─ Check if already processed
├─ Process order (payments, fulfillment)
├─ Data Store Write: "order_state_{{orderId}}" = {status: "completed", timestamp: now}
└─ Avoid duplicates if webhook retried
```

**2. Deduplication:**
```
Data Store: "processed_events"
├─ Key: {{eventId}}
├─ Read before processing:
│  └─ If exists: Skip (already processed)
│  └─ If not exists: Continue
├─ Process event
├─ Write to store: Mark as processed
```

**3. Caching/Lookup:**
```
Data Store: "exchange_rates"
├─ Keys: "USD_EUR", "EUR_GBP", ecc
├─ Values: {rate: 0.92, updated: 1708012345}
├─ On each scenario:
│  └─ Check cache before API call
│  └─ If stale (> 24h): Refresh
```

**4. Configuration Management:**
```
Data Store: "config"
├─ Keys: "app_version", "max_retries", "api_timeout"
├─ Values: {value: "2.1.3"}, {value: 5}, {value: 30000}
├─ Scenarios reference config centrally
├─ Update config without editing scenarios
```

---

## 8. Gestione Errori — Pattern Avanzati

### 8.1 Architettura Error Handler

Make supporta **5 direttive di error handling** per modulo:

```
Module X
  │
  ├─ [SUCCESS] → Next module
  │
  └─ [ERROR] ──→ Error Handler choice
                 ├─ Resume: Fornisce valore fallback
                 ├─ Ignore: Continua senza output
                 ├─ Break: Interrompe, salva a incomplete
                 ├─ Commit: Marca come success e ferma
                 └─ Rollback: Tenta annullare operazioni
```

### 8.2 Tipi di Error Handler

#### Resume

```
Module: "Send Email via SendGrid"
  ├─ Configuration
  ├─ On error: Resume
  └─ Resume with: {{fallbackEmail}}

Scenario execution:
1. Try to send email
2. SendGrid API timeout (error)
3. Resume with {{fallbackEmail}} value
4. Next module riceve: {{fallbackEmail}}
5. Continua normalmente
```

**Use case:** Fallback values quando service indisponibile.

#### Ignore

```
Module: "Log to analytics"
  ├─ On error: Ignore
  └─ (no value provided)

Scenario execution:
1. Try to log
2. Analytics API error
3. Module output: None (ignored)
4. Next module never runs
5. Scenario continua da routing logic
```

**Use case:** Non-critical operations (logging, tracking).

#### Break

```
Module: "Update CRM"
  ├─ On error: Break
  └─ Store incomplete execution

Scenario execution:
1. Try to update CRM
2. CRM API unauthorized (error)
3. Execution stops
4. Scenario moved to: Incomplete Executions queue
5. Data disponibile per: Manual resume/retry
6. Retention: 30 giorni (Free), 90+ giorni (Paid)
```

**Use case:** Critical operations che devono essere garantite.

#### Commit

```
Module: "Process payment"
  ├─ On error: Commit
  └─ Mark as success (despite error)

Module: "Send confirmation email"
  └─ (executes anyway)

Scenario execution:
1. Try charge credit card
2. Card declined (error)
3. Commit: Marks execution as successful
4. Email module still runs
5. User notified via email
6. Scenario completes successfully
```

**Use case:** Quando vogliamo completare processo nonostante errore parziale.

#### Rollback

```
Module 1: "Create user in DB" ✓
Module 2: "Assign license" ✓
Module 3: "Send welcome email" ✗ (Error)

With Rollback on Module 3:
├─ Attempts to:
│  ├─ Delete user (reverse Module 1)
│  ├─ Revoke license (reverse Module 2)
│  └─ Result: Incomplete execution stored
│
└─ Limitation:
   └─ Not all APIs support rollback
   └─ May end in partial rollback state
```

**Use case:** Transazioni che devono essere all-or-nothing.

### 8.3 Incomplete Executions

**Architecture:**
```
Scenario with "Break" handler
  │
  ├─ Error occurs
  │
  ├─ Execution stored in queue
  │  ├─ Execution data
  │  ├─ Module outputs up to error
  │  ├─ Error details
  │  └─ Timestamp
  │
  └─ Retention:
     ├─ Free plan: 30 days
     ├─ Paid plans: 60+ days
     └─ Enterprise: Custom
```

**Resuming incomplete execution:**
```
1. User reviews incomplete execution
2. Fixes underlying issue (e.g., API key updated)
3. Clicks "Resume"
4. Scenario continues from error point
5. Data preserved from previous partial execution
```

### 8.4 Notifiche Errori

Make notifica errori tramite:
```
├─ Email notifications
│  └─ Send to scenario owner on error
├─ Dashboard alerts
│  └─ Scenario status changes
├─ Webhooks
│  └─ POST to custom endpoint on error
└─ Integrations
   └─ Slack, Microsoft Teams, ecc
```

### 8.5 Pattern: Circuit Breaker

**Implementazione in Make:**
```
Scenario: Integrate with external API
├─ Module: HTTP GET to API
│  └─ On error: Resume with cached data
├─ Data Store Read: "api_failover_cache"
│  └─ IF API failed multiple times:
│     └─ Use cached value for 1 hour
├─ Data Store Write: "api_failures"
│  └─ Track failure count
└─ IF failures > threshold:
   └─ Set "circuit_breaker_open" = true
```

---

## 9. API Make.com

### 9.1 Architettura REST API

L'API Make.com è un'API **REST pura** per accesso programmatico:

```
Base URL: https://www.make.com/api/v2
Authentication: Bearer token
Requests/minute: Based on plan (Teams: 240 req/min)
```

### 9.2 Endpoint Principali

**Organizations:**
```
GET    /organizations/{id}
       Get organization details

PUT    /organizations/{id}
       Update organization

GET    /organizations/{id}/teams
       List teams in organization
```

**Teams:**
```
GET    /teams/{id}
       Get team details

GET    /teams/{id}/scenarios
       List team scenarios

GET    /teams/{id}/connections
       List team connections
```

**Scenarios:**
```
GET    /scenarios/{id}
       Get scenario blueprint

POST   /scenarios
       Create new scenario

PUT    /scenarios/{id}
       Update scenario

DELETE /scenarios/{id}
       Delete scenario

POST   /scenarios/{id}/run
       Execute scenario on demand
```

**Connections:**
```
GET    /connections/{id}
       Get connection details

POST   /connections
       Create new connection

DELETE /connections/{id}
       Delete/revoke connection
```

**Data Stores:**
```
GET    /data-stores/{id}/records
       List all records

GET    /data-stores/{id}/records/{key}
       Get specific record

POST   /data-stores/{id}/records
       Create record (upsert)

DELETE /data-stores/{id}/records/{key}
       Delete record
```

**Webhooks:**
```
GET    /webhooks
       List all webhooks

POST   /webhooks
       Create new webhook

DELETE /webhooks/{id}
       Delete webhook
```

**Analytics:**
```
GET    /analytics/scenarios/{id}/executions
       Get scenario execution history

GET    /analytics/teams/{id}/operations
       Get operations usage
```

### 9.3 Autenticazione API

**Token-based auth:**
```
Header: Authorization: Bearer YOUR_API_TOKEN

Token generation:
1. Go to Make Dashboard
2. Settings → Apps & Devices
3. Generate Personal API Token
4. Store securely (not in version control)
```

**Rate limiting:**
```
X-RateLimit-Limit: 240
X-RateLimit-Remaining: 239
X-RateLimit-Reset: 1708099200

If exceeded:
HTTP 429 Too Many Requests
Retry-After: 60 (seconds)
```

### 9.4 Scenario Execution via API

```
POST /scenarios/{scenarioId}/run

Request:
{
  "data": {
    "email": "user@example.com",
    "items": [1, 2, 3],
    "metadata": {"source": "api"}
  }
}

Response:
{
  "success": true,
  "executionId": "exec_123abc",
  "status": "queued"
}

Check status:
GET /executions/{executionId}
{
  "status": "success",
  "startTime": "2024-02-15T10:00:00Z",
  "endTime": "2024-02-15T10:00:03Z",
  "operations": 5
}
```

---

## 10. Moduli Ecosystem

### 10.1 Panoramica

Make fornisce **3.000+ moduli pre-built** organizzati per categoria:

#### CRM & Sales
```
├─ Salesforce
│  ├─ Create Record
│  ├─ Update Record
│  ├─ Get Record
│  ├─ List Records (with pagination)
│  └─ Execute Apex (admin)
│
├─ HubSpot
│  ├─ Create Contact
│  ├─ Create Deal
│  ├─ Add Note
│  └─ Update Property
│
├─ Pipedrive
│  ├─ Create Deal
│  ├─ Update Person
│  └─ Get Deals
│
└─ Others: Zoho, Microsoft Dynamics, SAP
```

#### E-commerce
```
├─ Shopify
│  ├─ Watch for new orders
│  ├─ Get product
│  ├─ Update inventory
│  └─ Create fulfillment
│
├─ WooCommerce
│  ├─ Watch for orders
│  ├─ Get products
│  ├─ Create coupons
│  └─ Update customer
│
├─ BigCommerce
├─ PrestaShop
└─ Magento
```

#### Database & Storage
```
├─ Supabase
│  ├─ Insert row
│  ├─ Update row
│  ├─ Query (with filters)
│  └─ Delete row
│
├─ Airtable
│  ├─ Create record
│  ├─ Update record
│  ├─ List records
│  └─ Watch records
│
├─ Google Sheets
│  ├─ Add/Update row
│  ├─ Get cells
│  ├─ Clear cells
│  └─ Create spreadsheet
│
├─ MongoDB
├─ PostgreSQL
└─ MySQL
```

#### Communication
```
├─ Slack
│  ├─ Send message
│  ├─ Create channel
│  ├─ Update message
│  └─ Get channel info
│
├─ Email (SMTP/Gmail)
│  ├─ Send email
│  ├─ Watch for emails
│  ├─ Create label
│  └─ Get attachment
│
├─ WhatsApp
│  ├─ Send message
│  ├─ Send template
│  └─ Watch messages
│
└─ Teams, Discord, Telegram, Twilio
```

#### AI & Machine Learning
```
├─ OpenAI
│  ├─ Create message
│  ├─ Create embeddings
│  ├─ List files
│  └─ (GPT-4, GPT-3.5, etc)
│
├─ Anthropic Claude
│  ├─ Create message
│  ├─ Stream completion
│  └─ API via Make integration
│
├─ Google AI
├─ Cohere
└─ HuggingFace
```

#### Project Management
```
├─ ClickUp
│  ├─ Create task
│  ├─ Update task
│  ├─ Watch tasks
│  └─ Create checklist
│
├─ Asana
│  ├─ Create task
│  ├─ Create project
│  ├─ List tasks
│  └─ Watch tasks
│
├─ Jira
│  ├─ Create issue
│  ├─ Update issue
│  └─ Transition workflow
│
└─ Monday.com, Notion, Trello
```

#### Accounting & Finance
```
├─ QuickBooks
│  ├─ Create invoice
│  ├─ Create expense
│  ├─ Get account
│  └─ Create payment
│
├─ Xero
│  ├─ Create invoice
│  ├─ Create contact
│  └─ Get bank account
│
├─ Stripe
│  ├─ Watch payments
│  ├─ Create customer
│  ├─ Create charge
│  └─ Get payment intent
│
└─ PayPal, Square, Wave
```

#### File Storage
```
├─ Google Drive
│  ├─ Upload file
│  ├─ Watch files
│  ├─ Move/Delete file
│  └─ Get file metadata
│
├─ Dropbox
│  ├─ Upload file
│  ├─ Download file
│  ├─ Delete file
│  └─ Watch folder
│
└─ OneDrive, Box, FTP/SFTP
```

### 10.2 HTTP Module — Universal Connector

Per integrare servizi **non pre-built**:

```
HTTP Module - Make a request

GET request example:
├─ URL: https://api.example.com/v1/users/{{userId}}
├─ Method: GET
├─ Headers:
│  ├─ Authorization: Bearer {{apiToken}}
│  ├─ Content-Type: application/json
│  └─ X-Custom-Header: value
├─ Query parameters:
│  ├─ page: {{currentPage}}
│  └─ limit: 50
├─ Response handling:
│  ├─ Parse response as JSON: TRUE
│  └─ Timeout: 30 seconds
│
└─ Output:
   ├─ status: 200
   ├─ headers: {...}
   ├─ body: {parsed JSON response}
   └─ size: 1024 bytes

POST request with body:
├─ URL: https://api.example.com/v1/users
├─ Method: POST
├─ Body:
│  {
│    "name": {{fullName}},
│    "email": {{email}},
│    "role": "user",
│    "active": true
│  }
├─ Content-Type: application/json
└─ Response: Created object with ID
```

### 10.3 JSON & Tools Module

**JSON Module:**
```
├─ Create JSON: Estructtura JSON custom
├─ Parse JSON: JSON string → Object
├─ Update JSON: Merge/update JSON object
└─ Convert JSON to XML/CSV
```

**Tools Module:**
```
├─ Set variable: Salva valore in {{variable}}
├─ Sleep/Delay: Pausa X secondi
├─ Compose: Combina testi/valori
├─ Text to number, number to text
└─ Match pattern (regex)
```

---

## 11. Modello di Esecuzione e Performance

### 11.1 Concetto Operations (pre-agosto 2025)

**Nota importante:** Make ha cambiato il modello di fatturazione da "operations" a "credits" ad agosto 2025.

**Vecchio modello (operations):**
```
1 operation = 1 module execution

Esempio scenario:
├─ Trigger: +1 op (per webhook)
├─ Module A: +1 op
├─ Iterator (3 elementi): +3 ops
├─ Module B (×3): +3 ops
├─ Aggregator: +1 op
└─ Total: 9 operations per execution
```

**Nuovo modello (credits):**
```
Credits = Billing unit after Aug 2025
├─ Most modules: 1 credit per execution
├─ Advanced AI modules: Variable credits (usage-based)
│  ├─ OpenAI: Pay per token used
│  ├─ Claude: Pay per token used
│  └─ Other LLMs: Similar
├─ Conversion: Old operations converted 1:1 to credits

Pricing change:
├─ Free: 1.000 credits/month
├─ Core: 10.000 credits/month
├─ Pro: 10.000 + custom
└─ Teams/Enterprise: Custom
```

### 11.2 Data Transfer Limits

```
Free plan:  50 MB/month
Core plan:  250 MB/month
Pro plan:   500 MB/month
Teams:      1.000 MB/month
Enterprise: Unlimited (SLA)
```

**Data transfer calcolato come:**
- Size di bundle elaborati tramite moduli
- Webhook payloads ricevuti
- HTTP response downloaded
- File transfers

### 11.3 Timeout e Limite Esecuzione

```
Timeout per esecuzione: 40-45 minuti
├─ Hard limit: 45 minuti di wall-clock time
├─ Se scenario non completa: Execution killed
├─ Result: Incomplete execution
└─ Can resume: Se configured with Break handler

Concurrent executions per piano:
├─ Free: 1 (sequenziale)
├─ Core: 3 parallele
├─ Pro: 10 parallele
└─ Teams/Enterprise: 50+ parallele
```

### 11.4 Intervallo di Scheduling

```
Free plan:    15 minuti minimo
Core+ plans:  1 minuto minimo
Cron support: Pro+ plans

Esempi:
├─ "Every 15 minutes" (Free compatible)
├─ "Every minute" (Core+)
├─ "0 */2 * * *" (Every 2 hours, cron, Pro+)
└─ "0 9 * * 1-5" (Weekdays 9am, cron, Pro+)
```

---

## 12. Pattern Avanzati

### 12.1 Pagination Handling

**Pattern: Offset-based Pagination**
```
Scenario: Fetch all users in batches

Setup:
├─ HTTP Module: GET /api/users?offset={{offset}}&limit=100
├─ Set variable: offset = 0
│
├─ Loop:
│  ├─ Execute HTTP
│  ├─ IF response.length = 100:
│  │  ├─ Set offset += 100
│  │  └─ Re-trigger (via subscenario)
│  └─ ELSE: (< 100 items) Done
│
└─ Aggregator: Combine all pages

Alternative: Native pagination in Make modules:
├─ Some modules (Salesforce, HubSpot) have pagination:
│  ├─ Pagination type: Offset
│  ├─ Limit per page: 100
│  └─ Make auto-paginates internally
```

**Pattern: Cursor-based Pagination**
```
API uses: {next_cursor: "abc123"}

Setup:
├─ First request: No cursor
│  └─ Response: {data: [...], next_cursor: "xyz"}
├─ Variable: cursor = null
│
├─ Loop:
│  ├─ HTTP GET with: cursor={{if(cursor; cursor; "")}}
│  ├─ Variable: cursor = {{response.next_cursor}}
│  ├─ IF cursor not empty: Continue
│  └─ ELSE: Done
│
└─ Aggregator: Combine all pages
```

### 12.2 Rate Limit Management

**Pattern: Exponential Backoff**
```
Module: HTTP Request (with error handler)

On error (429 Too Many Requests):
├─ Resume directive with:
│  └─ Wait {{retryCount}} seconds before retry
├─ Increment retry counter
├─ IF retryCount < 5:
│  └─ Re-trigger module via subscenario
└─ ELSE: Fail (incomplete execution)

Implementation:
├─ Data Store: Store retryCount
├─ Sleep module: Pause {{exponentialBackoff}}
├─ Subscenario: Retry up to N times
```

### 12.3 Idempotent Processing

**Pattern: Deduplication with Data Store**
```
Trigger: Webhook from external system (might retry)

Flow:
├─ Data Store Read: "processed_webhooks"
│  └─ Key: {{webhookId}}
├─ IF exists (already processed):
│  └─ Return cached response (skip rest)
├─ ELSE (new):
│  ├─ Process webhook
│  ├─ Data Store Write: Mark as processed
│  │  └─ Store: {id: {{webhookId}}, result: {...}}
│  └─ Return response
│
Benefit: Multiple webhook deliveries → Single processing
```

### 12.4 State Management via Data Stores

**Pattern: Multi-step Workflow**
```
Order fulfillment workflow:

Step 1 (Scenario A): Validate order
├─ Check inventory
├─ Data Store Write: "order_step_{{orderId}}" = {step: 1, status: "validated"}
└─ Webhook response: "OK"

Step 2 (Scenario B): Process payment (triggered later)
├─ Data Store Read: "order_step_{{orderId}}"
├─ Check: if step >= 1 (validate prerequisite)
├─ Charge card
├─ Data Store Write: Update to {step: 2, status: "paid"}
└─ Trigger Step 3

Step 3 (Scenario C): Fulfill order
├─ Data Store Read: Check step
├─ Create fulfillment
├─ Data Store Write: {step: 3, status: "shipped"}
└─ Complete
```

### 12.5 Sub-scenario (Calling Scenario)

**Architecture:**
```
Parent Scenario
├─ Trigger: New order
├─ Module: Run Scenario "Validate Order"
│  ├─ Input: {{orderData}}
│  └─ Output: {{validationResult}}
├─ Module: IF {{validationResult.isValid}}
│  └─ Run Scenario "Process Payment"
│     ├─ Input: {{orderData}}, {{paymentMethod}}
│     └─ Output: {{transactionId}}
├─ Module: Run Scenario "Send Confirmation"
│  └─ Input: {{transactionId}}, {{email}}
└─ End

Sub-scenarios must:
├─ Be in same Team
├─ Have trigger: "Scheduled" → "On demand"
├─ Define inputs via Scenario Inputs
├─ Define outputs via Scenario Outputs
├─ Be ACTIVE (not paused)
```

**Benefits:**
- Code reuse (1 sub-scenario, multiple parents)
- Modularity (easier to test/debug)
- Parallel processing control
- Input/output contracts

### 12.6 Webhook Chaining

**Pattern: Trigger external system from scenario**
```
Scenario A (Make.com)
├─ Trigger: Schedule
├─ Process data
├─ Final module: HTTP POST to external webhook
│  └─ URL: https://external.com/webhook/process
│  └─ Method: POST
│  └─ Body: {{processedData}}
│
External System
├─ Receives webhook
├─ Processes asynchronously
├─ Calls back: POST to Make webhook
│  └─ URL: https://hook.make.com/scenario-b-xxxx
│  └─ Make Scenario B triggers
│
Scenario B (Make.com)
└─ Processes result

Result: Bi-directional integration
```

---

## 13. Prezzi e Piani

### 13.1 Piano Tariffario (Febbraio 2024+)

| Aspetto | Free | Core | Pro | Teams | Enterprise |
|--------|------|------|-----|-------|------------|
| **Prezzo** | Gratuito | $9/mese | $16/mese | $34.12/mese | Custom |
| **Billing** | Monthly | Annual | Annual | Annual | Annual |
| **Credits/mese** | 1.000 | 10.000 | 10.000+ | Custom | Custom |
| **Data transfer** | 50 MB | 250 MB | 500 MB | 1.000 MB | Unlimited |
| **Scenarios attive** | 2 | Unlimited | Unlimited | Unlimited | Unlimited |
| **Intervallo minimo** | 15 min | 1 min | 1 min | 1 min | < 1 min (SLA) |
| **Cron support** | No | No | Yes | Yes | Yes |
| **API access** | No | Yes | Yes | Yes | Yes |
| **Incomplete retention** | 30 gg | 60 gg | 90 gg | 180 gg | Custom |
| **Team members** | 1 | 1 | 1 | Unlimited | Unlimited |
| **Team features** | No | No | No | Yes | Yes |
| **Priority support** | No | No | No | No | 24/7 |
| **SLA** | No | No | No | No | 99.9% |

### 13.2 Operations vs Credits

**Cambio agosto 2025:**
```
Old (Operations):
├─ 1 operation = 1 module execution
├─ All modules cost 1 operation
├─ No distinction for complexity

New (Credits):
├─ 1 credit = 1 module execution (most modules)
├─ AI modules: Variable credits (usage-based)
│  ├─ OpenAI: Pay per token used
│  ├─ Claude: Pay per token used
│  └─ Other LLMs: Similar
├─ Conversion: Old operations → credits 1:1
```

### 13.3 Overage Pricing

```
Free plan: No overages
├─ Cannot exceed 1.000 credits/month
├─ Scenarios disabled if limit reached
└─ Wait for next billing cycle

Paid plans: Auto-scaling
├─ Use more credits → Automatic charge
├─ Overage rate: Typically 10x regular rate
│  └─ Example: 10.000 extra credits = {{10.000 / 10}} cost
├─ No hard limit
└─ Billing surprise possible (set alerts)
```

### 13.4 Cost Optimization

```
Strategies:
├─ 1. Use polling minimum interval wisely
│  ├─ If data changes rarely: 15 min (Free-compatible)
│  └─ If data changes frequently: 1 min (Core+, watch ops carefully)
│
├─ 2. Avoid unnecessary operations
│  ├─ Use filters between modules (no ops)
│  ├─ Combine HTTP calls (1 op instead of 5)
│  └─ Remove unnecessary modules
│
├─ 3. Use webhooks instead of polling
│  ├─ Polling: 1 op per interval (wasted if no data)
│  ├─ Webhook: 0 ops unless data arrives
│  └─ Savings: 95%+ in typical usage
│
├─ 4. Aggregate data before scenarios
│  ├─ If processing 100 items: Use loop (100 ops)
│  ├─ Alternative: Send array, iterator, aggregator
│  └─ Trade-off: Complexity vs. credits
│
└─ 5. Archive old scenarios
   └─ Don't delete (lose history), but deactivate
```

---

## 14. Sicurezza

### 14.1 Encryption & Data Protection

**In Transit:**
```
TLS 1.2/1.3 (AES-256)
├─ All API communications encrypted
├─ Webhook delivery encrypted
└─ Data transfer to/from modules encrypted
```

**At Rest:**
```
Encryption model:
├─ Database: AES-256 encryption
├─ Secrets (API keys, tokens): Encrypted separately
├─ Data Stores: Encrypted
└─ Incomplete executions: Encrypted
```

### 14.2 Compliance & Certifications

```
├─ SOC 2 Type II: Yes (audit completed)
├─ SOC 3: Yes
├─ ISO 27001: Yes
├─ GDPR: Compliant (EU GDPR processing)
├─ HIPAA: No (not certified for healthcare)
├─ PCI-DSS: Limited (avoid storing payment data)
└─ CCPA: Compliant (California privacy)
```

### 14.3 Access Control

**Organization-level:**
```
Roles:
├─ Owner: Full access, billing
├─ Admin: Manage users, scenarios, teams
├─ Member: Create/edit scenarios in assigned teams
└─ Viewer: Read-only access
```

**Team-level:**
```
Roles:
├─ Team Owner: Full team control
├─ Team Admin: Manage members, scenarios
├─ Editor: Create/edit scenarios, connections
├─ Viewer: Read-only
└─ Restricted Editor: Limited module access
```

### 14.4 Audit Logs

```
Make tracks:
├─ Who accessed what (user, timestamp, resource)
├─ Scenario executions (start time, duration, status)
├─ Connection authorizations/revocations
├─ API token generation/revocation
├─ Data Store access
├─ Team member additions/removals
└─ Retention: Up to 90 days (varies by plan)
```

### 14.5 Network Security

```
Make infrastructure:
├─ Private VPC (isolated from internet)
├─ VPN-only admin access
├─ AWS security groups (IP whitelisting)
├─ Enterprise: Dedicated isolated environment
└─ IP whitelisting available for enterprise
```

---

## 15. Integrazioni Custom

### 15.1 HTTP Module — Pattern

**Pattern: REST API Integration**
```
API documentation: GET /users/{id}

Make setup:
├─ HTTP Module
├─ Method: GET
├─ URL: https://api.example.com/users/{{userId}}
├─ Headers:
│  ├─ Authorization: Bearer {{apiToken}}
│  └─ Accept: application/json
├─ Response parsing: JSON
└─ Mapping:
   ├─ ID: {{response.id}}
   ├─ Name: {{response.name}}
   └─ Email: {{response.email}}
```

### 15.2 Custom App Development

Make permet creare **custom app modules**:

```
Steps:
1. Create Custom App in Make
2. Define base URL, authentication
3. Create modules (Actions, Searches, Triggers)
4. Configure IML for data transformation
5. Test with HTTP calls
6. Deploy to organization
7. Use in scenarios like native app

Example: Custom internal API
├─ Base: https://internal.company.com/api
├─ Auth: API key
├─ Module: Get employee data
│  ├─ Endpoint: /employees/{id}
│  ├─ Input: employeeId
│  └─ Output: {id, name, email, department}
```

### 15.3 GraphQL Support

```
HTTP Module with GraphQL:

POST request:
├─ URL: https://api.example.com/graphql
├─ Method: POST
├─ Headers:
│  ├─ Content-Type: application/json
│  └─ Authorization: Bearer {{token}}
├─ Body (Raw):
│  {
│    "query": "{ user(id: {{userId}}) { id name email } }"
│  }
└─ Parse as JSON: TRUE

Response:
└─ {{response.data.user}}
```

### 15.4 SOAP Support

```
HTTP Module con SOAP (XML):

POST request:
├─ URL: https://soap.example.com/ws
├─ Method: POST
├─ Headers:
│  ├─ Content-Type: text/xml
│  └─ SOAPAction: urn:getUser
├─ Body (Raw XML):
│  <?xml version="1.0"?>
│  <soap:Envelope ...>
│    <soap:Body>
│      <getUser><userId>{{userId}}</userId></getUser>
│    </soap:Body>
│  </soap:Envelope>

Response: Parse XML to JSON via JSON parser
```

### 15.5 FTP/SFTP

```
FTP/SFTP Module (if available in Make):

Upload file:
├─ Server: ftp.example.com
├─ Username: {{ftpUser}}
├─ Password: {{ftpPass}}
├─ File path: /uploads/{{filename}}
├─ File content: {{fileBase64}}
└─ Result: File uploaded

Download file:
├─ Retrieve file
├─ Convert to base64
└─ Save to Data Store or pass to next module
```

---

## 16. Make.com vs Alternative

### 16.1 Confronto Dettagliato

| Criterio | Make | Zapier | n8n | Power Automate |
|----------|------|--------|-----|------------------|
| **Fondazione** | 2012 | 2011 | 2019 | 2018 |
| **Tipo** | Cloud iPaaS | Cloud iPaaS | Open source + Cloud | Proprietario Microsoft |
| **Integrazioni** | 3.000+ | 6.000+ | 350+ | 600+ |
| **Learning curve** | Medio | Basso | Elevato | Basso-Medio |
| **Pricing model** | Credits | Tasks/month | Executions | User/Flow |
| **Entry price** | $0 (Free) | $19.99 | $0 (self-hosted) | $15/user |
| **Complex workflows** | Eccellente | Buono | Eccellente | Buono |
| **Branching logic** | Router nativo | Multi-zap | If/Then | If/Then |
| **Data transformation** | IML potente | Zapier Formatter | JavaScript/Node | Power Query |
| **Error handling** | 5 tipi | Resume/Retry | Try/Catch | Retry policy |
| **Sub-workflows** | Yes (sub-scenario) | Possible | Native | No |
| **Self-hosted** | No (cloud only) | No | Yes | No |
| **API access** | Full | Full | Full | Partial |
| **Microsoft integration** | Buono | Buono | Buono | **Eccellente** |
| **Enterprise support** | 24/7 | Business hours | Custom | 24/7 Microsoft |

### 16.2 Quando scegliere Make.com

**Make è ideale per:**
```
✓ Flussi con logica condizionale complessa
✓ Trasformazioni dati sofisticate (IML)
✓ Gestione stato intermedia (Data Stores)
✓ Scalabilità con operazioni/crediti bassi
✓ Scenari che richiedono sub-workflows
✓ Team tech-savvy (non necesariamente developer)
✓ Integrazione con app non mainstream
✓ Prezzi competitivi per volume medio-alto
```

**Make NON è ideale per:**
```
✗ Aziende full Microsoft (use Power Automate)
✗ Flussi semplicissimi (Zapier è più veloce)
✗ Applicazioni mission-critical con SLA strict (solo Enterprise)
✗ Necessità self-hosting (use n8n)
✗ Parsing/trasformazione testo semplice (Zapier formatter è intuitivo)
```

---

## 17. Limitazioni Tecniche

### 17.1 Vincoli Architetturali

```
Module limits:
├─ Max moduli per scenario: ~200 (pratico)
├─ Max nesting depth: ~10 livelli
├─ Max bundle size: 100 MB
└─ Max array size: 500.000 elementi

Scenario limits:
├─ Execution timeout: 40-45 minuti
├─ Max concurrent executions:
│  ├─ Free: 1
│  ├─ Core: 3
│  ├─ Pro: 10
│  └─ Enterprise: 50+
├─ Max webhook queue size: 50 items
└─ If full: HTTP 400 "Queue is full"

Data limits:
├─ Webhook payload max: 100 MB
├─ HTTP response max: 50 MB (tipicamente)
├─ Data Store record max: ~50 MB per key
└─ Text field max: No hard limit (JSON escaping)
```

### 17.2 API Rate Limits

```
Make API limits:
├─ Default: 240 requests/minute per organization
├─ Exceeded: HTTP 429 Too Many Requests
├─ Retry-After header: Included
└─ Per-team limits: Can be customized

Webhook limits:
├─ Default: 30 webhooks/second
├─ Queue size: 50 items
├─ If exceeded: HTTP 429 (webhook rejected)
└─ Enterprise: Custom limits available
```

### 17.3 Timeout Limits

```
HTTP Module timeouts:
├─ Default: 30 secondi per richiesta
├─ Max: User-configurable up to 60 sec
├─ If exceeded: Timeout error

Scenario execution timeout:
├─ Hard limit: 40-45 minuti di wall-clock
├─ If scenario non completa: Killed
└─ Incomplete execution: Stored for resume

Module-level timeout:
├─ Some modules: 10-30 secondi custom timeout
└─ Retry: Configurable on error
```

---

## 18. Best Practice

### 18.1 Principi di Design dello Scenario

```
1. Single Responsibility Principle
   ├─ One scenario = One primary function
   ├─ Use sub-scenarios for modularity
   └─ Avoid scenario with 100+ modules

2. Error Handling First
   ├─ Plan error handlers before build
   ├─ Test failure modes
   └─ Use appropriate directives (Resume, Break, Commit)

3. Performance Optimization
   ├─ Minimize operations per execution
   ├─ Use webhooks instead of polling
   ├─ Filter early (no ops wasted)
   └─ Aggregate efficiently

4. Naming Conventions
   ├─ Scenario: "[Team] - [Function] - [Version]"
   │  └─ Example: "Sales - New Lead Follow-up - v2.1"
   ├─ Modules: Sequential (1. Get contact, 2. Send email)
   ├─ Variables: camelCase {{customerEmail}}, {{orderTotal}}
   └─ Data Stores: snake_case "processed_orders"

5. Documentation
   ├─ Add notes to complex modules
   ├─ Document IML expressions
   ├─ Version scenario blueprints (GitHub)
   └─ Maintain data flow diagram
```

### 18.2 Error Handling Patterns

```
Pattern 1: Graceful Degradation
├─ Critical module: Resume with fallback
│  └─ Example: Send email (if fails, log only)
└─ Non-critical module: Ignore

Pattern 2: Retry Logic
├─ Error handler: Resume
├─ Sleep module: Pause before retry
├─ Counter: Track retries
└─ Limit: Max 5 retries, then fail

Pattern 3: Dead Letter Pattern
├─ Module error: Break (incomplete execution)
├─ Manual review: Check incomplete queue
├─ Fix issue: Update scenario/connections
├─ Resume: Retry from incomplete state

Pattern 4: Commit Checkpoint
├─ Critical operation completed: Commit
├─ Non-critical failures: Continue anyway
├─ Ensures partial success captured
└─ Used in payment/fulfillment scenarios
```

### 18.3 Performance Optimization

```
1. Reduce Operations
   ├─ Combine HTTP calls (batch API calls)
   ├─ Filter early (no downstream waste)
   ├─ Use field mapping (not array mapping)
   └─ Iterator only when necessary

2. Webhook Efficiency
   ├─ Use instant triggers over polling
   ├─ Savings: 95%+ of operations
   ├─ Latency: < 100ms vs 15-60 sec
   └─ Cost: Depends on volume

3. Data Aggregation
   ├─ Aggregate to array: Reduce downstream ops
   ├─ Text aggregator: Combine strings
   ├─ Batch updates: Send arrays not singles
   └─ Trade-off: Scenario complexity increases

4. Concurrency Control
   ├─ Queue webhooks for rate limiting
   ├─ Sleep module: Throttle requests
   ├─ Data Store locking: Prevent duplicates
   └─ Exponential backoff: Handle 429 errors
```

### 18.4 Monitoring e Alerting

```
Setup:
├─ Scenario settings: Enable error notifications
├─ Email alerts: Configurable recipients
├─ Webhook alerts: POST to external system
├─ Slack integration: Alert channel
└─ Teams: Microsoft Teams notifications

Metrics to track:
├─ Success rate: (successful executions / total)
├─ Avg execution time: Duration per run
├─ Error rate: Errors / total executions
├─ Operations used: Monthly burn rate
└─ Incomplete executions: Queue depth
```

---

## 19. Troubleshooting

### 19.1 Errori Comuni

| Errore | Causa | Soluzione |
|--------|-------|----------|
| **HTTP 429** | Rate limit raggiunto | Retry con exponential backoff, aumenta intervallo polling |
| **Connection failed** | Token scaduto | Riautorizza connessione, refresh token |
| **Queue is full** | 50 webhook in coda | Elabora più velocemente, increase concurrency |
| **Timeout (45 min)** | Scenario troppo lungo | Splitti in sub-scenarios, ottimizza moduli |
| **Invalid JSON** | Parse error in mapping | Valida input con JSON tool, usa escape chars |
| **Key not found** | Data Store miss | Check key naming, verify write prima read |
| **Incomplete execution** | Module errore con Break | Resume da incomplete queue, fix issue |
| **Authorization denied** | Permessi insufficienti | Verify team role, grant access |

### 19.2 Debugging Techniques

```
1. Execution Inspector
   ├─ View ogni bundle
   ├─ Check output per modulo
   ├─ Inspect IML expressions
   └─ Debug mapping issues

2. Logging
   ├─ Slack message: Log value during scenario
   ├─ Data Store: Temporary debug entries
   ├─ Email: Send debug report
   └─ Webhook: POST to logging service

3. Testing
   ├─ Manual trigger: Test scenario on demand
   ├─ Mock data: Test with dummy inputs
   ├─ Incomplete execution: Test resume
   └─ Sub-scenario: Test independently

4. Performance Analysis
   ├─ Execution time per module
   ├─ Bundles processed per module
   ├─ Operations consumed
   └─ Bottleneck identification
```

---

## 20. Risorse

### 20.1 URL Ufficiali

```
Sito principale:           https://www.make.com
Documentazione:           https://help.make.com
Developer Hub:            https://developers.make.com
API Documentation:        https://developers.make.com/api-documentation
Custom Apps:              https://developers.make.com/custom-apps-documentation
App Marketplace:          https://www.make.com/en/integrations
Community Forum:          https://community.make.com
Status Page:              https://status.make.com
Blog:                     https://www.make.com/en/blog
Pricing:                  https://www.make.com/en/pricing
Security:                 https://www.make.com/en/security
Enterprise:               https://www.make.com/en/enterprise
```

### 20.2 Certificazioni Disponibili

```
Make Academy:
├─ Make Fundamentals Certificate
├─ Make Advanced Certificate
├─ Make Expert Certification
└─ Certification exam available online
```

---

## Changelog

| Versione | Data | Cambiamenti Principali |
|----------|------|----------------------|
| 1.0 | Feb 2024 | Documentazione iniziale basata su Make.com 2024 |
| 1.1 | Aug 2024 | Credits system introduced (replacing operations) |
| 1.2 | Jan 2025 | Updated pricing tiers, enterprise features |
| 1.3 | Feb 2026 | Current documentation |

---

**Nota Finale:** Questa documentazione rappresenta l'architettura tecnica completa di Make.com al febbraio 2026. Make è una piattaforma in continua evoluzione; consultare sempre la documentazione ufficiale (https://help.make.com) per informazioni sulle ultime versioni e modifiche architetturali.

**Livello di Dettaglio:** Documento destinato a CTO, System Architects, Lead Engineers e technical decision makers che valutano o implementano Make.com come piattaforma di automazione enterprise.