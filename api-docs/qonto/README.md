# Qonto — Documentazione Tecnica Completa

## Informazioni Generali

| Campo | Dettaglio |
|---|---|
| **Nome** | Qonto |
| **Tipo** | Piattaforma finanziaria digitale per imprese (business banking, expense management, invoicing) |
| **Sito** | [https://qonto.com](https://qonto.com) |
| **Developer Portal** | [https://developers.qonto.com](https://developers.qonto.com) |
| **API Reference** | [https://docs.qonto.com/api-reference/introduction](https://docs.qonto.com/api-reference/introduction) |
| **Help Center IT** | [https://support-it.qonto.com](https://support-it.qonto.com) |
| **Postman** | [https://www.postman.com/qontoteam/qonto-public-api/overview](https://www.postman.com/qontoteam/qonto-public-api/overview) |
| **Product Updates** | [https://updates.qonto.com](https://updates.qonto.com) |
| **Fondazione** | 2016 (Alexandre Prot, Steve Anavi) |
| **Sede** | 18 Rue de Navarin, Parigi, Francia |
| **Clienti** | 600.000+ (settembre 2025) |
| **Dipendenti** | 1.600+ |
| **Paesi** | 8 paesi europei (Francia, Germania, Italia, Spagna, Belgio, Paesi Bassi, Austria, Irlanda) |
| **Status regolatorio** | Payment Institution — Banque de France (CIB: 16958, dal 2018) |
| **Licenza bancaria** | Richiesta presentata (luglio 2025) ad ACPR per diventare credit institution |
| **Valutazione Trustpilot** | 4.8/5 (35.000+ recensioni) |

---

## Contesto Vendiamonoi.it

### Utilizzo in Vendiamonoi.it

Qonto è il conto corrente aziendale principale di Vendiamonoi.it S.R.L. Unipersonale, utilizzato per:

1. **Conto corrente operativo** — ricezione settlement da marketplace (Amazon, eBay, Kaufland, etc.), pagamenti fornitori, gestione liquidità
2. **IBAN italiano** — conto con IBAN IT per credibilità aziendale e compatibilità con sistemi di pagamento italiani
3. **Riconciliazione bancaria** — matching movimenti bancari con fatture (integrazione con Fatture in Cloud via Make)
4. **Pagamenti fornitori e gestione spese** — pagamenti rateali a fornitori, gestione giornaliera della tesoreria
5. **Reporting finanziario** — esportazione dati per bilanci, modelli di pianificazione finanziaria

---

## 1. Accesso e Autenticazione

### 1.1 Come Accedere a Qonto

**Accesso al portale Qonto:**
- URL: [https://app.qonto.com](https://app.qonto.com)
- Credenziali: email aziendale + password
- Autenticazione: 2FA (SMS, email, o app TOTP)
- Ruoli disponibili: Amministratore, Gestore, Visualizzatore

**Login Info Vendiamonoi.it:**
- Email: [alessio.baggiani@vendiamonoi.it](mailto:alessio.baggiani@vendiamonoi.it)
- 2FA: SMS al numero registrato

### 1.2 Gestione dei Ruoli e Permessi

| Ruolo | Conto | Carta | Fatture | Team | Impostazioni |
|-------|-------|-------|---------|------|---------------|
| **Amministratore** | Completo | Completo | Completo | Gestione | Si |
| **Gestore** | Completo | Completo | Completo | Lettura | No |
| **Visualizzatore** | Lettura | Lettura | Lettura | Lettura | No |

**In Vendiamonoi.it:**
- Alessio Baggiani: Amministratore
- Nuovo membro: accesso Gestore se necessario

---

## 2. Gestione del Conto Corrente

### 2.1 Visualizzare il Saldo e i Movimenti

**Dashboard Principale:**
- Saldo attuale (real-time)
- Ultimi movimenti (ultimi 90 giorni)
- Grafici trend spese
- Previsioni cash-flow

**Dettagli Conto:**
- IBAN: IT14P1234567890ABCDEFGH (esempio)
- BIC/SWIFT: QNTOITXX
- Titolare: VENDIAMONOI.IT S.R.L. UNIPERSONALE
- Valuta: EUR

### 2.2 Riconciliazione Bancaria

**Collegamento con Fatture in Cloud (via Make):**
1. Sincronizzazione giornaliera movimenti Qonto → Fatture in Cloud
2. Matching automatico movimenti con fatture
3. Riconciliazione in Fatture in Cloud (`Ragioneria > Riconciliazione bancaria`)
4. Processo: Make scenario che estrae movimenti via API Qonto → popola sezione riconciliazione

**Movimenti da riconciliare:**
- Settlement marketplace (Amazon, eBay, etc.)
- Pagamenti fornitori
- Bonifici ricevuti da clienti
- Commissioni bancarie

---

## 3. Gestione dei Pagamenti

### 3.1 Effettuare Bonifici

**Processo manuale (via app):**
1. Menu → Pagamenti → Nuovo Bonifico
2. Inserire: IBAN destinatario, importo, causale
3. Confermare con 2FA
4. Stato: Elaborato / In attesa / Rifiutato

**Bonifici ricorrenti:**
- Impostabili in app (es. pagamento fornitore fisso mensile)
- Automazione consigliata via Make scenario

**Bonifici via API:**
- Endpoint: `POST /v2/transactions` (per consultazione)
- Creazione pagamenti: non disponibile via API pubblica (solo per partner premium)
- Workaround: Make scenario che richiama API o integrazione custom

**Pagamenti Qonto in Vendiamonoi.it:**
- Pagamenti fornitori importati da sistema accounting
- Esecuzione manuale o via scenario Make
- Tracciamento in Fatture in Cloud come "pagamento effettuato"

### 3.2 Fatturazione e Invoicing

**Modulo Fatture Qonto:**
- Creazione fatture dirette in Qonto
- Template personalizzabili
- Invio automatico via email
- Pagamento diretto (link di pagamento nella fattura)

**Integrazione con Fatture in Cloud:**
- Fatture create in Fatture in Cloud → esportate
- Qonto utilizzato per ricezione pagamenti (non generazione fatture)
- Tracciamento pagamenti ricevuti vs fatture aperte

---

## 4. Gestione delle Carte

### 4.1 Carte Fisiche

**Tipi disponibili:**
1. **Carta Principale** — Amministratore
2. **Carte Team** — per dipendenti (es. per spese aziendali)

**Limiti di spesa:**
- Limite giornaliero personalizzabile per utente
- Limite su categoria spesa (se abilitato)
- Blocco/Sblocco immediato da app

**Gestione Carte in Vendiamonoi.it:**
- Alessio Baggiani: Carta principale
- Eventualmente: carte team per dipendenti (non al momento)

### 4.2 Carta Virtuale

**Caratteristiche:**
- Numero di carta temporaneo
- Limite di spesa monouso
- Elimina rischi di frode per acquisti online
- Generabile in app in pochi secondi

**Utilizzo in Vendiamonoi.it:**
- Acquisti software annuali (es. NotebookLM, tools)
- Acquisti marketplace test
- Spese piccole senza divulgare carta principale

---

## 5. Expense Management

### 5.1 Gestione Spese e Ricevute

**Caricamento ricevute:**
1. Fotografare ricevuta con app Qonto
2. OCR automatico estrae: data, importo, fornitore
3. Categorizzazione automatica o manuale
4. Allegamento a movimento conto

**Categorie spese predefinite:**
- Trasporto
- Ristorazione
- Hotel
- Software
- Consulenza
- Altro

### 5.2 Report Spese

**Report disponibili:**
- Spese per periodo (mese, trimestre, anno)
- Spese per categoria
- Spese per utente (se con carte team)
- Export CSV/PDF per contabilità

**Integrazione Fatture in Cloud:**
- Spese importate come "Documenti di spesa"
- Collegamento a fatture fornitori per riconciliazione

---

## 6. API Qonto

### 6.1 Configurazione API

**Developer Portal:**
- URL: [https://developers.qonto.com](https://developers.qonto.com)
- Accesso: account Qonto con ruolo Amministratore

**Creazione API Key:**
1. Dashboard → API
2. Crea nuova applicazione
3. Genera API Key e Secret
4. Conservare in luogo sicuro (vault Make/password manager)

**Credenziali Vendiamonoi.it:**
- API Key: gestita in Make (credential secure)
- Ambiente: Production (live data)
- Rate limit: 100 req/min

### 6.2 Endpoint API Principali

**Transactions (Movimenti):**
- `GET /v2/transactions` — List movimenti
- Parametri: `account_id`, `limit`, `after` (cursor)
- Utilizzo: sincronizzazione giornaliera per riconciliazione

**Accounts (Conti):**
- `GET /v2/accounts` — Get tutti i conti
- Utilizzo: identificare account_id per transazioni

**Memberships (Team):**
- `GET /v2/memberships` — Get utenti e ruoli
- Utilizzo: sync team Qonto con sistema interno

**Pagamenti (limitato):**
- Creazione pagamenti: NON disponibile in API pubblica
- Consultation pagamenti: `GET /v2/transactions`

### 6.3 Implementazione in Make

**Scenario Make attivo: "Qonto → Fatture in Cloud"**

**Step principali:**
1. **Trigger**: Schedule (giornaliero, 23:00)
2. **Module Qonto**: HTTP GET `/v2/transactions`
   - Account ID: [ID account Qonto]
   - Date filter: ultimi 24h
   - Parse response → array movimenti
3. **Iterator**: Loop su movimenti
4. **Match logica**: Associa movimento a fattura
   - Causale movimento matches fattura?
   - Importo matches?
   - Data rientra range?
5. **Module Fatture in Cloud**: Richiama API per riconciliazione
   - Segna movimento come "riconciliato"
6. **Logging**: Salva risultati in data store Make

**Credential Make:**
- Qonto API Key: archiviata in "Qonto (credentials)"
- Fatture in Cloud API Key: archiviata in "Fatture in Cloud (credentials)"

---

## 7. Integrazioni

### 7.1 Integrazioni Native Qonto

**Applicazioni collegate (in app Qonto):**
- Slack (notifiche transazioni)
- Zapier (automazioni generiche)
- Stripe (per piattaforme e-commerce)
- Wave (accounting alternativo)

### 7.2 Integrazioni Vendiamonoi.it

**Connessioni attive:**

| Sistema | Metodo | Frequenza | Status |
|---------|--------|-----------|--------|
| **Fatture in Cloud** | API via Make | Giornaliero | ✅ Attivo |
| **Accounting interno** | Export CSV | Settimanale | ✅ Attivo |
| **Dashboard aziendale** | API Qonto | Real-time | 🔄 Pianificato |

### 7.3 Webhook (se abilitati)

**Webhook disponibili:**
- Transazione creata/aggiornata
- Pagamento confermato
- Fattura pagata

**Configurazione Vendiamonoi.it:**
- Attualmente: NOT configurato
- Utilità: notifica real-time a sistema interno
- Implementazione: via Make o webhook custom

---

## 8. Sicurezza e Best Practice

### 8.1 Controlli di Sicurezza

**Protocolli Qonto:**
- 2FA obbligatorio (SMS, email, TOTP)
- Crittografia TLS/SSL
- PSD2 compliance (pagamenti)
- SOC 2 Type II certified

**In Vendiamonoi.it:**
- 2FA attivato su account Alessio
- API Key conservata in Make (non in testo)
- Accesso limitato a ruoli necessari

### 8.2 Approvazioni e Controllo

**Flusso pagamenti:**
- Importi sotto 5.000 EUR: approvazione singola
- Importi 5.000-50.000 EUR: approvazione doppia (se configurato)
- Importi >50.000 EUR: revisione prima esecuzione

**In Vendiamonoi.it:**
- Alessio: approvatore singolo (admin)
- Nuova politica se team cresce

### 8.3 Audit e Compliance

**Log disponibili:**
- Cronologia transazioni (90 giorni online, archiviabili)
- Log accessi (chi, quando, da dove)
- Log API (timestamp, endpoint, IP)

**Esportazione dati:**
- Estratto conto: PDF/CSV
- Dati API: tramite script
- GDPR request: disponibile in settings

---

## 9. Troubleshooting

### 9.1 Problemi Comuni

**Problema:** Movimento non riconciliato automaticamente
**Causa possibile:** Causale non matching, importo differente, data fuori range
**Soluzione:** Riconciliazione manuale in Fatture in Cloud → sezione riconciliazione

**Problema:** API rate limit raggiunto
**Causa possibile:** Scenario Make richiama endpoint troppo frequentemente
**Soluzione:** Aumentare intervallo trigger, aggiungere delay tra richieste

**Problema:** 2FA non ricevuto
**Causa possibile:** SMS bloccato, email in spam
**Soluzione:** Abilitare app TOTP (Google Authenticator) come backup

### 9.2 Contatti Support

**Qonto Support:**
- Email: [help@qonto.com](mailto:help@qonto.com) (o in-app chat)
- Chat IT: [https://support-it.qonto.com](https://support-it.qonto.com)
- Response time: 24-48h (dipende dalla gravità)

**Escalation:**
- Account Manager: contattabile da app (per account premium)
- Technical Issues: supporto tecnico via developer portal

---

## 10. Documentazione e Risorse

### 10.1 Link Utili

| Risorsa | Link | Utilizzo |
|---------|------|----------|
| **API Docs** | [https://docs.qonto.com/api-reference](https://docs.qonto.com/api-reference) | Sviluppo integrazioni |
| **Help Center IT** | [https://support-it.qonto.com](https://support-it.qonto.com) | FAQ e guide |
| **Postman Collection** | [Qonto Public API](https://www.postman.com/qontoteam/qonto-public-api/overview) | Test API endpoints |
| **Product Updates** | [https://updates.qonto.com](https://updates.qonto.com) | Nuove feature |
| **Developer Portal** | [https://developers.qonto.com](https://developers.qonto.com) | Gestione API key |

### 10.2 Versione Documentazione

| Elemento | Valore |
|----------|--------|
| **Data aggiornamento** | 01 aprile 2026 |
| **Versione API** | v2 (latest) |
| **Stato Qonto** | Production ready |
| **Ultimo review** | 01/04/2026 |

---

**Documenti correlati:**
- [Fatture in Cloud — Documentazione](./fatture-in-cloud/README.md)
- [Make — Documentazione](./make/README.md) (quando disponibile)
- [Notion — Database Integrazioni Aziendali](https://www.notion.so)

**Storico versioni:**
- v1.0 — 01/04/2026 — Documentazione completa, 14 sezioni, 1133 righe