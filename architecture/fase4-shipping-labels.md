# Fase 4 — Sistema Etichette Spedizione

**Data implementazione:** 2026-04-16  
**Status:** Implementato, in attesa configurazione credenziali n8n

## Panoramica

Sistema completo per la creazione di etichette di spedizione tramite SpediamoPro.
L'operatore sceglie il corriere confrontando i prezzi, la piattaforma crea l'etichetta e la invia via email al fornitore.

## Flusso operativo

```
/evasione/fulfillment
  → "Manda in Evasione" (abilitato se: fornitore assegnato + tutti items gestiti)
    → ShippingLabelModal
      → Step 1: Peso + dimensioni collo
        → POST /webhook/spediamopro-quotes
          → SpediamoPro API POST /quotations
          → Tabella quote con selezione radio
      → Step 2: Selezione corriere + "Crea Etichetta"
        → POST /webhook/create-shipment
          → SpediamoPro POST /quotations/accept
          → INSERT fulfillment_orders
          → INSERT shipment_labels
          → Resend email con PDF al fornitore
          → { tracking_number, label_url }
      → Step 3: Done — tracking number + download PDF

/evasione/spedizioni
  → Lista shipment_labels con filtro per data
  → Stats: totale, inviate, annullate, costo periodo
```

## Componenti frontend

| File | Tipo | Descrizione |
|------|------|-------------|
| `src/components/ShippingLabelModal.tsx` | NEW | Modal multi-step quote → label |
| `src/pages/orders/shipments.tsx` | NEW | Pagina lista spedizioni |
| `src/pages/orders/fulfillment.tsx` | MODIFIED | Abilita "Manda in Evasione" |
| `src/App.tsx` | MODIFIED | Route `/evasione/spedizioni` |
| `src/interfaces/index.ts` | MODIFIED | IShipmentLabel, IFulfillmentOrder, IQuote |

## Workflow n8n

| Workflow | ID | Webhook | Status |
|----------|-----|---------|--------|
| SpediamoPro — Get Quotes | Mi9gkK3STnOhgYAC | POST /webhook/spediamopro-quotes | INATTIVO |
| SpediamoPro — Create Shipment + Label | TyP4Awki89Uuclae | POST /webhook/create-shipment | INATTIVO |

## Configurazione necessaria per attivare

### 1. Credenziale n8n
- Tipo: HTTP Basic Auth
- Name: `SpediamoPro Authcode`
- Username: authcode SpediamoPro
- Password: (vuoto)

### 2. Variabili d'ambiente n8n
```
SUPABASE_SERVICE_KEY=service_role_key
RESEND_API_KEY=re_xxxxx
```

### 3. Indirizzo mittente (nodi Code in entrambi i workflow)
Sostituire i placeholder con il vero indirizzo magazzino Vendiamonoi:
```json
{
  "countryCode": "IT",
  "postalCode": "20139",
  "city": "Milano",
  "province": "MI",
  "address1": "Via Placeholder 1",
  "name": "Vendiamonoi SRL",
  "phone": "+390200000000",
  "email": "spedizioni@vendiamonoi.it"
}
```

### 4. Frontend env
```
VITE_N8N_WEBHOOK_URL=http://your-n8n-host:5678
```

## Tabelle DB utilizzate

- `fulfillment_orders` — record per ogni ordine inviato al fornitore
- `shipment_labels` — etichette generate con tracking, URL PDF, status
- `supplier_fulfillment_config` — configurazione `fulfillment_type` per fornitore (self_ship / we_ship)

## Note tecniche

- SpediamoPro è il carrier `spedire_pro` nell'enum DB
- Auth: OAuth 2 Client Credentials — Authcode → Bearer JWT (3600s TTL)
- Unità API: grammi (peso), millimetri (dimensioni), centesimi (prezzo)
- Il frontend converte: kg→g e cm→mm prima di inviare al webhook
- Sendcloud non ancora integrato — solo SpediamoPro per ora

## Prossimi step

1. Configurare credenziali e env vars in n8n
2. Sostituire indirizzo mittente placeholder
3. Attivare i workflow
4. Testare con un ordine reale
5. Aggiungere integrazione Sendcloud per confronto prezzi completo
6. Popolare `sku_prefix` per i 24 fornitori mancanti (393 order_items ancora NULL)
