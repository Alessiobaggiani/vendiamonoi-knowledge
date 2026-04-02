# Domain 5.6 — Financial Automation & Reconciliation - PART 2
## Bank Reconciliation, Invoice Automation, FatturaPA & SDI

(Lines 751-1400 of comprehensive guide)

---

## 4. BANK RECONCILIATION (Qonto + PSD2 Open Banking)

### 4.1 Qonto Bank Account Integration

**Qonto API Endpoint:** `https://api.qonto.eu/v2/`

**Authentication:** API Key + signature
**Rate Limit:** 100/min

**Key Endpoints:**

```
GET /transactions
  - Returns all transactions for organization
  - Params: 
    - account_slug (e.g., "eur-account-1")
    - from, to (date range, 100-day max window)
    - current_page, items_per_page (default 100)
  - Returns: txn_id, amount, timestamp, label, category
  
GET /accounts/{id}
  - Account details (IBAN, balance, currency)
  
POST /webhooks/subscriptions
  - Subscribe to real-time txn notifications (optional)
```

**Example Qonto Transaction:**

```json
{
  "transaction": {
    "transaction_id": "qonto-tx-20260401-001",
    "settled_at": "2026-04-01T14:30:00Z",
    "side": "debit",
    "amount_cents": 4149,
    "currency": "EUR",
    "label": "AMAZON EU SARL",
    "note": "Marketplace settlement",
    "category": "marketplace_deposits",
    "reference": "111-1234567-1234567"
  }
}
```

**Reconciliation Workflow:**

1. **Daily Import** (6 AM) — Fetch all transactions from past 3 days
2. **Match to GL** — Link each bank transaction to marketplace posting
3. **Identify Variance** — Flag transactions not yet matched
4. **FX Conversion** (if multi-currency) — Apply daily ECB rates
5. **Exception Handling** — Create tasks for unmatched items

---

### 4.2 Multi-Currency Deposits & FX Handling

**Vendiamonoi operates on 8-12 currencies (GBP, PLN, HUF, CZK, SEK, DKK, etc.)**

**FX Conversion Hierarchy:**

1. **Marketplace settlement currency** — Each marketplace reports in local currency
2. **Bank deposit currency** — Usually EUR for EU operations (sometimes GBP for UK)
3. **Reporting currency** — EUR (company accounting)

**Example Scenario:**

```
eBay.uk settlement (GBP):
  Gross sales: £1,200.00
  Commissions: -£180.00
  Net: £1,020.00
  Settlement date: 2026-04-01

Qonto bank deposit (EUR):
  Amount: €1,198.45
  Deposit date: 2026-04-03 (2-day clearing)
  
FX Calculation:
  Marketplace amount: £1,020.00
  Rate (ECB 2026-04-03): 1 GBP = €1.1751
  Expected EUR: €1,198.65
  Actual EUR: €1,198.45
  FX Gain/Loss: -€0.20 (slight loss)
  
GL Posting:
    DR Bank (Qonto) €1,198.45
    DR FX Loss €0.20
    CR Marketplace Revenue €1,198.65
```

---

## 5. INVOICE AUTOMATION (OCR + AI)

### 5.1 Supplier Invoice Capture

**Workflow:**

```
Paper/PDF Invoice
  ↓
Upload (email, portal, or automated folder)
  ↓
OCR Engine (Google Vision API or Tesseract)
  ↓
AI Extraction (invoice fields: vendor, amount, VAT, date, PO ref)
  ↓
Validation (required fields present?)
  ├─ Pass → Next step
  └─ Fail → Flag for manual entry
  ↓
Duplicate Check (by invoice #, vendor, amount)
  ├─ Duplicate found → Quarantine
  └─ New invoice → Process
  ↓
Matching to Purchase Order
  ├─ Match by PO# → Automatic approval
  ├─ No PO → Requires manual approval
  └─ PO mismatch → Flag variance
  ↓
Posting to AP (Accounts Payable)
```

**Tools Comparison:**

| Tool | OCR Accuracy | Field Extraction | Cost | Notes |
|------|-------------|-----------------|------|-------|
| Google Vision API | 95% | 85% (requires own logic) | €3.5/1000 pages | Fast, reliable |
| AWS Textract | 97% | 90% (structured output) | €1.5/page | Better for forms |
| Microsoft Azure Form Recognizer | 96% | 92% (pre-trained models) | €2/page | Good for invoices |
| Tesseract (open-source) | 88% | 60% (requires rules) | Free | Labor-intensive |

---

### 5.2 Automated Customer Invoicing

**Trigger:** Daily at 7 PM for orders shipped that day + marketplace fees

**Automated Invoice Generation:**

```python
def generate_customer_invoice(orders_shipped_today):
    """
    Creates customer invoice from marketplace orders
    """
    invoices = {}
    
    for order in orders_shipped_today:
        customer_id = order.customer_id
        if customer_id not in invoices:
            invoices[customer_id] = {
                "invoice_date": datetime.now().date(),
                "invoice_number": generate_invoice_number(),
                "line_items": [],
                "total": 0
            }
        
        # Add line item
        line_item = {
            "description": order.product_name,
            "quantity": order.quantity,
            "unit_price": order.selling_price,
            "line_total": order.quantity * order.selling_price
        }
        invoices[customer_id]["line_items"].append(line_item)
        invoices[customer_id]["total"] += line_item["line_total"]
    
    # Calculate VAT (22% for Italy)
    for customer_id, invoice_data in invoices.items():
        invoice_data["vat_amount"] = invoice_data["total"] * 0.22
        invoice_data["total_with_vat"] = invoice_data["total"] + invoice_data["vat_amount"]
        
        # Send via FatturaPA
        send_fattura_pa(customer_id, invoice_data)
    
    return invoices
```

---

### 5.3 Credit Notes & Returns Processing

**Trigger:** Refund initiated by customer or marketplace chargeback

**Credit Note Workflow:**

```
Refund Request
  ↓
Calculate Credit Amount
  ├─ Product refund: selling price - restocking fee (10-20%)
  ├─ Shipping refund: full or proportional
  └─ Marketplace fee credit: usually refunded by marketplace
  ↓
Generate Credit Note (FatturaPA format)
  ├─ Reference original invoice #
  ├─ List reversed items (negative amounts)
  └─ Set GL code to "Sales Returns"
  ↓
Post to AR
  ├─ Reduce customer receivable
  └─ Increase sales returns expense
  ↓
Send to Customer (FatturaPA SDI)
```

---

## 6. FATTURA ELETTRONICA (FatturaPA) & SDI Integration

### 6.1 FatturaPA XML Standard (Italian E-Invoicing)

**Mandatory since:** January 2019 for all B2B invoices in Italy
**Format:** XML (UBL 2.1)
**Transmission:** Via SDI (Sistema di Interscambio, Italian Electronic Invoicing Exchange)

**Complete FatturaPA Invoice Structure (Simplified):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<p:FatturaElettronica versione="FPR12">
  <!-- HEADER -->
  <FatturaElettronicaHeader>
    <DatiTrasmissione>
      <ProgressivoInvio>00001</ProgressivoInvio>
      <FormatoTrasmissione>FPR12</FormatoTrasmissione>
      <CodiceDestinatario>XXXXXXX</CodiceDestinatario>
      <PECDestinatario>customer@pec.it</PECDestinatario>
    </DatiTrasmissione>
    
    <!-- ISSUER (Vendiamonoi) -->
    <CedentePrestatore>
      <DatiAnagrafici>
        <IdFiscaleIVA>
          <IdPaese>IT</IdPaese>
          <IdCodice>12345678901</IdCodice>
        </IdFiscaleIVA>
        <Anagrafica>
          <Denominazione>Vendiamonoi S.R.L.</Denominazione>
        </Anagrafica>
        <RegimeFiscale>RF01</RegimeFiscale>
      </DatiAnagrafici>
      <Sede>
        <Indirizzo>Via Roma 123</Indirizzo>
        <CAP>00100</CAP>
        <Comune>Roma</Comune>
        <Provincia>RM</Provincia>
        <Nazione>IT</Nazione>
      </Sede>
    </CedentePrestatore>
    
    <!-- RECIPIENT (Customer) -->
    <CessionarioCommittente>
      <DatiAnagrafici>
        <IdFiscaleIVA>
          <IdPaese>IT</IdPaese>
          <IdCodice>98765432109</IdCodice>
        </IdFiscaleIVA>
        <Anagrafica>
          <Denominazione>Customer Company S.P.A.</Denominazione>
        </Anagrafica>
      </DatiAnagrafici>
      <Sede>
        <Indirizzo>Via Commercio 456</Indirizzo>
        <CAP>20100</CAP>
        <Comune>Milano</Comune>
        <Provincia>MI</Provincia>
        <Nazione>IT</Nazione>
      </Sede>
    </CessionarioCommittente>
  </FatturaElettronicaHeader>
  
  <!-- BODY -->
  <FatturaElettronicaBody>
    <DatiGenerali>
      <DatiGeneraliDocumento>
        <TipoDocumento>TD01</TipoDocumento>
        <Divisa>EUR</Divisa>
        <Data>2026-04-01</Data>
        <Numero>INV-2026-02150</Numero>
      </DatiGeneraliDocumento>
    </DatiGenerali>
    
    <DatiBeniServizi>
      <!-- LINE ITEM 1 -->
      <DettaglioLinee>
        <NumeroLinea>1</NumeroLinea>
        <CodiceArticolo>
          <CodiceTipo>SKU</CodiceTipo>
          <CodiceValore>CASE-001</CodiceValore>
        </CodiceArticolo>
        <Descrizione>Smartphone Case Black Leather</Descrizione>
        <Quantita>10.00</Quantita>
        <UnitaMisura>PC</UnitaMisura>
        <PrezzoUnitario>15.00</PrezzoUnitario>
        <PrezzoTotale>150.00</PrezzoTotale>
        <AliquotaIVA>22.00</AliquotaIVA>
      </DettaglioLinee>
    </DatiBeniServizi>
    
    <DatiPagamento>
      <CondizioniPagamento>TP02</CondizioniPagamento>
      <DettaglioPagamento>
        <ModalitaPagamento>BP20</ModalitaPagamento>
        <DataScadenzaPagamento>2026-05-01</DataScadenzaPagamento>
        <ImportoPagamento>244.00</ImportoPagamento>
        <IBAN>IT60X0542811101000000123456</IBAN>
      </DettaglioPagamento>
    </DatiPagamento>
  </FatturaElettronicaBody>
</p:FatturaElettronica>
```

---

### 6.2 SDI (Sistema di Interscambio) Transmission

**SDI handles:** Routing invoices to customers, validating format, confirming receipt

**Process:**

```
1. Generate FatturaPA XML (Vendiamonoi system)
2. Digitally sign with qualified certificate (PKCS#7)
3. Submit to SDI (web portal or API)
4. SDI validates:
   - Schema compliance
   - Business rule checks
   - Recipient code exists
5. If valid → route to customer's certified PEC or SDI
6. Send confirmation to issuer (success or rejection)
7. Receipt acknowledged by customer
```

**Key Validations SDI Performs:**

| Rule | Example | Action if Fail |
|------|---------|----------------|
| XML Well-formed | Check syntax | Reject immediately |
| Schema compliant | FPR12 structure | Reject |
| Required fields | Invoice #, date, VAT ID | Reject |
| Recipient exists | CodiceDestinatario valid | Reject (or send to PEC) |
| Signature valid | P7M signature check | Accept but mark for review |
| Fiscal regime valid | RF01 = standard VAT | Reject |
| VAT calculation | Sum of line VAT = total VAT | Reject |

---

### 6.3 Mandatory Fields Checklist

```
Header:
  ✓ ProgressivoInvio (sequential number)
  ✓ FormatoTrasmissione (FPR12)
  ✓ CodiceDestinatario (recipient SDI code or 0000000)
  ✓ Issuer VAT ID
  ✓ Recipient VAT ID

Body:
  ✓ TipoDocumento (TD01 = invoice)
  ✓ Data (invoice date)
  ✓ Numero (invoice number, unique)
  ✓ Description of goods/services
  ✓ Quantity
  ✓ Unit price
  ✓ VAT rate or Natura (if no VAT)
  ✓ Total amount

Payment:
  ✓ ModalitaPagamento (payment method)
  ✓ DataScadenzaPagamento (due date)
  ✓ ImportoPagamento (amount due)

Signature:
  ✓ P7M digital signature (qualified cert required)
```

---

### 6.4 Tools & Platforms for FatturaPA Management

| Tool | Monthly Cost | Features | Notes |
|------|------------|----------|-------|
| **Fatture in Cloud** | €35-99 | Cloud invoicing, auto SDI, mobile app, API | Popular in Italy, good for SMBs |
| **Aruba** | €40-120 | Certified invoicing, PKI services, storage | Enterprise-grade, strong support |
| **Danea** | €25-65 | Desktop + cloud, automatic SDI | Good UX, lower cost |
| **Telemaco** | €50-150 | Advanced features, integrations, API | For larger operations |
| **In-house (API)** | ~€500 setup | Full control, custom rules, SDI intermediary | Vendiamonoi approach (recommended) |

**Vendiamonoi Recommended Stack:**
- **Primary:** In-house integration (Python + XML generation)
- **SDI Intermediary:** Partner with Aruba or Telemaco (handles SDI routing, archival)
- **Cost:** ~€100/month + €0.50 per invoice

---

**END OF PART 2**

Continue with Part 3: Workflows, Accounting Integration, Error Handling, ROI