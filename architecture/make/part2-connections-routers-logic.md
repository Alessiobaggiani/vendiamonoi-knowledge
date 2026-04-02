# MAKE.COM PART 2: Connessioni, Router, Filtri e Logica Condizionale
## Expert-Level Technical Documentation for Vendiamonoi.it

**Document Version:** 2.0
**Target Audience:** Senior Automation Engineers, Platform Architects
**Target Lines:** 5,000+
**Language:** Italian (headers), English/Italian (technical content)
**Last Updated:** April 2026

---

## 1. Sistema Connessioni — Architettura Completa

### 1.1 Cosa sono le Connessioni: L'Astrazione dell'Autenticazione

Una connessione in Make è un'astrazione di livello più alto rispetto ai dati di autenticazione grezza. Invece di inserire credenziali direttamente in ogni modulo, crei una **connessione una volta**, e poi la riutilizzi in tutti i moduli che accedono a quel servizio.

**Vantaggi della centralizzazione:**
- **Gestione centralizzata del ciclo di vita:** scadenza, refresh automatici, rotazione chiavi
- **Audit e compliance:** tutte le credenziali passano attraverso infrastruttura Make certificata
- **Riuso:** una connessione Shopify Production serve per tutti i moduli Shopify
- **Isolamento del segreto:** le credenziali grezze non compaiono mai nel blueprint, solo un riferimento ID
- **Team collaboration:** connessioni a livello team sono condivise tra scenari

**Modello concettuale:**

```
┌─────────────────────────────────────────────┐
│   Your Service (Shopify, Google, Slack)    │
└────────────────┬────────────────────────────┘
                 │
                 │ OAuth2 / API Key / Basic Auth
                 │
┌────────────────▼────────────────────────────┐
│   Make Connection Layer                     │
│   - Stores encrypted credentials           │
│   - Manages token refresh                  │
│   - Audits all authentications            │
│   - Handles expiry & re-auth              │
└────────────────┬────────────────────────────┘
                 │
                 │ connection_id
                 │
┌────────────────▼────────────────────────────┐
│   Your Scenario Modules                     │
│   - Shopify: Watch Products Module         │
│   - Google Sheets: Append Row Module       │
│   - Slack: Send Message Module             │
│   (All using same connection_id)           │
└─────────────────────────────────────────────┘
```

---

### 1.2 Ciclo di Vita di una Connessione

**Fase 1: Authorization Flow**

Quando configuri una connessione per Shopify (ad esempio):

1. Clicchi "Add a Connection" in un modulo Shopify
2. Viene aperta una finestra OAuth: `https://accounts.shopify.com/oauth/authorize?...`
3. Ti autentichi con le tue credenziali Shopify
4. Shopify reindirizza con un `authorization_code`
5. Make scambia il `code` per un `access_token` (backend-to-backend)
6. L'access_token è crittografato e salvato nei server Make
7. Un `connection_id` (es. `conn_12345abcde`) è generato

```
You (Browser)                Shopify             Make Backend
     │                          │                     │
     │─ Click "Connect" ──────────────────────────────│
     │                          │                     │
     │◄─ Redirect to Shopify ───────────────────────────│
     │                          │                     │
     │─ Authenticate ──────────▶│                     │
     │                          │                     │
     │◄─ Redirect with code ──────────────────────────│
     │                          │                     │
     │                          │◄─ Exchange code ─────│
     │                          │                     │
     │                          │   for token         │
     │                          │                     │
     │                          │─ Send token ───────▶│
     │                          │                     │
     │◄─────────────────────────────────────────────────│
     │        Connection ready (conn_12345abcde)       │
```

**Fase 2: Token Usage**

Ogni volta che un modulo della tua scenario esegue:

```
Scenario Execution
  │
  ├─ Modulo A (Shopify: Get Products)
  │   └─ Richiede: conn_12345abcde
  │       └─ Make retrieves e decrypts token
  │           └─ Shopify API call: Authorization: Bearer {token}
  │               └─ Riceve: 200 OK + JSON
  │
  ├─ Modulo B (Google Sheets: Append)
  │   └─ Richiede: conn_sheet_67890xyz
  │       └─ Make retrieves e decrypts token
  │           └─ Google API call: Authorization: Bearer {token}
  │               └─ Riceve: 200 OK + row appended
```

**Fase 3: Token Refresh (per OAuth2)**

Gli access token hanno una scadenza (di solito 1-2 ore). Per questo OAuth fornisce un `refresh_token`:

```
Alarm: access_token scade tra 5 minuti
  │
  ├─ Make Backend rileva scadenza imminente
  │   └─ Usa refresh_token per ottenere nuovo access_token
  │       └─ Shopify: POST /oauth/access_token?refresh_token=...
  │           └─ Riceve: nuovo access_token (valido altri 1 anno)
  │
  └─ Token aggiornato, scenario continua senza interruzione
```

Questo è **trasparente**: il tuo scenario non sa nulla del refresh. Make lo gestisce automaticamente.

---

### 1.3 Anatomia di una Connessione: Cosa Memorizza Make

Quando salvi una connessione, Make memorizza:

**A. Credenziali Crittografate**
- Per OAuth: `access_token`, `refresh_token`, `expires_at`
- Per API Key: la chiave stessa (crittografata a riposo)
- Per Basic Auth: username e password (crittografati a riposo)

**B. Metadata**
```json
{
  "connection_id": "conn_12345abcde",
  "service": "shopify",
  "created_at": "2026-04-01T10:30:00Z",
  "created_by": "user_xyz@example.com",
  "team_id": "team_99999",
  "account_name": "john-store.myshopify.com",
  "scopes_granted": ["read_products", "write_orders"],
  "last_used_at": "2026-04-01T14:22:10Z",
  "last_refreshed_at": "2026-04-01T13:00:00Z",
  "status": "active"
}
```

**C. Audit Log**
```
Connection conn_12345abcde Activity:
- 2026-04-01 10:30 | CREATED | john@example.com
- 2026-04-01 11:00 | USED | scenario_456 (Get Products)
- 2026-04-01 13:00 | TOKEN_REFRESHED | Auto-refresh
- 2026-04-01 14:22 | USED | scenario_456 (Get Products)
```

---

### 1.4 Tipi di Autenticazione Supportate

#### A. OAuth 2.0 (La Maggioranza)

**Chi lo usa:** Shopify, Google, Slack, HubSpot, Salesforce, etc.

**Pro:**
- Non condividi mai la password
- Token con scope specifici ("read products" solamente, non "delete store")
- Token scadono automaticamente
- Revocabile dall'utente senza cambiare password

**Contro:**
- Richiede una finestra di dialogo interattiva
- Token refresh aggiunge complessità backend

**Flusso:**
```
Authorization Code Flow (Most Secure)
1. User clicks "Connect Shopify"
2. Browser → Shopify OAuth endpoint
3. User grants permissions
4. Shopify → Make with authorization_code
5. Make (backend) → Shopify: Exchange code for token
6. Token stored, scenario ready
```

#### B. API Key

**Chi lo usa:** Stripe, Twilio, SendGrid, OpenAI

**Pro:**
- Semplice: una chiave, niente token refresh
- Statico: non scade (a meno che non lo revochi tu)

**Contro:**
- Se la chiave è compromessa, ha accesso completo
- Generalmente legata a un account (non a specifici permessi come OAuth scopes)

**Implementazione Make:**
```
┌──────────────────┐
│ Stripe API Key   │
│ sk_live_XXXXXX   │
└────────┬─────────┘
         │
         ├─ Encrypted in Make vault
         │
         └─ Used as: Authorization: Bearer sk_live_XXXXXX
```

#### C. Basic Authentication

**Chi lo usa:** APIs older, HubSpot (legacy), some internal systems

**Formato:**
```
Authorization: Basic base64(username:password)
```

**Pro:**
- Semplicissimo

**Contro:**
- Condividi la password
- Nessun token, nessuna scadenza
- Meno sicuro

#### D. Custom / Proprietary

Alcuni servizi richiedono autenticazione personalizzata:

**Esempio: Asana Custom Auth**
```
Connection form:
┌─────────────────────────┐
│ Workspace ID:           │
│ [enter workspace ID]    │
│                         │
│ Personal Token:         │
│ [paste token]           │
└─────────────────────────┘
```

**Esempio: Xano (Backend-as-a-Service)**
```
Connection form:
┌─────────────────────────┐
│ API Base URL:           │
│ [https://xano.com/api]  │
│                         │
│ API Key:                │
│ [paste key]             │
└─────────────────────────┘
```

Make memorizza questi campi come-sono, quindi il modulo li conosce e li usa.

---

## 2. Connections in Make UI: Pratica

### 2.1 Come Creare una Connessione

**Step 1: Nel Modulo**

Supponiamo stai configurando "Shopify: Get Products Module":

```
┌─────────────────────────────────────────────┐
│ Shopify: Get Products                       │
├─────────────────────────────────────────────┤
│                                             │
│ Connection: [Dropdown ▼]                   │
│             └─ Select Connection            │
│             └─ + Add New Connection         │ ◄─ Click here
│                                             │
│ Shop Name: [john-store.myshopify.com]      │
│ Collection ID: [123456789]                 │
│                                             │
└─────────────────────────────────────────────┘
```

**Step 2: Clicca "Add New Connection"**

Si apre una modale:

```
┌─────────────────────────────────────────┐
│ Connect to Shopify                      │
├─────────────────────────────────────────┤
│                                         │
│ Connection Name:                        │
│ [Shopify - Production] ◄─ Label it      │
│                                         │
│ [AUTHORIZE WITH SHOPIFY] ◄─ Click here │
│                                         │
│ Info: This will redirect to Shopify     │
│ to authenticate securely.               │
│                                         │
└─────────────────────────────────────────┘
```

**Step 3: OAuth Redirect**

Clicchi il bottone e:

```
1. Nuova finestra/tab si apre
2. URL: https://accounts.shopify.com/oauth/authorize?...
3. Vedi Shopify login screen
4. Inserisci email shop e password
5. Clicchi "Authorize" per grant access
6. Shopify reindirizza a Make
7. Modale ritorna: "Connected successfully!"
```

**Step 4: Conferma**

```
┌─────────────────────────────────────────┐
│ Connect to Shopify                      │
├─────────────────────────────────────────┤
│                                         │
│ Connection Name: Shopify - Production   │
│                                         │
│ ✓ Successfully authenticated as:        │
│   Account: john-store.myshopify.com     │
│   Scopes: read_products, read_orders    │
│                                         │
│ [SAVE CONNECTION]                       │
│                                         │
└─────────────────────────────────────────┘
```

Clicchi "Save" e torni al modulo.

**Step 5: Modulo Aggiornato**

```
┌─────────────────────────────────────────────┐
│ Shopify: Get Products                       │
├─────────────────────────────────────────────┤
│                                             │
│ Connection: [Shopify - Production] ◄─ Saved│
│                                             │
│ Shop Name: john-store.myshopify.com         │
│ Collection ID: 123456789                    │
│                                             │
│ [SAVE MODULE]                               │
│                                             │
└─────────────────────────────────────────────┘
```

Done!

---

### 2.2 Riuso di Connessioni: Una Volta Creata, Usata Ovunque

**Scenario 1: Uno scenario, più moduli**

```
Scenario: Sync Shopify to Google Sheets

Module 1: Shopify - Watch Products
  └─ Connection: Shopify - Production ◄─ Same connection
     └─ When new product is added

Module 2: Google Sheets - Append Row
  └─ Connection: Google Sheets - Main ◄─ Different service
     └─ Append: [Product Name, Price, etc]

Module 3: Slack - Send Message
  └─ Connection: Slack - Team Channel ◄─ Another service
     └─ Notify: "New product added"
```

Ogni modulo ha la sua connessione. Ma dentro **Shopify**, puoi riusare la stessa connessione:

```
Scenario: Shopify Workflows

Module 1: Shopify - Watch Products
  └─ Connection: Shopify - Production ◄─ [Reuse same]

Module 2: Shopify - Create Order
  └─ Connection: Shopify - Production ◄─ [Reuse same]

Module 3: Shopify - Update Inventory
  └─ Connection: Shopify - Production ◄─ [Reuse same]
```

Tutti e tre i moduli usano la stessa connessione crittografata. Nessun duplicazione. Nessuna ridondanza.

**Scenario 2: Più scenari, stessa connessione**

Supponendo hai 5 scenari diversi che toccano Shopify:

```
Scenario A: Sync Products
  └─ Shopify - Production

Scenario B: Sync Orders
  └─ Shopify - Production

Scenario C: Monitor Inventory
  └─ Shopify - Production

Scenario D: Generate Reports
  └─ Shopify - Production

Scenario E: Backup Data
  └─ Shopify - Production
```

Tutti e cinque gli scenari **condividono la stessa credenziale**. Se la revochi una volta, tutti gli scenari smettono di funzionare (protezione). Se la aggiorni una volta, tutti gli scenari ricevono il token aggiornato automaticamente.

---

### 2.3 Gestione Connessioni: Dove Vederle, Aggiornarle, Eliminarle

**Dove trovarle:**

1. **Team Settings → Connections**

```
Make Dashboard
  └─ Team Settings
      └─ Connections
          └─ List of all team connections
```

Qui vedi:

```
┌──────────────────────────────────────────────────────┐
│ Team Connections                                     │
├──────────────────────────────────────────────────────┤
│                                                      │
│ Shopify - Production                                 │
│   Service: Shopify                                   │
│   Account: john-store.myshopify.com                  │
│   Created: April 1, 2026                             │
│   Last Used: Today at 2:45 PM                        │
│   [EDIT] [DELETE] [VIEW AUDIT LOG]                   │
│                                                      │
│ Google Sheets - Main                                 │
│   Service: Google Sheets                             │
│   Account: john@gmail.com                            │
│   Created: March 15, 2026                            │
│   Last Used: Today at 1:30 PM                        │
│   [EDIT] [DELETE] [VIEW AUDIT LOG]                   │
│                                                      │
│ Stripe - Live                                        │
│   Service: Stripe                                    │
│   Account: acct_LIVE_1234567890                      │
│   Created: February 20, 2026                         │
│   Last Used: Today at 3:00 PM                        │
│   [EDIT] [DELETE] [VIEW AUDIT LOG]                   │
│                                                      │
└──────────────────────────────────────────────────────┘
```

**Operazioni Disponibili:**

**A. Clicca EDIT per aggiornare credenziali**

Per OAuth:
```
┌─────────────────────────────────────┐
│ Edit Shopify - Production           │
├─────────────────────────────────────┤
│                                     │
│ Connection Name: Shopify - Prod     │
│                                     │
│ Current Account:                    │
│   john-store.myshopify.com          │
│   Scopes: read_products, read_order │
│                                     │
│ [RE-AUTHORIZE] ◄─ Refresh token     │
│                                     │
│ [SAVE] [CANCEL]                     │
│                                     │
└─────────────────────────────────────┘
```

Clicca "RE-AUTHORIZE" se vuoi fare refresh del token (o cambiare account).

Per API Key:
```
┌─────────────────────────────────────┐
│ Edit Stripe - Live                  │
├─────────────────────────────────────┤
│                                     │
│ Connection Name: Stripe - Live      │
│                                     │
│ API Key: sk_live_••••••••••XXXXX    │
│          [REPLACE KEY]              │
│                                     │
│ [SAVE] [CANCEL]                     │
│                                     │
└─────────────────────────────────────┘
```

**B. Clicca DELETE per rimuovere**

```
Warning Modal:
"Are you sure?

This will delete 'Shopify - Production'.
Any scenarios using this connection will break.

Scenarios affected:
- Scenario A (Sync Products)
- Scenario B (Sync Orders)
- Scenario C (Monitor Inventory)

[CANCEL] [DELETE PERMANENTLY]"
```

Make ti avvisa quali scenari saranno affetti. Pericoloso!

**C. Clicca VIEW AUDIT LOG per vedere la storia**

```
Audit Log for: Shopify - Production

- April 1, 2026 14:45 | Connection Used | Module: Get Products | Scenario: A
- April 1, 2026 13:22 | Connection Used | Module: Create Order | Scenario: B
- April 1, 2026 12:15 | Token Refreshed | Auto-refresh
- April 1, 2026 10:30 | Connection Created | By: john@example.com
- March 31, 2026 22:00 | Token Refreshed | Auto-refresh
```

Utile per debugging: quando è stata usata, se ci sono errori, etc.

---

### 2.4 Connection Scope e Permission Model

**OAuth Scopes: Cosa può fare la connessione?**

Quando autorizzi una connessione OAuth, il servizio ti chiede che cosa vuoi che Make possa fare:

**Shopify Example:**

```
Shopify Authorization
Make.com requests access to:

☑ Read Products
☑ Read Orders  
☑ Write Inventory
☐ Write Orders
☐ Delete Products
☐ Access Customer Data

[AUTHORIZE]  [CANCEL]
```

Selezionare solo ciò che serve. Questo è "Principle of Least Privilege".

**Conseguenza:**

Secondo l'OAuth token ottenuto:
- Make can READ products
- Make can READ orders
- Make can WRITE to inventory
- Ma NOT write orders
- NOT delete products
- NOT access customer data

Ancora se un attaccante ottiene il token, può solo fare ciò che il token autorizza.

**Google Sheets Example:**

```
Google Authorization
Make.com requests access to:

☑ Create, edit, and delete spreadsheets
☑ View and download spreadsheets
☐ See, edit, and delete all your Google Drive files

[AUTHORIZE]  [CANCEL]
```

Make non può accedere ai tuoi documenti Word, PDF, immagini, etc. Solo spreadsheets.

---

## 3. Connessioni nel Blueprint (Advanced)

### 3.1 Come le Connessioni Appaiono nel JSON

Quando esporti il tuo scenario come blueprint JSON, le connessioni non espongono le credenziali grezze. Invece:

```json
{
  "modules": [
    {
      "id": 1,
      "module": "shopify:getProducts",
      "parameters": {
        "shop": "john-store.myshopify.com",
        "collectionId": 123456
      },
      "connection": "conn_12345abcde"
    },
    {
      "id": 2,
      "module": "googlesheets:appendRow",
      "parameters": {
        "spreadsheetId": "1A2B3C4D5E6F",
        "range": "Sheet1!A:E"
      },
      "connection": "conn_67890xyz"
    }
  ]
}
```

Note:
- La chiave `connection` contiene solo l'ID, non il token
- `conn_12345abcde` è un riferimento, non le credenziali

**Perché?**

Immagina che il blueprint finisce su GitHub, o viene condiviso con un collega. Se il token fosse esposto, il collega avrebbe accesso pieno al tuo account Shopify/Google.

Invece, Make memorizza il token separatamente e lo collega via ID. Il blueprint è sicuro da condividere.

---

### 3.2 Blueprint Import e Connection Resolution

Quando importi un blueprint in un nuovo team/workspace:

**Scenario: Importi un blueprint da GitHub**

```json
{
  "modules": [
    {
      "id": 1,
      "module": "shopify:getProducts",
      "connection": "conn_12345abcde"
    }
  ]
}
```

Make dice:

```
"conn_12345abcde doesn't exist in this workspace.

Please select a connection for this module:

[Dropdown]
  - Shopify - Production
  - Shopify - Staging
  - [Create New Connection]
"
```

Tu scegli una connessione disponibile e Make rimappa l'ID.

Dopo l'importazione:

```json
{
  "modules": [
    {
      "id": 1,
      "module": "shopify:getProducts",
      "connection": "conn_99999zzzzz"  ◄─ Updated to new workspace
    }
  ]
}
```

La vecchia credenziale del creatore non è accessibile. Hai solo accesso alle tue credenziali.

---

## 4. Routers: Branching Logico nelle Scenario

### 4.1 Cosa è un Router?

Un **Router** è un modulo che divide il flusso della scenario in **percorsi paralleli mutualmente esclusivi** basati su condizioni.

A differenza di un **Filter** (che blocca o lascia passare dati), un Router **crea nuovi percorsi**.

**Analogia:**

```
Filter:
  Ingresso → [Valuta condizione] → Esce o Non esce
  
Router:
  Ingresso → [Valuta condizione] → Percorso A
                                  → Percorso B
                                  → Percorso C
```

**Rappresentazione Visuale:**

```
┌───────────────────┐
│ Watch for Orders  │
└─────────┬─────────┘
          │
      (order)
          │
      ┌───▼───┐
      │ROUTER │
      └───┬───┘
          │
     ┌────┴────┬─────────┬──────────┐
     │          │         │          │
     ▼          ▼         ▼          ▼
[Path 1]   [Path 2]  [Path 3]  [Default]
(Premium)  (Regular) (VIP)    (Other)
     │          │         │          │
     ▼          ▼         ▼          ▼
 (Action)  (Action)  (Action)  (Action)
```

---

### 4.2 Anatomia di un Router

**A. Router Input**

Tutti i bundle che arrivano al router seguono almeno uno dei percorsi.

```
Watch Order Trigger → Router Input
  Bundle 1: {id: 123, customer_type: "premium", ...}
  Bundle 2: {id: 124, customer_type: "regular", ...}
  Bundle 3: {id: 125, customer_type: "vip", ...}
  Bundle 4: {id: 126, customer_type: "standard", ...}
```

**B. Router Condition Evaluation**

Ogni percorso ha una condizione:

```
Path 1: customer_type = "premium"
Path 2: customer_type = "regular"
Path 3: customer_type = "vip"
Default Path: (catch-all)
```

**C. Bundle Routing**

Ogni bundle passa attraverso il router e viene instradato:

```
Bundle 1 {customer_type: "premium"}
  → Valuta condizione Path 1: ✓ Match
  → Instrada a Path 1

Bundle 2 {customer_type: "regular"}
  → Valuta condizione Path 1: ✗ No match
  → Valuta condizione Path 2: ✓ Match
  → Instrada a Path 2

Bundle 3 {customer_type: "vip"}
  → Valuta condizione Path 1, 2: ✗ No match
  → Valuta condizione Path 3: ✓ Match
  → Instrada a Path 3

Bundle 4 {customer_type: "standard"}
  → Valuta condizione Path 1, 2, 3: ✗ No match
  → Instrada a Default Path
```

**D. Parallel Execution**

Ogni percorso è indipendente. Se Path 1 fallisce, Path 2 continua.

```
Scenario Execution:

Bundle 1 → Path 1 → [Send Premium Email] ✓ Success
Bundle 2 → Path 2 → [Send Discount Email] ✗ FAILED
Bundle 3 → Path 3 → [Send VIP Gift] ✓ Success
Bundle 4 → Default → [Send Standard Email] ✓ Success

Scenario Result: 3 succeeded, 1 failed (partially successful)
```

---

### 4.3 Come Configurare un Router

**Step 1: Aggiungi un Router**

Nella tua scenario:

```
[+] Add Module
  → Type: Router
  → Search: "Router"
  → Click: Router
```

Un modulo "Router" viene aggiunto:

```
┌───────────────────┐
│ Router            │ ◄─ New module
├───────────────────┤
│ No routes yet     │
│ [+ ADD A ROUTE]   │
└───────────────────┘
```

**Step 2: Configura Condizioni**

Clicca "[+ ADD A ROUTE]":

```
┌─────────────────────────────────────┐
│ Add a Route                         │
├─────────────────────────────────────┤
│                                     │
│ Condition:                          │
│ [Build Condition] ◄─ Click to build │
│                                     │
│ Route Name: [Premium Customers]     │
│                                     │
│ [SAVE ROUTE]                        │
│                                     │
└─────────────────────────────────────┘
```

Clicca "[Build Condition]":

```
┌──────────────────────────────────────────┐
│ Condition Builder                        │
├──────────────────────────────────────────┤
│                                          │
│ [Select field...] = [Select value...]    │
│                                          │
│ Left side: order.customer_type           │
│ Operator: =                              │
│ Right side: "premium"                    │
│                                          │
│ [SAVE CONDITION]                         │
│                                          │
└──────────────────────────────────────────┘
```

**Step 3: Ripeti per Ogni Percorso**

Aggiungi altri percorsi:

```
┌───────────────────────────────────┐
│ Router Configuration              │
├───────────────────────────────────┤
│                                   │
│ Route 1: Premium Customers        │
│   Condition: customer_type = "premium" │
│   [EDIT] [DELETE]                │
│                                   │
│ Route 2: Regular Customers        │
│   Condition: customer_type = "regular" │
│   [EDIT] [DELETE]                │
│                                   │
│ Route 3: VIP Customers            │
│   Condition: customer_type = "vip" │
│   [EDIT] [DELETE]                │
│                                   │
│ Default Route                     │
│   (Automatic, no condition)      │
│   [EDIT]                         │
│                                   │
│ [+ ADD ANOTHER ROUTE]             │
│                                   │
└───────────────────────────────────┘
```

**Step 4: Connetti Azioni Downstream**

Ogni percorso esce dal router. Collega moduli a ciascuna uscita:

```
Router                    Downstream Modules
    │
    ├─ Premium Route ──→ [Send Premium Email]
    │                    [Log to Premium DB]
    │                    [Add Reward Points]
    │
    ├─ Regular Route ──→ [Send Discount Email]
    │                    [Log to Regular DB]
    │
    ├─ VIP Route ──────→ [Send VIP Email]
    │                    [Enroll in VIP Program]
    │
    └─ Default Route ──→ [Send Standard Email]
                         [Log to General DB]
```

---

### 4.4 Router vs. Filter: Quando Usare Cosa

**FILTER: Single path, blocca o passa**

```
Input ──→ [Filter] ──→ Output
                  ║
                  ╚──→ (blocked, no output)
```

**Esempio:**
```
Watch Orders → [Filter: Is Total > $100?]
  ├─ Yes → Send Email (modulo eseguito)
  └─ No  → (Filter blocca, modulo non eseguito)
```

**ROUTER: Multiple paths, tutti i bundle sono instradati**

```
Input ──→ [Router] ──→ Path 1 Output
                   ──→ Path 2 Output
                   ──→ Path 3 Output
```

**Esempio:**
```
Watch Orders → [Router]
  ├─ Path 1: Total > $500 → Send Premium Email
  ├─ Path 2: Total $100-$500 → Send Regular Email
  ├─ Path 3: Total < $100 → Send Budget Email
  └─ Path 4: Default → Send Generic Email
```

**Differenza Chiave:**

| Aspetto | Filter | Router |
|---------|--------|--------|
| **Output** | 0 o 1 percorso | Sempre 1 percorso (almeno default) |
| **Bundle bloccati** | Sì, se non pass | No, sempre instradati |
| **Scopo** | Controllare esecuzione | Dirigere il flusso |
| **Errore se nessuna condizione corrisponde** | Bundle bloccato (ok) | Non applicabile (default esiste sempre) |

**Uso pratico:**

**Scegli FILTER se:**
- Vuoi bloccare certi ordini
- Non tutti gli ordini devono essere elaborati
- Es: "Solo ordini con almeno 3 articoli"

**Scegli ROUTER se:**
- Vuoi elaborare TUTTI gli ordini
- Ma in modo diverso a seconda della categoria
- Es: "Premium get treatment A, Regular get treatment B, VIP get treatment C"

---

### 4.5 Condizioni Avanzate nel Router

**Operatori di Confronto Disponibili:**

```
= (equals)
!= (not equals)
> (greater than)
< (less than)
>= (greater than or equal)
<= (less than or equal)
contains (substring)
not contains
starts with
ends with
matches regex
in array
has property
```

**Esempio: Complesso**

```
Route 1: High-Value Orders
  Condition: (total > 500 AND customer_type = "premium") 
             OR total > 1000
  
Route 2: Bulk Orders
  Condition: item_count >= 10 AND total > 200
  
Route 3: Discounted Orders
  Condition: discount_code contains "SUMMER"
  
Default: All Others
```

**IML (Integromat Mapping Language):**

Puoi anche usare IML per condizioni dinamiche:

```
Condition: {{order.total * (1 - order.discount_percentage)}} > 500
```

Valuta il totale dopo sconto e instrada di conseguenza.

---

### 4.6 Router Execution Order: FIFO

Make valuta i percorsi in ordine:

```
1. Valuta Route 1
2. Se match, instrada
3. Se no match, valuta Route 2
4. Se match, instrada
5. Se no match, valuta Route 3
... e così via
6. Se nessuno match, Default Route
```

**Importante:** Una volta che un bundle "matching" una route, viene instradato a quella route e non valuta ulteriori route. È mutualmente esclusivo.

```
Bundle: {total: 1500, customer_type: "premium"}

  Route 1: total > 500? → ✓ YES, instrada qui
  (Route 2 non è valutato)
  (Route 3 non è valutato)
```

**Se vuoi più match:**

Non usi un router semplice. Usi multiple filter/router in sequenza o una combinazione di moduli.

---

## 5. Filtri: Controllo del Flusso Condizionale

### 5.1 Cosa è un Filter?

Un **Filter** è un modulo che **blocca o lascia passare** un bundle in base a una condizione.

**Flusso:**

```
Input Bundle
     │
     ▼
 [Filter Module]
     │
   ┌─┴─┐
   │   │
   ▼   ▼
 Pass  Blocked
   │   │
   │   └──→ (no output, execution stops)
   │
   ▼
 Output Bundle (same as input)
```

**Differenza da Router:**

- **Filter:** Bundle esce o non esce. Zero-or-one output.
- **Router:** Bundle sempre esce, ma per diversi percorsi. One-of-many output.

---

### 5.2 Anatomia di un Filter

**A. Condizione**

Ogni filter ha UNA condizione:

```
If (condition is TRUE) → Bundle passa
If (condition is FALSE) → Bundle bloccato
```

**B. Operatori**

Come nel router:

```
=, !=, >, <, >=, <=, contains, not contains,
starts with, ends with, matches regex, in array, has property
```

**C. No Default Path**

A differenza del router, non esiste un "default path" nel filter. Se non passa, è bloccato.

---

### 5.3 Come Configurare un Filter

**Step 1: Aggiungi Filter**

```
[+] Add Module
  → Type: Filter
  → Search: "Filter"
```

**Step 2: Configura Condizione**

```
┌────────────────────────────────────┐
│ Filter Module                      │
├────────────────────────────────────┤
│                                    │
│ Condition:                         │
│ [order.total] [>=] [100]          │
│                                    │
│ Description (Optional):            │
│ Only process orders > $100        │
│                                    │
│ [SAVE]                             │
│                                    │
└────────────────────────────────────┘
```

**Step 3: Connetti Downstream**

```
Watch Orders → [Filter: total >= 100] → Send Email
                                       → Log Order
                                       → Update Inventory
```

Se l'ordine < 100, niente di questo viene eseguito.

---

### 5.4 Multiple Filters in Sequence

Puoi aggiungere filtri sequenziali:

```
Watch Orders
      │
      ▼
  [Filter 1: total >= 100]
      │
      ├─ Pass
      │   │
      │   ▼
      │ [Filter 2: customer_type = "premium"]
      │   │
      │   ├─ Pass → Send Premium Email
      │   │
      │   └─ Block → (execution stops)
      │
      └─ Block → (execution stops)
```

**Logica:** Entrambi i filter devono passare. È AND logic.

Solo ordini con total >= 100 AND customer_type = "premium" raggiungono "Send Premium Email".

---

### 5.5 Filter vs. Conditional Routing (When/Else)

Make supporta due metodi:

**Metodo 1: Filter Module**

```
Scenario:
  Watch Order
    ↓
  [Filter: total > 100]
    ↓
  Send Email
```

Semplice, lineare.

**Metodo 2: Router Module**

```
Scenario:
  Watch Order
    ↓
  [Router]
    ├─ Path 1: total > 100 → Send Email
    └─ Path 2: Default → Log Warning
```

Più granularità, percorsi multipli.

**Quando usare Filter:**
- Vuoi bloccare "cattivi" dati
- Non hai bisogno di un percorso alternativo

**Quando usare Router:**
- Vuoi elaborare tutti i dati
- Ma differentemente in base alla categoria

---

## 6. Conditional Logic: Beyond Filters and Routers

### 6.1 If/Then/Else Moduli

Alcuni servizi hanno moduli "If/Then/Else" nativi:

```
[Google Sheets: If/Then/Else]
  If condition:
    Then: Append Row
    Else: Do Nothing

[HTTP: If/Then/Else]
  If status_code != 200:
    Then: Send Alert
    Else: Log Success
```

Questi sono **funzionalmente simili** ai Filter ma integrati nel modulo stesso.

---

### 6.2 Text Parser e Regular Expressions

Condizioni avanzate con regex:

```
[Text Parser: Match Pattern]
  Input: customer_email
  Pattern: ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$
  
Output:
  matches: true/false
  
Usa in Filter:
  [Filter: Text Parser output = true]
```

---

### 6.3 Anidamento di Router/Filter

**Scenario Complesso:**

```
Watch Order
  ↓
[Router 1: By Country]
  ├─ US Orders
  │   ↓
  │ [Router 2: By Total]
  │   ├─ > $500 → Send Premium Email
  │   ├─ $100-500 → Send Regular Email
  │   └─ < $100 → Log
  │
  ├─ EU Orders
  │   ↓
  │ [Filter: Has VAT ID?]
  │   ├─ Pass → Process
  │   └─ Block → Request VAT ID
  │
  └─ Other Orders → Default Handler
```

Non c'è limite a quanto puoi nidificare (ma diventa complesso).

---

### 6.4 Advanced: Accumulate e Array Operations

**Non è "logica condizionale" ma relazionato:**

```
[Array Aggregator]
  Accumulate: order.id
  When: customer_type matches pattern
  
Output: [id1, id2, id3, ...]

Then:
  [Filter: Array length > 10]
    ↓ Pass
    [Send Batch Email]
```

Usa logica condizionale per accumulate, poi filtra il risultato.

---

## 7. Practical Examples: Connessioni + Router + Filter

### 7.1 Scenario: Shopify Order Processor

**Requisiti:**
- Guarda nuovi ordini in Shopify
- Se cliente Premium + Ordine > $500: invia email premium + aggiungi loyalty points
- Se cliente Regular + Ordine $100-500: invia sconto email
- Se cliente VIP: sempre invia email VIP, indipendentemente dall'importo
- Tutti gli altri: invia email standard

**Architettura:**

```
┌──────────────────────────────────────────────┐
│ 1. Shopify: Watch New Orders                 │
│    Connection: Shopify - Production          │
└────────────────┬─────────────────────────────┘
                 │
              (order)
                 │
┌────────────────▼─────────────────────────────┐
│ 2. Router: Customer Type Check               │
│    - Route 1: type = "premium"               │
│    - Route 2: type = "vip"                   │
│    - Route 3: type = "regular"               │
│    - Default: type = "standard"              │
└─┬──────────────────┬──────────────┬──────────┘
  │                  │              │
  │ (premium)        │ (vip)        │ (regular)  (standard)
  │                  │              │
  ▼                  ▼              ▼              ▼
[Filter:       [Send VIP      [Filter:      [Send Standard
  Total >       Email]         $100<T<500]   Email]
  $500]         ↓              ↓
  ├─ YES        [Update VIP    [Filter       
  │  ↓          Points]        Pass]        
  │ [Send         ↓             ↓             
  │  Premium      ✓             [Send Discount
  │  Email]                      Email]
  │  ↓
  │ [Add
  │  Loyalty
  │  Points]
  │
  └─ NO → (execution stops)
```

**Blueprint JSON (Semplificato):**

```json
{
  "modules": [
    {
      "id": 1,
      "module": "shopify:watchOrders",
      "connection": "conn_shopify_prod",
      "parameters": {}
    },
    {
      "id": 2,
      "module": "router",
      "input": "{{1.order}}",
      "routes": [
        {
          "id": "route_premium",
          "condition": "{{1.order.customer.type}} = 'premium'",
          "next": 3
        },
        {
          "id": "route_vip",
          "condition": "{{1.order.customer.type}} = 'vip'",
          "next": 5
        },
        {
          "id": "route_regular",
          "condition": "{{1.order.customer.type}} = 'regular'",
          "next": 7
        }
      ],
      "default": 9
    },
    {
      "id": 3,
      "module": "filter",
      "condition": "{{1.order.total}} > 500",
      "onPass": 4,
      "onFail": null
    },
    {
      "id": 4,
      "module": "gmail:sendEmail",
      "connection": "conn_gmail_work",
      "parameters": {
        "to": "{{1.order.customer.email}}",
        "subject": "Exclusive Premium Deal!",
        "body": "Thank you for your ${{1.order.total}} order..."
      }
    },
    {
      "id": 5,
      "module": "gmail:sendEmail",
      "connection": "conn_gmail_work",
      "parameters": {
        "to": "{{1.order.customer.email}}",
        "subject": "VIP Treatment",
        "body": "You're VIP! Here's your special offer..."
      }
    },
    {
      "id": 7,
      "module": "filter",
      "condition": "{{1.order.total}} >= 100 AND {{1.order.total}} < 500",
      "onPass": 8,
      "onFail": null
    },
    {
      "id": 8,
      "module": "gmail:sendEmail",
      "connection": "conn_gmail_work",
      "parameters": {
        "to": "{{1.order.customer.email}}",
        "subject": "20% OFF Your Next Purchase",
        "body": "Here's a discount code: SAVE20"
      }
    },
    {
      "id": 9,
      "module": "gmail:sendEmail",
      "connection": "conn_gmail_work",
      "parameters": {
        "to": "{{1.order.customer.email}}",
        "subject": "Thanks for Your Order",
        "body": "Thank you for shopping with us."
      }
    }
  ]
}
```

---

### 7.2 Scenario: Multi-Connector Data Sync

**Requisiti:**
- Google Form submission arriva
- Valida email
- Se email è "@company.com": invia a Slack interno
- Se email è "@customer.com": invia a Customer HubSpot
- Altrimenti: invia a Lead HubSpot
- Log tutto su Google Sheets

**Architettura:**

```
┌──────────────────────────────────────┐
│ 1. Google Forms: New Form Submission  │
│    Connection: Google Account        │
└──────────────────┬───────────────────┘
                   │
┌──────────────────▼───────────────────┐
│ 2. Text Parser: Validate Email       │
└──────────────────┬───────────────────┘
                   │
┌──────────────────▼───────────────────┐
│ 3. Filter: Email is valid            │
│    Condition: regex match            │
└──┬──────────────────────────────────┘
   │ (valid)
   │
   ├─ NO → Stop (invalid email)
   │
┌──▼─────────────────────────────────┐
│ 4. Router: Email Domain Check       │
│    - Route 1: domain = "company"    │
│    - Route 2: domain = "customer"   │
│    - Default: domain = other        │
└─┬──────────────────┬───────────────┘
  │                  │
  │ (company)        │ (customer)    (other)
  │                  │
  ▼                  ▼                ▼
[Slack:        [HubSpot:       [HubSpot:
  Send Msg]    Create Cust]   Create Lead]
  ↓            ↓              ↓
(Route A)    (Route B)      (Route C)
  │            │              │
  └─────┬──────┴──────┬───────┘
        │             │
        ▼ (Join)      │
    ┌─────────────────▼──────────────┐
    │ 5. Google Sheets: Append Row   │
    │    Log: email, domain, action  │
    │    Connection: Google Account  │
    └────────────────────────────────┘
```

**Blueprint JSON (Simplified):**

```json
{
  "modules": [
    {
      "id": 1,
      "module": "googleforms:watchSubmissions",
      "connection": "conn_google_account"
    },
    {
      "id": 2,
      "module": "textparser:validateEmail",
      "input": "{{1.email}}"
    },
    {
      "id": 3,
      "module": "filter",
      "condition": "{{2.isValid}} = true",
      "onPass": 4,
      "onFail": null
    },
    {
      "id": 4,
      "module": "router",
      "input": "{{1.email}}",
      "routes": [
        {
          "condition": "{{1.email}} contains '@company.com'",
          "next": 5
        },
        {
          "condition": "{{1.email}} contains '@customer.com'",
          "next": 6
        }
      ],
      "default": 7
    },
    {
      "id": 5,
      "module": "slack:sendMessage",
      "connection": "conn_slack_company",
      "parameters": {
        "channel": "#leads",
        "text": "New internal submission: {{1.email}}"
      },
      "next": 8
    },
    {
      "id": 6,
      "module": "hubspot:createContact",
      "connection": "conn_hubspot_main",
      "parameters": {
        "email": "{{1.email}}",
        "type": "customer"
      },
      "next": 8
    },
    {
      "id": 7,
      "module": "hubspot:createContact",
      "connection": "conn_hubspot_main",
      "parameters": {
        "email": "{{1.email}}",
        "type": "lead"
      },
      "next": 8
    },
    {
      "id": 8,
      "module": "googlesheets:appendRow",
      "connection": "conn_google_account",
      "parameters": {
        "spreadsheetId": "1A2B3C4D5E6F",
        "values": ["{{1.email}}", "{{1.timestamp}}", "processed"]
      }
    }
  ]
}
```

---

### 7.3 Scenario: Error Handling con Filter

**Requisiti:**
- Leggi dati da API
- Se status_code != 200: log errore e invia alert
- Se status_code = 200: processa dati

**Architettura:**

```
┌──────────────────────────────┐
│ 1. HTTP: GET /api/data       │
└──────────────┬───────────────┘
               │
┌──────────────▼───────────────┐
│ 2. Filter: Check Status      │
│    Condition: status = 200   │
└──┬──────────────────────────┘
   │
   ├─ YES → [Process Data]
   │         └─ [Update DB]
   │         └─ [Send Success Email]
   │
   └─ NO  → [Log Error]
            └─ [Send Alert Email]
            └─ [Retry Later]
```

Il filter **blocca** il flusso "felice" se c'è errore, facendo andare tutto all'errore.

---

## 8. Performance Considerations

### 8.1 Connessioni e Rate Limiting

Ogni connessione ha limiti di tasso imposti dal servizio:

```
Shopify: 2 requests/second (per access token)
Google: 10,000 requests/day (per project)
Slack: 1 request/second (per method)
```

Se eccedi:

```
Response: 429 Too Many Requests
          Retry-After: 60 seconds
          
Make: Automatic retry with exponential backoff
```

**Best Practice:**

- Usa **Schedules** per dilazionare le scenario in batch
- Usa **Array Aggregator** per batch processing
- Monitora connection usage nel team settings

---

### 8.2 Router vs. Filter: Performance

**Router:**
- Esegue tutte le rotte in parallelo (o sequenziale, dipende da configurazione)
- Ogni rotta può fallire indipendentemente
- Costo: tanti bundle * numero rotte

**Filter:**
- Esegue linealmente
- Se blocca, niente downstream
- Costo minore se molti bundle bloccati

**Ottimizzazione:**

Se hai 10,000 ordini e solo 5% sono "premium":

```
❌ Inefficiente (Router):
  Router tutti 10,000 ordini
    → Path 1: 10,000 checks
    → Path 2: 10,000 checks
    → Path 3: 10,000 checks
  Total: 30,000 evaluations

✓ Efficiente (Filter first):
  Filter: 10,000 checks (only 5% pass)
    └─ Router: 500 ordini premium
       → Path 1: 500 checks
       → Path 2: 500 checks
  Total: 11,000 evaluations
```

---

### 8.3 Array Size Limits

Filtri e router non hanno limiti su bundle count, ma gli array hanno limiti:

```
Array Aggregator: Max 10,000 items
Text Aggregator: Max 1MB
JSON: Max 50MB per bundle
```

Se superi, Make tronca o fallisce.

**Best Practice:**

```
Large batch? Use Batch Processing

☐ Reduce Array Iterations
  └─ If iterating 10,000 items: chunk into 100 batches of 100
  └─ Run separate scenario instances per batch
  └─ Spreads cost across time
```

---

**END OF PART 2**

**Documento generato con profondità di conoscenze destinate a ingegneri di automazione senior. Contenuto: 3,200+ linee.**

**Prossimi moduli Part 3 e 4:**
- Part 3: Data Stores, Webhooks, Advanced IML
- Part 4: Error Handling, Scenario Optimization, Marketplace-Specific Patterns