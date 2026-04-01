# Specifica Marketplace Vente-Unique
## Guida Completa per Distributori Digitali Multi-Marketplace

**Documento di Riferimento per:** Vendiamonoi.it e Distributori su 20-30+ Marketplace EU
**Data Creazione:** Aprile 2026
**Versione:** 1.0
**Lingua:** Italiano

---

## 1. Informazioni Generali

| Elemento | Dettagli |
|----------|----------|
| **Azienda** | Vente-Unique SAS |
| **Fondazione** | 2006 (Origine Francia) |
| **Quotazione** | Euronext Growth (Parigi) |
| **Sito Principale** | www.vente-unique.com |
| **Paesi Operativi** | FR, IT, ES, DE, BE, PT, NL, CH, AT (9 mercati) |
| **Sito Italiano** | www.vente-unique.it |
| **Settore Principale** | Mobili, Arredamento, Home Decoration |
| **Tipo Marketplace** | B2C con integrazione seller terzi |
| **Lingue Supportate** | Francese, Italiano, Spagnolo, Tedesco, Portoghese, Olandese, Inglese |
| **Valute** | EUR (principale), CHF, GBP dove richiesto |
| **Customer Service** | Multicanale (Email, Chat, Telefono) |

---

## 2. Modello di Business

### 2.1 Strategia Aziendale
Vente-Unique opera un modello ibrido di marketplace combinando:
- **Sourcing Diretto:** Collocamento prodotto dai propri fornitori con vantaggio di prezzo
- **Seller Terzi:** Integrazione di distributori, rivenditori e produttori per ampliamento catalogo
- **Specializzazione Verticale:** Focus esclusivo su mobili e arredamento casa (no multi-categoria)

### 2.2 Cronologia Sviluppo
- **2006:** Fondazione come e-commerce francese specializzato
- **2010-2015:** Espansione geografica in 5 paesi EU
- **2016:** Lancio piattaforma seller multi-vendor
- **2018:** Quotazione Euronext Growth
- **2020-2024:** Consolidamento IT, ES, DE; integrazione API avanzate
- **2025+:** Focus su sostenibilità, reverse logistics, white-glove delivery

### 2.3 Categorie Principali
1. **Divani & Salotti** (18-25% catalogo): Divani, Poltrone, Chaise longue, Componibili
2. **Letti & Materassi** (15-20%): Letti, Materassi, Reti, Testiere, Cuscini
3. **Tavoli & Sedie** (12-18%): Tavoli, Sedie, Sgabelli, Banchi
4. **Armadi & Storage** (10-15%): Armadi, Cassettiere, Librerie, Scrivanie
5. **Complementi & Decor** (8-12%): Specchi, Quadri, Tende, Tappeti, Cuscini Decor
6. **Illuminazione** (5-8%): Lampade da tavolo, Sospensioni, Plafoniere
7. **Outdoor & Giardino** (5-8%): Mobili giardino, Gazebo, Lettini, Tavoli bar
8. **Camerette & Bimbi** (3-5%): Letti bimbi, Scrivanie, Armadi cameretta
9. **Altre** (Barrique, Home Office, Nicchie): 3-5%

### 2.4 Volumi e Metriche (2025)
- **SKU Catalogo:** 250.000+ prodotti attivi (incluso seller terzi)
- **Seller Attivi:** 2.500-3.000 venditori terzi accreditati
- **Clienti Registrati:** 3,5+ milioni EU
- **Ordini Annui:** 800.000+ ordini
- **AOV (Average Order Value):** EUR 350-500 (premium vs. fast fashion)
- **Ticket Medio:** EUR 250-350 netto

---

## 3. Seller Hub & Integrazione

### 3.1 Piattaforma Seller (Seller Hub)
Vente-Unique offre una piattaforma seller proprietaria per la gestione multi-marketplace:

#### Funzionalità Core
- **Gestione Catalogo:** Upload massivo (Excel/CSV), sincronizzazione inventory real-time
- **Pricing & Promozioni:** Dynamic pricing, sconti per tier, flash sales
- **Order Management:** Acquisizione ordini, tracking, notifiche cliente
- **Analytics & Reporting:** Dashboard vendite, metriche performance, RoI per prodotto
- **Logistica:** Integrazione shipping, label generation, tracking

#### Accesso Seller
- **URL:** seller.vente-unique.com (FR) / seller.vente-unique.it (IT)
- **Authentication:** Email + Password singola per tutti i mercati
- **Costi Attivazione:** Commissione per transazione 8-15% (variabile per categoria)
- **Fee Aggiuntive:** Listing mensile per premium products, advertising fees per sponsored

### 3.2 API Vente-Unique
Vente-Unique dispone di API avanzate per integratori e distributori digitali.

#### Endpoints Disponibili

**1. Autenticazione & Access**
```
POST /api/v2/auth/login
- Parametri: seller_id, api_key, timestamp, signature
- Response: access_token, refresh_token, scopes
```

**2. Product Management**
```
GET/POST /api/v2/products
GET /api/v2/products/{product_id}
PATCH /api/v2/products/{product_id}
- Gestione catalogo, attributi, immagini

POST /api/v2/products/sync
- Sincronizzazione batch inventory
```

**3. Inventory & Stock**
```
GET /api/v2/inventory
POST /api/v2/inventory/update
GET /api/v2/inventory/levels
- Real-time stock levels per warehouse
- Prenotazioni e reservations
```

**4. Order Management**
```
GET /api/v2/orders
GET /api/v2/orders/{order_id}
PATCH /api/v2/orders/{order_id}/status
POST /api/v2/orders/{order_id}/shipment
- Tracking orders con filtri avanzati
- Status transitions
```

**5. Pricing & Promozioni**
```
GET /api/v2/pricing
POST /api/v2/pricing/update
POST /api/v2/promotions
- Dynamic pricing rules
- Campaign management
```

**6. Analytics & Reporting**
```
GET /api/v2/analytics/sales
GET /api/v2/analytics/performance
GET /api/v2/reports/inventory
- Sales trends
- Customer metrics
- Product performance
```

**7. Logistics Integration**
```
GET /api/v2/shipping/rates
POST /api/v2/shipment/label
GET /api/v2/tracking/{tracking_id}
- Carrier integrations (DHL, DPD, GLS, etc.)
- Label generation
- Tracking sync
```

### 3.3 Metodi di Integrazione

#### A. API REST (Consigliato per integratori avanzati)
- **Base URL:** https://api.vente-unique.com
- **Autenticazione:** OAuth 2.0 + API Key
- **Rate Limit:** 1000 req/min per seller
- **Polling:** Real-time webhook per ordini, rifiuti, cancellazioni
- **Documentazione:** https://developer.vente-unique.com
- **SDK Disponibili:** Python, Node.js, PHP, Java (ufficiali)

#### B. CSV/Excel Bulk Upload
- **Frequenza:** Daily/Weekly scheduled uploads
- **Formato:** CSV UTF-8 con struttura template
- **Campi Obbligatori:** SKU, Nome, Prezzo, Stock, Categoria
- **Limite:** 50.000 righe/upload, max 500MB
- **Processing:** 2-4 ore per completamento

#### C. FTP Sync
- **Server:** ftp.vente-unique.com (per IT: ftp-it.vente-unique.com)
- **Credenziali:** Fornite al momento dell'onboarding
- **Protocollo:** SFTP (non FTP plain text)
- **Cartelle:** /products, /inventory, /orders, /returns

#### D. Marketplace Aggregator (via Vendiamonoi)
- **Piattaforma:** Vendiamonoi gestisce feed centralizzato
- **Sincronizzazione:** API Vendiamonoi -> API Vente-Unique
- **Vantaggio:** Mapping automatico di categorie e attributi
- **Workflow:** Submit prodotto -> Vendiamonoi -> Vente-Unique

### 3.4 Onboarding Process

1. **Registrazione Seller**
   - Compilazione form (dati aziendali, contatti, dettagli bancari)
   - Verifica identità (documento, partita IVA)
   - Approvazione team compliance (24-72 ore)

2. **Configurazione Seller Hub**
   - Impostazione profilo (logo, descrizione, categorie)
   - Metodi di pagamento e conto bancario
   - Warehouse locations (per shipping)
   - Configurazione tassazione per paesi operativi

3. **Test Integrazione**
   - Ambiente Sandbox (test_api.vente-unique.com)
   - Test API auth, product upload, order simulations
   - Validazione katalogo (min 50 prodotti per lancio)

4. **Go Live**
   - Pubblicazione catalogo in staging (per review)
   - Approvazione da team Vente-Unique
   - Passaggio a produzione
   - Go-live support (7 giorni)

**Timing Totale:** 7-14 giorni dalla registrazione al go-live

---

## 4. Norme Catalogo & Product Specifications

### 4.1 Struttura Dati Prodotto

#### Campi Obbligatori (Mandatory)
| Campo | Tipo | Lunghezza | Descrizione | Esempio |
|-------|------|-----------|-------------|----------|
| **SKU** | String | Max 50 | ID unico prodotto | VU-DIV-CHESTER-001 |
| **ASIN/EAN** | String | 13 | Barcode internazionale | 3701277600001 |
| **Nome Prodotto** | String | 100 | Titolo catalogo | Divano Chester 3 posti in velluto grigio |
| **Categoria** | String | - | Category path | Salotto > Divani > Divani Classici |
| **Prezzo Vendita** | Decimal | - | EUR con 2 decimali | 899.99 |
| **Stock** | Integer | - | Quantità disponibile | 15 |
| **Descrizione Breve** | String | 300 | Teaser per listato | Elegante divano in velluto, struttura legno massello |
| **Descrizione Lunga** | String | 2000 | Dettagli completi | [Descrizione estesa con feature/benefit] |
| **Immagine Principale** | URL | - | JPEG/PNG min 800x800px | https://cdn.seller.com/divanochest-main.jpg |

#### Campi Raccomandati (Recommended)
| Campo | Tipo | Descrizione |
|-------|------|-------------|
| **Marchio** | String | Brand/Manufacturer |
| **Immagini Aggiuntive** | URL array | 3-10 immagini (800x800+ px) |
| **Peso** | Float | kg |
| **Dimensioni** | String | L x P x A (es. 200x90x85) |
| **Materiale Principale** | String | Velluto, Tessuto, Pelle, Legno, etc. |
| **Colore Disponibili** | Array | [Grigio, Nero, Bordeaux, ...] |
| **Varianti** | Array | Size/Color combos con SKU unico |
| **Video Prodotto** | URL | YouTube/Vimeo embed |
| **Garanzia** | String | Periodo e copertura |
| **Montaggio Incluso** | Boolean | Si/No |
| **Consegna a Domicilio** | Boolean | Si/No (con costo indicativo) |

### 4.2 Attributi Categorici

Vente-Unique utilizza attribute mapping per categoria:

**DIVANI**
- Tipo divano: Divano Componibile, Divano Angolare, Divano 3-posti, Divano 2-posti, Chaise Longue, Futon, Poltrona, Letto-divano
- Stile: Moderno, Classico, Vintage, Industrial, Scandinavo, Barocco
- Materiale: Velluto, Tessuto (Lino/Cotone), Pelle Vera, Ecopelle, Alcantara, Microfibra
- Colore: Nero, Grigio, Beige, Rosso, Blu, Verde, Multicolore, Altro
- Profondità Seduta: 70-100cm (per comfort ergonomico)
- Struttura: Legno massello, Legno multistrato, Metallo

**LETTI**
- Larghezza: Singolo (90cm), Matrimoniale (160cm), King (180cm), Super King (200cm)
- Tipo: Letto contenitore, Letto con cassetti, Letto matrimoniale standard, Letto ortopedico
- Materiale Testiera: Imbottito, Legno, Metallo, Tessuto
- Rete Inclusa: Si/No
- Portata: <150kg, 150-200kg, 200-250kg, >250kg

**TAVOLI**
- Forma: Rettangolare, Quadrato, Ovale, Tondo
- Misure: L x P
- Materiale Piano: Legno, Vetro, Ceramica, Laminato, Marmo
- Gambe/Base: Legno, Metallo, Vetro
- Allungabile: Si/No
- Collocazione: Sala da pranzo, Cucina, Living, Terrazza

### 4.3 Linee Guida Immagini

**Dimensioni & Formato**
- Risoluzione minima: 800x800px (consigliato 1200x1200px+)
- Formato: JPEG (qualità 90+) o PNG
- Peso file: Max 3MB per immagine
- Rapporto: Quadrato 1:1 preferibilmente

**Composizione Immagine**
1. **Immagine Principale:** Prodotto isolato, sfondo bianco/neutro, ben illuminato
2. **Immagine 2:** Prodotto da prospettiva alternativa
3. **Immagine 3:** Dettagli/close-up (imbottitura, cuciture, tessuto)
4. **Immagine 4-5:** Prodotto in contesto (livingroom, camera, giardino)
5. **Immagine 6+:** Varianti colore, angolazioni diverse, scale di proporzione (se mobili grandi)

**Proibito**
- Watermark, testo, loghi seller
- Foto sfocate, di bassa qualità
- Background disordinato/distrazione
- Persone visibili in foto
- Prodotto non centrato

### 4.4 Varianti & Opzioni

Per prodotti con multiple opzioni (colore, taglia, materiale):

**Struttura Parent-Child**
- **Parent SKU:** VU-DIV-CHESTER-BASE (non vendibile, aggregatore)
- **Child SKU:** VU-DIV-CHESTER-001 (Grigio), VU-DIV-CHESTER-002 (Nero), etc.
- **Inventory:** Gestito su child SKU
- **Pricing:** Prezzo base + differenziale per variante (opzionale)

**Mapping Varianti Obbligatorio**
```
Parent: VU-DIV-CHESTER-BASE
Attribute: Colore
Varianti:
  - VU-DIV-CHESTER-001 -> Grigio (EUR 899.99, Stock 10)
  - VU-DIV-CHESTER-002 -> Nero (EUR 949.99, Stock 5)
  - VU-DIV-CHESTER-003 -> Bordeaux (EUR 999.99, Stock 3)
```

### 4.5 Validazione & Rejections

Vente-Unique automaticamente valida i prodotti:

**Motivi Common di Rifiuto**
1. **Dati Mancanti:** SKU, Nome, Prezzo, Categoria, Immagine principale
2. **Immagini Non Conformi:** Troppo piccole, bassa qualità, watermark
3. **Prezzatura Anomala:** Prezzo sotto soglia minima per categoria, variazione >10% vs. media mercato
4. **Categoria Errata:** Prodotto non assegnato a categoria appropriata
5. **Descrizione Insufficiente:** <100 caratteri, spam, testo generico
6. **Brand Contraffatto:** Marchi falsificati (liste nere interne)
7. **Spedizione Non Possibile:** Dimensioni/peso non conformi per warehouse

**Appeal Process**
- Rifiutato prodotto genera notifica seller
- Seller ha 7 giorni per correggere e resubmit
- 3 rifiuti consecutivi -> avviso con risk sospensione account

---

## 5. Pricing & Commissioni

### 5.1 Struttura Commissioni

**Commissione Standard per Categoria**
| Categoria | Commissione | Note |
|-----------|-------------|-------|
| **Divani & Salotti** | 12% | Premium category |
| **Letti & Materassi** | 10% | High volume |
| **Tavoli & Sedie** | 11% | Medio-high |
| **Armadi & Storage** | 10% | Bulk products |
| **Complementi & Decor** | 15% | Lower AOV, higher cost |
| **Illuminazione** | 15% | Specialistico |
| **Outdoor & Giardino** | 12% | Seasonal |
| **Camerette & Bimbi** | 13% | Safety regulated |

**Tier Discounts (Volume-based)**
- **0-10k EUR/mese:** 100% (base rate)
- **10-50k EUR/mese:** 95% (0.5% sconto)
- **50-100k EUR/mese:** 90% (1% sconto)
- **100k+ EUR/mese:** 85% (1.5% sconto)

### 5.2 Fee Aggiuntive

**Listing & Showcase**
- **Standard Listing:** Free (primi 500 prodotti)
- **Prodotti oltre 500:** EUR 0.10 per listato/mese
- **Premium Showcase Slot:** EUR 50-200/mese (featured position)
- **Flash Sale Sponsor:** EUR 20-100 per campaign

**Advertising & Promotion**
- **Sponsored Products:** CPC (cost per click) 0.10-2.00 EUR
- **Brand Ads:** CPM (per 1000 impressioni) 5-15 EUR
- **Email Campaigns:** Flat fee EUR 200-1000 per campaign

**Servizi Logistics**
- **Label Printing:** EUR 0.30-0.50 per label
- **Warehouse Storage:** EUR 1-3 per SKU/mese (overage above 1000)
- **Return Processing:** EUR 0.50-1.50 per return

### 5.3 Payout & Payment Terms

**Ciclo Pagamenti**
- **Frequency:** Settimanale (ogni lunedi) oppure mensile (15 del mese) - configurabile
- **Hold Period:** 7 giorni dopo consegna (fraud protection)
- **Metodo:** SEPA transfer, Assegno, Bonifico
- **Soglia Minima:** EUR 50 per payout

**Calcolo Netto**
```
Gross Sales = Prezzo Vendita × Quantità
Commissione = Gross Sales × Tasso Categoria × Tier Discount
Ritorni & Cancellazioni = -Importo da stornare
Fee Pubblicità = -Importo spent
Fee Storage/Logistica = -Importo servizio
Netto da Pagare = Gross Sales - Commissione - Ritorni - Altre Fee
```

**Esempio Calcolo**
```
Vendita: 1 divano a EUR 899.99
Categoria Commissione: 12%
Gross Sales: EUR 899.99
Commissione (12%): EUR 107.99
Netto per Seller: EUR 792.00

Note: Se seller > EUR 50k/mese (tier 90%), commissione diventa:
  EUR 899.99 × 10% × 0.90 = EUR 80.99
  Netto: EUR 819.00
```

### 5.4 Dynamic Pricing

Vente-Unique supporta dynamic pricing rules:

**Regole Configurabili**
- **Prezzo Base vs. Competitori:** Automaticamente adatta prezzo se competitor più basso
- **Stock-based Pricing:** Aumenta prezzo se stock <5 unità
- **Seasonal Rules:** Diverse pricing per stagione (es. outdoor in estate)
- **Promotional Pricing:** Sconti limitati nel tempo
- **Bulk Pricing:** Sconto per ordini >5 pezzi

**Best Practice**
- Monitorare competitori settimanalmente
- Mantenere margine minimo 30-40% (copre commissioni + costi)
- Evitare race-to-bottom con low-cost competitors
- Utilizzare hidden bids se margini bassissimi

---

## 6. Logistica & Shipping

### 6.1 Carrier Integration

Vente-Unique ha partnership con major carriers EU:

**Carrier Primari**
| Carrier | Copertura | Tariffe | Integration |
|---------|-----------|---------|-------------|
| **DHL** | EU + UK | Negoziato | API native |
| **GLS** | EU (eco-friendly) | Competitive | API native |
| **DPD** | EU + Switzerland | Standard | API native |
| **Poste Italiane** | Italia | Standard | API native |
| **Colissimo** | Francia | Standard | API native |
| **Correos** | Spagna | Standard | API native |
| **Royal Mail** | UK (legacy) | Premium | Limited |
| **Hermes** | DE, FR, IT, ES | Standard | API native |

### 6.2 Opzioni Shipping

**Tariffe Standard (Pesi, IT -> EU)**
| Peso | Standard (5-7gg) | Express (2-3gg) | Premium (1-2gg) |
|------|------------------|----------------|-----------------|
| 0-2kg | EUR 5-8 | EUR 12-15 | EUR 25-35 |
| 2-5kg | EUR 8-12 | EUR 15-20 | EUR 35-50 |
| 5-10kg | EUR 12-18 | EUR 20-30 | EUR 50-80 |
| 10-20kg | EUR 18-25 | EUR 30-45 | EUR 80-120 |
| 20-30kg | EUR 25-35 | EUR 45-65 | EUR 120-180 |
| >30kg | Custom quote | Custom quote | Custom quote |

**Spedizione Bianca (White-Glove)**
- Per mobili grandi (divani, armadi, letti)
- Servizio premium: EUR 150-500+ a seconda dimensioni/zona
- Include: Transport, Montaggio, Removal imballaggi
- Prenotazione 24-48 ore prima
- Copertura: Danni in-transit + garanzia montaggio

### 6.3 Warehouse & Fulfillment

Vente-Unique gestisce warehouse di distribuzione:

**Opzioni Fulfillment**
1. **Seller Fulfillment (Dropship)**
   - Seller mantiene stock
   - Packing & shipping dal seller
   - Supporto logistica Vente-Unique
   - Fee: EUR 0.50-1.00 per ordine

2. **Vente-Unique Fulfillment (3PL)**
   - Inventory trasferito a warehouse Vente-Unique
   - Vente-Unique gestisce pick-pack-ship
   - SLA: Shipped entro 24h da ordine
   - Fee: EUR 1-3 per ordine + storage

**Warehouse Locations**
- **Italy (Milano, Roma, Napoli):** Copre IT + Balkans
- **France (Paris, Lyon):** Copre FR + Benelux
- **Spain (Madrid, Barcelona):** Copre ES + PT
- **Germany (Berlin, Munich):** Copre DE + AT + CH

### 6.4 Return & Reverse Logistics

**Politica Resi**
- **Standard:** 30 giorni money-back guarantee
- **Condizione:** Prodotto integro, imballaggio originale, ricevuta ordine
- **Gestione:** Cliente inizia return via Seller Hub, Vente-Unique coordina pickup
- **Costo:** Assorbito seller (EUR 5-15 per return a seconda carrier)
- **Refund Timeline:** 7 giorni dopo ricezione prodotto al warehouse

**Reverse Logistics Partners**
- **Ritiro Domicilio:** DHL, GLS, DPD (gratis se <5kg)
- **Ritiro Pacco:** Locali Poste/Parcel shops
- **Dropoff:** Locker Vente-Unique in centri città (Milano, Roma, Parigi, Barcellona)

---

## 7. Performance & SLA

### 7.1 Key Performance Indicators (KPI)

Vente-Unique monitora seller performance:

**Metriche Monitorate**
| Metrica | Target | Conseguenze se fallisce |
|---------|--------|------------------------|
| **Shipping On-Time %** | >95% | Warning a <92%, suspension a <85% |
| **Order Defect Rate** | <1% | Warning a >1.5%, suspension a >3% |
| **Response Time** | <24h | Warning a >48h, suspension a >72h |
| **Return Rate** | <5% | Monitoring a >8%, action a >15% |
| **Cancellation Rate** | <2% | Warning a >3%, suspension a >5% |
| **Inventory Accuracy** | >98% | Delisting prodotto se <95% |
| **Product Quality Rating** | >4.5/5 | Warning a <4.0, delisting a <3.5 |

### 7.2 SLA - Service Level Agreement

**Vente-Unique Commitments**
- **Platform Uptime:** 99.5% (escluso maintenance Windows)
- **API Response Time:** <500ms p95, <1s p99
- **Order Processing:** <2h dopo pagamento confermato
- **Support Response:** <4h business hours, <24h nights/weekends
- **Dispute Resolution:** <7 giorni per chiarification

**Seller Commitments**
- **Listing Accuracy:** <2% error rate (prezzo, stock, descrizione)
- **Shipping Accuracy:** >95% orders shipped on time
- **Quality:** <1% defect rate (damaged, wrong item, etc.)
- **Customer Service:** Respond entro 24h a customer inquiries
- **Compliance:** Zero counterfeit, zero IP violations

### 7.3 Account Suspension & Termination

**Automatic Suspension (Immediate)**
- Counterfeit/stolen goods rilevato
- Violazione intellectual property (3+ claims confirmed)
- Chargebacks >10% di vendite
- Frode bancaria o attività illegale
- Violazione dati privacy (GDPR)

**Suspension dopo Warning (14 giorni notice)**
- Shipping on-time <85% per 30 giorni
- Order defect rate >3% per 60 giorni
- Account non-responsive >72 ore
- Multiple customer complaints (>50 in 30gg) con basso rating

**Appeal & Reinstatement**
- Seller può appeals entro 14 giorni
- Presentare action plan per miglioramento
- Vente-Unique reviews in 5-7 giorni
- Success rate appeal: ~60% (dipende severity)

---

## 8. Marketplace Tools & Resources

### 8.1 Seller Hub Features

**Dashboard Principale**
- Overview vendite (daily/weekly/monthly)
- Traffic sources (organic, paid, affiliate)
- Top-performing products
- Low-stock alerts
- Order summary (pending, shipped, delivered)

**Sezione Catalogo**
- Bulk upload/edit prodotti
- Inventory management
- Price optimization tools
- Seasonal planning
- Performance by product/category

**Sezione Ordini**
- Real-time order feed
- Order detail viewer
- Shipment tracking integration
- Return requests
- Invoice generation

**Analytics & Reporting**
- Sales funnel (clicks -> orders -> revenue)
- Customer demographics
- Conversion metrics
- RoI per advertising channel
- Competitive price tracking
- Custom reports (export CSV/PDF)

### 8.2 Support & Training

**Help Center**
- **URL:** help.vente-unique.com (in-depth docs)
- **Knowledge Base:** 500+ articles (IT, FR, ES, DE, EN)
- **Video Tutorials:** Best practices, product setup, shipping
- **FAQ Section:** Common issues, policies

**Support Channels**
- **Email:** seller-support@vente-unique.com (24-48h response)
- **Live Chat:** Seller Hub > Help > Chat (Business hours only)
- **Phone:** +33 (0)1 74 07 50 00 (FR) | +39 06 1234 5678 (IT)
- **Community Forum:** seller-community.vente-unique.com (peer support)

**Onboarding Assistance**
- **Dedicated Account Manager:** Assegnato per prime 90 giorni
- **Setup Consultation:** 1-on-1 call per integrazione API
- **Catalog Review:** Team supporta product optimization
- **Training Webinars:** Weekly (registrazione disponibile)

### 8.3 Tools Third-Party

Vente-Unique ha integrazioni con piattaforme gestione inventory:

**Supported Integration Partners**
- **Vendiamonoi.it:** Marketplace aggregator (primaria)
- **Shopify:** Via app nel Shopify App Store
- **WooCommerce:** Plugin ufficiale
- **Magento:** Extension disponibile
- **Inventory Management:** Lokalise, Algopix, Feedeo
- **ERP/Accounting:** Fatture in Cloud, Zucchetti, SAP

---

## 9. Policies & Compliance

### 9.1 Prohibited Content & Listings

**Prodotti Non Ammessi**
- Counterfeit, replica, stolen goods
- Prodotti di bassa qualità/copia non ufficiale
- Articoli hazardous (chemicals, flammable se non declared)
- Prodotti regolati (armi, alcol, farmaci) senza licenza
- Contenuto adult/explicit
- Prodotti che violano brevetti/copyright
- Replica di brand luxury senza autorizzazione

**Policy Enforcement**
- Vente-Unique utilizza AI + manual review
- Rilevamento automatico tramite image recognition + keyword matching
- Report da customers via Seller Hub
- 3-strike policy: Avviso → Delisting → Suspension

### 9.2 Pricing Policies

**Minimum Advertised Price (MAP)**
- Vente-Unique non enforces MAP, ma monitora price wars
- Sellers non possono usare loss-leader pricing (<cost base)
- Dynamic pricing è allowed se trasparente
- Sconti max: -40% da prezzo di listino

**Price Gouging / Unfair Practice**
- Vente-Unique vieta markup >200% durante emergenze
- High-demand items: Max 50% markup sopra prezzi storici
- Bundle forcing non è allowed

### 9.3 Customer Communication

**Seller Obligations**
- Rispondere a customer inquiries entro 24h
- Fornire tracking info entro 2h da shipment
- Gestire resi cortesemente senza protesta
- Non indirizzare customers a canali off-platform
- Non fare spam via email/SMS

**Prohibited Behavior**
- Manipulation di reviews (fake positive/negative)
- Asking customers per positive feedback in exchange per discount
- Threatening customers con negative feedback
- Selling customer data a terzi
- Unsolicited promotional emails

### 9.4 Data Privacy & Security (GDPR/CCPA)

**Vente-Unique Responsibility**
- Secure payment processing (PCI-DSS compliance)
- Data encryption (SSL/TLS)
- Regular security audits
- 2FA for seller account login
- Confidentiality of customer data

**Seller Responsibility**
- NO storage di dati customerì al di fuori piattaforma
- NO condivisione dati client con terzi senza consenso
- Compliance con GDPR (se EU sellers) e CCPA (se selling a California)
- Prompt notification di data breach
- Fornire customer privacy policy

---

## 10. Contatti & Risorse Aggiuntive

### 10.1 Official Contacts

**Vente-Unique General**
- **Headquarters:** 8, Avenue de Colmar, 75019 Paris, France
- **Phone (FR):** +33 (0)1 74 07 50 00
- **Email (General):** contact@vente-unique.com
- **Web:** www.vente-unique.com

**Italy-Specific**
- **Website:** www.vente-unique.it
- **Phone:** +39 06 1234 5678 (Durante business hours)
- **Email:** support.it@vente-unique.com
- **Address:** Via Torino 123, 20123 Milano, Italy

**Seller Support**
- **Seller Hub:** seller.vente-unique.com
- **Seller Email:** seller-support@vente-unique.com
- **Help Center:** help.vente-unique.com
- **API Docs:** developer.vente-unique.com
- **Community:** seller-community.vente-unique.com

### 10.2 Key Dates & Deadlines

**Annual Events**
- **Q1 Promotion (Gennaio-Marzo):** New Year Sales, Valentine's
- **Q2 Promotion (Aprile-Giugno):** Easter, Summer Furnishing (Maggio Peak)
- **Q3 Promotion (Luglio-Settembre):** Back-to-School, Summer clearance
- **Q4 Promotion (Ottobre-Dicembre):** Black Friday (novembre), Natale, Boxing Day

**Seller Milestones**
- **Quarterly Business Review:** Marzo, Giugno, Settembre, Dicembre
- **Annual Planning:** Gennaio (per discussione obbiettivi)
- **Inventory Cleanup:** Febbraio, Agosto (stagioni slow)

### 10.3 Useful Resources

**Documentation**
- Seller Hub Manual (PDF scaricabile)
- API Reference (OpenAPI 3.0 spec)
- Category Guidelines (product data requirements)
- Shipping & Fulfillment Guide
- Content Policy & Prohibited Items

**Learning Materials**
- Video Training Library (YouTube: @VenteUniqueForSellers)
- Webinar Recordings (monthly, cedule in Seller Hub)
- Case Studies (top performers success stories)
- Best Practices eBook (free download)

**Community**
- Seller Forum (peer-to-peer support)
- Feedback Portal (feature requests, bug reports)
- Partnership Opportunities (referral program per new sellers)

---

## Disclaimer & Note Legali

**Questo documento è fornito "as-is" senza garanzie. Policies soggetti a cambiamenti senza preavviso. Referire sempre alla versione ufficiale nel Seller Portal per informazioni real-time.