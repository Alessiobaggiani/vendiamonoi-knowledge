# Miro PRO — Documentazione Tecnica Completa

## Informazioni Generali

| Campo | Dettaglio |
|---|---|
| **Software** | Miro PRO |
| **Categoria** | Visual Collaboration / Whiteboard Digitale / Diagrammi / Project Management Visuale |
| **Sito ufficiale** | https://miro.com |
| **Reparto** | Tutti i reparti — Strategia, Operazioni, Catalogo, Marketplace, Fornitori |
| **Responsabile** | Alessio Baggiani |
| **Data documentazione** | 2026-03-31 |
| **Stato** | ✅ Attivo |
| **Piano** | PRO (Business disponibile per upgrade) |
| **Licenza** | Per utente/mese — Starter $8/utente/mese, Business $20/utente/mese |

---

## 1. Panoramica e Posizionamento Strategico

### Cos'è Miro

Miro è una piattaforma di visual collaboration che fornisce una lavagna digitale infinita (infinite canvas) progettata per team distribuiti. Fondata nel 2011 a Perm (Russia) come RealtimeBoard, è stata rinominata Miro nel 2019. Conta oltre 80 milioni di utenti registrati e 130.000+ clienti aziendali, inclusi il 99% delle aziende Fortune 100.

### Perché Miro per Vendiamonoi.it

Per un'azienda di distribuzione digitale multi-marketplace come Vendiamonoi.it, Miro è strategico per:

1. **Mappatura processi aziendali** — Visualizzare e ottimizzare i flussi operativi di tutti i 6 reparti (Fornitori, Catalogo, Marketplace, Evasione Ordini, Customer Service, Amministrazione)
2. **Architettura di sistema** — Diagrammare l'infrastruttura tecnologica (Channable → Marketplace, Make → Automazioni, Supabase → Database)
3. **Onboarding fornitori** — Creare visual workflow per il processo di onboarding dei 100+ fornitori
4. **Strategia marketplace** — Mappare la presenza su 20-30 marketplace europei con analisi visuale
5. **Workshop strategici** — Sessioni di brainstorming e pianificazione con team e collaboratori esterni
6. **Customer Journey Mapping** — Tracciare il percorso dell'ordine dal marketplace al cliente finale

### Posizionamento nel Tech Stack Vendiamonoi

```
LIVELLO STRATEGICO / VISUAL THINKING
┌─────────────────────────────────────────────┐
│  MIRO PRO                                    │
│  • Process mapping                           │
│  • System architecture diagrams              │
│  • Brainstorming & strategy sessions         │
│  • Flowcharts operativi                      │
│  • Onboarding visuale fornitori              │
└──────────────────┬──────────────────────────┘
                   │ Alimenta
                   ▼
┌─────────────────────────────────────────────┐
│  NOTION — Knowledge Base & Documentation     │
│  CLICKUP — Task Management & Execution       │
│  MAKE — Automation Engine                    │
│  SUPABASE — Database & Backend               │
└─────────────────────────────────────────────┘
```

---

## 2. Piani e Pricing Dettagliato

### Confronto Piani (Aggiornato 2025-2026)

| Feature | Free | Starter ($8/u/m) | Business ($20/u/m) | Enterprise (Custom) |
|---|---|---|---|---|
| Board | 3 board | Illimitati | Illimitati | Illimitati |
| Membri team | Illimitati (viewer) | Illimitati | Illimitati | Illimitati |
| Template | Base | 2500+ | 2500+ | 2500+ + Custom |
| Integrazioni | Base | Tutte | Tutte + SSO | Tutte + SCIM |
| Video chat | ❌ | ✅ | ✅ | ✅ |
| Voting | ❌ | ✅ | ✅ | ✅ |
| Timer | ❌ | ✅ | ✅ | ✅ |
| Custom templates | ❌ | ❌ | ✅ | ✅ |
| SSO (SAML) | ❌ | ❌ | ✅ | ✅ |
| Audit logs | ❌ | ❌ | ❌ | ✅ |
| Data governance | ❌ | ❌ | ❌ | ✅ |
| AI Credits | Limitati | 2000/mese | 2000/mese | Custom |
| Support | Community | Email | Priority | Dedicated CSM |

### Raccomandazione per Vendiamonoi

**Piano attuale: Starter/PRO** — Sufficiente per le esigenze attuali (board illimitati, integrazioni, video chat). Valutare upgrade a Business quando:
- Servono template custom per processi standardizzati
- Team cresce oltre 10 persone con necessità SSO
- Necessità di Smart Diagramming avanzato

---

## 3. Funzionalità Core

### 3.1 Infinite Canvas (Lavagna Infinita)

La funzionalità principale di Miro. Una superficie di lavoro illimitata dove posizionare qualsiasi tipo di contenuto.

**Caratteristiche tecniche:**
- Zoom da 2% a 400%
- Pan infinito in tutte le direzioni
- Grid snap opzionale per allineamento
- Minimap per navigazione rapida
- Frames per organizzare sezioni logiche della board

**Uso Vendiamonoi:**
- Creare "zone" per ogni reparto su una board master
- Frame separati per: Processo Ordini, Flusso Catalogo, Architettura Tech, Mappa Marketplace
- Zoom semantico: vista macro (strategia) → vista micro (dettagli operativi)

### 3.2 Sticky Notes e Testo

**Sticky Notes:**
- Colori personalizzabili (16+ colori)
- Dimensioni variabili (S, M, L, XL)
- Tag e categorizzazione
- Bulk creation (copia-incolla lista → sticky note multiple)
- Clustering automatico con AI

**Testo:**
- Rich text con formattazione completa
- Heading levels (H1-H3)
- Liste puntate e numerate
- Link ipertestuali
- Code blocks

**Uso Vendiamonoi:**
- Sticky notes per brainstorming nuovi marketplace da attivare
- Categorizzazione per colore: verde = attivo, giallo = in valutazione, rosso = problematico
- Note rapide durante workshop strategici con fornitori

### 3.3 Shapes e Connettori

**Shapes disponibili:**
- Rettangoli, cerchi, diamanti, triangoli, parallelogrammi
- Shape personalizzate (SVG import)
- Container/group per raggruppare elementi
- Swimlanes per diagrammi di processo

**Connettori:**
- Linee dritte, curve, angolate
- Frecce unidirezionali e bidirezionali
- Etichette sui connettori
- Stili personalizzabili (colore, spessore, tratteggio)
- Auto-routing intelligente

**Uso Vendiamonoi:**
- Flowchart del processo ordine: Marketplace → ChannelEngine → Fornitore → Spedizione → Tracking → Cliente
- Diagrammi di architettura: connessioni tra Channable, ChannelEngine, Shopify, Make, Supabase
- Swimlane per responsabilità: chi fa cosa in ogni fase del processo

### 3.4 Frames e Presentazioni

**Frames:**
- Contenitori visivi per organizzare contenuto
- Navigazione rapida tra frames
- Export singolo frame come immagine/PDF
- Sequence di frames per presentazioni

**Presentazione Mode:**
- Transizioni tra frames
- Presenter view con note
- Remote control per presentazioni distribuite
- Recording delle sessioni

**Uso Vendiamonoi:**
- Frame per ogni fase del processo di onboarding fornitore
- Presentazioni per pitch a nuovi fornitori/partner
- Review meeting settimanali con frame dedicati per KPI

### 3.5 Drawing e Pen Tool

- Freehand drawing
- Shape recognition (disegno → forma pulita)
- Highlighter
- Eraser
- Pressure sensitivity (con tablet/stylus)

### 3.6 Media e Embed

**Tipi supportati:**
- Immagini (PNG, JPG, SVG, GIF)
- Video (upload diretto o embed YouTube/Vimeo/Loom)
- PDF (visualizzazione inline con navigazione pagine)
- Documenti (Google Docs, Sheets, Slides embed)
- Website embed (iframe)
- Code snippets

**Uso Vendiamonoi:**
- Embed screenshot marketplace per analisi competitive
- PDF cataloghi fornitori inline per review visuale
- Video tutorial per procedure operative

---

## 4. Funzionalità Avanzate

### 4.1 Smart Diagramming

Miro ha un engine di diagramming professionale integrato:

**Tipi di diagrammi:**
- Flowcharts
- Sequence diagrams
- UML (Class, Activity, State, Use Case)
- ER diagrams (Entity-Relationship)
- BPMN 2.0 (Business Process Model and Notation)
- Network diagrams
- Org charts
- Mind maps
- Wireframes e mockup

**Funzionalità Smart:**
- Auto-layout per riorganizzare diagrammi
- Snap-to-grid intelligente
- Suggerimenti di connessione
- Import/Export Visio (.vsdx)
- Generazione da testo (AI)

**Uso Vendiamonoi — Diagrammi Critici:**

```
DIAGRAMMA 1: Flusso Ordine Completo
┌──────────┐    ┌───────────────┐    ┌──────────────┐
│Marketplace│───▶│ChannelEngine/ │───▶│  Supabase    │
│ (ordine)  │    │  Channable    │    │ (DB ordini)  │
└──────────┘    └───────────────┘    └──────┬───────┘
                                            │
                                            ▼
┌──────────┐    ┌───────────────┐    ┌──────────────┐
│  Cliente  │◀──│   Corriere    │◀──│  Fornitore   │
│(tracking) │    │ (spedizione)  │    │ (evasione)   │
└──────────┘    └───────────────┘    └──────────────┘

DIAGRAMMA 2: Architettura Tech Stack
┌────────────────────────────────────────────────┐
│                 FRONTEND                        │
│  Shopify │ Marketplace portals │ Admin Panel    │
└────────────────────┬───────────────────────────┘
                     │
┌────────────────────▼───────────────────────────┐
│              MIDDLEWARE / INTEGRATION            │
│  Channable │ ChannelEngine │ Make │ API custom  │
└────────────────────┬───────────────────────────┘
                     │
┌────────────────────▼───────────────────────────┐
│                 BACKEND / DATA                  │
│  Supabase │ Google Sheets │ Notion │ ClickUp   │
└────────────────────────────────────────────────┘
```

### 4.2 Mind Mapping

- Nodi espandibili/collassabili
- Auto-layout radiale o ad albero
- Conversione mind map → sticky notes → task board
- Import da file .mm (FreeMind)
- Export come outline testuale

**Uso Vendiamonoi:**
- Mind map strategia marketplace per paese (DE, FR, ES, IT, NL...)
- Mind map categorie prodotto per fornitore
- Brainstorming nuovi servizi e offerte

### 4.3 Kanban Board

- Colonne personalizzabili
- Card con descrizione, assegnatari, date, tag
- WIP limits
- Swimlanes
- Filtri e ordinamento

**Nota:** Per il task management quotidiano, Vendiamonoi usa ClickUp. Il Kanban di Miro è utile per workshop di pianificazione e sessioni strategiche, non per execution giornaliera.

### 4.4 Estimation e Planning

**Voting:**
- Dot voting
- Thumbs up/down
- Custom scales (1-5, T-shirt sizing)
- Anonymous voting
- Results visualization

**Timer:**
- Countdown timer visibile a tutti
- Timer per timeboxing workshop
- Pomodoro integrato

**Estimation:**
- Planning Poker integrato
- Story point estimation
- T-shirt sizing
- Fibonacci sequence

**Uso Vendiamonoi:**
- Votazione priorità marketplace da attivare
- Estimation effort per integrazione nuovi fornitori
- Timeboxing sessioni di pianificazione trimestrale

---

## 5. Funzionalità AI (Miro AI / Miro Assist)

### 5.1 Panoramica AI

Miro ha integrato funzionalità AI significative (powered by OpenAI e modelli proprietari):

**AI Credits:**
- Starter: 2000 crediti/mese per team
- Business: 2000 crediti/mese per team
- Enterprise: Custom allocation
- 1 azione AI = ~1-10 crediti (varia per complessità)

### 5.2 Funzionalità AI Disponibili

**Generazione contenuto:**
- **Brainstorm con AI** — Genera sticky notes da un prompt
- **Mind map generation** — Crea mind map da un topic
- **Image generation** — Genera immagini direttamente sulla board
- **Summarize** — Riassume contenuto di sticky notes o testo
- **Expand** — Espande un'idea in sotto-idee

**Organizzazione:**
- **Clustering** — Raggruppa automaticamente sticky notes per tema
- **Categorize** — Assegna categorie a elementi
- **Sort** — Ordina per priorità, tema, urgenza

**Diagramming AI:**
- **Generate diagram from text** — Descrivi a parole, Miro genera il diagramma
- **Auto-layout** — Riorganizza diagrammi per leggibilità
- **Suggest connections** — Suggerisce connessioni mancanti

**Code & Technical:**
- **Generate user stories** — Da requisiti a user stories
- **Create flowchart from description** — Testo → flowchart
- **ERD generation** — Genera Entity-Relationship da descrizione

### 5.3 Miro AI Sidekicks (2025-2026)

Nuova funzionalità avanzata: agenti AI specializzati per task specifici:
- **Facilitator Sidekick** — Guida workshop automaticamente
- **Analyst Sidekick** — Analizza contenuto della board e suggerisce insight
- **Designer Sidekick** — Suggerisce layout e design migliorati

### 5.4 Miro AI Flows (Automazioni AI)

Workflow automatici powered by AI:
- Trigger → Condizione → Azione AI
- Esempio: "Quando aggiungi sticky note → categorizza automaticamente → sposta nella colonna corretta"
- Integrazione con workflow esterni via webhook

**Uso Vendiamonoi — AI:**
- Brainstorming AI per generare idee espansione marketplace
- Clustering automatico feedback fornitori per tema
- Generazione automatica flowchart processi da descrizione testuale
- Summarize risultati workshop in action items

---

## 6. Integrazioni

### 6.1 Integrazioni Native (150+)

Miro offre un ecosistema di integrazioni molto ampio:

**Project Management:**
- Jira (bidirezionale: card ↔ issue)
- Asana (card ↔ task)
- Monday.com
- ClickUp ✅ **Usato da Vendiamonoi**
- Trello
- Azure DevOps

**Communication:**
- Slack (embed board, notifiche)
- Microsoft Teams (app nativa, tab in canali)
- Zoom (whiteboard in call)
- Google Meet
- Webex

**Design:**
- Figma (embed design, sync frames)
- Adobe Creative Cloud
- Sketch
- InVision

**Documentation:**
- Notion ✅ **Usato da Vendiamonoi** (embed board in pagine Notion)
- Confluence
- Google Docs/Sheets/Slides
- SharePoint

**Development:**
- GitHub (issues, PRs visualizzati)
- GitLab
- Bitbucket

**Data & Analytics:**
- Google Sheets ✅ **Usato da Vendiamonoi**
- Excel Online
- Power BI
- Tableau
- Looker

**Storage:**
- Google Drive ✅ **Usato da Vendiamonoi**
- OneDrive
- Dropbox
- Box

**Automation:**
- Zapier
- Make (Integromat) ✅ **Usato da Vendiamonoi**
- Power Automate

### 6.2 Integrazioni Critiche per Vendiamonoi

**Miro ↔ Notion:**
- Embed board Miro dentro pagine Notion
- Link tra card Miro e pagine Notion
- Sincronizzazione contenuto visuale con knowledge base

**Miro ↔ ClickUp:**
- Conversione sticky notes → task ClickUp
- Visualizzazione task ClickUp sulla board Miro
- Planning session → task execution

**Miro ↔ Make (Integromat):**
- Trigger webhook quando board viene modificata
- Creazione automatica card Miro da eventi Make
- Sincronizzazione dati tra Miro e altri tool

**Miro ↔ Google Sheets:**
- Import dati tabellari su board
- Export contenuto board → spreadsheet
- Visualizzazione dati da sheets

---

## 7. REST API

### 7.1 Panoramica API

Miro offre una REST API v2 completa per automazione e integrazione programmatica.

**Base URL:** `https://api.miro.com/v2`

**Autenticazione:**
- OAuth 2.0 (Authorization Code Flow)
- Bearer Token
- Scopes granulari per permessi

**Rate Limits:**
- Standard: 100 requests/minuto per token
- Enterprise: Limiti aumentati
- Burst: Fino a 200 requests in 10 secondi

### 7.2 Endpoint Principali

**Boards:**
```
GET    /boards                    # Lista board
POST   /boards                    # Crea board
GET    /boards/{board_id}         # Dettaglio board
PATCH  /boards/{board_id}         # Aggiorna board
DELETE /boards/{board_id}         # Elimina board
GET    /boards/{board_id}/members # Membri board
POST   /boards/{board_id}/copy   # Copia board
```

**Items (elementi sulla board):**
```
GET    /boards/{board_id}/items              # Lista items
GET    /boards/{board_id}/items/{item_id}    # Dettaglio item
PATCH  /boards/{board_id}/items/{item_id}    # Aggiorna item
DELETE /boards/{board_id}/items/{item_id}    # Elimina item
```

**Sticky Notes:**
```
POST   /boards/{board_id}/sticky_notes       # Crea sticky note
GET    /boards/{board_id}/sticky_notes/{id}  # Dettaglio
PATCH  /boards/{board_id}/sticky_notes/{id}  # Aggiorna
DELETE /boards/{board_id}/sticky_notes/{id}  # Elimina
```

**Shapes:**
```
POST   /boards/{board_id}/shapes             # Crea shape
GET    /boards/{board_id}/shapes/{id}        # Dettaglio
PATCH  /boards/{board_id}/shapes/{id}        # Aggiorna
DELETE /boards/{board_id}/shapes/{id}        # Elimina
```

**Connectors:**
```
POST   /boards/{board_id}/connectors         # Crea connettore
GET    /boards/{board_id}/connectors/{id}    # Dettaglio
PATCH  /boards/{board_id}/connectors/{id}    # Aggiorna
DELETE /boards/{board_id}/connectors/{id}    # Elimina
```

**Frames:**
```
POST   /boards/{board_id}/frames             # Crea frame
GET    /boards/{board_id}/frames/{id}        # Dettaglio
PATCH  /boards/{board_id}/frames/{id}        # Aggiorna
DELETE /boards/{board_id}/frames/{id}        # Elimina
```

**Tags:**
```
POST   /boards/{board_id}/tags               # Crea tag
GET    /boards/{board_id}/tags               # Lista tag
GET    /boards/{board_id}/tags/{id}          # Dettaglio
PATCH  /boards/{board_id}/tags/{id}          # Aggiorna
DELETE /boards/{board_id}/tags/{id}          # Elimina
```

**Images:**
```
POST   /boards/{board_id}/images             # Upload immagine
GET    /boards/{board_id}/images/{id}        # Dettaglio
PATCH  /boards/{board_id}/images/{id}        # Aggiorna
DELETE /boards/{board_id}/images/{id}        # Elimina
```

**Documents:**
```
POST   /boards/{board_id}/documents          # Upload documento
GET    /boards/{board_id}/documents/{id}     # Dettaglio
PATCH  /boards/{board_id}/documents/{id}     # Aggiorna
DELETE /boards/{board_id}/documents/{id}     # Elimina
```

### 7.3 Esempio API — Creazione Board con Elementi

```javascript
// 1. Crea board
const boardResponse = await fetch('https://api.miro.com/v2/boards', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${MIRO_TOKEN}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'Vendiamonoi - Processo Ordini Q2 2026',
    description: 'Flowchart processo evasione ordini multi-marketplace',
    policy: {
      permissionsPolicy: {
        collaborationToolsStartAccess: 'all_editors',
        copyAccess: 'team_members',
        sharingAccess: 'team_members_and_collaborators'
      },
      sharingPolicy: {
        access: 'private',
        inviteToAccountAndBoardLinkAccess: 'editor',
        organizationAccess: 'private',
        teamAccess: 'edit'
      }
    }
  })
});
const board = await boardResponse.json();
const boardId = board.id;

// 2. Crea Frame container
await fetch(`https://api.miro.com/v2/boards/${boardId}/frames`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${MIRO_TOKEN}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    data: { title: 'Fase 1 - Ricezione Ordine', format: 'custom' },
    geometry: { width: 1200, height: 800 },
    position: { x: 0, y: 0 }
  })
});

// 3. Aggiungi Sticky Notes
const steps = [
  { content: '📦 Ordine ricevuto da Marketplace', color: 'light_blue', x: 100, y: 100 },
  { content: '🔄 Sync con ChannelEngine/Channable', color: 'light_yellow', x: 400, y: 100 },
  { content: '📋 Registrazione in Supabase', color: 'light_green', x: 700, y: 100 },
  { content: '📧 Notifica a fornitore', color: 'light_pink', x: 1000, y: 100 },
  { content: '🚚 Fornitore spedisce', color: 'light_blue', x: 100, y: 400 },
  { content: '📍 Tracking aggiornato', color: 'light_yellow', x: 400, y: 400 },
  { content: '✅ Ordine completato', color: 'light_green', x: 700, y: 400 }
];

for (const step of steps) {
  await fetch(`https://api.miro.com/v2/boards/${boardId}/sticky_notes`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${MIRO_TOKEN}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      data: { content: step.content, shape: 'square' },
      style: { fillColor: step.color, textAlign: 'center', textAlignVertical: 'middle' },
      geometry: { width: 250 },
      position: { x: step.x, y: step.y }
    })
  });
}

// 4. Crea connettori tra steps
// (dopo aver ottenuto gli ID degli sticky notes creati)
await fetch(`https://api.miro.com/v2/boards/${boardId}/connectors`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${MIRO_TOKEN}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    startItem: { id: stickyNote1Id },
    endItem: { id: stickyNote2Id },
    style: {
      strokeColor: '#1a1a2e',
      strokeWidth: '2.0',
      strokeStyle: 'normal',
      startStrokeCap: 'none',
      endStrokeCap: 'stealth'
    }
  })
});
```

### 7.4 Webhook API

```
POST   /boards/{board_id}/webhooks     # Crea webhook
GET    /boards/{board_id}/webhooks     # Lista webhook
GET    /webhooks/{webhook_id}          # Dettaglio
DELETE /webhooks/{webhook_id}          # Elimina
```

**Eventi supportati:**
- `board_subscription_changed` — Cambio permessi board
- `item_created` — Nuovo elemento creato
- `item_updated` — Elemento modificato
- `item_deleted` — Elemento eliminato
- `board_exported` — Board esportata

**Uso Vendiamonoi — Webhook:**
```javascript
// Webhook: notifica su Slack/Make quando board processo viene modificata
await fetch(`https://api.miro.com/v2/boards/${boardId}/webhooks`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${MIRO_TOKEN}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    callbackUrl: 'https://hook.eu2.make.com/vendiamonoi-miro-webhook',
    boardSubscription: {
      status: 'enabled',
      eventTypes: ['item_created', 'item_updated']
    }
  })
});
```

### 7.5 Web SDK (Miro App)

Oltre alla REST API, Miro offre un Web SDK per creare app personalizzate che girano dentro Miro:

**Funzionalità Web SDK:**
- Accesso al canvas programmatico
- Creazione di pannelli laterali custom
- Modal dialog custom
- Bottom bar widgets
- Event listeners per interazioni utente
- Manipolazione items in real-time

```typescript
// Esempio: App Miro custom per Vendiamonoi
import { OnlineUserInfo } from '@mirohq/websdk-types';

// Ascolta creazione nuovi sticky note
miro.board.ui.on('items:create', async (event) => {
  const items = event.items;
  for (const item of items) {
    if (item.type === 'sticky_note') {
      // Auto-tag sticky notes con prefisso reparto
      const content = item.content;
      if (content.includes('ordine') || content.includes('spedizione')) {
        await item.update({ data: { content: `[EVASIONE] ${content}` } });
      }
    }
  }
});

// Crea pannello laterale con KPI
miro.board.ui.openPanel({
  url: 'panel.html',
  height: 600
});
```

---

## 8. Integrazione con Make (Integromat)

### 8.1 Moduli Make per Miro

Make ha un connettore nativo per Miro con i seguenti moduli:

**Trigger:**
- Watch Board Items — Monitora nuovi elementi
- Watch Boards — Monitora nuove board

**Actions:**
- Create a Board — Crea nuova board
- Create a Sticky Note — Crea sticky note
- Create a Shape — Crea forma
- Create a Card — Crea card
- Create a Connector — Crea connettore
- Create a Frame — Crea frame
- Update an Item — Aggiorna elemento
- Delete an Item — Elimina elemento
- Get Board — Ottieni dettagli board
- List Board Items — Lista elementi
- Make an API Call — Chiamata API personalizzata

### 8.2 Scenario Make Esempio: Sync Ordini → Miro

```
SCENARIO: Nuovo ordine marketplace → Aggiorna board Miro tracking

Trigger: ChannelEngine webhook (nuovo ordine)
    │
    ├─▶ Module 1: HTTP - Parse ordine
    │   • Estrai: marketplace, prodotto, fornitore, tracking
    │
    ├─▶ Module 2: Miro - Create Sticky Note
    │   • Board: "Tracking Ordini Live"
    │   • Content: "ORD-{id} | {marketplace} | {prodotto}"
    │   • Color: light_blue (nuovo)
    │   • Position: colonna "In Arrivo"
    │
    ├─▶ Module 3: Router
    │   ├─▶ Route 1 (tracking aggiornato):
    │   │   Miro - Update Sticky Note
    │   │   • Color: light_yellow (in transito)
    │   │   • Position: sposta a colonna "In Spedizione"
    │   │
    │   └─▶ Route 2 (ordine completato):
    │       Miro - Update Sticky Note
    │       • Color: light_green (completato)
    │       • Position: sposta a colonna "Completati"
    │
    └─▶ Module 4: Miro - Create Connector
        • Collega ordine a fornitore corrispondente
```

### 8.3 Scenario Make: Workshop Automation

```
SCENARIO: Post-workshop → Esporta risultati automaticamente

Trigger: Schedule (ogni lunedì alle 9:00)
    │
    ├─▶ Module 1: Miro - List Board Items
    │   • Board: "Workshop Settimanale"
    │   • Type: sticky_note
    │
    ├─▶ Module 2: Iterator
    │   • Itera su tutti gli sticky notes
    │
    ├─▶ Module 3: Text Parser - Extract
    │   • Categorizza per colore/tag
    │
    ├─▶ Module 4: Router
    │   ├─▶ Route 1 (action items):
    │   │   ClickUp - Create Task
    │   │   • Lista: "Action Items Workshop"
    │   │
    │   ├─▶ Route 2 (idee):
    │   │   Notion - Create Page
    │   │   • Database: "Idea Bank"
    │   │
    │   └─▶ Route 3 (problemi):
    │       Notion - Create Page
    │       • Database: "Issue Tracker"
    │
    └─▶ Module 5: Slack - Send Message
        • Canale: #team-updates
        • Messaggio: "Workshop completato: {n} action items, {n} idee, {n} problemi"
```

---

## 9. Sicurezza e Compliance

### 9.1 Certificazioni

| Certificazione | Status | Note |
|---|---|---|
| SOC 2 Type II | ✅ | Audit annuale |
| ISO 27001 | ✅ | Information Security Management |
| ISO 27017 | ✅ | Cloud Security |
| ISO 27018 | ✅ | PII Protection in Cloud |
| GDPR | ✅ | Data Processing Agreement disponibile |
| HIPAA | ✅ | Enterprise only |
| CSA STAR | ✅ | Cloud Security Alliance |
| PCI DSS | ❌ | Non applicabile (no pagamenti) |

### 9.2 Data Residency

- **Data center:** AWS (US, EU, AU)
- **EU data residency:** Disponibile su Enterprise
- **Encryption at rest:** AES-256
- **Encryption in transit:** TLS 1.2+
- **Data retention:** Configurabile (Enterprise)
- **Right to deletion:** GDPR compliant

### 9.3 Controlli di Accesso

**Board-level:**
- Private (solo invitati)
- Team (tutti i membri del team)
- Organization (tutti nell'organizzazione)
- Public (link pubblico)

**Ruoli:**
- Viewer — Solo visualizzazione
- Commenter — Visualizzazione + commenti
- Editor — Modifica completa
- Admin — Gestione board + permessi

**Enterprise features:**
- SSO SAML 2.0
- SCIM provisioning (auto user management)
- Audit logs
- Data loss prevention (DLP)
- IP restrictions
- Domain management

### 9.4 Raccomandazioni Sicurezza per Vendiamonoi

1. **Board sensibili (contratti fornitori, pricing):** Impostare SEMPRE come Private
2. **Accesso esterno:** Usare link con scadenza per condivisione temporanea con fornitori
3. **Naming convention:** `[REPARTO]-[TIPO]-Nome` (es. `[FORN]-FLOWCHART-Onboarding Nuovo Fornitore`)
4. **Revisione trimestrale:** Controllare chi ha accesso a quali board
5. **Non salvare credenziali** su board Miro (usare Notion/Vault per quello)

---

## 10. Template e Framework Consigliati

### 10.1 Template Nativi Miro Utili per Vendiamonoi

| Template | Uso Vendiamonoi |
|---|---|
| **Customer Journey Map** | Tracciare esperienza ordine: dal click su marketplace alla consegna |
| **Process Flow** | Documentare procedure operative di ogni reparto |
| **Org Chart** | Struttura organizzativa e responsabilità |
| **SWOT Analysis** | Analisi marketplace (pro/contro per ogni marketplace) |
| **Kanban Board** | Planning sprint e backlog progetti |
| **Mind Map** | Strategia espansione, categorizzazione prodotti |
| **Retrospective** | Review periodica processi e miglioramenti |
| **Stakeholder Map** | Mappare fornitori per importanza/relazione |
| **Value Stream Map** | Ottimizzare flusso dalla richiesta ordine alla consegna |
| **Service Blueprint** | Architettura servizi end-to-end |
| **Business Model Canvas** | Evoluzione modello business distribuzione digitale |
| **Fishbone Diagram** | Root cause analysis problemi ricorrenti |

### 10.2 Template Custom Consigliati per Vendiamonoi

**Template 1: Onboarding Nuovo Fornitore**
```
Frame 1: Checklist Pre-Onboarding
  ├── Sticky: Contatto iniziale ✅
  ├── Sticky: NDA firmato ✅
  ├── Sticky: Catalogo ricevuto ✅
  ├── Sticky: Listino prezzi ✅
  └── Sticky: Condizioni logistiche ✅

Frame 2: Setup Tecnico
  ├── Shape: Integrazione feed CSV/XML
  ├── Shape: Mapping categorie
  ├── Shape: Setup Channable rules
  └── Shape: Test ordini

Frame 3: Go-Live Checklist
  ├── Sticky: Primo marketplace attivo ✅
  ├── Sticky: Ordine test completato ✅
  ├── Sticky: KPI baseline definiti ✅
  └── Sticky: Contratto firmato ✅
```

**Template 2: Analisi Nuovo Marketplace**
```
Frame 1: Overview
  ├── Info base (paese, GMV, commissioni, categorie)
  ├── SWOT analysis (4 quadranti)
  └── Competitor presence

Frame 2: Requisiti Tecnici
  ├── Tipo integrazione (API/feed/Mirakl)
  ├── Formato dati richiesto
  ├── Requisiti documenti
  └── Timeline setup

Frame 3: Business Case
  ├── Revenue potenziale
  ├── Costi setup + running
  ├── ROI stimato
  └── Decision: GO / NO-GO
```

**Template 3: Review Mensile Performance**
```
Frame 1: KPI Dashboard
  ├── Ordini totali (this month vs last month)
  ├── Revenue per marketplace
  ├── Tasso resi
  └── Tempo medio evasione

Frame 2: Issues & Action Items
  ├── Colonna: Problemi aperti
  ├── Colonna: In risoluzione
  └── Colonna: Risolti

Frame 3: Next Month Priorities
  ├── Top 3 priorities
  ├── Risorse necessarie
  └── Timeline
```

---

## 11. Collaborazione e Workshop

### 11.1 Funzionalità Collaborative

**Real-time:**
- Cursori live di tutti i partecipanti
- Editing simultaneo senza conflitti
- Chat integrata nella board
- Video chat built-in (fino a 25 partecipanti)
- Screen sharing
- Remote pointer ("Follow me" mode)
- Reactions ed emoji

**Async:**
- Commenti su qualsiasi elemento
- @mention di team member
- Risoluzione commenti
- Version history
- Board activity log

### 11.2 Workshop Facilitation

**Strumenti facilitazione:**
- Timer visibile a tutti (countdown)
- Voting/polling integrato
- Attention management ("Bring to me")
- Lock elementi per evitare modifiche accidentali
- Presentation mode per walkthrough guidato
- Breakout frames per lavoro in sotto-gruppi
- Ice-breaker templates

**Best Practice Workshop Vendiamonoi:**

1. **Preparazione (30 min prima):**
   - Preparare board con frames vuoti e istruzioni
   - Testare link di accesso
   - Verificare che tutti abbiano account/accesso

2. **Apertura (5 min):**
   - Condividere obiettivo del workshop
   - Spiegare regole (timer, voting, etc.)
   - Ice-breaker rapido

3. **Esecuzione (30-60 min):**
   - Usare timer per timeboxing ogni attività
   - Facilitare con voting per prioritizzazione
   - Usare "Bring to me" per allineare focus

4. **Chiusura (10 min):**
   - Recap action items
   - Voting finale su priorità
   - Screenshot/export risultati

5. **Follow-up (post workshop):**
   - Esportare action items → ClickUp
   - Documentare decisioni → Notion
   - Condividere board view-only con stakeholder

---

## 12. Piattaforme e Compatibilità

### 12.1 Piattaforme Supportate

| Piattaforma | Disponibilità | Note |
|---|---|---|
| **Web App** | ✅ | Browser moderni (Chrome, Firefox, Safari, Edge) |
| **Desktop Windows** | ✅ | App nativa con performance migliori |
| **Desktop macOS** | ✅ | App nativa con performance migliori |
| **iOS (iPad/iPhone)** | ✅ | Ottimizzato per Apple Pencil su iPad |
| **Android (Tablet/Phone)** | ✅ | Touch optimized |
| **Microsoft Teams** | ✅ | Tab e app integrata |
| **Zoom** | ✅ | Whiteboard in-call |
| **Slack** | ✅ | Embed e notifiche |

### 12.2 Requisiti Browser

- Chrome 90+ (consigliato)
- Firefox 88+
- Safari 14+
- Edge 90+
- WebGL richiesto per performance ottimali
- Minimum 4GB RAM consigliati per board grandi

---

## 13. Performance e Limiti

### 13.1 Limiti Tecnici

| Parametro | Limite |
|---|---|
| Elementi per board | ~10.000 (consigliato < 5.000 per performance) |
| Dimensione file upload | 10 MB per immagine |
| Dimensione board | Tecnicamente infinita (performance degrada oltre 10K items) |
| Webhook per board | 20 |
| API calls | 100/min per token |
| Board per team | Illimitate (piano a pagamento) |
| Membri per board | Illimitati |
| Version history | 14 giorni (Starter), 90 giorni (Business), illimitato (Enterprise) |

### 13.2 Best Practice Performance

1. **Archiviare board vecchie** — Spostare in cartella "Archivio" board non più attive
2. **Suddividere board grandi** — Se > 3000 elementi, considerare split in board multiple
3. **Limitare embed** — Video e iframe rallentano il rendering
4. **Usare frames** — Organizzare contenuto in frames migliora la navigazione
5. **Pulizia periodica** — Rimuovere sticky notes e elementi temporanei dopo workshop
6. **Desktop app** — Per board pesanti, preferire l'app desktop al browser

---

## 14. Export e Import

### 14.1 Formati Export

| Formato | Cosa esporta | Uso |
|---|---|---|
| **PDF** | Board completa o frames selezionati | Documentazione, condivisione offline |
| **PNG/JPG** | Immagine raster della board/frame | Embedding in altri documenti |
| **SVG** | Vettoriale della board | Alta qualità per stampa/design |
| **CSV** | Dati strutturati (card, sticky notes) | Import in spreadsheet/database |
| **Miro backup** | File .rtb completo | Backup e migrazione |

### 14.2 Formati Import

| Formato | Cosa importa |
|---|---|
| **Visio (.vsdx)** | Diagrammi Microsoft Visio |
| **CSV** | Dati tabellari → card/sticky notes |
| **Jira/Trello** | Board e issue |
| **Immagini** | PNG, JPG, SVG, GIF |
| **PDF** | Documenti come immagini inline |
| **Miro backup (.rtb)** | Restore board completa |

**Uso Vendiamonoi:**
- Export PDF dei flowchart processi per condividere con fornitori
- Export CSV dei risultati workshop per import in ClickUp/Notion
- Import CSV cataloghi per visualizzazione rapida dati prodotto
- Export PNG per embedding in documentazione Notion

---

## 15. Struttura Organizzativa Consigliata

### 15.1 Organizzazione Board per Vendiamonoi

```
📁 Vendiamonoi.it (Team)
│
├── 📁 🏢 Strategia & Management
│   ├── 🗺️ Business Model Canvas
│   ├── 🗺️ Roadmap Annuale 2026
│   ├── 🗺️ OKR Trimestrali
│   └── 🗺️ Meeting Notes & Decisions
│
├── 📁 📦 Reparto Fornitori
│   ├── 🗺️ Onboarding Flowchart
│   ├── 🗺️ Mappa Fornitori Attivi
│   ├── 🗺️ Pipeline Nuovi Fornitori
│   └── 🗺️ Template: Onboarding Nuovo Fornitore
│
├── 📁 📋 Reparto Catalogo
│   ├── 🗺️ Workflow Creazione Scheda Prodotto
│   ├── 🗺️ Mapping Categorie Marketplace
│   ├── 🗺️ Data Quality Process
│   └── 🗺️ Feed Generation Flow
│
├── 📁 🏪 Reparto Marketplace
│   ├── 🗺️ Mappa Marketplace Europei
│   ├── 🗺️ Processo Pubblicazione
│   ├── 🗺️ Analisi Nuovo Marketplace (template)
│   └── 🗺️ KPI Dashboard Visuale
│
├── 📁 🚚 Reparto Evasione Ordini
│   ├── 🗺️ Processo Ordine End-to-End
│   ├── 🗺️ Tracking Board Live
│   ├── 🗺️ Escalation Flowchart
│   └── 🗺️ Processo Reso
│
├── 📁 💬 Customer Service
│   ├── 🗺️ Decision Tree Risposte
│   ├── 🗺️ Escalation Process
│   └── 🗺️ FAQ Knowledge Map
│
├── 📁 💰 Amministrazione
│   ├── 🗺️ Processo Fatturazione
│   ├── 🗺️ Riconciliazione Flow
│   └── 🗺️ Processo OSS
│
├── 📁 ⚙️ Tech & Infrastruttura
│   ├── 🗺️ Architettura Sistema Completa
│   ├── 🗺️ Integrazioni Map
│   ├── 🗺️ Data Flow Diagram
│   └── 🗺️ API Dependencies Map
│
└── 📁 📦 Archivio
    ├── 🗺️ Workshop Q4 2025
    └── 🗺️ Board deprecate
```

### 15.2 Naming Convention

Formato: `[REPARTO]-[TIPO]-Nome Descrittivo`

**Reparti:** FORN, CAT, MKT, ORD, CS, AMM, TECH, STRAT

**Tipi:** FLOW (flowchart), MAP (mappa), WS (workshop), DIAG (diagramma), TEMP (template), PLAN (planning), RETRO (retrospettiva)

**Esempi:**
- `FORN-FLOW-Onboarding Nuovo Fornitore`
- `MKT-MAP-Marketplace Europei 2026`
- `ORD-FLOW-Processo Evasione Ordine`
- `TECH-DIAG-Architettura Sistema`
- `STRAT-WS-Pianificazione Q3 2026`

---

## 16. Automazioni Interne Miro

### 16.1 Miro Automations (Built-in)

Miro ha un sistema di automazioni native (simile a Notion automations):

**Trigger disponibili:**
- Item moved to frame
- Item created
- Item updated
- Tag added/removed
- Timer expired

**Azioni disponibili:**
- Send notification
- Move item
- Change item style (colore, dimensione)
- Add tag
- Send webhook
- Create item

**Esempio automazione:**
```
TRIGGER: Sticky note spostata nel frame "Completato"
ACTION 1: Cambia colore → verde
ACTION 2: Aggiungi tag "done"
ACTION 3: Invia webhook → Make scenario (sync con ClickUp)
```

### 16.2 Automazioni Consigliate per Vendiamonoi

1. **Auto-color coding:** Quando sticky note viene taggata con un reparto, cambia colore automaticamente
2. **Completion tracking:** Spostamento in frame "Done" → webhook → aggiorna ClickUp task
3. **Notification on update:** Quando board strategica viene modificata → notifica team leads
4. **Archive trigger:** Dopo 30 giorni senza modifiche → notifica per review/archiviazione

---

## 17. Confronto con Alternative

### 17.1 Miro vs Competitor

| Feature | Miro | FigJam | Lucidchart | Whimsical | Microsoft Whiteboard |
|---|---|---|---|---|---|
| **Canvas infinito** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Diagramming** | ⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **Collaboration** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Integrazioni** | 150+ | Figma ecosystem | 80+ | Limitato | Microsoft ecosystem |
| **API** | ⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ | ❌ | ⭐⭐⭐ |
| **AI features** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ |
| **Template library** | 2500+ | 300+ | 1000+ | 100+ | Limitato |
| **Workshop tools** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **Prezzo (per user/mo)** | $8-$20 | $5-$15 | $7.95-$20 | $12-$20 | Incluso in M365 |

### 17.2 Perché Miro per Vendiamonoi

1. **Miglior collaboration** — Essendo un team distribuito, la collaborazione real-time è critica
2. **Integrazioni ampie** — Si integra nativamente con tutto il tech stack (Notion, ClickUp, Make, Slack)
3. **API robusta** — Permette automazioni avanzate con Make
4. **Workshop tools** — Voting, timer, estimation built-in per sessioni produttive
5. **Template library** — 2500+ template risparmiano tempo nella creazione di nuove board
6. **AI features** — Brainstorming e organizzazione assistiti da AI

---

## 18. Troubleshooting e Problemi Comuni

### 18.1 Problemi Frequenti e Soluzioni

| Problema | Causa | Soluzione |
|---|---|---|
| Board lenta | Troppi elementi (>5000) | Archiviare elementi vecchi, split in board multiple |
| Embed non carica | Blocco iframe da terze parti | Verificare URL, usare immagine statica come fallback |
| Export PDF tagliato | Frame non configurato correttamente | Verificare dimensioni frame, usare "Fit to frame" |
| API 429 Too Many Requests | Rate limit superato | Implementare exponential backoff, rispettare 100 req/min |
| Webhook non arriva | URL non raggiungibile | Verificare endpoint Make, controllare HTTPS |
| Collaboratore non vede board | Permessi insufficienti | Verificare sharing settings, inviare nuovo invito |
| Connettori scompaiono | Bug noto con molti connettori | Salvare, refresh pagina, ricollegare se necessario |
| Import Visio incompleto | Formati complessi non supportati | Semplificare in Visio prima dell'import |

### 18.2 Supporto

| Canale | Piano | Dettaglio |
|---|---|---|
| Community Forum | Tutti | https://community.miro.com |
| Email Support | Starter+ | support@miro.com |
| Priority Support | Business+ | Risposta entro 4 ore |
| Dedicated CSM | Enterprise | Account manager dedicato |
| Phone Support | Enterprise | Su appuntamento |

---

## 19. Risorse e Link Utili

### 19.1 Documentazione Ufficiale

| Risorsa | URL |
|---|---|
| Help Center | https://help.miro.com |
| API Documentation | https://developers.miro.com/reference |
| Developer Portal | https://developers.miro.com |
| Community | https://community.miro.com |
| Academy | https://academy.miro.com |
| Blog | https://miro.com/blog |
| Status Page | https://status.miro.com |
| Changelog | https://miro.com/changelog |
| Security & Compliance | https://miro.com/trust |
| Template Library | https://miro.com/templates |

### 19.2 Risorse Formazione

- **Miro Academy** — Corsi gratuiti su funzionalità e best practice
- **YouTube Channel** — Tutorial e webinar
- **Community Templates** — Template creati dalla community
- **Developer Documentation** — Guide per API e SDK

### 19.3 Contatti

| Tipo | Contatto |
|---|---|
| Sales | sales@miro.com |
| Support | support@miro.com |
| Partnership | partners@miro.com |
| Developer Relations | developers@miro.com |

---

## Changelog

| Data | Versione | Modifiche | Autore |
|---|---|---|---|
| 2026-03-31 | 1.0 | Creazione documentazione completa | Claude AI / Alessio Baggiani |