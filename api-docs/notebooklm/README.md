# NotebookLM — Documentazione Tecnica

> **Vendiamonoi.it S.R.L.** — Documentazione interna
> **Ultimo aggiornamento:** 31/03/2026
> **Autore:** Claude (Cowork)
> **Pagina Notion:** [NotebookLM](https://www.notion.so/28dab24b308680899f89ca26ebfdd869)

---

## Panoramica

Google NotebookLM è un assistente di ricerca AI-powered basato su Gemini che permette di caricare documenti (PDF, siti web, YouTube, Google Drive, testo) e interrogarli con risposte fondate e citate.

**Rilevanza per Vendiamonoi.it:** knowledge base fornitori, analisi documentazione marketplace, formazione team, generazione contenuti audio/video da documentazione tecnica.

### Note Critiche

- NotebookLM **Consumer** NON ha API REST ufficiale — solo interfaccia web
- NotebookLM **Enterprise** ha API REST ufficiale via Google Cloud Discovery Engine (da settembre 2025)
- Esistono librerie **unofficial** (notebooklm-py) che usano endpoint non documentati — soggette a rottura senza preavviso
- Per automazioni production-grade: valutare Enterprise o alternative con Gemini API diretta

---

## Piani e Limiti

### Free Plan
- Notebook illimitati (con limiti di fonti)
- Fino a 50 fonti per notebook
- Singolo documento max 200 MB o 500.000 parole
- Audio Overview limitate
- Nessun accesso API

### NotebookLM Plus (~$9.99/mese o incluso in Google One AI Premium)
- Limiti aumentati su fonti e query
- Audio Overview illimitate
- Personalizzazione tono e lunghezza risposte
- Notebook condivisi

### NotebookLM Ultra (~$250/mese)
- 5x limiti rispetto a Plus
- Funzionalità avanzate enterprise-lite
- Priorità nelle risposte

### NotebookLM Enterprise ($9/licenza/mese su Google Cloud)
- API REST ufficiale
- VPC-SC compliance
- IAM access controls
- 5x limiti per voice summaries, notebook e fonti
- Data residency (US o EU multi-region)
- Admin console e audit log

---

## 📡 API Documentation

### API REST — NotebookLM Enterprise (Ufficiale)

#### Autenticazione

**Metodo:** Google Cloud Application Default Credentials (ADC)

```bash
gcloud auth application-default login
gcloud config set project [YOUR_PROJECT_ID]
```

**Header:** `Authorization: Bearer $(gcloud auth print-access-token)`

#### Base URL

```
https://{LOCATION}-discoveryengine.googleapis.com/v1alpha/projects/{PROJECT_NUMBER}/locations/{LOCATION}
```

**Regioni supportate:** `us`, `eu`, `global`

#### Endpoint Principali

| Metodo | Endpoint | Descrizione |
|--------|----------|-------------|
| POST | `/notebooks` | Crea un nuovo notebook |
| GET | `/notebooks/{notebookId}` | Recupera dettagli notebook |
| GET | `/notebooks` | Lista tutti i notebook |
| DELETE | `/notebooks/{notebookId}` | Elimina un notebook |
| POST | `/notebooks/{notebookId}:share` | Condividi notebook |
| POST | `/notebooks/{notebookId}/sources:batchCreate` | Aggiungi fonti in batch |
| GET | `/notebooks/{notebookId}/sources/{sourceId}` | Dettagli fonte |
| DELETE | `/notebooks/{notebookId}/sources/{sourceId}` | Rimuovi fonte |

#### Esempio: Creare un Notebook

```bash
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth print-access-token)" \
  -H "Content-Type: application/json" \
  "https://us-discoveryengine.googleapis.com/v1alpha/projects/123456/locations/us/notebooks" \
  -d '{"title": "Catalogo Fornitori Q1 2026"}'
```

#### Esempio: Aggiungere Fonti

```bash
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth print-access-token)" \
  -H "Content-Type: application/json" \
  "https://us-discoveryengine.googleapis.com/v1alpha/projects/123456/locations/us/notebooks/nb-001/sources:batchCreate" \
  -d '{"requests": [{"source": {"userContent": {"rawBytes": "BASE64_ENCODED", "mimeType": "application/pdf"}}}]}'
```

#### Rate Limits

- Non pubblicati ufficialmente — soggetti a quota Google Cloud
- Consigliato: implementare exponential backoff
- Monitorare via Google Cloud Console > Quotas

#### Error Codes

| Codice | Significato | Soluzione |
|--------|-------------|-----------|
| 401 | Token scaduto/invalido | Rinnovare con `gcloud auth print-access-token` |
| 403 | Permessi insufficienti | Verificare IAM roles (Discovery Engine Admin) |
| 404 | Notebook/Source non trovato | Verificare ID e location |
| 429 | Rate limit superato | Implementare backoff esponenziale |
| 500 | Errore server | Retry con backoff |

---

### API Unofficial — notebooklm-py (Python SDK)

#### Installazione

```bash
pip install notebooklm-py
pip install "notebooklm-py[browser]"
playwright install chromium
```

#### Autenticazione

```bash
notebooklm login
notebooklm auth check --test
```

Credenziali salvate in `~/.notebooklm/`

#### Operazioni Principali (CLI)

| Comando | Descrizione |
|---------|-------------|
| `notebooklm create "Nome"` | Crea notebook |
| `notebooklm list` | Lista notebook |
| `notebooklm rename ID "Nuovo Nome"` | Rinomina |
| `notebooklm delete ID` | Elimina |
| `notebooklm source add <url/file>` | Aggiungi fonte |
| `notebooklm source add-research "query"` | Ricerca web auto-import |
| `notebooklm source refresh` | Aggiorna fonti |
| `notebooklm ask "domanda"` | Interroga notebook |

#### Generazione Contenuti (CLI)

```bash
# Audio
notebooklm generate audio "istruzioni" --wait
notebooklm download audio ./podcast.mp3

# Video
notebooklm generate video --style whiteboard --wait
notebooklm download video ./overview.mp4

# Slide Deck
notebooklm generate slide-deck
notebooklm download slide-deck ./slides.pdf

# Quiz & Flashcards
notebooklm generate quiz --difficulty hard
notebooklm generate flashcards --quantity more

# Infographic, Mind Map, Data Table
notebooklm generate infographic --orientation portrait
notebooklm generate mind-map
notebooklm generate data-table "struttura"
```

#### Python API

```python
from notebooklm import NotebookLMClient

async with await NotebookLMClient.from_storage() as client:
    # Creare notebook
    nb = await client.notebooks.create("Catalogo Fornitori")

    # Aggiungere fonti
    await client.sources.add_url(nb.id, "https://esempio.com/catalogo.pdf", wait=True)
    await client.sources.add_file(nb.id, "./scheda_tecnica.pdf", wait=True)

    # Interrogare
    risposta = await client.chat.ask(nb.id, "Quali prodotti hanno margine >30%?")

    # Generare audio
    task = await client.artifacts.generate_audio(nb.id, "Crea un podcast riassuntivo")
    await client.artifacts.wait_for_completion(nb.id, task.id)
    await client.artifacts.download_audio(nb.id, "./podcast_fornitori.mp3")
```

#### Metodi Python API Completi

**Notebooks:** `create(name)`, `list()`, `delete(id)`, `rename(id, name)`

**Sources:** `add_url(nb_id, url)`, `add_file(nb_id, path)`, `refresh(nb_id, source_id)`, `get_fulltext(nb_id, source_id)`

**Chat:** `ask(nb_id, question)` con supporto cronologia conversazioni e persona custom

**Artifacts:**
- `generate_audio(nb_id, instructions)` — 4 formati (deep-dive, brief, critique, debate), 3 lunghezze, 50+ lingue
- `generate_video(nb_id, style)` — Explainer, brief, cinematic; 9 stili visivi
- `generate_slide_deck(nb_id)` — Output PDF o PPTX
- `generate_quiz(nb_id)` — Export JSON, Markdown, HTML
- `generate_flashcards(nb_id)` — Export JSON, Markdown, HTML
- `generate_infographic(nb_id)` — PNG
- `generate_mind_map(nb_id)` — JSON
- `generate_data_table(nb_id)` — CSV
- `generate_report(nb_id, template_type)` — Report personalizzati
- `wait_for_completion(nb_id, task_id)`
- Tutti i metodi `download_*` corrispondenti

**Sharing:** `create_public_link(nb_id)`, `create_private_link(nb_id)`, `add_user(nb_id, email, role)`

#### Fonti Supportate

URL, YouTube, PDF, testo, Markdown, Word (.docx), audio, video, immagini, Google Drive, testo incollato

#### ⚠️ Disclaimer

Libreria **unofficial** — usa endpoint Google non documentati. Soggetta a: rate limiting arbitrario, rottura senza preavviso, nessun supporto ufficiale. **NON usare per flussi production-critical senza fallback.**

---

## 🔌 MCP Server

### Opzione 1: notebooklm-mcp (Community — Browser Automation)

**Disponibilità:** Community — Progetto open-source attivamente mantenuto
**Repository:** [github.com/PleasePrompto/notebooklm-mcp](https://github.com/PleasePrompto/notebooklm-mcp)

#### Installazione

**Claude Code:**
```bash
claude mcp add notebooklm npx notebooklm-mcp@latest
```

**claude_desktop_config.json:**
```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "npx",
      "args": ["-y", "notebooklm-mcp@latest"]
    }
  }
}
```

#### Autenticazione

Dire a Claude: "Log me in to NotebookLM" → Chrome si apre → login Google → credenziali salvate localmente.

#### Profili e Tools

**Minimal (5 tools — solo lettura):**

| Tool | Descrizione |
|------|-------------|
| `ask_question` | Interroga NotebookLM |
| `get_health` | Verifica stato servizio |
| `list_notebooks` | Lista notebook |
| `select_notebook` | Imposta notebook attivo |
| `get_notebook` | Dettagli notebook |

**Standard (10 tools — + gestione libreria):**

| Tool | Descrizione |
|------|-------------|
| `setup_auth` | Inizializza autenticazione |
| `list_sessions` | Vedi sessioni attive |
| `add_notebook` | Salva notebook con metadata |
| `update_notebook` | Modifica info notebook |
| `search_notebooks` | Cerca per criteri |

**Full (16 tools — tutte le operazioni):**

| Tool | Descrizione |
|------|-------------|
| `cleanup_data` | Rimuovi tutti i dati |
| `re_auth` | Forza ri-autenticazione |
| `remove_notebook` | Elimina dalla libreria |
| `reset_session` | Pulisci stato sessione |
| `close_session` | Chiudi sessione attiva |
| `get_library_stats` | Statistiche utilizzo |

#### Configurazione

```bash
npx notebooklm-mcp config set profile minimal|standard|full
npx notebooklm-mcp config set disabled-tools "cleanup_data,re_auth"
```

**Environment:** `NOTEBOOKLM_PROFILE=standard`
**Persistenza:** `~/.config/notebooklm-mcp/settings.json`

#### Architettura

```
Task → AI Agent (Claude) → MCP Server → Browser Automation (Puppeteer/Chrome) → NotebookLM → Gemini 2.5 → Documenti
```

#### Requisiti

- Node.js + npx
- Chrome/Chromium
- Account Google con accesso NotebookLM

---

### Opzione 2: notebooklm_sdk (Enterprise — API Ufficiale)

**Disponibilità:** Community — SDK Python per NotebookLM Enterprise con server MCP integrato
**Repository:** [github.com/dandye/notebooklm_sdk](https://github.com/dandye/notebooklm_sdk)

#### Installazione

```bash
pip install -e .
```

#### claude_desktop_config.json

```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "notebooklm-mcp",
      "env": {
        "NOTEBOOKLM_PROJECT_NUMBER": "YOUR_PROJECT_NUMBER",
        "NOTEBOOKLM_LOCATION": "global"
      }
    }
  }
}
```

#### Autenticazione

Google Cloud ADC — richiede `gcloud auth application-default login`

#### Tools

- Crea notebook
- Upload documenti (PDF, testo)
- Verifica stato parsing
- Lista notebook

#### Quando usarlo

Solo con NotebookLM Enterprise su Google Cloud. Usa API ufficiali → più stabile e production-ready.

---

## 🚀 Padronanza Avanzata

### Use Cases per Vendiamonoi.it

#### 1. Knowledge Base Fornitori
Caricare cataloghi, schede tecniche, contratti di ogni fornitore in notebook dedicati. Interrogare con: "Quali prodotti di questo fornitore hanno EAN mancante?" o "Riassumi le condizioni di reso del contratto."

#### 2. Documentazione Marketplace
Creare un notebook per ogni marketplace (Amazon, Kaufland, Leroy Merlin ecc.) con le relative policy, guide seller, FAQ. Il team può interrogare rapidamente: "Quali sono i requisiti immagine per Kaufland?" con risposte citate.

#### 3. Formazione Team (Audio Overview)
Generare podcast formativi da documentazione interna: procedure operative, onboarding nuovi collaboratori, aggiornamenti policy. Il team può ascoltare mentre lavora.

#### 4. Analisi Competitiva
Caricare report di mercato, pricing competitor, trend di categoria. Interrogare: "Come si posizionano i prezzi del fornitore X rispetto al mercato?"

#### 5. Customer Service Knowledge
Notebook con tutte le FAQ, template risposte, policy reso per marketplace. Il CS può interrogare e ottenere risposte immediate con citazione della fonte.

#### 6. Compliance e Normative
Caricare normative OSS, regolamenti marketplace, GDPR, normative prodotto. Interrogare: "Quali obblighi IVA abbiamo per vendite in Germania?"

### Integrazioni con Stack Vendiamonoi

#### NotebookLM + Claude (via MCP)
Claude può interrogare direttamente i notebook NotebookLM per ottenere risposte grounded dalla documentazione aziendale. Setup: installare MCP server notebooklm-mcp.

#### NotebookLM + Make.com
**Scenario possibile:**
1. Webhook Make riceve nuovo catalogo fornitore
2. Make chiama notebooklm-py via HTTP (wrapper REST)
3. Crea notebook + upload catalogo
4. Interroga per estrarre dati strutturati
5. Output verso Supabase/Google Sheets

**⚠️ Richiede:** wrapper REST custom o NotebookLM Enterprise API

#### NotebookLM + Supabase
Salvare risposte strutturate da NotebookLM in tabelle Supabase per consultazione rapida e dashboard operative.

#### NotebookLM + Notion
Esportare riassunti e insight da NotebookLM come pagine Notion per documentazione permanente.

### Limitazioni Rilevanti

- **Nessuna API consumer ufficiale** — Google non ha rilasciato API per la versione gratuita/Plus
- **Enterprise API limitata** — Solo CRUD notebook/fonti, no chat/query via API (al momento)
- **Librerie unofficial fragili** — notebooklm-py usa browser automation, soggetta a rottura
- **Rate limits non documentati** — Nessun header X-RateLimit, nessuna documentazione ufficiale
- **Singolo documento max 200 MB / 500K parole**
- **Max 150.000 celle attive per foglio Excel**
- **Audio Overview non disponibili via API Enterprise** (solo UI o unofficial)
- **Nessun webhook nativo** — Non è possibile ricevere notifiche su eventi
- **Data residency limitata** — Solo US o EU multi-region per Enterprise

### Risorse Ufficiali

- [NotebookLM Web App](https://notebooklm.google.com)
- [NotebookLM Enterprise Overview](https://docs.cloud.google.com/gemini/enterprise/notebooklm-enterprise/docs/overview)
- [Enterprise API — Notebooks](https://docs.cloud.google.com/gemini/enterprise/notebooklm-enterprise/docs/api-notebooks)
- [Enterprise API — Sources](https://docs.cloud.google.com/gemini/enterprise/notebooklm-enterprise/docs/api-notebooks-sources)
- [Enterprise Setup Guide](https://docs.cloud.google.com/gemini/enterprise/notebooklm-enterprise/docs/set-up-notebooklm)
- [NotebookLM Plans & Pricing](https://notebooklm.google/plans)
- [NotebookLM FAQ](https://support.google.com/notebooklm/answer/16269187)
- [MCP Server — notebooklm-mcp (Community)](https://github.com/PleasePrompto/notebooklm-mcp)
- [notebooklm-py — Unofficial SDK](https://github.com/teng-lin/notebooklm-py)
- [notebooklm_sdk — Enterprise SDK](https://github.com/dandye/notebooklm_sdk)
- [Google AI Developer Forum](https://discuss.ai.google.dev/t/notebooklm-api/55950)

---

## Changelog

| Data | Versione | Modifiche | Autore |
|------|----------|-----------|--------|
| 31/03/2026 | 1.0 | Creazione documentazione completa: API Enterprise, API Unofficial, MCP Server (2 opzioni), SDK, Use Cases, Integrazioni Stack, Limitazioni | Claude (Cowork) |
