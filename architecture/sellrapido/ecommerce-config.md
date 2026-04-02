# E-Commerce Configuration - SellRapido

## Indice

1. [Shopify](#shopify)
2. [Magento](#magento)
3. [PrestaShop](#prestashop)
4. [WooCommerce](#woocommerce)

---

## SHOPIFY

### Prerequisiti

Per configurare Shopify è necessario avere accesso amministratore al back office Shopify.

### Step 1: Configura l'app di SellRapido su Shopify

1. Accedi con le tue credenziali di amministratore ad https://admin.shopify.com
2. Clicca **Impostazioni**, nell'angolo in basso a sinistra della pagina
3. Clicca **APP** nella barra laterale
4. Clicca il pulsante **Sviluppa app** nella Dev Dashboard
5. Clicca sul pulsante **Crea app** che appare nell'angolo in alto a destra
6. Inserisci come App name: **"Sellrapido"**
7. Clicca **Crea**
8. Verrà aperta la sezione Versioni
9. Scorri la pagina e compila i campi seguenti:
   - **Ambiti**: `read_customers,write_customers,write_inventory,read_inventory,write_locations,read_locations,read_merchant_managed_fulfillment_orders,write_merchant_managed_fulfillment_orders,read_orders,write_orders,read_products,write_products,customer_read_orders,customer_write_orders`
   - **URL di reindirizzamento**: `https://app.sellrapido.com/sr_company_ws/marketplace/shopify/settoken`
10. Una volta compilati tutti i campi, clicca su **Rilascia**

### Step 2: Ottieni le chiavi di accesso

1. Nel menu a sinistra clicca **Impostazioni**
2. Copia **Id Client** e **Segreto** (per mostrare il Segreto, premi sull'occhio a sinistra del campo)
3. Salva questi dati in un luogo sicuro

### Step 3: Installa l'APP

1. Nel menu a sinistra clicca **Home**
2. Clicca su **Installa** nella barra dei menu in alto a destra
3. Seleziona il sito Shopify dove vuoi installare l'APP
4. Clicca **Installa**

### Step 4: Collega Shopify a SellRapido

1. Accedi al tuo account SellRapido e vai su **Impostazioni > Credenziali Marketplace > Shopify**
2. Clicca sul segno "+" per aggiungere la credenziale
3. Inserisci i dati del sito:
   - **Sito Web**: indirizzo Shopify completo (incluso http:// o https://), formato: `https://{shop}.myshopify.com`
   - **Segreto**: (dal punto 2 di Step 2)
   - **ID Client**: (dal punto 2 di Step 2)
   - **NON inserire il "Token"**: lasciare il campo vuoto
4. Clicca su **Salva**

### Avvertenze sulla Pubblicazione

#### Gestione dei prodotti e delle inserzioni

Shopify permette al massimo la creazione/modifica/cancellazione di **un prodotto/inserzione al secondo** (teoricamente 2 al secondo, ma con operazioni accessorie si può pubblicare massimo 1 al secondo).

**Conseguenza**: La creazione/modifica di 3.600 inserzioni impiega un'ora.

#### Modifica delle varianti

Shopify permette la pubblicazione/modifica di al massimo **100 varianti al giorno**. Dopo tale limite, viene bloccata qualsiasi pubblicazione fino al giorno successivo.

**Raccomandazione**: Nella maggior parte delle situazioni le varianti non sono utilizzabili. Si consiglia di pubblicare ogni variante come prodotto a sé stante.

#### Tag e categorie-negozio

SellRapido associa le categorie-negozio come "tag" di Shopify. L'utente deve creare smart collection di Shopify sulla base dei tag creati da SellRapido.

**Esempio**: Un prodotto in SellRapido associato alle categorie "Casa", "Cucina", "Stoviglie" verrà pubblicato con tag Shopify "Casa", "Cucina", "Stoviglie".

**Tag Shopify pubblicati**:
1. Categorie negozio della mappa categorie (Impostazioni > Categorie)
2. Brand
3. Valori degli attributi di prodotto (es. "Colore: Bianco" = tag "Bianco")

#### Spese di spedizione

La struttura di Shopify non permette di associare una modalità di spedizione a un singolo prodotto. Le spese di spedizione definite in SellRapido servono unicamente per la definizione del prezzo di vendita finale. Le modalità effettive (e i prezzi) di spedizione saranno decise all'interno di Shopify secondo le regole previste.

---

## MAGENTO

### Prerequisiti

- **Versione**: Magento 2.x (non Magento 1.x)
- **Accesso**: API di Magento con privilegi amministratore

Per il collegamento è necessario un utente con privilegi amministratore. In caso di errori/anomalie è necessario rivolgersi a chi fornisce supporto per la manutenzione del sito.

### Step 1: Ottieni le chiavi di accesso

1. Entra nel tuo account Magento
2. Vai alla sezione **System > All Users**
3. Nella pagina di utente con ruolo amministratore, troverai:
   - **Nome Utente**
   - **Password**

Questi dati dovranno essere inseriti nella credenziale SellRapido.

### Step 2: Collega Magento a SellRapido

1. Accedi al tuo account SellRapido e vai su **Impostazioni > Credenziali Marketplace > Magento**
2. Clicca sul segno "+" per aggiungere la credenziale
3. Inserisci i dati di Magento:
   - **Sito Web**: URL completo, incluso "www" (es. https://www.example.com)
   - **Nome utente**: User amministratore
   - **Password**: Password amministratore
4. Clicca su **Salva**

### Attenzione

Il **titolo di ogni prodotto pubblicato su Magento deve essere univoco**. Si consiglia di prefissare o postfissare sempre il codice SKU nel titolo dell'inserzione. Vedi la guida sulla personalizzazione del titolo per maggiori dettagli.

---

## PRESTASHOP

### Prerequisiti

**Versioni testate**:
- 1.6
- 1.7
- 8.2

**Protezioni da disattivare**: Per poter configurare PrestaShop è necessario disattivare temporaneamente i dispositivi di protezione (es. Cloudflare). In alternativa, contattare SellRapido per ottenere l'indirizzo IP da configurare in Cloudflare affinché il sistema possa effettuare le chiamate REST/HTTP.

### Step 1: Ottieni le chiavi di accesso

1. Accedi al pannello di amministrazione di PrestaShop
2. Vai a **Parametri avanzati > Webservice**
3. Attiva il servizio **Webservice** di PrestaShop
4. Se il server lo richiede, attiva **modalità CGI per PHP** (da chiedere al sistemista del server se necessario)
5. Crea una nuova **chiave API** e associala a **tutti i privilegi**

### Step 2: Collega PrestaShop a SellRapido

1. Accedi al tuo account SellRapido e vai su **Impostazioni > Credenziali Marketplace > PrestaShop**
2. Clicca sul segno "+" per aggiungere la credenziale
3. Inserisci i dati del sito:
   - **Sito Web**: URL completo del sito (es. https://www.example.com)
   - **Chiave API**: (dalla configurazione Webservice)
4. Clicca su **Salva**

---

## WOOCOMMERCE

### Prerequisiti

**Versioni testate**: 3.x fino a 9.6

**Importante**: Possono essere collegati **solo siti https://**

### Versioni dalla 3.4 alla 5.6

#### Ottieni le chiavi di accesso (versioni 3.4-5.6)

1. Entra nel back office di WooCommerce e clicca su **WooCommerce > Impostazioni > Avanzate > API REST > Aggiungi chiave**
2. Inserisci:
   - **Descrizione**: Sellrapido
   - **Utente**: scegli lo user tra quelli disponibili
   - **Permessi**: Leggi/Scrivi
3. Clicca su **Genera chiave API**
4. **Attenzione**: Copia **Chiave Utente** e **Utente nascosto** subito. Se non incolli subito questi dati, dovrai generare nuovamente le chiavi.
5. Vai su **WooCommerce > Impostazioni > Avanzate > API Legacy** e assicurati che sia spuntata **Abilita l'API REST legacy**
6. Vai alla sezione **Plugin** e attiva il plugin **Application Passwords** (necessario per caricare immagini via API)
7. Accedi a **Utenti > Aggiungi Nuovo** e inserisci:
   - **Nome utente**: Sellrapido
   - **Email**
   - **Nome**
   - **Cognome**
   - **Sito web**
   - **Lingua**
   - **Password**
8. Clicca su **Aggiungi nuovo utente**
9. **Attenzione**: Copia la password generata subito. Se non la incolli subito, dovrai generare nuovamente la password.

#### Collega WooCommerce a SellRapido (versioni 3.4-5.6)

1. Accedi al tuo account SellRapido e vai su **Impostazioni > Credenziali Marketplace > Woocommerce**
2. Clicca sul segno "+" per aggiungere la credenziale
3. Inserisci i dati dell'account WooCommerce:
   - **Website**: URL completo del sito (nota: se presente "www", deve rimanere)
   - **Consumer Key**: Chiave utente
   - **Consumer Secret**: Utente nascosto
   - **Username**: Nome utente
   - **Password**: Password
4. Clicca su **Salva**

### Versioni dalla 5.6 alle 9.6

#### Ottieni le chiavi di accesso (versioni 5.6-9.6)

1. Entra nel back office di WooCommerce e vai a **Utenti > Aggiungi** e inserisci:
   - **Ruolo**: "Gestore negozio" oppure "Amministratore"
   - **Nickname**: Sellrapido
   - **Nome pubblico da visualizzare**: Sellrapido
   - **Email**: qualsiasi email
2. Scorri la pagina:
   - Inserisci "Sellrapido" nella sezione **Aggiungi nuova password applicazione**
   - Clicca su **Aggiungi una nuova password dell'applicazione**
   - **Attenzione**: Copia la password generata subito. Se non la incolli subito, dovrai generare nuovamente.
3. Vai su **WooCommerce > Impostazioni > Avanzate > API REST > Aggiungi chiave**
4. Inserisci:
   - **Descrizione**: Sellrapido
   - **Utente**: scegli l'utente creato al punto 1
   - **Permessi**: Leggi/Scrivi
5. Clicca su **Genera chiave API**
6. **Attenzione**: Copia **Chiave Utente** e **Utente nascosto** subito.
7. Vai su **WooCommerce > Impostazioni > Avanzate > API Legacy** e assicurati che sia spuntata **Abilita l'API REST legacy**
   - **NOTA**: Per Woocommerce 9.6 non è necessario (la sezione è stata soppressa)
   - **Nota**: "Application Passwords" non deve essere attivo per questa versione

#### Collega WooCommerce a SellRapido (versioni 5.6-9.6)

1. Accedi al tuo account SellRapido e vai su **Impostazioni > Credenziali Marketplace > Woocommerce**
2. Clicca sul segno "+" per aggiungere la credenziale
3. Inserisci i dati dell'account WooCommerce:
   - **Website**: URL completo del sito (nota: se presente "www", deve rimanere)
   - **Consumer Key**: Chiave utente
   - **Consumer Secret**: Utente nascosto
   - **Nome Utente**: Sellrapido
   - **Password**: Password del punto 2 della sezione Ottieni le chiavi
4. Clicca su **Salva**

---

## Riepilogo E-Commerce

| Piattaforma | Versioni | Requisiti |
|---|---|---|
| Shopify | Tutte | Account admin, https:// |
| Magento | 2.x | Account admin con API access |
| PrestaShop | 1.6, 1.7, 8.2 | Webservice attivo, https:// |
| WooCommerce | 3.x - 9.6 | https://, Account admin |

---

## Punti Critici per Ecommerce

### Shopify
- Limite 1 prodotto/secondo
- Max 100 varianti/giorno
- Spedizione gestita dentro Shopify
- Varianti come tag

### Magento
- Titoli devono essere univoci
- Versione 2.x obbligatoria
- Accesso admin necessario

### PrestaShop
- Webservice deve essere attivo
- Disattivare Cloudflare temporaneamente
- Chiave API con tutti i privilegi

### WooCommerce
- Molto dipendente dalla versione
- https:// obbligatorio
- API REST legacy per versioni precedenti
- Application Passwords per versioni 3.4-5.6
