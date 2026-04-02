# Domain 6.4 — Marketplace Health Metrics & Account Protection

**Comprehensive Guide for Vendiamonoi.it Multi-Marketplace Operations**

## Overview

This repository contains the complete operational framework for managing seller account health across 20-30+ EU marketplaces. It covers metrics, suspension prevention, recovery strategies, monitoring systems, and compliance requirements for Amazon, eBay, Bol.com, Kaufland, Cdiscount, Allegro, ManoMano, and Fnac-Darty.

**Status:** Active | **Version:** 1.0 | **Last Updated:** April 2, 2026

---

## Contents

### Part 1: Platform-Specific Health Metrics
`part1_platform_metrics.md` — 3,500+ lines

Detailed breakdown of:
- **Amazon Account Health** — ODR, LSR, PFCR, VTR, IDR, AHR scoring (sections 1.1-1.7)
- **eBay Seller Standards** — TDR, DSR, tier structure, Top Rated Plus (sections 2.1-2.4)
- **Bol.com Performance** — Track & Trace, cancellation, on-time delivery, response times, Partner Charter (sections 3.1-3.6)
- **Other Marketplaces** — Kaufland quality score, Cdiscount, Allegro, ManoMano, Fnac-Darty (sections 4.1-4.5)
- **Metric Thresholds Summary Table** — Quick reference for all platforms

**Key Metrics at a Glance:**
- Amazon: ODR < 1%, LSR < 4%, PFCR < 2.5%, VTR > 95%
- eBay: TDR < 2%, DSR > 4.5, Top Rated Plus achievable
- Bol.com: Cancel rate < 3%, Response time < 4 hours, On-time > 95%

---

### Part 2: Suspension Prevention & Recovery
`part2_suspension_recovery.md` — 3,500+ lines

Comprehensive coverage of:
- **Suspension Prevention** — Early warning indicators, daily monitoring checklist, weekly reviews, operational excellence (sections 1.1-1.6)
- **Suspension Recovery** — Immediate response protocol, POA template with sample (Amazon ODR case), eBay appeals, Bol.com reinstatement, recovery timelines (sections 2.1-2.6)
- **Multi-Account Strategy** — When legitimate, platform policies, risk mitigation (sections 3.1-3.3)
- **Monitoring & Alerting** — Dashboard design (Sellerboard, SellerPulse, Google Sheets), alert thresholds by marketplace, review cadence, tool configurations with examples (sections 4.1-4.4)
- **Compliance Documentation** — Essential documents checklist, POA supporting docs, archival requirements (sections 5.1-5.2)
- **Action Items** — Immediate and 12-month roadmap

**Sample POA Included:** Full Amazon ODR suspension recovery plan (2,000+ words) with specific dates, owner assignments, and supporting documentation requirements.

---

## Quick Start for Operations Team

1. **Read Part 1** — Understand your target metrics per marketplace
2. **Set up monitoring** — Use template from Part 2, Section 4.1
3. **Implement daily checks** — Follow Section 1.2 checklist
4. **Schedule weekly reviews** — Use agenda from Section 1.3
5. **Prepare for risk** — Keep POA template ready (Section 2.2)

---

## Metric Reference Tables

### Amazon Account Health Thresholds

| Metric | Green | Yellow | Red | Suspension |
|--------|-------|--------|-----|----------|
| **ODR** | 0-0.5% | 0.5-0.9% | 0.9-1.5% | 2.0%+ |
| **LSR** | 0-2.0% | 2.0-3.5% | 3.5-4.0% | 4.0%+ |
| **PFCR** | 0-0.8% | 0.8-1.5% | 1.5-2.0% | 2.5%+ |
| **VTR** | 98-100% | 95-97% | 90-94% | <90% |

### eBay Seller Standards Thresholds

| Status | TDR | LSR | Fee Reduction | Benefits |
|--------|-----|-----|---------------|----------|
| Below Standard | 4.0%+ | - | +20-100% | Visibility penalized |
| Standard | 2.0-3.99% | - | Baseline | Normal visibility |
| Top Rated | <2.0% | <2.5% | -10% | Search boost |
| Top Rated Plus | <2.0% | <2.5% | -20% | Premium status |

### Bol.com Performance Thresholds

| Metric | Green | Yellow | Red | Action |
|--------|-------|--------|-----|--------|
| Cancellation Rate | 0-1.5% | 1.5-2.0% | 2.0-2.5% | Inventory review |
| Response Time | <4 hours | 4-6 hours | 6-24 hours | Coverage analysis |
| On-Time Delivery | 95%+ | 92-94% | <92% | Logistics review |

---

## Suspension Recovery Timelines

| Platform | Cooling Period | Review Period | Total Timeline | Notes |
|----------|----------------|---------------|----------------|-------|
| **Amazon** | N/A | 5-14 days (POA review) | 14-90 days | May appeal 2x (14 days each) |
| **eBay** | N/A | 5-10 days (appeal) | 14-28 days | Limited to 2 appeal attempts |
| **Bol.com** | 14 days (mandatory) | 7-14 days (review) | 21-40 days | Non-negotiable cooling period |

---

## POA (Plan of Action) Guide

### Structure (Amazon Format)
1. **Acknowledgment** — Demonstrate understanding of violation
2. **Root Cause Analysis** — Data-backed explanation with evidence
3. **Corrective Actions Taken** — Specific actions already implemented with dates
4. **Preventive Measures** — Systems/processes to prevent recurrence
5. **Monitoring & Metrics** — Daily/weekly/monthly tracking plan with owners
6. **Supporting Documentation** — Invoices, logs, contracts, policies, training materials

### Success Factors
- ✓ Genuine understanding of the issue
- ✓ Root cause analysis with data
- ✓ Specific, measurable corrective actions
- ✓ Evidence of implementation (not promises)
- ✓ Professional, respectful tone

---

## Recommended Monitoring Stack
1. **Sellerboard** — Multi-marketplace aggregator (Amazon, eBay, Bol.com, Kaufland)
2. **SellerPulse** — Amazon-specific monitoring (IPI, ranking, inventory)
3. **Google Sheets** — Internal tracking (daily entry, automated alerts, archive)

---

## Compliance Documentation Checklist

### Keep for 2+ Years
- Supplier invoices (proof of authenticity)
- Shipping records (carrier proof)
- Quality inspection logs
- Return/refund processing logs
- Customer complaint analysis
- Corrective action records
- Monthly P&L per marketplace
- VAT records (IOSS, intra-EU trade)
- Supplier contracts & SLAs
- Carrier agreements
- Insurance policies
- Business registration documents
- GDPR compliance documentation

---

## 12-Month Implementation Roadmap

**Q1 2026:** Establish baseline metrics, implement monitoring, document processes

**Q2 2026:** Achieve green status on all metrics, optimize carriers, expand monitoring

**Q3 2026:** Achieve premium status (Top Rated Plus, AHR 95+), implement multi-account strategy

**Q4 2026:** Conduct compliance audit, review processes, plan 2027 improvements

---

## Document Structure

- `README.md` — This file (overview, quick reference)
- `part1_platform_metrics.md` — Platform metrics, thresholds (3,500+ lines)
- `part2_suspension_recovery.md` — Prevention, recovery, monitoring (3,500+ lines)

**Total Content:** 8,000+ lines of operational documentation covering 8 marketplaces, 100+ metric thresholds, POA templates, monitoring configurations, compliance requirements, and 12-month roadmap.

---

**Status:** Active | **Version:** 1.0 | **Last Updated:** April 2, 2026
**Confidential — Internal Use Only**