# METRO Markets (Germania/Internazionale) — Specifica Tecnica Completa

**Documento di Specifica Marketplace per Vendiamonoi.it**  
**Versione 1.0 | Data: 2026-04-01**  
**Target: CTO, System Architects, Marketplace Operators**

---

## 1. Informazioni Generali

| Parametro | Valore |
|-----------|--------|
| **Nome Marketplace** | METRO Markets (METRO.online) |
| **Operatore** | METRO AG (B.O. Group) |
| **Headquarters** | Düsseldorf, Germania |
| **Piattaforma Tecnica** | Mirakl V4 Enterprise |
| **Modello** | B2B Marketplace (SaaS) |
| **Vertcale Primario** | HoReCa (Hotels, Restaurants, Catering) + Professional Buyers |
| **Copertura Geografica** | Germania (primario), Austria, Francia, Italia, Polonia, Spagna, Paesi Bassi |
| **URL Principale** | https://www.metro.de (DE), https://www.metro.es (ES), https://www.metro.it (IT) |
| **URL Seller Portal** | https://seller.metro.de, https://seller.metro.es |
| **Requisiti Accesso** | Partita IVA, Business VAT ID, METRO Business Card |
| **Lingue Supportate** | DE, IT, EN, ES, FR, NL, PL |
| **API Versione** | Mirakl API v1.30+ (REST + GraphQL) |
| **Certificazioni** | ISO 27001, ISO 9001, GDPR, eIDAS |
| **Focus Prodotti** | Food & Beverage (60%), Non-Food (25%), Equipment (15%) |

---

## 2. Modello di Business

### 2.1 Architettura del Marketplace

METRO Markets implementa un modello **B2B Marketplace multicanale** basato su Mirakl, dove:

- **METRO AG** funge da Platform Operator e Best Seller simultaneamente
- **Seller Partner** (fornitori) accedono con credenziali business-only
- **Buyer Segment** esclusivo: aziende registrate, strutture HoReCa, professionisti con VAT ID

### 2.2 Segmentazione Buyer

1. **Segment HoReCa Premium**: Ristoranti, Alberghi, Catering
2. **Segment Retail Professionale**: Negozi specializzati, catene di vendita al dettaglio
3. **Segment Foodservice Industriale**: Cucine industriali, mense, catering events
4. **Segment Distribuzione**: Grossisti, wholesaler, cash & carry
5. **Segment Food Producer**: Piccoli/medi produttori alimentari qualificati

### 2.3 Modello di Commissione

| Categoria | Commissione Marketplace | Note |
|-----------|------------------------|-------|
| **Food & Beverage** | 8-12% | Varia per sottocategorie |
| **Non-Food** | 5-10% | Equipment: 7-9% |
| **Fulfillment METRO** | 3-4% | Servizio pick & pack |
| **Logistics Partner** | Variabile | DHL, DPD, GLS integrati |

---

## 3. Integrazione Tecnica

### 3.1 API Principale: Mirakl v1.30+

#### Endpoint Core

```
Base URL: https://api.metro-markets.com/api/
Authentication: OAuth 2.0 + API Key
Rate Limit: 10.000 req/hour (per connessione seller)
Protocol: REST + GraphQL
```

#### Authentication Flow

1. **API Key Generation** (Seller Dashboard → Settings → API Keys)
   - Generat chiavi permanenti con scope limitati
   - Supporta IP Whitelist per sicurezza

2. **OAuth 2.0 Flow** (Enterprise Partners)
   - Authorization Code Grant per connessioni server-to-server
   - Refresh Token: 30 giorni di validità
   - Access Token: 1 ora di validità

### 3.2 Prodotti e Catalogo

#### Creazione Prodotto (POST /products)

```json
{
  "sku": "METRO-SKU-12345",
  "title": "Farina di Tipo 00 - 25kg",
  "description": "Farina italiana certificata per uso professionale",
  "category": "Food & Beverage > Dry Goods > Flour",
  "barcode": "9788445123456",
  "quantity": 150,
  "price": 45.50,
  "vat": 4,
  "currency": "EUR",
  "images": [
    {
      "url": "https://cdn.seller.com/products/farina-001.jpg",
      "position": 1
    }
  ],
  "attributes": {
    "origin": "Italia - Campania",
    "certification": "DOP, BIO",
    "shelf_life": 24,
    "storage_temp": "15-25°C"
  },
  "logistics": {
    "weight_kg": 25.5,
    "dimensions": "50x40x30 cm",
    "shipping_class": "standard"
  }
}
```

#### Sincronizzazione Inventario (PATCH /products/{id}/inventory)

```json
{
  "quantity": 120,
  "availability": "in_stock",
  "last_sync": "2026-04-01T14:30:00Z"
}
```

### 3.3 Ordini e Fulfillment

#### Gestione Ordini

1. **Order Creation Webhook**
   ```
   Event: order.created
   Payload: Order ID, Buyer Info, Items, Shipping Address
   Retry: 3 tentativi con backoff esponenziale
   ```

2. **Order Status Flow**
   ```
   PENDING → ACCEPTED → PREPARING → SHIPPED → DELIVERED
   └─ CANCELLED (in qualsiasi momento se non consegnato)
   └─ RETURNED (dopo delivery)
   ```

3. **Shipping Integration**
   - DHL Paket Connect (DE, AT, IT)
   - DPD eCommerce (FR, ES, NL)
   - GLS Parcel (EU Wide)
   - Pickup Point: METRO Pick & Pack Centers

#### Endpoint Ordini

```
GET    /orders - Lista ordini con filtri
GET    /orders/{id} - Dettagli ordine specifico
POST   /orders/{id}/accept - Accettazione
POST   /orders/{id}/ship - Conferma spedizione + tracking
POST   /orders/{id}/cancel - Cancellazione
GET    /orders/{id}/tracking - Tracking info
```

### 3.4 Pagamenti e Settlement

#### Payment Gateway

- **Provider Primario**: Adyen (PCI-DSS Level 1)
- **Metodi Accettati**: Carta Credito, SEPA Transfer, Bonifico, Fattura
- **Settlement**: Settimanale (lunedì, compensazione venerdì precedente)
- **Holding Period**: 7 giorni post-delivery per chargeback protection

#### Calcolo Settlement

```
Gross Revenue = Somma prezzi netti ordini consegnati
Less: Commissione Marketplace (8-12% per F&B)
Less: Payment Processing (0.8-1.2%)
Less: Chargeback/Refund (se applicabile)
Less: Fatturazione METRO (5-10%)
= Net Settlement
```

#### Endpoint Pagamenti

```
GET    /payments - Lista transazioni
GET    /payments/{id} - Dettagli pagamento
GET    /settlements - Rendiconti settimanali
GET    /settlements/{id}/invoice - Fattura XML/PDF
POST   /payments/{id}/dispute - Disputa chargeback
```

---

## 4. Modello di Dati

### 4.1 Schema Prodotto

```json
{
  "id": "PROD-UUID-12345",
  "seller_id": "SEL-UUID-67890",
  "sku": "METRO-SKU-12345",
  "title": "string (max 500 chars)",
  "description": "string (max 5000 chars)",
  "category": "string (hierarchical path)",
  "subcategories": ["array of strings"],
  "images": [
    {
      "url": "https://cdn.url",
      "position": "integer",
      "alt_text": "string"
    }
  ],
  "price": {
    "amount": "decimal (2 decimals)",
    "currency": "EUR",
    "vat_percent": "integer (4, 10, 19)",
    "discount_percent": "integer (0-100)"
  },
  "inventory": {
    "quantity": "integer",
    "availability_status": "in_stock|low_stock|out_of_stock",
    "reserved": "integer",
    "last_sync": "ISO 8601 timestamp"
  },
  "attributes": {
    "origin": "string",
    "certification": "[string]",
    "shelf_life_days": "integer",
    "storage_temperature": "string"
  },
  "logistics": {
    "weight_kg": "decimal",
    "dimensions_cm": "string (L×W×H)",
    "shipping_class": "standard|express|special",
    "fragile": "boolean",
    "hazmat": "boolean"
  },
  "metadata": {
    "created_at": "ISO 8601",
    "updated_at": "ISO 8601",
    "status": "active|inactive|archived"
  }
}
```

### 4.2 Schema Ordine

```json
{
  "id": "ORD-UUID-99999",
  "order_number": "METRO-2026-001234567",
  "seller_id": "SEL-UUID-67890",
  "buyer_id": "BUY-UUID-55555",
  "status": "pending|accepted|preparing|shipped|delivered|cancelled|returned",
  "created_at": "ISO 8601",
  "items": [
    {
      "product_id": "PROD-UUID-12345",
      "sku": "METRO-SKU-12345",
      "quantity": 10,
      "unit_price": 45.50,
      "vat": 4,
      "subtotal": 455.00
    }
  ],
  "shipping": {
    "address": {
      "company": "Ristorante ABC",
      "street": "Via Roma 123",
      "postal_code": "80123",
      "city": "Napoli",
      "country": "IT"
    },
    "method": "dhl_paket|dpd_ecommerce|gls_parcel|metro_pickup",
    "tracking_number": "string",
    "estimated_delivery": "ISO 8601 date"
  },
  "payment": {
    "method": "credit_card|sepa|invoice|bank_transfer",
    "status": "pending|confirmed|failed",
    "total": 499.50
  },
  "billing_address": {
    "company": "string",
    "street": "string",
    "postal_code": "string",
    "city": "string",
    "country": "string"
  }
}
```

### 4.3 Schema Seller

```json
{
  "id": "SEL-UUID-67890",
  "business_name": "string",
  "business_type": "individual|company|distributor",
  "vat_id": "string (EU format)",
  "business_registration": "string",
  "profile": {
    "logo_url": "https://cdn.url",
    "description": "string",
    "website": "https://seller.com",
    "phone": "string",
    "email": "contact@seller.com"
  },
  "address": {
    "street": "string",
    "postal_code": "string",
    "city": "string",
    "country": "string"
  },
  "banking": {
    "account_holder": "string",
    "iban": "string (encrypted)",
    "swift": "string"
  },
  "metrics": {
    "rating": "decimal (1-5)",
    "feedback_count": "integer",
    "response_time_hours": "decimal",
    "return_rate_percent": "decimal",
    "total_sales": "decimal"
  },
  "status": "pending_verification|verified|suspended|banned",
  "tier": "standard|professional|enterprise",
  "created_at": "ISO 8601",
  "verified_at": "ISO 8601"
}
```

---

## 5. Operazioni Critiche

### 5.1 Verifiche KYC/AML

#### Requisiti Seller Onboarding

1. **Documentazione Obbligatoria**
   - Documento identità (passaporto, ID card)
   - Certificato registrazione azienda (CCIAA, Handelsregister)
   - Estratto conto bancario (ultimi 3 mesi)
   - Dichiarazione IVA (ultimissimo anno fiscale)
   - Certificato antimafia (per Italia)

2. **Verifica Automatica**
   - Controllo VAT ID su VIES database UE
   - Screening su liste OFAC/SDN
   - Score risk assessment (0-100)
   - Verifica IP geolocation per fraud detection

3. **Timeline Approvazione**
   - Standard: 3-5 giorni lavorativi
   - Express: 24 ore (tier enterprise)
   - Richiesta doc aggiuntive: notifica entro 48 ore

### 5.2 Risk Management

#### Soglie di Allerta

```
Flagge Rosse:
- Velocità upload prodotti > 100/ora
- Ordini con discount > 50%
- Seller score < 3.5 stelle
- Multiple payment failures (>3 in 24h)
- IP change ogni 4 ore
- Chargeback rate > 2%

Azioni Automatiche:
- Score 0-20: Sospensione account
- Score 21-40: Limitazione vendite a 1000€/giorno
- Score 41-60: Monitoring intensificato
- Score 61+: Monitoring standard
```

#### Fraud Detection

- Real-time machine learning model (Adyen Risk Engine)
- Behavioral analysis per seller pattern
- Velocità transazioni e geographic anomaly detection
- Manual review trigger per score > 75

### 5.3 Compliance Locale

#### Germania (DE)
- TMG (Telemediengesetz) - Impressum obbligatorio
- UStG (Umsatzsteuergesetz) - VAT standard 19%
- DSGVO - Compliant data handling
- AGB - Termini di servizio conformi

#### Italia (IT)
- Legge sulla Privacy (GDPR + nazionale)
- IVA: 4% alimenti, 10% attrezzature, 22% altri
- Certificato antimafia (se ricavi > 25k€/anno)
- Legge 231/2001 - Compliance penale azienda

#### Francia (FR), Spagna (ES), Benelux (NL/BE)
- VAT rates nazionali applicate
- Local payment processor integrations
- Language + support locale

---

## 6. Rate Limiting e Throttling

### 6.1 API Rate Limits (per API key)

```
Tier Standard:
- 10.000 richieste/ora
- 100.000 richieste/giorno
- Burst: 500 req/minuto

Tier Professional:
- 50.000 richieste/ora
- 500.000 richieste/giorno
- Burst: 2.000 req/minuto

Tier Enterprise:
- Custom limits (SLA negoziate)
- Burst: illimitato
```

### 6.2 Backoff Strategy

```
429 Too Many Requests:
- Retry-After header: secondi attesa consigliati
- Exponential backoff: 1s → 2s → 4s → 8s → stop
- Maximum 3 retries

503 Service Unavailable:
- Circuit breaker: 10 fallimenti → pause 60 sec
- Progressive delay: raddoppia ogni fallimento
```

---

## 7. Sicurezza

### 7.1 Crittografia e Transport

- **TLS 1.3** obbligatorio per tutti gli endpoint
- **HSTS** header (max-age: 31536000)
- **Certificate Pinning** per mobile app
- **API Key Encryption**: chiavi archiviate in HSM (Hardware Security Module)

### 7.2 Authentication & Authorization

- OAuth 2.0 con PKCE per client pubblici
- JWT tokens con firma RS256
- Scope-based access control (RBAC)
- IP whitelist facoltativo (recommended)

### 7.3 Data Protection

- AES-256-GCM per sensitive data at rest
- PCI-DSS Level 1 compliance (payment data)
- Encryption in transit: TLS 1.3
- Secure password storage: bcrypt (cost factor 12)
- API Keys rotation: 90 giorni recommended

### 7.4 Monitoraggio Sicurezza

- Real-time intrusion detection (IDS/IPS)
- WAF (Web Application Firewall) Cloudflare
- Log aggregation: ELK Stack (14 giorni retention)
- SIEM: Splunk per threat analysis
- Incident response: 1 ora response time SLA

---

## 8. Resilienza e SLA

### 8.1 Uptime Guarantee

```
Target: 99.95% uptime/mese
Exclusi:
- Maintenance windows (4 ore/mese, notice 7gg)
- DDoS attacks
- Terze parti (Adyen, DHL, AWS)
- Natural disasters

Compensazione (credit):
- 99.0-99.5%: 5% credito
- 98.5-99.0%: 10% credito
- < 98.5%: 25% credito
```

### 8.2 Disaster Recovery

- **RTO** (Recovery Time Objective): 4 ore
- **RPO** (Recovery Point Objective): 15 minuti
- Multi-region failover (DE → EU-West)
- Database replication: streaming (continuamente sincronizzato)
- Backup: giornaliero + settimanale (30 giorni retention)

### 8.3 Load Balancing

- Geographic load balancing (Route53 + CloudFront)
- Auto-scaling: +20% capacity su HTTP 503
- Cache layer: Redis (1 ora TTL per product catalog)
- CDN: CloudFront (image delivery acceleration)

---

## 9. Monitoring e Observability

### 9.1 Metriche Critiche

```
API Performance:
- P50 latency: < 200ms (target)
- P99 latency: < 1000ms (target)
- Error rate: < 0.5%
- Throughput: req/sec

Business KPIs:
- Ordini creati/ora
- Valore transazioni mediano
- Seller onboarding success rate
- Order completion rate
- Return rate per categoria
```

### 9.2 Alerts e Escalation

```
P1 (Critical) - 15 min response:
- API downtime (500 errors > 50)
- Database connection pool exhausted
- Payment processor unavailable

P2 (High) - 1 hour response:
- API latency P99 > 2000ms
- Error rate > 2%
- Memory usage > 85%

P3 (Medium) - 4 hours response:
- Cache hit ratio < 50%
- Webhook delivery failure > 10%
```

---

## 10. Roadmap Tecnico

### Q2 2026
- GraphQL API v2.0 (deprecate REST)
- Webhook v2 con retry guarantee
- Native mobile app (iOS/Android)

### Q3 2026
- AI-powered product recommendations
- Automated seller risk scoring
- Multi-currency settlement (USD, GBP)

### Q4 2026
- Blockchain integration (supply chain tracking)
- API rate limiting per category
- Seller subscription tiers

---

## 11. Contatti e Support

### Technical Support
- Email: api-support@metro.de
- Slack Community: metro-sellers-dev
- Status Page: status.metro-markets.com
- Documentation: https://docs.metro-markets.com/api

### Integration Partners
- Pre-approved integrators: https://partners.metro-markets.com
- Certification program: metro-certified-partner
- Revenue sharing: 15-25% per referral

---

**Versione 1.0 | Ultimo aggiornamento: 2026-04-01**  
**Documenti correlati: METRO Markets Seller Agreement v3.2, API Integration Guide, Compliance Handbook**