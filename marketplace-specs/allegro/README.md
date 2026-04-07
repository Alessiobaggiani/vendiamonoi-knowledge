# Allegro — Deep-Dive Marketplace Spec per Vendiamonoi.it

## EXPERT-LEVEL TECHNICAL DEEP-DIVE

**Documento**: Specifica completa Allegro.pl per distributori digitali europei
**Target**: CTO, Marketplace Manager, Operations Manager — Vendiamonoi.it
**Versione**: 2026
**Ultimo aggiornamento**: 2026-04-07
**Complemento a**: marketplace-specs/otto, marketplace-specs/amazon, data-models/product-information

---

## INDICE COMPLETO

1. [Overview e Posizionamento Strategico](#1-overview-e-posizionamento-strategico)
2. [Onboarding e Requisiti Seller](#2-onboarding-e-requisiti-seller)
3. [Struttura Commissioni e Costi](#3-struttura-commissioni-e-costi)
4. [Product Listing Requirements](#4-product-listing-requirements)
5. [Allegro Smart! Program](#5-allegro-smart-program)
6. [API e Integrazione Tecnica](#6-api-e-integrazione-tecnica)
7. [Order Management](#7-order-management)
8. [Logistica e Spedizioni](#8-logistica-e-spedizioni)
9. [Resi e Rimborsi](#9-resi-e-rimborsi)
10. [Compliance e Normative Polacche](#10-compliance-e-normative-polacche)
11. [Performance Metrics e KPI](#11-performance-metrics-e-kpi)
12. [Integrazione Channel Manager](#12-integrazione-channel-manager)
13. [Strategia per Vendiamonoi](#13-strategia-per-vendiamonoi)

---

# 1. Overview e Posizionamento Strategico

## 1.1 Cos'è Allegro

Allegro è il più grande marketplace dell'Europa Centrale e Orientale, con sede in Polonia. Fondato nel 1999, domina il mercato polacco dell'e-commerce con una quota di mercato del 30%+ per valore e raggiunge l'80%+ degli acquirenti online polacchi. È il più grande marketplace e-commerce di origine europea.

| Parametro | Valore |
|-----------|--------|
| **GMV 2025** | ~PLN 70 miliardi (~€16.7 miliardi) |
| **Crescita GMV** | +8% YoY (Q1 2025) |
| **Seller attivi** | ~164.000 merchant |
| **Clienti attivi** | 21.1 milioni (PL) + 4.2 milioni (internazionali) |
| **Visite mensili** | 166.91 milioni (dicembre 2025) |
| **Categorie** | 23.056+ |
| **Prodotti** | 413+ milioni |
| **Quota mercato PL** | ~30%+ per valore, 80%+ reach |
| **Contributo PIL** | ~1% del PIL polacco |
| **Quotazione** | Borsa di Varsavia (più grande IPO nella storia della borsa) |

## 1.2 Allegro Group — Struttura Societaria

| Società | Ruolo |
|---------|-------|
| **Allegro.pl** | Marketplace principale (Polonia) |
| **Ceneo.pl** | Piattaforma comparazione prezzi |
| **eBilet** | Vendita biglietti eventi |
| **Allegro.cz** | Marketplace Rep. Ceca |
| **Allegro.sk** | Marketplace Slovacchia |
| **Mall.hu** | Marketplace Ungheria |
| **Mimovrste** | Marketplace Slovenia |
| **Mali.hr** | Marketplace Croazia |
| **Allegro One** | Logistica (One Fulfillment, One Box, One Courier) |
| **Allegro Pay** | Fintech (pagamenti e finanziamento clienti) |

**Espansione internazionale**: I marketplace internazionali hanno raggiunto una crescita GMV del 50-80% YoY nel 2025, con 4.2 milioni di acquirenti attivi.

## 1.3 Mercato Polacco E-Commerce

| Parametro | Valore |
|-----------|--------|
| **Valore mercato 2025** | PLN 150+ miliardi (~€36-40 miliardi) |
| **Proiezione 2027** | PLN 187 miliardi (~€45 miliardi) |
| **Crescita annua** | +9.6% |
| **Quota marketplace** | 50-60% di tutte le vendite online |

## 1.4 Allegro vs Amazon.pl — Confronto Strategico

| Fattore | Allegro | Amazon.pl |
|---------|---------|----------|
| **Quota di mercato** | 30%+ | 1.3% |
| **Utenti mensili** | 20-22 milioni | Molto inferiore |
| **GMV** | ~PLN 70 miliardi | ~10x inferiore |
| **Fulfillment** | Allegro One (FBA-like) | Amazon FBA |
| **Pagamenti** | BLIK, Allegro Pay | Carte, bonifico |
| **Logistica** | InPost Paczkomaty (#1 PL) | Amazon Logistics |
| **Presenza** | Dominante, legacy dal 2000 | In crescita, 3° centro fulfillment 2026 |
| **Target** | Mass market PL | Global, price-sensitive |

**Dato chiave**: Allegro è circa 10x più grande di Amazon in Polonia. Situazione opposta rispetto a quasi tutti gli altri mercati europei.

## 1.5 Categorie con Migliori Performance

| Categoria | Quota Allegro | Note |
|-----------|--------------|------|
| **Home & Garden** | 74% | Forza storica |
| **Elettronica** | 62% | Categoria più grande per volumi |
| **Fashion** | 46% | In crescita |
| **Health & Beauty** | Alta | 2ª categoria più popolare |
| **Automotive** | Forte | Parti, accessori, manutenzione |
| **Supermarket/FMCG** | Emergente | Cibo, pulizia, casa |

## 1.6 Demografia Clienti

- **Area geografica**: Forte bias urbano (Varsavia, Cracovia, Breslavia, Poznań, Danzica)
- **Età**: 18-55 primaria, in crescita 55+
- **Dispositivi**: Mobile-first, alta penetrazione smartphone
- **Pagamento preferito**: BLIK (pagamento istantaneo mobile) dominante
- **Consegna preferita**: InPost Paczkomaty (armadietti automatici), 93% degli utenti internet polacchi li usa
- **Sensibilità prezzo**: Moderata-alta, confrontano prezzi
- **Spedizione gratuita**: Aspettativa forte (soglie PLN 40-80)

---

# 2. Onboarding e Requisiti Seller

## 2.1 Seller Internazionali (Italia)

**Un'azienda italiana può vendere direttamente su Allegro?**

**SÌ** — Allegro accetta seller da tutto lo Spazio Economico Europeo (SEE), inclusa l'Italia.

### Documenti Necessari per Seller EEA

| Documento | Dettaglio |
|-----------|----------|
| Registrazione impresa | Certificato camerale o equivalente |
| Documenti identità | Titolare e rappresentanti legali |
| Registro titolari effettivi | Beneficial ownership register |
| Traduzione inglese | Obbligatoria per tutti i documenti non in inglese |
| Conto bancario | Verifica via bonifico 1 PLN/1 EUR o estratto conto (max 14 giorni) |

## 2.2 Tipi di Account

| Tipo | Per chi | Transazioni |
|------|---------|-------------|
| **Regular Account** | Venditori occasionali | Fino a 10 transazioni/mese, verifica semplificata |
| **Business Account** | Venditori regolari | Illimitate, verifica completa |

## 2.3 Processo di Verifica

| Fase | Durata |
|------|--------|
| Registrazione online | 15-30 minuti |
| Verifica email | 24 ore (link scade poi dati cancellati) |
| KYC e verifica documenti | Alcuni giorni |
| Attivazione account | Notifica via email |
| **Totale stimato** | 3-7 giorni lavorativi |

## 2.4 Prima di Iniziare a Vendere

Dopo l'attivazione, obbligatorio:
- Aggiungere termini resi
- Aggiungere termini reclami
- Configurare impostazioni consegna
- Fornire indirizzo reso **in Polonia** (obbligatorio per Smart!)

---

# 3. Struttura Commissioni e Costi

## 3.1 Riforma Fee Marzo 2025

**Cambio importante**: Dal 3 marzo 2025, Allegro ha semplificato la struttura fee:
- **Eliminati**: listing fee e unit transaction fee
- **Consolidato**: solo commissione di vendita obbligatoria

## 3.2 Commissioni per Categoria

| Categoria | Commissione (netta) |
|-----------|--------------------|
| **Elettronica (laptop, desktop)** | 2.5% |
| **Elettronica (altri)** | 4-8% |
| **Fashion** | 8-12% |
| **Home & Garden** | 8-12% |
| **Health & Beauty** | 10-12% |
| **Supermarket** | 10.5% |
| **Range generale** | 4-15% |

**Nota**: La commissione si calcola sul valore prodotto + spese di spedizione.

## 3.3 Piani Abbonamento

| Piano | Prezzo | Durata | Feature |
|-------|--------|--------|---------|
| **Basic** | 49 PLN (~€12) | 30 giorni | Strumenti seller base |
| **Professional** | 199 PLN (~€47) | 30 giorni | Statistiche avanzate, trial nel welcome package |
| **Expert** | 3.000 PLN (~€715) | 30 giorni | Tutte le feature + logo sui listing, supporto prioritario (Lun-Ven 9-17), hotline dedicata |

## 3.4 Allegro Ads

| Tipo | Costo Minimo |
|------|-------------|
| **CPC (Cost Per Click)** | 0.10 PLN per click |
| **CPM (Cost Per Thousand)** | 9 PLN / 1.000 impression, budget minimo giornaliero 3 PLN |
| **Pubblicità grafica** | Budget minimo giornaliero 15 PLN |
| **Modello** | Asta (prezzi variano per categoria e competizione) |

**Revenue pubblicitaria**: +30% nel 2025, indicando forte domanda da parte dei seller.

## 3.5 Ciclo di Pagamento

| Parametro | Valore |
|-----------|--------|
| **Frequenza payout** | Giornaliera |
| **Timing** | Entro fine giorno lavorativo successivo |
| **Per ordini contrassegno** | 2 giorni lavorativi dopo ritiro pacco |
| **Payout minimo** | PLN 20 |
| **Valuta** | Solo PLN su Allegro.pl |
| **Allegro Pay** | Pagamento immediato dopo vendita, registrato in 1-2 giorni |

## 3.6 Simulazione Costi per Vendiamonoi

**Scenario**: Prodotto Home & Garden, prezzo PLN 200 (~€48)

| Voce | Importo PLN | Importo EUR (~) |
|------|------------|----------------|
| Prezzo vendita (IVA 23% inclusa) | 200.00 | €47.62 |
| IVA polacca (23%) | -37.40 | -€8.90 |
| Commissione Allegro (10% netto) | -20.00 | -€4.76 |
| IVA su commissione (23%) | -4.60 | -€1.10 |
| **Ricavo netto** | **138.00** | **€32.86** |
| Conversione PLN→EUR (~2.5% fee) | — | -€0.82 |
| **Ricavo in EUR** | — | **€32.04** |
| Costo spedizione IT→PL (DHL) | — | -€8-12 |
| **Margine disponibile** | — | **€20-24** |

---

# 4. Product Listing Requirements

## 4.1 Attributi Obbligatori

| Campo | Tipo | Note |
|-------|------|------|
| **GTIN/EAN** | String (8-14 cifre) | Deve essere registrato nel database GS1. Allegro verifica e rimuove EAN non validi. Obbligatorio in sempre più categorie |
| **Brand/Produttore** | String | Obbligatorio |
| **Numero di serie** | String | Dipende dalla categoria |
| **Codice produttore** | String | Codice MPN |
| **Modello** | String | Dipende dalla categoria |
| **Titolo** | String | Max 50 caratteri |
| **Parametri categoria** | Vari | Obbligatori per categoria (dictionary, range, float) |

## 4.2 Requisiti Immagini

| Parametro | Specifica |
|-----------|-----------|
| **Risoluzione massima** | 26 Mpx |
| **Dimensioni** | Lato lungo 400-2560 px (auto-scale sopra 2560) |
| **Aspect ratio** | Qualsiasi (1:1, 4:3, 16:9, etc.) |
| **Formati** | JPG, JPEG, PNG, WEBP (WEBP solo via API) |
| **Profilo colore** | sRGB raccomandato |
| **Sfondo** | Bianco aumenta vendite fino al 12% |
| **Contenuto vietato** | Niente testo, loghi, nomi negozio, descrizioni feature |
| **Quantità max (Business)** | 16 immagini per offerta |
| **Quantità max (Regular)** | 10 immagini per offerta |

## 4.3 Descrizione Prodotto

- Formato rich text con 20 righe
- Ogni riga supporta 1-2 colonne
- Categorizzazione obbligatoria
- Content mapping obbligatorio per offerte prodotto
- **Lingua polacca obbligatoria** per il mercato PL

## 4.4 HTML Limitato Supportato

Allegro usa un editor rich text proprio (non HTML grezzo). Le descrizioni sono strutturate in un formato a righe e colonne specifico di Allegro.

## 4.5 Distinzione Prodotto vs Offerta

| Concetto | Descrizione |
|----------|-------------|
| **Product** | Elemento nel catalogo centrale di Allegro (database prodotti condiviso) |
| **Offer** | Listing specifico del seller (prezzo, quantità, parametri seller-specific) |

I seller creano offerte collegate a prodotti del catalogo. Se il prodotto non esiste nel catalogo, il seller può proporre un nuovo prodotto.

## 4.6 Lingua

- **Polacco obbligatorio** per listing su Allegro.pl
- Il form di listing attualmente NON supporta l'inglese come lingua sorgente
- Traduzione automatica disponibile ma deve essere verificata dal seller
- Allegro sta sviluppando supporto per inglese come lingua sorgente (coming soon)

---

# 5. Allegro Smart! Program

## 5.1 Cos'è Allegro Smart!

Programma di loyalty per acquirenti che offre consegna gratuita illimitata sugli ordini idonei. È **fondamentale** per il successo su Allegro:
- **5x crescita più veloce** rispetto a seller non-Smart!
- **2.5x vendite più alte** sulle offerte Smart! vs altre offerte
- Visibilità preferenziale e posizionamento migliore
- 5+ milioni di clienti polacchi usano Allegro Smart!

## 5.2 Soglie Spedizione Gratuita (per clienti)

| Tipo Consegna | Soglia Minima |
|---------------|---------------|
| **Armadietti/Punti ritiro** | PLN 40 (~€9.50) |
| **Corriere a domicilio** | PLN 80 (~€19) |

## 5.3 Requisiti per Seller

| Requisito | Dettaglio |
|-----------|----------|
| Livello qualità vendita | Superiore a "Neutral" |
| Rating positivo | 98%+ |
| Opzioni consegna (Business) | Minimo 2 doorstep + 3 armadietti/punti ritiro |
| Corrieri qualificanti | Lista specifica Allegro |
| Spedizione puntuale | Rispetto tempi dichiarati |
| Tracking accurato | Informazioni tracking corrette |
| Fatture piattaforma | Pagate puntualmente |
| **Indirizzo reso in Polonia** | **OBBLIGATORIO** |

## 5.4 Punti di Consegna Smart!

- InPost Paczkomaty (49.000+ locations)
- One Punkt by Allegro
- One Box by Allegro
- Żabka stores
- Biedronka stores
- ORLEN stations
- Poczta Polska / Kolporter points

---

# 6. API e Integrazione Tecnica

## 6.1 Architettura API

| Parametro | Valore |
|-----------|--------|
| **Tipo** | RESTful (HTTP methods: GET, POST, PUT, PATCH, DELETE) |
| **Formato** | JSON (primario), XML (legacy) |
| **Specifica** | OpenAPI 3.0 |
| **Production** | `https://api.allegro.pl` |
| **Sandbox** | `https://api.allegro.pl.allegrosandbox.pl` |
| **Auth Server** | `https://allegro.pl` |
| **Documentazione** | https://developer.allegro.pl/documentation |
| **Postman Collection** | Disponibile pubblicamente |

## 6.2 Autenticazione OAuth2

| Parametro | Valore |
|-----------|--------|
| **Token Endpoint** | `https://allegro.pl/auth/oauth/token` |
| **Metodo** | POST (GET deprecato dal 23 agosto 2025) |
| **Flow supportati** | Authorization Code, Client Credentials, Device Flow |
| **Credenziali** | Basic auth con Base64(client_id:client_secret) |
| **Scopes** | Granulari per operazione |

```
# Ottenere access token
POST https://allegro.pl/auth/oauth/token
Authorization: Basic Base64(client_id:client_secret)
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
```

## 6.3 Rate Limiting

| Parametro | Valore |
|-----------|--------|
| **Limite globale** | 9.000 richieste/minuto per client_id (150 req/sec) |
| **Endpoint specifici** | Limiti più restrittivi (es. PUT /sale/offers/{id}: 60 req/min) |
| **Operazioni bulk** | 250.000 modifiche offerta/ora o 9.000/minuto |
| **Errore superamento** | HTTP 429 Too Many Requests |

**Nota**: Rate limit di Allegro (150 req/sec) è **7.5x più generoso** di Otto (20 req/sec).

## 6.4 Interfacce API Principali

| API | Endpoint | Scopo |
|-----|----------|-------|
| **Product Offers** | `/sale/product-offers` | Creazione/aggiornamento offerte (async) |
| **Categories** | `/sale/categories/{id}/parameters` | Parametri per categoria |
| **Orders** | `/order/checkout-forms/{id}` | Dettagli ordine |
| **Order Events** | `/order/events` | Polling eventi ordini |
| **Shipments** | `/shipment-management/shipments` | Gestione spedizioni |
| **Returns** | `/order/` (endpoints post-purchase) | Gestione resi |
| **Billing** | `/billing/billing-entries` | Storico fatturazione |
| **Fee Preview** | `/pricing/offer-fee-preview` | Anteprima commissioni |
| **Images** | `https://upload.allegro.pl/sale/images` | Upload immagini |

## 6.5 Product Offers API — Dettaglio

### Creazione Offerta (Asincrona)

```
POST /sale/product-offers
Body: {
  "productSet": [{"product": {"id": "product-id"}}],
  "sellingMode": {"price": {"amount": "49.99", "currency": "PLN"}},
  "stock": {"available": 100},
  "publication": {"status": "ACTIVE"}
}

# Response: HTTP 201 Created (o 202 Accepted per async)
# Polling stato: GET /sale/product-offers/{offerId}/operations/{operationId}
```

### Eventi Tracciati

- `OFFER_ACTIVATED` — Offerta diventa disponibile
- `OFFER_ENDED` — Offerta non più disponibile
- `OFFER_CHANGED` — Campi modificati
- `OFFER_STOCK_CHANGED` — Stock aggiornato
- `OFFER_PRICE_CHANGED` — Prezzo aggiornato

### Aggiornamento Parziale

```
PATCH /sale/offers/{offerId}
Body: {
  "sellingMode": {"price": {"amount": "39.99", "currency": "PLN"}}
}
# Aggiorna solo il prezzo senza toccare altri campi
```

## 6.6 Orders API — Dettaglio

### Polling Eventi (No Webhook Nativi per Ordini)

```
GET /order/events?from={lastEventId}

# IMPORTANTE: Allegro NON supporta webhook nativi per ordini
# Usare polling su /order/events come pattern principale
```

### Dettagli Ordine

```
GET /order/checkout-forms/{checkoutFormId}
```

### Fatture per Ordine

```
POST /order/checkout-forms/{id}/invoices
# Max 10 fatture per ordine
# Upload: PUT .../invoices/{invoiceId}/file (max 3 MB PDF)
```

## 6.7 Shipments API — Dettaglio

### Corrieri Supportati via API

`INPOST`, `DPD`, `DHL`, `POCZTA_POLSKA`, `UPS`, `GLS`, `RUCH`, `FEDEX`, `TNT_EXPRESS`, `DB_SCHENKER`, `RABEN`, `GEIS`, `DTS`, `PEKAES`, `PATRON`, `X_PRESS_COURIERS`, `RHENUS_LOGISTICS`, `OTHER`

### Creazione Spedizione

```
POST /shipment-management/shipments/create-commands
```

### Tracking

```
GET /order/carriers/{carrierId}/tracking?waybill={waybill}
```

## 6.8 Sandbox

| Parametro | Valore |
|-----------|--------|
| **URL** | `https://api.allegro.pl.allegrosandbox.pl` |
| **Registrazione** | Panel sandbox separato |
| **Feature** | Aggiornato frequentemente come production |
| **Nuove feature** | A volte deployate prima in sandbox |

---

# 7. Order Management

## 7.1 Flusso Ordine

```
Cliente ordina su Allegro
  │
  ▼
Pagamento processato (BLIK, Allegro Pay, carta, bonifico)
  │
  ▼
Ordine visibile al seller via /order/events
  │
  ▼
Seller prepara ordine (pick & pack)
  │
  ▼
Seller spedisce e conferma tracking
  │
  ▼
Consegna (InPost Paczkomaty / corriere)
  │
  ▼
[Opzionale] Reso entro 14 giorni (legge polacca)
```

## 7.2 Gestione Fatture

- Max 10 fatture per ordine
- File PDF, max 3 MB
- Alternativa: link a documenti di fatturazione (max 10 link)
- POST `/order/{orderId}/billing-documents/links`

## 7.3 Rimborsi — Tempistiche

| Tipo | Deadline |
|------|----------|
| Cancellazione | 2 giorni lavorativi |
| Recesso (withdrawal) | 7 giorni da stato consegna (max 14 giorni dal form reso) |
| Reclamo (difetto) | Rimborso immediato richiesto |

---

# 8. Logistica e Spedizioni

## 8.1 Allegro One Fulfillment

| Parametro | Valore |
|-----------|--------|
| **Tipo** | Servizio FBA-like (stoccaggio, packaging, spedizione) |
| **Come funziona** | Seller invia prodotti a magazzino Allegro, Allegro gestisce fulfillment |
| **Costo base** | Per unità, basato su volume (larghezza × lunghezza × altezza) |
| **Transfer fee** | 1.59 PLN netto per unità |
| **Programma Small & Cheap** | 99 PLN netto per ciclo 30 giorni |
| **Requisito CRITICO** | **Partita IVA polacca obbligatoria** (White List VAT payers) |
| **Indirizzo** | Deve corrispondere esattamente a White List |

## 8.2 Corrieri Supportati

### Corrieri Principali Allegro Delivery

| Corriere | Integrazione | Note |
|----------|-------------|------|
| **InPost Paczkomaty** | ✅ Nativa | 25.000+ armadietti, 93% utenti internet PL li usano |
| **DPD** | ✅ Nativa | Integrazione completa Allegro Delivery |
| **DHL** | ✅ Nativa | Integrazione completa |
| **ORLEN Paczka** | ✅ Partner | Programma Allegro Delivery |
| **UPS** | ✅ | Supportato |
| **Poczta Polska** | ✅ | Supportato (anche COD) |

### InPost — Fondamentale per il Mercato Polacco

| Parametro | Valore |
|-----------|--------|
| **Armadietti in Polonia** | 25.000+ |
| **Armadietti globali** | 82.000+ in 9 paesi (inclusa Italia!) |
| **Pacchi annui** | 1+ miliardo (2024) |
| **Utilizzo** | 93% degli utenti internet polacchi |
| **Cambio importante** | Dal 1° marzo 2026: obbligatorio supporto armadietti/punti ritiro |

## 8.3 Cross-Border Shipping Italia → Polonia

| Parametro | Valore |
|-----------|--------|
| **Fattibilità** | ✅ Sì (entrambi UE, no dogana) |
| **Tempi standard** | Fino a 5 giorni lavorativi |
| **Tempi express** | 1-2 giorni lavorativi |
| **Corrieri consigliati** | DHL, DPD, EuroSender |
| **Costi** | Variabili per dimensioni/peso/servizio |

### Opzioni Logistiche per Vendiamonoi

| Opzione | Pro | Contro |
|---------|-----|--------|
| **A: Spedizione da Italia** | Nessun magazzino PL | Tempi lunghi, SLA Smart! difficile, resi costosi |
| **B: Magazzino in Polonia** | SLA rispettata, Smart! ok, resi locali | Costo infrastruttura |
| **C: 3PL polacco** | Scalabile, Smart! ok | Costo per ordine |
| **D: Allegro One Fulfillment** | Massima integrazione | Richiede P.IVA polacca |

## 8.4 Aspettative Consegna Mercato Polacco

- **Next-day delivery**: Standard per aree urbane principali
- **2-3 giorni**: Standard per aree regionali
- **Paczkomaty**: Metodo preferito, aspettativa 1-2 giorni
- **Spedizione gratuita**: Aspettativa forte a soglie PLN 40-80

---

# 9. Resi e Rimborsi

## 9.1 Diritto di Recesso

| Parametro | Valore |
|-----------|--------|
| **Finestra** | 14 giorni dalla consegna (legge polacca) |
| **Condizione** | Senza motivazione |
| **Eccezioni** | Prodotti personalizzati, prodotti sigillati, etc. |
| **Costo reso** | A carico del seller (se non offre metodo reso proprio) |
| **Rimborso** | Entro 14 giorni dalla ricezione del reso |

## 9.2 Allegro Protect (Buyer Protection)

| Parametro | Valore |
|-----------|--------|
| **Rimborso garantito** | Entro 48 ore |
| **Copertura** | 2 anni dalla data di acquisto |
| **Importo massimo** | PLN 20.000 per reclamo |
| **Cause coperte** | Pacco non arrivato, prodotto danneggiato, non conforme |

## 9.3 Dispute Seller

| Fase | Tempistica |
|------|------------|
| Risposta reclamo | 14 giorni (poi buyer può richiedere Allegro Protect) |
| Se Allegro rimborsa buyer | Importo può essere addebitato nella fattura seller |

## 9.4 Indirizzo Reso — Requisito Critico

**L'indirizzo reso deve essere in Polonia** per:
- Partecipare ad Allegro Smart!
- Rispettare diritto di recesso 14 giorni
- Logistica partner
- Limite caratteri: max 35 (via+numero), 50 (nome azienda)

**Opzioni per Vendiamonoi**:
1. Ufficio virtuale in Polonia (€100-200/mese)
2. Partner fulfillment con indirizzo PL (incluso nel servizio)
3. Sussidiaria polacca (€1.500+ setup)

---

# 10. Compliance e Normative Polacche

## 10.1 IVA Polacca — Requisito Critico

**NON esiste soglia di esenzione** per trader non residenti in Polonia. Qualsiasi attività tassabile richiede registrazione IVA.

### Aliquote IVA Polonia 2025

| Aliquota | Applicazione |
|----------|--------------|
| **23%** | Standard (maggior parte dei beni) |
| **8%** | Ridotta (libri, medicinali, cibo) |
| **5%** | Super-ridotta (prodotti selezionati) |
| **0%** | Esportazioni |

### Opzioni per Seller Italiano

| Opzione | Dettaglio |
|---------|----------|
| **A: Registrazione IVA PL** | Registrarsi come contribuente estero + rappresentante fiscale polacco (raccomandato) |
| **B: OSS (One Stop Shop)** | Dichiarare e pagare IVA da Italia — solo se fatturato EU ≤ €10.000 (soglia molto bassa) |

**In pratica**: Vendiamonoi dovrà registrarsi per IVA polacca dato che supererà la soglia €10.000 rapidamente.

## 10.2 EPR — Extended Producer Responsibility

| Parametro | Valore |
|-----------|--------|
| **Deadline CRITICO** | 18 agosto 2025 |
| **Registro** | BDO (Baza Danych Odpadowych) |
| **Obbligatorio per** | Tutti i seller per consegne UE |
| **Enforcement Allegro** | Listing **bloccati automaticamente** senza numero EPR |
| **Sanzioni** | PLN 5.000 - PLN 1.000.000 |
| **Registrazione** | Gratuita |

## 10.3 GPSR — General Product Safety Regulation

| Parametro | Valore |
|-----------|--------|
| **In vigore dal** | 13 dicembre 2024 |
| **Obblighi** | Informazioni produttore complete, info sicurezza, lingua polacca |
| **Strumenti Allegro** | Database catalogo prodotti, template informazioni sicurezza |
| **Penali** | Rimozione listing e penali account |

## 10.4 Protezione Consumatore Polacca

- 14 giorni diritto di recesso (vendite a distanza)
- Informazione chiara pre-acquisto
- Indirizzo reso obbligatorio
- Rimborso entro 14 giorni
- Privacy policy in polacco obbligatoria

## 10.5 RODO (GDPR in Polonia)

- RODO = implementazione polacca del GDPR EU
- Identica al GDPR standard EU
- Raccolta dati lecita, trattamento per scopi dichiarati
- Notifica breach all'autorità polacca se necessario

## 10.6 Checklist Compliance per Vendiamonoi

- [ ] Registrazione IVA polacca + rappresentante fiscale
- [ ] Registrazione EPR nel registro BDO (**URGENTE: deadline 18 agosto 2025**)
- [ ] Dati GPSR per tutti i prodotti
- [ ] Privacy policy in polacco
- [ ] Termini resi in polacco
- [ ] Termini reclami in polacco
- [ ] Indirizzo reso in Polonia
- [ ] Team customer service in polacco
- [ ] Traduzioni prodotti in polacco

---

# 11. Performance Metrics e KPI

## 11.1 Sales Quality Index

| Metrica | Target | Impatto |
|---------|--------|--------|
| **Rating positivo** | 98%+ | Requisito per Smart! |
| **Tempo risposta domande** | 15 minuti | Golden rule — metrica più impattante |
| **Tempo risposta dispute** | 24 ore | Mancanza = escalation automatica |
| **Spedizione puntuale** | 100% | Tracking accurato |
| **Livello qualità** | Superiore a "Neutral" | Minimum per Smart! |

## 11.2 Impatto Performance

Allegro è **estremamente esigente** sulla velocità di risposta:
- 15 minuti è il target per massimo rating
- Ritardi riducono direttamente il Sales Quality Index
- Perdita status Super Seller
- Significative penali di visibilità sulla piattaforma

---

# 12. Integrazione Channel Manager

## 12.1 ChannelEngine

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Integrazione completa, partner ufficiale |
| **Funzionalità** | Sync inventario, listing optimization, automazione ordini, One Fulfillment |
| **Setup** | Selezione prodotti → Categorizzazione → Mapping → Pricing → Attivazione |
| **Guida** | https://support.channelengine.com/hc/en-us/sections/4412492461969-Allegro |

## 12.2 Channable

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Integrazione completa |
| **Funzionalità** | Creazione/editing bulk, sync inventario real-time, regole ottimizzazione |
| **Feature** | Regole if-then per segmentazione, sync centrale cross-canale |

## 12.3 SellRapido

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ⚠️ NON esplicitamente menzionato |
| **Note** | Verificare direttamente con SellRapido |
| **Alternativa** | Usare ChannelEngine, Channable, o BaseLinker |

## 12.4 BaseLinker (Base.com) — Strumento Polacco Leader

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Integrazione nativa completa (strumento polacco) |
| **Funzionalità** | Sync bidirezionale, import ordini, update inventario real-time, generazione fatture/etichette |
| **Vantaggio** | Tool nato per Allegro, massima integrazione |
| **Integrazioni extra** | WooCommerce, Shoper, Mall, etc. per cross-channel |

## 12.5 Altri Integrator Verificati

| Tool | Supporto |
|------|----------|
| **EffectConnect** | ✅ |
| **Koongo** | ✅ |
| **Outvio** | ✅ |
| **Linker Cloud** | ✅ |
| **Omnicado** | ✅ |

## 12.6 Pattern Integrazione per Vendiamonoi

### Opzione A: ChannelEngine (Raccomandato per multi-marketplace)
```
SellRapido → ChannelEngine → Allegro API
            ↕                ↕
        Supabase          Allegro OAuth2
            ↕
       Make.com
```

### Opzione B: BaseLinker (Ottimale per focus Polonia)
```
Feed prodotti → BaseLinker → Allegro API
                    ↕
              Ordini + Tracking
```

### Opzione C: API Diretta (Make.com)
```
Make.com HTTP Module → Allegro API (OAuth2)
  │  Rate limit: 150 req/sec (molto generoso)
  ↕
Supabase → Polling /order/events ogni 5 min
```

---

# 13. Strategia per Vendiamonoi

## 13.1 Go/No-Go Assessment

**VERDETTO: GO** — Allegro rappresenta un'opportunità significativa per Vendiamonoi:
- Mercato enorme (PLN 70B GMV)
- Seller italiano ammesso direttamente (EEA)
- Commissioni competitive (4-15%)
- Payout giornaliero
- API generosa (150 req/sec)
- Brand italiani valorizzati (beauty, fashion, home)

## 13.2 Barriere all'Ingresso

| Barriera | Livello | Soluzione |
|----------|---------|----------|
| Lingua polacca (listing + CS) | 🔴 Alta | Traduttore pro + agenzia CS polacca (€1.000-2.000/mese) |
| IVA polacca | 🔴 Alta | Registrazione + rappresentante fiscale (€300-600) |
| EPR/BDO | 🟡 Media | Registrazione gratuita (URGENTE: deadline 18/08/2025) |
| Indirizzo reso PL | 🟡 Media | Ufficio virtuale (€100-200/mese) o 3PL |
| Valuta PLN | 🟢 Bassa | Wise/Revolut per conversione favorevole |
| GPSR | 🟢 Bassa | Dati già disponibili dai fornitori |

## 13.3 Stima Costi

### Setup (Una Tantum)

| Voce | Costo |
|------|-------|
| Registrazione IVA PL + rappresentante | €300-600 |
| Registrazione EPR/BDO | Gratuita |
| Account Allegro Business | Gratuito |
| Traduzione catalogo iniziale (50 prodotti) | €300-800 |
| Indirizzo reso PL (3 mesi) | €300-600 |
| **TOTALE** | **€900-2.000** |

### Costi Mensili

| Voce | Costo |
|------|-------|
| Customer service polacco (agenzia) | €800-1.500 |
| Traduzione nuovi prodotti | €200-500 |
| Indirizzo reso virtuale | €100-200 |
| Abbonamento Allegro Professional | ~€47 (199 PLN) |
| **TOTALE** | **€1.147-2.247** |

## 13.4 Categorie Strategiche per Vendiamonoi su Allegro

| Categoria | Commissione | Fit IT Brand | Opportunità |
|-----------|------------|-------------|-------------|
| **Beauty & Cosmetics** | 10-12% | ⭐⭐⭐ | Brand italiani premium molto valorizzati, alta domanda |
| **Fashion & Apparel** | 8-12% | ⭐⭐⭐ | Italian fashion = posizionamento premium |
| **Home & Décor** | 8-12% | ⭐⭐⭐ | Design italiano premium, 74% quota Allegro |
| **Health & Supplements** | 10-12% | ⭐⭐ | Prodotti naturali italiani in crescita |
| **Elettronica** | 2.5-8% | ⭐ | Commodity market, margini sottili |

## 13.5 Timeline Ingresso Raccomandato

| Fase | Mese | Azione |
|------|------|--------|
| 1 | 1-2 | Registrazione IVA PL, EPR/BDO, account Allegro Business |
| 2 | 2-3 | Traduzione 50 prodotti, setup logistica, indirizzo reso PL |
| 3 | 3 | Lancio 50-100 listing, test fulfillment |
| 4 | 3-4 | Monitoraggio performance, feedback clienti |
| 5 | 4-6 | Espansione catalogo, target Smart! |
| **Totale** | **3-6 mesi** | Dal via alla presenza profittevole |

## 13.6 Confronto Otto vs Allegro per Vendiamonoi

| Fattore | Otto | Allegro |
|---------|------|--------|
| **Accessibilità seller IT** | Richiede entità DE | Accetta S.R.L. italiana direttamente |
| **Costo ingresso** | €3.200-13.500 | €900-2.000 |
| **Costi mensili** | €2.650-6.300 | €1.147-2.247 |
| **Commissioni** | 7-18% | 4-15% |
| **API rate limit** | 20 req/sec | 150 req/sec |
| **Payout** | Settimanale | Giornaliero |
| **Lingua** | Tedesco | Polacco |
| **Fulfillment** | No FBA | Allegro One (FBA-like) |
| **Smart/Prime** | No | Smart! (5x growth) |
| **Valuta** | EUR | PLN (rischio cambio) |

**Conclusione**: Allegro è **più accessibile e meno costoso** di Otto per un seller italiano, ma richiede gestione valuta PLN e team CS in polacco.

---

## FONTI E RIFERIMENTI

### Documentazione Ufficiale Allegro
- [Allegro Media Center — GMV 2025](https://en.media.allegro.pl/449995-allegros-gmv-nears-70-bln-pln-in-2025-as-it-enters-new-market-segments)
- [Allegro Help Center — Registrazione Seller EEA](https://help.allegro.com/en/sell/a/registration-for-sellers-from-outside-poland-in-the-european-economic-area-O3PwMxBDRs7)
- [Allegro Fees & Pricing](https://help.allegro.com/en/fees/en)
- [Allegro Smart! Program](https://help.allegro.com/en/sell/c/allegro-smart)
- [Allegro Pay Business](https://help.allegro.com/en/sell/a/allegro-pay-business-information-for-sellers-q0aonWwGqUK)
- [Image Requirements](https://help.allegro.com/en/sell/a/rules-for-images-in-the-gallery-and-in-descriptions-8dvWB8Y2PIq)
- [Product Parameters](https://help.allegro.com/en/sell/a/parameters-on-allegro-YWx9qDeO9Ib)
- [GPSR Requirements](https://help.allegro.com/en/sell/a/what-obligations-the-gpsr-imposes-on-you-b25l85Xz6ib)
- [EPR Requirements](https://help.allegro.com/en/sell/a/extended-producer-responsibility-information-for-sellers-m0PoLlleKsM)
- [VAT Distance Selling](https://help.allegro.com/en/sell/a/vat-taxation-of-distance-selling-to-poland-q0awyvmDxsl)
- [Payout Account](https://help.allegro.com/en/sell/c/payout-account)
- [Buyer Protection](https://help.allegro.com/en/sell/a/what-allegro-buyer-protection-is-and-what-to-do-when-my-customer-applies-for-compensation-7G6E75KlPcM)

### API Documentation
- [Developer Portal](https://developer.allegro.pl/documentation)
- [GitHub Allegro API](https://github.com/allegro/allegro-api)
- [REST API Guidelines](https://allegro.github.io/restapi-guideline/)
- [OAuth2 Authentication](https://developer.allegro.pl/tutorials/uwierzytelnianie-i-autoryzacja-zlq9e75GdIR)
- [Offer Management](https://developer.allegro.pl/tutorials/jak-zarzadzac-ofertami-7GzB2L37ase)
- [Ship with Allegro](https://developer.allegro.pl/tutorials/jak-zarzadzac-przesylkami-przez-wysylam-z-allegro-LRVjK7K21sY)
- [Fee Preview](https://developer.allegro.pl/tutorials/jak-sprawdzic-oplaty-nn9DOL5PASX)

### Analisi Mercato
- [Polish E-Commerce 2025-2027](https://www.ecommercebridge.com/polands-e-commerce-boom-23-marketplaces-drive-9-6-market-growth/)
- [Allegro Market Share](https://www.lengow.com/get-to-know-more/top-marketplaces-in-poland/)
- [Amazon vs Allegro](https://www.retail-insight-network.com/analyst-comment/amazon-allegro-polish-ecommerce/)
- [Top Categories](https://www.koongo.com/blog/top-10-best-selling-categories-on-allegro-and-how-to-succeed-in-them/)
- [Allegro Fee Reform 2025](https://aimgroup.com/2025/02/04/allegro-announces-new-fee-structure-for-sellers/)

### Integrazioni
- [ChannelEngine Allegro](https://support.channelengine.com/hc/en-us/sections/4412492461969-Allegro)
- [Channable Allegro](https://www.channable.com/integrations/allegro)
- [BaseLinker Allegro](https://baselinker.com/pl-PL/integracje/allegro/blconnect/)

### Compliance
- [Polish VAT 2025](https://www.numeral.com/blog/poland-vat-rates-and-compliance)
- [InPost Paczkomaty](https://wediditinpoland.eu/en/innowacja/paczkomat-inpost/)
- [RODO/GDPR Poland](https://capitallegal.pl/en/service/gdpr-rodo/)

---

**Documento creato**: 2026-04-07
**Ricerca**: 3 agenti paralleli (seller/commissions, API/technical, logistics/compliance)
**Complemento**: marketplace-specs/otto, marketplace-specs/amazon, marketplace-specs/kaufland
**Prossimo**: marketplace-specs/emag (P2.3)
