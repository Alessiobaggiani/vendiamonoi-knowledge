# eBay Marketplace — Specifiche Tecniche Complete

**Versione:** 1.0
**Data:** Aprile 2026
**Target Audience:** Operatori tecnici avanzati, gestori multi-marketplace, integratori API
**Scope:** Piattaforma eBay Europa (B2C e C2C), architettura tecnica, conformità normativa, specifiche API

---

## Informazioni Generali

### Piattaforma eBay: Dati Essenziali

| Parametro | Dettaglio |
|-----------|----------|
| **Fondazione** | 1995 |
| **Modello** | Marketplace C2C/B2C |
| **Siti Europei** | ebay.de, ebay.it, ebay.fr, ebay.es, ebay.co.uk, ebay.be, ebay.nl, ebay.at, ebay.ie, ebay.pl |
| **Valuta Principale (EU)** | EUR (con conversione per UK/siti locali) |
| **Lingua Predefinita** | Locale per sito (es: italiano per ebay.it, francese per ebay.fr) |
| **Registrazione** | Email verificato + verifica identità (per seller EU) |
| **Verifica EU** | Obbligatoria dal 2025 per Business Sellers vendendo in UE (Digital Services Act) |

### Conformità Normativa Europea

- **Digital Services Act (DSA)**: Seller EU devono fornire nome azienda, indirizzo, email, numero di telefono, numero registrazione
- **DAC7**: Rendicontazione per seller con ≥30 transazioni O >2.000 EUR/anno
- **GPSR (General Product Safety Regulation)**: Applicabile a prodotti nuovi e usati nell'UE/NI
- **EPR (Extended Producer Responsibility)**: Responsabilità gestione rifiuti per produttori
- **OSS (One-Stop Shop VAT)**: Per vendite B2C transfrontaliere quando fatturato >10.000 EUR/anno

---

## 1. Architettura API

### Transizione API: Trading → REST

eBay ha completato la transizione dalle legacy Trading API (XML-based) alle REST API moderne. La piattaforma supporta ancora le Trading API per compatibilità backwards, ma lo sviluppo nuovo utilizza esclusivamente REST.

**API REST principali:**
- **Sell API** (Inventory, Account, Fulfillment, Marketing)
- **Buy API** (Browse, Buy)
- **Commerce API** (Notification, Taxonomy, Translation)
- **Developer API** (Analytics)

### Autenticazione: OAuth 2.0

eBay utilizza **OAuth 2.0** con due tipi di token:

**1. Application Token (Server-to-Server)**
```
POST https://api.ebay.com/identity/v1/oauth2/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id={your_client_id}
&client_secret={your_client_secret}
&scope=https://api.ebay.com/oauth/api_scope
```
- Valido per operazioni generiche (Inventory, Catalog)
- No scadenza di sessione utente

**2. User Token (Authorization Code Flow)**
```
GET https://auth.ebay.com/oauth2/authorize?
response_type=code&
client_id={your_client_id}&
redirect_uri={your_redirect_uri}&
scope=https://api.ebay.com/oauth/api_scope/sell.account
```
- Richiesto per operazioni utente-specifiche (Account, Orders)
- Refresh token fornito per rinnovamento

### Endpoint API per Marketplace

| API | Endpoint | Utilizzo | Rate Limit |
|-----|----------|----------|----------|
| **Inventory API** | `/sell/inventory/v1` | Creare/gestire offerte e listing | 2.000 call/ora |
| **Fulfillment API** | `/sell/fulfillment/v1` | Ordini, tracking, shipping | 1.000 call/ora |
| **Account API** | `/sell/account/v1` | Configurazione seller, policy | 500 call/ora |
| **Marketing API** | `/sell/marketing/v1` | Promoted Listings, pubblicità | 300 call/ora |
| **Browse API** | `/buy/browse/v1` | Ricerca, dettagli item (buyer) | 10.000 call/ora |
| **Taxonomy API** | `/commerce/taxonomy/v1` | Categorie, item specifics | 5.000 call/ora |
| **Feed API** | `/sell/feed/v1` | Operazioni bulk (25 MB max) | 400 request/giorno per feed type |
| **Analytics API** | `/developer/analytics/v1` | Rate limit monitoring | 10.000 call/ora |

**Nota:** Rate limit per application token. User token ha limiti aggiuntivi.

### Sandbox vs Produzione

- **Sandbox Environment**: `api.sandbox.ebay.com` - Test completo senza impatto dati reali
- **Produzione**: `api.ebay.com` - Sistema live
- **Credenziali separate** required per environment
- Account sandbox pre-caricato con seller/buyer fittizi per testing

### Esempio: Creare Inventory Item

```json
POST /sell/inventory/v1/inventory_item/SKU123456
Authorization: Bearer {user_token}
Content-Type: application/json

{
  "availability": {
    "shipToLocationAvailability": [
      {
        "availabilityType": "IN_STOCK",
        "quantity": 50,
        "shipToLocationId": "DEFAULT"
      }
    ]
  },
  "condition": "NEW",
  "listing_duration": "GTC",
  "pricing": {
    "pricingVisibility": "SHOW",
    "minimumAdvertisedPrice": {
      "currency": "EUR",
      "value": "29.99"
    },
    "price": {
      "currency": "EUR",
      "value": "49.99"
    }
  },
  "product": {
    "title": "iPhone 15 Pro Max 256GB Space Black",
    "description": "Brand new, sealed, warranty included",
    "imageUrls": [
      "https://example.com/image1.jpg",
      "https://example.com/image2.jpg"
    ],
    "aspects": {
      "Brand": ["Apple"],
      "Model": ["iPhone 15 Pro Max"],
      "Storage Capacity": ["256GB"],
      "Color": ["Space Black"],
      "Condition": ["New"]
    }
  }
}
```

**Response (201 Created):**
```json
{
  "sku": "SKU123456",
  "inventoryItemGroupKey": "item-group-001"
}
```

---

## 2. Tipi di Listing e Formati

### Auction vs Fixed Price

| Tipo | Caratteristiche | Durata | Reinserimento |
|------|-----------------|--------|------------------|
| **Auction** | Prezzo iniziale, offerte progressive | 1/3/5/7/10 giorni | Manuale |
| **Fixed Price** | Prezzo fisso "Buy It Now" | GTC (illimitato) o specifica | Automatico (GTC) |
| **Classified Ads** | Contatto diretto seller, no pagamento eBay | Variabile | Specifico |

### Good 'Til Cancelled (GTC)

- Listing rimane attivo fino a rimozione manuale o esaurimento scorte
- Rinnovamento automatico mensile se configurato
- Per Fixed Price listings
- Migliora ranking Cassini (segnale di seller affidabile)

### Multi-Variation Listings

Struttura per SKU multipli (colore, taglia, ecc.) su un unico listing:

```json
{
  "variations": [
    {
      "sku": "SKU-RED-XL",
      "specifics": {
        "Color": ["Red"],
        "Size": ["XL"]
      },
      "quantity": 25,
      "price": { "currency": "EUR", "value": "39.99" }
    },
    {
      "sku": "SKU-BLUE-XL",
      "specifics": {
        "Color": ["Blue"],
        "Size": ["XL"]
      },
      "quantity": 15,
      "price": { "currency": "EUR", "value": "39.99" }
    }
  ]
}
```

### Item Condition Values

Valori standardizzati (obbligatori):
1. **NEW** - Sigillato, originale, mai usato
2. **LIKE_NEW** - Usato ma perfette condizioni
3. **VERY_GOOD** - Minimi segni di usura
4. **GOOD** - Usato, normale usura visibile
5. **ACCEPTABLE** - Usato, difetti/danni visibili ma funzionante

---

## 3. Requisiti Dati Prodotto

### Item Specifics (Aspetti) per Categoria

Ogni categoria ha item specifics **obbligatori** e **consigliati**. Cassini premia completezza:

**Esempio: Cellulari**
```json
"aspects": {
  "Brand": ["Apple"],                          // Obbligatorio
  "MPN": ["MGML3ZD/A"],                        // Obbligatorio
  "Model": ["iPhone 15 Pro Max"],              // Obbligatorio
  "Operating System": ["iOS"],                 // Obbligatorio
  "Storage Capacity": ["256GB"],               // Obbligatorio
  "Color": ["Space Black"],                    // Obbligatorio
  "Network": ["Unlocked"],                     // Consigliato
  "Warranty": ["Apple 12 months"]              // Consigliato
}
```

**Impatto SEO:**
- Item specifics completi = +25-35% visibilità ricerca
- Specifics non compilati = downranking Cassini
- Specifics errati = removal listing

### Identificatori Prodotto (GTIN)

| Tipo | Formato | Utilizzo | Obbligatorio? |
|------|---------|----------|---------------|
| **EAN-13** | 13 cifre | Europa principalmente | No, ma consigliato |
| **UPC-A** | 12 cifre | Nord America | No |
| **ISBN** | 10 o 13 cifre | Libri | Sì se applicabile |
| **Brand/MPN** | Coppia | Alternativa GTIN | Sì se GTIN assente |

**Catalog Matching Benefit:** Se fornisci UPC/EAN valido, eBay tenta match con catalogo interno. Se trova match unico:
- Titolo, descrizione, foto standard applicate
- Specifics auto-compilati
- Consistenza dati garantita

### Immagini: Requisiti Tecnici

| Parametro | Specifica | Note |
|-----------|-----------|-------|
| **Dimensione Minima** | 500 pixel (lato lungo) | Zoom non attivo sotto 500px |
| **Dimensione Ottimale** | 800-1.600 pixel | Abilita zoom viewer |
| **Dimensione Massima** | 9.000 × 9.000 pixel | Limitazione piattaforma |
| **Formato Accettato** | JPEG, PNG, TIFF, BMP | NO GIF animate |
| **Qualità JPEG** | ≥90 su scala qualità | Compressione minima |
| **Peso Massimo** | 7 MB per immagine | Limitazione uploader |
| **Quantità** | 12 immagini standard, 1 video | Pro sellers: 24+ immagini |
| **Watermark** | Vietato | Incluso loghi, testi |
| **Bordi** | Vietati (eccetto naturali) | No cornici aggiunte |

### Titolo Listing

```
Limite: 80 caratteri esatti (inclusi spazi)
Formato: [Brand] [Modello] [Variant Key] [Condizione]

BUONO:  "iPhone 15 Pro Max 256GB Space Black nuovo"        (47 char)
CATTIVO: "IPHONE 15 PRO MAX 256GB!!! **SUPER OFFERTA**"    (Violazioni: caps, simboli)
```

**Impatto Cassini:**
- Keyword matching è principale fattore ranking
- Keyword stuffing = penalità
- Capitalizzazione eccessiva = downranking

### Descrizione

- **Formato**: Testo plain (HTML sconsigliato)
- **Lunghezza**: 4.000 caratteri (soft limit)
- **Limitazioni**: No HTML, no ALL CAPS, no font multipli
- **Active Content**: Flash, JavaScript bloccato per sicurezza

---

## 4. Struttura Commissioni in Europa

### Commissioni Transazione (da 2025)

#### Final Value Fee (Commissione Finale)

Calcolata su: **Prezzo articolo + Costi spedizione**

| Categoria | Fee % | Base | Incremento 2025 |
|-----------|-------|------|------------------|
| **Abbigliamento/Accessori** | 13.25% | $0.30 | +0.35% |
| **Elettronica** | 13.55% | $0.30 | +0.35% |
| **Casa/Giardino** | 12.90% | $0.30 | Invariato |
| **Libri/Fumetti** | 12.90% | $0.30 | Invariato |
| **Motori** | 13.25% | $0.30 | +0.35% |
| **Monete/Francobolli** | 15.00% | $0.30 | Invariato (max) |

**Nota UE:** Private C2C sellers su ebay.de residenti in EEA = ZERO Final Value Fee

#### Insertion Fee (Commissione Inserimento)

- **Private sellers EU**: 250 listing gratuiti/mese, poi €0,35 per listing aggiuntivo
- **Business sellers**: Variabile per piano Store

#### International Fee (Commissione Internazionale)

- **1.65%** calcolato su importo totale se buyer è fuori US
- Applicato in aggiunta a Final Value Fee
- Non applicabile per siti eBay European (buyer EU = no fee)

#### Managed Payments Processing Fee

```
2.70% + €0.25 per transazione
Incluso nel calcolo commissione totale
Supporta: Carte credito, PayPal, Bonifici EU SEPA
```

#### Subscription Store

| Tier | Costo Mensile (Annual) | Costo Mensile (Monthly) | Listing Gratuiti | FVF Ridotto |
|------|----------------------|------------------------|------------------|----------|
| **Starter** | €4,95 | €7,95 | 100 | -0.5% |
| **Basic** | €24,95 | €27,95 | 500 | -1.0% |
| **Premium** | €59,95 | €74,95 | 2.000 | -1.5% |
| **Anchor** | €299,95 | €349,95 | 10.000 | -2.0% |
| **Enterprise** | €2.995,00 | N/A | 100.000 | -2.0% + concierge |

**Break-even Example (Premium):**
```
€74,95 costo mensile
Sconto FVF: 1.5% su vendite
Break-even volume: €5.000 vendite/mese (€75 risparmio)
ROI: Positivo da volume medio ≥ 5-7k €/mese
```

#### Promoted Listings (Pubblicità)

- **Standard (CPC)**: Pay per click, bid automatico
- **Advanced/Priority (CPC)**: Controllo manuale, max CPC, budget mensile
- **Range CPC**: €0,05 - €20,00 per click
- **Budget Minimo**: No minimo giornaliero, ma €0,10-1,00/giorno consigliato

---

## 5. Gestione Ordini

### Order States (Lifecycle)

```
CREATED (ricevuto)
  ↓
APPROVED (pagamento confermato)
  ↓
SHIPPED (tracking fornito)
  ↓
DELIVERY_IN_PROGRESS (in transito)
  ↓
COMPLETED (consegnato)
  ↓
[RETURNED] (opzionale, se reso accettato)
```

### Managed Payments Flow

1. **Buyer paga** tramite eBay Managed Payments (centralized)
2. **eBay detiene fondi** in escrow per protezione
3. **Seller spedisce** entro 2 giorni da payment confirmation
4. **Buyer riceve** tracking entro SLA
5. **Buyer accetta consegna** (automatico dopo ~5 giorni)
6. **eBay libera fondi** a seller con payout programmato

### Shipping Requirements e SLA

| Metrica | SLA | Penalità Cassini |
|---------|-----|------------------|
| **Handling Time** | ≤2 giorni (top rated) | Late marking se >2gg |
| **Tracking Upload** | Entro 2 giorni da spedizione | Defect se mancante |
| **On-Time Delivery** | Per buyer location | Late shipment penalità se >3% |
| **Return Processing** | 30 giorni da ricevimento | Defect se tardivo |

### Global Shipping Programme (GSP)

**Flusso:**
```
Seller ship to → eBay Hub (UK per EU sellers)
                    ↓
                Customs clearance (eBay manages)
                    ↓
                International carrier delivery (DHL, FedEx, etc.)
                    ↓
                Buyer receives
```

**Vantaggi:**
- Seller protezione per spedizioni internazionali (no carrier liability)
- Automatico calcolo dazi/tasse buyer
- Cobertura 60+ milioni buyer worldwide
- Seller ship domestic only

**Costi:**
- No insertion fee per GSP
- Finale Value Fee applicata
- Import/duty sul buyer (transparent pricing)

### eBay International Shipping (eIS)

**Disponibilità:** Solo US/Canada sellers
**Copertura:** 200+ destinazioni
**Costo:** Zero per seller (buyer paga tutto)
**Eligibilità:** Top Rated or Above Standard seller

---

## 6. Standard Performance Seller

### Metriche di Valutazione

Valutate **mensilmente** il giorno 20 basato su **ultimi 90 giorni di transazioni**:

| Metrica | Below Standard | Above Standard | Top Rated |
|---------|---|---|---|
| **Transaction Defect Rate** | >2% | ≤2% | ≤0.5% |
| **Late Shipment Rate** | >5% | ≤5% | ≤3% |
| **Cases Closed w/o Resolution** | >0.3% | ≤0.3% | ≤0.3% |
| **Tracking Upload Rate** | <90% | ≥90% | ≥95% |

### Seller Levels e Conseguenze

| Livello | Badge | Benefici | Restrizioni |
|---------|-------|----------|-------------|
| **Below Standard** | None | None | Listing limits ridotti, no promotional tools |
| **Above Standard** | None | GSP/International available | Standard limits |
| **Top Rated** | **Green badge** | -2% FVF, buyer trust signal | Deve mantenere standard |
| **Top Rated Plus** | **Green badge +** | -20% FVF boost, featured listing | Stringent requirements |

### Defect Definition

- **Transaction Defect**: Item Not As Described, Return/Refund, Case Opened Against Seller
- **Late Shipment**: Spedito dopo 3 giorni (handling time) + variabilità carrier
- **Case Closed w/o Resolution**: Unresolved dispute in Resolution Center

---

## 7. Managed Payments

### Come Funziona

eBay gestisce **100% della tesoreria seller**. Tutti i pagamenti fluiscono attraverso piattaforma centralizzata:

```
Buyer Payment → eBay Escrow → Seller Account → Bank Payout
                (hold period)
```

### Payout Schedule Options

| Frequenza | Timing | Giorni Lavorativi Banca |
|-----------|--------|----------------------|
| **Daily** | Entro 2 giorni da payment | 1-3 giorni |
| **Weekly** | Ogni martedì (Mon-Sun precedente) | 1-3 giorni |
| **Biweekly** | Ogni 2° martedì | 1-3 giorni |
| **Monthly** | Primo martedì mese | 1-3 giorni |

**Express Payout (Opzionale):**
- €2,00 fee flat per payout
- Stesso giorno processamento (se ante 14:00 CET)

### Fee Structure Managed Payments

```
Total Fee: 2.70% + €0.25 + [International fee se applicabile]

Esempio €100 vendita EU:
- Final Value: €13.25
- Managed Payments: €2.70 + €0.25 = €2.95
- Total Fee: €16.20 (16.2%)
- Seller receives: €83.80
```

### Metodi Pagamento Supportati (EU)

- Carte credito/debito (Visa, Mastercard, Amex)
- PayPal (collegato account)
- Bonifici SEPA (buyer business)
- Wallets locali (Apple Pay, Google Pay, ecc.)

---

## 8. eBay SEO e Algoritmo Cassini

### Panoramica Cassini

Cassini è l'algoritmo machine-learning di ranking eBay. A differenza di Google (indice globale), Cassini ottimizza per **likelihood to purchase** dentro l'ecosistema eBay.

**Fattori Ranking Cassini (2025):**

| Fattore | Peso Stimato | Impatto |
|---------|-------------|----------|
| **Keyword Relevance (Titolo)** | 30% | Alta - Matching diretto query |
| **Sales Velocity** | 20% | Alta - Listing converti bene |
| **Item Specifics Completeness** | 15% | Media-Alta - Structured data |
| **Seller Rating** | 15% | Media - Feedback, performance |
| **Click-Through Rate (CTR)** | 10% | Media - Engagement signal |
| **Pricing Competitiveness** | 5% | Bassa-Media - Price matching |
| **Shipping Speed** | 3% | Bassa - Handling time <2gg |
| **Image Quality** | 2% | Bassa - Zoom-enabled images |

### Keyword Optimization

```
Titolo (max 80 char):
❌ BAD: "SUPER MEGA OFFERTA CALDO IPHONE TELEFONO CELLULARE"
✓ GOOD: "Apple iPhone 15 Pro Max 256GB Space Black nuovo"

Pattern: [Brand] [Model] [Key Variant] [Condition]
```

**Title Strategies:**
- Front-load keywords (primo 50 caratteri decisivi)
- No keyword stuffing (penalità algoritmica)
- No ALL CAPS (bad UX, downranking)
- Include primary intent (brand, model)

### Item Specifics Impact

```json
Completezza Impact:
- 100% specifics completi: +25% visibilità ricerca
- 60% specifics: -10% visibilità
- <40% specifics: Likely delisting

Per categoria Electronics:
Minimo 8 specifics, target 12+
```

### Seller Metrics e Cassini

**Transaction Defect Rate Impact:**
```
<0.5%  → Boost ranking
0.5-2% → Neutral
>2%    → Penalty ranking (-15%)
>5%    -> Likely delisting
```

### Promoted Listings e Cassini

Promoted Listings **non influenzano** organic Cassini ranking, ma:
- Aumentano CTR organica (più visibilità pagina search)
- Più ordini → better sales velocity signal
- Indiretto boost tramite performance metrics

---

## 9. Promoted Listings (Campagne Pubblicitarie)

### Campaign Types

**Standard Promoted Listings (Legacy)**
- Simple CPC bidding
- No budget controls
- eBay smart placement
- Sunset planned 2026

**Priority Promoted Listings (New Standard)**
- Launched 2025
- Manuale keyword + bids
- Budget mensile (pacing)
- **Exclusive first ad slot** (top of search) dal January 2026

### CPC Bidding Model

Secondo prezzo auction (come Google Ads):

```
Bid 1: €1,50
Bid 2: €1,20 ← Winner pays €1,21 (Bid 2 + €0,01)
Bid 3: €0,80

Winner: Bid 1 seller @ €1,21 CPC
```

**Fattori Prezzo:**
- Listing quality score (eBay internal)
- Keyword competitiveness
- Reserve price floor (minimum)
- Your bid amount

### Budget Pacing (June 2025+)

**Monthly Averaging:**
```
Target Daily Budget: €10
Calendar Month Budget: €304 (10 × 30.4)
eBay distributes spend over 30 days
No overspend > 30.4x daily budget
```

### CPC Recommendations

| Competitiveness | Recommended CPC | Conversion Expectancy |
|-----------------|-----------------|----------------------|
| Low (new seller) | €0,10-0,30 | 1-2% ROAS |
| Medium | €0,50-1,00 | 2-4% ROAS |
| High | €1,50-3,00 | 4-8% ROAS |
| Ultra-competitive | €3,00+ | 8%+ (if optimized) |

**ROAS Calculation:**
```
Revenue from ads / Ad spend = ROAS
Example: €500 sales from €50 ads = 10x ROAS (900% ROI)
eBay target: 5-15x ROAS depending category
```

---

## 10. eBay Stores

### Store Types e Costi

| Tipo | Abbonamento | Free Listings | FVF Discount | Tools |
|------|-------------|---------------|-------------|-------|
| **No Store** | €0 | 250/mese | 0% | Basic |
| **Starter** | €4,95/mese | 100 | 0.5% | SEO, Marketing |
| **Basic** | €24,95/mese | 500 | 1,0% | + Markdown Manager |
| **Premium** | €59,95/mese | 2.000 | 1,5% | + Email marketing |
| **Anchor** | €299,95/mese | 10.000 | 2,0% | + Logo, branded page |
| **Enterprise** | €2.995/mese | 100.000 | 2,0% | + Concierge support |

### Store Features per Tier

**Markdown Manager** (Premium+)
- Sconti scheduling automatico
- Bulk discount application
- Inventory-linked pricing

**Custom Pages** (Anchor+)
- Branded landing page
- Custom CSS/HTML
- Cross-promotion tools

**Email Marketing** (Premium+)
- Newsletter builder
- Buyer list targeting
- Performance analytics

### Terapeak (Market Research)

Incluso in store Basic+:
- Demand/competition metrics
- Price trends (90-day)
- Category analysis
- Seasonal patterns

---

## 11. Operazioni Bulk e API Feed

### Feed API Overview

Processamento **asincronico** di operazioni bulk (listing creazione, modifica, cancellazione).

| Proprietà | Specifica |
|-----------|----------|
| **File Type** | XML (.xml) o ZIP (.zip) |
| **Max Size** | 25 MB per file |
| **Encoding** | UTF-8 |
| **Feed Types** | AddFixedPriceItem, ReviseFixedPriceItem, EndFixedPriceItem, ecc. |
| **Rate Limit** | 400 request/giorno per feed type per seller |

### XML Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<BulkDataExchangeRequests>
  <AddFixedPriceItem>
    <Item>
      <SKU>BULK-SKU-001</SKU>
      <Title>Product Title Max 80 Characters Here</Title>
      <Description>Product description text...</Description>
      <PrimaryCategory>
        <CategoryID>172008</CategoryID>
      </PrimaryCategory>
      <StartPrice>49.99</StartPrice>
      <Quantity>100</Quantity>
      <ListingDuration>GTC</ListingDuration>
      <ItemSpecifics>
        <NameValueList>
          <Name>Color</Name>
          <Value>Black</Value>
        </NameValueList>
        <NameValueList>
          <Name>Size</Name>
          <Value>Large</Value>
        </NameValueList>
      </ItemSpecifics>
      <PictureDetails>
        <PictureURL>https://example.com/image1.jpg</PictureURL>
      </PictureDetails>
      <ShippingDetails>
        <ShippingType>Flat</ShippingType>
        <ShippingServiceOptions>
          <ShippingServicePriority>1</ShippingServicePriority>
          <ShippingService>StandardInternational</ShippingService>
          <ShippingServiceCost>10.00</ShippingServiceCost>
        </ShippingServiceOptions>
      </ShippingDetails>
      <ReturnPolicy>
        <ReturnsAccepted>ReturnsAccepted</ReturnsAccepted>
        <RefundOption>MoneyBack</RefundOption>
        <ReturnsWithinOption>Days_30</ReturnsWithinOption>
        <ShippingCostPaidByOption>Buyer</ShippingCostPaidByOption>
      </ReturnPolicy>
    </Item>
  </AddFixedPriceItem>
</BulkDataExchangeRequests>
```

### Feed Submission via API

```
POST /sell/feed/v1/inventory_task

{
  "feed_type": "LMS_FIXED_PRICE_ITEM",
  "file": "<binary_file_content>",
  "feed_file_metadata": {
    "inputFileName": "bulk-listings.xml"
  }
}
```

**Response:**
```json
{
  "task_id": "task123456789",
  "feed_type": "LMS_FIXED_PRICE_ITEM",
  "status": "IN_PROGRESS",
  "creation_date": "2025-04-01T10:30:00Z"
}
```

**Polling Status:**
```
GET /sell/feed/v1/inventory_task/task123456789
```

### Processing Timeline

```
Upload → Queued → Processing → Completed/Failed
                     (1-24 ore tipicamente)
```

---

## 12. Limitazioni e Restrizioni

### Listing Limits

| Seller Type | Monthly Limit | Reasons |
|-------------|--------------|----------|
| **New Seller** | 10 listings | Per fraud prevention |
| **New Seller + Phone Verified** | 50 listings | Basic verification |
| **Established (3+ months, clean)** | 250.000+ | No limit practically |
| **Suspended/Below Standard** | 0 | Automatic enforcement |

### Category Restrictions

**Alcune categorie richiedono:**
- Seller authorization
- Specific feedback threshold
- Business registration
- Insurance (rare)

**Esempi:**
- **Veicoli**: 50 positive feedback minimo
- **Wines/Spirits**: ID verificato + local regulations
- **Weapons**: Extreme restrictions per jurisdiction
- **Electronics**: Authentic seller program consigliato

### API Rate Limits Dettagliati

```
Inventory API:     2.000 call/ora
Fulfillment API:   1.000 call/ora
Account API:         500 call/ora
Marketing API:       300 call/ora
Browse API:       10.000 call/ora
Taxonomy API:      5.000 call/ora
Feed API:            400 request/giorno
```

**Throttling Behavior:**
- Rate limit hit → 429 HTTP response
- Exponential backoff recommended (retry dopo 60-300s)
- Analytics API espone limiti attuali via `getRateLimits`

### VeRO (Verified Rights Owner Program)

IP owners (Nike, Apple, ecc.) possono:
- Riportare listing contraffatti
- Suspension seller automatica se violazione
- Recovery difficile (require company authorization)

---

## 13. Specifiche VAT e Compliance EU

### Registrazione VAT OSS

**Trigger:** Vendite B2C transfrontaliere >€10.000/anno

```
€10.001 vendite annue
  ↓
Obbligo registrazione OSS (One-Stop-Shop)
  ↓
Dichiarazione trimestrale (IVA % per paese destinazione)
  ↓
Pagamento singolo a autorità fiscale home country
```

### DAC7 Reporting

**Trigger:** ≥30 transazioni **O** >€2.000 pagamenti/anno

eBay fornisce automaticamente:
```
{
  "seller_id": "abc123",
  "year": 2025,
  "gross_transaction_value": 25000.00,
  "transaction_count": 45,
  "report_to": "German Tax Authority (BZSt)"
}
```

Seller riceve **copia dichiarazione** per tax filing.

### GPSR Compliance

Seller responsabile di:
- Product safety standards (EN/ISO)
- Safety information documentation
- Traceability (tracciabilità prodotto)
- Non-compliance reporting

**eBay enforcement:** Può suspendere listing se buyer reports GPSR violation.

### EPR (Extended Producer Responsibility)

Applicabile a:
- Electrical equipment
- Batteries
- Packaging
- Textiles (new as of 2025)

Seller deve:
- Register con organismi EPR per paese
- Pagare fees (€0,10-5,00 per prodotto tipicamente)
- Provide proof se richiesto

---

## 14. Taxonomy API e Categorie

### Category Lookup

```
GET /commerce/taxonomy/v1/category_tree/{marketplaceId}

Marketplaces:
EBAY_IT = 101 (Italy)
EBAY_DE = 77 (Germany)
EBAY_FR = 71 (France)
EBAY_GB = 3 (UK)
EBAY_ES = 186 (Spain)
```

**Response:**
```json
{
  "categoryTreeId": 101,
  "categories": [
    {
      "categoryId": "172008",
      "categoryName": "Telefoni Cellulari",
      "categoryLevel": 3,
      "parentCategoryId": "7881",
      "isLeafCategory": true,
      "defaultCategoryTreeId": 101
    }
  ]
}
```

### Item Specifics per Category

```
GET /commerce/taxonomy/v1/category/{categoryId}/aspects

Response:
{
  "aspects": [
    {
      "localizedAspectName": "Brand",
      "aspectEnable": "ENABLED",
      "aspectRequired": true,
      "aspectValues": [
        { "localizedValue": "Apple" },
        { "localizedValue": "Samsung" }
      ]
    }
  ]
}
```

---

## 15. International Selling Europe

### eBay European Sites

| Sito | Lingua | Valuta | Particolarità |
|------|--------|--------|----------------|
| **ebay.de** | Tedesco | EUR | Largest EU site |
| **ebay.it** | Italiano | EUR | C2C strong |
| **ebay.fr** | Francese | EUR | Fashion-focused |
| **ebay.es** | Spagnolo | EUR | Growing market |
| **ebay.co.uk** | Inglese | GBP | Post-Brexit |
| **ebay.be** | Olandese/Fr | EUR | Smaller |
| **ebay.nl** | Olandese | EUR | Strong C2C |
| **ebay.at** | Tedesco | EUR | Austrian niche |
| **ebay.ie** | Inglese | EUR | Irish market |
| **ebay.pl** | Polacco | PLN | Eastern Europe |

### Cross-Border Listing Strategy

**Option 1: Global Listing (US seller)**
- List on ebay.com (ships worldwide)
- GSP handles international
- International fee 1.65% added

**Option 2: EU Seller Multi-Site**
- Separate listings per site (EU seller)
- Optimize per locale (language, currency)
- No international fee (intra-EU)
- Better Cassini ranking (local relevance)

**Option 3: Hybrid**
- Core categories on main site (ebay.de if Germany-based)
- Expansion to adjacent markets (ebay.at, ebay.ch)
- Use GSP for distant markets

---

## 16. Analytics e Performance Dashboard

### Seller Hub Metrics

**Real-time KPIs:**
```
- Listing Views: Impressione unique per listing
- Conversion Rate: Orders / Views
- Sell-Through Rate: Sold items / Listed items
- Average Price: ARV (average realized value)
- Shipping Costs: Compare vs eBay estimates
```

### Analytics API

```
GET /developer/analytics/v1/user_rate_limit

Response:
{
  "resource": "Inventory API",
  "limit": 2000,
  "remaining": 1847,
  "resetTime": 3600,
  "timeWindow": 86400
}
```

---

## 17. Confronto eBay vs Amazon

| Aspetto | eBay | Amazon |
|--------|------|--------|
| **Fee Structure** | Final Value 13-15% + Insertion | Referral 15% + Fulfillment (FBA) |
| **Listing Approval** | Immediate | 48h review (new ASIN) |
| **SEO Ranking** | Cassini (sales velocity heavy) | A9 (keyword, price, ratings) |
| **Seller Authority** | Strong (feedback) | Lower (brand-gated) |
| **Auction Support** | Yes (core feature) | No |
| **Logistics** | GSP optional | FBA required (competitive) |
| **Target Audience** | B2B buyers, vintage collectors, niches | Mainstream consumers |
| **Bulk Operations** | Feed API (async) | Vendor Central (sync) |
| **Brand Controls** | VeRO only | Brand Registry strict |
| **International** | Native multi-site | US-centric (EU via logistic partners) |

---

## 18. Risorse Ufficiali

### Developer Documentation

- [eBay REST API Docs](https://developer.ebay.com/api-docs/static/ebay-rest-landing.html)
- [Sell API Overview](https://developer.ebay.com/api-docs/sell/static/overview.html)
- [Inventory API Reference](https://developer.ebay.com/api-docs/sell/inventory/overview.html)
- [Fulfillment API Reference](https://developer.ebay.com/api-docs/sell/fulfillment/overview.html)
- [Analytics API](https://developer.ebay.com/api-docs/developer/analytics/overview.html)

### Policy e Seller Resources

- [eBay Seller Center](https://www.ebay.com/sellercenter)
- [eBay International Selling](https://export.ebay.com/en/)
- [Seller Performance Policies](https://export.ebay.com/en/growth/seller-performance/)
- [Global Shipping Programme](https://pages.ebay.com/shipping/globalshipping/)
- [VAT/Tax Resources](https://export.ebay.com/en/fees-regulations-policies/taxes/)
- [Digital Services Act Compliance](https://www.ebay.com/sellercenter/resources/digital-services-act)

### Community e Support

- [eBay Developers Forum](https://forums.developer.ebay.com/)
- [eBay Community Boards](https://community.ebay.com/)
- [eBay Seller Handbook](https://pages.ebay.com/seller-center/tools-resources/)

---

## Changelog

| Versione | Data | Aggiornamenti |
|----------|------|---------------|
| 1.0 | April 2026 | Documento iniziale, API REST, Cassini, Fee 2025, Promoted Listings Priority, DAC7, OSS compliance, Store tiers |

---

## Disclaimer

Questo documento rappresenta specifiche tecniche pubbliche di eBay Marketplace al 2026-04. eBay modifica policy e fee regolarmente. Consultare always documentation ufficiale (developer.ebay.com, export.ebay.com) per informazioni attuali. Questo documento non costituisce consulenza legale o fiscale.

**Last Update:** 2026-04-01
**Target Version:** eBay API 2025.Q2+
