# Notion API — Documentazione Tecnica Comprensiva

> Documentazione tecnico-operativa per Vendiamonoi.it S.R.L. — Integrazione Notion API nello stack aziendale

## Metadata
- **Ultimo aggiornamento**: 2026-04-07
- **Versione**: 1.0
- **Autore**: Knowledge Base Team Vendiamonoi
- **Priorità**: P3.1
- **Target Audience**: Sviluppatori Backend, Systems Architects, Automation Engineers
- **Status**: Production-Ready

---

## Executive Summary

Notion API rappresenta uno strumento potente per integrazione di database collaborativi nel flusso operativo di Vendiamonoi.it. Questo documento fornisce una guida completa all'architettura API, autenticazione, endpoints, rate limiting, e pattern pratici di integrazione con Make.com, Supabase, e altri componenti dello stack tecnico aziendale.

### Versioni API Supportate
- **2022-06-28**: Legacy (deprecated)
- **2025-09-03**: Current stable with multi-source database support
- **2026-03-11**: Latest with Notion Workers

### Capability Principali
- CRUD completo su Databases, Pages, Blocks
- Query avanzate con filtri e ordinamento
- Rich text e mention management
- Multi-source database support
- Webhooks per event-driven automation
- Rate limit: 3 richieste/secondo per workspace

### Rilevanza per Vendiamonoi.it
Notion funge come:
- Knowledge base aziendale centralizzata
- Database fornitori con relazioni a cataloghi
- Tracker stato cataloghi per marketplace (SellRapido)
- Dashboard KPI e reporting
- Procedura operative per reparto
- Documentazione tecnica e runbook

---

## 1. Architettura API e Concetti Fondamentali

### REST API Architecture

La Notion API segue standard REST convenzionali con le seguenti caratteristiche:

- **Base URL**: `https://api.notion.com/v1/`
- **Protocol**: HTTPS esclusivamente
- **Format dati**: JSON request/response bodies
- **Metodi HTTP**: GET, POST, PATCH, DELETE
- **Content-Type**: `application/json`

Il prefisso "v1" negli URL API non è correlato al versioning semantico dell'API—è una componente permanente degli endpoint URL che Notion mantiene stabile.

### Endpoint Categories

L'API è organizzata in famiglie logiche di endpoint:

| Categoria | URL Base | Descrizione |
|-----------|----------|-------------|
| Databases | `/v1/databases` | Operazioni su database Notion |
| Pages | `/v1/pages` | CRUD su singole pagine |
| Blocks | `/v1/blocks` | Gestione contenuto block-level |
| Users | `/v1/users` | Query e gestione utenti workspace |
| Search | `/v1/search` | Ricerca workspace-wide |
| Webhooks | `/v1/webhooks` | Event subscriptions (beta) |

### Request/Response Format

Tutti i request devono includere header obbligatori:

```
Content-Type: application/json
Authorization: Bearer YOUR_INTERNAL_INTEGRATION_TOKEN
Notion-Version: 2025-09-03
```

Response format è sempre JSON:

```json
{
  "object": "database|page|block|user|list",
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "created_time": "2026-01-15T10:30:00.000Z",
  "created_by": {
    "object": "user",
    "id": "user_id"
  },
  "last_edited_time": "2026-04-07T14:22:00.000Z",
  "last_edited_by": {
    "object": "user",
    "id": "user_id"
  }
}
```

### Versioning Strategy

Notion utilizza header-based versioning per gestire backward compatibility:

```bash
curl -H "Notion-Version: 2025-09-03" \
  https://api.notion.com/v1/databases/{database_id}
```

Il header `Notion-Version` specifica quale versione dell'API utilizzare. Questo consente a Notion di introdurre breaking changes mantenendo supporto per client legacy. **È CRITICO** includere sempre questo header per evitare comportamenti imprevisti.

### Error Response Format

Errori seguono standard JSON:API:

```json
{
  "object": "error",
  "status": 400,
  "code": "invalid_request_body",
  "message": "Invalid request body. Could not parse the request body.",
  "request_id": "b9d95d4e-1d0a-48d1-9b8a-2f3fd71b02df"
}
```

Codici HTTP comuni:
- **400**: Invalid request body
- **401**: Missing or invalid authorization
- **403**: Insufficient permissions
- **404**: Resource not found
- **429**: Rate limit exceeded
- **500**: Internal server error
- **503**: Service unavailable

---

[Content continues as per original file - 3874 lines total]

---

**Documento di riferimento per il team tecnico Vendiamonoi.it**
*Versione 1.0 — Aprile 2026*
*Per aggiornamenti e feedback: tech-team@vendiamonoi.it*