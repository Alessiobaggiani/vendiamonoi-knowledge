# Superhuman — Documentazione Tecnica Completa

> **Azienda:** Vendiamonoi.it S.R.L. Unipersonale
> **Reparto:** Tech / Direzione / Customer Service / Marketplace
> **Responsabile documentazione:** Alessio Baggiani
> **Data creazione:** 31/03/2026
> **Ultima revisione:** 31/03/2026
> **Status:** ✅ Attivo

---

## Indice

1. [Panoramica](#1-panoramica)
2. [Storia e Acquisizione Grammarly](#2-storia-e-acquisizione-grammarly)
3. [Superhuman Suite — Prodotti](#3-superhuman-suite--prodotti)
4. [Piani e Pricing](#4-piani-e-pricing)
5. [Superhuman Mail — Funzionalità Core](#5-superhuman-mail--funzionalità-core)
6. [Funzionalità AI](#6-funzionalità-ai)
7. [Keyboard Shortcuts](#7-keyboard-shortcuts)
8. [Team & Collaboration](#8-team--collaboration)
9. [Integrazioni CRM](#9-integrazioni-crm)
10. [Superhuman Go — AI Assistant](#10-superhuman-go--ai-assistant)
11. [Sicurezza e Compliance](#11-sicurezza-e-compliance)
12. [Enterprise Features](#12-enterprise-features)
13. [Piattaforme Supportate](#13-piattaforme-supportate)
14. [API e Automazioni](#14-api-e-automazioni)
15. [Use Case per Vendiamonoi.it](#15-use-case-per-vendiamonoit)
16. [Integrazioni con lo Stack Vendiamonoi](#16-integrazioni-con-lo-stack-vendiamonoi)
17. [Limitazioni](#17-limitazioni)
18. [Risorse e Link Utili](#18-risorse-e-link-utili)
19. [Changelog Documentazione](#19-changelog-documentazione)

---

## 1. Panoramica

**Superhuman** è un client email AI-native progettato per massimizzare la produttività nella gestione delle email. Originariamente fondato come email client premium per Gmail, nel luglio 2025 è stato acquisito da Grammarly. A ottobre 2025, Grammarly ha completato il rebranding dell'intera azienda sotto il nome **Superhuman**, creando la **Superhuman Suite** che integra:

- **Superhuman Mail** — Client email AI-native
- **Grammarly** — Writing AI assistant
- **Coda** — Workspace collaborativo
- **Superhuman Go** — AI assistant cross-platform

### Metriche di impatto dichiarate

| Metrica | Valore |
|---------|--------|
| Risparmio tempo settimanale | 4 ore |
| Velocità di risposta | 12 ore più veloce |
| Throughput email | 2.35x |
| Keyboard shortcuts disponibili | 100+ |

### Provider email supportati

- **Gmail** (Google Workspace incluso)
- **Microsoft Outlook** (dal maggio 2022)
- **NON supportati:** IMAP generico, iCloud Mail, Yahoo Mail, ProtonMail

---

## 2. Storia e Acquisizione Grammarly

### Timeline

| Data | Evento |
|------|--------|
| 2014 | Fondazione Superhuman Inc. da Rahul Vohra |
| 2017 | Lancio versione beta (solo Gmail, solo Mac) |
| 2019 | Apertura accesso pubblico |
| 2022 maggio | Supporto Microsoft Outlook |
| 2023 | Lancio funzionalità AI (Write with AI, Instant Reply) |
| 2025 luglio | Grammarly acquisisce Superhuman |
| 2025 ottobre | Grammarly si rinomina "Superhuman", lancia Superhuman Suite e Superhuman Go |
| 2025 ottobre | Auto Drafts e Auto Labels rilasciati |
| 2026 febbraio | Superhuman Go espande agent ecosystem (Box, Gamma, Wayground) |

### Implicazioni dell'acquisizione per Vendiamonoi

- **Pricing consolidato**: I piani ora sono parte della Superhuman Suite (include Grammarly + Coda + Mail + Go)
- **AI potenziata**: L'infrastruttura AI di Grammarly rafforza le capacità di writing e automazione email
- **Coda integrato**: Workspace collaborativo disponibile nello stesso abbonamento — potenziale per gestione documenti e workflow
- **Superhuman Go**: AI assistant che funziona su 1M+ app e siti web, con agent ecosystem

---

## 3. Superhuman Suite — Prodotti

### 3.1 Superhuman Mail
Client email AI-native. Core product per la gestione email ad alta velocità.

### 3.2 Grammarly (Writing AI)
Assistente di scrittura AI integrato ovunque: email, documenti, browser. Corregge grammatica, tono, chiarezza e stile.

### 3.3 Coda
Workspace all-in-one che combina documenti, spreadsheet e app. Post-acquisizione, i docs Coda sincronizzano dati da altre app e creano single source of truth. Funzionalità in arrivo: processing automatico di meeting notes in action items con assegnazione automatica.

### 3.4 Superhuman Go
AI assistant cross-platform che funziona su oltre 1 milione di app e siti web. Orchestration layer per agenti specializzati. Dettagli nella [Sezione 10](#10-superhuman-go--ai-assistant).

---

## 4. Piani e Pricing

### Struttura pricing (post-acquisizione Grammarly, Q4 2025)

| Piano | Prezzo Mensile | Prezzo Annuale | Note |
|-------|---------------|----------------|------|
| **Free** | $0 | $0 | Solo Grammarly writing AI + Coda. **NO** Superhuman Mail |
| **Pro** | — | — | Grammarly writing AI avanzata + Coda. **NO** Superhuman Mail |
| **Starter** | $30/mese | $300/anno ($25/mese) | Superhuman Mail core + Grammarly + Coda |
| **Business** | $40/mese | $396/anno ($33/mese) | Tutto Starter + AI avanzata + CRM integrations |
| **Enterprise** | Custom | Custom | Tutto Business + SSO/SAML + SCIM + Admin console + SLA dedicato |

### Cosa include ogni piano Mail

#### Starter ($30/mese)
- Split Inbox
- Scheduled Send
- Read Statuses
- Snippets (template email)
- Reminders & Follow-up
- Undo Send
- 100+ keyboard shortcuts
- Calendar integration (mobile)
- Superhuman Go (base)
- Grammarly writing AI
- Coda workspace

#### Business ($40/mese) — Raccomandato per Vendiamonoi
Tutto il piano Starter, più:
- **Auto Drafts** — AI genera bozze automatiche di follow-up
- **Auto Labels** — Classificazione automatica email in categorie
- **Auto Archive** — Archiviazione automatica email marketing/cold
- **Ask AI** — Assistente AI che cerca nel inbox, calendario e web
- **Instant Reply** — Risposte rapide AI
- **Write with AI** — Composizione email completa con AI
- **CRM integrations** — HubSpot, Salesforce, Pipedrive
- **Custom Auto Labels** — Etichette personalizzate
- **Team analytics** avanzate

#### Enterprise (Custom)
Tutto il piano Business, più:
- **SAML SSO** — Single Sign-On con Okta, Azure AD, Google
- **SCIM** — Provisioning/deprovisioning automatico utenti
- **MDM** — Mobile Device Management
- **DLP** — Data Loss Prevention
- **Admin console** centralizzata
- **SLA dedicato**
- **Account manager dedicato**
- **Priority support**

### Costo stimato per Vendiamonoi

| Configurazione | Utenti | Piano | Costo Mensile | Costo Annuale |
|---------------|--------|-------|---------------|---------------|
| Solo Alessio | 1 | Business | $40 | $396 |
| Team direzione + CS | 3 | Business | $120 | $1,188 |
| Team allargato | 5 | Business | $200 | $1,980 |

---

## 5. Superhuman Mail — Funzionalità Core

### 5.1 Split Inbox
Divide automaticamente la inbox in sezioni separate basate su filtri Gmail/Outlook. Esempio configurazione per Vendiamonoi:

| Split | Filtro | Scopo |
|-------|--------|-------|
| **VIP Fornitori** | Da: lista email fornitori chiave | Comunicazioni fornitori prioritarie |
| **Marketplace** | Da: *@amazon.*, *@ebay.*, *@kaufland.* | Notifiche marketplace |
| **Clienti** | Subject contiene "ordine", "spedizione", "reso" | Customer service |
| **Team** | Da: @vendiamonoi.it | Comunicazioni interne |
| **Newsletter** | Label: newsletter OR category:promotions | Newsletter e promozioni |

### 5.2 Snippets (Template Email)
Template riutilizzabili attivabili con `⌘ + ;` (Mac) o `Ctrl + ;` (Windows).

**Esempi per Vendiamonoi:**

| Snippet | Shortcut | Contenuto |
|---------|----------|-----------|
| `//fornitore-onboarding` | ⌘; | Email standard per nuovo fornitore con checklist documenti |
| `//marketplace-issue` | ⌘; | Template per segnalazione problema marketplace |
| `//ordine-conferma` | ⌘; | Conferma ordine standard con tracking |
| `//reso-autorizzazione` | ⌘; | Autorizzazione reso con istruzioni |
| `//pagamento-sollecito` | ⌘; | Sollecito pagamento fornitori |
| `//firma-alessio` | ⌘; | Firma email completa Alessio |

### 5.3 Read Statuses (Conferme di Lettura)
Tracking avanzato che mostra:
- **Quando** il destinatario ha aperto l'email
- **Su quale dispositivo** (laptop, mobile, tablet)
- **Quante volte** l'email è stata letta
- **Team Read Statuses**: se un collega manda un'email con te in CC, vedi quando il destinatario la legge

**Uso per Vendiamonoi:**
- Monitorare se i fornitori leggono le proposte commerciali
- Verificare se i marketplace hanno letto le contestazioni
- Sapere quando fare follow-up (se letto ma non risposto → follow-up mirato)

### 5.4 Reminders & Auto Reminders
- **Remind me**: Imposta un promemoria per qualsiasi email (H = Set Reminder)
- **Auto Reminders**: Se il destinatario non risponde entro X giorni (1-7), l'email riappare automaticamente nella inbox
- **Snooze**: Rimanda email a data/ora specifica (Ctrl+H)

### 5.5 Scheduled Send
Programma l'invio email a data e ora specifica. Utile per:
- Inviare email a fornitori europei negli orari lavorativi locali
- Programmare follow-up sequenziali
- Gestire fusi orari diversi (marketplace EU)

### 5.6 Undo Send
Finestra di annullamento invio configurabile (default: pochi secondi dopo l'invio).

### 5.7 Calendar Integration
Integrazione calendario nativa su mobile (iOS/Android):
- Visualizzazione eventi del giorno
- Scheduling meeting direttamente dall'email
- Con Auto Drafts: quando qualcuno chiede un meeting, AI propone automaticamente slot disponibili

### 5.8 Inbox Zero Workflow
Superhuman è progettato attorno al concetto di **Inbox Zero**:
1. **E** = Mark Done (archivia)
2. **H** = Set Reminder (snooze per dopo)
3. **R** = Reply
4. **Del** = Delete
5. **Cmd+K** = Superhuman Command (ricerca e azione rapida)

---

## 6. Funzionalità AI

### 6.1 Write with AI
Composizione email completa basata su AI:
- Scrivi poche frasi/keyword → AI genera email completa
- Mantiene il tuo **tono e stile** personale (apprende dalle email precedenti)
- Supporto **voice-to-email**: tocca icona microfono → parla → email generata
- Funziona con: nuove email, risposte, forward

**Esempio Vendiamonoi:**
```
Input: "conferma ricezione ordine 5000 unità, spedizione prevista 15 aprile, chiedere conferma indirizzo magazzino"
Output AI: Email completa e professionale con tutti i dettagli, nel tono abituale di Alessio
```

### 6.2 Instant Reply
Genera risposte rapide contestuali:
- Analizza il thread completo della conversazione
- Propone 2-3 opzioni di risposta
- Velocità 2x rispetto alla scrittura manuale
- **Piano:** Business e superiori

### 6.3 Auto Drafts (Ottobre 2025)
Feature rivoluzionaria — AI genera automaticamente bozze di follow-up **senza intervento umano**:
- Analizza il contesto del thread
- Comprende la cronologia della conversazione
- Genera risposte complete nel tuo stile di scrittura
- Le bozze appaiono pronte per revisione, modifica e invio
- **Per scheduling**: quando qualcuno chiede un meeting, genera automaticamente una risposta con slot disponibili e booking link
- **Piano:** Business e superiori

### 6.4 Auto Labels (Ottobre 2025)
Classificazione automatica AI di ogni email in arrivo:

| Categoria | Descrizione | Azione suggerita |
|-----------|-------------|-----------------|
| **Response Needed** | Email che richiedono risposta | Priorità alta |
| **Waiting On** | In attesa di risposta da altri | Monitorare |
| **Meetings** | Inviti, conferme, scheduling | Calendar |
| **Marketing** | Newsletter, promozioni | Auto-archive opzionale |
| **Cold Pitches** | Email commerciali non richieste | Auto-archive |
| **Social** | Notifiche social, community | Bassa priorità |
| **Custom Labels** | Etichette personalizzate (Business plan) | Configurabile |

**Auto Archive**: Le email categorizzate come Marketing e Cold Pitches vengono automaticamente spostate da "Important" a "Other" e archiviate.

### 6.5 Ask AI
Assistente AI conversazionale che cerca informazioni attraverso:
- **Inbox** completa
- **Calendario**
- **Web** (ricerca esterna)

**Esempi di query:**
- "Dove si tiene l'offsite?" → cerca nelle email e restituisce location
- "Qual è il numero di tracking dell'ultimo ordine da [fornitore]?" → cerca email recenti
- "Rispondi con 3 ristoranti vicino all'ufficio" → cerca sul web e genera risposta

### 6.6 AI Summary
Riassunti automatici di thread email lunghi:
- Condensamento di conversazioni multi-messaggio
- Evidenziazione dei punti chiave e action items
- Visibile in cima al thread

---

## 7. Keyboard Shortcuts

Superhuman offre **100+ keyboard shortcuts** che coprono ogni azione. I più importanti:

### Navigazione

| Shortcut | Azione |
|----------|--------|
| `J` / `K` | Email successiva / precedente |
| `G → I` | Vai a Inbox |
| `G → S` | Vai a Starred |
| `G → D` | Vai a Drafts |
| `G → T` | Vai a Sent |
| `G → E` | Vai a Done (Archive) |
| `G → L` | Vai a Label/Split specifico |

### Azioni Email

| Shortcut | Azione |
|----------|--------|
| `E` | Mark Done (Archive) |
| `R` | Reply |
| `A` | Reply All |
| `F` | Forward |
| `C` | Compose |
| `Del` | Delete |
| `H` | Set Reminder |
| `Ctrl + H` | Snooze |
| `⌘ + Shift + U` | Mark Unread |
| `L` | Add Label |
| `V` | Move to folder |
| `!` | Report Spam |

### AI & Produttività

| Shortcut | Azione |
|----------|--------|
| `⌘ + K` | Superhuman Command (search + azioni) |
| `⌘ + ;` | Inserisci Snippet |
| `⌘ + J` | Write with AI |
| `⌘ + Shift + L` | Scheduled Send |
| `/` | Search |

### Formatting

| Shortcut | Azione |
|----------|--------|
| `⌘ + B` | Bold |
| `⌘ + I` | Italic |
| `⌘ + Shift + 7` | Numbered list |
| `⌘ + Shift + 8` | Bulleted list |
| `⌘ + Shift + .` | Quote |

---

## 8. Team & Collaboration

### 8.1 Shared Conversations
Condivisione completa della cronologia di una conversazione email con qualsiasi membro del team:
- Il destinatario riceve la storia completa del thread
- Riceve aggiornamenti futuri automaticamente
- **NON serve** essere in To, Cc o Bcc
- Funziona anche con persone che non usano Superhuman

### 8.2 Team Comments
Commenti interni su email visibili solo al team:
- Commenta direttamente su qualsiasi email
- Menziona colleghi con @
- I commenti sono privati (il destinatario esterno non li vede)
- Utile per coordinamento interno su comunicazioni con fornitori/marketplace

### 8.3 Team Reply Indicators
Indicatori in tempo reale che mostrano:
- Quando un collega sta scrivendo una risposta alla stessa email
- Quando un collega ha programmato l'invio di una risposta
- **Previene**: reply duplicati, confusione nella comunicazione con fornitori

### 8.4 Team Read Statuses
Estensione dei Read Statuses per il team:
- Se un collega manda un'email con te in CC, vedi quando il destinatario la legge
- Visibilità condivisa su chi ha letto cosa

### 8.5 Team Insights (Analytics)
Dashboard analytics per il team:
- Tempo medio di risposta per utente
- Volume email gestite
- Trend di produttività
- Confronto team

---

## 9. Integrazioni CRM

### 9.1 Salesforce Integration
Disponibile nel piano **Business** e superiori:
- **Sidebar CRM**: Visualizza dati Salesforce direttamente in Superhuman senza cambiare app
- **Contact details**: Nome, azienda, ruolo, deal in corso
- **Activity logging**: Le email vengono automaticamente loggate in Salesforce
- **Deal context**: Visualizza pipeline, stage, valore deal
- **Risparmio**: 3-4 minuti per email (evita switch tra app)

### 9.2 HubSpot Integration
Disponibile nel piano **Business** e superiori:
- **Contact & Company info**: Visualizza dati HubSpot nella sidebar
- **Deal pipeline**: Stage, valore, probabilità di chiusura
- **Activity sync**: Email automaticamente tracciate come attività in HubSpot
- **Timeline view**: Cronologia interazioni con il contatto
- Registrato nell'[HubSpot Marketplace](https://ecosystem.hubspot.com/marketplace/apps/superhuman-2529077)

### 9.3 Pipedrive Integration
Disponibile nel piano **Business** e superiori:
- **Deal tracking**: Visualizza deal associati al contatto
- **Contact details**: Info contatto e organizzazione
- **Activity logging**: Sync automatico email → Pipedrive

### 9.4 Copper Integration
Integrazione nativa che automaticamente traccia e logga email inviate da Superhuman all'interno di Copper CRM.

---

## 10. Superhuman Go — AI Assistant

### 10.1 Cos'è Superhuman Go
Superhuman Go è un **AI assistant cross-platform** che funziona su oltre 1 milione di app e siti web. È un layer di orchestrazione che coordina agenti specializzati per:
- Redigere email
- Pianificare meeting
- Richiamare informazioni istantaneamente
- Operare su qualsiasi app web-based

### 10.2 Come funziona
Superhuman Go funziona come layer intelligente sopra le applicazioni web:
1. **Comprende il contesto** della pagina/app corrente
2. **Orchestrates agents** specializzati per eseguire azioni
3. **Proattivo**: suggerisce azioni basate sul contesto senza prompt manuali

### 10.3 Agent Ecosystem
Go opera attraverso un **Agent Store** con tre categorie:

#### Connector Agents (integrazione dati)
Portano contesto real-time dagli strumenti esistenti:
- **Google Workspace** (Gmail, Docs, Sheets, Calendar)
- **Microsoft Outlook**
- **Atlassian Jira** (ticket tracking)
- **Atlassian Confluence** (documentation)
- **Box** (document repository) — aggiunto febbraio 2026

#### Partner Agents (funzionalità estese)
- **Gamma** — Creazione contenuti visivi e presentazioni
- **Wayground** — Learning interattivo
- **Box** — Gestione documenti: crea, cerca, riassumi file Box

#### Grammarly Writing Agents
- Correzione grammaticale e stilistica
- Adattamento tono e voce
- Traduzione e localizzazione

### 10.4 Disponibilità
- **Browser extension** per Chrome e Edge ✅
- **Mac e Windows**: in arrivo (roadmap 2026)
- **Incluso** in tutti i piani Superhuman Suite

---

## 11. Sicurezza e Compliance

### 11.1 Certificazioni

| Certificazione | Status |
|---------------|--------|
| **SOC 2 Type 2** | ✅ Certificato |
| **ISO 27001** | ✅ Certificato |
| **GDPR** | ✅ Compliant |
| **CCPA** | ✅ Compliant |

### 11.2 Encryption
- **At rest**: tutti i dati criptati
- **In transit**: TLS/SSL
- **Application-level**: dati particolarmente sensibili con encryption aggiuntiva a livello applicativo

### 11.3 Architettura di Sicurezza
- **Infrastruttura**: Completamente gestita su Google Cloud
- **Data centers**: SOC 2 Type 2 compliant, localizzati negli USA
- **OAuth-only**: Nessuna password memorizzata — solo token OAuth per Gmail/Outlook
- **Principle of least privilege**: Servizi e utenti ricevono i permessi minimi necessari
- **Trust Center**: [trust.superhuman.com](https://trust.superhuman.com/) — SOC 2 report, Security Whitepaper, Pen test

### 11.4 Privacy
- **Solo tu e Google/Microsoft** potete leggere le email
- **Nessuna vendita dati** a terze parti
- **Nessun targeting pubblicitario** basato sul contenuto email
- **DPA disponibile**: [superhuman.com/dpa](https://superhuman.com/dpa)

---

## 12. Enterprise Features

### 12.1 Admin Console
Dashboard centralizzata per gestire:
- Account utenti (50-5000+)
- Permessi e ruoli
- Billing consolidato
- Policy di sicurezza

### 12.2 SSO (Single Sign-On)
Protocolli supportati:
- **SAML** — Per provider enterprise (Okta, Azure AD, OneLogin)
- **Google SSO** — Login con account Google Workspace
- **Microsoft SSO** — Login con account Microsoft 365

### 12.3 SCIM (User Provisioning)
Provisioning automatico utenti:
- **Create**: Nuovo utente in directory aziendale → automaticamente creato in Superhuman
- **Update**: Cambio ruolo/reparto → aggiornato in Superhuman
- **Deactivate**: Utente disabilitato → accesso Superhuman revocato
- **Supporto provider**: Okta, Azure AD, OneLogin, JumpCloud

### 12.4 MDM & DLP
- **MDM (Mobile Device Management)**: Gestione dispositivi mobili aziendali con Superhuman
- **DLP (Data Loss Prevention)**: Protezione contro la fuoriuscita di dati sensibili

---

## 13. Piattaforme Supportate

| Piattaforma | App | Note |
|-------------|-----|------|
| **macOS** | App nativa | Esperienza completa |
| **Windows** | App nativa | Esperienza completa |
| **iOS** | App nativa | Calendar integration nativa |
| **Android** | App nativa | Calendar integration nativa |
| **Web** | Browser-based | Accessibile da qualsiasi browser |
| **Chrome** | Extension (Go) | Superhuman Go AI assistant |
| **Edge** | Extension (Go) | Superhuman Go AI assistant |

---

## 14. API e Automazioni

### 14.1 Superhuman API
Superhuman fornisce una API documentata con le seguenti componenti:

| Componente | Descrizione |
|-----------|-------------|
| **REST API** | Endpoint per operazioni programmatiche |
| **Webhooks** | Notifiche push per eventi email |
| **OAuth** | Autenticazione per integrazioni third-party |
| **OpenAPI/Swagger** | Specifiche API in formato standard |
| **Postman Collection** | Collezione pronta per testing |
| **Sandbox** | Ambiente di test |

**Risorse API:**
- **API Tracker**: [apitracker.io/a/superhuman](https://apitracker.io/a/superhuman)
- **GitHub**: [github.com/superhuman](https://github.com/superhuman)

### 14.2 Automazioni Indirette (via Gmail/Outlook)
Poiché Superhuman opera sopra Gmail/Outlook, le automazioni possono sfruttare le API native dei provider:

#### Via Gmail API
```
Trigger: Email ricevuta in Gmail → Label applicata → Webhook → Make.com/Zapier
Risultato: L'email gestita in Superhuman triggerà automazioni via Gmail API
```

#### Via Microsoft Graph API
```
Trigger: Email ricevuta in Outlook → Folder/Category → Webhook → Make.com/Zapier
Risultato: L'email gestita in Superhuman triggerà automazioni via Graph API
```

### 14.3 Integrazione con Make.com (Indiretta)
Superhuman non ha un modulo Make nativo. Le automazioni passano attraverso:

**Flusso consigliato:**
```
[Superhuman Mail] → [Gmail/Outlook] → [Gmail/Outlook trigger in Make] → [Automazione]
```

**Esempio scenario Make per Vendiamonoi:**
```
1. Trigger: Gmail - Watch Emails (label: "Fornitore")
2. Filter: Subject contiene "ordine" OR "conferma" OR "spedizione"
3. Router:
   a. Ordine nuovo → Crea record in Google Sheets + Notifica Bitrix24
   b. Conferma spedizione → Aggiorna tracking in ChannelEngine
   c. Problema/reclamo → Crea task in ClickUp + Alert Slack
```

### 14.4 Integrazione con Zapier
Zapier è elencato tra gli strumenti che si integrano con Superhuman. Le automazioni possono combinare:
- Trigger email (New Email, New Attachment)
- Filtri avanzati
- Azioni su 5000+ app

### 14.5 Google Apps Script
Per automazioni avanzate basate su Gmail:
```javascript
// Esempio: Monitoraggio email fornitori e logging su Google Sheets
function processSupplierEmails() {
  const threads = GmailApp.search('label:fornitori newer_than:1d');
  const sheet = SpreadsheetApp.openById('SHEET_ID').getActiveSheet();

  threads.forEach(thread => {
    const messages = thread.getMessages();
    const lastMessage = messages[messages.length - 1];

    sheet.appendRow([
      new Date(),
      lastMessage.getFrom(),
      lastMessage.getSubject(),
      lastMessage.getPlainBody().substring(0, 200),
      'Da processare'
    ]);
  });
}
```

---

## 15. Use Case per Vendiamonoi.it

### Use Case 1: Gestione Comunicazioni Fornitori (Direzione)
**Problema:** Alessio gestisce 100+ fornitori via email. Le comunicazioni si perdono, i follow-up vengono dimenticati.

**Soluzione Superhuman:**
1. **Split Inbox "VIP Fornitori"**: Email fornitori separate e prioritizzate
2. **Auto Reminders**: Se un fornitore non risponde entro 3 giorni → email riappare
3. **Read Statuses**: Verifica se il fornitore ha letto la proposta → decide se richiamare
4. **Snippets**: Template per onboarding nuovi fornitori, richieste catalogo, solleciti pagamento
5. **Auto Drafts (AI)**: Genera automaticamente bozze di follow-up per proposte inviate

**Impatto stimato:** -60% tempo gestione email fornitori, 0 follow-up dimenticati

### Use Case 2: Customer Service Marketplace (CS Team)
**Problema:** Il team CS riceve email da 30+ marketplace. Gestire le priorità e i tempi di risposta è critico per mantenere i KPI marketplace.

**Soluzione Superhuman:**
1. **Split Inbox per marketplace**: Amazon, eBay, Kaufland, Carrefour, etc.
2. **Auto Labels**: Classificazione automatica (resi, spedizioni, reclami, domande prodotto)
3. **Instant Reply (AI)**: Risposte rapide per casi standard
4. **Snippets marketplace-specific**: Template per ogni tipo di risposta per marketplace
5. **Team Comments**: CS team commenta internamente prima di rispondere

**Impatto stimato:** -40% tempo risposta, +95% tasso risposta entro SLA

### Use Case 3: Sales & Business Development (Direzione)
**Problema:** Proposte commerciali a nuovi marketplace, partner, fornitori richiedono follow-up sistematici.

**Soluzione Superhuman:**
1. **Write with AI**: Genera email commerciali professionali da pochi bullet point
2. **Scheduled Send**: Invio in orari ottimali per timezone destinatario
3. **Read Statuses**: Monitora apertura proposte → follow-up mirato
4. **Ask AI**: "Qual era il prezzo che avevamo proposto a [fornitore]?" → cerca nel inbox

**Impatto stimato:** +30% conversion rate proposte, -50% tempo redazione email

### Use Case 4: Gestione Ordini e Logistica (Evasione Ordini)
**Problema:** Comunicazioni con fornitori su spedizioni, tracking, problemi logistici distribuite su email e altri canali.

**Soluzione Superhuman:**
1. **Split Inbox "Logistica"**: Filtra email con keyword logistiche
2. **Snippets logistici**: Template per richieste tracking, conferme spedizione, reclami corriere
3. **Auto Reminders**: Alert se fornitore non conferma spedizione entro 24h
4. **AI Summary**: Riassunto thread lunghi con corrieri/fornitori

**Impatto stimato:** -30% ritardi comunicazione, tracking rate 99%

### Use Case 5: Coordinamento Team Interno (Direzione)
**Problema:** Coordinare team su più reparti via email è dispersivo.

**Soluzione Superhuman:**
1. **Shared Conversations**: Condividi thread fornitore con team rilevante
2. **Team Comments**: Discuti internamente prima di rispondere a fornitori/marketplace
3. **Team Reply Indicators**: Evita risposte duplicate
4. **Team Insights**: Monitora produttività email del team

**Impatto stimato:** -50% email interne duplicate, +40% coordinamento

### Use Case 6: Amministrazione e Fatturazione (Admin)
**Problema:** Email relative a fatture, note di credito, riconciliazioni si mischiano con il flusso generale.

**Soluzione Superhuman:**
1. **Split Inbox "Amministrazione"**: Filtra email da commercialista, banche, fornitori su argomenti finanziari
2. **Auto Labels custom**: Classifica per tipo (fattura, nota credito, pagamento, OSS)
3. **Snippets admin**: Template per solleciti pagamento, richieste documenti fiscali
4. **Scheduled Send**: Invia solleciti in giorni/orari strategici

### Use Case 7: Monitoraggio Marketplace Notifications (Marketplace Team)
**Problema:** Notifiche dai 30+ marketplace (policy changes, account health, promozioni) si perdono nel flusso email.

**Soluzione Superhuman:**
1. **Split Inbox per marketplace**: Ogni marketplace ha la sua sezione
2. **Auto Labels**: Distingui notifiche critiche (account health, policy violation) da informative (promozioni, report)
3. **Reminders**: Alert per deadline marketplace (documenti, compliance, promozioni)
4. **Ask AI**: "Ci sono notifiche di policy violation questa settimana?" → scan automatico

---

## 16. Integrazioni con lo Stack Vendiamonoi

### 16.1 Superhuman + Gmail/Google Workspace
```
Superhuman Mail ←→ Gmail API ←→ Google Apps Script / Make.com
                                 ↓
                    Google Sheets (logging, analytics)
                    Google Drive (allegati, documenti)
                    Google Calendar (scheduling)
```

### 16.2 Superhuman + Make.com (via Gmail)
```
Gmail Watch Trigger → Make.com Router → Bitrix24 (CRM update)
                                      → Google Sheets (logging)
                                      → ChannelEngine (order update)
                                      → Slack/Telegram (notifiche)
```

### 16.3 Superhuman + Bitrix24
Integrazione indiretta via email:
- Email in arrivo da fornitori → Gmail trigger → Make.com → Crea/aggiorna lead in Bitrix24
- Email importante → Forward automatico a inbox Bitrix24 per tracking CRM

### 16.4 Superhuman + ChannelEngine/Channable
Via Make.com:
- Email conferma spedizione fornitore → Parsing (Make) → Aggiorna tracking in ChannelEngine
- Notifica marketplace (email) → Auto-label + Alert team su ClickUp/Slack

### 16.5 Superhuman Go + Stack Aziendale
Con l'agent ecosystem di Superhuman Go:
- **Google Workspace Connector**: Accesso diretto a Docs, Sheets, Calendar dall'AI assistant
- **Jira Connector**: Visualizza e gestisci ticket senza lasciare la pagina corrente
- Potenziale futuro: connector per Notion, Bitrix24, etc.

---

## 17. Limitazioni

### 17.1 Limitazioni Tecniche
| Limitazione | Dettaglio | Impatto per Vendiamonoi |
|-------------|-----------|------------------------|
| Solo Gmail e Outlook | Nessun supporto IMAP, iCloud, Yahoo, ProtonMail | Basso (usiamo Gmail/Workspace) |
| No Unified Inbox | Non puoi vedere più account email in una vista unica | Medio (se usi più account Gmail) |
| No modulo Make nativo | Automazioni solo via Gmail/Outlook API | Medio (workaround via Gmail trigger) |
| Data centers USA | Dati email processati in data center US | Basso (email già su Google US) |
| Superhuman Go solo Chrome/Edge | Non disponibile su Firefox, Safari, desktop app | Basso |

### 17.2 Limitazioni di Pricing
| Limitazione | Dettaglio |
|-------------|-----------|
| Nessun piano gratuito per Mail | Minimo $30/mese per utente |
| AI avanzata solo Business | Auto Drafts, Ask AI, CRM richiedono $40/mese |
| Enterprise pricing custom | Nessun prezzo pubblico per SSO/SCIM |
| No trial gratuito | Richiede impegno economico immediato |

### 17.3 Limitazioni Operative
| Limitazione | Dettaglio |
|-------------|-----------|
| Curva di apprendimento | 100+ shortcut richiedono settimane per padronanza |
| Calendar solo su mobile | Integrazione calendario nativa solo su iOS/Android |
| Dipendenza da provider | Se Gmail/Outlook ha problemi → Superhuman non funziona |

---

## 18. Risorse e Link Utili

### Documentazione Ufficiale
- **Sito ufficiale**: [superhuman.com](https://superhuman.com/)
- **Help Center**: [help.superhuman.com](https://help.superhuman.com/hc/en-us)
- **Blog**: [blog.superhuman.com](https://blog.superhuman.com/)
- **Product Updates**: [new.superhuman.com](https://new.superhuman.com/)
- **Trust Center**: [trust.superhuman.com](https://trust.superhuman.com/)
- **Privacy Policy**: [superhuman.com/privacy](https://superhuman.com/privacy)
- **DPA**: [superhuman.com/dpa](https://superhuman.com/dpa)
- **Pricing**: [superhuman.com/plans](https://superhuman.com/plans)

### Guide e Onboarding
- **Getting Started**: [blog.superhuman.com/inbox-zero-in-7-steps](https://blog.superhuman.com/inbox-zero-in-7-steps/)
- **Keyboard Shortcuts PDF**: [download.superhuman.com/Superhuman Keyboard Shortcuts.pdf](https://download.superhuman.com/Superhuman%20Keyboard%20Shortcuts.pdf)
- **AI Features**: [superhuman.com/products/mail/ai](https://superhuman.com/products/mail/ai)
- **CRM Integrations**: [superhuman.com/products/mail/crm-integrations](https://superhuman.com/products/mail/crm-integrations)
- **Enterprise**: [superhuman.com/products/mail/enterprise](https://superhuman.com/products/mail/enterprise)

### API & Developer
- **API Tracker**: [apitracker.io/a/superhuman](https://apitracker.io/a/superhuman)
- **GitHub**: [github.com/superhuman](https://github.com/superhuman)

### Help Center — Articoli Chiave
- **AI Features — Write with AI**: [help.superhuman.com/articles/38456855116307](https://help.superhuman.com/hc/en-us/articles/38456855116307-Write-with-AI)
- **AI Features — Ask AI**: [help.superhuman.com/articles/38458628979091](https://help.superhuman.com/hc/en-us/articles/38458628979091-Ask-AI)
- **AI Features — Instant Reply**: [help.superhuman.com/articles/38458397554963](https://help.superhuman.com/hc/en-us/articles/38458397554963-Instant-Reply)
- **Team Features**: [help.superhuman.com/articles/49369800640147](https://help.superhuman.com/hc/en-us/articles/49369800640147-Team-Features)
- **Shared Conversations**: [help.superhuman.com/articles/38457432565267](https://help.superhuman.com/hc/en-us/articles/38457432565267-Shared-Conversations-and-Team-Comments)
- **SCIM Provisioning**: [help.superhuman.com/articles/38456240559891](https://help.superhuman.com/hc/en-us/articles/38456240559891-User-Provisioning-SCIM)
- **SSO**: [help.superhuman.com/articles/38456478385683](https://help.superhuman.com/hc/en-us/articles/38456478385683-SSO-for-Google-and-Microsoft)
- **Compliance**: [help.superhuman.com/articles/43054495888915](https://help.superhuman.com/hc/en-us/articles/43054495888915-Privacy-Terms-of-Service-and-Compliance)
- **Superhuman Suite**: [help.superhuman.com/articles/46191245096979](https://help.superhuman.com/hc/en-us/articles/46191245096979-Superhuman-Suite)

### Articoli di Riferimento
- **Grammarly Acquires Superhuman**: [grammarly.com/blog/company/grammarly-to-acquire-superhuman](https://www.grammarly.com/blog/company/grammarly-to-acquire-superhuman/)
- **Rebranding Announcement**: [grammarly.com/blog/company/introducing-new-superhuman](https://www.grammarly.com/blog/company/introducing-new-superhuman/)
- **SOC 2 Compliance**: [blog.superhuman.com/superhuman-soc-2-compliant-data-privacy](https://blog.superhuman.com/superhuman-soc-2-compliant-data-privacy/)
- **Superhuman Go Agents**: [grammarly.com/blog/company/superhuman-go-new-partner-agents](https://www.grammarly.com/blog/company/superhuman-go-new-partner-agents/)

---

## 19. Changelog Documentazione

| Data | Versione | Modifiche | Autore |
|------|----------|-----------|--------|
| 31/03/2026 | 1.0 | Creazione documentazione completa | Claude AI + Alessio Baggiani |
