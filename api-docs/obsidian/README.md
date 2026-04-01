# Obsidian — Documentazione Tecnica Completa

## Informazioni Generali

| Campo | Dettaglio |
|---|---|
| **Software** | Obsidian |
| **Categoria** | Knowledge Management / Note-Taking / Personal Knowledge Base / Markdown Editor |
| **Sito ufficiale** | https://obsidian.md |
| **Reparto** | Tutti i reparti — Knowledge Base, Documentazione, Strategia, Operazioni |
| **Responsabile** | Alessio Baggiani |
| **Data documentazione** | 2026-04-01 |
| **Stato** | ✅ Attivo |
| **Piano** | Free + Sync ($4/utente/mese) + Commercial License ($50/utente/anno) |
| **Licenza** | Gratuito per uso personale — Commercial License per uso aziendale |

---

## 1. Panoramica e Posizionamento Strategico

### Cos'è Obsidian

Obsidian è un'applicazione di knowledge management e note-taking basata su file Markdown locali, sviluppata da Shida Li e Erica Xu (precedentemente fondatori di Dynalist). Lanciata in beta pubblica il 13 marzo 2020 e uscita dalla beta a ottobre 2022, Obsidian è un'applicazione **local-first**: tutti i dati sono memorizzati come file .md sul dispositivo dell'utente, senza cloud obbligatorio.

**Numeri chiave:**
- 1.5+ milioni di utenti attivi (2026)
- 22% crescita anno su anno
- 4.3 milioni di download su Android
- 2000+ plugin community
- 10.000+ organizzazioni con licenza commerciale
- ~$25M ARR stimato
- 100% finanziata dagli utenti (zero venture capital)
- Sede: Canada

### Perché Obsidian per Vendiamonoi.it

Per un'azienda di distribuzione digitale multi-marketplace come Vendiamonoi.it, Obsidian è strategico per:

1. **Knowledge Base locale e privata** — Documentazione tecnica, procedure, architettura di sistema in file Markdown versionabili, indipendenti dal cloud
2. **Vault aziendale sincronizzato** — Obsidian Sync con crittografia E2E per condividere knowledge base tra dispositivi e team
3. **Documentazione tecnica software** — Schede tecniche di tutti i tool aziendali (questo stesso progetto), facilmente ricercabili e cross-linkate
4. **Second Brain aziendale** — Collegare conoscenze tra reparti (Fornitori, Marketplace, Catalogo) tramite link bidirezionali e graph view
5. **Integrazione con il tech stack** — File .md compatibili con GitHub, sincronizzabili via automazioni Make, consultabili offline
6. **Zero vendor lock-in** — File Markdown puri, nessuna dipendenza da piattaforme proprietarie

### Posizionamento nel Tech Stack Vendiamonoi

```
LIVELLO KNOWLEDGE MANAGEMENT / DOCUMENTAZIONE
┌─────────────────────────────────────────────────┐
│  OBSIDIAN                                        │
│  • Documentazione tecnica software               │
│  • Knowledge base locale                         │
│  • Note operative e procedure                    │
│  • Architettura e diagrammi (Canvas)             │
│  • Second brain aziendale                        │
└──────────────────┬──────────────────────────────┘
                   │ Complementare a
                   ▼
┌─────────────────────────────────────────────────┐
│  NOTION — Knowledge Base collaborativa (cloud)   │
│  GITHUB — Versionamento documentazione           │
│  MIRO — Visual thinking e diagrammi              │
│  NOTEBOOKLM — AI-powered research               │
└─────────────────────────────────────────────────┘
```

---

## 2. Piani e Pricing Dettagliato

### Struttura Prezzi (Aggiornato 2026)

| Componente | Prezzo | Tipo | Note |
|---|---|---|---|
| **Obsidian (Core)** | Gratuito | Per sempre | Nessuna registrazione richiesta |
| **Obsidian Sync** | $4/utente/mese | Annuale | Sync E2E encrypted tra dispositivi |
| **Obsidian Publish** | $8/sito/mese | Annuale | Pubblica note come sito web |
| **Catalyst License** | $25 una tantum | Lifetime | Accesso beta, badge VIP Discord |
| **Commercial License** | $50/utente/anno | Annuale | Obbligatoria per uso aziendale |

### Obsidian Sync — Dettagli

| Parametro | Limite |
|---|---|
| Vault sincronizzabili | 10 per utente |
| Storage | Fino a 100GB (espandibile) |
| Dimensione max file | 100-200MB |
| Crittografia | AES-256 end-to-end |
| Selezione server region | Sì |
| Version history | Sì (rollback per ogni nota) |
| Shared vault | Sì (collaborazione) |

---

## 3. Funzionalità Core

### 3.1 Markdown Editor (Live Preview)

Formattazione supportata: Headers (H1-H6), bold/italic/strikethrough/highlight, liste, checklist, code blocks (150+ linguaggi), tabelle, citazioni, link/immagini, footnotes, LaTeX, Mermaid diagrams, callout blocks.

Tipi callout nativi: note, abstract, info, tip, success, question, warning, failure, danger, bug, example, quote.

### 3.2 Linking e Backlinks

- `[[Nome Nota]]` — Link a nota
- `[[Nome Nota|Alias]]` — Link con alias
- `[[Nome Nota#Heading]]` — Link a heading
- `[[Nome Nota^block-id]]` — Link a blocco
- `![[Nome Nota]]` — Transclusion (embed)
- Backlinks automatici con contesto
- Unlinked mentions

### 3.3 Graph View

Visualizzazione interattiva connessioni. Filtri per tag/cartelle, colorazione gruppi, local graph e global graph.

### 3.4 Canvas

Workspace infinito: drag-and-drop note/immagini/PDF, card testo, gruppi colorati, connessioni, canvas annidati.

### 3.5 Properties (YAML Frontmatter)

Metadati strutturati: testo, numeri, date, checkbox, liste, tag. Interrogabili con Dataview.

### 3.6 Tags Gerarchici

`#reparto/marketplace`, `#tipo/software`, `#stato/attivo`

### 3.7 Templates e Daily Notes

Core Templates + Templater (avanzato con JS). Daily Notes con naming `YYYY-MM-DD.md`.

### 3.8 Search e Quick Switcher

Full-text, regex, operatori, frontmatter search. Quick Switcher `Ctrl+O`, Command Palette `Ctrl+P`.

---

## 4. Plugin Ecosystem (2000+)

### Core Plugins

Daily Notes, Templates, Graph View, Outline, Search, File Recovery, Command Palette, Backlinks, Canvas, Bookmarks.

### Plugin Essenziali per Vendiamonoi

| Plugin | Uso |
|---|---|
| **Dataview** | Query SQL-like, dashboard dinamiche |
| **Templater** | Template avanzati con JavaScript |
| **Obsidian Git** | Auto-commit/push su GitHub |
| **Calendar** | Navigazione daily notes |
| **Excalidraw** | Disegni e diagrammi |
| **Advanced Tables** | Tabelle potenziate |
| **Kanban** | Board stile Trello |
| **Post Webhook** | Integrazione Make/Zapier |
| **Local REST API** | API per automazioni esterne |

### Plugin API (TypeScript)

Vault API, event registration, custom views/commands, settings tabs, ribbon icons, modal dialogs.

---

## 5. Obsidian Sync

### Crittografia E2E

- **Algoritmo:** AES-256
- **Dove:** Solo sul dispositivo utente
- **Server:** Zero-knowledge
- **Password recovery:** NON possibile
- **Audit:** Terze parti indipendenti

### Shared Vaults

Collaborazione su stesso vault, version history, diff, rollback.

### Sync vs Alternative

| Feature | Obsidian Sync | iCloud | Git | Syncthing |
|---|---|---|---|---|
| E2E Encryption | ✅ AES-256 | ✅ (Apple) | ❌ | ✅ |
| Version History | ✅ | ❌ | ✅ | ✅ |
| Conflict Resolution | ✅ Auto | ⚠️ | ⚠️ Manuale | ✅ |
| Mobile | ✅ | ✅ (Apple) | ⚠️ | ⚠️ |
| Costo | $4/mese | Gratuito | Gratuito | Gratuito |

---

## 6. Obsidian Publish

Note → sito web statico. Pubblicazione selettiva, dominio custom, graph view, ricerca full-text, temi. $8/sito/mese.

---

## 7. Integrazioni

### Obsidian ↔ GitHub

Plugin Obsidian Git: auto-commit, push/pull. Vault = repo Git.

### Obsidian ↔ Make

Plugin Post Webhook → scenario Make. Sync bidirezionale Obsidian ↔ Notion via Make.

### Obsidian ↔ Notion

Nessuna integrazione diretta. Triple-publish: Notion + Obsidian + GitHub.

### Web Clipper

Extension browser ufficiale: pagine web → vault Markdown.

### URI Scheme

`obsidian://open?vault=Vendiamonoi.it&file=path`

### Local REST API

GET/PUT/POST/DELETE su note via HTTP. Integrazione Make HTTP module.

---

## 8. Sicurezza e Privacy

**Local-first:** Dati locali, nessun cloud obbligatorio, offline-first.
**Privacy:** Zero telemetria, zero analytics.
**Sync:** AES-256 E2E, zero-knowledge server.

**Raccomandazioni Vendiamonoi:**
1. Mai credenziali nel vault → Bitwarden
2. Sync con password E2E forte
3. Git backup su repo privato
4. Solo plugin verificati
5. Proteggere accesso dispositivo

---

## 9. Piattaforme

| Piattaforma | Disponibilità |
|---|---|
| Windows (10/11) | ✅ |
| macOS (Intel + Apple Silicon) | ✅ |
| Linux (x64, ARM) | ✅ |
| iOS (iPhone + iPad) | ✅ |
| Android (5.0+) | ✅ |

---

## 10. Performance e Limiti

| Parametro | Limite |
|---|---|
| Dimensione vault | Nessun limite (testato 281K note) |
| Dimensione file | Consigliato < 1MB |
| Sync max file | 100-200MB |
| Sync storage | Fino a 100GB |
| Sync vault | Max 10 |

---

## 11. Struttura Vault Vendiamonoi.it

```
📁 Vendiamonoi.it (Vault)
├── 📥 00 - Inbox
├── 📋 01 - Progetti
├── 🏢 02 - Reparti
├── 📚 03 - Risorse (🔧 Tool e Software)
├── 📅 04 - Daily Notes
├── 🗄️ 05 - Archivio
├── 📎 06 - Allegati
└── ⚙️ 07 - Template
```

---

## 12. Confronto Alternative

| Feature | Obsidian | Notion | Logseq | Roam |
|---|---|---|---|---|
| Architettura | Local-first | Cloud-first | Local-first | Cloud-only |
| File format | Markdown | Proprietario | Markdown | Proprietario |
| Vendor lock-in | ❌ Nessuno | ⚠️ | ❌ Nessuno | ✅ Alto |
| Offline | ✅ | ⚠️ | ✅ | ❌ |
| Plugin | 2000+ | Limitato | 200+ | Limitato |
| Graph view | ⭐⭐⭐⭐⭐ | ❌ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Prezzo | Gratuito | $8-15/mese | Gratuito | $15/mese |

---

## 13. Enterprise

Commercial License $50/utente/anno. 10.000+ organizzazioni (Amazon, Capital One, Meta, Shopify, UK Government).

Limitazioni: No permessi granulari, no audit log, no SSO/SAML, no admin panel.

---

## 14. Troubleshooting

| Problema | Causa | Soluzione |
|---|---|---|
| Sync non funziona | Connessione/config | Verificare account, riavviare |
| Plugin non carica | Incompatibilità | Aggiornare plugin e Obsidian |
| Vault lento | Troppi plugin | Disattivare non usati |
| Graph lenta | Vault >50K note | Usare local graph |
| Dataview lento | Query broad | Aggiungere WHERE, LIMIT |

---

## 15. Risorse

| Risorsa | URL |
|---|---|
| Sito ufficiale | https://obsidian.md |
| Help Center | https://help.obsidian.md |
| Developer Docs | https://docs.obsidian.md |
| Forum | https://forum.obsidian.md |
| Community | https://obsidian.md/community |
| Changelog | https://obsidian.md/changelog |
| Plugin Directory | https://obsidian.md/plugins |
| Enterprise | https://obsidian.md/enterprise |
| GitHub | https://github.com/obsidianmd |

---

## Changelog

| Data | Versione | Modifiche | Autore |
|---|---|---|---|
| 2026-04-01 | 1.0 | Creazione documentazione completa | Claude AI / Alessio Baggiani |