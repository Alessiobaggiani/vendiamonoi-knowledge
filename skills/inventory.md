# Inventario MCP e Skill — Vendiamonoi OS

**Data:** 15 Aprile 2026
**Compilato da:** Claude Code (Cursor)

---

## MCP Attivi

### MCP Locali (configurati in `.claude.json`)

| Nome | Comando | Cosa fa | Scope |
|------|---------|---------|-------|
| `github` | MCP cloud claude.ai | Accesso completo GitHub: repo, PR, issue, commit, branch | Globale |
| `notebooklm` | `/Users/alessiobaggiani/.local/bin/notebooklm-mcp` | Integrazione NotebookLM: notebook, query, sessioni | Progetto |
| `obsidian-vault` | `npx mcp-remote http://localhost:22360/sse` | Lettura/scrittura vault Obsidian locale (richiede server locale attivo) | Progetto |
| `context7` | `npx -y @upstash/context7-mcp@latest` | Documentazione aggiornata in tempo reale per librerie e framework | Progetto |
| `qmd-vault` | `npx @tobilu/qmd serve` | Accesso vault Obsidian CartellaPRO/Vendiamonoi.it | Progetto |

### MCP Cloud (connessi via claude.ai)

| Nome | Cosa fa | Stato |
|------|---------|-------|
| `GitHub` | Lettura/scrittura repository, PR, issue | Attivo ✅ |
| `Notion` | Lettura/scrittura workspace Notion | Attivo ✅ |
| `Miro` | Lettura/scrittura board Miro (richiede URL board) | Attivo ✅ |
| `Gmail` | Lettura/scrittura email Gmail | Attivo ✅ |
| `Make` | Gestione scenari Make.com | Attivo ✅ |
| `Supabase` | Gestione progetti Supabase, SQL, Edge Functions | Attivo ✅ |
| `NotebookLM` | Notebook LM via MCP cloud | Attivo ✅ |
| `Google Calendar` | Calendario Google | Attivo ✅ |
| `ClickUp` | Gestione task e progetti ClickUp | Attivo ✅ |

---

## Skill Attive

### Skill Globali (`~/.claude/skills/`)

| Nome | Posizione file | Cosa fa |
|------|----------------|---------|
| `owasp-security` | `~/.claude/skills/owasp-security/SKILL.md` | Security review: OWASP Top 10:2025, ASVS 5.0, Agentic AI security. Si attiva quando si revisionano vulnerabilità, autenticazione, input utente |

### Skill di Progetto (`.claude/commands/` — Vendiamonoi OS)

| Nome | Posizione file | Cosa fa |
|------|----------------|---------|
| `refine-expert` | `/Users/alessiobaggiani/Documents/Claude/.claude/commands/refine-expert.md` | Esperto Refine.dev con stack Supabase + Ant Design + TypeScript per Vendiamonoi OS |

### Skill Scheduled (`/Users/alessiobaggiani/Documents/Claude/Scheduled/`)

| Nome | Posizione file | Cosa fa |
|------|----------------|---------|
| `kb-staleness-check` | `Scheduled/kb-staleness-check/SKILL.md` | Controllo freschezza knowledge base |
| `stack-changelog-monitor` | `Scheduled/stack-changelog-monitor/SKILL.md` | Monitoraggio changelog stack tecnologico |

### Skill Obsidian (`.claude/skills/` — vault Vendiamonoi.it)

| Nome | Posizione file | Cosa fa |
|------|----------------|---------|
| `weekly-review` | `/Applications/Obsidian - CartellaPRO/Vendiamonoi.it/.claude/skills/weekly-review/SKILL.md` | Review settimanale progetto |
| `daily-note` | `/Applications/Obsidian - CartellaPRO/Vendiamonoi.it/.claude/skills/daily-note/SKILL.md` | Gestione note giornaliere |

---

## Note e Problemi

| Elemento | Stato | Note |
|----------|-------|------|
| Context7 MCP | ✅ Installato | `claude mcp add context7 -- npx -y @upstash/context7-mcp@latest` |
| OWASP Security skill | ✅ Installata | Scaricata da `agamm/claude-code-owasp`, path corretto: `.claude/skills/owasp-security/SKILL.md` |
| Frontend Design skill (Anthropic) | ❌ Non trovata | Il repo `anthropics/claude-code-skills` non esiste. Skill non disponibile al 15/04/2026 |
| `obsidian-vault` MCP | ⚠️ Condizionale | Richiede server locale attivo su `localhost:22360` |
| Refine Expert skill | ✅ Verificata | Presente in `.claude/commands/refine-expert.md` del progetto |

---

*Generato da Claude Code — sessione Cursor*
