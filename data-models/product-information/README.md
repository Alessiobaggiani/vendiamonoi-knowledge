# Data Model — Product Information Management
## EXPERT-LEVEL TECHNICAL DEEP-DIVE

**Documento**: Modello dati completo per la gestione delle informazioni prodotto in un sistema di distribuzione digitale multi-marketplace
**Target**: CTO, Data Architects, Senior Engineers — Vendiamonoi.it
**Versione**: 2026
**Ultimo aggiornamento**: 2026-04-06
**Complemento a**: architecture/make/, architecture/n8n/, architecture/sellrapido/, architecture/supabase/

---

## INDICE COMPLETO

1. [Identificazione Prodotto — Standard Globali](#1-identificazione-prodotto--standard-globali)
   - 1.1 GS1 / GTIN / EAN
   - 1.2 Algoritmo Check Digit
   - 1.3 Identificatori per Marketplace (ASIN, ePID, BPID)
   - 1.4 MPN — Manufacturer Part Number
   - 1.5 SKU — Convenzioni Multi-Marketplace
   - 1.6 Brand Name Management
2. [Tassonomia e Categorizzazione](#2-tassonomia-e-categorizzazione)
   - 2.1 GPC — Global Product Classification
   - 2.2 Amazon Browse Node
   - 2.3 eBay Category Tree
   - 2.4 Google Product Category
   - 2.5 UNSPSC
   - 2.6 ETIM Classification
   - 2.7 Mapping Cross-Marketplace
3. [Attributi Prodotto per Marketplace](#3-attributi-prodotto-per-marketplace)
   - 3.1 Amazon — Product Type Definitions
   - 3.2 eBay — Item Specifics
   - 3.3 Kaufland — Mandatory Attributes
   - 3.4 Bol.com — Content Requirements
   - 3.5 Otto.de — Attribute Requirements
   - 3.6 Mirakl-Based (Carrefour, Leroy Merlin, Fnac-Darty, MediaWorld)
   - 3.7 GPSR — General Product Safety Regulation
4. [Data Quality Standards](#4-data-quality-standards)
   - 4.1 GS1 Data Quality Framework
   - 4.2 Amazon IDQ Score
   - 4.3 Icecat / ETIM Standards
   - 4.4 Quality Gates Vendiamonoi
5. [Database Schema — Supabase/PostgreSQL](#5-database-schema--supabasepostgresql)
   - 5.1 Approccio Architetturale: JSONB Hybrid
   - 5.2 Schema Core Completo
   - 5.3 Product Variants (Parent-Child)
   - 5.4 Multi-Language (Localizzazione)
   - 5.5 Media Management
   - 5.6 Product Lifecycle State Machine
   - 5.7 Supplier Integration Layer
   - 5.8 Indexing Strategy
   - 5.9 Row-Level Security (Multi-Supplier)
   - 5.10 Partitioning per Milioni di SKU
6. [Dati Logistica](#6-dati-logistica)
   - 6.1 Dimensioni e Peso
   - 6.2 Packaging Hierarchy
   - 6.3 HS Codes e Country of Origin
   - 6.4 Dangerous Goods Classification
7. [Pricing Data Model](#7-pricing-data-model)
   - 7.1 Struttura Prezzi
   - 7.2 Margini e Commissioni Marketplace
   - 7.3 IVA per Paese EU
   - 7.4 MAP e RRP in Europa
   - 7.5 Currency Handling
8. [Feed Specifications](#8-feed-specifications)
   - 8.1 Formati Feed (CSV, XML, JSON)
   - 8.2 Amazon JSON_LISTINGS_FEED
   - 8.3 Mirakl Product/Offer Distinction
   - 8.4 API Real-Time vs Batch
9. [Scalabilità e Performance](#9-scalabilità-e-performance)
   - 9.1 Materialized Views per Feed
   - 9.2 Change Data Capture (CDC)
   - 9.3 Bulk Operations
   - 9.4 Performance Targets
10. [Pattern Vendiamonoi — Implementazione Completa](#10-pattern-vendiamonoi--implementazione-completa)

---

# 1. Identificazione Prodotto — Standard Globali

## 1.1 GS1 / GTIN / EAN

Il **Global Trade Item Number (GTIN)** è lo standard universale per l'identificazione dei prodotti commerciali. Gestito da GS1, è obbligatorio su tutti i marketplace europei.

### Formati GTIN

| Formato | Cifre | Uso | Esempio |
|---------|-------|-----|--------|
| **GTIN-8 (EAN-8)** | 8 | Prodotti piccoli | 96385074 |
| **GTIN-12 (UPC-A)** | 12 | Nord America | 042100005264 |
| **GTIN-13 (EAN-13)** | 13 | Europa e mondo | 5901234123457 |
| **GTIN-14** | 14 | Unità logistiche/case | 15901234123451 |
| **ISBN-13** | 13 | Libri | 9780306406157 |
| **ISSN** | 8 | Periodici | 2049-3630 |

### Struttura EAN-13

```
5  9  0  1  2  3  4  1  2  3  4  5  7
│  │  │  │  │  │  │  │  │  │  │  │  └─ Check Digit
│  │  │──────────────────────────────── Item Reference
│──│──│ GS1 Company Prefix (variabile: 7-10 cifre)
└──┘   Country Prefix (2-3 cifre)
```

**Country Prefix**: Non indica l'origine del prodotto ma il paese dell'organizzazione GS1 che ha assegnato il codice. Esempi: 80-83 = Italia, 400-440 = Germania, 300-379 = Francia, 840-849 = Spagna.

### GS1 GDSN (Global Data Synchronisation Network)

Rete globale per sincronizzazione dati prodotto tra partner commerciali:
- Pool di dati certificati interconnessi
- GS1 Global Registry come backbone
- Data Quality Framework integrato
- Benefici documentati: 30% efficienza operativa, 50% riduzione costi supply chain, 25% meno errori dati

### GS1 Verified

Servizio (dal gennaio 2024, ex GEPIR) per verifica e lookup di GTIN, UPC, EAN, GLN. Dati affidabili sourced direttamente dai titolari delle chiavi GS1.

---

## 1.2 Algoritmo Check Digit (Mod 10)

Ogni GTIN ha un'ultima cifra di controllo calcolata con l'algoritmo Mod 10:

```
Passi per EAN-13 "590123412345?":

1. Cifre posizioni dispari (1ª, 3ª, 5ª...): 5+0+2+4+2+4 = 17
2. Cifre posizioni pari (2ª, 4ª, 6ª...): 9+1+3+1+3+5 = 22
3. Moltiplica pari × 3: 22 × 3 = 66
4. Somma: 17 + 66 = 83
5. Arrotonda al multiplo di 10 superiore: 90
6. Check digit = 90 - 83 = 7

EAN completo: 5901234123457
```

### Validazione in JavaScript

```javascript
function validateEAN13(ean) {
  if (!/^\d{13}$/.test(ean)) return false;
  
  const digits = ean.split('').map(Number);
  const checkDigit = digits.pop();
  
  const sum = digits.reduce((acc, digit, index) => {
    return acc + digit * (index % 2 === 0 ? 1 : 3);
  }, 0);
  
  const calculated = (10 - (sum % 10)) % 10;
  return calculated === checkDigit;
}

// Validazione in SQL (Supabase)
// CREATE FUNCTION validate_ean13(ean TEXT) RETURNS BOOLEAN AS $$
//   ... implementazione PL/pgSQL
// $$ LANGUAGE plpgsql;
```

---

## 1.3 Identificatori per Marketplace

Oltre al GTIN, ogni marketplace usa identificatori interni:

| Marketplace | Identificatore | Formato | Note |
|-------------|---------------|---------|------|
| **Amazon** | ASIN | 10 caratteri alfanumerici | Auto-generato da EAN valido |
| **eBay** | ePID | Numerico | ID catalogo globale eBay |
| **Bol.com** | BPID | Numerico | EAN multipli possono mappare a stesso BPID |
| **Kaufland** | Item ID | Numerico | Basato su EAN-13 obbligatorio |
| **Otto** | — | EAN-13 obbligatorio | Non deve iniziare con 2 |
| **Mirakl** | Product ID | Variabile | Specifico per operatore |

### Amazon ASIN

- 10 caratteri alfanumerici assegnati automaticamente
- Creato quando nuovo prodotto con UPC/EAN valido viene listato
- Ogni variante (taglia, colore) riceve ASIN unico
- Amazon valida che UPC/EAN sia registrato al venditore, non a terzi

### eBay ePID

- ID del catalogo prodotti globale eBay
- Usando ePID, eBay auto-popola titolo, descrizione, aspetti, immagini stock
- Semplifica creazione listing attraverso dati catalogo

### Bol.com BPID

- ID prodotto interno Bol.com
- Rappresenta EAN mergiati per stesso prodotto
- Varianti regionali, dimensioni pacchetto, colori/gusti usano EAN multipli

---

## 1.4 MPN — Manufacturer Part Number

- Codice alfanumerico unico assegnato dal produttore
- Nessun formato universale standard
- Trovabile su cataloghi, siti web, etichette prodotto
- Critico per: tracking inventario, compatibilità componenti, identificazione precisa
- Richiesto come coppia Brand + MPN su molti marketplace quando manca GTIN

---

## 1.5 SKU — Convenzioni Multi-Marketplace

### Regole per Sistema Multi-Marketplace

| Regola | Dettaglio |
|--------|----------|
| **Unicità** | Lo stesso SKU deve identificare lo stesso prodotto su TUTTI i marketplace |
| **Lunghezza max** | 32 caratteri (compatibilità universale) |
| **Formato** | Solo alfanumerico + trattini. No spazi, no caratteri speciali |
| **Struttura consigliata** | `{BRAND3}-{CATEGORY2}-{EAN}` o `{BRAND3}-{MPN}` |
| **Case** | UPPERCASE per uniformità |
| **Immutabilità** | Una volta assegnato, MAI cambiare |

### Formato Vendiamonoi Consigliato

```
FOR-ELE-5901234123457
│   │   └── EAN-13 completo
│   └────── Categoria (2-3 char)
└────────── Brand abbreviato (3 char)

Altro formato:
FOR-12345ABC
│   └── MPN del produttore
└────── Brand abbreviato
```

**CRITICO**: SKU disallineati tra marketplace richiedono linking manuale, creando inefficienza operativa massiva con 20-30 canali.

---

## 1.6 Brand Name Management

- Obbligatorio su tutti i marketplace europei
- Deve corrispondere esattamente al branding ufficiale del produttore
- Gestire varianti: "Samsung" vs "SAMSUNG" vs "samsung" → normalizzare
- Alcune piattaforme richiedono brand registry (Amazon Brand Registry)
- Tabella brand centralizzata con varianti note

---

# 2. Tassonomia e Categorizzazione

## 2.1 GPC — Global Product Classification

Sistema gerarchico a 4 livelli gestito da GS1:

```
Segmento (livello 1)
  └── Famiglia (livello 2)
        └── Classe (livello 3)
              └── Brick (livello 4) + Attributi
```

Fornisce classificazione uniforme globale per consistenza nella rete GDSN.

## 2.2 Amazon Browse Node

- Struttura gerarchica con 30.000+ categorie distinte
- Ogni categoria ha Browse Node ID unico
- Gerarchia: Department (root) → Category (branch) → Subcategory (leaf)
- **Browse Tree Guide (BTG)**: documento con Node IDs, percorsi, Item Type Keywords
- Node IDs diversi per marketplace (amazon.it vs amazon.de vs amazon.fr)

## 2.3 eBay Category Tree

- Ogni marketplace eBay (20+) ha albero categorie diverso
- Nodi: leaf (per listing) o parent (per navigazione)
- **Aspects**: attributi predefiniti associati a categorie leaf
- API: `getCategoryTree`, `getItemAspectsForCategory` (Taxonomy API)
- gzip raccomandato per payload grandi

## 2.4 Google Product Category

- 6.000+ categorie in tassonomia in evoluzione
- Struttura ad albero gerarchica
- Si può inviare category ID o percorso completo (non entrambi)
- Alcune categorie (Apparel, Mobile Phones, Software) impongono campi obbligatori aggiuntivi
- Subdivision disponibile in 12 paesi inclusa Italia

## 2.5 UNSPSC

- United Nations Standard Products and Services Code
- 4 livelli codificati come numero a 8 cifre (+ opzionale 5° livello a 10 cifre)
- Versione 26.0801 (agosto 2024): 157.116 items totali
- Gestito da GS1 US per UNDP

## 2.6 ETIM Classification

- ElectroTechnical Information Model per prodotti tecnici
- ETIM 10.0 rilasciato dicembre 2024
- Struttura: Classe (EC) → Attributo (EF) → Valore (EV)
- Usato in 150+ paesi, 21 con rappresentanza locale
- Modello indipendente dalla lingua
- **ETIM xChange**: formato JSON rilasciato febbraio 2024 per scambio dati

## 2.7 Mapping Cross-Marketplace

Per distribuire su 20-30 marketplace serve un mapping centralizzato:

```sql
CREATE TABLE category_mappings (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  internal_category_id UUID NOT NULL,
  marketplace VARCHAR(50) NOT NULL,
  marketplace_category_id VARCHAR(100) NOT NULL,
  marketplace_category_path TEXT,
  confidence DECIMAL(3,2),  -- 0.00-1.00 se mapping automatico
  verified BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(internal_category_id, marketplace)
);

-- Esempio:
-- internal: "Elettronica > Smartphone"
-- amazon.it: Browse Node 473390031
-- ebay.it: Category 9355
-- kaufland.de: Category "Handys & Smartphones"
-- bol.com: Category "Smartphones"
```

---

# 3. Attributi Prodotto per Marketplace

## 3.1 Amazon — Product Type Definitions

### JSON Schema Dinamici

Amazon usa JSON Schema 2019-09 con logica condizionale `allOf`:
- Gli attributi obbligatori cambiano dinamicamente in base ai valori compilati
- **Product Type Definitions API**: restituisce schema per ogni product type
- Feed JSON_LISTINGS_FEED: fino a 25.000 SKU per richiesta, 5 richieste per 5 minuti

### Attributi Core Amazon

| Attributo | Obbligatorio | Note |
|-----------|-------------|------|
| ASIN | Auto-generato | Da EAN/UPC valido |
| Product Type | Sì | Determina schema attributi |
| Title | Sì | Max ~200 caratteri, regole per marketplace |
| Brand | Sì | Brand Registry consigliato |
| EAN/UPC | Sì | Validato vs GS1 |
| Bullet Points | Sì | 5 punti elenco |
| Description | Sì | HTML limitato o A+ Content |
| Images | Sì | Min 1000px, sfondo bianco |
| Price | Sì | Per marketplace |
| Category | Sì | Browse Node ID |
| Net Content | Condizionale | Obbligatorio per 8 product types (2024+) |
| Item Form | Condizionale | Obbligatorio per 86 product types (2024+) |

### Parent-Child (Varianti)

- Parent ASIN: non acquistabile, lega i child
- Child ASIN: prodotto acquistabile con attributi specifici
- **Variation Theme**: unico per parent (Size, Color, Size-Color), immutabile dopo creazione
- Limite: massimo 2.000 child per parent
- Campo: `parent_sku` nel child row

### Immagini Amazon

| Tipo | Codice | Requisiti |
|------|--------|----------|
| Main | MAIN | Sfondo bianco RGB(255,255,255), prodotto 85%+ frame |
| PT01 | PT01 | Tutti i componenti, angolo retto |
| PT02 | PT02 | Funzione/vista posteriore |
| SWATCH | SWATCH | Thumbnail colore/pattern |
| Format | — | TIFF, JPEG, GIF, PNG |
| Min size | — | 1000px (1200px per zoom) |
| Max size | — | 10 MB |

### A+ Content

- Fino a 7 moduli da 17 opzioni disponibili
- Tipi: immagini, video, 360° spin, hotspot, tabelle comparazione, carousel
- Premium Video Carousel: fino a 6 pannelli

**IMPORTANTE**: Feed XML/flat file legacy deprecati dal 31 luglio 2025. Migrare a JSON_LISTINGS_FEED.

---

## 3.2 eBay — Item Specifics

### Attributi Dinamici per Categoria

- Recuperati via `getItemAspectsForCategory` (Taxonomy API)
- Campo `aspectConstraint.aspectRequired = true` indica obbligatorietà
- GTIN (UPC, EAN, ISBN) obbligatorio per molte categorie
- Oppure coppia Brand + MPN

### Condition System

| Condition ID | Descrizione |
|-------------|------------|
| 1000 | Brand New |
| 1500 | New other |
| 2000 | Manufacturer refurbished |
| 2500 | Seller refurbished |
| 3000 | Used |
| 7000 | For parts or not working |

- **Condition Descriptors**: coppie nome-valore category-specific con lunghezza max e cardinalità

### Varianti eBay

- Parent SKU: virtuale, non acquistabile
- Child SKU: acquistabile con valori variation-specific
- Limite: 250 varianti per listing
- Max 5 variation specifics con 50 valori ciascuno

### International Selling

- 4 programmi standard per paese destinazione
- Alcune categorie escluse (Video Games, Vehicles, ecc.)
- Country of Origin obbligatorio (paese produzione, non spedizione)
- UK: UKCA marking richiesto per toys, auto parts

---

## 3.3 Kaufland — Mandatory Attributes

| Attributo | Obbligatorio | Dettaglio |
|-----------|-------------|----------|
| EAN-13 | Sì | 13 cifre, no UPC senza conversione |
| Product Name | Sì | Titolo chiaro |
| Description | Sì | Minimo 90 caratteri |
| Brand | Sì | — |
| Category | Sì | 5.000-6.400+ categorie |
| Main Image | Sì | — |
| Material Composition | Condizionale | Obbligatorio se ≥80% fibre tessili |
| GPSR Info | Sì (dal 13/12/2024) | Contatto sicurezza + certificato CE |

- File CSV con separatore punto e virgola, codifica UTF-8
- Mercati: Germania, Austria, Repubblica Ceca, Slovacchia, Polonia, Francia, Italia
- Divieto di listare stesso prodotto sotto EAN multipli

---

## 3.4 Bol.com — Content Requirements

| Attributo | Obbligatorio | Dettaglio |
|-----------|-------------|----------|
| EAN | Sì | Obbligatorio per processamento |
| Title | Sì | — |
| Description | Sì | — |
| Images | Sì | 30KB-10MB, 500x500 a 6000x6000 px |
| Brand | Sì | — |
| Category | Sì | 6.000+ attributi combinati per categoria |

### Immagini Bol.com

- Zoom: minimo 1200x1200 px
- Main image: sfondo bianco/neutro, no ombra, no modelli/testo
- Vietati: etichette sconto, watermark, logo azienda, testo promozionale
- Asset Labels: min 1, max 2 per immagine (FRONT, SIDE, ecc.)
- CE MARKING asset obbligatorio per categorie regolamentate
- Tempo processamento: min 24 ore (testo ~1 ora, immagini più lungo)

---

## 3.5 Otto.de — Attribute Requirements

| Attributo | Obbligatorio | Dettaglio |
|-----------|-------------|----------|
| EAN-13 | Sì | Non deve iniziare con 2 |
| Brand | Sì | — |
| Main Image | Sì | Min 500x1000 px, max 4500 px, JPG/PNG, RGB |
| GPSR | Sì | Indirizzo distributore + produttore/importatore con email |
| Sales Unit | Sì | — |
| User Manual | Condizionale | URL per articoli complessi |
| Warning Labels | Condizionale | URL per etichette pericolo |

- Requisiti seller: sede Germania, Partita IVA tedesca, customer service tedesco, warehouse tedesco
- Solo prodotti nuovi

---

## 3.6 Mirakl-Based Marketplaces

### Distinzione Prodotto vs Offerta

Punto fondamentale dei marketplace Mirakl:

| Livello | Contenuto | Chi lo gestisce |
|---------|-----------|----------------|
| **Product** | Titolo, brand, descrizione, categoria, SKU, EAN, immagini | Operatore marketplace |
| **Offer** | Prezzo, stock, spedizione, condizioni venditore | Venditore |

- I prodotti devono essere creati/accettati PRIMA delle offerte
- `Product.SellerProductID` deve matchare `product-id` nei file offerta
- AI-powered: deduplicazione automatica, mapping categorie, standardizzazione

### Marketplace Mirakl Europei

| Marketplace | Paese | Lingua Required | Note |
|-------------|-------|----------------|------|
| Carrefour | FR | Francese | Nome max 85 char, desc min 90 char, img max 500KB |
| Leroy Merlin | FR/IT/PL | Locale | Img 1:1, min 600x600, sfondo bianco |
| Fnac-Darty | FR/BE | Francese | EAN-based matching, catalogo unico per entrambi |
| MediaWorld | IT | Italiano | Solo elettronica, selezione seller rigorosa |
| Rue du Commerce | FR | Francese | — |

### Mirakl Catalog Integration (MCI)

- Upload: CSV, XLS, XML
- API: REST APIs per catalog management
- Supporto tassonomie: UNSPSC, ETIM, eClass
- AI: rileva similarità sintattiche, auto-mappa categorie/valori
- Features: product buffering, consolidation, seller enrichment, digital asset management, validation rules engine

---

## 3.7 GPSR — General Product Safety Regulation

Dal **13 dicembre 2024**, tutti i prodotti venduti nell'UE devono includere:

| Campo | Obbligatorio | Dettaglio |
|-------|-------------|----------|
| Product Safety Contact | Sì | Nome + indirizzo completo + email del responsabile |
| Manufacturer Info | Sì | Nome, indirizzo, email del produttore |
| Importer Info | Condizionale | Se prodotto fabbricato fuori UE |
| CE Certificate | Condizionale | Per categorie regolamentate |
| Warning Labels URL | Condizionale | Per prodotti con rischi specifici |

**Impatto Vendiamonoi**: Ogni prodotto distribuito deve avere queste informazioni raccolte dal fornitore e pubblicate su ogni marketplace.

---

# 4. Data Quality Standards

## 4.1 GS1 Data Quality Framework

- Best practice per miglioramento continuo della qualità dati master
- Allineato con GDSN e standard GS1
- Include: toolkit implementazione, scorecard auto-valutazione
- Componenti: regole validazione, sistemi warning, protocolli sincronizzazione

## 4.2 Amazon IDQ Score

**IDQ (Inventory Data Quality) Score**: prodotti devono raggiungere 90+ per:
- Inclusione nell'algoritmo di ricerca Amazon
- Eligibilità per deals e promozioni

Fattori:
- Immagini alta qualità (min 1000px)
- Titoli descrittivi (seguire formula per categoria)
- Bullet points chiari e informativi
- Categorizzazione corretta
- Variation families complete
- Hidden keywords ottimizzate

## 4.3 Icecat / ETIM Standards

**Icecat**:
- Database prodotti standardizzato con 6.000+ attributi
- Sincronizzazione automatica con piattaforme e-commerce
- PIM integrato con Shopify, Magento, WooCommerce, Amazon, Shopware

**ETIM**:
- Standard classificazione per prodotti tecnici/industriali
- Struttura: Classe → Attributo → Valore
- Aggiornamento ogni ~2 anni

## 4.4 Quality Gates Vendiamonoi

Regole di validazione prima della pubblicazione su marketplace:

```sql
CREATE TABLE quality_rules (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  rule_name VARCHAR NOT NULL,
  rule_type VARCHAR NOT NULL,  -- 'mandatory', 'format', 'range', 'consistency'
  field_path VARCHAR NOT NULL,
  condition JSONB NOT NULL,
  severity VARCHAR DEFAULT 'error',  -- 'error', 'warning', 'info'
  marketplace VARCHAR,  -- NULL = tutti i marketplace
  active BOOLEAN DEFAULT true
);

-- Esempi regole:
-- EAN-13 valido (formato + check digit)
-- Titolo: 10-200 caratteri, no caratteri speciali eccessivi
-- Descrizione: min 90 caratteri
-- Almeno 1 immagine con min 1000px
-- Prezzo > 0
-- Stock >= 0
-- Brand non vuoto
-- Categoria mappata per marketplace target
```

---

# 5. Database Schema — Supabase/PostgreSQL

## 5.1 Approccio Architetturale: JSONB Hybrid

Dopo analisi di tre approcci (EAV, Wide Table, JSONB), l'architettura raccomandata è **JSONB Hybrid**:

| Approccio | Pro | Contro | Raccomandazione |
|-----------|-----|--------|----------------|
| **EAV** | Flessibilità massima | Self-join per ogni query, lentissimo su scala | NO |
| **Wide Table** | Query semplici | Colonne sparse, inflessibile | NO |
| **JSONB Hybrid** | Bilanciato: colonne tipizzate per campi frequenti + JSONB per variabili | Richiede GIN index | SÌ ✓ |

**JSONB è 50.000x più veloce di EAV** per query non indicizzate. Database 3x più piccolo.

---

## 5.2 Schema Core Completo

```sql
-- =============================================
-- SCHEMA CORE: PRODUCT INFORMATION MANAGEMENT
-- Vendiamonoi.it — Supabase/PostgreSQL
-- =============================================

-- Estensioni necessarie
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pg_trgm";

-- =============================================
-- TABELLE REFERENCE
-- =============================================

CREATE TABLE brands (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  normalized_name VARCHAR(255) NOT NULL,  -- UPPERCASE, trimmed
  logo_url VARCHAR(500),
  website VARCHAR(500),
  amazon_brand_registry BOOLEAN DEFAULT false,
  variants JSONB DEFAULT '[]',  -- ["Samsung", "SAMSUNG", "samsung"]
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(normalized_name)
);

CREATE TABLE categories (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  parent_id UUID REFERENCES categories(id),
  name VARCHAR(255) NOT NULL,
  slug VARCHAR(255) NOT NULL,
  level INTEGER NOT NULL DEFAULT 0,
  path TEXT,  -- "Elettronica > Smartphone > Android"
  attributes_schema JSONB,  -- attributi richiesti per questa categoria
  active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE suppliers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  code VARCHAR(10) NOT NULL UNIQUE,  -- "FOR", "SAM", etc.
  contact_email VARCHAR(255),
  catalog_format VARCHAR(50),  -- 'csv', 'xml', 'api', 'excel'
  price_list_currency VARCHAR(3) DEFAULT 'EUR',
  lead_time_days INTEGER DEFAULT 3,
  gpsr_contact JSONB,  -- {name, address, email, phone}
  manufacturer_info JSONB,  -- per GPSR
  active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE product_states (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  code VARCHAR(20) UNIQUE NOT NULL,
  label VARCHAR(50) NOT NULL,
  description TEXT
);

INSERT INTO product_states (code, label, description) VALUES
  ('draft', 'Bozza', 'Prodotto creato ma non ancora pronto per pubblicazione'),
  ('active', 'Attivo', 'Prodotto pubblicabile e disponibile per vendita'),
  ('paused', 'In Pausa', 'Temporaneamente sospeso (stock, stagionalità)'),
  ('discontinued', 'Discontinuato', 'Fine vita, non più in produzione'),
  ('blocked', 'Bloccato', 'Blocco amministrativo per compliance/policy');

-- =============================================
-- TABELLA PRODOTTI PRINCIPALE
-- =============================================

CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  
  -- Identificazione
  sku VARCHAR(32) NOT NULL UNIQUE,
  ean VARCHAR(13),
  mpn VARCHAR(100),
  brand_id UUID REFERENCES brands(id),
  
  -- Classificazione
  category_id UUID REFERENCES categories(id),
  
  -- Stato
  state_id UUID NOT NULL REFERENCES product_states(id),
  state_changed_at TIMESTAMPTZ DEFAULT now(),
  state_changed_by UUID,
  
  -- Fornitore
  supplier_id UUID NOT NULL REFERENCES suppliers(id),
  supplier_sku VARCHAR(100),  -- SKU del fornitore
  
  -- Dati base (colonne tipizzate per query frequenti)
  title VARCHAR(500) NOT NULL,
  description TEXT,
  
  -- Pricing base
  cost_price DECIMAL(10,2),
  cost_currency VARCHAR(3) DEFAULT 'EUR',
  rrp DECIMAL(10,2),  -- Recommended Retail Price
  
  -- Logistica base
  weight_gross_kg DECIMAL(8,3),
  weight_net_kg DECIMAL(8,3),
  length_cm DECIMAL(8,2),
  width_cm DECIMAL(8,2),
  height_cm DECIMAL(8,2),
  hs_code VARCHAR(20),
  country_of_origin VARCHAR(2),  -- ISO 3166-1 alpha-2
  
  -- Attributi variabili (JSONB per flessibilità)
  attributes JSONB DEFAULT '{}',
  -- Esempio: {"color": "nero", "material": "cotone", "size": "M",
  --          "voltage": "220V", "warranty_months": 24}
  
  -- GPSR (General Product Safety Regulation)
  gpsr_data JSONB DEFAULT '{}',
  -- {"safety_contact": {"name": "", "address": "", "email": ""},
  --  "manufacturer": {"name": "", "address": "", "email": ""},
  --  "ce_certificate_url": "", "warning_labels_url": ""}
  
  -- Dangerous Goods
  dangerous_goods BOOLEAN DEFAULT false,
  dangerous_goods_class VARCHAR(10),
  
  -- Metadata
  metadata JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

-- =============================================
-- LOCALIZZAZIONE (Multi-Language)
-- =============================================

CREATE TABLE product_translations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  language_code VARCHAR(2) NOT NULL,  -- 'it', 'en', 'fr', 'de', 'es', 'nl', 'pl', 'pt', 'ro', 'cs'
  title VARCHAR(500),
  description TEXT,
  bullet_points JSONB DEFAULT '[]',  -- ["punto 1", "punto 2", ...]
  seo_title VARCHAR(200),
  seo_keywords TEXT,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(product_id, language_code)
);

-- Lingue target Vendiamonoi:
-- IT (italiano), EN (inglese), FR (francese), DE (tedesco),
-- ES (spagnolo), NL (olandese), PL (polacco), PT (portoghese),
-- RO (rumeno), CS (ceco), SK (slovacco)

-- =============================================
-- VARIANTI (Parent-Child)
-- =============================================

CREATE TABLE product_variants (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  sku VARCHAR(32) NOT NULL UNIQUE,
  ean VARCHAR(13),
  
  -- Attributi variante (override del parent)
  variant_attributes JSONB NOT NULL,
  -- Esempio: {"size": "M", "color": "nero"}
  
  -- Pricing specifico variante
  cost_price DECIMAL(10,2),
  rrp DECIMAL(10,2),
  
  -- Logistica specifica variante
  weight_gross_kg DECIMAL(8,3),
  length_cm DECIMAL(8,2),
  width_cm DECIMAL(8,2),
  height_cm DECIMAL(8,2),
  
  -- Stato
  active BOOLEAN DEFAULT true,
  
  sort_order INTEGER DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

-- Traduzioni variante
CREATE TABLE variant_translations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  variant_id UUID NOT NULL REFERENCES product_variants(id) ON DELETE CASCADE,
  language_code VARCHAR(2) NOT NULL,
  title_suffix VARCHAR(200),  -- " - Taglia M, Nero"
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(variant_id, language_code)
);

-- =============================================
-- IMMAGINI E MEDIA
-- =============================================

CREATE TABLE product_images (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  variant_id UUID REFERENCES product_variants(id) ON DELETE CASCADE,
  
  -- Tipo immagine
  image_type VARCHAR(20) NOT NULL DEFAULT 'additional',
  -- 'main', 'additional', 'swatch', 'lifestyle', 'size_chart', 'package'
  
  -- URL
  url_original VARCHAR(1000) NOT NULL,
  url_cdn VARCHAR(1000),  -- URL trasformata CDN
  
  -- Metadata tecnici
  width INTEGER,
  height INTEGER,
  file_size_bytes INTEGER,
  format VARCHAR(10),  -- 'jpg', 'png', 'webp'
  
  -- SEO
  alt_text TEXT,
  
  -- Ordinamento
  sort_order INTEGER DEFAULT 0,
  
  -- Stato
  active BOOLEAN DEFAULT true,
  
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

-- =============================================
-- STOCK & INVENTARIO
-- =============================================

CREATE TABLE stock (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id),
  variant_id UUID REFERENCES product_variants(id),
  supplier_id UUID NOT NULL REFERENCES suppliers(id),
  
  quantity INTEGER NOT NULL DEFAULT 0,
  reserved INTEGER NOT NULL DEFAULT 0,  -- quantità riservata per ordini
  available INTEGER GENERATED ALWAYS AS (quantity - reserved) STORED,
  
  warehouse_code VARCHAR(20),
  lead_time_days INTEGER,
  
  last_sync_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  
  UNIQUE(product_id, variant_id, supplier_id, warehouse_code)
);

-- =============================================
-- PRICING PER MARKETPLACE
-- =============================================

CREATE TABLE marketplace_prices (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id),
  variant_id UUID REFERENCES product_variants(id),
  marketplace VARCHAR(50) NOT NULL,
  
  selling_price DECIMAL(10,2) NOT NULL,
  currency VARCHAR(3) NOT NULL DEFAULT 'EUR',
  
  -- Scomposizione prezzo
  cost_price DECIMAL(10,2),
  margin_percent DECIMAL(5,2),
  commission_percent DECIMAL(5,2),
  vat_percent DECIMAL(5,2),
  shipping_cost DECIMAL(10,2),
  
  -- Prezzi speciali
  sale_price DECIMAL(10,2),
  sale_start TIMESTAMPTZ,
  sale_end TIMESTAMPTZ,
  
  -- Stato
  active BOOLEAN DEFAULT true,
  auto_reprice BOOLEAN DEFAULT false,
  min_price DECIMAL(10,2),  -- floor price
  max_price DECIMAL(10,2),  -- ceiling price
  
  last_calculated_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  
  UNIQUE(product_id, variant_id, marketplace)
);

-- =============================================
-- MARKETPLACE LISTINGS (stato pubblicazione)
-- =============================================

CREATE TABLE marketplace_listings (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id),
  variant_id UUID REFERENCES product_variants(id),
  marketplace VARCHAR(50) NOT NULL,
  
  -- Identificatori marketplace
  marketplace_product_id VARCHAR(100),  -- ASIN, ePID, etc.
  marketplace_sku VARCHAR(100),
  listing_url VARCHAR(1000),
  
  -- Stato
  status VARCHAR(20) NOT NULL DEFAULT 'pending',
  -- 'pending', 'published', 'suppressed', 'error', 'inactive'
  
  -- Categoria marketplace
  marketplace_category_id VARCHAR(100),
  marketplace_category_path TEXT,
  
  -- Quality
  quality_score INTEGER,  -- IDQ score per Amazon, etc.
  
  -- Errori
  last_error JSONB,
  error_count INTEGER DEFAULT 0,
  
  -- Sync
  last_sync_at TIMESTAMPTZ,
  last_published_at TIMESTAMPTZ,
  
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  
  UNIQUE(product_id, variant_id, marketplace)
);

-- =============================================
-- CATEGORY MAPPINGS
-- =============================================

CREATE TABLE category_mappings (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  internal_category_id UUID NOT NULL REFERENCES categories(id),
  marketplace VARCHAR(50) NOT NULL,
  marketplace_category_id VARCHAR(100) NOT NULL,
  marketplace_category_path TEXT,
  confidence DECIMAL(3,2),
  verified BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(internal_category_id, marketplace)
);

-- =============================================
-- SUPPLIER DATA IMPORT
-- =============================================

CREATE TABLE supplier_imports (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  supplier_id UUID NOT NULL REFERENCES suppliers(id),
  
  -- Dati raw dal fornitore
  supplier_sku VARCHAR(100),
  raw_data JSONB NOT NULL,  -- dati originali non normalizzati
  
  -- Mapping
  canonical_product_id UUID REFERENCES products(id),
  mapping_status VARCHAR(20) DEFAULT 'pending',
  -- 'pending', 'mapped', 'new_product', 'duplicate', 'error'
  
  -- Quality
  validation_errors JSONB DEFAULT '[]',
  
  imported_at TIMESTAMPTZ DEFAULT now(),
  processed_at TIMESTAMPTZ
);

CREATE TABLE supplier_field_mappings (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  supplier_id UUID NOT NULL REFERENCES suppliers(id),
  source_field VARCHAR(100) NOT NULL,  -- campo nel file fornitore
  target_field VARCHAR(100) NOT NULL,  -- campo nel nostro schema
  transform_rule JSONB,  -- {"type": "uppercase"}, {"type": "map", "values": {"S": "Small"}}
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(supplier_id, source_field)
);

-- =============================================
-- PACKAGING HIERARCHY
-- =============================================

CREATE TABLE product_packaging (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id),
  
  level VARCHAR(20) NOT NULL,  -- 'each', 'inner', 'case', 'pallet'
  
  quantity_per_unit INTEGER,  -- pezzi per unità
  parent_packaging_id UUID REFERENCES product_packaging(id),
  
  -- Dimensioni packaging
  length_cm DECIMAL(8,2),
  width_cm DECIMAL(8,2),
  height_cm DECIMAL(8,2),
  weight_gross_kg DECIMAL(8,3),
  
  -- Codici
  gtin VARCHAR(14),  -- GTIN-14 per case/pallet
  barcode_type VARCHAR(20),  -- 'ean13', 'gtin14', 'sscc'
  
  created_at TIMESTAMPTZ DEFAULT now()
);

-- =============================================
-- STATE MACHINE AUDIT LOG
-- =============================================

CREATE TABLE product_state_transitions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id UUID NOT NULL REFERENCES products(id),
  from_state_id UUID REFERENCES product_states(id),
  to_state_id UUID NOT NULL REFERENCES product_states(id),
  transitioned_at TIMESTAMPTZ DEFAULT now(),
  transitioned_by UUID,
  reason TEXT
);
```

---

## 5.3 Product Variants (Parent-Child)

Il modello parent-child è universale nei marketplace:

```
Prodotto Parent (non acquistabile)
├── Variante 1: Taglia S, Colore Nero (SKU: FOR-TSH-001-S-BLK)
├── Variante 2: Taglia M, Colore Nero (SKU: FOR-TSH-001-M-BLK)
├── Variante 3: Taglia L, Colore Nero (SKU: FOR-TSH-001-L-BLK)
├── Variante 4: Taglia S, Colore Bianco (SKU: FOR-TSH-001-S-WHT)
└── ...
```

**Ereditarietà**: Le varianti ereditano titolo, descrizione, categoria, immagini dal parent e overridano solo gli attributi specifici (EAN, taglia, colore, peso, prezzo).

---

## 5.4 Multi-Language (Localizzazione)

Con 11 lingue target per marketplace europei:

| Lingua | Codice | Marketplace |
|--------|--------|------------|
| Italiano | it | Amazon.it, eBay.it, MediaWorld, ePrice |
| Inglese | en | Amazon.co.uk, eBay.co.uk |
| Francese | fr | Amazon.fr, Carrefour, Fnac-Darty, Leroy Merlin, Rue du Commerce, Cdiscount |
| Tedesco | de | Amazon.de, eBay.de, Kaufland, Otto, Metro |
| Spagnolo | es | Amazon.es, eBay.es, PCComponentes |
| Olandese | nl | Bol.com, Amazon.nl |
| Polacco | pl | Allegro, Amazon.pl, Leroy Merlin PL |
| Portoghese | pt | Worten |
| Rumeno | ro | eMAG |
| Ceco | cs | Kaufland CZ |
| Slovacco | sk | Kaufland SK |

**Strategia JSONB alternativa** (per query più semplici):

```sql
-- Invece di tabella separata, JSONB diretto:
ALTER TABLE products ADD COLUMN titles JSONB;
-- {"it": "Smartphone Samsung Galaxy S24", "fr": "Smartphone Samsung Galaxy S24", "de": "Samsung Galaxy S24 Smartphone"}

-- Query:
SELECT titles->>'fr' AS french_title FROM products WHERE id = '...';
```

La tabella `product_translations` è preferibile per:
- Full-text search per lingua
- Gestione workflow traduzione (stato: da tradurre, tradotto, revisionato)
- Report su completezza traduzione per marketplace

---

## 5.5 Media Management

### Pipeline Immagini

```
Upload Fornitore → S3/R2 (originale) → Trasformazione CDN → URL Ottimizzate
                                        (Cloudinary/ImageKit)
                                        ├── Amazon: 1200x1200, JPEG, sfondo bianco
                                        ├── eBay: 1600x1600, JPEG
                                        ├── Bol.com: 1200x1200, JPEG, sfondo bianco
                                        └── Kaufland: 800x800, JPEG
```

### Requisiti per Marketplace

| Marketplace | Min Size | Max Size | Sfondo | Formato |
|-------------|----------|----------|--------|--------|
| Amazon | 1000px | 10MB | Bianco RGB(255,255,255) | JPEG, PNG, TIFF, GIF |
| eBay | 500px | — | Raccomandato bianco | JPEG, PNG |
| Bol.com | 500px | 10MB | Bianco/neutro | JPEG |
| Kaufland | — | — | — | JPEG |
| Otto | 500x1000px | 4500px | — | JPG, PNG (RGB) |
| Carrefour | — | 500KB | — | — |
| Leroy Merlin | 600x600px | — | Bianco | — |

---

## 5.6 Product Lifecycle State Machine

### Transizioni Valide

```
                    ┌─────────────────────────┐
                    │                         │
    ┌───────┐  publish  ┌────────┐  pause  ┌────────┐
    │ Draft │──────────→│ Active │────────→│ Paused │
    └───────┘           └────────┘         └────────┘
                          │    ↑              │
                          │    │ resume       │
                          │    └──────────────┘
                          │                   │
                     EOL  │              EOL  │
                          ↓                   ↓
                     ┌──────────────┐
                     │ Discontinued │
                     └──────────────┘
                     
    Qualsiasi Stato ──── compliance ────→ Blocked
    Blocked ──── resolution ────→ Active
```

### Trigger su Transizione

| Transizione | Azione |
|-------------|--------|
| → Active | Pubblica su marketplace target, genera feed |
| → Paused | Disattiva offerte su tutti i marketplace |
| → Discontinued | Rimuovi listing, notifica customer service |
| → Blocked | Rimuovi immediato, log compliance |

---

## 5.7 Supplier Integration Layer

### Pipeline Importazione

```
Feed Fornitore → Schema Detection → Validazione → Normalizzazione →
Deduplicazione → Arricchimento → Canonical Model → Quality Gates →
Tabella Products
```

### Normalizzazione Dati

1. **Cleaning**: Rimuovi typo, standardizza formati
2. **Trasformazione**: Regole predefinite (spellings, categorie, unità)
3. **Normalizzazione attributi**: Mapping nomi consistenti cross-fornitore
4. **Deduplicazione**: Fuzzy matching su titoli e brand normalizzati

```sql
-- Fuzzy match per deduplicazione cross-fornitore
SELECT sp1.id, sp2.id, similarity(sp1.title, sp2.title) as match_score
FROM supplier_imports sp1
JOIN supplier_imports sp2 ON sp1.supplier_id != sp2.supplier_id
WHERE sp1.title % sp2.title  -- operatore trigram similarity
AND similarity(sp1.title, sp2.title) > 0.85;
```

---

## 5.8 Indexing Strategy

```sql
-- B-Tree per lookup diretti
CREATE INDEX idx_products_sku ON products(sku);
CREATE INDEX idx_products_ean ON products(ean);
CREATE INDEX idx_products_brand ON products(brand_id);
CREATE INDEX idx_products_category ON products(category_id);
CREATE INDEX idx_products_supplier ON products(supplier_id);
CREATE INDEX idx_products_state ON products(state_id);

-- GIN per JSONB
CREATE INDEX idx_products_attributes ON products USING GIN(attributes);
CREATE INDEX idx_products_metadata ON products USING GIN(metadata);
CREATE INDEX idx_products_gpsr ON products USING GIN(gpsr_data);

-- Trigram per ricerca fuzzy
CREATE INDEX idx_products_title_trgm ON products USING GIN(title gin_trgm_ops);

-- Partial index per prodotti attivi
CREATE INDEX idx_products_active ON products(created_at)
  WHERE state_id = (SELECT id FROM product_states WHERE code = 'active');

-- Index per traduzioni
CREATE INDEX idx_translations_product_lang ON product_translations(product_id, language_code);

-- Index per marketplace listings
CREATE INDEX idx_listings_marketplace ON marketplace_listings(marketplace, status);
CREATE INDEX idx_listings_product ON marketplace_listings(product_id);

-- Index per stock
CREATE INDEX idx_stock_product ON stock(product_id, supplier_id);

-- Index per prezzi
CREATE INDEX idx_prices_product_mp ON marketplace_prices(product_id, marketplace);
```

---

## 5.9 Row-Level Security (Multi-Supplier)

Per dare accesso ai fornitori solo ai propri prodotti:

```sql
ALTER TABLE products ENABLE ROW LEVEL SECURITY;

-- Policy: fornitori vedono solo i propri prodotti
CREATE POLICY supplier_isolation ON products
  FOR SELECT
  USING (
    supplier_id = auth.uid()
    OR
    EXISTS (
      SELECT 1 FROM user_roles
      WHERE user_id = auth.uid()
      AND role IN ('admin', 'catalog_manager')
    )
  );

-- Policy: fornitori possono aggiornare solo stock e prezzi dei propri prodotti
CREATE POLICY supplier_update ON products
  FOR UPDATE
  USING (supplier_id = auth.uid())
  WITH CHECK (supplier_id = auth.uid());

-- CRITICO: indicizzare colonne usate nelle policy RLS
CREATE INDEX idx_products_supplier_rls ON products(supplier_id);
```

---

## 5.10 Partitioning per Milioni di SKU

Partitioning raccomandato quando tabella supera 100M righe o 50GB.

Per Vendiamonoi (target milioni di SKU):

```sql
-- Partitioning per range temporale
CREATE TABLE products_partitioned (
  -- stessa struttura di products
) PARTITION BY RANGE (created_at);

CREATE TABLE products_2024 PARTITION OF products_partitioned
  FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');

CREATE TABLE products_2025 PARTITION OF products_partitioned
  FOR VALUES FROM ('2025-01-01') TO ('2026-01-01');

CREATE TABLE products_2026 PARTITION OF products_partitioned
  FOR VALUES FROM ('2026-01-01') TO ('2027-01-01');
```

Alternativa: **Hash partitioning** per distribuzione uniforme senza range naturali:

```sql
CREATE TABLE products_hash (
  -- stessa struttura
) PARTITION BY HASH (id);

CREATE TABLE products_hash_0 PARTITION OF products_hash
  FOR VALUES WITH (MODULUS 4, REMAINDER 0);
CREATE TABLE products_hash_1 PARTITION OF products_hash
  FOR VALUES WITH (MODULUS 4, REMAINDER 1);
-- etc.
```

Usare `pg_partman` per gestione automatica partizioni temporali.

---

# 6. Dati Logistica

## 6.1 Dimensioni e Peso

| Campo | Tipo | Unità | Note |
|-------|------|-------|------|
| weight_gross_kg | DECIMAL(8,3) | kg | Con imballaggio |
| weight_net_kg | DECIMAL(8,3) | kg | Solo prodotto |
| weight_volumetric_kg | COMPUTED | kg | (L×W×H cm) ÷ 6000 |
| length_cm | DECIMAL(8,2) | cm | Lato più lungo |
| width_cm | DECIMAL(8,2) | cm | — |
| height_cm | DECIMAL(8,2) | cm | — |

### Peso Volumetrico

```
Peso Volumetrico (kg) = (Lunghezza × Larghezza × Altezza in cm) ÷ 6000

Peso Fatturabile = MAX(Peso Reale, Peso Volumetrico)
```

Alcuni corrieri usano divisore 5000 invece di 6000.

## 6.2 Packaging Hierarchy

```
Pallet (SSCC-18)
└── Master Carton (GTIN-14)
    └── Inner Carton (GTIN-14)
        └── Each (EAN-13)
```

Ogni livello ha proprie dimensioni, peso, codice a barre.

## 6.3 HS Codes e Country of Origin

### HS Codes (Harmonized System)

- 6 cifre internazionali + cifre addizionali per paese
- Critico per: classificazione doganale, calcolo dazi, statistiche commercio
- Obbligatorio per import/export

### Country of Origin (CoO)

- ISO 3166-1 alpha-2 (es. "CN" per Cina, "IT" per Italia)
- Obbligatorio per alcune categorie prodotto
- Quando produzione multi-paese: CoO = paese dell'ultima trasformazione sostanziale
- Per tessili: origine obbligatoria se prodotto fuori UE

## 6.4 Dangerous Goods Classification

- Classificazione per spedizione aerea/marittima/terrestre
- HS code critico per identificazione merci pericolose
- Amazon/eBay hanno requisiti specifici per DG (scheda sicurezza, etichettatura)
- Campi: `dangerous_goods` (bool), `dangerous_goods_class` (UN class), `msds_url` (scheda sicurezza)

---

# 7. Pricing Data Model

## 7.1 Struttura Prezzi

```
Cost Price (fornitore)
  + Margine target (es. 30%)
  + Commissione marketplace (es. 15% Amazon)
  + Costo spedizione
  + IVA (variabile per paese)
  = Selling Price
```

### Formula Calcolo

```javascript
function calculateSellingPrice(costPrice, params) {
  const {
    marginPercent = 0.30,
    commissionPercent = 0.15,
    shippingCost = 0,
    vatPercent = 0.22
  } = params;
  
  const priceBeforeVAT = (costPrice * (1 + marginPercent)) / (1 - commissionPercent) + shippingCost;
  const sellingPrice = priceBeforeVAT * (1 + vatPercent);
  
  return Math.round(sellingPrice * 100) / 100;
}
```

## 7.2 Margini e Commissioni Marketplace

| Marketplace | Commissione Media | Range | Note |
|-------------|------------------|-------|------|
| Amazon | 15% | 7-45% | Varia per categoria |
| eBay | 12% | 5-15% | + fee inserzione |
| Kaufland | 10% | 7-15% | Per categoria |
| Bol.com | 12% | 5-17% | Per categoria |
| Otto | 15% | 10-20% | Selettivo |
| Carrefour | 11% | 8-15% | Mirakl |
| Fnac-Darty | 13% | 10-16% | Mirakl |
| Leroy Merlin | 12% | 10-15% | + fee mensile 39€ |

### Benchmark Margini E-Commerce 2024-2025

- **Margine lordo medio**: ~45%
- **Margine netto medio**: ~10% (dopo marketing, fulfillment, operativo)
- Per categoria: Cosmetica 58%, Gioielli 63%, Elettronica 43%, Sport 41%

## 7.3 IVA per Paese EU

| Paese | Aliquota Standard | Aliquota Ridotta | Note |
|-------|------------------|-----------------|------|
| Italia | 22% | 4%, 5%, 10% | — |
| Germania | 19% | 7% | Ristorazione 7% dal 2026 |
| Francia | 20% | 5.5%, 10% | — |
| Spagna | 21% | 4%, 10% | — |
| Olanda | 21% | 9% | Alloggio 21% (dal 2024) |
| Polonia | 23% | 5%, 8% | — |
| Belgio | 21% | 6%, 12% | — |
| Austria | 20% | 10%, 13% | — |
| Portogallo | 23% | 6%, 13% | — |
| Romania | 21% | 5%, 9% → 11% | Consolidato agosto 2025 |
| Rep. Ceca | 21% | 12% | — |
| Slovacchia | 23% | 10% | — |
| Ungheria | 27% | 5%, 18% | Più alta in EU |
| Svezia | 25% | 6%, 12% | — |
| Danimarca | 25% | — | No aliquota ridotta |
| Finlandia | 25.5% | 10%, 14% | — |
| Croazia | 25% | 5%, 13% | — |
| Estonia | 22% → 24% | 9% → 13% | Aumento luglio 2025 |
| Lussemburgo | 17% | 3%, 8% | Più bassa in EU |

**Media EU**: 21.8% (aliquota standard)

**Regime OSS**: Vendiamonoi utilizza il regime OSS (One-Stop-Shop) per dichiarare l'IVA in tutti i paesi EU da un singolo punto.

## 7.4 MAP e RRP in Europa

### RRP (Recommended Retail Price)

- Prezzo suggerito dal produttore
- Non vincolante legalmente
- Formula: RRP = COGS + (COGS × Margine Lordo %) + Costi Distribuzione
- Supporta protezione margine e positioning brand

### MAP (Minimum Advertised Price)

**ATTENZIONE EU**: In Europa, MAP policy devono rispettare Articolo 101 TFEU:
- Trattate come violazione antitrust (resale price maintenance)
- Enforcement EU rigoroso: €611M di multa a 10 produttori elettrodomestici (dicembre 2024) per fissazione prezzi
- Non si possono restringere i prezzi di vendita effettivi, solo quelli pubblicizzati
- **Raccomandazione Vendiamonoi**: usare RRP (enforceable, brand-protecting) piuttosto che MAP (rischioso in EU)

## 7.5 Currency Handling

```sql
CREATE TABLE exchange_rates (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  from_currency VARCHAR(3) NOT NULL,
  to_currency VARCHAR(3) NOT NULL,
  rate DECIMAL(12,6) NOT NULL,
  source VARCHAR(50),  -- 'ecb', 'manual'
  valid_from TIMESTAMPTZ NOT NULL,
  valid_to TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE(from_currency, to_currency, valid_from)
);
```

Valute principali: EUR, GBP, PLN, CZK, SEK, DKK, RON, HUF.

---

# 8. Feed Specifications

## 8.1 Formati Feed

| Formato | Pro | Contro | Uso |
|---------|-----|--------|-----|
| **CSV** | Universale, semplice | No nested data | Bulk upload, legacy |
| **XML (BMEcat)** | Standard B2B, gerarchico | Verbose | Industriale, GDSN |
| **ETIM xChange (JSON)** | Moderno, API-friendly, leggibile | Meno supporto legacy | API, exchange moderno |
| **JSON** | Nesting naturale, web-native | — | REST API, feed moderni |

## 8.2 Amazon JSON_LISTINGS_FEED

- Formato: JSON Schema 2019-09
- Operazioni: PUT (create/replace), PATCH (update), DELETE
- Limite: 25.000 SKU per richiesta, 5 richieste per 5 minuti
- Schema dinamico via Product Type Definitions API
- **CRITICO**: Feed XML/flat file legacy deprecati dal 31 luglio 2025

## 8.3 Mirakl Product/Offer Distinction

```
1. Crea/matcha Prodotto → invia dati prodotto (titolo, descrizione, EAN, categoria, immagini)
2. Attendi accettazione prodotto dal marketplace operator
3. Crea Offerta → invia dati offerta (prezzo, stock, spedizione, condizioni)
4. Product.SellerProductID = product-id nel file offerta
```

Upload: CSV, XLS, XML o API REST.

## 8.4 API Real-Time vs Batch

| Aspetto | Batch (File) | Real-Time (API) |
|---------|-------------|----------------|
| Latenza | 5-24 ore | Secondi-minuti |
| Costo computazionale | Basso | Alto |
| Caso d'uso | Catalogo stabile | Prezzo, stock dinamici |
| Volume | Grandi cataloghi efficienti | Aggiornamenti incrementali |
| Formato | CSV, XML, JSON file | REST API JSON |

**Strategia Vendiamonoi**: Batch per catalogo iniziale e dati prodotto stabili. API real-time per aggiornamenti stock e prezzo (via SellRapido + Make.com/n8n).

---

# 9. Scalabilità e Performance

## 9.1 Materialized Views per Feed

```sql
CREATE MATERIALIZED VIEW mv_marketplace_feed AS
SELECT
  p.id,
  p.sku,
  p.ean,
  b.name AS brand,
  pt.title,
  pt.description,
  pt.bullet_points,
  mp.selling_price,
  mp.currency,
  s.available AS stock_quantity,
  pi.url_cdn AS main_image_url,
  ml.marketplace,
  ml.marketplace_category_id,
  cm.marketplace_category_path
FROM products p
JOIN brands b ON p.brand_id = b.id
LEFT JOIN product_translations pt ON p.id = pt.product_id
LEFT JOIN marketplace_prices mp ON p.id = mp.product_id
LEFT JOIN stock s ON p.id = s.product_id
LEFT JOIN product_images pi ON p.id = pi.product_id AND pi.image_type = 'main'
LEFT JOIN marketplace_listings ml ON p.id = ml.product_id
LEFT JOIN category_mappings cm ON p.category_id = cm.internal_category_id AND ml.marketplace = cm.marketplace
WHERE p.state_id = (SELECT id FROM product_states WHERE code = 'active');

-- Refresh concorrente (non blocca le query)
REFRESH MATERIALIZED VIEW CONCURRENTLY mv_marketplace_feed;

-- Index sulla vista
CREATE UNIQUE INDEX idx_mvfeed_id ON mv_marketplace_feed(id, marketplace);
CREATE INDEX idx_mvfeed_marketplace ON mv_marketplace_feed(marketplace);
```

**Scheduling**: Refresh ogni ora per dati stabili, ogni 5 minuti per stock/prezzo (o usare CDC).

## 9.2 Change Data Capture (CDC)

Per sync real-time senza impatto su production queries:

```
PostgreSQL → WAL (Write-Ahead Log) → Debezium → Kafka →
  ├── Feed Generator (aggiorna feed marketplace)
  ├── Search Index (aggiorna Elasticsearch/Typesense)
  ├── Cache Invalidation (invalida cache CDN)
  └── Analytics (stream verso data warehouse)
```

- Legge da WAL, non da tabelle (zero impatto sulle query)
- Cattura cambiamenti a livello di riga
- Event-driven: marketplace notificati in tempo reale

## 9.3 Bulk Operations

Per import cataloghi 100.000+ SKU:

```sql
-- 1. Usa COPY per insert (100x più veloce di INSERT)
COPY products_staging FROM '/path/to/file.csv' WITH (FORMAT csv, HEADER true);

-- 2. Batch update in transazioni (10.000 righe per batch)
UPDATE products p SET
  cost_price = s.new_price,
  updated_at = now()
FROM products_staging s
WHERE p.sku = s.sku;

-- 3. Disabilitare trigger durante bulk (riabilitare dopo)
ALTER TABLE products DISABLE TRIGGER ALL;
-- ... bulk operations ...
ALTER TABLE products ENABLE TRIGGER ALL;
```

## 9.4 Performance Targets

Per 1M+ SKU con indexing e partitioning corretti:

| Operazione | Target |
|-----------|--------|
| Lookup per SKU | <10ms |
| Lookup per EAN | <10ms |
| Generazione feed marketplace | 5-10 secondi (materialized view) |
| Full-text search | <100ms |
| Aggiornamento stock real-time | <1 secondo (CDC) |
| Import bulk 100K SKU | <5 minuti |
| Refresh materialized view | <30 secondi |

---

# 10. Pattern Vendiamonoi — Implementazione Completa

## 10.1 Architettura Complessiva

```
Fornitori (100+)
  │ CSV/XML/API/Excel
  ▼
┌─────────────────────┐
│ Import Pipeline      │ Make.com / n8n
│ (normalizzazione)    │
└─────────┬───────────┘
          ▼
┌─────────────────────┐
│ Supabase (PostgreSQL)│
│ PRODUCT MASTER DB    │
│ ┌─ products          │
│ ├─ translations      │
│ ├─ variants          │
│ ├─ images            │
│ ├─ stock             │
│ ├─ prices            │
│ └─ listings          │
└─────────┬───────────┘
          │ CDC / Materialized Views
          ▼
┌─────────────────────┐
│ SellRapido           │ Channel Manager
│ Feed Generator       │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────────────────┐
│ 20-30 Marketplace Europei       │
│ Amazon, eBay, Kaufland, Otto,   │
│ Bol.com, Carrefour, Fnac-Darty, │
│ Leroy Merlin, MediaWorld, etc.  │
└─────────────────────────────────┘
```

## 10.2 Workflow: Onboarding Nuovo Fornitore

1. Fornitore invia catalogo (qualsiasi formato)
2. Make.com/n8n: rileva schema, mappa campi via `supplier_field_mappings`
3. Validazione: EAN check digit, campi obbligatori, formato immagini
4. Normalizzazione: brand, categorie, unità misura
5. Deduplicazione: fuzzy match vs prodotti esistenti
6. Quality Gates: verifica completezza per marketplace target
7. Insert in `products` + `product_translations` + `product_images`
8. Stato → draft → review → active
9. Pubblicazione su marketplace via SellRapido

## 10.3 Workflow: Aggiornamento Prezzo Multi-Marketplace

1. Fornitore aggiorna listino prezzi
2. Make.com: rileva variazioni costo
3. Per ogni marketplace: ricalcola selling_price con formula
4. Aggiorna `marketplace_prices`
5. CDC: trigger aggiornamento su SellRapido
6. SellRapido: push prezzo a tutti i marketplace
7. Verifica: confronta prezzo pubblicato vs calcolato

## 10.4 Workflow: Sync Stock Real-Time

1. Schedule trigger (ogni 2 ore) o webhook fornitore
2. HTTP GET stock da fornitore
3. Compare vs `stock` table in Supabase
4. Update differenze
5. Se stock = 0: pausa offerta su marketplace
6. Se stock riappare: riattiva offerta
7. Log tutte le variazioni per analytics

## 10.5 Stack Tecnologico Consigliato

| Layer | Tool | Ruolo |
|-------|------|-------|
| Database | Supabase (PostgreSQL) | Product Master, Stock, Prezzi |
| Automazione | Make.com | Orchestrazione, logica business |
| Batch Processing | n8n (futuro) | ETL pesante, import cataloghi |
| Channel Manager | SellRapido | Pubblicazione multi-marketplace |
| Immagini | Cloudinary / ImageKit | Trasformazione, CDN |
| Search | Typesense / Supabase Full-Text | Ricerca prodotti |
| Monitoring | Supabase Dashboard + custom | KPI, qualità dati |

---

## FONTI E RIFERIMENTI

### Standard e Organizzazioni
- [GS1 Check Digit Calculator](https://www.gs1.org/services/check-digit-calculator)
- [GS1 GDSN](https://www.gs1.org/services/gdsn)
- [GS1 GPC Navigator](https://navigator.gs1.org/)
- [GS1 Verified](https://www.gs1.org/services/verified-by-gs1)
- [GS1 Data Quality Framework](https://www.gs1.org/services/data-quality/data-quality-framework)
- [ETIM International](https://www.etim-international.com/)
- [UNSPSC](https://findunspsc.com/en260801/)

### Marketplace Documentation
- [Amazon SP-API: Listings Items](https://developer-docs.amazon.com/sp-api/docs/listings-items-api)
- [Amazon Product Type Definitions](https://developer-docs.amazon.com/sp-api/docs/product-type-api-use-case-guide)
- [Amazon JSON_LISTINGS_FEED](https://developer-docs.amazon.com/sp-api/docs/listings-feed-type-values)
- [eBay Taxonomy API](https://developer.ebay.com/api-docs/commerce/taxonomy/overview.html)
- [eBay Item Specifics](https://www.ebay.com/sellercenter/listings/item-specifics-requirements)
- [eBay Catalog API](https://developer.ebay.com/api-docs/commerce/catalog/overview.html)
- [Kaufland Product Data Guideline](https://www.kauflandglobalmarketplace.com/en/seller-university/selling/product-data/product-data-guideline/)
- [Bol.com Product Content API](https://api.bol.com/retailer/public/Retailer-API/v10/functional/retailer-api/product-content-api.html)
- [Bol.com Image Rules](https://partnerplatform.bol.com/en/need-help/productinformation/optimise-images/)
- [Otto Marketplace Requirements](https://www.otto.market/en/howitworks/requirements.html)
- [Mirakl Catalog Manager](https://www.mirakl.com/products/mirakl-catalog-manager)
- [Mirakl Developer Portal](https://developer.mirakl.com/)
- [Carrefour Guide (ChannelEngine)](https://support.channelengine.com/hc/en-us/articles/5116910501405)
- [Leroy Merlin Guide (ChannelEngine)](https://support.channelengine.com/hc/en-us/articles/4700020245789)
- [Fnac-Darty Guide](https://cedcommerce.com/blog/sell-on-fnac-darty-marketplace/)

### Database e Architettura
- [Supabase RLS](https://supabase.com/docs/guides/database/postgres/row-level-security)
- [PostgreSQL JSONB vs EAV](https://www.razsamuel.com/postgresql-jsonb-vs-eav-dynamic-data/)
- [PostgreSQL Partitioning](https://www.velodb.io/glossary/ways-to-scale-postgresql)
- [Debezium CDC](https://www.confluent.io/blog/cdc-and-data-streaming-capture-database-changes-in-real-time-with-debezium/)
- [pg_trgm Extension](https://www.postgresql.org/docs/current/pgtrgm.html)

### Pricing e Normative
- [EU VAT Rates 2025](https://www.vatai.com/blog/2025-vat-rates-in-europe-country-rates-changes)
- [MAP Policy EU](https://dealavo.com/en/price-management-in-the-eu-legal-perspective-for-brands-and-e-shops/)
- [RRP Pricing](https://www.pricefy.io/articles/what-is-recommended-retail-price-rrp)
- [E-Commerce Margins 2024](https://trueprofit.io/blog/what-is-a-good-gross-profit-margin)

### Feed e Integrazione
- [ETIM xChange JSON](https://www.productsup.com/blog/etim-xchange-product-data-exchange-for-global-industrial-manufacturing/)
- [BMEcat Format](https://www.atamya.com/en/blog/bmecat/)
- [SellRapido Integrations](https://www.sellrapido.com/en/integrations/)
- [Amazon IDQ Score](https://incubeta.com/news-and-resources/the-amazon-idq-score-and-why-it-important/)
- [Icecat Product Data](https://icecat.com/)

---

**Documento creato**: 2026-04-06
**Parte del sistema**: Data Models / Product Information Management
**Complemento**: architecture/supabase/, architecture/sellrapido/, architecture/make/, architecture/n8n/
