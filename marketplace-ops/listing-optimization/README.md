# Listing Optimization — Deep Dive

> **Dominio**: 1 — Marketplace Operations & Listing Optimization
> **Punto Checklist**: 1.1 — Listing Optimization (Titoli, Bullet, Immagini, SEO)
> **Tipo**: Research Deep Dive — Informazioni universali e dogmatiche

---

## 1. Overview

La listing optimization è il processo di costruzione e ottimizzazione delle schede prodotto sui marketplace per massimizzare visibilità organica, click-through rate (CTR), conversion rate (CR) e conformità alle policy di piattaforma. Ogni marketplace ha regole specifiche su struttura, limiti caratteri, requisiti immagine e algoritmi di ranking. Questa guida documenta le specifiche tecniche universali e permanenti per ogni componente della scheda prodotto.

---

## 2. Principi Fondamentali (Dogmi Universali)

### 2.1 Il Triangolo della Listing

Ogni listing è composta da tre pilastri interdipendenti:

**Discoverability** → Il prodotto deve essere trovato (SEO, keyword, categorizzazione)
**Persuasion** → Il prodotto deve convincere (titolo, immagini, bullet, descrizione)
**Compliance** → Il prodotto deve rispettare le regole (policy marketplace, limiti tecnici)

Se manca uno dei tre pilastri, la listing fallisce. Una listing perfetta in SEO ma non compliant viene soppressa. Una listing compliant ma senza keyword non viene trovata. Una listing trovata ma non persuasiva non converte.

### 2.2 Principio di Completezza Dati

Ogni campo disponibile nella scheda prodotto DEVE essere compilato. I marketplace premiano le listing con il 100% dei campi compilati. I campi vuoti sono opportunità perse di ranking e filtro.

**Regola universale**: Un attributo non compilato è un filtro che esclude il tuo prodotto dai risultati di ricerca.

### 2.3 Principio di Specificità

Informazioni generiche non vendono e non rankano. Ogni elemento della listing deve essere il più specifico possibile: dimensioni esatte, materiali precisi, compatibilità elencate, use case definiti.

### 2.4 Principio Mobile-First

Su tutti i marketplace, il 60-80% del traffico è mobile. Solo il titolo (troncato), l'immagine principale e il prezzo sono visibili nel risultato di ricerca mobile. Le prime parole del titolo e la prima immagine determinano il CTR.

---

## 3. Titolo Prodotto — Specifiche Tecniche per Marketplace

### 3.1 Regole Universali dei Titoli

**Struttura titolo ottimale (formula universale)**:
`[Brand] + [Linea Prodotto] + [Attributo Chiave 1] + [Attributo Chiave 2] + [Dimensione/Quantità] + [Colore/Variante]`

**Dogmi titolo**:
- Le prime 5 parole determinano il CTR su mobile (troncamento)
- Il brand DEVE essere la prima parola (obbligatorio su quasi tutti i marketplace)
- Mai usare TUTTO MAIUSCOLO (viola le policy di tutti i marketplace principali)
- Mai inserire prezzo nel titolo
- Mai inserire termini promozionali ("offerta", "sconto", "best seller")
- Mai usare caratteri speciali decorativi (~, *, #, $, @)
- Ogni parola significativa inizia con la maiuscola (Title Case) su Amazon; su altri marketplace seguire le convenzioni locali
- I numeri di modello/SKU vanno inclusi se il cliente cerca per modello
- Le keyword di ricerca principali devono apparire nel titolo, non solo nei backend keywords

### 3.2 Limiti Caratteri Titolo per Marketplace

| Marketplace | Limite Caratteri | Note |
|---|---|---|
| **Amazon** (tutti i mercati EU) | 200 caratteri (max) | Raccomandato: 80 caratteri per mobile. Categorie specifiche possono avere limiti inferiori (es. 80 per Clothing). Oltre 200 = soppressione listing |
| **eBay** | 80 caratteri | Limite rigido. Troncamento a ~55 su mobile |
| **Kaufland** | 255 caratteri | Raccomandato: sotto 150 per leggibilità |
| **Cdiscount** | 132 caratteri | Limite rigido |
| **Fnac-Darty** (via Mirakl) | 255 caratteri | Ma l'interfaccia tronca a ~80 in ricerca |
| **Leroy Merlin** (via Mirakl) | 255 caratteri | Raccomandato: sotto 120 |
| **Carrefour** (via Mirakl) | 255 caratteri | Tronca a ~70 in ricerca mobile |
| **MediaMarkt/Saturn** | 200 caratteri | Raccomandato: sotto 100 |
| **Bol.com** | 255 caratteri | Raccomandato: sotto 150. Il titolo influenza direttamente il ranking |
| **ManoMano** | 255 caratteri | Focus su attributi tecnici nel titolo |
| **METRO Markets** | 255 caratteri | B2B: includere quantità e unità di vendita |
| **Allegro** | 75 caratteri | Limite molto stretto, essenzialità obbligatoria |
| **ePrice** | 200 caratteri | Raccomandato: sotto 120 |
| **Rue du Commerce** | 255 caratteri | Tronca in ricerca a ~90 |
| **Worten** | 200 caratteri | Raccomandato: sotto 130 |
| **PC Componentes** | 200 caratteri | Focus tecnico: specifiche nel titolo |
| **Shopify** (proprio store) | Nessun limite tecnico | SEO Google: sotto 60 caratteri per title tag |

### 3.3 Regole Titolo Specifiche per Marketplace

**Amazon**: Non ripetere il brand se è già nell'attributo "Brand" (dipende dalla categoria). Non usare "by [Brand]". Il titolo alimenta l'algoritmo A10 come segnale primario di rilevanza.

**eBay**: Le keyword nel titolo sono il fattore #1 dell'algoritmo Cassini. eBay NON ha backend keywords — tutto deve essere nel titolo. Usare tutte le varianti di keyword (sinonimi) nei 80 caratteri disponibili.

**Kaufland**: Supporta titoli lunghi ma il ranking premia titoli chiari e strutturati. Il sistema interno di matching funziona su keyword nel titolo.

**Bol.com**: Il titolo è il fattore di ranking più importante. Bol.com usa un proprio algoritmo che pesa titolo > attributi > descrizione.

---

## 4. Bullet Points & Descrizione — Specifiche Tecniche

### 4.1 Regole Universali Bullet Points

**Dogmi bullet points**:
- Ogni bullet point deve contenere UN beneficio + UN dato tecnico
- Struttura: `[BENEFICIO/FEATURE IN MAIUSCOLO]: Spiegazione con dettaglio tecnico`
- I primi 3 bullet sono i più importanti (unici visibili su mobile senza espansione)
- Mai duplicare informazioni già nel titolo come primo contenuto dei bullet
- Includere keyword secondarie nei bullet (non stuffing, ma naturale)
- Mai inserire informazioni su prezzo, spedizione, o promozioni
- Mai inserire link o riferimenti a siti esterni

### 4.2 Limiti Bullet Points per Marketplace

| Marketplace | N° Bullet | Caratteri per Bullet | Note |
|---|---|---|---|
| **Amazon** | 5 (standard), fino a 10 in alcune categorie | 500 caratteri per bullet (raccomandato: 200) | I primi 3 visibili su mobile senza scroll |
| **eBay** | N/A (descrizione libera) | 500.000 caratteri per descrizione | HTML consentito ma limitato. No JavaScript, no form, no link esterni attivi |
| **Kaufland** | Supportati via attributo | 1.000 caratteri descrizione | Strutturare con bullet nella descrizione |
| **Cdiscount** | Fino a 5 | 200 caratteri per bullet | Molto restrittivo |
| **Bol.com** | Fino a 8 | 300 caratteri per bullet | Influenzano il ranking |
| **ManoMano** | Fino a 10 | 500 caratteri per bullet | Focus tecnico: specifiche misurabili |
| **Shopify** | Descrizione libera | Nessun limite tecnico | SEO Google: strutturare con H2/H3, bullet HTML, testo unico |

### 4.3 Descrizione Prodotto

**Regole universali descrizione**:
- La descrizione deve essere UNICA per ogni marketplace (no contenuto duplicato cross-platform per SEO)
- Struttura ideale: Paragrafo introduttivo → Caratteristiche tecniche → Use case → Contenuto della confezione
- La descrizione è il luogo per keyword long-tail che non entrano nel titolo
- Su Amazon: il campo descrizione ha meno peso SEO dei bullet, ma è cruciale per A+ Content / Brand Story
- Su eBay: la descrizione è il contenuto principale — HTML responsive, immagini inline, struttura chiara
- Su marketplace Mirakl: la descrizione spesso è l'unico campo "libero" — usarlo al massimo

### 4.4 HTML nella Descrizione

| Marketplace | HTML Consentito | Tag Permessi | Divieti |
|---|---|---|---|
| **Amazon** | Solo in A+ Content (Brand Registry) | Nella descrizione base: testo puro, `<br>`, `<b>`, `<ul>`, `<li>` | No `<script>`, no CSS inline, no `<iframe>` |
| **eBay** | Sì, HTML completo | Tutti i tag standard HTML/CSS | No JavaScript, no `<form>`, no `<iframe>`, no link a siti esterni per vendita |
| **Kaufland** | HTML limitato | `<br>`, `<b>`, `<i>`, `<ul>`, `<li>`, `<p>` | No script, no immagini inline |
| **Bol.com** | HTML limitato | `<b>`, `<i>`, `<ul>`, `<li>`, `<br>`, `<p>` | No immagini, no CSS, no script |
| **Cdiscount** | HTML limitato | Tag base di formattazione | No script, no CSS complesso |
| **Marketplace Mirakl** | Dipende dalla configurazione dell'operatore | Generalmente: tag base HTML | Verificare per ogni operatore Mirakl |

---

## 5. Immagini Prodotto — Specifiche Tecniche

### 5.1 Regole Universali Immagini

**Dogmi immagini**:
- L'immagine principale DEVE avere sfondo bianco puro (RGB 255,255,255) su Amazon, eBay, Kaufland
- Il prodotto deve occupare almeno l'80% del frame nell'immagine principale
- Nessun testo, logo, watermark, bordo o badge sull'immagine principale (Amazon la rifiuta automaticamente)
- Immagini secondarie: lifestyle, dettagli, dimensioni, packaging, infografiche con testo
- La prima immagine è il fattore #1 di CTR — più importante del titolo per la conversione visiva
- Formato: JPEG (.jpg) per foto, PNG (.png) per infografiche con testo. TIFF accettato da Amazon.
- Color space: sRGB (standard per web)
- Le immagini devono essere SQUARE (1:1) per la maggior parte dei marketplace

### 5.2 Specifiche Immagini per Marketplace

| Marketplace | Dimensione Minima | Dimensione Raccomandata | Max Immagini | Formato | Sfondo Principale |
|---|---|---|---|---|---|
| **Amazon** | 1000×1000 px (per zoom) | 2000×2000 px | 9 (7 standard + 2) | JPEG, PNG, TIFF, GIF (no animato) | Bianco puro (RGB 255,255,255) |
| **eBay** | 500×500 px | 1600×1600 px | 24 | JPEG, PNG | Bianco preferito ma non obbligatorio |
| **Kaufland** | 500×500 px | 1500×1500 px | 10 | JPEG, PNG | Bianco puro obbligatorio per principale |
| **Bol.com** | 1200×1200 px | 2400×2400 px | 8 | JPEG, PNG | Bianco puro obbligatorio |
| **Cdiscount** | 450×450 px | 1500×1500 px | 9 | JPEG, PNG | Bianco raccomandato |
| **Fnac-Darty** | 500×500 px | 1200×1200 px | 9 | JPEG, PNG | Bianco obbligatorio |
| **Carrefour** | 800×800 px | 1500×1500 px | 7 | JPEG, PNG | Bianco obbligatorio |
| **ManoMano** | 800×800 px | 1500×1500 px | 8 | JPEG, PNG | Bianco preferito |
| **Leroy Merlin** | 800×800 px | 1500×1500 px | 6 | JPEG, PNG | Bianco obbligatorio |
| **Allegro** | 800×800 px | 1500×1500 px | 16 | JPEG, PNG | Bianco raccomandato |
| **MediaMarkt** | 500×500 px | 1500×1500 px | 10 | JPEG, PNG | Bianco obbligatorio |
| **METRO Markets** | 800×800 px | 1500×1500 px | 8 | JPEG, PNG | Bianco obbligatorio |
| **Shopify** | Nessun minimo tecnico | 2048×2048 px | Illimitato | JPEG, PNG, GIF, WEBP | Libero (brand-dependent) |

### 5.3 Amazon — Regole Immagini Avanzate

**Immagine principale (MAIN)**:
- Sfondo bianco puro RGB (255,255,255) — obbligatorio, verificato automaticamente
- Solo il prodotto, niente accessori non inclusi
- Niente testo, grafica, logo overlay
- Il prodotto deve riempire almeno l'85% del frame
- No mannequini invisibili per abbigliamento (ghost mannequin vietato in alcune categorie)
- Il prodotto deve essere fuori dalla confezione (mostrato com'è, non la scatola)

**Immagini secondarie (raccomandazione universale)**:
1. MAIN — sfondo bianco, prodotto puro
2. Angolazione alternativa
3. Dettaglio/close-up feature chiave
4. Infografica con dimensioni e specifiche
5. Lifestyle/ambientazione d'uso
6. Contenuto della confezione
7. Scala/comparazione dimensionale (es. prodotto accanto a oggetto noto)

### 5.4 Dimensione File e Performance

| Marketplace | Max Dimensione File | Compressione Raccomandata |
|---|---|---|
| **Amazon** | 10 MB per immagine | JPEG qualità 85-95% |
| **eBay** | 7 MB per immagine | JPEG qualità 80-90% |
| **Bol.com** | 10 MB per immagine | JPEG qualità 85-95% |
| **Marketplace Mirakl** | Varia (generalmente 5-10 MB) | JPEG qualità 85% |
| **Shopify** | 20 MB per immagine | WEBP per performance, JPEG fallback |

---

## 6. SEO & Algoritmi di Ranking

### 6.1 Fattori di Ranking Universali (Cross-Marketplace)

Tutti i marketplace usano varianti degli stessi segnali fondamentali:

**Segnali di Rilevanza (Relevance)**:
- Keyword match nel titolo (peso massimo)
- Keyword match nei bullet/attributi (peso medio)
- Keyword match nella descrizione (peso basso-medio)
- Categorizzazione corretta (peso alto)
- Completezza attributi (peso medio-alto)

**Segnali di Performance (Performance)**:
- Conversion Rate (CR) — il segnale più potente
- Click-Through Rate (CTR) — segnale primario
- Sales velocity — volume vendite recenti
- Seller performance metrics (spedizione, resi, feedback)
- Prezzo competitivo relativo alla categoria

**Segnali di Qualità (Quality)**:
- Numero e qualità immagini
- Completezza scheda prodotto (% campi compilati)
- Recensioni (numero e media)
- Tasso di reso basso
- Risposta messaggi veloce

### 6.2 Amazon — Algoritmo A10

L'algoritmo A10 (evoluzione dell'A9) determina il ranking organico su Amazon. Fattori ordinati per peso:

1. **Sales Velocity** — Il volume di vendite recenti è il fattore dominante
2. **Conversion Rate** — Rapporto tra visite e acquisti sulla listing
3. **Keyword Relevance** — Match tra query di ricerca e contenuto listing (titolo > bullet > backend keywords > descrizione)
4. **Click-Through Rate** — Rapporto tra impressioni e click (influenzato da immagine principale + titolo + prezzo + rating)
5. **Backend Search Terms** — 250 byte (non caratteri) per inserire keyword aggiuntive non visibili al cliente
6. **Seller Authority** — Storico venditore, feedback, metriche performance
7. **External Traffic** — Traffico da fonti esterne (social, Google) è un segnale positivo
8. **Organic Sales vs PPC Sales** — Le vendite organiche pesano più delle vendite PPC nel ranking

**Amazon Backend Search Terms — Regole**:
- Limite: 250 byte (≈250 caratteri ASCII, meno per caratteri speciali/accentati)
- Non ripetere keyword già nel titolo (spreco di byte)
- Separare con spazi (non virgole, non punto e virgola)
- Tutto minuscolo (Amazon non è case-sensitive)
- Includere: sinonimi, varianti ortografiche, termini in lingua locale, errori di battitura comuni
- Non includere: brand concorrenti (viola policy), termini offensivi, ASIN concorrenti
- Non ripetere plurali se il singolare è già presente (Amazon li associa)

### 6.3 eBay — Algoritmo Cassini

Cassini è l'algoritmo di ricerca di eBay. Differisce da Amazon perché eBay NON ha backend keywords:

1. **Title Keywords** — Fattore #1 assoluto. Tutte le keyword devono essere nel titolo (80 caratteri)
2. **Item Specifics** — I campi attributo compilati sono il fattore #2. Più specifics = più visibilità nei filtri
3. **Listing Quality** — Completezza della listing (immagini, descrizione, specifics)
4. **Seller Performance** — Defect rate, late shipment rate, tracking upload rate
5. **Click-Through Rate** — Cassini monitora il CTR e demote listing con CTR basso
6. **Conversion Rate** — Le listing che convertono salgono
7. **Recent Sales** — Velocità di vendita recente
8. **Free Shipping** — Le listing con spedizione gratuita hanno un boost di visibilità
9. **Best Match Algorithm** — eBay bilancia rilevanza + valore per il buyer + performance venditore

**eBay Item Specifics — Regole**:
- Compilare TUTTI gli item specifics disponibili per la categoria
- eBay aggiunge continuamente nuovi specifics — aggiornarli regolarmente
- Gli specifics alimentano i filtri laterali: se manca uno specific, il prodotto scompare quando il buyer filtra
- Usare i valori suggeriti da eBay (dropdown) quando disponibili per massima compatibilità

### 6.4 Bol.com — Algoritmo di Ranking

Bol.com ha un algoritmo proprietario con focus sulla qualità del contenuto:

1. **Titolo** — Fattore primario di matching
2. **Attributi Prodotto** — Completezza e accuratezza
3. **Content Score** — Bol.com assegna un punteggio di qualità contenuto (visibile nel seller dashboard)
4. **Prezzo** — Competitività rispetto ad altri seller sullo stesso prodotto
5. **Performance Venditore** — Rating, cancellation rate, delivery performance
6. **Stock Availability** — Prodotti sempre disponibili rankano meglio

### 6.5 Kaufland — Fattori di Ranking

1. **Titolo + Attributi** — Keyword matching primario
2. **Prezzo** — Kaufland è fortemente price-driven
3. **Disponibilità e Tempo di Spedizione** — Tempi di consegna rapidi premiano il ranking
4. **Performance Venditore** — Cancellation rate, late shipment, feedback
5. **Completezza Prodotto** — Tutti i campi compilati

---

## 7. Identificatori Prodotto (EAN/GTIN/UPC)

### 7.1 Regole Universali

**Dogmi identificatori**:
- Ogni prodotto DEVE avere un EAN/GTIN-13 valido per essere pubblicato sulla maggioranza dei marketplace europei
- L'EAN deve essere registrato presso GS1 e appartenere al brand owner
- Un EAN = un prodotto specifico (SKU). Varianti diverse (colore, taglia) = EAN diversi
- MAI riutilizzare un EAN per prodotti diversi
- MAI usare EAN inventati o non registrati — i marketplace verificano con il database GS1
- Il GTIN-14 (per cartoni/pallet) non sostituisce il GTIN-13 (per unità di vendita)

### 7.2 Requisiti EAN per Marketplace

| Marketplace | EAN Obbligatorio | Eccezioni |
|---|---|---|
| **Amazon** | Sì (GTIN-13 per EU) | Brand Registry con esenzione GTIN (da richiedere) |
| **eBay** | Sì per la maggior parte delle categorie | Alcune categorie vintage/handmade esenti |
| **Kaufland** | Sì | Rarissime eccezioni |
| **Bol.com** | Sì | No eccezioni |
| **Cdiscount** | Sì | No eccezioni |
| **Fnac-Darty** | Sì | No eccezioni |
| **Carrefour** | Sì | No eccezioni |
| **ManoMano** | Sì | Alcune categorie con codice MPN alternativo |
| **Allegro** | Raccomandato, non sempre obbligatorio | Dipende dalla categoria |
| **METRO Markets** | Sì | No eccezioni |

### 7.3 Verifica EAN

- Verificare SEMPRE la validità dell'EAN con il check digit algorithm (Modulo 10)
- Verificare la corrispondenza EAN-prodotto su gepir.gs1.org (database globale GS1)
- Un EAN errato causa: soppressione listing, merge con prodotto sbagliato, violazione policy
- Nei feed CSV/XML: il campo EAN deve essere STRING (non numero) per preservare gli zeri iniziali

---

## 8. Categorizzazione

### 8.1 Regole Universali

**Dogmi categorizzazione**:
- Ogni prodotto deve essere nella categoria PIÙ SPECIFICA disponibile (mai in categorie generiche/parent)
- La categoria determina quali attributi sono disponibili e obbligatori
- Categoria sbagliata = attributi sbagliati = ranking penalizzato
- Ogni marketplace ha il proprio albero categorie — il mapping è obbligatorio
- Il mapping categorie deve essere gestito con una tabella di corrispondenza (category mapping table)
- Tools come Channable e ChannelEngine hanno mapping categorie integrato

### 8.2 Impatto della Categorizzazione

- **Filtri laterali**: La categoria determina quali filtri appaiono. Categoria sbagliata = filtri irrilevanti = prodotto invisibile
- **Commissioni**: Su molti marketplace, la commissione varia per categoria. Categoria sbagliata = commissione errata
- **Competitor set**: La categoria determina contro chi competi nel ranking. Categoria troppo ampia = concorrenza eccessiva
- **Attributi obbligatori**: Ogni categoria ha attributi specifici. Se la categoria è sbagliata, gli attributi richiesti non corrispondono al prodotto

---

## 9. Varianti e Relazioni Prodotto

### 9.1 Regole Universali Varianti

**Dogmi varianti**:
- Le varianti (parent-child) raggruppano prodotti che differiscono per un attributo (colore, taglia, formato)
- Ogni variante DEVE avere un proprio EAN univoco
- Le varianti condividono la pagina prodotto: le recensioni si aggregano sul parent
- Il parent non è acquistabile — solo i child sono acquistabili
- La variante con più vendite/recensioni determina il ranking del gruppo
- Varianti correlate ma non identiche (accessori, prodotti complementari) NON devono essere varianti — usare cross-sell/upsell

### 9.2 Struttura Varianti per Marketplace

| Marketplace | Supporto Varianti | Tipo Relazione | Note |
|---|---|---|---|
| **Amazon** | Parent-Child | Variation theme (Color, Size, etc.) | Massimo 2 dimensioni di variazione |
| **eBay** | Multi-variation listing | Variation specifics | Fino a 5 dimensioni di variazione |
| **Kaufland** | Sì | Grouping by attribute | Limitato a colore/taglia |
| **Bol.com** | Sì | Family grouping | Tramite attributo "Family" |
| **Cdiscount** | Limitato | Modello parent-child semplificato | Verificare per categoria |
| **Shopify** | Sì | Varianti con opzioni | Fino a 3 opzioni, 100 varianti per prodotto |

---

## 10. Content Quality Score e Completezza

### 10.1 Metriche di Qualità Contenuto

Molti marketplace assegnano un punteggio di qualità al contenuto della listing:

- **Amazon**: "Listing Quality Dashboard" — mostra avvisi per immagini mancanti, titoli non conformi, attributi vuoti
- **Bol.com**: "Content Score" — punteggio percentuale visibile, target >85%
- **Kaufland**: Quality indicators nel seller portal
- **eBay**: "Listing Quality Report" — suggerimenti di miglioramento

### 10.2 Checklist Completezza Universale

Per ogni listing, verificare:
- Titolo: compilato, entro limiti, keyword presente, struttura corretta
- Immagine principale: sfondo bianco, dimensione minima rispettata, prodotto >80% frame
- Immagini secondarie: almeno 4-5 immagini (lifestyle, dettaglio, infografica, scala)
- Bullet points: tutti compilati, struttura beneficio+dato tecnico
- Descrizione: unica, completa, keyword long-tail, struttura chiara
- EAN/GTIN: valido, verificato, corrispondente al prodotto
- Categoria: la più specifica disponibile
- Attributi: TUTTI compilati, valori corretti e specifici
- Varianti: configurate correttamente se applicabile
- Backend keywords (Amazon): compilati, 250 byte utilizzati, no duplicati del titolo

---

## 11. Errori Comuni e Critici

### 11.1 Errori che Causano Soppressione/Rimozione

1. **EAN non valido o non corrispondente** — Listing rimossa automaticamente
2. **Titolo con claim non verificabili** ("il migliore", "n°1") — Violazione policy
3. **Immagini con watermark o testo promozionale** — Soppressione su Amazon
4. **Contenuto copiato da altri seller** — Penalizzazione SEO, possibile rimozione
5. **Prezzi nel titolo o bullet** — Violazione policy su tutti i marketplace
6. **Categoria errata deliberata** (per evitare commissioni) — Rischio ban account

### 11.2 Errori che Penalizzano il Ranking

1. **Attributi non compilati** — Prodotto escluso dai filtri
2. **Una sola immagine** — CTR drasticamente inferiore
3. **Titolo generico senza keyword** — Invisibilità organica
4. **Descrizione uguale su tutti i marketplace** — Nessun vantaggio SEO, contenuto duplicato
5. **Varianti non configurate** — Recensioni frammentate, ranking diluito
6. **Backend keywords con ripetizioni** — Spreco di byte su Amazon

---

## 12. Tool e Automazione per Listing Optimization

### 12.1 Tool per Gestione Feed

| Tool | Funzione | Marketplace Supportati |
|---|---|---|
| **Channable** | Feed management, mapping attributi, regole automatiche | 2500+ canali |
| **ChannelEngine** | Feed management, order management, repricing | 700+ marketplace |
| **DataFeedWatch** | Feed optimization, regole trasformazione | 2000+ canali |
| **Lengow** | Feed management, marketplace integration | 1600+ canali |
| **Productsup** | Feed management enterprise, data quality | Illimitati |

### 12.2 Tool per Keyword Research

| Tool | Funzione |
|---|---|
| **Helium 10** | Keyword research Amazon (Cerebro, Magnet), listing builder, tracking |
| **Jungle Scout** | Keyword research Amazon, competitor analysis |
| **Terapeak** (eBay) | Ricerca keyword e trend eBay (integrato in Seller Hub) |
| **Google Keyword Planner** | Keyword research per Shopify e marketplace con SEO Google |
| **Merchant Words** | Keyword database Amazon multi-mercato |

### 12.3 Tool per Immagini

| Tool | Funzione |
|---|---|
| **Pixelcut / Remove.bg** | Rimozione sfondo automatica per immagine principale |
| **Canva Pro** | Creazione infografiche prodotto e immagini lifestyle |
| **Adobe Photoshop/Lightroom** | Editing professionale, batch processing |
| **Clipping Magic** | Rimozione sfondo con edge refinement |

---

## 13. Specifiche per Vendiamonoi — Implementazione Operativa

### 13.1 Workflow di Creazione Listing

1. **Input**: Ricevere catalogo fornitore (CSV/Excel) con dati grezzi
2. **Pulizia**: Validare EAN, normalizzare titoli, verificare categoria
3. **Arricchimento**: Aggiungere keyword, completare attributi, scrivere bullet
4. **Immagini**: Verificare dimensioni minime, sfondo bianco, numero minimo
5. **Feed**: Generare feed per ogni marketplace via Channable/ChannelEngine
6. **Mapping**: Applicare mapping categorie marketplace-specifico
7. **Pubblicazione**: Push feed ai marketplace
8. **Verifica**: Controllare listing live, risolvere errori, controllare quality score

### 13.2 Regole per Feed Automation

- Il campo EAN nel feed deve essere tipo STRING (non numerico) per preservare zeri iniziali
- I titoli devono essere generati con template per marketplace (diverso per ogni canale)
- Le immagini devono essere URL HTTPS accessibili pubblicamente (no autenticazione, no redirect)
- Il feed deve includere TUTTI gli attributi mappati, anche se vuoti (per evitare errori di schema)
- Frequenza aggiornamento feed: almeno ogni 2-4 ore per stock/prezzo, giornaliero per contenuto
- Implementare regole di esclusione: non pubblicare prodotti senza EAN, senza immagine, con stock 0

### 13.3 KPI di Listing Quality

| KPI | Target | Frequenza Monitoraggio |
|---|---|---|
| Content Score (dove disponibile) | >85% | Settimanale |
| Completezza attributi | 100% campi obbligatori, >90% facoltativi | Ogni pubblicazione |
| Immagini per listing | ≥5 per prodotto | Ogni pubblicazione |
| Listing con errori/warning | <2% del catalogo | Giornaliero |
| Tasso soppressione | <0.5% del catalogo | Giornaliero |
| CTR medio (dove misurabile) | >3% categoria media | Settimanale |
| Conversion Rate | >10% (varia per categoria) | Settimanale |

---

## 14. Fonti e Riferimenti

Tutte le specifiche tecniche documentate in questo file provengono da:
- Documentazione ufficiale Seller Central Amazon (sellercentral.amazon.it/de/fr/es/nl)
- eBay Seller Hub documentation
- Kaufland Seller Portal documentation
- Bol.com Partner Platform documentation
- Documentazione Mirakl Connect (per marketplace Mirakl-powered)
- Channable knowledge base
- ChannelEngine documentation
- GS1 International standards (gs1.org)
- Helium 10 / Jungle Scout knowledge bases

> **Ultimo aggiornamento**: 2026-04-02
> **Autore**: Vendiamonoi Knowledge Base — AI-assisted research
> **Stato**: Prima versione completa — da aggiornare con nuove specifiche marketplace
