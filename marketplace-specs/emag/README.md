# eMAG — Deep-Dive Marketplace Spec per Vendiamonoi.it

## EXPERT-LEVEL TECHNICAL DEEP-DIVE

**Documento**: Specifica completa eMAG Marketplace per distributori digitali europei
**Target**: CTO, Marketplace Manager, Operations Manager — Vendiamonoi.it
**Versione**: 2026
**Ultimo aggiornamento**: 2026-04-07
**Complemento a**: marketplace-specs/otto, marketplace-specs/allegro, marketplace-specs/amazon

---

## INDICE COMPLETO

1. [Overview e Posizionamento Strategico](#1-overview-e-posizionamento-strategico)
2. [eMAG Group — Struttura Societaria](#2-emag-group--struttura-societaria)
3. [Onboarding e Requisiti Seller](#3-onboarding-e-requisiti-seller)
4. [Struttura Commissioni e Costi](#4-struttura-commissioni-e-costi)
5. [Ciclo Pagamenti e Payout](#5-ciclo-pagamenti-e-payout)
6. [Product Listing Requirements](#6-product-listing-requirements)
7. [API e Integrazione Tecnica](#7-api-e-integrazione-tecnica)
8. [Order Management](#8-order-management)
9. [Logistica e Spedizioni](#9-logistica-e-spedizioni)
10. [Resi e Rimborsi](#10-resi-e-rimborsi)
11. [eMAG Genius Program](#11-emag-genius-program)
12. [Compliance e Normative](#12-compliance-e-normative)
13. [Performance Metrics e KPI](#13-performance-metrics-e-kpi)
14. [Integrazione Channel Manager](#14-integrazione-channel-manager)
15. [Cross-Border: Vendere in 3 Paesi](#15-cross-border-vendere-in-3-paesi)
16. [Strategia per Vendiamonoi](#16-strategia-per-vendiamonoi)

---

# 1. Overview e Posizionamento Strategico

## 1.1 Cos'è eMAG

eMAG è la piattaforma e-commerce dominante dell'Europa Centrale e Orientale (CEE), fondata in Romania nel 2001. Opera come modello ibrido: retail diretto (1P) + marketplace terze parti (3P). Di proprietà di Naspers/Prosus N.V. (Euronext: PRX), è il più grande marketplace della regione CEE.

| Parametro | Valore |
|-----------|--------|
| **GMV annuo** | ~€1.3+ miliardi |
| **Revenue FY2025** | ~$2.5 miliardi (gruppo Prosus Ecommerce incluso eMAG) |
| **Crescita GMV** | +9-16% YoY |
| **Visite mensili** | 120+ milioni (tutte le properties) |
| **Clienti attivi** | 9+ milioni |
| **Seller marketplace attivi** | 36.000-53.000 |
| **Quota mercato Romania** | 37.5-40% del retail online |
| **Mercati attivi** | Romania (65% revenue), Ungheria, Bulgaria |
| **Piattaforma** | Proprietaria (NON Mirakl) |
| **Proprietà** | Naspers/Prosus N.V. |
| **Profittabilità** | EBIT 12x improvement FY2025, aEBITDA +23% H1 2026 |

## 1.2 Confronto con Concorrenti Regionali

| Marketplace | Mercato | GMV | Quota |
|-------------|---------|-----|-------|
| **eMAG** | Romania/CEE | €1.3B+ | 37.5% RO |
| **Allegro** | Polonia | €16.7B | 30% PL |
| **Amazon.de** | Germania/CE | — | Dominante DE |
| **OLX/Vinted** | CEE (C2C) | — | C2C, non comparabile |

## 1.3 Mercato E-Commerce Romania

| Parametro | Valore |
|-----------|--------|
| **Popolazione** | 19 milioni |
| **Penetrazione internet** | 85%+ |
| **Valore e-commerce 2025** | ~€6-8 miliardi |
| **Crescita annua** | +12-15% |
| **Metodo pagamento #1** | Contrassegno (COD) — ancora molto diffuso |
| **Metodo pagamento #2** | Carta online (Visa/Mastercard) |
| **Valuta** | RON (Leu romeno) |

## 1.4 Categorie Top su eMAG

| Categoria | Quota vendite | Commission |
|-----------|--------------|------------|
| **Elettronica** | 39% | 5% (hardlines) |
| **Fashion/Apparel** | 15-18% | 10% (softlines) |
| **Home/Kitchen** | 12-15% | 5% (hardlines) |
| **Beauty/Cosmetics** | 8-12% | 10% (softlines) |
| **Toys/Sports** | 5-7% | 5% (hardlines) |
| **Books/Media** | 5-7% | 5% |

---

# 2. eMAG Group — Struttura Societaria

## 2.1 Ecosistema eMAG

| Società | Tipo | Ruolo |
|---------|------|-------|
| **eMAG Marketplace** | 3P Platform | Marketplace principale (RO, HU, BG) |
| **eMAG Retail** | 1P Retail | Vendita diretta (elettronica dominante) |
| **Sameday** | Logistica | Last-mile delivery, easybox lockers (100% owned) |
| **Tazz by eMAG** | Food Delivery | In acquisizione da Wolt (ottobre 2024) |
| **Fashion Days** | Fashion | Marketplace verticale moda (acquisito 2015) |
| **Freshful by eMAG** | Grocery | Supermercato online premium (solo Bucarest/Ilfov) |
| **Fulfillment by eMAG (FBE)** | Logistica | Servizio warehouse FBA-like per seller |

## 2.2 Sameday — Logistica Proprietaria

| Parametro | Valore |
|-----------|--------|
| **easybox lockers** | 3.900+ in Romania |
| **Copertura** | Romania, Ungheria, Bulgaria |
| **Same-day delivery** | Disponibile in aree urbane principali |
| **Internazionale** | In espansione, attivo in più paesi |
| **Integrazione** | Nativa con eMAG marketplace |

## 2.3 Genius — Ecosistema Unificato

Un singolo abbonamento Genius copre:
- eMAG Marketplace (shopping)
- Tazz (food delivery → transizione a Wolt)
- Fashion Days (fashion)
- Freshful (grocery premium)

---

# 3. Onboarding e Requisiti Seller

## 3.1 Seller Internazionali (Italia)

**Un'azienda italiana può vendere direttamente su eMAG?**

**SÌ** — eMAG accetta seller internazionali dall'UE, inclusa l'Italia.

### Documenti Necessari

| Documento | Dettaglio |
|-----------|----------|
| **Registrazione impresa** | Certificato camerale (Registro Imprese) |
| **Partita IVA** | IT + 11 cifre |
| **Numero REA** | Registrazione economica amministrativa |
| **Documento identità** | Passaporto/CI del rappresentante legale |
| **Prova indirizzo** | Bolletta, contratto affitto, corrispondenza ufficiale |
| **Coordinate bancarie** | IBAN per pagamenti SEPA |
| **Contatto operativo** | Nome, email business, telefono (+39) |

## 3.2 Processo di Verifica

| Fase | Durata |
|------|--------|
| Registrazione online | 5-15 minuti |
| Upload documenti | 15 minuti |
| Pre-screening automatico | 24-48 ore |
| Revisione manuale | 2-7 giorni lavorativi |
| Notifica approvazione | 24 ore dopo revisione |
| Attivazione conto bancario | 1-3 giorni lavorativi |
| **Totale stimato** | **7-15 giorni lavorativi** |

## 3.3 Motivi Comuni di Rifiuto

- Partita IVA non valida o scaduta
- Documenti illeggibili o scaduti
- Mismatch nome azienda tra conto bancario e registrazione
- Indirizzo non verificabile

## 3.4 Costi di Registrazione

| Voce | Costo |
|------|-------|
| Registrazione seller | **GRATUITA** |
| Abbonamento mensile | **NESSUNO** |
| Listing fee | **NESSUNO** |
| Setup/attivazione | **NESSUNO** |
| Delisting prodotti | **NESSUNO** |

**eMAG è uno dei pochi marketplace EU senza costi fissi** — si paga solo la commissione sulle vendite.

---

# 4. Struttura Commissioni e Costi

## 4.1 Commissioni per Categoria

eMAG utilizza una struttura semplice a due livelli:

### Hardlines (5%)

| Categoria | Commissione |
|-----------|-----------|
| Elettronica (telefoni, laptop, componenti) | 5% |
| Elettrodomestici (frigo, lavatrici, forni) | 5% |
| Utensili e attrezzi | 5% |
| Giocattoli | 5% |
| Sport e outdoor | 5% |
| Giardino e outdoor | 5% |

### Softlines (10%)

| Categoria | Commissione |
|-----------|-----------|
| Fashion e abbigliamento | 10% |
| Gioielli e orologi | 10% |
| Cosmetici e beauty | 10% |
| Borse e valigeria | 10% |
| Tessili (biancheria, tende, tappeti) | 10% |

## 4.2 Base di Calcolo Commissione

- Commissione calcolata su: **prezzo netto prodotto (IVA esclusa) + spese spedizione addebitate al cliente**
- Esempio: prodotto €50 + spedizione €5 = base €55 × 10% = €5.50 commissione

## 4.3 Programmi Speciali

| Programma | Commissione | Requisiti |
|-----------|-------------|----------|
| **Open Bulgaria** | 5-10% | Fatturato ≤€393.700, prodotti fabbricati in Bulgaria |
| **Campagne eMAG** | -1/-3% sconto | Opt-in a campagne marketing |
| **Genius seller** | Negoziabile | Rating 4.5+, <2% resi, spedizione veloce |

## 4.4 Confronto Commissioni con Altri Marketplace

| Marketplace | Commissione Range | Fee Fisse |
|-------------|------------------|-----------|
| **eMAG** | 5-10% | NESSUNA |
| **Allegro** | 4-15% | PLN 49-3.000/mese |
| **Otto** | 7-18% | €99.90/mese |
| **Amazon** | 7-15% | €39/mese |
| **eBay** | 5-12% | Variabile |

**eMAG ha il modello di costi più semplice e trasparente** tra i marketplace EU.

---

# 5. Ciclo Pagamenti e Payout

## 5.1 Frequenza Payout

**Bi-mensile (2 volte al mese)**:

| Ciclo | Periodo ordini | Data payout |
|-------|---------------|-------------|
| **Primo** | 1-15 del mese | 17 del mese (o primo giorno lavorativo dopo) |
| **Secondo** | 16-fine mese | 3 del mese successivo (o primo giorno lavorativo dopo) |

## 5.2 Dettagli Pagamento

| Parametro | Valore |
|-----------|--------|
| **Valuta primaria** | RON (Leu romeno) |
| **Metodo payout** | SEPA Bank Transfer (per seller EU/Italia) |
| **Alternativa** | Wire Transfer (SWIFT) |
| **Elaborazione** | Automatica, nessuna richiesta manuale |
| **Conversione valuta** | Tasso interbancario (~1.5-2% spread) |
| **Tasso cambio** | ~1 EUR = 4.97 RON (variabile) |

## 5.3 Hold Period

| Tipo pagamento | Hold |
|----------------|------|
| Pagamento carta online | 4-5 giorni lavorativi post-consegna |
| Contrassegno (COD) | 2 cicli dopo ritiro |

## 5.4 Report Finanziari

- Dashboard seller con breakdown per ordine
- Report settimanali e mensili
- Dettaglio commissioni, prodotti venduti, spese consegna
- Filtri per range date

---

# 6. Product Listing Requirements

## 6.1 Campi Obbligatori Prodotto

| Campo | Tipo | Note |
|-------|------|------|
| **Nome/Titolo** | String | 50-80 caratteri raccomandati |
| **Brand** | String | Da lista brand whitelisted eMAG |
| **Categoria** | ID | Dalle categorie assegnate al seller |
| **Descrizione** | Text/HTML | Dettagli completi prodotto |
| **Immagini** | URL | Minimo 1 immagine |
| **Prezzo** | Decimal | IVA inclusa, nella valuta del marketplace |
| **IVA** | ID | Da endpoint /vat/read |
| **Garanzia** | Integer | Periodo in giorni |
| **Handling time** | Value | Da endpoint /handling_time/read |
| **Stock** | Array | Per warehouse_id + available_quantity |

## 6.2 Campi Obbligatori Offerta

| Campo | Tipo | Note |
|-------|------|------|
| **sale_price** | Decimal | Prezzo IVA inclusa |
| **vat_id** | Integer | ID aliquota IVA valida |
| **warranty** | Integer | Garanzia in giorni |
| **handling_time** | Value | Tempo preparazione |
| **stock** | Array | [{warehouse_id, available_quantity}] |

## 6.3 Requisiti EAN/GTIN

| Parametro | Valore |
|-----------|--------|
| **Formati supportati** | EAN-8, EAN-13, UPC-A, UPC-E, JAN, ISBN, GTIN-14 |
| **Campo** | Array di stringhe, 6-14 caratteri numerici |
| **Obbligo** | Dipende dalla categoria (eMAG definisce per categoria) |
| **Validazione** | Se EAN trovato nel catalogo eMAG, skip creazione prodotto → attach offerta direttamente |
| **Nota critica** | Usare barcode fornitore, NON codici interni aziendali |
| **Mutua esclusività** | `ean` OPPURE `part_number_key`, non entrambi |

## 6.4 Requisiti Immagini

| Parametro | Specifica |
|-----------|----------|
| **Formati** | JPG, JPEG, PNG |
| **Dimensioni massime** | 6.000 × 6.000 pixel |
| **Peso massimo** | 8 MB per immagine |
| **Risoluzione raccomandata** | 1.200-2.400 px |
| **Sfondo** | Bianco o grigio raccomandato |
| **Copertura prodotto** | ~85% della superficie immagine |
| **URL** | HTTPS obbligatorio, immagini ricaricate solo se URL cambia |
| **CDN raccomandato** | CloudFront, CloudFlare, Akamai |

## 6.5 Distinzione Prodotto vs Offerta

| Concetto | Descrizione |
|----------|----------|
| **Product** | Entità nel catalogo eMAG (validata da eMAG) |
| **Offer** | Listing specifico del seller (prezzo, stock, garanzia) |

Workflow a 2 step:
1. **Salva come draft** → richiede validazione umana eMAG
2. **Attach offerta** → dopo validazione, attach offerte per varianti/warehouse

**Importante**: Se EAN trovato nel catalogo eMAG, skip step 1 e attach offerta direttamente al prodotto esistente.

## 6.6 Lingua

| Marketplace | Lingua default | Codice |
|-------------|---------------|--------|
| eMAG.ro | Romeno | ro_RO |
| eMAG.hu | Ungherese | hu_HU |
| eMAG.bg | Bulgaro | bg_BG |

**Lingue supportate via API**: en_GB, ro_RO, pl_PL, bg_BG, hu_HU, de_DE, **it_IT**, fr_FR, es_ES, nl_NL, cs_CZ, ru_RU, el_GR, lt_LT, sk_SK, uk_UA

**Workflow traduzione**:
- Prodotti inviati in lingua non locale → eMAG attiva processo traduzione automatica
- eMAG offre servizio traduzione a pagamento per bulk
- **Best practice**: Inviare nella lingua locale del marketplace per pubblicazione più rapida
- **Per seller italiani**: Possibile inviare in italiano (it_IT) → eMAG traduce

---

# 7. API e Integrazione Tecnica

## 7.1 Architettura API

| Parametro | Valore |
|-----------|--------|
| **Tipo** | REST-based (POST-based requests) |
| **Formato** | JSON esclusivamente |
| **Versione corrente** | v4.5.1 (dal 02.03.2026) |
| **Base URL Romania** | `https://marketplace.emag.ro/api-3/[resource]/[action]` |
| **Base URL Ungheria** | `https://marketplace.emag.hu/api-3/[resource]/[action]` |
| **Base URL Bulgaria** | `https://marketplace.emag.bg/api-3/[resource]/[action]` |
| **Autenticazione** | HTTP Basic Auth (Username + Password + Base64 hash) |
| **Piattaforma** | Proprietaria (NON Mirakl) |

## 7.2 Sicurezza e Controllo Accesso

### IP Whitelisting (OBBLIGATORIO)

| Parametro | Valore |
|-----------|--------|
| **Requisito** | Fornire lista completa IP a eMAG PRIMA di qualsiasi chiamata API |
| **Enforcement** | eMAG blocca tutte le chiamate da IP non registrati |
| **Applicazione** | Tutti gli endpoint senza eccezione |
| **Modifica** | Contattare supporto eMAG per aggiornare IP |

### Diritti API

- L'account seller deve avere diritti API esplicitamente concessi da eMAG
- Non è self-service: richiedere accesso API al supporto seller

## 7.3 Validazione Response

**Ogni response DEVE contenere** `"isError": false` — chiamate senza questo indicatore o con `"isError": true` indicano errore e richiedono alert.

## 7.4 Rate Limiting

| Parametro | Valore |
|-----------|--------|
| **Paginazione default** | 100 item per pagina |
| **Paginazione max** | 1.000 item per pagina |
| **Throttling temporale** | Presente su save requests (soglie endpoint-dependent) |
| **Invalid request throttling** | Rate limiting automatico per richieste fallite ripetute |

## 7.5 Endpoint Principali

### Product Offer Management

| Endpoint | Metodo | Scopo |
|----------|--------|-------|
| `/api-3/product_offer/save` | POST | Crea/aggiorna offerte prodotto |
| `/api-3/product_offer/read` | POST | Recupera offerte (paginazione 100-1000) |
| `/api-3/product_offer/delete` | POST | Disattiva offerte (meglio status=0) |
| `/api-3/product_offer/count` | POST | Conta offerte per filtro |

### Order Management

| Endpoint | Metodo | Scopo |
|----------|--------|-------|
| `/api-3/order/read` | POST | Recupera ordini (default ultimi 100, max 1000) |
| `/api-3/order/save` | POST | Aggiorna stato ordine |
| `/api-3/order/acknowledge/[orderId]` | POST | Conferma ricezione ordine (CRITICO) |

### Return Management (RMA)

| Endpoint | Metodo | Scopo |
|----------|--------|-------|
| `/api-3/rma/read` | POST | Recupera richieste reso |
| `/api-3/rma/save` | POST | Aggiorna stato reso |

### Shipping (AWB)

| Endpoint | Metodo | Scopo |
|----------|--------|-------|
| `/api-3/awb/[action]` | POST | Gestione etichette spedizione e tracking |

### Reference Data

| Endpoint | Metodo | Scopo |
|----------|--------|-------|
| `/api-3/category/read` | POST | Categorie prodotto (supporto multilingua: EN, RO, HU, BG, DE) |
| `/api-3/vat/read` | POST | Aliquote IVA valide per paese |
| `/api-3/handling_time/read` | POST | Tempi di gestione validi |
| `/api-3/brand/read` | POST | Brand whitelisted |

## 7.6 Sistema di Notifiche (Callback-Based)

**eMAG usa notifiche callback, NON webhook tradizionali.**

### Notifiche Ordini

| Parametro | Valore |
|-----------|--------|
| **Trigger** | Nuovo ordine → eMAG fa GET request a URL callback seller |
| **URL format** | `<tuo_sito>/emkp_new_order/index/ping?order_id=[orderId]` |
| **Retry** | 48 ore se non confermato |
| **Acknowledge** | OBBLIGATORIO: chiamare `/api-3/order/acknowledge/[orderId]` per fermare duplicati |
| **Setup** | URL callback configurato da supporto eMAG (NON self-service) |

### Notifiche Resi (RMA)

- Trigger: nuova richiesta reso o cambio stato
- Eventi notificati: New → Acknowledged → Received
- Stesso meccanismo callback degli ordini
- Retry 48 ore se non confermato

### Alternativa: Polling

Se callback URL non configurato, usare polling:
- Periodicamente chiamare `/api-3/order/read` con filtri data
- Chiamare `/api-3/rma/read` per richieste reso

## 7.7 Stati Ordine

| Codice | Stato |
|--------|-------|
| 1 | New (in attesa azione seller) |
| 2 | In Progress (in preparazione) |
| 3 | Dispatched (in transito) |
| 4 | Delivered |
| 5 | Cancelled |
| 6 | Return In Progress |
| 7 | Returned (ricevuto dal seller) |
| 8 | Refunded |

## 7.8 Stati RMA (Reso)

| Codice | Stato |
|--------|-------|
| 1 | Incomplete (attesa info aggiuntive) |
| 2 | New (inviato dal cliente) |
| 3 | Acknowledged (dal seller) |
| 4 | Refused (rifiutato dal seller) |
| 5 | Cancelled (dal cliente) |
| 6 | Received (reso fisico ricevuto) |
| 7 | Finalized (risolto/rimborsato) |

## 7.9 Feed Import (Batch)

| Parametro | Valore |
|-----------|--------|
| **Formati supportati** | XML (raccomandato), CSV ("," e ";" delimiters), TSV |
| **Processo** | Asincrono con validazione umana |
| **Tempo elaborazione** | 2-24 ore |
| **Workflow** | Settings → Mapping campi → Preview/validazione → Schedule/import |
| **Vantaggi** | Upload bulk migliaia di SKU, importazioni scheduled |
| **Limitazioni** | Non real-time, richiede validazione umana per nuovi prodotti |

## 7.10 Sandbox/Test Environment

| Parametro | Valore |
|-----------|--------|
| **Disponibilità** | Sì, ambiente test supportato |
| **Accesso** | Stessi requisiti auth e IP whitelisting |
| **Setup** | Configurazione separata da production |
| **Nota** | Non è un sandbox pubblico dedicato; testing su account pre-production |

## 7.11 Documentazione e Risorse

| Risorsa | URL |
|---------|-----|
| **API Docs ufficiale** | `marketplace.emag.ro/infocenter/emag-academy/how-to-add-a-product/product-import-through-api-or-feeds/api-documentation/?lang=en` |
| **Versione corrente** | v4.5.1 (dal 02.03.2026) |
| **Postman Collection** | `postman.com/n0b1n3d4r4/emag/documentation` (community) |
| **Python Client** | `github.com/chawel/python-emag` (community) |
| **Infocenter RO** | `marketplace.emag.ro/infocenter/emag-academy` |
| **Infocenter HU** | `marketplace.emag.hu/infocenter/emag-academy` |
| **Infocenter BG** | `marketplace.emag.bg/infocenter/emag-academy` |

---

# 8. Order Management

## 8.1 Flusso Ordine

```
Cliente ordina su eMAG
  │
  ▼
Pagamento processato (carta, COD, Allegro Pay, BNPL)
  │
  ▼
Callback notification al seller (GET request)
  │
  ▼
Seller chiama /order/acknowledge/[orderId] (CRITICO: ferma retry 48h)
  │
  ▼
Seller prepara ordine (status 2: In Progress)
  │
  ▼
Seller genera AWB e spedisce (status 3: Dispatched)
  │
  ▼
Consegna (easybox / corriere a domicilio)
  │
  ▼
Status 4: Delivered
  │
  ▼
[Opzionale] Reso entro 30 giorni (Genius: 60 giorni)
```

## 8.2 Gestione Fatture

- eMAG gestisce fatturazione al cliente
- Seller riceve payout netto commissioni
- Report finanziari disponibili nel dashboard
- Export per integrazione contabile

---

# 9. Logistica e Spedizioni

## 9.1 Fulfillment by eMAG (FBE)

| Parametro | Valore |
|-----------|--------|
| **Tipo** | Servizio FBA-like (stoccaggio, picking, packing, spedizione) |
| **Come funziona** | Seller invia prodotti a magazzino eMAG → eMAG gestisce fulfillment |
| **Transfer fee** | ~€1.59 netto per unità |
| **Processing/Picking** | ~€3.40 per unità |
| **Storage (primi 30 giorni)** | Gratuito |
| **Warehouse principale** | Budapest (investimento €100M+, 100.000+ mq) |
| **Requisito** | Seller attivo con volume sufficiente |
| **Disponibilità internazionali** | Da verificare con eMAG |

## 9.2 Corrieri Supportati

| Corriere | Paese | Integrazione |
|----------|-------|-------------|
| **Sameday** | RO, HU, BG | ✅ Proprietario eMAG, easybox lockers |
| **Fan Courier** | RO | ✅ Leader Romania |
| **DPD** | RO, HU, BG | ✅ Pan-europeo |
| **DHL** | Tutti | ✅ Internazionale |
| **GLS** | HU, BG | ✅ Regionale |
| **eMAG Logistics** | RO | ✅ Proprio servizio |

## 9.3 Sameday easybox — Fondamentale per Romania

| Parametro | Valore |
|-----------|--------|
| **easybox in Romania** | 3.900+ locazioni |
| **Tipo** | Armadietti automatici (equivalente InPost Paczkomaty) |
| **Utilizzo** | Metodo consegna preferito dai clienti eMAG |
| **Same-day delivery** | Disponibile in aree urbane |
| **Integrazione** | Nativa con eMAG, API AWB dedicata |

## 9.4 Cross-Border Shipping Italia → Romania

| Parametro | Valore |
|-----------|--------|
| **Fattibilità** | ✅ Sì (entrambi UE, no dogana) |
| **Tempi standard** | 3-5 giorni lavorativi |
| **Tempi express** | 1-2 giorni lavorativi |
| **Corrieri consigliati** | DHL, DPD, FedEx, EuroSender |

### Opzioni Logistiche per Vendiamonoi

| Opzione | Pro | Contro |
|---------|-----|--------|
| **A: Spedizione da Italia** | Nessun magazzino locale | Tempi lunghi, resi costosi, no easybox |
| **B: 3PL in Romania** | SLA rispettati, easybox compatibile | Costo per ordine |
| **C: FBE (Fulfillment by eMAG)** | Massima integrazione, Genius boost | Costo processing, P.IVA romena consigliata |
| **D: Magazzino proprio in Romania** | Controllo totale | Investimento elevato |

## 9.5 Aspettative Consegna Romania

- **Same-day**: Standard per Bucarest e città principali
- **Next-day**: Standard per aree urbane
- **2-3 giorni**: Standard per aree rurali
- **easybox**: Metodo preferito dai clienti
- **COD**: Ancora molto diffuso (30-40% ordini)

---

# 10. Resi e Rimborsi

## 10.1 Diritto di Recesso

| Parametro | Valore |
|-----------|--------|
| **Standard** | 30 giorni dalla consegna |
| **Genius** | 60 giorni dalla consegna |
| **Legge romena** | 14 giorni minimo (OG 34/2014) |
| **eMAG estende** | +16 giorni oltre il minimo legale (marketing) |
| **Condizione** | Senza motivazione |

## 10.2 Processo Reso

1. Cliente richiede reso su eMAG
2. RMA creato → notification al seller
3. Seller conferma (acknowledge)
4. Corriere ritira dal cliente (pick-up) o cliente spedisce
5. Seller riceve e verifica prodotto
6. Rimborso processato

## 10.3 Costi Reso

- **Reso standard**: Costo spedizione a carico del seller
- **Reso per difetto**: Costo a carico del seller + eventuale sostituzione
- **Pick-up**: Corriere ritira dal cliente → consegna al seller

---

# 11. eMAG Genius Program

## 11.1 Cos'è eMAG Genius

Programma di loyalty premium per acquirenti con consegna gratuita illimitata. **Equivalente di Amazon Prime per CEE.**

| Parametro | Valore |
|-----------|--------|
| **Prezzo mensile** | RON 19.90 (~€4) |
| **Prezzo annuale** | RON 99 (~€20) |
| **Trial gratuito** | 1 mese |
| **Benefici clienti** | Consegna gratuita, resi estesi 60gg, offerte speciali |
| **Impatto seller** | 5x+ frequenza acquisto, 2x+ conversion rate |

## 11.2 Tre Opzioni Genius per Seller

| Opzione | Descrizione | Requisiti |
|---------|-------------|----------|
| **Genius Standard** | Badge + buybox preference + search ranking | Rating 4.5+, <2% resi, spedizione veloce |
| **Genius Easybox** | Consegna a easybox con sconto | Stessi requisiti + partnership logistica easybox |
| **Genius Home Delivery** | Consegna a domicilio premium | Stessi requisiti + capacità home delivery |

## 11.3 Impatto sui Seller

- Clienti Genius ordinano **5x più frequentemente**
- Conversion rate **2x-4x più alto** con badge Genius
- Posizionamento preferenziale nei risultati di ricerca
- Accesso a campagne Genius-only
- **Non-negotiable**: Ottenere Genius è fondamentale per il successo su eMAG

---

# 12. Compliance e Normative

## 12.1 IVA per Paese

| Paese | Aliquota Standard | Note |
|-------|------------------|------|
| **Romania** | 19% | Standard per maggior parte beni |
| **Ungheria** | 27% | Aliquota IVA più alta in EU |
| **Bulgaria** | 20% | Standard |

### Opzioni per Seller Italiano

| Opzione | Dettaglio |
|---------|----------|
| **OSS (One Stop Shop)** | Se fatturato EU ≤ €10.000 annui per paese → dichiarazione da Italia |
| **Registrazione IVA locale** | Obbligatoria se superi soglia €10.000 in un singolo paese |
| **Rappresentante fiscale** | Consigliato per Romania (costo €200-400/anno) |

**In pratica**: Vendiamonoi supererà €10.000 rapidamente → registrazione IVA romena obbligatoria.

## 12.2 EPR — Extended Producer Responsibility

| Parametro | Valore |
|-----------|--------|
| **Romania** | Registrazione obbligatoria per imballaggi |
| **Ente** | Agenzia Nazionale per la Protezione dell'Ambiente |
| **Obbligo** | Tutti i seller che introducono imballaggi sul mercato romeno |
| **Sanzioni** | Ammende significative per non-compliance |

## 12.3 GPSR — General Product Safety Regulation

| Parametro | Valore |
|-----------|--------|
| **In vigore dal** | 13 dicembre 2024 |
| **Obblighi** | Informazioni produttore, info sicurezza, lingua locale |
| **Enforcement eMAG** | Rimozione listing non conformi |

## 12.4 Protezione Consumatore Romania

| Normativa | Dettaglio |
|-----------|----------|
| **OG 34/2014** | 14 giorni recesso (eMAG estende a 30/60) |
| **Garanzia legale** | 2 anni (standard EU) |
| **Informazione pre-acquisto** | Obbligatoria (prezzo, caratteristiche, diritti) |
| **Lingua** | Romeno obbligatorio per informazioni consumatore |

## 12.5 Checklist Compliance per Vendiamonoi

- [ ] Registrazione IVA romena + eventuale rappresentante fiscale
- [ ] Registrazione IVA ungherese (se cross-border HU)
- [ ] Registrazione IVA bulgara (se cross-border BG)
- [ ] Registrazione EPR Romania per imballaggi
- [ ] Dati GPSR per tutti i prodotti
- [ ] Informazioni consumatore in romeno
- [ ] Termini resi conformi a legge romena
- [ ] Garanzia 2 anni documentata
- [ ] Privacy policy conforme GDPR

---

# 13. Performance Metrics e KPI

## 13.1 Sistema Rating Seller

| Parametro | Valore |
|-----------|--------|
| **Scala** | 1-5 stelle |
| **Valutato da** | Rating clienti post-ordine |
| **Misura** | Soddisfazione operativa (NON qualità prodotto) |
| **Visibilità** | Pubblico sul profilo seller |

## 13.2 Componenti Valutazione

- Velocità processamento ordine
- Qualità packaging
- Velocità e accuratezza spedizione
- Facilità processo reso

## 13.3 Impatto Rating

| Rating | Stato |
|--------|-------|
| < 4.0 | Rischio delisting o aumento commissioni |
| 4.0-4.3 | Seller standard |
| 4.4-4.8 | Idoneo per Genius |
| 4.9-5.0 | Priorità per posizionamento featured |

## 13.4 KPI Monitorati nel Dashboard

- Ordini totali e revenue
- Conversion rate
- Valore medio ordine (AOV)
- Tasso resi
- Tasso cancellazioni
- Score feedback clienti
- Report NPS e KPI seller
- Performance per prodotto

---

# 14. Integrazione Channel Manager

## 14.1 ChannelEngine

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Integrazione disponibile |
| **Funzionalità** | Catalogo unificato, sync inventario, gestione ordini, pricing |
| **Nota** | eMAG sulla roadmap ChannelEngine, esperti marketplace dedicati |
| **URL** | channelengine.com/en/marketplaces/emag |

## 14.2 Channable

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Compatibile via marketplace integrator |
| **Funzionalità** | Template eMAG, ottimizzazione dati, gestione ordini multicanale |
| **Nota** | Supporta 2.500+ canali marketplace |

## 14.3 BaseLinker

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ✅ Integrazione diretta completa |
| **Funzionalità** | API diretta eMAG, invio stato ordini, fatture PDF, AWB |
| **Bonus** | Traduzione contenuti gratuita, Account Development Manager gratuito (1+3 mesi) |
| **Integrazioni extra** | emSzmal, SellPander, Zapier, eMAG AWB, Xero, Labelcall, Price.ro |
| **URL** | baselinker.com/en-US/integrations/emag/ |

## 14.4 SellRapido

| Parametro | Valore |
|-----------|--------|
| **Supporto** | ⚠️ NON documentato specificamente |
| **Nota** | Non appare nella documentazione ufficiale eMAG o ChannelEngine |
| **Raccomandazione** | Contattare SellRapido direttamente per roadmap eMAG |
| **Alternativa** | Usare ChannelEngine o BaseLinker come bridge |

## 14.5 Altri Tool Verificati

| Tool | Supporto | Note |
|------|----------|------|
| **Lengow** | ✅ | Feed syndication 200+ marketplace |
| **Omnicado** | ✅ | Multi-marketplace selling |
| **EffectConnect** | ✅ | Channel management |

## 14.6 Pattern Integrazione per Vendiamonoi

### Opzione A: ChannelEngine (Raccomandato multi-marketplace)
```
SellRapido → ChannelEngine → eMAG API (v4.5.1)
            ↕                ↕
        Supabase          Basic Auth + IP Whitelist
            ↕
       Make.com
```

### Opzione B: BaseLinker (Ottimale per focus CEE)
```
Feed prodotti → BaseLinker → eMAG API
                    ↕
              Ordini + AWB + Fatture
```

### Opzione C: API Diretta (Make.com)
```
Make.com HTTP Module → eMAG API (Basic Auth)
  │  IP Whitelisting obbligatorio
  ↕
Supabase → Callback URL per notifiche ordini
```

---

# 15. Cross-Border: Vendere in 3 Paesi

## 15.1 Programma Cross-Border eMAG

| Parametro | Valore |
|-----------|--------|
| **Scope** | Un account seller → vendita in Romania + Ungheria + Bulgaria |
| **Attivazione** | 3-5 giorni lavorativi dal dashboard seller |
| **Traduzione** | Automatica gratuita (RO→HU, RO→BG) |
| **Traduzione premium** | A pagamento per qualità superiore |

## 15.2 Come Attivare

1. Registrarsi su eMAG.ro Marketplace (onboarding standard)
2. Navigare a sezione "Cross-Border" nel dashboard
3. Selezionare Ungheria + Bulgaria come mercati espansione
4. Compilare form cross-border (indirizzo, IVA per paese)
5. Auto-traduzione prodotti o fornitura traduzioni
6. Attivare sync offerte o pricing country-specific

## 15.3 Gestione Prezzi

| Opzione | Descrizione | Uso |
|---------|-------------|-----|
| **Synchronized** | Un prezzo in EUR/RON → conversione automatica HUF/BGN | Seller passivi |
| **Country-specific** | Prezzi diversi per mercato | Seller attivi che ottimizzano |

## 15.4 IVA per Cross-Border

| Paese | IVA | eMAG gestisce |
|-------|-----|---------------|
| Romania | 19% | ✅ Raccolta e versamento |
| Ungheria | 27% | ✅ Raccolta e versamento |
| Bulgaria | 20% | ✅ Raccolta e versamento |

**Seller riceve payout netto IVA** — ma deve comunque dichiarare e registrarsi per IVA in ogni paese se supera soglia €10.000.

## 15.5 Logistica Cross-Border

| Rotta | Tempi | Costo |
|-------|-------|-------|
| Romania → Ungheria | 3-5 gg | €3-5 |
| Romania → Bulgaria | 4-7 gg | €3-5 |
| Italia → Romania | 3-5 gg | €5-10 |
| FBE automatizzato | 1-2 gg | €3.40/unità processing + storage |

---

# 16. Strategia per Vendiamonoi

## 16.1 Go/No-Go Assessment

**VERDETTO: GO (QUALIFICATO)** — eMAG rappresenta un'opportunità valida per Vendiamonoi, con alcune condizioni:

### Motivi per GO
- Marketplace dominante CEE (37.5% quota Romania)
- **ZERO costi fissi** (nessun abbonamento, nessuna listing fee)
- Commissioni competitive (5-10%)
- Seller italiano accettato direttamente (EU)
- Cross-border automatico: 1 account → 3 paesi
- Traduzione automatica gratuita
- easybox/Sameday: logistica proprietaria avanzata

### Condizioni per Successo
- Focus su fashion, beauty, home (brand italiani valorizzati)
- Ottenere Genius entro 6 mesi (2x-4x conversion)
- Accettare payout bi-mensile (cash flow più lento di Allegro)
- Investire in traduzione qualità per listing romeni

## 16.2 Barriere all'Ingresso

| Barriera | Livello | Soluzione |
|----------|---------|----------|
| Lingua romena (listing + CS) | 🔴 Alta | eMAG auto-traduce + agenzia CS locale |
| IVA romena + HU + BG | 🔴 Alta | Registrazione + Taxually/commercialista (€200-400/anno per paese) |
| IP Whitelisting API | 🟡 Media | Configurazione una tantum con supporto eMAG |
| COD diffuso (cash flow) | 🟡 Media | Accettare COD, hold period più lungo |
| Concorrenza (36K+ seller) | 🟡 Media | Genius badge + eMAG Ads |
| Valuta RON | 🟢 Bassa | Wise/Revolut per conversione favorevole |
| EPR imballaggi | 🟢 Bassa | Registrazione obbligatoria |

## 16.3 Stima Costi

### Setup (Una Tantum)

| Voce | Costo |
|------|-------|
| Registrazione eMAG | Gratuita |
| Registrazione IVA Romania | €300-600 |
| Registrazione IVA Ungheria | €300-500 |
| Registrazione IVA Bulgaria | €200-400 |
| Registrazione EPR Romania | €100-200 |
| Traduzione catalogo iniziale (50 prodotti) | €300-600 |
| Setup logistica e corrieri | €200-500 |
| **TOTALE** | **€1.400-2.800** |

### Costi Mensili

| Voce | Costo |
|------|-------|
| Abbonamento eMAG | **GRATUITO** |
| Customer service romeno (agenzia) | €500-1.000 |
| Traduzione nuovi prodotti | €100-300 |
| eMAG Ads | €300-500 |
| Compliance VAT (3 paesi, software) | €150-300 |
| **TOTALE** | **€1.050-2.100** |

## 16.4 Simulazione Costi — Prodotto Home €50

| Voce | Importo EUR |
|------|------------|
| Prezzo vendita (IVA 19% inclusa per RO) | €50.00 |
| IVA romena (19%) | -€7.98 |
| Commissione eMAG (5% hardlines su netto) | -€2.10 |
| **Ricavo netto** | **€39.92** |
| Conversione RON→EUR (~1.5-2% fee) | -€0.80 |
| **Ricavo in EUR** | **€39.12** |
| Costo prodotto | -€25.00 |
| Spedizione IT→RO (DHL) | -€5-8 |
| **Margine netto** | **€6-9 per unità** |
| **Margine %** | **12-18%** |

**Con FBE (Fulfillment by eMAG)**:
- Processing aggiuntivo: -€3.40/unità
- Ma spedizione inclusa
- **Margine netto FBE**: €4-7/unità (8-14%)

## 16.5 Categorie Strategiche per Brand Italiani

| Categoria | Commissione | Fit IT Brand | Opportunità |
|-----------|------------|-------------|-------------|
| **Fashion & Apparel** | 10% | ⭐⭐⭐ | "Made in Italy" premium, alta domanda CEE |
| **Beauty & Cosmetics** | 10% | ⭐⭐⭐ | Italia = 67% produzione makeup EU, brand premium |
| **Home & Kitchen** | 5% | ⭐⭐⭐ | Design italiano, commissione bassa, alta domanda |
| **Gioielli** | 10% | ⭐⭐ | Artigianato italiano valorizzato |
| **Elettronica** | 5% | ⭐ | Mercato saturato, margini sottili |

## 16.6 Confronto eMAG vs Allegro vs Otto

| Fattore | eMAG | Allegro | Otto |
|---------|------|---------|------|
| **Accessibilità seller IT** | ✅ S.R.L. diretta | ✅ S.R.L. diretta | ❌ Richiede entità DE |
| **Costi fissi** | ZERO | PLN 49-3.000/mese | €99.90/mese |
| **Commissioni** | 5-10% | 4-15% | 7-18% |
| **Payout** | Bi-mensile | Giornaliero | Settimanale |
| **API rate limit** | Endpoint-dependent | 150 req/sec | 20 req/sec |
| **Setup cost** | €1.400-2.800 | €900-2.000 | €3.200-13.500 |
| **Costi mensili** | €1.050-2.100 | €1.150-2.250 | €2.650-6.300 |
| **Mercati** | RO + HU + BG (3) | PL + CZ/SK/HU/SI/HR (6) | DE + NL (2) |
| **Lingua** | Romeno (+HU/BG) | Polacco | Tedesco |
| **Fulfillment** | FBE (FBA-like) | Allegro One | No FBA |
| **Loyalty program** | Genius (5x) | Smart! (5x) | No |

## 16.7 Timeline Ingresso Raccomandato

| Fase | Periodo | Azione |
|------|---------|--------|
| 1 | Settimane 1-4 | Registrazione eMAG.ro, IVA Romania, EPR, setup logistica |
| 2 | Settimane 5-8 | Lista 50-100 SKU hero, test fulfillment, €300 eMAG Ads |
| 3 | Settimane 9-16 | Target Genius (4.5+ rating, <2% resi), scale a 80-100 unità/mese |
| 4 | Settimane 17-20 | Attivazione Cross-Border (HU + BG), traduzioni, €600/mese ads |
| 5 | Settimane 21-26 | Ottimizzazione top SKU, eventuale FBE, 200+ unità/mese |
| **Totale** | **6 mesi** | Dal via alla presenza profittevole in 3 paesi |

## 16.8 Proiezioni Finanziarie Anno 1

| Periodo | Unità/Mese | Prezzo Medio | Revenue Lordo | Commissione | Revenue Netto | Costi Ops | Profitto Netto |
|---------|-----------|-------------|--------------|-------------|--------------|-----------|---------------|
| Mese 1-2 (Prep) | 0 | — | €0 | — | €0 | €1.500 | -€1.500 |
| Mese 3-4 (Soft Launch) | 25 | €45 | €1.125 | ~9% | €1.024 | €1.500 | -€476 |
| Mese 5-8 (Ramp-Up) | 50 | €50 | €2.500 | ~8% | €2.288 | €2.000 | +€288 |
| Mese 9-12 (Genius) | 100 | €52 | €5.200 | ~8% | €4.784 | €3.500 | +€1.284 |

**Break-even**: Mese 8-9
**Profitto Anno 1**: ~€8.000-10.000 (conservativo)
**Anno 2+ con Cross-Border**: €200.000-300.000 potenziale (300+ unità/mese × 3 paesi)

---

## FONTI E RIFERIMENTI

### Documentazione Ufficiale eMAG
- [eMAG Marketplace Infocenter Romania](https://marketplace.emag.ro/infocenter/?lang=en)
- [eMAG Marketplace Infocenter Hungary](https://marketplace.emag.hu/infocenter/?lang=en)
- [eMAG Marketplace Infocenter Bulgaria](https://marketplace.emag.bg/infocenter/?lang=en)
- [eMAG API Documentation v4.5.1](https://marketplace.emag.ro/infocenter/emag-academy/how-to-add-a-product/product-import-through-api-or-feeds/api-documentation/?lang=en)
- [eMAG Image Standards](https://marketplace.emag.ro/infocenter/emag-academy/how-to-add-a-product/the-main-product-image-standard/?lang=en)
- [eMAG Genius Program](https://marketplace.emag.ro/infocenter/emag-academy/emag-genius/?lang=en)
- [eMAG Taxes & Commissions](https://marketplace.emag.ro/infocenter/start-selling/taxes-and-commissions/?lang=en)
- [eMAG Payouts](https://marketplace.emag.ro/infocenter/emag-academy/financial/payouts/?lang=en)
- [eMAG Cross-Border](https://marketplace.emag.ro/infocenter/grow-your-business/cross-border/?lang=en)
- [eMAG VAT Registration Guide](https://marketplace.emag.ro/infocenter/emag-academy/cross-border/vat-registration-for-selling-products-in-the-eu-territory/?lang=en)
- [eMAG Seller Rating](https://marketplace.emag.ro/infocenter/emag-academy/seller-rating/?lang=en)

### API & Technical
- [eMAG Postman Collection](https://www.postman.com/n0b1n3d4r4/emag/documentation)
- [Python eMAG Client (GitHub)](https://github.com/chawel/python-emag)

### Integrazioni
- [ChannelEngine eMAG](https://www.channelengine.com/en/marketplaces/emag)
- [BaseLinker eMAG](https://baselinker.com/en-US/integrations/emag/)
- [Channable Integrations](https://www.channable.com/integrations)

### Analisi Mercato
- [Prosus FY2025 Results](https://www.prosus.com/news-insights/group-updates/2025/prosus-accelerates-growth-and-profitability-with-12x-improvement)
- [Statista — eMAG Statistics](https://www.statista.com/topics/12219/emag/)
- [eMAG Hungary Sellers Day 2025](https://marketplace.emag.hu/infocenter/emag-academy/emag-sellers-day-2025/?lang=en)
- [eMAG Bulgaria Open Bulgaria Program](https://marketplace.emag.bg/infocenter/open-bulgaria/?lang=en)
- [ShopiVerse eMAG Guide](https://shopiverse.com/emag-marketplace-guide/)

### Compliance
- [Romanian VAT 2025](https://www.numeral.com/blog/romania-vat-rates-and-compliance)
- [Sameday easybox Network](https://www.sameday.ro/easybox)

---

**Documento creato**: 2026-04-07
**Ricerca**: 3 agenti paralleli (seller/business, API/technical, logistics/compliance)
**Complemento**: marketplace-specs/otto, marketplace-specs/allegro, marketplace-specs/amazon
**Prossimo**: marketplace-specs/conrad (P2.4)
