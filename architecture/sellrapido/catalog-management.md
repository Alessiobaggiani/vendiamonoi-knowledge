# Catalog Management - SellRapido

## Indice

1. [Overview](#overview)
2. [Caricamento cataloghi](#caricamento-cataloghi)
3. [Specifiche feed CSV](#specifiche-feed-csv)
4. [Varianti e attributi](#varianti-e-attributi)
5. [Esportazione catalogo](#esportazione-catalogo)
6. [Eliminazione catalogo](#eliminazione-catalogo)

---

## Overview

La gestione dei cataloghi è la fase preliminare alla pubblicazione. Un catalogo rappresenta l'elenco dei prodotti disponibili per la vendita.

### Fonti di importazione catalogo

SellRapido permette di importare cataloghi da diverse fonti:

1. **FTP** - Upload diretto via FTP
2. **Danea** - Integrazione con software di gestione Danea
3. **Fatture in Cloud** - Sincronizzazione da Fatture in Cloud
4. **Download dai marketplace** - Importazione da marketplace esistenti

### Processo di gestione catalogo

1. **Importazione**: Caricamento del catalogo nella piattaforma
2. **Verifica**: Controllo dati e assenza errori
3. **Configurazione**: Mapping categorie, specificazione dettagli
4. **Pubblicazione**: Invio su marketplace/ecommerce
5. **Manutenzione**: Aggiornamenti periodici

---

## Caricamento cataloghi

### Via FTP

Per caricare un catalogo via FTP:

1. Accedi a **Prevendita > Prodotti**
2. Clicca su **Nuovo Catalogo**
3. Seleziona **FTP** come fonte
4. Riceverai i dati di accesso FTP (host, username, password, directory)
5. Carica il tuo file CSV nella directory indicata
6. SellRapido leggerà automaticamente il file

### Via Danea

Per caricare da Danea:

1. Accedi a **Prevendita > Prodotti**
2. Clicca su **Nuovo Catalogo**
3. Seleziona **Danea** come fonte
4. Autenticati con le credenziali Danea
5. Seleziona il catalogo da importare
6. SellRapido sincronizzerà i dati

### Via Fatture in Cloud

Per caricare da Fatture in Cloud:

1. Accedi a **Prevendita > Prodotti**
2. Clicca su **Nuovo Catalogo**
3. Seleziona **Fatture in Cloud** come fonte
4. Autenticati con le credenziali Fatture in Cloud
5. SellRapido sincronizzerà automaticamente i dati dei prodotti

### Da Marketplace Esistenti

Per scaricare e reimportare da marketplace configurati:

1. Accedi a **Prevendita > Prodotti**
2. Clicca su **Download da Marketplace**
3. Seleziona il marketplace da cui scaricare
4. Scegli il catalogo da scaricare
5. SellRapido creerà un nuovo catalogo locale

---

## Specifiche feed CSV

### Tracciato del file

Il file CSV deve contenere una riga di intestazione con i nomi dei campi e una riga per ogni prodotto.

**Separatore**: virgola (,) o punto e virgola (;)
**Codifica**: UTF-8
**Estensione**: .csv

### Campi obbligatori

| Campo | Descrizione | Tipo | Esempio |
|---|---|---|---|
| SKU | Codice univoco del prodotto | Alfanumerico | ABC123 |
| Title | Titolo del prodotto | Testo | Samsung Galaxy S10 |
| Price | Prezzo di listino | Decimale | 599.99 |
| Quantity | Quantità disponibile | Numerico | 50 |

### Campi facoltativi

| Campo | Descrizione | Tipo |
|---|---|---|
| EAN | Codice EAN/UPC | Alfanumerico |
| Brand | Marca del prodotto | Testo |
| Category1 | Categoria livello 1 | Testo |
| Category2 | Categoria livello 2 | Testo |
| Category3 | Categoria livello 3 | Testo |
| Description | Descrizione prodotto | Testo |
| Image1 - Image9 | URL immagini (da 1 a 9) | URL |
| Weight | Peso prodotto in kg | Decimale |
| ShippingPrice | Costo spedizione | Decimale |
| ShippingDays | Giorni preparazione ordine | Numerico |
| IVA | Percentuale IVA | Decimale |
| Extra1 - Extra9 | Campi extra (da 1 a 9) | Testo |

### Campi per varianti

| Campo | Descrizione |
|---|---|
| VariantSKU | SKU della variante |
| VariantAttribute1 - VariantAttribute5 | Attributi della variante (es. Colore, Taglia) |
| VariantQuantity | Quantità della variante |
| VariantPrice | Prezzo della variante (se diverso) |

### Esempio di file CSV

```
SKU,Title,Price,Quantity,EAN,Brand,Category1,Description,Image1,Weight
SAMSUNG-S10,Samsung Galaxy S10,599.99,50,8806090092789,Samsung,Smartphone,Display AMOLED 6.1 pollici,https://image.example.com/samsung-s10.jpg,5.8
IPHONE-11,Apple iPhone 11,699.99,30,4549995053333,Apple,Smartphone,Processore A13 Bionic,https://image.example.com/iphone-11.jpg,5.4
```

### Varianti - Esempio

```
SKU,Title,Price,Quantity,VariantSKU,VariantAttribute1,VariantAttribute2
TSHIRT-001,T-Shirt Basic,19.99,100,TSHIRT-001-BL-XL,Black,XL
TSHIRT-001,T-Shirt Basic,19.99,100,TSHIRT-001-WH-M,White,M
TSHIRT-001,T-Shirt Basic,19.99,100,TSHIRT-001-BL-M,Black,M
```

---

## Varianti e attributi

### Gestione delle varianti

Le varianti permettono di pubblicare diverse versioni dello stesso prodotto (es. colori, taglie diverse).

### Come caricare varianti

#### Metodo 1: Nel feed CSV

1. Crea righe multiple con lo stesso SKU base
2. Aggiungi i campi **VariantSKU** e attributi variante
3. Ogni riga rappresenta una diversa configurazione

#### Metodo 2: Da back office SellRapido

1. Accedi a **Prevendita > Prodotti**
2. Seleziona il catalogo
3. Seleziona il prodotto
4. Clicca su **Varianti**
5. Clicca su **Aggiungi Variante**
6. Compila i campi della variante
7. Salva

### Attributi disponibili

Gli attributi rappresentano le caratteristiche che differenziano le varianti:

- **Colore** - Colore del prodotto
- **Taglia** - Taglia/Size
- **Peso** - Peso diverso
- **Lunghezza** - Lunghezza prodotto
- **Larghezza** - Larghezza prodotto
- **Altezza** - Altezza prodotto
- **Volume** - Volume/Capacità
- **Gusto** - Sapore/Gusto
- **Formato** - Formato confezione
- **Genere** - Uomo/Donna/Unisex

### Caricamento di immagini per varianti

È possibile associare immagini diverse a varianti diverse nel CSV:

```
SKU,VariantSKU,VariantAttribute1,Image1,Image2
SHOE-001,SHOE-001-BK-40,Black,https://image.example.com/shoe-black-1.jpg,https://image.example.com/shoe-black-2.jpg
SHOE-001,SHOE-001-RD-40,Red,https://image.example.com/shoe-red-1.jpg,https://image.example.com/shoe-red-2.jpg
```

---

## Esportazione catalogo

### Esportare il catalogo

Per esportare i dati del catalogo in un file:

1. Accedi a **Prevendita > Prodotti**
2. Seleziona il catalogo
3. Clicca su **Esporta**
4. Seleziona il formato:
   - **CSV** - Formato testo
   - **Excel** - Formato .xlsx
5. Clicca su **Esporta**
6. Il file verrà scaricato sul tuo computer

### Cosa viene esportato

L'esportazione include:
- Tutti i prodotti del catalogo
- Varianti (se presenti)
- Immagini (come URL)
- Tutti i campi configurati
- Quantità attuali

**Nota**: Le immagini vengono esportate come URL, non come file embedded.

---

## Eliminazione catalogo

### Procedura di eliminazione

Per eliminare un catalogo:

1. Accedi a **Prevendita > Prodotti**
2. Seleziona il catalogo da eliminare
3. Clicca su **Impostazioni**
4. Scorri fino al fondo
5. Clicca su **Elimina Catalogo**
6. Conferma la cancellazione

### Avvertenze

**Importante**: L'eliminazione del catalogo comporta:

- Rimozione di TUTTI i prodotti associati
- Chiusura automatica di tutte le inserzioni su tutti i marketplace/ecommerce
- Impossibilità di recuperare i dati (backup consigliato)

### Procedura consigliata prima dell'eliminazione

Se il catalogo è ancora pubblicato:

1. Recati a **Prevendita > Report Inserzioni**
2. Seleziona il catalogo
3. Crea un filtro di esclusione totale per chiudere le inserzioni
4. Attendi che il Report Inserzioni mostri "0" inserzioni
5. Solo allora procedere all'eliminazione

---

## Best Practices

### Preparazione del CSV

1. **Validazione**: Verificare che tutti gli SKU siano univoci
2. **Encoding**: Utilizzare sempre UTF-8
3. **Separatori**: Scegliere un separatore (virgola o punto-virgola) e rimanere coerenti
4. **URL immagini**: Verificare che siano raggiungibili
5. **Prezzi**: Utilizzare virgola per i decimali e verificare che siano numeri validi

### Organizzazione cataloghi

1. Creare cataloghi separati per categorie diverse (se ha senso logico)
2. Usare nomi descrittivi per i cataloghi
3. Documentare il tracciato dei dati utilizzati
4. Fare backup regolari del CSV originale

### Gestione aggiornamenti

1. **Importazione parziale**: SellRapido può riconoscere SKU già presenti e aggiornare solo i campi modificati
2. **Nuovi prodotti**: Aggiungere nuovi SKU a ogni importazione
3. **Frequenza**: Aggiornare regolarmente quantità e prezzi

### Immagini

1. **Formato**: JPG, PNG, WEBP
2. **Qualità**: Minimo 300x300 pixel
3. **URL pubblici**: Le immagini devono essere accessibili da internet
4. **Numero**: Massimo 9 immagini per prodotto

### Varianti

1. **Attributi coerenti**: Usare sempre gli stessi attributi per varianti dello stesso prodotto
2. **Quantità**: Specificare la quantità per ogni variante
3. **Prezzi**: Se diversi dal prezzo base, indicarli
4. **SKU univoci**: Assicurarsi che ogni variante abbia un SKU differente

---

## Troubleshooting

### Errori comuni durante l'importazione

| Errore | Causa | Soluzione |
|---|---|---|
| SKU duplicato | Due prodotti con lo stesso SKU | Verificare e rendere univoci gli SKU |
| Formato CSV non riconosciuto | File non in UTF-8 o separatore non riconosciuto | Riallineare il file a UTF-8 e usare separatore standard |
| Errore nei prezzi | Prezzi con formato non numerico (es. "€500") | Rimuovere simboli e usare solo numeri e virgola |
| Immagini non caricate | URL immagini non raggiungibili | Verificare che gli URL siano pubblici e corretti |
| Quantità non aggiornata | Campo quantità non presente o vuoto | Verificare nome colonna e valorizzare tutte le righe |

---

## Aggiornamento Dati

### Frequenza aggiornamenti

SellRapido aggiorna le quantità dei prodotti una volta l'ora.

**Conseguenza**: Tra un aggiornamento e l'altro possono esserci differenze tra i dati locali e le pubblicazioni online.

### Sincronizzazione

Per forzare un aggiornamento immediato:

1. Vai a **Prevendita > Prodotti**
2. Seleziona il catalogo
3. Clicca su **Sincronizza Ora** (se disponibile)
4. Attendi il completamento della sincronizzazione

---

## Supporto

Per problemi legati al caricamento dei cataloghi, aprire un ticket con categoria:
- **Cataloghi**
- **Sottocategoria**: Messaggio di errore importazione catalogo in SellRapido
