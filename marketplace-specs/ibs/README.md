# Specifica Marketplace IBS.it
## Guida Completa per Venditori - Piattaforma Feltrinelli Group

---

## 1. Informazioni Generali

| Attributo | Dettaglio |
|-----------|----------|
| **Nome Piattaforma** | IBS.it (Internet Bookshop Italia) |
| **URL Principale** | https://www.ibs.it |
| **Sede Legale** | Milano, Italia |
| **Società Madre** | Feltrinelli Group |
| **Fondazione** | 1998 |
| **Mercato** | Italia (Italia-only marketplace) |
| **Categorie Principali** | Libri, Musica, Film/DVD, Giochi, Elettronica, Casa, Cancelleria |
| **Tipo di Marketplace** | Ibrido (retail + third-party sellers) |
| **Modello Seller** | IBS Marketplace per venditori terzi |
| **Lingua Ufficiale** | Italiano |
| **Valuta** | EUR (Euro) |

---

## 2. Modello di Business

### Storia e Evoluzione
IBS.it è stato fondato nel 1998 come primo sito di vendita online di libri in Italia, pioneering l'e-commerce nel paese. Nel corso degli anni è stato acquisito dal Gruppo Feltrinelli, il maggiore distributore librario italiano, consolidando così la sua posizione di leader nel mercato.

### Estratto Feltrinelli Group
- Proprietario: Feltrinelli Editore e Distribution Network
- Integrazione verticale: dalle pubblicazioni alla distribuzione alla vendita al dettaglio
- Sinergia con Feltrinelli Store fisici: estensione online del marchio storico
- Iniziative omnichannel: coordinamento tra piattaforma digitale e negozi fisici

### Espansione Marketplace
IBS.it ha evolvuto il suo modello da rivenditore puro a piattaforma marketplace, permettendo a venditori terzi di offrire prodotti. Questa espansione è stata guidata da:

1. **Ampliamento Categorie Prodotto**
   - Fase 1 (1998-2008): Esclusivamente libri
   - Fase 2 (2009-2015): Musica fisica, film, giochi
   - Fase 3 (2016-2022): Elettronica, casa, cancelleria
   - Fase 4 (2023-presente): Generalista merchandise con focus libri/media

2. **Categorizzazione Attuale**
   - **Libri**: Fiction, non-fiction, saggistica, fumetti, ebook (frazione crescente)
   - **Musica**: CD, vinili, audio fisico
   - **Film/DVD**: DVD, Blu-ray, film rari, collezioni
   - **Giochi**: Videogiochi (console, PC, mobile), giochi da tavolo
   - **Elettronica**: Accessori libro (e-reader, illuminazione), dispositivi tech generici
   - **Casa**: Arredamento librerie, complementi d'arredo, article regalo
   - **Cancelleria**: Diari, quaderni, articoli scolastici, burotica

### Strategia Competitiva
- Posizionamento come marketplace "culturale" (focus su media, libri, contenuti)
- Differenziazione da Amazon per specializzazione italiana e community reader
- Integrazione con network Feltrinelli per logistica e sourcing
- Enfasi su esperienza utente per appassionati di lettura

---

## 3. Piattaforma Tecnica

### Architettura Marketplace
IBS.it utilizza una piattaforma marketplace proprietaria con componenti open-source integrate per garantire scalabilità e affidabilità.

#### Componenti Core
- **Frontend**: Piattaforma e-commerce responsiva (web e mobile app iOS/Android)
- **Backend APIs**: REST APIs per integrazione seller e gestione catalogo
- **Data Management**: Sistema di catalogazione ISBN/EAN nativo
- **Payment Gateway**: Stripe, PayPal, carte di credito dirette
- **Search Engine**: Elasticsearch per full-text search e faceted navigation

### Integrazione Seller

#### Feed Integration (Preferito)
- **Formato**: XML, CSV, JSON (su richiesta)
- **Frequenza**: Batch giornaliero (overnight) o real-time push
- **Endpoint**: SFTP secure, HTTP POST, webhook
- **Validazione**: Schema validation automatico con feedback dettagliato errori

#### API REST
```
Base URL: https://api.ibs.it/v2/
Autenticazione: OAuth 2.0 con API key
Rate Limit: 1000 requests/hour per seller token
```

Endpoints disponibili:
- `POST /products`: Caricamento prodotto
- `PUT /products/{sku}`: Aggiornamento catalogo
- `GET /products/{sku}`: Verificare stato pubblicazione
- `POST /inventory`: Update stock in tempo reale
- `GET /orders`: Retrieve ordini seller
- `POST /orders/{orderId}/shipment`: Notifica spedizione
- `GET /seller/performance`: Metriche seller

### Seller Portal
- **URL**: https://seller.ibs.it o integrazione account IBS esistente
- **Accesso**: Credenziali uniche, 2FA opzionale
- **Dashboard**: Analytics in tempo reale su ordini, visite, conversion rate
- **Gestione**: Catalogo, ordini, fatturazione, comunicazioni

### ChannelEngine Integration
IBS.it supporta integrazioni tramite ChannelEngine (piattaforma multi-marketplace):
- Sincronizzazione automatica inventory across channels
- Gestione ordini centralizzata
- Mapping categorie e attributi automatico
- Supporto per oltre 30 marketplace EU (Amazon, eBay, Cdiscount, ecc.)

---

## 4. Catalogo Prodotti

### Requisiti Generali

#### Identificativi Prodotto
- **ISBN**: Obbligatorio per libri, audiolibri, alcuni format fisici
- **EAN/UPC**: Obbligatorio per musica, film, giochi, elettronica, cancelleria
- **SKU Seller**: Identificativo univoco presso il venditore (max 100 char)
- **GTIN**: Global Trade Item Number dove applicabile

**Validazione ISBN:**
- Formato: ISBN-13 (obbligatorio) con o senza trattini
- Esempio valido: 978-8817148993 oppure 9788817148993
- Checksum validation automatico
- Rifiuto: ISBN duplicati, non-validi, o intestati a prodotti diversi

#### Attributi Comuni (Tutti i Prodotti)
```
- Titolo: 1-255 caratteri, deve contenere nome prodotto principale
- Descrizione: Fino a 5000 caratteri, HTML supportato (tag limitati)
- Categoria IBS: Selezione da tassonomia predefinita (36+ categorie)
- Marca/Autore: Obbligatorio per tracciabilità
- Immagini: Min 1, max 20, almeno 1 square 500x500px
- Lingua: Selezione da elenco (IT, EN, FR, ES, DE, ecc.)
- Condizione: Nuovo, Usato come nuovo, Usato buono, Usato accettabile
```

### Attributi Categoria-Specifici

#### LIBRI (35% inventario IBS)
```
Attributi Obbligatori:
- Autore: Nome/cognome (min 1, max 10 autori)
- Editore: Denominazione esatta casa editrice
- ISBN-13: Identificativo Standard Internazionale Libri
- Data Pubblicazione: YYYY-MM-DD
- Numero Pagine: Integer positivo (0-9999)
- Formato: Copertina rigida, Copertina flessibile, eBook, Audiobook
- Lingua Originale: IT / EN / FR / altre

Attributi Fortemente Consigliati:
- Collana: Nome collana editoriale se presente
- Numero in Serie: Per serie e saghe
- Genere: Letteratura, Mistero, Fantascienza, Romanzo, Manuale, ecc.
- Descrizione Trama: 200-1000 caratteri
- ETA_LETTURA: Bambini (0-5), Ragazzi (6-12), Adolescenti (13-18), Adulti (18+)

Attributi Facoltativi:
- Riconoscimenti: Premi letterari, bestseller status
- Numero Edizione: Edizione 1, 2, rivista, ecc.
- Illustrazioni: Sì/No per libri illustrati
- Traduzioni: Lingua + Nome traduttore
```

#### MUSICA (8% inventario IBS)
```
Attributi Obbligatori:
- Artista: Nome musicista/band primario
- Titolo Album: Nome univoco dell'album
- EAN/UPC: Identificativo fisico disco
- Formato: CD, Vinile, Cassetta, SACD
- Anno Rilascio: YYYY
- Numero Tracce: Integer
- Genere Musicale: Pop, Rock, Jazz, Classico, ecc.

Attributi Consigliati:
- Etichetta Discografica: Label produttore
- Numero Catalogo: Codice distributor
- Durata Totale: Minuti:Secondi formato
- Lingua: Lingua vocali (IT, EN, ecc.)
- Tracklist: Lista canzoni con durata singola
```

#### FILM & DVD (7% inventario IBS)
```
Attributi Obbligatori:
- Titolo Originale: Titolo lingua originale
- Titolo Italiano: Traduzione italiana
- EAN/UPC: Codice DVD/Blu-ray
- Formato Video: DVD, Blu-ray, 4K UHD
- Anno Rilascio: YYYY
- Genere: Azione, Dramma, Commedia, Horror, ecc.
- Regista: Nome regista principale
- Durata: MM:SS formato

Attributi Consigliati:
- Lingua Audio: Originale, Italiana, Multi-lingua
- Sottotitoli: Disponibili sì/no, lingue
- Case Produttive: Studio principale
- Numero Attori: Principali 3-5 nomi
- Trama: 100-500 caratteri
- Rating Età: G, PG, PG-13, 14, 16, 18
```

#### GIOCHI (6% inventario IBS)
```
Attributi Obbligatori:
- Titolo Gioco: Nome ufficiale
- Piattaforma: PC, PlayStation 5/4, Xbox Series X|S, Nintendo Switch, Mobile, Tavolo
- Editore: Publisher ufficiale
- EAN/UPC: Identificativo fisico se presente
- Genere: FPS, RPG, Strategia, Puzzle, Tavolo, ecc.
- Data Rilascio: YYYY-MM-DD
- Lingua: Lingue disponibili (UI, audio, sottotitoli)

Attributi Consigliati:
- Rating PEGI: 3, 7, 12, 16, 18
- Numero Giocatori: Singolo/Multiplayer (online/locale)
- Descrizione Gameplay: 200-800 caratteri
- Requisiti Sistema: Per PC, CPU min/consigliata, GPU, RAM, storage
- DLC Inclusi: Listare se con pass stagionale
```

#### ELETTRONICA (9% inventario, crescita rapida)
```
Attributi Obbligatori:
- Nome Prodotto: Modello ufficiale
- Marca: Produttore
- Modello/SKU Produttore: Identificativo univoco brand
- EAN/UPC: Codice a barre
- Categoria Prodotto: E-reader, Illuminazione, Cuffie, ecc.
- Descrizione Tecnica: 300-1500 caratteri

Attributi Consigliati:
- Specifiche Tecniche: Risoluzione, batteria, capacità, connettività
- Colore: Se applicabile
- Peso e Dimensioni: Per spedizione
- Batteria: Durata in ore/giorni di uso tipico
- Connettività: Bluetooth, Wi-Fi, USB, tipo connettore
- Garanzia: Durata mesi, tipo internazionale/italiana
```

#### CASA & CANCELLERIA (8% inventario)
```
Attributi Obbligatori:
- Nome Articolo: Descrizione prodotto
- Categoria: Libreria, Arredamento, Cancelleria, Quaderni, Diari
- Marca: Produttore/Designer
- EAN/UPC: Se applicabile
- Materiale Principale: Legno, Metallo, Plastica, Carta, ecc.

Attributi Consigliati:
- Dimensioni: Lunghezza x Larghezza x Profondità (cm)
- Colore: Principale + alternative
- Peso: Kg (importante per logistica)
- Peso Confezione: Per calcolo volume
- Numero Fogli/Pagine: Per cancelleria
- Certificazioni: FSC, PEFC, eco-sostenibilità
```

### Immagini Prodotto

#### Specifiche Tecniche
```
Formato: JPEG, PNG
Risoluzione: Min 500x500px (consigliato 1500x1500px)
Qualità: Min 80% compressione per JPEG
Numero: Min 1 immagine (max 20 per listato)
Peso File: Max 10 MB per immagine

Requisiti Contenuto:
1. Prima Immagine: Prodotto chiaro su fondo neutro (bianco/grigio)
2. Angoli Multipli: Fronte, retro, dettagli per libri
3. Proporzioni: 1:1 (quadrata) preferito, 4:3 accettato
4. Nessuna Watermark: I watermark vengono rimossi automaticamente
5. Nessun Testo Sovrapposto: Eccetto marchio brand minore
```

#### Best Practices
- Immagine 1: Copertina (fronte) per libri, face prodotto per altri
- Immagini 2-3: Retro, spine (libri) o dettagli costruttivi
- Immagini 4+: Lifestyle, contesto uso, packaging
- Evitare: Immagini sfocate, ritagliate, con background ingombrante

---

## 5. Gestione Offerte

### Gestione Prezzi

#### Strategia Pricing
- **Competitive Positioning**: IBS monitora continuamente prezzi Amazon, Feltrinelli, competitor
- **Price Matching**: Logica algoritmica auto-ajusta prezzi per competitività
- **Discount Periodici**: IBS sponsor promozionali per categorie durante stagioni (Natale, back-to-school)
- **Dynamic Pricing**: Permesso per sellers solo con approvazione account premium

#### Vincoli di Prezzo
```
Minimo: 0,01 EUR
Massimo: 99.999,99 EUR
Decimal Places: 2 decimali obbligatori
Cambio Prezzo: Effettivo entro 15 minuti da submit feed
Formato: 12,50 (virgola decimale) oppure 12.50 (punto decimale)
```

#### Sconto Visibile
- Prezzo Originale vs Prezzo Offerta: Ambedue visibili se differenti
- Percentuale Sconto: Calcolata e visualizzata automaticamente (max 2 decimali)
- Sconto Minimo Visibile: 5% per visualizzare badge "in sconto"

### Gestione Inventory (Stock)

#### Sincronizzazione Real-Time
- **Update Frequency**: Push immediato alla vendita (webhook) o batch giornaliero
- **Stock Minimo Avvertimento**: Sistema avverte quando stock < 5 unità
- **Out of Stock**: Prodotto rimosso dall'index ricerca, ma rimane visibile in caso restock
- **Pre-ordine**: Supportato per libri non ancora in commercio (autorizzazione speciale richiesta)

#### Logica Stock
```
Stock Positivo: Disponibile per acquisto
Stock Zero: Out of stock (non vendibile)
Stock Negativo: NON PERMESSO - causa sospensione feed
Back Order: Prodotto messo in "Ordinabile in [N] giorni"
Reserved: Stock dedotto per ordini in-fulfillment
```

#### Feed Inventory Update
```
Formato: <inventory>
  <product_sku>ABC123</product_sku>
  <stock_quantity>47</stock_quantity>
  <warehouse_location>Milano</warehouse_location>
  <restock_date>2026-05-15</restock_date>
</inventory>
```

### Gestione Spedizione

#### Configurazione Shippable
- **Peso Prodotto**: Obbligatorio (kg, decimali accettati)
- **Dimensioni**: Lunghezza, Larghezza, Profondità in cm (anche se approssimate)
- **Fragile**: Flag se richiede imballaggio speciale (libri sono generalmente no)
- **Hazardous**: Flag se materia pericolosa (raro in categorie IBS)

#### Costi Spedizione

**Modello 1: Seller Gestisce (Preferred)**
- Seller definisce costi per fascia peso/zona
- IBS calcola automaticamente a checkout
- Seller responsabile per delivery

**Modello 2: IBS Gestisce (Integrated Logistics)**
- IBS partnership con SDA (Poste), GLS, DPD, Bartolini
- Seller fornisce tracking number
- IBS gestisce comunicazioni cliente spedizione

#### Fasce Spedizione Tipiche (IBS Retail Benchmark)
```
Per Libri (Media 0.5 kg):
- Italia Continental: 0,01-0,99 kg = 2,90 EUR
- Italia Continental: 1,00-2,99 kg = 3,90 EUR
- Italia Continental: 3,00+ kg = 5,90 EUR
- Isole Maggiori (Sicilia, Sardegna): +2,00 EUR
- Estero (EU): Varia per nazione, base 8,90 EUR

Per Elettronica (Media 1.5 kg):
- Italia Continental: 0,01-2,99 kg = 5,90 EUR
- Italia Continental: 3,00+ kg = 9,90 EUR
- Isole: +3,00 EUR
- Estero (EU): 12,90 EUR base

Gratis per:
- Ordini over 49,00 EUR (promozione perpetua IBS)
- Seller premium con volume > 200 ordini/mese
```

#### Tempi di Spedizione
```
Processing: 1 giorno lavorativo (00:00-24:00 dopo ordine)
Pickup Carrier: 1 giorno lavorativo dopo processing
In Transit:
  - Italia Continental: 2-3 giorni (SDA, GLS express)
  - Isole: 3-5 giorni
  - EU: 5-15 giorni variabile per paese
Totale: Da ordering a consegna 3-6 giorni ITA, 8-20 giorni EU
```

### Condizioni Prodotto (Usato)

#### Classificazione Condizione
Applicabile solo a Libri, Musica, Film (media fisico):

1. **NUOVO**:
   - Non mai utilizzato
   - Imballaggio originale inviolato
   - Tutte le parte originali presenti
   - Zero difetti, zero segni uso

2. **USATO COME NUOVO**:
   - Utilizzato minimamente
   - Nessun evidenti segni lettura
   - Nessun appunti, sottolineature, pieghe
   - Imballaggio può essere aperto
   - Condizione pari al nuovo

3. **USATO BUONO**:
   - Utilizzato normale
   - Piccoli segni uso evidenti (pieghe copertina, tracce maneggio)
   - Possibili piccole sottolineature o annotazioni marginali minori
   - Nessun danno strutturale
   - Tutte le pagine presenti e leggibili
   - Prezzo 20-40% sotto nuovo

4. **USATO ACCETTABILE**:
   - Utilizzo pesante ma funzionale
   - Visibili segni uso (strappi minori, chiazze, pieghe importanti)
   - Possibili sottolineature e annotazioni sparse
   - Possibili piccoli danni non strutturali (separazione binding minore, pagina angolo strappato)
   - Tutte le pagine presenti
   - Leggibile e preservato in funzionalità
   - Prezzo 40-60% sotto nuovo

#### Descrizione Condizione Obbligatoria
```
Esempio "USATO BUONO":
"In ottime condizioni di lettura. Minime pieghe sulla copertina.
Nessun appunto interno. Binding intatto. Consigliato per lettori
che cercano risparmio pur garantendo qualità."
```

#### Verifica IBS
- IBS preserva diritto di ispezionare prodotti usati su reclamo cliente
- Discrepanza condizione vs descrizione = Rimborso cliente + penalty seller
- Reso su base condizione inferiore a descritta
- Pattern di reclami → sospensione categoria usato

---

## 6. Ciclo Ordini

### Stati Ordine (Order Lifecycle)

```
1. PENDING (Ordine Ricevuto)
   - Cliente ha completato pagamento
   - IBS invia notifica ordine a seller via email + API
   - Seller ha 24 ore per accettare/rifiutare
   - Se seller non risponde entro 24h → auto-accept
   - Azione: Seller accetta ordine

2. CONFIRMED (Ordine Accettato)
   - Seller ha confermato disponibilità prodotto
   - Seller ha 48 ore per effettuare spedizione
   - Preparazione imballaggio e documentazione
   - Azione: Seller crea shipment con tracking

3. SHIPPED (Ordine Spedito)
   - Seller ha fornito tracking number carrier
   - Tracking sincronizzato con ibs.it
   - Cliente riceve email con tracking automaticamente
   - Azione: Carrier consegna o cliente ritiene

4. DELIVERED (Consegnato)
   - Tracking indica consegna completata
   - Cliente ha 14 giorni per diritto recesso
   - Seller responsabilità finisce (salvo reclami qualità)
   - Azione: Cliente reclama o accetta

5. COMPLETED (Finalizzato)
   - 14 giorni recesso scaduti
   - Nessun reclamo presentato
   - Ordine chiuso, seller riceve pagamento finale
   - Azione: Nessuna

Alternative Stati:
- CANCELLED: Cliente o Seller cancella prima shipping
- RETURNED: Cliente esercita diritto recesso
- DISPUTED: Reclamo aperto in arbitrato IBS
- REFUNDED: Rimborso emesso
```

### Acceptance & Response Times

#### Obbligo Accettazione
- **Timeframe**: 24 ore da ricezione ordine
- **Respingimento**: Motivi validi (stock non disponibile, indirizzo invalido, pagamento sospetto)
- **Auto-Accept**: Se seller non risponde entro 24h, ordine auto-accettato
- **Conseguenza Rifiuto**: Track rifiuti - > 2% rifiuti/mese = warning → sospensione

#### SLA Tempi Risposta Supporto
- **Domande Cliente**: Max 24 ore risposta (tramite IBS messaging)
- **Reclami**: Max 48 ore accuse, max 5 giorni risoluzione proposta
- **Dispute Aperture**: Seller ha 7 giorni per controreplica

### Ciclo Reso e Tracking

#### Tracciamento Spedizione
- **Provider Supportati**: SDA, GLS, DPD, Bartolini, Poste Italiane, Corriere Espresso
- **Integrazione**: Tracking number inoltrato automaticamente a cliente
- **Update Automatico**: IBS sincronizza status carrier 1x giorno
- **Notifiche**: Cliente riceve SMS/email per: spedito, in transito, consegnato, tentativo fallito

#### Timeline Tipica Ordine
```
Giorno 0: Cliente ordina, pagamento confermato
Giorno 0-1: Seller accetta ordine (entro 24h)
Giorno 1-2: Seller prepara e spedisce (entro 48h accepted)
Giorno 2: Seller fornisce tracking
Giorno 2-5: Prodotto in transito (Italia continental)
Giorno 5: Consegna solitamente
Giorno 5-19: Cliente può esercitare diritto recesso (14 giorni)
Giorno 20+: Ordine finalizzato, seller riceve pagamento
```

---

## 7. Fulfillment

### Logistics Partner IBS

#### Carrier Primari
1. **SDA (Poste)**
   - Copertura: 99% Italia
   - Specialità: Pacco standard, books media mail
   - Tracking: Real-time
   - Consegna: 2-3 giorni ITA continental

2. **GLS**
   - Copertura: 99% Italia + EU selezionato
   - Specialità: Pacco espresso, B2B
   - Tracking: Real-time con foto
   - Consegna: 1-2 giorni ITA, 5-10 giorni EU

3. **DPD (Coriere Express)**
   - Copertura: 98% Italia
   - Specialità: Pacco valore, signature required
   - Tracking: Real-time
   - Consegna: 1-2 giorni ITA

4. **Poste Italiane Paccocelere**
   - Copertura: 100% Italia + UE
   - Specialità: Economico, affidabile
   - Tracking: Periodico
   - Consegna: 3-5 giorni ITA, 7-15 giorni EU

### Imballaggio Libro-Specifico

#### Standard Imballaggio Libri (Raccomandato)
```
Materiali:
- Scatola cartonato: Ondulato minimo 3 strati
- Imbottitura: Pluriball, carta kraft, chips poliestere
- Nastro: Adesivo marrone trasparente, min 2 linee
- Etichetta: IBS shipping label stampa termica

Procedure:
1. Posizionare libri piatti (non in piedi) se singolo, altrimenti stack
2. Avvolgere in pluriball se valore > 30 EUR
3. Posizionare in scatola con 2-3 cm imbottitura su tutti lati
4. Seal scatola con nastro (3 lati: sopra, due lati)
5. Applicare label IBS su fronte, dati seller dietro
6. Verificare peso con bilancia (comunicare a IBS se discrepanza > 100g)

Per Libri Rari/Preziosi:
- Scatola doppia ondulata
- Imbottitura 5+ cm
- Wrap singolo pluriball per ogni libro
- Assicurazione dichiarata (valore in dogana EU)
```

#### Dimensioni Scatole Standard
```
Per 1-3 Libri (0.5-1.5 kg): 24x17x8 cm
Per 4-6 Libri (1.5-3 kg): 30x20x15 cm
Per 7+ Libri (3+ kg): 40x30x20 cm
```

### Carrier Requirements per Categoria

#### Musica/Film (Media Fisico)
- Packaging come libri (cartone ondulato min)
- Imbottitura min 2 cm se valore > 50 EUR
- Per vinili: Imbottitura 3+ cm + protezione angoli
- Per Blu-ray/DVD: Imbottitura 1-2 cm sufficiente

#### Elettronica
- Imballaggio originale produttore se disponibile
- Se usato: Scatola sturdy ondulata con imbottitura 3+ cm
- Accessori: Sacchetto antistatico se contenuto
- Caricabatterie/alimentatori: Wrap separato
- Garanzia: Documentazione incollata internamente

#### Casa (Arredamento)
- Per piccoli articoli: Cartone standard ondulato
- Per librerie/mensole: Imballaggio specializzato o pre-accordato con IBS
- Materiali fragili: Imbottitura generosa
- Istruzioni montaggio: Incluse se pertinenti

### Tracking & Notifiche

#### Integrazione Tracking IBS
```
Endpoint API: POST /orders/{orderId}/shipment
Parametri:
- order_id: Riferimento ordine IBS
- tracking_number: Numero carrier
- carrier_code: SDA | GLS | DPD | POSTE
- expected_delivery: YYYY-MM-DD
- shipping_method: Standard | Express | International

Risposta: 200 OK con confirmation ID tracking
```

#### Notifiche Cliente Automatiche
- **Ordine Confermato**: Entro 2 ore da accept
- **Spedito**: Quando tracking comunicato
- **In Transito**: Update giornaliero (se carrier permette)
- **Consegna Domani**: Notifica 24h prima
- **Consegnato**: Tracking status completed
- **Tentativo Fallito**: Se carrier non riesce consegna primo tentativo

---

## 8. Resi e Reclami

### Diritto Recesso (Consumatore)

#### Legislazione
- **Normativa**: Codice Civile italiano + Direttiva UE 2011/83 (Consumer Rights)
- **Durata**: 14 giorni naturali da ricezione prodotto
- **Applicazione**: Tutti i prodotti new & used eccetto custom/personalizzati
- **Modalità**: Notifica scritta tramite form IBS, email, o contatto carrier

#### Esercizio Recesso per Libri
```
Step 1: Cliente avvia recesso tramite IBS dashboard entro 14 giorni
Step 2: IBS invia email a seller e cliente con modalità restituzione
Step 3: Cliente prepara reso (seller fornisce etichetta restituzione)
Step 4: Cliente effettua spedizione con tracking (pagato da seller)
Step 5: Seller riceve e ispeziona condizione
Step 6: Seller conferma ricezione entro 3 giorni
Step 7: IBS processa rimborso (meno costo spedizione se usato) entro 5 giorni
```

#### Costi Recesso
- **Libri Nuovo**: Rimborso 100% prezzo, seller paga return shipping
- **Libri Usato**: Rimborso 100% prezzo, ma return shipping split 50/50 o full seller se condizione non matching descrizione
- **Altri Prodotti**: Rimborso 100% prezzo se difetto, altrimenti split shipping
- **Restocking**: Non applicato (proibito da legge EU)

#### Ispezione Seller per Reso
```
Libri Ricevuti In Reso:
- Se condizione peggiore descrizione originale: Accetta reso
- Se condizione match: Accetta reso
- Se cliente ha evidenti danni che non erano presenti: Potrebbe rifiutare, ma IBS arbitra
- Se libro è sporco/danneggiato/illeggibile: Rifiuta reso, escalate a IBS

Procedura: Seller fotografa libro in ricezione, carica su dispute panel IBS, indica assessment.
IBS review e arbitra se discrepanza.
```

### Sistema Reclami (Beyond Return Rights)

#### Tipologie Reclami Validi
1. **Prodotto Non Conforme**: Condizione peggiore descritta, difetti nascosti
2. **Mancata Consegna**: Carrier lost, item non arrivato dopo 20 giorni
3. **Pagamento Non Ricevuto**: Seller non ha ricevuto payment dal cliente
4. **Comunicazione Assente**: Seller non risponde da > 3 giorni

#### Processo Reclamo
```
Step 1: Cliente avvia reclamo da ordine (entro 30 giorni da ordine)
Step 2: Cliente descrive problema + carica foto se richieste
Step 3: IBS invia notifica a seller (2 ore)
Step 4: Seller ha 5 giorni per rispondere con proposta risoluzione
Step 5A: Se accordo → Reclamo chiuso, risoluzione implementata
Step 5B: Se disaccordo → IBS arbitra entro 7 giorni (decisione binding)
Step 6: Esecuzione rimborso/sostituzione/reso
```

#### Arbitrato IBS
- **Arbitri**: Team di moderatori IBS trained
- **Criteri**: Descrizione listing, foto prova, policy IBS, precedenti seller
- **Decisione**: Binding per ambedue le parti
- **Appeal**: Solo se evidenza di frode seller, non per disaccordo opinione
- **Tempi**: Decisione entro 7-10 giorni

### Metriche Recesso e Dispute

#### KPI Monitorate
- **Return Rate**: % ordini ritorni over 30 giorni
  - Target: < 3% per categoria
  - Warning: > 5% per 2 mesi consecutivi
  - Azione: Account review, possible suspension > 8%

- **Reclami Validi**: % reclami supportati da IBS arbitrato
  - Target: < 2%
  - Pattern: Se > 10 reclami simili in 90 giorni = investigation

- **Dispute Resolution Time**: Tempo medio accettazione reso
  - Target: < 3 giorni
  - SLA: Max 5 giorni sopra penalità seller (ritardo payout)

---

## 9. Struttura Commissioni

### Commissioni per Categoria

IBS applica commissioni variabili in base categoria prodotto e performance seller. Commissioni trattenute da IBS su ogni ordine completato.

#### Commissioni Standard (Base)

| Categoria | Commissione | Note |
|-----------|-------------|------|
| **Libri** | 12-15% | 12% se seller volume > 500/mese, 15% se < 100/mese |
| **Musica (CD/Vinile)** | 12-15% | Come libri, specialità musica rara |
| **Film (DVD/Blu-ray)** | 12-15% | Come libri, incluso vinile+film combo |
| **Giochi (Fisici)** | 10-12% | Console, board games, accessori gaming |
| **Elettronica** | 7-10% | Fascia bassa per volumi alti; 10% per niche products |
| **Casa/Arredamento** | 12-15% | Incluso librerie, complementi design |
| **Cancelleria/Articoli Scuola** | 12-15% | Diari, quaderni, articoli scolastici |

#### Sconti Commissione (Seller Incentivi)

1. **Volume Tier Reduction**
   - 100-250 ordini/mese: -1% commissione
   - 250-500 ordini/mese: -2% commissione
   - 500+ ordini/mese: -3% commissione (max)

2. **Performance Bonus**
   - Perfect Feedback (5 stelle, 0 reclami): -0.5% per 3 mesi
   - Fast Shipping (< 24h processing): -0.5%
   - Return Rate < 2%: -1%

3. **Categoria Strategica** (IBS Priority)
   - Libri italiani contemporanei: -2% speciale
   - Self-publishing: -3% incentivo (supporto autori indipendenti)
   - Resto mondo (non UK/US): -1% (rarità/nicchia)

#### Fee Aggiuntivi

1. **Logistics Fee** (non commissione)
   - 0,30 EUR per spedizione standard (cover IBS platform costs)
   - 0,50 EUR per spedizione EU
   - Trattenuti separatamente da commissione

2. **FVG (Featured Visibility Guarantee)** - Opzionale
   - Placement boost: 0,50 EUR per listato al mese
   - Featured position ricerca/categoria
   - Rinnovabile

3. **Advertising Fee** (se seller usa IBS Ads)
   - Self-service advertising: 0,50 EUR costo minimo daily
   - CPC (Cost Per Click) models: 0,05-0,50 EUR per click
   - ROI tracking platform integrated

---

## 10. Standard Prestazionali

### Metriche Seller Monitorate

#### KPI Dashboard Seller
```
1. FULFILLMENT RATE
   - Definizione: % ordini confermati entro 24h su totale
   - Target: > 95%
   - SLA: 24 ore da ordine ricevuto
   - Violazione: Automessaggio warning, visible badge "Slow Processing"

2. SHIPPING SPEED
   - Definizione: Giorni medi da conferma ordine a shipping
   - Target: < 2 giorni
   - SLA: Max 48 ore per libri, max 72 ore per elettronica
   - Violazione: Visible badge "Delayed Shipping", customer alert

3. RETURN RATE
   - Definizione: % ordini cliente esercita diritto recesso
   - Target: < 3%
   - SLA: Valutato over 90 giorni rolling
   - Violazione: > 8% = account investigation

4. RECLAMI RATIO
   - Definizione: % ordini con reclamo valido sostenuto da IBS
   - Target: < 2%
   - SLA: Valutato over 30 giorni
   - Violazione: > 5 reclami simili in 90 giorni = escalation

5. RESPONSE TIME
   - Definizione: Tempo medio risposta a domande/reclami cliente
   - Target: < 18 ore (buono), < 24 ore (acceptable)
   - SLA: Max 48 ore, sopra trigger warning
   - Violazione: Visible badge "Non-responsive", customer hesitation

6. FEEDBACK SCORE
   - Definizione: Average rating dalle recensioni cliente (1-5 stelle)
   - Target: > 4.3 stelle
   - SLA: Min 10 recensioni per mostrare rating
   - Violazione: < 3.5 stelle = mandatory improvement plan

7. INVENTORY ACCURACY
   - Definizione: % ordini recevuti vs stock actual (discrepanza = out of stock order)
   - Target: 100% (zero out of stock)
   - SLA: < 0.5% tollerato
   - Violazione: > 2% = feed suspension until corrected
```

### Sanzioni Performance

#### Tier Sanctions
```
LIVELLO 1 (Warning) - 1 Punto
- Visible "Slow Processing" badge
- Non-impacts listing visibility

LIVELLO 2 (Restricting) - 2 Punti
- Return rate > 5%
- Response time > 48 ore consistently
- Azione: Delisting non-performing products per categoria

LIVELLO 3 (Suspension) - 3+ Punti
- Accumulo 3+ warning points in 30 giorni
- OR return rate > 8%
- OR reclami > 10 in 90 giorni
- Azione: Account suspended, seller non può pubblicare

LIVELLO 4 (Termination)
- Violazione policy IBS (frode, counterfeit, abuso)
- Accumulo 6+ warning points in 90 giorni
- Azione: Account closed, balance held 30 giorni
```

#### Appeal Process
- Seller può appellare decisione IBS entro 7 giorni
- IBS review appeal con nuove evidenze
- Supervisor level decision (non moderator)
- Binding decision, no further appeal (salvo frode provata)

### Seller Grade (Public Rating)

IBS assegna Seller Grade pubblico basato su metriche sopra:

**GOLD** (Eccellente)
- Fulfillment > 98%, Shipping < 1.5 giorni
- Return < 2%, Feedback > 4.6 stelle
- 0 reclami in 30 giorni

**SILVER** (Buono)
- Fulfillment > 95%, Shipping < 2 giorni
- Return < 3%, Feedback > 4.3 stelle
- < 2% reclami

**BRONZE** (Accettabile)
- Fulfillment > 90%, Shipping < 3 giorni
- Return < 5%, Feedback > 4.0 stelle
- < 5% reclami

**NON-RATED** (Problematico)
- Fallimento di sopra oppure novo seller < 30 giorni
- No badge visibile, disclosure "Unrated Seller"

---

## 11. Aspetti Finanziari

### Fatturazione Elettronica Italiana

#### Requisiti Legali
- **Normativa**: D.L. 50/2017, Agenzia Entrate, obbligatorio dal 1 gennaio 2019
- **Formato**: XML standard (formato SDI - Sistema di Interscambio)
- **Ricezione**: Trasmissione diretta via SDI Agenzia Entrate (IBS intermediaria)
- **Conservazione**: 10 anni digitale certificato

#### Documentazione Richiesta
```
1. Partita IVA italiana (obbligatorio per sellers IT)
   - Se non italiana: Partita IVA EU alternative, es. Numero IVA LU123456789

2. Dati Aziendali:
   - Ragione Sociale (come iscritto Registro Imprese)
   - Cognome/Nome (se ditta individuale)
   - Indirizzo Sede Legale
   - Codice Fiscale / P. IVA
   - Regime Fiscale (forfettario, ordinario, semplificato)

3. Dati Fatturazione (se diverso sede):
   - Indirizzo fatturazione se diverso
   - Email fatturazione (destinatario ricevute)
   - PEC aziendali (posta certificata) - opzionale ma consigliato
```

#### Ciclo Fatturazione IBS
```
1. Ordine completato (Delivered + 14 giorni recesso scaduti)
2. IBS genera fattura XML in automatico
3. Fattura trasmessa via SDI in 24-48 ore
4. Seller riceve notifica con numero fattura e PDF
5. Pagamento liquidato via bonifico 15 giorni dopo (ciclo settimanale)
```

### IVA (Imposta sul Valore Aggiunto)

#### Aliquote Standard per Categoria

| Categoria | Aliquota IVA | Eccezione |
|-----------|-------------|----------|
| **Libri** | 4% | IVA agevolata per libri fisici (art. 4.1.c Dir. 2006/112) |
| **Musica/Film** | 22% | Standard; exception libri audio 4% |
| **Giochi Tavolo** | 22% | Standard |
| **Videogiochi** | 22% | Standard |
| **Elettronica** | 22% | Standard |
| **Casa/Arredamento** | 22% | Standard |
| **Cancelleria** | 22% | Standard |

#### Calcolo Prezzo IVA-inclusive
```
Esempio Libro:
- Prezzo netto editore: 20,00 EUR
- IVA 4%: 0,80 EUR
- Prezzo finale cliente: 20,80 EUR
- IBS/Seller vede: 20,00 EUR (imponibile) + 0,80 EUR (IVA)

Commissione calcolata su prezzo netto (IBS standard):
- Commissione 12% su 20,00 EUR = 2,40 EUR
- Seller riceve netto: 20,00 EUR - 2,40 EUR = 17,60 EUR (+ IVA seller responsabile)
```

#### IVA Reverse Charge (EU Sellers)
Se seller non è italiano ma EU:
- IVA inversione contabile applicata
- Seller EU NON applica IVA su vendita IBS
- IBS/Cliente italiano pagano IVA 4% (libri) o 22% (altri)
- Documentazione speciale richiesta (comunicazione IVA IV trimestre)

### Liquidazione e Pagamenti

#### Ciclo di Pagamento Standard
```
SETTIMANALE (Default)
- Lunedì: Liquidazione ordini completati domenica precedente
- Modalità: Bonifico bancario
- Tempi: Credito conto 2-5 giorni lavorativi
- Minimo: 5,00 EUR per liquidazione (altrimenti roll-forward)

RESERVE POLICY (Contingent su performance)
- IBS retention: 5% di ogni liquidazione per 60 giorni (protezione reclami)
- Seller scarico reserve: Auto dopo 60 giorni se no active disputes
- Early release: Disponibile se seller Gold status
```

#### Conto Seller Liquidazione
```
Liquidazione Settimanale Esempio:
+----------------------------------+
| Importo Ordini Completati     | 1.000,00 EUR |
| - Commissioni (12%)           |   -120,00    |
| - Logistics Fee               |    -3,00    |
| - Advertising (se usado)      |   -15,00    |
| - Return Shipping Paid        |   -25,00    |
| - Reserve (5% treat hold)     |   -50,00    |
| = NETTO LIQUIDAZIONE          |   787,00    |
| = NETTO DOPO IVA (22%)        |   962,14    |
+----------------------------------+
```

#### Metodi di Pagamento Seller
1. **Bonifico Bancario** (Preferito)
   - IBAN italiano o EU
   - Commissione IBS: 2,50 EUR per bonifico
   - Conto intestato a ragione sociale seller

2. **PayPal (Alternative)**
   - Commissione IBS: 1.5% aggiuntivo
   - Meno utilizzato (costi per seller)

3. **Assegno/Contrassegno** (Deprecato)
   - Non consigliato
   - Disponibile solo per sellers legacy

#### Rendiconto IBS
- **Periodicità**: Settimanale via email + dashboard
- **Formato**: CSV exportable, detailed transaction view
- **Dettagli**: Per-order commission, fee, shipping, return amounts
- **Riconciliazione**: Seller può fare dispute reclami in 30 giorni

---

## 12. Pattern Integrazione

### Feed Integration (XML/CSV)

#### Modello Feed Preferito

**Frequenza**: Giornaliero (overnight batch 2-4 AM CET) oppure real-time push

**Formato XML Campione**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<products>
  <product>
    <sku>LIBRO123</sku>
    <title>Io Sono Un Dio - Salman Rushdie</title>
    <description>Autobiografia straordinaria dell'autore di Versi Satanici...</description>
    <isbn>978-8817148993</isbn>
    <price currency="EUR">25.00</price>
    <images>
      <image priority="1">https://seller.com/images/libro123-front.jpg</image>
      <image priority="2">https://seller.com/images/libro123-back.jpg</image>
    </images>
    <category>Letteratura</category>
    <author>Salman Rushdie</author>
    <publisher>Mondadori</publisher>
    <language>IT</language>
    <pages>400</pages>
    <condition>NUOVO</condition>
    <stock>15</stock>
    <weight_kg>0.6</weight_kg>
  </product>
</products>
```

#### Upload Modalities
1. **SFTP**
   - Host: sftp.ibs.it
   - Directory: /seller_{seller_id}/products/
   - Credential: SFTP username + key (generate da seller portal)
   - Naming: products_{timestamp}.xml

2. **HTTP POST**
   - Endpoint: https://api.ibs.it/v2/feed/upload
   - Auth: Bearer token
   - Content-Type: application/xml
   - Max payload: 50 MB

3. **Webhook Listener**
   - IBS fornisce callback URL a seller
   - Seller invia feed a URL IBS periodicamente
   - Handshake + signature verification

#### Validazione Risposta
- **200 OK**: Feed accettato, processing in coda
- **400 Bad Request**: XML malformato, dettagli errori in risposta JSON
- **401 Unauthorized**: Token scaduto o invalido
- **413 Payload Too Large**: File supera limite 50 MB

#### Esempio Risposta Validazione
```json
{
  "status": "accepted",
  "feed_id": "feed_20260415_001",
  "timestamp": "2026-04-15T03:45:22Z",
  "products_received": 1247,
  "processing_status": "queued",
  "estimated_processing_time": "5 minutes",
  "errors": [
    {
      "line": 42,
      "sku": "LIBRO456",
      "error": "Invalid ISBN format (missing checksum validation)"
    }
  ]
}
```

#### ChannelEngine Integration (Preferito per Multi-Marketplace)
IBS.it supporta integrazione tramite ChannelEngine (piattaforma multi-marketplace):
- Sincronizzazione automatica inventory across 30+ channels
- Gestione ordini centralizzata via ChannelEngine dashboard
- Mapping categorie e attributi automatico (IBS + Amazon + eBay + ecc.)
- Feature: Auto-language translate, price optimization per marketplace

**Vantaggio**: Seller non deve mantenere feed singolo per IBS, lo fa via ChannelEngine per tutti.

---

## 13. Prezzi & Commissioni Venditori

### Modello Subscription Seller

#### Tier Subscription (Opzionale, Fornisce Sconti)

| Tier | Costo Mensile | Sconti Commissione | Best For |
|------|---------------|-------------------|----------|
| **Free** | 0 EUR | 0% | Tester, micro-sellers |
| **Starter** | 19,99 EUR | -1% commission | Sellers 50-200 ordini/mese |
| **Professional** | 49,99 EUR | -2% commission + FVG | Sellers 200-500 ordini/mese |
| **Enterprise** | 99,99 EUR + | -3% commission + Dedicated support | Sellers 500+ ordini/mese |

#### Benefici per Tier
```
FREE:
- Accesso basic seller portal
- Feed upload settimanale
- Standard support email (48h response)

STARTER (19,99 EUR):
- Feed upload giornaliero / real-time
- +1 featured listing placement
- Priority support (24h response)
- Advanced analytics dashboard

PROFESSIONAL (49,99 EUR):
- Tutti sopra +
- +5 featured placements
- API access
- Dedicated account manager (1x/mese)
- ChannelEngine integration support

ENTERPRISE (99,99 EUR+):
- Tutti sopra +
- Custom featured placements (15+)
- White-label seller portal option
- Dedicated 24/7 support
- Custom commission negotiation
- Training/onboarding included
```

### Commissioni Itemized

#### Calcolo Commissione Esempio (Libro)
```
Ordine Completato:
- Prezzo Cliente (IVA-inclusive): 20,80 EUR
- Prezzo Netto (IVA 4% libri): 20,00 EUR

Commissione Base (categoria libri, Free tier): 15%
- Importo: 20,00 EUR × 15% = 3,00 EUR

Sconti Applicati:
- Seller Premium (250 ordini/mese): -2% = 0,40 EUR
- Subscription Starter: -1% = 0,20 EUR
- Total Discount: 0,60 EUR
- Commissione Finale: 3,00 EUR - 0,60 EUR = 2,40 EUR

Seller Netto:
- 20,00 EUR - 2,40 EUR (commissione) = 17,60 EUR
  (+ 0,80 EUR IVA reverso per seller responsabilità)
```

#### Commissioni per Ordine vs. Subscription
- **Without Subscription**: Commissioni maggiori (base rates sopra)
- **With Subscription**: Fee mensile fisso ma % commissioni reduce
- **ROI**: Break-even a ~15-20 ordini/mese per Starter

### Pricing Transparency
- **Disclosed**: Tutti gli importi visibili in seller dashboard pre-liquidation
- **Disputable**: Seller può contestare calcoli in 30 giorni
- **Refundable**: IBS rimborsa se errore provato

---

## 14. Conformità Normativa

### Normative Italiane Applicabili

#### Codice Civile e Leggi Commercio
- **Art. 1469-bis e seg.**: Protezione consumatore (clausole vessatorie)
- **Art. 1521 e seg.**: Reso e garanzia (14-30 giorni diritto recesso)
- **Decreto Legge 50/2017**: Fatturazione elettronica obbligatoria
- **Codice della Privacy**: GDPR compliance, cookie policy, dati personali

#### Normative Prodotto
- **ISBN - UNI 8949:2021**: Standard International Standard Book Number
  - Obbligatorio per tutti i libri in commercio Italia/EU
  - Validazione checksum, no duplicati

- **EAN/UPC - ISO/IEC 646**: Codice a barre per musica, film, giochi
  - 13 digit standard per EU
  - Assegnazione GSS GS1 (autorità ufficiale)

- **Marchi Registrati**: Verificazione brand matching prodotto
  - IBS verifica se seller authorized per brand (anti-counterfeit)
  - Marche notorie protette (Disney, Harry Potter, Lego, ecc.)

#### IVA Agevolata Libri (Art. 4.1.c Dir. 2006/112/UE)
```
Applicabile a:
- Libri fisici cartacei (4% IVA)
- NON ebook digitali (22% IVA)
- NON audiobook abbonamento (22% IVA)
- SÌ audiobook supporto fisico/CD (4% IVA)
- SÌ fumetti (4% IVA)

Fonte: Comunicazione ministero economia / Agenzia Entrate
Seller responsabile per corretta classificazione
```

### Compliance Seller

#### Documentazione Seller
- **Termini Servizio**: Seller deve firmare TOS IBS (acceptance vincolante)
- **Privacy Policy**: Seller deve avere propria informativa privacy
- **Cookie Consent**: Seller portal richiede cookie accept
- **KYC/AML**: Verifica identità seller (Know Your Customer)

#### Controllo Fraud Anti-Counterfeit
IBS implementa:
- **AI Flagging**: ML algorithm per detect listing potenzialmente fake
- **Manual Review**: Team moderatori per verifiche sospetti brand
- **Seller Verification**: Brand authority check (es., registered distributor)
- **Consequences**: Delisting, sospensione account, legal action

#### Protezione Proprietà Intellettuale
- **Copyright**: Seller garantisce no infringement music/film
- **Trademark**: Seller warranta authorized reseller o genuine prodotto
- **Patent**: Seller responsabile per legittimità novelty goods
- **Sanzione**: DMCA takedown, account closure, legal liability seller

---

## 15. Limitazioni & Restrizioni

### Categorie Vietate

#### Assoluto Divieto
```
- Droga/Stupefacenti (incluso CBD, prodotti cannabis)
- Armi (fucili, pistole, coltelli letali)
- Munizioni e esplosivi
- Materiale sessuale esplicito / Pornografia
- Falsi documenti / Documenti contraffatti
- Beni rubati o ricettazione
- Animali vivi (salvo specifiche eccezioni acquari)
- Diamanti conflict / Minerali conflict
- Componenti nucleari / Materiali pericolosi
- Contraffatto / Marchi falsificati
- Tossici / Pesticidi non-regolamentati
- Rifiuti/Spazzatura
```

#### Restrizioni Parziali
```
- Medicinali (SOLO con autorizzazione, farmacista registered)
- Alcol (SOLO con age-gate, vendibile a 18+)
- Tabacco (VIETATO dall'EU)
- Medicinali omeopatici (autorizzazione)
- Integratori alimentari (EFSA compliance)
- E-cigarette/Vape (età 18+, warning label)
- Libri rari/antichi (verificazione rarità)
```

### Limitazioni Prodotto Specifiche

#### Libri
- **ISBN Non-Valido**: Rifiuto sistematico
- **Counterfeit Print**: Rilevamento qualità stampa vs. originale
- **Editing Non-Autorizzato**: Seller non può ri-pubblicare senza diritti
- **Self-Publishing**: Permesso con ISBN valido

#### Musica/Film
- **Bootleg Records**: Vietato (pressioni non-autorizzate)
- **Pirated Content**: Assoluto divieto (AGCOM enforcement)
- **Region Coding**: Avvertenza se regione non-compatibile lettore
- **Used Media**: Permesso se condizione ben-descritta

#### Elettronica
- **Refurbished Non-Dichiarato**: Vietato, deve indicare chiaramente "Refurbished"
- **Broken for Parts**: Permesso se chiaramente indicato
- **Open Box/Return**: Permesso se come nuovo + 2-5% sconto dichiarato

### Listing Requirements Compliance

#### Verifiche Automatiche Sistema IBS
```
1. Titolo: Lunghezza 5-255 char, no ALL CAPS, no special chars eccesso
2. Descrizione: Min 50 char, max 5000, no contact info (email/phone)
3. Immagini: Min 1, min 500x500px, no watermark/overlay
4. ISBN/EAN: Checksum validation, no duplicates
5. Prezzo: Between 0,01 EUR e 99.999,99 EUR
6. Stock: Non negativo, integer
7. Categoria: Deve essere una delle tassonomia IBS approved
```

#### Violazione & Consequenze
- **Warning**: Primo offense, automessaggio notifica
- **Temporary Delisting**: Non-vendibile per 7 giorni se violazione grave
- **Permanent Delisting**: Ripetute violazioni
- **Account Suspension**: Pattern di abuso

---

## 16. Best Practices Venditori

### Ottimizzazione Catalogo Libri (Core Business)

#### SEO Listing Optimization
```
Titolo (alta priorità per ricerca):
SCADENTE:  "Libro"
MIGLIORATA:  "Io sono un dio - Rushdie | Autobiografia straordinaria"

Descrizione (importante per conversione):
SCADENTE:  "Bel libro"
MIGLIORATA:
  "Racconto affascinante dell'autore di Versi Satanici. Rushdie
  descrive la sua vita in clandestinità per 14 anni, il viaggio
  verso la libertà, e la riconciliazione con la fede. Una memoria
  umana, ironico, ispirata. Ideale per chi ama letteratura
  contemporanea, satira, identity studies."
```

#### Scelta Foto
- Prima foto: Copertina FRONTAL riconoscibile
- Foto 2-3: Retro, spine, riportelle
- Foto 4: Stack di copie se multiple edizioni
- Evitare: Foto angolato, blur, mano holding (non professionale)

#### Attributi Criticali per Libri
- **Autore**: Essenziale, rispecchia reale
- **ISBN**: Validare prima upload
- **Data Pubblicazione**: Importante per relevance ricerca (nuovi bestseller)
- **Collana/Serie**: Se applicabile, aumenta discoverability
- **Genere**: Multiselezione (Fiction, Saggistica, ecc.)

#### Pricing Competitivo
- **Monitor Competitors**: Amazon, Feltrinelli Store, IBS retail
- **Unique Value**: Prezzo + shipping gratis per volumi
- **Seasonal Pricing**: Sconto Natale 10-15% per bestseller
- **Bundle Offers**: 2 libri -5% (cross-selling)

### Gestione Inventario Media (Musica, Film, Giochi)

#### Strategie Stock
```
HIGH VELOCITY (Stock > 5 unità):
- Bestseller DVD, CD recenti, giochi mainstream
- Update stock giornaliero
- Price dynamic basato demand

MEDIUM VELOCITY (Stock 2-5):
- Specialty music (jazz, classico), film rari
- Update stock 2x/settimana
- Price stabile, emphasis su condition

LOW VELOCITY (Stock < 2):
- Vinyl raro, film out-of-print, retro games
- Stock update settimanale
- Premium pricing, detailed description
```

#### Ricerca Nichia
- Vinili rari hanno **alto margine** (specialista audience)
- Classico jazz ha **bassa concorrenza** vs. pop
- Game retro ha **crescente demand** (nostalgia market)
- Build reputation in nichia, premium pricing justified

### Marketing & Visibility Opzioni

#### Featured Placement (a Pagamento)
```
Cost: 0,50-2,00 EUR per giorno per prodotto
Position: Homepage category, Featured section
Best ROI: Bestseller stagionali, nuovi arrivi
Timing: Natale (dicembre), Back-to-school (settembre), regalo giorni
```

#### Advertising IBS Ads (Self-Service)
```
Model: Cost-per-click, self-service dashboard
Cost: 0,05-0,50 EUR per click (bidding)
ROI Tracking: Conversion pixel, seller dashboard
Best For: High-margin products, test demand
```

#### Seasonal Campaigns Sponsor
- **Natale**: IBS official promo, sconti coordinated
- **Black Friday**: Novembre events
- **Back-to-school**: Agosto-settembre, articoli scolastici
- **Easter**: Aprile, gift items, libri bambini

### Gestione Recessi & Reclami

#### Strategie Riduzione Resi
1. **Accurate Description**: Fotografie chiare, condizione esatta
2. **Quality Control**: Ispezionare ogni libro pre-spedire
3. **Packaging Excellence**: Imbottitura adeguata, zero danno transito
4. **Fast Communication**: Rispondere 24h customer dubbi pre-acquisto

#### Handling Reclami Professionalmente
- **Accettare Reso**: Se cliente ha ragione, processo veloce (3 giorni accettazione)
- **Proposta Alternativa**: Sconto parziale se cliente soddisfatto senza reso
- **Documentazione**: Foto danno/condizione discrepanza, supporta contestazione IBS
- **Improvement**: Analizzare pattern reclami, fix root cause (es. imballaggio)

---

## 17. Risorse & Contatti

### URL Principali

| Risorsa | URL |
|---------|-----|
| **Sito Principale** | https://www.ibs.it |
| **Seller Portal** | https://seller.ibs.it |
| **API Documentation** | https://dev.ibs.it/docs |
| **ChannelEngine Integration** | https://www.channelengine.net (partner) |
| **Seller FAQ** | https://seller.ibs.it/help/faq |
| **Contact Seller Support** | support@seller.ibs.it |
| **Status Page** | https://status.ibs.it |
| **Feltrinelli Group** | https://www.feltrinelligroup.com |

### Contatti Support

#### Tier 1: Email Support (24-48h response)
```
Email: seller-support@ibs.it
Argomenti: Problemi generali, domande policy, reclami
Tempo risposta: Max 48 ore
```

#### Tier 2: Telefono (Premium Sellers)
```
Numero: +39 02 XXXX XXXX (Milano HQ)
Orari: Lunedì-Venerdì 9-17 CET
Lingue: Italiano, Inglese
Abbonamento: Richiesto (Professional+ tier)
```

#### Tier 3: Dedicated Account Manager (Enterprise)
```
Assignment: Sellers 500+ ordini/mese
Funzioni: Monthly calls, strategic planning, rate negotiation
Contatto: Direct email + Slack integration
```

### Risorse Onboarding Sellers

#### Guida Startup
1. **Getting Started Guide**: PDF scaricabile da seller portal
2. **Video Tutorials**: YouTube channel IBS Sellers (feed upload, API, pricing)
3. **Webinar Mensili**: Live Q&A con seller team (registrazione richiesta)
4. **Sandbox Environment**: Test API integration prima production (dev.ibs.it)

#### Marketplace Stats & Insights
- **Public Dashboard**: https://marketplace.ibs.it/insights (anonymized stats)
- **Category Trends**: Rapporto mensile trend per categoria
- **Competitive Pricing**: Benchmark tool vs. competitors (Premium+)
- **Demand Forecast**: Predictive analytics per seasonal (Professional+)

### Documentazione

#### Pubblicazioni Ufficiali
- **Seller Agreement Terms of Service**: Vincolante, accettazione richiesta
- **Commission Policy**: Dettagli commissioni per categoria
- **Data Privacy & Security Policy**: Conformità GDPR
- **Anti-Fraud Policy**: Counterfeit, abuso, reclami procedures
- **Return Policy**: Dettagli diritto recesso consumatore

#### Guide Specializzate
- **Guida ISBN per Sellers**: Cosa è ISBN, validazione, lookup
- **Best Practices Libri Usati**: Condizioni, pricing, inspection
- **Packaging & Logistics**: Requisiti carrier, assicurazione
- **Tax & Compliance Italia**: IVA, fatturazione, obblighi seller
- **ChannelEngine Integration**: Step-by-step setup

---

## Conclusione

IBS.it rappresenta un'opportunità di mercato significativa per sellers specializzati in libri e media fisico, con crescente espansione verso categoria generalista. La piattaforma opera con standard professionali europei, offre integrazione API robusta, e fornisce accesso a audience italiano qualificata.

Sellers che rispettano policy, mantengono elevati standard operativi (fulfillment < 2 giorni, return rate < 3%), e ottimizzano catalogo per SEO interno, possono costruire business sostenibile su IBS.it, particolarmente in nicchia libri, musica specializzata, e media rara.

**Data Documento**: Aprile 2026
**Versione**: 2.3 (Update Q2 2026)
**Prossimo Aggiornamento**: Settembre 2026

---

*Documento creato per Vendiamonoi.it - Digital Distribution Platform su 30+ EU Marketplaces*