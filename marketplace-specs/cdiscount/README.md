# Cdiscount Marketplace - Specifiche Tecniche Complete

> **Documento di riferimento per CTO, System Architects, Marketplace Operators**
>
> **Versione:** 2.1.1 | **Data:** Aprile 2026 | **Audience:** Vendiamonoi.it Engineering
>
> **Sottosistema:** Cdiscount Pro, Cdiscount Marketplace (powered by Octopia/Mirakl), C le Marché
>
> **Regione:** Francia + DOM-TOM | **Valuta primaria:** EUR

---

## 1. Informazioni Generali

| Attributo | Valore |
|-----------|--------|
| **Nome Ufficiale** | Cdiscount.com / Cdiscount Marketplace |
| **Fondazione** | 1998 |
| **Sede Legale** | Bordeaux, Francia |
| **Società Madre** | Groupe Sofina (Société Générale Surveillance) / Capital affiliation depuis 2020 |
| **País Operativo Primario** | Francia (FRA) + DOM-TOM (Réunion, Guadeloupe, Martinique, Guyane) |
| **URL Ufficiale Seller** | https://www.cdiscount.com/sellers |
| **URL API Documentazione** | https://api-docs.octopia.io / https://developers.cdiscount.com |
| **URL Portal Venditori** | https://sellers.cdiscount.com |
| **Categoria GMV Globale** | Top 10 marketplace Francia; €3.2B GMV annuale (2024) |
| **Tipologia Modello** | B2C Marketplace + B2B Fulfillment |
| **Numero Seller Attivi** | 7000+ (Francia); 2500+ internazionali |
| **SKU Totali Catalogo** | 80+ milioni (incluse varianti) |
| **Copertura Geografica Spedizioni** | Francia, Benelux, Spagna, Italia, Germania, Svizzera, Austria, Polonia, República Checa |

---

## 2. Modello di Business — Tre Pilastri

### 2.1 Cdiscount Marketplace (Powered by Octopia/Mirakl Hybrid)

**Architettura tecnica:** Sistema ibrido proprietario Octopia basato su codebase Mirakl 2.x.
- **Visibilità Prodotti:** Integrazione search engine dedicato (Algolia + Elasticsearch proprietario)
- **Featured Placement:** Algoritmo A/B testing per posizionamento bestseller
- **Commissioni:** Tabella variabile per categoria (vedi sezione 9)
- **Pagamento Seller:** Ciclo settimanale (lunedì)
- **Onboarding:** 5-7 giorni lavorativi post-KYC KYSC