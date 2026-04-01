# Kaufland Global Marketplace — Specifiche Tecniche Complete

**Versione:** 2.0
**Data:** Aprile 2026
**Stato:** Specifiche Tecniche di Riferimento per Integratori e Seller Avanzati
**Lingua:** Italiano (IT)

---

## Informazioni Generali

| Parametro | Valore |
|-----------|--------|
| **Nome Piattaforma** | Kaufland Global Marketplace |
| **Società Madre** | Schwarz Group (Lidl/Kaufland) |
| **Fondazione** | 2006 (come Hitmeister); acquisito da Schwarz Group nel 2008 |
| **Portale Seller** | https://www.kauflandglobalmarketplace.com |
| **Documentazione API** | https://sellerapi.kaufland.com/ |
| **API Playground** | https://sellerapi-playground.kaufland.com/ |
| **Paesi Supportati** | 7 (Germania, Repubblica Ceca, Slovacchia, Polonia, Austria, Francia, Italia) |
| **URL Principali per Paese** | DE: kaufland.de, CZ: kaufland.cz, SK: kaufland.sk, PL: kaufland.pl, AT: kaufland.at, FR: kaufland.fr, IT: kaufland.it |
| **Clienti Mensili Potenziali** | 139 milioni di utenti online |
| **Visitatori Mensili (DE)** | 32 milioni di visite mensili |
| **Modello Commissionale** | Commissione variabile per categoria + canone mensile fisso |
| **Canone Mensile Base** | EUR 39,95 - EUR 59,95 (escluso IVA) |
| **Range Commissioni** | 4% - 16% a seconda della categoria e della nazione |
| **Valuta Principale** | EUR (multi-valuta per paese) |
| **Tipo API** | REST API nativa (non Mirakl-based) |

---

## 1. Architettura API

### 1.1 Panoramica Generale

L'API Marketplace Seller di Kaufland è un'interfaccia HTTP RESTful che consente ai seller di:
- Gestire catalogo prodotti e inventario
- Amministrare ordini e fulfillment
- Tracciare restituzioni
- Gestire comunicazioni con clienti
- Accedere a report e analytics
- Configurare impostazioni di spedizione

La piattaforma utilizza una **REST API nativa proprietaria**, non costruita su Mirakl o altre piattaforme di marketplace-as-a-service. Questo significa che l'architettura è completamente personalizzata per le esigenze specifiche di Kaufland e del suo network di seller globali.

### 1.2 Endpoint Base

**URL Produzione:** `https://merchant.kaufland.com/api/`
**URL Staging:** `https://partner.kaufland.com/api/` (per test)
**Versione API:** v2 (raccomandato), v1 (deprecato ma ancora supportato)

### 1.3 Formato Dati

- **Request/Response:** JSON (ISO 8859-1 per input dati, UTF-8 per output)
- **Autenticazione:** Token Bearer basato su OAuth 2.0
- **Rate Limit:** 100 richieste/minuto per seller
- **Timeout:** 30 secondi per endpoint standard

---

## 2. Autenticazione e Autorizzazione

### 2.1 Flusso OAuth 2.0

Kaufland implementa un **OAuth 2.0 Server-to-Server** per l'autenticazione API:

```
GET /api/authorize?client_id=YOUR_CLIENT_ID&redirect_uri=https://yoursite.com&response_type=code
```

**Parametri:**
- `client_id`: ID assegnato durante l'iscrizione al Developer Program
- `client_secret`: Secret generato durante l'iscrizione (conservare in sicurezza)
- `redirect_uri`: URL di callback registrato in precedenza
- `scope`: Permessi richiesti (es: `catalog:read`, `orders:read`, `orders:write`)

### 2.2 Ottenimento Token

**Endpoint:** `POST /api/oauth/token`

```json
{
  "grant_type": "authorization_code",
  "code": "AUTH_CODE_RICEVUTO",
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET",
  "redirect_uri": "https://yoursite.com"
}
```

**Risposta:**
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "refresh_token_value",
  "scope": "catalog:read orders:read orders:write"
}
```

### 2.3 Refresh Token

I token OAuth scadono in 1 ora. Utilizzare il `refresh_token` per ottenere un nuovo `access_token` senza reinserire le credenziali.

**Endpoint:** `POST /api/oauth/token`
```json
{
  "grant_type": "refresh_token",
  "refresh_token": "REFRESH_TOKEN_VALUE",
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET"
}
```

### 2.4 Scopes Disponibili

| Scope | Descrizione | Note |
|-------|-------------|-------|
| `catalog:read` | Lettura catalogo prodotti | Obbligatorio |
| `catalog:write` | Modifica catalogo prodotti | Per aggiornamenti inventario |
| `orders:read` | Lettura ordini | Accesso agli ordini ricevuti |
| `orders:write` | Modifica stato ordini | Shipment, cancellazioni |
| `returns:read` | Lettura resi | Tracciamento resi |
| `returns:write` | Gestione resi | Approvazione/rifiuto resi |
| `messages:read` | Lettura messaggi | Comunicazioni buyer-seller |
| `messages:write` | Invio messaggi | Risposte buyer |
| `reports:read` | Accesso report | Analytics e KPI |
| `settings:read` | Lettura impostazioni | Configurazione account |
| `settings:write` | Modifica impostazioni | Cambio impostazioni negozio |

---

## 3. Gestione Catalogo Prodotti

### 3.1 Endpoints Catalogo

**GET /api/catalog/products** - Elenco prodotti
- Parametri: `limit` (max 100), `offset`, `sku`, `category_id`, `status`
- Risposta: Array di prodotti con metadati

**GET /api/catalog/products/{product_id}** - Dettagli prodotto
- Parametri: `product_id` (SKU o ID marketplace)
- Risposta: Oggetto prodotto completo

**POST /api/catalog/products** - Creazione prodotto
- Body: JSON con dati prodotto (vedi schema sottostante)
- Risposta: Prodotto creato con ID assegnato

**PUT /api/catalog/products/{product_id}** - Aggiornamento prodotto
- Body: Campi da aggiornare
- Risposta: Prodotto aggiornato

**DELETE /api/catalog/products/{product_id}** - Rimozione prodotto
- Nota: Solo prodotti senza ordini possono essere eliminati
- Risposta: Conferma di cancellazione

### 3.2 Schema Prodotto

```json
{
  "sku": "ABC-123-XYZ",
  "ean": "5901234123457",
  "title": "Prodotto Esempio - Categoria Premium",
  "description": "Descrizione dettagliata del prodotto...",
  "category_id": 5432,
  "price": {
    "amount": 29.99,
    "currency": "EUR",
    "net_price": 24.99
  },
  "stock": {
    "quantity": 150,
    "warehouse_id": "WH_001",
    "restock_date": "2026-05-15"
  },
  "attributes": {
    "color": "Rosso",
    "size": "M",
    "material": "Cotone 100%"
  },
  "images": [
    {
      "url": "https://example.com/image1.jpg",
      "position": 1,
      "is_primary": true
    }
  ],
  "shipping": {
    "weight_kg": 0.5,
    "dimensions": {
      "length_cm": 20,
      "width_cm": 15,
      "height_cm": 10
    },
    "shipping_class": "standard"
  },
  "seo": {
    "meta_title": "Meta title SEO",
    "meta_description": "Meta description SEO",
    "url_slug": "prodotto-esempio"
  },
  "status": "active"
}
```

### 3.3 Gestione Varianti Prodotto

I prodotti con varianti (colori, taglie, ecc.) devono essere strutturati come prodotto padre con varianti figlie:

**POST /api/catalog/products/{parent_id}/variants** - Crea variante
```json
{
  "variant_sku": "ABC-123-XYZ-RED-M",
  "variant_attributes": {
    "color": "Rosso",
    "size": "M"
  },
  "variant_price": 29.99,
  "variant_stock": 45
}
```

### 3.4 Bulk Upload

Per caricare grandi cataloghi (1000+ prodotti):

**POST /api/catalog/bulk/upload** - Upload CSV/Excel
- Content-Type: `multipart/form-data`
- Campo: `file` (CSV con separatore `;`)
- Ritorna: Job ID per monitoraggio progresso

**GET /api/catalog/bulk/upload/{job_id}** - Stato bulk upload
- Risposta: `{"status": "processing|completed|failed", "processed": 1234, "errors": []}`

---

## 4. Gestione Ordini

### 4.1 Endpoints Ordini

**GET /api/orders** - Elenco ordini
- Filtri: `status`, `date_from`, `date_to`, `seller_id`, `limit`, `offset`
- Stato possibili: `new`, `pending`, `processing`, `shipped`, `delivered`, `cancelled`

**GET /api/orders/{order_id}** - Dettagli ordine
- Include: buyer info, items, shipment, timeline

**PUT /api/orders/{order_id}/status** - Aggiornamento stato
```json
{
  "status": "processing",
  "expected_shipment_date": "2026-04-05"
}
```

**POST /api/orders/{order_id}/shipment** - Registra spedizione
```json
{
  "carrier": "DHL",
  "tracking_number": "123456789ABC",
  "tracking_url": "https://track.dhl.com/...",
  "shipped_date": "2026-04-04",
  "estimated_delivery": "2026-04-08"
}
```

### 4.2 Schema Ordine

```json
{
  "order_id": "ORD-2026-001234",
  "order_number": "#1234",
  "status": "new",
  "created_at": "2026-04-01T10:30:00Z",
  "updated_at": "2026-04-01T10:35:00Z",
  "buyer": {
    "id": "BUYER_ID_123",
    "username": "buyer_username",
    "email": "buyer@example.com",
    "phone": "+39 123456789",
    "rating": 4.8,
    "total_purchases": 150
  },
  "items": [
    {
      "product_id": "PROD_001",
      "sku": "ABC-123-XYZ",
      "title": "Prodotto Esempio",
      "quantity": 1,
      "price": 29.99,
      "attributes": {"color": "Rosso", "size": "M"}
    }
  ],
  "totals": {
    "items_price": 29.99,
    "shipping_cost": 5.99,
    "tax": 7.20,
    "discount": 0,
    "total": 43.18
  },
  "shipping_address": {
    "first_name": "Mario",
    "last_name": "Rossi",
    "street": "Via Roma 1",
    "city": "Milano",
    "postal_code": "20100",
    "country": "IT",
    "phone": "+39 123456789"
  },
  "payment_method": "credit_card",
  "payment_status": "paid",
  "notes": "Consegnare fra 14:00 e 18:00"
}
```

### 4.3 Notifiche Ordini

Kaufland invia webhook per gli eventi ordini:

**Webhook Events:**
- `order.created` - Nuovo ordine
- `order.confirmed` - Ordine confermato dal buyer
- `order.processing` - In elaborazione
- `order.shipped` - Spedito
- `order.delivered` - Consegnato
- `order.cancelled` - Cancellato
- `order.return_requested` - Richiesta reso

**Configurazione Webhook:** Portale seller > Settings > API > Webhooks

---

## 5. Gestione Resi (Returns)

### 5.1 Endpoints Resi

**GET /api/returns** - Elenco resi
- Filtri: `status`, `order_id`, `date_from`, `date_to`
- Stato: `requested`, `approved`, `shipped_back`, `received`, `refunded`, `rejected`

**GET /api/returns/{return_id}** - Dettagli reso

**PUT /api/returns/{return_id}/status** - Aggiorna stato reso
```json
{
  "status": "approved",
  "return_address": "Via Resi 10, 20100 Milano, IT",
  "return_label_url": "https://example.com/label.pdf",
  "refund_amount": 29.99,
  "refund_reason": "Approved by seller"
}
```

### 5.2 Policy Resi

Kaufland ha una **politica reso di 30 giorni** per i buyer:
- Periodo reso: 30 giorni dal ricevimento
- Refund: Generalmente entro 5-10 giorni dalla ricezione della merce resa
- Seller può offrire politica più generosa ma non più restrittiva

---

## 6. Messaggistica Buyer-Seller

### 6.1 Endpoints Messaggi

**GET /api/messages** - Elenco conversazioni
- Filtri: `order_id`, `buyer_id`, `unread_only`, `limit`

**GET /api/messages/{conversation_id}** - Storico conversazione

**POST /api/messages/{conversation_id}/reply** - Invia messaggio
```json
{
  "message": "Grazie per l'acquisto! Prodotto spedito domani.",
  "attachments": [
    {
      "type": "image",
      "url": "https://example.com/shipping_photo.jpg"
    }
  ]
}
```

### 6.2 Automatizzazione Messaggi

Può essere utile automatizzare risposte comuni:
- Conferma ricezione ordine
- Notifica spedizione
- Richiesta feedback post-consegna
- Gestione problemi di consegna

---

## 7. Report e Analytics

### 7.1 Endpoints Report

**GET /api/reports/sales** - Report vendite
```json
Parametri:
- date_from: "2026-03-01"
- date_to: "2026-03-31"
- group_by: "daily|weekly|monthly|product"
- limit: 100
```

Risposte:
```json
{
  "period": "2026-03-01 to 2026-03-31",
  "total_orders": 456,
  "total_revenue": 12345.67,
  "average_order_value": 27.06,
  "items_sold": 1234,
  "returns": 45,
  "return_rate": 3.6,
  "by_day": [
    {
      "date": "2026-03-01",
      "orders": 12,
      "revenue": 321.45
    }
  ]
}
```

**GET /api/reports/performance** - Report performance
- Metriche: Response time, Return rate, Cancellation rate, Rating
- Benchmark: Confronto con media marketplace

**GET /api/reports/inventory** - Report inventario
- Stock levels, Turnover rate, Aging products

### 7.2 Export Dati

**POST /api/reports/export** - Esporta report
```json
{
  "report_type": "sales|performance|inventory",
  "date_from": "2026-03-01",
  "date_to": "2026-03-31",
  "format": "csv|excel|json"
}
```

Ritorna: URL per download file (disponibile 24 ore)

---

## 8. Impostazioni Seller

### 8.1 Endpoints Impostazioni

**GET /api/settings/profile** - Profilo seller

**PUT /api/settings/profile** - Aggiorna profilo
```json
{
  "business_name": "My Store",
  "business_description": "Descrizione negozio",
  "logo_url": "https://example.com/logo.png",
  "website_url": "https://example.com",
  "phone": "+39 123456789",
  "support_email": "support@example.com"
}
```

**GET /api/settings/shipping-methods** - Metodi di spedizione

**POST /api/settings/shipping-methods** - Aggiungi metodo di spedizione
```json
{
  "carrier_name": "DHL",
  "base_cost": 5.99,
  "free_shipping_threshold": 50,
  "delivery_time_days": 3,
  "available_countries": ["IT", "DE", "FR", "ES"]
}
```

**GET /api/settings/payment-methods** - Metodi di pagamento

**GET /api/settings/policies** - Politiche negozio
- Return policy, Warranty, Customer service hours

### 8.2 Webhooks Configuration

**POST /api/settings/webhooks** - Registra webhook
```json
{
  "url": "https://yoursite.com/webhook/kaufland",
  "events": ["order.created", "order.shipped", "return.requested"],
  "secret": "your_secret_key",
  "active": true
}
```

**Verifica Webhook:** Incluso nell'header `X-Kaufland-Signature`

---

## 9. Gestione Errori

### 9.1 Codici di Errore

| Codice | Status HTTP | Descrizione | Azione |
|--------|-------------|-------------|--------|
| 400 | 400 | Bad Request | Verifica parametri |
| 401 | 401 | Unauthorized | Verifica token OAuth |
| 403 | 403 | Forbidden | Scope insufficient |
| 404 | 404 | Not Found | Risorsa non esiste |
| 409 | 409 | Conflict | Duplicato/Conflitto dati |
| 429 | 429 | Too Many Requests | Rate limit exceeded |
| 500 | 500 | Internal Server Error | Errore Kaufland (retry) |
| 503 | 503 | Service Unavailable | Maintenance (retry) |

### 9.2 Esempio Risposta Errore

```json
{
  "error": {
    "code": "INVALID_SKU",
    "message": "SKU already exists in catalog",
    "details": {
      "field": "sku",
      "value": "ABC-123-XYZ",
      "constraint": "unique"
    },
    "request_id": "req_12345678"
  }
}
```

### 9.3 Retry Strategy

- **Errori 5xx**: Retry con backoff esponenziale (1s, 2s, 4s, 8s)
- **Errori 429**: Attendi X secondi indicato in header `Retry-After`
- **Errori 4xx**: Non ritentare (fix richiesto)

---

## 10. Rate Limiting e Performance

### 10.1 Limiti API

| Tipo | Limite | Periodo |
|------|--------|--------|
| Richieste generiche | 100 | 1 minuto |
| Bulk upload | 1 | 5 minuti |
| Export report | 10 | 1 ora |
| Webhook delivery | 10 rps | Per endpoint |

### 10.2 Headers Rate Limit

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 87
X-RateLimit-Reset: 1685000000
```

### 10.3 Ottimizzazione Richieste

- Usa batch operations quando possibile
- Implementa caching lato client
- Riduci frequenza aggiornamento inventario
- Usa webhooks al posto di polling

---

## 11. Best Practices

### 11.1 Gestione Inventario

1. **Sincronizzazione**: Aggiorna inventario entro 1 ora da una vendita
2. **Stock Reserve**: Prenota stock per ordini in processing
3. **Overselling**: Implementa buffer (es. -5% per calo naturale)
4. **Allert**: Avvisa quando stock scende sotto soglia

### 11.2 Pricing

1. **Competitività**: Monitora prezzi competitor
2. **Promozioni**: Usa sconto margine per promozionare
3. **Tasse**: Includi IVA automaticamente
4. **Currency**: Specifica sempre valuta (EUR standard)

### 11.3 Qualità Prodotto

1. **Descrizioni**: Dettagliate, con errori ortografici minimi
2. **Immagini**: Almeno 3 foto, alta risoluzione (1200x1200px)
3. **Attributi**: Completi per facilitare ricerca
4. **Review**: Rispondi a tutti i feedback entro 48 ore

### 11.4 Customer Service

1. **Response Time**: Rispondi entro 24 ore
2. **Resolution**: Risolvi problemi in prima comunicazione
3. **Rating**: Obiettivo >4.5 stelle
4. **Feedback**: Chiedi review dopo consegna

---

## 12. Integrazione con Channable e Make

### 12.1 Channable Integration

I seller spesso usano **Channable** per:
- Sincronizzazione inventario
- Feed prodotti
- Aggiornamento prezzi
- Multi-channel distribution

**Connessione:**
1. In Channable, configura Kaufland come canale di vendita
2. Autorizza connessione OAuth
3. Mappo i campi prodotto Channable → Kaufland
4. Attiva sincronizzazione ordini/inventario

### 12.2 Make Integration

**Make.com** consente automazioni come:
- Notifiche ordini → Email/SMS
- Inventario Kaufland → Google Sheets
- Ordini → Sistema contabilità
- Tracking → Dashboard personalizzata

**Scenario Esempio:**
1. Trigger: Nuovo ordine in Kaufland
2. Action: Aggiungi riga a Google Sheets
3. Action: Invia SMS al magazzino
4. Action: Crea task in Asana

---

## 13. Testing e Sandbox

### 13.1 Sandbox Environment

**URL:** `https://partner.kaufland.com/api/` (uso test credentials)

**Vantaggi:**
- Nessun effetto su dati produzione
- Stesso schema e behavior
- Rate limits più alti per testing

### 13.2 Test Credentials

```
Client ID: test_client_123
Client Secret: test_secret_abc
Test SKU: TEST-001 (prodotto dummy)
Test Buyer: test_buyer_001
```

### 13.3 Test Checklist

- [ ] Autenticazione OAuth
- [ ] CRUD prodotto completo
- [ ] Gestione ordini (creazione → spedizione)
- [ ] Gestione resi
- [ ] Messaggistica
- [ ] Webhook delivery
- [ ] Error handling
- [ ] Rate limiting behavior

---

## 14. Support e Risorse

### 14.1 Contatti Support

| Canale | Indirizzo | Response Time |
|--------|-----------|---------------|
| Email | seller-api-support@kaufland.de | 24-48 ore |
| Forum | forum.kauflandglobal.com | 48-72 ore |
| Phone | +49 30 123456 (DE only) | 2-4 ore |
| Chat | Portale seller (per errori 5xx) | Istantaneo |

### 14.2 Documentazione Online

- **API Docs**: https://sellerapi.kaufland.com/documentation
- **Sandbox**: https://sellerapi-playground.kaufland.com
- **Changelog**: https://sellerapi.kaufland.com/changelog
- **FAQ**: https://help.kauflandglobal.com/faq/api

### 14.3 Risorse Sviluppatori

- SDK ufficiali: Python, PHP, Node.js, Java
- Postman Collection: Download dal portale
- Webhook tester: https://webhook.site
- Rate limit calculator: Portale seller

---

## 15. Migrazione da Altre Piattaforme

### 15.1 Preparazione Dati

1. **Mapping Categorie**: Kaufland → Vecchia piattaforma
2. **Normalizzazione**: Prezzi, taglie, colori standardizzati
3. **Validazione**: Controlla EAN, immagini, descrizioni
4. **Test**: Carica 10 prodotti in sandbox

### 15.2 Migrazione Inventario

**Fase 1: Import Prodotti**
```python
for product in old_platform.get_products():
    sku = normalize_sku(product['sku'])
    kaufland.create_product({
        'sku': sku,
        'title': product['name'],
        'price': convert_currency(product['price']),
        'stock': product['quantity']
    })
```

**Fase 2: Sincronizzazione Ordini**
- Importa ordini storici (es. ultimi 90 giorni)
- Contrassegna come completati
- Monitora nuovi ordini

**Fase 3: Valutazione**
- Verifica concordanza inventario
- Test ordini di prova
- Monitora fulfillment

---

## 16. Roadmap Kaufland API

### 16.1 Versione 2.1 (Maggio 2026)

- Bulk order operations
- Enhanced reporting with ML insights
- Dynamic pricing API
- Sustainability metrics tracking

### 16.2 Versione 3.0 (Q3 2026)

- GraphQL endpoint
- Webhooks v2 (batch delivery)
- Advanced inventory forecasting
- B2B API (marketplace to business)

### 16.3 Deprecazioni

- API v1: Fine supporto Gennaio 2027
- Legacy OAuth: Migrazione obbligatoria a OAuth 2.0

---

## Appendice: Glossario Termini

| Termine | Definizione |
|---------|------------|
| **EAN** | European Article Number, barcode univoco prodotto |
| **SKU** | Stock Keeping Unit, codice interno seller |
| **Fulfillment** | Processo da ordine a consegna |
| **Webhook** | Callback HTTP verso endpoint seller |
| **Bearer Token** | Token OAuth per autenticazione API |
| **Rate Limit** | Limite richieste per unità tempo |
| **Bulk Operation** | Operazione su molti item simultaneamente |
| **Sandbox** | Ambiente di test isolato |

---

**Versione:** 2.0
**Ultima Modifica:** Aprile 2026
**Prossima Revisione:** Maggio 2026 - Ottobre 2026 (revisione semestrale consigliata)
**Mantentore:** Kaufland Developer Relations Team
**Licenza:** Attribution-NonCommercial 4.0 (per uso commerciale consultare Kaufland)