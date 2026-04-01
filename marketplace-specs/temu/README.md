# Temu — Marketplace Specification

## 1. Panoramica Piattaforma

### 1.1 Fondamenti Aziendali

Temu è una piattaforma di e-commerce ultra-low-cost posseduta da **PDD Holdings** (Pinduoduo Inc., società quotata al NASDAQ: PDD), fondata nel 2015 in Cina con sede operativa nella Silicon Valley. Temu è stata lanciata negli Stati Uniti a settembre 2022 e ha raggiunto un tasso di crescita senza precedenti nel mercato globale dell'e-commerce.

**Timeline di espansione:**
- Settembre 2022: Lancio negli USA
- Marzo 2023: Ingresso nel mercato EU (Regno Unito, Germania, Francia)
- 2023-2024: Espansione rapida in Italia, Spagna, Paesi Bassi, Belgio, Polonia, Svezia, Portogallo
- Attualmente: >60 paesi supportati con localizzazione completa per EU

**Struttura Geografica:**
- **Headquarter US**: Boston, Massachusetts
- **EU Regional Hub**: Dublin, Irlanda (registrazione, compliance EU, customer support)
- **Tech Headquarters**: Shanghai, China (PDD parent company)
- **Logistics Centers**: Distributed across China, EU, USA con centri di fulfillment locali in UK, Germania, Francia, Italia

### 1.2 Modello di Business Fondamentale

Temu opera sulla base di **Consumer-to-Manufacturer (C2M)** model — il concetto distintivo è eliminare intermediari della supply chain e collegare direttamente i consumatori europei con produttori cinesi. Questo crea dinamiche di pricing radicalmente diverse dagli altri marketplace.

**Caratteristiche distintive:**

1. **Ultra-Low-Price DNA**: Il modello di business è costruito attorno a prezzi estremamente competitivi. Non è strategia di penetrazione temporanea — è il core business model permanente. I prezzi di Temu sono tipicamente 40-70% sotto quelli di Amazon/eBay per categorie comparabili.

2. **Aggressive Customer Acquisition**: Temu investe massicciamente in user acquisition (2023: spesa marketing stimata >$2 miliardi). Strategie includono:
   - Referral gamification system (bonus per condividere con amici)
   - Video advertising (TikTok, YouTube, social media)
   - In-app daily rewards e lucky draws
   - Cashback e shop credits
   - Flash deals e limited-time offers

3. **Mobile-First Architecture**: >90% del traffico è via mobile app (iOS/Android). La piattaforma è ottimizzata per engagement mobile, con feed infinito, gamification, livestream shopping.

4. **GMV Growth**: Temu ha raggiunto ~€2 bilioni stimati di GMV annuale globale (2024), rendendola il marketplace con crescita più veloce nel mondo. In EU è il 3-4° più grande per volume.

### 1.3 Modelli di Vendita

Temu offre due modelli di vendita distinti per seller:

#### Semi-Managed Model (Preferred per EU sellers)
- Seller mantiene il controllo su pricing (con limiti), inventario, e product data
- Temu fornisce fulfillment opzionale tramite EU warehouse
- Shipping da warehouse locale EU (Germania, Italia, etc.) — cruciale per velocità
- Seller riceve ordini in Seller Center, ship autonomamente o tramite Temu logistics
- Margine: Commission tipicamente 20-28% (categoria-dependent)
- Ideal per: Distributori locali EU, brand europei, fornitori con warehouse in EU

#### Fully-Managed Model
- Seller spedisce inventory a Temu warehouse (primariamente China, alcuni hub EU)
- Temu controlla totalmente pricing, promozioni, presentation
- Temu gestisce fulfillment, shipping, returns
- Seller ha visibilità limitata su pricing/performance
- Margine: Commission 18-24% (leggermente inferiore, ma Temu spesso abbassa ulteriormente il prezzo)
- Ideal per: Manufacturer cinesi, fornitori senza presence EU, brand entry-level

**Hybrid Approach**: Alcuni seller operano entrambi i modelli simultaneamente per diversi SKU set.

### 1.4 Metriche di Crescita e Penetrazione

- **DAU (Daily Active Users)**: >80 milioni globali (2024), ~15-20 milioni nell'EU allargata
- **EU Market Position**:
  - Germania: Market leader tra marketplace per numero di nuovi utenti (2024 SuperData)
  - Italia: Top 10 e-commerce platform per visita (similarweb)
  - Francia: Crescita anno-su-anno 350%+ (2022-2024)
- **Conversion Rate**: 1.5-3% (basso, ma compensato da volume)
- **Average Order Value**: €8-15 (estremamente basso vs. €40-60 su Amazon)
- **Repeat Purchase Rate**: 25-35% (forte retention di utenti acquisiti)

### 1.5 Posizionamento Competitivo

**vs. Amazon**:
- Prezzi 50-70% inferiori
- No Prime/subscription required
- Shipping più lento (14-28 giorni EU standard)
- Meno brand premium, focus su volume/novelty
- Seller controls meno su presentation

**vs. AliExpress**:
- Temu: mobile-optimized, localizzato completamente in EU, customer support locale
- AliExpress: web-first, meno gamification, seller-centric
- Temu: maggiore volume per bestseller, logistics più veloci

**vs. eBay**:
- Temu: marketplace solo (no auctions/collectibles)
- Temu: mega-volume su beni nuovi vs. eBay mixed used/new
- Shipping: Temu centralizzato vs. eBay seller-dispersed

### 1.6 Struttura del Marketplace Temu in EU

Temu opera con una struttura **multi-region federata** in Europa, non come singolo marketplace monolitico. Questo ha implicazioni cruciali per seller operation.

**Regional Instances**:

1. **Temu.com (Multi-country default)**
   - Serves: UK, France, Germany (primary German traffic)
   - Primary warehouse: Frankfurt, Germany