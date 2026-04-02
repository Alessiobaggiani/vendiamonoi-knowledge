# Make.com - Part 3: IML, Data Stores, Webhooks e Trasformazione Dati
## EXPERT-LEVEL TECHNICAL REFERENCE — 5,000+ Lines

**Target Audience**: Senior automation engineers training to become Make.com platform experts.
**Version**: Make.com 2026 (current)
**Last Updated**: 2026-04-01

---

## TABLE OF CONTENTS

1. IML — Integration Markup Language: Riferimento Completo
2. Data Stores — Database Integrato in Make
3. Webhooks — Trigger in Tempo Reale
4. Trasformazione Dati Avanzata
5. HTTP Module — Integrazione API Universale

---

# 1. IML — Integration Markup Language: Riferimento Completo

## 1.1 Sintassi Base di IML

IML è il linguaggio di espressione nativo di Make utilizzato in campi mapper, condizionali, e moduli di trasformazione. È il fondamento di ogni trasformazione dati complessa in Make.

### Struttura Fondamentale

```
Literal text outside expressions
{{function(parameter)}} - expression inside curly braces
Mixed: "prefix-{{expression}}-suffix"
```