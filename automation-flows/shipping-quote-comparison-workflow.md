# TASK: Workflow Unificato Comparazione Preventivi Spedizione

## Contesto
Vendiamonoi.it usa DUE piattaforme di spedizione: **SpediamoPro** e **SendCloud**.
L'obiettivo è creare un workflow n8n che, dato un ordine, chieda preventivi a ENTRAMBE le piattaforme, li confronti, e restituisca SOLO il preventivo migliore (più economico) in una singola linea unificata.

## Dove trovare la documentazione API

### SpediamoPro API v2
- **GitHub:** `Alessiobaggiani/vendiamonoi-knowledge` → file `integrations/spediamopro-api-v2.md`
- **Auth:** OAuth2 (client_credentials) → POST /oauth/token → Bearer token
- **Endpoint preventivi:** POST /quotations
- **Endpoint creazione spedizione:** POST /quotations/accept

### SendCloud API v2
- **GitHub:** `Alessiobaggiani/vendiamonoi-knowledge` → file `integrations/sendcloud-api-v2.md`
- **Auth:** HTTP Basic Auth (Public Key = username, Secret Key = password)
- **Endpoint preventivi:** GET /shipping-price?shipping_method_id=X&from_country=IT&to_country=XX&weight=X
- **Endpoint lista metodi:** GET /shipping_methods (per ottenere gli ID dei metodi)
- **Endpoint creazione spedizione:** POST /parcels (con request_label: true)
- **Base URL:** https://panel.sendcloud.sc/api/v2/

## Credenziali necessarie (da chiedere ad Alessio)

### SpediamoPro:
1. Client ID (OAuth2)
2. Client Secret (OAuth2)

### SendCloud:
1. Public Key (Basic Auth username)
2. Secret Key (Basic Auth password)

### Dati operativi:
1. Indirizzo magazzino/mittente reale Vendiamonoi
2. Conferma from_country = IT

## Workflow 1: Comparazione Preventivi

### Input (webhook):
```json
{
  "order_id": "string",
  "weight_kg": 2.5,
  "length_cm": 30,
  "width_cm": 20,
  "height_cm": 15,
  "from_country": "IT",
  "from_postal_code": "00100",
  "to_country": "DE",
  "to_postal_code": "10115",
  "to_city": "Berlin",
  "to_name": "Mario Rossi",
  "to_address": "Berliner Str. 1"
}
```

### Logica:
1. Calcola peso volumetrico: (L × W × H) / 5000
2. Peso fatturabile: max(peso_reale, peso_volumetrico)
3. In parallelo:
   - Ramo A: SpediamoPro → POST /quotations
   - Ramo B: SendCloud → GET /shipping_methods → GET /shipping-price per ogni metodo
4. Normalizza in formato unificato
5. Ordina per prezzo crescente
6. Restituisci il migliore

### Formato normalizzato:
```json
{
  "source": "spediamopro | sendcloud",
  "carrier": "DHL",
  "service": "Standard Parcel",
  "price_eur": 6.50,
  "currency": "EUR",
  "delivery_days": 3,
  "shipping_method_id": 8,
  "raw_response": {}
}
```

### Output:
```json
{
  "order_id": "ORD-001",
  "best_quote": { ... },
  "all_quotes": [ ... ],
  "weight_used_kg": 2.5,
  "volumetric_weight_kg": 1.8,
  "billable_weight_kg": 2.5
}
```

## Workflow 2: Creazione Spedizione

### Logica:
- Se source == "spediamopro" → POST /quotations/accept
- Se source == "sendcloud" → POST /parcels con request_label: true
- Salva in Supabase: fulfillment_orders + shipment_labels
- Invia email al fornitore con tracking + etichetta

## Note
- Timeout 10s per provider (se non risponde, usa solo l'altro)
- Gestione errori (un provider fallisce → non bloccare tutto)
- Credenziali come variabili d'ambiente in n8n
- Supabase project ID: smozehzbhoeceioqxodx
