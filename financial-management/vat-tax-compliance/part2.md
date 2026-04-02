# Domain 5.3: VAT & Tax Compliance (Part 2)

## 8. IOSS (Import One-Stop Shop) Regime

**Applicability:** Goods only (physical products from non-EU origins), value ≤€150 per shipment, EU-based customers ordering from non-EU seller. Examples: Chinese supplier selling via Shopify to German customer, UK seller to Italian buyer (post-Brexit).

For Vendiamonoi.it: If sourcing from China/Asia ≤€150 per unit and selling B2C to EU, must register IOSS even though based in Italy. IOSS handles VAT at point of import.

**IOSS Registration:** Portal per MS (coordinated via EMCS). Italy: https://www.agenziaentrate.gov.it/portala/ioss. Required: VAT number, business address, bank account, importer details. Processing: 20-30 days. Cost: Free.

**Monthly Returns (Article 36 VAT Directive):**
- Period: Calendar month
- Deadline: 15th following month (Jan sales due Feb 15, etc.)
- Submission: Electronic via national portal
- Payment: Due same day as return

**H7 Declaration:** Supplementary VAT for IOSS imports. When required: goods arrive at EU border for B2C. Content: buyer details, HS code, value, destination MS. Deadline: 30 days. Filed by: customs broker or IOSS representative.

**IOSS Monthly Example (March 2026, imported from China):**
- Germany: 45 units × €120 = €5,400 → 19% VAT = €1,026
- France: 28 units × €135 = €3,780 → 20% VAT = €756
- Poland: 32 units × €110 = €3,520 → 23% VAT = €810
- Spain: 25 units × €125 = €3,125 → 21% VAT = €657
- Netherlands: 30 units × €140 = €4,200 → 21% VAT = €882
- **Total revenue:** €20,025 | **Total VAT:** €4,131 | **Payment deadline:** April 15

**Intermediary Role (Article 35):** Person/company acting on behalf of IOSS seller. Use case: customs broker handles IOSS compliance. Responsibilities: register IOSS system, file monthly returns, remit VAT, maintain 10-year records. Relationship: signed agreement, acts as VAT representative, seller ultimately liable if intermediary fails. Recommended: €2M+ errors & omissions insurance.

**IOSS Penalties:**
- Late filing (0-30 days): 5% of VAT
- Late filing (31-60 days): 10% of VAT
- Late filing (>60 days): 15% of VAT + interest 0.5%/month
- Missing IOSS registration: €5,000-€50,000 + VAT reassessment

## 9. DAC7 (Directive 2021/514/EU) – Platform Reporting

**Overview:** DAC7 effective January 1, 2023 for reporting (filed in 2024 for 2023 data). Requires marketplaces/payment processors to report seller activity to tax authorities. Impacts 27 EU MS + Iceland, Liechtenstein, Norway, Switzerland.

**Key Provisions:**
1. Reporting Entities: Marketplace operators, payment processors
2. Reported Data: Seller identity, transaction volumes, payment details
3. Threshold: €2,000 cumulative turnover in reporting year
4. Information Exchanged: Via AEOI (Automatic Exchange) between authorities

For Vendiamonoi.it: Selling on Amazon, eBay, Allegro, Vinted = automatic DAC7 reporting. If €2,000+ annual on ANY platform → data reported to Agenzia delle Entrate. No direct seller action required (marketplace handles).

**DAC7 Data Reported:**
- Seller Info: Full name, Tax ID, address, country
- Transaction Summary: Gross revenue (all sales), transaction count, fees/commissions, refunds, suspensions
- Payment Info: Dates, amounts, currency, withholding/deductions

**DAC7 Deadlines:**
- Reporting Year: January 1 - December 31
- Reporting Deadline: March 31 following year (2025 sales → March 31, 2026)
- Seller Receives: June 30 (copy of report filed)
- Authority Exchange: October 31 (inter-MS data exchange)

**DAC7 Hypothetical (Amazon 2025):**
Amazon EU Sarl (Luxembourg), March 30, 2026 Report, Vendiamonoi.it IT12345678901:
- Total Gross Revenue: €487,230
- Transactions: 1,840
- Commissions: -€97,446 (20%)
- VAT Collected: €91,234
- Refunds: -€23,411
- Net Paid: €366,149

**Reporting Chain:** Marketplace collects data → Compiles DAC7 → Files with Luxembourg → CTIE validates/exchanges → Agenzia delle Entrate receives → Cross-references OSS/IOSS → Audit trigger if DAC7 income > OSS reported income.

**Risk Mitigation:** Ensure OSS/IOSS match platform data. Request annual sales summaries from each marketplace. Reconcile DAC7 (June 30) against returns before July 31. File amendments if discrepancies found. Keep settlement reports.

**DAC7 & OSS Reconciliation:** Amazon (deemed supplier, not in OSS) — DAC7 revenue NOT in OSS return. eBay (not deemed) — DAC7 eBay revenue MUST match OSS reported eBay.

## 10. Country-Specific Compliance

### Germany
**USt-IdNr:** Format DE + 9 digits. Applied: BZSt. Processing: 10-15 days. Cost: Free. Mandatory if: B2B supplies, EU acquisitions, IOSS/OSS.
**§22f (Reverse Charge):** B2B supplies from outside Germany. Effect: No German VAT by supplier. Responsibility: German recipient self-assesses 19%.
**LUCID:** Agricultural VAT monitoring system. Not applicable unless selling agricultural items.
**Marketplace Rules:** Amazon, eBay must report to BZSt. Quarterly returns include marketplace breakdown. E-invoicing (ZUGFeRD) mandatory for B2B as of January 2025.

### France
**TVA:** Standard 20%. Reduced: 5.5% (food), 2.1% (newspapers). Registration: Required if French revenue >€85,000/year (not just OSS).
**TVA Intra-communautaire:** French businesses buying from EU file Déclaration d'échange de biens (DEB). Vendiamonoi selling to French business ≥€1,000: file declaration, buyer files matching DEB, cross-reference via INTRASTAT.
**Marketplace Rules:** Amazon France (September 2022+): Amazon collects 20% TVA (deemed supplier, no OSS needed). eBay France: Seller responsible (not deemed). Monthly reporting: Déclaration de TVA (impots.gouv.fr).
**Record-Keeping:** 6-year retention (longer than EU standard). Digital records must be tamper-proof.

### Spain
**IVA:** Standard 21%. Reduced: 10% (food), 4% (books/medical), 0% (exports). Registration: If Spanish revenue >€3,600/year.
**SII (Real-time VAT Reporting):** Mandatory since 2017. Requirement: E-invoices with VAT declaration to Agencia Tributaria within 1 hour. For digital sellers: SII-compliant marketplace handles. Manual: DivulgaCF portal (daily). Failure: €3,000+ daily fine + 4-20% interest.
**Marketplace Reporting:** OLX, Wallapop report to Agencia Tributaria. Turnover ≥€1,500/year → automatic notification.

### Netherlands
**BTW:** Standard 21%. Reduced: 9% (food, books, medical). Registration: If Dutch revenue >€20,000/year (highest threshold in EU). OSS threshold: Still €10,000 rolling 12 months.
**BTW Returns:** Frequency: Monthly (≥€1M) or quarterly. Filing: DigiD portal. Payment: 15th following month.
**Peculiarity:** No deemed supplier concept (even Amazon collects NL VAT on behalf of sellers). Sellers on Amazon.nl must report VAT to Belastingdienst. Chain: Seller → Amazon (collects) → Seller reports → Amazon remits.

### Poland
**VAT:** Standard 23%. Reduced: 8% (basic food), 5% (services). Registration: If Polish revenue >€5,000/year. Quarterly returns: VAT-7 form, deadline 25th following month.
**Allegro:** Acts as deemed supplier for Polish VAT. Seller has no VAT liability. Allegro remits 23%. Impact: No VAT reporting for Allegro sales in OSS. No separate Polish VAT registration needed.
**E-invoicing:** Mandatory July 2024 (Jednolity Plik Kontrolny - JPK). All invoices electronic, standardized XML to Ministerstwo Finansów.

## 11. Intrastat & EC Sales List

**Intrastat:** Applies to EU trade (goods only). Threshold: €1,000,000 exports OR €2,800,000 imports. Vendiamonoi likely exceeds. Filing: Monthly. Deadline: 10th following month. Required: HS code (8 digits), value, quantity, destination MS, transport mode.

**Italian Portal:** INTRASTAT (ISTAT). Access: Cassetto Fiscale. Form: Modulo INTRASTAT. Example: April 2026, HS 4901.99.00 (books), destination DE, €12,450, 2,500 units, road transport.

**Intrastat Penalties:**
- Missing return: €500-€5,000
- Incorrect HS codes: €1,000-€10,000 + tariff reassessment
- Late (0-30 days): 5% of transaction value
- Late (>30 days): 10% of transaction value

**EC Sales List:** B2B supplies to other EU MS. Threshold: €5,000 in single MS/year. Filing: Annual (by June 15 following year). Information: Customer VAT ID, amount, invoice reference. Vendiamonoi selling B2B to France/Germany >€5,000: Report to Agenzia, customer files matching DEB, cross-check VIES.

**VIES (VAT Information Exchange):** Central EU VAT database. Italian portal: agenziaentrate.gov.it/vies. Verify trading partner VAT IDs. Before accepting reverse charge on B2B: Verify in VIES. If invalid → seller liable for VAT.

## 12. VAT Recovery & Input VAT

**Input VAT Mechanism:** Definition: VAT paid on business inputs (goods, services, transport). Recovery: Claimable against output VAT (VAT from customers). Net effect: Business acts as VAT pass-through (neutral).

**Eligible Input VAT:**
- Goods purchased: 100% recoverable
- Services: Logistics, accounting, web hosting, marketplace commissions — 100% recoverable
- Fixed assets: Shelving, furniture — capitalized/depreciated recovery
- Ineligible: Meals, vehicle fuel (50% in some MS), personal expenses

**Q2 2026 Example:**
- Output VAT (OSS): €245,600 sales → €51,234 VAT collected
- Input VAT:
  - Inventory China: €125,000 + 0% = €0
  - Amazon FBA fees: €18,000 + 19% = €3,420
  - Accounting (Italian): €2,400 + 22% = €528
  - IT services (Taxdoo): €1,500 + 22% = €330
  - Transport: €5,200 + 22% = €1,144
  - **Total input:** €5,422
- **VAT to remit:** €51,234 - €5,422 = €45,812

**Cross-Border Refund (13th Directive):** VAT paid in other EU MS not recoverable locally. Example: Warehouse in Germany, property VAT €1,200/month × 19% = €228 monthly. File refund claim with BZSt. Process: 6-12 months. Refund issued to Italian account in EUR.

**FBA VAT Implications:** Inventory to Amazon FRA: Transport no VAT (intra-company). Storage fees: Input VAT 19% recoverable. Final sales: Amazon collects (deemed supplier), seller can't reclaim input VAT on goods. Effect: Higher VAT cost for FBA vs. self-fulfillment.

**Recovery Restrictions:**
- Deemed supplier sales: Cannot claim input VAT on goods
- Exempt supplies: Cannot reclaim input VAT
- Mixed business: Pro-rata recovery (80% taxable → recover 80% input)

**Documentation:** Keep invoices, import docs, payment proofs, business purpose justification, 10-year retention.

## 13. Tax Technology Solutions

**Avalara (avalara.com):** 200+ jurisdictions, EU-focused. Features: Real-time VAT calculation, marketplace integration (Amazon, eBay, WooCommerce), OSS generation, IOSS compliance, reporting dashboard. Pricing: $1,500-$5,000/month. Setup: 2-4 weeks.

**Vertex (vertexinc.com):** 200+ jurisdictions, enterprise-focused. Specialization: Complex chains, multi-currency, transfer pricing. Features: Indirect tax determination, IOSS/OSS automation, VIES validation, audit logs. Pricing: $3,000-$10,000+/month. Setup: 6-12 weeks.

**Taxdoo (taxdoo.com) - RECOMMENDED:** 27 EU MS + UK, SME focus. Specialization: Marketplace sellers (eBay, Amazon, Etsy, Shopify). Languages: German, English, Italian, French, Polish, Spanish. Features: Marketplace data import, OSS quarterly generation, IOSS monthly filing, multi-marketplace dashboard, tax calendar, 10-year compliance checklist, Agenzia delle Entrate integration (coming 2026). Pricing: €99-€299/month. Setup: 1-2 weeks. Free trial: 30 days.

**Taxdoo Workflow:** Sign up (Italy + Marketplace Seller) → Connect marketplaces (Amazon DE/FR/IT/ES/NL/PL, eBay all regions, Allegro, Vinted) → Monthly sync → Q2 review (Germany €28,500 → €5,415 VAT, etc.) → Click "Generate OSS Return" → SPID login → Auto-upload → Payment suggestion.

**HelloTax (hellotax.com):** 50 countries, marketplace focus. Languages: English (limited Italian). Features: Amazon FBA tracking, multi-country filing (US + EU VAT), commission management, per-transaction calculator. Pricing: $9.99-$19.99/month basic to €99+/month advanced EU. Limitations: Limited OSS, no IOSS, not localized for Italian authorities. **Verdict:** Not recommended for Italian sellers.

**SimplyVAT (simplyvat.com):** UK + 27 EU MS, UK post-Brexit specialist. Features: OSS/IOSS management, multi-currency (EUR/GBP), UK MOSS, VIES integration, time-logging. Pricing: £199-£499/month. Setup: 3-4 weeks. Best for: UK sellers to EU (reverse Vendiamonoi scenario).

**Recommendation for Vendiamonoi.it:**
- Primary: Taxdoo (€99-€199/month) — Italian support, OSS+IOSS, 20+ marketplace connections, Agenzia delle Entrate compatible
- Secondary: Local accountant (€300-€500/month) — Audit representation, compliance coordination
- Tech stack: Taxdoo + Wave Accounting + Shopify/WooCommerce + marketplace APIs

## 14. Penalties & Risk Management

**VAT Registration Penalties (Italy):**
- Unregistered cross-border sales: 10-30% of turnover + VAT reassessment
- Non-filing OSS: €500-€5,000 + 0.5% interest/month
- Late OSS (0-30 days): 5% of VAT
- False/fraudulent return: 30-100% of VAT + criminal prosecution

**Record-Keeping Penalties:**
- Missing invoices (per document): €500-€5,000
- Failure to retain 10 years: Entire VAT disallowed

**Audit Red Flags:**
- DAC7 revenue ≠ OSS reported (>5% variance)
- Turnover spike without VAT increase
- Multiple marketplaces but missing OSS
- No geo-location documentation

**Audit Defense:**
1. Document everything: Geo-IP logs, shipping address confirmations, settlement reports
2. Quarterly reconciliation: Compare DAC7 expected vs. OSS filings (June 30)
3. Professional representation: Italian commercialista
4. Insurance: €2M+ professional liability

## 15. Implementation Checklist

**Phase 1 - Registration (Week 1-2):**
- [ ] Confirm Italian Partita IVA
- [ ] Register OSS (Agenzia delle Entrate)
- [ ] Register IOSS (if importing ≤€150)
- [ ] Verify VIES listing

**Phase 2 - Systems (Week 2-4):**
- [ ] Sign up Taxdoo
- [ ] Connect marketplaces (Amazon, eBay, Allegro, Vinted)
- [ ] Configure all 27 EU destinations
- [ ] Set tax calendar (Apr 20, Jul 20, Oct 20, Jan 20)

**Phase 3 - Documentation (Week 3-4):**
- [ ] Create VAT filing procedure
- [ ] Train staff on OSS deadlines
- [ ] Set up 10-year retention system
- [ ] Establish audit trail

**Phase 4 - Compliance (Quarterly):**
- [ ] Export marketplace data
- [ ] Reconcile OSS expected vs. actual
- [ ] File Modello IVA OSS (by 20th)
- [ ] Remit VAT (same deadline)
- [ ] Generate compliance report

**Phase 5 - Optimization (Quarterly+):**
- [ ] Analyze VAT rates by country
- [ ] Maximize input VAT claims
- [ ] Audit Intrastat/EC Sales List (if threshold exceeded)
- [ ] Reconcile DAC7 (June 30) vs. OSS

---

**End of Part 2: 2,150 words**
**Total Domain 5.3 Content: 4,100 words**