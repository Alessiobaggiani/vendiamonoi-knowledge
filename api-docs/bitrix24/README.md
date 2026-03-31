# Bitrix24 — Documentazione Tecnica

> **Vendiamonoi.it S.R.L.** — Documentazione interna
> **Ultimo aggiornamento:** 31/03/2026
> **Autore:** Claude (Cowork)
> **Pagina Notion:** [Bitrix24](https://www.notion.so/287ab24b308680468bb2d5f8abef5867)

---

## Panoramica

Bitrix24 è il CRM e sistema di gestione aziendale utilizzato da Vendiamonoi.it per la gestione contatti fornitori, pipeline commerciali, task operativi, comunicazioni interne e automazione processi. L'API REST permette integrazione completa con Make.com, Supabase, Notion e altri strumenti dello stack aziendale.

**Rilevanza per Vendiamonoi.it:** CRM fornitori/clienti, gestione task per reparti (catalogo, marketplace, ordini, CS), comunicazione interna, pipeline commerciali per nuovi fornitori, automazione notifiche e workflow operativi.

### Note Critiche

- Bitrix24 REST API ha **rate limit di 2 richieste/secondo** — critico per operazioni bulk
- Due metodi di autenticazione: **OAuth 2.0** (per app marketplace) e **Webhook** (per integrazioni interne — consigliato)
- Token OAuth scadono dopo **30 minuti** — refresh token dura 1 mese
- **Webhook inbound** = chiamare API Bitrix24 da esterno; **Webhook outbound** = ricevere eventi da Bitrix24
- API copre: CRM, Task, Calendar, Drive, Chat, Telephony, Business Processes, E-commerce, e 30+ moduli
- 3 MCP Server disponibili: 1 ufficiale (documentazione), 1 community (operativo CRM), 1 alternativo
- SDK ufficiali per **PHP**, **JavaScript/TypeScript**, **Python**

---

## Piani e Limiti

### Free Plan
- Utenti illimitati / 5 GB storage / CRM base / API REST / Webhook

### Basic ($49/mese per 5 utenti)
- 24 GB storage / CRM completo / Online store

### Standard ($99/mese per 50 utenti)
- 100 GB storage / Marketing automation / Business processes

### Professional ($199/mese per 100 utenti)
- 1024 GB storage / Sales intelligence / BP designer avanzato

### Enterprise ($399/mese per 250 utenti)
- 3 TB storage / Multi-branch / HIPAA / Advanced telephony

**Rate Limit API:** 2 req/s per tutti i piani. Batch API (50 comandi) = 1 richiesta.

---

## 📡 API Documentation

### Autenticazione

#### 1. Inbound Webhook (Consigliato)

```
https://YOUR-DOMAIN.bitrix24.com/rest/USER_ID/WEBHOOK_CODE/
```

Path: Developer resources → Other → Inbound webhook. Il codice webhook è un access key — mantenerlo segreto.

#### 2. OAuth 2.0 (Per app marketplace)

Access token dura **30 minuti**, refresh token **1 mese**.

```bash
curl "https://oauth.bitrix.info/oauth/token/?grant_type=refresh_token&client_id=APP_ID&client_secret=APP_SECRET&refresh_token=REFRESH_TOKEN"
```

### Scopes (35+ Moduli)

| Scope | Modulo | Descrizione |
|-------|--------|-------------|
| `crm` | CRM | Contatti, aziende, deal, lead, preventivi, fatture |
| `task` | Tasks | Gestione task e progetti |
| `calendar` | Calendario | Eventi e calendario |
| `disk` | Bitrix24.Disk | File storage e documenti |
| `department` | Struttura | Dipartimenti e gerarchia |
| `im` | Chat | Chat e notifiche |
| `imbot` | Chat Bot | Chatbot |
| `imopenlines` | Open Lines | Canali comunicazione clienti |
| `telephony` | Telefonia | Sistemi telefonici |
| `user` | Utenti | Gestione utenti (brief/basic/full) |
| `catalog` | Catalogo | Prodotti e magazzino |
| `sale` | E-commerce | Ordini e vendite |
| `bizproc` | Business Processes | Automazioni, RPA, robot CRM |
| `lists` | Liste | Liste personalizzabili |
| `entity` | Data Storage | Storage dati custom |
| `delivery` | Spedizioni | Gestione consegne |
| `biconnector` | BI Analytics | Business Intelligence |
| `ai_admin` | Copilot | Servizi AI |
| `rpa` | RPA | Robotic Process Automation |
| `documentgenerator` | Documenti | Generazione documenti |

### Endpoint — CRM

#### Deals

| Metodo | Descrizione |
|--------|-------------|
| `crm.deal.add` | Crea nuova trattativa |
| `crm.deal.get` | Recupera trattativa per ID |
| `crm.deal.list` | Lista trattative con filtri |
| `crm.deal.update` | Aggiorna trattativa |
| `crm.deal.delete` | Elimina trattativa |
| `crm.deal.fields` | Schema campi |
| `crm.deal.contact.add` | Aggiungi contatto a trattativa |
| `crm.deal.productrows.set` | Imposta prodotti |

#### Contacts

| Metodo | Descrizione |
|--------|-------------|
| `crm.contact.add` | Crea contatto |
| `crm.contact.get` | Recupera per ID |
| `crm.contact.list` | Lista con filtri |
| `crm.contact.update` | Aggiorna |
| `crm.contact.delete` | Elimina |

#### Companies

| Metodo | Descrizione |
|--------|-------------|
| `crm.company.add` | Crea azienda |
| `crm.company.get` | Recupera per ID |
| `crm.company.list` | Lista con filtri |
| `crm.company.update` | Aggiorna |

#### Leads

| Metodo | Descrizione |
|--------|-------------|
| `crm.lead.add` | Crea lead |
| `crm.lead.get` | Recupera per ID |
| `crm.lead.list` | Lista con filtri |
| `crm.lead.update` | Aggiorna |

#### Activities

| Metodo | Descrizione |
|--------|-------------|
| `crm.activity.add` | Crea attività |
| `crm.activity.get` | Recupera |
| `crm.activity.list` | Lista |
| `crm.activity.update` | Aggiorna |

### Endpoint — Tasks

| Metodo | Descrizione |
|--------|-------------|
| `tasks.task.add` | Crea task |
| `tasks.task.get` | Recupera task |
| `tasks.task.list` | Lista task |
| `tasks.task.update` | Aggiorna task |
| `tasks.task.complete` | Completa task |
| `tasks.task.delegate` | Delega task |
| `task.comment.add` | Aggiungi commento |

### Endpoint — Calendar, Drive, Users

| Metodo | Descrizione |
|--------|-------------|
| `calendar.event.add` | Crea evento |
| `calendar.event.getlist` | Lista eventi |
| `disk.folder.getchildren` | Contenuto cartella |
| `disk.file.upload` | Carica file |
| `user.get` | Lista utenti |
| `user.current` | Utente corrente |
| `department.get` | Lista dipartimenti |

### Esempio: Creare un Deal

```bash
curl -X POST "https://vendiamonoi.bitrix24.com/rest/1/WEBHOOK_CODE/crm.deal.add.json" \
  -H "Content-Type: application/json" \
  -d '{"fields": {"TITLE": "Nuovo Fornitore - Elettronica SpA", "STAGE_ID": "NEW", "ASSIGNED_BY_ID": 1}}'
```

### Esempio: Batch Request (50 comandi)

```bash
curl -X POST "https://vendiamonoi.bitrix24.com/rest/1/WEBHOOK_CODE/batch.json" \
  -H "Content-Type: application/json" \
  -d '{"halt": 0, "cmd": {"deal": "crm.deal.get?ID=123", "contacts": "crm.deal.contact.items.get?ID=123"}}'
```

### Rate Limits

- **2 req/s** per webhook/app
- **Batch API:** 50 comandi = 1 richiesta
- **Paginazione:** 50 record, campo `start` per offset
- HTTP **200** con `QUERY_LIMIT_EXCEEDED` se superato

### Error Codes

| Errore | Significato | Soluzione |
|--------|-------------|-----------|
| `QUERY_LIMIT_EXCEEDED` | Rate limit | Backoff esponenziale |
| `NO_AUTH_FOUND` | Token invalido | Verificare webhook URL |
| `INVALID_TOKEN` | OAuth scaduto | Refresh token |
| `ACCESS_DENIED` | Permessi insufficienti | Verificare scopes |
| `ERROR_METHOD_NOT_FOUND` | Metodo non esiste | Verificare nome |

> **Nota:** Bitrix24 restituisce HTTP 200 anche per errori — codice errore nel body JSON.

---

## 🔔 Webhook

### Outbound (Ricevere eventi da Bitrix24)

Path: Developer resources → Other → Outbound webhook → Handler URL + Eventi

#### CRM Events

| Evento | Descrizione |
|--------|-------------|
| `ONCRMDEALADD` | Nuova trattativa |
| `ONCRMDEALUPDATE` | Trattativa aggiornata |
| `ONCRMDEALDELETE` | Trattativa eliminata |
| `ONCRMCONTACTADD` | Nuovo contatto |
| `ONCRMCONTACTUPDATE` | Contatto aggiornato |
| `ONCRMCOMPANYADD` | Nuova azienda |
| `ONCRMLEADADD` | Nuovo lead |
| `ONCRMLEADUPDATE` | Lead aggiornato |
| `ONCRMACTIVITYADD` | Nuova attività |

#### Task Events

| Evento | Descrizione |
|--------|-------------|
| `ONTASKADD` | Nuovo task |
| `ONTASKUPDATE` | Task aggiornato |
| `ONTASKDELETE` | Task eliminato |
| `ONTASKCOMMENTADD` | Nuovo commento |

#### Payload

```json
{
  "event": "ONCRMDEALUPDATE",
  "data": {"FIELDS": {"ID": "456"}},
  "auth": {
    "domain": "vendiamonoi.bitrix24.com",
    "application_token": "APP_TOKEN"
  }
}
```

> Payload contiene solo ID — fetch API per dati completi.

---

## 🔌 MCP Server

### Opzione 1: Bitrix MCP REST Doc (Ufficiale)

**URL:** `https://mcp-dev.bitrix24.com/mcp` — HTTP hosted, zero setup
**Repository:** [github.com/bitrix24/mcp-rest-doc](https://github.com/bitrix24/mcp-rest-doc)

```json
{"mcpServers": {"bitrix-mcp-rest": {"type": "http", "url": "https://mcp-dev.bitrix24.com/mcp"}}}
```

| Tool | Descrizione |
|------|-------------|
| `bitrix-search` | Ricerca semantica metodi e docs |
| `bitrix-method-details` | Doc metodo specifico |
| `bitrix-article-details` | Articoli documentazione |
| `bitrix-event-details` | Doc eventi webhook |

### Opzione 2: bitrix24-mcp-server (Community CRM)

**Repository:** [github.com/gunnit/bitrix24-mcp-server](https://github.com/gunnit/bitrix24-mcp-server)
**Tipo:** Node.js locale — 40+ tools operativi

```json
{"mcpServers": {"bitrix24": {"command": "node", "args": ["/path/to/build/index.js"], "env": {"BITRIX24_WEBHOOK_URL": "https://vendiamonoi.bitrix24.com/rest/1/CODE/"}}}}
```

**Tools:** CRUD contatti/deal/lead/aziende/task, analytics, monitoring, search CRM, pipeline tracking, sales reports, team dashboard, forecast.

### Opzione 3: bitrix24-mcp (Python)

**Repository:** [github.com/kartochka/bitrix24-mcp](https://github.com/kartochka/bitrix24-mcp)

---

## 📦 SDK e Librerie

### PHP SDK (Ufficiale) — b24phpsdk

```bash
composer require bitrix24/b24phpsdk
```

OAuth + Webhook, rinnovo automatico token, offline queues, PHP 8+.

```php
use Bitrix24\SDK\Services\ServiceBuilderFactory;
$sb = ServiceBuilderFactory::createServiceBuilderFromWebhook('https://vendiamonoi.bitrix24.com/rest/1/CODE/');
$deals = $sb->getCRMScope()->deal()->list([], ['TITLE', 'STAGE_ID'], ['DATE_CREATE' => 'DESC'], 0);
```

### JavaScript/TypeScript SDK (Ufficiale) — b24jssdk

```bash
npm install @bitrix24/b24jssdk
```

v1.0.5 (marzo 2026), Browser + Node.js, TypeScript 68%, ESM/UMD.

```javascript
import { B24Js } from '@bitrix24/b24jssdk';
const b24 = new B24Js({ domain: 'vendiamonoi.bitrix24.com', userId: 1, secret: 'CODE' });
const deals = await b24.callMethod('crm.deal.list', { filter: { STAGE_ID: 'WON' } });
```

### Python SDK (Ufficiale) — b24pysdk

```bash
pip install b24pysdk
```

v0.4.0 (gennaio 2026), Python 3.9+, Beta. Webhook + OAuth, typing, batch ops.

```python
from b24pysdk import BitrixWebhook, Client
token = BitrixWebhook(domain="vendiamonoi.bitrix24.com", auth_token="1/CODE")
client = Client(token)
deals = client.crm.deal.list(filter={"STAGE_ID": "WON"})
new_deal = client.crm.deal.add(fields={"TITLE": "Nuovo Fornitore", "STAGE_ID": "NEW"})
batch = client.call_batch({"d1": client.crm.deal.get(bitrix_id=1), "d2": client.crm.deal.get(bitrix_id=2)})
```

### Community: `bitrix24-rest` (pip, webhook only), `pybitrix24` (zero deps), `CRest` (PHP minimale)

---

## 🚀 Padronanza Avanzata

### Use Cases Vendiamonoi.it

1. **Pipeline Fornitori** — CRM pipeline: Lead → Contatto → Contratto → Firma → Integrazione → Attivo
2. **Task per Reparti** — Gruppi Bitrix24 per Catalogo, Marketplace, Ordini, CS. Task automatici via API
3. **Onboarding Fornitore** — Nuovo CRM → Make.com → GDrive + Notion + Chat + Task
4. **Dashboard Performance** — API user + tasks + activities → Supabase → Dashboard KPI
5. **CS Centralizzato** — Open Lines → Attività CRM → Tracking risoluzioni
6. **Notifiche Intelligenti** — Webhook + Make.com con condizioni per notifiche mirate

### Integrazioni Stack

- **Bitrix24 + Make.com** — Trigger Watch Events, Azioni CRM/Task/Notification
- **Bitrix24 + Supabase** — Sync CRM → Supabase per analytics via Make.com
- **Bitrix24 + Notion** — CRM master → Notion knowledge base via Make.com
- **Bitrix24 + Claude (MCP)** — Doc server (ufficiale) + CRM server (community)
- **Bitrix24 + SellRapido** — Ordini marketplace → attività CRM

### Limitazioni

- Rate limit 2 req/s — restrittivo per bulk
- Batch max 50 comandi
- Paginazione max 50 record
- Token OAuth scade 30 min
- HTTP 200 anche per errori
- Webhook payload minimale (solo ID)
- Python SDK beta (v0.4.0)
- Campi custom prefisso `UF_CRM_*`

### Risorse Ufficiali

- [API Documentation](https://apidocs.bitrix24.com/)
- [Training REST Help](https://training.bitrix24.com/rest_help/)
- [Developer Hub GitHub](https://github.com/bitrix24/bitrix24-dev-hub)
- [MCP REST Doc](https://github.com/bitrix24/mcp-rest-doc)
- [PHP SDK](https://github.com/bitrix24/b24phpsdk)
- [JS SDK](https://github.com/bitrix24/b24jssdk)
- [Python SDK](https://github.com/bitrix24/b24pysdk)
- [MCP Server Community](https://github.com/gunnit/bitrix24-mcp-server)
- [Scopes](https://apidocs.bitrix24.com/api-reference/scopes/permissions.html)

---

## Changelog

| Data | Versione | Modifiche | Autore |
|------|----------|-----------|--------|
| 31/03/2026 | 1.0 | Documentazione completa: API REST, Webhook, MCP (3 opzioni), SDK (PHP+JS+Python), Use Cases, Integrazioni | Claude (Cowork) |
