# Amazon Marketplace — Specifiche Tecniche Complete

**Versione:** 1.0
**Data:** Aprile 2026
**Lingua:** Italiano
**Target:** Gestori tecnici di operazioni multi-marketplace a livello enterprise
**Status:** Documento di riferimento universale

---

## Informazioni Generali

### Marketplace Europei Amazon

| Marketplace | Dominio | Lingua Ufficiale | Launch | Valuta | Categoria |
|-------------|---------|------------------|--------|--------|----------|
| Germania | amazon.de | Tedesco | 1997 | EUR | Marketplace maturo (maggiore volume EU) |
| Francia | amazon.fr | Francese | 1998 | EUR | Marketplace maturo |
| Italia | amazon.it | Italiano | 1999 | EUR | Marketplace in crescita |
| Spagna | amazon.es | Spagnolo | 1999 | EUR | Marketplace in crescita |
| Regno Unito | amazon.co.uk | Inglese | 1998 | GBP | Marketplace maturo (post-Brexit) |
| Paesi Bassi | amazon.nl | Olandese | 2018 | EUR | Marketplace in espansione |
| Belgio | amazon.be | Francese/Olandese | 2020 | EUR | Marketplace emergente |
| Polonia | amazon.pl | Polacco | 2020 | PLN | Marketplace emergente |
| Svezia | amazon.se | Svedese | 2020 | SEK | Marketplace emergente |
| Irlanda | amazon.ie | Inglese | 2025 | EUR | Marketplace nuovo |

### Requisiti di Registrazione Base

- **Account Type:** Professional Seller (consigliato) o Individual
- **Sottoscrizione:** €39,99/mese (Professional) o 0,99 EUR/articolo venduto + €0,20 (Individual)
- **Documento d'identità:** Richiesto durante la verifica dell'account
- **Metodo di pagamento:** Carta di credito/debito valida
- **Numero IVA:** Obbligatorio per venditori non-UE e per registrazione VAT in UE
- **Informazioni bancarie:** Coordinate IBAN per settlement dei pagamenti

---

## 1. SP-API (Selling Partner API)

### 1.1 Panoramica Architetturale

La **Selling Partner API (SP-API)** è l'API REST moderna che sostituisce il deprecated MWS (Marketplace Web Service). Fornisce accesso programmatico a:

- Dati di ordini e spedizioni
- Gestione inventario
- Informazioni prodotto
- Dati finanziari
- Metriche di performance

**Documentazione ufficiale:** https://developer-docs.amazon.com/sp-api

### 1.2 Autenticazione

#### Login with Amazon (LWA)

Meccanismo OAuth 2.0 che consente l'autenticazione sicura:

```
POST https://api.amazon.com/auth/o2/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&code=[AUTH_CODE_FROM_SELLER]
&client_id=[YOUR_CLIENT_ID]
&client_secret=[YOUR_CLIENT_SECRET]
```

**Response:**
```json
{
  "access_token": "Atzc2z...",
  "refresh_token": "Atzr...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

- **Validità Access Token:** 1 ora
- **Validità Refresh Token:** Fino a 10 anni

#### IAM Roles (AWS Identity and Access Management)

Per applicazioni ospitate su AWS, utilizza IAM roles per assumere credenziali temporanee:

```
AssumeRole ARN: arn:aws:iam::ACCOUNT_ID:role/AmazonSellingPartnerAPIRole
```

### 1.3 Tipi di API

#### Sellers API
- Accesso dati per merchant che vendono su Amazon
- Controllo completo su inventory, pricing, ordini
- Rate limit standard per ciascun endpoint

#### Vendors API
- Accesso dati per vendor (distribuzione diretta da Amazon)
- Accesso a PO (purchase orders), fatturazione
- Diversi rate limit rispetto Sellers API

### 1.4 Rate Limits

Ogni endpoint SP-API ha specifici rate limit basati sul **token bucket algorithm**:

| Endpoint | Rate Limit (Requests/sec) | Burst | Tipo |
|----------|---------------------------|-------|------|
| Orders API (GET) | 1/min | 2 token | Statico |
| Listings Items API | Dinamico | Variabile | Dinamico |
| Catalog Items API | Dinamico | Variabile | Dinamico |
| Feeds API (createFeed) | Varia per feed type | Variabile | Dinamico |
| Reports API | Varia | Variabile | Dinamico |
| Product Pricing API | Dinamico | Variabile | Dinamico |

**Strumento di calcolo:** https://developer-docs.amazon.com/sp-api/docs/usage-plans-and-rate-limits

**Errore di throttling:** `429 Too Many Requests`

### 1.5 Ambienti

#### Sandbox (Testing)
- Endpoint: `https://sandbox.sellingpartnerapi-na.amazon.com`
- Dati di test forniti da Amazon
- No rate limit enforcement
- Utilizzare per sviluppo e test

#### Production
- Endpoint: `https://sellingpartnerapi-eu.amazon.com` (EU)
- Endpoint: `https://sellingpartnerapi-na.amazon.com` (NA)
- Endpoint: `https://sellingpartnerapi-fe.amazon.com` (FE)
- Dati reali
- Rate limit enforcement attivo

### 1.6 Endpoint Principali

#### Catalog Items API
Retrieve informazioni prodotto dal catalogo Amazon:

```
GET /catalog/2022-04-01/items
Query params:
  - identifiers (ASIN, SKU, UPC, GTIN, EAN)
  - identifiersType
  - marketplaceIds (es: "A1F83G7Y2T0IW2", "A1PA6795UKMFR9" per EU)
```

**Risposta:**
```json
{
  "items": [{
    "asin": "B08KCSB2TY",
    "attributes": {
      "title": "Product Name",
      "brand": "Brand Name",
      "bulletPoints": ["Feature 1", "Feature 2"],
      "description": "Long description",
      "images": [{"link": "https://images..."}],
      "color": "Blue",
      "size": "Large"
    }
  }]
}
```

#### Listings Items API
Gestisci i tuoi listing:

```
PATCH /listings/2021-08-01/items/{sellerId}/{sku}
Content-Type: application/json

{
  "attributes": {
    "title": "Updated Title",
    "brand": "Brand Name",
    "description": "Description",
    "bullet_points": ["Point 1", "Point 2"],
    "item_type": "PRODUCT_TYPE",
    "fulfillment_channels": ["FBA", "FBM"]
  }
}
```

**Rate limit:** Dinamico (varia per account)

#### Orders API
Recupera informazioni ordini:

```
GET /orders/v0/orders
Query params:
  - CreatedAfter (ISO 8601 datetime)
  - CreatedBefore (ISO 8601 datetime)
  - OrderStatuses (PendingFulfillment, Unshipped, PartiallyShipped, Shipped, etc.)
  - MarketplaceId
```

**Limit importante:** Max 1 request/min - pianifica strategicamente

```json
{
  "Orders": [{
    "AmazonOrderId": "123-1234567-1234567",
    "OrderStatus": "Unshipped",
    "FulfillmentChannel": "FBM",
    "OrderItems": [{
      "ASIN": "B08KCSB2TY",
      "SellerSKU": "MY-SKU-001",
      "QuantityOrdered": 1,
      "QuantityShipped": 0
    }],
    "ShipmentServiceLevelCategory": "Standard",
    "OrderTotal": {
      "CurrencyCode": "EUR",
      "Amount": "49.99"
    }
  }]
}
```

#### Feeds API
Bulk update di dati tramite upload file:

```
POST /feeds/2021-06-30/feeds
Content-Type: application/json

{
  "feedType": "JSON_LISTINGS_FEED",
  "marketplaceIds": ["A1F83G7Y2T0IW2"],
  "inputFeedDocumentId": "amzn1.upload.feed.7d234ad8-5533-..."
}
```

**Feed type supportati (2025+):**
- `JSON_LISTINGS_FEED` - Creazione/aggiornamento listing (CONSIGLIATO)
- `JSON_LISTINGS_PRICING_FEED` - Aggiornamento prezzo
- `JSON_LISTINGS_INVENTORY_FEED` - Aggiornamento inventario
- `JSON_LISTINGS_PRODUCT_IMAGE_FEED` - Gestione immagini
- ~~`POST_PRODUCT_DATA`~~ - **DEPRECATO dal 31 luglio 2025**

#### Reports API
Genera e scarica report bulk:

```
POST /reports/2021-06-30/reports
{
  "reportType": "GET_FLAT_FILE_OPEN_LISTINGS_DATA",
  "marketplaceIds": ["A1F83G7Y2T0IW2"],
  "reportOptions": {
    "reportFrequency": "DAILY"
  }
}
```

**Report types comuni:**
- `GET_FLAT_FILE_OPEN_LISTINGS_DATA` - Listing aperti
- `GET_MERCHANT_LISTINGS_DATA` - Listing FBM
- `GET_FBA_FULFILLMENT_CURRENT_INVENTORY_DATA` - Inventario FBA
- `GET_SELLER_FEEDBACK_DATA` - Feedback clienti
- `GET_ORDER_REPORT_DATA_GENERAL` - Dati ordini (bulk)

#### Fulfillment Inbound API
Gestisci shipment FBA in ingresso:

```
POST /fba/inbound/v0/shipments
{
  "ShipmentName": "Shipment-001",
  "DestinationFulfillmentCenterId": "PHX",
  "InboundShipmentItems": [{
    "SellerSKU": "MY-SKU-001",
    "FulfillmentNetworkSKU": "FN-001",
    "Quantity": 100
  }]
}
```

#### Fulfillment Outbound API
Crea ordini di fulfillment in uscita:

```
POST /fba/outbound/2020-07-01/fulfillmentOrders
{
  "fulfillmentOrders": [{
    "sellerFulfillmentOrderId": "SFOR-001",
    "displayableOrderId": "123-1234567-1234567",
    "fulfillmentAction": "SHIP",
    "fulfillmentOrderItems": [{
      "sellerSku": "MY-SKU-001",
      "quantity": 5
    }]
  }]
}
```

#### Product Pricing API
Recupera informazioni di pricing:

```
GET /products/pricing/v0/pricing
Query params:
  - Asins (comma-separated)
  - ItemType (ASIN or SKU)
  - MarketplaceId
```

#### Notifications API (Webhooks)
Subscribe a notifiche in tempo reale:

```
POST /notifications/v1/subscriptions
{
  "resourceType": "ORDER_CHANGE",
  "eventVersion": "2.0"
}
```

**Tipi di evento:**
- `ORDER_CHANGE` - Cambio stato ordine
- `FEED_PROCESSING_FINISHED` - Feed elaborato
- `FBA_OUTBOUND_SHIPMENT_STATUS` - Status shipment FBA
- `INVENTORY_HEALTH_CHANGED` - Cambio salute inventario

---

## 2. Product Data Requirements (Specifiche Dati Prodotto)

### 2.1 Identificatori Prodotto

#### ASIN (Amazon Standard Identification Number)
- **Formato:** 10 caratteri (lettere e numeri)
- **Assegnazione:** Automatica da Amazon
- **Unico per:** Catalogo globale Amazon
- **Esempio:** B08KCSB2TY

#### GTIN (Global Trade Item Number) / UPC / EAN
- **GTIN:** Termine ombrello per UPC, EAN, ISBN
- **UPC:** 12 cifre (USA/Canada)
- **EAN:** 13 cifre (Europa, standard ISO 6346)
- **ISBN:** Per libri
- **Sorgente:** GS1 (unico fornitore legittimo)
- **Obbligatorio:** Sì, salvo GTIN Exemption
- **Validazione:** Amazon verifica presso GS1

Esempio EAN 13: `5901234123457`

#### SKU (Stock Keeping Unit)
- **Formato:** Fino a 40 caratteri
- **Creatore:** Seller (interno)
- **Unico per:** Account seller + ASIN
- **Non è:** Identificatore globale
- **Esempio:** MY-BLUE-T-SHIRT-SIZE-L

#### FNSKU (Fulfillment Network SKU)
- **Creatore:** Amazon (per FBA)
- **Formato:** Codice barre 15 caratteri
- **Unico per:** SKU per seller per marketplace
- **Generazione:** Automatica al primo inbound FBA
- **Utilizzo:** Etichettatura FBA, identificazione interna

### 2.2 Campi Obbligatori per Categoria

#### Tutti i Prodotti
- Title (Titolo)
- Description (Descrizione)
- Brand (Marca)
- Product Type (Tipo Prodotto)
- Category (Categoria principale)
- Price (Prezzo)
- Currency (Valuta)
- GTIN (o GTIN Exemption)
- Condition (Condizione)
- Main Image (Immagine principale)

#### Prodotti con Variazioni
- Parent ASIN (se esistente)
- Variation Relationship Type (se child)
- Variation Theme (es: Color, Size)
- Variation Value (es: Blue, Large)

#### Categorie Specifiche (esempi)

**Abbigliamento:**
- Size (Taglia)
- Color (Colore)
- Material (Materiale)
- Care Instructions (Istruzioni lavaggio)
- Gender (Genere)

**Elettronica:**
- Manufacturer (Produttore)
- Warranty (Garanzia)
- Voltage (Voltaggio)
- Weight (Peso)
- Dimensions (Dimensioni)

**Libri:**
- Author (Autore)
- Publisher (Editore)
- Publication Date (Data pubblicazione)
- ISBN (identificatore primario)
- Number of Pages (Numero pagine)

### 2.3 Albero delle Categorie (Browse Nodes)

Amazon organizza i prodotti in albero gerarchico:

```
Marketplace Root
├── Libri
│   ├── Narrativa
│   ├── Saggistica
│   └── Testi scientifici
├── Elettronica
│   ├── Smartphone
│   ├── Tablet
│   └── Accessori
└── Abbigliamento
    ├── Uomo
    │   ├── T-shirt
    │   └── Pantaloni
    └── Donna
```

**Ricerca Browse Node:** Usa Catalog Items API o Seller Central

### 2.4 Tipi di Condizione

| Condition | Descrizione | FBA | FBM |
|-----------|-------------|-----|-----|
| New | Nuovo, sigillato | Si | Si |
| Refurbished | Rinnovato, full warranty | Si | Si |
| Used - Like New | Usato come nuovo | Si | Si |
| Used - Very Good | Usato, molto buono | Si | Si |
| Used - Good | Usato, buono | Si | Si |
| Used - Acceptable | Usato, accettabile | Si | Si |
| Collectible - Like New | Collezionabile come nuovo | No | Si |

### 2.5 Specifiche di Immagine

#### Main Product Image (Immagine Principale)
- **Dimensioni:** Minimo 1.000 px su lato più lungo (consigliato 2.000-10.000 px)
- **Aspect Ratio:** 1:1 (quadrato) o 5:6 (portrait)
- **Sfondo:** Bianco puro (RGB 255, 255, 255)
- **Copertura prodotto:** Minimo 85% della cornice
- **Testo/Loghi:** Vietati
- **Watermark:** Vietati
- **Formato:** JPEG, PNG, TIFF, GIF (no GIF animate)
- **Zoom:** Attivato automaticamente con pixel sufficienti

#### Secondary Images (Immagini Secondarie)
- **Numero consigliato:** 3-7 immagini aggiuntive
- **Contenuto:** Lifestyle, angoli diversi, dettagli
- **Infographics:** Ammesse (dimensioni, caratteristiche, benefici)
- **Background:** Qualsiasi (lifestyle context, colore uniforme)
- **Dimensioni:** Stesse specifiche immagine principale

#### Regole Infographics
- Solo su immagini secondarie, NON sulla principale
- Testo semplice, leggibile
- Massimo 20% area immagine
- Colori chiari con buon contrasto
- Niente loghi competitor

### 2.6 Titolo Prodotto

#### Limite caratteri per marketplace:

| Marketplace | Limite | Consigliato |
|-------------|--------|-------------|
| amazon.de | 100 | 70-90 |
| amazon.fr | 100 | 70-90 |
| amazon.it | 100 | 70-90 |
| amazon.es | 100 | 70-90 |
| amazon.nl | 100 | 70-90 |
| amazon.co.uk | 100 | 70-90 |

#### Formato ottimale:
```
[Brand] [Product Type] - [Key Feature 1] [Key Feature 2] - [Condition/Use Case]
```

Esempio:
```
Sony WH-1000XM5 Cuffie Wireless - Active Noise Cancelling, 30h Battery - Blue
```

### 2.7 Backend Search Terms (Termini di Ricerca)

- **Limite:** 250 byte (non caratteri)
- **Formato:** Separati da spazio
- **Best Practice:**
  - Varianti di spelling
  - Sinonimi
  - Abbreviazioni comuni
  - Errori di ortografia comuni

**Esempio (contabyte):**
```
wireless earbuds bluetooth earphone earphones true wireless
```

(Circa 65 byte)

### 2.8 Relationships (Variazioni Prodotto)

#### Parent-Child Structure
```
Parent ASIN (Tipo: VARIATION)
├── Child 1 (Color: Red, Size: M)
├── Child 2 (Color: Red, Size: L)
├── Child 3 (Color: Blue, Size: M)
└── Child 4 (Color: Blue, Size: L)
```

**Regole:**
- Parent: Nessun inventory o pricing
- Child: Numero univoco SKU, inventory, pricing separati
- Variazione theme: Max 2 dimensioni di variazione per parent

#### Variation Themes supportati:
- Color
- Size
- Length
- Material
- Pattern
- Brand
- Flavor
- Style
- Edition
- Format

---

## 3. Listing Management (Gestione Listing)

### 3.1 Creazione Listing

#### Match vs New ASIN

**Match (Creazione Listing su ASIN Esistente):**
- Fornisci ASIN esistente
- Assegna SKU personale
- Attiva inventory + pricing
- La visibilità dipende dalla tua competitività

**New ASIN (Creazione Nuovo Prodotto):**
- Fornisci GTIN nuovo o GTIN Exemption
- Amazon crea ASIN automaticamente
- Diventi referrer primario del nuovo prodotto

#### Via API:

```
PUT /listings/2021-08-01/items/{sellerId}/{sku}
{
  "attributes": {
    "title": "Product Name",
    "brand": "Brand Name",
    "description": "Description",
    "bullet_points": ["Feature 1", "Feature 2"],
    "item_type": "PRODUCT_TYPE",
    "gtin": "5901234123457",
    "fulfillment_channels": ["FBM"],
    "ean_list": ["5901234123457"],
    "condition_type": "New",
    "recommended_browse_nodes": ["12345678"]
  }
}
```

### 3.2 Upload via Feeds

#### JSON_LISTINGS_FEED (Consigliato)

```json
{
  "feedType": "JSON_LISTINGS_FEED",
  "listings": [
    {
      "sku": "MY-SKU-001",
      "attributes": {
        "title": "Product Name",
        "brand": "Brand Name",
        "item_type": "T_SHIRT",
        "description": "Full description",
        "bullet_points": ["Point 1", "Point 2"],
        "gtin": "5901234123457",
        "color": "Blue",
        "size": "Large",
        "images": [
          {
            "link": "https://example.com/image1.jpg",
            "sequence": 1
          }
        ],
        "price": {
          "currency_code": "EUR",
          "amount": "49.99"
        },
        "quantity": 100,
        "fulfillment_channel": "FBM"
      }
    }
  ]
}
```

#### Processo Upload:

```
1. Crea input feed document:
   POST /feeds/2021-06-30/documents

2. Upload file (base64):
   PUT /feeds/2021-06-30/documents/{documentId}/contents

3. Crea feed:
   POST /feeds/2021-06-30/feeds

4. Monitora stato:
   GET /feeds/2021-06-30/feeds/{feedId}
```

### 3.3 Feed Processing

**Estados:**
- `SUBMITTED` - Feed inviato
- `IN_PROGRESS` - Elaborazione in corso
- `CANCELLED` - Annullato
- `DONE` - Completato

**Risultati:**
- `PROCESSING_STARTED` - Iniziato
- `SUCCESS` - Successo completo
- `PARTIAL_SUCCESS` - Parziale (alcuni item falliti)
- `FATAL` - Errore fatale (nessun item processato)

**Processing time:**
- Tipico: 5-30 minuti
- Picco di carico: Fino a 8 ore
- Evita: Molti feed piccoli al secondo (crea backlog)

### 3.4 Listing Quality Dashboard

Visualizza in Seller Central:

- **ASIN Health:** Visione completeness per ASIN
- **Required Information:** Dati obbligatori mancanti
- **Enhanced Content:** A+ Content status
- **Suppressed Issues:** Listing sospesi e motivi
- **Inventory Sync:** Sincronizzazione multicanale

### 3.5 Listing Sospesi/Inattivi

#### Cause Comuni:
- Prezzo zero
- Inventory zero
- Missing required data
- Policy violation
- Trademark claim
- Quality issues
- Categoria ristretta (gated)

#### Risoluzione:
```
1. Verifica Listing Quality Dashboard
2. Leggi notification message (specifico)
3. Fix issues (inventory, data, compliance)
4. Submit per activation se richiesto
5. Monitora status (24-72h)
```

### 3.6 Multi-Marketplace Listing

#### Build International Listings
- Seleziona ASIN base (es: amazon.de)
- Attiva su ulteriori marketplace EU
- Tradotto automaticamente o manuale?
  - **Manuale:** Migliore per SEO e conversione
  - **Automatico:** Più veloce, qualità variabile

**Consiglio:** Traduzione professionale per marketplace con volume alto

#### Data Persistenza:
- Title, Description, Images: Specifici per marketplace
- GTIN, SKU, Inventory: Condivisi (con mapping SKU)

---

## 4. Commission e Fee Structure (Commissioni e Tariffe)

### 4.1 Referral Fees (Commissioni di Referral) - 2025/2026

Percentuale sul prezzo di vendita (esclusa la parte VAT per acquirenti EU):

#### Categorie Principali (Amazon.de/fr/it/es):

| Categoria | Commissione Standard | Note |
|-----------|--------------------|----||
| Libri | 3% | Esclusa VAT |
| Musica/DVD | 6% | Esclusa VAT |
| Elettronica | 8% | Esclusa VAT |
| Abbigliamento (>€20) | 15% | Esclusa VAT |
| Abbigliamento (€15-20) | 10% | Esclusa VAT (2026) |
| Abbigliamento (<€15) | 5% | Esclusa VAT (2026) |
| Casa/Giardino (>€20) | 15% | Esclusa VAT |
| Casa/Giardino (€0-20) | 8% | Esclusa VAT (2026) |
| Giocattoli | 8% | Esclusa VAT |
| Sport | 8-15% | Esclusa VAT |
| Alimentari | 8-10% | Esclusa VAT |
| Bellezza | 10% | Esclusa VAT |
| Automotive | 5-10% | Esclusa VAT |

#### Commissioni Articoli Pesanti (FBA):
- **Minimo:** €20 EUR/articolo (ridotto da €25 nel 2025)
- **Applica a:** Prodotti FBA >30kg per il quale il peso è più importante del volume
- **Esclusione:** Non applicare se referral fee standard è già superiore

#### Aggiornamenti 2026:
- Riduzione media €0,15-0,17 per unità
- Abbigliamento ridotto: 15% → 10% (€15-20), 8% → 5% (<€15)
- Casa ridotto: 15% → 8% (€0-20)
- Pet/Grocery/Vitamins ridotto: Variabile → 5% (<€10)

### 4.2 FBA Fulfillment Fees (Tariffe Fulfillment FBA)

#### Fulfillment Fee per Unità - Parcels (Effettivo Feb 2025)

Basato su: Peso + Dimensioni

**Standard Size (Small & Light):**
- €2,50 - €4,50 EUR per unità (dipende da dimensioni esatte)

**Standard Size (Regular):**
- €3,20 - €6,80 EUR per unità (dipende da peso/dimensioni)

**Oversize:**
- €5,60 - €12,00+ EUR per unità (per tier weight/size)

**Envelope (Flat):**
- €2,10 - €2,50 EUR per unità

**Low Price FBA:**
- Articoli ≤€20: Riduzione fino a -€0,45 per unità (Dec 2025)

*Calcolatore ufficiale:* https://sellercentral-europe.amazon.com/help/hub

#### Monthly Storage Fee (Tariffe di Stoccaggio Mensile)

**Standard Size:**
- €0,87 EUR/unità/mese (Jan-Sept)
- €2,62 EUR/unità/mese (Oct-Dec) - festività

**Oversize:**
- €0,26 EUR/unità/mese (Jan-Sept)
- €0,79 EUR/unità/mese (Oct-Dec)

Pagato il **15 di ogni mese** per inventory presente il 1° giorno

#### Long-Term Storage Fee (Stoccaggio a Lungo Termine)

Applica a inventory:
- 181-270 giorni di storage: €0,87 EUR/unità
- 271+ giorni: €1,74 EUR/unità

Pagato il 15 di ogni mese per inventory non venduto

#### Storage Utilization Surcharge (Supplemento Utilizzo Stoccaggio)

Se inventory > 30% della capacità per 4+ settimane consecutive:
- €0,44 EUR/unità di excess (addizionale a storage fee)

#### Disposition Fees (Tariffe di Smaltimento)

- **Return to Seller:** €0,70 EUR/unità
- **Disposal:** €0,35 EUR/unità
- **Liquidation:** Nessuna tariffa (ricevi % del valore)

### 4.3 FBM-Specific Fees (Tariffe FBM)

**Subscription Professional:** €39,99 EUR/mese
- Unlimited product listings
- Access a all tools e reports
- Accesso PPC Advertising

**Subscription Individual:** €0,00
- €0,99 EUR per articolo venduto
- Massimo 40 articoli/mese (professionale se oltre)

### 4.4 Pan-European FBA Fees (Tariffe Pan-EU FBA)

Quando inventory è distribuito in più paesi EU:

- **Low-Inventory Fee:** €0,22 EUR per ASIN se inventory < 200 unità totali
- **Oversize Surcharge:** +€0,44 EUR per unità (oltre standard FBA)
- **Distribution costs:** Inclusi in fulfillment fee standard

### 4.5 European Fulfillment Network (EFN) Fees

Fulfillment tra UK-EU (Remote Fulfillment):

- **Surcharge:** +€0,20-0,35 EUR per unità (aggiunto a fulfillment standard)
- **Availability:** Dipende da categoria e volume

### 4.6 Multi-Channel Fulfillment (MCF) Fees

Fulfillment ordini da canali non-Amazon:

| Tier di Peso | Fee (EUR) |
|-------------|----------|
| 0-100g | €2,50 |
| 101-500g | €3,10 |
| 501-1kg | €4,00 |
| 1-5kg | €5,60 |
| 5-10kg | €8,70 |
| 10-20kg | €14,50 |
| 20-30kg | €18,00 |

### 4.7 Currency Conversion Fees

Se prezzi in valuta diversa da EUR (es: GBP per Amazon.co.uk):

- **Fee:** 1-2% sul tasso di conversione
- **Quando applica:** Settlement settimanale/quindicinale
- **Consolida con:** Referral fees e FBA fees

### 4.8 Advertising Fees (Tariffe Pubblicitarie)

**Sponsored Products/Brands/Display:**
- Model: CPC (Cost Per Click)
- **Range bid:** €0,01 - €10,00+ EUR per click (dipende categoria)
- **ACoS ottimale:** 20-30% (ACOS = Ad Spend / Revenue)
- **TACOS:** Total Ad Cost of Sale (ACoS + Organic ROI)

### 4.9 Professional Seller Subscription Addons

| Feature | Tipo | Costo |
|---------|------|-------|
| Amazon Brand Registry | One-time | Incluso (se trademark) |
| Transparency Program | Optional | €0,001 per unità |
| Project Zero | Optional | €0,001-0,002 per unità |
| Amazon Vine Program | Invitation-only | Gratis |

---

## 5. Order Management (Gestione Ordini)

### 5.1 Ordine Stato (Order States)

```
Order Flow Diagram:

[Pending]
  ↓ (Acknowledgement required within 24h)
[Unshipped]
  ↓ (Must ship within SLA days)
[Shipped]
  ↓ (Con tracking)
[Delivered] ← Final State
  ↓ (Possible return window)
[Returned] ← If customer returns within 30 days
  ↓
[Returned - Refund Issued]
  ↓
[Cancelled] ← If cancelled before shipment
```

**FBM Specifici:**
- `Pending` → `Unshipped` (auto con FBA)
- `Unshipped` → `Shipped` (seller action)
- `Shipped` → `Delivery` (tracking carrier)

### 5.2 Metriche SLA (Service Level Agreement)

#### Ship-by Date (Data Entro cui Spedire)

**Calcolato da:**
```
Ship-by Date = Order Date + X giorni SLA
```

**Giorni SLA per Marketplace:**

| Fulfillment | SLA Giorni | Note |
|-------------|-----------|------|
| FBM Standard | 2-3 | Entro data SLA |
| FBM Expedited | 1 | Giorno successivo |
| FBA | Automatico | Amazon gestisce |
| Seller Fulfilled Prime | 1 | Same/next day |
| Premium Shipping | 0 | Same day (di nuovo dal 2025) |

**Mancato rispetto:** Aumenta Late Shipment Rate (LSR)

### 5.3 Acknowledgement Requirements

**Entro:** 24 ore da order (per FBM)

```
PATCH /orders/v0/orders/{orderId}/acknowledgement
{
  "orderItems": [{
    "orderItemId": "order-item-id",
    "acknowledgementStatus": "Accepted"  // o "Rejected"
  }]
}
```

**Motivi Reject:**
- Inventory non disponibile
- Indirizzo invalido
- Dettagli ordine erronei

### 5.4 Shipping Confirmation Requirements

**Obbligatorio per:** Tutti gli ordini FBM

```
POST /orders/v0/orders/{orderId}/shipmentConfirmation
{
  "packageDetail": {
    "shippingType": "Standard"  // Standard, Express, etc.
  },
  "codCollectionMethod": null,  // Se COD
  "fulfillmentDate": "2026-01-15T10:30:00Z",
  "shipFromAddress": {
    "name": "Seller Name",
    "addressLine1": "Street Address",
    "city": "City",
    "postalCode": "12345",
    "countryCode": "DE"
  },
  "itemList": [{
    "orderItemId": "order-item-id",
    "quantity": 1
  }],
  "trackingList": [{
    "trackingNumber": "1234567890",
    "carrierCode": "UPS"  // AMAEUROPORT, UPS, DHL, etc.
  }]
}
```

### 5.5 Tracking Requirements

**Obbligatorio:** Numero tracciamento per ogni shipment

**Carrier Supportati (EU):**
- Amazon Logistics (AMAEUROPORT)
- DHL
- DPD
- GLS
- UPS
- Hermes
- Correos
- Poste Italiane
- La Poste

**Valid Tracking Rate (VTR):** >95% richiesto
- Minimo dal 2025: Tutti i carrier supportati
- Prima: Solo Amazon-integrated carriers

### 5.6 Order Defect Metrics (Metriche di Difetto Ordine)

Misurate su rolling 30/60-day window:

#### Order Defect Rate (ODR)
```
ODR % = (A-to-Z + Negative Feedback + Chargeback) / Total Orders * 100
```

**Target:** < 1% per account sano
- **1-5%:** Avviso, rischio sospensione
- **>5%:** Account a rischio sospensione immediata

#### Late Shipment Rate (LSR)
```
LSR % = Orders shipped after ship-by date / Total Orders * 100
```

**Target:** < 4%

#### Pre-Fulfillment Cancel Rate (PFCR)
```
PFCR % = Orders cancelled by seller before ship-by date / Total Orders * 100
```

**Target:** < 2,5%

#### Customer Return Rate
```
Return % = Returned items / Total items shipped * 100
```

**Target:** < 15% (varia per categoria)

---

## 6. FBA (Fulfillment by Amazon)

### 6.1 Come Funziona FBA

**Flusso:**

```
1. Seller preparazione:
   - Etichetta SKU/FNSKU
   - Imballaggio secondo specifiche
   - Preparazione documentazione

2. Inbound shipment:
   - Crea shipment in Seller Central
   - Genera label FNSKU
   - Spedisci a fulfillment center Amazon

3. Receipt & QC:
   - Amazon riceve shipment
   - Quality check (sampling)
   - Stowing in inventory

4. Order fulfillment:
   - Customer ordina
   - Amazon prepara ordine
   - Amazon spedisce con tracking

5. Returns handling:
   - Customer restituisce a centro rientri
   - Inspection & restocking o disposal
   - Refund processato da Amazon
```

### 6.2 Inbound Shipment Flow

#### Fase 1: Preparazione Shipment

```
POST /fba/inbound/v0/shipments
{
  "InboundShipmentHeader": {
    "ShipmentName": "Shipment-20260401-001",
    "DestinationFulfillmentCenterId": "PHX3",
    "ShipmentStatus": "WORKING",
    "LabelPrepPreference": "SELLER_LABEL"  // o AMAZON_LABEL_ONLY
  },
  "InboundShipmentItems": [{
    "SellerSKU": "MY-SKU-001",
    "FulfillmentNetworkSKU": "FN-SKU-001",  // se AMAZON_LABEL_ONLY
    "Quantity": 100,
    "PrepDetailsList": [{
      "PrepInstruction": "Labeling",
      "PrepOwner": "SELLER"
    }]
  }],
  "InboundShipmentInfo": {
    "ShipmentLength": 100,  // cm
    "ShipmentWidth": 50,
    "ShipmentHeight": 50,
    "ShipmentWeight": 500,  // kg
    "ShippingType": "Ocean"  // Ocean, Standard, Expedited, etc.
  }
}
```

#### Fase 2: Etichettatura

**FNSKU Label (Code 128):**
- Amazon assegna FNSKU univoco per seller/SKU
- Barcode 15 caratteri (es: X00N3V7Q1FW23)
- Stampare e applicare su ogni unità
- Dimensioni minime: 1,5" x 0,5"

**Alternativa:** SELLER_LABEL mode
- Usi SKU tuoi con label Amazon
- Meno setup, accettato da FBA

#### Fase 3: Packing & Delivery

**Requirement pacchi:**
- Scatola in buone condizioni
- Massimo 30kg per pacco
- Massimo dimensioni: 63,5cm x 63,5cm x 63,5cm
- Packing slip con tracking number
- Etichette di avvertimento se necessario (batterie, liquidi)

**Carrier ammessi:**
- Amazon Logistics (consigliato)
- DHL
- DPD
- GLS

**Tracking:** Obbligatorio per monitoraggio

#### Fase 4: Receipt Confirmation

Amazon ti notifica:
```
{
  "shipmentId": "FBA123456789",
  "status": "SHIPPED",
  "lastUpdated": "2026-01-15T10:30:00Z",
  "trackingStatus": "In Transit"
}
```

Quando ricevuto:
```
{
  "status": "RECEIVED",
  "warehouseReceiptDate": "2026-01-18T14:00:00Z",
  "totalQuantityReceived": 95,  // Nota: possibile variance
  "discrepancies": [{
    "sku": "MY-SKU-001",
    "quantityExpected": 100,
    "quantityReceived": 95,
    "discrepancyReason": "UNACCOUNTABLE"
  }]
}
```

### 6.3 Prep Requirements (Requisiti di Preparazione)

#### Labeling Rules (Regole Etichettatura)

| Item Type | Labeling | Poly-Bagging | Wrapping |
|-----------|----------|-------------|----------|
| Apparel | Required | Optional | Optional |
| Food/Vitamins | Required | Required | Optional |
| Electronics | Required | Recommended | Optional |
| Shoes | Required | Optional | Recommended |
| Fragile Items | Required | Optional | Required |
| Hazmat | Required | Not allowed | Case by case |

#### Poly-Bagging (Sacchetti di Plastica)

**Richiesto per:**
- Articoli di moda che si stropicciano
- Articoli con liquidi/polveri
- Prodotti piccoli che potrebbero disperdersi

**Specifiche:**
- Spessore: Minimo 1 micron
- Clear o colored permitted
- Etichetta FNSKU visibile

#### Hazardous Material (Materiali Pericolosi)

Richiede **Safety Data Sheet (SDS)** e dichiarazione:

**Esempi:**
- Batterie al litio
- Liquidi infiammabili
- Corrosivi
- Pressurizzato (spray)

**Procedura:** Submit documents via Seller Central, Amazon approva prima di accepting shipment

### 6.4 Inventory Placement Service (Distribuzione Inventario)

**Default:** Amazon decide basato su domanda
- Distribuisce automaticamente tra FC (Fulfillment Centers)
- Ottimizza per tempi di consegna

**Placement Preference:** Puoi richiedere concentrazione:
```
Concentrated in fewest possible FCs
  vs.
Distributed across all FCs
```

**Trade-offs:**
- Concentrato: Minore costo MCF, più lento per ordini distant
- Distribuito: Consegne più veloci, più MCF inter-FC

### 6.5 Stranded Inventory (Inventario Bloccato)

**Cause:**
- Listing sospeso/inattivo
- Prezzo troppo basso
- No sales da 90+ giorni
- Categoria ristretta

**Action:**
```
1. Verifica Listing Status
2. Fix issues (reattiva listing)
3. Update inventory in Seller Central
4. Monitora per 14+ giorni se comincia a vendere
```

**Se non vendibile:** Richiesta rimozione/smaltimento

### 6.6 Removal/Disposal Orders

**Rimozione (Return to Seller):**
```
POST /fba/outbound/v0/removalShipments
{
  "removalShipmentItems": [{
    "sellerSKU": "MY-SKU-001",
    "quantity": 50,
    "removalMethod": "RETURN_TO_SELLER"  // o DISPOSE
  }]
}
```

**Costo:**
- Return: €0,70 EUR/unità
- Dispose: €0,35 EUR/unità
- Liquidation: Nessuna tariffa (ricevi %)

### 6.7 FBA Capacity & Inventory Performance Index (IPI)

#### Capacity Limits

Non esiste **limite assoluto** per inventory, ma Amazon usa:

**Inventory Performance Index (IPI):**
```
IPI = (Sell-through × 35%) + (In-Stock Inventory × 20%)
      + (Inventory Age × 20%) + (Stranded Inventory × 25%)
```

**Soglie:**
- IPI > 500: Capacity illimitata
- IPI 400-500: Capacity limitata
- IPI < 400: Severe restrictions, possibile suspension

**Monitoring:** Seller Central > Inventory > Inventory Planning > IPI Report

### 6.8 Pan-European FBA

#### Overview

Inventory distribuito automaticamente tra:
- Germany (PHX, BER, etc.)
- France (LYS, etc.)
- Italy (MXP, etc.)
- Spain (MAD, etc.)
- Poland (WRO, etc.)

#### Requisiti 2025

**Obbligatorio dal 25 giugno 2025:**
- Attivare **amazon.nl** (Paesi Bassi)
- Altrimenti rischio rimozione da Pan-EU program

#### Features

- **Automatica:** Inventory distribuito senza configurazione
- **Costo aggiunto:** Low-Inventory fee + potential oversize surcharge
- **Benefit:** Accesso a mercati EU con single shipment

### 6.9 European Fulfillment Network (EFN)

**Remote Fulfillment** per ordini tra UK-EU:

```
UK Seller → EU Orders fulfillment
EU Seller → UK Orders fulfillment (post-Brexit)
```

**Attivazione:** Seller Central > Fulfillment Settings > Remote Fulfillment

**Costo aggiunto:** +€0,20-0,35 EUR/unità

### 6.10 Multi-Country Inventory (MCI)

**Permitere inventory placement** in multiple country FCs:

```
Seller Central > Inventory > FBA Inventory > Permissioned Marketplaces
```

**Permissioni:**
- Home marketplace: Automatico
- Additional countries: Optional enable
- Default: Inventory may not go there senza explicit permission

---

## 7. FBM (Fulfillment by Merchant)

### 7.1 Requisiti e SLA

#### Eligibility Basics

- **Account Status:** Professional (consigliato) o Individual
- **Performance Metrics:** Below da monitoare
- **Listing Requirements:** Shipping template configurato

#### Metric Thresholds

| Metrica | Target | Margine |
|---------|--------|----------|
| Order Defect Rate | < 1% | 0-1% = OK, >1% = warning |
| Late Shipment Rate | < 4% | 0-4% = OK, >4% = penalità |
| Pre-fulfillment Cancel Rate | < 2,5% | 0-2,5% = OK, >2,5% = issue |
| Valid Tracking Rate | > 95% | Must provide tracking |
| Customer Return Rate | < 15% | Varia per categoria |

### 7.2 Shipping Templates

Configura in **Seller Central > Settings > Shipping Settings**:

```
Template Details:
├─ Shipping Service Level
│   ├─ Standard (default SLA days)
│   ├─ Expedited
│   └─ Premium (NEW: 0-day handling)
├─ Shipping Costs
│   ├─ Flat rate per order
│   ├─ Per weight
│   └─ Calculated by ZIP
├─ Handling Time
│   ├─ 1-2 business days
│   ├─ 2-3 business days
│   ├─ 3-5 business days
│   └─ 0 days (premium shipping)
├─ Excluded Locations
│   └─ Countries/regions no shipping
└─ Shipping Restrictions
    └─ Max weight, dimensions, etc.
```

### 7.3 Buy Box Eligibility (FBM Requirements)

**Minimo requirement:**
- Professional seller account
- ODR < 1%
- LSR < 4%
- PFCR < 2,5%
- Valid tracking > 95%
- Feedback > 90%
- Account status: Good standing

**Bonus per Buy Box:**
- **Seller Fulfilled Prime:** 1-day shipping capability
- **Premium Shipping:** 0-day handling (NEW Oct 2025)
- **Historical sales velocity**
- **Competitive pricing**

### 7.4 Seller Fulfilled Prime (SFP)

**Eligibilità:**
```
ODR < 1%
AND LSR < 4%
AND VTR > 95%
AND 100+ SFP orders/month
AND Consistent daily shipping
```

**SFP Badge:** "Amazon Prime Eligible" on listing
- Tasso di conversione più alto
- Accesso a more Prime customers

**Handling Time:** 1 business day

**Carrier:**
- Deve consegnare entro SLA (1-2 giorni)
- Tracking obbligatorio
- USPS, UPS, FedEx supportati

### 7.5 Performance Monitoring

**Dashboard FBM:**
- Seller Central > Performance > Account Health
- Real-time metric tracker
- Notifications per threshold breaches
- Recommendations per miglioramento

---

## 8. Buy Box Algorithm (Algoritmo Buy Box)

### 8.1 Panoramica

**Featured Offer (Buy Box):**
- Visualizzato prominentemente
- Uno solo per ASIN (varia ogni minuto)
- ~75-82% di tutti gli ordini Amazon
- Rotazione tra sellers che qualified

### 8.2 Eligibility Requirements

**Tier 1 - Mandatory:**
```
✓ Professional seller account
✓ Account in good standing
✓ Valid payment method on file
✓ Clear seller performance metrics (published)
```

**Tier 2 - Competitive:**
```
✓ ODR < 1%
✓ LSR < 4%
✓ PFCR < 2,5%
✓ Valid Tracking Rate > 95%
✓ Feedback score ≥ 90% (positive)
✓ Response time < 24 hours (messaging)
✓ Return rate < 15% (típico)
```

**Tier 3 - Optimization:**
```
✓ Competitive pricing (within 5% of lowest)
✓ Fulfillment method: FBA preferred, FBM with SFP strong
✓ Inventory depth (stock availability)
✓ Shipping speed (faster = better)
✓ Seller tenure (longer = more authority)
✓ Historical sales velocity (consistent sales)
```

### 8.3 Known Ranking Factors

#### Prezzo (Price)
- **Landed Price:** Product + Shipping
- **Threshold:** Must be within ~5% of lowest offer
- **Penalty:** Even small deviation can lose Box
- **Strategy:** Dynamic pricing per monitor competitors

#### Fulfillment Method
- **FBA:** Priorità (Prime eligible)
- **SFP:** Quasi parità con FBA se metrics buone
- **FBM:** Disadvantage, serve metrics eccellenti
- **Trend 2025:** FBM migliorato if 0-day handling

#### Shipping Speed
- **FBA:** Automatico (Amazon optimizes)
- **SFP:** 1-day requirement
- **Premium:** 0-day handling (ship same day)
- **Standard FBM:** 3-5 days = disadvantage

#### Seller Metrics
- **ODR:** Più importante (>1% = disqualified)
- **LSR:** 2° priority (>4% = rotate out)
- **Feedback:** Score ratio matters
- **Tenure:** Account age = trust signal

#### Inventory Depth
- **Stock availability:** More = longer hold
- **Stock-out:** Immediate rotation
- **Deep inventory:** +70% Buy Box share possible

#### Sales Velocity
- **Recent sales rate:** High velocity = boost
- **Consistency:** Regular sales > sporadic
- **Click-through rate (CTR):** Conversion % per visit

### 8.4 Buy Box Rotation

**A performer (metrics 95%+):** ~70% Box share
**Average performer (metrics 85%):** ~30% Box share
**Below-average (metrics 70%):** <5% Box share

**Rotation changes:** Every 60 seconds
- Amazon calculates new featured offer
- All qualified sellers get rotation
- Top-ranked gets majority of 24-hour sales

---

## 9. Account Health and Performance Metrics

### 9.1 Key Performance Indicators (KPI)

#### Order Defect Rate (ODR)
```
Calcolo: (A-to-Z Claims + Negative Feedback + Chargebacks) / Total Orders

Finestra: 30 giorni rolling
Target: < 1%
Soglia sospensione: > 5%
Effetto Buy Box: Loss di eligibility if > 1%
```

**Componenti ODR:**
- **A-to-Z Claim:** Customer disputes via Amazon (no resolution)
- **Negative Feedback:** <3 stars (rating 1-2)
- **Chargeback:** Credit card/payment dispute

#### Late Shipment Rate (LSR)
```
Calcolo: Orders shipped AFTER ship-by date / Total orders

Finestra: 30-day rolling
Target: < 4%
Threshold: 5% = avviso, >5% = penalità
Effetto Buy Box: Rotation out of featured offer
```

**Mitigazione:**
- Estensione SLA shipping (extra 1-2 giorni)
- Buffer di tempo nel handling
- Automation per tracking

#### Pre-Fulfillment Cancel Rate (PFCR)
```
Calcolo: Orders cancelled by seller before ship-by / Total orders

Target: < 2,5%
Threshold: 5%+ = non-compliance
Causa: Stock out, wrong listing, pricing error
```

#### Valid Tracking Rate (VTR)
```
Calcolo: Orders with valid tracking / Total shipped orders

Target: > 95%
Minimo 95% richiesto
NEW 2025: All carriers supportati (non solo Amazon-integrated)
```

**Carrier supportati per VTR:**
- Amazon Logistics (AMAEUROPORT)
- DHL
- DPD
- GLS
- UPS
- Hermes
- DPD

#### Return Rate
```
Calcolo: Returned items / Total items shipped

Target: < 15% (varia per categoria)
Soglia: > 20% = investigation

Categoria-specific:
- Abbigliamento: 15-25% normale
- Elettronica: 5-10%
- Libri: 2-5%
```

#### Feedback Score
```
Calcolo: Positive ratings (3-5 stars) / Total ratings

Target: > 90%
Minimo per Buy Box: 90%

Note: Feedback ≠ ODR (diversi)
- ODR: Defects
- Feedback: Seller rating (optional customer action)
```

### 9.2 Account Suspension Triggers

**Immediate (Automatic):**
- ODR > 5% (within 24h)
- Multiple policy violations
- Trademark/IP infringement
- Selling restricted items without approval

**Gradual (Performance-based):**
- ODR 3-5% + other issues
- LSR > 10% sustained
- PFCR > 7%
- Feedback consistently < 85%

### 9.3 Performance Dashboard

**Seller Central > Performance > Account Health**

Visualizza:
- Real-time metrics (daily update)
- Comparison to threshold (visual indicator)
- Trend graphs (30/60/90 day)
- Recommended actions
- Warning notifications
- Appeal option (if suspended)

---

## 10. SEO e A10 Algorithm

### 10.1 A10 Algorithm Overview

**Evoluzione:** Amazon A9 → A10 (2023+)

**Cambio principale:**
- Emphasize esterno traffic (social, blogs)
- Seller authority + reputation boost
- Click-through rate (CTR) now critical
- Sales velocity remains importante

### 10.2 Ranking Factors

#### Fattori Confermati (High Impact)

**1. Rilevanza Keyword**
- Posizionamento in title
- Backend search terms (250 byte max)
- Bulletpoint keywords
- Description keywords

**2. Sales History & Velocity**
- Consistent sales momentum
- Recent conversion rate
- Sales per page visit (tracked per keyword)

**3. Seller Authority**
- Account age (tenure)
- Historical sales
- Feedback score
- Metric compliance (ODR, LSR, etc.)

**4. Fulfillment Method**
- FBA > SFP > FBM (generalmente)
- Prime eligibility
- Shipping speed

**5. Customer Reviews & Rating**
- Average star rating
- Total # of reviews
- Review velocity (recent reviews)
- Review content quality

**6. Click-Through Rate (CTR)**
- Visits to listing / impressions in search
- High CTR = relevance signal
- Enhanced by quality images + title

#### Fattori Sospettati (Medium Impact)

**7. Esterno Traffic**
- Traffic da social media, blog, advertising
- Segnale di demand off-Amazon
- Aumentato weight in A10 vs A9

**8. Engagement Metrics**
- Time on page
- Pages per session
- Bounce rate (meno rilevante)

**9. Price Competitiveness**
- Non ranking factor diretto
- Influenze conversione → velocity

**10. Inventory Depth**
- Stock availability
- Consistent in-stock status

### 10.3 Title Optimization

#### Limite Caratteri per Marketplace

| Marketplace | Limite Caratteri |
|-------------|------------------|
| amazon.de | 100 |
| amazon.fr | 100 |
| amazon.it | 100 |
| amazon.es | 100 |
| amazon.co.uk | 100 |

#### Struttura Ottimale

```
[Brand] [Product Type] - [Primary Keyword] + [Secondary Keyword] - [Use Case/Benefit]

Esempio:
Sony WH-1000XM5 Cuffie Wireless - Cancellazione Rumore Active, 30h Batteria - Nero

Characters: 94 (sotto 100, ottimale)
Keywords:
  - "Cuffie wireless" (primary)
  - "Cancellazione rumore" (secondary)
  - "Batteria 30h" (benefit)
```

#### Best Practices

- Posiziona keyword principale nei primi 30 caratteri
- Include brand + product type + key adjective
- Utilizza trattini per separare, non virgole
- Evita keywords ripetuti (keyword stuffing)
- Niente ALL CAPS (tranne brand nome)
- Niente special characters (©, ®, ™ in marketplace)

### 10.4 Backend Search Terms

**Limite:** 250 byte TOTALI (non caratteri)

```
Backend Search Terms:
wireless earbuds bluetooth true wireless earphones earphone

Byte count: ~65 bytes (sotto limite)
```

#### Strategia

1. **Non duplicare** title/description keywords (già indexato)
2. **Varianti di spelling:** colour/color, grey/gray
3. **Sinonimi comuni:** phone/mobile, car/automobile
4. **Abbreviazioni:** kg/kilogram, USD/dollars
5. **Errori ortografici comuni:** whireles (wireless), suond (sound)

#### Attenzione

- Non usare competitor brand names (trademark violation)
- Niente collocazioni "scam" (no richiesta di review)
- Niente negative (no "non è")
- Byte-aware: spazi contano verso limite

### 10.5 Browse Node & Category Relevance

**Browse Nodes:**
- Categoria primaria: Importante per ranking
- Categoria secondaria (opzionale): Aiuta visibilità
- Multi-category: Amazon decide automaticamente

**Selezione corretta:**
- Corrispondenza con prodotto esatto
- Evita forced categorization
- Verifica nella sezione "Recommended Browse Nodes" API

### 10.6 Indexing Rules

**Quando keywords NON sono indexate:**

```
X Competitor brand names (non tue)
X Frasi con accenti errati (per lingua)
X Keyword stuffing (>2x ripetizione)
X Parole inappropriate o profane
X Numero di keywords > 250 byte
X Formato malformed UTF-8
```

**Quando keywords SONO indexate:**

```
✓ Posizionato in Title
✓ In first 100 byte di Backend Search Terms
✓ In Bullet Point
✓ In Description (primi 200 caratteri)
✓ In Product Type field
✓ In Variation Attributes (se applicabile)
```

---

## 11. Advertising (Amazon Ads)

### 11.1 Ad Types

#### Sponsored Products
- **Format:** Visivo singolo prodotto
- **Placement:** Search results, product pages
- **Targeting:** Keyword o ASIN-based
- **Bid Model:** CPC (Cost Per Click)
- **Budget:** Daily customizzabile
- **Minimo daily:** €1 EUR

#### Sponsored Brands
- **Format:** Header image + multiple products
- **Placement:** Top search results
- **Targeting:** Keyword-based
- **Bid Model:** CPC
- **Req.:** Brand Registry o Amazon Vendor
- **Minimo daily:** €10+ EUR

#### Sponsored Display
- **Format:** Display ads, retargeting
- **Placement:** Third-party sites + Amazon
- **Targeting:** ASIN, categoria, audience
- **Bid Model:** CPC o viewable impressions
- **Budget:** Daily customizzabile

#### Amazon DSP (Demand-Side Platform)
- **Format:** Programmatic display/video
- **Placement:** Premium publisher sites
- **Targeting:** Audience avanzato
- **Minimo:** €15.000+ EUR/month (enterprise)

### 11.2 Bidding Strategies

#### Manual Bidding
- Seller imposta bid massimo per keyword/ASIN
- Default: €0,25-2,00 EUR per click

#### Automatic Bidding
- Amazon imposta bid basato su competizione
- Less control, ease of management

#### Dynamic Bidding
- Down-adjust per mobile, non-converting traffic
- Up-adjust per high-conversion periods

### 11.3 Campaign Structure

```
Advertising Account
├─ Campaign 1
│   ├─ Ad Group 1
│   │   ├─ Keywords/ASIN 1
│   │   └─ Keywords/ASIN 2
│   └─ Ad Group 2
├─ Campaign 2
└─ Campaign 3
```

**Best Practice:**
- Separate campaigns per product type
- Group keywords per theme in Ad Groups
- Monitor per-keyword performance

### 11.4 Key Metrics

#### ACoS (Advertising Cost of Sale)
```
ACoS % = Ad Spend / Sale Revenue * 100

Target: 20-30% (dipende da margine)
ACoS = 20%: €20 spend per €100 sale
```

#### TACOS (Total Advertising Cost of Sale)
```
TACOS % = (Ad Spend + Shipping Cost) / Total Revenue * 100

Includes: Fulfillment fee + referral fee + ad spend
```

#### ROAS (Return on Ad Spend)
```
ROAS = Sale Revenue / Ad Spend

ROAS = 3x: €300 revenue per €100 ad spend
Conversione: ROAS 3 = ACoS 33%
```

#### CTR (Click-Through Rate)
```
CTR % = Clicks / Impressions * 100

Target: 0,5-1,5% (Sponsored Products)
Audience match influenza CTR
```

#### Conversion Rate
```
CVR % = Orders / Sessions * 100

Target: 10-20% (dalla ricerca)
Buona listing quality aumenta CVR
```

---

## 12. VAT e Compliance Europa

### 12.1 Registrazione VAT

#### Chi Must Register?

**Sempre:**
- EU citizens selling all goods to EU
- Non-EU selling directly on Amazon EU

**Soglia distance selling:**
- €10.000 nel year (all channels)
- Oltre = VAT registration obbligatorio
- Amazon FBA in EU = automatic triggerAuditor

#### VAT Number Format per Paese

| Paese | Formato | Esempio |
|-------|---------|----------|
| Germania | DExxxxxxxxxxxx | DE123456789 |
| Francia | FRxx xxxxxxxxx | FR12 123456789 |
| Italia | ITxxxxxxxxxxxx | IT123456789 |
| Spagna | ESxxxxxxxxxx | ES12345678X |
| Paesi Bassi | NLxxxxxxxxxx | NL123456789B01 |

### 12.2 OSS (One-Stop-Shop) Regime

**Applica a:** Venditori remoti che forniscono servizi digitali o beni fisici <€10k

**Come funziona:**
- Registrazione singola in **Portale OSS** (EU tax)
- Single VAT return per tutti i paesi EU
- Aliquota VAT destinazione applicate
- Settlement una volta/trimestre

**Portal:** https://ec.europa.eu/taxation_customs/oss/

**Vantaggi:**
- Semplicità amministrativa
- No multi-country registrations
- Single payment

### 12.3 DAC7 Reporting

**Directive 2021/1589:**
- Amazon reports a tax authorities
- Informazioni: Seller name, address, TID, transaction data
- Annuale (January) per year precedente
- Enforcement: Growing audit rates

**Amazon communica:**
- Non ti avvisa automaticamente
- Tax authorities ricevono dati
- Possibilità audit se non compliant

### 12.4 GPSR (General Product Safety Regulation)

**Effettivo:** 13 Dicembre 2024

**Obbligo principale:**
- Nomin **EU Responsible Person** per prodotti non-EU sellers

**Chi è obbligato:**
- Tutti i non-EU venditori
- Tutti i prodotti (con rare eccezioni)
- Anche se già vendute (compliance obbligatoria)

**Consequenze non-compliance:**
- Listing removal
- Account suspension
- Fini (€50.000+)

**Procedura Amazon:**
```
1. Seller Central > Compliance > GPSR
2. Nomina Responsible Person
3. Provide contact details
4. Verifica Amazon
5. Approval entro 7-14 giorni
```

### 12.5 EPR (Extended Producer Responsibility)

**Paesi con obbligo:**
- **Germania:** LUCID system (packaging.lucid-ident.de)
- **Francia:** Eco-emballages (eco-emballages.fr)
- **Italia:** CONAI partnership (obbligatorio)
- **Spagna:** SistEO (sisteo.es)

**Cosa:** Registrare packaging + materiali
**Costo:** €0,15-0,50 EUR per unità (varia)
**Tempistica:** Pre-sales, non post

### 12.6 CE Marking Requirements

**Prodotti richiedenti:**
- Giocattoli (EN 71 standard)
- Elettronica (RoHS, WEEE)
- Macchinari
- Costruzioni
- PPE (Personal Protective Equipment)

**Procedura:**
1. Conformare a directive EU rilevante
2. Creare Technical File
3. Apporre CE marking su prodotto
4. Mantenere documentation

### 12.7 WEEE (Waste Electrical & Electronic Equipment)

**Applica a:** Electronics (mobile, earbuds, chargers, etc.)

**Obblighi:**
- Registrazione presso organismo nazionale
- Contributo WEEE/unità venduta
- Labeling WEEE info
- Recycling program

**Costo:** €0,10-1,00 EUR/unità

### 12.8 Battery Regulation

**Applica a:** Prodotti con batterie (mobile, earbuds, power banks, etc.)

**Obblighi:**
- Dichiarazione componenti batteria
- Raggiungibilità info rimozione batteria
- Labeling % metalli pesanti
- Recycling info

**Documenti:** Scheda tecnica batterie

### 12.9 Packaging Regulation

**Post-Jan 2025 Directives:**
- Minimo 60% contenuto riciclato (target 2030)
- Labeling identificazione materiale (PE, PP, etc.)
- Avoiding excess packaging
- WEEE/EPR marking ove richiesto

---

## 13. Brand Protection