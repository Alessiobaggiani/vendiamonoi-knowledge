# OpenClaw — Settore 2: Installazione & Setup Completo

## Deep-Dive Tecnico per Vendiamonoi.it

**Versione:** 1.0
**Data:** Aprile 2026
**Tipo:** Deep-Dive Tecnico
**Stato:** ✅ Completato

---

## 1. Requisiti di Sistema

### 1.1 Hardware Minimo

| Componente | Minimo | Raccomandato |
|------------|--------|--------------|
| RAM | 2 GB | 4 GB+ |
| Disco | 500 MB (solo OpenClaw) | 2 GB+ (con Docker e skills) |
| CPU | Qualsiasi x64/ARM64 | Multi-core per operazioni concorrenti |
| Rete | Connessione internet stabile | Banda larga per streaming AI e canali |

### 1.2 Software Richiesto

| Software | Versione | Note |
|----------|----------|------|
| **Node.js** | 24+ (raccomandato), 22.14+ (minimo) | Runtime principale |
| **npm** o **pnpm** | Incluso con Node.js | Package manager |
| **Docker** + **Docker Compose v2** | Ultima versione | Solo per deployment Docker |
| **Git** | Qualsiasi recente | Per installazione da sorgente |
| **WSL2** | Ubuntu raccomandato | Solo per Windows |

### 1.3 Compatibilità Sistemi Operativi

| SO | Supporto | Note |
|----|----------|------|
| **macOS** | ✅ Nativo | Supporto completo incluso menu bar app |
| **Linux** | ✅ Nativo | Ubuntu, Debian, Fedora, Arch, NixOS |
| **Windows** | ✅ Via WSL2 | Nativo non testato, WSL2 fortemente raccomandato |
| **Raspberry Pi** | ✅ Via Docker | ARM64 supportato |
| **NAS** (Synology/QNAP) | ✅ Via Docker | Container-based |

---

## 2. Metodi di Installazione

OpenClaw offre 5 metodi di installazione, in ordine di complessità crescente:

### 2.1 Metodo 1: npm (Raccomandato per la maggior parte degli utenti)

```bash
# Verifica versione Node.js (deve essere 22.14+)
node --version

# Installa OpenClaw globalmente
npm install -g openclaw@latest

# Avvia il wizard di onboarding
openclaw onboard --install-daemon

# Avvia il gateway
openclaw gateway --port 18789 --verbose
```

Se Node.js non è installato o è troppo vecchio:
```bash
# Installa nvm (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash

# Installa Node.js 24 (raccomandato)
nvm install 24
nvm use 24

# Verifica
node --version  # deve mostrare v24.x.x
```

### 2.2 Metodo 2: Docker (Raccomandato per server/produzione)

```bash
# Clone il repository
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# Avvia con lo script di setup (crea volumi e configura)
./docker-setup.sh

# Oppure manualmente con Docker Compose
docker compose up -d
```

**Volumi Docker persistenti:**
- `~/.openclaw/` → `/home/node/.openclaw` (config, credenziali, sessioni)
- `~/openclaw/workspace/` → `/home/node/.openclaw/workspace` (file dell'agente)

**Docker Compose personalizzato:**
```yaml
version: '3.8'
services:
  openclaw:
    image: ghcr.io/openclaw/openclaw:latest
    ports:
      - "18789:18789"
    volumes:
      - ~/.openclaw:/home/node/.openclaw
      - ~/openclaw/workspace:/home/node/.openclaw/workspace
    environment:
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:18789/healthz"]
      interval: 30s
      timeout: 10s
      retries: 3
```

**Health check integrato:** L'immagine Docker include un HEALTHCHECK su `/healthz`. Se fallisce, Docker marca il container come unhealthy.

### 2.3 Metodo 3: Da Sorgente (Per sviluppatori/contributor)

```bash
# Clone
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# Installa dipendenze
pnpm install

# Build UI e progetto
pnpm ui:build && pnpm build

# Onboarding
pnpm openclaw onboard --install-daemon

# Dev mode con auto-reload
pnpm gateway:watch
```

### 2.4 Metodo 4: Nix/NixOS (Dichiarativo e riproducibile)

Usando il flake ufficiale `nix-openclaw`:
```nix
# flake.nix
{
  inputs = {
    nix-openclaw.url = "github:openclaw/nix-openclaw";
  };
  # ... configurazione Home Manager
}
```

Vantaggi: configurazione dichiarativa, version-controlled, riproducibile, nessun drift.

### 2.5 Metodo 5: Cloud 1-Click Deploy

| Provider | Tipo | Prezzo |
|----------|------|--------|
| **DigitalOcean** | 1-Click App con hardening | Da $6/mese |
| **Contabo** | 1-Click Add-On per VPS/VDS | Gratuito (solo costo VPS) |
| **Hostinger** | Template Docker pre-configurato | Da $5/mese |
| **Zeabur** | Deploy template | Pay-as-you-go |

---

## 3. Il Wizard di Onboarding

### 3.1 Avvio

```bash
openclaw onboard
# oppure con installazione daemon automatica:
openclaw onboard --install-daemon
```

### 3.2 Modalità di Setup

| Modalità | Descrizione | Quando usarla |
|----------|-------------|---------------|
| **QuickStart** | Defaults ottimizzati, modalità locale forzata | Prima installazione, utenti non tecnici |
| **Advanced** | Controllo completo su ogni parametro | Utenti esperti, setup remoto, multi-agente |

### 3.3 Step del Wizard (in ordine)

**Step 1: Modello AI e Autenticazione**
- Scelta provider: Anthropic (Claude), OpenAI, Google (Gemini), OpenRouter, Ollama, ecc.
- Metodo auth: API key (incolla la chiave), OAuth (flow nel browser), setup-token
- Scelta modello di default (es. `anthropic/claude-sonnet-4-5`)
- Consiglio: Claude Sonnet 4.6 è il più popolare per rapporto qualità/velocità/costo

**Step 2: Workspace**
- Path del workspace: default `~/.openclaw/workspace`
- Il workspace è dove l'agente salva i file di lavoro
- Separato da `~/.openclaw/` (config, credenziali, sessioni)

**Step 3: Gateway**
- Porta: default `18789`
- Bind address: default `127.0.0.1` (solo localhost — sicuro)
- Per accesso remoto: configurare Tailscale o reverse proxy successivamente

**Step 4: Canali e Skills**
- Installazione skills raccomandate
- Configurazione opzionale dei canali (Telegram, WhatsApp, ecc.)
- Può essere fatto dopo

**Step 5: Web Search (opzionale)**
- Provider: Perplexity, Brave, Gemini, Grok, Kimi
- Richiede API key del provider scelto

**Step 6: Health Check**
- Esegue `openclaw health` automaticamente
- Verifica connettività, config, e dipendenze

### 3.4 Re-Run del Wizard

```bash
# Re-run senza perdere dati (aggiorna solo config)
openclaw onboard

# Reset completo config + credenziali + sessioni
openclaw onboard --reset

# Reset totale incluso workspace
openclaw onboard --reset --reset-scope full
```

---

## 4. Struttura Directory

### 4.1 Directory di Sistema (`~/.openclaw/`)

Questa è la "engine room" — NON va nel repository.

```
~/.openclaw/
├── openclaw.json          # Configurazione principale (JSON5)
├── credentials/           # Chiavi API e token (cifrati)
├── agents/
│   └── <agentId>/
│       └── sessions/      # Sessioni conversazione
├── channels/              # Config canali (YAML per piattaforma)
├── skills/                # Skills gestite (managed/bundled)
├── cron/                  # Job schedulati persistenti
├── plugins/               # Plugin installati
└── logs/                  # Log del gateway
```

### 4.2 Workspace dell'Agente (`~/.openclaw/workspace/`)

Questa è l'"ufficio" dell'agente — può essere un repo Git.

```
workspace/
├── AGENTS.md              # Istruzioni operative (caricato ogni sessione)
├── SOUL.md                # Persona, tono, limiti (caricato ogni sessione)
├── USER.md                # Chi è l'utente (caricato ogni sessione)
├── IDENTITY.md            # Nome, stile, emoji dell'agente
├── TOOLS.md               # Note sui tool locali (solo guida, non controlla disponibilità)
├── HEARTBEAT.md           # Checklist per heartbeat run (opzionale, tienilo corto)
├── BOOT.md                # Checklist startup al restart del gateway (opzionale)
├── memory/
│   └── YYYY-MM-DD.md      # Note giornaliere (decisioni, fix, errori)
├── skills/                # Skills workspace-specific (override managed/bundled)
└── canvas/                # File Canvas UI (es. canvas/index.html)
```

**File critici per Vendiamonoi:**
- `AGENTS.md` → qui definirai le regole per l'agente Vendiamonoi (priorità, comportamento, regole)
- `SOUL.md` → personalità e tono dell'agente (professionale, bilingue IT/EN, ecc.)
- `USER.md` → informazioni su Alessio e il team
- `memory/` → log giornaliero delle decisioni e delle azioni dell'agente

---

## 5. Configurazione dei Canali

### 5.1 Panoramica Canali

La configurazione dei canali risiede in `~/.openclaw/channels/` con file YAML per piattaforma.

| Canale | Metodo Auth | Difficoltà | Consigliato per primo? |
|--------|------------|------------|------------------------|
| **Telegram** | Bot Token (@BotFather) | ⭐ Facilissimo | ✅ Sì, raccomandato |
| **WhatsApp** | QR Code (Baileys) | ⭐⭐ Facile | ✅ Sì |
| **Discord** | Bot Token + OAuth | ⭐⭐ Facile | Opzionale |
| **Slack** | Bolt App + OAuth | ⭐⭐⭐ Medio | Per uso aziendale |
| **Signal** | Linked Device | ⭐⭐ Facile | Per privacy |
| **iMessage** | BlueBubbles server | ⭐⭐⭐⭐ Complesso | Solo macOS |
| **Microsoft Teams** | Azure App Registration | ⭐⭐⭐⭐ Complesso | Per enterprise |
| **WebChat** | Built-in (nessuna config) | ⭐ Zero setup | ✅ Già attivo |

### 5.2 Setup Telegram (Raccomandato come primo canale)

```bash
# 1. Crea bot su Telegram
# Apri Telegram → cerca @BotFather → /newbot
# Scegli nome e username (deve finire con "bot")
# Copia il token

# 2. Configura in openclaw.json
# oppure usa il wizard:
openclaw channels login telegram
```

**Config manuale in `openclaw.json`:**
```json5
{
  "channels": {
    "telegram": {
      "enabled": true,
      "botToken": "YOUR_BOT_TOKEN"
    }
  }
}
```

**Comportamento di default:**
- DM: pairing protection attivo (l'utente deve essere approvato)
- Gruppi: l'agente risponde solo quando menzionato con @

### 5.3 Setup WhatsApp

```bash
# 1. Avvia il login
openclaw channels login whatsapp

# 2. Scansiona il QR code
# Apri WhatsApp sul telefono → Impostazioni → Dispositivi collegati → Collega dispositivo
# Scansiona il QR code nel terminale

# 3. Fatto! L'agente è ora raggiungibile su WhatsApp
```

**Limitazione importante:** WhatsApp disconnette dopo ~14 giorni. Quando succede, ri-esegui `openclaw channels login whatsapp` e riscansiona il QR code. Usa Baileys (libreria non ufficiale del protocollo WhatsApp Web).

### 5.4 Setup Discord

```bash
# 1. Crea una Discord Application su https://discord.com/developers/applications
# 2. Sezione Bot → crea bot → copia token
# 3. Invita il bot nel tuo server con i permessi necessari
# 4. Configura:
```

```json5
{
  "channels": {
    "discord": {
      "enabled": true,
      "botToken": "YOUR_DISCORD_BOT_TOKEN"
    }
  }
}
```

### 5.5 Setup Slack

1. Crea una Slack App su https://api.slack.com/apps
2. Configura con Bolt framework
3. Installa l'app nel workspace Slack
4. Configura bot token e signing secret in `openclaw.json`

### 5.6 WebChat (Zero Setup)

Il WebChat è sempre disponibile senza configurazione:
```bash
# Apri il dashboard nel browser
openclaw dashboard
# Il WebChat è integrato nella Control UI
```

Per il primo test, è il modo più veloce per parlare con l'agente.

---

## 6. Configurazione Provider AI

### 6.1 Metodi di Autenticazione

| Metodo | Sicurezza | Uso |
|--------|-----------|-----|
| **Environment Variable** | ⭐⭐⭐ Alta | Raccomandato per produzione |
| **Config file** | ⭐⭐ Media | Per setup personale |
| **SecretRef** | ⭐⭐⭐⭐ Massima | Per ambienti condivisi (env, file, exec) |
| **OAuth** | ⭐⭐⭐ Alta | Per provider che lo supportano |

### 6.2 Setup per Provider

**Claude (Anthropic):**
```bash
# Via environment variable (raccomandato)
export ANTHROPIC_API_KEY="sk-ant-..."

# Rotazione chiavi (opzionale)
export ANTHROPIC_API_KEY_1="sk-ant-...primo..."
export ANTHROPIC_API_KEY_2="sk-ant-...secondo..."
```

**OpenAI:**
```bash
export OPENAI_API_KEY="sk-..."
# Rotazione:
export OPENAI_API_KEY_1="sk-...primo..."
export OPENAI_API_KEY_2="sk-...secondo..."
```

**Google Gemini:**
```bash
export GEMINI_API_KEY="AIza..."
# Rotazione:
export GEMINI_API_KEY_1="AIza...primo..."
export GEMINI_API_KEY_2="AIza...secondo..."
```

**OpenRouter (accesso a 100+ modelli):**
```bash
export OPENROUTER_API_KEY="sk-or-..."
```

**Ollama (locale, gratuito):**
```bash
# Nessuna API key necessaria
# Ollama viene auto-rilevato su http://127.0.0.1:11434/v1
# Assicurati che Ollama sia in esecuzione:
ollama serve
```

### 6.3 Configurazione Modello nel Config

```json5
// ~/.openclaw/openclaw.json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-sonnet-4-5",
        "fallback": "openai/gpt-4o",
        "economy": "anthropic/claude-haiku-4-5"  // per task leggeri
      }
    }
  }
}
```

**Rotazione chiavi:**
Le richieste vengono re-tentate con la chiave successiva solo su errori rate-limit. Errori non rate-limit falliscono immediatamente senza rotazione.

---

## 7. Gateway Daemon (Servizio in Background)

### 7.1 Installazione Automatica

```bash
# L'onboarding installa automaticamente il daemon:
openclaw onboard --install-daemon

# Oppure manualmente:
openclaw daemon install
```

### 7.2 Implementazione per OS

| OS | Tecnologia | Path del servizio |
|----|-----------|-------------------|
| **macOS** | LaunchAgent (launchd) | `~/Library/LaunchAgents/ai.openclaw.gateway.plist` |
| **Linux** | systemd user service | `~/.config/systemd/user/openclaw-gateway.service` |
| **Windows (WSL2)** | systemd dentro WSL | Richiede systemd abilitato in WSL |
| **Docker** | `restart: unless-stopped` | Gestito da Docker |

### 7.3 Comandi di Gestione

```bash
# Status
openclaw gateway autostart status

# Start/Stop/Restart
openclaw gateway start
openclaw gateway stop
openclaw gateway restart

# Logs
openclaw logs --follow

# Diagnostica
openclaw doctor
```

### 7.4 Auto-Restart

In tutti i casi, OpenClaw riavvia automaticamente se crasha. Il daemon supervisor (launchd/systemd) si occupa di mantenerlo in vita.

**Nota importante:** I servizi di sistema NON ereditano il PATH della shell. Usare il path assoluto del binario nel unit file. Trovalo con: `which openclaw`

---

## 8. Aggiornamenti e Versioning

### 8.1 Canali di Release

| Canale | Formato | npm dist-tag | Auto-update | Uso |
|--------|---------|-------------|-------------|-----|
| **stable** | vYYYY.M.D | latest | Sì (con delay + jitter) | Produzione |
| **beta** | vYYYY.M.D-beta.N | beta | Sì (check orario) | Testing nuove feature |
| **dev** | Head del main | dev | No (solo manuale) | Sviluppo |

### 8.2 Comandi di Aggiornamento

```bash
# Aggiorna alla latest stable
openclaw update

# Cambia canale
openclaw update --channel stable
openclaw update --channel beta
openclaw update --channel dev

# Downgrade (richiede conferma)
openclaw update --version vYYYY.M.D
```

### 8.3 Aggiornamento Automatico

**Stable:** quando viene rilevata una nuova versione, OpenClaw attende `stableDelayHours` e poi applica un jitter deterministico (`stableJitterHours`) per distribuire il rollout.

**Beta:** controlla ogni `betaCheckIntervalHours` (default: ogni ora) e applica quando disponibile.

**Dev:** nessun auto-update. Solo `openclaw update` manuale.

### 8.4 Post-Aggiornamento

Dopo ogni aggiornamento:
1. Esegue preflight checks
2. Installa dipendenze
3. Build del progetto e Control UI
4. Esegue `openclaw doctor` come safety check finale
5. Sincronizza plugin al canale attivo

---

## 9. Setup Specifico per Windows (WSL2)

### 9.1 Prerequisiti

```powershell
# Da PowerShell come Amministratore
wsl --install
# Riavvia il computer
# Apri Ubuntu dal Menù Start
```

### 9.2 Configurazione WSL2

**Abilitare systemd** (necessario per il daemon):
```bash
# In WSL, crea/modifica /etc/wsl.conf
sudo nano /etc/wsl.conf
```
```ini
[boot]
systemd=true
```

**Gestione RAM:**
Senza `.wslconfig`, WSL2 userà fino all'80% della RAM di sistema.
```
# %USERPROFILE%/.wslconfig (su Windows)
[wsl2]
memory=4GB
processors=2
```

### 9.3 Regole Critiche per WSL2

- **SEMPRE** clonare nel filesystem Linux (`~/`), MAI in `/mnt/c/` (10x più lento)
- **SEMPRE** usare Node.js, MAI Bun (incompatibilità con canali WhatsApp/Telegram)
- **Se WSL non parte:** `wsl --shutdown` da PowerShell, poi `wsl`
- **Se DNS non funziona:** `sudo rm /etc/resolv.conf && sudo bash -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'`
- **Problema PATH:** Se `openclaw: command not found`, usa il path completo: `~/.npm-global/bin/openclaw`

---

## 10. Troubleshooting Comune

### 10.1 Problemi Frequenti e Soluzioni

| Problema | Causa | Soluzione |
|----------|-------|----------|
| `openclaw: command not found` | PATH non aggiornato | Riapri terminale, o usa path completo |
| Node.js version error | Versione troppo vecchia | `nvm install 24 && nvm use 24` |
| Gateway non si avvia | Porta 18789 occupata | `lsof -i :18789` per trovare chi la usa |
| WhatsApp disconnesso | Sessione scaduta (14 giorni) | `openclaw channels login whatsapp` + riscansiona QR |
| Telegram bot non risponde | Token errato o bot non avviato | Verifica token con @BotFather, riavvia gateway |
| Docker build fallisce | RAM insufficiente | Servono almeno 2 GB RAM |
| Skills non si installano | Conflitto versioni | `openclaw skills repair` |
| Config corrotta | Edit manuale errato | `openclaw doctor` per diagnosticare, `openclaw onboard --reset` per reset |

### 10.2 Comandi di Diagnostica

```bash
# Diagnostica completa (IL comando più utile)
openclaw doctor

# Logs in tempo reale
openclaw logs --follow

# Verifica configurazione
openclaw config show

# Stato del gateway
openclaw gateway status

# Test connettività modello
openclaw agent --message "test"
```

---

## 11. CLI Reference Essenziale

### 11.1 Comandi Principali

| Comando | Descrizione |
|---------|-------------|
| `openclaw onboard` | Wizard di setup interattivo |
| `openclaw gateway` | Avvia il control plane |
| `openclaw gateway restart` | Riavvia il gateway |
| `openclaw agent --message "testo"` | Invia messaggio all'agente |
| `openclaw doctor` | Diagnostica completa |
| `openclaw config show` | Mostra configurazione |
| `openclaw config set <path> <value>` | Modifica configurazione |
| `openclaw dashboard` | Apri Control UI nel browser |
| `openclaw update` | Aggiorna alla versione più recente |
| `openclaw channels login <canale>` | Setup canale di messaggistica |
| `openclaw logs --follow` | Log in tempo reale |
| `openclaw daemon install` | Installa servizio in background |

### 11.2 Comandi Messaging

| Comando | Descrizione |
|---------|-------------|
| `openclaw message send --to <dest> --message "testo"` | Invia messaggio |
| `openclaw message search --query "termine"` | Cerca nei messaggi |
| `openclaw message react` | Reagisci a un messaggio |
| `openclaw message pin` | Pinna un messaggio |

### 11.3 Comandi Gestione Agente

| Comando | Descrizione |
|---------|-------------|
| `openclaw agents list` | Lista agenti configurati |
| `openclaw agents add` | Aggiungi nuovo agente isolato |
| `openclaw models` | Configurazione modelli |
| `openclaw skills install <nome>` | Installa una skill |
| `openclaw skills list` | Lista skills installate |
| `openclaw plugins doctor` | Diagnostica plugin |

---

## 12. Primo Setup Raccomandato per Vendiamonoi

### 12.1 Sequenza Consigliata

1. **Installa Node.js 24** via nvm
2. **Installa OpenClaw** via npm
3. **Esegui onboarding** con Claude Sonnet come modello primario
4. **Testa via WebChat** (zero config, nel browser)
5. **Configura Telegram** come primo canale esterno
6. **Configura WhatsApp** come secondo canale
7. **Installa il daemon** per esecuzione 24/7
8. **Configura il workspace** con AGENTS.md personalizzato per Vendiamonoi

### 12.2 AGENTS.md Template per Vendiamonoi

```markdown
# Agente Vendiamonoi

Sei l'agente AI di Vendiamonoi.it, azienda di distribuzione digitale su marketplace europei.

## Regole
- Rispondi sempre in italiano, salvo richieste in altre lingue
- Priorità: efficienza operativa, automazione, scalabilità
- Tono: professionale ma accessibile
- Per decisioni importanti: chiedi conferma prima di agire

## Contesto Aziendale
- 20-30+ marketplace europei (Amazon, eBay, Kaufland, Leroy Merlin...)
- 100+ fornitori attivi
- Milioni di SKU gestiti
- Reparti: Fornitori, Catalogo, Marketplace, Ordini, CS, Amministrazione

## Priorità Operative
1. Monitoraggio ordini e stock
2. Comunicazione fornitori
3. Report giornalieri marketplace
4. Alert su anomalie pricing
5. Supporto customer service multi-lingua
```

---

## Fonti e Riferimenti

- [OpenClaw Node.js Installation](https://docs.openclaw.ai/install/node)
- [OpenClaw Docker Installation](https://docs.openclaw.ai/install/docker)
- [OpenClaw Nix Installation](https://docs.openclaw.ai/install/nix)
- [OpenClaw Windows/WSL2](https://docs.openclaw.ai/platforms/windows)
- [OpenClaw Onboarding Wizard](https://docs.openclaw.ai/start/wizard)
- [OpenClaw CLI Reference](https://docs.openclaw.ai/cli)
- [OpenClaw Agent Workspace](https://docs.openclaw.ai/concepts/agent-workspace)
- [OpenClaw Daemon Management](https://docs.openclaw.ai/cli/daemon)
- [OpenClaw Update Management](https://docs.openclaw.ai/cli/update)
- [OpenClaw Telegram Setup](https://docs.openclaw.ai/channels/telegram)
- [OpenClaw Slack Setup](https://docs.openclaw.ai/channels/slack)
- [OpenClaw Authentication](https://docs.openclaw.ai/gateway/authentication)
- [OpenClaw Configuration](https://docs.openclaw.ai/gateway/configuration)
- [OpenClaw Cheatsheet](https://openclawcheatsheet.com/)
- [Awesome OpenClaw Skills](https://github.com/VoltAgent/awesome-openclaw-skills)
