# Conrad — Deep-Dive Marketplace Spec per Vendiamonoi.it

## EXPERT-LEVEL TECHNICAL DEEP-DIVE

**Documento**: Specifica completa Conrad Marketplace B2B per distributori digitali europei
**Target**: CTO, Marketplace Manager, Operations Manager — Vendiamonoi.it
**Versione**: 2026
**Ultimo aggiornamento**: 2026-04-07
**Complemento a**: marketplace-specs/otto, marketplace-specs/allegro, marketplace-specs/emag

---

## INDICE COMPLETO

1. [Overview e Posizionamento Strategico](#1-overview-e-posizionamento-strategico)
2. [Conrad Marketplace vs Conrad Retail](#2-conrad-marketplace-vs-conrad-retail)
3. [Focus B2B — Implicazioni per Seller](#3-focus-b2b--implicazioni-per-seller)
4. [Onboarding e Requisiti Seller](#4-onboarding-e-requisiti-seller)
5. [Struttura Commissioni e Costi](#5-struttura-commissioni-e-costi)
6. [Ciclo Pagamenti e Payout](#6-ciclo-pagamenti-e-payout)
7. [Product Listing Requirements](#7-product-listing-requirements)
8. [API e Integrazione Tecnica (Mirakl)](#8-api-e-integrazione-tecnica-mirakl)
9. [Order Management](#9-order-management)
10. [Logistica e Spedizioni](#10-logistica-e-spedizioni)
11. [Resi e Rimborsi](#11-resi-e-rimborsi)
12. [Compliance e Normative](#12-compliance-e-normative)
13. [Performance Metrics e KPI](#13-performance-metrics-e-kpi)
14. [Integrazione Channel Manager](#14-integrazione-channel-manager)
15. [Copertura Geografica](#15-copertura-geografica)
16. [Strategia per Vendiamonoi](#16-strategia-per-vendiamonoi)

---

# 1. Overview e Posizionamento Strategico

## 1.1 Cos'è Conrad

Conrad Electronic è uno dei principali distributori B2B europei per elettronica e forniture tecniche, fondato nel 1923 a Berlino. Dal 2017 opera anche come marketplace B2B (3P) costruito su piattaforma Mirakl. A differenza della maggior parte dei marketplace generalisti, Conrad è **primariamente B2B** — i clienti sono professionisti degli acquisti, dipartimenti R&D, istituti educativi e industrie manifatturiere.

| Parametro | Valore |
|-----------|--------|
| **Fondazione** | 1923 (Berlino) |
| **Sede** | Hirschau, Baviera, Germania |
| **Proprietà** | Famiglia Conrad (Conrad Holding SE, 3° generazione) |
| **Revenue annuo** | ~€1.1 miliardi |
| **Split revenue** | 70% B2B, 30% B2C (2022) |
| **Visite mensili** | 8+ milioni |
| **Clienti B2B** | 2.3 milioni worldwide, 2M+ solo Germania |
| **Dipendenti** | 2.300+ in 32 sedi globali |
| **Paesi attivi** | 17 paesi europei (retail), 6+ marketplace |
| **Piattaforma marketplace** | Mirakl (dal 2017) |
| **Seller marketplace** | 800-1.000 (curated, selezionati) |
| **Prodotti marketplace** | 10+ milioni di offerte |
| **Prodotti Conrad retail** | ~850.000 propri |
| **Focus** | Elettronica, IT, automazione, IoT, strumenti, componenti |

## 1.2 Posizionamento Unico

Conrad è **fondamentalmente diverso** da marketplace consumer come Amazon, eBay, Allegro:

| Aspetto | Conrad B2B | Marketplace Consumer |
|---------|-----------|---------------------|
| **Clienti** | Procurement manager, R&D, industria | Consumatori finali |
| **Ordini** | Alto valore, bassa frequenza | Basso valore, alta frequenza |
| **Pricing** | Netto, graduated, volume-based | Retail con IVA |
| **Pagamento** | Pay-on-due-date, termini netti | Pagamento immediato |
| **Resi** | Solo difettosi (B2B) | 14-30 giorni senza motivazione |
| **Fatturazione** | E-Procurement, EDI, PunchOut | Standard e-commerce |
| **Catalogo** | Curated, quality-checked | Aperto a tutti |

## 1.3 Timeline Storica

| Anno | Evento |
|------|--------|
| 1923 | Fondazione a Berlino (Max Conrad) |
| 1946 | Trasferimento a Hirschau, Baviera |
| 2017 | **Lancio marketplace B2B** (partnership Mirakl) |
| 2021 | Espansione marketplace Austria |
| 2022 | Espansione marketplace Paesi Bassi |
| 2023 | Espansione marketplace Francia e **Italia** |
| 2026 | 10M+ prodotti, 1.000+ seller, 6+ paesi marketplace |

## 1.4 Crescita Marketplace

- Conrad ha impiegato **100 anni** per offrire 800.000 prodotti propri
- Con il marketplace ha raggiunto **7+ milioni** di prodotti in soli **5 anni**
- Oggi: **10+ milioni** di offerte combinate (1P + 3P)

---

# 2. Conrad Marketplace vs Conrad Retail

## 2.1 Modello 1P (Retail Diretto)

| Parametro | Valore |
|-----------|--------|
| **Prodotti** | ~850.000 a inventario Conrad |
| **Fulfillment** | Magazzini Conrad, 50.000 pacchi/giorno |
| **Target** | B2B e B2C |
| **Paesi** | 17 paesi europei |

## 2.2 Modello 3P (Marketplace)

| Parametro | Valore |
|-----------|--------|
| **Lancio** | Luglio 2017 |
| **Piattaforma** | Mirakl (9 mesi da planning a go-live) |
| **Seller** | 800-1.000 (curated, quality-checked) |
| **Prodotti** | 10+ milioni di offerte |
| **Target** | Solo B2B |
| **Paesi marketplace** | 6+ (DE, AT, NL, FR, IT, BE) |

## 2.3 Esperienza “One-Stop Shopping”

L'integrazione stock Conrad + marketplace crea un'esperienza unica per clienti B2B:
- Confronto offerte tra prodotti Conrad e seller terzi
- Un unico checkout per tutto
- Procurement integrato (ERP, PunchOut, EDI)
- 750.000+ prodotti con pricing customer-specific

---

# 3. Focus B2B — Implicazioni per Seller

## 3.1 Tipologia Clienti

| Segmento | Descrizione |
|----------|------------|
| **R&D Engineers** | Componenti, dev kit, prototyping |
| **Design Engineers** | Materiali prototipazione |
| **Istituti Educativi** | Kit didattici, componenti |
| **Industria Elettronica** | Bulk componenti, equipment |
| **Maintenance & Repair** | Ricambi, strumenti, diagnostica |
| **Manufacturing** | Automazione, controllo, industriale |
| **IT Departments** | Networking, computing |
| **Costruzioni/Installazioni** | Smart home, automazione, utensili |
| **Sanità** | Elettronica specializzata, misura |

## 3.2 Procurement B2B Features

| Feature | Descrizione |
|---------|------------|
| **ERP Integration** | Connessione diretta con sistemi ERP clienti |
| **OCI/PunchOut** | Integrazione cataloghi procurement |
| **EDI** | Scambio documenti automatizzato |
| **eCatalog** | Deployment cataloghi dedicati |
| **SFTP/HTTPS** | Trasferimento dati sicuro |
| **Conrad Smart Procure** | Tool procurement browser-based |
| **Volume Pricing** | Sconti graduali per quantità |
| **Pay-on-due-date** | Pagamento a scadenza (non immediato) |
| **White-label invoicing** | Fatture a nome del seller |

## 3.3 Impatto sul Business Model del Seller

- **Valore ordine più alto** ma frequenza più bassa
- **Ciclo acquisizione cliente più lungo** (procurement ha processi approvazione)
- **Supporto tecnico necessario** (clienti B2B chiedono specifiche tecniche)
- **Compliance fatturazione** obbligatoria (E-Procurement, termini netti)
- **Integrazione ERP** richiesta per clienti grandi

---

# 4. Onboarding e Requisiti Seller

## 4.1 Seller Internazionali (Italia)

**Un'azienda italiana può vendere su Conrad?**

**SÌ** — Conrad ha lanciato il marketplace in Italia nel 2023. Un'azienda italiana S.R.L. può vendere su Conrad.it.

### Requisiti per Conrad Italia

| Requisito | Dettaglio |
|-----------|----------|
| **Entità legale** | S.R.L. italiana riconosciuta |
| **Partita IVA** | Italiana obbligatoria |
| **Logistica/Magazzino** | Capacità logistica in Italia |
| **Spedizione** | Singoli ordini (no solo bulk) |
| **Qualità** | Quality check Conrad OPPURE certificazione ISO |
| **E-commerce experience** | Richiesta |

### Requisiti per Conrad Germania (per espansione)

| Requisito | Dettaglio |
|-----------|----------|
| **Entità legale** | GmbH minimo (entità tedesca) |
| **P.IVA tedesca** | Steuernummer obbligatorio |
| **Magazzino** | In Germania obbligatorio |
| **Spedizione** | Singoli ordini individuali |

## 4.2 Modello Onboarding: Curated (NON Open)

**Conrad NON è un marketplace aperto.** Il modello è curated:
- Non tutti gli applicanti vengono accettati
- Ogni seller viene verificato personalmente dal team Business Development
- Solo fornitori che superano quality check OPPURE sono ISO-certified vengono accettati
- ~800-1.000 seller totali (vs 100.000+ su Amazon)

## 4.3 Documenti Necessari

| Documento | Dettaglio |
|-----------|----------|
| **Registrazione impresa** | Certificato camerale / Registro Imprese |
| **Partita IVA** | Valida per il paese target |
| **Documento identità** | Rappresentante legale |
| **Estratto conto bancario** | Ultimi 2-3 mesi |
| **Coordinate bancarie** | IBAN/BIC per payout EUR |
| **Catalogo prodotti** | Con specifiche tecniche |

## 4.4 Processo di Verifica

| Fase | Durata |
|------|--------|
| 1. Contatto via form seller | Minuti |
| 2. Assessment team Business Development | Pochi giorni |
| 3. Briefing call con BD representative | 30-60 minuti |
| 4. Completamento vetting seller | 1-5 giorni |
| 5. Attivazione account | 24 ore |
| 6. Creazione master data e documentazione | 1-3 giorni |
| 7. Approvazione Webhelp (payment processor) | 1-3 giorni |
| 8. Shop online | Immediato dopo approvazione |
| **Totale** | **1-3 settimane** |

## 4.5 Contatti per Registrazione

- **URL registrazione**: [platform.conrad.de/en/seller/become-a-seller.html](https://platform.conrad.de/en/seller/become-a-seller.html)
- **Seller FAQ**: [platform.conrad.de/en/seller/seller-faq.html](https://platform.conrad.de/en/seller/seller-faq.html)
- **Supporto tecnico**: tom.mp@conrad.de

---

# 5. Struttura Commissioni e Costi

## 5.1 Fee Fisse

| Voce | Costo |
|------|-------|
| **Abbonamento mensile** | €99/mese (IVA esclusa) |
| **Costo annuo** | €1.188/anno |
| **Listing fee** | NESSUNA |
| **Setup fee** | NESSUNA |
| **Delisting fee** | NESSUNA |

## 5.2 Commissioni per Categoria

| Categoria | Commissione |
|-----------|------------|
| **Range generale** | 5-15% (IVA esclusa) |
| **HDD/Storage** | 5% |
| **Elettronica generica** | 5-15% (varia per sottocategoria) |
| **Dettaglio per categoria** | Non pubblicamente divulgato — ottenere da Conrad BD |

**Nota**: La commissione NON viene calcolata sulle spese di spedizione.

## 5.3 Simulazione Costi — Prodotto Elettronica €100

### Scenario A: Commissione 5% (es. Storage/HDD)

| Voce | Importo |
|------|--------|
| Prezzo vendita (netto) | €100.00 |
| Commissione Conrad (5%) | -€5.00 |
| Allocazione abbonamento (€99/20 vendite) | -€4.95 |
| **Payout netto** | **€90.05** |
| **Costo effettivo** | **9.95%** |

### Scenario B: Commissione 10% (es. Componenti)

| Voce | Importo |
|------|--------|
| Prezzo vendita (netto) | €100.00 |
| Commissione Conrad (10%) | -€10.00 |
| Allocazione abbonamento (€99/20 vendite) | -€4.95 |
| **Payout netto** | **€85.05** |
| **Costo effettivo** | **14.95%** |

### Scenario C: Commissione 15% (es. Accessori)

| Voce | Importo |
|------|--------|
| Prezzo vendita (netto) | €100.00 |
| Commissione Conrad (15%) | -€15.00 |
| Allocazione abbonamento (€99/20 vendite) | -€4.95 |
| **Payout netto** | **€80.05** |
| **Costo effettivo** | **19.95%** |

## 5.4 Break-Even Mensile

| Commissione | Vendite necessarie per coprire €99/mese |
|-------------|----------------------------------------|
| 5% | €1.980 |
| 10% | €990 |
| 15% | €660 |

## 5.5 Confronto con Altri Marketplace

| Marketplace | Fee Fisse | Commissione | Modello |
|-------------|-----------|-------------|--------|
| **Conrad** | €99/mese | 5-15% | B2B curated |
| **eMAG** | ZERO | 5-10% | B2C/B2B open |
| **Allegro** | PLN 49-3K/mese | 4-15% | B2C open |
| **Otto** | €99.90/mese | 7-18% | B2C curated |
| **Amazon** | €39/mese | 7-15% | B2C/B2B open |

---

# 6. Ciclo Pagamenti e Payout

## 6.1 Frequenza Payout

**3 volte al mese (tri-mensile):**

| Data trigger | Elaborazione Thunes | Accredito CPS |
|-------------|--------------------|--------------|
| **10 del mese** | 11 del mese | 12 del mese |
| **20 del mese** | 21 del mese | 22 del mese |
| **Ultimo giorno del mese** | 1° del mese successivo | 2 del mese successivo |

**Eccezione**: Se il giorno di elaborazione cade di sabato/domenica, il trasferimento avviene il lunedì successivo.

## 6.2 Dettagli Pagamento

| Parametro | Valore |
|-----------|--------|
| **Valuta** | EUR |
| **Metodo** | SEPA Bank Transfer |
| **Payment processor** | Webhelp (fatturazione) + Thunes (processing) + CPS (clearing) |
| **Timeline** | 1-2 giorni lavorativi dopo trigger |
| **Per seller italiani** | IBAN italiano con SEPA, nessuna conversione valuta |

## 6.3 Flusso Pagamento

```
Cliente B2B ordina su Conrad
  │
  ▼
Webhelp emette fattura a nome del seller
  │
  ▼
Conrad trattiene commissione
  │
  ▼
Payout trigger (10°, 20°, ultimo giorno)
  │
  ▼
Thunes processa (giorno +1)
  │
  ▼
CPS accredita su conto seller (giorno +2)
```

---

# 7. Product Listing Requirements

## 7.1 Campi Obbligatori

**Conrad ha requisiti minimi molto snelli:**

| Campo | Tipo | Obbligatorio |
|-------|------|-------------|
| **Price** | Decimal | ✅ Sì |
| **Reverse Charge** | Boolean | ✅ Sì ("No" per default) |
| **EAN/GTIN** | String | ⚠️ Opzionale (ma se assente, brand obbligatorio) |
| **Brand/Manufacturer** | String | ⚠️ Obbligatorio se no EAN |
| **Categoria** | ID | ✅ Da categorie assegnate |
| **Titolo** | String | ✅ 100-200 caratteri |
| **Descrizione** | Text | ✅ Dettagli prodotto |
| **Immagini** | URL | ✅ Minimo 1 |

**Nota importante**: Molti attributi opzionali esistono (specifici per tipo prodotto + generici). La review qualità viene fatta manualmente dal team Conrad.

## 7.2 Requisiti EAN

| Parametro | Valore |
|-----------|--------|
| **Status** | OPZIONALE |
| **Regola** | Se EAN non disponibile → DEVE fornire brand/manufacturer |
| **Immutabilità** | EAN NON modificabile dopo creazione prodotto |
| **Unicità** | EAN aiuta product matching ed evita duplicati |

## 7.3 Requisiti Immagini

| Parametro | Specifica |
|-----------|----------|
| **Formati** | JPG, PNG |
| **Hosting** | URL diretti su server/CDN dedicati |
| **NON supportati** | Google Drive, Dropbox, iCloud, YouTube, Vimeo |
| **CDN raccomandati** | CloudFront, CloudFlare, Akamai |
| **Documentazione** | PIR (Product Image Requirements) disponibile su piattaforma |

## 7.4 Upload Prodotti — 2-Step Process

1. **Step 1**: Upload product master data (categoria, brand, MPN, specifiche tecniche)
2. **Step 2**: Upload sales details/offerte (prezzo, stock, info seller, shipping class)

**Formati supportati**:
- CSV (UTF-8, raccomandato)
- XLSX (Excel)
- XML

**Regole critiche**:
- Usare **attribute codes** (non labels) nei CSV/XML
- Solo **UNA** header row permessa
- Encoding **UTF-8** obbligatorio

## 7.5 Lingua

| Marketplace | Lingua |
|-------------|--------|
| Conrad.de | Tedesco |
| Conrad.it | **Italiano** |
| Conrad.fr | Francese |
| Conrad.nl | Olandese |
| Conrad.at | Tedesco |

## 7.6 Categorie Prodotto

### Categorie Core

| # | Categoria | Sottocategorie Principali |
|---|-----------|---------------------------|
| 1 | **Componenti Elettronici** | Passivi, attivi, semiconduttori, microcontrollori, dev kit |
| 2 | **Computing** | Server, workstation, periferiche |
| 3 | **IT & Networking** | Switch, router, cabling, data center |
| 4 | **Automazione & Controllo** | PLC, sensori, IoT |
| 5 | **Power & Energy** | Alimentatori, batterie, motori |
| 6 | **Strumenti & Equipment** | Test, misura, diagnostica |
| 7 | **Smart Home** | Controller, illuminazione, building automation |
| 8 | **Multimedia & AV** | Audio, video, display, cabling AV |
| 9 | **Forniture Industriali** | Componenti industriali, sicurezza, maintenance |
| 10 | **Accessori & Consumabili** | Cavi, connettori, storage, ricambi |

### Prodotti NON Ammessi

- Contenuti FSK 18+ (film, videogiochi)
- Contenuti che glorificano violenza o guerra
- Fashion/abbigliamento consumer
- Prodotti deperibili
- Prodotti che richiedono catena del freddo
- Mobili grandi/non tecnici

---

# 8. API e Integrazione Tecnica (Mirakl)

## 8.1 Piattaforma: Mirakl

**Conrad marketplace è costruito su Mirakl Marketplace Platform (MMP).**

| Parametro | Valore |
|-----------|--------|
| **Piattaforma** | Mirakl MMP |
| **Lancio partnership** | 2017 |
| **Tipo API** | REST (HTTPS only) |
| **Formato** | JSON, CSV, XML |
| **Specifica** | OpenAPI 3.0 |
| **Developer Portal** | developer.mirakl.com |
| **Seller API Docs** | developer.mirakl.com/content/product/mmp/rest/seller/openapi3 |

## 8.2 Autenticazione

### API Key (Metodo Primario per Seller)

| Parametro | Valore |
|-----------|--------|
| **Tipo** | API Key in Authorization Header |
| **Header** | `Authorization: ApiKey YOUR_API_KEY` |
| **Generazione** | Seller portal → Account settings → "API Key" tab |
| **Scope** | Offerte, ordini, spedizioni, pricing |

### Bearer Token (Per Operazioni Piattaforma)

| Parametro | Valore |
|-----------|--------|
| **Tipo** | JWT (JSON Web Token) |
| **Header** | `Authorization: Bearer YOUR_JWT_TOKEN` |
| **Credenziali** | Client ID + Client Secret (da Mirakl Partner team) |
| **Uso** | Connect Channel Platform APIs |

## 8.3 Rate Limiting

| Parametro | Valore |
|-----------|--------|
| **Protezione** | Tutti gli API protetti da rate limit |
| **Errore** | HTTP 429 "Too Many Requests" |
| **Retry** | Header `Retry-After` specifica secondi attesa |
| **Limiti specifici** | Variano per endpoint (documentati nel developer portal) |

## 8.4 Endpoint Principali

### Product & Offer Management

| Endpoint | Codice | Metodo | Scopo |
|----------|--------|--------|-------|
| Upload offerte | OF01 | POST | Crea/aggiorna/elimina offerte in bulk |
| Info import offerte | OF02 | GET | Recupera info su import file |
| Report errori import | OF03 | GET | Log errori dettagliati |
| Lista offerte per prodotto | P11 | GET | Recupera offerte esistenti |
| Download offerte CSV | OF51 | GET | Esporta offerte |
| Upload prezzi | PRI01 | POST | Aggiorna pricing catalogo |

### Order Management

| Endpoint | Codice | Metodo | Scopo |
|----------|--------|--------|-------|
| Lista ordini | OR11 | GET | Ordini con paginazione |
| Dettagli ordine | OR12 | GET | Info complete ordine singolo |
| Accetta/Rifiuta | OR21 | POST | Conferma capacità fulfillment |
| Tracking spedizione | OR23 | POST | Aggiorna tracking (OBBLIGATORIO) |
| Rimborso parziale | OR26 | POST | Processa rimborsi |
| Rimborso totale | OR28 | POST | Cancella transazione |

### Document Management

| Endpoint | Codice | Metodo | Scopo |
|----------|--------|--------|-------|
| Lista documenti | OR72 | GET | Fatture, etichette |
| Download documento | OR73 | GET | Scarica documento specifico |
| Upload documento | OR74 | POST | Invia conferme, documenti dogana |

## 8.5 Category Mapping

- Conrad ha tassonomia categorie rigorosa per elettronica
- **Mapping Assistant** disponibile nel backoffice Mirakl: "Mapping Wizard"
- Auto-mapping per nomi attributi corrispondenti
- Mapping manuale necessario per attributi custom
- **Campi rossi** = attributi obbligatori da compilare

## 8.6 SDK e Risorse Developer

### SDK Ufficiali Mirakl

| Linguaggio | Repository | Note |
|------------|-----------|------|
| **PHP** | github.com/mirakl/sdk-php-shop | Maturo, classi auto-generate |
| **Java** | github.com/mirakl | Maven/Gradle, strongly typed |
| **JavaScript/Node** | Non ufficiale | Axios/fetch + chiamate manuali |

### Postman Collections

| Collection | URL |
|------------|-----|
| **Mirakl Seller API (MMP)** | postman.com/jbc16/api-training/documentation/nbb0ivb/mirakl-seller-api-mmp |
| **Mirakl Operator API** | postman.com/sirvis-e2y/mirakl-training/documentation/au29dyk/mirakl-operator-api-mmp |
| **Mirakl Connect API** | postman.com/docking-module-administrator-43133859/mirakl/collection/nzuuscm/mirakl-connect-api-connect |
| **Mirakl Front API** | postman.com/maintenance-operator-9053390/mirakl/collection/y3mkl7o/mirakl-front-api-mmp |

## 8.7 Codici Errore HTTP

| Codice | Significato | Azione |
|--------|------------|--------|
| 200 | OK | Successo |
| 201 | Created | Risorsa creata |
| 400 | Bad Request | Errore parametri → correggere request |
| 401 | Unauthorized | API key mancante/invalida |
| 403 | Forbidden | Permessi insufficienti |
| 404 | Not Found | Risorsa non esistente |
| 429 | Too Many Requests | Rate limit → attendere Retry-After |
| 500 | Server Error | Errore Mirakl → retry con backoff |

---

# 9. Order Management

## 9.1 Flusso Ordine B2B

```
Cliente B2B ordina su Conrad
  │
  ▼
Ordine visibile nel backoffice (My Orders > All Orders)
  │
  ▼
Seller clicca "Accept Order" (OR21 API)
  │
  ▼
Preparazione ordine (pick & pack)
  │
  ▼
Conferma spedizione + tracking number (OR23 API) — OBBLIGATORIO
  │
  ▼
⚠️ NON includere fattura nel pacco (solo delivery note)
  │
  ▼
Webhelp invia fattura automaticamente al buyer a nome del seller
  │
  ▼
Conrad trattiene commissione → payout al seller (3x/mese)
```

## 9.2 Regole Critiche

| Regola | Dettaglio |
|--------|----------|
| **Tracking number** | OBBLIGATORIO per ogni ordine |
| **Fattura nel pacco** | MAI — solo delivery note |
| **Fatturazione** | Webhelp emette fattura a nome del seller |
| **Spedizione singola** | Ogni ordine spedito individualmente |

---

# 10. Logistica e Spedizioni

## 10.1 Requisiti Logistici

| Parametro | Valore |
|-----------|--------|
| **Magazzino** | Nel paese del marketplace (IT per Conrad.it, DE per Conrad.de) |
| **Spedizione** | Ordini singoli individuali (no batch) |
| **Tracking** | OBBLIGATORIO per ogni pacco |
| **Nota nel pacco** | Solo delivery note (no fattura) |

## 10.2 Fulfilment by Conrad

| Parametro | Valore |
|-----------|--------|
| **Disponibilità** | Sì, per seller qualificati |
| **Come funziona** | Seller invia inventario a Conrad Logistics & Distribution |
| **Conrad gestisce** | Picking, packing, spedizione |
| **Volume Conrad** | 50.000 pacchi/giorno dai propri centri |
| **Requisiti** | Da negoziare con Conrad BD |

## 10.3 Cross-Border per Vendiamonoi

### Opzione A: Conrad.it (Italia)
- Logistica da Italia → clienti italiani B2B
- Nessun cross-border necessario
- Massimo controllo

### Opzione B: Espansione a Conrad.de (Germania)
- Richiede entità tedesca (GmbH) o partner logistico DE
- Magazzino in Germania obbligatorio
- P.IVA tedesca obbligatoria

### Opzione C: Fulfilment by Conrad
- Inviare stock a Conrad
- Conrad gestisce fulfillment
- Ideale per espansione senza infrastruttura locale

---

# 11. Resi e Rimborsi

## 11.1 Policy Resi B2B

| Parametro | Valore |
|-----------|--------|
| **Diritto di reso standard** | **NO** (B2B, non B2C) |
| **Eccezione** | Solo prodotti difettosi |
| **Policy** | Il seller può applicare policy no-return più restrittive |
| **Rimborsi** | Via API (OR26 rimborso parziale, OR28 rimborso totale) |

**Differenza chiave rispetto a marketplace B2C**: Su Conrad B2B non esiste diritto di recesso 14 giorni. Solo difetti/non conformità danno diritto al reso.

---

# 12. Compliance e Normative

## 12.1 Compliance Tedesca (per Conrad.de)

| Normativa | Requisito | Sanzioni |
|-----------|-----------|----------|
| **VerpackG/LUCID** | Registrazione imballaggi obbligatoria | Fino a €200.000 |
| **ElektroG/WEEE** | Registrazione apparecchiature elettriche | Fino a €100.000 |
| **BattG** | Registrazione batterie (se applicabile) | Ammende significative |
| **CE Marking** | Obbligatorio per prodotti tecnici | Rimozione dal mercato |
| **RoHS** | Restrizione sostanze pericolose in elettronica | Obbligatorio EU |
| **REACH** | Registrazione sostanze chimiche | Obbligatorio EU |
| **Impressum** | Informazioni legali obbligatorie | Obbligatorio |
| **Lingua tedesca** | Per listing su Conrad.de | Obbligatorio |

## 12.2 Compliance Italiana (per Conrad.it)

| Normativa | Requisito |
|-----------|----------|
| **IVA** | 22% standard Italia |
| **CE Marking** | Obbligatorio per prodotti tecnici |
| **GPSR** | Dal 13 dicembre 2024 |
| **RAEE/WEEE** | Registrazione per apparecchiature elettriche |
| **Imballaggi** | CONAI (Consorzio Nazionale Imballaggi) |
| **Lingua** | Italiano per Conrad.it |

## 12.3 GPSR — General Product Safety Regulation

| Parametro | Valore |
|-----------|--------|
| **In vigore dal** | 13 dicembre 2024 |
| **Obblighi** | Info produttore, info sicurezza, lingua locale |
| **Impatto** | Tutti i prodotti tecnici su Conrad |

## 12.4 Fatturazione B2B

| Requisito | Dettaglio |
|-----------|----------|
| **Rechnungspflichtangaben** (DE) | Nome, indirizzo, P.IVA, data, importo netto/IVA |
| **Reverse charge** | Applicabile per vendite B2B intra-EU |
| **Gestione** | Webhelp gestisce fatturazione a nome seller |
| **E-Invoicing** | Integrazione con sistemi procurement B2B |

## 12.5 IVA per Seller Italiano

| Scenario | IVA |
|----------|-----|
| **Vendita su Conrad.it a clienti IT** | 22% IVA italiana |
| **Vendita su Conrad.de a clienti DE** | 19% IVA tedesca (richiede registrazione) |
| **Vendita B2B intra-EU con P.IVA** | Reverse charge (0% + autofattura) |
| **OSS** | Applicabile se vendite B2C cross-border < €10.000 |

## 12.6 Checklist Compliance per Vendiamonoi

**Per Conrad.it (Italia)**:
- [ ] Partita IVA italiana attiva
- [ ] Registrazione RAEE/WEEE (per prodotti elettronici)
- [ ] Registrazione CONAI (imballaggi)
- [ ] Marcatura CE per prodotti tecnici
- [ ] Conformità GPSR
- [ ] RoHS/REACH per elettronica
- [ ] Listing in italiano

**Per espansione Conrad.de (Germania)**:
- [ ] Entità tedesca (GmbH) o rappresentante
- [ ] P.IVA tedesca
- [ ] Registrazione VerpackG/LUCID
- [ ] Registrazione ElektroG/WEEE tedesca
- [ ] Registrazione BattG (se batterie)
- [ ] CE Marking
- [ ] Impressum in tedesco
- [ ] Listing in tedesco

---

# 13. Performance Metrics e KPI

## 13.1 Modello Curated

Conrad mantiene il marketplace curated monitorando strettamente le performance dei seller:

| Metrica | Descrizione |
|---------|------------|
| **Qualità prodotto** | Review manuale del team Conrad |
| **Tempo processamento ordine** | Velocità da ordine a spedizione |
| **Tracking compliance** | % ordini con tracking number |
| **Tasso resi/reclami** | Deve rimanere basso |
| **Soddisfazione cliente** | Feedback B2B |
| **Disponibilità stock** | Precisione inventario |

## 13.2 Impatto Performance

- Rating basso può portare a **rimozione dal marketplace** (curated model)
- Conrad non divulga soglie specifiche pubblicamente
- Il team BD monitora attivamente e comunica problemi

---

# 14. Integrazione Channel Manager

## 14.1 ChannelEngine (INTEGRAZIONE PIÙ COMPLETA)

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Integrazione completa |
| **Paesi** | Conrad DE, IT, FR, NL, AT, CZ |
| **Funzionalità** | Product sync, inventory, order management, pricing |
| **Connessione** | Mirakl SSO (OAuth-style) |
| **Limitazione** | NO repricing (prezzi gestiti manualmente) |
| **URL** | channelengine.com/en/all-marketplaces/conrad |

## 14.2 Channable (INTEGRAZIONE MATURA)

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Integrazione via Mirakl API |
| **Metodi** | API (raccomandato) + Feed (deprecato) |
| **Funzionalità** | Listing optimization, order automation |
| **Certificazione** | Mirakl Connect Partner ufficiale |
| **URL** | channable.com/integrations/conrad-marketplace-api |

## 14.3 nexoma CatalogExpress (FOCUS ERP)

| Parametro | Valore |
|-----------|--------|
| **Specializzazione** | ERP → Mirakl data transformation |
| **Sorgenti** | ERP, PIM, DAM via FTP o API diretta |
| **Output** | XML, CSV, openTRANS |
| **Vantaggio** | Go-live possibile dal giorno 1 |

## 14.4 SellRapido

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ❌ NON documentato |
| **Nota** | SellRapido non appare nella documentazione Conrad/ChannelEngine |
| **Alternativa** | Usare ChannelEngine o Channable come bridge |

## 14.5 Altri Tool

| Tool | Supporto |
|------|----------|
| **EffectConnect** | ✅ Real-time inventory sync |
| **Shop-Vibes** | ✅ Mirakl listing |
| **Shopware + Stockpilot** | ✅ Via connector |
| **BindCommerce** | ✅ Category matching documentato |

## 14.6 Pattern Integrazione per Vendiamonoi

### Opzione A: ChannelEngine (Raccomandato)
```
SellRapido → ChannelEngine → Conrad Mirakl API
            ↕                ↕
        Supabase          Mirakl SSO + API Key
            ↕
       Make.com
```

### Opzione B: Channable (Feed + API)
```
Feed prodotti → Channable → Conrad Mirakl API
                    ↕
              Ordini + Sync inventario
```

### Opzione C: API Diretta Mirakl (Make.com)
```
Make.com HTTP Module → Conrad Mirakl API (API Key)
  │  Endpoint: OF01 (prodotti), OR11 (ordini)
  ↕
Supabase → Sync stock + pricing
```

---

# 15. Copertura Geografica

## 15.1 Marketplace Attivi (3P)

| Paese | Anno Lancio | Requisiti Seller |
|-------|------------|------------------|
| **Germania (DE)** | 2017 | GmbH + P.IVA DE + magazzino DE |
| **Austria (AT)** | 2021 | P.IVA AT |
| **Paesi Bassi (NL)** | 2022 | P.IVA NL + magazzino NL |
| **Francia (FR)** | 2023 | P.IVA FR |
| **Italia (IT)** | 2023 | **P.IVA IT + logistica IT** |
| **Belgio (BE)** | 2023+ | P.IVA BE |

## 15.2 Retail Diretto (1P) — 17 Paesi

Conrad retail (non marketplace) opera in 17 paesi europei. L'espansione marketplace a ulteriori paesi è prevista.

## 15.3 Strategia Multi-Paese per Vendiamonoi

1. **Start**: Conrad.it (mercato locale, zero friction)
2. **Fase 2**: Conrad.de (mercato più grande, richiede entità DE)
3. **Fase 3**: Conrad.at, Conrad.nl, Conrad.fr (espansione EU)

---

# 16. Strategia per Vendiamonoi

## 16.1 Go/No-Go Assessment

**VERDETTO: GO CONDIZIONATO** — Conrad rappresenta un'opportunità unica per il segmento B2B, ma richiede prodotti tecnici nel catalogo.

### Motivi per GO
- Unico **marketplace B2B puro** nel portfolio Vendiamonoi
- Conrad.it lanciato nel **2023** → mercato nuovo, meno competizione
- **Curated** = meno seller = meno competizione per chi entra
- **10+ milioni di prodotti** = alta visibilità piattaforma
- **2.3 milioni clienti B2B** = base clienti qualificata
- **Mirakl-powered** = integrazione standardizzata (stessa piattaforma di altri marketplace)
- **Payout 3x/mese** = buon cash flow
- **No resi consumer** = meno costi operativi
- **EUR nativo** = nessun rischio cambio per seller italiano

### Condizioni per Successo
- Avere prodotti nel catalogo che rientrano nelle categorie Conrad (elettronica, IT, automazione, strumenti)
- Fornitori con prodotti tecnici/B2B
- Capacità di supporto tecnico (clienti B2B richiedono specifiche)

## 16.2 Barriere all'Ingresso

| Barriera | Livello | Soluzione |
|----------|---------|----------|
| Catalogo tecnico/B2B | 🔴 Alta (se non hai prodotti tecnici) | Focus su fornitori elettronica/IT |
| Onboarding curated | 🟡 Media | Preparazione documentazione + quality check |
| €99/mese fissi | 🟢 Bassa | Break-even con €660-1.980 vendite/mese |
| Supporto tecnico B2B | 🟡 Media | Team con competenze tecniche |
| Fatturazione B2B | 🟢 Bassa | Webhelp gestisce automaticamente |
| Lingua (per DE) | 🔴 Alta | Non necessario per Conrad.it |

## 16.3 Stima Costi

### Setup (Una Tantum)

| Voce | Costo |
|------|-------|
| Registrazione Conrad.it | Gratuita (se accettati) |
| Preparazione catalogo tecnico | €500-1.000 |
| Integrazione ChannelEngine/Channable | €200-500 |
| Registrazione RAEE/WEEE | €100-300 |
| **TOTALE** | **€800-1.800** |

### Costi Mensili

| Voce | Costo |
|------|-------|
| Abbonamento Conrad | €99 |
| ChannelEngine/Channable (quota) | €100-300 |
| Supporto tecnico B2B | €200-500 |
| **TOTALE** | **€399-899** |

## 16.4 Confronto Conrad vs Otto vs Allegro vs eMAG

| Fattore | Conrad | Otto | Allegro | eMAG |
|---------|--------|------|---------|------|
| **Tipo** | B2B | B2C | B2C | B2B+B2C |
| **Accessibilità IT** | ✅ Conrad.it diretto | ❌ Entità DE | ✅ S.R.L. diretta | ✅ S.R.L. diretta |
| **Fee fisse** | €99/mese | €99.90/mese | PLN 49-3K/mese | ZERO |
| **Commissioni** | 5-15% | 7-18% | 4-15% | 5-10% |
| **Payout** | 3x/mese | Settimanale | Giornaliero | 2x/mese |
| **Resi** | Solo difettosi | 30gg | 14gg | 30-60gg |
| **Piattaforma** | Mirakl | Proprietaria | Proprietaria | Proprietaria |
| **Seller** | 800-1K curated | Curated | 164K open | 36K-53K open |
| **Setup** | €800-1.800 | €3.2-13.5K | €900-2K | €1.4-2.8K |
| **Mensile** | €399-899 | €2.6-6.3K | €1.1-2.2K | €1-2.1K |

## 16.5 Timeline Ingresso Raccomandato

| Fase | Periodo | Azione |
|------|---------|--------|
| 1 | Settimana 1-2 | Contattare Conrad BD, sottomettere application |
| 2 | Settimana 2-4 | Vetting, briefing call, approvazione |
| 3 | Settimana 4-6 | Setup catalogo, integrazione ChannelEngine, upload 50-100 prodotti |
| 4 | Settimana 6-8 | Go-live, primi ordini B2B |
| 5 | Mese 3-6 | Scala catalogo, analisi performance |
| **Totale** | **2-6 mesi** | Dal contatto alla presenza stabile |

## 16.6 Fit Strategico per Vendiamonoi

Conrad è l'unico **marketplace B2B puro** nel portfolio dei 20-30 marketplace di Vendiamonoi. Questo è strategicamente importante perché:

1. **Diversificazione canale**: B2B è un mercato completamente diverso da B2C
2. **Margini più alti**: Ordini B2B hanno valore medio superiore
3. **Clienti sticky**: Procurement B2B è basato su relazioni e ripetitività
4. **Meno resi**: B2B non ha diritto di recesso consumer
5. **Mirakl ecosystem**: Stessa piattaforma di altri marketplace Mirakl (Carrefour, Leroy Merlin, MediaMarkt)

---

## FONTI E RIFERIMENTI

### Documentazione Ufficiale Conrad
- [Conrad Seller Platform](https://platform.conrad.de/en/seller/seller.html)
- [Become a Seller](https://platform.conrad.de/en/seller/become-a-seller.html)
- [Seller FAQ](https://platform.conrad.de/en/seller/seller-faq.html)
- [Commission Rates](https://platform.conrad.de/en/seller/competence-center/zahlungen_gebuhren/commission.html)
- [Receiving Payments](https://platform.conrad.de/en/seller/competence-center/zahlungen_gebuhren/zahlungen_gebuhren.html)
- [Managing Invoices](https://platform.conrad.de/en/seller/competence-center/zahlungen_gebuhren/rechnungen_verwalten.html)
- [Product Data Upload](https://platform.conrad.de/en/seller/competence-center/verkaufaufderplatform/katalogverwaltung/produktdaten-upload.html)
- [Product Image Requirements](https://platform.conrad.de/en/seller/competence-center/verkaufaufderplatform/katalogverwaltung/produktdaten-verwalten/produktabbildungsbedingungen.html)
- [Order Processing](https://platform.conrad.de/en/seller/competence-center/verkaufaufderplatform/order-processing.html)
- [Seller Requirements](https://platform.conrad.de/en/seller/competence-center/loslegen/verkaufer_voraussetzungen.html)
- [Marketplace Overview](https://platform.conrad.de/en/seller/competence-center/loslegen/marktplatzuberblick.html)
- [Conrad eProcurement](https://www.conrad.com/en/service/eprocurement.html)

### API & Technical
- [Mirakl Developer Portal](https://developer.mirakl.com/)
- [Mirakl Seller API OpenAPI 3.0](https://developer.mirakl.com/content/product/mmp/rest/seller/openapi3)
- [Mirakl PHP SDK](https://github.com/mirakl/sdk-php-shop)
- [Postman Collections](https://www.postman.com/)

### Integrazioni
- [ChannelEngine Conrad](https://www.channelengine.com/en/all-marketplaces/conrad)
- [Channable Conrad API](https://www.channable.com/integrations/conrad-marketplace-api)
- [nexoma CatalogExpress](https://nexoma.de/conrad-de-marketplace-connection-synchronized-order-management-and-automated-data-updating/?lang=en)
- [BindCommerce Conrad](https://www.bindcommerce.com/en/tutorials/marketplace/conrad-integration/conrad-category-matching)

### Case Study & Analisi
- [Mirakl Case Study Conrad](https://www.mirakl.com/resources/conrad-case-study)
- [BusinessWire — Conrad Mirakl Launch 2017](https://www.businesswire.com/news/home/20170718005288/en/Conrad-and-Mirakl-Launch-First-B2B-Marketplace-for-Technology-and-Electronics-in-Germany)
- [Conrad Italy Launch 2023](https://www.elettronica-av.it/conrad-electronic-lancia-il-marketplace-per-i-clienti-business-in-italia/)
- [eStrategy Consulting — Conrad Marketplace Leaders](https://www.estrategy-consulting.de/en/insights/marketplace-leaders-ralf-buehler-conrad-electronic/)

---

**Documento creato**: 2026-04-07
**Ricerca**: 3 agenti paralleli (seller/business, API/technical, logistics/compliance)
**Complemento**: marketplace-specs/otto, marketplace-specs/allegro, marketplace-specs/emag
**Prossimo**: marketplace-specs/pccomponentes (P2.5)
