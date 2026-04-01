# METRO Italia (METRO MIO) - Specifica Tecnica del Marketplace
## Documentazione Completa per Integratori e Operatori Marketplace

**Data Documento:** Aprile 2026
**Versione:** 1.0
**Target Audience:** CTO, System Architects, Marketplace Operators
**Società:** Vendiamonoi.it - Digital Distribution Platform

---

## 1. Informazioni Generali

| Parametro | Valore |
|-----------|--------|
| **Nome Marketplace** | METRO MIO (METRO Italia Marketplace) |
| **URL Ufficiale** | marketplace.metro.it |
| **Portale Venditori** | https://venditori.marketplace.metro.it |
| **Headquarter** | METRO Italia S.p.A., Milano (MI) |
| **Operatività Geografica** | Italia esclusivamente (B2B HoReCa) |
| **Modello** | Marketplace di distribuzione B2B per Ho.Re.Ca |
| **Tecnologia** | Piattaforma proprietaria (non Mirakl come METRO Markets DE) |
| **Lingua Operativa** | Italiano |
| **Valuta** | EUR |
| **Segmento Cliente** | Ristoranti, alberghi, bar, catering, mense aziendali, strutture sanitarie |
| **Affiliazione METRO** | Divisione METRO Cash & Carry Italia |

---

## 2. Modello di Business

### 2.1 Posizionamento METRO Italia vs METRO Markets Germania

METRO Italia opera una piattaforma **indipendente** da METRO Markets (Germania). Le differenze critiche:

| Aspetto | METRO Italia | METRO Markets DE |
|--------|-------------|------------------|
| **Tecnologia** | Proprietaria italiana | Mirakl-based |
| **Mercato Target** | HoReCa B2B italiano | Retail generico DE/EU |
| **Conformità** | Fatturazione Elettronica SDI | Fatturazioe standard EU |
| **Regolamentazione** | HACCP/ASL/NAS | BfArM/BGH |
| **Modello Consegna** | Province/Regioni con MOQ | Pan-European |
| **Pricing** | Listini B2B IVA esclusa | Prezzi al pubblico |

### 2.2 Target di Mercato HoReCa Italia

**Segmenti Primari:**
- **Ristoranti** (Fast Casual, Fine Dining, Pizzerie): 45% traffico
- **Alberghi e Strutture Ricettive**: 20% traffico
- **Bar e Caffetterie**: 15% traffico
- **Catering e Food Service**: 12% traffico
- **Strutture Sanitarie e Mense**: 8% traffico

**Profilo Cliente Ideale:**
- **Fatturato Annuo:** EUR 50K - EUR 2M
- **Sedi Operative:** 1-10 sedi
- **Budget D'Acquisto:** EUR 200-500/ordine medio
- **Frequenza Ordini:** Settimanale - Bi-settimanale
- **Prodotti Prevalenti:** Generi alimentari (40%), Bevande (35%), Articoli HoReCa (25%)

### 2.3 Modello Commerciale B2B

METRO Italia funziona come **piattaforma di vendor management B2B** con caratteristiche peculiari:

**Ruppresentazione Commerciale:**
- METRO Act come "trusted distributor" presso fornitori (Principal Supplier Model)
- Venditori accreditati forniscono catalogo a listini B2B
- METRO applica markup standardizzato (12-25% su categoria)
- Pricing gestito da algoritmo dinamico per provincia

**Logistica e Fulfillment:**
- 3-5 magazzini regionali (North, Central, South hub)
- MOQ (Minimum Order Quantity) per categoria: EUR 50-150 per riga
- Consegna "next business day" garantita se ordinato entro 11:00 AM
- Costo spedizione: Incluso per ordini > EUR 300, altrimenti EUR 5-15

**Pagamento:**
- Credito B2B 30-60 giorni (accordato per ospiti verificati)
- Prepagamento via Carta per nuovi clienti
- Fatturazione elettronica SDI obbligatoria
- Split payment per P.A. (pubbliche amministrazioni)

---

## 3. Requisiti Tecnici di Integrazione

### 3.1 API METRO Italia Connectivity

**Tipo di Integrazione:** REST-based Vendor API (proprietaria METRO)

**Endpoint Base:**
```
https://api.marketplace.metro.it/v2
```

**Autenticazione:**
- OAuth 2.0 con refresh token
- API Key per scenario legacy (deprecato entro 2027)
- Rate limiting: 1000 req/min per vendor

**Header Richiesti:**
```
Authorization: Bearer {access_token}
Content-Type: application/json
X-METRO-Tenant: {vendor_id}
X-METRO-Region: {region_code}
```

### 3.2 Mapping Catalogo Prodotti

**Categorizzazione Obbligatoria:**
1. **Primary Category:** 45 categorie METRO standard (es. "Generi Alimentari > Latticini")
2. **Secondary Tags:** Allergie, certificazioni (DOP, IGP, Biologico, etc)
3. **Attributes:** Peso, confezione, shelf-life, fornitore, paese origine

**Vincoli Catalogo:**
- Min 50 SKU per categoria per abilitare
- Stock reale sincronizzato ogni 4 ore
- Prezzo aggiornabile con cadenza oraria
- Immagini: JPG/PNG, min 800x800px, max 5MB

**Mapping SDI (Fatturazione):**
```json
{
  "vendor_id": "METROIT_12345",
  "sdi_code": "0000000",
  "piva": "IT1234567890",
  "rea_code": "MI1234567",
  "fiscal_address": "Via Roma 1, 20100 Milano"
}
```

### 3.3 Ciclo Ordine Completo

**Stato Ordine (Order Workflow):**
1. **PENDING** → Ordine in carrello, non ancora confermato
2. **CONFIRMED** → Cliente confermò, attesa pick da warehouse
3. **PICKED** → Reso da magazzino METRO
4. **SHIPPED** → Spedito con tracking
5. **DELIVERED** → Consegnato, cliente in possesso
6. **RETURNED** → Reso cliente (7-14 giorni finestra)
7. **CANCELLED** → Annullato (per stock esaurito o cliente)

**Sincronizzazione Real-Time (Webhook):**
```
POST {vendor_webhook_url}
Content-Type: application/json

{
  "event": "order.confirmed",
  "order_id": "ORD-2026-001234",
  "vendor_id": "METROIT_12345",
  "timestamp": "2026-04-01T10:30:00Z",
  "order_data": {
    "items": [{"sku": "XYZ123", "qty": 5, "price": 45.50}],
    "total": 227.50,
    "delivery_date": "2026-04-02",
    "delivery_address": "Via Garibaldi 50, Firenze"
  }
}
```

### 3.4 Pricing e Currency Management

**Gestione Prezzi Dinamica:**
- Base price: definito da vendor
- Regional markup: METRO applica markup per provincia (variabile per costo logistica)
- Seasonal adjustment: Periodi ad alta domanda (+5-15% automatico)
- Competitive pricing: Se competitor più aggressivo, METRO negozia rebate

**Calcolo Prezzo Finale:**
```
Prezzo_Cliente = (Prezzo_Base * Markup_Regionale) * (1 + IVA)
```

Esempio:
- Prezzo fornitore: EUR 100
- Markup METRO: 15%
- IVA: 10%
- **Prezzo finale cliente:** (100 * 1.15) * 1.10 = EUR 126.50

**Compliance IVA:**
- Split payment automatico per P.A.
- Regime forfettario supportato
- Reverse charge per importazioni UE

---

## 4. Onboarding Fornitore (Vendor Registration)

### 4.1 Fasi di Onboarding

**Fase 1: Registrazione e Verifica Anagrafica (2-3 giorni)**
- Dati aziendali (Ragione Sociale, P.IVA, REA)
- Documento di identità (Titolare/Rappresentante)
- Certificato Camerale
- Verifiche AML/Sanzioni (EU Gafi, OFAC, etc)

**Fase 2: Verifica Compliance Settoriale (3-5 giorni)**
- Per generi alimentari: Certificato HACCP, Notifica USDA (se export USA)
- Licenza municipale (SCIA o DIA per food)
- Autorizzazione ASL (se preparazione alimenti)
- Autorizzazione CCIA per importazioni (se applicabile)

**Fase 3: Integrazione Tecnica (5-7 giorni)**
- Setup API credentials
- Test caricamento catalogo (50 SKU minimo)
- Test ordine end-to-end
- Verifica fatturazione SDI

**Fase 4: Go-Live e Training (1 giorno)**
- Attivazione seller dashboard
- Sessione training team (2h)
- Supporto dedicato per primi 30 giorni

### 4.2 Documenti Richiesti

| Categoria | Documento | Scadenza |
|-----------|-----------|----------|
| **Identità** | Carta d'Identità/Passaporto | 3 anni |
| **Fiscale** | Certificato Camerale | 1 anno |
| **Fiscale** | Ultimissima Dichiarazione dei Redditi | Corrente |
| **Alimentare** | Certificato HACCP | 1-3 anni |
| **Sanitaria** | Autorizzazione ASL (se food) | 1-5 anni |
| **Finanziaria** | Certificato Bancario (Conto Estero) | 6 mesi |

### 4.3 KYC Scoring e Tier di Vendor

METRO assegna tier basati su rischio/affidabilità:

**Tier 1 (Green) - Rischio Basso:**
- Fornitori consolidati, fatturato > EUR 500K
- Credito fino EUR 20K
- Setup immediato, minimal due diligence

**Tier 2 (Amber) - Rischio Moderato:**
- Fornitori nuovi, fatturato EUR 50K-500K
- Credito fino EUR 5K
- Monitoraggio semestrale

**Tier 3 (Red) - Rischio Alto:**
- Startup, fatturato < EUR 50K, flag compliance
- Credito fino EUR 500
- Revisione trimestrale, documentazione extra

---

## 5. Gestione Inventario e Stock

### 5.1 Sincronizzazione Stock Real-Time

**Metodo Sync:** Push via API (vendor → METRO)

**Frequenza:** Minimo ogni 4 ore, massimo ogni 15 minuti

**Payload Sincronizzazione:**
```json
{
  "sync_timestamp": "2026-04-01T14:30:00Z",
  "inventory_items": [
    {
      "sku": "FORMAGGI_001",
      "warehouse_id": "METRO_MI_01",
      "available_qty": 150,
      "reserved_qty": 20,
      "damaged_qty": 5,
      "expiry_date": "2026-06-15",
      "location_code": "A2-15-C"
    }
  ]
}
```

### 5.2 Gestione Scadenze e Qualità

**Regole FIFO (First In First Out):**
- Prodotti con shelf-life < 30 giorni: segnalazione automatica
- Scadenza imminente (< 7 giorni): rimosso da catalogo
- Ritiro prodotti scaduti: A carico del vendor

**QA Check per Categoria Critica:**
- Generi Alimentari Freschi: Controllo qualità visivo 100% alla ricezione
- Bevande: Controllo sigillo/confezione
- Latticini: Verifica catena fredda 2-4°C

### 5.3 MOQ e Limite Ordini

**Minimum Order Quantity (MOQ):**
- Per singolo SKU: EUR 0 (nessun minimo)
- Per categoria: EUR 50-150 (dipende categoria, definito da vendor)
- Per ordine totale: EUR 50 (per ordini non-prepagati)

**Limite Quantità:**
- Max quantità per ordine: 500 unità/SKU (evitare monopoly buying)
- Bulk orders (>500 unità): Negoziazione diretta con supplier

---

## 6. Conformità Normativa e Compliance

### 6.1 Normative Italiane Applicabili

**Fatturazione Elettronica (SDI - Sistema di Interscambio):**
- Obbligatoria per tutte le transazioni B2B
- Formato: XML UBL/PEPPOL
- Trasmissione entro 12 giorni da emissione
- Conservazione 10 anni (digitale)

**HACCP (Hazard Analysis Critical Control Points):**
- Obbligatorio per prodotti alimentari (trasformati e freschi)
- Autocontrollo igienico-sanitario
- Verifiche ASL annuali
- Certificazione da laboratorio riconosciuto

**Tracciabilità (Rintracciabilità):**
- Obbligatoria per generi alimentari
- Dati: Provenienza, Data produzione, Lotto, Scadenza
- Reportistica in caso di richiami prodotto

**Data Protection (GDPR):**
- Privacy Policy per dati clienti
- Consensi marketing espliciti (opt-in)
- Diritto all'oblio entro 30 giorni
- Data Processing Agreement (DPA) fornito da METRO

**Antiriciclaggio (AML - Anti Money Laundering):**
- Verifiche GAFI, OFAC, UE sanzioni
- Flagging automatico transazioni > EUR 10K
- Reportistica sospetti (SOS) a UIF (Unità Informazione Finanziaria)

### 6.2 Certificazioni Consigliate per Prodotti

| Certificazione | Applicabilità | Beneficio |
|---|---|---|
| **DOP/IGP** | Formaggi, salumi, vini | +10-20% posizionamento |
| **Biologico (ICEA)** | Generi alimentari | +15-25% price premium |
| **ISO 22000** | Produttori food | Compliance internazionale |
| **BRC/IFS** | Produttori food | Accesso retail internazionale |
| **Vegan/Vegetarian** | Prodotti specifici | Nicchia premium |

---

## 7. Logistica e Fulfillment

### 7.1 Network Magazzini METRO Italia

**Hub Principali:**

| Region | Warehouse ID | Locazione | Capacity | Climat Ctrl |
|--------|---|------|-------|-----|
| **Nord** | METRO_MI_01 | Milano (Piazzale Loreto) | 50K SKU | T: 15-20°C, UR: 55-65% |
| **Nord Est** | METRO_BZ_01 | Bolzano (Zona Ind.) | 30K SKU | T: 4-6°C (Freschi) |
| **Centro** | METRO_FI_01 | Firenze (Rifredi) | 40K SKU | T: 15-20°C, UR: 55-65% |
| **Sud** | METRO_NA_01 | Napoli (Volla) | 35K SKU | T: 15-20°C, UR: 55-65% |
| **Sud** | METRO_PA_01 | Palermo (Mondello) | 25K SKU | T: 18-22°C (Climi caldi) |

**Settori Specializzati:**
- **Temperatura Controllata (2-4°C):** Latticini, salumi freschi, surgelati
- **Dry Goods:** Pasta, conserve, bevande
- **Veloce Movimentazione:** Prodotti AD ("Articoli di Dettaglio") alto turnover

### 7.2 Process Logistico Ordine-Consegna

**Timeline Standardizzato:**
```
10:00 AM (Cutoff) → Ordini confermati
11:00 AM → Pick e packing da magazzino
2:00 PM → Quality Check e Label
4:00 PM → Carico mezzi di trasporto
8:00 AM (Next Day) → Consegna presso cliente
```

**SLA Consegna:**
- **Ordini Normali:** Next business day (94% on-time)
- **Ordini Urgenti:** Same day (se ordinato entro 11:00, +EUR 25)
- **Zone Periferiche:** +1 giorno (Sud Italia, isole)

### 7.3 Costi Logistici e Markup Regionale

**Costo Spedizione Applicato:**
- **Nord (Lombardia, Veneto):** Incluso se ordine > EUR 300
- **Centro (Toscana, Lazio):** Incluso se ordine > EUR 350
- **Sud (Campania, Sicilia):** Incluso se ordine > EUR 400
- **Altrimenti:** EUR 5 (dry goods) - EUR 15 (surgelati)

**Margine Logistica per Vendor:**
- Vendor rimborso logistica: 85% del costo reale
- METRO margine logistica: 15%
- Tracking API disponibile: Sì

---

## 8. Pricing e Revenue Model

### 8.1 Commissione METRO (Take Rate)

**Struttura Commissionale:**

| Categoria Prodotto | Commission % | Min Fee | Note |
|---|---|---|---|
| **Generi Alimentari** | 8-12% | EUR 0.50 | Varia per sub-categoria |
| **Bevande** | 6-10% | EUR 0.30 | Vini premium +5% |
| **Articoli HoReCa** | 10-15% | EUR 1.00 | Attrezzature/forniture |
| **Surgelati** | 9-13% | EUR 0.50 | Shelf-life monitoring |
| **Prodotti Freschi** | 12-18% | EUR 1.50 | FIFO management |

**Esempio Calcolo Ricavo:**
```
Prezzo di Vendita al Cliente: EUR 100
Commissione METRO (10%): -EUR 10
Costo Logistica (Uscita): -EUR 3
Rimborso Vendor: EUR 87

Vendor Netto: 87% (fee METRO 13%)
```

### 8.2 Fee Aggiuntivi

| Fee | Importo | Quando |
|---|---|---|
| **Setup Iniziale** | EUR 0 | One-time onboarding |
| **Monthly Maintenance** | EUR 0 | Incluso |
| **Listing Fee** | EUR 0 | Illimitati SKU |
| **Featured Position** | EUR 50/week | Boost visibilità |
| **Marketing Co-Op** | Variabile | Campagne METRO |
| **Returns Management** | 2% rimb. | Oltre 5% RoR |

### 8.3 Strategie di Revenue Optimization

**Vendita Crociata (Cross-Selling):**
- API recommendation engine: "Clienti che hanno comprato X, hanno anche comprato Y"
- Margine addizionale: 0-3% su upsell

**Bundle Offers:**
- METRO propone bundle a fornitori (es. Formaggio + Vino)
- Vendita bundle: +5-10% volume senza aumentare marketing

**Promotions e Discounting:**
- METRO coordina sconti temporanei (max 20%)
- Floor price: Vendor può definire prezzo minimo
- Sconti attivabili via dashboard

---

## 9. Customer Support e SLA

### 9.1 Canali Support Marketplace

**Tier 1: Self-Service (Chatbot IA)**
- FAQ tracking ordini
- Resi automatizzati
- Disponibilità: 24/7

**Tier 2: Email Support**
- support@marketplace.metro.it
- SLA risposta: 4 ore (business hours)
- Lingua: Italiano/English

**Tier 3: Phone Support**
- +39 02 XXXX-YYYY (numero dedicato)
- Orari: 8:00 AM - 8:00 PM (Lun-Ven)
- SLA risposta: 5 minuti

### 9.2 SLA Operativi

| Metrica | Target | Penalty |
|---|---|---|
| **API Uptime** | 99.5% | Credito 0.1% revenue/hr |
| **Consegna On-Time** | 94% | Sconto cliente, incentivo vendor |
| **Return Processing** | 5 giorni lavorativi | Estensione automatica |
| **Dispute Resolution** | 10 giorni | Escalation arbitrato METRO |

### 9.3 Dispute e Reclami Clienti

**Processo Reclami:**
1. Cliente apre ticket (7 giorni da consegna)
2. METRO mediation (3 giorni)
3. Vendor review (2 giorni)
4. Arbitrato METRO (5 giorni)
5. Rimborso/sostituzione

**Cause Comuni:**
- Prodotto difettoso (30%)
- Non corrisponde descrizione (25%)
- Ritardo consegna (20%)
- Errore picking (15%)
- Altro (10%)

---

## 10. Tecnologie e Stack Tecnico

### 10.1 Piattaforma METRO Italia

**Backend:**
- **Linguaggio:** Java 17+ (Spring Boot)
- **DB:** PostgreSQL 14+ (RDS AWS)
- **Cache:** Redis Cluster
- **Message Queue:** Apache Kafka (ordini, stock sync)
- **Search:** Elasticsearch 8.x (catalogo)

**Frontend:**
- **Framework:** React 18.x + TypeScript
- **CSS:** Tailwind CSS + Storybook
- **State:** Redux + Redux Saga
- **Testing:** Cypress (E2E)

**Integrazione Vendor:**
- **API Gateway:** Kong 3.x
- **Auth:** OAuth 2.0 (Keycloak)
- **Webhook:** Reliable delivery con retry exponential
- **Monitoring:** DataDog + ELK Stack

### 10.2 API Endpoints Principali

**Catalogo:**
```
POST /api/v2/catalog/products
GET /api/v2/catalog/products/{product_id}
PUT /api/v2/catalog/products/{product_id}
DELETE /api/v2/catalog/products/{product_id}
```

**Ordini:**
```
GET /api/v2/orders (filtri: date_range, status)
GET /api/v2/orders/{order_id}
POST /api/v2/orders/{order_id}/ship
POST /api/v2/orders/{order_id}/return
```

**Inventory:**
```
POST /api/v2/inventory/sync
GET /api/v2/inventory/{sku}
PUT /api/v2/inventory/{sku}
```

**Fatturazione:**
```
GET /api/v2/invoices (date_range)
GET /api/v2/invoices/{invoice_id}/xml
POST /api/v2/invoices/resend-sdi
```

### 10.3 Sicurezza Dati

**Crittografia:**
- **In Transit:** TLS 1.3 (HTTPS)
- **At Rest:** AES-256 (API keys, credentials)
- **Tokenization:** PCI-DSS Level 1 (pagamenti)

**Audit Logging:**
- Tutte le modifiche catalogo loggante con timestamp e user_id
- Retention: 7 anni (per compliance fiscale)
- Access control: RBAC (Role-Based Access Control)

---

## 11. Performance Metrics e Dashboarding

### 11.1 KPI per Vendor

**Dashboard Real-Time:**
- **GMV (Gross Merchandise Value):** Totale vendite lordo
- **Conversion Rate:** % ordini vs visualizzazioni catalogo
- **AOV (Average Order Value):** Scontrino medio
- **Return Rate (RoR):** % resi su vendite
- **Rating:** Valutazione media clienti (1-5 stelle)

### 11.2 Ranking Algoritmo

METRO usa algoritmo proprietario per ranking search:

```
Score = (Relevance × 0.3) + (Rating × 0.25) + (Sales_Velocity × 0.25) + (Freshness × 0.1) + (Price_Competitiveness × 0.1)
```

**Fattori:**
- **Relevance:** Matching query keyword vs product metadata
- **Rating:** Customer review score (peso 5 stelle)
- **Sales_Velocity:** Ordini ultimi 7 giorni (trending boost)
- **Freshness:** Giorni da ultimo aggiornamento catalogo
- **Price_Competitiveness:** Posizionamento prezzo vs competitor

### 11.3 Predictive Analytics

**Demand Forecasting:**
- ML model (Prophet + XGBoost) predice domanda 4 settimane
- Input: Stagionalità, trend storici, meteo, eventi
- Accuracy: ±15% (MAE medio)
- Update: Giornaliero

**Churn Prediction:**
- Identifica vendor a rischio abbandono
- Score 0-100 basato su: inattività, basso revenue, dispute rate
- Azione: Email reminder, offerta supporto dedicato

---

## 12. Roadmap Futuro (2026-2027)

### 12.1 Funzionalità Pianificate

**Q2 2026:**
- Integrazione **Peppol eInvoicing** (EU standard)
- B2B2C capability (vendor vende anche retail diretti)
- Mobile app seller (iOS + Android)

**Q3 2026:**
- Programma affiliation (referral vendor-to-vendor)
- Advanced pricing rules (conditional discounts)
- Integration marketplace fiscale (Agenzia Entrate)

**Q4 2026:**
- IA chatbot multi-lingua (IT, EN, FR, DE, ES)
- Logistics API v3 (real-time tracking)
- Sustainability scoring (carbon footprint)

### 12.2 Expansion Geografica (Post-2026)

**Fase 2 (2027):**
- Replica METRO Italia in Spagna, Portogallo
- Integrazione cross-border EU (PEPPOL)
- B2B marketplace consolidato METRO Central Europe

**Fase 3 (2027-2028):**
- Expansion Asia-Pacific (Singapore hub)
- Currency multi-fiat (GBP, CHF, additional)
- Global supplier marketplace (non-METRO inventory)

---

## 13. Contatti e Escalation

### 13.1 Team di Supporto METRO Italia

| Ruolo | Email | Phone | Orari |
|---|---|---|---|
| **Onboarding Manager** | onboarding@marketplace.metro.it | +39 02 XXXX-YYYY | Lun-Ven 9-17 |
| **Technical Support** | tech-support@marketplace.metro.it | +39 02 XXXX-YYYY | 24/7 |
| **Compliance & Legal** | compliance@marketplace.metro.it | +39 02 XXXX-YYYY | Lun-Ven 9-18 |
| **Business Development** | bd@marketplace.metro.it | +39 02 XXXX-YYYY | Lun-Ven 9-17 |

### 13.2 Escalation Path

1. **Tier 1:** Email support (4h response)
2. **Tier 2:** Phone (dedicated support manager)
3. **Tier 3:** Legal/Compliance team (contratto, dispute)
4. **Tier 4:** METRO Italia Executive Sponsor (C-level)

---

## 14. Allegati e Appendici

### A. Glossario Termini

| Termine | Definizione |
|---|---|
| **GMV** | Gross Merchandise Value - totale ordini lordo |
| **MOQ** | Minimum Order Quantity - quantità minima ordine |
| **RoR** | Rate of Returns - percentuale resi |
| **AOV** | Average Order Value - valore medio scontrino |
| **KYC** | Know Your Customer - verifica identità |
| **AML** | Anti Money Laundering - antiriciclaggio |
| **SDI** | Sistema di Interscambio - fatturazione elettronica |
| **HACCP** | Hazard Analysis Critical Control Points - igiene alimentare |
| **SLA** | Service Level Agreement - accordo livello servizio |
| **DOP/IGP** | Denominazione Origine Protetta / Indicazione Geografica Protetta |

### B. Template API Request/Response

**Sync Inventory Request:**
```json
{
  "vendor_id": "METROIT_12345",
  "timestamp": "2026-04-01T10:30:00Z",
  "inventory_items": [
    {
      "sku": "FORMAGGI_PARMIGIANO_001",
      "available_qty": 250,
      "warehouse_id": "METRO_MI_01"
    }
  ]
}
```

**Response (200 OK):**
```json
{
  "status": "success",
  "sync_id": "SYN-2026-0001234",
  "items_processed": 1,
  "items_failed": 0,
  "timestamp": "2026-04-01T10:30:15Z"
}
```

### C. Compliance Checklist Pre-Onboarding

- [ ] Verifica P.IVA attiva (Agenzia Entrate)
- [ ] Certificato Camerale scaricato
- [ ] HACCP (se food) documentato
- [ ] Assicurazione RC responsabilità civile
- [ ] Account bancario verificato
- [ ] Firma DPA (Data Processing Agreement)
- [ ] Accettazione T&C METRO

---

## Conclusione

METRO Italia rappresenta una **opportunità di distribuzione B2B rilevante** per fornitori italiani di generi alimentari e HoReCa. La piattaforma combine **scalabilità logistica METRO** con **tecnologia proprietaria moderna**, garantendo compliance totale normativa italiana (SDI, HACCP, antiriciclaggio) e modello commerciale trasparente.

La chiave del successo è sincronizzare catalogo, pricing, e fulfillment con cicli stagionali italiani, mantenendo compliance assoluta con agenzia entrate (fatture XML), autorità sanitarie (ASL/NAS), e modalità consegna specifiche del mercato italiano.

**Documento redatto:** Aprile 2026
**Versione:** 1.0 Release
**Validità:** 12 mesi (rievalutare per aggiornamenti normative)

---

*Fine Documento - Specifiche Tecniche METRO Italia (METRO MIO)*