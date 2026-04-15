# ADR-001: Refine.dev invece di Base44

**Status:** Accepted
**Date:** 2026-04-15

## Context
Serviva scegliere la piattaforma per costruire il gestionale Vendiamonoi OS.

## Decision
Refine.dev (React/TypeScript) invece di Base44.

## Reasons
- Base44 ha lock-in totale (codice non esportabile)
- Base44 rating 2.2/5 su Trustpilot, outage feb 2026
- Refine genera codice React/TypeScript standard
- Codice hostabile ovunque (Vercel, VPS, cloud)
- Modificabile con Cursor/Claude Code
- Connettore Supabase nativo
- Open-source, gratuito

## Consequences
- Curva apprendimento piu' alta (serve React base)
- L'AI (Claude/Cursor) scrive il 90% del codice
- Codice tuo per sempre, nessun lock-in
