---
tags: [architettura, migrazione, cloud, hetzner, n8n, make, vendiamonoi]
source: vendiamonoi-knowledge/architecture/cloud-migration-checklist.md
created: 2026-04-19
type: migration-checklist
---

# Cloud Migration Checklist — Da Locale a Hetzner

## Contesto
Tutto l'ecosistema (n8n, frontend Refine, workflow Make.com) gira attualmente in locale.
Quando n8n si sposta su Hetzner e il frontend va su Vercel, le seguenti cose **devono** essere aggiornate.
Questo documento è la fonte di verità per quella migrazione.

**Regola:** non toccare il codice adesso. Quando arriva il momento, usa questa lista e aggiorna tutto in parallelo.

---

## BLOCCO 1 — n8n: URL localhost da aggiornare

Questi 2 workflow chiamano `http://localhost:5678/...` — si rompono su Hetzner.

| Workflow | ID | Nodo | URL attuale | URL produzione |
|---|---|---|---|---|
| Crea Spedizione — SpediamoPro + SendCloud | `TyP4Awki89Uuclae` | Chiama Registro Tracking | `http://localhost:5678/webhook/tracking-register` | `https://<n8n-hetzner>/webhook/tracking-register` |
| SR - Sincronizza Spedizione | `8ARIYB8Cww39SyFS` | Chiama Registro Tracking SR | `http://localhost:5678/webhook/tracking-register` | `https://<n8n-hetzner>/webhook/tracking-register` |

---

## BLOCCO 2 — n8n: Webhook pubblici da comunicare a servizi esterni

Quando n8n va su Hetzner, questi URL cambiano hostname. Ogni servizio esterno indicato deve essere aggiornato.

| Workflow | ID | Path | URL produzione | Chi aggiornare |
|---|---|---|---|---|
| Tracking — Ricevi Aggiornamenti 17track | `WMXls9EiLZtbIx8T` | `/17track-webhook` | `https://<n8n-hetzner>/webhook/17track-webhook` | **17track Dashboard → Settings → Package Webhook** |
| Comparazione Preventivi | `RA6YV6ql8fpRHtOU` | `/shipping-quotes` | `https://<n8n-hetzner>/webhook/shipping-quotes` | Gestionale Refine (env var `VITE_N8N_WEBHOOK_URL`) |
| Crea Spedizione | `TyP4Awki89Uuclae` | `/create-shipment` | `https://<n8n-hetzner>/webhook/create-shipment` | Gestionale Refine (env var `VITE_N8N_WEBHOOK_URL`) |
| Comunica con Fornitore | `Yk1t6OtZ2pRaQLV3` | `/send-supplier-email` | `https://<n8n-hetzner>/webhook/send-supplier-email` | Gestionale Refine (env var `VITE_N8N_WEBHOOK_URL`) |
| SR - Sincronizza Spedizione | `8ARIYB8Cww39SyFS` | `/segna-spedito` | `https://<n8n-hetzner>/webhook/segna-spedito` | Gestionale Refine |
| SR - Sincronizza Annullamento | `LxowDJhzo5lHgdf2` | `/annulla-ordine` | `https://<n8n-hetzner>/webhook/annulla-ordine` | Gestionale Refine |
| SR - Sincronizza Accettazione | `vkvXa8Y4ZAiaApCs` | `/accetta-ordine` | `https://<n8n-hetzner>/webhook/accetta-ordine` | Gestionale Refine |
| Import 60 giorni | `nkORl6cWJ1GQ5Azg` | `/import-60-giorni` | `https://<n8n-hetzner>/webhook/import-60-giorni` | Uso manuale / Make |
| Aggiornamento Stati | `k8j7TkQ015qbOEdi` | `/status-ordini` | `https://<n8n-hetzner>/webhook/status-ordini` | Uso manuale / Make |

---

## BLOCCO 3 — n8n: Credenziali hardcoded da spostare nel credential store

**P0 — CRITICO — Supabase service_role key** in chiaro in 5 workflow
La chiave bypassa RLS (accesso illimitato al DB). Va nel credential store n8n.
(Chiave redatta in questo documento — recuperarla da Supabase Dashboard → Project Settings → API)

Workflow interessati: `0VeEkjefbMv9ndT2`, `QZjKIcS1DD33Iloo`, `WMXls9EiLZtbIx8T`, `TyP4Awki89Uuclae`, `8ARIYB8Cww39SyFS`

**P0 — CRITICO — Password SellRapido** in chiaro nel corpo dei nodi
`username: sellrapidohost@gmail.com` + `password: <redatta>` hardcoded in 4 workflow.

Workflow interessati: `8ARIYB8Cww39SyFS`, `LxowDJhzo5lHgdf2`, `vkvXa8Y4ZAiaApCs`, `TyP4Awki89Uuclae`

**P1 — 17track API token** hardcoded nell'header del nodo.
(Token redatto — recuperare da 17track Dashboard → API)

Workflow: `QZjKIcS1DD33Iloo`

**P2 — Supabase anon JWT** hardcoded negli header di 10 workflow.
Da spostare nel credential store n8n come connessione Supabase.

Workflow interessati: `9DyAgNOKA4iipfyF`, `DJEEPl2Ic7t3jLIj`, `k8j7TkQ015qbOEdi`, `nkORl6cWJ1GQ5Azg`, `navx1hV9VrkMlfJj`, `8ARIYB8Cww39SyFS`, `vkvXa8Y4ZAiaApCs`, `WpGibdF6sqZ14ZC9`, `rsdqrbfpL9J7Ydcd`, `wpxds0p9uXQSORdb`

**P2 — FattureInCloud Bearer token** duplicato: presente sia come credential che hardcoded nell'header.

Workflow: `9YjpRZQmEVU2Bb4i` — rimuovere il valore hardcoded dall'header.

---

## BLOCCO 4 — Frontend Refine: env var da configurare su Vercel

| Variabile | Valore attuale (locale) | Valore produzione |
|---|---|---|
| `VITE_SUPABASE_URL` | `https://smozehzbhoeceioqxodx.supabase.co` | Stesso (il progetto Supabase è già cloud) |
| `VITE_SUPABASE_ANON_KEY` | Anon key corrente | Stessa (finché non si ruota) |
| `VITE_N8N_WEBHOOK_URL` | vuoto (usa proxy Vite) | `https://<n8n-hetzner>` |

**File da aggiornare anche:**
- `frontend/vite.config.ts` — la riga `target: 'http://localhost:5678'` nel proxy va rimossa o wrappata in `if (mode !== 'production')`

---

## BLOCCO 5 — Supabase Edge Functions: variabili d'ambiente su Supabase Dashboard

Queste funzioni già girano in cloud ma usano `Deno.env.get()` — assicurarsi che le seguenti siano configurate in Supabase Dashboard → Project Settings → Edge Functions → Secrets:

| Secret | Funzione | Note |
|---|---|---|
| `SUPABASE_URL` | Tutte | Auto-iniettato da Supabase |
| `SUPABASE_SERVICE_ROLE_KEY` | Tutte | Auto-iniettato da Supabase |
| `IDESK_WEBHOOK_SECRET` | `idesk-webhook` | Generare stringa casuale sicura |
| `EDESK_API_TOKEN` | `edesk-proxy`, `edesk-reply`, `edesk-suggest` | Token produzione eDesk |
| `ANTHROPIC_API_KEY` | `edesk-suggest` | Chiave produzione |
| `OPENROUTER_API_KEY` | `ean-guard` | Chiave produzione |
| `RESEND_API_KEY` | `ean-guard` | Chiave produzione |
| `EAN_GUARD_ALERT_EMAILS` | `ean-guard` | Email destinatari alert |

**CORS da restringere** da `*` a dominio specifico (es. `https://app.vendiamonoi.it`) in tutte le Edge Functions:
- `idesk-webhook/index.ts`, `edesk-proxy/index.ts`, `edesk-reply/index.ts`, `edesk-suggest/index.ts`, `ean-guard/index.ts`

---

## BLOCCO 6 — Make.com: credenziali hardcoded da centralizzare

**P1 — Supabase anon key** hardcoded negli header HTTP di 6 scenari
Da migrare al connector Supabase nativo Make.com (già usato correttamente in altri scenari).

Scenari interessati: `8845166`, `8871871`, `8844531`, `8879057`, `8879067`, `8879070`

**P1 — SpediamoPro authCode** hardcoded in 4 scenari
Da salvare come Custom Variable Make.com `SPEDIAMOPRO_AUTH_CODE`.
(Codice redatto — recuperare dai scenari Make.com)

Scenari: `7448627`, `7527736`, `7567286`, `8385016`

**P2 — SellRapido company UUID** (`ae030eaf-e818-4333-96a0-1abbf5d337d4`) hardcoded in 5 scenari
Da salvare come Custom Variable Make.com `SELLRAPIDO_COMPANY_ID`.

Scenari: `8845166`, `8867890`, `8879070`, `6128585`, SellRapido→Supabase Ordini Marketplace

**NOTA:** I webhook Make.com (hookId `2714743`, `3186255`, `3322448`) sono hosted su Make.com e non cambiano mai — nessuna azione necessaria.

---

## BLOCCO 7 — n8n su Hetzner: variabili d'ambiente da configurare

Prima di importare i workflow su Hetzner, configurare queste env var nel file `.env` di n8n (o Docker Compose):

```
SUPABASE_URL=https://smozehzbhoeceioqxodx.supabase.co
SUPABASE_SERVICE_KEY=<service_role_key>
SR_USERNAME=sellrapidohost@gmail.com
SR_PASSWORD=<password>
SEVENTEEN_TRACK_TOKEN=<17track_api_token>
RESEND_API_KEY=<chiave_resend>
N8N_BASE_URL=https://<dominio-hetzner>
```

---

## CHECKLIST FINALE — Ordine di esecuzione

Quando arriva il momento della migrazione, eseguire in questo ordine:

- [ ] 1. Creare VPS Hetzner + installare Docker + Coolify
- [ ] 2. Installare n8n con Docker, configurare dominio e SSL
- [ ] 3. Impostare tutte le env var di n8n (Blocco 7)
- [ ] 4. Importare tutti i workflow n8n dall'export locale
- [ ] 5. Aggiornare i 2 URL localhost nei workflow (Blocco 1)
- [ ] 6. Creare credential store n8n per: Supabase service key, Supabase anon key, SellRapido, 17track (Blocco 3)
- [ ] 7. Sostituire credenziali hardcoded con riferimenti al credential store (Blocco 3)
- [ ] 8. Aggiornare webhook URL su 17track Dashboard (Blocco 2)
- [ ] 9. Deployare frontend su Vercel con env var produzione (Blocco 4)
- [ ] 10. Configurare `VITE_N8N_WEBHOOK_URL` su Vercel con URL Hetzner (Blocco 4)
- [ ] 11. Aggiornare `vite.config.ts` per rimuovere proxy localhost (Blocco 4)
- [ ] 12. Verificare Edge Functions Supabase hanno tutti i secrets (Blocco 5)
- [ ] 13. Migrare credenziali hardcoded Make.com a Custom Variables (Blocco 6)
- [ ] 14. Test end-to-end: creare ordine di test e seguire tutto il flusso
- [ ] 15. Disattivare n8n locale
