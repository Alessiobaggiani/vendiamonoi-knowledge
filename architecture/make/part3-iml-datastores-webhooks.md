# Make.com — Part 3: IML, Data Stores, Webhooks e HTTP Module
## EXPERT-LEVEL TECHNICAL DEEP-DIVE

**Documento**: Riferimento tecnico esaustivo per IML expressions, Data Stores avanzati, Webhooks e HTTP Module
**Target**: Senior automation engineers e architetti di sistema Vendiamonoi.it
**Versione**: Make.com 2026
**Ultimo aggiornamento**: 2026-04-06
**Complemento a**: README.md (overview architetturale) — questo documento è il deep-dive tecnico

---

## INDICE COMPLETO

1. [IML — Integration Markup Language: Riferimento Completo](#1-iml--integration-markup-language-riferimento-completo)
   - 1.1 Sintassi Fondamentale e Regole
   - 1.2 Operatori (Algebrici, Logici, Relazionali)
   - 1.3 Funzioni Stringa (20+ funzioni)
   - 1.4 Funzioni Matematiche (10+ funzioni)
   - 1.5 Funzioni Data/Ora (15+ funzioni)
   - 1.6 Funzioni Array (15+ funzioni)
   - 1.7 Funzioni Generali e Conversione (15+ funzioni)
   - 1.8 Funzioni Binarie e Hash
   - 1.9 Pattern Avanzati e Nesting
   - 1.10 Type Coercion e Gestione Null/Empty
   - 1.11 Pattern IML per E-commerce Marketplace
   - 1.12 Performance Tips e Gotchas Critici
2. [Data Stores — Database Integrato Avanzato](#2-data-stores--database-integrato-avanzato)
   - 2.1 Architettura Interna
   - 2.2 Limiti per Piano
   - 2.3 Data Structures (Schema Definition)
   - 2.4 Moduli CRUD Completi (9 moduli)
   - 2.5 Filtri e Query Avanzate
   - 2.6 Concorrenza e Locking
   - 2.7 API REST per Data Stores
   - 2.8 Pattern Avanzati per E-commerce
   - 2.9 Quando Usare Data Stores vs Database Esterno
3. [Webhooks — Trigger Real-Time Avanzati](#3-webhooks--trigger-real-time-avanzati)
   - 3.1 Tipi di Webhook
   - 3.2 Configurazione e Data Structure Determination
   - 3.3 Sicurezza (IP Whitelist, HMAC, Token)
   - 3.4 Webhook Response Module
   - 3.5 Coda e Gestione Queue
   - 3.6 Rate Limits e Payload Limits
   - 3.7 Pattern Webhook per Marketplace
4. [HTTP Module — Connettore Universale Completo](#4-http-module--connettore-universale-completo)
   - 4.1 Varianti del Modulo HTTP (6 moduli)
   - 4.2 Autenticazione (Basic, OAuth 2.0, API Key, mTLS)
   - 4.3 Request Body Types
   - 4.4 Paginazione Automatica (Offset, Cursor, Page)
   - 4.5 Configurazioni Avanzate (Proxy, TLS, Timeout, Redirect)
   - 4.6 Response Parsing e Header Extraction
   - 4.7 File Upload Multipart
5. [Pattern Vendiamonoi — Applicazioni Concrete](#5-pattern-vendiamonoi--applicazioni-concrete)

---

# 1. IML — Integration Markup Language: Riferimento Completo

IML (Integromat Markup Language) è il linguaggio di espressione nativo di Make.com. Viene utilizzato in ogni campo mapper, condizione di filtro, router e modulo di trasformazione. Padroneggiare IML è fondamentale per costruire automazioni complesse e performanti.

## 1.1 Sintassi Fondamentale e Regole

### Struttura Base delle Espressioni

Le espressioni IML sono racchiuse tra doppie parentesi graffe (mustache syntax):

```
Testo letterale fuori dalle espressioni
{{funzione(parametro)}} — espressione IML
Misto: "prefisso-{{espressione}}-suffisso"
```

### REGOLA CRITICA: Separatore Parametri

**In IML si usa il PUNTO E VIRGOLA (;) come separatore, NON la virgola.**

```
✅ CORRETTO:  {{if(status == "active"; "Attivo"; "Inattivo")}}
❌ SBAGLIATO: {{if(status == "active", "Attivo", "Inattivo")}}
```

Questo è l'errore più comune per chi viene da JavaScript/Python. La virgola in IML causa errori silenziosi o risultati inattesi.

### Accesso alle Proprietà

```
{{body.data.property}}              — dot notation per proprietà note
{{body.data.nested.deep.property}}  — accesso annidato multi-livello
{{get(body; "dynamic-key")}}        — accesso con chiave dinamica
{{body.data[1]}}                     — primo elemento array (1-based!)
{{body.data[-1]}}                    — ultimo elemento array
```

### REGOLA CRITICA: Indicizzazione Array 1-Based

**IML usa indicizzazione 1-based, NON 0-based come JavaScript.**

```
✅ CORRETTO:  {{array[1]}}   — primo elemento
❌ SBAGLIATO: {{array[0]}}   — NON funziona come ci si aspetta
{{array[-1]}}               — ultimo elemento
{{array[-2]}}               — penultimo elemento
```

### Stringhe Letterali

Si possono usare sia apici singoli che doppi:

```
{{"Hello, " + item.name}}
{{'Hello, ' + item.name}}
```

---

## 1.2 Operatori

### Operatori Algebrici

| Operatore | Descrizione | Esempio | Risultato |
|-----------|-------------|---------|-----------|
| `+` | Addizione (numeri) o concatenazione (stringhe) | `{{5 + 3}}` | `8` |
| `-` | Sottrazione | `{{10 - 4}}` | `6` |
| `*` | Moltiplicazione | `{{3 * 7}}` | `21` |
| `/` | Divisione | `{{20 / 4}}` | `5` |
| `%` | Modulo (resto) | `{{17 % 5}}` | `2` |

**Nota sulla divisione per zero**: Restituisce `Infinity` o `-Infinity`, non un errore.

### Operatori di Uguaglianza

| Operatore | Alias | Descrizione |
|-----------|-------|-------------|
| `==` | `=`, `===` | Uguaglianza stretta |
| `!=` | `!==` | Disuguaglianza stretta |

**Attenzione**: In IML `==`, `=` e `===` sono tutti equivalenti (uguaglianza stretta). Non esiste l'uguaglianza debole di JavaScript.

### Operatori Relazionali

| Operatore | Descrizione | Esempio |
|-----------|-------------|---------|
| `<` | Minore di | `{{price < 100}}` |
| `<=` | Minore o uguale | `{{stock <= 0}}` |
| `>` | Maggiore di | `{{quantity > 50}}` |
| `>=` | Maggiore o uguale | `{{rating >= 4.5}}` |

### Operatori Logici

| Operatore | Descrizione | Esempio |
|-----------|-------------|---------|
| `&&` | AND logico | `{{stock > 0 && price < 100}}` |
| `\|\|` | OR logico | `{{status == "new" \|\| status == "pending"}}` |
| `!` | NOT logico | `{{!processed}}` |

### Keywords e Costanti

| Keyword | Tipo | Descrizione |
|---------|------|-------------|
| `true` | Boolean | Valore vero |
| `false` | Boolean | Valore falso |
| `null` | Null | Valore nullo/indefinito |
| `empty` | Special | Valore vuoto (equivale a non definito) |
| `emptystring` | String | Stringa vuota (`""`) |

---

## 1.3 Funzioni Stringa — Riferimento Completo

### Tabella Completa

| Funzione | Sintassi | Descrizione | Esempio → Risultato |
|----------|----------|-------------|---------------------|
| `ascii` | `ascii(testo)` | Rimuove tutti i caratteri non-ASCII | `ascii("ěščřžýáíé")` → `"escrzyaie"` |
| `base64` | `base64(testo)` | Codifica testo in Base64 | `base64("Hello")` → `"SGVsbG8="` |
| `capitalize` | `capitalize(testo)` | Maiuscola il primo carattere | `capitalize("hello world")` → `"Hello world"` |
| `contains` | `contains(testo; cerca)` | Verifica se il testo contiene la stringa cercata | `contains("hello world"; "world")` → `true` |
| `decodeurl` | `decodeurl(testo)` | Decodifica caratteri URL-encoded | `decodeurl("hello%20world")` → `"hello world"` |
| `encodeurl` | `encodeurl(testo)` | Codifica caratteri speciali per URL | `encodeurl("hello world")` → `"hello%20world"` |
| `endsWith` | `endsWith(testo; cerca)` | Verifica se il testo termina con la stringa | `endsWith("hello.jpg"; ".jpg")` → `true` |
| `escapehtml` | `escapehtml(testo)` | Escape dei tag HTML | `escapehtml("<div>")` → `"&lt;div&gt;"` |
| `escapemarkdown` | `escapemarkdown(testo)` | Escape dei tag Markdown | `escapemarkdown("# titolo")` → `"\# titolo"` |
| `indexOf` | `indexOf(testo; cerca)` | Posizione della prima occorrenza (1-based) | `indexOf("hello"; "l")` → `3` |
| `length` | `length(testo)` | Numero di caratteri | `length("ciao")` → `4` |
| `lower` / `lowercase` | `lower(testo)` | Converte in minuscolo | `lower("HELLO")` → `"hello"` |
| `upper` / `uppercase` | `upper(testo)` | Converte in maiuscolo | `upper("hello")` → `"HELLO"` |
| `md5` | `md5(testo)` | Hash MD5 del testo | `md5("password")` → `"5f4dcc3b5aa765d61d8327deb882cf99"` |
| `replace` | `replace(testo; cerca; sostituisci)` | Sostituisce TUTTE le occorrenze | `replace("hello"; "l"; "r")` → `"herro"` |
| `sha1` | `sha1(testo; [chiave]; [encoding])` | Hash SHA1 o HMAC-SHA1 | `sha1("testo")` → hash hex |
| `sha256` | `sha256(testo; [chiave]; [encoding])` | Hash SHA256 o HMAC-SHA256 | `sha256("testo"; "secret"; "hex")` → HMAC hex |
| `sha512` | `sha512(testo; [chiave]; [encoding])` | Hash SHA512 o HMAC-SHA512 | `sha512("testo"; "secret"; "base64")` → HMAC base64 |
| `split` | `split(testo; separatore)` | Divide il testo in array | `split("a,b,c"; ",")` → `["a", "b", "c"]` |
| `startsWith` | `startsWith(testo; cerca)` | Verifica se il testo inizia con la stringa | `startsWith("hello"; "he")` → `true` |
| `trim` | `trim(testo)` | Rimuove spazi iniziali e finali | `trim("  hello  ")` → `"hello"` |
| `substring` | `substring(testo; inizio; [lunghezza])` | Estrae porzione di testo | `substring("hello world"; 1; 5)` → `"hello"` |

### Funzioni Hash — Dettaglio HMAC

Le funzioni `sha1`, `sha256`, `sha512` supportano tre modalità:

**Modalità 1 — Hash semplice**:
```
{{sha256("testo")}}
→ restituisce hash hex del testo
```

**Modalità 2 — HMAC con chiave**:
```
{{sha256("testo"; "chiave_segreta")}}
→ restituisce HMAC-SHA256 in hex (default)
```

**Modalità 3 — HMAC con encoding specifico**:
```
{{sha256("testo"; "chiave_segreta"; "hex")}}
{{sha256("testo"; "chiave_segreta"; "base64")}}
{{sha256("testo"; "chiave_segreta"; "latin1")}}
```

**Encoding disponibili**: `hex` (default), `base64`, `latin1`

**Caso d'uso Vendiamonoi**: Verifica HMAC delle webhook signatures dai marketplace:
```
{{sha256(body.rawPayload; "webhook_secret_key"; "hex")}}
```

---

## 1.4 Funzioni Matematiche — Riferimento Completo

| Funzione | Sintassi | Descrizione | Esempio → Risultato |
|----------|----------|-------------|---------------------|
| `abs` | `abs(numero)` | Valore assoluto | `abs(-5.7)` → `5.7` |
| `average` | `average(array)` o `average(n1; n2; ...)` | Media aritmetica | `average(10; 20; 30)` → `20` |
| `ceil` | `ceil(numero)` | Arrotonda per eccesso | `ceil(4.1)` → `5` |
| `floor` | `floor(numero)` | Arrotonda per difetto | `floor(4.9)` → `4` |
| `max` | `max(array)` o `max(n1; n2; ...)` | Valore massimo | `max(10; 20; 5)` → `20` |
| `min` | `min(array)` o `min(n1; n2; ...)` | Valore minimo | `min(10; 20; 5)` → `5` |
| `round` | `round(numero; [decimali])` | Arrotondamento standard | `round(4.567; 2)` → `4.57` |
| `sum` | `sum(array)` o `sum(n1; n2; ...)` | Somma dei valori | `sum(10; 20; 30)` → `60` |
| `formatNumber` | `formatNumber(num; dec; [sep_dec]; [sep_migliaia])` | Formatta numero con separatori | `formatNumber(1234.5; 2; ","; ".")` → `"1.234,50"` |
| `parseNumber` | `parseNumber(testo; [sep_decimale])` | Converte testo in numero | `parseNumber("1.234,56"; ",")` → `1234.56` |

### Pattern Prezzi per Marketplace Europei

**Formato italiano** (virgola decimale, punto migliaia):
```
{{formatNumber(price; 2; ","; ".")}}
→ 1.234,50 €
```

**Formato tedesco** (uguale all'italiano):
```
{{formatNumber(price; 2; ","; ".")}}
→ 1.234,50 €
```

**Formato UK/US** (punto decimale, virgola migliaia):
```
{{formatNumber(price; 2; "."; ",")}}
→ 1,234.50
```

**Formato francese** (virgola decimale, spazio migliaia):
```
{{formatNumber(price; 2; ","; " ")}}
→ 1 234,50 €
```

**Parsing prezzo da stringa marketplace**:
```
{{parseNumber("1.234,56 €"; ",")}}
→ 1234.56 (numero utilizzabile per calcoli)
```

### Edge Cases Matematici

- `round()` senza parametro decimali → arrotonda all'intero più vicino
- `formatNumber()` con decimali negativi → arrotonda a potenze di 10 (es. -1 arrotonda alle decine)
- Divisione per zero → restituisce `Infinity`, non errore
- `parseNumber("abc")` → restituisce `NaN` o errore
- `sum()` su array vuoto → restituisce `0`

---

## 1.5 Funzioni Data/Ora — Riferimento Completo

| Funzione | Sintassi | Descrizione | Esempio |
|----------|----------|-------------|---------|
| `now` | `now()` | Data/ora corrente (ISO 8601) | `now()` → `2026-04-06T12:30:45.000Z` |
| `addDays` | `addDays(data; numero)` | Aggiunge/sottrae giorni | `addDays(now(); 5)` |
| `addHours` | `addHours(data; numero)` | Aggiunge/sottrae ore | `addHours(now(); -2)` |
| `addMinutes` | `addMinutes(data; numero)` | Aggiunge/sottrae minuti | `addMinutes(now(); 30)` |
| `addSeconds` | `addSeconds(data; numero)` | Aggiunge/sottrae secondi | `addSeconds(now(); 60)` |
| `addMonths` | `addMonths(data; numero)` | Aggiunge/sottrae mesi | `addMonths(now(); 1)` |
| `addYears` | `addYears(data; numero)` | Aggiunge/sottrae anni | `addYears(now(); -1)` |
| `formatDate` | `formatDate(data; formato; [timezone])` | Formatta data come stringa | `formatDate(now(); "DD/MM/YYYY")` |
| `parseDate` | `parseDate(testo; formato; [timezone])` | Converte testo in data | `parseDate("06/04/2026"; "DD/MM/YYYY")` |
| `setDate` | `setDate(data; giorno)` | Imposta il giorno del mese | `setDate(now(); 1)` → primo del mese |
| `setHour` | `setHour(data; ora)` | Imposta l'ora | `setHour(now(); 0)` → mezzanotte |
| `setMinute` | `setMinute(data; minuto)` | Imposta i minuti | `setMinute(now(); 0)` |
| `setSecond` | `setSecond(data; secondo)` | Imposta i secondi | `setSecond(now(); 0)` |
| `setMonth` | `setMonth(data; mese)` | Imposta il mese (1-12) | `setMonth(now(); 1)` → gennaio |
| `setYear` | `setYear(data; anno)` | Imposta l'anno | `setYear(now(); 2026)` |

### Token di Formato Data

| Token | Descrizione | Esempio |
|-------|-------------|---------|
| `YYYY` | Anno a 4 cifre | `2026` |
| `YY` | Anno a 2 cifre | `26` |
| `MM` | Mese a 2 cifre (01-12) | `04` |
| `M` | Mese senza zero iniziale | `4` |
| `DD` | Giorno a 2 cifre (01-31) | `06` |
| `D` | Giorno senza zero iniziale | `6` |
| `HH` | Ora a 2 cifre 24h (00-23) | `14` |
| `hh` | Ora a 2 cifre 12h (01-12) | `02` |
| `mm` | Minuti a 2 cifre (00-59) | `30` |
| `ss` | Secondi a 2 cifre (00-59) | `45` |
| `SSS` | Millisecondi a 3 cifre | `123` |
| `Z` | Offset timezone | `+02:00` |
| `A` | AM/PM | `PM` |
| `dddd` | Nome giorno completo | `Monday` |
| `ddd` | Nome giorno abbreviato | `Mon` |
| `MMMM` | Nome mese completo | `April` |
| `MMM` | Nome mese abbreviato | `Apr` |
| `X` | Unix timestamp (secondi) | `1775654400` |
| `x` | Unix timestamp (millisecondi) | `1775654400000` |

### Pattern Data per Marketplace

**Amazon Seller Central** (formato ISO 8601):
```
{{formatDate(order.purchaseDate; "YYYY-MM-DDTHH:mm:ssZ")}}
```

**eBay** (formato ISO 8601):
```
{{formatDate(order.creationDate; "YYYY-MM-DDTHH:mm:ss.SSSZ")}}
```

**Fatturazione italiana** (GG/MM/AAAA):
```
{{formatDate(now(); "DD/MM/YYYY")}}
```

**Scadenza pagamento +30 giorni**:
```
{{formatDate(addDays(invoice.date; 30); "DD/MM/YYYY")}}
```

**Inizio mese corrente** (per report):
```
{{formatDate(setDate(now(); 1); "YYYY-MM-DD")}}
```

**Fine mese precedente**:
```
{{formatDate(addDays(setDate(now(); 1); -1); "YYYY-MM-DD")}}
```

**Conversione timezone per marketplace diversi**:
```
{{formatDate(now(); "YYYY-MM-DD HH:mm"; "Europe/Rome")}}
{{formatDate(now(); "YYYY-MM-DD HH:mm"; "Europe/Berlin")}}
{{formatDate(now(); "YYYY-MM-DD HH:mm"; "Europe/Paris")}}
{{formatDate(now(); "YYYY-MM-DD HH:mm"; "Europe/Madrid")}}
```

---

## 1.6 Funzioni Array — Riferimento Completo

| Funzione | Sintassi | Descrizione | Esempio → Risultato |
|----------|----------|-------------|---------------------|
| `add` | `add(array; val1; val2; ...)` | Aggiunge valori all'array | `add([1;2]; 3; 4)` → `[1,2,3,4]` |
| `contains` | `contains(array; valore)` | Verifica se l'array contiene il valore | `contains([1;2;3]; 2)` → `true` |
| `distinct` | `distinct(array; [chiave])` | Rimuove duplicati | `distinct([1;2;2;3])` → `[1,2,3]` |
| `first` | `first(array)` | Primo elemento | `first(["a";"b";"c"])` → `"a"` |
| `flatten` | `flatten(array; [profondità])` | Appiattisce array annidati | `flatten([[1;2];[3;4]])` → `[1,2,3,4]` |
| `join` | `join(array; separatore)` | Unisce array in stringa | `join(["a";"b";"c"]; ",")` → `"a,b,c"` |
| `keys` | `keys(oggetto)` | Array dei nomi delle proprietà | `keys({a:1; b:2})` → `["a","b"]` |
| `last` | `last(array)` | Ultimo elemento | `last(["a";"b";"c"])` → `"c"` |
| `length` | `length(array)` | Numero di elementi | `length([1;2;3])` → `3` |
| `merge` | `merge(array1; array2; ...)` | Unisce più array | `merge([1;2]; [3;4])` → `[1,2,3,4]` |
| `remove` | `remove(array; val1; val2; ...)` | Rimuove valori specificati | `remove([1;2;3;2]; 2)` → `[1,3]` |
| `reverse` | `reverse(array)` | Inverte l'ordine | `reverse([1;2;3])` → `[3,2,1]` |
| `slice` | `slice(array; inizio; [lunghezza])` | Estrae porzione di array | `slice([1;2;3;4;5]; 2; 3)` → `[2,3,4]` |
| `sort` | `sort(array; [chiave]; [ordine])` | Ordina l'array | `sort([3;1;2])` → `[1,2,3]` |
| `map` | `map(array; chiave)` | Estrae una proprietà da ogni oggetto | `map(products; "name")` → `["Prod1","Prod2"]` |
| `values` | `values(oggetto)` | Array dei valori delle proprietà | `values({a:1; b:2})` → `[1,2]` |
| `toArray` | `toArray(valore)` | Converte in array | `toArray("hello")` → `["hello"]` |

### Pattern Array per Gestione Prodotti

**Deduplicazione prodotti per SKU**:
```
{{distinct(products; "sku")}}
```

**Estrazione lista EAN da catalogo**:
```
{{map(products; "ean")}}
```

**Filtraggio prodotti attivi con prezzo > 0**:
```
— Usa in combinazione con filtro del modulo Iterator
— IML da solo non ha una funzione filter() nativa
— Pattern: Iterator → Filter → Aggregator
```

**Join SKU per query API**:
```
{{join(map(products; "sku"); ",")}}
→ "SKU001,SKU002,SKU003"
```

**Conteggio prodotti per categoria** (pattern avanzato):
```
{{length(distinct(products; "category"))}}
→ numero di categorie uniche
```

### Differenza Cruciale: `add` vs `merge`

- `add()` → aggiunge ELEMENTI singoli a un array
- `merge()` → CONCATENA due o più array

```
add([1;2]; 3)         → [1, 2, 3]        — aggiunge il valore 3
add([1;2]; [3;4])     → [1, 2, [3,4]]    — aggiunge l'ARRAY come elemento!
merge([1;2]; [3;4])   → [1, 2, 3, 4]     — concatena i due array
```

---

## 1.7 Funzioni Generali e Conversione — Riferimento Completo

### Funzioni Condizionali

| Funzione | Sintassi | Descrizione |
|----------|----------|-------------|
| `if` | `if(condizione; valore_true; valore_false)` | Restituisce un valore basato sulla condizione |
| `ifempty` | `ifempty(valore; alternativa)` | Restituisce alternativa se valore è null/empty/undefined |
| `switch` | `switch(expr; val1; ris1; val2; ris2; ...; [default])` | Confronta espressione con valori multipli |

### Dettaglio `switch`

La funzione switch funziona come un multi-case:

```
{{switch(order.marketplace;
  "amazon"; "Amazon EU";
  "ebay"; "eBay Europe";
  "kaufland"; "Kaufland.de";
  "fnac"; "Fnac-Darty";
  "Marketplace Sconosciuto"
)}}
```

L'ultimo parametro senza coppia è il **valore default**.

### Funzioni di Manipolazione Oggetti

| Funzione | Sintassi | Descrizione | Esempio |
|----------|----------|-------------|---------|
| `get` | `get(oggetto; chiave)` | Ottiene proprietà con chiave dinamica | `get(data; fieldName)` |
| `omit` | `omit(oggetto; key1; key2; ...)` | Rimuove chiavi specificate | `omit(order; "internalId"; "debug")` |
| `pick` | `pick(oggetto; key1; key2; ...)` | Mantiene SOLO le chiavi specificate | `pick(order; "id"; "total"; "status")` |

### Funzioni di Conversione Tipo

| Funzione | Sintassi | Da → A |
|----------|----------|--------|
| `toString` | `toString(valore)` | Qualsiasi → Stringa |
| `toNumber` | `toNumber(valore)` | Stringa/Boolean → Numero |
| `toDate` | `toDate(valore)` | Stringa → Data |
| `toBoolean` | `toBoolean(valore)` | Qualsiasi → Boolean |
| `toBinary` | `toBinary(valore; [encoding])` | Stringa → Binary (hex, base64) |
| `toArray` | `toArray(valore)` | Qualsiasi → Array |
| `toCollection` | `toCollection(array)` | Array → Collection/Object |

### Pattern `pick` e `omit` per API Marketplace

**Selezionare solo i campi necessari prima di inviare a un'API**:
```
{{pick(product; "sku"; "title"; "price"; "stock"; "ean")}}
→ rimuove tutti i campi interni/debug, invia solo il necessario
```

**Rimuovere campi sensibili prima del logging**:
```
{{omit(apiResponse; "apiKey"; "secret"; "token")}}
→ sicuro per il logging
```

---

## 1.8 Funzioni Binarie e Hash

### Conversione Binary

```
{{toBinary("Hello World")}}                    — testo → binary
{{toBinary("SGVsbG8="; "base64")}}              — base64 → binary
{{toBinary("48656c6c6f"; "hex")}}               — hex → binary
{{toString(binaryData)}}                         — binary → testo
{{base64(binaryData)}}                           — binary → base64 string
```

### Tabella Hash Completa

| Funzione | Output Length | Sicurezza | Uso Tipico |
|----------|--------------|-----------|------------|
| `md5` | 32 char hex | Bassa (deprecated per sicurezza) | Checksum file, deduplicazione |
| `sha1` | 40 char hex | Media | Legacy webhook signatures |
| `sha256` | 64 char hex | Alta | Webhook HMAC, API signatures |
| `sha512` | 128 char hex | Molto alta | Sicurezza avanzata |

### Verifica Webhook Signature (Pattern Critico)

```
— Ricezione webhook con HMAC-SHA256 signature
— Header: X-Signature: abc123def456...

{{if(
  sha256(body.__IMTRAWBODY__; "webhook_secret"; "hex") == headers.xSignature;
  true;
  false
)}}
```

**Nota**: `__IMTRAWBODY__` è la variabile speciale che contiene il body raw non-parsato, necessaria per la verifica HMAC.

---

## 1.9 Pattern Avanzati e Nesting

### Nesting Multi-Livello

Le funzioni IML possono essere annidate a qualsiasi profondità:

```
— Livello 1: funzione semplice
{{upper(name)}}

— Livello 2: funzione annidata
{{upper(trim(name))}}

— Livello 3: triplo nesting
{{if(length(trim(name)) > 0; upper(trim(name)); "N/A")}}

— Livello 4+: nesting complesso
{{formatDate(
  addDays(
    parseDate(order.dateStr; "DD/MM/YYYY");
    if(order.priority == "express"; 1; 3)
  );
  "YYYY-MM-DD"
)}}
```

### Pattern Condizionale Multi-Branch

```
— Calcolo commissione marketplace variabile
{{if(marketplace == "amazon";
  price * 0.15;
  if(marketplace == "ebay";
    price * 0.12;
    if(marketplace == "kaufland";
      price * 0.10;
      price * 0.08
    )
  )
)}}

— Versione migliore con switch:
{{price * switch(marketplace;
  "amazon"; 0.15;
  "ebay"; 0.12;
  "kaufland"; 0.10;
  "fnac"; 0.13;
  "carrefour"; 0.11;
  "leroy-merlin"; 0.12;
  0.10
)}}
```

### Pattern Concatenazione Dinamica

```
— Titolo SEO prodotto per marketplace
{{capitalize(product.brand) + " " + product.name + " - " + product.model + " | " + switch(marketplace; "amazon"; "Spedizione Gratuita"; "ebay"; "Vendita Online"; "Miglior Prezzo")}}
```

---

## 1.10 Type Coercion e Gestione Null/Empty

### Regole di Coercion

Quando un Boolean è atteso:
- Numeri → `true` (ANCHE `0` diventa `true` in certi contesti!)
- Testo `"false"` → `false`
- Testo vuoto `""` → `false`
- `null` / `undefined` → `false`
- Qualsiasi altro valore esistente → `true`

### Gerarchia Null/Empty

```
— Questi sono TUTTI trattati come "vuoto" da ifempty():
undefined     — campo non presente nell'oggetto
null          — valore esplicitamente nullo
""            — stringa vuota
emptystring   — keyword per stringa vuota
empty         — keyword per valore vuoto

— Questi NON sono vuoti:
0             — è un numero valido
false         — è un boolean valido
"0"           — è una stringa non vuota
" "           — è una stringa con spazio (non vuota!)
[]            — array vuoto — NON catturato da ifempty!
```

### Pattern Difensivi per Dati Marketplace

```
— Prezzo con fallback (mai 0 o null)
{{ifempty(product.price; 0)}}

— Ma attenzione: se price è 0, ifempty NON lo cattura!
— Per gestire anche 0:
{{if(product.price > 0; product.price; defaultPrice)}}

— Stock con validazione
{{if(toNumber(ifempty(product.stock; "0")) >= 0; toNumber(product.stock); 0)}}

— EAN con validazione lunghezza
{{if(length(ifempty(product.ean; "")) == 13; product.ean; "")}}

— Titolo con pulizia
{{if(length(trim(ifempty(product.title; ""))) > 0; trim(product.title); "Titolo non disponibile")}}
```

---

## 1.11 Pattern IML per E-commerce Marketplace

### Generazione SKU Composito

```
{{upper(substring(supplier.code; 1; 3)) + "-" + product.ean + "-" + substring(md5(product.id); 1; 6)}}
→ "FOR-5901234567890-a1b2c3"
```

### Calcolo Prezzo con Margine e IVA

```
— Prezzo fornitore + margine 30% + IVA 22%
{{round(product.costPrice * 1.30 * 1.22; 2)}}

— Prezzo con commissione marketplace variabile
{{round(
  product.costPrice / (1 - switch(marketplace;
    "amazon"; 0.15;
    "ebay"; 0.12;
    "kaufland"; 0.10;
    0.10
  )) * 1.22;
  2
)}}
```

### Stato Disponibilità Multi-Livello

```
{{switch(true;
  product.stock > 50; "In Stock";
  product.stock > 10; "Disponibile";
  product.stock > 0; "Ultimi Pezzi";
  product.stock == 0; "Esaurito";
  "Non Disponibile"
)}}
```

### Normalizzazione EAN/GTIN

```
— Padding EAN a 13 cifre
{{if(
  length(toString(product.ean)) == 13;
  toString(product.ean);
  if(
    length(toString(product.ean)) == 12;
    "0" + toString(product.ean);
    ""
  )
)}}
```

### Pulizia Titolo per Marketplace

```
{{trim(
  replace(
    replace(
      replace(product.title; "  "; " ");
      "\n"; " "
    );
    "\t"; " "
  )
)}}
```

---

## 1.12 Performance Tips e Gotchas Critici

### I 10 Errori Più Comuni in IML

| # | Errore | Sbagliato | Corretto |
|---|--------|-----------|----------|
| 1 | Separatore virgola | `if(x; "a", "b")` | `if(x; "a"; "b")` |
| 2 | Array 0-based | `array[0]` | `array[1]` |
| 3 | Comparazione stringhe numeriche | `"10" > "2"` = false (lessicografico) | `toNumber("10") > 2` |
| 4 | ifempty non cattura 0 | `ifempty(0; "default")` → `0` | `if(val > 0; val; default)` |
| 5 | Spazi nel trim | `trim()` non gestisce `\n` | `replace(trim(val); "\n"; "")` |
| 6 | Date senza timezone | `formatDate(date; "HH:mm")` | `formatDate(date; "HH:mm"; "Europe/Rome")` |
| 7 | Nested map O(n²) | `map(a; map(b; ...))` | Flatten + single map |
| 8 | JSON in stringa | `toString(object)` | `{{JSON.stringify}}` via Set Variable |
| 9 | Array vuoto check | `ifempty(array; ...)` non funziona | `if(length(array) > 0; ...; ...)` |
| 10 | Operatore = singolo | `if(x = 1; ...)` funziona ma confonde | `if(x == 1; ...)` più chiaro |

### Tips Performance

1. **Evitare map() dentro map()** — complessità O(n²). Usare flatten + single map
2. **Filtrare PRIMA di mappare** — riduce il dataset prima della trasformazione
3. **Usare distinct() presto** — deduplica prima di processare
4. **Preferire get() a catene if/else** per accesso dinamico a proprietà
5. **Usare switch() invece di if() annidati** quando ci sono 3+ condizioni
6. **Specificare sempre timezone** nelle funzioni data per risultati consistenti
7. **Usare parseNumber() per numeri da testo** — mai fare calcoli su stringhe

---

# 2. Data Stores — Database Integrato Avanzato

## 2.1 Architettura Interna

I Data Stores di Make.com sono **database key-value table-based** ospitati nell'infrastruttura Make. Ogni store contiene record (righe) definiti da una Data Structure che funge da schema.

**Caratteristiche architetturali**:
- Chiave primaria indicizzata per lookup O(1)
- Record strutturati secondo la Data Structure definita
- Scoped al team Make (qualsiasi scenario nel team può accedere allo stesso store)
- Nessuna dipendenza da database esterni
- Supporto per tipi: Text, Number, Boolean, Date, Collection, Array
- Nesting multi-livello per strutture complesse

## 2.2 Limiti per Piano

| Piano | Dimensione per Data Store | Storage Totale | Costo per Operazione |
|-------|--------------------------|----------------|---------------------|
| Free | Non disponibile | N/A | N/A |
| Core | 1 MB | ~1 MB per 1.000 ops | 1 credito |
| Pro | 10 MB | ~10 MB per 10.000 ops | 1 credito |
| Teams | 10 MB | ~10 MB + collaboration | 1 credito |
| Enterprise | Custom | Custom | 1 credito |

**Limiti chiave**:
- **Record singolo**: massimo 512 KB
- **Nessun limite rigido** al numero di record (limitato dallo storage totale)
- Storage condiviso tra TUTTI i data stores del team
- Errore `"Not enough space in storage"` quando si supera il limite

## 2.3 Data Structures (Schema Definition)

### Creazione Manuale

Ogni campo della struttura ha:
- **Name**: identificatore programmatico (usato in API e mapping)
- **Type**: tipo di dato (Text, Number, Boolean, Date, Collection, Array)
- **Label**: nome leggibile per l'UI
- **Default**: valore predefinito quando non fornito
- **Required**: booleano per rendere il campo obbligatorio

### Generazione Automatica da JSON

```json
// Incolla questo JSON nel generatore:
{
  "sku": "ABC-123",
  "title": "Prodotto Test",
  "price": 29.99,
  "stock": 150,
  "active": true,
  "lastUpdated": "2026-04-06T12:00:00Z",
  "dimensions": {
    "weight": 1.5,
    "length": 30,
    "width": 20,
    "height": 10
  },
  "marketplaces": ["amazon", "ebay", "kaufland"]
}
// → Make genera automaticamente la struttura con tutti i tipi corretti
```

### Strutture Annidate (Collections)

Le Collections permettono di raggruppare campi correlati:

```
Product (Data Structure)
├── sku (Text, Required)
├── title (Text, Required)
├── price (Number)
├── stock (Number)
├── dimensions (Collection)
│   ├── weight (Number)
│   ├── length (Number)
│   ├── width (Number)
│   └── height (Number)
├── marketplaces (Array of Text)
└── metadata (Collection)
    ├── createdAt (Date)
    ├── updatedAt (Date)
    └── source (Text)
```

## 2.4 Moduli CRUD Completi — I 9 Moduli

### Modulo 1: Add/Replace a Record

**Scopo**: Inserisce nuovo record o SOVRASCRIVE completamente un record esistente

**Parametri**:
- `Data Store`: quale data store (obbligatorio)
- `Key`: identificatore unico del record (obbligatorio)
- `Overwrite`: se true, sovrascrive record esistente (default: true)
- Campi della data structure: mappati singolarmente

**Comportamento CRITICO**:
- Se la chiave esiste e Overwrite=true → **SOVRASCRIVE TUTTO il record**
- I campi NON mappati vengono **CANCELLATI** (impostati a null)
- Se la chiave non esiste → crea nuovo record
- Idempotente: chiamate multiple con stessi dati producono stesso risultato

**Return**: il record completo appena salvato

### Modulo 2: Update a Record

**Scopo**: Modifica solo campi specifici preservando il resto

**Parametri**:
- `Data Store`: quale data store
- `Key`: identificatore del record
- Campi da aggiornare: solo quelli mappati

**Comportamento CRITICO**:
- **SOLO i campi mappati vengono modificati**
- I campi non mappati **MANTENGONO i valori esistenti**
- Se la chiave non esiste → crea nuovo record

**Differenza fondamentale da Add/Replace**: Update è un PATCH (modifica parziale), Add/Replace è un PUT (sovrascrittura completa).

### Modulo 3: Get a Record

**Scopo**: Recupera un singolo record tramite chiave primaria

**Parametri**: `Key` (obbligatorio)

**Performance**: O(1) — lookup diretto per chiave. Il metodo PIÙ efficiente per recuperare un singolo record.

**Return**: l'oggetto record completo con tutti i campi, oppure null se non esiste.

### Modulo 4: Search Records

**Scopo**: Cerca record che corrispondono a condizioni di filtro

**Parametri**:
- `Data Store`: quale data store
- `Filter`: condizione/i di ricerca
- `Sort By`: campo per ordinamento
- `Sort Direction`: ASC o DESC
- `Limit`: massimo risultati (default: tutti)
- `Offset`: salta i primi N risultati (paginazione)

**Operatori filtro disponibili**:

| Operatore | Descrizione |
|-----------|-------------|
| Equal (EQ) | Uguale a |
| Not Equal (NE) | Diverso da |
| Contains | Contiene la stringa |
| Does not contain | Non contiene |
| Greater than (GT) | Maggiore di |
| Less than (LT) | Minore di |
| Greater or equal (GTE) | Maggiore o uguale |
| Less or equal (LTE) | Minore o uguale |
| Starts with | Inizia con |
| Ends with | Finisce con |

**Logica filtri**:
- Condizioni multiple → **AND di default**
- Per simulare OR → due Search separati + Array Aggregator
- Formato API: `{FIELD}~{OPERATOR}~{VALUE}|AND|{FIELD}~{OPERATOR}~{VALUE}`

**Performance**: O(n) — scansione completa della tabella. Per store con migliaia di record, preferire Get a Record quando possibile.

### Modulo 5: Check if Record Exists

**Scopo**: Verifica se un record con una chiave specifica esiste
**Return**: Boolean (true/false)
**Uso**: Deduplicazione pre-inserimento, validazione flusso

### Modulo 6: Count Records

**Scopo**: Conta il numero totale di record nel data store
**Return**: Integer
**Uso**: Monitoraggio crescita, validazione batch

### Modulo 7: Delete a Record

**Scopo**: Rimuove un record specifico per chiave
**Comportamento**: Idempotente — cancellare una chiave inesistente non genera errore

### Modulo 8: Delete All Records

**Scopo**: Rimuove tutti i record, con possibilità di eccezioni
**Parametri**:
- `All`: true per cancellare tutto
- `Confirmed`: conferma obbligatoria
- `ExceptKeys`: array di chiavi da preservare (opzionale)

### Modulo 9: List All Records

**Scopo**: Recupera tutti i record con paginazione
**Parametri**: Limit, Offset, Sort By, Sort Direction

## 2.5 Filtri e Query Avanzate

### Filtro Multi-Campo (AND)

```
Search Records:
  Filter 1: status EQ "active"
  AND
  Filter 2: stock GT 0
  AND
  Filter 3: marketplace EQ "amazon"
```

### Simulazione OR con Merge

```
Scenario:
  Search Records (status EQ "new") → Route 1
  Search Records (status EQ "pending") → Route 2
  Array Aggregator (merge risultati) → risultato combinato
```

### Paginazione via API

```
GET /data-stores/{id}/data?pg[offset]=0&pg[limit]=100&pg[sortBy]=createdAt&pg[sortDir]=DESC
GET /data-stores/{id}/data?pg[offset]=100&pg[limit]=100  — pagina 2
GET /data-stores/{id}/data?pg[offset]=200&pg[limit]=100  — pagina 3
```

## 2.6 Concorrenza e Locking

### Problemi Noti

1. **Visibilità ritardata**: Dati scritti da uno scenario potrebbero non essere immediatamente visibili a scenari successivi chiamati via webhook
2. **Record locking**: Errore `"Record is locked by scenario X"` anche dopo il completamento dello scenario
3. **Race condition**: Problemi timing-dependent con accesso in scrittura concorrente

### Strategie di Mitigazione

**Optimistic Locking con Timestamp**:
```
1. GET record → salva lastModified
2. Processa dati
3. UPDATE record con condizione: lastModified == valore salvato
4. Se fallisce → ri-GET e riprova
```

**Chiavi Uniche per Prevenire Conflitti**:
```
— Chiave composita con timestamp
Key: {{order.id + "_" + formatDate(now(); "X")}}

— Chiave con hash univoco
Key: {{md5(order.id + order.marketplace + formatDate(now(); "x"))}}
```

**Ritardo tra Scrittura e Lettura**:
```
— Aggiungere modulo Sleep (1-2 secondi) tra write e read se necessario
Add/Replace Record → Sleep (2s) → Get Record
```

## 2.7 API REST per Data Stores

### Base URL

```
https://{zone}.make.com/api/v2/
```

Zone: `eu1`, `eu2`, `us1`, `us2` (dipende dalla regione del tuo account)

### Autenticazione

```
Authorization: Token YOUR_API_TOKEN
```

Creazione token: Profile → API Access → Add Token → Seleziona scopes → Salva

### Endpoints Completi

**Gestione Data Stores**:

| Metodo | Endpoint | Descrizione |
|--------|----------|-------------|
| `GET` | `/data-stores` | Lista tutti i data stores |
| `POST` | `/data-stores` | Crea nuovo data store |
| `GET` | `/data-stores/{id}` | Dettaglio data store |
| `PATCH` | `/data-stores/{id}` | Aggiorna nome/descrizione |
| `DELETE` | `/data-stores/{id}` | Elimina data store |

**Gestione Record**:

| Metodo | Endpoint | Descrizione |
|--------|----------|-------------|
| `GET` | `/data-stores/{id}/data` | Lista record (con filtri, paginazione) |
| `POST` | `/data-stores/{id}/data` | Crea nuovo record |
| `PUT` | `/data-stores/{id}/data/{key}` | Aggiorna record per chiave |
| `DELETE` | `/data-stores/{id}/data` | Elimina record (con filtri) |
| `DELETE` | `/data-stores/{id}/data/{key}` | Elimina record per chiave |

### Esempio: Creazione Record via API

```bash
curl -X POST "https://eu1.make.com/api/v2/data-stores/12345/data" \
  -H "Authorization: Token your-api-token" \
  -H "Content-Type: application/json" \
  -d '{
    "key": "SKU-001",
    "data": {
      "sku": "SKU-001",
      "title": "Prodotto Test",
      "price": 29.99,
      "stock": 150,
      "marketplace": "amazon",
      "active": true
    }
  }'
```

### Esempio: Search con Filtri via API

```bash
curl -X GET "https://eu1.make.com/api/v2/data-stores/12345/data?filters=%5B%7B%22a%22%3A%22status%22%2C%22b%22%3A%22active%22%2C%22o%22%3A%22text%3Aeq%22%7D%5D&pg%5Blimit%5D=50" \
  -H "Authorization: Token your-api-token"
```

Filtro decodificato: `[{"a":"status","b":"active","o":"text:eq"}]`

## 2.8 Pattern Avanzati per E-commerce

### Pattern 1: Cache Catalogo Prodotti

**Data Structure**:
```
catalogCache
├── cacheKey (Text, Required) — chiave: "sku_" + SKU
├── productData (Text) — JSON stringificato del prodotto
├── lastUpdated (Date)
├── source (Text) — "sellrapido", "fornitore_x"
├── ttlMinutes (Number) — default: 1440 (24 ore)
```

**Flusso**:
```
1. Get Record (key = "sku_" + SKU)
2. IF found AND (now() - lastUpdated) < ttlMinutes minuti
   → parseJSON(productData) → usa dati cached
3. ELSE
   → Chiama API fornitore → Aggiorna record → usa dati freschi
```

### Pattern 2: Deduplicazione Ordini Cross-Marketplace

**Data Structure**:
```
orderDedup
├── orderKey (Text, Required) — md5(orderId + marketplace)
├── orderId (Text)
├── marketplace (Text)
├── processedAt (Date)
├── status (Text) — "processing", "complete", "error"
```

**Flusso**:
```
1. Genera orderKey = md5(order.id + order.marketplace)
2. Check if Exists (key = orderKey)
3. IF exists → Skip (ordine già processato)
4. IF not exists → Add Record + Processa ordine
5. Update Record (status = "complete")
6. Cleanup periodico: Delete record con processedAt > 30 giorni fa
```

### Pattern 3: State Management per Sync Incrementale

**Data Structure**:
```
syncState
├── syncId (Text, Required) — "amazon_orders", "ebay_inventory"
├── lastCursor (Text) — cursore/token per paginazione
├── lastProcessedDate (Date)
├── totalProcessed (Number)
├── status (Text) — "idle", "running", "error"
├── errorMessage (Text)
```

**Flusso**:
```
1. Get Record (key = "amazon_orders")
2. Usa lastCursor e lastProcessedDate per query incrementale
3. Processa batch di ordini
4. Update Record con nuovo cursor e data
5. Al prossimo run → riprende da dove si era fermato
```

### Pattern 4: Lookup Table Marketplace

**Data Structure**:
```
marketplaceLookup
├── code (Text, Required) — "amazon_it", "ebay_de"
├── name (Text) — "Amazon Italia"
├── commissionRate (Number) — 0.15
├── vatRate (Number) — 0.22
├── currency (Text) — "EUR"
├── slaHours (Number) — 24
├── active (Boolean)
```

### Pattern 5: Error Tracking per Monitoring

**Data Structure**:
```
errorLog
├── errorId (Text, Required) — timestamp + random
├── scenario (Text) — nome dello scenario
├── marketplace (Text)
├── errorType (Text) — "api_error", "validation", "timeout"
├── errorMessage (Text)
├── inputData (Text) — JSON dell'input che ha causato l'errore
├── timestamp (Date)
├── resolved (Boolean) — default: false
├── retryCount (Number) — default: 0
```

## 2.9 Quando Usare Data Stores vs Database Esterno

| Criterio | Data Store Make | Database Esterno (Supabase/PostgreSQL) |
|----------|----------------|---------------------------------------|
| **Record** | < 100.000 | > 100.000 |
| **Dimensione totale** | < 10 MB | Illimitato |
| **Query complesse** | Solo filtri base | JOIN, subquery, aggregazioni |
| **Indici** | Solo chiave primaria | Indici multipli personalizzati |
| **Transazioni** | No | Sì (ACID) |
| **Accesso esterno** | Solo API Make | SQL, REST, GraphQL |
| **Costo** | Incluso nel piano | Costo separato |
| **Setup** | Zero config | Richiede configurazione |
| **Latenza** | < 50ms (interno) | 50-200ms (rete) |
| **Concorrenza** | Limitata (locking) | Completa (MVCC) |
| **Backup** | No built-in | Automatico |
| **Uso ideale** | Cache, dedup, state, lookup | Dati core business, analytics |

**Regola per Vendiamonoi**: Usa Data Stores per cache temporanee e stato scenario. Usa Supabase per dati prodotto, ordini, e analytics.

---

# 3. Webhooks — Trigger Real-Time Avanzati

## 3.1 Tipi di Webhook

### Custom Webhook

**Scopo**: Ricevere dati arbitrari da qualsiasi servizio esterno via HTTP
**Trigger**: Istantaneo (esegue immediatamente quando il webhook URL riceve una richiesta)
**Metodi HTTP accettati**: POST (primario), GET (supportato)
**Formato dati**: JSON, form-data (urlencoded), query string

**URL generato**:
```
https://hook.eu1.make.com/xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
https://hook.us1.make.com/xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

Il subdomain (`eu1`, `us1`) corrisponde alla zona del tuo account.

### Custom Mailhook

**Scopo**: Trigger scenario quando un'email arriva a un indirizzo dedicato
**Indirizzo generato**: `xxxxx@hook.make.com`
**Uso**: Automazioni basate su email (conferme ordine, notifiche)

### Webhook Response Module

**Scopo**: Inviare risposte HTTP personalizzate al chiamante del webhook
**Posizione**: Alla fine dello scenario (dopo l'elaborazione)
**Configurazione**: Status code + Headers + Body

## 3.2 Configurazione e Data Structure Determination

### Determinazione Automatica

1. Aggiungi Custom Webhook al tuo scenario
2. Copia l'URL generato
3. Invia un test request con payload di esempio
4. Make analizza il payload e genera la data structure automaticamente
5. Messaggio: "Successfully determined"

### Ri-determinazione

Quando il formato del payload cambia:
1. Click su "Re-determine data structure"
2. Invia nuovo payload di esempio
3. Make aggiorna la struttura

### Variabili IML Disponibili nel Webhook

| Variabile | Descrizione |
|-----------|-------------|
| `body` | Contenuto del body della richiesta |
| `headers` | Tutti gli header della richiesta (array di oggetti name/value) |
| `query` | Parametri query string dell'URL |
| `method` | Metodo HTTP (GET, POST, etc.) |
| `parameters` | Parametri input del webhook |
| `__IMTRAWBODY__` | Body raw non-parsato (cruciale per verifica HMAC) |

## 3.3 Sicurezza

### IP Whitelisting

**Configurazione**: Lista di IP separati da virgola nella configurazione webhook
**CIDR supportato**: es. `192.168.1.0/24` per interi subnet
**Funzionamento**: Solo richieste dagli IP specificati vengono processate, tutte le altre rifiutate

**IP Outbound di Make.com** (per whitelist da parte di servizi esterni):
- Zone-specific: EU1, EU2, US1, US2
- Documentazione: https://help.make.com/allow-connections-to-and-from-make-ip-addresses

### Verifica HMAC Signature

**Come funziona**:
1. Il provider del webhook firma il messaggio con chiave segreta + HMAC-SHA256
2. La firma viene inclusa in un header (es. `X-Hub-Signature-256`)
3. Nel tuo scenario, ricalcoli l'hash usando la stessa chiave segreta
4. Confronti le firme per verificare autenticità e integrità

**Implementazione in Make**:
```
— Modulo: Custom Webhook (riceve il payload)
— Modulo: Set Variable o Filter

Calcolo firma:
{{sha256(body.__IMTRAWBODY__; "tua_chiave_segreta"; "hex")}}

Confronto:
{{if(
  sha256(body.__IMTRAWBODY__; "webhook_secret"; "hex") == headers.xHubSignature256;
  true;
  false
)}}
```

**Nota importante**: I webhook custom di Make NON verificano automaticamente HMAC. Devi implementare la verifica manualmente nel flusso dello scenario.

### Token-Based Validation

**Metodo 1 — API Key nell'header**:
```
Webhook config → Require API Key → Header: "X-API-Key"
Il chiamante deve includere: X-API-Key: tuo_token_segreto
```

**Metodo 2 — Token nel query string**:
```
URL: https://hook.eu1.make.com/xxx?token=tuo_token_segreto
Verifica: {{if(query.token == "tuo_token_segreto"; true; false)}}
```

**Metodo 3 — Bearer Token**:
```
Header: Authorization: Bearer tuo_token_segreto
Verifica: {{if(headers.authorization == "Bearer tuo_token_segreto"; true; false)}}
```

## 3.4 Webhook Response Module — Dettaglio

### Configurazione Completa

```
Webhook Response:
  Type: json | urlencoded | text
  Status: 200 (OK) | 201 (Created) | 400 (Bad Request) | 401 (Unauthorized) | ...
  Headers: {
    "Content-Type": "application/json",
    "X-Custom-Header": "valore"
  }
  Body: {
    "status": "success",
    "orderId": "{{order.id}}",
    "message": "Ordine ricevuto"
  }
```

### Status Code Comuni per E-commerce

| Code | Significato | Quando usarlo |
|------|------------|---------------|
| `200` | OK | Webhook ricevuto e processato con successo |
| `201` | Created | Nuovo record creato (es. nuovo ordine) |
| `202` | Accepted | Ricevuto, sarà processato in background |
| `400` | Bad Request | Payload invalido o campi mancanti |
| `401` | Unauthorized | Token/API key mancante o invalido |
| `404` | Not Found | Risorsa richiesta non trovata |
| `409` | Conflict | Ordine già esistente (deduplicazione) |
| `429` | Too Many Requests | Rate limit superato |
| `500` | Internal Error | Errore interno nello scenario |

### Risposta Interattiva (HTML)

È possibile restituire pagine HTML complete come risposta webhook:

```html
Status: 200
Headers: {"Content-Type": "text/html"}
Body:
<html>
<body>
  <h1>Ordine {{order.id}} ricevuto</h1>
  <p>Stato: {{order.status}}</p>
  <a href="https://hook.eu1.make.com/xxx?action=confirm&id={{order.id}}">Conferma</a>
</body>
</html>
```

## 3.5 Coda e Gestione Queue

### Capacità Coda

- **Formula**: Ogni 10.000 operazioni mensili → fino a 667 items per coda webhook
- **Massimo assoluto**: 10.000 items nella coda
- **Quando piena**: Make rifiuta tutti i webhook in arrivo che superano il limite
- **Eliminazione**: I dati vengono eliminati permanentemente dopo l'elaborazione

### Ordine di Elaborazione

- **Default**: Elaborazione PARALLELA (più esecuzioni webhook simultanee)
- **Opzione sequenziale**: Disponibile nelle impostazioni scenario per elaborazione one-at-a-time

**Quando usare elaborazione sequenziale**:
- Operazioni order-sensitive (ordini che devono essere processati in sequenza)
- Vincoli database (evitare deadlock)
- Aggiornamento stock (evitare overselling)

### Gestione Buildup Coda

1. **Aumentare Max Results** per esecuzione scenario
2. **Aumentare frequenza** di esecuzione scenario
3. **Monitorare regolarmente** la sezione webhook per lo stato della coda
4. **Clear via API** se necessario: endpoint Make API per svuotare la coda

### Log e Storico

| Piano | Retention Log |
|-------|---------------|
| Standard | 3 giorni |
| Enterprise | 30 giorni |

Informazioni nei log: statusId (1=success, 3=failed), payload completo, timestamp

## 3.6 Rate Limits e Payload Limits

| Limite | Valore |
|--------|--------|
| Rate limit webhook | **30 webhook/secondo** (default) |
| Risposta rate limit | HTTP 429 "Too Many Requests" |
| Coda piena | HTTP 400 "Queue is full" |
| Dimensione coda default | 50 items (configurabile) |
| Payload massimo | **5 MB** (5.242.880 bytes) |
| Timeout risposta | Configurabile nelle impostazioni avanzate |

### Gestione Rate Limit per Alto Volume

Per scenari Vendiamonoi con alto volume di webhook (sync ordini multi-marketplace):

1. **Hookdeck** come gateway intermedio — aggiunge queuing, rate limiting, retry avanzato
2. **Batch webhook** — aggregare più eventi in un singolo webhook
3. **Load balancing** — distribuire su più webhook URL
4. **Exponential backoff** — implementare retry con ritardo crescente lato chiamante

## 3.7 Pattern Webhook per Marketplace

### Pattern: Ricezione Ordini Real-Time

```
Custom Webhook (ricezione ordine da SellRapido/marketplace)
→ Verify HMAC Signature
→ Check Dedup (Data Store)
→ Router:
    Route 1 (Amazon): Processa ordine Amazon
    Route 2 (eBay): Processa ordine eBay  
    Route 3 (Kaufland): Processa ordine Kaufland
→ Webhook Response (200 OK)
```

### Pattern: Notifica Cambio Stock

```
Custom Webhook (stock update dal fornitore)
→ Parse payload → Validate EAN/SKU
→ Update Data Store (cache stock)
→ Iterator (per ogni marketplace attivo)
→ HTTP Request (aggiorna stock su marketplace)
→ Webhook Response (202 Accepted)
```

### Pattern: Conferma Spedizione

```
Custom Webhook (tracking da corriere)
→ Get Order from Supabase
→ Router:
    Route 1: Update marketplace (via API)
    Route 2: Notifica cliente (via eDesk)
    Route 3: Update Supabase (stato ordine)
→ Webhook Response (200 + tracking URL)
```

---

# 4. HTTP Module — Connettore Universale Completo

## 4.1 Varianti del Modulo HTTP

| Modulo | Autenticazione | Uso |
|--------|---------------|-----|
| **Make a request** | Nessuna (o manuale via headers) | API pubbliche, custom |
| **Make a Basic Auth request** | Username + Password | API con HTTP Basic Auth |
| **Make an OAuth 2.0 request** | OAuth 2.0 (auto token management) | Google, Microsoft, etc. |
| **Make an API Key Auth request** | API Key (header o query) | Maggior parte delle API |
| **Make a Client Certificate request** | Certificato client + chiave privata | API ad alta sicurezza |
| **Get a file** | Variabile | Download file da URL |
| **Retrieve headers** | Variabile | Estrazione header specifici |
| **Resolve a target URL** | Nessuna | Risoluzione URL redirect |

## 4.2 Autenticazione — Dettaglio

### Basic Auth

```
Modulo: Make a Basic Auth request
Username: your_username
Password: your_password

→ Make genera automaticamente:
   Header: Authorization: Basic base64(username:password)
```

### OAuth 2.0

```
Modulo: Make an OAuth 2.0 request
Connection: [Crea nuova connessione]
  → Client ID: (dal provider)
  → Client Secret: (dal provider)
  → Authorize URI: (endpoint autorizzazione)
  → Token URI: (endpoint token)
  → Scopes: (permessi richiesti)
  → Redirect URI: (generato da Make)

→ Make gestisce automaticamente:
   • Acquisizione token iniziale
   • Refresh automatico alla scadenza
   • Retry con nuovo token se 401
```

### API Key

```
Modulo: Make an API Key Auth request
API Key Location: Header | Query Parameter
Parameter Name: "X-API-Key" | "Authorization" | "api_key" | custom
API Key Value: "your_api_key" | "Bearer your_token"
```

### Client Certificate (mTLS)

```
Modulo: Make a Client Certificate request
→ Upload certificato client (.pem o .crt)
→ Upload chiave privata (.key)
→ Opzionale: CA certificate per chain di fiducia

Uso: API bancarie, gateway pagamento ad alta sicurezza
```

## 4.3 Request Body Types

### JSON (application/json)

```
Body type: JSON
Content: {
  "sku": "{{product.sku}}",
  "price": {{product.price}},
  "stock": {{product.stock}},
  "active": {{product.active}}
}

→ Content-Type automatico: application/json
→ Auto-escape delle stringhe
```

### Form Data (application/x-www-form-urlencoded)

```
Body type: urlencoded
Fields:
  sku = {{product.sku}}
  price = {{product.price}}
  action = update

→ Serializzato come: sku=ABC123&price=29.99&action=update
```

### Multipart/Form-Data (file upload)

```
Body type: multipart/form-data
Fields:
  file = [Binary data dal modulo precedente]
  filename = "catalog.csv"
  metadata = {"type": "product_feed"}

→ Boundary automatico generato da Make
→ Supporta file multipli
```

### Raw/Text

```
Body type: Raw
Content-Type: text/xml
Body: <?xml version="1.0"?>
<product>
  <sku>{{product.sku}}</sku>
  <price>{{product.price}}</price>
</product>
```

## 4.4 Paginazione Automatica

Make supporta tre pattern di paginazione configurabili nel modulo HTTP.

### Offset-Based Pagination

```
Configurazione:
  URL: https://api.example.com/products?limit=100&offset={{(pagination.page - 1) * 100}}
  Stop condition: Risposta con 0 record

Funzionamento:
  Page 1: offset=0   → record 0-99
  Page 2: offset=100  → record 100-199
  Page 3: offset=200  → record 200-299
  ...fino a risposta vuota
```

### Cursor-Based Pagination

```
Configurazione:
  URL: https://api.example.com/orders?cursor={{ifempty(pagination.cursor; "")}}&limit=100
  Next cursor: {{body.nextCursor}}
  Stop condition: nextCursor è null/empty

Funzionamento:
  Request 1: cursor=""          → dati + nextCursor="abc123"
  Request 2: cursor="abc123"    → dati + nextCursor="def456"
  Request 3: cursor="def456"    → dati + nextCursor=null → STOP
```

### Page-Based Pagination

```
Configurazione:
  URL: https://api.example.com/products?page={{pagination.page}}&per_page=50
  Stop condition: body.hasMore == false

Funzionamento:
  Page 1 → Page 2 → Page 3 → ... → hasMore=false → STOP
```

### Direttive Paginazione Avanzate

```json
{
  "url": "URL per la richiesta di paginazione",
  "method": "GET",
  "headers": {},
  "qs": {},
  "body": {},
  "mergeParams": true
}
```

- `mergeParams: true` (default): parametri paginazione UNITI ai parametri originali
- `mergeParams: false`: solo parametri paginazione nella richiesta successiva

## 4.5 Configurazioni Avanzate

### Timeout

```
Range: 1-300 secondi
Default: 40 secondi
Raccomandato per API lente: 120 secondi
Raccomandato per API marketplace: 60 secondi
```

### Redirect

```
Follow redirects: true/false
Massimo: 10 redirect consecutivi
Uso: URL abbreviati, redirect OAuth, CDN
```

### Proxy

```
Host: dominio o IP del proxy
Port: 1-65535
Username: (opzionale, per proxy autenticati)
Password: (opzionale)
```

### TLS/SSL

```
— Self-Signed Certificates:
Upload CA certificate o certificato self-signed
Uso: ambienti staging/development

— Mutual TLS (mTLS):
Upload certificato client + chiave privata
Uso: API bancarie, payment gateway

— Custom CA:
Upload Certificate Authority personalizzata
```

## 4.6 Response Parsing e Header Extraction

### Struttura Risposta

```
Response:
  status: 200 (codice HTTP)
  headers: [{name: "Content-Type", value: "application/json"}, ...]
  body: {parsed JSON/XML object}
  data: {extracted content, se disponibile}
```

### Estrazione Header Specifici

Gli header nella risposta sono un array di oggetti `{name, value}`. Per estrarre un header specifico:

```
— Per posizione (se conosci l'indice):
{{response.headers[3].value}}

— Per nome (pattern più robusto):
— Usa modulo Set Variable con formula di ricerca nell'array headers
```

### Auto-Parsing Risposta

- **JSON**: Automaticamente deserializzato in oggetto
- **XML**: Automaticamente parsato in struttura
- **Binary**: Disponibile come buffer per moduli successivi
- **Text**: Disponibile come stringa

## 4.7 File Upload Multipart

```
Configurazioni:
  Content-Type: multipart/form-data (automatico)
  File source: output da modulo precedente (es. Download, Google Drive)
  Filename: specificabile manualmente
  Additional fields: key-value pairs aggiuntivi
  Multiple files: supportato con più parametri file

Esempio per upload catalogo CSV a marketplace:
  1. Download CSV da Google Drive
  2. HTTP Make a Request:
     URL: https://api.marketplace.com/catalog/upload
     Method: POST
     Body: multipart/form-data
       file = {{GoogleDrive.fileContent}}
       filename = "catalog_{{formatDate(now(); "YYYYMMDD")}}.csv"
       format = "csv"
       marketplace = "amazon_it"
```

---

# 5. Pattern Vendiamonoi — Applicazioni Concrete

## 5.1 Scenario: Sync Prezzi Multi-Marketplace

```
Trigger: Scheduled (ogni 6 ore)
→ HTTP Request: GET prezzi da SellRapido API
→ Iterator: per ogni prodotto
→ IML: calcolo prezzo per marketplace
   {{round(product.costPrice * (1 + marginRate) * (1 + vatRate); 2)}}
→ Router:
    Route 1 (Amazon): HTTP POST update prezzo
    Route 2 (eBay): HTTP POST update prezzo
    Route 3 (Kaufland): HTTP POST update prezzo
→ Data Store: Update lastSyncDate
→ Error Handler: Log errori in Data Store
```

## 5.2 Scenario: Gestione Ordini Real-Time

```
Trigger: Custom Webhook (ordine da marketplace)
→ HMAC Verification
→ Data Store: Check deduplicazione
→ IML: Normalizzazione dati ordine
   {
     orderId: {{body.order.id}},
     marketplace: {{switch(body.source; "AMZ"; "amazon"; "EBY"; "ebay"; "KFL"; "kaufland"; "unknown")}},
     total: {{parseNumber(body.order.total; ",")}},
     date: {{formatDate(parseDate(body.order.date; "DD/MM/YYYY HH:mm"); "YYYY-MM-DDTHH:mm:ssZ")}}
   }
→ HTTP Request: POST a Supabase (salva ordine)
→ HTTP Request: POST a Fatture in Cloud (genera fattura)
→ HTTP Request: POST a fornitore (richiesta evasione)
→ Webhook Response: 200 OK + orderId
```

## 5.3 Scenario: Monitoraggio Stock con Cache

```
Trigger: Scheduled (ogni 2 ore)
→ Data Store: Get syncState (lastCursor)
→ HTTP Request: GET stock da fornitore (paginato con cursor)
   URL: api.fornitore.com/stock?cursor={{lastCursor}}&limit=500
→ Iterator: per ogni prodotto
→ Data Store: Update/Add cache stock
→ Router:
    Route 1 (stock == 0): HTTP POST → disattiva su marketplace
    Route 2 (stock > 0 AND cambio): HTTP POST → aggiorna stock
    Route 3 (stock invariato): Skip
→ Data Store: Update syncState (nuovo cursor + timestamp)
```

## 5.4 Tabella IML Formule Vendiamonoi Più Usate

| Formula | Descrizione | Espressione IML |
|---------|-------------|-----------------|
| Prezzo con margine | Costo + 30% margine | `{{round(cost * 1.30; 2)}}` |
| Prezzo con IVA IT | Prezzo + 22% IVA | `{{round(price * 1.22; 2)}}` |
| Prezzo con IVA DE | Prezzo + 19% IVA | `{{round(price * 1.19; 2)}}` |
| Prezzo con IVA FR | Prezzo + 20% IVA | `{{round(price * 1.20; 2)}}` |
| Prezzo con IVA ES | Prezzo + 21% IVA | `{{round(price * 1.21; 2)}}` |
| SKU composito | Brand + EAN + hash | `{{upper(substring(brand;1;3)) + "-" + ean + "-" + substring(md5(id);1;6)}}` |
| Validazione EAN-13 | Check lunghezza | `{{if(length(toString(ean)) == 13; ean; "")}}` |
| Data fattura IT | Formato italiano | `{{formatDate(now(); "DD/MM/YYYY")}}` |
| Scadenza 30gg | Data + 30 giorni | `{{formatDate(addDays(now(); 30); "DD/MM/YYYY")}}` |
| Pulizia titolo | Trim + remove newlines | `{{trim(replace(replace(title; "\n"; " "); "  "; " "))}}` |
| Stato stock | Multi-livello | `{{if(stock > 50; "In Stock"; if(stock > 0; "Ultimi Pezzi"; "Esaurito"))}}` |
| Marketplace display | Code → nome | `{{switch(code; "AMZ"; "Amazon"; "EBY"; "eBay"; "KFL"; "Kaufland"; code)}}` |
| Commissione calc | % per marketplace | `{{round(price * switch(mp; "amazon"; 0.15; "ebay"; 0.12; 0.10); 2)}}` |
| JSON escape | Per body API | `{{replace(replace(text; "\\"; "\\\\"); "\""; "\\\"")}}` |
| Dedup key | Hash univoco ordine | `{{md5(orderId + marketplace + formatDate(now(); "YYYYMMDD"))}}` |

---

## FONTI E RIFERIMENTI

### Documentazione Ufficiale Make.com
- [Help Center — Functions](https://help.make.com/functions)
- [Help Center — String Functions](https://help.make.com/string-functions)
- [Help Center — Math Functions](https://help.make.com/math-functions)
- [Help Center — Date Functions](https://help.make.com/date-and-time-functions)
- [Help Center — Array Functions](https://help.make.com/array-functions)
- [Help Center — General Functions](https://help.make.com/general-functions)
- [Help Center — Data Stores](https://help.make.com/data-stores)
- [Help Center — Data Structures](https://help.make.com/data-structures)
- [Help Center — Webhooks](https://help.make.com/webhooks)
- [Developer Hub — IML](https://developers.make.com/custom-apps-documentation/app-components/iml)
- [Developer Hub — Custom IML Functions](https://developers.make.com/custom-apps-documentation/app-components/iml-functions)
- [Developer Hub — API Data Stores](https://developers.make.com/api-documentation/api-reference/data-stores)
- [Developer Hub — API Data Records](https://developers.make.com/api-documentation/api-reference/data-stores-greater-than-data)
- [Developer Hub — Pagination](https://developers.make.com/custom-apps-documentation/component-blocks/api/pagination)
- [Help Center — IP Addresses](https://help.make.com/allow-connections-to-and-from-make-ip-addresses)
- [Help Center — OAuth 2.0](https://help.make.com/connect-to-any-web-service-using-oauth-20)

### Community e Risorse Esterne
- [Make Community — Pagination Techniques](https://community.make.com/t/master-pagination-techniques-of-api-in-make-formerly-integromat/7140)
- [Hookdeck — Webhook Rate Limit Solutions](https://hookdeck.com/webhooks/platforms/how-to-solve-make-com-webhook-rate-limit-errors)
- [4Spot Consulting — Webhook Best Practices](https://4spotconsulting.com/make-com-webhooks-best-practices-for-resilient-secure-workflows/)
- [Securing Webhooks with HMAC](https://medium.com/@shahrukhkhan_7802/securing-webhooks-with-hmac-authentication-127bf24186cb)

---

**Documento creato**: 2026-04-06
**Prossimo aggiornamento previsto**: Quando Make rilascia aggiornamenti significativi a IML, Data Stores o HTTP Module
**Complemento**: Questo documento è Part 3 della serie Make.com Architecture. Vedi anche Part 1 (Core Architecture), Part 2 (Connections, Routers, Logic), Part 4 (Error Handling, Performance, Production).
