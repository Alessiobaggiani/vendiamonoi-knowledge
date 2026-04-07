# OpenAI/ChatGPT API & Anthropic/Claude API — Deep-Dive Technical Documentation

**Checklist**: P3.6 | **Data**: 2026-04-07 | **Dimensione Totale**: 281KB, 8,847 righe, 24 sezioni

## Struttura Documento

Documento suddiviso in 3 parti per gestione dimensione:

### [Part 1 — Core Platform](./part1-core-platform.md)
**Sezioni 1-8** (~97KB, 3,000 righe)
1. Overview & Architettura delle Piattaforme AI
2. Modelli Disponibili — Catalogo Completo 2025-2026
3. Autenticazione e Sicurezza
4. Rate Limiting e Gestione Traffico
5. Chat Completions API — OpenAI
6. Messages API — Anthropic Claude
7. Function Calling & Tool Use
8. Vision & Multimodal Capabilities

### [Part 2 — Integrations & Advanced](./part2-integrations-advanced.md)
**Sezioni 9-16** (~93KB, 3,000 righe)
9. Embeddings & Vector Search
10. Batch API & Cost Optimization
11. SDKs Ufficiali — Installazione e Configurazione
12. Make.com Integration — Moduli e Configurazione
13. Supabase + AI Integration Patterns
14. Prompt Engineering — Best Practices Produzione
15. Structured Outputs & JSON Schema
16. Image Generation — DALL-E & GPT Image

### [Part 3 — Production & Vendiamonoi](./part3-production-vendiamonoi.md)
**Sezioni 17-24** (~91KB, 2,847 righe)
17. Audio — Text-to-Speech & Whisper
18. Fine-tuning & Custom Models
19. AI Agent Frameworks & Orchestration
20. Multilingual AI — Performance & Translation
21. Customer Service AI Automation
22. AI Monitoring, Observability & Cost Tracking
23. Compliance, GDPR & AI Act
24. Architettura Completa Vendiamonoi — AI Integration Blueprint

## Key Data

| Aspetto | OpenAI | Anthropic Claude |
|---------|--------|------------------|
| Base URL | api.openai.com/v1 | api.anthropic.com/v1 |
| Top Model | GPT-4o ($2.50/$10 per 1M) | Claude Opus 4 ($15/$75 per 1M) |
| Best Value | GPT-4o-mini ($0.15/$0.60) | Claude Haiku 3.5 ($0.80/$4) |
| Context Window | 128K (GPT-4o) | 200K (Claude) |
| Batch Discount | 50% (Batch API) | 90% (Prompt Caching reads) |
| Make.com | Nativo (ChatGPT module) | Nativo + HTTP workaround |
| Vision | GPT-4o (immagini) | Claude (immagini + PDF nativi) |
| Embeddings | text-embedding-3-small ($0.02/1M) | N/A (usa OpenAI) |

## Vendiamonoi Use Cases

- **Product Descriptions**: Bulk generation 1000+ prodotti/giorno, 6+ lingue
- **SEO Optimization**: Titoli marketplace-specific, keyword density
- **Customer Service**: eDesk + Claude auto-responder
- **PDF Extraction**: Cataloghi fornitori → dati strutturati
- **Translation**: IT/FR/DE/ES/NL/PL con quality assurance AI
- **Embeddings**: Product similarity search via Supabase pgvector
- **Cost Target**: ~$200-400/mese per operazioni full-scale

## Triple-Publish Status

- [x] GitHub: `api-docs/ai-apis/` (3 parti + index)
- [x] Obsidian: `📚 03 - Risorse/🔧 Tool & API/OpenAI ChatGPT & Anthropic Claude API — Deep-Dive Technical Documentation.md`
- [x] Notion: Pagina sotto 🔌 Integrazioni (`33bab24b-3086-81b8-a375-cf7aa5aed5d9`)
