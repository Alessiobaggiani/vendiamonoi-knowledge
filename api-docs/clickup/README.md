# ClickUp — Documentazione Tecnica Completa

## Informazioni Generali

| Campo | Dettaglio |
|---|---|
| **Nome** | ClickUp |
| **Tipo** | Piattaforma di project management e produttività all-in-one |
| **Sito** | [https://clickup.com](https://clickup.com) |
| **Developer Portal** | [https://developer.clickup.com](https://developer.clickup.com) |
| **API Reference** | [https://developer.clickup.com/reference](https://developer.clickup.com/reference) |
| **Help Center** | [https://help.clickup.com](https://help.clickup.com) |
| **Integrazioni** | [https://clickup.com/integrations](https://clickup.com/integrations) |
| **Fondazione** | 2017 (Zeb Evans, Alex Yurkowski) |
| **Sede** | San Diego, California |
| **Valutazione** | $4 miliardi (Series C, Ottobre 2021) |
| **Revenue** | $300M (2025) |
| **Clienti paganti** | 100.000+ |
| **Utenti totali** | 10+ milioni |
| **Team** | 2+ milioni nel mondo |
| **Certificazioni** | SOC 2 Type II, ISO 27001, ISO 27017, ISO 27018, GDPR, CCPA, HIPAA |

---

## Contesto Vendiamonoi.it

### Utilizzo in Vendiamonoi.it

ClickUp è la piattaforma di project management e gestione operativa di Vendiamonoi.it S.R.L. Unipersonale, utilizzata per:

1. **Gestione task operativi** — task per ogni reparto (fornitori, catalogo, marketplace, evasione, CS, admin)
2. **Workflow marketplace** — tracking pubblicazioni, ottimizzazioni, problemi per marketplace
3. **Gestione fornitori** — onboarding, stato cataloghi, comunicazioni, documenti
4. **Sprint e iterazioni** — cicli di lavoro settimanali/bisettimanali con burndown
5. **Automazioni** — task automatici da webhook, notifiche, assegnazioni rule-based
6. **Dashboard KPI** — metriche operative per marketplace, fornitori, ordini
7. **Documenti interni** — procedure, wiki, knowledge base aziendale via ClickUp Docs
8. **Time tracking** — monitoraggio ore per attività e reparto
9. **Forms** — raccolta richieste interne, segnalazioni problemi, onboarding fornitori

### Flusso Operativo

```
GESTIONE OPERATIVA:
  Task Vendiamonoi.it
    → Workspace "Vendiamonoi"
    → Spaces per reparto (Fornitori, Catalogo, Marketplace, Evasione, CS, Admin)
    → Folders per area funzionale
    → Lists per progetto/attività
    → Tasks con Custom Fields marketplace-specific

AUTOMAZIONE:
  Webhook / Make.com / Automazioni native
    → Creazione task automatica (es. nuovo fornitore → task onboarding)
    → Assegnazione automatica per reparto
    → Notifiche su cambi stato
    → Sync con Supabase / eDesk / Fatture in Cloud

REPORTING:
  Dashboard ClickUp
    → KPI per marketplace
    → Stato task per reparto
    → Burndown sprint
    → Time tracking per attività
```

---

## 1. Architettura della Piattaforma

### 1.1 Gerarchia Organizzativa

| Livello | Funzione |
|---|---|
| **Workspace** | Unità organizzativa top-level contenente utenti, spaces, folders, lists, tasks |
| **Spaces** | Organizzazione per reparti, team, iniziative, clienti |
| **Folders** | Livello organizzativo opzionale contenente lists (non può contenere altri folders) |
| **Lists** | Contenitore di task relativi a un progetto o obiettivo |
| **Tasks** | Elementi actionable con proprietà personalizzabili |
| **Subtasks** | Task innestati dentro altri task, ereditano proprietà |
| **Checklists** | Liste di controllo dentro i task |

### 1.2 Core Features

| Feature | Funzione |
|---|---|
| **Tasks** | Assegnazione, date, priorità, custom fields, commenti, dipendenze, relazioni |
| **Custom Fields** | Campi personalizzati: testo, numero, dropdown, checkbox, date, email, URL, formula, relazione |
| **Views** | 13+ tipi di visualizzazione (List, Board, Calendar, Table, Timeline, Gantt, Map, Workload, Activity, Team, Mind Map, Chat, Docs) |
| **Docs** | Documenti collaborativi real-time con nested pages, versioning, wiki |
| **Whiteboards** | Lavagne collaborative per brainstorming e pianificazione |
| **Dashboards** | Report personalizzati con charts, tabelle, workload, time tracking, calcoli |
| **Goals & OKR** | Tracking obiettivi con target, key results, dashboard integrati |
| **Chat** | Messaggistica AI-assisted integrata con task |
| **Time Tracking** | Tracking ore nativo con billable, timesheet, report |
| **Automations** | Trigger, condizioni, azioni per automazione workflow |
| **Forms** | Form builder con custom fields, logica condizionale, task automatici |
| **Sprints** | Workflow agile con sprint planning, burndown/burnup, automazione |
| **ClickUp Brain** | AI integrata per task generation, summarization, writing assistance |

### 1.3 Tipi di Vista

| Vista | Funzione |
|---|---|
| **List** | Vista predefinita, task in formato lista scansionabile |
| **Board** | Kanban drag-and-drop per status |
| **Calendar** | Pianificazione schedule e risorse |
| **Table** | Layout tabulare tipo spreadsheet |
| **Timeline** | Visualizzazione lineare schedule per roadmap |
| **Gantt** | Timeline progetto con milestones, export PDF |
| **Map** | Mapping geografico per task sul campo |
| **Workload** | Capacità team per giorno/settimana/mese con limiti |
| **Activity** | Storico modifiche, completamenti, cambi date |
| **Team** | Workload individuale, completamenti, capacità |
| **Mind Map** | Brainstorming gerarchico (free-form o task-based) |
| **Chat** | Messaggistica e collaborazione AI-assisted |
| **Docs/Whiteboards** | Documenti collaborativi e canvas |

---

## 2. ClickUp Brain (AI)

### 2.1 Funzionalità AI

| Feature | Dettaglio |
|---|---|
| **Task Generation** | Creazione task AI-generated da contesto, estrazione action items da discussioni |
| **Subtask Generation** | Genera subtask automaticamente dal nome del task |
| **Smart Summarization** | Riassumi Docs, task threads, aggiornamenti, Inbox, commenti |
| **AI Custom Fields** | Campi AI-generated che standardizzano informazioni task |
| **Smart Reply** | Risposte contestuali generate da AI |
| **Writing Assistance** | Assistenza scrittura per docs, commenti, descrizioni |
| **Activity Summary** | Riassunto attività per Spaces, Folders, Lists |
| **AI Agents** | Task Summarizer Agent, Business Copilot Agent |

### 2.2 Contesto AI

- Legge descrizioni task, thread commenti, progresso subtask, documenti collegati
- Fornisce riassunti su: stato attuale, decisioni chiave, azioni in sospeso, blocchi, cambi scope
- Supporto mobile per summarization
- Disponibile su tutti i piani (add-on)

---

## 3. Automazioni

### 3.1 Struttura Automazione

| Componente | Funzione |
|---|---|
| **Triggers** | Evento che avvia l'automazione (task creato, status cambiato, data raggiunta, etc.) |
| **Conditions** | Criteri opzionali che devono essere veri (Business Plan+) |
| **Actions** | Eventi eseguiti dopo il trigger (esecuzione top-to-bottom, riordinabili) |

### 3.2 Tipi di Automazione

| Tipo | Dettaglio |
|---|---|
| **Task Automations** | Per eventi su task/subtask (creazione, update, status change, assignee change) |
| **Chat Automations** | Trigger "Message is posted" |
| **Webhook Automations** | Invio dati a sistemi esterni su update task o messaggi chat |

### 3.3 Trigger Disponibili

- Task creato / aggiornato / eliminato
- Status cambiato
- Assignee cambiato
- Priorità cambiata
- Due date raggiunta / cambiata
- Custom field cambiato
- Tag aggiunto / rimosso
- Checklist completata
- Commento aggiunto
- Task spostato
- Subtask creato

### 3.4 Azioni Disponibili

- Cambia status / priorità / assignee
- Aggiungi / rimuovi tag
- Aggiungi commento
- Crea task / subtask
- Sposta task
- Applica template
- Invia webhook
- Invia email / notifica
- Aggiorna custom field

### 3.5 Esempi per Vendiamonoi.it

```
Automazione 1: Nuovo Fornitore
  QUANDO: Task creato in List "Onboarding Fornitori"
  ALLORA: Assegna a Reparto Fornitori → Crea subtask checklist documenti

Automazione 2: Catalogo Pronto
  QUANDO: Status = "Catalogo Completo" in Space Catalogo
  ALLORA: Crea task "Pubblica su Marketplace" → Assegna a Reparto Marketplace

Automazione 3: Ordine Problematico
  QUANDO: Tag "Problema" aggiunto
  ALLORA: Priorità → Urgente → Assegna a Team Leader → Notifica Slack

Automazione 4: Sprint Completato
  QUANDO: Tutti i task in sprint sono "Done"
  ALLORA: Notifica → Crea report → Sposta incompleti al prossimo sprint

Automazione 5: Webhook eDesk
  QUANDO: Webhook ricevuto (ticket eDesk escalation)
  ALLORA: Crea task in List "Escalation CS" con dati ticket
```

---

## 4. Docs e Wiki

### 4.1 ClickUp Docs

| Feature | Dettaglio |
|---|---|
| **Collaborazione** | Real-time con cursori live e commenti inline |
| **Nested Pages** | Pagine gerarchiche con drag-and-drop |
| **Versioning** | Storico versioni completo |
| **Task Integration** | Converti testo in task tracciabili |
| **Tagging** | Tag collaboratori con action items |
| **Commenti** | Thread commenti risolti e inline |
| **Relazioni** | Collega task e docs con page links |

### 4.2 Wiki

| Feature | Dettaglio |
|---|---|
| **Funzione** | Knowledge base centralizzata con documenti connessi |
| **Contenuto** | Processi, onboarding, prodotti, policy interne |
| **Free/Business** | 1 wiki per Workspace |
| **Business Plus** | Wiki illimitati |

---

## 5. Forms

### 5.1 Form Builder

| Feature | Dettaglio |
|---|---|
| **Builder** | Drag-and-drop |
| **Campi** | Dropdown, checkbox, date picker, campi task built-in, tutti i custom field types |
| **Logica** | Campi nascosti, logica condizionale |
| **Design** | Layout, tema, sfondo, colori bottoni, cover images |
| **Condivisione** | Link, embed su pagine web, export risposte |

### 5.2 Automazioni da Forms

- Auto-genera task da ogni submission
- Imposta assignee, due date, priorità, allegati
- Auto-assign basato su campo categoria
- Due date basata su tempo submission o opzione selezionata

---

## 6. Team Management

### 6.1 Ruoli Utente

| Ruolo | Tipo | Permessi |
|---|---|---|
| **Owner** | Member | Proprietà e gestione completa workspace |
| **Admin** | Member | Permessi extra per gestione workspace |
| **Member** | Member | Collaborazione su task e team cross-funzionali |
| **Limited Member** | Member | Solo accesso a Chat e lavoro assegnato |
| **Limited Member - View Only** | Member | Solo visualizzazione elementi assegnati |
| **View Only Guest** | Guest | Accesso esterno solo in lettura |
| **Permission-Controlled Guest** | Guest | Accesso esterno con permessi specifici |

### 6.2 Permessi

| Livello | Dettaglio |
|---|---|
| **Default** | Members, owners, admins hanno full edit su items pubblici |
| **Granulari** | Admins/owners possono modificare permessi individuali (time estimates, tracking, tag, viste) |
| **Custom Roles** | Business Plus+ — ruoli personalizzati con permessi specifici |
| **Enterprise** | Ruoli custom illimitati |

### 6.3 Collaborazione

| Feature | Dettaglio |
|---|---|
| **@mention** | Tag colleghi nei commenti e task |
| **Assegnazione multipla** | Più assignee per task |
| **Watchers** | Osservatori notificati su cambi |
| **Internal Notes** | Commenti interni |
| **Guest Access** | Invito esterni con permessi controllati |

---

## 7. API v2

### 7.1 Panoramica

| Aspetto | Dettaglio |
|---|---|
| **Base URL** | `https://api.clickup.com/api/v2` |
| **Formato** | JSON |
| **Versioni** | v2 (stabile), v3 (disponibile) |
| **Documentazione** | [https://developer.clickup.com/reference](https://developer.clickup.com/reference) |
| **Postman** | Collection pubblica disponibile |

### 7.2 Autenticazione

**Personal API Token:**
```bash
# Token generato da Settings → Apps → API Token
# Formato: pk_xxxxxxxxx
Authorization: {personal_token}
Content-Type: application/json
```

**OAuth 2.0:**
```bash
# Step 1: Authorization URL
https://app.clickup.com/api?client_id={client_id}&redirect_uri={redirect_uri}&state={state}

# Step 2: Token Exchange
POST https://api.clickup.com/api/v2/oauth/token
{
  "client_id": "{client_id}",
  "client_secret": "{client_secret}",
  "code": "{authorization_code}"
}

# Step 3: Header
Authorization: Bearer {access_token}
```

**Nota:** Access token OAuth attualmente non scade. Solo Owner/Admin possono creare OAuth apps.

### 7.3 Rate Limits

| Piano | Richieste/Minuto |
|---|---|
| **Free Forever** | 100 |
| **Unlimited** | 100 |
| **Business** | 100 |
| **Business Plus** | 1.000 |
| **Enterprise** | 10.000 |

**Headers:** `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`
**Superamento:** HTTP 429

### 7.4 Endpoints Principali

#### Workspaces/Teams

```bash
# Get workspace (team = workspace in v2)
GET /api/v2/team/{team_id}

# Get authenticated user
GET /api/v2/user
```

#### Spaces

```bash
# Get space
GET /api/v2/space/{space_id}

# Create space
POST /api/v2/team/{team_id}/space

# Update space
PUT /api/v2/space/{space_id}

# Delete space
DELETE /api/v2/space/{space_id}
```

#### Folders

```bash
# Get folder
GET /api/v2/folder/{folder_id}

# Create folder
POST /api/v2/space/{space_id}/folder

# Update folder
PUT /api/v2/folder/{folder_id}

# Delete folder
DELETE /api/v2/folder/{folder_id}
```

#### Lists

```bash
# Get list
GET /api/v2/list/{list_id}

# Get lists in folder
GET /api/v2/folder/{folder_id}/list

# Create list
POST /api/v2/folder/{folder_id}/list

# Update list
PUT /api/v2/list/{list_id}

# Delete list
DELETE /api/v2/list/{list_id}

# Get list members
GET /api/v2/list/{list_id}/member
```

#### Tasks

```bash
# Get task
GET /api/v2/task/{task_id}

# Get tasks in list (max 100/page)
GET /api/v2/list/{list_id}/task

# Filtered team tasks (search globale)
GET /api/v2/team/{team_id}/task
  ?assignees[]=user_id
  &statuses[]=status
  &include_closed=true
  &due_date_gt=timestamp
  &due_date_lt=timestamp
  &subtasks=true

# Create task
POST /api/v2/list/{list_id}/task

# Create subtask (set parent in body)
POST /api/v2/list/{list_id}/task
  { "parent": "parent_task_id", ... }

# Update task
PUT /api/v2/task/{task_id}

# Delete task
DELETE /api/v2/task/{task_id}
```

#### Comments

```bash
# Get task comments
GET /api/v2/task/{task_id}/comment

# Create comment
POST /api/v2/task/{task_id}/comment

# Update comment
PUT /api/v2/comment/{comment_id}

# Delete comment
DELETE /api/v2/comment/{comment_id}
```

#### Custom Fields

```bash
# Get list custom fields
GET /api/v2/list/{list_id}/field

# Get accessible custom fields
GET /api/v2/field

# Set custom field value
POST /api/v2/task/{task_id}/field/{field_id}
```

**Nota:** Aggiornare custom fields tramite endpoint Set, NON tramite Update Task.

#### Members

```bash
# Get workspace members
GET /api/v2/team/{team_id}/member

# Get task members
GET /api/v2/task/{task_id}/member

# Invite user
POST /api/v2/team/{team_id}/user

# Invite guest (Enterprise only)
POST /api/v2/team/{team_id}/guest
```

**User Roles:** 1 = Owner, 2 = Admin, 3 = Member, 4 = Guest

#### Tags

```bash
# Get space tags
GET /api/v2/space/{space_id}/tag

# Create space tag
POST /api/v2/space/{space_id}/tag

# Add tag to task
POST /api/v2/task/{task_id}/tag/{tag_name}

# Remove tag from task
DELETE /api/v2/task/{task_id}/tag/{tag_name}
```

#### Goals

```bash
# Get goals
GET /api/v2/team/{team_id}/goal

# Create goal
POST /api/v2/team/{team_id}/goal

# Update goal
PUT /api/v2/goal/{goal_id}

# Delete goal
DELETE /api/v2/goal/{goal_id}
```

#### Views

```bash
# Get views
GET /api/v2/team/{team_id}/view

# Create view
POST /api/v2/space/{space_id}/view

# Update view
PUT /api/v2/view/{view_id}

# Delete view
DELETE /api/v2/view/{view_id}
```

#### Webhooks

```bash
# Create webhook
POST /api/v2/team/{team_id}/webhook

# Get webhooks
GET /api/v2/team/{team_id}/webhook

# Update webhook
PUT /api/v2/webhook/{webhook_id}

# Delete webhook
DELETE /api/v2/webhook/{webhook_id}
```

#### Time Tracking

```bash
# Get time entries for task
GET /api/v2/task/{task_id}/time

# Get team time entries (con date range)
GET /api/v2/team/{team_id}/time_entries

# Add time entry
POST /api/v2/task/{task_id}/time

# Update time entry
PUT /api/v2/time_entry/{time_entry_id}

# Delete time entry
DELETE /api/v2/time_entry/{time_entry_id}

# Bulk time in status
GET /api/v2/task/bulk_time_in_status/task_ids
```

#### Checklists

```bash
# Create checklist
POST /api/v2/task/{task_id}/checklist

# Update checklist
PUT /api/v2/checklist/{checklist_id}

# Delete checklist
DELETE /api/v2/checklist/{checklist_id}

# Create checklist item
POST /api/v2/checklist/{checklist_id}/checklist_item

# Update checklist item
PUT /api/v2/checklist_item/{checklist_item_id}

# Delete checklist item
DELETE /api/v2/checklist_item/{checklist_item_id}
```

#### Dependencies

```bash
# Add dependency
POST /api/v2/task/{task_id}/dependency

# Remove dependency
DELETE /api/v2/task/{task_id}/dependency
```

**Tipi:** "Blocks" e "Blocked by" (waiting on)

### 7.5 Paginazione

- Parametro query: `page` (0-indexed)
- Risposta include: `last_page` (boolean)
- Default/Max: 100 items per pagina

### 7.6 Errori Comuni

| Codice | Significato |
|---|---|
| **429** | Rate limit superato |
| **401** | Token invalido o mancante |
| **403** | Permessi insufficienti |
| **404** | Risorsa non trovata |
| **OAUTH_023-045** | Errori OAuth (workspace non autorizzato, token revocato) |

---

## 8. Webhooks

### 8.1 Eventi Disponibili

| Evento | Descrizione |
|---|---|
| `taskCreated` | Task creato |
| `taskUpdated` | Task aggiornato |
| `taskDeleted` | Task eliminato |
| `taskStatusUpdated` | Status cambiato |
| `taskAssigneeUpdated` | Assignee cambiato |
| `taskCommentPosted` | Commento aggiunto |
| `listCreated` | Lista creata |
| `listUpdated` | Lista aggiornata |
| `listDeleted` | Lista eliminata |
| `folderCreated` | Folder creato |
| `folderUpdated` | Folder aggiornato |
| `folderDeleted` | Folder eliminato |
| `spaceCreated` | Space creato |
| `spaceUpdated` | Space aggiornato |
| `spaceDeleted` | Space eliminato |
| `goalCreated` | Goal creato |
| `goalUpdated` | Goal aggiornato |
| `goalDeleted` | Goal eliminato |
| `keyResultCreated` | Key Result creato |
| `keyResultUpdated` | Key Result aggiornato |
| `keyResultDeleted` | Key Result eliminato |

**Wildcard:** Usa `*` per sottoscrivere tutti gli eventi.

### 8.2 Payload Structure

```json
{
  "event": "taskUpdated",
  "webhook_id": "wh_xxxxxxxx",
  "history_items": [
    {
      "date": "1697500000000",
      "field": "status",
      "user": {
        "id": 123456,
        "username": "Alessio",
        "email": "user@example.com"
      },
      "before": {
        "status": "in progress",
        "type": "custom"
      },
      "after": {
        "status": "complete",
        "type": "closed"
      }
    }
  ]
}
```

### 8.3 Sicurezza Webhook

- Ogni webhook riceve un **shared secret** unico
- Tutti gli eventi sono **firmati** con questo secret
- Verifica firma per garantire autenticità dell'origine ClickUp

---

## 9. Reporting e Dashboards

### 9.1 Dashboard

| Feature | Dettaglio |
|---|---|
| **Widget** | Charts, tabelle, workload, time tracking, calcoli, goal progress |
| **Click-through** | Riassegna, aggiorna, chiudi task direttamente da card |
| **Layout** | Drag, resize, arrangiamento libero card |
| **Export** | PDF per condivisione |
| **Dati** | Tasks, tempo, sprint, goals, dati custom |

### 9.2 Reporting Capabilities

| Report | Dettaglio |
|---|---|
| **Sprint** | Burndown/burnup charts, velocity, story points |
| **Time Tracking** | Ore per task/utente/periodo, billable vs non-billable |
| **Workload** | Capacità team, distribuzione carico |
| **Goals/OKR** | Progresso obiettivi, key results |
| **Custom** | Widget personalizzabili con calcoli e formule |

---

## 10. Time Tracking

### 10.1 Tracking Nativo

| Feature | Dettaglio |
|---|---|
| **Timer** | Start/Stop timer integrato |
| **Manual Entry** | Inserimento manuale ore |
| **Billable** | Marcatura ore come fatturabili/non fatturabili |
| **Timesheet** | Vista timesheet per workspace |
| **Dashboard** | Report time tracking su dashboard |
| **Colonna** | Colonna "time tracked" in List/Table views |

### 10.2 Integrazioni Time Tracking

| Tool | Dettaglio |
|---|---|
| **Everhour** | Migliore integrazione, tracking automatico, profondità |
| **Harvest** | Time tracking + invoicing, start/stop timer, reporting |
| **Clockify** | Calcolo automatico ore billable |
| **Timely** | Report live progetto, generazione fatture |
| **Toggl** | Time tracking popolare con sincronizzazione |

---

## 11. Pricing e Piani

### 11.1 Piani

| Piano | Prezzo | Storage | Note |
|---|---|---|---|
| **Free Forever** | $0 | 60 MB | Utenti illimitati, task illimitati, 1 wiki |
| **Unlimited** | $7/utente/mese | Illimitato | Tutte le feature Free + storage illimitato |
| **Business** | $12/utente/mese | Illimitato | + Guest seats, condizioni automazioni |
| **Business Plus** | $19/utente/mese | Illimitato | + Custom roles, wiki illimitati, priority support |
| **Enterprise** | Custom | Illimitato | + SSO, branding custom, ruoli illimitati, support dedicato |

### 11.2 Rate Limits API per Piano

| Piano | Richieste/Minuto |
|---|---|
| Free/Unlimited/Business | 100 |
| Business Plus | 1.000 |
| Enterprise | 10.000 |

### 11.3 Note

- Fatturazione annuale per prezzi migliori
- Free Forever include task e utenti illimitati
- Automazioni con condizioni richiedono Business+
- Custom roles richiedono Business Plus+
- SSO richiede Enterprise

**Per Vendiamonoi.it:** piano **Business** ($12/utente/mese) per automazioni con condizioni e guest seats, o **Business Plus** ($19/utente/mese) per custom roles e wiki illimitati.

---

## 12. Integrazioni

### 12.1 Integrazioni Native

| Integrazione | Funzione |
|---|---|
| **Slack** | Notifiche task in chat, creazione task da messaggi |
| **Google Workspace** | Gmail, Drive, Calendar, Docs, Sheets |
| **Microsoft 365** | Outlook, OneDrive |
| **GitHub** | Commits, branches, pull requests linking |
| **GitLab** | Repository integration |
| **Zoom** | Meeting scheduling da task |
| **HubSpot** | CRM integration |
| **Jira** | Project management sync |

### 12.2 Integrazione Make.com

| Modulo | Funzione |
|---|---|
| **Create Task** | Crea task con dettagli custom |
| **Update Task** | Aggiorna proprietà task esistente |
| **Watch Tasks** | Trigger su creazione/update task |
| **Add Tags** | Gestione tag automatica |
| **Time Tracking** | Tracking ore programmatico |
| **Dependencies** | Imposta dipendenze task |
| **Checklists** | Gestione checklist task |

### 12.3 Pattern Integrazione per Vendiamonoi.it

```
ClickUp ←→ Make.com:
  • Watch Tasks (status change) → Make → Supabase (sync stato)
  • Make schedule → ClickUp API (report task) → Google Sheets
  • Webhook ClickUp → Make → Notifica Slack/Email

ClickUp ←→ Supabase:
  • Nuovo fornitore in Supabase → Make → ClickUp: crea task onboarding
  • Ordine problematico in Supabase → Make → ClickUp: crea task escalation

ClickUp ←→ eDesk:
  • eDesk webhook (escalation) → Make → ClickUp: crea task CS
  • Task ClickUp completato → Make → eDesk: nota interna aggiornamento

ClickUp ←→ Fatture in Cloud:
  • Task "Emetti fattura" completato → Make → FiC: genera fattura
  • Fattura emessa → Make → ClickUp: aggiorna task con link fattura

ClickUp ←→ Google Sheets:
  • Export settimanale task → Google Sheets (report direzionale)
  • KPI marketplace da Sheets → ClickUp Dashboard widget
```

---

## 13. Sprints e Agile

### 13.1 Sprint Features

| Feature | Dettaglio |
|---|---|
| **Sprint Planning** | Date sprint, story points, priorità |
| **Point System** | Completamente personalizzabile |
| **Rollup** | Punti da subtask aggregati |
| **Per Assignee** | Punti suddivisi per assegnatario |
| **Auto-Complete** | Sprint auto-completato a end date |
| **Auto-Move** | Incompleti spostati al prossimo sprint |
| **Auto-Create** | Nuovo sprint creato automaticamente dopo completamento |

### 13.2 Visualizzazione

| Vista | Funzione |
|---|---|
| **Board** | Kanban per sprint (To Do, In Progress, Done) |
| **Burndown** | Pace vs target |
| **Burnup** | Lavoro completato + scope rimanente |
| **Velocity** | Punti completati per sprint |

### 13.3 Integrazioni Dev

- Sync con GitHub, GitLab, Bitbucket
- Linking commits, branches, pull requests a task sprint

---

## 14. Sicurezza e Compliance

| Aspetto | Dettaglio |
|---|---|
| **SOC 2 Type II** | Certificato, audit annuale indipendente |
| **ISO 27001:2022** | Information Security Management |
| **ISO 27017:2015** | Cloud Security |
| **ISO 27018:2019** | PII Protection in Cloud |
| **GDPR** | Piena conformità |
| **CCPA** | Conforme |
| **HIPAA** | Disponibile su richiesta |
| **Crittografia in transito** | TLS 1.2 |
| **Crittografia at rest** | AES-256 |
| **SSO** | Okta, Google, Microsoft Azure (Enterprise) |
| **2FA** | Autenticazione a due fattori |
| **RBAC** | Role-Based Access Control granulare |

---

## 15. Troubleshooting

### 15.1 Errori Comuni

| Errore | Causa | Soluzione |
|---|---|---|
| API 429 | Rate limit superato | Ridurre frequenza richieste, verificare piano (Free = 100/min) |
| API 401 | Token invalido | Rigenerare token da Settings > Apps > API Token |
| Webhook non ricevuto | URL non raggiungibile | Verificare URL target, controllare firewall, testare con webhook.site |
| Automazione non scatta | Condizioni non soddisfatte | Verificare trigger/condizioni (condizioni richiedono Business+) |
| Custom fields non aggiornati via API | Endpoint errato | Usare POST /task/{id}/field/{field_id}, NON PUT /task/{id} |
| Subtask non visibili | Parametro mancante | Aggiungere ?subtasks=true nelle query GET |
| Guest non può accedere | Permessi non assegnati | Invitare guest a location specifica con permessi corretti |

### 15.2 Best Practice

- **Gerarchia pulita** — Workspace > Spaces per reparto > Folders per area > Lists per progetto
- **Custom Fields consistenti** — Usare naming convention (MKP_, FRN_, ORD_) per campi marketplace
- **Automazioni con condizioni** — Piano Business+ per condizioni granulari
- **Webhook + Make** — Preferire webhook ClickUp → Make per integrazioni complesse
- **Rate limiting** — Implementare exponential backoff per integrazioni API intensive
- **Custom Task Types** — Standardizzare tipi task per reparto (Onboarding, Pubblicazione, Reso, etc.)
- **Sprint settimanali** — Cicli brevi per adattarsi rapidamente ai cambi marketplace
- **Dashboard per direzione** — Report visuale con KPI aggregati per decisioni rapide

---

## 16. Risorse e Riferimenti

| Risorsa | URL |
|---|---|
| Sito ufficiale | https://clickup.com |
| Pricing | https://clickup.com/pricing |
| Developer Portal | https://developer.clickup.com |
| API Reference v2 | https://developer.clickup.com/reference |
| Authentication | https://developer.clickup.com/docs/authentication |
| Rate Limits | https://developer.clickup.com/docs/rate-limits |
| Webhooks | https://developer.clickup.com/docs/webhooks |
| Webhook Payloads | https://developer.clickup.com/docs/webhooktaskpayloads |
| Common Errors | https://developer.clickup.com/docs/common_errors |
| Help Center | https://help.clickup.com |
| Core Features | https://help.clickup.com/hc/en-us/articles/6311563319063 |
| Hierarchy | https://help.clickup.com/hc/en-us/articles/13856392825367 |
| Views | https://help.clickup.com/hc/en-us/articles/6329880717719 |
| Automations | https://help.clickup.com/hc/en-us/articles/6312102752791 |
| Docs | https://help.clickup.com/hc/en-us/articles/6328174371351 |
| Forms | https://help.clickup.com/hc/en-us/articles/6310233090711 |
| Time Tracking | https://help.clickup.com/hc/en-us/articles/6304291811479 |
| Permissions | https://help.clickup.com/hc/en-us/articles/6309225399703 |
| Security | https://clickup.com/security |
| Integrations | https://clickup.com/integrations |
| Make.com Integration | https://www.make.com/en/integrations/clickup |
| Sprints | https://clickup.com/features/sprints |
| Dashboards | https://clickup.com/features/dashboards |
| ClickUp Brain | https://help.clickup.com/hc/en-us/articles/12578085238039 |
| Postman Collection | https://www.postman.com/clickup-api/clickup-public-api |

---

## Changelog

| Data | Versione | Modifica |
|---|---|---|
| 2026-04-01 | 1.0 | Creazione documentazione completa |
