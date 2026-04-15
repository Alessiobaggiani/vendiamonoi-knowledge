# ADR-003: n8n self-hosted per automazioni pesanti

**Status:** Accepted
**Date:** 2026-04-15

## Decision
Strategia ibrida Make + n8n:
- Make.com per webhook semplici e integrazioni rapide
- n8n self-hosted per ETL, bulk processing, import ordini

## Reasons
- Make conta per operazione (costoso a scala)
- n8n conta per esecuzione (1 workflow = 1 exec, indipendente dal numero di step)
- n8n self-hosted: esecuzioni illimitate per ~5 euro/mese
- A 20.000 ordini/mese: Make ~500+euro vs n8n ~5 euro

## Infrastructure
- Hetzner VPS (~5 euro/mese)
- Docker + Coolify
- PostgreSQL per persistenza workflow
