---
name: vendiamonoi-context-loader
description: >
  Caricatore automatico di contesto dalla knowledge base Vendiamonoi. ATTIVA OBBLIGATORIAMENTE questa skill ALL'INIZIO DI OGNI SESSIONE e prima di qualsiasi task operativo. Carica automaticamente i file rilevanti dal repository GitHub vendiamonoi-knowledge in base al tipo di task richiesto. MANDATORY TRIGGERS: inizio sessione, nuovo task, contesto Vendiamonoi, carica contesto, knowledge base, prima di lavorare, prepara contesto, carica informazioni, background knowledge, marketplace task, automazione task, pricing task, listing task, qualsiasi richiesta operativa su marketplace/fornitori/cataloghi/ordini/automazioni. Anche quando l'utente dice "lavoriamo su X", "devo fare Y", "aiutami con Z" dove X/Y/Z riguardano marketplace, fornitori, prodotti, automazioni, pricing, ordini, listing, customer service, analytics, Shopify, Make, n8n.
---

# Vendiamonoi Context Loader v2.0 — Standup + Caricamento Intelligente

Il tuo compito è eseguire lo STANDUP e caricare il contesto giusto dalla knowledge base prima di iniziare qualsiasi lavoro operativo.

## FASE 1 — STANDUP (OBBLIGATORIO, sempre per primo)

### Step 1: Leggi STATUS.md

```
Repository: Alessiobaggiani/vendiamonoi-knowledge
File: STATUS.md
Tool: mcp__github__get_file_contents
```

STATUS.md contiene:
- **Stato attuale del progetto** — dove siamo
- **Thread aperti** — task non completati dalla sessione precedente
- **Decisioni recenti** — decisioni prese di recente
- **Prossimi step** — cosa è stato raccomandato come prossimo
- **Log sessioni** — storia delle ultime sessioni
- **Session metadata** (commento HTML) — dati machine-readable per anomaly detection

### Step 2: Controlla Staleness

Leggi il campo `last_updated` nel metadata HTML di STATUS.md:
- Se `staleness_days > 7` → Segnala all'utente: "STATUS.md non aggiornato da X giorni"
- Se `open_threads > 0` → Elenca i thread aperti e chiedi priorità

### Step 3: Presenta lo Stato

Comunica all'utente in formato compatto:
```
📊 Stato: [ultimo aggiornamento] | Thread aperti: [N] | Focus precedente: [descrizione]
📌 Prossimo step suggerito: [dal STATUS.md]
```

Solo se rilevante, non se l'utente ha già dato un task chiaro.

## FASE 2 — CARICAMENTO CONTESTO SELETTIVO

### Principio: Carica SOLO ciò che serve

Budget token target: <25K di contesto caricato per sessione.
MAI caricare tutto il repository. MAI caricare file "per sicurezza".

### Repository di Riferimento

- **Owner:** Alessiobaggiani
- **Repo:** vendiamonoi-knowledge
- **Branch:** main
- **Tool:** `mcp__github__get_file_contents`

### Matrice Decisionale per Contesto

Analizza la richiesta dell'utente e carica SOLO i file mappati:

#### Livello 1 — Sempre (ogni sessione)
```
STATUS.md                              (~500 token)
```

#### Livello 2 — Per Tipo di Task

**MAKE.COM / AUTOMAZIONI MAKE:**
```
architecture/make/README.md
architecture/make/part1-core-architecture.md        (se serve dettaglio)
architecture/make/part2-connections-routers-logic.md (se serve dettaglio)
architecture/make/part3-iml-datastores-webhooks.md   (se serve dettaglio)
architecture/make/part4-errorhandling-performance-production.md (se serve dettaglio)
automation-flows/make-blueprints/README.md
```

**N8N / AUTOMAZIONI N8N:**
```
architecture/n8n/part1-core-architecture.md
architecture/n8n/part2-credentials-routing-expressions.md
architecture/n8n/part3-errorhandling-subworkflows-webhooks.md
architecture/n8n/part4-selfhosting-performance-production.md
automation-flows/n8n-workflows/README.md
```

**CROSS-PLATFORM (Make + n8n):**
```
automation-flows/cross-platform/README.md
```

**MARKETPLACE SPECIFICO:**
```
marketplace-specs/<nome-marketplace>/README.md
```
Mapping: Amazon→amazon, eBay→ebay, Kaufland→kaufland, Bol.com→bolcom, Carrefour→carrefour, Leroy Merlin→leroy-merlin, ManoMano→manomano, MediaWorld→mediaworld, METRO→metro-italia/metro-markets, Conrad→conrad, Shein→shein, Temu→temu, Cdiscount→cdiscount, ePrice→eprice, IBS→ibs, BricoBravo→bricobravo, Rue du Commerce→rue-du-commerce, Vente-Unique→vente-unique, Mirakl→mirakl-marketplaces

**COMPARAZIONE / STRATEGIA:**
```
research/marketplace-comparison/README.md
research/automation-tools-benchmark/README.md
```

**DESIGN PIATTAFORMA / APP:**
```
app-design/requirements/README.md
app-design/architecture/README.md
```

**SUPABASE / DATABASE:**
```
architecture/supabase/README.md
data-models/product-information/README.md
```

**SHOPIFY:** `architecture/shopify/README.md`
**CHANNABLE / FEED:** `architecture/channable/README.md`
**CHANNELENGINE:** `architecture/channelengine/README.md`
**PRICING:** `pricing-strategy/<sottocartella>/README.md`
**CUSTOMER SERVICE:** `customer-service/<sottocartella>/README.md`
**FINANCE:** `financial-management/<sottocartella>/README.md` + `architecture/fatture-in-cloud/README.md`
**SUPPLY CHAIN:** `supply-chain/<sottocartella>/README.md`
**ADVERTISING:** `advertising-ppc/<sottocartella>/README.md`
**SOFTWARE SPECIFICO:** `api-docs/<nome-software>/README.md`

### Livello 3 — Contesto Multiplo

Per task complessi, combina i contesti. Esempi:
- "Integra fornitore su Amazon e Kaufland" → marketplace-specs/amazon + kaufland + data-models
- "Automazione ordini Make→Supabase" → architecture/make + architecture/supabase + automation-flows

## FASE 3 — ANTI-DUPLICAZIONE (CRITICO)

**PRIMA di qualsiasi ricerca o creazione contenuto:**

1. Controlla se esiste già su GitHub: `mcp__github__get_file_contents` → directory rilevante
2. Se esiste → NON ricreare. Usa quello che c'è.
3. Se manca → procedi con la creazione.
4. Se incompleto → arricchisci, non ricominciare.

**REGOLA INVIOLABILE. L'utente si è arrabbiato per duplicazioni in passato.**

## Procedura Operativa

1. **Leggi STATUS.md** → stato del progetto
2. **Analizza** la richiesta
3. **Identifica** domini coinvolti
4. **Verifica anti-duplicazione** se task prevede creazione
5. **Carica** file per priorità (STATUS → architecture → specs → flows)
6. **Conferma** all'utente in formato compatto
7. **Procedi** con il task

## Regole

- Budget <25K token di contesto
- Se file troppo grande → leggi solo sezioni rilevanti
- SEMPRE controllare GitHub prima di creare
- Usa subagent per investigazioni pesanti
- Se manca info → segnala come gap
