# Dynamic Pricing & Automation - Complete Reference

**Version 1.0** | Last Updated: April 2, 2026 | Status: Production Reference Document

## Overview

This is a comprehensive guide to dynamic pricing tools, algorithms, and automation strategies for European marketplace sellers. It covers 8+ repricing platforms, competitor monitoring techniques, algorithm methodologies (rule-based and AI/ML), API limitations and costs, and automation safety practices to prevent race-to-bottom scenarios.

## Key Statistics

- **Tools Analyzed**: 8 major repricing platforms (RepricerExpress, SellerLogic, Feedvisor, BQool, Informed.co, Channable, plus analysis of in-house approaches)
- **Cost Range**: €25-€1,299+ monthly depending on platform and SKU volume
- **Amazon SP-API Cost**: $1,400/year + $0.001 per GET request (starting April 2026)
- **Performance Improvements**: 
  - Buy Box win rate: 10-18% improvement
  - Margin improvement: 5-15% depending on algorithm type
  - Conversion optimization: 2-8% velocity improvement typical
- **Data Lag**: 1-20 minutes for Amazon, 2-45 minutes for eBay

## Document Structure

This reference is split into three logical parts:

### Part 1: Repricing Tools & Monitoring
**Content Focus**: Tool comparison and competitor monitoring setup

Covers:
- Comprehensive tool comparison: RepricerExpress, SellerLogic, Feedvisor, BQool, Informed.co, Channable
- Feature matrix: supported marketplaces, pricing tiers, repricing frequency, rule templates
- Competitor monitoring standards (frequency: Amazon 5-15 min, eBay 30-60 min)
- Database schema for competitor price tracking
- Rule template libraries (35-47 templates per platform)
- Performance benchmarks: Buy Box improvement (8-18%), margin improvement (5-15%)

**Key Insights**:
- RepricerExpress best for multi-channel (Amazon/eBay/WooCommerce) at €85-€1,299/month
- SellerLogic specializes in Buy Box optimization with 8-12% win rate improvement per pricing percentile
- Feedvisor offers AI/RL with -2.1 to -3.2 price elasticity modeling; 6-12% margin improvement
- BQool provides 3 AI strategies (Smart Pricing, Aggressive Growth, Value Preservation) at €25-€300/month
- Informed.co's "Get the Buy Box" algorithm uses 40% price weight, 35% seller metrics, 15% fulfillment, 10% competitive pressure
- Channable excels at multi-channel synchronization across 100+ platforms with margin-based pricing

### Part 2: Algorithms & API Limitations
**Content Focus**: Pricing algorithms and technical constraints

Covers:
- Demand curve estimation: Q(P) = Q_est × (P/Current)^Elasticity
- Reinforcement Learning convergence timeline (week 1-2: random €800/day → week 9-12: refined €1,480/day at 95% optimal)
- Game theory: Bertrand competition leading to Nash equilibrium
- Complete price optimization algorithm (6 steps): demand estimation → margin calc → daily profit → 7-day maximization → rules → execution
- LED Desk Lamp worked example: Current €40, COGS €15, 22% fees, optimal €50 price
- Amazon SP-API rate limits and costs ($0.001 per GET, $100-$5,000 annually for 100K-5M calls)
- Batch operation optimization reducing API calls 90% (960K → 48K daily for 10K SKUs)
- Data lag measurements: 0.5-20 minutes for Amazon SP-API, 2-45 minutes for eBay

**Key Insights**:
- Price elasticity typically -2.1 to -3.2 for commodity products; varies by category
- RL models reach 95% optimal pricing within 4-6 weeks of activation
- Game theory shows race-to-bottom occurs unless differentiation or price floors applied
- Amazon SP-API costs can exceed tool costs for high-volume sellers (5M+ calls)
- Data lag measurement critical for setting repricing frequency vs cost optimization

### Part 3: Race Prevention & Automation Safety
**Content Focus**: Automation safety measures and alert systems

Covers:
- Safety implementation: price floors, margin guardrails, circuit breakers
- Repricing velocity analysis (2/day manual vs 10+/day aggressive bot)
- Alert conditions: Price Drop, Aggressive Repricing, Race-to-Bottom, Buy Box Loss
- Competitor segmentation: Tier 1 (direct threats), Tier 2 (secondary), Tier 3 (weak), Tier 4 (anomalies)
- Race-to-bottom prevention: competitor behavior analysis and circuit breaker thresholds
- Automation best practices: testing, monitoring, alert setup, manual override capability

**Key Insights**:
- Price floor enforcement prevents margin collapse in competitive situations
- Circuit breaker at 10+ reprices/day indicates race-to-bottom; human review needed
- Competitor segmentation prevents reaction to anomalies or weak competitors
- Buy Box loss alert triggers manual investigation before automated response
- Testing in sandbox/staging environment reduces race-to-bottom risk

## Platform Selection Guide

### By Use Case

| Use Case | Recommended Platform | Rationale |
|----------|----------------------|----------|
| Multi-channel (Amazon+eBay) | RepricerExpress | Best multi-marketplace support; 47 templates |
| Buy Box focused | SellerLogic or Informed.co | Specialized algorithms; 8-12% win rate improvement |
| Margin optimization | Feedvisor | AI/RL with elasticity modeling; 6-12% improvement |
| Cost-conscious starter | BQool Essential | €25/month; 3 AI strategies |
| Multi-marketplace sync | Channable | 100+ platform support; margin-based rules |

### By Scale

| Annual Revenue | Recommended Tier | Typical Cost | Tool Focus |
|---|---|---|---|
| <€100K | BQool Essential or repricer.com Starter | €25-85/month | Single marketplace, basic rules |
| €100K-€500K | SellerLogic Buy Box or BQool Professional | €199-€299/month | Category-specific optimization |
| €500K-€2M | Feedvisor or SellerLogic Push | €299-€749/month | AI/RL models; margin maximization |
| €2M+ | Enterprise (Feedvisor, Informed.co) | €999+/month or custom | Custom models, API access, dedicated support |

## Tactical Implementation Checklist

- [ ] Select repricing tool based on platform mix (single vs multi)
- [ ] Define price floors (minimum acceptable contribution margin)
- [ ] Set price ceilings (maximum before demand collapse)
- [ ] Configure competitor monitoring (frequency based on category volatility)
- [ ] Implement segmentation (Tier 1-4 competitors)
- [ ] Test rules in staging environment for 1-2 weeks
- [ ] Set up alerts for: price drops >5%, aggressive repricing >8/day, Buy Box loss
- [ ] Configure circuit breakers (halt repricing if conditions met)
- [ ] Track API costs monthly (may exceed tool cost at scale)
- [ ] Review repricing decisions weekly initially, monthly after stabilization

## Key Technical Specifications

### Amazon SP-API Costs (April 2026 Pricing)

```
Monthly Cost = (Number of GET requests / 1,000) × $1
Annual minimum: $1,400 (140,000 requests/year baseline)
Examples:
- 100K calls/month: $100/month = $1,200/year
- 500K calls/month: $500/month = $6,000/year
- 5M calls/month: $5,000/month = $60,000/year
```

### Repricing Frequency Impact

| Frequency | Data Freshness | API Cost Impact | Race-to-Bottom Risk |
|---|---|---|---|
| Every 60 min | 30-60 min lag | Low | Very Low |
| Every 30 min | 15-30 min lag | Medium | Low |
| Every 15 min | 7-15 min lag | High | Medium |
| Every 5 min | 2-5 min lag | Very High | High |
| Real-time | <2 min lag | Extreme | Very High |

## Cross-References

- See Part 1 for detailed tool comparisons and feature matrices
- See Part 2 for algorithm technical specifications and API cost calculations
- See Part 3 for automation safety implementation and alert configuration

## Document Version History

- **v1.0** (April 2, 2026): Initial comprehensive reference document
  - Covers 8+ repricing platforms with detailed feature analysis
  - 2624 lines of technical guidance and tactical implementation
  - Includes algorithm details, API costs, safety practices

---

**Related Documents in This Knowledge Base**:
- Domain 2.1: Pricing Fundamentals
- Domain 2.2: Cost Structure Analysis
- Domain 2.3: Buy Box Mechanics
- Domain 2.4: Promotional Pricing Strategy
- Domain 2.5: International Pricing Strategy