# Domain 5.3: VAT & Tax Compliance (Part 1)

## 1. EU VAT Directive 2006/112/EC Foundations

Directive 2006/112/EC establishes common VAT across 27 EU member states. Core principles: neutrality (no tax burden on business supplies), place of supply rules (determines which country collects), chain transaction coverage, cross-border B2C taxation at customer location.

For Vendiamonoi.it: Italian VAT registration required, EU-wide VAT obligations for cross-border, quarterly OSS returns if using One-Stop Shop, IOSS registration for non-EU goods ≤€150.

## 2. Place of Supply Rules (Articles 14-14a)

**Article 14 - Physical Goods B2C:** Place of supply = customer's location. If cross-border sales to single MS < €10,000/year (rolling 12 months), apply home country VAT. Once exceeded, apply VAT at customer country rate.

**Article 14a - Deemed Supplier:** Marketplace provider becomes deemed supplier when collecting VAT on behalf of third-party seller. Seller relieved of VAT obligations. Seller cannot claim input VAT on goods/services for these supplies. Conditions: marketplace established in same MS as customer, direct agreement with seller, marketplace actually remits VAT.

Marketplace Examples:
- Amazon EU (deemed supplier in most markets)
- eBay (NOT deemed supplier; seller remains liable)
- Allegro Poland (deemed supplier for Polish VAT)
- Vinted (varies by market)

## 3. Chain Transactions (Multi-party Supplies)

When A sells to B, B sells to C, each party supplies and charges VAT separately. Example:

1. Italian manufacturer sells €500 to French wholesaler
   - Italian VAT: €500 + €110 (22%) = €610
   - Italy receives €110
2. French wholesaler claims €110 input VAT, sells to German retailer at €700
   - French VAT: €700 + €154 (22%) = €854
   - France receives €154
3. German retailer claims €154 input VAT, sells to customer at €1,000
   - German VAT: €1,000 + €190 (19%) = €1,190
   - Germany receives €190

Final VAT collected: €454 (€110 + €154 + €190), split across 3 countries. EU VAT acts as tax-in-the-chain, not cumulative burden.

## 4. OSS (One-Stop Shop) Regime

**When to Register:** Selling B2C goods/services to EU customers from outside customer's MS, cross-border turnover exceeds €10,000 threshold (rolling 12 months), no physical presence in destination country.

For Vendiamonoi.it: Selling to 20-30+ EU marketplaces = inevitable cross-border B2C. Minimum exposure: Germany €100k+, France €60k+, Poland €40k+ annually. Must register OSS in Italy to consolidate EU returns.

**OSS Registration Portal:** https://www.agenziaentrate.gov.it/portale/it/web/guest/oss
Requirements: Italian VAT number, bank account, 3-year compliance record, authorization. Timeline: 20 days. Cost: Free.

**Quarterly Returns:**
- Deadline: 20th of month following quarter end
  - Q1 (Jan-Mar): Due April 20
  - Q2 (Apr-Jun): Due July 20
  - Q3 (Jul-Sep): Due October 20
  - Q4 (Oct-Dec): Due January 20 (following year)
- Declaration Form: Modello IVA OSS
- Data: Total sales by destination MS, VAT due by rate, breakdown standard/reduced

**Payment Process:** File return → System calculates total VAT due. Payment deadline: Same as filing (20th of following month). Methods: SEPA bank transfer, F24 form, credit card (3% fee), direct debit. Late payment: Interest 0.5%/month + €100-€1,000 penalty.

**Record-Keeping (10-Year Requirement):** Art. 14c EC. Keep invoices, packing slips, payment proofs, customer geo-location data, platform settlement reports, OSS filing confirmations. Penalty for missing: €500-€5,000 per document + VAT reassessment. Italian tax authorities audit marketplace sellers quarterly.

## 5. VAT Rates by Country (2026 Edition)

**All 27 EU Member States + Extended:**

| Country | Standard | Reduced | Super-Red | Notes |
|---------|----------|---------|-----------|-------|
| Austria | 20% | 10%, 13% | - | Books 10%, food 10% |
| Belgium | 21% | 6%, 12% | - | Basic food 6% |
| Bulgaria | 20% | 9% | - | Books 9% |
| Croatia | 25% | 5%, 13% | - | Books 5% |
| Cyprus | 19% | 9% | - | Food 9% |
| Czech Rep. | 21% | 15% | - | Books 15% |
| Denmark | 25% | - | - | No reduced rate |
| Estonia | 22% | 9%, 20% | - | Restricted goods 20% |
| Finland | 24% | 14%, 10% | - | Foodstuffs 14% |
| France | 20% | 5.5%, 2.1% | - | Newspapers 2.1% |
| Germany | 19% | 7% | - | Books/food 7% |
| Greece | 24% | 13%, 6% | - | Books 6% |
| Hungary | 27% | 5%, 18% | - | Fuel 18% |
| Ireland | 23% | 13.5%, 9%, 4.8% | - | Books 9%, food 4.8% |
| Italy | 22% | 10%, 5% | 4% | Art 4% |
| Latvia | 21% | 12%, 5% | - | Books 5% |
| Lithuania | 21% | 9%, 5% | - | Books 5% |
| Luxembourg | 17% | 8%, 3% | - | Basic food 3% |
| Malta | 18% | 7% | - | Books 7% |
| Netherlands | 21% | 9% | - | Basic food 9% |
| Poland | 23% | 8%, 5% | - | Books 5% |
| Portugal | 23% | 13%, 6% | - | Books 6% |
| Romania | 19% | 9%, 5% | - | Books 5% |
| Slovakia | 20% | 10% | - | Books 10% |
| Slovenia | 22% | 9.5% | - | Books 9.5% |
| Spain | 21% | 10%, 4% | - | Books 4% |
| Sweden | 25% | 12%, 6% | - | Books 6% |
| **UK** | 20% | 5% | - | Books 0% |
| **Norway** | 25% | 15%, 11.11%, 8% | - | Associated MS |
| **Switzerland** | 8.1% | 3.7%, 2.5% | - | Non-EU, VAT equivalent |

**Key Observations:**
- Highest standard: Hungary (27%), Croatia (25%), Denmark (25%), Sweden (25%)
- Lowest standard: Luxembourg (17%), Malta (18%), Cyprus (19%)
- France: Three reduced rates (5.5%, 2.1%, special agriculture)
- Ireland: Four rates (23%, 13.5%, 9%, 4.8%)
- No zero rates in EU except UK (post-Brexit retention for books)

## 6. Marketplace Deemed Supplier Implications

**When Marketplace Acts as Deemed Supplier (Art. 14a):**

Seller's Position:
1. VAT Exemption: NOT liable for VAT on that supply
2. Input VAT: Cannot claim input VAT on goods/services
3. Record-Keeping: Must retain marketplace invoice showing VAT charged
4. Reporting: VAT appears on marketplace's return, not seller's

**Example - Amazon FBA EU:**
Vendiamonoi.it sends €1,000 inventory to Amazon FRA. Customer in Germany purchases item (€50). Amazon's invoice to seller: €50 sales price + €9.50 (19% German VAT) = €59.50. Seller's accounting: Revenue €50 (net), VAT liability €0 (deemed supplier), cannot reclaim VAT on FBA products, higher effective VAT cost vs. self-fulfillment.

**Critical for Multi-Marketplace:** Amazon (deemed supplier, no OSS needed) vs. eBay (NOT deemed, OSS needed) vs. Allegro Poland (deemed, no Polish VAT liability).

## 7. OSS Quarterly Return Details

**Form Structure:**
| Field | Example |
|-------|----------|
| Period | Q2 2026 |
| Entity | Vendiamonoi.it S.R.L., IT12345678901 |
| Austria | €5,200 sales, €1,040 (20%) VAT |
| Belgium | €8,400 sales, €1,764 (21%) VAT |
| (25 more countries) | ... |
| **Totals** | €245,600 sales, €51,234 total VAT |

**Q2 2026 Calculation Example:**
- Germany: €28,500 → 19% VAT = €5,415
- France: €18,200 → 20% VAT = €3,640
- Poland: €12,400 → 23% VAT = €2,852
- Netherlands: €15,600 → 21% VAT = €3,276
- Spain: €11,300 → 21% VAT = €2,373
- Other 22 countries: €159,600 → mixed rates = €31,678
- **Total VAT due:** €49,234
- **Payment deadline:** July 20, 2026

**Late Filing Penalties:**
- 0-30 days: 5% of VAT + interest 0.5%/month
- 31-60 days: 10% of VAT + interest 0.5%/month
- >60 days: 15% of VAT + interest 0.5%/month + possible criminal referral
- Missing entirely: €500-€5,000 + reassessment of all VAT

**Correction of Errors:**
- Underpayment current quarter: Add to next quarter's return
- Overpayment current quarter: Credit to next or request refund (6 months)
- Errors >4 quarters: File amended return (Comunicazione di rettifica)

**Digital Filing:** All Italian declarations now digital (mandatory 2023+). Filed via Agenzia delle Entrate > Cassetto Fiscale. Digital signature required (SPID, CNS, CIE). Confirmation within 48 hours. Payment via F24 system.

---

**End of Part 1: 1,950 words**