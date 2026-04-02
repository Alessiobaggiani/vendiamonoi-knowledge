# SellRapido — Regole Operative Permanenti

## RULE-SELLRAPIDO-001
**Quando si creano regole condizionali (prezzo o spedizione), impostare TUTTI i campi sulla regola principale PRIMA di creare le sottoregole.** In particolare il campo `shipping_code` (Metodo di spedizione) non si eredita automaticamente e deve essere verificato/impostato su ogni sottoregola dopo la creazione. Prima di salvare, eseguire SEMPRE una verifica programmatica di tutti i campi su tutti i record.

Origine: ERR-20260402-001

### Checklist rapida
1. Compila TUTTI i campi della regola base (incluso shipping_code)
2. Crea le sottoregole
3. Verifica shipping_code su ogni sottoregola
4. Se vuoto → imposta esplicitamente
5. Log di verifica finale prima di Conferma
