# Domain 6.6 — Multi-Language Support & Localization (Part 1)
## Marketplace Language Requirements & Translation Technology

---

## 1. Language Requirements by Marketplace

### 1.1 Mandatory & Recommended Languages Matrix

| Marketplace | Country | Mandatory Languages | Recommended | Legal Requirements |
|---|---|---|---|---|
| **Amazon.de** | Germany | German (DE) | English fallback | Product safety, returns in German |
| **Amazon.fr** | France | French (FR) | English fallback | Legal docs in French (CNIL) |
| **Bol.com** | Netherlands | Dutch (NL) | English | Consumer protection law (Dutch) |
| **Allegro** | Poland | Polish (PL) | English | Warranty, complaint process in Polish |
| **Cdiscount** | France | French (FR) | English | Mandatory French for all comms |
| **Kaufland** | Germany/Austria | German (DE) | English | Product info in German |
| **ManoMano** | Multi-EU | FR/DE/ES/IT/EN all supported | All five | Localized per country |
| **Fnac** | France/Belgium | French (FR) | Dutch (BE) | French mandatory |
| **Leroy Merlin** | Multi-EU | FR/IT/ES/DE | All four | Country-specific requirements |
| **Idealo.de** | Germany | German (DE) | English | German-only product comparisons |
| **eBay.de/.fr/.nl** | Multi | Country language | English | Local language for legal texts |

### 1.2 EU Legal Language Requirements

**Mandatory Local Language Communications:**
- **Returns Policy**: Must be available in local language (DIRECTIVE 2013/29/EU)
- **Warranty Information**: Consumer protection law requires local language (DGCCRF in France, BfG in Germany)
- **Safety Warnings**: Product warnings MUST be in end-user language
- **Contract Terms**: Purchase contract in buyer's language (REGULATION 2011/83/EU)
- **Invoice**: Language of marketplace (exceptions for B2B if agreed)
- **Complaint/Dispute Resolution**: Local language mandatory
- **Data Privacy (GDPR)**: Privacy policy in user's language

**Languages Required by Country:**
- Germany: German (mandatory for all product info, legal docs)
- France: French (CNIL data protection, consumer law)
- Poland: Polish (mandatory for complaints, warranty)
- Netherlands: Dutch (consumer rights, product info)
- Spain: Spanish (mandatory consumer protection)
- Italy: Italian (mandatory for local operations)

---

## 2. Translation Technology Stack

### 2.1 Machine Translation Services Comparison

#### DeepL API
**Pricing:**
- Free: 500,000 characters/month
- Pro: €4.99-9.99/month (freelancers), €720/month (business)
- Enterprise: Custom pricing

**Quality Metrics:**
- BLEU Score: 35-42 (industry-leading for European languages)
- Strengths: German↔English, French, Italian (excellent)
- Specialized: Context-aware, handles nuance well
- Speed: 150,000 chars/second API throughput

**Best for:** Product descriptions, marketing copy, customer emails

#### Google Cloud Translation (Google Translate API)
**Pricing:**
- Advanced: $16-25 per million characters
- Custom: Neural Machine Translation (NMT) available

**Quality Metrics:**
- BLEU Score: 28-35
- Strengths: Large language coverage, real-time updates
- Weakness: Less nuanced for product/legal content
- Speed: Handles high volume

**Best for:** Quick translations, high-volume product feeds

#### Amazon Translate
**Pricing:**
- $15 per million characters
- Real-time streaming available

**Quality Metrics:**
- BLEU Score: 26-33
- Strengths: Integration with AWS ecosystem
- Weakness: Lower quality than DeepL for EU languages
- Speed: Built-in batch processing

**Best for:** AWS-integrated workflows, document processing

#### Microsoft Translator
**Pricing:**
- Standard: $10-30 per million characters
- Custom: €50-200/month glossary management

**Quality Metrics:**
- BLEU Score: 27-34
- Strengths: Office 365 integration, domain customization
- Weakness: Learning curve for configuration
- Speed: Good for batch operations

**Best for:** Enterprise integration, custom terminology

### 2.2 Recommended Hybrid Workflow: MT + Human Review

```
[SOURCE CONTENT]
      ↓
[MACHINE TRANSLATION] ← Choose: DeepL (quality), Google (speed), Amazon (volume)
      ↓
[POST-EDITING PHASE] ← Native speaker review (20-30% of MT time)
      ↓
[GLOSSARY CHECK] ← Marketplace-specific terms, brand names
      ↓
[QA VALIDATION] ← BLEU score >32, no formatting errors
      ↓
[PUBLICATION] ← ManoMano, Allegro, Cdiscount, etc.
```

**Cost Breakdown (per 10,000 words):**
- DeepL MT: €3-5
- Human post-edit (20%): €15-25
- QA & glossary: €5-10
- **Total per 10,000 words: €23-40**

---

## 3. Localization vs Translation: Cultural Adaptation

### 3.1 Language-Specific Formality Levels

**German (Sie vs du)**
- Amazon.de: Use **Sie** (formal, professional) for all content
- Product descriptions: Formal "Sie" register
- Marketplace tone: "Unsere Kunden schätzen..." (Our customers value)
- Exception: Marketing copy targeting young audience may use "du"
- Pronouns: Avoid second person when possible

**French (tu vs vous)**
- Cdiscount/ManoMano/Fnac: Use **vous** (formal, formal B2C)
- Product pages: Formal register
- Marketing: "Vous découvrirez..." (You will discover - formal)
- Customer service: Always vous
- Informal marketing: Rare, only for lifestyle products to young demographics

**Dutch (Direct Approach)**
- Bol.com: Dutch culture prefers direct communication
- Avoid flowery language; be concise and clear
- Typical Dutch phrase: "Dit product is solide en betrouwbaar" (straightforward)
- Marketing tone: Facts-first, then benefits
- Formality: Less formal than German/French

**Polish (Pan/Pani Formality)**
- Allegro: Business register uses formal titles (Pan/Pani)
- Product descriptions: Neutral, avoid informal "ty"
- Marketplace communication: Mr./Ms. in English context
- Respect for authority: More formal tone than Western EU

### 3.2 Units & Measurements Localization

| Region | Length | Weight | Temperature | Volume | Electricity |
|---|---|---|---|---|---|
| Germany | cm/m | kg/g | °C | l/ml | kW/kWh |
| France | cm/m | kg/g | °C | l/ml | kW/kWh |
| Poland | cm/m | kg/g | °C | l/ml | kW/kWh |
| Netherlands | cm/m | kg/g | °C | l/ml | kW/kWh |
| Spain | cm/m | kg/g | °C | l/ml | kW/kWh |

**Conversion Required:** Only for UK (imperial) — NOT in EU scope

### 3.3 Date, Address & Phone Formats

**Date Format Localization:**
- Germany: `dd.mm.yyyy` (14.04.2026)
- France: `dd/mm/yyyy` (14/04/2026)
- Poland: `dd.mm.yyyy` (14.04.2026)
- Netherlands: `dd-mm-yyyy` (14-04-2026)
- Spain: `dd/mm/yyyy` (14/04/2026)
- Italy: `dd/mm/yyyy` (14/04/2026)

**Address Format (Marketplace Profile):**
- Germany: Straße/Strasse, Postleitzahl, Stadt, Bundesland
- France: Rue/Avenue, Code Postal, Commune, Région
- Poland: Ulica, Kod Pocztowy, Miasto, Województwo
- Netherlands: Straat, Postcode, Plaats, Provincie
- Spain: Calle, Código Postal, Municipio, Comunidad

**Phone Format:**
- Germany: +49 XXX XXXXXXXXX
- France: +33 X XX XX XX XX
- Poland: +48 XX XXX XX XX
- Netherlands: +31 X XXXX XXXX
- Spain: +34 9XX XXX XXX
- Italy: +39 XXX XXXXXXX

### 3.4 Payment Preferences & Currency

| Country | Preferred Method | Currency | Payment Notes |
|---|---|---|---|
| Germany | SEPA/Bank transfer, PayPal | EUR | Invoice culture strong |
| France | Card, PayPal, ApplePay | EUR | High BNPL adoption |
| Poland | Card, Przelewy24, BLIK | PLN | Digital wallets essential |
| Netherlands | iDEAL, Card, Bunq | EUR | iDEAL dominates (60%) |
| Spain | Card, Bizum, BNPL | EUR | Cash declining rapidly |
| Italy | Card, PayPal, Satispay | EUR | Payment trust varies |

---

## 4. Multi-Language Template Management

### 4.1 Translation Workflow & Version Control

**Workflow Process:**

```
[SOURCE MASTER] (Italian, en_IT)
      ↓
[EXPORT TO TM] ← Translation Memory (e.g., Memsource, memoQ)
      ↓
[IDENTIFY NEW STRINGS] ← Compare with previous version
      ↓
[AUTO-TRANSLATE REPETITIONS] ← Leverage TM from previous campaigns
      ↓
[HUMAN TRANSLATION] ← Native speakers, subject-matter experts
      ↓
[IN-CONTEXT REVIEW] ← Visual preview on actual marketplace
      ↓
[APPROVAL WORKFLOW] ← Marketplace manager sign-off
      ↓
[PUBLICATION] ← Push to marketplace via API/CSV
```

### 4.2 Version Control & Git Workflow

**Directory Structure:**
```
/vendiamonoi-translations/
├── /templates/
│   ├── product_description_master.md (IT)
│   ├── terms_conditions_master.md (IT)
│   └── return_policy_master.md (IT)
├── /translations/
│   ├── /de/ (German)
│   │   ├── product_description_de.md
│   │   └── terms_conditions_de.md
│   ├── /fr/ (French)
│   ├── /nl/ (Dutch)
│   ├── /pl/ (Polish)
│   └── /es/ (Spanish)
├── /glossaries/
│   ├── master_glossary.xlsx
│   ├── amazon_de_glossary.xlsx
│   └── allegro_pl_glossary.xlsx
├── /translation_memory/
│   ├── vendiamonoi_it_de.tmx
│   └── vendiamonoi_it_fr.tmx
└── /approved_versions/
    ├── v1.0_2026-04-01/
    └── v1.1_2026-05-15/
```

**Git Commit Convention:**
```
feat(translations): Add German product descriptions for Q2 campaign
- 45 products translated via DeepL + human review
- BLEU score: 38.2
- 2 linguists (5 hrs total post-edit)
- Ready for Amazon.de publication
```

### 4.3 Glossary & Translation Memory Management

**Master Glossary Maintenance (Excel/Google Sheets):**

| English | Italian | German | French | Polish | Dutch | Domain | Notes |
|---|---|---|---|---|---|---|---|
| Waterproof | Impermeabile | Wasserdicht | Étanche | Wodoodporny | Waterdicht | Product Feature | ALL marketplaces |
| Return Policy | Politica dei Resi | Rückgaberecht | Politique de Retour | Polityka Zwrotów | Retourbeleid | Legal | Exact translation required |
| Business Days | Giorni Lavorativi | Geschäftstage | Jours Ouvrables | Dni Robocze | Werkdagen | Operational | NOT calendar days |
| EU Consumer Law | Diritto Consumatori EU | EU-Verbraucherschutz | Droit Consommateurs UE | Prawo Konsumenta UE | EU-consumentenrecht | Legal | Reference, don't translate |

**Translation Memory (TM) Tools:**
- **Memsource**: €99-299/month, excellent EU language support
- **memoQ**: €30-100/month, desktop-based, good for small teams
- **Trados**: Enterprise, €2,000+/year, industry standard
- **Free Alternative**: OmegaT (open source, no cost)

**TM Leverage Strategy:**
- Segment repetitive content (variations of return policy)
- Auto-populate from previous translations (50%+ match reduction)
- Update TM after each approved publication
- Monthly TM audit for consistency

### 4.4 Approval Workflow & Sign-Off

**Approval Chain:**
```
Translator → Reviewer (Native Speaker) → Marketplace Manager → QA Tool → Publication
    ↓            ↓                              ↓                 ↓         ↓
  24hrs        24hrs (check                   24hrs            Automated   Go-live
            context, tone)              (final formatting)   (BLEU >32)
```

**Approval Checklist (Reviewer):**
- [ ] No terminology inconsistencies (glossary match 100%)
- [ ] Formality level correct (Sie/vous/Pan)
- [ ] Units/dates/phone formats localized
- [ ] No English words left untranslated
- [ ] Links/URLs intact
- [ ] Special characters (ö, ç, ł, ñ) rendered correctly
- [ ] Character count within 110% of source (for UI fitting)
- [ ] No machine-detected issues (DeepL quality check)

---

**End of Part 1**

For Part 2, see: part2.md
For complete overview, see: README.md