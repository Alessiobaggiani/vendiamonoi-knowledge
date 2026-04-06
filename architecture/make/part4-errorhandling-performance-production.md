# Make.com — Part 4: Error Handling, Performance e Production
## EXPERT-LEVEL TECHNICAL DEEP-DIVE

**Documento**: Riferimento tecnico esaustivo per error handling, ottimizzazione performance e best practice produzione
**Target**: Senior automation engineers e architetti di sistema Vendiamonoi.it
**Versione**: Make.com 2026 (sistema crediti)
**Ultimo aggiornamento**: 2026-04-06
**Complemento a**: Part 1 (Core Architecture), Part 2 (Connections, Routers, Logic), Part 3 (IML, Data Stores, Webhooks)

---

## INDICE COMPLETO

1. [Error Handling — Sistema Completo](#1-error-handling--sistema-completo)
   - 1.1 I 5 Error Handler Types
   - 1.2 Error Object Structure
   - 1.3 Error Routes Architecture
   - 1.4 Incomplete Executions System
   - 1.5 Retry Automatico e Exponential Backoff
   - 1.6 Rate Limiting (429) e Gestione
   - 1.7 Error Notification Setup
   - 1.8 Pattern Error Handling per E-commerce
2. [Performance Optimization](#2-performance-optimization)
   - 2.1 Modello Crediti/Operazioni (2025-2026)
   - 2.2 Strategie Riduzione Operazioni
   - 2.3 Iterator + Aggregator Optimization
   - 2.4 Router e Filter Optimization
   - 2.5 Data Store vs Servizi Esterni
   - 2.6 Custom Functions per Riuso Codice
   - 2.7 Scheduling Ottimizzato
   - 2.8 Limiti di Esecuzione e Workaround
   - 2.9 Scaling Patterns per Alto Volume
3. [Production Best Practices](#3-production-best-practices)
   - 3.1 Versioning e Backup (Blueprint)
   - 3.2 Naming Convention e Folder Organization
   - 3.3 Environment Management (Dev/Staging/Prod)
   - 3.4 Testing e Debugging
   - 3.5 Monitoring e Alerting
   - 3.6 Organization, Teams e Permissions
   - 3.7 Secrets e Credentials Management
   - 3.8 Disaster Recovery
   - 3.9 Documentazione Scenario
4. [Pattern Vendiamonoi — Produzione](#4-pattern-vendiamonoi--produzione)
   - 4.1 Architettura Error Handling Multi-Marketplace
   - 4.2 Monitoring Dashboard Setup
   - 4.3 Scaling per Milioni di SKU
   - 4.4 Checklist Production Readiness

---

# 1. Error Handling — Sistema Completo

## 1.1 I 5 Error Handler Types

Make.com fornisce 5 tipi di error handler, ciascuno con comportamento specifico. La scelta dell'handler corretto è critica per la resilienza del sistema.

### BREAK

**Comportamento**: Ferma l'esecuzione e la marca come **incompleta** (non come fallita)

**Dettagli**:
- L'esecuzione viene salvata nello store delle incomplete executions
- Tutti i dati processati fino a quel punto vengono preservati
- Richiede "Allow storing of incomplete executions" abilitato
- Supporta auto-retry se "automatic run completion" è configurato
- Le operazioni consumate fino al punto di errore vengono addebitate

**Quando usarlo**:
- Errori critici che richiedono intervento manuale prima del retry
- Timeout API temporanei che probabilmente si risolvono da soli
- Rate limiting da API terze parti
- Qualsiasi errore dove NON vuoi perdere i dati

**Configurazione auto-retry**:
```
Scenario Settings → Allow storing of incomplete executions: ON
Scenario Settings → Automatic run completion: ON
→ Make riprova automaticamente con exponential backoff
```

### RESUME

**Comportamento**: Fornisce valori di fallback predefiniti e **continua** l'esecuzione

**Dettagli**:
- Devi specificare i valori sostitutivi nel mapping dell'error handler
- L'output del modulo fallito viene sostituito con i tuoi valori
- Lo scenario continua e completa normalmente nonostante l'errore
- Lo stato finale è "Success"

**Quando usarlo**:
- Errori recuperabili dove un valore default è accettabile
- API che restituisce dati parziali (usa i campi disponibili)
- Moduli non critici dove il fallback non compromette il flusso

**Esempio Vendiamonoi**:
```
Modulo "Get Product Price from API" → ERRORE
  ↓
Resume Handler: price = lastKnownPrice, source = "cache"
  ↓
Continua con il prezzo cached invece di fermarsi
```

### IGNORE

**Comportamento**: Sopprime l'errore e **continua** senza dati sostitutivi

**Dettagli**:
- Nessun valore di fallback fornito
- I moduli downstream NON ricevono input da questo bundle
- Lo scenario completa con stato "Success"
- I moduli successivi che dipendono dall'output di questo modulo riceveranno null/undefined

**Quando usarlo**:
- Moduli completamente non critici
- Logging/notifiche secondarie che non devono bloccare il flusso
- Operazioni "best effort" dove il fallimento è accettabile

**Attenzione**: Se i moduli downstream richiedono input dal modulo ignorato, causerà errori a cascata.

### COMMIT

**Comportamento**: Salva le modifiche database già effettuate e **ferma** l'elaborazione

**Dettagli**:
- Funziona SOLO con moduli database ACID-compliant
- Persiste tutte le modifiche completate fino al punto di errore
- NON può annullare email inviate, file eliminati, o azioni non-database
- Marca l'esecuzione come "Success" nonostante l'errore

**Quando usarlo**:
- Operazioni database dove il successo parziale è accettabile
- Flussi dove le prime operazioni devono essere preservate

### ROLLBACK

**Comportamento**: Tenta di **revertire** tutte le azioni nell'esecuzione e ferma l'elaborazione

**Dettagli**:
- Reverte SOLO operazioni database ACID
- NON reverte chiamate API, invio email, operazioni file
- Marca l'esecuzione come "Error"
- Può creare inconsistenze se ci sono azioni non-database già eseguite

**Quando usarlo**:
- Transazioni all-or-nothing (rare in automazione marketplace)
- Solo quando TUTTE le operazioni sono database ACID

**Limitazione pratica**: Inadatto per workflow che mescolano database e API calls (che è il 99% dei casi marketplace).

### Tabella Comparativa Completa

| Handler | Continua? | Dati fallback? | Stato finale | Incomplete? | Auto-retry? | Uso tipico |
|---------|-----------|---------------|--------------|-------------|-------------|------------|
| **Break** | ❌ No | ❌ No | Incomplete | ✅ Sì | ✅ Sì | Errori temporanei, rate limit |
| **Resume** | ✅ Sì | ✅ Sì | Success | ❌ No | ❌ No | Fallback con valori default |
| **Ignore** | ✅ Sì | ❌ No | Success | ❌ No | ❌ No | Moduli non critici |
| **Commit** | ❌ No | ❌ No | Success | ❌ No | ❌ No | Salvare operazioni DB parziali |
| **Rollback** | ❌ No | ❌ No | Error | ❌ No | ❌ No | Transazioni all-or-nothing |

### Albero Decisionale Error Handler

```
Modulo in errore?
├─ Errore temporaneo (timeout, rate limit, connessione)?
│   └─ SÌ → BREAK (con auto-retry)
├─ Esiste un valore di fallback accettabile?
│   └─ SÌ → RESUME (configura valori sostitutivi)
├─ Il modulo è non critico (logging, notifica secondaria)?
│   └─ SÌ → IGNORE
├─ Operazione database che deve essere preservata?
│   └─ SÌ → COMMIT
└─ Tutto deve essere annullato (solo operazioni DB)?
    └─ SÌ → ROLLBACK
```

---

## 1.2 Error Object Structure

Quando un modulo genera un errore, l'oggetto errore contiene:

| Proprietà | Tipo | Descrizione |
|-----------|------|-------------|
| `message` | String | Descrizione leggibile dell'errore |
| `type` | String | Categoria errore (RuntimeError, ConnectionError, DataError, etc.) |
| `code` | String/Number | Codice errore machine-readable |
| `module` | Object | Riferimento al modulo che ha generato l'errore |
| `bundle` | Object | Il bundle dati che ha causato l'errore |

### Tipi di Errore Comuni

| Tipo | Descrizione | Causa Tipica | Handler Consigliato |
|------|-------------|-------------|--------------------|
| `ConnectionError` | Impossibile connettersi al servizio | Servizio down, credenziali scadute | Break (auto-retry) |
| `RuntimeError` | Errore riportato dall'API terza parte | Errore generico API | Break o Resume |
| `RateLimiterError` | Rate limit superato (429) | Troppe richieste | Break (auto-retry automatico) |
| `ModuleTimeoutError` | Timeout del modulo | API lenta, payload grande | Break (auto-retry) |
| `DataError` | Dati invalidi o tipo mismatch | Campo mancante, formato sbagliato | Resume con fallback |
| `BundleValidationError` | Validazione mapping fallita | Campo required mancante | Resume o fix mapping |
| `MaxFileSizeExceededError` | File troppo grande | Upload supera limite | Resume o Ignore |
| `IncompleteDataError` | Dati parziali ricevuti | API restituisce risposta incompleta | Resume con default |

---

## 1.3 Error Routes Architecture

Gli error handler si collegano all'output di errore di un modulo, creando un flusso parallelo:

```
                   ┌─ [SUCCESS] → Modulo 2 → Modulo 3 → ...
Modulo 1 ──────────┤
                   └─ [ERROR] → Error Handler → [opzionale: logging, notifica]
```

### Pattern: Try-Catch Equivalente

```
HTTP Request ("try")
  ├─ SUCCESS → Process Response → Save to Supabase
  └─ ERROR → Resume Handler (fallback values)
              ├─ Set Variable: errorOccurred = true
              └─ Continue flow con dati di fallback

→ Dopo il flow:
  Router:
    Route 1 (errorOccurred == false): Flusso normale
    Route 2 (errorOccurred == true): Log errore + Notifica
```

### Pattern: Error Logging Centralizzato

```
Qualsiasi Modulo
  └─ ERROR → Break Handler
              → HTTP Webhook a "Scenario Error Logger"
              → Payload: {scenario, module, error, timestamp, bundle}
```

---

## 1.4 Incomplete Executions System

Le incomplete executions sono una rete di sicurezza che salva le esecuzioni fallite con il loro stato dati, prevenendo la perdita di dati.

### Come Funziona

1. Un modulo con Break handler genera errore
2. L'esecuzione viene salvata con stato "Unresolved"
3. Progressione stato: Unresolved → Pending → In progress → Resolved
4. Auto-delete dopo 30 giorni se resolved
5. Il conteggio massimo dipende dal piano

### Trigger Auto-Retry (senza intervento manuale)

Make riprova automaticamente per questi tipi di errore:
- `RateLimiterError` (429)
- `ConnectionError`
- `ModuleTimeoutError`
- Break errors con automatic run completion abilitato

### Opzioni Retry Manuale

- Retry singola esecuzione incompleta
- Batch retry di multiple esecuzioni
- Fix scenario prima, poi retry

### Schedule Exponential Backoff Automatico

| Tentativo | Senza Incomplete Execution | Con Incomplete Execution |
|-----------|---------------------------|-------------------------|
| 1 | 1 minuto | 1 minuto |
| 2 | 10 minuti | 2 minuti |
| 3 | 10 minuti | 5 minuti |
| 4 | 10 minuti | 10 minuti |
| 5 | 30 minuti | 30 minuti |
| 6 | 1 ora | 1 ora |
| 7 | 6 ore | 6 ore |
| 8 | 24 ore | 24 ore |

---

## 1.5 Retry Automatico e Exponential Backoff

### Backoff Automatico Built-In

Make applica automaticamente exponential backoff per:
- RateLimiterError (429)
- ConnectionError
- ModuleTimeoutError

### Custom Exponential Backoff Pattern

Per implementare retry personalizzato dentro lo scenario:

```
Modulo API Call
  └─ ERROR → Set Variable (retryCount = retryCount + 1)
              → Router:
                Route 1 (retryCount <= 3):
                  → Sleep (retryCount * retryCount * 1000 ms)
                  → Ripeti API Call
                Route 2 (retryCount > 3):
                  → Break (salva come incomplete)
                  → Log errore finale
```

**Formula sleep con backoff**:
```
Sleep duration (ms) = {{min(retryCount * retryCount * 1000; 30000)}}

Tentativo 1: 1 secondo
Tentativo 2: 4 secondi
Tentativo 3: 9 secondi
Max: 30 secondi
```

---

## 1.6 Rate Limiting (429) e Gestione

### Rate Limits per Piano Make.com

| Piano | Richieste API Make/minuto |
|-------|---------------------------|
| Core | 60 |
| Pro | 120 |
| Teams | 240 |
| Enterprise | 1.000 |

### Rate Limits API Marketplace (riferimento)

| Marketplace | Rate Limit Tipico |
|-------------|------------------|
| Amazon SP-API | 1-30 req/sec (varia per endpoint) |
| eBay | 5.000/giorno (varia per API) |
| Kaufland | 100 req/min |
| Fnac-Darty | 60 req/min |
| Leroy Merlin | 30 req/min |
| Carrefour | Variabile |

### Strategia Anti-Rate-Limit

1. **Sleep tra le chiamate**: `Sleep(1000)` tra ogni API call
2. **Batch operations**: Aggregare e inviare in blocco
3. **Distribute nel tempo**: Scheduling sfalsato per marketplace diversi
4. **Break + auto-retry**: Per errori 429 inevitabili
5. **Data Store rate counter**: Traccia chiamate per endpoint

### Pattern Rate Counter con Data Store

```
1. Get Record (key = "api_endpoint_name")
2. IF callCount < rateLimit AND window non scaduta:
   → Increment callCount → Procedi con API call
3. IF callCount >= rateLimit:
   → Sleep fino a fine window → Reset count
4. IF window scaduta:
   → Reset callCount = 1 → Procedi
```

---

## 1.7 Error Notification Setup

### Canali di Notifica Disponibili

1. **Email**: Notifiche errore native di Make
2. **Webhook**: Payload JSON a sistema esterno
3. **Slack**: Via webhook o modulo Slack nativo
4. **Data Store**: Logging errori persistente
5. **Supabase**: Tabella errori per analytics

### Pattern: Centralized Error Hub

```
[Scenario Produzione 1] → Error → HTTP Webhook → 
[Scenario Produzione 2] → Error → HTTP Webhook → [Error Hub Scenario]
[Scenario Produzione 3] → Error → HTTP Webhook → 
                                                    ↓
                                              Parse payload
                                                    ↓
                                              Router:
                                              ├─ Severity HIGH → Slack + Email
                                              ├─ Severity MEDIUM → Slack only
                                              └─ Severity LOW → Data Store log only
```

### Payload Error Webhook

```json
{
  "scenario": "{{scenario.name}}",
  "scenarioId": "{{scenario.id}}",
  "module": "{{error.module}}",
  "errorType": "{{error.type}}",
  "errorMessage": "{{error.message}}",
  "timestamp": "{{formatDate(now(); \"YYYY-MM-DDTHH:mm:ssZ\")}}",
  "marketplace": "{{bundle.marketplace}}",
  "orderId": "{{bundle.orderId}}",
  "severity": "{{if(error.type == \"ConnectionError\"; \"HIGH\"; \"MEDIUM\")}}"
}
```

---

## 1.8 Pattern Error Handling per E-commerce

### Pattern: Ordine Marketplace con Error Recovery

```
Webhook (ordine in arrivo)
  → Validate payload (Filter: ha tutti i campi required?)
    ├─ Invalid → Webhook Response 400 + Log errore
    └─ Valid → Continue
  → Dedup check (Data Store)
    ├─ Duplicato → Webhook Response 409 + Skip
    └─ Nuovo → Continue
  → Save to Supabase
    └─ ERROR → Break (auto-retry) + Notifica Slack
  → Create Invoice (Fatture in Cloud)
    └─ ERROR → Resume (invoice = null, invoiceError = true)
  → Forward to Supplier
    └─ ERROR → Break (auto-retry) + Notifica urgente
  → Webhook Response 200 OK
  → Post-check:
    IF invoiceError == true → Queue per retry fatturazione
```

### Pattern: Sync Prezzi con Graceful Degradation

```
Scheduled trigger
  → Get products from SellRapido
    └─ ERROR → Break (stop tutto, non sincronizzare con dati vecchi)
  → Iterator (per ogni prodotto)
  → Calculate marketplace price
  → Update price on Amazon
    └─ ERROR → Resume (amazonUpdate = "failed") → Log
  → Update price on eBay  
    └─ ERROR → Resume (ebayUpdate = "failed") → Log
  → Update price on Kaufland
    └─ ERROR → Resume (kauflandUpdate = "failed") → Log
  → Aggregator → Report finale
  → IF any failed → Slack notification con lista errori
```

---

# 2. Performance Optimization

## 2.1 Modello Crediti/Operazioni (2025-2026)

### Transizione a Crediti (Agosto 2025)

- Da agosto 2025: passaggio da operazioni a crediti
- Conversione: 1 Operazione = 1 Credito (legacy auto-convert)
- Il modello crediti offre tracking più granulare

### Cosa Conta come Crediti

| Tipo | Consumo |
|------|--------|
| Workflow non-AI | 1 operazione = 1 credito |
| Workflow AI (Made AI) | 1 operazione + token consumati = crediti variabili |
| AI con chiave propria (OpenAI/Claude) | 1 credito per chiamata (fisso) |

### Piani e Crediti (2026)

| Piano | Costo | Crediti | Extra Credits |
|-------|-------|---------|---------------|
| Free | $0 | Limitati | N/A |
| Core | ~$10.59/mese | 10.000 | +25% premium |
| Pro | ~$18.82/mese | Fino a 8M/mese | +25% premium |
| Teams | ~$34.12/mese | Illimitati | +25% premium |
| Enterprise | Custom | Custom | Negoziabile |

### Cosa Conta come Operazione (1 credito ciascuna)

- Ogni modulo che processa un bundle = 1 operazione
- Un Iterator che split 100 items = 100 operazioni per i moduli successivi
- Un Aggregator che combina 100 items = 1 operazione
- Un Router = 0 operazioni (non conta)
- Un Filter che blocca = 0 operazioni (bundle bloccato non esegue moduli successivi)
- Sleep = 1 operazione
- Set Variable = 1 operazione
- Data Store operations = 1 operazione ciascuna

---

## 2.2 Strategie Riduzione Operazioni

### Le 10 Regole d'Oro

1. **Filtra PRESTO**: Metti i filtri il prima possibile nel flusso per ridurre i dati che attraversano i moduli successivi

2. **Usa operazioni bulk**: Una singola API call con batch di 100 record = 1 operazione. 100 API call singole = 100 operazioni

3. **Aggregator prima di API call**: Iterator → Process → Aggregator → Singola API call bulk

4. **Scheduling appropriato**: Non schedulare ogni minuto se bastano 15 minuti

5. **Rimuovi moduli inutilizzati**: Ogni modulo inattivo che processa dati consuma operazioni

6. **Router con filtri precisi**: Evita che bundle attraversino branch che non li riguardano

7. **Data Store come cache**: Evita chiamate API ripetute per dati che cambiano raramente

8. **Custom Functions**: Combina 10-12 moduli di trasformazione in 1-2 custom functions

9. **Conditional processing**: Processa solo i dati che sono effettivamente cambiati

10. **Monitora e analizza**: Identifica quali automazioni consumano più crediti

**Risparmio potenziale**: Fino al 40% di riduzione senza perdita di funzionalità.

### Esempio Concreto: Prima vs Dopo

**PRIMA (inefficiente) — 500 prodotti = 2.500 operazioni**:
```
Get 500 products → Iterator → HTTP Update Amazon → HTTP Update eBay → HTTP Update Kaufland
= 500 × 5 = 2.500 operazioni
```

**DOPO (ottimizzato) — 500 prodotti = ~550 operazioni**:
```
Get 500 products → Iterator → Filter (solo changed) → 100 changed
→ Aggregator (batch 100) → HTTP Bulk Update Amazon → HTTP Bulk Update eBay → HTTP Bulk Update Kaufland
= 500 + 100 + 1 + 1 + 1 + 1 ≈ 550 operazioni (78% risparmio)
```

---

## 2.3 Iterator + Aggregator Optimization

### Iterator

**Funzione**: Divide un bundle grande in multiple bundle individuali

**Proprietà avanzate**:
- `bundleOrderPosition`: posizione nel sequenza (utile per tracking)
- `totalNumberOfBundles`: numero totale di items (utile per progress)

### Array Aggregator

**Funzione**: Inverso dell'Iterator — combina multiple bundle in un singolo array

**Feature avanzate**:
- `Group By`: organizza output aggregato per campo specifico
- Combina con operazioni bulk per riduzione drammatica operazioni

### Pattern Ottimale

```
Search Records (500 risultati)
  → Iterator (500 bundle individuali)
    → Process Item (trasforma, valida)
    → Filter (solo items validi/changed)
  → Array Aggregator (raccoglie risultati)
  → HTTP Bulk Write (singola API call)

Risultato: da N chiamate API a 1 chiamata bulk
```

### Caso Studio Reale

Agenzia di marketing digitale: sync giornaliero Airtable ridotto da **2 ore a 10 minuti** usando Iterator + Aggregator con bulk operations.

---

## 2.4 Router e Filter Optimization

### Router

**Meccanica**: Un input → multiple output branch. Ogni bundle segue UNA sola branch (la prima che matcha il filtro).

**Ottimizzazione**:
- Condizioni filtro non sovrapposte → più efficiente
- Ordine branch: metti le condizioni più frequenti PRIMA
- Branch catch-all (senza filtro) sempre ULTIMA
- Evita branching ridondante — consolida condizioni simili

### Filter

**Costo zero per bundle filtrati**: I bundle che NON passano il filtro non eseguono i moduli successivi = 0 operazioni per quei bundle.

**Risparmio**: Studi mostrano fino all'80% di riduzione processing con filtri appropriati.

**Pattern per Marketplace**:
```
Get Orders → Filter (status == "new" AND marketplace == "amazon")
→ Solo ordini Amazon nuovi passano → Process

Senza filtro: 1000 ordini × 5 moduli = 5000 ops
Con filtro (100 Amazon nuovi): 1000 + 100 × 5 = 1500 ops (70% risparmio)
```

---

## 2.5 Data Store vs Servizi Esterni

### Quando Usare Data Store (Interno)

- Cache risposte API (TTL-based)
- Deduplicazione tra esecuzioni
- State management (cursor, last processed)
- Lookup tables statiche (commissioni, IVA)
- < 100.000 record
- Latenza < 50ms

### Quando Usare Database Esterno (Supabase)

- Dati core business (prodotti, ordini)
- > 100.000 record
- Query complesse (JOIN, aggregazioni)
- Analytics e reporting
- Multi-accesso (API, dashboard, altri servizi)
- Backup automatico

### Confronto Performance

| Operazione | Data Store | Supabase (esterno) |
|-----------|-----------|-------------------|
| Get by key | < 50ms | 50-200ms |
| Search/filter | 50-500ms (O(n)) | 10-100ms (indexed) |
| Write | < 50ms | 50-200ms |
| Bulk write 1000 | N/A (one at a time) | < 500ms |
| Complex query | Non supportato | < 100ms |

---

## 2.6 Custom Functions per Riuso Codice

### Cosa Sono

Funzioni JavaScript personalizzate riutilizzabili in tutti i moduli dello scenario.

**Vantaggi**:
- Riducono il numero di moduli (da 10-12 a 1-2)
- Condivise tra tutto il team
- Nomi descrittivi (formatInvoiceDate, normalizePhoneNumber)

**Limitazioni**:
- Non possono chiamare altre custom functions
- No ricorsione
- Condivise solo a livello team (non cross-team)

### Esempio per Vendiamonoi

```javascript
// Custom function: calculateMarketplacePrice
function calculateMarketplacePrice(costPrice, marketplace) {
  const commissions = {
    'amazon': 0.15,
    'ebay': 0.12,
    'kaufland': 0.10,
    'fnac': 0.13,
    'carrefour': 0.11,
    'leroy-merlin': 0.12
  };
  const commission = commissions[marketplace] || 0.10;
  const vatRate = 0.22; // IVA italiana
  const margin = 0.30; // 30% margine
  
  return Math.round(
    (costPrice * (1 + margin) / (1 - commission)) * (1 + vatRate) * 100
  ) / 100;
}
```

---

## 2.7 Scheduling Ottimizzato

### Opzioni Schedule

- Intervalli regolari: 1, 5, 15, 30, 60 minuti
- Una volta (one-time)
- Giornaliero a ora specifica
- Giorni specifici della settimana
- Giorni specifici del mese
- Data specifica custom

### Strategia per Vendiamonoi

| Scenario | Frequenza Consigliata | Motivazione |
|----------|----------------------|-------------|
| Sync ordini marketplace | Webhook (real-time) | Ordini critici, serve immediatezza |
| Sync stock | Ogni 2 ore | Stock cambia spesso ma non in real-time |
| Sync prezzi | Ogni 6 ore | Prezzi cambiano raramente intra-giorno |
| Sync catalogo nuovi prodotti | Ogni 24 ore (notte) | Catalogo cambia quotidianamente |
| Report giornaliero | 1×/giorno alle 07:00 | Aggregazione dati giornaliera |
| Cleanup/manutenzione | 1×/settimana | Pulizia data stores, log |
| Riconciliazione fatture | 1×/mese | Ciclo mensile fatturazione |

### Staggered Scheduling

Per evitare spike di carico, sfalsare gli scenari:

```
Scenario A (Amazon sync): :02 di ogni ora
Scenario B (eBay sync): :07 di ogni ora
Scenario C (Kaufland sync): :12 di ogni ora
Scenario D (Fnac sync): :17 di ogni ora
→ Previene thundering herd + distribuisce operazioni
```

---

## 2.8 Limiti di Esecuzione e Workaround

### Limiti Hard

| Limite | Valore |
|--------|--------|
| Tempo massimo esecuzione | **40 minuti** (alcuni report: 45 min) |
| Payload webhook | 5 MB |
| Dimensione bundle | Variabile (dipende dal piano) |
| Data store record | 512 KB |
| Rate limit webhook | 30/secondo |
| Rate limit API Make | 60-1000/min (per piano) |

### Workaround Timeout 40 Minuti

**Pattern: Scenario Chain**
```
Scenario A (raccolta dati) — max 10 min
  → Salva stato in Data Store
  → Trigger via webhook → Scenario B

Scenario B (elaborazione) — max 10 min
  → Legge stato da Data Store
  → Processa batch
  → Trigger via webhook → Scenario C

Scenario C (output) — max 10 min
  → Legge risultati
  → Invia a destinazioni finali
```

**Pattern: Queue-Based Processing**
```
Scenario Ingestion (leggero):
  Webhook → Validate → Save to Data Store queue → Response 202

Scenario Processor (schedulato ogni 5 min):
  Search Data Store (status = "pending", limit = 100)
  → Process batch
  → Update status = "complete"
  → Repeat until queue vuota (entro 40 min)
```

---

## 2.9 Scaling Patterns per Alto Volume

### Range Efficace di Make.com

- **Sweet spot**: 10K-100K operazioni/mese per singola automazione
- **Scala bene fino a**: ~1M operazioni/mese con ottimizzazione
- **Oltre 1M**: Considerare architettura ibrida (Make + n8n self-hosted)
- **Case study reale**: FINN opera 1.000+ scenari con 20M+ operazioni/mese

### Pattern Worker per Alto Volume

```
Main Scenario (Dispatcher):
  → Get large dataset
  → Split in chunk da 100
  → Per ogni chunk: HTTP Request a Worker Webhook
  → NON aspetta completamento (fire-and-forget)

Worker Scenario (via Webhook):
  → Riceve chunk di 100 items
  → Processa
  → Salva risultati
  → Webhook Response 200

→ 10 Worker eseguono in parallelo = throughput 10×
```

### Pattern per Milioni di SKU (Vendiamonoi)

```
Approccio ibrido:

1. SellRapido gestisce la distribuzione catalogo principale
2. Make gestisce:
   - Sync incrementali (solo changed items)
   - Gestione ordini (volume gestibile)
   - Monitoring e alerting
   - Riconciliazione
3. Supabase come buffer/cache per dati prodotto
4. n8n (futuro) per batch processing pesante

Regola: Make per orchestrazione e logica, non per bulk processing di milioni di record.
```

---

# 3. Production Best Practices

## 3.1 Versioning e Backup (Blueprint)

### Version History Built-In

- Nuova versione creata ad ogni click su "Save"
- Retention: 30-90 giorni (dipende dal piano)
- Accesso: tre puntini → Finestra versioni
- Restore istantaneo a qualsiasi versione precedente

### Blueprint Export/Import

**Export**:
1. Apri scenario → Controls (tre puntini)
2. Click "Export Blueprint"
3. File scaricato come `blueprint.json`
4. Salva in location di backup

**Contenuto del blueprint**:
- ✅ Configurazione moduli e parametri
- ✅ Valori mappati e formule
- ✅ Condizioni router e filtri
- ✅ Error handlers
- ✅ Logica flusso scenario
- ❌ Credenziali API e password
- ❌ Token autenticazione
- ❌ Valori environment-specific
- ❌ Informazioni login connessioni

**Import**:
1. Crea nuovo scenario
2. Menu ellipsis → "Import Blueprint from File"
3. Seleziona file .json
4. Configura connessioni e webhook
5. Aggiorna valori environment-specific
6. Salva

### Strategia Backup Automatico

```
Scenario Backup (schedulato settimanale):
  → Make API: GET /scenarios (lista tutti gli scenari)
  → Iterator: per ogni scenario
  → Make API: GET /scenarios/{id}/blueprint
  → Google Drive: Salva blueprint.json
    Filename: "backup_{{scenario.name}}_{{formatDate(now(); 'YYYYMMDD')}}.json"
  → Mantieni ultimi 4 backup per scenario
```

---

## 3.2 Naming Convention e Folder Organization

### Convention Consigliata per Vendiamonoi

```
[REPARTO]-[PROCESSO]-[MARKETPLACE/DETTAGLIO]-[ENV]

Esempi:
ORDINI-SyncOrdini-Amazon-PROD
ORDINI-SyncOrdini-eBay-PROD
CATALOGO-SyncPrezzi-AllMarketplaces-PROD
CATALOGO-SyncStock-AllMarketplaces-PROD
FATTURAZIONE-AutoFattura-FattureInCloud-PROD
CS-SyncTickets-eDesk-PROD
MONITORING-ErrorHub-Central-PROD
MANUTENZIONE-Cleanup-DataStores-PROD

Ambienti:
-DEV  → sviluppo e test
-STAG → staging con dati reali limitati
-PROD → produzione
```

### Struttura Folder

```
📁 Vendiamonoi Make.com
├── 📁 🔴 PRODUZIONE
│   ├── 📁 Ordini
│   ├── 📁 Catalogo
│   ├── 📁 Fatturazione
│   ├── 📁 Customer Service
│   └── 📁 Monitoring
├── 📁 🟡 STAGING
│   └── (mirror di produzione)
├── 📁 🟢 DEVELOPMENT
│   └── (scenari in test)
└── 📁 ⚙️ INFRASTRUCTURE
    ├── Error Hub
    ├── Backup
    └── Manutenzione
```

---

## 3.3 Environment Management (Dev/Staging/Prod)

### Tre Ambienti

**Development**:
- Connessioni a account sandbox/test
- Solo dati di test
- Modifiche frequenti
- Nessun requisito performance

**Staging**:
- Connessioni a account produzione (scope limitato)
- Volume dati simile a produzione
- Test finale prima della produzione
- Mirror della configurazione produzione

**Production**:
- Connessioni live
- Marcato chiaramente con -PROD
- Configurazioni stabili e testate
- Monitoring e alerting attivi
- Accesso modifiche ristretto

### Promozione Scenario

```
1. Sviluppa in -DEV
2. Testa manualmente
3. Export blueprint
4. Import in -STAG
5. Configura connessioni staging
6. Test con dati reali limitati
7. Verifica per 24-48 ore
8. Export blueprint
9. Import in -PROD
10. Configura connessioni produzione
11. Attiva monitoring
12. Deploy completo
```

---

## 3.4 Testing e Debugging

### Approcci di Test

1. **Run manuale**: Esegui intero scenario una volta
2. **Run singolo modulo**: Testa modulo in isolamento
3. **Run parziale**: Testa sottoinsieme dal punto specifico
4. **Replay esecuzione precedente**: Ri-esegui esecuzione storica

### Debugging Best Practices

- Decomponi in componenti testabili
- Testa moduli separatamente prima di assemblarli
- Ispeziona dati input/output di ogni modulo
- Verifica che i campi mappati contengano valori attesi
- Usa Set Variable per logging intermedio
- Scenario Debugger: cerca moduli per nome, ispeziona bundle

### Checklist Pre-Deploy

- [ ] Run manuale completato con successo
- [ ] Tutti i percorsi errore testati
- [ ] Error handlers configurati su moduli critici
- [ ] Dati di test rappresentativi
- [ ] Performance accettabile (tempo < 5 min per run tipico)
- [ ] Naming convention rispettata
- [ ] Blueprint esportato come backup

---

## 3.5 Monitoring e Alerting

### Analytics Dashboard (Enterprise)

Metriche disponibili:
- Operazioni per team e totali
- Errori totali e error rate
- Esecuzioni per scenario
- Trending 24h, 7gg, 30gg
- Filtri per stato, team, folder

### API Monitoring

| Endpoint | Descrizione |
|----------|-------------|
| `GET /api/v2/analytics/{orgId}` | Analytics organizzazione |
| `GET /api/v2/audit-logs` | Audit log per compliance |
| `GET /api/v2/hooks/{hookId}/logs` | Log esecuzioni webhook |
| `GET /api/v2/scenarios/{id}` | Dettaglio scenario |

### Metriche Chiave da Monitorare

| Metrica | Threshold Alert | Azione |
|---------|----------------|--------|
| Error rate | > 5% | Investigare causa |
| Execution time | > 30 min | Ottimizzare o split |
| Incomplete executions | > 10 pending | Risolvere o escalare |
| Credits usage | > 80% piano | Ottimizzare o upgrade |
| API 429 errors | > 10/ora | Implementare rate limiting |

---

## 3.6 Organization, Teams e Permissions

### Struttura

```
Organizzazione (Vendiamonoi.it)
├── Team: Operazioni
│   ├── Scenari ordini
│   ├── Scenari stock
│   └── Scenari spedizioni
├── Team: Catalogo
│   ├── Scenari sync prodotti
│   ├── Scenari prezzi
│   └── Scenari feed
├── Team: Amministrazione
│   ├── Scenari fatturazione
│   ├── Scenari riconciliazione
│   └── Scenari OSS
└── Team: Infrastruttura
    ├── Monitoring
    ├── Backup
    └── Manutenzione
```

### Ruoli Disponibili

| Ruolo | Accesso | Gestione Team | Pubblica Scenari |
|-------|---------|---------------|------------------|
| Team Admin | Completo | ✅ Sì | ✅ Sì |
| Team Member | Completo | ❌ No | ✅ Sì |
| Team Restricted | Completo | ❌ No | ❌ No |
| Team Operator | Read-only + start/stop | ❌ No | ❌ No |
| Team Monitoring | Read-only | ❌ No | ❌ No |

### Asset per Team

Ogni team possiede e gestisce:
- Scenari e Templates
- Connessioni e Webhook
- Data Stores e Data Structures
- Custom Functions
- Keys e Devices

---

## 3.7 Secrets e Credentials Management

### Best Practices

1. **Usa il sistema Connection nativo** di Make quando disponibile
2. **Non hardcodare** credenziali nei campi testo dello scenario
3. **Data Store per secrets** che devono essere referenziati dinamicamente
4. **Ruota regolarmente** chiavi API e token
5. **Limita visibilità** — solo ruoli necessari vedono scenari con credenziali
6. **Documenta** quali scenari usano quali credenziali

### Pattern per API Keys Dinamiche

```
Data Store "credentials":
  key: "amazon_api_key"
  value: "amzn1.xxx..." (encrypted at rest)
  expiresAt: "2026-12-31"
  lastRotated: "2026-04-01"

Scenario:
  1. Get Record (key = "amazon_api_key")
  2. Check expiresAt
  3. Use value in HTTP Request header
```

---

## 3.8 Disaster Recovery

### Strategia Backup

| Cosa | Frequenza | Dove | Retention |
|------|-----------|------|-----------|
| Blueprint scenari critici | Settimanale | Google Drive | 12 mesi |
| Data Store contenuti | Giornaliera | Supabase | 30 giorni |
| Lista connessioni | Mensile | Documentazione | Permanente |
| Configurazione webhook URL | Ad ogni modifica | Documentazione | Permanente |

### Procedura Recovery

1. **Rollback versione**: Tre puntini → Versioni → Restore
2. **Import blueprint**: Import da backup .json
3. **Ri-connetti**: Configura tutte le connessioni
4. **Re-deploy webhook**: Aggiorna URL nei sistemi esterni
5. **Retry incomplete**: Processa esecuzioni incomplete pendenti
6. **Verifica**: Run manuale per confermare funzionamento

---

## 3.9 Documentazione Scenario

### Template Documentazione

```
# Nome Scenario: [REPARTO]-[PROCESSO]-[DETTAGLIO]-[ENV]

## Scopo
[Quale problema business risolve]

## Data Flow
[Input source] → [Processing] → [Output destination]

## Dipendenze
- Sistemi esterni: [lista]
- Altri scenari: [lista]
- Timing: [requisiti temporali]

## Error Handling
- Tipo: [Break/Resume/Ignore per ogni modulo critico]
- Notifiche: [chi viene avvisato]
- Recovery: [procedura di recovery]

## Owner
- Responsabile: [nome]
- Creato: [data]
- Ultimo aggiornamento: [data]

## Changelog
- [data]: [modifica]
```

---

# 4. Pattern Vendiamonoi — Produzione

## 4.1 Architettura Error Handling Multi-Marketplace

```
                    ┌─ Amazon Sync
                    │   └─ ERROR → Resume (log + continue)
Scheduled Trigger → ├─ eBay Sync  
                    │   └─ ERROR → Resume (log + continue)
                    ├─ Kaufland Sync
                    │   └─ ERROR → Resume (log + continue)
                    └─ Fnac Sync
                        └─ ERROR → Resume (log + continue)
                    ↓
              Aggregator (raccogli risultati + errori)
                    ↓
              IF errori > 0:
                → Slack notification
                → Data Store: salva errori
                → Supabase: log analytics
```

**Principio**: Un marketplace in errore NON blocca gli altri. Resume per ogni branch, aggregazione errori alla fine.

## 4.2 Monitoring Dashboard Setup

```
Scenario: MONITORING-DailyReport-PROD
Schedule: Ogni giorno alle 07:00

1. Make API: GET esecuzioni ultime 24h
2. Calcola:
   - Totale esecuzioni
   - Errori per scenario
   - Errori per marketplace
   - Tempo medio esecuzione
   - Crediti consumati
3. Formatta report
4. Invia su Slack #monitoring
5. Salva su Supabase per storico
```

## 4.3 Scaling per Milioni di SKU

```
Architettura consigliata:

[SellRapido] ←→ [Supabase] ←→ [Make.com] ←→ [Marketplace]
       ↑              ↑              ↑
   Catalogo       Buffer/Cache   Orchestrazione
   completo       incrementale   & logica

- SellRapido: gestisce il catalogo completo (milioni SKU)
- Supabase: buffer per sync incrementale (solo changed)
- Make: orchestrazione, logica business, error handling
- n8n (futuro): batch processing pesante

Regola d'oro: Make non processa mai milioni di record.
Make processa solo i DELTA (cambiamenti).
```

## 4.4 Checklist Production Readiness

### Error Handling

- [ ] Error handler su ogni modulo HTTP/API
- [ ] Break con auto-retry per errori temporanei
- [ ] Resume con fallback per moduli non critici
- [ ] Incomplete executions abilitato
- [ ] Error Hub scenario attivo
- [ ] Notifiche Slack per errori critici

### Performance

- [ ] Filtri posizionati il prima possibile
- [ ] Bulk operations dove possibile
- [ ] Scheduling appropriato (non over-frequent)
- [ ] Tempo esecuzione < 30 min
- [ ] Consumo crediti monitorato

### Production

- [ ] Naming convention rispettata
- [ ] Blueprint esportato come backup
- [ ] Connessioni produzione configurate
- [ ] Test completati in staging
- [ ] Documentazione scenario aggiornata
- [ ] Permissions team configurate
- [ ] Monitoring attivo
- [ ] Recovery plan documentato

---

## FONTI E RIFERIMENTI

### Documentazione Ufficiale
- [Help Center — Error Handlers](https://help.make.com/error-handlers)
- [Help Center — Error Types](https://www.make.com/en/help/errors/types-of-errors-in-make)
- [Help Center — Incomplete Executions](https://www.make.com/en/help/scenarios/incomplete-executions)
- [Help Center — Automatic Retry](https://help.make.com/automatic-retry-of-incomplete-executions)
- [Help Center — Exponential Backoff](https://help.make.com/exponential-backoff)
- [Help Center — Credits](https://help.make.com/credits)
- [Help Center — Operations](https://help.make.com/operations)
- [Help Center — Scenario History](https://help.make.com/scenario-history)
- [Help Center — Blueprints](https://help.make.com/blueprints)
- [Help Center — Schedule](https://help.make.com/schedule-a-scenario)
- [Help Center — Teams](https://help.make.com/teams)
- [Help Center — Organizations](https://help.make.com/organizations)
- [Help Center — Analytics Dashboard](https://help.make.com/analytics-dashboard)
- [Help Center — Rate Limiting](https://help.make.com/fixing-rate-limit-errors)
- [Developer Hub — Rate Limiting API](https://developers.make.com/api-documentation/getting-started/rate-limiting)
- [Developer Hub — Blueprints API](https://developers.make.com/api-documentation/api-reference/scenarios-greater-than-blueprints)
- [Developer Hub — Audit Logs](https://developers.make.com/api-documentation/api-reference/audit-logs)

### Community e Risorse Esterne
- [Best Practices Error Handling](https://medium.com/@supbarty/best-practises-for-error-handling-in-make-com-explained-b73e1cfbf86d)
- [FINN Monitoring at Scale](https://dev.to/finnauto/how-we-monitor-our-make-instance-at-finn-1f9a)
- [1000+ Scenarios with 20M Operations](https://community.make.com/t/4-steps-to-operate-1-000-scenarios-with-over-20-million-operations-per-month/48396)
- [Queue Pattern for Timeout Prevention](https://community.make.com/t/scenario-hack-preventing-maximum-execution-time-rate-limiting-using-queues/10509)
- [Naming Conventions](https://slashrepeat.com/best-method-naming-make-com-scenarios/)
- [Custom Functions Guide](https://consultevo.com/make-com-custom-functions-guide/)
- [Scenario Optimization](https://mcstarters.com/blog/how-to-optimize-make-com-scenarios-for-faster-performance/)

---

**Documento creato**: 2026-04-06
**Prossimo aggiornamento previsto**: Quando Make rilascia aggiornamenti significativi al sistema crediti o error handling
**Complemento**: Part 4 della serie Make.com Architecture. Vedi anche Part 1 (Core), Part 2 (Connections), Part 3 (IML, Data Stores, Webhooks).
