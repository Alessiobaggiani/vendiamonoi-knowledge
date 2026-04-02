# Domain 6.1 — Customer Communication & Response Management

**Vendiamonoi.it European Marketplace Operations**

---

## Overview

This comprehensive guide covers customer communication strategies across 20+ European marketplaces (Amazon, eBay, Kaufland, Bol.com, Cdiscount, Fnac-Darty, Allegro, ManoMano, Leroy Merlin, and others) for Vendiamonoi.it S.R.L.

It addresses:
- **Messaging platform requirements** per marketplace
- **SLA compliance** and penalty structures
- **Multi-language support** (EN, DE, FR, IT, ES, NL, PL)
- **Communication automation** workflows (Make.com, n8n, Zendesk)
- **Tone and brand guidelines** with de-escalation techniques
- **Performance metrics** and staffing models
- **GDPR compliance** and data retention policies

---

## Document Structure

This documentation is split into two parts for clarity:

### **Part 1: Messaging Systems, SLAs & Response Requirements**
- Marketplace messaging platforms (1.1-1.10)
- Character limits, response time SLAs, forbidden content
- Penalty structures for non-compliance
- SLA management framework (Tier 1-3 acknowledgment)
- Core communication templates in 7 languages
- Response time tracking KPIs

**Key Files:** `domain_6_1_part1.md`

### **Part 2: Automation, Language Strategy, Tone & Compliance**
- Multi-language support strategy
- Translation tool comparison (DeepL, Google, Transifex)
- Translation glossary for critical terms
- Brand voice and tone guidelines
- De-escalation protocol for angry customers
- Make.com and n8n automation workflows
- Zendesk, Freshdesk, Gorgias integration
- QA program and staffing models
- GDPR compliance and data retention
- Implementation roadmap and cost summary

**Key Files:** `domain_6_1_part2.md`

---

## Key Metrics at a Glance

| Metric | EU Standard | Vendiamonoi Target |
|---|---|---|
| **Response Time SLA** | 24 hours (most platforms) | <30 minutes median |
| **SLA Compliance** | >85% required | >98% target |
| **First-Contact Resolution** | N/A | >80% target |
| **CSAT Score** | >4.0/5 | >4.2/5 target |
| **Seller Rating** | >4.0/5 | >4.5/5 target |

---

## Quick Implementation Guide

### Phase 1: Infrastructure (Weeks 1-4)
1. Select ticketing system (Zendesk or Freshdesk)
2. Connect marketplace channels (Amazon, eBay, Bol.com, etc.)
3. Build template library in 7 languages
4. Train team

### Phase 2: Automation (Weeks 3-6)
1. Set up Make.com workflows:
   - Automated acknowledgment (Tier 1)
   - Intelligent routing (Tier 2)
   - Refund/return automation (Tier 3)
2. Test and integrate with Zendesk

### Phase 3: Quality & Compliance (Weeks 5-8)
1. Implement GDPR procedures
2. Set up data retention policies
3. Create QA framework (10% sampling)
4. Configure CSAT surveys

### Phase 4: Monitor & Optimize (Ongoing)
1. Daily: SLA compliance review
2. Weekly: CSAT scores, escalation rates
3. Monthly: QA audit results
4. Quarterly: Template updates, staffing analysis

---

## Marketplace SLA Summary

| Marketplace | SLA | Compliance Target | Penalty |
|---|---|---|---|
| **Amazon** | 24h | >80% | Suspension |
| **eBay** | 1 business day | >95% | "Slow to Respond" badge |
| **Kaufland** | 24h (strict) | >85% | Visibility reduction |
| **Bol.com** | 24h | >95% | Featured placement removal |
| **Cdiscount** | 24h | >90% | Seller Score penalty |
| **Fnac-Darty** | 24h (48h for product Q) | >90% | Account warning |
| **Allegro** | 24h | >90% | Seller rating impact |
| **ManoMano** | 24h | >90% | Account restrictions |
| **Leroy Merlin** | 24h | >90% | Delisting risk |

---

## Communication Templates Included

All templates provided in **7 languages:**
1. Order confirmation
2. Shipping delay notification
3. Out of stock notice
4. Product damage claim response
5. Return instructions
6. Refund confirmation
7. Product question responses (template library)

Each template includes:
- Professional, empathetic tone
- Marketplace compliance
- Customer-centric language
- Clear call-to-action
- Native speaker quality

**Example (Order Confirmation):**
```
EN: "Thank you for your order [ORDER_ID]..."
DE: "Danke für Ihre Bestellung [ORDER_ID]..."
FR: "Merci pour votre commande [ORDER_ID]..."
IT: "Grazie per il vostro ordine [ORDER_ID]..."
ES: "Gracias por su pedido [ORDER_ID]..."
NL: "Dank u voor uw bestelling [ORDER_ID]..."
PL: "Dziękujemy za Waszą zamówienie [ORDER_ID]..."
```

---

## Automation Workflows (Make.com)

### Workflow 1: Automated Acknowledgment (Tier 1)
- Triggered: New message received on any marketplace
- Action: Send templated acknowledgment within 15 minutes
- Result: 24-hour SLA clock starts; customer sees immediate response
- Coverage: 100% of incoming messages

### Workflow 2: Intelligent Routing (Tier 2)
- Triggered: 90-min timer; auto-resolved messages bypass
- Action: Route to appropriate agent by language + marketplace + complexity
- Result: Standard responses completed within 2 hours
- Coverage: 85% of inquiries resolved at this stage

### Workflow 3: Refund/Return Automation
- Triggered: Refund or return request detected
- Action: Verify order, check policy, generate label, send instructions
- Result: Compliance with marketplace return SLAs
- Coverage: 100% of return/refund requests

**Implementation:** 20-40 hours setup | Cost: EUR 300-500/month

---

## Language Support Strategy

**3-Tier Translation System:**

| Tier | Volume | Method | Cost | SLA |
|---|---|---|---|---|
| **Tier 1 (Templates)** | 80% | DeepL API + QA | EUR 240/year | Immediate |
| **Tier 2 (Complex)** | 15% | Human translator | EUR 0.05-0.10/msg | 4 hours |
| **Tier 3 (Escalations)** | 5% | Professional translator | EUR 0.10-0.20/msg | 4 hours |

**Translation Tool Recommendation:** DeepL API (95-98% accuracy for EU languages) + Zendesk integration

---

## Staffing Model

| Volume | Team Size | Structure | Tools | Monthly Cost |
|---|---|---|---|---|
| <50/day | 1 FTE | 1 generalist | Zendesk (starter) + templates | EUR 2,000 |
| 50-150/day | 1.5-2 FTE | Coordinator + language specialists | Zendesk (pro) + Make.com | EUR 4,000 |
| 150-400/day | 3-4 FTE | Supervisor + 3 language specialists + backup | Zendesk (enterprise) + Make.com + AI | EUR 10,000 |

**For Vendiamonoi (estimated 200+ daily messages):** 2.5-3 FTE required with automation

---

## Quality Assurance Program

**Audit Frequency:** 10% of messages (200+ per month for high volume)

**Scoring Rubric (0-100):**
- Accuracy (30%): Correct information?
- Tone (20%): Professional, empathetic, on-brand?
- Completeness (30%): All questions answered?
- Compliance (20%): No violations, SLA met?

**Action Triggers:**
- Score <70: Coaching + re-training
- Score <60: Supervisor escalation + probation
- Score >90: Recognition + incentive

---

## GDPR Compliance Checklist

- [ ] Data Processing Agreements (DPA) signed with all tools (Zendesk, Make.com, DeepL)
- [ ] Standard Contractual Clauses (SCC) in place for non-EU processors
- [ ] Data retention policy documented (24-month maximum)
- [ ] Erasure request procedure created
- [ ] Privacy notice updated with communication practices
- [ ] Quarterly data deletion audit scheduled
- [ ] GDPR breach notification procedure established
- [ ] Staff trained on data privacy (2-3 hours)

---

## Cost Summary (Annual)

| Component | Cost | Notes |
|---|---|---|
| Zendesk (Professional) | EUR 7,200 | 3 agents, EUR 25/user/month |
| Make.com Workflows | EUR 3,600 | 5-10 workflows |
| DeepL API | EUR 240 | ~2,000 messages/month |
| Team Salary | EUR 60,000-80,000 | 2-3 FTE @ EUR 2,500-3,500/month |
| Training/Development | EUR 2,000 | Ongoing improvements |
| **Total Estimated** | **EUR 73,000-93,000** | For 150-400 msgs/day |

---

## Key Success Factors

1. **Template-First Approach:** 95%+ of responses from pre-approved templates
2. **Automation:** Make.com workflows handle 80-90% of acknowledgments
3. **Language Specialists:** Native speakers per major language
4. **SLA Monitoring:** Daily dashboard review; <24h response time
5. **QA Culture:** 10% monthly sampling; continuous improvement
6. **GDPR Embedded:** Privacy-by-design in all processes
7. **Tool Integration:** Zendesk → Make.com → Marketplace APIs

---

## Next Steps

1. **Select ticketing system** (Zendesk recommended for mid-to-large volume)
2. **Inventory current message volume** per marketplace
3. **Hire or allocate staff** (language specialists by market)
4. **Build template library** in all 7 languages
5. **Set up Make.com account** and design Workflow 1
6. **Test end-to-end** with 100 sample messages
7. **Launch Phase 1** and iterate

---

## References & Tools

**Recommended Tools:**
- **Ticketing:** Zendesk (primary), Freshdesk (budget alternative)
- **Automation:** Make.com (cloud), n8n (self-hosted)
- **Translation:** DeepL API (primary), Google Translate (fallback)
- **Analytics:** Zendesk Insights, custom Tableau dashboards
- **Team Communication:** Slack, Microsoft Teams

**Related Documentation:**
- Marketplace Seller Policies (per marketplace)
- GDPR Compliance Framework
- Staffing and Hiring Guidelines
- Customer Service Training Curriculum

---

**Document Version:** 1.0 | **Last Updated:** 2026-04-02 | **Owner:** Vendiamonoi Operations

For questions or updates, contact: operations@vendiamonoi.it