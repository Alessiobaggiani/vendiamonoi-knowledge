---
tags: [spediamopro, shipping, api, etichette, corrieri, vendiamonoi]
source: vendiamonoi-knowledge/integrations/spediamopro-api-v2.md
created: 2026-04-16
type: api-reference
---

# SpediamoPro API v2 — Riferimento Tecnico

> Versione API: 2.0.0 (rilascio 2026-02-xx, work in progress)  
> Documento creato: 2026-04-16  
> Uso previsto: Phase 4 — Etichette Spedizione (Vendiamonoi OS)

---

## Panoramica

SpediamoPro API v2 permette di gestire l'intero ciclo di vita delle spedizioni:
- Confronto prezzi tra corrieri (quotazioni)
- Creazione spedizioni e generazione etichette (PDF/ZPL/GIF)
- Tracking in tempo reale
- Gestione ritiri (pickup)
- Ricerca punti PUDO

**Corrieri supportati:** BRT, SDA Express, UPS, InPost  
**NON supportati:** GLS, DHL → usare Sendcloud per questi.

---

## Server

| Ambiente | URL base |
|---|---|
| **Produzione** | `https://core.spediamopro.com/api/v2` |
| **Staging (test)** | `https://core.spediamopro.it/api/v2` |

> Testare sempre su staging prima di andare in produzione.

---

## Autenticazione

### Tipo: OAuth 2.0 Client Credentials via Authcode

**Step 1 — Ottenere il token**

```http
POST /api/v2/auth/token
Content-Type: application/x-www-form-urlencoded
Authorization: Basic <base64(authcode:)>

grant_type=client_credentials
```

Equivalente cURL:
```bash
curl -X POST https://core.spediamopro.com/api/v2/auth/token \
  -u "<authcode>:" \
  -d "grant_type=client_credentials"
```

**Risposta:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

**Step 2 — Usare il token in ogni richiesta:**
```http
Authorization: Bearer <access_token>
```

> Il token scade dopo **3600 secondi (1 ora)**. Rinnovarlo prima della scadenza.

**Dove trovare l'Authcode:** Profile icon → "Authcode" nell'app SpediamoPro web.

---

## Rate Limiting

| Parametro | Valore |
|---|---|
| Limite | **120 richieste/minuto** per utente |
| Algoritmo | Sliding window (60 secondi mobili) |
| Risposta superamento | `429 Too Many Requests` |
| Header utili | `X-RateLimit-Remaining`, `X-RateLimit-Reset`, `Retry-After` |

---

## Unità di Misura — IMPORTANTE

API v2 usa **interi** (non float come v1):

| Grandezza | Unità v2 | Esempio |
|---|---|---|
| Dimensioni collo | **millimetri interi** | `300` = 30 cm |
| Peso collo | **grammi interi** | `1500` = 1,5 kg |
| Prezzi/importi | **centesimi di Euro interi** | `895` = €8,95 |
| Aliquote IVA | **centesimi di percentuale** | `2200` = 22% |

---

## Workflow Principale: Quotazione → Etichetta

```
1. POST /quotations            → lista prezzi corrieri
2. (scegli quotazione)
3. POST /documents             → solo se spedizione UPS extra-UE
4. POST /quotations/accept     → crea spedizione, ottieni shipment.id
5. GET  /shipments/{id}/labels → download PDF etichetta
6. (stampa/invia PDF al fornitore)
```

---

## Endpoint di Riferimento

### 1. `POST /quotations` — Richiedi preventivi

**Request body:**
```json
{
  "parcels": [
    {
      "weightGrams": 1500,
      "lengthMm": 300,
      "widthMm": 200,
      "heightMm": 150
    }
  ],
  "sender": {
    "countryCode": "IT",
    "postalCode": "20100",
    "city": "Milano",
    "province": "MI"
  },
  "consignee": {
    "countryCode": "IT",
    "postalCode": "00100",
    "city": "Roma",
    "province": "RM"
  },
  "cashOnDeliveryAmountCents": 0,
  "insuredValueCents": 0
}
```

**Risposta:** array di `Quotation`
```json
{
  "data": [
    {
      "id": "quot_abc123",
      "courier": "brt",
      "service": "BRT Express",
      "totalPriceCents": 895,
      "expectedDeliveryDate": "2026-04-18",
      "labelFormats": [0, 2]
    }
  ]
}
```

Note: Province obbligatoria per Italia e Canada. State obbligatorio per USA.

---

### 2. `POST /quotations/accept` — Accetta e crea spedizione

**Request body:**
```json
{
  "parcels": [
    {
      "weightGrams": 1500,
      "lengthMm": 300,
      "widthMm": 200,
      "heightMm": 150
    }
  ],
  "sender": {
    "countryCode": "IT",
    "postalCode": "20100",
    "city": "Milano",
    "province": "MI",
    "address1": "Via Roma 1",
    "name": "Vendiamonoi SRL",
    "phone": "+39021234567",
    "email": "spedizioni@vendiamonoi.it"
  },
  "consignee": {
    "countryCode": "IT",
    "postalCode": "00100",
    "city": "Roma",
    "province": "RM",
    "address1": "Via Garibaldi 5",
    "name": "Mario Rossi",
    "phone": "+393331234567",
    "email": "mario@example.it"
  },
  "quotationId": "quot_abc123",
  "courier": "brt",
  "service": "BRT Express",
  "totalPriceCents": 895,
  "labelFormat": 0,
  "externalId": "ORDER-12345",
  "reference": "Ordine #12345 - Fornitore Rossi"
}
```

**Risposta:** oggetto `Shipment` con `id`

> **CRITICO:** Il prezzo viene ri-verificato in tempo reale. Se aumentato, la spedizione NON viene creata.

---

### 3. `GET /shipments/{id}/labels` — Download etichetta

**Risposta:** file binario (PDF/ZPL/GIF)

Header risposta:
- `Content-Type: application/pdf`
- `X-Filename: label_12345.pdf`

---

### 4. `GET /shipments/{id}/tracking` — Tracking

**Risposta:**
```json
{
  "data": [
    {
      "timestamp": "2026-04-17T09:30:00Z",
      "status": "in_transit",
      "description": "Pacco in transito verso Milano",
      "location": "Roma CRS"
    }
  ]
}
```

---

### 5. `POST /pickups` — Pianifica ritiro corriere

### 6. `POST /pickups/simulate` — Simula ritiro (date disponibili)

### 7. `POST /documents` — Upload documento (solo UPS extra-UE)

```http
POST /documents?filename=invoice.pdf&type=1
[binary body]
```

Tipi documento: `1`=Fattura, `2`=Pro-forma, `3`=Dichiarazione libera esportazione, `4`=Altro.

---

## Formati Etichetta

| Codice | Formato | Corrieri |
|---|---|---|
| `0` | PDF A4 | BRT, SDA, InPost — **usare per email fornitore** |
| `1` | GIF | UPS only |
| `2` | ZPL | Tutti |
| `3` | PDF 10x11cm | InPost only |

---

## Corrieri Disponibili

| Codice | Nome |
|---|---|
| `brt` | BRT S.p.A. |
| `sda` | SDA Express Courier S.p.A. |
| `ups` | United Parcel Service of America |
| `inpost` | Locker InPost Italia S.r.l. |

> GLS e DHL NON sono su SpediamoPro → usare Sendcloud

---

## Gestione Errori

```json
{
  "error": {
    "id": "err_4f3d2c1b...",
    "status": 422,
    "source": "client",
    "message": "Validation failed.",
    "details": [
      { "source": "email", "message": "Email non valida." }
    ]
  }
}
```

**Valori `source`:** `client` | `server` | `external`

**Quick handling:**
1. Status `204` → successo senza dati
2. `Content-Type` != `application/json` → file binario, leggi `X-Filename`
3. JSON con `data` → successo
4. JSON con `error` → errore

---

## Documenti Export UPS Extra-UE

Prima di `POST /quotations/accept` con UPS verso paesi non-UE, caricare:

| Tipo | Codice | Obbligatorio |
|---|---|---|
| Fattura commerciale (con HS codes) | `1` | Sempre |
| Fattura pro-forma | `2` | Alternativa se uso personale |
| Dichiarazione Libera Esportazione | `3` | Sempre per spedizioni dall'Italia |

Max 13 documenti, max 10 MiB ciascuno. File cancellati dopo 30 giorni.

---

## Integrazione Vendiamonoi OS — Phase 4

### Divisione corrieri

| Piattaforma | Corrieri |
|---|---|
| SpediamoPro | BRT, SDA, UPS, InPost |
| Sendcloud | GLS, DHL, + 80 altri |

n8n chiama entrambe le API in parallelo → confronto prezzi in Refine → operatore seleziona → etichetta PDF → email al fornitore.

### Dati input per quotazione

Da `supplier_orders` + `orders` + `supplier_configs`:
- Peso/dimensioni: da `supplier_configs.default_parcel_*` o input manuale operatore
- Mittente: da `supplier_configs.pickup_address`
- Destinatario: da `orders.shipping_address` (nome, via, CAP, città, provincia, telefono, email)

### Credenziali n8n

```
SPEDIAMOPRO_AUTHCODE=<authcode dal profilo SpediamoPro>
SPEDIAMOPRO_BASE_URL=https://core.spediamopro.com/api/v2
SPEDIAMOPRO_STAGING_URL=https://core.spediamopro.it/api/v2
```

### Token caching in n8n

Token dura 3600s. Salvare `token` + `expires_at` in variabile statica del workflow. Rinnovare se `expires_at - now() < 300s`.

---

## Link

- OpenAPI spec: `https://core.spediamopro.com/api/v2/openapi.json`
- Staging: `https://core.spediamopro.it/api/v2`
- Test client (Bruno): `https://www.usebruno.com`
