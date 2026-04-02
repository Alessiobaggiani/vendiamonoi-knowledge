# Listing Performance Metrics — Deep Dive

> **Dominio**: 1 — Marketplace Operations & Listing Optimization
> **Punto Checklist**: 1.4 — Listing Performance Metrics
> **Tipo**: Research Deep Dive — Informazioni universali e dogmatiche

---

## 1. Overview

Le performance metrics delle listing sono i segnali quantitativi che misurano la salute e l'efficacia di ogni scheda prodotto. Monitorarle è obbligatorio per identificare problemi, ottimizzare conversioni e mantenere il ranking. Questo documento copre le metriche fondamentali, i benchmark per categoria, gli strumenti di monitoraggio e i framework decisionali.

**Principio fondamentale**: Una listing non monitorata è una listing che degenera. Le performance cambiano nel tempo per fattori interni (contenuto, prezzo, stock) ed esterni (concorrenza, stagionalità, algoritmo).

---

## 2. Click-Through Rate (CTR)

### 2.1 Definizione e Calcolo

**CTR** = (Click sulla listing / Impressioni totali) × 100

Il CTR misura l'attrattività della listing nei risultati di ricerca. È influenzato da:
- Immagine principale (fattore #1)
- Titolo (prime 5-8 parole visibili)
- Prezzo
- Rating stelle e numero recensioni
- Badge (Prime, Free Shipping, Best Seller)
- Posizione nei risultati (rank)

### 2.2 Benchmark CTR per Marketplace

| Marketplace | CTR Medio Organico | CTR Buono | CTR Eccellente | Note |
|---|---|---|---|---|
| **Amazon** (tutte le categorie) | 0.4-0.8% | 1.0-2.0% | >2.5% | Varia enormemente per posizione: posizione 1 = 15-30% CTR, posizione 10 = 1-3% |
| **Amazon** (Sponsored Products) | 0.3-0.5% | 0.5-1.0% | >1.5% | CTR ads più basso dell'organico per la stessa posizione |
| **eBay** | 0.5-1.5% | 1.5-3.0% | >3.0% | CTR medio più alto di Amazon (meno listing per pagina) |
| **Kaufland** | 0.3-0.7% | 0.8-1.5% | >1.5% | Marketplace più giovane, meno concorrenza |
| **Bol.com** | 0.5-1.0% | 1.0-2.0% | >2.0% | Audience olandese/belga più targetizzata |
| **Shopify** (Google Shopping) | 1.0-2.0% | 2.0-4.0% | >4.0% | CTR Google Shopping mediamente più alto |

**Fattori che influenzano il CTR per posizione (Amazon)**:

| Posizione Organica | CTR Medio Stimato |
|---|---|
| 1 | 15-35% |
| 2 | 8-15% |
| 3 | 5-10% |
| 4-5 | 3-6% |
| 6-10 | 1-3% |
| 11-20 (pagina 2) | 0.5-1.5% |
| 21+ (pagina 3+) | <0.5% |

### 2.3 Benchmark CTR per Categoria (Amazon)

| Categoria | CTR Organico Medio | Note |
|---|---|---|
| Electronics | 0.3-0.6% | Alta concorrenza, basso CTR |
| Home & Kitchen | 0.5-1.0% | Visivamente differenziabile |
| Beauty & Personal Care | 0.6-1.2% | Brand loyalty alto |
| Sports & Outdoors | 0.5-0.9% | Stagionalità forte |
| Toys & Games | 0.7-1.5% | Picchi Q4 |
| Fashion/Clothing | 0.4-0.8% | Alto return rate impatta sentiment |
| Grocery & Food | 0.3-0.5% | Basso CTR, alto repeat purchase |
| Baby Products | 0.6-1.0% | Buyer attento, ricerca lunga |
| Automotive | 0.3-0.5% | Ricerca per numero modello/OEM |
| Garden & Outdoor | 0.5-1.0% | Forte stagionalità primavera |

### 2.4 Diagnosi CTR Basso

**CTR sotto il benchmark di categoria indica problemi in**:

1. **Immagine principale** (causa più comune)
   - Sfondo non bianco puro
   - Prodotto troppo piccolo nel frame
   - Qualità bassa / sfocata
   - Non mostra chiaramente il prodotto
   - **Fix**: A/B test con nuova immagine principale

2. **Titolo non ottimizzato**
   - Keyword principale assente nelle prime parole
   - Titolo generico ("Prodotto di qualità")
   - Titolo troppo lungo troncato su mobile
   - **Fix**: Ristrutturare con keyword primaria + brand + attributo chiave

3. **Prezzo non competitivo**
   - Prezzo significativamente sopra la media di categoria
   - Nessun badge sconto/coupon
   - **Fix**: Analisi competitiva, coupon, repricing

4. **Rating/Recensioni**
   - Rating sotto 4.0 stelle
   - Poche recensioni (<10)
   - **Fix**: Migliorare prodotto/servizio, programma review request

5. **Posizione nei risultati**
   - Rank troppo basso (pagina 2+)
   - **Fix**: SEO listing + PPC per aumentare sales velocity

---

## 3. Conversion Rate (CR)

### 3.1 Definizione e Calcolo

**CR** = (Ordini / Sessioni sulla listing) × 100

Il Conversion Rate misura l'efficacia della listing nel trasformare visitatori in acquirenti. È il segnale più potente per il ranking su tutti i marketplace.

### 3.2 Benchmark CR per Marketplace

| Marketplace | CR Medio | CR Buono | CR Eccellente | Note |
|---|---|---|---|---|
| **Amazon** (tutte le categorie) | 10-15% | 15-20% | >20% | CR molto alto rispetto a ecommerce tradizionale (media web: 2-3%) |
| **Amazon** (da Sponsored Products) | 8-12% | 12-18% | >18% | CR da PPC leggermente inferiore all'organico |
| **eBay** | 3-5% | 5-10% | >10% | CR più basso: buyer compara di più |
| **Kaufland** | 5-8% | 8-12% | >12% | Audience tedesca: decisione rapida se prezzo giusto |
| **Bol.com** | 5-10% | 10-15% | >15% | CR medio-alto per audience NL/BE |
| **Shopify** (DTC) | 1.5-3% | 3-5% | >5% | CR DTC molto più basso dei marketplace |

### 3.3 Benchmark CR per Categoria (Amazon)

| Categoria | CR Medio | Note |
|---|---|---|
| Electronics | 8-12% | Ricerca lunga, confronto specs |
| Home & Kitchen | 12-18% | Acquisto d'impulso più frequente |
| Beauty & Personal Care | 10-15% | Repeat purchase alto |
| Sports & Outdoors | 10-14% | Stagionalità influenza |
| Grocery & Food | 15-25% | CR altissimo: repeat, necessità |
| Baby Products | 12-18% | Buyer motivato |
| Clothing/Fashion | 5-10% | Basso CR per incertezza taglia |
| Automotive Parts | 8-12% | Ricerca per compatibilità |
| Office Products | 12-16% | Acquisto razionale |

### 3.4 CR per Fonte di Traffico

| Fonte Traffico | CR Relativo | Note |
|---|---|---|
| **Ricerca organica (keyword esatta)** | Baseline (100%) | Il traffico con intent più alto |
| **Ricerca organica (keyword broad)** | 60-80% del baseline | Intent meno preciso |
| **Sponsored Products (keyword esatta)** | 80-95% del baseline | Quasi uguale all'organico |
| **Sponsored Products (keyword broad)** | 50-70% del baseline | Meno targetizzato |
| **Sponsored Display (retargeting)** | 40-60% del baseline | Utente già visto ma non convertito |
| **Sponsored Display (prospecting)** | 20-40% del baseline | Audience fredda |
| **Traffico esterno (Google)** | 30-50% del baseline | Intent variabile |
| **Traffico esterno (Social)** | 10-30% del baseline | Traffico più freddo |
| **Browse/Category** | 50-70% del baseline | Utente esplora, intent medio |
| **Deal/Coupon traffic** | 120-150% del baseline | CR alto ma margine ridotto |

### 3.5 Diagnosi CR Basso

**CR sotto il benchmark indica problemi in**:

1. **Contenuto listing insufficiente**
   - Poche immagini (<5)
   - Bullet points generici
   - Descrizione vuota o minimale
   - **Fix**: Completare tutti i campi, aggiungere immagini lifestyle e infografiche

2. **Prezzo**
   - Prezzo sopra la media senza giustificazione visibile
   - Shipping cost aggiuntivo visibile
   - **Fix**: Repricing, free shipping, coupon

3. **Recensioni negative**
   - Rating sotto 4.0
   - Recensioni recenti negative in evidenza
   - **Fix**: Risolvere problemi prodotto, rispondere a recensioni, migliorare Q&A

4. **Mismatch aspettativa**
   - Titolo/immagine promettono qualcosa che la descrizione non conferma
   - Varianti confuse (cliente non trova la sua taglia/colore)
   - **Fix**: Allineare titolo, immagini e contenuto. Semplificare varianti

5. **Competizione sulla stessa ASIN**
   - Altro seller ha Buy Box con prezzo inferiore
   - **Fix**: Repricing per vincere Buy Box

6. **Problemi tecnici**
   - Listing su "suppressed" o con warning
   - Immagini non caricano
   - Varianti rotte
   - **Fix**: Controllare Listing Quality Dashboard

---

## 4. Session Duration e Bounce Rate

### 4.1 Definizione

**Session Duration**: Tempo medio che un visitatore trascorre sulla listing.
**Bounce Rate**: Percentuale di visitatori che lasciano la listing senza interazione (no scroll, no click su variante, no add to cart).

### 4.2 Disponibilità per Marketplace

| Marketplace | Session Duration Disponibile | Bounce Rate Disponibile | Dove |
|---|---|---|---|
| **Amazon** | No (non fornito ai seller) | No | Amazon non condivide questi dati con i seller |
| **eBay** | Parziale (Terapeak) | No diretto | Terapeak fornisce metriche engagement |
| **Shopify** | Sì (Google Analytics) | Sì | GA4 integration |
| **Bol.com** | No | No | Non disponibile nel seller dashboard |
| **Kaufland** | No | No | Non disponibile |

**Nota critica**: Su Amazon e la maggior parte dei marketplace, session duration e bounce rate NON sono metriche accessibili ai seller. Sono metriche interne usate dall'algoritmo ma non condivise.

### 4.3 Proxy Metrics (Alternative)

Poiché session duration e bounce rate non sono disponibili, usare proxy:

| Proxy Metric | Cosa Indica | Come Calcolarlo |
|---|---|---|
| **Unit Session Percentage** (Amazon) | CR = proxy inverso di bounce | Ordini / Sessions × 100 |
| **Page Views vs Sessions** | Engagement (più pagine = più interesse) | Page Views / Sessions (ratio >1 = buon engagement) |
| **Add to Cart Rate** | Intent alto anche senza acquisto | Add to Cart / Sessions × 100 |
| **Buy Box Percentage** | Se perdi Buy Box, il "bounce" è verso altro seller | % tempo con Buy Box |

### 4.4 Interpretazione

- **Session Duration alta + CR basso** = Il cliente è interessato ma qualcosa blocca l'acquisto (prezzo, recensioni, incertezza)
- **Session Duration bassa + CR basso** = La listing non è rilevante per la query (problema SEO/categorizzazione)
- **Session Duration bassa + CR alto** = Acquisto rapido e deciso (prodotto commodity, repeat purchase)
- **Session Duration alta + CR alto** = Listing eccellente per prodotti complessi

---

## 5. Return Rate Monitoring

### 5.1 Definizione e Importanza

**Return Rate** = (Unità rese / Unità vendute) × 100

Il return rate è una metrica critica perché:
- Impatta direttamente la profittabilità (costo reso + prodotto potenzialmente non rivendibile)
- Impatta il ranking (Amazon penalizza listing con alto return rate)
- Indica problemi di listing o prodotto
- Può causare soppressione listing se troppo alto

### 5.2 Benchmark Return Rate per Categoria

| Categoria | Return Rate Medio | Soglia Allarme | Note |
|---|---|---|---|
| Electronics | 5-8% | >12% | Resi per difetto o incompatibilità |
| Clothing/Fashion | 15-30% | >35% | Taglia sbagliata è causa #1 |
| Shoes | 20-35% | >40% | Calzata è causa #1 |
| Home & Kitchen | 3-6% | >10% | Aspettativa vs realtà |
| Beauty | 2-4% | >8% | Allergie, texture non gradita |
| Sports & Outdoors | 4-7% | >10% | Taglia/fit per abbigliamento sportivo |
| Toys | 2-4% | >6% | Basso return rate |
| Grocery/Food | <1% | >3% | Quasi nullo (non restituibile) |
| Baby Products | 3-5% | >8% | Compatibilità |
| Furniture | 5-10% | >15% | Dimensioni/assemblaggio |

### 5.3 Analisi Cause Reso

**Problema di LISTING** (risolvibile senza cambiare prodotto):
- "Non come descritto" → Immagini non accurate, descrizione fuorviante
- "Taglia sbagliata" → Size chart assente o errata
- "Colore diverso" → Immagini non color-accurate, variante selezionata sbagliata
- "Non compatibile" → Compatibilità non specificata in listing

**Problema di PRODOTTO** (richiede intervento sul fornitore):
- "Difettoso/Rotto" → Quality control issue
- "Qualità inferiore" → Materiali scadenti
- "Arrivato danneggiato" → Packaging inadeguato

**Problema di ASPETTATIVA** (mismatch listing-prodotto):
- "Più piccolo del previsto" → Aggiungere dimensioni in infografica
- "Non funziona come pensavo" → Migliorare descrizione use case

### 5.4 Framework Decisionale Return Rate

| Return Rate | Azione |
|---|---|
| < Benchmark categoria | Monitoraggio standard (settimanale) |
| Benchmark +5% | Analisi cause. Verificare listing content. Check recensioni negative recenti |
| Benchmark +10% | Azione urgente. Root cause analysis. Confronto con concorrenti. Possibile sospensione listing |
| Benchmark +15% | Rischio soppressione. Considerare rimozione listing fino a fix |
| >30% (qualsiasi categoria) | Amazon può sopprimere automaticamente. Intervento immediato obbligatorio |

---

## 6. Customer Review Sentiment Analysis

### 6.1 Metriche Review

| Metrica | Definizione | Target |
|---|---|---|
| **Rating Medio** | Media stelle (1-5) | ≥4.0 per competitività |
| **Review Velocity** | Nuove review/mese | Proporzionale alle vendite (1-3% dei buyer lascia review) |
| **Sentiment Ratio** | % review positive (4-5★) vs negative (1-2★) | >80% positive |
| **Review Recency** | Età delle ultime 10 review | <90 giorni per rilevanza |

### 6.2 Impatto Rating sulle Conversioni

| Rating | Impatto CR vs 4.5★ Baseline |
|---|---|
| 4.8-5.0 ★ | +5-10% (ma sospetto fake se poche review) |
| 4.5-4.7 ★ | Baseline (ottimale) |
| 4.0-4.4 ★ | -5-15% |
| 3.5-3.9 ★ | -20-35% |
| 3.0-3.4 ★ | -40-60% |
| <3.0 ★ | -70%+ (listing praticamente morta) |

**Soglia critica**: Sotto 3.5 stelle, la listing perde competitività in modo quasi irreversibile. Considerare: nuovo ASIN, fix prodotto radicale, o sunset.

### 6.3 Topic Analysis delle Review

Categorizzare le review negative per tema:

| Tema | Azione |
|---|---|
| **Qualità materiale** | Feedback al fornitore, possibile cambio supplier |
| **Dimensioni/fit** | Aggiornare listing con misure precise, size chart |
| **Funzionalità** | Migliorare descrizione use case, aggiungere FAQ |
| **Packaging/spedizione** | Migliorare imballaggio, verificare logistica |
| **Aspettativa vs realtà** | Allineare immagini e descrizione al prodotto reale |
| **Istruzioni poco chiare** | Aggiungere istruzioni nella listing o nella confezione |
| **Difetti ricorrenti** | Root cause analysis con fornitore, AQL review |

### 6.4 Tool per Review Analysis

| Tool | Funzione | Marketplace |
|---|---|---|
| **Helium 10 (Review Insights)** | Sentiment analysis, keyword extraction da review | Amazon |
| **Jungle Scout (Review Automation)** | Monitoraggio review, alert, trend | Amazon |
| **FeedbackWhiz** | Gestione review, alert negativi, analytics | Amazon |
| **Terapeak** | Analisi trend review eBay | eBay |
| **Bol.com Seller Dashboard** | Rating overview, feedback | Bol.com |
| **Google Alerts** | Monitoraggio menzioni brand | Cross-platform |
| **ReviewMeta** | Analisi autenticità review | Amazon |

---

## 7. Share of Voice (SOV) per Keyword

### 7.1 Definizione

**Share of Voice** = Percentuale di visibilità della tua listing per una keyword specifica rispetto al totale delle impressioni disponibili.

Approssimazione pratica: frequenza con cui la tua listing appare in posizione organica top 10 per una keyword target.

### 7.2 Come Misurare SOV

**Su Amazon**:
- Amazon Brand Analytics (se Brand Registry): Search Query Performance report
  - Mostra: impression share, click share, purchase share per keyword
  - Disponibile per brand registrati
  - Aggiornamento: settimanale
- Tool esterni: Helium 10 Keyword Tracker, Jungle Scout Rank Tracker
  - Monitorano posizione organica per keyword
  - Alert per variazioni significative

**Su eBay**:
- Terapeak: trend di categoria, posizione relativa
- Non esiste un SOV diretto

**Su altri marketplace**:
- SOV generalmente non misurabile con tool dedicati
- Proxy: monitorare manualmente posizione per keyword top 5-10

### 7.3 Benchmark SOV

| Posizione nel Mercato | SOV Target (per keyword primaria) |
|---|---|
| Leader di categoria | 15-30% |
| Top 5 seller | 5-15% |
| Seller medio | 1-5% |
| Nuovo entrante | <1% |

### 7.4 Framework SOV → Azione

| SOV Trend | Diagnosi | Azione |
|---|---|---|
| SOV in crescita | Listing sta guadagnando rilevanza | Mantenere strategia, scalare PPC |
| SOV stabile | Posizione mantenuta | Ottimizzare marginalmente, difendere |
| SOV in calo | Competitor in crescita o listing deteriorata | Analisi urgente: controllare prezzo, stock, rating, contenuto |
| SOV = 0 | Listing non indicizzata per quella keyword | Verificare keyword nel titolo/backend, controllare soppressione |

---

## 8. Framework KPI Integrato per Listing

### 8.1 Dashboard Metriche per Listing

| Metrica | Frequenza Monitoraggio | Tool | Soglia Allarme |
|---|---|---|---|
| **CTR** | Settimanale | Seller Central / Brand Analytics | Sotto benchmark categoria -30% |
| **Conversion Rate** | Settimanale | Seller Central Business Reports | Sotto benchmark categoria -20% |
| **Return Rate** | Settimanale | Seller Central Returns Report | Sopra benchmark +10% |
| **Rating Medio** | Giornaliero | Alert automatico | Sotto 4.0 stelle |
| **Review Velocity** | Settimanale | Tool review monitoring | Calo >50% mese su mese |
| **SOV (keyword top 5)** | Settimanale | Helium 10 / Jungle Scout | Calo >3 posizioni |
| **Buy Box %** | Giornaliero | Seller Central | Sotto 90% |
| **Sessions** | Settimanale | Business Reports | Calo >20% WoW |
| **Page Views** | Settimanale | Business Reports | Calo >20% WoW |
| **Content Quality Score** | Mensile | Listing Quality Dashboard | Sotto 85% |

### 8.2 Prioritizzazione Azioni

**Ordine di priorità quando più metriche sono sotto soglia**:

1. **Buy Box** → Se non hai Buy Box, tutto il resto è irrilevante
2. **Soppressione/Warning** → Risolvere problemi tecnici immediatamente
3. **Rating** → Se sotto 3.5, considerare intervento radicale
4. **Return Rate** → Se sopra soglia, rischio soppressione
5. **CR** → Listing content, pricing, recensioni
6. **CTR** → Immagine principale, titolo, prezzo
7. **SOV** → SEO, PPC, sales velocity

### 8.3 Ciclo di Ottimizzazione

**Settimanale**: Review KPI dashboard, identificare listing sotto soglia
**Bisettimanale**: A/B test su listing sottoperformanti (immagine o titolo)
**Mensile**: Review completa top 20% SKU (quelli che generano 80% revenue)
**Trimestrale**: Audit completo catalogo, sunset listing non profittevoli

---

## 9. Tool e Automazione per Performance Monitoring

### 9.1 Tool Nativi Marketplace

| Marketplace | Tool | Metriche Disponibili |
|---|---|---|
| **Amazon** | Business Reports (Seller Central) | Sessions, Page Views, Units Ordered, CR, Buy Box % |
| **Amazon** | Brand Analytics | Search Query Performance, Click Share, Market Basket |
| **Amazon** | Listing Quality Dashboard | Content score, warning, suppression |
| **eBay** | Seller Hub Analytics | Views, Impressions, CTR, Sales |
| **eBay** | Terapeak | Category trends, competitor analysis |
| **Bol.com** | Seller Dashboard Analytics | Orders, returns, content score |
| **Kaufland** | Seller Portal Reports | Orders, returns, performance metrics |
| **Shopify** | Shopify Analytics + GA4 | Tutto (sessions, bounce, CR, AOV, LTV) |

### 9.2 Tool Terzi

| Tool | Funzione Principale | Marketplace |
|---|---|---|
| **Helium 10** | Keyword tracking, listing health, profit tracking, review monitoring | Amazon |
| **Jungle Scout** | Rank tracking, competitor monitoring, review alerts | Amazon |
| **Sellics/Perpetua** | Profit analytics, advertising optimization | Amazon |
| **DataHawk** | SEO tracking, market intelligence, content scoring | Amazon |
| **ChannelEngine** | Analytics cross-marketplace, performance reporting | Multi-marketplace |
| **Channable** | Feed performance, error reporting | Multi-marketplace |

---

## 10. Specifiche Vendiamonoi — Implementazione Operativa

### 10.1 Setup Monitoraggio

1. **Configurare alert automatici** per: rating sotto 4.0, return rate sopra soglia, Buy Box perso >10% tempo, listing soppressa
2. **Report settimanale** con KPI per top 50 SKU (o top 20% per revenue)
3. **Dashboard centralizzato** che aggrega dati da tutti i marketplace
4. **Review dei dati**: meeting settimanale di 30 minuti per analizzare trend

### 10.2 Workflow di Intervento

1. Alert → Identificare listing problematica
2. Diagnosi → Quale metrica è fuori soglia? Quale causa probabile?
3. Azione → Applicare fix (contenuto, prezzo, immagine, o escalation fornitore)
4. Verifica → Monitorare per 7-14 giorni post-fix
5. Documentare → Registrare causa e fix per knowledge base

---

## 11. Fonti e Riferimenti

- Amazon Seller Central Business Reports documentation
- Amazon Brand Analytics documentation
- eBay Seller Hub Analytics help
- Bol.com Seller Manual (Analytics section)
- Helium 10, Jungle Scout knowledge bases
- Industry benchmark reports (Marketplace Pulse, Feedvisor, Pattern)

> **Ultimo aggiornamento**: 2026-04-02
> **Autore**: Vendiamonoi Knowledge Base — AI-assisted research
> **Stato**: Prima versione completa — benchmark da validare con dati interni
