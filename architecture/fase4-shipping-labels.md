# Fase 4 — Sistema Etichette Spedizione (SpediamoPro + SendCloud)

**Data implementazione:** 2026-04-16
**Status:** Implementato — in attesa di SUPABASE_SERVICE_KEY e RESEND_API_KEY in n8n

---

## Panoramica

Sistema completo per la creazione di etichette di spedizione che confronta prezzi tra **SpediamoPro** (BRT/SDA/UPS/InPost) e **SendCloud** (GLS/DHL/altri) e sceglie il corriere più economico.

## Flusso operativo

```
/evasione/fulfillment
  → "Manda in Evasione" (abilitato se: fornitore assegnato + tutti gli item gestiti)
    → ShippingLabelModal
      → Step 1: Peso + dimensioni collo
        → POST /webhook/shipping-quotes
          → [parallelo] SpediamoPro: token → POST /quotations
          → [parallelo] SendCloud: GET /shipping_methods
          → Normalizza entrambi → ordina per prezzo → { all_quotes, best_quote }
          → Tabella quote con selezione radio (ordinata per prezzo crescente)
      → Step 2: Selezione corriere + "Crea Etichetta"
        → POST /webhook/create-shipment
          → IF source === 'spediamopro':
              → token → POST /quotations/accept → shipment_id + tracking
          → ELSE (sendcloud):
              → POST /parcels (request_label=true) → parcel_id + tracking + label_url
          → INSERT fulfillment_orders (Supabase)
          → INSERT shipment_labels (Supabase)
          → Email al fornitore via Resend
          → { tracking_number, label_url }
      → Step 3: Done — tracking + download PDF

/evasione/spedizioni
  → Lista shipment_labels con filtro data
  → Stats: totale, inviate, annullate, costo periodo
```

---

## Componenti frontend

| File | Tipo | Descrizione |
|------|------|-------------|
| `src/components/ShippingLabelModal.tsx` | NEW | Modal multi-step con comparazione prezzi |
| `src/pages/orders/shipments.tsx` | NEW | Pagina lista spedizioni |
| `src/pages/orders/fulfillment.tsx` | MODIFIED | Abilita "Manda in Evasione" |
| `src/App.tsx` | MODIFIED | Route `/evasione/spedizioni` |
| `src/interfaces/index.ts` | MODIFIED | IShipmentLabel, IFulfillmentOrder, IQuote (nuovo formato) |

### Formato IQuote (unificato)
```typescript
interface IQuote {
  quote_id: string;
  source: "spediamopro" | "sendcloud";
  carrier: string;         // es. "brt", "dhl"
  service: string;         // es. "Standard Parcel"
  price_eur: number;       // in EUR (non centesimi)
  currency: string;
  delivery_days: number | null;
  expected_delivery_date: string | null;
  shipping_method_id: number | null;  // solo per sendcloud
}
```

---

## Workflow n8n

| Workflow | ID | Webhook | Status |
|----------|-----|---------|--------|
| Comparazione Preventivi — SpediamoPro + SendCloud | RA6YV6ql8fpRHtOU | POST /webhook/shipping-quotes | INATTIVO |
| Crea Spedizione — SpediamoPro + SendCloud | TyP4Awki89Uuclae | POST /webhook/create-shipment | INATTIVO |

### Workflow 1: Comparazione Preventivi (11 nodi)
```
Webhook → Calcola Peso Fatturabile
  ├── SpediamoPro: Token → Prepara Body → Preventivi → Normalizza SpediamoPro ──┐
  └── SendCloud: Metodi di Spedizione → Normalizza SendCloud ───────────────────┤
                                                                    Merge Preventivi
                                                                    → Componi Risposta
                                                                    → Risposta Webhook
```
Output: `{ order_id, best_quote, top_3_quotes, all_quotes, weight_* }`

### Workflow 2: Crea Spedizione (15 nodi)
```
Webhook → Prepara Dati → IF source=spediamopro?
  ├── TRUE:  Token → Build Accept Body → Accept Quotation → Estrai SpediamoPro ──┐
  └── FALSE: Build Parcel Body → Create Parcel SendCloud → Estrai SendCloud ──────┤
                                                              Insert Fulfillment Order
                                                              → Prepara Insert Label
                                                              → Insert Shipment Label
                                                              → Email Etichetta Fornitore
                                                              → Risposta Webhook
```

---

## Credenziali n8n (già configurate)

| Nome | Tipo | ID | Servizio |
|------|------|----|---------|
| SpediamoPro Authcode | httpBasicAuth | fODbNiSFfIFaUTtw | SpediamoPro |
| SendCloud API | httpBasicAuth | pfSUBjPHDSAUlmTj | SendCloud |

---

## Mittente spedizioni (hardcoded nei workflow)

```
VendiamoNoi.it S.R.L. Unipersonale
Via Ferdinando Magellano 60
56010 Vicopisano (PI) — IT
```

---

## Configurazione necessaria per attivare

### 1. Variabili d'ambiente n8n
```
SUPABASE_SERVICE_KEY=service_role_key_from_supabase
RESEND_API_KEY=re_xxxxx
```

### 2. Frontend
```
VITE_N8N_WEBHOOK_URL=http://your-n8n-host:5678
```

### 3. Attivare i 2 workflow in n8n

---

## DB — Enum carrier in shipment_labels

| Valore enum | Piattaforma | Corrieri |
|-------------|-------------|---------|
| `spedire_pro` | SpediamoPro | BRT, SDA, UPS, InPost |
| `sendcloud` | SendCloud | GLS, DHL, DPD, altri |
| `supplier_carrier` | Fornitore diretto | variabile |

---

## Pendenti / Prossimi step

1. Impostare SUPABASE_SERVICE_KEY e RESEND_API_KEY in n8n
2. Attivare i 2 workflow
3. Test end-to-end con un ordine reale
4. Download PDF etichetta SpediamoPro: attualmente si salva l'endpoint API (richiede Bearer token per scaricare). Da valutare upload automatico su Supabase Storage.
5. Tracking automatico: webhook SendCloud per aggiornamenti status (quando n8n è su Hetzner)
