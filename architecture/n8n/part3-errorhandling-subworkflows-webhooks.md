# N8N WORKFLOW AUTOMATION PLATFORM — PARTE 3
## Error Handling, Sub-Workflows, Webhooks e Nodi Avanzati

**Document Version:** 3.0
**Last Updated:** 2026-04-01
**Target Audience:** Senior Automation Engineers, Platform Architects, DevOps Teams
**Content Depth:** Expert-Level (5,000+ lines)
**Knowledge Base:** Vendiamonoi.it E-Commerce & Marketplace Automation

---

## PARTE 1: SISTEMA COMPLETO DI ERROR HANDLING

### 1.1 Modello di Errore in n8n — Anatomia Completa

#### 1.1.1 Tipologie di Errore Fondamentali

n8n implementa un sistema di error typing sofisticato che categorizza gli errori a livello di esecuzione del nodo. Comprendere le tipologie di errore è critico per costruire workflow robusti e mantenerli in produzione.

**NodeOperationError**
- Definizione: errore generato durante l'esecuzione di un'operazione di nodo
- Causa tipica: dato malformato, validazione fallita, logica di nodo non soddisfatta
- Esempio pratico:
  ```
  Webhook riceve: {"name": "John", "email": "invalid-email"}
  → Nodo "Validate Email" genera NodeOperationError
  → Message: "Email format invalid"
  ```
- Stack trace: includere tutte le funzioni coinvolte nel nodo
- Recovery: è possibile catturare e gestire nel nodo genitore con Error Output