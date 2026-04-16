# SendCloud API v2 — Documentazione Completa

> **Base URL:** `https://panel.sendcloud.sc/api/v2/`
> **Autenticazione:** HTTP Basic Auth (Public Key = username, Secret Key = password)
> **Content-Type:** `application/json`
> **Versione:** v2 (stabile, v3 disponibile ma v2 ancora supportata)

---

## 1. Autenticazione

SendCloud usa **Basic Authentication** per tutte le richieste API.

### Credenziali
- **Public Key** → username
- **Secret Key** → password

### Come ottenere le API keys
1. Accedi a `https://panel.sendcloud.sc`
2. Vai su **Settings → Integrations → API**
3. Crea una nuova integrazione
4. Copia Public Key e Secret Key

### Esempio cURL
```bash
curl -u PUBLIC_KEY:SECRET_KEY \
  https://panel.sendcloud.sc/api/v2/user
```

### Header equivalente
```
Authorization: Basic base64(PUBLIC_KEY:SECRET_KEY)
```

### Test senza costi
Usa il metodo di spedizione **"Unstamped letter"** (`shipping_option_code: "sendcloud:letter"`) per testare senza generare costi reali.

---

## 2. Shipping Methods (Metodi di Spedizione)

### 2.1 Lista tutti i metodi di spedizione

**Endpoint:** `GET /shipping_methods`

**URL completo:** `https://panel.sendcloud.sc/api/v2/shipping_methods`

**Descrizione:** Restituisce tutti i metodi di spedizione disponibili per il tuo account, basati sui corrieri che hai attivato in SendCloud.

**Query Parameters:**
| Parametro | Tipo | Obbligatorio | Descrizione |
|---|---|---|---|
| `sender_address` | integer | No | Filtra per indirizzo mittente specifico |
| `is_return` | boolean | No | Filtra solo metodi di reso |

**Risposta:**
```json
{
  "shipping_methods": [
    {
      "id": 8,
      "name": "DHL - Standard Parcel",
      "carrier": "dhl",
      "min_weight": "0.001",
      "max_weight": "31.500",
      "service_point_input": "none",
      "price": 6.50,
      "countries": [
        {
          "id": 1,
          "name": "Netherlands",
          "iso_2": "NL",
          "iso_3": "NLD",
          "price": 6.50,
          "lead_time_hours": 24,
          "price_breakdown": {
            "value": 6.50,
            "label": "Base price"
          }
        }
      ]
    }
  ]
}
```

**Campi risposta principali:**
| Campo | Tipo | Descrizione |
|---|---|---|
| `id` | integer | ID univoco del metodo di spedizione (usare per `shipment.id` nelle parcel) |
| `name` | string | Nome leggibile del metodo |
| `carrier` | string | Identificativo del corriere (es. `"dhl"`, `"brt"`, `"gls"`, `"ups"`, `"dpd"`) |
| `min_weight` | string | Peso minimo supportato (kg) |
| `max_weight` | string | Peso massimo supportato (kg) |
| `service_point_input` | string | `"none"`, `"required"`, `"optional"` |
| `price` | number | Prezzo base |
| `countries` | array | Paesi supportati con prezzi per paese |
| `countries[].iso_2` | string | Codice ISO 2 del paese |
| `countries[].price` | number | Prezzo per quel paese |
| `countries[].lead_time_hours` | integer | Tempo di consegna stimato in ore |

### 2.2 Dettaglio singolo metodo

**Endpoint:** `GET /shipping_methods/{id}`

**URL completo:** `https://panel.sendcloud.sc/api/v2/shipping_methods/{id}`

---

## 3. Shipping Products (Prodotti di Spedizione)

### 3.1 Lista prodotti di spedizione

**Endpoint:** `GET /shipping_products`

**URL completo:** `https://panel.sendcloud.sc/api/v2/shipping_products`

**Descrizione:** Un "shipping product" è un gruppo di metodi di spedizione offerti da un corriere. Questo endpoint li elenca filtrabili per dimensioni pacco, peso, paesi e funzionalità.

**Query Parameters:**
| Parametro | Tipo | Obbligatorio | Descrizione |
|---|---|---|---|
| `from_country` | string (ISO 2) | No | Paese di origine |
| `to_country` | string (ISO 2) | No | Paese di destinazione |
| `weight` | number | No | Peso del pacco in kg |
| `weight_unit` | string | No | Unità peso: `"kilogram"` (default) o `"gram"` |
| `length` | number | No | Lunghezza in cm |
| `width` | number | No | Larghezza in cm |
| `height` | number | No | Altezza in cm |

---

## 4. Shipping Prices (Prezzi di Spedizione) — CHIAVE PER COMPARAZIONE

### 4.1 Ottieni prezzo di spedizione

**Endpoint:** `GET /shipping-price`

**URL completo:** `https://panel.sendcloud.sc/api/v2/shipping-price`

**Descrizione:** Restituisce il prezzo per un metodo di spedizione specifico, dato il peso e le rotte paese. **Questo è l'endpoint chiave per la comparazione prezzi.**

**Query Parameters:**
| Parametro | Tipo | Obbligatorio | Descrizione |
|---|---|---|---|
| `shipping_method_id` | integer | **Sì** | ID del metodo di spedizione (ottenuto da GET /shipping_methods) |
| `from_country` | string (ISO 2) | **Sì** | Paese di origine (es. `"IT"`) |
| `to_country` | string (ISO 2) | No | Paese destinazione. Se omesso, restituisce prezzi per tutti i paesi |
| `weight` | number | No | Peso del pacco |
| `weight_unit` | string | No | `"kilogram"` o `"gram"` |
| `contract` | integer | Condizionale | Obbligatorio se hai più contratti attivi per lo stesso corriere. Ottieni l'ID da GET /contracts |

**Esempio richiesta:**
```bash
curl -u PUBLIC_KEY:SECRET_KEY \
  "https://panel.sendcloud.sc/api/v2/shipping-price?shipping_method_id=8&from_country=IT&to_country=DE&weight=2&weight_unit=kilogram"
```

**Risposta:**
```json
[
  {
    "shipping_method_id": 8,
    "price": "6.50",
    "currency": "EUR",
    "to_country": "DE",
    "breakdown": [
      {
        "type": "price_without_insurance",
        "label": "Label",
        "value": 6.50
      }
    ]
  }
]
```

**Campi risposta:**
| Campo | Tipo | Descrizione |
|---|---|---|
| `shipping_method_id` | integer | ID del metodo |
| `price` | string | Prezzo totale (null se non disponibile) |
| `currency` | string | Valuta (es. `"EUR"`) |
| `to_country` | string | Codice ISO 2 paese destinazione |
| `breakdown` | array | Dettaglio prezzo (base, assicurazione, extra) |

**Note importanti:**
- Se `to_country` è omesso → array con prezzi per TUTTI i paesi supportati
- Se `to_country` è presente → array con UN solo elemento
- `price` e `currency` saranno `null` se nessun prezzo disponibile per quella rotta

---

## 5. Parcels (Pacchi/Spedizioni) — CREAZIONE SPEDIZIONE

### 5.1 Crea un pacco

**Endpoint:** `POST /parcels`

**URL completo:** `https://panel.sendcloud.sc/api/v2/parcels`

**Descrizione:** Crea un pacco (spedizione). Se `request_label: true`, genera anche l'etichetta immediatamente.

**Request Body:**
```json
{
  "parcel": {
    "name": "Mario Rossi",
    "company_name": "Azienda Srl",
    "address": "Via Roma",
    "house_number": "10",
    "address_2": "",
    "city": "Milano",
    "postal_code": "20100",
    "country": "IT",
    "telephone": "+393331234567",
    "email": "mario@example.com",
    "order_number": "ORD-2024-001",
    "external_reference": "ref-12345",
    "weight": "2.500",
    "length": "30",
    "width": "20",
    "height": "15",
    "request_label": true,
    "apply_shipping_rules": false,
    "shipment": {
      "id": 8
    },
    "parcel_items": [
      {
        "description": "Prodotto esempio",
        "quantity": 1,
        "weight": "2.500",
        "value": "29.99",
        "hs_code": "8544.42",
        "origin_country": "IT",
        "sku": "SKU-001",
        "product_id": "PROD-001"
      }
    ],
    "from_name": "Vendiamonoi.it",
    "from_company_name": "Vendiamonoi.it S.R.L.",
    "from_address_1": "Via Magazzino",
    "from_address_2": "",
    "from_house_number": "1",
    "from_city": "Citta",
    "from_postal_code": "00100",
    "from_country": "IT",
    "from_telephone": "+390001234567",
    "from_email": "ordini@vendiamonoi.it",
    "is_return": false,
    "request_label_async": false
  }
}
```

**Campi principali del request body:**

| Campo | Tipo | Obbligatorio | Descrizione |
|---|---|---|---|
| `name` | string | **Sì** | Nome destinatario |
| `address` | string | **Sì** | Indirizzo (via) |
| `house_number` | string | **Sì** | Numero civico |
| `city` | string | **Sì** | Città |
| `postal_code` | string | **Sì** | CAP |
| `country` | string (ISO 2) | **Sì** | Paese destinazione |
| `telephone` | string | Consigliato | Telefono destinatario |
| `email` | string | Consigliato | Email destinatario |
| `weight` | string | **Sì** | Peso in kg (stringa decimale) |
| `length` | string | No | Lunghezza cm |
| `width` | string | No | Larghezza cm |
| `height` | string | No | Altezza cm |
| `order_number` | string | Consigliato | Numero ordine interno |
| `external_reference` | string | No | Riferimento esterno (idempotency) |
| `request_label` | boolean | No | `true` per generare etichetta subito |
| `shipment.id` | integer | **Sì se request_label=true** | ID del metodo di spedizione scelto |
| `parcel_items` | array | Per spedizioni internazionali | Lista articoli nel pacco |
| `from_*` | string | No | Dati mittente (override indirizzo default) |
| `company_name` | string | No | Ragione sociale destinatario |
| `is_return` | boolean | No | Se è un reso |
| `apply_shipping_rules` | boolean | No | Applica regole di spedizione automatiche |
| `request_label_async` | boolean | No | Genera etichetta in modo asincrono |
| `to_service_point` | integer | Se service point | ID del service point di consegna |
| `to_post_number` | string | Se service point | Numero postale del destinatario |

**Risposta (successo HTTP 200):**
```json
{
  "parcel": {
    "id": 123456,
    "name": "Mario Rossi",
    "address": "Via Roma",
    "house_number": "10",
    "city": "Milano",
    "postal_code": "20100",
    "country": {
      "iso_2": "IT",
      "iso_3": "ITA",
      "name": "Italy"
    },
    "status": {
      "id": 1000,
      "message": "Ready to send"
    },
    "tracking_number": "3SXXXX1234567890",
    "tracking_url": "https://tracking.sendcloud.sc/forward?carrier=dhl&code=3SXXXX1234567890&destination=IT",
    "weight": "2.500",
    "label": {
      "normal_printer": [
        "https://panel.sendcloud.sc/api/v2/label/normal_printer/123456?start_from=0",
        "https://panel.sendcloud.sc/api/v2/label/normal_printer/123456?start_from=1",
        "https://panel.sendcloud.sc/api/v2/label/normal_printer/123456?start_from=2",
        "https://panel.sendcloud.sc/api/v2/label/normal_printer/123456?start_from=3"
      ],
      "label_printer": "https://panel.sendcloud.sc/api/v2/label/label_printer/123456"
    },
    "shipment": {
      "id": 8,
      "name": "DHL - Standard Parcel"
    },
    "carrier": {
      "code": "dhl"
    },
    "order_number": "ORD-2024-001",
    "created_at": "2024-01-15T10:30:00+00:00"
  }
}
```

### 5.2 Recupera un pacco

**Endpoint:** `GET /parcels/{id}`

### 5.3 Cancella un pacco

**Endpoint:** `POST /parcels/{id}/cancel`

**Descrizione:** Cancella un pacco prima che venga ritirato dal corriere. Dopo la cancellazione, l'etichetta non è più valida.

---

## 6. Labels (Etichette)

### 6.1 Scarica etichetta singola

**Endpoint (A4/A6 normale):** `GET /label/normal_printer/{parcel_id}`

**URL completo:** `https://panel.sendcloud.sc/api/v2/label/normal_printer/{parcel_id}?start_from=0`

**Query Parameters:**
| Parametro | Tipo | Descrizione |
|---|---|---|
| `start_from` | integer | Posizione sulla pagina A4 (0-3). Utile per stampare più etichette per foglio |

**Endpoint (Stampante etichette):** `GET /label/label_printer/{parcel_id}`

**URL completo:** `https://panel.sendcloud.sc/api/v2/label/label_printer/{parcel_id}`

**Risposta:** File PDF binario dell'etichetta.

### 6.2 Stampa multipla (bulk)

**Endpoint:** `POST /labels`

**Request Body:**
```json
{
  "label": {
    "parcels": [123456, 123457, 123458]
  }
}
```

**Risposta:** PDF unico con tutte le etichette.

---

## 7. Returns (Resi)

### 7.1 Crea un reso

**Endpoint:** `POST /parcels`

**Descrizione:** Usa lo stesso endpoint di creazione pacco, ma con `is_return: true` e invertendo mittente/destinatario.

---

## 8. Contracts (Contratti)

### 8.1 Lista contratti

**Endpoint:** `GET /contracts`

**URL completo:** `https://panel.sendcloud.sc/api/v2/contracts`

**Descrizione:** Restituisce i contratti attivi con i corrieri. Il `contract_id` è necessario nell'endpoint shipping-price se hai più contratti per lo stesso corriere.

---

## 9. Sender Addresses (Indirizzi Mittente)

### 9.1 Lista indirizzi mittente

**Endpoint:** `GET /user/addresses/sender`

**URL completo:** `https://panel.sendcloud.sc/api/v2/user/addresses/sender`

**Risposta:**
```json
{
  "sender_addresses": [
    {
      "id": 1234,
      "company_name": "Vendiamonoi.it S.R.L.",
      "contact_name": "Alessio Baggiani",
      "street": "Via Magazzino",
      "house_number": "1",
      "city": "Citta",
      "postal_code": "00100",
      "country": "IT",
      "telephone": "+39000123456",
      "email": "ordini@vendiamonoi.it"
    }
  ]
}
```

---

## 10. Workflow Comparazione Preventivi — Come Usare per Vendiamonoi

### Flusso per ottenere il miglior prezzo:

1. **GET /shipping_methods** → Ottieni tutti i metodi disponibili con i relativi ID
2. **Per ogni metodo rilevante**, chiama **GET /shipping-price** con:
   - `shipping_method_id` = ID del metodo
   - `from_country` = `"IT"` (magazzino fornitore)
   - `to_country` = paese destinazione ordine
   - `weight` = peso pacco in kg
3. **Confronta i prezzi** tra tutti i metodi e seleziona il più economico
4. **POST /parcels** con `shipment.id` = ID del metodo scelto e `request_label: true`
5. **Scarica etichetta** dall'URL nel campo `label` della risposta

### Nota su peso volumetrico
SendCloud non calcola automaticamente il peso volumetrico. Devi calcolare tu:
```
peso_volumetrico_kg = (lunghezza_cm × larghezza_cm × altezza_cm) / 5000
peso_fatturabile = max(peso_reale, peso_volumetrico)
```

---

## 11. Codici di Stato Pacco

| Status ID | Messaggio | Descrizione |
|---|---|---|
| 1 | Announced | Pacco annunciato, in attesa di consegna al corriere |
| 3 | En route to sorting center | In transito verso centro smistamento |
| 4 | Delivered to sorting center | Consegnato al centro smistamento |
| 5 | Sorted | Smistato |
| 6 | Not sorted | Non smistato |
| 7 | Being delivered | In consegna |
| 8 | Delivered | Consegnato |
| 9 | Delivery attempt failed | Tentativo di consegna fallito |
| 10 | Returned to sender | Restituito al mittente |
| 11 | Delivery exception | Eccezione nella consegna |
| 12 | Shipment collected by customer | Ritirato dal cliente |
| 1000 | Ready to send | Pronto per l'invio |
| 1001 | Being announced | In fase di annuncio |
| 1002 | Announcement failed | Annuncio fallito |
| 1999 | Cancellation requested | Cancellazione richiesta |
| 2000 | Cancelled | Cancellato |
| 2001 | Submitting cancellation request | Invio richiesta cancellazione |

---

## 12. Rate Limits

SendCloud applica rate limiting sulle API. Non documentano il limite esatto, ma le best practice sono:
- Max 5-10 richieste/secondo
- Usa batch dove possibile (POST /parcels accetta array)
- Implementa retry con exponential backoff su HTTP 429

---

## 13. Errori Comuni

| HTTP Code | Significato | Azione |
|---|---|---|
| 400 | Bad Request — parametri mancanti o invalidi | Verifica campi obbligatori |
| 401 | Unauthorized — credenziali errate | Verifica Public/Secret Key |
| 403 | Forbidden — permessi insufficienti | Verifica permessi integrazione |
| 404 | Not Found — risorsa inesistente | Verifica ID pacco/metodo |
| 429 | Too Many Requests — rate limit | Implementa backoff |
| 500 | Internal Server Error | Riprova dopo qualche secondo |

---

## Fonti
- [SendCloud API Developer Portal](https://sendcloud.dev/)
- [SendCloud Public REST API Reference](https://api.sendcloud.dev/docs/sendcloud-public-api/)
- [SendCloud API Quick Start Guide](https://support.sendcloud.com/hc/en-us/articles/360024967012)
- [SendCloud API GitHub Example](https://github.com/SendCloud/api-integration-example)
- [SendCloud Postman Collection](https://www.postman.com/sendcloud-api/sendcloud-rest-api/documentation/b68a4cc/sendcloud-api)
