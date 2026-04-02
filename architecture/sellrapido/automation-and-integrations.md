# Automation and Integrations - SellRapido

## Indice

1. [Overview](#overview)
2. [Automatismi ordini](#automatismi-ordini)
3. [Integrazioni ERP e contabilità](#integrazioni-erp-e-contabilità)
4. [Integrazioni B2BHub](#integrazioni-b2bhub)
5. [Webhooks e callback](#webhooks-e-callback)
6. [Sincronizzazione inventario](#sincronizzazione-inventario)

---

## Overview

SellRapido fornisce diverse soluzioni di automazione per semplificare i flussi di lavoro e ridurre le operazioni manuali. Queste incluono automatismi ordini, integrazioni con sistemi ERP e sincronizzazione inventario.

### Benefici dell'automazione

- Riduzione errori manuali
- Accelerazione tempi di processing
- Sincronizzazione automatica dati
- Riduzione carico lavoro operativo
- Migliore tracciamento e reporting

---

## Automatismi ordini

### Cosa sono gli automatismi ordini

Gli automatismi ordini sono regole che attivano azioni automatiche quando si verifica un evento (es. ricezione ordine, pagamento confermato).

### Automatismi disponibili

#### Automatismo 1: Inoltro automatico a corriere

Quando attivato, gli ordini vengono inoltrati automaticamente al corriere configurato.

**Trigger disponibili**:
- Quando lo stato ordine passa a "Pagato"
- Quando lo stato ordine passa a "Spedito"
- Immediatamente dopo la ricezione dell'ordine

**Configurazione**:

1. Vai a **Impostazioni > Automatismi > Inoltro Corrieri**
2. Seleziona il corriere
3. Abilita **Inoltro Automatico**
4. Scegli il trigger
5. Salva

**Risultato**: Gli ordini vengono inoltrati al corriere senza intervento manuale, il tracking viene recuperato automaticamente.

---

#### Automatismo 2: Cambio stato automatico

Lo stato dell'ordine cambia automaticamente in base a eventi specifici.

**Trigger disponibili**:
- Transizioni di stato predefinite
- Base a regole personalizzate
- Basate su tracking consegna

**Configurazione**:

1. Vai a **Impostazioni > Automatismi > Cambio Stato**
2. Crea una nuova regola di cambio stato
3. Seleziona:
   - **Stato attuale**: Lo stato di partenza
   - **Stato futuro**: Lo stato di destinazione
   - **Trigger**: L'evento che attiva il cambio
4. Salva

---

#### Automatismo 3: Invio email automatica

Email automatica viene inviata quando si verifica un evento specifico.

**Trigger disponibili**:
- Ricezione ordine
- Cambio stato ordine
- Pagamento confermato
- Ordine spedito (con tracking)
- Consegna completata

**Template email disponibili**:
- Conferma ordine
- Notifica pagamento
- Notifica spedizione
- Richiesta feedback
- Notifica consegna

**Configurazione**:

1. Vai a **Impostazioni > Automatismi > Email Automatiche**
2. Clicca **Nuova Email Automatica**
3. Configura:
   - **Nome**: Identificativo dell'automatismo
   - **Trigger**: Evento che attiva l'email
   - **Template**: Quale email inviare
   - **Destinatario**: Cliente, Interno, Entrambi
4. Salva

---

#### Automatismo 4: Aggiornamento inventario

L'inventario su SellRapido viene aggiornato automaticamente in base alle vendite.

**Configurazione**:

1. Vai a **Impostazioni > Automatismi > Aggiornamento Inventario**
2. Abilita **Decrementa quantità alla vendita**
3. Scegli l'opzione:
   - Al ricevimento ordine (consigliato)
   - Al pagamento confermato
   - Manualmente
4. Salva

**Nota**: La sincronizzazione inversa (da SellRapido ai marketplace) avviene una volta l'ora.

---

#### Automatismo 5: Creazione documenti

Fatture e documenti di trasporto vengono creati automaticamente.

**Configurazione**:

1. Vai a **Impostazioni > Automatismi > Generazione Documenti**
2. Abilita **Crea fattura automaticamente**
3. Seleziona il trigger:
   - Quando ordine è Pagato
   - Quando ordine è Spedito
4. Se integrato con software contabile, documenti verranno esportati automaticamente
5. Salva

---

### Disattivare gli automatismi

Se necessario disattivare temporaneamente un automatismo:

1. Vai a **Impostazioni > Automatismi**
2. Seleziona l'automatismo
3. Clicca **Disabilita**
4. Conferma la disattivazione

Gli automatismi disabilitati non eseguiranno azioni ma potranno essere riabilitati in futuro.

---

## Integrazioni ERP e contabilità

### Integrazioni disponibili

SellRapido si integra con i seguenti sistemi ERP e contabili:

| Sistema | Funzionalità | Setup |
|---|---|---|
| **Danea** | Importazione catalogo, esportazione ordini e fatture | Credenziali Danea |
| **Fatture in Cloud** | Sincronizzazione catalogo, esportazione fatture e ordini | API key Fatture in Cloud |
| **Odoo** | Gestione inventario, ordini, fatturazione | API token Odoo |
| **Pastel** | Esportazione ordini e fatture per contabilità | Configurazione FTP/API |

### Integrazione Danea

#### Collegare Danea a SellRapido

1. Vai a **Impostazioni > Integrazioni > Danea**
2. Clicca **Collega Danea**
3. Inserisci le credenziali Danea
4. SellRapido richiederà autorizzazione
5. Clicca **Autorizza** dalla pagina Danea
6. Al ritorno in SellRapido, l'integrazione è attiva

#### Sincronizzazione catalogo da Danea

1. Vai a **Prevendita > Prodotti**
2. Clicca **Nuovo Catalogo**
3. Seleziona **Danea**
4. Scegli il catalogo Danea da importare
5. Configura il mapping campi
6. Clicca **Sincronizza**

La sincronizzazione avviene una volta al giorno automaticamente. Puoi forzare un aggiornamento:

1. Accedi a **Prevendita > Prodotti**
2. Seleziona il catalogo da Danea
3. Clicca **Sincronizza Ora**

---

### Integrazione Fatture in Cloud

#### Collegare Fatture in Cloud

1. Vai a **Impostazioni > Integrazioni > Fatture in Cloud**
2. Clicca **Collega Fatture in Cloud**
3. Inserisci le credenziali di accesso Fatture in Cloud
4. SellRapido richiederà autorizzazione
5. Autorizza l'accesso dalla pagina Fatture in Cloud
6. L'integrazione è attiva

#### Esportazione ordini a Fatture in Cloud

Quando abilitato, gli ordini pagati vengono esportati automaticamente come fatture in Fatture in Cloud:

1. Vai a **Impostazioni > Integrazioni > Fatture in Cloud**
2. Abilita **Esportazione Ordini Automatica**
3. Configura i parametri:
   - **Causale**: Quale causale usare (Vendita, etc.)
   - **Conto**: Account donde registrare i ricavi
   - **IVA**: Aliquota IVA per le vendite
4. Salva

---

### Integrazione Odoo

#### Prerequisiti Odoo

- Odoo versione 12 o superiore
- Modulo "Sales" attivato
- Token API generato

#### Generare token API Odoo

1. Accedi a Odoo
2. Vai a **Impostazioni > Utenti**
3. Seleziona il tuo utente
4. Clicca **Azioni > Crea token API**
5. Copia il token generato

#### Collegare Odoo a SellRapido

1. Vai a **Impostazioni > Integrazioni > Odoo**
2. Clicca **Collega Odoo**
3. Inserisci:
   - **URL Odoo**: URL completo del tuo Odoo
   - **Database**: Nome del database Odoo
   - **Utente**: Username di accesso
   - **Token API**: Token generato sopra
4. Clicca **Test Connessione**
5. Se il test passa, clicca **Salva**

#### Sincronizzazione dati Odoo

Una volta collegato, SellRapido sincronizza automaticamente:

- Catalogo prodotti → Odoo (una volta al giorno)
- Ordini SellRapido → Ordini vendita Odoo (entro 1 ora dalla ricezione)
- Inventario Odoo → SellRapido (una volta al giorno)

---

## Integrazioni B2BHub

### Cosa è B2BHub

B2BHub è una piattaforma di vendita wholesale integrata in SellRapido che consente di vendere a grossisti.

### Attivare B2BHub

1. Vai a **Impostazioni > B2BHub**
2. Clicca **Attiva B2BHub**
3. Configura i parametri:
   - **Listino B2B**: Seleziona il catalogo per la vendita wholesale
   - **Sconti Grossisti**: Imposta sconti percentuali per volumi
   - **Termini pagamento**: giorni pagamento, modalità, etc.
4. Salva

### Gestione grossisti

#### Aggiungere grossisti

1. Vai a **Post Vendita > B2BHub > Grossisti**
2. Clicca **Aggiungi Grossista**
3. Inserisci i dati:
   - **Ragione sociale**
   - **P.IVA**
   - **Email**
   - **Indirizzo**
   - **Condizioni di pagamento**
4. Clicca **Salva**

#### Bloccare/Sbloccare grossisti

1. Vai a **Post Vendita > B2BHub > Grossisti**
2. Seleziona il grossista
3. Clicca **Blocca** o **Sblocca**

Quando un grossista è bloccato, non può più effettuare ordini.

---

### Ordini B2BHub

#### Ricezione ordini B2BHub

Gli ordini B2BHub vengono ricevuti come ordini normali in SellRapido:

1. Accedi a **Post Vendita > Ordini**
2. Filtra per **Fonte: B2BHub**
3. Gli ordini appaiono come ordini regolari ma marcati come "Wholesale"

#### Gestione automatismi B2BHub

Per gli ordini B2BHub puoi configurare automatismi specifici:

1. Vai a **Impostazioni > Automatismi B2BHub**
2. Configura:
   - **Inoltro automatico a corriere**: Invia gli ordini al corriere B2B
   - **Assegnazione automatica**: Assegna gli ordini a team specifici
   - **Email notifica**: Notifica email al grossista

---

### Condizioni pagamento B2BHub

#### Configurare termini pagamento

1. Vai a **Impostazioni > B2BHub > Condizioni Pagamento**
2. Crea una nuova condizione:
   - **Nome**: Es. "Net 30"
   - **Giorni**: 30
   - **Descrizione**: "Pagamento a 30 giorni da ricevimento merce"
3. Salva

#### Assegnare a grossista

1. Seleziona il grossista
2. Clicca **Modifica**
3. Seleziona la condizione di pagamento
4. Salva

---

## Webhooks e callback

### Cosa sono i webhooks

I webhooks sono notifiche HTTP che SellRapido invia a URL esterni quando si verificano specifici eventi. Permettono l'integrazione con sistemi terzi.

### Eventi webhook disponibili

| Evento | Trigger | Payload |
|---|---|---|
| **order.created** | Nuovo ordine ricevuto | Dati ordine completi |
| **order.paid** | Ordine pagato | ID ordine, data pagamento |
| **order.shipped** | Ordine spedito | ID ordine, tracking, corriere |
| **order.delivered** | Ordine consegnato | ID ordine, data consegna |
| **product.updated** | Prodotto modificato | ID prodotto, campi modificati |
| **inventory.updated** | Inventario aggiornato | ID prodotto, quantità nuova |
| **publication.error** | Errore pubblicazione | Dettagli errore, SKU |

### Configurare webhooks

#### Step 1: Preparare l'endpoint

Il tuo endpoint deve:
- Essere raggiungibile via HTTPS (HTTP non supportato)
- Accettare POST requests
- Rispondere con status 200 OK entro 30 secondi
- Processare il JSON payload

Esempio endpoint (Node.js):
```javascript
app.post('/sellrapido-webhook', (req, res) => {
  const event = req.body;
  console.log('Evento ricevuto:', event.type);
  res.status(200).send('OK');
});
```

#### Step 2: Registrare il webhook

1. Vai a **Impostazioni > Webhook**
2. Clicca **Nuovo Webhook**
3. Configura:
   - **URL**: Endpoint dove ricevere i webhook
   - **Evento**: Quale evento ascoltare
   - **Attivo**: Spunta per abilitare
4. Clicca **Salva**

#### Step 3: Test webhook

1. Seleziona il webhook creato
2. Clicca **Test**
3. SellRapido invierà un evento di test
4. Verifica che il tuo endpoint riceva la richiesta

### Payload webhook

Ogni webhook contiene:

```json
{
  "id": "webhook_123456",
  "type": "order.created",
  "timestamp": "2026-04-02T10:30:00Z",
  "data": {
    "order_id": "ORD-2026-00123",
    "customer": "Mario Rossi",
    "email": "mario@example.com",
    "total": 99.99,
    "items": [...]
  }
}
```

### Retry webhook

Se il tuo endpoint non risponde con 200 OK:

- 1° tentativo: Immediato
- 2° tentativo: Dopo 5 minuti
- 3° tentativo: Dopo 30 minuti
- 4° tentativo: Dopo 2 ore
- Dopo il 4° tentativo fallito, il webhook viene disabilitato

Per riabilitare dopo fallimenti:

1. Vai a **Impostazioni > Webhook**
2. Seleziona il webhook disabilitato
3. Clicca **Riabilita**

---

## Sincronizzazione inventario

### Come funziona la sincronizzazione

SellRapido sincronizza l'inventario in entrambe le direzioni:

**SellRapido → Marketplace**: Una volta all'ora
- Le quantità in SellRapido vengono inviate ai marketplace
- Se quantità scende sotto il minimo, l'inserzione viene chiusa

**Marketplace → SellRapido**: Una volta all'ora
- Ordini dai marketplace riducono l'inventario
- Le quantità vengono aggiornate dopo ogni transazione

### Modalità sincronizzazione inventario

#### Modalità 1: Importa da listino

Le quantità vengono importate dal catalogo originale:

1. Vai a **Prevendita > Prodotti > Impostazioni > Quantità**
2. Seleziona **Importa dal Listino**
3. L'inventario si aggiorna ogni ora automaticamente

---

#### Modalità 2: Quantità fissa

Tutti i prodotti mostrano la stessa quantità:

1. Vai a **Prevendita > Prodotti > Impostazioni > Quantità**
2. Seleziona **Quantità Fissa**
3. Inserisci il numero di pezzi da visualizzare
4. Salva

Questa modalità è utile quando vuoi nascondere l'inventario reale.

---

#### Modalità 3: Quantità random

Una quantità casuale all'interno di un range:

1. Vai a **Prevendita > Prodotti > Impostazioni > Quantità**
2. Seleziona **Quantità Random**
3. Inserisci minimo e massimo
4. Salva

Ogni ora, per ogni prodotto, SellRapido genera una quantità casuale tra min e max.

---

### Allineamento inventario forzato

Se si sospetta che l'inventario sia desincronizzato:

1. Vai a **Prevendita > Report Inserzioni**
2. Seleziona il marketplace
3. Clicca **Richiedi Allineamento Inventario**
4. SellRapido richiederà i dati attuali dal marketplace
5. Confronterà con i dati locali
6. Riallineerà automaticamente

Il processo può richiedere 30 minuti - 2 ore a seconda del numero di prodotti.

---

### Quantità minima per mantenere inserzione

È possibile impostare una quantità minima:

1. Vai a **Prevendita > Prodotti > Impostazioni > Filtri**
2. Imposta **Quantità Minima**: Es. 8 pezzi
3. Se la quantità scende sotto il minimo, l'inserzione viene automaticamente chiusa

Questo previene l'overselling.

---

### Sincronizzazione multi-marketplace

Se un prodotto è pubblicato su più marketplace:

1. Una vendita su Amazon riduce l'inventario globale
2. Questa riduzione si riflette su eBay, Carrefour, etc. entro 1 ora
3. Se l'inventario scende sotto il minimo, tutte le inserzioni vengono chiuse simultaneamente

Questo previene la vendita dello stesso prodotto più volte.

---

## Troubleshooting Automazione

### Automatismi non funzionano

**Causa possibile**: Automatismo disabilitato

**Soluzione**:
1. Vai a **Impostazioni > Automatismi**
2. Verifica che l'automatismo sia abilitato
3. Se disabilitato, clicca **Abilita**

---

### Ordini non si sincronizzano con ERP

**Causa possibile**: Integrazione non autenticata

**Soluzione**:
1. Vai a **Impostazioni > Integrazioni**
2. Clicca sul sistema ERP
3. Verifica che la connessione sia attiva
4. Se no, ricollega seguendo la procedura

---

### Inventario non si aggiorna

**Causa possibile**: Sincronizzazione oraria non ancora completata

**Soluzione**:
1. Attendi fino all'ora successiva (max 60 minuti)
2. Se il problema persiste, fai clic a **Prevendita > Report Inserzioni > Richiedi Allineamento Inventario**

---

## Support

Per problemi legati all'automazione e integrazioni:

1. Vai a **Impostazioni > Supporto > Area Ticket**
2. Apri un ticket con categoria appropriata:
   - **B2BHub**: Per problemi grossisti
   - **Corrieri**: Per integrazioni corrieri
   - **Ordini**: Per automatismi ordini
   - Descrivi il problema e i passaggi per riprodurlo
