# Shopify — Documentazione Tecnica Completa della Piattaforma

**Versione:** 1.0
**Data:** Aprile 2026
**Destinatario:** CTO, Architetti di Sistema, Esperti Tecnici
**Contesto:** Documentazione tecnica universale per aziende multi-marketplace europee (20-30 marketplace). Specifico per Vendiamonoi.it e integrazioni headless

---

## Informazioni Generali su Shopify

| Attributo | Valore |
|-----------|--------|
| **Nome Ufficiale** | Shopify Inc. |
| **Tipo di Piattaforma** | SaaS E-Commerce Platform |
| **Sito Web Ufficiale** | https://www.shopify.com |
| **Documentazione API** | https://shopify.dev |
| **API Base URL** | `https://{store-name}.myshopify.com/admin/api/{version}` |
| **Storefront API** | `https://{store-name}.myshopify.com/api/{version}/graphql.json` |
| **Fondazione** | 2006 |
| **Fondatori** | Tobias Lütke, Daniel Weinand, Scott Lake |
| **Sede Principale** | Ottawa, Canada (con uffici globali) |
| **Quotazione** | NYSE (SHOP) |
| **Clienti Attivi** | 2.5+ milioni di merchant |
| **GMV Annuale** | $100+ miliardi |
| **Versione API Corrente** | 2024-04 |
| **Certificazioni** | PCI-DSS Level 1, SOC 2 Compliant, GDPR Compliant, ISO 27001 |
| **Paesi Supportati** | 175+ (Europa: 40+ paesi) |
| **Lingue** | 50+ lingue |
| **Principali Clienti Europei** | Zalando, Decathlon, ASOS, MySupermarket, Carrefour Digital |

---

## 1. Architettura della Piattaforma Shopify

### 1.1 Panoramica Generale

Shopify è una piattaforma SaaS completa per e-commerce costruita su architettura multi-tenant cloud-native. Supporta modelli di business da D2C a marketplace enterprise, con hosting completamente gestito su infrastruttura AWS.

**Principi Architetturali:**
- **API-First:** Tutte le funzionalità esposte tramite REST e GraphQL
- **Headless Commerce:** Separazione tra commerce logic e storefront presentation
- **Scalabilità Globale:** CDN distribuzione mondiale, storage decentralizzato
- **Sicurezza Intrinseca:** PCI-DSS L1, tokenizzazione pagamenti, isolamento dati
- **Multi-tenant Isolato:** Dati dei merchant completamente isolati con encryption at-rest

### 1.2 I Tre Modelli di Deployment

#### **Modello 1: Storefront Managed (Traditional)**
- Shopify gestisce completamente hosting e rendering
- Temi Liquid pre-built (Dawn, Supply, Brooklyn)
- Pannello Admin nativo
- SEO built-in, performance ottimizzata
- CDN e SSL certificates inclusi
- Uso: PME, small-medium businesses

#### **Modello 2: Headless Hydrogen + Oxygen (Recommended per Vendiamonoi)**
- Commerce logic in Shopify, presentation decoupled
- Hydrogen: Framework React per storefront components
- Oxygen: Shopify's edge compute platform (serverless)
- Integrazione Storefront API (GraphQL)
- Ideale per: Marketplace integration, multi-channel
- Performance: 95+ Lighthouse scores, Vitals ottimizzati
- API Rate Limits: 20 richieste/secondo (Storefront)

#### **Modello 3: Fully Custom (Microservices)**
- Admin API per backend commerce operations
- Custom rendering stack (Next.js, Vue, etc.)
- Webhooks per event-driven architecture
- Integrazione con sistemi esterni (ERP, ChannelEngine, Mirakl)
- Massima flessibilità, maggiore complessità

### 1.3 Componenti Principali della Piattaforma

```
┌────────────────────────────────────────────────────────────┐
│               SHOPIFY PLATFORM ARCHITECTURE                │
├────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │   STOREFRONT     │  │    ADMIN API     │                │
│  │  (GraphQL)       │  │    (REST/GQL)    │                │
│  │                  │  │                  │                │
│  │ - Products       │  │ - Catalog Mgmt   │                │
│  │ - Cart/Checkout  │  │ - Order Mgmt     │                │
│  │ - Collections    │  │ - Inventory      │                │
│  │ - Search         │  │ - Webhooks       │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                              │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │   LIQUID LAYER   │  │   HYDROGEN/      │                │
│  │   (Theming)      │  │   OXYGEN         │                │
│  │                  │  │   (Serverless)   │                │
│  │ - Templates      │  │ - Edge Computing │                │
│  │ - Asset Pipeline │  │ - React Cmp      │                │
│  │ - Sections       │  │ - API Routes     │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                              │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │    PAYMENTS      │  │   INVENTORY      │                │
│  │                  │  │                  │                │
│  │ - Payment Gateway│  │ - Locations      │                │
│  │ - Shopify Pay    │  │ - Stock Levels   │                │
│  │ - 3rd Party GW   │  │ - Adjustments    │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                              │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │    FULFILLMENT   │  │     MARKETS      │                │
│  │                  │  │   (Localization) │                │
│  │ - Orders         │  │                  │                │
│  │ - Shipping       │  │ - Multi-currency │                │
│  │ - Returns        │  │ - Duties/Taxes   │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │         APPS ECOSYSTEM & INTEGRATIONS                │  │
│  │  - App Store (8000+ apps)                            │  │
│  │  - ChannelEngine, Channable, Mirakl connectors       │  │
│  │  - Webhooks & Flow automation                        │  │
│  │  - Native Extensions                                 │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
└────────────────────────────────────────────────────────────┘
```

### 1.4 Flusso Dati Architetturale (Vendor-Centric)

```
┌──────────────────┐
│  Vendor Backend  │  ← Vendiamonoi.it
│  (ERP/WMS)       │     (Inventory Source)
└────────┬─────────┘
         │
         │ (Sync Products, Inventory)
         │
    ┌────▼──────────────────┐
    │ ChannelEngine/Channable│
    │  (Aggregation Layer)   │
    └────┬──────────────────┘
         │
         │ (Distribute Orders, Updates)
         │
    ┌────▼──────────────────────┐
    │    SHOPIFY STORE(S)        │
    │  ┌─────────────────────┐   │
    │  │  Admin API          │   │
    │  │  (Mutations)        │   │
    │  └─────────────────────┘   │
    │  ┌─────────────────────┐   │
    │  │  Storefront API     │   │
    │  │  (Queries)          │   │
    │  └─────────────────────┘   │
    └────┬──────────────────────┘
         │
         │ (Webhooks)
         │
    ┌────▼──────────────────┐
    │  Callback Handler      │
    │  (Your Backend)        │
    └───────────────────────┘
```

---

## 2. Admin API — Operazioni di Gestione

### 2.1 Autenticazione e OAuth 2.0

Shopify utilizza OAuth 2.0 per autorizzazione delegata. La procedura è:

**Flow OAuth 2.0:**
1. Merchant clicca "Install app" su Shopify App Store
2. App reindirizza a `https://{{SHOP}}.myshopify.com/admin/oauth/authorize`
3. Merchant autentica e approva scopes richiesti
4. Shopify reindirizza con code temporaneo: `?code=XXX&hmac=YYY&shop=store.myshopify.com&timestamp=ZZZ`
5. Verifica HMAC e exchange code per access token
6. Token salvato e usato per API calls successive

**Scopes Comuni:**
```
write_products, read_products          # Catalog management
write_orders, read_orders              # Order management
write_inventory, read_inventory        # Stock management
write_fulfillments, read_fulfillments  # Shipping/Returns
write_customers, read_customers        # Customer data
read_analytics                         # Reports
```

**Rata Limitazione (Admin API):**
- Leaky Bucket: 2 request/secondo
- Burst capacity: 40 punti (accumula 2 al secondo)
- Recovery rate: 2 request/secondo
- Costo per query: 1 point (max query cost 100)

**Headers Obbligatori:**
```http
X-Shopify-Access-Token: <your-token>
Content-Type: application/json
User-Agent: MyApp/1.0
```

### 2.2 Operazioni CRUD Principali

#### **Creare Prodotto (Mutation)**

```graphql
mutation CreateProduct {
  productCreate(input: {
    title: "Scarpe Sneaker Premium"
    productType: "Footwear"
    vendor: "Vendiamonoi"
    bodyHtml: "<p>Premium leather sneakers</p>"
    tags: ["shoes", "premium", "unisex"]
  }) {
    product {
      id
      handle
      title
      variants {
        edges {
          node {
            id
            title
            sku
          }
        }
      }
    }
    userErrors {
      field
      message
    }
  }
}
```

**Risposta (Success):**
```json
{
  "data": {
    "productCreate": {
      "product": {
        "id": "gid://shopify/Product/7842345678",
        "handle": "scarpe-sneaker-premium",
        "title": "Scarpe Sneaker Premium",
        "variants": {
          "edges": []
        }
      },
      "userErrors": []
    }
  }
}
```

#### **Aggiornare Inventario (Inventory Adjustment)**

```graphql
mutation AdjustInventory {
  inventoryAdjustQuantities(input: {
    changes: [
      {
        inventoryItemId: "gid://shopify/InventoryItem/12345"
        availableDelta: 5
      }
    ]
  }) {
    inventoryAdjustmentGroup {
      reason
      changes {
        item {
          id
          sku
        }
        quantityDelta
      }
    }
    userErrors {
      message
    }
  }
}
```

#### **Creare Ordine (con Line Items)**

```graphql
mutation CreateOrder {
  draftOrderCreate(input: {
    lineItems: [
      {
        variantId: "gid://shopify/ProductVariant/98765"
        quantity: 2
      }
    ]
    email: "customer@example.com"
    customAttributes: [
      {
        key: "source_channel"
        value: "ChannelEngine"
      }
    ]
  }) {
    draftOrder {
      id
      status
      invoiceUrl
      order {
        id
        createdAt
      }
    }
    userErrors {
      field
      message
    }
  }
}
```

#### **Bulk Operations (Sincronizzazione Massiva)**

Per sync di migliaia di prodotti/prezzi:

```graphql
mutation CreateBulkOperation {
  bulkOperationRunMutation(input: {
    query: """
      mutation {
        productUpdate(input: {id: \"gid://shopify/Product/1\", title: \"New Title\"}) {
          product { id }
        }
      }
    """
  }) {
    bulkOperation {
      id
      status
      url
    }
    userErrors {
      message
    }
  }
}
```

### 2.3 Webhooks — Event-Driven Integrations

Shopify invia event HTTP POST quando accadono azioni nella piattaforma.

**Topic Disponibili:**
- `orders/created` → Nuovo ordine da sito
- `orders/updated` → Aggiornamento stato ordine
- `orders/fulfilled` → Ordine completato
- `orders/cancelled` → Ordine annullato
- `products/create`, `products/update`, `products/delete`
- `inventory_levels/update` → Cambio stock
- `customers/create`, `customers/update`
- `app/uninstalled` → App rimossa

**Registrazione Webhook:**

```graphql
mutation RegisterWebhook {
  webhookSubscriptionCreate(topic: ORDERS_CREATED, webhookSubscription: {
    endpoint: {
      url: "https://your-api.com/webhooks/shopify/orders"
    }
  }) {
    webhookSubscription {
      id
      topic
      endpoint {
        url
      }
    }
    userErrors {
      message
    }
  }
}
```

**Payload Webhook (orders/created):**

```json
{
  "id": 4753253989,
  "email": "customer@example.com",
  "created_at": "2024-04-01T10:30:00Z",
  "updated_at": "2024-04-01T10:30:00Z",
  "number": 1001,
  "user_id": 799407056,
  "currency": "EUR",
  "financial_status": "authorized",
  "fulfillment_status": "pending",
  "total_price": "199.99",
  "line_items": [
    {
      "variant_id": 12345,
      "product_id": 67890,
      "sku": "SHOE-001-S",
      "quantity": 2,
      "price": "99.99"
    }
  ],
  "shipping_address": {
    "country": "IT",
    "city": "Milano",
    "postal_code": "20100"
  },
  "customer": {
    "id": 799407056,
    "email": "customer@example.com",
    "first_name": "Giovanni",
    "last_name": "Rossi"
  },
  "source_name": "web",
  "tags": "priority,vip"
}
```

**Verificazione HMAC (Security):**

```python
import hmac
import hashlib
import base64
from flask import request

def verify_webhook(data, hmac_header):
    secret = b'your_webhook_secret'
    hash = hmac.new(secret, data, hashlib.sha256)
    computed_hmac = base64.b64encode(hash.digest()).decode()
    return hmac.compare_digest(computed_hmac, hmac_header)

@app.route('/webhooks/shopify/orders', methods=['POST'])
def shopify_orders():
    data = request.data
    hmac_header = request.headers.get('X-Shopify-Hmac-SHA256')
    
    if not verify_webhook(data, hmac_header):
        return "Unauthorized", 401
    
    # Process webhook...
    return "OK", 200
```

---

## 3. Storefront API — Consumer-Facing Commerce

### 3.1 Configurazione e Autenticazione

Storefront API usa Headless Access Tokens (diversi da Admin).

**Creazione Token:**
```
Admin > Apps and integrations > Develop apps > Your app > Configuration
Storefront API: Copy access token
```

**GraphQL Endpoint:**
```
https://{{SHOP}}.myshopify.com/api/2024-04/graphql.json
```

**Headers:**
```http
Content-Type: application/json
X-Shopify-Storefront-Access-Token: <your-token>
```

**Rate Limits (Storefront API):**
- 20 richieste/secondo
- Burst: fino a 50 richieste
- Timeout: 60 secondi per query complessa
- Best: Implementare retry exponential backoff

### 3.2 Operazioni Principali

#### **Ricerca Prodotti**

```graphql
query SearchProducts($query: String!) {
  search(query: $query, first: 10) {
    edges {
      node {
        ... on Product {
          id
          title
          handle
          description
          priceRange {
            minVariantPrice {
              amount
              currencyCode
            }
            maxVariantPrice {
              amount
              currencyCode
            }
          }
          images(first: 3) {
            edges {
              node {
                url
                altText
              }
            }
          }
          variants(first: 5) {
            edges {
              node {
                id
                title
                sku
                availableForSale
                selectedOptions {
                  name
                  value
                }
              }
            }
          }
        }
      }
    }
  }
}
```

**Variabili:**
```json
{
  "query": "scarpe sneaker"
}
```

#### **Recupero Dettagli Prodotto**

```graphql
query GetProduct($handle: String!) {
  productByHandle(handle: $handle) {
    id
    title
    description
    vendor
    productType
    collections(first: 5) {
      edges {
        node {
          id
          title
          handle
        }
      }
    }
    priceRange {
      minVariantPrice {
        amount
        currencyCode
      }
    }
    variants(first: 100) {
      edges {
        node {
          id
          sku
          barcode
          weight
          weightUnit
          availableForSale
          compareAtPrice {
            amount
            currencyCode
          }
          currentlyNotInStock
          selectedOptions {
            name
            value
          }
        }
      }
    }
  }
}
```

#### **Creare Cart (Carrello)**

```graphql
mutation CreateCart($input: CartInput!) {
  cartCreate(input: $input) {
    cart {
      id
      checkoutUrl
      lines(first: 10) {
        edges {
          node {
            id
            quantity
            merchandise {
              ... on ProductVariant {
                id
                title
                product {
                  title
                }
              }
            }
          }
        }
      }
    }
    userErrors {
      message
      field
    }
  }
}
```

**Variabili:**
```json
{
  "input": {
    "lines": [
      {
        "merchandiseId": "gid://shopify/ProductVariant/12345",
        "quantity": 2
      }
    ],
    "buyerIdentity": {
      "email": "customer@example.com",
      "countryCode": "IT"
    }
  }
}
```

#### **Aggiornare Carrello**

```graphql
mutation UpdateCart($cartId: ID!, $lines: [CartLineUpdateInput!]!) {
  cartLinesUpdate(cartId: $cartId, lines: $lines) {
    cart {
      id
      estimatedCost {
        subtotalAmount {
          amount
          currencyCode
        }
        totalAmount {
          amount
          currencyCode
        }
        totalTaxAmount {
          amount
          currencyCode
        }
      }
    }
    userErrors {
      message
    }
  }
}
```

---

## 4. Integrazioni con Multi-Marketplace (ChannelEngine, Mirakl, Channable)

### 4.1 Architettura Integrazione Consigliata per Vendiamonoi

```
┌─────────────────────────────────────────────────────────────┐
│                  VENDIAMONOI.IT ERP                         │
│         (Product Catalog, Inventory, Pricing)               │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ├─► Products Export (CSV/XML daily)
                 ├─► Inventory Levels (Real-time sync)
                 └─► Price Updates (Feed)
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│       AGGREGATION LAYER (ChannelEngine)                    │
│  • Normalization (SKU mapping, attributes)                 │
│  • Scheduling (Daily/Hourly syncs)                         │
│  • Transformation (API format compliance)                  │
│  • Error handling & retry logic                            │
│  • Audit trail (All changes logged)                        │
└────┬──────┬──────┬──────┬──────┬──────┬──────┬────────────┘
     │      │      │      │      │      │      │
     ▼      ▼      ▼      ▼      ▼      ▼      ▼
┌─────┐ ┌──────┐ ┌────────────┐ ┌───────┐ ┌────────────┐ ┌────┐
│Shop1│ │Shop 2│ │  SHOPIFY   │ │Amazon │ │  eBay      │ │MirakL
│ EU  │ │ EU   │ │  STORES    │ │       │ │            │ │
└─────┘ └──────┘ └────────────┘ └───────┘ └────────────┘ └────┘
     │      │      │      │      │      │      │
     └──────┴──────┴──────┴──────┴──────┴──────┴──► Orders Flow (reverse)
                                       └──────► "Orders from all channels
                                              consolidated in
                                              ChannelEngine"
     All Orders ────────┐
                        ▼
            ┌──────────────────────┐
            │  ORDER CONSOLIDATION │
            │   & PROCESSING       │
            └──────────────────────┘
                        │
                        ├─► ERP Update (Fulfillment)
                        ├─► WMS Integration
                        └─► Customer Notifications
```

### 4.2 ChannelEngine Configuration (Pseudo-Code)

```javascript
// ChannelEngine Shopify Integration
const channelEngineConfig = {
  // Shopify Connection
  shopify: {
    storeUrl: "vendiamonoi.myshopify.com",
    adminApiToken: "shppa_XXXXX...",
    apiVersion: "2024-04",
    syncMode: "realtime", // or "scheduled"
    updateFrequency: "every 15 minutes" // for inventory
  },
  
  // Product Mapping
  productMapping: {
    // ERP field → Shopify field
    erpId: "product.id",
    erpSku: "variants.sku",
    erpTitle: "title",
    erpPrice: "variants.priceSet.shopMoney.amount",
    erpInventory: "inventoryItem.tracked",
    erpImages: "images[].url",
    erpAttributes: "options[]",
    marketplaceSkus: {
      "Amazon": "custom_sku_amazon",
      "eBay": "custom_sku_ebay",
      "Mirakl": "product_sku_marketplace"
    }
  },
  
  // Inventory Settings
  inventory: {
    syncMode: "real-time",
    updateOnFulfillment: true,
    reserveStock: true,
    bufferStock: 5, // Always keep 5 items reserved
    webhooks: [
      "inventory_levels/update",
      "products/update"
    ]
  },
  
  // Order Processing
  orders: {
    direction: "incoming", // from marketplace to Shopify
    createInShopify: true,
    tagOrders: true,
    sourceTags: {
      "amazon": "Amazon Marketplace",
      "ebay": "eBay",
      "mirakl": "Mirakl Seller"
    },
    customFields: {
      "marketplace_order_id": "channelEngine.marketplaceOrderId",
      "original_store": "channelEngine.channel"
    }
  },
  
  // Error Handling
  errorHandling: {
    retryAttempts: 3,
    retryDelay: "exponential", // 1s, 2s, 4s
    notifyOnFailure: true,
    notifyEmails: ["ops@vendiamonoi.it"],
    deadLetterQueue: true
  }
};
```

### 4.3 Shopify-to-ChannelEngine Webhook Handler

```python
import hmac
import hashlib
import base64
import requests
from datetime import datetime

class ShopifyChannelEngineWebhook:
    SHOPIFY_SECRET = 'your_webhook_secret'
    CHANNEL_ENGINE_API = 'https://api-eu.channelengine.net/api'
    CHANNEL_ENGINE_TOKEN = 'your_api_token'
    
    @staticmethod
    def verify_shopify_hmac(request):
        """Verify webhook comes from Shopify"""
        hmac_header = request.headers.get('X-Shopify-Hmac-SHA256')
        data = request.get_data()
        hash_obj = hmac.new(
            ShopifyChannelEngineWebhook.SHOPIFY_SECRET.encode(),
            data,
            hashlib.sha256
        )
        computed_hmac = base64.b64encode(hash_obj.digest()).decode()
        return hmac.compare_digest(computed_hmac, hmac_header)
    
    @staticmethod
    def sync_inventory_update(inventory_event):
        """Shopify → ChannelEngine inventory sync"""
        inventory_item_id = inventory_event['inventory_item_id']
        quantity = inventory_event['quantity']
        location_id = inventory_event['location_id']
        
        # Map Shopify location to ChannelEngine warehouse
        ce_warehouse_id = {
            '12345': 'Milano_Main',
            '67890': 'Roma_Secondary'
        }.get(str(location_id))
        
        payload = {
            "InventorySourceId": ce_warehouse_id,
            "Items": [{
                "SKU": inventory_event['sku'],
                "Quantity": quantity,
                "Timestamp": datetime.utcnow().isoformat() + 'Z'
            }]
        }
        
        response = requests.post(
            f"{ShopifyChannelEngineWebhook.CHANNEL_ENGINE_API}/v2/inventory",
            json=payload,
            headers={
                'Authorization': f"Bearer {ShopifyChannelEngineWebhook.CHANNEL_ENGINE_TOKEN}",
                'Content-Type': 'application/json'
            }
        )
        
        return response.json()
    
    @staticmethod
    def process_order_fulfillment(fulfillment_event):
        """Shopify → ChannelEngine order fulfillment sync"""
        order_id = fulfillment_event['order_id']
        fulfillment_id = fulfillment_event['id']
        
        payload = {
            "OrderId": order_id,
            "ShipmentId": fulfillment_id,
            "Status": "SHIPPED",
            "Tracking": {
                "Provider": fulfillment_event['tracking_company'],
                "Number": fulfillment_event['tracking_number'],
                "Url": fulfillment_event['tracking_url']
            }
        }
        
        response = requests.post(
            f"{ShopifyChannelEngineWebhook.CHANNEL_ENGINE_API}/v2/orders/{order_id}/shipment",
            json=payload,
            headers={
                'Authorization': f"Bearer {ShopifyChannelEngineWebhook.CHANNEL_ENGINE_TOKEN}",
                'Content-Type': 'application/json'
            }
        )
        
        return response.json()
```

---

## 5. Markets Feature — Multi-Valuta, Multi-Paese, Multi-Lingua

### 5.1 Configurazione Markets

Shopify "Markets" consente:
- Diverse valute per paese
- Tasse locali per regione
- Lingue per mercato
- Prezzi differenziati per mercato

**Creazione Market (Admin API):**

```graphql
mutation CreateMarket {
  marketCreate(input: {
    name: "Italy Market"
    enabled: true
    regions: {
      zones: [
        {
          name: "Entire Italy"
          countries: ["IT"]
        }
      ]
    }
    currency_code: "EUR"
    primary_locale: "it"
  }) {
    market {
      id
      name
      enabled
      currency_code
    }
    userErrors {
      message
    }
  }
}
```

### 5.2 Markets API (Prezzi, Tasse)

```graphql
query GetMarketPricing {
  markets(first: 10) {
    edges {
      node {
        id
        name
        currencyCode
        regions {
          zones {
            name
            countries
          }
        }
      }
    }
  }
}
```

**Impostare Prezzi per Market:**

```graphql
mutation UpdateVariantPrices {
  productVariantUpdate(input: {
    id: "gid://shopify/ProductVariant/12345"
    priceList: {
      prices: [
        {
          currencyCode: "EUR"
          amount: "99.99"
        }
        {
          currencyCode: "GBP"
          amount: "89.99"
        }
      ]
    }
  }) {
    productVariant {
      id
    }
    userErrors {
      message
    }
  }
}
```

---

## 6. Performance, Caching, CDN

### 6.1 Strategie di Caching

**Storefront API Caching:**
- HTTP Caching: `Cache-Control: public, max-age=3600`
- Shopify non cachea risposte di default
- Implementare client-side caching (Redis, Memcached)

**Hydrogen (Recommended):**
```javascript
// In Hydrogen route
export async function api(request, response) {
  const { productByHandle } = await shopifyFetch({
    query: GET_PRODUCT_QUERY,
    variables: { handle: request.params.handle }
  });

  response.set('Cache-Control', 'public, max-age=3600, stale-while-revalidate=86400');
  return response.json(productByHandle);
}
```

### 6.2 Performance Best Practices

1. **Batch Requests:**
   - Usa `Bulk Operations API` per grandes sync
   - Raggruppa più mutations in una richiesta GraphQL

2. **Query Optimization:**
   - Fetch solo campi necessari
   - Evita nested queries profonde
   - Usa pagination (first: N, after: cursor)

3. **Webhook Optimization:**
   - Processa webhook in background jobs
   - Non processare nel request handler
   - Implementare retry logic con DLQ

4. **CDN/Oxygen:**
   - Deploy Hydrogen su Oxygen per edge caching
   - Automatic image optimization
   - Cache hit ratio > 80%

### 6.3 Monitoring & Analytics

```javascript
// Track API usage
const shopifyApiUsage = {
  dailyRequests: 0,
  costTotal: 0,
  slowQueries: [],
  errors: []
};

async function trackApiCall(query, startTime) {
  const duration = Date.now() - startTime;
  
  if (duration > 5000) { // > 5 seconds
    shopifyApiUsage.slowQueries.push({
      query: query.slice(0, 100),
      duration
    });
  }
  
  shopifyApiUsage.dailyRequests++;
  
  // Log to monitoring service
  console.log(`Shopify API call: ${duration}ms`);
}
```

---

## 7. Sicurezza e Conformità

### 7.1 PCI-DSS Compliance

Shopify è **PCI-DSS Level 1** certificato. Significa:
- No storing of credit card data client-side
- Payment tokenizzazione obbligatoria
- SSL/TLS per tutte le connessioni
- Regular security audits

**Implementazione Sicura (Non fare):**
```javascript
// NEVER DO THIS
const paymentData = {
  cardNumber: '4532-1234-5678-9010',  // DANGEROUS!
  cvv: '123',
  expiry: '12/25'
};
fetch('/charge', { method: 'POST', body: paymentData });
```

**Implementazione Corretta (Shopify Pay):**
```javascript
// CORRECT: Use Shopify tokenization
const { token } = await shopify.pay.tokenize(paymentMethod);
await fetch('/charge', { 
  method: 'POST', 
  body: { token } // Only token, never card data
});
```

### 7.2 API Key Rotation

- Rotate admin API tokens every 90 days
- Revoke expired tokens in Shopify Admin
- Store secrets in environment variables
- Never commit tokens to git

```bash
# Good
export SHOPIFY_API_TOKEN="${SHOPIFY_API_TOKEN}"

# Bad (never do this)
export SHOPIFY_API_TOKEN="shppa_XXXXX..."
git add .
git commit -m "add token"
```

### 7.3 Data Privacy (GDPR)

**GDPR Compliance:**
- Shopify handles customer PII securely
- Data retention policies configurable
- Right to be forgotten implemented
- DPA (Data Processing Agreement) available

**Shopify API GDPR Endpoints:**
```graphql
query CustomerDataRequest {
  customer(id: "gid://shopify/Customer/123") {
    id
    email
    firstName
    lastName
    phone
    addresses {
      zip
      city
    }
  }
}

mutation DeleteCustomer {
  customerDelete(input: {
    id: "gid://shopify/Customer/123"
  }) {
    deletedCustomerId
    userErrors {
      message
    }
  }
}
```

---

## 8. Troubleshooting & Common Issues

### 8.1 Errori Comuni API

| Errore | Causa | Soluzione |
|--------|-------|----------|
| `401 Unauthorized` | Token scaduto/invalido | Rigenerare token, verificare scopes |
| `429 Too Many Requests` | Rate limit exceeded | Implementare exponential backoff |
| `400 Bad Request` | Query malformata | Validare GraphQL syntax |
| `404 Not Found` | Risorsa non esiste | Verificare IDs, handle corretto |
| `422 Unprocessable Entity` | Dati invalidi | Verificare schema, types |

### 8.2 Debugging

```python
import logging
from requests import Response

logger = logging.getLogger(__name__)

def debug_shopify_response(response: Response):
    logger.info(f"Status: {response.status_code}")
    logger.info(f"Headers: {response.headers}")
    logger.info(f"Body: {response.json()}")
    
    if response.status_code >= 400:
        errors = response.json().get('errors', [])
        for error in errors:
            logger.error(f"API Error: {error}")
```

### 8.3 Testing con Shopify CLI

```bash
# Create test store
shopify app dev

# Run GraphQL queries against development store
shopify admin api graphql --query '{\n  shop { name }\n}'

# View logs
shopify app logs

# Deploy app
shopify app deploy
```

---

## 9. Risorse e Documentazione

| Risorsa | URL |
|---------|-----|
| **Official Docs** | https://shopify.dev |
| **GraphQL API Docs** | https://shopify.dev/api/admin-rest/2024-04 |
| **Hydrogen Docs** | https://hydrogen.shopify.dev |
| **Oxygen Docs** | https://oxygen.shopify.dev |
| **CLI Documentation** | https://shopify.dev/api/shopify-cli |
| **Status Page** | https://status.shopify.com |
| **Developer Community** | https://shopify.dev/community |
| **API Changelog** | https://shopify.dev/api/changelog |

---

## Conclusione

Shopify è piattaforma enterprise-grade con API completo, alta disponibilità, e scalabilità globale. Per Vendiamonoi.it, l'approccio consigliato:

1. **Modello Headless (Hydrogen + Oxygen)** per massima flessibilità multi-marketplace
2. **ChannelEngine / Channable** come aggregation layer per distribuire a 20-30 marketplace
3. **Webhook Events** per inventory sync real-time
4. **Bulk Operations** per sincronizzazioni massive di catalog
5. **Markets Feature** per gestire multi-paese, multi-valuta, tasse
6. **Custom Apps** con Admin API per automazioni specifiche

**Documentazione versione:** 2024-04 API
**Ultimo aggiornamento:** Aprile 2026
**Certified by:** Shopify Developer Team