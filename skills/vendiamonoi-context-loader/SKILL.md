---
name: vendiamonoi-context-loader
description: >
  Caricatore automatico di contesto dalla knowledge base Vendiamonoi. ATTIVA OBBLIGATORIAMENTE questa skill ALL'INIZIO DI OGNI SESSIONE e prima di qualsiasi task operativo. Carica automaticamente i file rilevanti dal repository GitHub vendiamonoi-knowledge in base al tipo di task richiesto. MANDATORY TRIGGERS: inizio sessione, nuovo task, contesto Vendiamonoi, carica contesto, knowledge base, prima di lavorare, prepara contesto, carica informazioni, background knowledge, marketplace task, automazione task, pricing task, listing task, qualsiasi richiesta operativa su marketplace/fornitori/cataloghi/ordini/automazioni. Anche quando l'utente dice "lavoriamo su X", "devo fare Y", "aiutami con Z" dove X/Y/Z riguardano marketplace, fornitori, prodotti, automazioni, pricing, ordini, listing, customer service, analytics, Shopify, Make, n8n.
---

# Vendiamonoi Context Loader — Caricamento Intelligente Knowledge Base

Il tuo compito è caricare il contesto giusto dalla knowledge base prima di iniziare qualsiasi lavoro operativo. Vendiamonoi.it opera su 20-30+ marketplace europei con 100+ fornitori e milioni di SKU. Per dare risposte e soluzioni di livello world-class, hai bisogno delle informazioni giuste.

## Repository di Riferimento

- **Owner:** Alessiobaggiani
- **Repo:** vendiamonoi-knowledge
- **Branch:** main
- **Tool:** `mcp__github__get_file_contents`

## Logica di Caricamento Contesto

Analizza la richiesta dell'utente e determina quale contesto caricare. Segui questa matrice decisionale:

### Livello 1 — Sempre (ogni sessione)

Carica SEMPRE per primo:
```
strategy/master-checklist/README.md
```
Questo ti dà la mappa completa dei 1.000+ punti di competenza su 10 domini.

### Livello 2 — Per Tipo di Task

#### Se il task riguarda MAKE.COM o AUTOMAZIONI MAKE:
```
architecture/make/README.md
architecture/make/part1-core-architecture.md
architecture/make/part2-connections-routers-logic.md
architecture/make/part3-iml-datastores-webhooks.md
architecture/make/part4-errorhandling-performance-production.md
automation-flows/make-blueprints/README.md
```

#### Se il task riguarda N8N o AUTOMAZIONI N8N:
```
architecture/n8n/part1-core-architecture.md
architecture/n8n/part2-credentials-routing-expressions.md
architecture/n8n/part3-errorhandling-subworkflows-webhooks.md
architecture/n8n/part4-selfhosting-performance-production.md
automation-flows/n8n-workflows/README.md
```

#### Se il task riguarda AUTOMAZIONI CROSS-PLATFORM (Make + n8n):
```
automation-flows/cross-platform/README.md
```
Più i file Make e n8n rilevanti sopra.

#### Se il task riguarda un MARKETPLACE SPECIFICO:
```
marketplace-specs/<nome-marketplace>/README.md
```
Dove `<nome-marketplace>` corrisponde alla directory del marketplace. Mapping nomi comuni:
- Amazon → `amazon`
- eBay → `ebay`
- Kaufland → `kaufland`
- Bol.com → `bolcom`
- Carrefour → `carrefour`
- Leroy Merlin → `leroy-merlin`
- ManoMano → `manomano`
- MediaWorld → `mediaworld`
- METRO → `metro-italia` o `metro-markets`
- Conrad → `conrad`
- Shein → `shein`
- Temu → `temu`
- Cdiscount → `cdiscount`
- ePrice → `eprice`
- IBS → `ibs`
- BricoBravo → `bricobravo`
- Rue du Commerce → `rue-du-commerce`
- Vente-Unique → `vente-unique`
- Mirakl → `mirakl-marketplaces`

#### Se il task riguarda COMPARAZIONE MARKETPLACE o STRATEGIA:
```
research/marketplace-comparison/README.md
research/automation-tools-benchmark/README.md
strategy/master-checklist/README.md
```

#### Se il task riguarda DESIGN PIATTAFORMA o ARCHITETTURA APP:
```
app-design/requirements/README.md
app-design/architecture/README.md
```

#### Se il task riguarda SHOPIFY:
```
architecture/shopify/README.md
```

#### Se il task riguarda INTEGRAZIONE API o PATTERN:
```
integrations/api-design-patterns/README.md
```

#### Se il task riguarda DATI PRODOTTO, CATALOGO, PIM:
```
data-models/product-information/README.md
```

#### Se il task riguarda SUPABASE o DATABASE:
```
architecture/supabase/README.md
```

#### Se il task riguarda CHANNABLE o FEED MANAGEMENT:
```
architecture/channable/README.md
```

#### Se il task riguarda CHANNELENGINE o CHANNEL MANAGEMENT:
```
architecture/channelengine/README.md
```

#### Se il task riguarda MIRAKL (piattaforma, non singolo marketplace):
```
architecture/mirakl/README.md
```

### Livello 3 — Contesto Multiplo

Per task complessi che attraversano più domini, combina i contesti. Esempi:

- **"Integra nuovo fornitore su Amazon e Kaufland"** → Carica: marketplace-specs/amazon, marketplace-specs/kaufland, automation-flows/make-blueprints, data-models/product-information
- **"Ottimizza pricing su tutti i marketplace Mirakl"** → Carica: marketplace-specs/mirakl-marketplaces + ogni marketplace Mirakl specifico + strategy/master-checklist (sezione pricing)
- **"Crea automazione ordini Make→n8n"** → Carica: tutti i file Make + n8n + cross-platform

## Procedura Operativa

1. **Analizza** la richiesta dell'utente
2. **Identifica** i domini coinvolti (usa la matrice sopra)
3. **Carica** i file in ordine di priorità (strategy → architecture → specs → flows)
4. **Conferma** all'utente quali contesti hai caricato
5. **Procedi** con il task avendo il contesto completo

## Regole Importanti

- NON caricare file che non servono per il task (evita overhead inutile)
- Se un file è troppo grande, leggine le sezioni rilevanti
- Se non sei sicuro di quale contesto serve, carica la Master Checklist e chiedi
- Dopo aver caricato il contesto, integra le informazioni nel tuo ragionamento
- Cita le fonti quando usi informazioni specifiche dalla knowledge base
- Se manca informazione nella knowledge base, segnalalo come gap da colmare
