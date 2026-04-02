# Troubleshooting and Support - SellRapido

## Indice

1. [Guida rapida troubleshooting](#guida-rapida-troubleshooting)
2. [Problemi comuni e soluzioni](#problemi-comuni-e-soluzioni)
3. [Diagnostica system](#diagnostica-system)
4. [Come contattare il supporto](#come-contattare-il-supporto)
5. [FAQ](#faq)

---

## Guida rapida troubleshooting

### Problem-solving flow

```
1. Identifica il problema
   ↓
2. Cerca nella sezione "Problemi comuni"
   ↓
3. Se non trovato, vai a "Diagnostica system"
   ↓
4. Se ancora irrisolto, apri un ticket di supporto
```

### Checklist iniziale

Prima di contattare il supporto, verifica:

- [ ] Sei loggato correttamente in SellRapido
- [ ] La connessione internet è stabile
- [ ] Hai i permessi necessari (Pre-vendita, Post-vendita, Admin)
- [ ] Il browser è aggiornato e privo di cache
- [ ] Non ci sono messaggi di errore specifici in console browser
- [ ] I dati che stai cercando di modificare esistono effettivamente

---

## Problemi comuni e soluzioni

### Categoria: Accesso e Autenticazione

#### Problema: Non riesco ad accedere al mio account

**Messaggi di errore associati**:
- "Email o password errata"
- "Account bloccato"
- "Account non trovato"

**Soluzione**:

1. **Verifica le credenziali**:
   - Assicurati che la mail sia esatta (case-sensitive non applicabile)
   - Verifica la password (è case-sensitive)
   - Usa la stessa mail usata durante la registrazione

2. **Password dimenticata**:
   - Vai a https://app.sellrapido.com/
   - Clicca "Password dimenticata?"
   - Riceverai email con link di reset
   - Clicca il link e crea nuova password
   - Accedi con le nuove credenziali

3. **Account bloccato**:
   - Contatta il supporto (categoria: Impostazioni Account > Errore login account)
   - Fornisci l'email dell'account
   - Comunica il messaggio di errore ricevuto

4. **Se usi 2FA**:
   - Assicurati che l'app Google Authenticator sia sincronizzata
   - Se è scaduta o persa, contatta il supporto per reset 2FA

---

#### Problema: Ho perso i codici di backup 2FA

**Soluzione**:

1. Se ancora hai accesso al tuo account:
   - Vai a **Impostazioni > Sicurezza > Autenticazione a Due Fattori**
   - Clicca **Disabilita 2FA**
   - Verifica con il codice attuale
   - Riabilita 2FA e salva i nuovi codici di backup

2. Se non riesci ad accedere:
   - Contatta il supporto (categoria: Impostazioni Account > Errore login account)
   - Fornisci informazioni di identificazione dell'account
   - Il supporto disabiliterà 2FA

---

### Categoria: Cataloghi e Prodotti

#### Problema: Il catalogo non si importa

**Messaggi di errore comuni**:
- "Formato CSV non riconosciuto"
- "SKU duplicato"
- "Errore nei prezzi"

**Soluzione**:

1. **Verifica il formato CSV**:
   - Codifica deve essere UTF-8 (non ANSI)
   - Separatore deve essere coerente (virgola O punto-virgola, non misto)
   - Nomi colonne devono corrispondere esattamente (maiuscole/minuscole)

2. **Verifica i campi obbligatori**:
   - SKU: Presente e univoco
   - Title: Non vuoto
   - Price: Numero con virgola decimale (non €)
   - Quantity: Numero intero positivo

3. **Risolvi errori specifici**:
   - SKU duplicato: Verifica che ogni riga abbia SKU diverso
   - Prezzi: Rimuovi simboli €/$/etc, usa solo numeri e virgola
   - Immagini: Verifica che gli URL inizino con http:// o https://

4. **Se il problema persiste**:
   - Apri un ticket (categoria: Cataloghi > Messaggio di errore importazione catalogo in SellRapido)
   - Allega il file CSV
   - Copia il messaggio di errore esatto

---

#### Problema: Le quantità non si aggiornano

**Sintomi**:
- Le quantità mostrate su marketplace non cambiano
- Ordini continua ad entrarci anche con stock a zero
- L'inventario di SellRapido non riflette le vendite

**Soluzione**:

1. **Verifica la modalità quantità**:
   - Vai a **Prevendita > Prodotti > Impostazioni > Quantità**
   - Se è "Quantità Fissa", le quantità non cambiano automaticamente
   - Cambia a "Importa da Listino" per aggiornamento automatico

2. **Attendi la sincronizzazione**:
   - SellRapido aggiorna quantità una volta all'ora
   - Se hai ricevuto un ordine da poco, attendi fino all'ora successiva

3. **Forza allineamento inventario**:
   - Vai a **Prevendita > Report Inserzioni**
   - Seleziona il marketplace
   - Clicca **Richiedi Allineamento Inventario**
   - Attendi il completamento (30 min - 2 ore)

4. **Se il problema persiste**:
   - Apri un ticket (categoria: Marketplace/Ecommerce > Richiesta allineamento inventario)
   - Specifica il catalogo, marketplace, e SKU interessati

---

#### Problema: Le immagini non si vedono sul marketplace

**Sintomi**:
- Immagini non appaiono su Amazon/eBay/altro
- Placeholder grigio o icona spezzata

**Soluzione**:

1. **Verifica gli URL immagini**:
   - Gli URL devono essere completamente raggiungibili
   - Test: Copia l'URL e aprilo direttamente nel browser
   - Verifica che l'immagine si visualizzi

2. **Formati supportati**:
   - JPG, PNG, WEBP accettati
   - Evita GIF animate, BMP, TIFF
   - Dimensioni minime: 300x300 pixel

3. **Risolvi problemi URL**:
   - Assicurati che siano HTTPS (alcuni marketplace richiedono)
   - Rimuovi parametri di tracciamento (?utm_source=...)
   - Verifica che non ci siano reindirizzamenti

4. **Se le immagini sono su server locale**:
   - Carica le immagini su un server pubblico
   - Usa i link pubblici nel catalogo

5. **Se il problema persiste**:
   - Apri un ticket (categoria: Marketplace/Ecommerce > Incongruenza tra impostazioni e inserzioni)
   - Fornisci l'URL dell'immagine e lo SKU

---

### Categoria: Pubblicazioni

#### Problema: Le inserzioni non vengono pubblicate

**Sintomi**:
- Status rosso nel Report Inserzioni
- Numero inserzioni rimane 0
- Errore generico di pubblicazione

**Soluzione**:

1. **Verifica il Report Inserzioni**:
   - Vai a **Prevendita > Report Inserzioni**
   - Seleziona il marketplace
   - Clicca **Visualizza Errori**
   - Leggi il messaggio di errore specifico

2. **Errori comuni**:
   - Vedi la sezione "Errori di pubblicazione" in publication-errors.md
   - Segui le soluzioni specifiche per il tipo di errore

3. **Verifica configurazione canale**:
   - Vai a **Prevendita > Prodotti > Impostazioni**
   - Seleziona il marketplace
   - Clicca la rotella per aprire le impostazioni
   - Verifica che tutte le sezioni (Prezzi, Spedizione, etc.) siano compilate
   - Status deve essere verde

4. **Verifica i filtri**:
   - Vai a **Impostazioni > Filtri**
   - Verifica che ci sia almeno un filtro di inclusione
   - Se ci sono filtri di esclusione, verifica che non escludano tutto
   - Prova a rimuovere filtri restrittivi

5. **Rielabora le pubblicazioni**:
   - Nel Report Inserzioni, clicca **Rielabora**
   - Scegli **Solo errori** o **Tutti i prodotti**
   - Attendi il completamento

---

#### Problema: Pubblicazioni in errore su eBay

**Errori specifici eBay**:
- Vedi sezione "Errori eBay" in publication-errors.md

**Passi generali**:

1. Identifica l'errore specifico dal Report Inserzioni
2. Leggi la descrizione dell'errore
3. Applica la soluzione indicata
4. Rielabora il prodotto

---

### Categoria: Ordini e Post-vendita

#### Problema: Gli ordini non si importano

**Sintomi**:
- Nessun ordine appare in Post Vendita > Ordini
- Vendite sul marketplace ma non in SellRapido

**Soluzione**:

1. **Verifica il collegamento del marketplace**:
   - Vai a **Impostazioni > Credenziali Marketplace**
   - Verifica che il marketplace sia collegato
   - Clicca il marketplace e verifica il test di connessione

2. **Forza lo scarico ordini**:
   - Vai a **Post Vendita > Ordini**
   - Clicca **Forza Scarico Vendite**
   - Seleziona l'intervallo di date
   - Clicca **Scarica**

3. **Verifica le tempistiche**:
   - SellRapido scarica ordini automaticamente ogni ora
   - Se l'ordine è stato appena ricevuto, attendi fino all'ora successiva
   - Se attende da più di 2 ore, procedi ai passi successivi

4. **Verifica i filtri post-vendita**:
   - È possibile che gli ordini siano filtrati
   - Vai a **Post Vendita > Ordini**
   - Clicca **Rimuovi Filtri** (se presenti)

5. **Se il problema persiste**:
   - Apri un ticket (categoria: Ordini > Ordini in post vendita non entrano)
   - Fornisci: Marketplace, periodo, numero ordini attesi

---

#### Problema: Non riesco a cambiare lo stato dell'ordine

**Messaggio di errore**:
- "Cambio status ordine non funziona"

**Soluzione**:

1. **Verifica stato destinazione**:
   - Non tutti gli stati sono raggiungibili
   - Vedi la tabella "Transizioni di stato consentite" in order-management.md
   - Assicurati che lo stato di destinazione sia raggiungibile

2. **Verifica campi obbligatori**:
   - Se cambi a "Spedito", il tracking è obbligatorio
   - Compila i campi prima di salvare

3. **Riprova il cambio stato**:
   - A volte è un errore temporaneo
   - Ricarica la pagina e riprova

4. **Se il problema persiste**:
   - Apri un ticket (categoria: Ordini > Cambio status ordine non funziona)
   - Fornisci numero ordine e stati coinvolti

---

#### Problema: Il tracking non si sincronizza

**Sintomi**:
- Ho inserito il tracking in SellRapido
- Il tracking non appare sul marketplace
- Il cliente non riceve l'email di spedizione

**Soluzione**:

1. **Verifica l'inserimento del tracking**:
   - Vai a **Post Vendita > Ordini**
   - Seleziona l'ordine
   - Verifica che il tracking sia salvato (status = "Spedito")

2. **Attendi la sincronizzazione**:
   - La sincronizzazione avviene entro 15-30 minuti
   - Se è passato più tempo, procedi ai passi successivi

3. **Forza la sincronizzazione**:
   - Modifica l'ordine cliccando la rotella
   - Cambia il tracking o aggiungi un nuovo numero
   - Salva nuovamente

4. **Verifica il formato del tracking**:
   - Non deve contenere caratteri speciali
   - Deve essere il formato corretto per il corriere

5. **Se il problema persiste**:
   - Apri un ticket (categoria: Marketplace/Ecommerce > Incongruenza tra impostazioni e inserzioni)
   - Fornisci numero ordine e tracking

---

### Categoria: Corrieri e Spedizioni

#### Problema: L'inoltro automatico al corriere non funziona

**Sintomi**:
- Gli ordini non vengono inoltrati al corriere
- Status rimane "Pagato" invece di passare a "Spedito"

**Soluzione**:

1. **Verifica la configurazione corriere**:
   - Vai a **Impostazioni > Corrieri**
   - Seleziona il corriere
   - Clicca **Test Connessione**
   - Se il test fallisce, ricollega il corriere

2. **Verifica l'automatismo**:
   - Vai a **Impostazioni > Automatismi > Inoltro Corrieri**
   - Assicurati che il corriere sia abilitato
   - Verifica il trigger (Quando pagato? Quando spedito?)

3. **Inoltro manuale**:
   - Se l'automatismo non funziona, fai l'inoltro manualmente
   - Vai a **Post Vendita > Ordini**
   - Seleziona l'ordine
   - Clicca **Inoltra a Corriere**

4. **Se il problema persiste**:
   - Apri un ticket (categoria: Corrieri > Inoltro ordine a corriere non funziona)
   - Fornisci: Corriere, numero ordine, messaggio di errore

---

#### Problema: Il tracking non viene recuperato dal corriere

**Sintomi**:
- Ho inoltrato l'ordine al corriere
- Passano 30+ minuti ma il tracking non appare

**Soluzione**:

1. **Attendi il recupero**:
   - Alcuni corrieri impiegano fino a 30 minuti per fornire il tracking
   - Attendi almeno 30 minuti prima di procedere

2. **Verifica la connessione corriere**:
   - Vai a **Impostazioni > Corrieri**
   - Seleziona il corriere
   - Verifica il status della connessione
   - Se offline, ricollega

3. **Inserisci il tracking manualmente**:
   - Se il recupero automatico continua a fallire
   - Vai a **Post Vendita > Ordini**
   - Seleziona l'ordine
   - Clicca **Aggiungi Tracking**
   - Inserisci manualmente il numero di tracking

4. **Se il problema persiste**:
   - Apri un ticket (categoria: Corrieri > Inoltro ordine a corriere non funziona)
   - Fornisci: Corriere, numero ordine, data inoltro

---

## Diagnostica system

### Controllare lo status di SellRapido

Per verificare se SellRapido è operativo:

1. Accedi a https://status.sellrapido.com/
2. Verifica lo status dei servizi
3. Se ci sono interruzioni programmate o problemi, saranno indicati qui
4. Se il servizio è down, attendere il ripristino

### Controllare la console browser

Errori Javascript possono fornire informazioni:

1. Premi F12 per aprire Developer Tools
2. Vai alla sezione "Console"
3. Cerca messaggi di errore (testo rosso)
4. Copia il testo dell'errore completo
5. Se apri un ticket, includi lo screenshot dell'errore

### Testare la connessione al marketplace

Per verificare che le credenziali del marketplace funzionino:

1. Vai a **Impostazioni > Credenziali Marketplace**
2. Seleziona il marketplace
3. Clicca il pulsante **Test Connessione**
4. Se il test fallisce, l'errore ti indicherà il problema

### Verificare i log errori

1. Vai a **Impostazioni > Log**
2. Filtra per:
   - **Data**: Quando è avvenuto il problema
   - **Tipo**: Categoria di errore
   - **Marketplace**: Se il problema riguarda un canale specifico
3. Clicca su un log per visualizzare i dettagli

---

## Come contattare il supporto

### Apertura di un ticket

#### Passo 1: Accedi all'Area Ticket

1. Accedi al tuo account SellRapido
2. Vai a **Impostazioni > Supporto**
3. Clicca **Area Ticket**
4. Se è la prima volta, crea un account Customer Portal

#### Passo 2: Seleziona la categoria corretta

Usa questa tabella per trovare la categoria giusta:

| Problema | Categoria | Sottocategoria |
|---|---|---|
| Errore importazione catalogo | Cataloghi | Messaggio di errore importazione catalogo in SellRapido |
| Errore pubblicazione | Marketplace/Ecommerce | Pubblicazioni sul canale di vendita in errore |
| Ordini non importati | Ordini | Ordini in post vendita non entrano |
| Problemi login | Impostazioni Account | Errore login account |
| Aggiungi team member | Impostazioni Account | Aggiunta nuovi membri ad account |
| Problema corriere | Corrieri | Inoltro ordine a corriere non funziona |
| Configurazione B2B | B2Bhub | Consigli settaggi di pubblicazione |

#### Passo 3: Descrivi il problema

Fornisci informazioni dettagliate:

- **Cosa stai cercando di fare**: Passaggi passo-passo
- **Cosa ti aspettavi**: Risultato atteso
- **Cosa è successo**: Comportamento effettivo
- **Quando è iniziato**: Data/ora approssimativa
- **Screenshot**: Se applicabile, allega screenshot dell'errore
- **File**: Se rilevante (CSV, file catalogo, etc.)

#### Passo 4: Descrivi i tentativi di soluzione

- Indica cosa hai già provato
- Questo aiuta il supporto a velocizzare la risoluzione
- Evita di ripetere soluzioni già provate

#### Passo 5: Invia il ticket

- Clicca **Invia Ticket**
- Riceverai una email di conferma con il numero del ticket
- Usa questo numero per tracciare il ticket

### Tempi di risposta

- **Categoria Urgente**: 2-4 ore
- **Categoria Standard**: 8-24 ore
- **Categoria Bassa Priorità**: 24-72 ore

I tempi sono approssimativi e possono variare in base al carico di lavoro.

### Chiusura automatica ticket

- Un ticket rimane aperto per 5 giorni dopo la risposta del supporto
- Se non rispondi entro 5 giorni, il ticket viene chiuso automaticamente
- Se hai bisogno di ulteriore assistenza, rispondi alla email e il ticket verrà riaperto

---

## FAQ

### F: Quanto tempo impiega un catalogo a sincronizzarsi?

R: La sincronizzazione avviene una volta all'ora. Se importi un catalogo, attendi fino all'ora successiva per vedere gli aggiornamenti.

---

### F: Posso pubblicare gli stessi prodotti su più marketplace?

R: Sì, puoi pubblicare lo stesso catalogo su molteplici marketplace contemporaneamente. SellRapido sincronizza automaticamente l'inventario tra i canali.

---

### F: Cosa succede se un prodotto si esaurisce?

R: Se la quantità scende a zero (o sotto il minimo configurato), l'inserzione viene automaticamente chiusa su tutti i marketplace in cui è stata pubblicata.

---

### F: Posso modificare i dati del prodotto dopo la pubblicazione?

R: Sì. Modifica i dati nel catalogo in SellRapido e rielabora la pubblicazione. I marketplace verranno aggiornati entro 1 ora.

---

### F: Come posso aumentare le vendite?

R: Consulta la sezione "Best Practices" nei vari file di documentazione per raccomandazioni su prezzi, descrizioni, categorie e targeting.

---

### F: Posso vendere a livello internazionale?

R: Sì, molti marketplace supportano spedizioni internazionali. Configura le impostazioni di spedizione e seleziona le nazioni di destinazione nella configurazione del marketplace.

---

### F: Quanto costa SellRapido?

R: I prezzi variano in base al piano scelto. Per informazioni sui prezzi, vai a https://www.sellrapido.com/prezzi o contatta il team commerciale.

---

### F: SellRapido supporta i multi-valuta?

R: Sì. SellRapido converte automaticamente i prezzi nella valuta del marketplace al momento della pubblicazione, basandosi sui tassi di cambio attuali.

---

### F: Posso recuperare ordini cancellati?

R: No, gli ordini cancellati non possono essere recuperati. Tuttavia, puoi forzare uno scarico dal marketplace per ripristinare ordini mancanti.

---

### F: Come posso esportare i miei dati?

R: Puoi esportare:
- Cataloghi: **Prevendita > Prodotti > Esporta**
- Ordini: **Post Vendita > Ordini > Esporta**
- Report: **Prevendita > Report Inserzioni > Esporta Report**

---

### F: SellRapido ha un'app mobile?

R: Attualmente SellRapido è disponibile solo come applicazione web. È ottimizzato per accesso da browser su tablet per operazioni mobile.

---

### F: Posso automatizzare tutte le mie operazioni?

R: SellRapido offre automatismi per:
- Inoltro ordini a corrieri
- Cambio stato ordini
- Invio email automatiche
- Aggiornamento inventario
- Generazione documenti

Vedi la sezione "Automazione" per dettagli completi.

---

## Risorsi aggiuntivi

- **Status página**: https://status.sellrapido.com/
- **Blog SellRapido**: https://blog.sellrapido.com/
- **Webinar e tutorial**: Disponibili nell'area Supporto
- **Community forum**: Per domande da altri utenti

---

Per ulteriore assistenza, contatta il team di supporto SellRapido attraverso l'Area Ticket.
