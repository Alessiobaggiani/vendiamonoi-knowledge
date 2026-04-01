# Specifica Tecnica Marketplace MediaWorld Italia
## Guida Completa per Operatori e System Architects

**Versione:** 2.1
**Data:** Aprile 2026
**Audience:** CTO, System Architects, Marketplace Operators
**Lingua:** Italiano
**Scope:** Vendiamonoi.it - Operazioni su MediaWorld e MediaMarkt europei

---

## 1. Informazioni Generali

| Attributo | Valore |
|-----------|--------|
| **Brand** | MediaWorld (Italia) / MediaMarkt (Gruppo) |
| **Genitore Aziendale** | MediaMarktSaturn Retail GmbH (Parte di Ceconomy AG) |
| **Sede Italiana** | Milano, Italia |
| **URL Principale** | www.mediaworld.it |
| **Seller Portal** | seller.mediaworld.it (Mirakl) |
| **API Endpoint** | api.mediaworld.it/v1 |
| **Piattaforma** | Mirakl SaaS (Marketplace Engine) |
| **Categoria Merceologica** | Consumer Electronics, Elettrodomestici, Informatica, Telefonia Mobile |
| **Numero SKU Attivi** | ~800.000 (varia per categoria) |
| **Modello** | Hybrid: Marketplace 3rd-party + Direct-to-Consumer |
| **Paesi Operativi** | Italia, Germania, Spagna, Paesi Bassi, Belgio (gruppo) |
| **Compliance Regionale** | GDPR EU, Direttiva RAEE, Etichettatura Energetica EU |

---

## 2. Modello di Business

### 2.1 Strategia MediaMarktSaturn Ceconomy

MediaWorld Italia opera come **Hybrid Marketplace Model**:

- **Direct Inventory**: MediaWorld mantiene propri stock nelle categorie core (TV, smartphone, laptop)
- **Marketplace 3rd-party**: Seller external forniscono complementary inventory
- **Copertura Geografica**: MediaWorld è flagship per Italia; MediaMarkt per DE/ES/NL/BE
- **Sinergie Gruppo**: Condivisione logistica, payment infrastructure, compliance framework

### 2.2 Categorie Principale

| Categoria | Commission | Focus |
|-----------|-----------|-------|
| **TV & Monitor** | 7-8% | Samsung, LG, Sony, Hisense, OLED/4K/8K |
| **Smartphone & Tablet** | 5-7% | iPhone, Samsung Galaxy, Xiaomi, OnePlus |
| **Informatica (PC/Notebook)** | 6-8% | Dell, Lenovo, HP, ASUS, Apple |
| **Accessori Tech** | 8-10% | Cavi, Batterie, Caricatori, Cover, Pellicole |
| **Fotografia & Video** | 7-9% | Canon, Nikon, Sony, Fujifilm, DJI Drone |
| **Audio & Musica** | 8-10% | Cuffie, Speaker, Auricolari (Sony, Bose, Sennheiser) |
| **Gaming (Console/PC)** | 6-8% | PlayStation, Xbox, Nintendo, PC Gaming |
| **Elettrodomestici Grandi** | 4-6% | Frigoriferi, Lavatrici, Forni, Climatizzatori |
| **Climatizzazione** | 5-7% | Condizionatori, Ventilatori, Umidificatori |
| **Sicurezza & Sorveglianza** | 7-9% | Telecamere IP, Sistemi NVR, Sensori |
| **Smart Home & IoT** | 8-10% | Alexa, Google Home, Lampadine Smart, Prese WiFi |
| **Stampa & Scansione** | 6-8% | Stampanti Laser, Inkjet, All-in-one, Scanner |
| **Networking** | 6-7% | Router, Switch, Access Point, Cabling |

---

## 3. Struttura della Piattaforma Mirakl

### 3.1 Seller Portal (seller.mediaworld.it)

La piattaforma Mirakl-powered offre:

#### Dashboard Seller
- **Real-time Reporting**: Sales, Inventory, Performance Metrics
- **Order Management**: Ordini, Tracking, Returns, Messaging
- **Inventory Management**: Stock levels, SKU sync, Multi-warehouse support
- **Pricing Manager**: Dynamic pricing, Promotion rules, Competitor monitoring
- **Analytics**: Sales trends, Customer feedback, Conversion rates
- **Document Management**: Invoices, Shipping labels, Pick lists

#### Onboarding Process
1. Account registration (3-5 giorni)
2. KYC verification (2-7 giorni)
3. Store setup e configurazione catalogo
4. Product catalog upload (bulk via CSV/XML)
5. Test orders (3-5 transazioni di prova)
6. Go-live approval

---

## 4. Catalogo Prodotti e Integrazione

### 4.1 Feed di Prodotti

**Formato supportati:**
- CSV (Comma-Separated Values) - Recommended
- XML (custom schema Mirakl)
- SFTP automated feeds (daily sync)
- API-based real-time updates

**Obbligatori per ogni SKU:**
```
- SKU/EAN
- Product Title (200-400 caratteri)
- Description (800-2000 caratteri)
- Category (standard Mirakl taxonomy)
- Price (EUR)
- Stock/Quantity Available
- Condition (New, Refurbished, Used)
- Images (min 1, recommended 5+)
- Shipping weight & dimensions
- Brand
- Specifications (per categoria)
```

### 4.2 Attributi Estesi Richiesti

**Attributi Consumer Electronics (Mandatory):**
- Marca/Brand
- Modello
- Colore
- Capacità/RAM (per PC/Smartphone)
- Risoluzione (per monitor/TV)
- Tipo di connettività (WiFi, Bluetooth, 4G, LTE)
- Garanzia inclusa (mesi)
- Anno di uscita
- Compatibilità (SO, versione, ecc)
- Consumo energetico (kWh/anno) - Mandatory per EV

**Attributi Elettrodomestici (Mandatory):**
- Classe energetica (A+++, A++, A+, A, B, C, ...)
- Consumi (kWh/anno, L/ciclo)
- Rumorosità (dB)
- Dimensioni (L x P x A in cm)
- Capacità (L, kg, posti coperto)
- Certificazioni (CE, EEC, ENERGY STAR)

---

## 5. Logistica e Spedizione

### 5.1 Modelli di Spedizione Supportati

#### Fulfilled by Merchant (FBM) - DEFAULT
- Seller responsabile per tutti i shipment
- Pickup presso sede seller o warehouse
- Packing e labeling a carico del seller
- Integrazione con corrieri italiani

**Corrieri autorizzati:**
- DHL Express
- GLS
- SDA (Poste Italiane)
- Bartolini
- Hermes
- UPS
- TNT
- BRT (Bartolini)
- Nexive
- Amazon Logistics (se integrato)

#### Fulfilled by MediaWorld (FBW) - PREMIUM OPTION
- MediaWorld gestisce fulfillment end-to-end
- Seller invia inventory a warehouse MediaWorld
- Commission aggiuntiva: +3-5% su transaction
- Tempi di delivery ridotti: 24-48h in Italia
- Gestione automatica di return e refund

**Requisiti FBW:**
- Minimo 50 SKU
- Stock minimo: 100 unità per SKU
- Shipping pre-paid a warehouse MediaWorld
- Quality threshold: max 2% defective rate

### 5.2 Tariffe di Spedizione

**Standard (FBM - Seller managed):**
- 0-1 kg: EUR 4.99
- 1-5 kg: EUR 7.99
- 5-10 kg: EUR 11.99
- 10-20 kg: EUR 18.99
- 20+ kg: Custom quote

**Express (FBW - MediaWorld managed):**
- 0-1 kg: EUR 9.99 (24h delivery)
- 1-5 kg: EUR 13.99 (24h delivery)
- 5+ kg: EUR 19.99 (48h delivery)

**Free Shipping Thresholds:**
- Spese di spedizione gratis per ordini > EUR 49 (selettivo per categoria)

---

## 6. Pagamenti e Liquidazione Finanziaria

### 6.1 Payment Methods Accettati

**Customer-facing:**
- Credit Card (Visa, Mastercard, American Express)
- Debit Card (Maestro, Carte Bancaire)
- PayPal
- Klarna (Buy Now Pay Later - BNPL)
- Satispay (digital wallet italiano)
- Apple Pay / Google Pay
- Amazon Pay
- Bonifico Bancario (pre-paid)

### 6.2 Settlement & Payout

**Ciclo di pagamento (Payment Cycle):**
- Pagamento entro: T+5 giorni working days da order dispatch
- Frequenza: Weekly (ogni lunedì)
- Metodo: SEPA transfer diretto a conto IBAN seller
- Commissioni: Detratte automaticamente dalla liquidazione

**Commissioni applicate:**
- Merchant Commission: 5-10% (per categoria)
- Payment Processing Fee: 1.5-2.5% (su totale transazione)
- Chargeback protection: 0.5% (su transazioni rischiose)
- Return handling fee: EUR 2-5 per return (se FBW)

**Esempio di calcolo liquidazione:**
```
Ordine di EUR 100
- Prezzo prodotto: EUR 100
- Commission (7%): EUR -7
- Payment fee (2%): EUR -2
- Netto seller: EUR 91
Liquidazione: EUR 91 (dopo T+5 giorni)
```

### 6.3 Hold & Reserve Policy

- **New sellers**: First EUR 1000 held for 30 days (chargeback protection)
- **Violation reserve**: 10% of monthly revenue if policy violations detected
- **Return reserve**: 5% held pending return window closure

---

## 7. Politiche e Compliance

### 7.1 Performance Standards

**Seller Performance Metrics (KPIs monitored):**

| Metrica | Target | Penalità |
|---------|--------|----------|
| **Order Response Time** | Max 4 ore | Suspension |
| **Order Cancellation Rate** | < 2% | Warning (> 5% = suspension) |
| **Return Rate** | < 5% | Monitoring |
| **Late Shipment Rate** | < 2% | Downrank in search |
| **Customer Satisfaction** | > 4.0/5.0 stars | Deactivation < 3.0 |
| **Defect Rate** | < 2% | Quality review |
| **Non-delivery Rate** | < 1% | Warning |

### 7.2 Prohibited Items & Content

**Assolutamente VIETATO:**
- Counterfeit products
- Stolen goods
- Hazardous materials (senza autorizzazione)
- Adult/NSFW content
- Weapons, explosives, ammunition
- Drugstore items (farmaci senza ricetta)
- Perishable food (fresco)
- Live animals
- Bulk reselling di stock MediaWorld
- Items violating trademark/copyright

**Restricted (con approval speciale):**
- Alcohol & Tobacco (age verification required)
- Electronics refurbished (full disclosure required)
- Used electronics ("As-is" condition mandatory)
- White-label resellers (autorizzazione brand required)

### 7.3 Termini di Servizio

**Account Suspension Triggers:**
1. Violazione copyright/trademark (first offense = suspension)
2. Policy violations (3+ violations in 90 days)
3. Customer complaints (> 10 grievances in 30 days)
4. Chargeback rate > 5%
5. Counterfeit products
6. Failure to respond to customers (> 10 unresponded inquiries)
7. Financial issues (unpaid fees, invoice rejection)

**Appeal Process:**
- Appeal window: 48 hours after suspension
- Appeal submission via seller dashboard
- MediaWorld review team: 5-7 working days response
- Final decision: binding & non-negotiable

---

## 8. Strumenti Marketing & Promotions

### 8.1 Promotional Tools

**MediaWorld Promo Framework:**

#### Flash Sales (Offerte Lampo)
- Duration: 1-24 ore
- Discount: Max 50% off
- Inventory limit: Seller selects
- Frequency: Max 2 per week per SKU
- Fee: Gratis (parte di platform marketing)

#### Daily Deals & Featured Listings
- Homepage placement: EUR 5-20/day per product
- Category featured: EUR 3-10/day
- Email blast: EUR 15-50 per campaign
- Guarantee: Min 500 impressions

#### Seasonal Campaigns
- Black Friday: EUR 200-1000 per store (November)
- Natale: EUR 150-800 per store (October-December)
- Back to School: EUR 100-500 (August-September)
- Easter: EUR 50-300 (March-April)

### 8.2 SEO & Search Optimization

**Title Best Practices:**
- Format: [Brand] [Model] [Key Specs] - [Color/Size]
- Example: "Samsung 65\" QLED Q90B 4K Smart TV - Black"
- Max length: 200 chars (recommendation: 100-150)
- Include: Brand, Model, Key differentiator, Color

**Description Best Practices:**
- Structtura: Features → Benefits → Specs → Warranty
- Length: 800-2000 characters
- Include bullet points (max 5)
- Keywords: Natural integration, no keyword stuffing
- Avoid: HTML tags, excessive caps, misleading claims

**Image Requirements:**
- Minimum: 1 image
- Recommended: 5-8 images
- Size: Min 1024x1024px (recommended: 1500x1500px+)
- Format: JPG, PNG
- Content: Product shot + Lifestyle + Specs sheet
- Prohibito: Watermarks, competitor logos, external links

---

## 9. Customer Service & Support

### 9.1 Communication Channels

**Seller-to-Customer Communication:**
- Messaging platform (built-in): Mirakl messaging
- Response time SLA: Max 4 hours (or account warning)
- Language: Italiano (Italian only required)
- Escalation: Customer care per unresolved issues

**Pre-Purchase Support:**
- Q&A section (on product page)
- Seller can add FAQs
- Response time: 24 hours
- Rating system for helpful answers

### 9.2 Returns & Refund Policy

**Return Window:**
- Standard: 30 giorni da delivery
- Extended: 60 giorni per electrical/electronics items
- Condition: Must be unused, in original packaging

**Return Process:**
1. Customer initia return via seller portal/customer service
2. Seller approves/rejects within 24 hours
3. Customer ships back (cost: seller's responsibility per EU law)
4. Seller inspects & confirms receipt (3-5 days)
5. Refund processed (7-14 working days)

**Refund Policy:**
- Full refund if: Product defective, wrong item, not as described
- Partial refund (20-50%): If used, minor damage, missing accessories
- No refund if: Customer's remorse, damaged due to misuse

**Dispute Resolution:**
- Mediation: MediaWorld customer care (first step)
- Escalation: EU Alternative Dispute Resolution (ADR)
- Binding: Italian law applies (Milano jurisdiction)

---

## 10. API Integration & Technical

### 10.1 API Endpoints

**Base URL:** `https://api.mediaworld.it/v1`

**Authentication:**
```
Header: Authorization: Bearer {API_TOKEN}
Content-Type: application/json
```

### 10.2 Core API Operations

#### Products API
```
POST /products/upload
- Upload product catalog (CSV/XML)
- Batch size: max 500 SKUs per request
- Frequency: max 10x per day

GET /products/{sku}
- Retrieve product details
- Includes: Title, Description, Images, Inventory, Pricing

PUT /products/{sku}
- Update product (title, desc, price, images, specs)

DELETE /products/{sku}
- Delist product (not permanent)
```

#### Inventory API
```
GET /inventory/{sku}
- Check stock level
- Real-time

PUT /inventory/{sku}
- Update stock
- Request: { "quantity": 50 }

POST /inventory/batch
- Bulk inventory update
- Max 1000 SKUs per request
```

#### Orders API
```
GET /orders
- List seller's orders
- Filtering: Date range, Status, Shipping method

GET /orders/{order_id}
- Get order details
- Includes: Items, Shipping, Customer, Timeline

POST /orders/{order_id}/shipment
- Update tracking info
- Request: { "tracking_number": "...", "carrier": "DHL" }

POST /orders/{order_id}/cancel
- Cancel order (pre-shipment only)
```

#### Pricing API
```
PUT /pricing/{sku}
- Update price
- Request: { "price": 99.99 }

GET /pricing/recommendations
- Get dynamic pricing suggestions
- Based on competitor monitoring
```

#### Returns API
```
GET /returns
- List return requests

GET /returns/{return_id}
- Get return details

POST /returns/{return_id}/approve
- Approve a return request

POST /returns/{return_id}/reject
- Reject a return (with reason)
```

### 10.3 Webhooks

**Webhook Events (Seller subscribe to):**
- `order.created`: New order placed
- `order.payment_confirmed`: Payment authorized
- `order.shipped`: Seller marked shipped
- `order.delivered`: Carrier confirmed delivery
- `return.initiated`: Customer started return
- `return.completed`: Return closed
- `message.received`: New customer message
- `product.delisted`: Product removed from catalog
- `account.suspended`: Account action

**Webhook Setup:**
```json
{
  "event": "order.created",
  "webhook_url": "https://seller-system.com/webhooks/mediaworld",
  "headers": {
    "Authorization": "Bearer {WEBHOOK_TOKEN}"
  },
  "active": true
}
```

---

## 11. Analitiche e Reporting

### 11.1 Dashboard Metrics

**Seller Dashboard disponibilità:**
- Sales Summary: Daily, Weekly, Monthly, YTD
- Top Products: By revenue, by orders, by growth
- Traffic Sources: Organic search, category browse, direct
- Customer Insights: New vs repeat, geography, device type
- Inventory Health: Stock warnings, slow-moving items
- Performance Scorecard: KPI compliance, trend analysis

**Data Export:**
- CSV export (daily, weekly, monthly reports)
- API access for real-time data
- Retention: 24 months historical data

### 11.2 Customer Feedback

**Review System:**
- 5-star rating scale
- Written reviews (optional)
- Seller response capability
- Verification: Only verified buyers can review
- Moderation: MediaWorld reviews content

**Negative Review Management:**
- Seller can request review removal (if policy violation)
- Appeal window: 30 days
- MediaWorld decision: Final & binding
- Public response: Seller can reply to reviews

---

## 12. Soluzioni Specifiche per Categorie

### 12.1 Electronics & Computing

**Garanzia Obbligatoria:**
- Minimo: 12 mesi produttore
- Consigliato: 24-36 mesi per valori alti
- Opzione: Extended warranty (seller può offrire)

**Certificazioni Richieste:**
- CE mark (mandatory)
- RoHS compliance
- ENERGY STAR (per computer/monitor)
- UL/FCC (se importato da USA)

**Dichiarazioni Obbligatorie:**
- Country of origin
- Hazardous substances (RoHS)
- Consumo energetico (Energy label)
- Compatibilità (SO, browser, ecc)

### 12.2 Grandi Elettrodomestici

**Energy Label Obbligatoria:**
- EU directive 2021/341
- Product fiche (datasheet) required
- Consumo energetico (kWh/anno, L/ciclo, dB)
- Etichetta classe energetica (A+++, A++, A+, A, B, C, ...)

**Specifiche Obbligatorie per Categoria:**
- **Frigoriferi**: Consumo, Capacità litri, Classe energetica, Rumorosità
- **Lavatrici**: Capacità kg, Consumo, Classe energetica, Programmi, Rumorosità
- **Forni**: Capacità litri, Tipo (combinato, vapore, tradizionale), Consumo
- **Climatizzatori**: Potenza (kW), Tipo (split, portatile), SEER/SCOP, Rumorosità

**Garanzia Minima:**
- 24 mesi per grandi elettrodomestici
- 60 mesi for compressor (se applicabile)

### 12.3 Fotografia & Video

**Requisiti di Autenticità:**
- Solo lenti/body originali autorizzati
- Refurbished/Open box deve essere dichiarato
- Full compatibility specs required
- Accessori inclusi dettagliati

**Specifiche da includere:**
- Sensor resolution (MP)
- Focal length (mm) per lenti
- f-number (apertura)
- Image stabilization technology
- Video specs (resolution, fps, codec)
- Connectivity (WiFi, Bluetooth, USB)

---

## 13. Conformità Normativa (GDPR, RAEE, Etichettatura)

### 13.1 GDPR Compliance (Data Protection)

**Seller Responsabilità:**
- Protegge i dati client (email, indirizzi)
- Non condivide dati con terzi (senza consenso)
- Privacy policy trasparente
- Diritto di deletion ("Right to be forgotten")
- Breach notification: Entro 72 ore a MediaWorld

**Data Processing Agreement:**
- Signed by default (Mirakl DPA)
- Seller è "data processor" per customer data
- MediaWorld è "data controller"

### 13.2 RAEE (Rifiuti Apparecchiature Elettriche)

**Obblighi Seller (Electronic Waste):**
- Informazione sui rifiuti: Statement required in product listing
- Programma di ritiro: Seller DEVE offrire programma di ritiro (gratuito per cliente)
- Registro RAEE: Registrazione presso ECOS-Conai
- Documentazione: Licenza di smaltimento (licenza RAEE)

**Testo Obbligatorio da includere:**
```
"Il simbolo della pattumiera barrata sulla spalla (Allegato II Direttiva 2012/19/UE) 
indica che la raccolta differenziata è obbligatoria.

Conformemente alla Direttiva 2012/19/UE sui rifiuti di apparecchiature elettriche 
e elettroniche (RAEE), questo prodotto può essere riciclato.

Consegni il prodotto a fine vita presso un punto di raccolta RAEE designato 
oppure contacta il venditore per il ritiro.

Maggiori info: https://www.conai.org/RAEE"
```

### 13.3 Etichettatura Energetica (Energy Labeling)

**Direttive Applicabili:**
- EU 2021/341 (revised labeling requirements)
- EU 2021/342 (ecodesign requirements)
- Detergenti e prodotti pulizia: EU 1254/2014

**Requirements per Categoria:**

**Apparecchi Refrigerazione:**
- Energy consumption (kWh/year)
- Noise level (dB)
- EPREL database registration

**Lavatrici & Asciugatrici:**
- Consumo energetico (kWh/ciclo, kWh/anno)
- Consumo d'acqua (L/ciclo)
- Durata ciclo (minuti)
- EPREL registration

**Televisori:**
- Energy consumption (kWh/year, standby power)
- Classe energetica
- Diagonale schermo (pollici)

**Illuminazione:**
- Consumo energetico (W, kWh/1000h)
- Durata media (ore)
- Temperatura colore (K)
- Consumo energetico relativo (%)

---

## 14. Gestione delle Controversie & Escalation

### 14.1 Conflict Resolution Process

**Livello 1: Direct Communication (72 ore)**
- Customer e Seller comunicano direttamente
- Goal: Risoluzione amichevole
- Deadline: 48 ore per risposta

**Livello 2: MediaWorld Mediation (5-7 giorni)**
- Se non risolto, MediaWorld customer care interviene
- Revisione di entrambi i lati
- Decision sulla base di policy MediaWorld

**Livello 3: ADR (Dispute Resolution) (30 giorni)**
- Alternative Dispute Resolution (EU-mandated)
- Neutral arbitrator
- Binding decision (non-appealable)

**Livello 4: Legal Action**
- Italian courts (Milano jurisdiction)
- Consumer protection laws apply
- EU jurisdiction provisions (Brussels I Regulation)

### 14.2 Chargeback Protection

**Seller Protection Policy:**
- MediaWorld covers chargeback fees (seller indemnified)
- But: Seller liable if policy violations found
- Fraud pattern: Seller account suspended

**Fraud Prevention:**
- Address verification (AVS)
- 3D Secure authentication
- Card-not-present (CNP) monitoring
- IP geolocation tracking

---

## 15. Accordi Commerciali & Expansion

### 15.1 Seller Tiers & Benefits

#### Bronze Tier (Default - All New Sellers)
- Commission: 7-10% (base rate)
- Payment: Weekly (T+7 days)
- Support: Email support (48h response)
- Marketing: Self-service tools only
- Volume: No minimum

#### Silver Tier (EUR 10k+/month GMV)
- Commission: 5-8% (negotiable)
- Payment: Bi-weekly (T+5 days)
- Support: Priority support (24h response)
- Marketing: Promotional tools discount (10%)
- Volume: EUR 10k+/month requirement
- Eligibility: 90+ days active, 4.5+ rating

#### Gold Tier (EUR 50k+/month GMV)
- Commission: 3-6% (custom negotiation)
- Payment: Bi-weekly (T+3 days)
- Support: Dedicated account manager
- Marketing: Co-marketing opportunities
- Volume: EUR 50k+/month requirement
- Eligibility: 6+ months active, 4.7+ rating, < 1% cancellation

#### Platinum Tier (EUR 250k+/month GMV + Exclusive Agreement)
- Commission: 2-5% (fully negotiable)
- Payment: On-demand (T+1 day)
- Support: Dedicated account team (phone, email, Slack)
- Marketing: Featured brand positioning, homepage deals
- Logistics: Dedicated FBW fulfillment centers
- Volume: EUR 250k+/month guaranteed
- Contract: 12-24 months exclusive agreement
- Eligibility: 12+ months active, 4.8+ rating

### 15.2 Growth Incentives Program

**Quarter Acceleration Bonuses:**
- Q1 (Jan-Mar): +15% commission bonus if 30% QoQ growth
- Q2 (Apr-Jun): +12% commission bonus if 25% QoQ growth
- Q3 (Jul-Sep): +10% commission bonus if 20% QoQ growth
- Q4 (Oct-Dec): +8% commission bonus if 15% QoQ growth + holiday boost

**New Category Launch Incentive:**
- 0% commission for first EUR 5,000 GMV in new category
- 2.5% commission for next EUR 10,000
- Then standard tier rate applies

---

## 16. Contatti e Support

### 16.1 Seller Support

**Primary Contact:**
- Email: seller-support@mediaworld.it
- Hours: Monday-Friday, 09:00-18:00 CET
- Response time: 24-48 hours

**Escalation:**
- Phone: +39-02-XXXX-XXXX (Limited)
- Slack: partner.mediaworld.it (per enterprise sellers)
- Portal: https://seller.mediaworld.it/support

### 16.2 Documentation & Resources

**Official Resources:**
- Seller Academy: https://academy.mediaworld.it
- API Documentation: https://api-docs.mediaworld.it
- Community Forum: https://community.mediaworld.it
- Policy Center: https://seller.mediaworld.it/policies

**Training Programs:**
- Onboarding course (required, 2-3 ore)
- Category specialization courses (optional)
- Quarterly partner webinars
- 1-on-1 training sessions (by request)

---

## 17. Appendice: FAQ & Troubleshooting

### Q: Qual è il tempo medio di approvazione per un nuovo seller?
**A:** 5-10 working days. Fast-track available (2-3 days) per seller con esperienza provata.

### Q: Come posso aggiornare l'inventario in real-time?
**A:** Via API endpoints (/inventory/update) o CSV bulk upload. Sync consigliato: ogni 4 ore.

### Q: Quali categorie hanno le commissioni più basse?
**A:** Grandi elettrodomestici (4-6%), Smartphone (5-7%), Informatica (6-8%).

### Q: Posso offrire free shipping?
**A:** Sì, opzionale. Non è richiesto, ma migliora conversion. Configurabile per SKU/categoria.

### Q: Quanto tempo per ricevere pagamenti?
**A:** Standard: T+7 working days. Silver tier: T+5. Gold: T+3. Platinum: T+1 (on demand).

### Q: Come gestisco i resi?
**A:** Via dashboard returns management. Seller approva/rifiuta entro 24h, poi customer spedisce indietro.

### Q: Cosa succede se un prodotto è difettoso?
**A:** Customer avvia return. Se manufacturer defect provato, seller rimborsa integralmente + spese return.

---

## 18. Clausole Finali & Dichiarazioni

**Documento Confidenziale**
Questo documento contiene informazioni proprietarie e confidenziali di MediaWorld Italia e Ceconomy AG. 
Distribuzione autorizzata esclusivamente a personale autorizzato con accesso NDA attivo.

---

*Documento preparato per Vendiamonoi.it - Digital Distribution Partner*
*© 2026 Ceconomy AG, MediaWorld Italia. Tutti i diritti riservati.*