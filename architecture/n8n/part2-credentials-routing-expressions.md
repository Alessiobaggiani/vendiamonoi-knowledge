# n8n — Part 2: Credentials, Routing, Expressions e Trasformazione Dati
## EXPERT-LEVEL TECHNICAL DEEP-DIVE

**Documento**: Riferimento tecnico esaustivo per il sistema credentials, expressions, routing e trasformazione dati in n8n
**Target**: Senior automation engineers e architetti di sistema Vendiamonoi.it
**Versione**: n8n 2026
**Ultimo aggiornamento**: 2026-04-06
**Complemento a**: Part 1 (Core Architecture), Part 3 (Error Handling, Sub-workflows, Webhooks), Part 4 (Self-hosting, Performance, Production)

---

## INDICE COMPLETO

1. [Sistema Credentials — Architettura Completa](#1-sistema-credentials--architettura-completa)
   - 1.1 Tipi di Credenziali
   - 1.2 Crittografia e Storage
   - 1.3 OAuth2 Complete Flow
   - 1.4 External Secrets Management
   - 1.5 Environment Variables per Credentials
   - 1.6 Credential Sharing e Testing
   - 1.7 Custom Credential Types
2. [Expressions — Linguaggio di Espressione Completo](#2-expressions--linguaggio-di-espressione-completo)
   - 2.1 Sintassi Fondamentale
   - 2.2 Variabili Core ($json, $input, $node, etc.)
   - 2.3 Metodi Built-in (String, Array, Object, Number)
   - 2.4 Date/Time con Luxon
   - 2.5 JMESPath per Query JSON
   - 2.6 Regular Expressions
   - 2.7 Pattern Avanzati e Helper Functions
   - 2.8 Pattern Expressions per E-commerce
3. [Routing e Data Flow — Nodi Completi](#3-routing-e-data-flow--nodi-completi)
   - 3.1 IF Node (Branch Binario)
   - 3.2 Switch Node (Multi-Branch)
   - 3.3 Merge Node (Combinazione Dati)
   - 3.4 Split In Batches / Loop Over Items
   - 3.5 Code Node (JavaScript/Python)
   - 3.6 Set Node (Edit Fields)
   - 3.7 HTTP Request Node
   - 3.8 Item Lists Nodes (Aggregate, Sort, Limit, Split Out, Summarize, Remove Duplicates)
   - 3.9 Compare Datasets Node
   - 3.10 Wait Node
4. [Trasformazione Dati — Pattern Completi](#4-trasformazione-dati--pattern-completi)
   - 4.1 Data Mapping tra Nodi
   - 4.2 JSON Parsing e Building
   - 4.3 Binary Data Handling
   - 4.4 Conversione Tipi
   - 4.5 Filtraggio e Deduplicazione
   - 4.6 Aggregazione e Summarization
5. [Pattern Vendiamonoi — n8n per Marketplace](#5-pattern-vendiamonoi--n8n-per-marketplace)

---

# 1. Sistema Credentials — Architettura Completa

## 1.1 Tipi di Credenziali

n8n supporta un ampio ecosistema di tipi di autenticazione, ciascuno con caratteristiche specifiche.

| Tipo | Descrizione | Iniezione | Uso Tipico |
|------|-------------|-----------|------------|
| **API Key / Bearer Token** | Token nell'header Authorization | Header | Maggior parte API moderne |
| **Basic Auth** | Username + Password codificati Base64 | Header | API legacy, servizi interni |
| **Header Auth** | Header custom con nome e valore | Header | API con header custom |
| **Query Auth (QS)** | Credenziali come parametri URL | Query String | API con token in URL |
| **Custom Auth** | Coppie chiave/valore multiple flessibili | Variabile | Schemi custom |
| **OAuth1** | Flusso OAuth legacy | Header | Twitter (legacy), API vecchie |
| **OAuth2** | Autenticazione moderna con scopes | Header | Google, Microsoft, social |
| **Digest Auth** | Autenticazione digest challenge-response | Header | Sistemi interni legacy |
| **JWT Auth** | JSON Web Token con Passphrase o PEM Key | Header | Servizi enterprise |
| **SSL Certificate** | Autenticazione basata su certificato client | TLS | API bancarie, alta sicurezza |

### Metodi di Iniezione Credenziali

Le credenziali vengono iniettate nelle richieste API attraverso:

- **Header**: Header di autorizzazione (più comune)
- **Body**: Parametri nel corpo della richiesta
- **QS (Query String)**: Parametri URL
- **Auth**: Basic Auth con chiavi username/password

---

## 1.2 Crittografia e Storage

### AES-256-GCM Encryption

n8n utilizza crittografia **AES-256-GCM** per tutti i dati delle credenziali:

- Ogni API key, OAuth token, password database è crittografata prima dello storage
- Le credenziali sono memorizzate nel database PostgreSQL (o SQLite)
- La chiave di crittografia è auto-generata al primo avvio (salvata in `~/.n8n`)
- Conforme FIPS-140-2

### Master Encryption Key

```bash
# Impostare chiave custom via environment variable
export N8N_ENCRYPTION_KEY="la-tua-chiave-segreta-di-almeno-32-caratteri"

# Oppure via file (Docker secrets compatible)
export N8N_ENCRYPTION_KEY_FILE=/run/secrets/encryption_key
```

**CRITICO**: Se la chiave di crittografia viene persa, TUTTE le credenziali memorizzate diventano **permanentemente inaccessibili**. Backup della chiave obbligatorio.

### Dove Sono Memorizzate

| Componente | Location |
|-----------|----------|
| Credenziali (encrypted) | PostgreSQL / SQLite database |
| Encryption key (default) | `~/.n8n/config` |
| Encryption key (custom) | `N8N_ENCRYPTION_KEY` env var |
| OAuth tokens | Database (encrypted) |
| Refresh tokens | Database (encrypted) |

---

## 1.3 OAuth2 Complete Flow

### Authorization Code Grant (Interattivo)

Usato quando un utente deve autorizzare l'accesso:

```
1. L'utente clicca "Authorize" nell'UI di n8n
   ↓
2. n8n redirige il browser verso:
   https://provider.com/oauth/authorize?
     client_id=YOUR_CLIENT_ID&
     redirect_uri=https://your-n8n.com/rest/oauth2-credential/callback&
     scope=sheets.readonly,drive.readonly&
     state=RANDOM_STATE_TOKEN&
     response_type=code
   ↓
3. L'utente autorizza sul sito del provider
   ↓
4. Il provider redirige a:
   https://your-n8n.com/rest/oauth2-credential/callback?
     code=AUTH_CODE&
     state=RANDOM_STATE_TOKEN
   ↓
5. n8n valida il parametro state (protezione CSRF)
   ↓
6. n8n scambia il code per token:
   POST https://provider.com/oauth/token
   {
     grant_type: "authorization_code",
     code: AUTH_CODE,
     client_id: YOUR_CLIENT_ID,
     client_secret: YOUR_CLIENT_SECRET,
     redirect_uri: https://your-n8n.com/rest/oauth2-credential/callback
   }
   ↓
7. Il provider restituisce:
   {
     access_token: "eyJ0eXAiOiJKV1QiLCJhbGc...",
     refresh_token: "1/Tl6awhpFjk...",
     expires_in: 3600,
     token_type: "Bearer"
   }
   ↓
8. n8n memorizza nel database (crittografato AES-256-GCM):
   - access_token
   - refresh_token
   - expiry timestamp
   - scope list
```

### Client Credentials Grant (Server-to-Server)

Usato per comunicazione machine-to-machine senza interazione utente:

```
1. n8n invia direttamente:
   POST https://provider.com/oauth/token
   {
     grant_type: "client_credentials",
     client_id: YOUR_CLIENT_ID,
     client_secret: YOUR_CLIENT_SECRET,
     scope: "api.read api.write"
   }
   ↓
2. Provider restituisce access_token
   ↓
3. n8n usa il token per le richieste API
```

### Refresh Automatico Token

n8n gestisce automaticamente il refresh dei token OAuth2:
- Monitora la scadenza del token
- Prima della scadenza, invia richiesta di refresh
- Aggiorna il token nel database
- Il workflow non si interrompe mai per token scaduti

---

## 1.4 External Secrets Management

n8n supporta la gestione esterna dei secrets per ambienti enterprise.

### Provider Supportati

| Provider | Tipo | Note |
|----------|------|------|
| **HashiCorp Vault** | KV v1/v2 | AppRole auth, namespace isolation, multi-vault (v2.10.0+) |
| **AWS Secrets Manager** | Managed | Integrazione nativa |
| **Azure Key Vault** | Managed | Integrazione nativa |
| **GCP Secrets Manager** | Managed | Integrazione nativa |
| **1Password** | Via Connect Server | Richiede 1Password Connect |

### Vantaggi External Secrets

- **Decoupling**: Credenziali separate dal database n8n
- **Centralizzazione**: Gestione unica per tutti i servizi
- **Rotazione**: Credenziali rotabili senza modificare workflow
- **Audit**: Logging centralizzato degli accessi
- **Compliance**: Soddisfa requisiti enterprise di sicurezza

### Accesso nei Workflow

```javascript
// Accesso a secret esterno nelle expressions
{{ $secrets.mySecretName }}

// Nel Code node
const apiKey = $env.MY_API_KEY; // via environment variable
```

---

## 1.5 Environment Variables per Credentials

### Pattern _FILE per Docker Secrets

Molte variabili supportano il suffisso `_FILE` che legge il valore da un file:

```bash
# Diretto
export N8N_ENCRYPTION_KEY="mykey123"

# Via file (Docker secrets)
export N8N_ENCRYPTION_KEY_FILE=/run/secrets/encryption_key

# Database
export DB_PASSWORD_FILE=/run/secrets/db_password
```

### Variabili di Sicurezza Chiave

| Variabile | Descrizione |
|-----------|-------------|
| `N8N_ENCRYPTION_KEY` | Chiave master crittografia credentials |
| `N8N_BLOCK_ENV_ACCESS_IN_NODE` | Blocca accesso env vars nei nodi (true/false) |
| `DB_TYPE` | Tipo database (postgresdb, sqlite) |
| `DB_HOST`, `DB_PORT`, `DB_NAME` | Connessione database |
| `DB_USER`, `DB_PASSWORD` | Autenticazione database |
| `N8N_BASEURL` | URL base dell'istanza n8n |
| `N8N_PROTOCOL` | HTTP o HTTPS |
| `N8N_PORT` | Porta del servizio |
| `N8N_TIMEZONE` | Timezone per scheduling |

---

## 1.6 Credential Sharing e Testing

### Sharing

- Le credenziali possono essere condivise tra workflow
- Controllo accessi basato su ruoli (in installazioni enterprise)
- Le credenziali sono associate allo scope team/organizzazione

### Testing

- n8n testa le credenziali quando vengono salvate per confermare il funzionamento
- Funzioni di test custom implementabili per credenziali personalizzate
- Oggetti test-request definiscono endpoint di validazione
- Scanning automatico per secrets negli export e nei log

---

## 1.7 Custom Credential Types

È possibile creare tipi di credenziali personalizzati per:
- Nodi custom
- API non supportate nativamente
- Schemi di autenticazione proprietari

I dati di autenticazione vengono iniettati come parte degli oggetti richiesta API.

---

# 2. Expressions — Linguaggio di Espressione Completo

## 2.1 Sintassi Fondamentale

Le expressions in n8n sono delimitate da **doppie graffe** `{{ }}` e utilizzano una sintassi simile a JavaScript.

```
{{ espressione }}
```

**Caratteristiche**:
- Linguaggio simile a JavaScript che estende Tournament templating
- Imposta dinamicamente i valori dei parametri
- Accede ai dati dai nodi precedenti, metadati workflow, variabili ambiente
- Supporta assegnazioni di variabili e pattern IIFE per logica complessa

### Differenze Chiave da JavaScript Puro

- Sintassi `{{ }}` per le espressioni
- Variabili speciali con prefisso `$`
- Accesso diretto al contesto del workflow
- Supporto nativo Luxon per date
- JMESPath integrato per query JSON

---

## 2.2 Variabili Core

### Dati Item Corrente

| Variabile | Descrizione | Esempio |
|-----------|-------------|---------|
| `$json` | Dati JSON dell'item corrente | `{{ $json.name }}` |
| `$input.item.json` | Sintassi alternativa per item JSON corrente | `{{ $input.item.json.name }}` |
| `$input.first()` | Primo item di input | `{{ $input.first().json.id }}` |
| `$input.last()` | Ultimo item di input | `{{ $input.last().json.total }}` |
| `$input.all()` | Tutti gli item come array | `{{ $input.all().length }}` |

### Contesto Esecuzione Workflow

| Variabile | Descrizione | Esempio |
|-----------|-------------|---------|
| `$node('<nome>')` | Riferimento dati da nodo specifico | `{{ $node('HTTP Request').json.data }}` |
| `$node('<nome>').json` | JSON da nodo specifico | `{{ $node('Get Orders').json.orders }}` |
| `$node('<nome>').binary` | Dati binari da nodo specifico | `{{ $node('Download').binary.data }}` |
| `$workflow.id` | ID workflow corrente | `{{ $workflow.id }}` |
| `$workflow.name` | Nome workflow corrente | `{{ $workflow.name }}` |
| `$execution.id` | ID esecuzione corrente | `{{ $execution.id }}` |
| `$execution.mode` | Modalità (manual, automatic) | `{{ $execution.mode }}` |
| `$execution.resumeUrl` | URL resume per Wait node | `{{ $execution.resumeUrl }}` |

### Variabili Globali

| Variabile | Descrizione | Sicurezza |
|-----------|-------------|----------|
| `$env` | Variabili d'ambiente | Bloccabile con `N8N_BLOCK_ENV_ACCESS_IN_NODE=true` |
| `$secrets.<nome>` | Secrets esterni | Dipende dal provider |
| `$now` | Data/ora corrente (Luxon DateTime) | N/A |
| `$today` | Data corrente a mezzanotte (Luxon DateTime) | N/A |

---

## 2.3 Metodi Built-in

### Metodi Stringa

| Metodo | Descrizione | Esempio |
|--------|-------------|---------|
| `.trim()` | Rimuove spazi | `{{ $json.name.trim() }}` |
| `.replace(pattern, repl)` | Sostituisce prima occorrenza | `{{ $json.text.replace('old', 'new') }}` |
| `.replaceAll(pattern, repl)` | Sostituisce tutte le occorrenze | `{{ $json.text.replaceAll(' ', '_') }}` |
| `.split(delimiter)` | Divide in array | `{{ $json.tags.split(',') }}` |
| `.substring(start, end)` | Estrae sottostringa | `{{ $json.ean.substring(0, 3) }}` |
| `.toLowerCase()` | Minuscolo | `{{ $json.brand.toLowerCase() }}` |
| `.toUpperCase()` | Maiuscolo | `{{ $json.sku.toUpperCase() }}` |
| `.startsWith(str)` | Verifica inizio | `{{ $json.code.startsWith('IT') }}` |
| `.endsWith(str)` | Verifica fine | `{{ $json.file.endsWith('.csv') }}` |
| `.includes(str)` | Contiene | `{{ $json.title.includes('premium') }}` |
| `.match(regexp)` | Regex matching | `{{ $json.text.match(/\d+/) }}` |
| `.indexOf(str)` | Posizione occorrenza | `{{ $json.text.indexOf('@') }}` |
| `.padStart(len, char)` | Padding iniziale | `{{ $json.code.padStart(13, '0') }}` |
| `.padEnd(len, char)` | Padding finale | `{{ $json.id.padEnd(10, ' ') }}` |

### Metodi Array

| Metodo | Descrizione | Esempio |
|--------|-------------|---------|
| `.length` | Lunghezza array | `{{ $json.items.length }}` |
| `.push(val)` | Aggiunge alla fine | `{{ $json.arr.push('new') }}` |
| `.pop()` | Rimuove ultimo | `{{ $json.arr.pop() }}` |
| `.shift()` | Rimuove primo | `{{ $json.arr.shift() }}` |
| `.slice(start, end)` | Estrae porzione | `{{ $json.items.slice(0, 10) }}` |
| `.join(sep)` | Unisce in stringa | `{{ $json.tags.join(', ') }}` |
| `.filter(fn)` | Filtra elementi | `{{ $json.products.filter(p => p.stock > 0) }}` |
| `.map(fn)` | Trasforma elementi | `{{ $json.items.map(i => i.name) }}` |
| `.reduce(fn, init)` | Aggrega valori | `{{ $json.items.reduce((sum, i) => sum + i.price, 0) }}` |
| `.find(fn)` | Trova primo match | `{{ $json.orders.find(o => o.id === '123') }}` |
| `.includes(val)` | Contiene valore | `{{ $json.categories.includes('electronics') }}` |
| `.reverse()` | Inverte ordine | `{{ $json.items.reverse() }}` |
| `.sort(fn)` | Ordina | `{{ $json.items.sort((a,b) => a.price - b.price) }}` |
| `.flat()` | Appiattisce | `{{ $json.nested.flat() }}` |
| `.every(fn)` | Tutti soddisfano | `{{ $json.items.every(i => i.price > 0) }}` |
| `.some(fn)` | Almeno uno soddisfa | `{{ $json.items.some(i => i.stock === 0) }}` |

### Metodi Oggetto

| Metodo | Descrizione | Esempio |
|--------|-------------|---------|
| `Object.keys(obj)` | Nomi proprietà | `{{ Object.keys($json) }}` |
| `Object.values(obj)` | Valori proprietà | `{{ Object.values($json.prices) }}` |
| `Object.entries(obj)` | Coppie chiave-valore | `{{ Object.entries($json) }}` |
| `Object.assign(target, src)` | Merge oggetti | `{{ Object.assign({}, $json, {status: 'new'}) }}` |

### Metodi Numero

| Metodo | Descrizione | Esempio |
|--------|-------------|---------|
| `.toFixed(digits)` | Arrotonda a decimali | `{{ $json.price.toFixed(2) }}` |
| `.toString()` | Converte a stringa | `{{ $json.quantity.toString() }}` |
| `.toLocaleString()` | Formato locale | `{{ $json.price.toLocaleString('it-IT') }}` |
| `Number(val)` | Converte a numero | `{{ Number($json.priceStr) }}` |
| `parseInt(val)` | Converte a intero | `{{ parseInt($json.quantity) }}` |
| `parseFloat(val)` | Converte a decimale | `{{ parseFloat($json.price) }}` |
| `Math.round(val)` | Arrotondamento | `{{ Math.round($json.price * 100) / 100 }}` |
| `Math.ceil(val)` | Per eccesso | `{{ Math.ceil($json.weight) }}` |
| `Math.floor(val)` | Per difetto | `{{ Math.floor($json.score) }}` |
| `Math.max(...arr)` | Massimo | `{{ Math.max(...$json.prices) }}` |
| `Math.min(...arr)` | Minimo | `{{ Math.min(...$json.prices) }}` |

### Helper Functions

| Funzione | Descrizione | Esempio |
|----------|-------------|---------|
| `$if(cond, true, false)` | Condizionale (ternario) | `{{ $if($json.stock > 0, 'In Stock', 'Esaurito') }}` |
| `$ifEmpty(val, default)` | Null coalescing | `{{ $ifEmpty($json.title, 'Senza titolo') }}` |
| `typeof` | Tipo variabile | `{{ typeof $json.price }}` |
| `JSON.parse(str)` | Parse JSON da stringa | `{{ JSON.parse($json.jsonString) }}` |
| `JSON.stringify(obj)` | Oggetto a JSON string | `{{ JSON.stringify($json) }}` |

---

## 2.4 Date/Time con Luxon

n8n integra la libreria **Luxon** per una gestione avanzata di date e orari.

### Variabili Data

| Variabile | Tipo | Descrizione |
|-----------|------|-------------|
| `$now` | Luxon DateTime | Data/ora corrente con timezone |
| `$today` | Luxon DateTime | Data corrente a mezzanotte |

### Operazioni Date

```javascript
// Data corrente formattata
{{ $now.toFormat('dd/MM/yyyy') }}
// → "06/04/2026"

// Aggiungere giorni
{{ $now.plus({days: 30}).toFormat('dd/MM/yyyy') }}
// → "06/05/2026"

// Sottrarre ore
{{ $now.minus({hours: 2}).toISO() }}
// → "2026-04-06T10:30:00.000+02:00"

// Differenza tra date
{{ $now.diff($today, 'hours').hours }}
// → numero di ore trascorse oggi

// Inizio mese
{{ $now.startOf('month').toFormat('yyyy-MM-dd') }}
// → "2026-04-01"

// Fine mese
{{ $now.endOf('month').toFormat('yyyy-MM-dd') }}
// → "2026-04-30"

// Conversione timezone
{{ $now.setZone('Europe/Rome').toFormat('HH:mm') }}
{{ $now.setZone('Europe/Berlin').toFormat('HH:mm') }}
{{ $now.setZone('America/New_York').toFormat('HH:mm') }}

// Parse da stringa
{{ DateTime.fromISO('2026-04-06T12:00:00Z') }}
{{ DateTime.fromFormat('06/04/2026', 'dd/MM/yyyy') }}
{{ DateTime.fromMillis(1775654400000) }}
```

### Token Formato Luxon

| Token | Descrizione | Esempio |
|-------|-------------|---------|
| `yyyy` | Anno 4 cifre | 2026 |
| `MM` | Mese 2 cifre | 04 |
| `dd` | Giorno 2 cifre | 06 |
| `HH` | Ora 24h | 14 |
| `mm` | Minuti | 30 |
| `ss` | Secondi | 45 |
| `EEEE` | Nome giorno completo | Monday |
| `MMMM` | Nome mese completo | April |
| `Z` | Offset timezone | +02:00 |
| `x` | Unix timestamp ms | 1775654400000 |

### Pattern Date per Marketplace

```javascript
// Amazon (ISO 8601)
{{ $json.purchaseDate }}  // già in formato ISO

// Fattura italiana (GG/MM/AAAA)
{{ $now.toFormat('dd/MM/yyyy') }}

// Scadenza 30 giorni
{{ $now.plus({days: 30}).toFormat('dd/MM/yyyy') }}

// Data ordine + lead time spedizione
{{ DateTime.fromISO($json.orderDate).plus({days: $json.leadTime}).toISO() }}

// Confronto: ordine più vecchio di 48 ore?
{{ $now.diff(DateTime.fromISO($json.orderDate), 'hours').hours > 48 }}
```

---

## 2.5 JMESPath per Query JSON

n8n integra **JMESPath** per query potenti su strutture JSON complesse.

### Sintassi

```javascript
// JavaScript
{{ $jmespath($json, 'searchString') }}

// Python (nel Code node)
_jmespath(object, searchString)
```

**Nota**: L'ordine parametri è oggetto prima, stringa di ricerca dopo (diverso dalla spec JMESPath standard).

### Esempi

```javascript
// Estrazione array di nomi prodotto
{{ $jmespath($json, 'products[*].name') }}
// → ["Prodotto A", "Prodotto B", "Prodotto C"]

// Filtraggio per prezzo
{{ $jmespath($json, 'products[?price > `100`]') }}
// → solo prodotti con prezzo > 100

// Proiezione: estrai solo nome e prezzo
{{ $jmespath($json, 'products[*].{nome: name, prezzo: price}') }}

// Flatten di array annidati
{{ $jmespath($json, 'orders[*].items[]') }}

// Max/Min
{{ $jmespath($json, 'max_by(products, &price)') }}
{{ $jmespath($json, 'min_by(products, &price)') }}

// Conteggio
{{ $jmespath($json, 'length(products)') }}

// Sorting
{{ $jmespath($json, 'sort_by(products, &price)') }}
```

### Proiezioni Supportate

- **List Projections**: `[*]` — tutti gli elementi
- **Slice Projections**: `[0:5]` — sottoinsieme
- **Object Projections**: `{nome: name, prezzo: price}` — reshape
- **Flatten Projections**: `[]` — appiattimento
- **Filter Projections**: `[?condition]` — filtro

---

## 2.6 Regular Expressions

```javascript
// Match: trova pattern
{{ $json.text.match(/\d{13}/) }}  // trova EAN-13
// → ["5901234567890"]

// Match globale: trova tutti
{{ $json.text.match(/\d{13}/g) }}  // trova tutti gli EAN
// → ["5901234567890", "4006381333931"]

// Replace con regex
{{ $json.title.replace(/[^a-zA-Z0-9\s]/g, '') }}  // rimuove caratteri speciali

// Test: verifica pattern
{{ /^\d{13}$/.test($json.ean) }}  // EAN valido?
// → true o false

// Named capture groups
{{ $json.sku.match(/^(?<brand>[A-Z]{3})-(?<code>\d+)$/) }}
// → { brand: "FOR", code: "12345" }

// Validazione email
{{ /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test($json.email) }}
```

---

## 2.7 Pattern Avanzati e Helper Functions

### Pattern Condizionale Multi-Branch

```javascript
// Ternario semplice
{{ $json.stock > 0 ? 'Disponibile' : 'Esaurito' }}

// Ternario annidato (max 2-3 livelli)
{{ $json.stock > 50 ? 'In Stock' : $json.stock > 0 ? 'Ultimi Pezzi' : 'Esaurito' }}

// Null coalescing
{{ $json.price ?? 0 }}
{{ $json.title ?? $json.name ?? 'Senza nome' }}

// Optional chaining
{{ $json.user?.address?.city ?? 'N/A' }}
```

### Pattern IIFE per Logica Complessa

```javascript
{{ (() => {
  const price = $json.costPrice;
  const margin = 0.30;
  const vat = 0.22;
  const commission = {
    'amazon': 0.15,
    'ebay': 0.12,
    'kaufland': 0.10
  }[$json.marketplace] || 0.10;
  
  return Math.round(price * (1 + margin) / (1 - commission) * (1 + vat) * 100) / 100;
})() }}
```

---

## 2.8 Pattern Expressions per E-commerce

| Pattern | Espressione |
|---------|-------------|
| Prezzo + margine 30% | `{{ Math.round($json.cost * 1.30 * 100) / 100 }}` |
| Prezzo + IVA 22% | `{{ ($json.price * 1.22).toFixed(2) }}` |
| Validazione EAN-13 | `{{ /^\d{13}$/.test($json.ean) }}` |
| Padding EAN a 13 cifre | `{{ $json.ean.toString().padStart(13, '0') }}` |
| SKU composito | `{{ $json.brand.substring(0,3).toUpperCase() + '-' + $json.ean }}` |
| Stato stock | `{{ $json.stock > 50 ? 'In Stock' : $json.stock > 0 ? 'Low' : 'Out' }}` |
| Pulizia titolo | `{{ $json.title.trim().replace(/\s+/g, ' ') }}` |
| Data fattura IT | `{{ $now.toFormat('dd/MM/yyyy') }}` |
| Scadenza 30gg | `{{ $now.plus({days: 30}).toFormat('dd/MM/yyyy') }}` |
| Prezzo formattato IT | `{{ Number($json.price).toLocaleString('it-IT', {minimumFractionDigits: 2}) }}` |

---

# 3. Routing e Data Flow — Nodi Completi

## 3.1 IF Node (Branch Binario)

Divide il workflow in due percorsi: **True** e **False**.

**Tipi di condizione**: Strings, integers, arrays, objects, booleans, dates

**Operatori**: equals, does not equal, greater than, less than, contains, regex match, is empty, is not empty

**Logica multipla**:
- AND: tutte le condizioni devono essere vere
- OR: almeno una condizione deve essere vera

**Opzione**: "Convert Types When Required" per conversione automatica tipi.

**Best practice**: Usa IF solo per split binari (2 percorsi). Per 3+ branch, usa Switch.

### Esempio Vendiamonoi

```
IF: $json.stock > 0 AND $json.price > 0 AND $json.ean.length === 13
  → True: Pubblica su marketplace
  → False: Skip / Log prodotto invalido
```

---

## 3.2 Switch Node (Multi-Branch)

Routa gli item verso output multipli.

### Modalità Rules (Default)

- Builder visuale condizioni
- Item routato al primo branch che matcha
- Opzione "Send data to all matching outputs" per routing multiplo

### Modalità Expression

- Espressione JavaScript completa
- Ritorna numero output route
- Per logica di routing complessa

### Fallback Output

| Opzione | Comportamento |
|---------|---------------|
| Extra Output | Item non matchati vanno a output separato |
| Output 0 | Item non matchati vanno al primo output |
| Discard | Item non matchati vengono scartati |

### Esempio Vendiamonoi: Routing per Marketplace

```
Switch Node:
  Rule 1: $json.marketplace === 'amazon' → Output 1 (Amazon flow)
  Rule 2: $json.marketplace === 'ebay' → Output 2 (eBay flow)
  Rule 3: $json.marketplace === 'kaufland' → Output 3 (Kaufland flow)
  Fallback: Extra Output → Output 4 (Other marketplaces)
```

---

## 3.3 Merge Node (Combinazione Dati)

Combina dati da stream multipli. Aspetta tutti gli input prima di processare.

### Modalità Merge

| Modalità | Descrizione | Uso |
|----------|-------------|-----|
| **Append** | Concatena item uno dopo l'altro | Unire risultati da fonti diverse |
| **Matching Fields** | Matcha item per valore campo | JOIN simile a SQL |
| **Position** | Merge per posizione (1° con 1°, 2° con 2°) | Dataset ordinati e allineati |
| **All Possible Combinations** | Prodotto cartesiano | Tutte le combinazioni |
| **SQL** | Join SQL-style con condizioni | Query complesse |

### Proprietà Merge

- **Deep Merge**: Merge a tutti i livelli inclusi oggetti annidati
- **Shallow Merge**: Merge solo proprietà top-level
- Mapping campi personalizzato per matching

### Esempio: Arricchimento Prodotti

```
[Get Products from SellRapido] → 
                                  [Merge (Matching: sku)] → Prodotto arricchito
[Get Prices from Supplier]    → 
```

---

## 3.4 Split In Batches / Loop Over Items

Itera attraverso gli item in batch gestibili.

### Parametri

- **Batch Size**: Numero item per iterazione
- **Reset Option**: Reinizializza dati incoming ad ogni loop

### Variabili Contesto

```javascript
// Indice iterazione corrente
{{ $('Loop Over Items').context['currentRunIndex'] }}

// Ci sono ancora item?
{{ $('Loop Over Items').context['noItemsLeft'] }}

// Reset property
{{ $('Loop Over Items').context['done'] }}
```

### Flusso Tipico

```
500 prodotti → Split In Batches (size: 50) → Loop:
  Batch 1 (50 items) → Process → API Call
  Batch 2 (50 items) → Process → API Call
  ...
  Batch 10 (50 items) → Process → API Call
→ Done output: tutti i risultati combinati
```

---

## 3.5 Code Node (JavaScript/Python)

Sostituisce i deprecati Function e Function Item nodes (v0.198.0+).

### Modalità Esecuzione

| Modalità | Descrizione | Uso |
|----------|-------------|-----|
| **Run Once for All Items** | Codice eseguito una volta | Aggregazione, trasformazione batch |
| **Run Once for Each Item** | Codice eseguito per ogni item | Trasformazione per-item |

### Linguaggi

- **JavaScript (Node.js)**: Performance migliore, consigliato
- **Python**: Capabilities addizionali ma più lento

### Esempio: Calcolo Prezzo Marketplace

```javascript
// Code Node — Run Once for All Items
const commissions = {
  'amazon': 0.15,
  'ebay': 0.12,
  'kaufland': 0.10,
  'fnac': 0.13,
  'carrefour': 0.11
};

return items.map(item => {
  const cost = item.json.costPrice;
  const mp = item.json.marketplace;
  const commission = commissions[mp] || 0.10;
  const margin = 0.30;
  const vat = 0.22;
  
  item.json.sellingPrice = Math.round(
    cost * (1 + margin) / (1 - commission) * (1 + vat) * 100
  ) / 100;
  
  item.json.marginAmount = item.json.sellingPrice - cost;
  
  return item;
});
```

---

## 3.6 Set Node (Edit Fields)

Il nodo di trasformazione più usato in n8n.

### Modalità

| Modalità | Descrizione | Uso |
|----------|-------------|-----|
| **Manual Mapping** | Interfaccia visuale drag-and-drop | Quando mantieni alcuni campi input |
| **JSON Output** | JSON raw con espressioni | Quando sostituisci tutto l'output |

### Dot Notation per Strutture Annidate

Campo `address.zip` crea automaticamente:
```json
{
  "address": {
    "zip": "00100"
  }
}
```

---

## 3.7 HTTP Request Node

Connettore universale per qualsiasi API REST.

### Metodi Supportati

GET, POST, PUT, PATCH, DELETE, HEAD

### Autenticazione

Tutti i tipi: Bearer Token, Basic Auth, Header Auth, Query Auth, OAuth1, OAuth2, JWT, Digest, SSL Certificates.

### Paginazione Built-in

| Tipo | Configurazione |
|------|----------------|
| **Response Contains Next URL** | API ritorna URL pagina successiva |
| **Update a Parameter** | Modifica parametro (page number) ad ogni richiesta |

```javascript
// Variabile paginazione
$pageCount  // numero pagine già fetchate
$pageCount + 1  // prossima pagina
```

### Esempio: Paginazione API Marketplace

```
HTTP Request Node:
  URL: https://api.marketplace.com/orders
  Method: GET
  Query Parameters:
    page: {{ $pageCount + 1 }}
    per_page: 100
  Pagination:
    Type: Update a Parameter
    Parameter: page
    Stop: Response has empty body
```

---

## 3.8 Item Lists Nodes

Famiglia di nodi per manipolazione liste.

| Nodo | Funzione | Uso Tipico |
|------|----------|------------|
| **Aggregate** | Raggruppa items insieme | Combinare in record singolo |
| **Remove Duplicates** | Rimuove duplicati | Dedup per campo specifico |
| **Sort** | Ordina items | Per prezzo, data, nome |
| **Limit** | Limita numero items | Top-N, paginazione |
| **Split Out** | Separa liste in items individuali | Flatten strutture annidate |
| **Summarize** | Aggregazione pivot-table | Count, sum, avg, group by |

---

## 3.9 Compare Datasets Node

Confronta due stream di input e identifica differenze.

### Output Branches

| Branch | Descrizione |
|--------|-------------|
| **In A only** | Items solo nel primo input |
| **In B only** | Items solo nel secondo input |
| **Same** | Items identici in entrambi |
| **Different** | Stessi match fields ma altre differenze |

### Gestione Differenze

- Use Input A Version: A come source of truth
- Use Input B Version: B come source of truth
- Mix Versions: Campi diversi da input diversi

### Esempio: Sync Stock

```
[Get Stock from Supplier] → 
                             [Compare Datasets (match: sku)] →
[Get Stock from Marketplace] → 
                                Different → Update marketplace
                                In A only → New products to publish
                                In B only → Products to disable
```

---

## 3.10 Wait Node

Mette in pausa il workflow e riprende quando una condizione è soddisfatta.

### Condizioni Resume

| Condizione | Descrizione |
|-----------|-------------|
| **Time Interval** | Attende durata specifica |
| **Specified Time** | Attende data/ora specifica |
| **Webhook Call** | Attende trigger webhook |
| **Form Submission** | Attende compilazione form |

**Gestione pause lunghe**: n8n serializza l'esecuzione e la salva. I dati NON vengono persi durante l'attesa.

---

# 4. Trasformazione Dati — Pattern Completi

## 4.1 Data Mapping tra Nodi

### Metodi

- **Drag and Drop**: Interfaccia visuale, auto-genera espressioni
- **Expressions Editor**: `$json.<field>`, `$('<node-name>').item.json.<field>`
- **Code Node**: Accesso programmatico completo

## 4.2 JSON Parsing e Building

```javascript
// Parse JSON da stringa
{{ JSON.parse($json.jsonString) }}

// Build JSON in Code node
return [{
  json: {
    sku: $json.sku,
    title: $json.title.trim(),
    price: parseFloat($json.price),
    stock: parseInt($json.stock),
    marketplace: 'amazon'
  }
}];
```

## 4.3 Binary Data Handling

I file in n8n esistono in un layer **Binary** separato dal JSON.

### Nodi Core per Binary

| Nodo | Funzione |
|------|----------|
| **Convert to File** | Input data → file output |
| **Extract From File** | Binary format → JSON data |
| **Read/Write Files from Disk** | I/O file su disco |

### Accesso

```javascript
// Accesso binary dell'item corrente
{{ $input.item.binary.data }}

// Metadata file
{{ $input.item.binary.data.fileName }}
{{ $input.item.binary.data.mimeType }}
{{ $input.item.binary.data.fileExtension }}
```

## 4.4 Conversione Tipi

| Da | A | Metodo |
|----|---|--------|
| Stringa | Numero | `Number(val)`, `parseInt()`, `parseFloat()` |
| Numero | Stringa | `.toString()`, `String(val)` |
| Stringa | Array | `.split(delimiter)` |
| Array | Stringa | `.join(delimiter)` |
| JSON String | Oggetto | `JSON.parse(str)` |
| Oggetto | JSON String | `JSON.stringify(obj)` |
| ISO String | Luxon DateTime | `DateTime.fromISO(str)` |
| Timestamp | Luxon DateTime | `DateTime.fromMillis(ts)` |

## 4.5 Filtraggio e Deduplicazione

### Filtraggio nel Code Node

```javascript
// Filtra prodotti attivi con stock
return items.filter(item => 
  item.json.active === true && 
  item.json.stock > 0 && 
  item.json.price > 0
);
```

### Deduplicazione

- **Remove Duplicates Node**: Per campo specifico o tutti i campi
- **Compare against previous executions**: Dedup cross-esecuzioni

## 4.6 Aggregazione e Summarization

### Summarize Node

Operazioni pivot-table: count, sum, average, min, max con group by.

### Code Node Aggregation

```javascript
// Totale vendite per marketplace
const totals = {};
for (const item of items) {
  const mp = item.json.marketplace;
  if (!totals[mp]) totals[mp] = { marketplace: mp, count: 0, revenue: 0 };
  totals[mp].count++;
  totals[mp].revenue += item.json.total;
}
return Object.values(totals).map(t => ({ json: t }));
```

---

# 5. Pattern Vendiamonoi — n8n per Marketplace

## 5.1 Workflow: Sync Stock Multi-Fornitore

```
Schedule Trigger (ogni 2 ore)
  → HTTP Request: GET stock da Fornitore A
  → HTTP Request: GET stock da Fornitore B
  → Merge (Append)
  → Code: normalizza formato
  → Compare Datasets (match: sku) vs stock corrente Supabase
    → Different: HTTP PATCH → update Supabase + notify SellRapido
    → In A only: nuovo prodotto → insert Supabase
    → Same: skip (nessun cambiamento)
```

## 5.2 Workflow: Gestione Ordini con Retry

```
Webhook Trigger (ordine da SellRapido)
  → IF: payload valido?
    → True:
      → Switch (marketplace):
        → Amazon: flow specifico Amazon
        → eBay: flow specifico eBay
        → Other: flow generico
      → HTTP Request: salva in Supabase
      → HTTP Request: crea fattura Fatture in Cloud
    → False:
      → Log errore
      → Respond 400
```

## 5.3 Workflow: Report Giornaliero

```
Schedule Trigger (07:00)
  → Supabase: GET ordini ultimi 24h
  → Summarize: totali per marketplace
  → Code: formatta report
  → Slack: invia summary su #monitoring
```

## 5.4 n8n vs Make.com per Vendiamonoi

| Aspetto | Make.com | n8n |
|---------|----------|-----|
| **Pricing** | Per operazione/credito | Self-hosted: gratis. Cloud: per esecuzione |
| **Scalabilità** | Limitato dal piano | Illimitata (self-hosted) |
| **Bulk processing** | Costoso per milioni di ops | Ideale per batch pesanti |
| **UI** | Più user-friendly | Più tecnico, più flessibile |
| **Expressions** | IML (limitato) | JavaScript completo |
| **Self-hosting** | No | Sì (Docker, Kubernetes) |
| **Uso consigliato** | Orchestrazione, logica business | Batch processing, ETL pesante |

**Strategia Vendiamonoi**: Make.com per orchestrazione real-time e logica business. n8n (futuro) per batch processing pesante, ETL cataloghi grandi, operazioni bulk dove il costo per-operazione di Make diventa proibitivo.

---

## FONTI E RIFERIMENTI

### Documentazione Ufficiale n8n
- [Credentials Library](https://docs.n8n.io/integrations/builtin/credentials/)
- [Expressions](https://docs.n8n.io/code/expressions/)
- [Expression Reference](https://docs.n8n.io/data/expression-reference/)
- [Built-in Methods](https://docs.n8n.io/code/builtin/overview/)
- [DateTime with Luxon](https://docs.n8n.io/code/cookbook/luxon/)
- [JMESPath](https://docs.n8n.io/code/cookbook/jmespath/)
- [Code Node](https://docs.n8n.io/code/code-node/)
- [External Secrets](https://docs.n8n.io/external-secrets/)
- [IF Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.if/)
- [Switch Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.switch/)
- [Merge Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.merge/)
- [Loop Over Items](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.splitinbatches/)
- [Set Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.set/)
- [HTTP Request](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/)
- [Compare Datasets](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.comparedatasets/)
- [Wait Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.wait/)
- [Binary Data](https://docs.n8n.io/data/binary-data/)
- [Data Mapping](https://docs.n8n.io/data/data-mapping/)
- [Environment Variables](https://docs.n8n.io/hosting/configuration/environment-variables/)
- [Encryption Key](https://docs.n8n.io/hosting/configuration/configuration-examples/encryption-key/)

---

**Documento creato**: 2026-04-06
**Complemento**: Part 2 della serie n8n Architecture. Vedi anche Part 1 (Core Architecture), Part 3 (Error Handling, Sub-workflows), Part 4 (Self-hosting, Performance, Production).
