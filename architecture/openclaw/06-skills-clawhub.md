# Settore 6 — Skills & ClawHub

> Guida completa al sistema di skill di OpenClaw, al registry ClawHub, ai comandi CLI per gestione skill, alla creazione di skill custom, e alle skill raccomandate per deployment aziendale.

---

## Indice

1. [Cos'è una Skill](#1-cosè-una-skill)
2. [Architettura di una Skill](#2-architettura-di-una-skill)
3. [SKILL.md — Il Cuore della Skill](#3-skillmd--il-cuore-della-skill)
4. [ClawHub — Il Registry](#4-clawhub--il-registry)
5. [Categorie e Numeri](#5-categorie-e-numeri)
6. [Top Skill per Categoria](#6-top-skill-per-categoria)
7. [Comandi CLI](#7-comandi-cli)
8. [Installazione e Gestione](#8-installazione-e-gestione)
9. [Creare una Skill Custom](#9-creare-una-skill-custom)
10. [Permessi e Sicurezza](#10-permessi-e-sicurezza)
11. [Pubblicazione su ClawHub](#11-pubblicazione-su-clawhub)
12. [Skill Raccomandate per Vendiamonoi](#12-skill-raccomandate-per-vendiamonoi)
13. [Fonti & Riferimenti](#13-fonti--riferimenti)

---

## 1. Cos'è una Skill

Una **skill** è un'estensione modulare che aggiunge capacità specifiche all'agente OpenClaw. Tecnicamente è una **cartella con un file SKILL.md** — il requisito minimo.

### Cosa può fare una skill

- Aggiungere nuove capacità (es. browsing web, gestione email)
- Definire workflow specifici (es. "come processare un ordine")
- Fornire conoscenza di dominio (es. "regole per listing su Amazon")
- Integrare strumenti esterni (es. API Shopify, Channable)
- Automatizzare task ripetitivi (es. traduzione cataloghi)

### Skill vs Tool vs MCP

| Componente | Cos'è | Esempio |
|---|---|---|
| **Skill** | Istruzioni + logica per l'agente | "Come gestire ordini marketplace" |
| **Tool** | Funzione eseguibile singola | `web_search`, `file_write` |
| **MCP Server** | Servizio esterno con più tool | Server Notion, server Shopify |

Le skill possono **usare** tool e MCP, ma sono di livello superiore: definiscono **quando e come** usarli.

---

## 2. Architettura di una Skill

### Struttura directory

```
~/.openclaw/skills/
├── web-search/
│   └── SKILL.md              # Unico file obbligatorio
├── image-processor/
│   ├── SKILL.md              # Configurazione e istruzioni
│   ├── scripts/
│   │   └── compress.py       # Script di supporto
│   └── references/
│       └── formats.md        # Documentazione di riferimento
├── marketplace-lister/
│   ├── SKILL.md
│   ├── templates/
│   │   ├── amazon.yaml       # Template listing Amazon
│   │   └── ebay.yaml         # Template listing eBay
│   └── data/
│       └── categories.json   # Mapping categorie
└── ...
```

### Ciclo di vita

```
Install → Load (all'avvio sessione) → Match (trigger description) → Execute → Update
```

1. **Install**: Download da ClawHub o creazione locale
2. **Load**: OpenClaw legge SKILL.md all'avvio
3. **Match**: La `description` nel frontmatter determina quando attivare la skill
4. **Execute**: Le istruzioni nel body guidano il comportamento dell'agente
5. **Update**: Aggiornamento manuale o automatico

---

## 3. SKILL.md — Il Cuore della Skill

### Struttura

```markdown
---
name: marketplace-lister
description: >
  Crea e ottimizza listing per marketplace europei.
  Supporta Amazon, eBay, Kaufland, Leroy Merlin.
version: 1.2.0
author: vendiamonoi
tags: [marketplace, listing, seo, e-commerce]
tools: [web_search, file_write, file_read]
env:
  - CHANNABLE_API_KEY
  - SHOPIFY_ACCESS_TOKEN
install:
  pip: [pandas, openpyxl]
  npm: [csv-parser]
---

# Marketplace Lister

## Quando usare questa skill
Usa questa skill quando l'utente chiede di:
- Creare un nuovo listing per un marketplace
- Ottimizzare titoli SEO per marketplace
- Generare feed prodotti
- Tradurre schede prodotto

## Workflow

### 1. Raccolta dati prodotto
1. Chiedi EAN, titolo, descrizione, prezzo
2. Cerca il prodotto nel catalogo (memory/fornitori.md)
3. Verifica completezza dati

### 2. Ottimizzazione SEO
1. Analizza keyword per il marketplace target
2. Genera titolo ottimizzato (max 200 caratteri)
3. Crea bullet point (5 punti, max 500 char ciascuno)
4. Scrivi descrizione HTML

### 3. Generazione feed
1. Seleziona template per marketplace
2. Mappa campi prodotto al formato richiesto
3. Genera file CSV/XML
4. Valida il feed

## Regole
- Sempre verificare EAN con check digit
- Mai pubblicare senza conferma utente
- Loggare ogni listing in memory/listings.md
```

### Campi frontmatter

| Campo | Obbligatorio | Funzione |
|---|---|---|
| `name` | Sì | Identificatore univoco |
| `description` | Sì | **Trigger di attivazione** — OpenClaw legge questo per decidere se attivare la skill |
| `version` | No | Versioning semantico |
| `author` | No | Chi l'ha creata |
| `tags` | No | Categorie per ricerca |
| `tools` | No | Tool richiesti dalla skill |
| `env` | No | Variabili d'ambiente necessarie |
| `install` | No | Dipendenze da installare |

**CRITICO**: La `description` non è marketing copy. È il **testo che OpenClaw usa per decidere se attivare la skill**. Deve essere precisa e descrivere esattamente quando la skill è rilevante.

---

## 4. ClawHub — Il Registry

### Overview

ClawHub è il registry pubblico di skill per OpenClaw.

| Dato | Valore (febbraio 2026) |
|---|---|
| Skill totali | 13.729 |
| Skill curate/verificate | 5.211 (38%) |
| Skill malevole scoperte | ~1.184 (vedi Settore 3) |
| Categorie | 31 |
| Publisher registrati | 4.500+ |

### Architettura tecnica ClawHub

| Componente | Tecnologia |
|---|---|
| Frontend | TanStack Start (React, Vite/Nitro) |
| Backend | Convex (DB + file storage + HTTP) |
| Auth | Convex Auth (GitHub OAuth) |
| Search | OpenAI embeddings + Convex vector search |
| CDN | Cloudflare |

### Moderation

- **Skill curate**: Revisionate manualmente dal team OpenClaw
- **Skill community**: Pubblicate da chiunque, non revisionate
- **Segnalazione**: Sistema di flag per skill sospette
- **Auto-scan**: Controllo automatico per pattern malevoli noti

---

## 5. Categorie e Numeri

### Distribuzione per macro-categoria

| Categoria | % del registry | Skill stimate |
|---|---|---|
| Storage & Data | 35.5% | ~4.870 |
| Web Automation | 28.2% | ~3.870 |
| DevOps & Coding | 22.1% | ~3.030 |
| Communication | 14.2% | ~1.950 |

### Top 31 categorie

| # | Categoria | Esempi |
|---|---|---|
| 1 | Productivity & Efficiency | Task management, note-taking, scheduling |
| 2 | Search & Research | Web search, academic search, data mining |
| 3 | Workflow Automation | Cron jobs, event triggers, pipelines |
| 4 | Communication | Email, messaging, notifications |
| 5 | Web Browsing | Page reading, scraping, form filling |
| 6 | File Management | Read, write, convert, organize |
| 7 | Code & Development | Code gen, review, refactoring, testing |
| 8 | Data Processing | CSV, JSON, XML, transformation |
| 9 | Image & Media | Generation, editing, OCR, compression |
| 10 | API Integration | REST, GraphQL, webhook management |
| 11 | Database | SQL, NoSQL, query, migration |
| 12 | Security & Auth | Encryption, scanning, credential mgmt |
| 13 | E-commerce | Listing, pricing, inventory, orders |
| 14 | SEO & Marketing | Keyword research, content optimization |
| 15 | Translation & Language | Multi-language, localization |
| 16 | Finance & Accounting | Invoice, reporting, reconciliation |
| 17 | Document Processing | PDF, DOCX, spreadsheet manipulation |
| 18 | Git & Version Control | Commits, PRs, branching, merge |
| 19 | Cloud & Infrastructure | AWS, GCP, Azure, Docker, K8s |
| 20 | Monitoring & Logging | Alerts, dashboards, log analysis |
| 21 | AI & ML | Model management, training, inference |
| 22 | Calendar & Scheduling | Events, reminders, availability |
| 23 | CRM & Sales | Contact management, pipeline, deals |
| 24 | Social Media | Posting, analytics, engagement |
| 25 | IoT & Hardware | Device control, sensor data |
| 26 | Voice & Audio | TTS, STT, audio processing |
| 27 | Gaming | Game logic, mod management |
| 28 | Education | Learning, quiz, curriculum |
| 29 | Healthcare | Medical data, HIPAA tools |
| 30 | Legal & Compliance | Contract review, regulatory |
| 31 | Miscellaneous | Everything else |

---

## 6. Top Skill per Categoria

### Le più installate (marzo 2026)

| Skill | Categoria | Download | Funzione |
|---|---|---|---|
| **Web Browsing** (official) | Web | 180.000+ | Navigazione web, lettura pagine |
| **Telegram** (official) | Communication | 145.000+ | Integrazione Telegram bot |
| **Capability Evolver** | Productivity | 35.000+ | Auto-miglioramento agente |
| **Memory Setup** | Productivity | 28.000+ | Configurazione memoria avanzata |
| **ClawRouter** | Optimization | 22.000+ | Smart model routing |
| **Email Manager** | Communication | 18.000+ | Gestione email IMAP/SMTP |
| **Git Assistant** | DevOps | 15.000+ | Git workflow automation |
| **Data Transformer** | Data | 12.000+ | Conversione CSV/JSON/XML |
| **SEO Optimizer** | Marketing | 10.000+ | Ottimizzazione titoli e contenuti |
| **Scheduler** | Automation | 9.500+ | Cron job e task scheduling |

### Skill rilevanti per e-commerce

| Skill | Funzione | Curata? |
|---|---|---|
| shopify-connector | Integrazione Shopify API | ✅ |
| csv-processor | Elaborazione cataloghi CSV | ✅ |
| product-enricher | Arricchimento dati prodotto | ✅ |
| price-monitor | Monitoraggio prezzi competitor | ⚠️ Community |
| marketplace-feed | Generazione feed marketplace | ⚠️ Community |
| translation-pro | Traduzione multi-lingua | ✅ |
| image-optimizer | Ottimizzazione immagini prodotto | ✅ |
| inventory-sync | Sincronizzazione inventario | ⚠️ Community |

---

## 7. Comandi CLI

### Ricerca e scoperta

```bash
# Cercare skill nel registry
openclaw skills search email
openclaw skills search "marketplace listing"
openclaw skills search --tag e-commerce
openclaw skills search --curated-only            # Solo skill verificate

# Info su una skill specifica
openclaw skills info web-browsing
# Output: nome, autore, versione, download, rating, permessi, dipendenze
```

### Installazione

```bash
# Installare una skill
openclaw skills install web-browsing

# Installare versione specifica (raccomandato per production)
openclaw skills install web-browsing@2.1.0

# Installare con dipendenze
openclaw skills install marketplace-feed --with-deps

# Dry run (mostra cosa farà senza farlo)
openclaw skills install marketplace-feed --dry-run

# Installare più skill
openclaw skills install web-browsing telegram email-manager

# Force reinstall
openclaw skills install web-browsing --force
```

### Gestione

```bash
# Elencare skill installate
openclaw skills list

# Skill effettivamente eseguibili (check ambiente)
openclaw skills list --eligible

# Aggiornare tutte le skill
openclaw skills update --all

# Aggiornare una skill specifica
openclaw skills update web-browsing

# Verificare stato skill
openclaw skills check

# Rimuovere una skill
openclaw skills remove price-monitor

# Informazioni dettagliate
openclaw skills info web-browsing --verbose
```

### Sicurezza

```bash
# Audit sicurezza di tutte le skill
openclaw security audit

# Ispezionare permessi di una skill
openclaw skills inspect marketplace-feed

# Scaricare senza installare (per review codice)
openclaw skills download --no-install marketplace-feed

# Monitorare attività di una skill
openclaw skills monitor marketplace-feed
```

---

## 8. Installazione e Gestione

### Best practice installazione

1. **Solo skill curate** in production: `--curated-only`
2. **Pinning versione**: Sempre specificare `@version` in production
3. **Review prima**: `--dry-run` e `inspect` prima di installare
4. **Dipendenze**: Usare `--with-deps` per installare tutto il necessario
5. **Aggiornamenti controllati**: `auto_update: false` in security.yaml

### Configurazione gestione skill

```yaml
# ~/.openclaw/config/skills.yaml
skills:
  auto_update: false                  # No aggiornamenti automatici
  allow_unverified: false              # Solo skill curate
  install_location: ~/.openclaw/skills
  pinned:
    web-browsing: 2.1.0               # Versione fissa
    telegram: 1.8.3
    csv-processor: 3.0.1
  blocked:
    - super-helper-pro                 # Skill bloccate
    - free-tokens-generator
```

---

## 9. Creare una Skill Custom

### Step 1: Inizializzare la struttura

```bash
# Usare il generatore built-in
openclaw skills init my-custom-skill

# Oppure creare manualmente
mkdir -p ~/.openclaw/skills/my-custom-skill
touch ~/.openclaw/skills/my-custom-skill/SKILL.md
```

### Step 2: Scrivere SKILL.md

```markdown
---
name: vendiamonoi-order-manager
description: >
  Gestisce ordini dai marketplace europei per Vendiamonoi.
  Verifica disponibilità, conferma ordini, richiede spedizioni,
  aggiorna tracking. Supporta Amazon, eBay, Kaufland, Carrefour.
version: 1.0.0
author: vendiamonoi
tags: [orders, marketplace, e-commerce, shipping]
tools: [web_search, file_read, file_write]
env:
  - CHANNELENGINE_API_KEY
---

# Vendiamonoi Order Manager

## Trigger
Attiva questa skill quando l'utente menziona:
- ordini, ordine nuovo, gestione ordini
- spedizione, tracking, conferma ordine
- marketplace + ordine

## Workflow: Nuovo Ordine

### 1. Ricezione
1. Identifica marketplace di provenienza
2. Estrai dati ordine (SKU, quantità, indirizzo)
3. Verifica in memory/fornitori.md chi è il fornitore

### 2. Verifica Disponibilità
1. Contatta fornitore (email o API)
2. Conferma disponibilità e tempi
3. Se non disponibile → proponi alternativa o cancella

### 3. Conferma e Spedizione
1. Conferma ordine su marketplace
2. Richiedi spedizione al fornitore
3. Attendi tracking number
4. Aggiorna tracking su marketplace

### 4. Post-vendita
1. Monitora consegna
2. Gestisci eventuali reclami
3. Logga risultato in memory/ordini.md

## Regole
- MAI confermare senza verifica disponibilità
- Tempi di risposta marketplace: entro 24h
- Escalare ad Alessio se ordine > €500
- Loggare OGNI ordine processato
```

### Step 3: Testare

```bash
# Verificare che la skill si carichi
openclaw skills list --eligible

# Testare in sessione
# Invia un messaggio che dovrebbe triggerare la skill
# Es: "Ho un nuovo ordine da Amazon, gestiscilo"
```

### Step 4: Iterare

- Aggiungere casi edge nei workflow
- Aggiungere file di supporto (template, dati di riferimento)
- Raffinare la `description` per trigger più precisi
- Aggiungere script di automazione in `scripts/`

---

## 10. Permessi e Sicurezza

### Modello di permessi

Le skill **non concedono permessi**. Se la policy dell'agente blocca `exec`, una skill che richiede shell commands si caricherà ma fallirà quando prova a eseguire.

### Dichiarazione tool

```yaml
# Nel frontmatter di SKILL.md
tools:
  - web_search       # Ricerca web
  - file_read        # Lettura file
  - file_write       # Scrittura file
  - shell_exec       # ⚠️ Esecuzione comandi — RED FLAG se non necessario
  - network          # ⚠️ Accesso rete — verificare domini
```

### Red flags nei permessi

| Permesso | Rischio | Quando è legittimo |
|---|---|---|
| `shell_exec` | Esecuzione arbitraria | Script Python/Node specifici |
| `network: ["*"]` | Accesso rete illimitato | Mai legittimo |
| `file_write: ["/"]` | Scrittura ovunque | Mai legittimo |
| `env: [PASSWORD, SECRET]` | Accesso credenziali | Solo se API richiede |

### Checklist sicurezza skill (vedi Settore 3)

1. Publisher verificato?
2. Permessi minimi?
3. Codice revisionato?
4. Testato in sandbox?

---

## 11. Pubblicazione su ClawHub

### Requisiti

- Account GitHub collegato a ClawHub
- SKILL.md con frontmatter completo
- README con istruzioni d'uso
- Nessuna credenziale hardcoded

### Comandi

```bash
# Pubblicare una skill
clawhub skill publish ~/.openclaw/skills/my-skill

# Sincronizzare aggiornamenti
clawhub sync ~/.openclaw/skills/my-skill

# Rinominare
clawhub skill rename old-slug new-slug

# Unire skill duplicate
clawhub skill merge source-skill target-skill
```

### Processo di curation

1. Pubblica su ClawHub (status: community)
2. Richiedi curation review
3. Team OpenClaw revisiona codice, permessi, qualità
4. Se approvata: status → curated (✅)
5. Skill curate appaiono in ricerche prioritarie

---

## 12. Skill Raccomandate per Vendiamonoi

### Setup iniziale (skill curate)

```bash
# Skill essenziali
openclaw skills install web-browsing@latest
openclaw skills install telegram@latest
openclaw skills install email-manager@latest
openclaw skills install csv-processor@latest
openclaw skills install data-transformer@latest
openclaw skills install translation-pro@latest

# Skill per e-commerce
openclaw skills install shopify-connector@latest
openclaw skills install image-optimizer@latest

# Skill per ottimizzazione
openclaw skills install clawrouter@latest
openclaw skills install memory-setup@latest
```

### Skill custom da creare per Vendiamonoi

| Skill | Funzione | Priorità |
|---|---|---|
| `vendiamonoi-order-manager` | Gestione ordini marketplace | Alta |
| `vendiamonoi-catalog-enricher` | Arricchimento cataloghi fornitori | Alta |
| `vendiamonoi-listing-optimizer` | Ottimizzazione listing SEO | Alta |
| `vendiamonoi-supplier-comms` | Comunicazione fornitori | Media |
| `vendiamonoi-price-manager` | Gestione prezzi e margini | Media |
| `vendiamonoi-returns-handler` | Gestione resi e reclami | Media |
| `vendiamonoi-analytics` | Report e KPI marketplace | Bassa |

### Configurazione skills.yaml per Vendiamonoi

```yaml
skills:
  auto_update: false
  allow_unverified: false
  pinned:
    web-browsing: 2.1.0
    telegram: 1.8.3
    email-manager: 1.5.0
    csv-processor: 3.0.1
    shopify-connector: 2.0.0
    clawrouter: 1.2.0
  blocked:
    - "*"                              # Blocca tutto non elencato
  custom_skills_path: ~/.openclaw/skills/vendiamonoi/
```

---

## 13. Fonti & Riferimenti

### Documentazione ufficiale
- Skills: https://docs.openclaw.ai/tools/skills
- Creating Skills: https://docs.openclaw.ai/tools/creating-skills
- ClawHub GitHub: https://github.com/openclaw/clawhub
- Skill Creator (built-in): https://github.com/openclaw/openclaw/blob/main/skills/skill-creator/SKILL.md

### Guide e tutorial
- DigitalOcean — What are Skills?: https://www.digitalocean.com/resources/articles/what-are-openclaw-skills
- LumaDock — Skills Guide: https://lumadock.com/tutorials/openclaw-skills-guide
- LumaDock — Build Custom Skills: https://lumadock.com/tutorials/build-custom-openclaw-skills
- DataCamp — Building Custom Skills: https://www.datacamp.com/tutorial/building-open-claw-skills
- Zen van Riel — Custom Skill Creation: https://zenvanriel.com/ai-engineer-blog/openclaw-custom-skill-creation-guide/

### Registry e ranking
- Awesome OpenClaw Skills (5,400+ curate): https://github.com/VoltAgent/awesome-openclaw-skills
- Top 10 Skills (Apiyi): https://help.apiyi.com/en/openclaw-skill-recommendations-2026-en.html
- Best Skills Ranked (TheGuidex): https://theguidex.com/best-openclaw-skills/
- Best ClawHub Skills Curated: https://openclawlaunch.com/blog/best-clawhub-skills-curated-list-2026

### Sicurezza skill
- Vedi Settore 3 (ClawHavoc, skill vetting)

---

> **Nota per Vendiamonoi**: Creare skill custom è il modo più potente per personalizzare OpenClaw. Iniziare con `vendiamonoi-order-manager` e `vendiamonoi-catalog-enricher` come prime skill custom. Usare Git per versionare le skill interne.

---

*Ultimo aggiornamento: aprile 2026*
*Documento parte della Master Class OpenClaw — vendiamonoi-knowledge*
