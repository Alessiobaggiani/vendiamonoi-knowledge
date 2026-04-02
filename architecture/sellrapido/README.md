# SellRapido Knowledge Base

## Overview

SellRapido è una piattaforma di gestione centralizzata per la multi-channel selling. Consente di pubblicare cataloghi prodotti su molteplici marketplace e piattaforme ecommerce da una singola interfaccia, gestendo prezzi, spedizioni, quantità, filtri e ordini in modo unificato.

### Cos'è SellRapido

SellRapido funge da intermediario tra il tuo catalogo prodotti e i vari canali di vendita (marketplace come Amazon, eBay, ePrice; ecommerce come Shopify, Magento, PrestaShop). Permette di:

- Caricare e aggiornare cataloghi da molteplici fonti (FTP, Danea, Fatture in Cloud)
- Configurare regole di pubblicazione avanzate (prezzi, ricarichi, commissioni, spedizioni)
- Pubblicare prodotti su tutti i canali contemporaneamente
- Gestire ordini in post-vendita da un'unica dashboard
- Automatizzare processi di tracking, fatturazione e corrieri

### Come funziona

SellRapido opera in due fasi principali:

#### Fase 1: Pre-Vendita (Prevendita)
- Importazione catalogo
- Configurazione canali di vendita (marketplace/ecommerce)
- Impostazione regole di pubblicazione (prezzi, spedizioni, quantità, filtri)
- Pubblicazione prodotti su canali

#### Fase 2: Post-Vendita
- Ricezione ordini dai marketplace
- Gestione stati ordini
- Inserimento tracking e dati spedizione
- Invio email personalizzate
- Integrazione corrieri
- Fatturazione

## Indice della Knowledge Base

### Account e Setup
- **[account-setup.md](account-setup.md)** - Impostazione account, accesso, membri, fatturazione, autenticazione

### Configurazione Canali
- **[marketplace-config.md](marketplace-config.md)** - Configurazione di tutti i marketplace (Amazon, eBay, Carrefour, Cdiscount, Conrad, ePrice, Fnac, IBS, Kaufland, Leroy Merlin, ManoMano, Mediaworld)
- **[ecommerce-config.md](ecommerce-config.md)** - Configurazione piattaforme ecommerce (Shopify, Magento, PrestaShop, WooCommerce)

### Pre-Vendita (Cataloghi e Pubblicazione)
- **[catalog-management.md](catalog-management.md)** - Caricamento cataloghi, specifiche feed CSV, varianti, attributi, esportazione
- **[publication-rules.md](publication-rules.md)** - Regole di pubblicazione: prezzi, ricarichi, spedizioni, quantità, filtri, categorie, resi, titolo e template

### Post-Vendita (Ordini e Operazioni)
- **[order-management.md](order-management.md)** - Gestione ordini, stati, tracking, forza scarico, email, etichette Dymo, bandierine, API corrieri

### Troubleshooting
- **[publication-errors.md](publication-errors.md)** - Errori di pubblicazione e soluzioni per marketplace

## Architettura della Documentazione

Ogni documento è strutturato per contenere:
- **Indice** - Navigazione veloce ai contenuti
- **Procedure step-by-step** - Istruzioni dettagliate per ogni operazione
- **Tabelle di riferimento** - Campi disponibili, parametri, valori
- **Esempi concreti** - Casi d'uso e best practices
- **Avvertenze** - Limitazioni e casi speciali

## Principi Fondamentali

### Regole Generali vs Specifiche
- **Regole Generali**: impostate nella parte superiore, valgono per TUTTI i prodotti
- **Regole Specifiche**: impostate nella parte inferiore, applicate solo ai prodotti che rientrano nei parametri

### Priorità delle Regole
Se uno o più prodotti rientrano nei parametri di più righe, verrà applicata sempre la regola della riga più in alto. L'ordine può essere modificato trascinando verso l'alto o verso il basso.

### Filtri di Inclusione/Esclusione
- **Inclusione**: i prodotti che fanno match verranno PUBBLICATI
- **Esclusione**: i prodotti che fanno match NON verranno pubblicati
- **Priorità**: i filtri di esclusione prevalgono sempre su quelli di inclusione

### Best Practices
1. Evitare filtri di inclusione totali (mandare in pubblicazione gradualmente)
2. Inserire quantità minima di 8/10 pezzi per evitare overselling
3. Monitorare il Report Inserzioni dopo la pubblicazione
4. Contattare l'assistenza SellRapido per filtri per titolo
5. Verificare che la categoria canale sia corretta per commissioni accurate
6. Per il filtro per marca: inserire nomi separati da virgola senza spazi (es. `canon,infinity light,zyxel`)

## Flusso Tipico di Utilizzo

### Setup Iniziale
1. Creare account SellRapido
2. Configurare i canali di vendita desiderati (Impostazioni > Credenziali Marketplace)
3. Caricare il catalogo prodotti

### Pubblicazione Prodotti
1. Configurare le impostazioni di pubblicazione per ogni canale
2. Impostare prezzi e ricarichi
3. Configurare spedizione
4. Impostare quantità
5. Creare filtri di inclusione
6. Monitorare il Report Inserzioni

### Gestione Ordini
1. Gli ordini vengono scaricati automaticamente ogni 5-10 minuti
2. Verificare stato ordini in Post Vendita > Ordini
3. Inserire tracking e dati spedizione
4. Inviare email ai clienti
5. Esportare per fatturazione

## Versione Documento
- **Data**: 2 aprile 2026
- **Fonte**: Wiki SellRapido ufficiale (https://wiki.sellrapido.com)
- **Copertura**: Documentazione completa estratta da 8 pagine wiki

## Supporto

Per problemi specifici, aprire un ticket in SellRapido > Impostazioni > Supporto.

### Categorie di Supporto Principali
- **Cataloghi**: Aggiunta, modifica, errori importazione, rimozione
- **Marketplace/Ecommerce**: Configurazione canali, impostazioni pubblicazione, errori
- **Ordini**: Gestione stati, tracking, fatturazione
- **Corrieri**: Automatismo ordini, integrazioni
- **Impostazioni Account**: Access, fatturazione, metodi pagamento
- **Fatturazione**: Dati fatturazione, richieste fatture, rimborsi

### Contatto Supporto Diretto
Le tempistiche di gestione dei ticket sono generalmente veloci. Una volta ricevuta risposta dal team SellRapido, hai 5 giorni per controreplica prima della chiusura automatica del ticket.
