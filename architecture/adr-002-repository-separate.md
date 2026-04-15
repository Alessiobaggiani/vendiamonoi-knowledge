# ADR-002: Repository separate

**Status:** Accepted
**Date:** 2026-04-15

## Decision
Tre repository GitHub separate:
- `vendiamonoi-knowledge` = documentazione e knowledge base
- `vendiamonoi-make-automations` = scenari Make.com
- `vendiamonoi-os` = codice gestionale (Refine + Supabase + agenti)

## Reasons
- Separare codice applicativo da documentazione
- CI/CD dedicato per il gestionale
- Permessi diversi per team futuro
- Deploy indipendente

## Rules
- Il codice non va MAI nella cartella ~/Documents/Claude/
- La knowledge base non va MAI nella repo del codice
