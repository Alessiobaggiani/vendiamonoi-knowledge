# Specifica Marketplace BricoBravo
## Guida Completa per Venditori e Distributori Digitali

---

## 1. Informazioni Generali

| Parametro | Valore |
|-----------|--------|
| **Nome Marketplace** | BricoBravo |
| **Sito Principale** | bricobravo.com / www.bricobravo.it |
| **Paese Target Primario** | Italia |
| **Paesi Target Secondari** | Francia, Spagna, Germania (espansione futura) |
| **Categoria Merceologica** | Home Improvement, DIY, Ferramenta, Giardino, Riscaldamento |
| **Tipo di Marketplace** | C2C / B2C con seller professionali |
| **Modello di Vendita** | Marketplace a commissioni |
| **Tipologia Piattaforma** | Proprietaria / Mirakl (compatibile) |
| **Anno Fondazione** | 2015 (circa) |
| **Mercato di Riferimento** | Fascia media-alta, fai-da-te professionisti e hobbisti |

---

## 2. Modello di Business

### 2.1 Posizionamento nel Mercato

BricoBravo è un marketplace italiano specializzato in bricolage, fai-da-te e articoli per la casa. Nasce dall'esigenza del mercato italiano di una piattaforma dedicata alle categorie di home improvement con elevate competenze specifiche e customer service locale.

Il marketplace si posiziona tra i competitor nazionali come eBay.it, Amazon.it (sezione home), Leroy Merlin online e realtà più piccole, offrendo:
- Specializzazione verticale nel settore bricolage/fai-da-te
- Prezzi competitivi grazie alla disintermediazione
- Supporto locale in lingua italiana
- Expertise nel settore specifico

### 2.2 Categorie di Prodotto Principali

1. **Bricolage** (12-15% commissione)
   - Attrezzi manuali (martelli, cacciaviti, pinze)
   - Trapani, avvitatori, utensili elettrici
   - Accessori per utensili
   - Piccoli utensili specializzati

2. **Ferramenta** (12% commissione)
   - Viti, bulloni, dadi, rondelle
   - Chiavistelli, cerniere, serrature
   - Cancelletti, ringhiere, grate
   - Accessori metallici generici

3. **Giardino** (12% commissione)
   - Attrezzi da giardino (vanghe, rastrelli, cesoie)
   - Sementi, terriccio, concimi
   - Arredo giardino (tavoli, sedie, gazebo)
   - Barbecue, stufe esterne, riscaldatori da giardino
   - Illuminazione esterna

4. **Riscaldamento** (10-12% commissione)
   - Stufe a legna, pellet, gas
   - Caldaie, termosifoni
   - Radiatori elettrici
   - Accessori e ricambi

5. **Bagno** (12% commissione)
   - Sanitari (wc, bidet, lavabi)
   - Vasche, docce, piatti doccia
   - Rubinetteria
   - Accessori bagno, specchi, mensole

6. **Illuminazione** (12-15% commissione)
   - Lampade, plafoniere, faretti
   - Sistemi LED
   - Lampade da parete, da tavolo, da terra
   - Illuminazione esterna

7. **Climatizzazione** (10-12% commissione)
   - Condizionatori, pompe di calore
   - Ventilatori, umidificatori
   - Deumidificatori
   - Accessori e ricambi

8. **Arredo Giardino & Esterno** (12% commissione)
   - Tavoli, sedie, panche
   - Gazebo, pergolati
   - Amache, sdraio
   - Cuscini e tessuti outdoor

### 2.3 Strategia di Crescita

- **Q1-Q2**: Crescita primavera nel segmento giardino
- **Q3-Q4**: Picco autunnale per riscaldamento, climatizzazione, e preparazione invernale
- **Tutto l'anno**: Ferramenta, bricolage, illuminazione

---

## 3. Piattaforma Tecnica

### 3.1 Infrastruttura

BricoBravo utilizza una piattaforma proprietaria basata su tecnologie moderne compatibile con standard internazionali:
- Database relazionali per gestione catalogo
- API RESTful per integrazioni
- Feed XML/CSV supportati
- Sistema di pagamento PCI-DSS compliant

### 3.2 Metodi di Integrazione Seller

**Opzione 1: Feed XML/CSV (Consigliato per cataloghi grandi)**
- Upload periodico via SFTP
- Frequenza: giornaliera, settimanale, oraria
- Schema XSD fornito da BricoBravo
- Inclusi: SKU, EAN, titolo, descrizione, immagini, prezzo, stock, attributi

**Opzione 2: API REST**
- Endpoint per product catalog management
- Real-time stock updates
- Order management
- Documentazione OpenAPI disponibile
- Rate limit: 1000 req/min

**Opzione 3: Seller Portal Web**
- Interfaccia per inserimento manuale
- Ideale per seller piccoli
- Dashboard analitica integrata

**Opzione 4: ChannelEngine Integration**
- Partner ufficiale di BricoBravo
- Gestione centralizzata multi-marketplace
- Sincronizzazione automatica stock/prezzo

### 3.3 Seller Portal Features

- **Catalogo**: Gestione prodotti, varianti, attributi
- **Ordini**: Dashboard ordini, gestione fulfillment
- **Analytics**: Sales, visite, conversion rate, ROI per categoria
- **Finanza**: Rendiconti, incassi, commissioni dettagliate
- **Comunicazione**: Messaggi clienti, supporto ticket
- **Reports**: Export dati storici, trend analysis

---

## 4. Catalogo Prodotti

### 4.1 Attributi Obbligatori

**Base Information:**
- SKU (Stock Keeping Unit) - univoco per seller
- EAN/GTIN-13 - obbligatorio se disponibile
- Titolo prodotto (max 80 caratteri)
- Descrizione (max 3000 caratteri)
- Categoria primaria + categorie secondarie
- Immagine principale (obbligatoria)

### 4.2 Attributi Specifici per Categoria

**Bricolage/Utensili:**
- Tipo utensile (trapano, avvitatore, etc.)
- Voltaggio (V)
- Potenza (W)
- Batteria inclusa (sì/no)
- Marca
- Garanzia (mesi)
- Peso
- Dimensioni (L x P x A in cm)

**Riscaldamento:**
- Tipo (stufa a legna, pellet, gas, caldaia, radiatore)
- Potenza termica (kW)
- Efficienza energetica (%)
- Classe energetica (EU)
- Certificazione CE
- Voltaggio (se elettrico)
- Combustibile (legna, pellet, gas naturale, etc.)
- Controllo remoto incluso

**Giardino:**
- Materiale principale (legno, metallo, plastica, etc.)
- Dimensioni (L x P x A)
- Peso massimo supportato
- Colore
- Impermeabile/UV resistente
- Facile montaggio (sì/no)

**Bagno:**
- Materiale (ceramica, acciaio inox, etc.)
- Colore
- Dimensioni
- Installazione richiesta (sì/no)
- Certificazione europea

**Illuminazione:**
- Tipo lampadina (LED, alogena, fluorescente)
- Wattaggio equivalente
- Temperatura colore (K)
- Lumens
- Connettori (E27, E14, GU10, etc.)
- Durata vita (ore)
- Smart/WiFi (sì/no)

### 4.3 Immagini Prodotto

**Requisiti:**
- Formato: JPG, PNG
- Risoluzione minima: 1000x1000px
- Massimo: 5000x5000px
- File size: max 5MB
- Background: preferibilmente bianco per primo piano
- Minimo 1 immagine (obbligatoria), massimo 12 immagini

**Best Practice:**
- Prima immagine: prodotto in primo piano
- Immagini 2-3: dettagli, particolari
- Immagini 4-5: in contesto d'uso
- Immagini 6+: packaging, istruzioni, certificazioni

### 4.4 Tassonomia Categorie

```
Bricolage
├── Attrezzi Manuali
├── Utensili Elettrici
├── Accessori Utensili
└── Utensili Specializzati

Ferramenta
├── Viti e Bulloni
├── Serrature
├── Accessori Metallici
└── Cancelletti e Ringhiere

Giardino
├── Attrezzi Giardino
├── Piante e Terreno
├── Arredo Giardino
└── Riscaldatori Esterni

Riscaldamento
├── Stufe
├── Caldaie
├── Radiatori
└── Accessori

Bagno
├── Sanitari
├── Rubinetteria
├── Vasche e Docce
└── Accessori

Illuminazione
├── Lampade Interne
├── Illuminazione Esterna
├── Sistemi LED
└── Accessori

Climatizzazione
├── Condizionatori
├── Pompe Calore
├── Ventilatori
└── Accessori

Arredo Giardino
├── Mobili Giardino
├── Gazebo e Pergolati
└── Tessuti Outdoor
```

---

## 5. Offer Management

### 5.1 Pricing Strategy

**Modelli di Prezzo:**
- **Prezzo di Listino (RRP)**: Prezzo consigliato produttore
- **Prezzo di Vendita**: Prezzo effettivo BricoBravo
- **Sconto %**: Automaticamente calcolato
- **Prezzo Originale** (opzionale): Per mostrare risparmio

**Regole di Pricing:**
- Aggiornamenti real-time via API
- Modifiche via feed giornaliero
- Prezzo minimo: deve coprire commissioni + costi
- Competitività: benchmark con competitor

### 5.2 Stock Management

**Livelli di Stock:**
- Stock disponibile (quantità in magazzino)
- Stock riservato (ordini in elaborazione)
- Stock threshold (avviso basso)
- Stock minimo (stop vendita se raggiunto)

**Sincronizzazione:**
- Real-time API: aggiornamento immediato
- Feed giornaliero: update massivi
- Durata cache: 1-2 ore
- Esaurimento: prodotto rimosso automaticamente se stock = 0

### 5.3 Spedizione

**Configurazione:**
- Metodo di spedizione primario
- Costi di spedizione (tabelle per peso/destinazione)
- Tempi di consegna stimati
- Spedizione gratuita (soglia minimo ordine)

**Regioni Italiane:**
- Nord (Piemonte, Lombardia, Veneto, Friuli, Emilia, Toscana): 2-3 giorni
- Centro (Marche, Umbria, Lazio, Abruzzo): 3-4 giorni
- Sud e Isole (Campania, Puglia, Sicilia, Sardegna): 4-5 giorni

**Prodotti Speciali:**
- Stufe/Climatizzatori: spedizione dedicata, consegna con posizionamento
- Articoli pesanti (>30kg): ritiro con trasportatore specializzato
- Materiali fragili: imballaggio rafforzato obbligatorio

### 5.4 Condizioni Prodotto

- **Nuovo**: Non utilizzato, imballaggio originale intatto
- **Come Nuovo**: Aperto ma non utilizzato
- **Buone Condizioni**: Utilizzato, piccoli segni usura
- **Discrete Condizioni**: Utilizzato, usura visibile

---

## 6. Order Lifecycle

### 6.1 Stati Ordine

1. **Pending** - Ordine in attesa di accettazione seller
2. **Accepted** - Seller ha accettato, preparazione iniziata
3. **Ready to Ship** - Pacco pronto per ritiro corriere
4. **Shipped** - Pacco in transito
5. **Delivered** - Consegnato a destinatario
6. **Returned** - Iniziato processo reso
7. **Return Delivered** - Reso ricevuto da seller
8. **Cancelled** - Ordine annullato

### 6.2 Workflow Accettazione

**SLA Accettazione:**
- Max 24 ore per accettare/rifiutare ordine
- Rifiuto: motivo obbligatorio (stock esaurito, indirizzo invalido, etc.)
- Auto-accettazione dopo 24h se non gestito manualmente

**Motivazioni Rifiuto Consentite:**
- Stock non disponibile
- Indirizzo non raggiungibile
- Cliente ha richiesto cancellazione
- Problemi di pagamento
- Violazione policy

### 6.3 Scadenze Spedizione

- **Entro 24h**: Accettazione ordine
- **Entro 48h**: Uscita da magazzino/ritiro corriere
- **Entro SLA comunicato**: Consegna in base a zona geografica
- **Penali**: Mancato rispetto = sospensione lista, deduzione commissioni

---

## 7. Fulfillment

### 7.1 Logistica Italiana

**Corrieri Abilitati:**
- SDA (Poste Italiane)
- UPS
- DHL
- Bartolini
- GLS
- Corrieri regionali locali

**Packaging Standards:**
- Scatola robusta (ondulato minimo 3-5 mm)
- Materiale ammortizzante (polistirolo, bolle, carta)
- Etichetta spedizione protetta da plastica
- Peso massimo: 30kg per spedizione standard

### 7.2 Delivery Bulky Items

**Articoli Pesanti (>20kg):**
- Stufe, caldaie, radiatori, climatizzatori
- Arredi giardino grandi
- Lavabi, vasche, piatti doccia

**Requisiti:**
- Corriere specializzato per articoli voluminosi
- Ritiro in magazzino (non consegna al corriere generico)
- Possibilità di posizionamento in loco (+€20-50)
- Montaggio (su richiesta, a pagamento +€50-150)
- Foto consegna richiesta (prova consegna)

### 7.3 Imballaggio Specifico

**Stufe/Climatizzatori:**
- Imballaggio doppio se nuovi
- Protezione angoli con angolari cartone
- Supporti interni per evitare movimenti
- Etichetta "Fragile" e "Basso"

**Sanitari Ceramica:**
- Involucro singolo per ogni pezzo
- Cartone doppio
- Materiale ammortizzante abbondante
- Etichetta di avvertimento

**Arredo Giardino:**
- Protezione metallo da ossidazione (pellicola)
- Supporti per evitare danni trasporto
- Se smontabile: istruzioni montaggio incluse

---

## 8. Returns & Claims

### 8.1 Politica Resi

**Diritto di Recesso (Consumatore):**
- 14 giorni da ricezione (Diritto UE 2011/83/UE)
- Prodotto in condizioni per essere rivenduto
- Eccezioni: prodotti su misura, articoli igienici aperti

**Tempistiche:**
- Cliente comunica reso entro 14 giorni
- Invia merce indietro entro 30 giorni
- Seller accetta reso entro 5 giorni da ricezione
- Rimborso entro 14 giorni da accettazione reso

**Costi:**
- Client paga ritorno (salvo difetto/errore seller)
- Seller rimborsa importo intero se accetta reso

### 8.2 Resi per Difetti

**Responsabilità Seller:**
- Prodotto danneggiato in spedizione
- Prodotto non conforme a descrizione
- Prodotto defettoso

**SLA:**
- Cliente comunica entro 8 giorni da consegna
- Seller rimborsa + ritiro gratuito merce
- Rimborso totale (incluso spedizione originale)

### 8.3 Dispute & Claims

**Processo:**
1. Cliente apre claim su piattaforma (max 30 giorni da consegna)
2. Seller ha 5 giorni per rispondere
3. Mediatore BricoBravo interviene se non risolto
4. Decisione finale entro 15 giorni

**Motivi Claim:**
- Merce non ricevuta
- Prodotto danneggato/difettoso
- Non conforme a descrizione
- Numero ordine errato
- Rifiuto di rifiuto ingiustificato

---

## 9. Commissione Structure

### 9.1 Commissioni per Categoria

| Categoria | Commissione | Note |
|-----------|-------------|------|
| Bricolage | 12-15% | Maggiore per brand noti |
| Ferramenta | 12% | Fisso |
| Giardino | 12% | Picco primavera/estate |
| Riscaldamento | 10-12% | Picco autunno/inverno |
| Bagno | 12% | Fisso |
| Illuminazione | 12-15% | Variabile per tipo |
| Climatizzazione | 10-12% | Fisso |
| Arredo Giardino | 12% | Fisso |

### 9.2 Fee Aggiuntivi

- **Fee Pagamento**: 0% (assorto da BricoBravo)
- **Fee Fulfillment**: Incluso nel modello base
- **Fee Promozione**: Opzionale, variabile (5-20%)
- **Fee Sospensione**: €50 per violazione policy

### 9.3 Calcolo Commissione

```
Commissione = (Prezzo Vendita - Costi Spedizione) × % Commissione Categoria
```

Esempio:
- Prezzo vendita: €100
- Costo spedizione: €5
- Categoria: 12%
- Commissione: (100 - 5) × 12% = €11.40

---

## 10. Performance Standards

### 10.1 Metriche Seller

**KPI Monitorati:**
- **On-Time Shipment Rate**: Min 95% (spedizione entro SLA)
- **Order Defect Rate**: Max 1% (resi + claim + inattivi)
- **Customer Response Rate**: Min 80% (messaggi entro 24h)
- **Return Acceptance Rate**: Min 90% (resi legittimi accettati)

### 10.2 Seller Rating

**Scala 1-5 Stelle:**
- 5 stelle: Eccellente (95%+ KPI)
- 4 stelle: Buono (85-94% KPI)
- 3 stelle: Accettabile (75-84% KPI)
- 2 stelle: Critico (60-74% KPI) - Avvertimento
- 1 stella: Fallimento (<60% KPI) - Sospensione

**Impatto Rating:**
- Visibilità ricerca prodotto
- Trust & credibilità acquirenti
- Accesso a programmi di promozione premium

### 10.3 Shipping SLA

- **Nord Italia**: 2-3 giorni lavorativi
- **Centro Italia**: 3-4 giorni lavorativi
- **Sud Italia**: 4-5 giorni lavorativi
- **Isole**: 5-7 giorni lavorativi

**Penali:**
- Mancato rispetto SLA: deduzione 1-3% commissione
- Sospensione temporanea listini dopo 3 violazioni in 30 giorni

---

## 11. Finanza & Settlement

### 11.1 Fatturazione Elettronica (FE)

**Normativa Italiana:**
- Obbligatoria per B2B da gennaio 2019
- Obbligatoria per tutti da gennaio 2024
- Standard XML (FatturaPA)
- Codice Destinatario o PEC

**BricoBravo:**
- Emette FE automatica per ordini
- Dati seller necessari: Partita IVA, Indirizzo, PEC
- Report mensile in formato XML scaricabile

### 11.2 IVA & Tassazione

**Aliquote Italiane Standard:**
- 22% IVA standard (maggioranza prodotti)
- 10% IVA ridotta (alcuni articoli bagno, energia)
- 5% IVA super-ridotta (raro per categorie BricoBravo)

**Responsabilità:**
- Seller responsabile corretta categorizzazione IVA
- BricoBravo applica IVA sulla commissione
- Seller deve autoregolarsi con Agenzia Entrate

### 11.3 Settlement & Pagamenti

**Ciclo Pagamenti:**
- Liquidazione mensile
- Rimborso entro il 15 del mese successivo a vendita
- Trattenuta possibile per dispute in corso
- Metodo: Bonifico bancario IBAN

**Trattenute Possibili:**
- Dispute non risolte a favore acquirente
- Violazioni policy
- Chargeback/storni pagamento
- Resi non autorizzati

**Prospetto Liquidazione:**
```
Periodo: 01-30 Aprile
Ordini Completati: 50 ordini
Totale Vendite: €2.500
Commissioni (12%): -€300
Spese Promozione: -€50
Tratenute Dispute: -€100
Netto Liquidazione: €2.050
Data Pagamento: 15 Maggio
```

---

## 12. Integration Patterns

### 12.1 Feed XML

**Struttura Base:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<catalog seller_id="SELLER001" timestamp="2026-04-01T10:00:00Z">
  <product>
    <sku>SKU001</sku>
    <ean>5901234567890</ean>
    <title>Trapano Percussione 900W</title>
    <description>Trapano con funzione percussione...</description>
    <category_id>bricolage_utensili_elettrici</category_id>
    <price currency="EUR">89.99</price>
    <original_price currency="EUR">129.99</original_price>
    <stock>15</stock>
    <image_url>https://cdn.seller.com/product1.jpg</image_url>
    <attributes>
      <attribute name="potenza_w">900</attribute>
      <attribute name="voltaggio_v">220</attribute>
      <attribute name="marca">SellerBrand</attribute>
      <attribute name="garanzia_mesi">24</attribute>
    </attributes>
    <shipping_weight_kg>2.5</shipping_weight_kg>
    <shipping_cost_eur>5.99</shipping_cost_eur>
  </product>
</catalog>
```

### 12.2 API REST Endpoints

**Autenticazione:**
```
Bearer Token: Authorization: Bearer YOUR_API_KEY
```

**Endpoint Principali:**

- `POST /api/v1/products` - Crea prodotto
- `PUT /api/v1/products/{sku}` - Aggiorna prodotto
- `GET /api/v1/products/{sku}` - Recupera dettagli
- `DELETE /api/v1/products/{sku}` - Elimina prodotto
- `POST /api/v1/inventory/{sku}` - Aggiorna stock
- `GET /api/v1/orders` - Lista ordini
- `PUT /api/v1/orders/{order_id}/status` - Aggiorna stato ordine

### 12.3 ChannelEngine Integration

**Partner Ufficiale di BricoBravo:**

BricoBravo è integrato con ChannelEngine, permettendo ai seller di:
- Sincronizzare catalogo con 20+ marketplace da un'unica piattaforma
- Gestione stock centralizzata
- Ordini unificati
- Reporting aggregato

**Setup:**
1. Registrazione ChannelEngine
2. Connessione account BricoBravo
3. Mappatura categorie
4. Sincronizzazione iniziale
5. Monitoraggio KPI

---

## 13. Compliance & Normative

### 13.1 CE Marking

**Prodotti Soggetti:**
- Utensili elettrici (EN 60745)
- Climatizzatori (EN 14511)
- Caldaie (EN 297, 298, 677)
- Elettrodomestici (EN 60335)

**Responsabilità Seller:**
- Certificato di conformità incluso in documentazione
- Dichiarazione di Conformità UE
- Istruzioni uso in italiano

### 13.2 Energy Efficiency

**Normativa EU 2017/1369:**
- Etichetta energetica obbligatoria (A+++ a D)
- Per: Frigoriferi, lavatrici, caldaie, condizionatori, stufe

**Dati Richiesti su BricoBravo:**
- Classe energetica
- Consumo annuale (kWh/anno)
- Rumorosità (dB)
- Dimensioni

### 13.3 Product Safety

**Obblighi Generali:**
- Prodotti sicuri per consumatore
- Conformità a norme specifiche categoria
- Istruzioni chiare in italiano
- Contatti importatore/produttore visibili

**Prodotti Specifici:**

**Stufe/Caldaie:**
- Certificazione INAIL
- Test emissioni (EN 13229, EN 303-5)
- Libretto caldaia (se applicabile)
- Assicurazione responsabilità civile

**Illuminazione:**
- Conformità EN 60598
- RoHS Directive 2011/65/EU
- REACH compliance

**Climatizzatori/Pompe Calore:**
- Etichetta energetica obbligatoria
- Freon certificato (non piratato)
- Certificazione frigorigeno

### 13.4 Consumer Rights

**Direttiva 2011/83/UE (Consumo a Distanza):**
- Diritto di recesso 14 giorni
- Informazioni pre-contrattuale chiare
- Condizioni di ritorno esplicite
- Costi spedizione trasparenti

**Codice Consumo Italiano:**
- Garanzia legale 24 mesi per vizi di conformità
- Seller responsabile per 2 anni da acquisto
- Spese riparazione/sostituzione a carico seller

---

## 14. Best Practices

### 14.1 Italian DIY Market Insights

**Trend di Mercato:**
- Crescente fai-da-te homeowner (COVID boom)
- Sustainability: prodotti eco-friendly
- Smart home integration
- Personalizzazione/customizzazione

**Opportunità:**
- Bundle prodotti (es: trapano + accessori)
- Video tutorial/assembly guides
- Consulenza pre-vendita (chat, email)
- Programmi fedeltà

### 14.2 Seasonal Strategies

**Primavera (Marzo-Maggio):**
- Picco: Giardino, arredo esterno
- Prodotti: Semine, arredo, strumenti giardino
- Strategie: Foto lifestyle, bundle giardino, promozioni volume

**Estate (Giugno-Agosto):**
- Picco: Illuminazione esterna, riscaldatori giardino
- Prodotti: Gazebo, barbecue, sistemi illuminazione
- Strategie: Video dimostrazione, guide montaggio

**Autunno (Settembre-Novembre):**
- Picco: Riscaldamento, climatizzazione
- Prodotti: Stufe, caldaie, radiatori
- Strategie: Certificazioni energetiche, confronto consumi, promozioni pre-invernali

**Inverno (Dicembre-Febbraio):**
- Picco: Riscaldamento, manutenzione
- Prodotti: Accessori stufe, ricambi, isolamento
- Strategie: Bundle risparmio energetico, guide manutenzione

### 14.3 Titoli Prodotto Efficaci

**Formula Vincente:**
```
[Marca] [Modello] [Specifica Principale] [Valore Aggiunto]
```

**Esempi:**

Cattivo:
- "Trapano"
- "Utensile"

Buono:
- "Trapano Percussione 900W DeWalt DCD777 con 2 Batterie"
- "Stufa Pellet 12kW Arietta 2.0 Scaldamento Casa 150mq"
- "Tavolo Giardino Alluminio 150x90cm Antracite"

### 14.4 Descrizione Prodotto

**Struttura Consigliata:**
1. **Punto forte** (1-2 righe)
2. **Specifiche tecniche** (tabella)
3. **Caratteristiche distintive** (bullet points)
4. **Utilizzi/Applicazioni** (3-4 esempi)
5. **Contenuto confezione**
6. **Garanzia/Supporto**

**Esempio:**

"Trapano Percussione 900W - Potente e Versatile

SPECIFICHE:
- Potenza: 900W
- RPM: 3000 (senza carico)
- Perforazione: Legno 20mm, Metallo 10mm, Cemento 8mm
- 2 Batterie 18V 2.0Ah incluse
- Caricabatterie rapido 30min
- Garanzia: 24 mesi

CARATTERISTICHE:
- Doppia velocità per diversi materiali
- Mandrino autocentrante 13mm
- Cinghia spalla per lavori in verticale
- LED illuminazione area lavoro
- Custodia trasporto inclusa

UTILIZZI:
- Realizzare fori in legno, metallo, muratura
- Avvitare/svitare viti
- Montaggio mobili, librerie, scaffali
- Lavori di ristrutturazione

CONTENUTO CONFEZIONE:
- Trapano percussione
- 2 batterie 18V 2.0Ah
- Caricabatterie rapido
- Set 40 punte/inserti
- Cinghia spalla
- Custodia

GARANZIA: 24 mesi
SUPPORTO: servizio clienti telefonico gratuito"

### 14.5 Fotografía & Video

**Best Practice:**
- Foto principale: prodotto su fondo bianco
- Foto dettagli: zoom su features chiave
- Foto uso: prodotto in ambiente realistico
- Video: 30-60 sec demo funzionamento
- Lifestyle: persona che usa il prodotto

**Impatto:**
- Foto di qualità = +15-20% conversion
- Video = +25-35% engagement
- Reviews con foto = +40% credibilità

---

## 15. Risorse & Contatti

### 15.1 Link Ufficiali

| Risorsa | URL |
|---------|-----|
| Sito Principale | www.bricobravo.com / bricobravo.it |
| Seller Hub | seller.bricobravo.com / seller.bricobravo.it |
| API Documentation | api.bricobravo.com/docs |
| Help Center | help.bricobravo.com |
| Community Forum | forum.bricobravo.com |
| Twitter Support | @BricoBravo_it |
| Instagram | @BricoBravofficial |

### 15.2 Seller Portal

**Accesso:**
- URL: https://seller.bricobravo.it
- Metodo: Email + Password / SSO
- Supporto Account: support@bricobravo.it

**Sezioni Principali:**
- Dashboard (overview KPI)
- Catalogo Prodotti
- Gestione Ordini
- Analytics & Reports
- Impostazioni Account
- Fatturazione
- Messaggi Clienti
- Ticket Supporto

### 15.3 Documentazione Tecnica

**Feed XML Schema:**
- https://api.bricobravo.com/schema/product-feed-v2.xsd
- Validatore online: validator.bricobravo.com

**API Reference:**
- Swagger/OpenAPI: https://api.bricobravo.com/docs
- Postman Collection: https://api.bricobravo.com/postman

**Integrazioni:**
- ChannelEngine: https://channelengine.com
- Mirakl Compatibility: Supporto nativo

### 15.4 Contatti Supporto

| Team | Contatto | Ore |
|------|----------|-----|
| Supporto Seller | support@bricobravo.it | Lun-Ven 09:00-18:00 CET |
| Onboarding | onboarding@bricobravo.it | Lun-Ven 10:00-17:00 CET |
| Technical Support | tech-support@bricobravo.it | Lun-Ven 09:00-18:00 CET |
| Account Manager | accounts@bricobravo.it | Su appuntamento |
| Compliance | compliance@bricobravo.it | Lun-Ven 10:00-16:00 CET |
| Telefono | +39 02 XXXX XXXX | Lun-Ven 10:00-18:00 CET |

### 15.5 Risorse Utili per Seller

**Programmi di Crescita:**
- Seller Essentials (gratuito): Guide base, best practices
- Seller Pro (€49/mese): Analytics avanzate, promozioni, supporto prioritario
- Seller Enterprise (personalizzato): Dedicato account manager, API avanzate

**Formazione:**
- Webinar mensili (gratuiti): trends di mercato, ottimizzazione catalogo
- Certificazione Seller (gratuita): completare corso su piattaforma
- Workshop annuale (a pagamento): networking, strategie avanzate

**Marketing Support:**
- Promozioni in homepage (su performance)
- Programma affiliate per influencer/blogger
- Co-marketing opportunità per seller premium

---

## Appendice A: Checklist Onboarding Seller

- [ ] Account BricoBravo creato
- [ ] Verifica email completata
- [ ] Dati aziendali inseriti (Partita IVA, IBAN, indirizzo)
- [ ] Metodo integrazione scelto (Feed/API/Portal)
- [ ] Prime 50 prodotti caricati
- [ ] Immagini prodotto conformi a requisiti
- [ ] Politica resi pubblicata
- [ ] Modello spedizione configurato
- [ ] Carriera account manager assegnato
- [ ] Prime vendite completate
- [ ] KPI monitoring setup

---

**Versione Documento:** 2.1
**Data Aggiornamento:** 1 Aprile 2026
**Compilato per:** Vendiamonoi.it
**Marketplace:** BricoBravo

*Questo documento è proprietario e destinato esclusivamente all'uso di distributori digitali autorizzati.*