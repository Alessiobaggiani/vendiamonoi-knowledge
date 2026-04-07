# Otto Market — Deep-Dive Marketplace Spec per Vendiamonoi.it

## EXPERT-LEVEL TECHNICAL DEEP-DIVE

**Documento**: Specifica completa Otto Market per distributori digitali europei
**Target**: CTO, Marketplace Manager, Operations Manager — Vendiamonoi.it
**Versione**: 2026
**Ultimo aggiornamento**: 2026-04-07
**Complemento a**: marketplace-specs/amazon, marketplace-specs/kaufland, data-models/product-information

---

## INDICE COMPLETO

1. [Overview e Posizionamento Strategico](#1-overview-e-posizionamento-strategico)
2. [Onboarding e Requisiti Seller](#2-onboarding-e-requisiti-seller)
3. [Struttura Commissioni e Costi](#3-struttura-commissioni-e-costi)
4. [Product Listing Requirements](#4-product-listing-requirements)
5. [API e Integrazione Tecnica](#5-api-e-integrazione-tecnica)
6. [Order Management](#6-order-management)
7. [Logistica e Spedizioni](#7-logistica-e-spedizioni)
8. [Resi e Rimborsi](#8-resi-e-rimborsi)
9. [Compliance e Normative Tedesche](#9-compliance-e-normative-tedesche)
10. [Performance Metrics e KPI](#10-performance-metrics-e-kpi)
11. [Integrazione Channel Manager](#11-integrazione-channel-manager)
12. [Strategia per Vendiamonoi](#12-strategia-per-vendiamonoi)

---

# 1. Overview e Posizionamento Strategico

## 1.1 Cos'è Otto Market

Otto Market è la piattaforma marketplace del gruppo Otto, il secondo più grande marketplace tedesco dopo Amazon.de. Originariamente un retailer di catalogo fondato nel 1949, Otto si è trasformato in una piattaforma e-commerce che ha aperto ai seller terzi creando "Otto Market".

| Parametro | Valore |
|-----------|--------|
| **GMV 2024/2025** | €7.0 miliardi (+9% YoY) |
| **Seller attivi** | ~6.200 marketplace partner (+33% YoY) |
| **Visite mensili** | 90+ milioni |
| **Clienti attivi** | 12+ milioni |
| **Posizione Germania** | #2 dopo Amazon.de, davanti a eBay |
| **Revenue Otto Group** | €16.2 miliardi (2023) |
| **Sede** | Werner-Otto Straße 1-7, 22179 Hamburg |
| **Mercato principale** | Germania (94% dei ricavi) |

## 1.2 Otto Group — Struttura Societaria

Il gruppo Otto è un conglomerato internazionale di e-commerce e servizi:

| Società | Ruolo |
|---------|-------|
| **OTTO** | Marketplace principale e retailer e-commerce |
| **Hermes Fulfilment** | Logistica e consegna pacchi (75% venduto ad Advent International nel 2020, integrazione operativa mantenuta) |
| **About You** | Fashion e-commerce (acquisito da Zalando luglio 2025) |
| **Bonprix** | Fashion retail |
| **Crate & Barrel** | Home & living |
| **Baur** | General retail |
| **Limango** | Shopping club |
| **Manufactum** | Premium retail |
| **SupplyX** | Supply chain services (ex Otto Group Logistics GmbH) |
| **OTTO Payments** | Payment processing (dal luglio 2022) |

## 1.3 Mercato Tedesco E-Commerce — Contesto Competitivo

| Marketplace | GMV 2024 | Quota di Mercato |
|-------------|----------|------------------|
| **Amazon.de** | €15.0+ miliardi | ~63% |
| **Otto.de** | €7.0 miliardi | ~5-6% |
| **Zalando** | €2.71 miliardi | ~3% |
| **MediaMarkt** | €1.89 miliardi | ~2% |
| **Mercato totale DE** | €84.7 miliardi (2025) | +3.8% YoY |

**Dato chiave**: Tutta la crescita 2024 del mercato tedesco è venuta dai marketplace (+8.8%), non dal retail diretto.

## 1.4 Otto vs Amazon.de — Confronto Strategico per Seller

| Fattore | Otto | Amazon |
|---------|------|--------|
| **Tipo marketplace** | Curato (approvazione selettiva) | Aperto (meno restrittivo) |
| **Fulfillment** | No FBA equivalente; seller gestisce spedizioni | FBA opzionale |
| **Fee mensile** | €99.90/mese | €39/mese |
| **Commissioni** | 7-18% per categoria | 7-45% per categoria |
| **Audience** | 45-54 anni, quality-conscious, brand-loyal | Ampio demografico, price-sensitive |
| **Categorie forti** | Fashion (26%), Home & Living, Electronics | Tutto (marketplace generalista) |
| **Qualità dati** | Importanza critica per ranking | Importante ma più flessibile |
| **Competizione prezzo** | Moderata, non price-war driven | Intensa, Buy Box driven |
| **Customer service** | Seller deve offrire supporto in tedesco | Seller-provided con A-to-Z Guarantee |

## 1.5 Demografica Clienti Otto

| Parametro | Valore |
|-----------|--------|
| **Fascia d'età principale** | 45-54 anni |
| **Reddito** | 62.9% €0-34.999 annui, 35% fascia bassa |
| **Genere** | 60.26% maschile, 39.74% femminile |
| **Comportamento d'acquisto** | Quality-conscious, fedele ai brand, meno sensibile al prezzo |
| **Loyalty** | Forte base di clienti ripetitivi (legacy catalogo Otto) |

## 1.6 Categorie con Migliori Performance

1. **Fashion** (26% delle vendite) — categoria più forte
2. **Home & Living / Arredamento** — forza storica
3. **Elettronica** — categoria solida (richiede WEEE compliance)
4. **Elettrodomestici da cucina** — prodotti featured
5. **Sport & Tempo libero** — in crescita
6. **Giardinaggio & DIY** — categoria emergente

## 1.7 Espansione Europea (2025+)

| Timeline | Paesi | Stato |
|----------|-------|-------|
| **H1 2025** | Paesi Bassi | Accettando applicazioni (servizio clienti tedesco richiesto) |
| **Fine 2026** | Polonia, Austria, Francia, Spagna | Annunciato |
| **Successivo** | Danimarca, Belgio | Timeline non specificata |

**Implicazione per Vendiamonoi**: L'apertura a seller europei rende Otto sempre più accessibile. Monitorare la timeline per l'apertura a seller con entità legale italiana.

---

# 2. Onboarding e Requisiti Seller

## 2.1 Requisiti Entità Legale

### Forme Giuridiche Accettate (attualmente)

Solo forme giuridiche tedesche o olandesi:

**Tedesche**: AG, Einzelunternehmen ohne Handelsregistrierung, e.K., eGbR, GbR, GmbH, GmbH & Co. OHG, GmbH & Co. KgaA, KG, KGaA, OHG, SE, UG (haftungsbeschränkt), UG (haftungsbeschränkt) & Co. KG

**Olandesi**: Forme giuridiche NL accettate dal H1 2025

### Forme Escluse

- Piccole imprese secondo §19 UStG (regime forfettario tedesco)
- Partita IVA condivisa (non è permesso usare la P.IVA della società madre)
- **Forme giuridiche italiane: NON accettate** (al momento)

## 2.2 Requisiti IVA

- Partita IVA tedesca obbligatoria (DE + 9 cifre)
- Non è possibile usare la P.IVA dell'entità madre
- Solo prodotti soggetti ad aliquota IVA 19% (standard tedesca)
- Verifica IVA: Otto confronta i dati forniti con il Bundeszentralamt für Steuern (BZSt)

## 2.3 Processo di Approvazione

| Fase | Durata | Dettaglio |
|------|--------|----------|
| **1. Registrazione** | 1 giorno | Compilazione form su otto.market |
| **2. Review applicazione** | Variabile | Ogni applicazione esaminata individualmente |
| **3. Accettazione contratto** | 1-2 giorni | Firma contratto marketplace |
| **4. KYC (Know Your Customer)** | 5-10 giorni | Verifica identità e documenti aziendali |
| **5. Legitimation check** | Fino a 15 giorni lavorativi | Verifica legale completa |
| **6. Attivazione tecnica** | 1-5 giorni | Credenziali OTTO Partner Connect |
| **Totale stimato** | 1-3 mesi | Otto Market è selettivo, non tutti vengono accettati |

## 2.4 Documenti Necessari

- Certificato di registrazione commerciale (Handelsregisterauszug o equivalente)
- Partita IVA tedesca valida
- Documento d'identità del rappresentante legale
- Coordinate bancarie (conto SEPA in EUR)
- Descrizione prodotti/catalogo
- Prova della capacità di fornire servizio clienti in tedesco

## 2.5 Standard di Qualità Prodotto

Otto è noto per essere selettivo:

- Alta qualità dei dati prodotto (descrizioni, immagini, specifiche)
- Otto si riserva il diritto di escludere "prodotti usa e getta di bassa qualità"
- Ogni applicazione è vagliata individualmente
- I prodotti devono rispettare tutti gli standard di sicurezza EU

---

# 3. Struttura Commissioni e Costi

## 3.1 Fee Mensile Base

| Voce | Importo |
|------|--------|
| **Canone mensile** | €99.90/mese (IVA inclusa) |
| **Inizio fatturazione** | Primo mese dopo attivazione tecnica |
| **Metodo addebito** | Trattenuto dal credito vendite o addebito diretto |
| **Rescissione** | Possibile in qualsiasi momento con 1 mese di preavviso |

## 3.2 Commissioni per Categoria

| Categoria | Commissione |
|-----------|------------|
| **Elettronica & Media** | 7-8% |
| **Sport & Tempo Libero** | 10-12% |
| **Home & Living** | 12-15% |
| **Arredamento** | 12-15% |
| **Fashion & Lifestyle** | 12-15% |
| **Gioielleria** | 18% |
| **Default (maggior parte)** | 12-15% |

## 3.3 Fee di Pagamento

| Voce | Importo |
|------|--------|
| **Payment processing fee** | 2.7% del valore transazione |
| **Processore** | OTTO Payments (interno al gruppo) |
| **Note** | Inclusa nelle commissioni sopra per alcune categorie |

## 3.4 Fee Aggiuntive

- **Setup fee**: Nessuna
- **Fee per media/inserzioni**: Nessuna
- **Fee per grandi quantità di offerte**: Nessuna
- **Fee per rimborsi**: Nessuna

## 3.5 Sconti Disponibili

- Seller con Science-Based Target (SBT) validato: riduzione commissione di -0.5 a -1.0 punti percentuali

## 3.6 Ciclo di Pagamento

| Parametro | Valore |
|-----------|--------|
| **Frequenza payout** | Settimanale (ogni giovedì) |
| **Termine pagamento** | 14 giorni dalla data di payout |
| **Visibilità saldo** | Real-time in OTTO Partner Connect |
| **Valuta** | Solo EUR |
| **Metodo** | Bonifico SEPA su conto bancario registrato |

## 3.7 Simulazione Costi per Vendiamonoi

**Scenario**: Vendita prodotto Home & Living a €50

| Voce | Importo | % |
|------|---------|---|
| Prezzo vendita | €50.00 | 100% |
| Commissione Otto (15%) | -€7.50 | 15% |
| Payment fee (2.7%) | -€1.35 | 2.7% |
| IVA inclusa nel prezzo (19%) | -€7.98 | 15.97% |
| **Ricavo netto pre-costi** | **€33.17** | **66.33%** |
| Canone mensile (pro-rata su 100 ordini/mese) | -€1.00 | 2% |
| **Margine disponibile per costo prodotto + logistica** | **€32.17** | **64.33%** |

---

# 4. Product Listing Requirements

## 4.1 Attributi Prodotto Obbligatori

| Campo | Tipo | Note |
|-------|------|------|
| **SKU** | String | Codice prodotto unico del seller |
| **EAN/GTIN** | String (13 cifre) | Non deve iniziare con "2" |
| **Categoria prodotto** | ID | Dal category tree Otto |
| **Brand ID / Nome brand** | String | Registrato nel sistema Otto |
| **Prezzo di vendita** | Decimal | IVA 19% inclusa |
| **Per varianti**: SKU e EAN | Per ogni variante | Obbligatori per ogni variante |
| **Indirizzo distributore** | String | GPSR compliance — nome, indirizzo, regione |
| **Email produttore** | String | GPSR compliance |

## 4.2 Struttura Prodotto-Variante

```
Product (contenitore)
├── Proprietà condivise: brand, categoria, nome, dimensioni
├── Variation 1 (SKU acquistabile)
│   ├── SKU: VEND-LAMP-001-WHT
│   ├── EAN: 8012345678901
│   ├── Prezzo: €49.99
│   └── Colore: Bianco
├── Variation 2
│   ├── SKU: VEND-LAMP-001-BLK
│   ├── EAN: 8012345678902
│   ├── Prezzo: €49.99
│   └── Colore: Nero
└── ...
```

## 4.3 Requisiti Immagini

| Parametro | Specifica |
|-----------|-----------|
| **Dimensione minima** | 500×1000 px |
| **Dimensione massima** | 4.500 px per lato |
| **Formati accettati** | JPG o PNG |
| **Spazio colore** | RGB (CMYK non supportato) |
| **Nome file** | No caratteri speciali, no umlaut |
| **Immagine principale** | Ritaglio su sfondo bianco, no ombre/riflessi |
| **Eccezione** | Abbigliamento: sfondo non bianco accettato |

## 4.4 HTML Supportato nelle Descrizioni

I campi free-text accettano HTML limitato:

`<b>`, `<i>`, `<font>`, `<s>`, `<u>`, `<o>`, `<sup>`, `<sub>`, `<ins>`, `<del>`, `<strong>`, `<strike>`, `<tt>`, `<code>`, `<big>`, `<small>`, `<span>`, `<em>`

**NON supportati**: `<table>`, `<div>`, `<img>`, `<a>`, `<script>`, `<style>`, `<iframe>`

## 4.5 Categorie Principali

| Macro-Categoria | Sotto-Categorie |
|-----------------|------------------|
| **Home & Living** | Cucina, Illuminazione, Mobili, Decorazione, Tessili casa |
| **Fashion & Lifestyle** | Accessori, Beauty, Abbigliamento, Calzature, Borse, Gioielli, Orologi |
| **Technology & Media** | Cancelleria, Elettronica, Elettrodomestici, Media, Software |
| **Sports & Leisure** | Accessori bambino, Giocattoli, Attrezzatura sportiva, Biciclette |
| **Giardinaggio & DIY** | Attrezzi, Piante, Mobili da giardino |
| **Drogheria** | Prodotti salute e igiene |

## 4.6 Attributi Legali per Categoria

Alcune categorie hanno attributi con `featureRelevance=LEGAL` che sono **obbligatori per legge**:

- Informazioni GPSR (produttore, indirizzo, contatto)
- Classe energetica (per elettrodomestici)
- Materiali (per tessili — regolamento EU tessile)
- Avvertenze sicurezza (per giocattoli — EN 71)
- Composizione (per cosmetici)

## 4.7 Processo di Validazione Prodotto

```
Upload prodotto
  │
  ▼
Validazione 1 (Import)
  ├─ Errori formato → Import Error Report
  └─ OK → Validazione 2
  │
  ▼
Validazione 2 (Marketplace)
  ├─ Errori contenuto → Marketplace Status errors
  └─ OK → Prodotto ATTIVO su otto.de
  │
  ▼
Prodotto visibile ai clienti
```

---

# 5. API e Integrazione Tecnica

## 5.1 Architettura API

| Parametro | Valore |
|-----------|--------|
| **Tipo** | RESTful (HTTP methods: GET, POST, PUT, DELETE) |
| **Formato** | JSON (`application/json;charset=utf-8`) |
| **Specifica** | Swagger/OpenAPI con generazione automatica oggetti |
| **Sandbox** | `https://sandbox.api.otto.market` |
| **Production** | `https://api.otto.market` |
| **Documentazione** | https://api.otto.market/docs/ |
| **API Index** | https://api.otto.market/docs/api-docs/ |

## 5.2 Autenticazione OAuth2

| Parametro | Valore |
|-----------|--------|
| **Metodo** | OAuth 2.0 Bearer Token |
| **Flow** | Client Credential Flow o Authorization Code Flow |
| **Scopes** | Controllo accesso granulare via OAuth scopes |
| **Token refresh** | Via refresh_token con grant_type "refresh_token" e client_id "token-otto-api" |
| **Header richiesto** | `Authorization: Bearer {access_token}` |

```
# Ottenere access token
POST https://api.otto.market/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id=token-otto-api
&client_secret={your_secret}
&scope={required_scopes}

# Response
{
  "access_token": "eyJhbGciOi...",
  "token_type": "bearer",
  "expires_in": 1800,
  "refresh_token": "abc123..."
}

# Refresh token
POST https://api.otto.market/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token
&client_id=token-otto-api
&refresh_token={refresh_token}
```

## 5.3 Rate Limiting

| Parametro | Valore |
|-----------|--------|
| **Limite globale** | 20 richieste/secondo per partner |
| **Endpoint specifici** | Alcuni endpoint hanno limiti inferiori |
| **Errore superamento** | HTTP 429 Too Many Requests |
| **Best practice** | Usare endpoint bulk per massimizzare efficienza |

## 5.4 Versioning API

| Parametro | Valore |
|-----------|--------|
| **Schema** | Semantic versioning (solo major version in URL) |
| **Esempio URL** | `/v2/products`, `/v4/orders` |
| **Supporto versione** | 6 mesi dopo annuncio nuova versione |
| **Breaking changes** | Solo in nuove major versions |
| **Best practice** | Ignorare campi sconosciuti nelle response |

## 5.5 Interfacce Funzionali

| API | Endpoint Base | Scopo |
|-----|--------------|-------|
| **Products** | `/v2/products` | Gestione prodotti, pubblicazione, visibilità |
| **Orders** | `/v4/orders` | Visualizzazione ordini e position items |
| **Shipments** | `/v1/shipments` | Conferma spedizioni con tracking |
| **Return Shipments** | `/v1/return-shipments` | Gestione resi e tracking ritorno |
| **Receipts** | `/v3/receipts` | Documenti fiscali (fatture, note credito) |
| **Quantities** | `/v2/quantities` | Gestione stock/disponibilità |
| **Prices** | `/v2/prices` | Aggiornamento prezzi |

## 5.6 Products API — Dettaglio

### Creare/Aggiornare Prodotti

Le operazioni prodotto sono **asincrone**:

```
POST /v2/products
Body: [{product_data}]

# Response (stato PENDING)
{
  "state": "PENDING",
  "processId": "abc-123",
  "links": {
    "failed": "/v2/products/update-tasks/abc-123/failed",
    "succeeded": "/v2/products/update-tasks/abc-123/succeeded",
    "unchanged": "/v2/products/update-tasks/abc-123/unchanged"
  }
}

# Polling risultato (quando state = "done")
GET /v2/products/update-tasks/abc-123/failed
GET /v2/products/update-tasks/abc-123/succeeded
```

### Fetch Categorie (Sempre Prima dell'Upload)

```
GET /v2/products/categories

# Le categorie cambiano regolarmente!
# Sempre fetch la versione corrente prima di inviare dati
```

## 5.7 Orders API — Dettaglio

### Fetch Ordini

```
GET /v4/orders?fulfillmentStatus=PROCESSABLE

# Ordini visibili dopo 35 minuti (finestra cancellazione cliente)
```

### Conferma Ordine

```
POST /v4/orders/{orderId}/positionItems/{positionItemId}/confirm
```

### Cancella Position Item

```
PUT /v4/orders/{orderId}/positionItems/{positionItemId}/cancel

# Solo per stato PROCESSABLE o ANNOUNCED
# Irreversibile una volta cancellato
```

## 5.8 Shipments API — Dettaglio

### Conferma Spedizione

```
POST /v1/shipments/carriers/{carrier}/trackingnumbers/{trackingNumber}
Body: {
  "positionItems": [
    {"positionItemId": "xyz", "quantity": 1}
  ],
  "shipFromAddress": {
    "city": "Milano",
    "zipCode": "20121",
    "countryCode": "ITA"  // ISO 3166-1 alpha-3
  },
  "returnTrackingKey": "return-tracking-123"  // OBBLIGATORIO per Otto
}
```

**CRITICO**: Otto richiede SEMPRE il `returnTrackingKey` nella conferma spedizione. Senza questo campo, la spedizione non viene accettata.

### Carrier Supportati per Spedizioni Outbound

`DHL`, `DHL_EXPRESS`, `DPD`, `GLS`, `HERMES`, `UPS`

### Spedizioni Parziali (Multi-Parcel)

```
# Aggiungere item a spedizione esistente
POST /v1/shipments/carriers/{carrier}/trackingnumbers/{trackingNumber}/positionitems

# Solo per tipo consegna PARCEL (non forwarder)
```

## 5.9 Returns API — Dettaglio

### Accettare Reso

```
POST /v1/return-shipments/{returnShipmentId}/accept
Body: {
  "reason": "RETURNED_IN_GOOD_CONDITION",
  "condition": "GOOD"
}
# Trigger automatico: rimborso cliente
```

### Rifiutare Reso

```
POST /v1/return-shipments/{returnShipmentId}/reject
Body: {
  "reason": "ITEM_DAMAGED_BY_CUSTOMER",
  "condition": "DAMAGED"
}
# Può essere accettato successivamente se si cambia idea
```

## 5.10 Sandbox / Test Environment

| Parametro | Valore |
|-----------|--------|
| **URL** | `https://sandbox.api.otto.market` |
| **Separazione** | Completamente separata dalla produzione |
| **Generare ordini test** | `POST https://sandbox.api.otto.market/v4/orders/testorders` |
| **Scenari test** | `POST .../v4/orders/testorders/scenario1` |
| **Limitazioni** | Products e Quantities non disponibili in sandbox |
| **Seconda validazione** | Simulata in sandbox |

## 5.11 Struttura Errori API

```json
{
  "errors": [
    {
      "path": "/v2/products",
      "title": "Invalid Attribute",
      "code": "490",
      "detail": "EAN format is invalid",
      "detail-structured": {...},
      "jsonpath": "$.variations[0].ean",
      "logref": "abc-log-ref-123"
    }
  ]
}
```

---

# 6. Order Management

## 6.1 Flusso Ordine Completo

```
Cliente ordina su otto.de
  │
  ▼
35 min attesa (finestra cancellazione cliente)
  │
  ▼
Ordine PROCESSABLE → visibile al seller
  │
  ▼
Seller CONFERMA ordine
  │
  ▼
Preparazione (pick & pack)
  │
  ▼
Seller SPEDISCE → fornisce:
  ├─ Luogo di partenza (ISO 3166-1 alpha-3)
  ├─ Corriere (DHL, Hermes, DPD, GLS, UPS)
  ├─ Numero tracking outbound
  └─ Numero tracking reso (OBBLIGATORIO)
  │
  ▼
Position items → stato SENT
  │
  ▼
Consegna al cliente (SLA: 2-3 giorni lavorativi)
  │
  ▼
[Opzionale] Reso entro 30 giorni
```

## 6.2 Stati Position Item

| Stato | Descrizione | Transizione |
|-------|-------------|-------------|
| **ANNOUNCED** | Ordine ricevuto, non ancora elaborabile | → PROCESSABLE |
| **PROCESSABLE** | Pronto per elaborazione | → CONFIRMED / CANCELLEDBYPARTNER |
| **CONFIRMED** | Confermato dal seller per fulfillment | → SENT |
| **SENT** | Spedito (via Shipments API) | → RETURNED (se reso) |
| **RETURNED** | Almeno un item reso | Stato finale |
| **CANCELLEDBYPARTNER** | Cancellato dal seller | Stato finale (irreversibile) |
| **CANCELLEDBYMARKETPLACE** | Cancellato da Otto | Stato finale (non consegnare!) |

## 6.3 Tempistiche Critiche

| Evento | Tempistica |
|--------|------------|
| Finestra cancellazione cliente | 35 minuti |
| Conferma ordine | Appena possibile dopo PROCESSABLE |
| Spedizione standard | 2-3 giorni lavorativi (incluso sabato) |
| Spedizione express | Max 3 giorni lavorativi |
| Visibilità tracking | Immediata dopo conferma spedizione |

---

# 7. Logistica e Spedizioni

## 7.1 Fulfillment Model

Otto **NON** offre servizi di fulfillment tipo FBA. I seller gestiscono autonomamente:
- Stoccaggio
- Pick & pack
- Spedizione
- Gestione resi

**Hermes Fulfilment** (società del gruppo Otto) è disponibile come partner logistico terzo per warehousing, packing e spedizione, ma non è integrato come servizio marketplace.

## 7.2 Corrieri Supportati

### Spedizioni Outbound (Consegna)

| Corriere | Supportato | Note |
|----------|-----------|------|
| **DHL** | ✅ | Il più usato in Germania |
| **DHL Express** | ✅ | Per consegne express |
| **DPD** | ✅ | Ampia copertura EU |
| **GLS** | ✅ | Economico per cross-border |
| **Hermes** | ✅ | Gruppo Otto, buona integrazione |
| **UPS** | ✅ | Premium per B2B |

### Resi verso Germania

| Corriere | Supportato |
|----------|----------|
| **DHL** | ✅ |
| **GLS** | ✅ |
| **Hermes** | ✅ |

### Resi verso altri Paesi EU

| Paese | Corriere | Note |
|-------|----------|------|
| Danimarca | Solo DHL | |
| Francia | Solo DHL | |
| **Italia** | **Solo DHL** | Rilevante per Vendiamonoi |
| Paesi Bassi | Solo DHL | |
| Austria | Solo DHL | |
| Polonia | Solo DHL | |
| Spagna | Solo DHL | |
| Rep. Ceca | Solo DHL | |

## 7.3 Cross-Border Shipping

**Può un seller italiano spedire dalla propria sede?**

- **SÌ** — il magazzino può trovarsi ovunque in Germania o nell'UE
- La merce può essere spedita dall'Italia ai clienti tedeschi
- **MA** è necessaria un'entità legale tedesca/olandese (non italiana)
- I resi dall'Italia sono accettati tramite DHL

### Considerazioni Pratiche per Vendiamonoi

| Opzione | Pro | Contro |
|---------|-----|--------|
| Spedizione da Italia | Nessun magazzino DE, costi bassi | Tempi consegna più lunghi, SLA difficile |
| Magazzino in Germania | SLA rispettata (2-3 gg), resi facili | Costo affitto + gestione |
| 3PL tedesco | SLA OK, scalabile | Costo per ordine, meno controllo |
| Hermes Fulfilment | Integrazione gruppo Otto | Costi da negoziare |

## 7.4 Requisiti Tracking

- **Obbligatorio**: Numero tracking outbound E numero tracking reso per OGNI ordine
- La conferma spedizione deve includere:
  - Luogo di partenza (città, CAP, codice paese ISO 3166-1 alpha-3)
  - Nome corriere
  - Numero spedizione outbound
  - Numero spedizione reso
- Otto genera etichette reso e QR code per i clienti usando il numero di reso fornito dal seller

---

# 8. Resi e Rimborsi

## 8.1 Politica Resi

| Parametro | Valore |
|-----------|--------|
| **Finestra reso** | 30 giorni dalla consegna |
| **Processo** | Cliente registra reso via account Otto |
| **Etichetta** | Otto genera etichetta reso + QR code |
| **Formato** | PDF scaricabile dall'account cliente |
| **Costo reso** | Determinato dalla policy del seller |

## 8.2 Flusso Reso

```
Cliente decide di restituire (entro 30 giorni)
  │
  ▼
Registra revocazione via account otto.de
  │
  ▼
Otto genera etichetta reso + QR code
(basata sul return shipment number del seller)
  │
  ▼
Cliente stampa etichetta e spedisce
  │
  ▼
Tracking reso visibile
  │
  ▼
Seller riceve pacco reso
  │
  ▼
Seller accetta/rifiuta via Returns API
  ├─ Accettato → Rimborso automatico al cliente
  └─ Rifiutato → Motivo comunicato, può accettare dopo
```

## 8.3 Logistica Resi per Vendiamonoi

**Scenario: Seller con magazzino in Italia**

- Resi dalla Germania all'Italia: **supportato via DHL**
- Costo DHL cross-border: €5-15 per pacco (a carico seller o cliente)
- Tempistica: 5-10 giorni lavorativi per reso cross-border
- **Alternativa**: Indirizzo reso in Germania (3PL) per resi più veloci e economici

---

# 9. Compliance e Normative Tedesche

## 9.1 GPSR — General Product Safety Regulation

| Parametro | Valore |
|-----------|--------|
| **In vigore dal** | 13 dicembre 2024 |
| **Sanzioni** | Fino a €100.000 |
| **Enforcement Otto** | Prodotti senza dati GPSR disattivati automaticamente |

### Campi Obbligatori per Ogni Prodotto

| Campo | Descrizione |
|-------|-------------|
| `manufacturer.name` | Nome completo del produttore |
| `manufacturer.email` | Email di contatto produttore |
| `manufacturer.address` | Indirizzo fisico completo |
| `manufacturer.regionCode` | Codice paese EEA |
| `manufacturer.url` | URL contatto produttore |

Otto ha introdotto il container `productSafety` nei dati prodotto per la compliance.

## 9.2 VerpackG — German Packaging Act

| Parametro | Valore |
|-----------|--------|
| **Chi deve registrarsi** | Tutti i seller che immettono prodotti imballati sul mercato tedesco (inclusi seller online stranieri) |
| **Registro** | LUCID (Zentrale Stelle Verpackungsregister — ZSVR) |
| **Formato numero** | DE + 13 cifre (es. DE1234567890123) |
| **Costo registrazione** | Gratuita |
| **Tempo registrazione** | 1-2 settimane |
| **Sanzioni** | Fino a €200.000 + ban vendite immediato in Germania |

### Obblighi Operativi

1. Registrazione gratuita online su LUCID
2. Contratto con operatore sistema duale (es. Der Grüne Punkt, Interseroh, Reclay)
3. Pagamento fee basate su peso/tipo materiale imballaggio
4. Report annuale: peso totale e tipo materiale degli imballaggi immessi
5. Informazioni richieste: nome azienda, persona designata (CEO), contatto email

## 9.3 ElektroG / WEEE — Per Prodotti Elettronici

| Parametro | Valore |
|-----------|--------|
| **Si applica a** | Tutti gli apparecchi elettrici ed elettronici (EEE) |
| **Registro** | Stiftung EAR (Fondazione per la registrazione apparecchiature elettriche) |
| **Seller non tedeschi** | Devono nominare rappresentante autorizzato in Germania |
| **Simbolo richiesto** | Bidone barrato sui prodotti |
| **Report** | Dichiarazioni mensili WEEE (unità vendute in Germania) |
| **Enforcement** | Prodotti senza registrazione WEEE valida delisted automaticamente |
| **Sanzioni** | Fino a €100.000, confisca profitti, ban vendite completo |

## 9.4 BattG — German Battery Act

| Parametro | Valore |
|-----------|--------|
| **Deadline critico** | 15 gennaio 2026 |
| **Si applica a** | Batterie ricaricabili o portatili |
| **Definizione produttore** | Chi per primo immette batterie sul mercato tedesco (inclusi importatori/distributori) |
| **Obblighi** | Registrazione Stiftung EAR, servizio di ritiro batterie, adesione a PRO, dichiarazioni mensili |
| **Sanzioni** | Fino a €100.000, delisting, ban vendite |

## 9.5 CE Marking

- Produttori devono assicurare conformità a tutte le direttive CE applicabili
- **Importatori da fuori Europa**: piena responsabilità per sicurezza prodotto se il produttore non ha rappresentante autorizzato in UE
- **Regola quasi-produttore**: Aziende che vendono con proprio brand sono trattate come produttori
- Vendiamonoi come distributore: deve verificare conformità del produttore e rifiutare prodotti non conformi

## 9.6 Requisiti Lingua

| Elemento | Lingua |
|----------|--------|
| **Listing prodotto** | Tedesco obbligatorio |
| **Customer service** | Tedesco obbligatorio |
| **Impressum** | Tedesco |
| **Comunicazioni Otto** | Tedesco/Inglese |
| **Fatture** | Tedesco raccomandato |

## 9.7 Impressum (Legal Notice)

Obbligatorio per tutti i seller online in Germania. Deve includere:
- Nome completo azienda
- Persona responsabile (Geschäftsführer)
- Indirizzo completo sede legale
- Dati di contatto (email, telefono)
- Numero registro commerciale (Handelsregisternummer)
- Partita IVA (Umsatzsteuer-Identifikationsnummer)
- Tribunale di riferimento

## 9.8 Checklist Compliance per Vendiamonoi

- [ ] Entità legale tedesca costituita
- [ ] Partita IVA tedesca ottenuta
- [ ] Registrazione LUCID (VerpackG) completata
- [ ] Contratto sistema duale attivato
- [ ] Registrazione WEEE/ElektroG (se elettronica) completata
- [ ] Registrazione BattG (se batterie) completata
- [ ] Dati GPSR per tutti i prodotti inseriti
- [ ] CE marking verificato per tutti i prodotti
- [ ] Impressum completo preparato
- [ ] Team customer service tedesco attivato
- [ ] Traduzioni prodotti in tedesco completate

---

# 10. Performance Metrics e KPI

## 10.1 Metriche Monitorate

| Metrica | Descrizione |
|---------|-------------|
| **Cancellation Rate** | % ordini cancellati dal seller; tassi alti danneggiano reputazione |
| **Late Shipment Rate** | % ordini spediti dopo data stimata |
| **On-Time Delivery (OTD)** | % ordini consegnati entro tempistiche promesse |
| **Order Fulfillment Rate** | Efficienza elaborazione e spedizione ordini |
| **Customer Satisfaction** | Valutazioni e recensioni clienti |
| **Account Health** | Compliance complessiva e aderenza alle policy |

## 10.2 SLA Spedizione

| Tipo | SLA |
|------|-----|
| Standard | 2-3 giorni lavorativi (incluso sabato) |
| Express | Max 3 giorni lavorativi |
| Tracking | Obbligatorio per verifica compliance |

## 10.3 Monitoraggio

- Tutte le metriche visibili su OTTO Partner Connect
- Otto non pubblica soglie numeriche specifiche nella documentazione pubblica
- Contattare supporto Otto per target KPI specifici e soglie sospensione

## 10.4 Best Practice Performance

- Confermare ordini entro 1 ora da PROCESSABLE
- Spedire entro 24 ore dalla conferma
- Mantenere cancellation rate < 2%
- Mantenere late shipment rate < 4%
- Rispondere ai messaggi clienti entro 24 ore
- Tasso accettazione resi > 95%

---

# 11. Integrazione Channel Manager

## 11.1 ChannelEngine

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Integrazione completa dedicata |
| **Autenticazione** | OAuth 2.0 integrata |
| **Funzionalità** | Product content, ordini, spedizioni, resi, inventario |
| **Requisito** | Ruolo "Dienstleister Freigaben" nell'account Otto |
| **Nota CRITICA** | Return tracking code obbligatorio con ogni spedizione |
| **Guida** | https://support.channelengine.com/hc/en-us/articles/4409484944925 |

## 11.2 Channable

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Integrazione completa nel modulo Marketplaces |
| **Funzionalità** | Catalogo, ordini, resi, cancellazioni, inventario |
| **Compliance** | GPSR e DSA gestiti nelle Shared attributes |
| **Setup** | https://helpcenter.channable.com/hc/en-us/articles/5078899812626 |

## 11.3 SellRapido

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ⚠️ NON specificamente menzionato nella documentazione |
| **Note** | SellRapido integra 70+ piattaforme ma Otto potrebbe non essere supportato |
| **Verifica** | Controllare direttamente su sellrapido.com/integrations |
| **Alternativa** | Usare ChannelEngine o Channable come bridge |

## 11.4 Altri Channel Manager Supportati

| Tool | Supporto | Note |
|------|----------|------|
| **M2E Pro** | ✅ | Gestione inventario e ordini |
| **Lengow** | ✅ | Integrazione feed |
| **API2Cart** | ✅ | Piattaforma universale |
| **TradeByte** | ✅ | Partner integrazione per seller Otto |
| **brickfox** | ✅ | Middleware hub |
| **OscWare** | ✅ | Transfer CSV manuale/automatico |
| **Tricoma** | ✅ | Plugin import/export |
| **DataFeedManager** | ✅ | Gestione feed prodotto |

## 11.5 Pattern Integrazione per Vendiamonoi

### Opzione A: ChannelEngine (Raccomandato)
```
SellRapido → ChannelEngine → Otto Market
            ↕                ↕
        Supabase          Otto API
            ↕
       Make.com
```

### Opzione B: Channable
```
Shopify/Feed → Channable → Otto Market
                  ↕
            Otto API
```

### Opzione C: API Diretta (Make.com)
```
Make.com HTTP Module → Otto Market API (OAuth2)
       ↕
   Supabase
       ↕
  SellRapido
```

**Nota**: Se SellRapido non supporta Otto direttamente, ChannelEngine è la scelta migliore perché già integrato con entrambi.

---

# 12. Strategia per Vendiamonoi

## 12.1 Fattibilità di Ingresso

### Barriere all'Ingresso

| Barriera | Livello | Soluzione |
|----------|---------|----------|
| Entità legale tedesca | 🔴 Alta | Costituire GmbH o UG (costo: €500-2.000, tempo: 2-4 settimane) |
| Partita IVA tedesca | 🔴 Alta | Richiesta tramite BZSt (tempo: 4-8 settimane) |
| Customer service tedesco | 🟡 Media | Outsourcing (€2.000-5.000/mese) o team interno |
| Traduzioni catalogo | 🟡 Media | Traduzione professionale + automazione AI |
| LUCID registrazione | 🟢 Bassa | Gratuita, 1-2 settimane |
| GPSR compliance | 🟢 Bassa | Dati già disponibili dai fornitori |
| Logistica cross-border | 🟡 Media | 3PL tedesco o spedizione da IT con DHL |

## 12.2 Stima Costi Setup

| Voce | Costo Una Tantum | Costo Mensile |
|------|-----------------|---------------|
| Costituzione GmbH/UG | €500-2.000 | — |
| Partita IVA DE | €0 (gratuita) | — |
| LUCID registrazione | €0 (gratuita) | — |
| WEEE registrazione | €200-500 | — |
| Sistema duale | — | €50-200 (dipende da volumi) |
| Otto canone mensile | — | €99.90 |
| Customer service DE | — | €2.000-5.000 |
| Traduzioni catalogo | €2.000-10.000 | €500-1.000 |
| 3PL tedesco (opzionale) | €500-1.000 setup | €3-8 per ordine |
| **TOTALE STIMATO** | **€3.200-13.500** | **€2.650-6.300** |

## 12.3 Analisi Margine

**Scenario: Prodotto Home & Living, prezzo €80**

| Voce | Importo |
|------|--------|
| Prezzo vendita (IVA 19% inclusa) | €80.00 |
| IVA (19%) | -€12.77 |
| Commissione Otto (15%) | -€12.00 |
| Payment fee (2.7%) | -€2.16 |
| Costo spedizione DE (DHL) | -€5.50 |
| **Ricavo netto** | **€47.57** |
| Costo prodotto (ipotesi 40%) | -€32.00 |
| **Margine lordo** | **€15.57 (19.5%)** |
| Costi fissi pro-rata (€3.000/mese ÷ 200 ordini) | -€15.00 |
| **Margine netto per ordine** | **€0.57 (0.7%)** |

**Conclusione**: Con 200 ordini/mese su Otto a €80 medi, il margine è sottilissimo. Serve:
- Volume > 500 ordini/mese per diluire costi fissi
- Prezzo medio > €100
- Commissione media < 12%
- O combinazione di questi fattori

## 12.4 Categorie Strategiche per Vendiamonoi su Otto

| Categoria | Commissione | Fit Vendiamonoi | Note |
|-----------|------------|-----------------|------|
| **Elettronica** | 7-8% | ✅ Alto | Margine migliore, richiede WEEE |
| **Home & Living** | 12-15% | ✅ Alto | Grande mercato, Made in Italy premium |
| **Fashion** | 12-15% | ⚠️ Medio | Forte concorrenza, brand italiani valorizzati |
| **Sport** | 10-12% | ✅ Alto | Buon margine, meno concorrenza |
| **Gioielleria** | 18% | ❌ Basso | Commissione troppo alta |

## 12.5 Timeline Ingresso Raccomandato

| Fase | Settimana | Azione |
|------|-----------|--------|
| 1 | 1-4 | Costituire entità tedesca (GmbH/UG), richiedere P.IVA DE |
| 2 | 5-8 | Registrazioni compliance (LUCID, WEEE se necessario) |
| 3 | 8-10 | Applicazione Otto Market, preparazione catalogo in tedesco |
| 4 | 10-16 | Processo approvazione Otto (1-3 mesi) |
| 5 | 16-18 | Setup tecnico (ChannelEngine/API), test sandbox |
| 6 | 18-20 | Go-live con catalogo iniziale (100-500 SKU) |
| 7 | 20-24 | Scale-up catalogo e ottimizzazione |
| **Totale** | **~6 mesi** | Dal via alla prima vendita |

## 12.6 Monitoring e KPI Target

| KPI | Target | Azione se Non Raggiunto |
|-----|--------|------------------------|
| Cancellation rate | < 2% | Verificare stock accuracy con SellRapido |
| Late shipment rate | < 4% | Valutare 3PL tedesco |
| On-time delivery | > 96% | Upgrade corriere o magazzino locale |
| Response time CS | < 24h | Aumentare team CS tedesco |
| Return rate | < 15% | Migliorare schede prodotto |
| Ordini/mese | > 500 | Espandere catalogo, pricing competitivo |

---

## OTTO PARTNER CONNECT — Portale Seller

### Funzionalità Principali

| Sezione | Descrizione |
|---------|-------------|
| **Product Management** | Creazione, manutenzione, gestione inventario prodotti |
| **Order & Fulfillment** | Overview ordini real-time, stato spedizioni, gestione resi |
| **Financial Management** | Storico pagamenti, payout pendenti/passati, saldo real-time |
| **Business Analytics** | Cifre vendita, KPI performance, metriche business |
| **Business Services** | Servizi aggiuntivi e prenotazione attività promozionali |
| **Account Setup** | 2FA, profilo azienda, profilo seller |

---

## FONTI E RIFERIMENTI

### Documentazione Ufficiale Otto
- [Otto Market — Homepage](https://www.otto.market/en.html)
- [Otto Market — Requisiti](https://www.otto.market/en/howitworks/requirements.html)
- [Otto Market — Registrazione](https://www.otto.market/en/howitworks/registration.html)
- [Otto Market — Billing](https://www.otto.market/en/advantages-and-services/billing.html)
- [Otto Market — Spedizioni & Resi](https://www.otto.market/en/howitworks/shipping-returns.html)
- [Otto Market — Partner Connect](https://www.otto.market/en/howitworks/otto-partner-connect.html)
- [Otto Market — FAQ](https://www.otto.market/en/faq/faq.html)
- [Otto Market — Onboarding Guide PDF](https://www.otto.market/_Resources/Persistent/2/4/f/2/24f243a28fd26374c6404a73883d5115201abffc/240618_Onboarding-Guide_EN.pdf)

### API Documentation
- [API Documentation Hub](https://api.otto.market/docs/)
- [API Getting Started](https://api.otto.market/docs/getting-started/)
- [API Guidelines & Best Practices](https://api.otto.market/docs/about-the-api/api-guides/)
- [API Best Practices](https://api.otto.market/docs/about-the-api/best-practices/)
- [Products API](https://api.otto.market/docs/functional-interfaces/products/)
- [Orders API](https://api.otto.market/docs/functional-interfaces/orders/)
- [Shipments API](https://api.otto.market/docs/functional-interfaces/shipments/)
- [Returns API](https://api.otto.market/docs/functional-interfaces/returns/)
- [Sandbox Guide](https://api.otto.market/docs/about-the-api/sandbox/)
- [API Changelog](https://api.otto.market/docs/changelog/)

### Compliance & Normative
- [GPSR Configuration - M2E Pro](https://blog.m2epro.com/configure-gpsr-requirements-on-otto-step-by-step-with-m2e-connect/)
- [LUCID Packaging Register](https://www.ecosistant.eu/en/lucid-packaging-register-background-and-guide/)
- [VerpackG Guide](https://for-sure.net/blogs/epr-news-hub/verpackg-registration-lucid-number-guide/)
- [WEEE/ElektroG Guide](https://tbaglobal.com/weee-compliance-in-germany-guide-for-electronics-sellers/)
- [BattG German Battery Act](https://www.epr-compliance.com/en/german-battery-directive)
- [CE Marking Germany](https://www.dguv.de/dguv-test/prod-testing-certi/ce-conform/responsibility/index.jsp)

### Analisi Mercato
- [German E-Commerce Market 2025](https://ecommercegermany.com/blog/german-ecommerce-market-generates-revenues-of-e84-7-billion-in-2025-surpassing-pandemic-levels/)
- [Otto vs Amazon Analysis](https://www.expan.do/post/amazon-de-vs-otto-an-in-depth-analysis-of-sales-performance-of-germanys-largest-marketplaces/)
- [Otto Marketplace Trends](https://ecommercenews.eu/ottos-marketplace-is-losing-sellers-and-is-in-crisis/)
- [Otto European Expansion](https://ecommercenews.eu/otto-to-open-its-marketplace-to-european-sellers/)

### Integrazioni
- [ChannelEngine — Otto Market](https://www.channelengine.com/en/all-marketplaces/otto-market)
- [ChannelEngine — Otto Guide](https://support.channelengine.com/hc/en-us/articles/4409484944925)
- [Channable — Otto Integration](https://www.channable.com/integrations/otto)
- [Channable — Otto Setup](https://helpcenter.channable.com/hc/en-us/articles/5078899812626)
- [SellRapido — Integrazioni](https://www.sellrapido.com/en/integrations/marketplaces/)
- [Otto Market Fees — Magnalister](https://www.magnalister.com/en/otto-fees/)

---

**Documento creato**: 2026-04-07
**Ricerca**: 3 agenti paralleli (seller/API, logistics/compliance, technical deep-dive)
**Complemento**: marketplace-specs/amazon, marketplace-specs/kaufland, automation-flows/make-blueprints
**Prossimo**: marketplace-specs/allegro (P2.2)
