# Settore 9 — Browser Control & Web Automation

> Guida completa al sistema di browser automation di OpenClaw: tre modalità di controllo browser (Extension Relay, Managed, Remote CDP), snapshot system, Playwright/Puppeteer, scraping, form filling, e configurazione per Vendiamonoi.

---

## Indice

1. [Panoramica Browser Control](#1-panoramica-browser-control)
2. [Architettura CDP](#2-architettura-cdp)
3. [Tre Modalità di Controllo](#3-tre-modalità-di-controllo)
4. [Snapshot System](#4-snapshot-system)
5. [Azioni Browser Disponibili](#5-azioni-browser-disponibili)
6. [Skill Browser Disponibili](#6-skill-browser-disponibili)
7. [Playwright vs Puppeteer in OpenClaw](#7-playwright-vs-puppeteer-in-openclaw)
8. [Configurazione Browser](#8-configurazione-browser)
9. [Web Scraping](#9-web-scraping)
10. [Form Filling & Automazione](#10-form-filling--automazione)
11. [Screenshot & PDF](#11-screenshot--pdf)
12. [Sicurezza Browser](#12-sicurezza-browser)
13. [Configurazione Vendiamonoi](#13-configurazione-vendiamonoi)
14. [Fonti & Riferimenti](#14-fonti--riferimenti)

---

## 1. Panoramica Browser Control

OpenClaw fornisce **controllo completo del browser** tramite il Chrome DevTools Protocol (CDP). L'agente AI può navigare il web, compilare form, estrarre dati, fare screenshot, e automatizzare qualsiasi interazione web.

### Capacità principali

| Capacità | Descrizione |
|---|---|
| Navigazione | Visitare URL, click link, navigazione back/forward |
| Form filling | Compilare form, select dropdown, checkbox, upload file |
| Data extraction | Leggere testo, tabelle, dati strutturati da pagine |
| Screenshot | Catturare pagine intere o elementi specifici |
| PDF generation | Generare PDF da pagine web |
| Authentication | Login con credenziali, gestione sessione |
| Dynamic content | Attendere AJAX, JavaScript rendering, SPA navigation |
| Multi-tab | Gestione tab multipli |

### Come funziona (high-level)

```
Agente AI → Comando ("vai su amazon.de") → OpenClaw Browser Engine
                                                    │
                                          CDP Protocol (WebSocket)
                                                    │
                                                    ▼
                                          Chrome/Chromium Instance
                                                    │
                                                    ▼
                                              Web Page
```

---

## 2. Architettura CDP

### Chrome DevTools Protocol

OpenClaw usa **CDP** (Chrome DevTools Protocol) come layer di comunicazione con il browser. CDP fornisce una connessione WebSocket persistente (non HTTP polling), garantendo bassa latenza e controllo in tempo reale.

### Architettura porte

| Servizio | Porta | Offset | Funzione |
|---|---|---|---|
| Gateway | 18789 | base | WebSocket principale OpenClaw |
| Control Service | 18791 | base+2 | Servizio di controllo |
| CDP Relay | 18792 | base+3 | Relay per Chrome Extension |
| Managed Browser 1 | 18800 | dedicata | Prima istanza browser isolata |
| Managed Browser 2 | 18801 | dedicata | Seconda istanza browser isolata |
| ... | 18800-18899 | dedicata | Pool di browser managed |

### Flusso CDP

```
1. OpenClaw invia comando CDP via WebSocket
2. Chrome/Chromium riceve ed esegue
3. Risultato ritorna via WebSocket
4. OpenClaw interpreta il risultato
5. AI decide prossima azione
```

---

## 3. Tre Modalità di Controllo

### Modalità 1: Extension Relay (porta 18792)

```
Chrome (tuo browser) ── Extension ── CDP Relay (18792) ── OpenClaw Gateway
```

| Aspetto | Dettaglio |
|---|---|
| **Come funziona** | Estensione Chrome si collega al relay, forwarda comandi CDP ai tab aperti |
| **Login state** | Preserva sessioni login esistenti (Amazon, marketplace...) |
| **Profilo** | Usa il tuo profilo Chrome con cookie, password, estensioni |
| **Isolamento** | Nessuno — accede al tuo browser reale |
| **Uso ideale** | Task che richiedono login esistente, testing manuale |
| **Rischio** | Alto — accesso a tutti i tab e dati del profilo |

### Modalità 2: OpenClaw-Managed (porte 18800-18899)

```
OpenClaw ── Lancia Chromium isolato ── CDP diretto (18800+)
```

| Aspetto | Dettaglio |
|---|---|
| **Come funziona** | OpenClaw lancia un Chromium separato con user data directory dedicata |
| **Login state** | Nessuno — browser pulito (o profilo dedicato) |
| **Profilo** | Isolato — mai tocca cookie, history, password del tuo browser |
| **Isolamento** | Alto — istanza completamente separata |
| **Visual** | Browser con accent color arancione (distinguibile) |
| **Uso ideale** | Automazione production, scraping, task sicuri |
| **Rischio** | Basso — sandbox completo |

### Modalità 3: Remote CDP

```
OpenClaw ── WebSocket ── Cloud Browser (Browserless, BrowserBase)
```

| Aspetto | Dettaglio |
|---|---|
| **Come funziona** | Connessione CDP a browser remoto in cloud |
| **Profilo** | Cloud-managed, effimero |
| **Isolamento** | Massimo — niente locale |
| **Scalabilità** | Alta — pool di browser paralleli |
| **Uso ideale** | Scraping su scala, automazione headless production |
| **Costo** | Variabile (Browserless: da $0.01/min) |

### Confronto modalità

| | Extension Relay | Managed | Remote CDP |
|---|---|---|---|
| Setup | Semplice | Medio | Complesso |
| Login esistente | ✅ | ❌ | ❌ |
| Isolamento | ❌ | ✅ | ✅ |
| Headless | ❌ | ✅ | ✅ |
| Scalabilità | ❌ | Media | Alta |
| Production | ❌ | ✅ | ✅ |
| Costo | $0 | $0 | $$ |

---

## 4. Snapshot System

### Come funziona

Il **Snapshot System** è il modo in cui l'AI "vede" la pagina web. Assegna riferimenti numerici (refs) a ogni elemento interattivo.

### Due tipi di snapshot

| Tipo | Cosa fa | Quando usarlo |
|---|---|---|
| **AI Snapshot** | Analisi semantica della pagina per l'AI | Default, comprensione pagina |
| **Role Snapshot** | Riferimenti basati su ruoli ARIA | Accessibilità, form complessi |

### Ciclo snapshot

```
1. Naviga a pagina
2. Snapshot → mappa di ref numerici per ogni elemento
3. AI analizza snapshot
4. AI decide azione (click ref[3], type ref[7])
5. Azione eseguita
6. Navigazione cambia? → Nuovo snapshot necessario
```

### Regola critica

**I ref scadono alla navigazione.** Dopo ogni cambio di pagina (click link, submit form, redirect), serve un nuovo snapshot. Mai riusare ref vecchi.

---

## 5. Azioni Browser Disponibili

### Azioni complete

| Azione | Comando | Descrizione |
|---|---|---|
| Navigare | `goto(url)` | Visitare un URL |
| Click | `click(ref)` | Cliccare un elemento |
| Type | `type(ref, text)` | Scrivere in un campo |
| Select | `select(ref, value)` | Selezionare da dropdown |
| Drag | `drag(from, to)` | Trascinare elemento |
| Scroll | `scroll(direction)` | Scorrere la pagina |
| Screenshot | `screenshot()` | Catturare la pagina |
| PDF | `pdf()` | Generare PDF |
| Wait | `wait(selector/time)` | Attendere elemento o tempo |
| Evaluate | `evaluate(js)` | Eseguire JavaScript |
| Back/Forward | `back()` / `forward()` | Navigazione storica |
| New Tab | `newTab(url)` | Aprire nuovo tab |
| Close Tab | `closeTab()` | Chiudere tab corrente |
| Switch Tab | `switchTab(index)` | Cambiare tab |

### Azioni avanzate

| Azione | Comando | Descrizione |
|---|---|---|
| Upload file | `upload(ref, path)` | Caricare file |
| Hover | `hover(ref)` | Passare sopra elemento |
| Focus | `focus(ref)` | Dare focus a elemento |
| Press key | `press(key)` | Premere tasto (Enter, Tab...) |
| Set cookie | `setCookie(...)` | Impostare cookie |
| Clear cookies | `clearCookies()` | Pulire cookie |
| Intercept | `intercept(pattern)` | Intercettare richieste network |

---

## 6. Skill Browser Disponibili

### Skill ufficiali e community

| Skill | Download | Tipo | Funzione |
|---|---|---|---|
| **web-browsing** (official) | 180.000+ | Built-in | Navigazione base, lettura pagine |
| **browser-use** | 45.000+ | Community | Automazione browser completa |
| **playwright-mcp** | 30.000+ | MCP | Playwright via MCP server |
| **playwright-cli** | 20.000+ | Community | Playwright da riga di comando |
| **clawbrowser** | 15.000+ | Community | Browser automation avanzato |
| **browser-automation-skill** | 10.000+ | Community | Toolkit CLI headless |

### web-browsing (skill ufficiale)

La skill più scaricata di OpenClaw (180K+). Capacità:
- Navigazione e lettura pagine
- Estrazione testo e dati
- Screenshot
- Ricerca web integrata
- Gestione tab multipli

### playwright-mcp (MCP server)

```json
// openclaw.json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@anthropic/playwright-mcp-server"]
    }
  }
}
```

Fornisce tool avanzati via MCP: navigazione, screenshot, PDF, form filling, valutazione JavaScript.

---

## 7. Playwright vs Puppeteer in OpenClaw

### Ruoli

| Engine | Ruolo in OpenClaw | Uso |
|---|---|---|
| **Playwright** | Engine principale per browser managed | Default per automazione, PDF, snapshot AI |
| **Puppeteer** | Supporto per script custom avanzati | Task specifici, compatibilità legacy |

### Confronto

| Aspetto | Playwright | Puppeteer |
|---|---|---|
| Multi-browser | Chromium, Firefox, WebKit | Solo Chromium |
| Auto-wait | Sì (built-in) | Manuale |
| Snapshot AI | Sì (nativo OpenClaw) | No |
| Performance | Veloce | Veloce |
| Community OpenClaw | Primario | Secondario |
| Headless | Sì | Sì |

### Raccomandazione

Usare **Playwright** (default) per tutto. Puppeteer solo per script legacy già esistenti.

---

## 8. Configurazione Browser

### Configurazione base

```json
// ~/.openclaw/config.json
{
  "browser": {
    "headless": true,                    // true per automazione, false per debug
    "noSandbox": false,                  // true SOLO per Docker/containers
    "defaultProfile": "openclaw",        // Profilo browser
    "profiles": {
      "openclaw": {
        "cdpPort": 18800
      },
      "work": {
        "cdpPort": 18801
      }
    }
  },
  "remoteCdp": {
    "timeoutMs": 1500,
    "handshakeTimeoutMs": 3000
  }
}
```

### Headless mode

```bash
# Abilitare headless (per server/automazione)
openclaw config set browser.headless true

# Disabilitare (per debug visuale)
openclaw config set browser.headless false
```

### Profili multipli

Ogni profilo ha:
- User data directory separata
- Cookie e sessioni isolate
- CDP port dedicata
- Configurazione indipendente

```
Profilo "openclaw" (18800) → Automazione generale
Profilo "work" (18801)     → Login marketplace specifici
Profilo "scraping" (18802) → Scraping senza cookie
```

---

## 9. Web Scraping

### Capacità di scraping

| Feature | Descrizione |
|---|---|
| HTML parsing | Lettura e parsing completo della pagina |
| Dynamic content | Attende JavaScript rendering e AJAX |
| Table extraction | Estrazione strutturata da tabelle HTML |
| Pagination | Navigazione automatica tra pagine |
| Structured data | Output JSON/CSV da dati web |
| Natural language | Descrivi cosa vuoi estrarre, l'AI capisce |

### Pattern di scraping per e-commerce

```
1. Naviga a pagina prodotto/listing
2. Snapshot della pagina
3. AI identifica: titolo, prezzo, disponibilità, venditore
4. Estrai dati in formato strutturato
5. Se più pagine: paginazione automatica
6. Salva risultati in CSV/JSON
```

### Anti-detection

| Tecnica | Descrizione |
|---|---|
| User-Agent rotation | Cambiare user-agent ad ogni sessione |
| Request throttling | Limitare velocità richieste |
| Cookie management | Gestire cookie per sembrare utente reale |
| Proxy rotation | Usare proxy diversi (opzionale) |
| Headless detection bypass | Playwright ha built-in anti-detection |

### Limiti e responsabilità

- Rispettare `robots.txt` e ToS dei siti
- Rate limiting per non sovraccaricare server target
- Non scrape dati personali senza consenso
- Marketplace hanno anti-bot: usare API ufficiali quando disponibili

---

## 10. Form Filling & Automazione

### Workflow form filling

```
1. Naviga al form
2. Snapshot → identifica campi (ref numerici)
3. Per ogni campo:
   a. type(ref, valore) per input testo
   b. select(ref, opzione) per dropdown
   c. click(ref) per checkbox/radio
4. Upload file se necessario
5. Screenshot di verifica (opzionale)
6. Submit
7. Verifica risultato
```

### Casi d'uso per Vendiamonoi

| Task | Descrizione | Frequenza |
|---|---|---|
| Listing marketplace | Compilare form listing su marketplace minori | Ad-hoc |
| Registrazione marketplace | Registrarsi su nuovi marketplace | Raro |
| Apertura ticket | Aprire ticket supporto su marketplace | Ad-hoc |
| Aggiornamento dati | Aggiornare info account su marketplace | Raro |
| Download report | Scaricare report da dashboard marketplace | Giornaliero |

---

## 11. Screenshot & PDF

### Screenshot

```
# Tipi di screenshot
screenshot()                    # Pagina intera
screenshot(element: ref[5])     # Solo un elemento
screenshot(fullPage: true)      # Tutta la pagina (scroll incluso)
screenshot(clip: {x, y, w, h}) # Area specifica
```

**Usi**:
- Documentare listing competitor
- Salvare stato ordini da dashboard
- Evidence per dispute marketplace
- Debug automazione

### PDF Generation

```
pdf()                          # Pagina corrente come PDF
pdf(format: "A4")              # Formato specifico
pdf(printBackground: true)     # Includere background
```

**Usi**:
- Generare fatture da template web
- Salvare documenti da portali fornitori
- Archiviare pagine importanti

---

## 12. Sicurezza Browser

### Rischi specifici

| Rischio | Descrizione | Mitigazione |
|---|---|---|
| **Cookie theft** | Extension relay espone cookie del profilo | Usare Managed mode per production |
| **Session hijack** | Accesso a sessioni login attive | Profili separati per automazione |
| **Data leakage** | Scraping può esporre dati sensibili | Sandbox + log audit |
| **XSS via browser** | Pagine malevole che attaccano l'agente | NoScript, isolamento profilo |
| **Credential exposure** | Password salvate nel profilo browser | Mai salvare credenziali nel profilo managed |
| **Port exposure** | CDP ports esposti su rete | Localhost only, mai esporre 18800+ |

### Hardening checklist browser

```yaml
# 10 regole sicurezza browser
1. MAI usare Extension Relay in production
   # Solo per testing/debug con supervisione

2. Usare SEMPRE Managed mode per automazione
   # Isolamento completo, profilo dedicato

3. Headless in production
   # browser.headless: true

4. Porte su localhost only
   # MAI esporre 18792, 18800-18899 su rete pubblica

5. Profili separati per uso diverso
   # openclaw: automazione, work: marketplace, scraping: dati

6. Non salvare password nel profilo managed
   # Usare variabili d'ambiente per credenziali

7. Sandbox attivo
   # noSandbox: false (true solo per Docker necessità)

8. Rate limiting per scraping
   # Rispettare ToS e robots.txt

9. Timeout per tutte le operazioni
   # remoteCdpTimeoutMs: 1500

10. Log di tutte le azioni browser
    # Per audit e debug
```

---

## 13. Configurazione Vendiamonoi

### Setup raccomandato

```json
// ~/.openclaw/config.json
{
  "browser": {
    "headless": true,
    "noSandbox": false,
    "defaultProfile": "openclaw",
    "profiles": {
      "openclaw": {
        "cdpPort": 18800,
        "description": "Automazione generale"
      },
      "marketplace": {
        "cdpPort": 18801,
        "description": "Login marketplace per download report"
      },
      "scraping": {
        "cdpPort": 18802,
        "description": "Scraping prezzi competitor - no cookie"
      }
    }
  },
  "remoteCdp": {
    "timeoutMs": 2000,
    "handshakeTimeoutMs": 3000
  }
}
```

### Skill browser da installare

```bash
# Skill browser essenziali
openclaw skills install web-browsing@latest        # Navigazione base (ufficiale)
openclaw skills install playwright-mcp@latest      # Automazione avanzata via MCP
```

### MCP server Playwright

```json
// In openclaw.json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@anthropic/playwright-mcp-server"]
    }
  }
}
```

### Casi d'uso per Vendiamonoi

| Caso d'uso | Modalità | Profilo | Frequenza | Cron? |
|---|---|---|---|---|
| Scraping prezzi competitor | Managed headless | scraping | 2x/giorno | ✅ |
| Download report marketplace | Managed | marketplace | 1x/giorno | ✅ |
| Screenshot listing competitor | Managed headless | scraping | Settimanale | ✅ |
| Compilazione form marketplace | Managed | marketplace | Ad-hoc | ❌ |
| Verifica listing propri | Managed headless | openclaw | Settimanale | ✅ |
| Ricerca prodotti/trend | Managed headless | openclaw | Ad-hoc | ❌ |

### Cron job browser per Vendiamonoi

```bash
# Scraping prezzi competitor (2x/giorno)
openclaw cron add --name "price-scraping" \
  --cron "0 7,19 * * *" --session isolated --model haiku \
  --message "Naviga su Amazon.de e eBay.de. Scraping prezzi top 50 SKU competitor. \
Salva risultati in /data/prices/$(date +%Y-%m-%d).json"

# Download report marketplace (1x/giorno)
openclaw cron add --name "marketplace-reports" \
  --cron "0 6 * * *" --session isolated \
  --message "Scarica report giornalieri da Amazon Seller Central, eBay, Kaufland. \
Salva in /data/reports/"

# Screenshot listing competitor (settimanale)
openclaw cron add --name "competitor-screenshots" \
  --cron "0 5 * * 1" --session isolated --model haiku \
  --message "Screenshot dei top 20 listing competitor su Amazon.de per le nostre categorie. \
Salva in /data/screenshots/competitor/"
```

### API vs Scraping — Quando usare cosa

| Situazione | Usare API | Usare Browser |
|---|---|---|
| Dati propri (ordini, inventario) | ✅ ChannelEngine API | ❌ |
| Dati competitor (prezzi) | ❌ (no API disponibile) | ✅ Scraping |
| Report marketplace | Dipende (API se disponibile) | ✅ Se no API |
| Registrazione nuovo marketplace | ❌ | ✅ Form filling |
| Listing prodotti | ✅ API/feed | ❌ |
| Monitoring listing propri | ✅ API + ✅ browser (verifica visiva) | ✅ |

**Regola**: Usare API quando disponibili. Browser solo per ciò che non ha API.

---

## 14. Fonti & Riferimenti

### Documentazione ufficiale
- Browser (OpenClaw-managed): https://docs.openclaw.ai/tools/browser
- Security: https://docs.openclaw.ai/gateway/security

### Guide e tutorial
- Apiyi — 5 Core Browser Features: https://help.apiyi.com/en/openclaw-browser-automation-guide-en.html
- LaoZhang — Browser Control Modes & CDP: https://blog.laozhang.ai/en/posts/openclaw-browser-control
- Level Up Coding — Browser Automation Complete Guide 2026: https://levelup.gitconnected.com/openclaw-browser-automation-the-complete-guide-for-2026
- AI Free API — Browser Relay Guide: https://www.aifreeapi.com/en/posts/openclaw-browser-relay-guide
- SkyWork — Browser Relay Extension: https://skywork.ai/skypage/en/openclaw-browser-relay-extension/
- GetClawKit — Browser Automation Setup: https://getclawkit.com/docs/guides/browser-automation-openclaw
- GetOpenClaw — Browser Setup: https://www.getopenclaw.ai/en/help/browser-automation-setup
- Hostinger — Browser Extension: https://www.hostinger.com/tutorials/how-to-use-openclaw-browser-extension

### Skill browser
- browser-use: https://playbooks.com/skills/openclaw/skills/browser-use
- playwright-mcp: https://lobehub.com/skills/openclaw-skills-playwright-mcp
- playwright-cli: https://playbooks.com/skills/openclaw/skills/playwright-cli
- clawbrowser: https://lobehub.com/skills/openclaw-skills-clawbrowser
- browser-automation-skill: https://lobehub.com/skills/openclaw-skills-browserautomation-skill

### Sicurezza
- Vedi Settore 3 (CVE-2026-25253, hardening)
- Bitsight — OpenClaw Security Risks: https://www.bitsight.com/blog/openclaw-ai-security-risks-exposed-instances
- Nebius — Security Architecture: https://nebius.com/blog/posts/openclaw-security

---

> **Nota per Vendiamonoi**: Il browser control è potente ma va usato con giudizio. Usare API (ChannelEngine, Shopify) per operazioni standard. Browser per: scraping prezzi competitor, download report senza API, screenshot listing, compilazione form marketplace minori. Sempre Managed mode in production, mai Extension Relay.

---

*Ultimo aggiornamento: aprile 2026*
*Documento parte della Master Class OpenClaw — vendiamonoi-knowledge*
