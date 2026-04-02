# Domain 6.1 — Customer Communication & Response Management
## Part 1: Messaging Systems, SLAs & Response Requirements

**Vendiamonoi.it European Marketplace Operations** | Updated: 2026-04-02

---

## 1. MARKETPLACE MESSAGING SYSTEMS & REQUIREMENTS

### 1.1 Amazon (EU: DE, IT, FR, ES, UK, NL, PL, SE)

**Platform:** Amazon Seller Central Messaging
- **Character Limit:** 1,000 characters per message
- **SLA Response Time:** 24 hours from buyer inquiry
- **Business Days Only:** No (24/7 monitoring required)
- **Forbidden Content:**
  - External contact information (email, phone, URL)
  - Requests to move transactions off-platform
  - Promotional language beyond purchase context
  - Threats, abusive language
- **Escalation Path:** Buyer contacts A-to-z Guarantee → Account Health Dashboard alert
- **Auto-Close:** Unresolved threads close after 2 customer messages
- **Seller Metrics Impact:** Response rate affects Seller Performance Dashboard; <80% = suspension risk
- **Languages Supported Per Marketplace:**
  - Germany: DE, EN
  - Italy: IT, EN
  - France: FR, EN
  - Spain: ES, EN
  - UK: EN
  - Netherlands: NL, EN
  - Poland: PL, EN
  - Sweden: SE, EN

**Best Practice:** Use pre-approved templates, automate acknowledgment within 2 hours, assign named agents per language.

---

### 1.2 eBay (Multiple Markets)

**Platform:** eBay Messaging Service
- **Character Limit:** 20,000 characters per message
- **SLA Response Time:** 1 business day (48 hours including weekends in practice)
- **Business Days Only:** No (24/7 recommended)
- **Forbidden Content:**
  - Phone numbers, email addresses, website URLs
  - Payment method instruction outside eBay
  - Shipping address confirmation requests (use eBay labels)
  - Warranty claims outside eBay Resolution
- **Escalation Path:** Item Not Received → Resolution Center → eBay Investigation
- **Auto-Close:** 2 weeks inactive = thread archived
- **Seller Metrics Impact:** Message response rate (Target: >95%); late responses trigger "slow to respond" badge
- **Auto-Response Feature:** Built-in, 100-character limit for auto-reply

**Best Practice:** Set auto-response confirming receipt within 1 hour; use templates for 90% of common inquiries.

---

### 1.3 Kaufland (Central/Eastern Europe)

**Platform:** Kaufland Marketplace Messaging
- **Character Limit:** 5,000 characters
- **SLA Response Time:** 24 hours maximum (strict)
- **Business Days Only:** No
- **Forbidden Content:**
  - External communication channels
  - Discounts contingent on off-platform sales
  - Negative competitor references
- **Escalation Path:** Customer complaint → Kaufland Investigation → Suspension (if pattern detected)
- **Seller Metrics Impact:** Response rate affects seller visibility; below 85% = penalties
- **Languages:** DE, PL, CZ, RO, HU primarily; English widely understood

**Critical:** Kaufland automated enforcement is rigid; template usage prevents violations.

---

### 1.4 Bol.com (Netherlands, Belgium)

**Platform:** Bol.com Merchant Inbox
- **Character Limit:** 5,000 characters
- **SLA Response Time:** 24 hours (service level agreement)
- **Business Days Only:** No
- **Forbidden Content:**
  - Direct customer contact details sharing
  - Payment method changes requests
  - URL links (except in specific cases pre-approved by Bol)
- **Escalation Path:** Customer dispute → Bol.com Dispute Team → Seller account review
- **Auto-Acknowledgment:** Not built-in; must implement via API integration
- **Seller Metrics:** Response time published in Seller Performance; impacts search visibility
- **Languages:** NL, EN, FR, DE

**API Integration Available:** Bol Partner API allows programmatic message retrieval/response.

---

### 1.5 Cdiscount (France)

**Platform:** Cdiscount Message Center
- **Character Limit:** 10,000 characters
- **SLA Response Time:** 24 hours (enforced with seller rating penalties)
- **Business Days Only:** No
- **Forbidden Content:**
  - Solicitation to buy off-platform
  - Spam, duplicate messages
  - Commercial solicitation unrelated to order
- **Escalation Path:** Buyer opens complaint → Cdiscount arbitration → Potential account suspension
- **Auto-Response:** Limited; recommend template library approach
- **Languages:** FR primary, EN accepted
- **Seller Score Impact:** Response rate weighted 15% in Cdiscount Seller Score algorithm

**Requirement:** All sellers must provide support email address on Cdiscount profile (monitored separately).

---

### 1.6 Fnac-Darty (France, Spain, Belgium)

**Platform:** Integrated Marketplace Messaging
- **Character Limit:** 2,000 characters per message
- **SLA Response Time:** 24 hours for order-related; 48 hours for product inquiries
- **Business Days Only:** No
- **Forbidden Content:**
  - External website/email solicitation
  - Demands for customer reviews/feedback
  - Payment disputes raised outside official channels
- **Escalation Path:** Customer complaint → Fnac Seller Relations → Account warning/suspension
- **Platform Integration:** Connected to Fnac seller metrics dashboard
- **Languages:** FR, EN, ES, IT supported; FR prioritized

---

### 1.7 Allegro (Poland)

**Platform:** Allegro Wiadomości (Messenger)
- **Character Limit:** 5,000 characters
- **SLA Response Time:** 24 hours (competitive marketplace norm)
- **Business Days Only:** No
- **Forbidden Content:**
  - Phone/email/address outside official order context
  - Requests to complete payment off-Allegro
  - Negative reviews solicitation
- **Escalation Path:** Complaint → Allegro Dispute Resolution → Seller Rating Impact
- **Special Feature:** Allegro ratings tied heavily to message response quality
- **Languages:** PL primary, EN widely understood
- **Seller Info:** High-volume Polish marketplace; expect 40-60% inquiries in Polish

---

### 1.8 ManoMano (France, Spain, Germany, UK, IT, Belgium)

**Platform:** ManoMano Messaging Hub
- **Character Limit:** 3,000 characters
- **SLA Response Time:** 24 hours
- **Business Days Only:** No
- **Forbidden Content:**
  - External contact solicitation
  - Competitor disparagement
  - Payment method changes outside ManoMano systems
- **Escalation Path:** Buyer protection claim → ManoMano Review Team → Account restriction possible
- **Unique Feature:** ManoMano emphasizes craftsmanship/expertise; tone matters significantly
- **Languages:** Multi-market (FR, ES, DE, EN, IT); recommend native speakers per market

---

### 1.9 Leroy Merlin (France, Italy, Spain, Poland, Portugal, Belgium)

**Platform:** Leroy Merlin Seller Communication
- **Character Limit:** 4,000 characters
- **SLA Response Time:** 24 hours (B2B tone expected; professional communication required)
- **Business Days Only:** No
- **Forbidden Content:**
  - External payment/shipping arrangements
  - Warranty claims outside official Leroy Merlin process
- **Escalation Path:** Complaint → Leroy Merlin Merchant Relations → Delisting risk
- **Seller Rating:** Heavily weighted on communication tone/professionalism
- **Languages:** Market-specific (IT, ES, FR, PL, PT)

---

### 1.10 Additional Marketplaces Summary Table

| Marketplace | Region | SLA | Char Limit | Auto-Response | Key Restriction |
|---|---|---|---|---|---|
| **Vinted** | EU | 24h | 1,000 | No | No external contact |
| **Mercado Libre** | ES/IT | 24h | 5,000 | Yes | No off-platform sales |
| **OLX** | EU | 24h | 2,000 | Limited | Phone verification only |
| **Swappa** | EU (emerging) | 48h | 3,000 | No | Account-tied messaging |
| **Back Market** | EU | 24h | 4,000 | Yes | Warranty terms strict |

---

## 2. RESPONSE TIME REQUIREMENTS & SLA MANAGEMENT

### 2.1 SLA Compliance Framework

**Critical Definition:** Response Time = Time from customer message receipt to seller's first substantive reply (auto-acknowledgment counts; template-generated replies count).

**Penalty Structure by Marketplace:**

**Amazon:**
- <80% response rate = Performance notification
- <50% response rate = Account suspension warning
- Continued failure = Selling privileges removed

**eBay:**
- <95% response rate = "Slow to Respond" badge (visible to buyers)
- <90% for 30 days = Account review trigger
- Impact: Reduced search visibility, buyer trust damage

**Kaufland:**
- <85% response rate = Seller visibility reduced 20%
- <70% for 14 days = Potential account restriction
- Rigid enforcement; no grace periods

**Bol.com:**
- <95% response rate = Performance dashboard flagged
- <85% sustained = Marketplace review
- Impact: Featured placement removal

**Cdiscount:**
- <90% response rate = Seller Score penalty (-5 points)
- Multiple violations = Delisting risk
- Weighted 15% in overall Seller Score

---

### 2.2 Operational Impact of Response Delays

| SLA Violation | Marketplace Impact | Timeline |
|---|---|---|
| 24-48h delay | Visual buyer-facing badge/notification | Immediate |
| 48-72h delay | Seller metrics flagged; reduced visibility | 3-7 days |
| 72h+ repeated | Account review; potential restrictions | 14-30 days |
| 7+ days unresolved | Automatic escalation to platform resolution | 7 days |

**Staffing Formula:** For 100+ suppliers with 200+ daily inquiries:
- **Peak volume (100+ daily messages):** 3-4 FTE agents required
- **Moderate volume (50-100/day):** 1.5-2 FTE required
- **Low volume (<50/day):** 1 FTE + automation sufficient
- **Add 25% buffer** for training, vacation, quality assurance

---

### 2.3 Automated Acknowledgment Strategy

**Tier 1 (Immediate - within 15 minutes):**
- Automated template: "Thank you for your message. Our team will review your inquiry within 2 hours during business hours (CET). Order number: [auto-fill]. —Vendiamonoi Support"
- Deployment: Webhook-based on message receipt across all platforms
- Tools: Make.com, n8n, or native platform API (eBay, Bol, Cdiscount)

**Tier 2 (Standard Response - within 2 hours, business hours):**
- Agent-led using pre-built templates
- 90% of inquiries resolved at this stage
- Template selection based on message classification (Make.com AI routing)

**Tier 3 (Escalation - within 24 hours):**
- Complex cases, product issues, damage claims
- Requires manager review
- Affects <10% of inquiries

---

### 2.4 Response Time Tracking Dashboard

**Recommended KPIs to Monitor:**
1. **Median Response Time** per marketplace (Target: <30 minutes)
2. **95th Percentile Response Time** (Target: <4 hours)
3. **% Responses Within SLA** per marketplace (Target: >98%)
4. **Message Volume Trend** (weekly/monthly)
5. **First-Contact Resolution Rate** (Target: >80%)
6. **Escalation Rate** (Target: <15%)

**Tool:** Zendesk, Freshdesk, or Gorgias (see Part 2 for integration details)

---

## 3. COMMUNICATION TEMPLATES (EN, DE, FR, IT, ES, NL, PL)

### 3.1 Order Confirmation Template

**EN:**
Thank you for your order [ORDER_ID] placed on [DATE]. We confirm receipt and your order is being prepared for shipment. Tracking information will be sent to you within 24 hours. If you have any questions, please don't hesitate to reach out. Best regards, Vendiamonoi Support Team

**DE:**
Danke für Ihre Bestellung [ORDER_ID] vom [DATE]. Wir bestätigen den Eingang und Ihre Bestellung wird gerade vorbereitet. Tracking-Informationen werden Ihnen innerhalb von 24 Stunden mitgeteilt. Bei Fragen kontaktieren Sie uns bitte. Beste Grüße, Vendiamonoi Support Team

**FR:**
Merci pour votre commande [ORDER_ID] passée le [DATE]. Nous confirmons la réception et votre commande est en préparation. Les informations de suivi vous seront envoyées dans les 24 heures. N'hésitez pas à nous contacter pour toute question. Cordialement, Équipe Support Vendiamonoi

**IT:**
Grazie per il vostro ordine [ORDER_ID] effettuato il [DATE]. Confermamo la ricezione e il vostro ordine è in preparazione. Riceverete le informazioni di tracciamento entro 24 ore. Non esitate a contattarci per qualsiasi domanda. Cordiali saluti, Team Support Vendiamonoi

**ES:**
Gracias por su pedido [ORDER_ID] realizado el [DATE]. Confirmamos la recepción y su pedido está siendo preparado. Recibirá la información de seguimiento dentro de 24 horas. No dude en contactarnos si tiene preguntas. Saludos cordiales, Equipo de Soporte Vendiamonoi

**NL:**
Dank u voor uw bestelling [ORDER_ID] geplaatst op [DATE]. Wij bevestigen ontvangst en uw bestelling wordt voorbereid. Traceringsinformatie wordt binnen 24 uur verzonden. Neem contact met ons op als u vragen hebt. Vriendelijke groeten, Vendiamonoi Support Team

**PL:**
Dziękujemy za Waszą zamówienie [ORDER_ID] złożone w dniu [DATE]. Potwierdzamy otrzymanie i Wasze zamówienie jest przygotowywane. Informacje o śledzeniu zostaną wysłane w ciągu 24 godzin. Prosimy o kontakt w razie pytań. Z poważaniem, Zespół Wsparcia Vendiamonoi

---

### 3.2 Shipping Delay Notification

**EN:**
We sincerely apologize for the delay in shipping your order [ORDER_ID]. Due to [REASON: high demand/supplier delay/logistics], your package will be shipped by [NEW_DATE] with tracking number [TRACKING]. We appreciate your patience and will keep you updated. Thank you for your understanding. Vendiamonoi Support

**DE:**
Wir entschuldigen uns aufrichtig für die Versandverzögerung Ihrer Bestellung [ORDER_ID]. Aufgrund von [GRUND], wird Ihr Paket am [NEUES_DATUM] mit der Verfolgungsnummer [TRACKING] versendet. Wir danken für Ihre Geduld. Vendiamonoi Support

**FR:**
Nous nous excusons sincèrement pour le retard d'expédition de votre commande [ORDER_ID]. En raison de [RAISON], votre colis sera expédié le [NOUVELLE_DATE] avec le numéro de suivi [TRACKING]. Merci de votre patience. Support Vendiamonoi

**IT, ES, NL, PL:** [Same structure, language-specific phrasing]

---

### 3.3-3.7 Additional Templates

**Out of Stock Notice Template (All Languages):**
Unfortunately, the item [PRODUCT_NAME] (SKU: [SKU]) from order [ORDER_ID] is currently out of stock. Options: (1) Full refund issued today, (2) Substitute product [ALTERNATIVE] at same price, (3) Wait until [RESTOCK_DATE]. Please reply within 24 hours. [Translated for: DE, FR, IT, ES, NL, PL]

**Damage Claim Response:**
Thank you for reporting damage to [PRODUCT_NAME] in order [ORDER_ID]. Provide clear photos within 24 hours. Upon verification, we will immediately send replacement or issue full refund per marketplace policy. [All 7 languages available]

**Return Instructions:**
Return approved for [ORDER_ID]. Use provided return label (valid 14 days). Repack in original condition. Refund issued within 14 days of our receipt and inspection. [All 7 languages]

**Refund Confirmation:**
Your refund of EUR [AMOUNT] has been processed. It will appear in your payment method within 3-5 business days. [All 7 languages]

**Product Questions (Template Library):**
- "When will this be in stock?" → "This item [in stock/arriving DATE]..."
- "What's the warranty?" → "This product comes with [DURATION] manufacturer's warranty..."
- "Is it compatible with [PRODUCT]?" → "Yes, compatible with [COMPATIBLE_PRODUCTS]..."

---

## PART 1 SUMMARY

Part 1 covers the technical requirements of each major EU marketplace, SLA requirements, penalty structures, and core communication templates in 7 languages. The framework shows how to implement automated acknowledgment, tier-based response handling, and SLA tracking.

**Key takeaways:**
- 24-hour SLA is the EU standard; <80% compliance triggers penalties across all platforms
- Automated acknowledgment within 15 minutes is critical
- Templates in native languages prevent violations and ensure consistency
- Zendesk/Freshdesk required for multi-marketplace tracking and team coordination

Continue to Part 2 for automation workflows, language strategy, tone guidelines, and compliance requirements.