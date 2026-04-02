# Order Management - SellRapido

## Indice

1. [Overview](#overview)
2. [Stati ordini](#stati-ordini)
3. [Cambio stato ordine](#cambio-stato-ordine)
4. [Inserimento tracking](#inserimento-tracking)
5. [Forza scarico vendite](#forza-scarico-vendite)
6. [Invio mail personalizzate](#invio-mail-personalizzate)
7. [Stampa etichette DYMO](#stampa-etichette-dymo)
8. [Bandierine e note](#bandierine-e-note)
9. [Configurazione API corrieri](#configurazione-api-corrieri)

---

## Overview

La gestione degli ordini è la fase di post-vendita che comprende tutte le operazioni successive alla ricezione dell'ordine dal marketplace/ecommerce, fino alla consegna al cliente.

### Flusso ordini principale

1. **Ingresso ordine**: L'ordine viene ricevuto dal marketplace/ecommerce
2. **Verifica e preparazione**: Controllo stock, preparazione merce
3. **Cambio stato**: Transizione da "Non pagato" a "Pagato" a "Spedito"
4. **Tracking**: Inserimento dati tracciamento spedizione
5. **Comunicazione**: Invio email con tracking al cliente
6. **Chiusura**: Feedback positivo su marketplace

---

## Stati ordini

### Stati standard

Gli ordini in SellRapido seguono questi stati principali:

| Stato | Descrizione | Azione Richiesta |
|---|---|---|
| **Non pagato** | Ordine ricevuto, pagamento non ancora confermato | Attendere conferma pagamento |
| **Pagato** | Pagamento confermato, ordine pronto per la spedizione | Preparare e spedire |
| **In preparazione** | Merce in corso di preparazione | Notificare cliente (opzionale) |
| **Spedito** | Ordine spedito con tracking | Inserire tracking numero |
| **Consegnato** | Ordine consegnato al cliente | Richiedere feedback |
| **Annullato** | Ordine annullato | Eseguire rimborso se necessario |
| **Reso** | Prodotto reso dal cliente | Gestire rimborso e reintegro stock |
| **Completato** | Ordine completato e chiuso | Nessuna azione |

### Stato speciale: Amazon FBA

Per gli ordini Amazon FBA (Fulfillment by Amazon):

- **Configurazione**: I prodotti devono essere marcati come "FBA"
- **Gestione**: Amazon gestisce automaticamente lo stato dell'ordine
- **Tracking**: Non è necessario inserire tracking manualmente
- **Procedura di configurazione**: Aprire ticket a SellRapido con categoria "Ordini" e sottocategoria "Configurazione Amazon FBA"

---

## Cambio stato ordine

### Come cambiare lo stato di un ordine

1. Accedi a **Post Vendita > Ordini**
2. Seleziona l'ordine da modificare
3. Clicca su **Modifica Stato**
4. Seleziona il nuovo stato dal menu a tendina
5. Se richiesto, compila i campi aggiuntivi:
   - Numero tracking
   - Tracking URL
   - Data spedizione
6. Clicca su **Salva**

### Transizioni di stato consentite

Non tutti gli stati sono raggiungibili da qualsiasi stato precedente. Le transizioni logiche sono:

- **Non pagato** → Pagato
- **Pagato** → In preparazione, Spedito, Annullato
- **In preparazione** → Spedito, Annullato
- **Spedito** → Consegnato, Reso
- **Consegnato** → Completato, Reso
- **Annullato** → (terminale)
- **Reso** → Completato
- **Completato** → (terminale)

### Errori durante il cambio stato

Se si verifica l'errore "Cambio status ordine non funziona":

1. Verifica che lo stato di destinazione sia raggiungibile dallo stato attuale
2. Verifica che tutti i campi obbligatori siano compilati
3. Apri un ticket a SellRapido con categoria "Ordini" e sottocategoria "Cambio status ordine non funziona"
4. Fornisci il numero ordine e gli stati coinvolti

---

## Inserimento tracking

### Come inserire il numero di tracking

#### Metodo 1: Durante il cambio stato

1. Vai a **Post Vendita > Ordini**
2. Seleziona l'ordine
3. Clicca su **Modifica Stato** → **Spedito**
4. Inserisci:
   - **Numero tracking**: il codice di tracking fornito dal corriere
   - **Tracking URL**: l'URL completo per tracciare la spedizione (opzionale)
   - **Data spedizione**: la data di partenza
5. Clicca **Salva**

#### Metodo 2: Modifica rapida

1. Vai a **Post Vendita > Ordini**
2. Seleziona l'ordine
3. Clicca su **Aggiungi Tracking**
4. Compila i campi come sopra

### Formato numero tracking

- **Nessun formato obbligatorio**: Puoi inserire il numero di tracking esattamente come fornito dal corriere
- **Caratteri consentiti**: Alfanumerici
- **Lunghezza**: Non c'è limite

### Tracking URL

L'URL di tracking è opzionale ma consigliato. Deve essere un URL completo raggiungibile:

Esempi:
- Amazon: `https://www.amazon.it/gp/your-account/order-details?orderID=`
- Corriere generico: `https://www.corriere.it/track?id=`

### Sincronizzazione tracking

Dopo aver inserito il tracking:

1. SellRapido sincronizza automaticamente il numero con il marketplace/ecommerce
2. Il cliente riceve una notifica automatica con il numero di tracking
3. Il marketplace aggiorna automaticamente lo stato dell'ordine a "Spedito"

**Nota**: La sincronizzazione avviene entro 15-30 minuti dall'inserimento.

---

## Forza scarico vendite

### Cosa significa "Forza scarico vendite"

La funzione "Forza scarico vendite" serve per scaricare manualmente i dati di vendita da un marketplace quando il collegamento automatico non funziona o è temporaneamente bloccato.

### Quando usare "Forza scarico vendite"

- Il marketplace non comunica gli ordini automaticamente
- Si sospetta che alcuni ordini non siano stati importati
- È necessario recuperare ordini perduti
- Il cron automatico di sincronizzazione ha subito ritardi

### Come forzare lo scarico

1. Accedi a **Post Vendita > Ordini**
2. Seleziona il marketplace dal quale scaricare gli ordini
3. Clicca su **Forza Scarico Vendite**
4. Seleziona:
   - **Data inizio**: Data da cui iniziare il download
   - **Data fine**: Data fino a cui scaricare (massimo 30 giorni indietro)
5. Clicca **Scarica**

### Procedura completa di download

Una volta cliccato **Scarica**:

1. SellRapido interroga il marketplace per gli ordini nel periodo specificato
2. Scarica i dati degli ordini
3. Verifica gli ordini già presenti in SellRapido per evitare duplicati
4. Crea nuovi record per gli ordini non ancora importati
5. Notifica il completamento dell'operazione

**Nota**: L'operazione può richiedere alcuni minuti a seconda del numero di ordini.

### Tipi di ordini scaricabili

Non tutti gli ordini sono scaricabili con forza scarico:

- **Ordini pagati**: Scaricabili
- **Ordini da pagare**: Scaricabili
- **Ordini annullati**: Dipende dal marketplace
- **Ordini completati**: Scaricabili

### Estrapolazione ordini per fatturazione

Per esportare gli ordini per la fatturazione:

1. Accedi a **Post Vendita > Ordini**
2. Usa i filtri per selezionare gli ordini desiderati:
   - Per periodo di data
   - Per stato
   - Per marketplace
3. Clicca **Esporta**
4. Seleziona il formato:
   - **CSV**: File Excel/Calc
   - **PDF**: Documento stampabile
   - **JSON**: Formato strutturato
5. Clicca **Scarica**

Il file conterrà:
- Numero ordine
- Data ordine
- Cliente (nome, email, indirizzo)
- Prodotti e quantità
- Prezzo totale
- IVA (se applicabile)
- Tracking (se disponibile)

---

## Invio mail personalizzate

### Quando inviare email personalizzate

Le email personalizzate sono utili per:

- Comunicare direttamente con il cliente
- Fornire informazioni aggiuntive su spedizione/prodotto
- Rispondere a domande sul prodotto
- Richieste di feedback specifiche

### Come inviare email personalizzate

#### Metodo 1: Da ordine singolo

1. Accedi a **Post Vendita > Ordini**
2. Seleziona l'ordine
3. Clicca su **Invia Email**
4. Seleziona il template (se desiderato)
5. Compila il modulo:
   - **Destinatario**: Email cliente (pre-compilata)
   - **Oggetto**: Titolo dell'email
   - **Corpo**: Contenuto dell'email
   - **Allegati**: Opzionale
6. Clicca **Invia**

#### Metodo 2: Email in massa

1. Accedi a **Post Vendita > Ordini**
2. Seleziona più ordini spuntando i checkbox
3. Clicca **Azioni Bulk > Invia Email**
4. Compila il modulo come sopra
5. Clicca **Invia a Tutti**

### Template email disponibili

SellRapido fornisce template predefiniti:

| Template | Uso | Contenuto |
|---|---|---|
| **Tracking** | Notifica tracking | Numero tracking e link tracciamento |
| **Spedito** | Conferma spedizione | Data spedizione e corriere |
| **Reso** | Istruzioni reso | Procedura reso e indirizzo |
| **Feedback** | Richiesta feedback | Invito a lasciare feedback positivo |
| **Problema** | Scuse e soluzione | Riferimento al problema e soluzione |

### Variabili dinamiche in email

Puoi inserire variabili che verranno sostituite automaticamente:

| Variabile | Valore |
|---|---|
| `{{customer_name}}` | Nome cliente |
| `{{order_number}}` | Numero ordine |
| `{{tracking_number}}` | Numero tracking |
| `{{tracking_url}}` | URL tracciamento |
| `{{courier}}` | Nome corriere |
| `{{ship_date}}` | Data spedizione |

Esempio:
```
Caro {{customer_name}}, il tuo ordine {{order_number}} è stato spedito il {{ship_date}}.
Numero tracking: {{tracking_number}}
Traccia il tuo pacco: {{tracking_url}}
```

### Registrazione email inviate

Tutte le email inviate vengono registrate:

1. Accedi a **Post Vendita > Ordini**
2. Seleziona l'ordine
3. Scorri a **Cronologia Email**
4. Visualizza:
   - Data/ora invio
   - Destinatario
   - Oggetto
   - Contenuto

---

## Stampa etichette DYMO

### Prerequisiti DYMO

Per stampare etichette DYMO è necessario avere:

1. Stampante DYMO 4XL connessa al computer
2. Software DYMO Label installato
3. Browser compatibile con DYMO API (Chrome/Firefox consigliati)
4. Etichette DYMO formato 4x6 (100mm x 152mm)

### Come stampare etichette DYMO

#### Metodo 1: Ordine singolo

1. Accedi a **Post Vendita > Ordini**
2. Seleziona l'ordine
3. Clicca **Stampa Etichetta DYMO**
4. Seleziona:
   - **Stampante DYMO**: Scegli la stampante dal menu
   - **Template**: Seleziona il layout dell'etichetta
   - **Copie**: Numero di copie da stampare
5. Clicca **Stampa**

#### Metodo 2: Stampa in massa

1. Accedi a **Post Vendita > Ordini**
2. Filtra gli ordini da stampare (per stato, data, marketplace)
3. Seleziona gli ordini spuntando i checkbox
4. Clicca **Azioni Bulk > Stampa Etichette DYMO**
5. Compila il modulo come sopra
6. Clicca **Stampa Tutto**

### Template DYMO disponibili

| Template | Contenuto | Uso |
|---|---|---|
| **Standard** | Indirizzo destinatario, numero ordine, barcode | Uso generico |
| **Compatto** | Solo indirizzo e barcode | Spazi ridotti |
| **Esteso** | Indirizzo, tracking, numero ordine, barcode | Tracking completo |
| **Internazionale** | Indirizzo con paese e flags | Spedizioni estere |

### Campi stampati su etichetta

L'etichetta standard contiene:

```
[BARCODE numero_ordine]

Nome Cliente
Indirizzo Cliente
CAP Città Paese

Ordine: #123456
Tracking: ABC123456XYZ
Corriere: GLS
```

### Configurazione template DYMO

Per personalizzare il template:

1. Vai a **Impostazioni > DYMO Label Template**
2. Clicca **Modifica Template**
3. Trascinare gli elementi sulla etichetta:
   - Caselle testo per dati variabili
   - Codice a barre
   - Logo aziendale
4. Clicca **Salva**

### Troubleshooting DYMO

| Problema | Soluzione |
|---|---|
| Stampante non riconosciuta | Verificare che DYMO Label sia aperto, stampante accesa e collegata |
| Errore "No printer found" | Installare DYMO Label v8.7 o superiore, riavviare browser |
| Etichette tagliate | Verificare che carta sia correttamente caricata nel modello 4x6 |
| QR code non stampato | Verificare che il template includa il campo barcode |

---

## Bandierine e note

### Cosa sono le bandierine

Le bandierine sono tag colorati che permettono di marcare gli ordini per prioritizzazione e organizzazione.

### Colori disponibili e significati

| Colore | Significato | Uso |
|---|---|---|
| **Rosso** | Urgente/Problematico | Ordini con problemi che richiedono attenzione immediata |
| **Arancione** | Moderata priorità | Ordini da elaborare velocemente |
| **Giallo** | Normale | Ordini in fase di elaborazione |
| **Verde** | Completo/OK | Ordini elaborati correttamente |
| **Blu** | In attesa | Ordini in attesa di azioni esterne (pagamento, etc.) |
| **Viola** | VIP/Speciale | Clienti importanti o ordini con istruzioni speciali |

### Come aggiungere bandierine

#### Metodo 1: Da singolo ordine

1. Accedi a **Post Vendita > Ordini**
2. Seleziona l'ordine
3. Clicca sull'icona **Bandierina** nella riga dell'ordine
4. Seleziona il colore desiderato
5. La bandierina viene applicata istantaneamente

#### Metodo 2: Azioni bulk

1. Seleziona più ordini spuntando i checkbox
2. Clicca **Azioni Bulk > Aggiungi Bandierina**
3. Seleziona il colore
4. Clicca **Applica**

### Come rimuovere bandierine

1. Clicca sulla bandierina dell'ordine
2. Seleziona **Rimuovi Bandierina**

### Note su ordini

Le note sono commenti privati associati all'ordine, non visibili al cliente.

### Come aggiungere note

1. Accedi a **Post Vendita > Ordini**
2. Seleziona l'ordine
3. Scorri a **Note Ordine**
4. Clicca **Aggiungi Nota**
5. Digita il testo della nota
6. Clicca **Salva**

Esempio di note utili:
- "Cliente ha richiesto imballaggio speciale"
- "Controllare tracking con corriere, consegna ritardata"
- "Preferenza: non inserire volantini pubblicitari"

### Filtri per bandierine

Puoi filtrare gli ordini per bandierina:

1. Vai a **Post Vendita > Ordini**
2. Nel pannello filtri, seleziona:
   - **Bandierina**: Scegli il colore
   - **Data**: Periodo ordine
   - **Marketplace**: Canale di vendita
3. Gli ordini verranno filtrati di conseguenza

---

## Configurazione API corrieri

### Corrieri integrati

SellRapido supporta integrazione diretta con i seguenti corrieri:

| Corriere | Funzionalità | Requisiti |
|---|---|---|
| **GLS** | Inoltro automatico, tracking | Account GLS con API |
| **DHL** | Inoltro automatico, tracking | Credenziali DHL Business |
| **DPD** | Inoltro automatico, tracking | Account DPD Shipper |
| **TNT** | Inoltro automatico, tracking | Credenziali TNT |
| **Fedex** | Inoltro automatico, tracking | Account Fedex per business |
| **UPS** | Inoltro automatico, tracking | Credenziali UPS |
| **Poste Italiane** | Inserimento manuale | Nessuno |
| **Corriere generico** | Inserimento manuale | Nessuno |

### Come configurare API corriere

#### Step 1: Ottenere credenziali

Contatta il corriere per ottenere:
- Account numero cliente
- API Key (se disponibile)
- Username/Password
- Contratto numero

#### Step 2: Inserire credenziali in SellRapido

1. Vai a **Impostazioni > Corrieri**
2. Clicca **Nuovo Corriere**
3. Seleziona il corriere dal menu
4. Inserisci i dati:
   - **Account**: Numero account corriere
   - **API Key**: Chiave API (se applicabile)
   - **Username**: Utente per accesso
   - **Password**: Password di accesso
5. Clicca **Test Connessione** per verificare
6. Clicca **Salva**

#### Step 3: Abilitare inoltro automatico

1. Vai a **Impostazioni > Automatismi > Inoltro Corrieri**
2. Seleziona il corriere
3. Abilita **Inoltro Automatico Ordini**
4. Scegli i trigger:
   - "Quando ordine è Pagato"
   - "Quando ordine è Spedito"
   - "Immediatamente dopo creazione"
5. Salva

### Inoltro ordini a corriere

#### Metodo 1: Automatico

Se configurato l'inoltro automatico:
1. L'ordine viene inoltrato automaticamente al corriere quando lo stato cambia
2. Il tracking viene recuperato automaticamente
3. Il numero tracking viene inserito automaticamente in SellRapido

#### Metodo 2: Manuale

Per inoltri manuali:

1. Accedi a **Post Vendita > Ordini**
2. Seleziona l'ordine
3. Clicca **Inoltra a Corriere**
4. Seleziona il corriere
5. Compila i dati aggiuntivi (se necessario):
   - Tipo spedizione (Standard, Express, etc.)
   - Servizi aggiuntivi (Assicurazione, firma, etc.)
6. Clicca **Inoltra**

### Recupero tracking da corriere

Dopo l'inoltro a corriere:

1. SellRapido automaticamente interroga l'API del corriere per il tracking
2. Una volta disponibile, il numero tracking viene inserito nell'ordine
3. Il cliente riceve automaticamente l'email di spedizione con il tracking
4. Lo stato dell'ordine passa a "Spedito"

**Nota**: Il recupero può richiedere da 5 a 30 minuti a seconda del corriere.

### Troubleshooting corrieri

| Problema | Soluzione |
|---|---|
| Inoltro non funziona | Verificare che le credenziali corriere siano corrette e che il test di connessione passi |
| Tracking non viene recuperato | Verificare che l'API del corriere sia attiva, attendere 30 minuti |
| Errore "Account non valido" | Verificare credenziali con il corriere, regenerare API key se necessario |
| "Inoltro ordine a corriere non funziona" | Aprire ticket a SellRapido con categoria "Corrieri" e sottocategoria "Inoltro ordine a corriere non funziona" |

### Estrapolazione dati ordini per corrieri

Per ottenere file di esportazione da inviare al corriere:

1. Vai a **Post Vendita > Ordini**
2. Filtra gli ordini da esportare
3. Seleziona gli ordini
4. Clicca **Esporta per Corriere**
5. Seleziona il corriere
6. Seleziona il formato (di solito CSV o Excel fornito dal corriere)
7. Clicca **Scarica**

Il file conterrà i dati nel formato specifico del corriere (indirizzo, pesi, importi, etc.)

---

## Best Practices Gestione Ordini

1. **Cambio stato rapido**: Cambiare lo stato dell'ordine a "Pagato" non appena confermato il pagamento
2. **Tracking prioritario**: Inserire il tracking entro 24 ore dalla spedizione
3. **Email tempestive**: Inviare notifica di spedizione con tracking al cliente immediatamente
4. **Bandierine di priorità**: Usare bandierine rosse per ordini problematici per una risposta veloce
5. **Feedback marketplace**: Richiedere feedback positivo dopo la consegna
6. **Automazione**: Configurare corrieri con inoltro automatico quando possibile
7. **Documentazione**: Aggiungere note su ordini speciali per futuro riferimento
8. **Monitoraggio**: Controllare regolarmente gli ordini nello stato "In preparazione" per evitare ritardi
9. **Resi**: Processare i resi rapidamente per mantenere alta la reputazione
10. **Backup**: Esportare regolarmente i dati degli ordini per backup

---

## Supporto Gestione Ordini

Per problemi legati alla gestione ordini, aprire un ticket con:

- **Categoria**: Ordini
- **Sottocategoria**: Una delle seguenti:
  - Cambio status ordine non funziona
  - Configurazione Amazon FBA
  - Estrapolazione ordini per fatturazione
  - Ordini in post vendita non entrano
  - Verifica informazioni dell'ordine
