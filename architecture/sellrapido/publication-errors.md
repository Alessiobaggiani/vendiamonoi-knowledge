# Publication Errors - SellRapido

## Indice

1. [Overview](#overview)
2. [Report Inserzioni](#report-inserzioni)
3. [Errori generali di pubblicazione](#errori-generali-di-pubblicazione)
4. [Errori eBay](#errori-ebay)
5. [Errori Amazon](#errori-amazon)
6. [Errori marketplace europei](#errori-marketplace-europei)
7. [Errori ecommerce](#errori-ecommerce)
8. [Diagnostica e troubleshooting](#diagnostica-e-troubleshooting)

---

## Overview

Gli errori di pubblicazione si verificano quando un prodotto non può essere pubblicato su un marketplace/ecommerce per vari motivi. SellRapido fornisce strumenti per identificare, diagnosticare e risolvere questi errori.

### Flusso gestione errori

1. **Identificazione**: Accesso a Report Inserzioni per visualizzare gli errori
2. **Diagnostica**: Analisi del messaggio di errore e della causa
3. **Correzione**: Modifica dei dati o delle configurazioni
4. **Ripubblicazione**: Riavvio della pubblicazione
5. **Verifica**: Controllo del corretto completamento

---

## Report Inserzioni

### Accesso al Report Inserzioni

Per visualizzare lo stato delle pubblicazioni e gli errori:

1. Accedi a **Prevendita > Report Inserzioni**
2. Seleziona il listino catalogo
3. Seleziona il marketplace/ecommerce (o tutti)
4. Clicca **Report su selezione corrente**

### Informazioni visualizzate

Il report mostra per ogni marketplace:

| Colonna | Descrizione |
|---|---|
| **Marketplace** | Nome del canale di vendita |
| **N° Inserzioni** | Numero totale di inserzioni pubblicate |
| **N° Inserzioni OK** | Numero di inserzioni senza errori |
| **N° Inserzioni in Errore** | Numero di inserzioni con errori |
| **Stato** | Status complessivo (OK, Warning, Error) |
| **Data Ultimo Aggiornamento** | Quando è stato aggiornato il report |
| **Azioni** | Pulsanti per dettagli e rielaborazione |

### Filtraggio errori

1. Nel Report Inserzioni, seleziona un marketplace con errori
2. Clicca **Visualizza Errori**
3. Usa i filtri per identificare specifici problemi:
   - Per prodotto
   - Per tipo di errore
   - Per categoria

### Dettagli errore

Cliccando su un errore specifico, visualizzerai:

- **Numero SKU**: Codice prodotto
- **Titolo**: Nome del prodotto
- **Categoria**: Categoria SellRapido
- **Errore**: Codice e descrizione dell'errore
- **Data errore**: Quando è stato rilevato
- **Possibili cause**: Suggerimenti per la risoluzione

---

## Errori generali di pubblicazione

### E001 - SKU duplicato

**Descrizione**: Un prodotto con lo stesso SKU è già pubblicato su questo marketplace.

**Cause possibili**:
- SKU duplicato nel catalogo
- Prodotto già pubblicato da precedente sessione
- Filtri non escludono correttamente duplicati

**Soluzione**:
1. Verifica che lo SKU sia univoco nel catalogo
2. Se il prodotto è già pubblicato, rimuovere il filtro di inclusione per evitare duplicazione
3. In caso di duplicato accidentale su marketplace, elimina manualmente la vecchia inserzione

---

### E002 - Titolo mancante

**Descrizione**: Il titolo del prodotto è vuoto o non valorizzato.

**Cause possibili**:
- Campo Title non presente nel catalogo
- Template titolo non correttamente configurato
- Dati non importati correttamente

**Soluzione**:
1. Verifica che il campo #Title# sia presente nel catalogo CSV
2. Controlla la configurazione del template titolo in Impostazioni > Titolo e Template
3. Reimporta il catalogo se i dati sono incompleti
4. Rielabora la pubblicazione

---

### E003 - Prezzo non valido

**Descrizione**: Il prezzo inserito non è valido per il marketplace.

**Cause possibili**:
- Prezzo negativo o zero
- Prezzo al di sotto del minimo consentito dal marketplace
- Prezzo al di sopra del massimo consentito
- Formato prezzo non numerico

**Soluzione**:
1. Verifica che i prezzi siano positivi nel catalogo
2. Controlla le regole di ricarico prezzi (potrebbero creare prezzi negativi)
3. Regola i prezzi per rientrare nei limiti del marketplace
4. Rielabora la pubblicazione

**Limiti prezzi comuni**:
- Amazon: minimo €0,01, massimo €999.999,99
- eBay: minimo €0,01, massimo €999.999,99
- Carrefour: minimo €0,50, massimo €999.999,99

---

### E004 - Quantità non valida

**Descrizione**: La quantità non è presente o non è un numero valido.

**Cause possibili**:
- Campo Quantity vuoto nel catalogo
- Quantità inferiore al minimo richiesto
- Quantità impostata con modalità "Quantità fissa" a 0

**Soluzione**:
1. Verifica che il campo Quantity sia presente e valorizzato
2. Controlla il filtro Quantità Minima (consigliato minimo 8/10)
3. Verifica la modalità quantità scelta (Importa da listino, Fissa, Random)
4. Rielabora la pubblicazione

---

### E005 - Categoria non mappata

**Descrizione**: La categoria del prodotto non è stata mappata alla categoria marketplace.

**Cause possibili**:
- Mappatura categorie non completata
- Categoria prodotto non riconosciuta
- Marketplace non supporta questa categoria

**Soluzione**:
1. Vai a **Prevendita > Prodotti > Impostazioni > Categorie**
2. Mappa le categorie SellRapido alle categorie marketplace
3. Assicurati che tutti i prodotti abbiano almeno una categoria
4. Rielabora la pubblicazione

---

### E006 - Descrizione mancante

**Descrizione**: La descrizione del prodotto è vuota o troppo breve.

**Cause possibili**:
- Campo Description non presente nel catalogo
- Template descrizione non configurato
- Descrizione al di sotto del minimo richiesto

**Soluzione**:
1. Verifica che il campo #Description# sia nel catalogo
2. Controlla il template descrizione in Impostazioni > Titolo e Template
3. Aggiungi contenuto descrittivo se mancante
4. Rielabora la pubblicazione

---

### E007 - Immagine non disponibile

**Descrizione**: L'immagine del prodotto non è raggiungibile o è in formato non supportato.

**Cause possibili**:
- URL immagine non raggiungibile
- Formato immagine non supportato (deve essere JPG, PNG, WEBP)
- Immagine rimossa dal server host
- URL con reindirizzamenti

**Soluzione**:
1. Verifica che gli URL delle immagini siano pubblici e raggiungibili
2. Verifica il formato immagine (JPG, PNG o WEBP)
3. Verifica che le immagini abbiano dimensioni minime 300x300px
4. Ricaricare le immagini su server accessibile
5. Rielabora la pubblicazione

---

### E008 - EAN non valido

**Descrizione**: Il codice EAN non è valido per il marketplace.

**Cause possibili**:
- EAN non è univoco (duplicato)
- EAN non è un codice valido (lunghezza errata)
- EAN è stato digitato male

**Soluzione**:
1. Verifica che l'EAN abbia 13 cifre (o 8 per alcuni marketplace)
2. Verifica che non siano presenti spazi o caratteri speciali
3. Utilizza uno strumento online per validare l'EAN
4. Se l'EAN non è corretto, rimuovi il campo EAN dal catalogo
5. Rielabora la pubblicazione

---

## Errori eBay

### eBay001 - Condizione mancante

**Descrizione**: eBay richiede la condizione dell'articolo (Nuovo, Usato, Ricondizionato, etc.).

**Cause possibili**:
- Condizione non configurata nelle impostazioni eBay
- Campo condizione non presente nel catalogo

**Soluzione**:
1. Vai a **Impostazioni > Marketplace > eBay > Impostazioni di pubblicazione**
2. Nella sezione Generali, imposta **Condizione articolo** (Nuovo consigliato)
3. Rielabora la pubblicazione

---

### eBay002 - Metodo spedizione non valido

**Descrizione**: Il metodo di spedizione configurato non è disponibile per la categoria.

**Cause possibili**:
- Metodo spedizione non abilitato per questa categoria eBay
- Categoria eBay non supporta spedizione gratuita
- Classe logistica non disponibile

**Soluzione**:
1. Verifica i metodi di spedizione disponibili per la categoria su eBay
2. Cambia il metodo spedizione in Impostazioni > Spedizione
3. Prova con "Spedizione gratuita" se supportato
4. Rielabora la pubblicazione

---

### eBay003 - Durata asta non valida

**Descrizione**: La durata dell'asta specificata non è valida per eBay.

**Cause possibili**:
- Durata non è un valore consentito (1, 3, 5, 7, 10, 30 giorni per item a prezzo fisso)

**Soluzione**:
1. Vai a **Impostazioni > Marketplace > eBay > Impostazioni di pubblicazione**
2. Imposta la durata a uno dei valori validi
3. Rielabora la pubblicazione

---

### eBay004 - Quantità riservata

**Descrizione**: La quantità disponibile per questa categoria eBay è limitata.

**Cause possibili**:
- Categoria eBay limita il numero di articoli pubblicabili
- Account eBay ha limiti di vendita

**Soluzione**:
1. Contatta il supporto eBay per aumentare i limiti di vendita
2. Usa categorie diverse se disponibili
3. Aumenta il livello del venditore su eBay
4. Rielabora quando il limite è aumentato

---

### eBay005 - Tag vietati nel titolo

**Descrizione**: Il titolo contiene tag HTML o caratteri non consentiti da eBay.

**Cause possibili**:
- Template titolo contiene tag HTML
- Titolo contiene caratteri speciali non permititi
- Titolo contiene simboli di valuta vietati

**Soluzione**:
1. Modifica il template titolo per rimuovere tag HTML
2. Rimuovi caratteri speciali dal titolo
3. Evita simboli €, £, $ nel titolo (descrivere il prezzo nel testo)
4. Rielabora la pubblicazione

---

### eBay006 - Brand non trovato

**Descrizione**: Il brand inserito non è nel database eBay.

**Cause possibili**:
- Brand non esiste nel database eBay
- Marca digitata male
- eBay non supporta il brand per questa categoria

**Soluzione**:
1. Verifica l'ortografia della marca
2. Usa il nome ufficiale del brand
3. Se il brand non è nel database eBay, seleziona "Marca non disponibile"
4. Rielabora la pubblicazione

---

## Errori Amazon

### AMZ001 - ASIN non valido

**Descrizione**: L'ASIN inserito non è valido o non esiste su Amazon.

**Cause possibili**:
- ASIN digitato male
- ASIN riferisce a un prodotto diverso
- Prodotto non disponibile su Amazon per la nazione

**Soluzione**:
1. Verifica l'ASIN sul catalogo Amazon
2. Assicurati che sia il prodotto corretto
3. Se non esiste ASIN, Amazon lo creerà automaticamente
4. Rielabora la pubblicazione

---

### AMZ002 - Offerta bloccata

**Descrizione**: Un altro venditore ha già la Buy Box per questo ASIN con prezzo inferiore.

**Cause possibili**:
- Prezzo è superiore alla Buy Box attuale
- Altro venditore ha una reputazione migliore
- Configurazione non idonea per Buy Box

**Soluzione**:
1. Ridurre il prezzo per competere con la Buy Box
2. Migliorare la reputazione venditore
3. Assicurarsi che la quantità sia sufficiente
4. Rielabora la pubblicazione dopo le modifiche

---

### AMZ003 - Prodotto inattivo

**Descrizione**: Il prodotto su Amazon è inattivo o sospeso.

**Cause possibili**:
- Richiesta di sospensione da parte di Amazon
- Catalogo ristretto per categoria gated
- Violazione di policy Amazon

**Soluzione**:
1. Verifica eventuali messaggi da Amazon nel seller central
2. Se categoria gated, richiedi accesso
3. Se violazione, correggi il problema
4. Contatta il supporto Amazon se necessario
5. Rielabora quando il problema è risolto

---

### AMZ004 - Categoria ristretta

**Descrizione**: Non hai accesso alla categoria gated di questo prodotto.

**Cause possibili**:
- Categoria gated (approvazione richiesta)
- Numero di prodotti già pubblicati insufficiente
- Reputazione venditore non adeguata

**Soluzione**:
1. Richiedi accesso alla categoria presso Amazon Seller Central
2. Fornisci documenti di fabbricante/distributore se richiesti
3. Attendi l'approvazione di Amazon (fino a 2 settimane)
4. Rielabora la pubblicazione dopo l'approvazione

---

### AMZ005 - Prezzo MAP violato

**Descrizione**: Il prezzo è al di sotto del Prezzo di Vendita Consigliato dal fabbricante (MAP).

**Cause possibili**:
- Prezzo inferiore al MAP del produttore
- Ricaricare riporta il prezzo sotto il MAP

**Soluzione**:
1. Contatta il produttore per verificare il MAP
2. Aumenta il prezzo per rispettare il MAP
3. Rimuovi i ricarichi negativi se necessario
4. Rielabora la pubblicazione

---

## Errori marketplace europei

### CAR001 - Carrefour - Categoria non supportata

**Descrizione**: Carrefour non supporta questa categoria di prodotto.

**Cause possibili**:
- Categoria non esiste in Carrefour
- Categoria è limitata per nuovi venditori
- Prodotto non è idoneo per il marketplace

**Soluzione**:
1. Verifica le categorie disponibili in Carrefour
2. Usa una categoria simile se disponibile
3. Contatta il supporto Carrefour per categorie gated
4. Rielabora la pubblicazione

---

### CDD001 - Cdiscount - Codice promotore non valido

**Descrizione**: Il codice Octopia Seller ID non è stato configurato correttamente.

**Cause possibili**:
- Seller ID non inserito nelle credenziali
- Seller ID non è corretto per il negozio
- Credenziale non è stata salvata correttamente

**Soluzione**:
1. Vai a **Impostazioni > Credenziali Marketplace > Cdiscount**
2. Verifica che l'Octopia Seller ID sia corretto
3. Riesegui il test di connessione
4. Salva le modifiche
5. Rielabora la pubblicazione

---

### KAU001 - Kaufland - Nazione non corretta

**Descrizione**: La nazione del marketplace Kaufland non corrisponde alle impostazioni.

**Cause possibili**:
- Nazione non selezionata nelle impostazioni canale
- Prodotto non è disponibile per questa nazione
- Configurazione canale errata

**Soluzione**:
1. Vai a **Prevendita > Prodotti > Impostazioni**
2. Seleziona il canale Kaufland
3. Verifica che la nazione sia corretta
4. Assicurati che il catalogo sia adatto per quella nazione
5. Rielabora la pubblicazione

---

### MAN001 - ManoMano - Seller Contract ID non valido

**Descrizione**: Il Seller Contract ID per questa nazione non è corretto.

**Cause possibili**:
- Contract ID digitato male
- Contract ID è per una nazione diversa
- Credenziale non è stata salvata

**Soluzione**:
1. Vai a **Impostazioni > Credenziali Marketplace > ManoMano**
2. Verifica il Seller Contract ID per ogni nazione
3. Riesegui il test di connessione
4. Salva le modifiche
5. Rielabora la pubblicazione

---

## Errori ecommerce

### SHOP001 - Shopify - Limite prodotti raggiunto

**Descrizione**: Hai raggiunto il limite di 1 prodotto al secondo su Shopify.

**Cause possibili**:
- Troppi prodotti in pubblicazione contemporaneamente
- API Shopify è throttled per limiti di rate

**Soluzione**:
1. Attendi qualche minuto prima di riprovare
2. Pubblica i prodotti in lotti più piccoli (max 100 al giorno)
3. Distribuisci le pubblicazioni nell'arco della giornata
4. Rielabora con delay tra i lotti

---

### SHOP002 - Shopify - Varianti limitate

**Descrizione**: Hai superato il limite di 100 varianti al giorno su Shopify.

**Cause possibili**:
- Troppe varianti pubblicate nello stesso giorno
- Limite giornaliero di Shopify raggiunto

**Soluzione**:
1. Attendi fino al giorno successivo
2. Nel futuro, distribuisci le varianti su più giorni
3. Considera di pubblicare ogni variante come prodotto separato
4. Rielabora il giorno successivo

---

### WOO001 - WooCommerce - Categoria mancante

**Descrizione**: La categoria del prodotto non esiste in WooCommerce.

**Cause possibili**:
- Categoria non è stata creata in WooCommerce
- Nome categoria non corrisponde

**Soluzione**:
1. Accedi al back office di WooCommerce
2. Crea le categorie mancanti in Prodotti > Categorie
3. Assicurati che i nomi corrispondano alle categorie SellRapido
4. Rielabora la pubblicazione

---

### WOO002 - WooCommerce - URL immagine non valida

**Descrizione**: L'URL dell'immagine non è raggiungibile da WooCommerce.

**Cause possibili**:
- URL immagine con reindirizzamento
- Immagine bloccata da firewall/Cloudflare
- SSL/TLS mismatch

**Soluzione**:
1. Verifica che gli URL siano HTTPS
2. Disattiva temporaneamente Cloudflare per il test
3. Carica le immagini sul server WooCommerce stesso
4. Rielabora la pubblicazione

---

### MAG001 - Magento - Titoli non univoci

**Descrizione**: Due prodotti hanno lo stesso titolo in Magento (richiesta univocità).

**Cause possibili**:
- Template titolo genera titoli duplicati
- SKU non è incluso nel titolo per differenziazione
- Titoli sono identici in catalogo

**Soluzione**:
1. Modifica il template titolo per includere SKU: `#SKU# #Title#`
2. Assicurati che ogni prodotto abbia SKU univoco
3. Aggiungi attributi differenzianti al titolo
4. Rielabora la pubblicazione

---

## Diagnostica e troubleshooting

### Come diagnosticare un errore

#### Step 1: Identificare il marketplace

1. Accedi a **Prevendita > Report Inserzioni**
2. Filtra per il marketplace in errore

#### Step 2: Visualizzare dettagli errore

1. Clicca su **Visualizza Errori** per quel marketplace
2. Cerca il prodotto che genera errore
3. Leggi il messaggio di errore dettagliato

#### Step 3: Identificare la causa

1. Confronta il messaggio con le sezioni di errore sopra
2. Identifica la categoria di errore (generale, marketplace-specifico, etc.)
3. Leggi le "Cause possibili"

#### Step 4: Applicare la soluzione

1. Segui i passaggi di "Soluzione" per il tipo di errore
2. Correggi i dati o le configurazioni
3. Rielabora la pubblicazione

### Rielaborazione della pubblicazione

Dopo aver corretto un errore:

1. Vai a **Prevendita > Report Inserzioni**
2. Seleziona il marketplace in errore
3. Clicca **Rielabora**
4. Scegli:
   - **Solo errori**: Rielabora solo i prodotti con errore
   - **Tutti i prodotti**: Rielabora tutto il listino
5. Clicca **Inizia Rielaborazione**
6. Attendi il completamento

**Nota**: La rielaborazione può richiedere da pochi minuti a diverse ore a seconda del numero di prodotti.

### Tools diagnostica

#### Verifica SKU

Per verificare che gli SKU siano univoci:

1. Vai a **Prevendita > Prodotti**
2. Seleziona il catalogo
3. Usa il menu a tendina **Visualizza > Duplicati SKU**
4. Verranno evidenziati gli SKU duplicati

#### Verifica immagini

Per testare gli URL delle immagini:

1. Copia l'URL immagine
2. Aprila in un browser
3. Verifica che sia accessibile e in formato idoneo

#### Verifica prezzi

Per controllare i calcoli di prezzo:

1. Vai a **Impostazioni > Canale > Prezzi**
2. Vedi l'anteprima del calcolo prezzo per un prodotto esempio
3. Verifica che il risultato sia positivo e ragionevole

### Contattare il supporto

Se l'errore persiste dopo i tentativi di risoluzione:

1. Accedi a **Impostazioni > Supporto > Area Ticket**
2. Apri un ticket con:
   - **Categoria**: Marketplace/Ecommerce
   - **Sottocategoria**: Pubblicazioni sul canale di vendita in errore (Report Inserzioni)
3. Fornisci:
   - Marketplace interessato
   - SKU del prodotto
   - Codice di errore
   - Azioni già intraprese

### Errori ricorrenti comuni

| Errore | Causa più probabile | Soluzione veloce |
|---|---|---|
| E003 Prezzo non valido | Ricarico negativo che porta prezzo sotto zero | Aumentare il ricarico fisso |
| E004 Quantità non valida | Filtro quantità minima esclude prodotto | Diminuire quantità minima filter |
| AMZ002 Offerta bloccata | Prezzo troppo alto rispetto concorrenza | Ridurre prezzo del 5-10% |
| eBay004 Quantità riservata | Limite vendite eBay raggiunto | Contattare eBay per aumento limite |
| E007 Immagine non disponibile | URL immagine con reindirizzamento | Usare URL diretta senza redirect |

---

## Best Practices Gestione Errori

1. **Monitoraggio regolare**: Controllare il Report Inserzioni almeno settimanalmente
2. **Azione rapida**: Correggere gli errori entro 24 ore per non perdere visibilità
3. **Test iniziali**: Pubblicare piccoli lotti per identificare problemi prima di mass publication
4. **Documentazione**: Annotare le cause di errore frequenti per prevenirle
5. **Backup dati**: Mantenere backup del catalogo originale per facilitare correzioni
6. **Validazione catalogo**: Esportare e validare il catalogo prima dell'importazione
7. **Comunicazione marketplace**: Monitorare messaggi da marketplace per policy changes
8. **Configurazione corretta**: Assicurarsi che tutte le impostazioni siano configurate prima di pubblicare
9. **Rielaborazione pianificata**: Rielaborare gli errori durante orari a basso traffico
10. **Escalation**: Contattare il supporto se gli errori riguardano problemi di account/limiti di marketplace
