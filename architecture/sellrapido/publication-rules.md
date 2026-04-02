# Publication Rules - SellRapido

## Indice

1. [Panoramica](#panoramica)
2. [Processo di pubblicazione](#processo-di-pubblicazione)
3. [Regole generali vs regole specifiche](#regole-generali-vs-regole-specifiche)
4. [Prezzi e ricarichi](#prezzi-e-ricarichi)
5. [Spedizione](#spedizione)
6. [Quantità](#quantità)
7. [Filtri di inclusione ed esclusione](#filtri-di-inclusione-ed-esclusione)
8. [Categorie](#categorie)
9. [Resi](#resi)
10. [Titolo e Template](#titolo-e-template)

---

## Panoramica

Le regole di pubblicazione definiscono come i prodotti vengono presentati sui marketplace/ecommerce. Includono prezzi, spedizioni, quantità, filtri di inclusione/esclusione e personalizzazione.

### Due Step Fondamentali

1. **Settaggio delle impostazioni di pubblicazione**: definiscono prezzi e ricarichi, spese di spedizione, metodi di pagamento, quantità e filtri
2. **Settaggio dei filtri di inclusione ed esclusione**: definiscono quali prodotti pubblicare e quali escludere

---

## Processo di pubblicazione

### Come settare le impostazioni di pubblicazione

**Passi**:
1. Vai su **Prevendita > Prodotti**
2. Seleziona il catalogo da pubblicare
3. Clicca su **Impostazioni**
4. Clicca su **Nuovo** per aggiungere un nuovo canale
5. Compila i seguenti campi:
   - **Marketplace**: selezionare il canale di vendita
   - **Credenziale**: verrà popolato automaticamente
   - **Canale**: selezionare la nazione di pubblicazione
6. Clicca su **Ok**
7. Clicca sulla rotella per aprire le impostazioni

Lo status sarà inizialmente in rosso e diventerà verde una volta definite tutte le impostazioni.

### Sezioni configurabili

- Prezzi
- Spedizione
- Pagamento
- Quantità
- Titolo e Template
- Filtri
- Categorie
- Resi

---

## Regole generali vs regole specifiche

### Regole Generali

- Impostate nella parte **superiore** di ciascuna scheda
- Valgono per **TUTTI** i prodotti pubblicati su un determinato canale
- Sono il default per tutti i prodotti

### Regole Specifiche

- Impostate nella parte **inferiore** di ciascuna scheda
- Applicate **esclusivamente** ai prodotti che rientrano nei parametri indicati
- Parametri disponibili:
  - Categoria 1, 2 e/o 3
  - Marca
  - Singoli o Gruppi di SKU
  - Singoli o Gruppi di EAN
  - Fasce di prezzo
  - Fasce di peso
  - Fasce di giorni di preparazione dell'ordine

#### Come inserire una regola specifica

1. Clicca il tasto **Nuovo**
2. Definisci i parametri per identificare i prodotti
3. Clicca su **Impostazioni** nella medesima riga
4. Configura la regola specifica
5. Salva

### Priorità delle regole

**Importante**: Se uno o più prodotti rientrano nei parametri di più righe, verrà applicata sempre la regola della riga più in alto.

Per modificare l'ordine: trascinare le righe verso l'alto o verso il basso.

---

## Prezzi e ricarichi

### Aggiungi IVA al prezzo

- Spuntare e indicare la percentuale di IVA se applicabile
- Generalmente i prezzi a monte sono al netto dell'IVA
- Se il catalogo ha colonna IVA valorizzata, NON spuntare questa casella (il sistema aggiungerà l'IVA presente nel catalogo)

### Esenzione IVA

- Attivare quando le vendite rientrano nel meccanismo del DPR n. 633/1972, art. 17 commi 5, 6 e 7
- Consultare il commercialista per maggiori informazioni

### Aggiungi Commissioni Marketplace

- Aggiunge al prezzo finale le commissioni previste dal marketplace/e-commerce
- Le commissioni vengono calcolate in base alla categoria merceologica

### Aggiungi IVA alle commissioni Marketplace

- Spuntando, aggiunge IVA alle commissioni marketplace
- **ATTENZIONE**: Se iscritto al VIES NON spuntare questa casella

### Arrotondamento

Scegliere una delle opzioni:
- **Alle cifre decimali** (es. 11,70 €)
- **All'unità** (es. 12 €)
- **All'unità meno un centesimo** (es. 11,99 €)

### Ricarico Prezzi % e Ricarico Prezzi Fisso

- Inserire ricarico percentuale e/o fisso da aggiungere al prezzo di listino
- Per applicare uno **sconto**: inserire segno meno (es. "-5,00")
- Tutti i calcoli usano la valuta del catalogo
- Al momento dell'invio SellRapido effettua conversione nella valuta del marketplace
- Ricarichi fissi devono essere sempre nella valuta del listino

**Esempio**:
- Prezzo base: €100
- Ricarico %: +10%
- Ricarico Fisso: +5€
- Risultato: (100 × 1,10) + 5 = €115

### Aggiungi Commissioni Pagamento

- Calcola la commissione di Paytipper (fisso 1,5%)

---

## Spedizione

### Tariffe

- Se il dato sulle spese di spedizione è presente nel catalogo: spuntare "Prese da listino"
- Altrimenti: inserire manualmente l'importo delle spese di spedizione

### Tempi di Preparazione dell'Ordine

- Se il dato è presente nel catalogo: spuntare "Prese da listino"
- Altrimenti: inserire il numero di giorni (minimo 1 giorno)

### Aggiungi IVA

- Se le tariffe di spedizione sono al netto dell'IVA, spuntare e indicare la percentuale di IVA

### Prezzi Includono Spese di Spedizione

- Spuntare se vuoi spostare il costo della spedizione sul prezzo finale
- In questo modo il cliente non vedrà il costo effettivo delle spese di spedizione

### Metodo - Classe di Logistica

Determina un costo aggiuntivo visualizzato all'acquirente. Opzioni:

- **Spedizione gratuita** - Se i costi sono predeterminati nel prezzo
- **Classi di logistica** - Se variabile in funzione del peso:
  - Classe Standard
  - Classe Express
  - Classe Premium
  - Custom

---

## Quantità

### Modalità disponibili (selezionabili da menu tendina)

#### Modalità 1: Importare le quantità dal listino

- La quantità mostrata è quella presente nel catalogo
- Aggiornata ogni ora

#### Modalità 2: Quantità fissa

- Inserire nel campo la quantità di pezzi da visualizzare
- Tutti i prodotti mostrano la stessa quantità

#### Modalità 3: Quantità random

- Pubblicare una quantità casuale compresa nel range dei due numeri inseriti
- Inserire numero minimo e numero massimo

### Opzione aggiuntiva

- È possibile impostare una **quantità massima** di pezzi da visualizzare nell'inserzione

**Nota**: SellRapido aggiorna le quantità dei prodotti una volta l'ora.

---

## Filtri di inclusione ed esclusione

### Logica dei filtri

- **Filtri di Inclusione**: I prodotti che fanno match verranno **PUBBLICATI**
- **Filtri di Esclusione**: I prodotti che fanno match **NON verranno pubblicati**
- **Priorità**: I filtri di esclusione prevalgono sempre su quelli di inclusione
- **Ordine**: Se uno o più prodotti rientrano nei parametri di più filtri, verrà applicata la regola della riga più in alto

### Parametri disponibili

- Categoria 1/2/3
- Marche
- Codice SKU
- Codice EAN
- Fascia di Prezzo
- Quantità Min/Max
- Fascia di Peso
- Fasce di giorni di preparazione dell'ordine

### Come impostare filtri

1. Vai su **Prevendita > Prodotti**
2. Seleziona il catalogo
3. Clicca su **Impostazioni**
4. Seleziona il canale
5. Clicca su **Impostazioni**
6. Recati nella scheda **Filtri**
7. Clicca su **Nuovo** per creare una nuova regola
8. Definisci i parametri
9. Clicca su **Conferma** in basso a destra

### Filtrare per Marca

**Sintassi**: Inserire i nomi delle marche separati da virgola, senza spazi prima e dopo.

Esempio: `canon,infinity light,zyxel`

È possibile filtrare più marche nella stessa riga.

### Filtrare per Titolo

**Nota**: È fortemente sconsigliato inserire il filtro per titolo senza assistenza SellRapido. Per utilizzarlo, aprire un ticket con:
- **Categoria**: Marketplace/Ecommerce
- **Sottocategoria**: Impostazioni di pubblicazione
- Includere: nome catalogo, canale di vendita, filtro desiderato

**Funzionamento**:

- **L'asterisco** al posto del suffisso di genere/numero include tutte le varianti della parola
  - Esempio: `Bambin*` includerà: bambino, bambina, bambini, bambine
- Il testo può essere scritto **indifferentemente in maiuscolo/minuscolo**
  - Esempio: `Bambin*` = `bambin*` = `BAMBIN*`
- Una parola intera seguita da asterisco filtra tutti i prodotti che iniziano con quella parola
  - Esempio: `Apple*` filtra: AppleCare, AppleCare+
- Per filtrare una parola in posizione **interna**, metterla tra asterischi
  - Esempio: `*bambin*` filtra: "Felpa Marvel bambino Iron Man", "Costume bambina principesse", "Scarpe bambino calcio"

### Quantità Minima

Consigliato inserire sempre una quantità minima di 8/10 pezzi.

Se lo stock del prodotto è inferiore alla quantità minima, il sistema chiuderà l'inserzione.

### Importante

**È caldamente sconsigliato inserire un filtro di inclusione totale**. Mandare in pubblicazione i prodotti in maniera graduale, specialmente se l'account è appena stato aperto.

### Procedura per chiudere tutte le pubblicazioni

Per rimuovere le pubblicazioni:

1. Vai su **Prevendita > Prodotti**
2. Seleziona il catalogo da eliminare
3. Clicca su **Impostazioni**
4. Accedi alle Impostazioni di pubblicazione di ogni canale
5. Vai sulla scheda **Generali** e rimuovi il flag da "Mantieni Rilevanza" (se inserito)
6. Recati in **Filtri > Esclusioni**
7. Clicca su **"Escludi Tutto"** per inserire un filtro di esclusione totale
   - Se sono presenti altri filtri di esclusione, inserire "Escludi Tutto" come prima riga
8. Ripeti per tutti i canali di vendita
9. Vai su **Prevendita > Report Inserzioni**
10. Seleziona il listino e i marketplace
11. Clicca su **"Report su selezione corrente"**
12. Quando N° Inserzioni sarà "0" per ogni canale, le pubblicazioni saranno chiuse

---

## Categorie

### Associazione categorie

Associare le Categoria 1/2/3 del listino alla Categoria Canale per:
- Permettere a SellRapido di calcolare la commissione corretta
- La commissione calcolata qui verrà usata se attivato "Aggiungi commissione Marketplace"

### Procedura

1. Selezionare una o più righe di categorie spuntando il box
2. Cliccare il tasto su una delle righe selezionate
3. Scegliere la categoria canale più affine
4. Cliccare la freccetta per impostarla
5. Cliccare **Conferma** in basso a destra

---

## Resi

### Campi da compilare

#### Giorni per la restituzione

- Inserire il numero di giorni necessario per la restituzione degli oggetti
- Selezionare dal menu a tendina

#### Istruzioni per la restituzione

- Digitare nel box le istruzioni per la restituzione
- Queste sovrascriveranno quelle eventualmente impostate nel back office del marketplace/e-commerce
- Esempio: "Restituzione a carico dell'acquirente"

#### L'oggetto sarà spedito da

- Indicare il luogo da cui verranno spediti gli oggetti (CAP e città)
- Selezionare il paese dal menu a tendina
- **ATTENZIONE**: il testo deve avere meno di 45 caratteri

---

## Titolo e Template

### Titolo

La scheda Titolo gestisce il titolo visualizzato nelle inserzioni dei prodotti.

#### Aggiunta di un campo

1. Cliccare sul menu a tendina nella sezione "Campi" in alto a destra
2. Ogni campo è delimitato dagli hashtag #
3. Al momento della pubblicazione, SellRapido popola automaticamente il titolo sostituendo ai nomi dei campi il relativo valore
4. Tra un campo e l'altro è sempre necessario inserire uno spazio
5. Esempio: `#SKU# #Title#`

#### Prima lettera maiuscola

- Spuntare la casella "Prima Lettera Maiuscola" per capitalizzare la prima lettera di ciascuna parola

### Template

La scheda Template modifica la descrizione delle inserzioni.

#### Aggiunta di un campo

1. Cliccare sul menu a tendina nella sezione "Campi" in alto a destra
2. **ATTENZIONE**: il template deve avere un massimo di 40.000 caratteri
3. Campi disponibili:

| Nome Campo | Spiegazione |
|---|---|
| #Category1# - #Category3# | Categorie listino |
| #Title# | Titolo |
| #Description# | Descrizione |
| #SKU# | Codice SKU |
| #EAN# | Codice EAN |
| #MPN# | Codice produttore (MPN) |
| #Brand# | Marca |
| #Image1# – #Image9# | URL immagine (da 1 a 9) |
| #extra1# – #extra9# | Campi extra (da 1 a 9) |

#### Template standard

- In SellRapido è presente un template standard
- Per utilizzarlo, selezionarlo dal menu a tendina
- Cliccare su "Carica Template"

#### Codice HTML

- Cliccare sul tasto "Codice Sorgente"
- Inserire il codice HTML desiderato

#### Importazione di un file

- Utilizzare il tasto "Carica da file"
- Scegliere il file (HTML o testo) dalla cartella in cui è salvato
- Cliccare "Apri"

#### Visualizzare l'anteprima del template

- Cliccare su "Anteprima Template"
- Visualizzare come apparirà il template

---

## Best Practices

1. **Evitare filtri di inclusione totali** - Mandare in pubblicazione gradualmente
2. **Monitorare il Report Inserzioni** - Controllare lo status dopo la pubblicazione
3. **Quantità minima** - Inserire sempre 8/10 pezzi per evitare overselling
4. **Categorie corrette** - Verificare che la categoria canale sia corretta per commissioni accurate
5. **Ordine priorità** - Ricordare che la regola nella riga più alta ha priorità
6. **Filtro per marca** - Non inserire spazi prima/dopo virgole
7. **Assistenza per filtri per titolo** - Contattare SellRapido per questa configurazione
8. **Test iniziali** - Testare le impostazioni con pochi prodotti prima di pubblicare in massa

---

## Verifica del Report Inserzioni

Dopo aver pubblicato, controllare il Report Inserzioni per:
- Verificare lo status delle pubblicazioni
- Analizzare eventuali errori
- Applicare le modifiche necessarie affinché la pubblicazione vada a buon fine

**Accesso**:
1. Vai su **Prevendita > Report Inserzioni**
2. Seleziona il listino e i marketplace
3. Clicca su **"Report su selezione corrente"**


---

## Regole di Prezzo Condizionali su SellRapido

### Concetto
Le regole condizionali sovrascrivono la regola prezzo principale per sottoinsiemi di prodotti che soddisfano determinati criteri. Ogni regola può filtrare per:
- **Prezzo** (min/max)
- **Categorie** (1, 2, 3)
- **Marche**
- **SKU / EAN**
- **Quantità** (min/max)
- **Peso** (min/max)
- **Giorni consegna** (min/max)

### Come funzionano
1. La **regola base** (configurata nella sezione Prezzi principale) si applica a TUTTI i prodotti
2. Le **regole condizionali** sovrascrivono la regola base SOLO per i prodotti che corrispondono ai filtri
3. Cliccando **"+ Nuovo"** nella tabella regole si crea una nuova regola condizionale
4. Il campo **"Prod. corrisp."** (calcolatrice) mostra quanti prodotti rientrano nella regola
5. Il pulsante **"Impostazioni"** (ingranaggio) apre il pannello prezzi specifico della regola
6. Le impostazioni della regola COPIANO automaticamente quelle della regola base, poi si modificano

### Impostazioni disponibili per ogni regola
- **Aggiungi IVA al Prezzo**: checkbox + % IVA
- **Esenzione IVA**: checkbox
- **Aggiungi Commissioni Marketplace**: checkbox + % extra commissioni
- **Aggiungi IVA alle Commissioni Marketplace**: checkbox
- **Arrotonda a**: unità / decimale
- **Arrotonda all'unità meno 1 centesimo**: checkbox (SEMPRE selezionata)
- **Ricarico Prezzi %**: percentuale di ricarico
- **Ricarico Prezzi Fisso**: importo fisso aggiuntivo
- **Commissioni pag %**: commissioni pagamento percentuali
- **Commissioni pag. fisse**: commissioni pagamento fisse
- **Aggiungi Commissioni Pagamento**: checkbox

### Regola operativa permanente
> **REGOLA**: "Arrotonda all'unità meno 1 centesimo" deve essere SEMPRE selezionata su ogni regola.

### Esempio pratico: Ferlegno → ManoMano Italia
**Regola base**: Ricarico 15%, IVA 22% (Esclusa), Commissioni Marketplace 0% (auto da Categorie)

**Regola condizionale 1**: Prodotti da €0 a €100
- Prezzo Min: 0 | Prezzo Max: 100
- Ricarico: **30%**
- Prodotti corrispondenti: ~2935

**Regola condizionale 2**: Prodotti da €100 in su
- Prezzo Min: 100 | Prezzo Max: 0 (nessun limite)
- Ricarico: **18%**
- Prodotti corrispondenti: ~4100

### Note tecniche
- Le regole condizionali vengono salvate con "Quick Save"
- Le modifiche NON si applicano fino alla successiva importazione del catalogo
- Se non ci sono regole di inclusione definite (Filtri), nessun prodotto sarà pubblicato
- I campi SKU, EAN e MARCA possono contenere più valori separati da "," (es: 123,124,125) o un solo filtro per parte di valore

---

## Regole di Spedizione Condizionali su SellRapido

### Concetto
Le regole di spedizione condizionali funzionano come le regole di prezzo condizionali: una regola base (Spedizione Principale) si applica a tutti i prodotti, e regole condizionali sovrascrivono la tariffa per sottoinsiemi filtrati per peso, prezzo, categoria, SKU o EAN.

### Come Funzionano
1. **Regola Base (Spedizione Principale)**: si applica a TUTTI i prodotti del listino su quel marketplace
2. **Regole Condizionali**: filtrano per peso (weight_start/weight_end) e sovrascrivono la tariffa base

### Campi Disponibili per Regola Spedizione
| Campo | Descrizione |
|-------|-------------|
| shipping_fee | Tariffa spedizione (NETTO, senza IVA) |
| include_vat | Checkbox "Aggiungi IVA" |
| vat_perc | Percentuale IVA (es. 22) |
| delivery_days | Tempi di preparazione ordine |
| shipping_code | Metodo di spedizione (es. Bartolini, SDA, DHL) |
| shipping_fee_additional | Tariffa aggiuntiva |
| shipping_fee_in_product_price | Se il prezzo prodotto include la spedizione |
| cod_cost | Costo contrassegno |

### Filtri Condizionali Disponibili
- **weight_start / weight_end**: filtra per peso prodotto (kg)
- **price_start / price_end**: filtra per prezzo prodotto
- **catalog_category1/2/3**: filtra per categoria
- **brands**: filtra per marca
- **skus / eans**: filtra per SKU o EAN specifici

### Regole Operative Permanenti
1. **SEMPRE** inserire prezzi NETTI (senza IVA) nel campo shipping_fee
2. **SEMPRE** spuntare "Aggiungi IVA" e impostare vat_perc = 22
3. SellRapido calcolerà automaticamente il lordo
4. **MAI** pre-calcolare l'IVA nei prezzi di spedizione
5. **SEMPRE** impostare il Metodo di spedizione (shipping_code) — campo obbligatorio
6. La regola base copre la fascia di peso minima (≤3kg per BRT)
7. Le regole condizionali coprono le fasce superiori con weight_start/weight_end

### Esempio: Ferlegno → ManoMano ITA — Tariffe BRT Express Italia (Sconto 22%)

**Regola Base (Spedizione Principale)**:
- shipping_fee: €4.27 (netto, ≤3kg)
- include_vat: ✅ (true)
- vat_perc: 22
- delivery_days: 3 giorni
- shipping_code: Bartolini

**Regole Condizionali per Fascia di Peso**:
| # | Peso Min (kg) | Peso Max (kg) | Tariffa Netta (€) | IVA | Metodo |
|---|--------------|--------------|-------------------|-----|--------|
| 1 | 3.01 | 5 | 4.83 | 22% | Bartolini |
| 2 | 5.01 | 10 | 6.97 | 22% | Bartolini |
| 3 | 10.01 | 20 | 7.97 | 22% | Bartolini |
| 4 | 20.01 | 30 | 9.47 | 22% | Bartolini |
| 5 | 30.01 | 50 | 14.47 | 22% | Bartolini |
| 6 | 50.01 | 75 | 17.47 | 22% | Bartolini |
| 7 | 75.01 | 100 | 19.97 | 22% | Bartolini |
| 8 | 100.01 | 150 | 34.97 | 22% | Bartolini |
| 9 | 150.01 | 200 | 49.97 | 22% | Bartolini |

### Note Tecniche
- Le tariffe sono dalla colonna "Sconto 22%" del listino BRT Express Italia
- I prezzi sono AL NETTO di IVA — SellRapido aggiunge l'IVA automaticamente con il flag include_vat
- Il campo shipping_code è OBBLIGATORIO: senza di esso il salvataggio fallisce
- Le regole condizionali ereditano i valori dalla regola base al momento della creazione
- Per modificare la tariffa di una regola condizionale, usare il dialog impostazioni (gear icon) sulla riga
- Grid ID tipico: `app-presales-catalog-shops-settings-grid-XXXX` (cambia ad ogni apertura)
