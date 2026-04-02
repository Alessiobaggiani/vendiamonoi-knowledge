# Domain 3.2: Campaign Structure & Optimization

**Complete PPC Campaign Architecture Reference for European Marketplace Distribution**

*Last Updated: April 2, 2026*
*Scope: Amazon EU, eBay, Kaufland, Bol.com*
*Total Content: 2,429 lines across 2 parts*

---

## Document Overview

This comprehensive guide documents campaign architecture frameworks, optimization strategies, and implementation procedures for managing multi-platform marketplace advertising at scale.

### Content Structure

**Part 1: Architecture & Auto Campaign Optimization** (`part1-architecture-auto-manual.md`)
- Campaign architecture frameworks and hierarchy
- Single-keyword vs multi-keyword campaign strategies
- SKU-level vs product-group approaches
- Campaign naming conventions and portfolio organization
- Waterfall, mirrored, and hybrid structural models
- Auto campaign fundamentals and keyword harvesting
- Auto campaign budget allocation and performance benchmarks

**Part 2: Manual Campaigns & Advanced Strategies** (`part2-targeting-lifecycle-testing.md`)
- Exact, phrase, and broad match campaign architecture
- SKAG methodology and negative keyword strategies
- Product targeting (ASIN, category, brand)
- Marketplace-specific approaches (Kaufland, Bol.com, eBay)
- Campaign lifecycle management (launch, growth, maturity, decline)
- Seasonal campaign management
- Performance review cadences (daily, weekly, monthly, quarterly)
- A/B testing frameworks and creative optimization

---

## Key Reference Tables

### Campaign Comparison Matrix
- Management overhead, bid precision, data clarity
- Scaling speed, campaign count requirements
- Profitability focus and discovery potential
- Single-keyword vs multi-keyword vs SKU-level vs product-group vs mirrored vs hybrid

### Performance Benchmarks
- Auto campaign performance by targeting type
- Match type performance expectations (CTR, conversion rate, ACOS)
- Seasonal ACOS targets across campaign phases
- Video ad and creative testing performance metrics

### Implementation Guidelines
- Campaign naming conventions (standardized system)
- ACoS and TACOS targets by margin tier
- Budget allocation strategies by campaign type
- Negative keyword priority matrix
- Campaign lifecycle phase transitions

---

## Core Architecture Principles

1. **The 25-Keyword Allocation Limit**: Amazon allocates meaningful budget to ~25 keywords max per campaign regardless of total keywords added.

2. **Campaign Hierarchy**: Portfolio → Campaign (named: Category_Intent_MatchType_Margin) → Ad Group → Keywords/Negative Keywords → Budget

3. **Waterfall Structure**: Sequential keyword progression from Auto → Broad → Phrase → Exact as keywords prove performance.

4. **Mirrored Architecture**: One campaign per match type per product group, eliminating cross-match conflicts.

5. **Hybrid Model**: Tier-based granularity combining SKU-level precision with product-group efficiency for 200+ SKU portfolios.

---

## Quick Start

1. **Read Part 1** if building campaign architecture from scratch
2. **Read Part 2** for ongoing optimization, testing, and lifecycle management
3. Use **Campaign Comparison Matrix** to choose optimal structure for portfolio size
4. Reference **FINAL IMPLEMENTATION CHECKLIST** for pre-launch, execution, and optimization phases

---

## Critical Success Factors

- Standardized campaign naming enables rapid identification and scaling
- Portfolio-level organization separates discovery from efficiency strategies
- Lifecycle-phase thinking prevents inefficient spend on declining products
- Structured A/B testing validates creative and messaging assumptions
- Regular performance review cadences (daily/weekly/monthly/quarterly) maintain optimization discipline

---

*For marketplace-specific implementation details, see Part 2 Section 4.4 (Kaufland, Bol.com, eBay specifications).*