# SellRapido — Registro Errori

## ERR-20260402-001

**Software:** SellRapido (ExtJS)
**Categoria:** assunzione non verificata / configurazione incompleta sottoregole
**Gravità:** media
**Frequenza:** nuova
**Data:** 2026-04-02
**Task:** Configurazione regole spedizione condizionali Ferlegno → ManoMano ITA

### Errore osservato
Quando ho creato le 9 regole condizionali di spedizione, ho impostato correttamente `shipping_fee`, `include_vat`, `vat_perc` e `delivery_days` nel setting JSON di ogni regola. Tuttavia NON ho impostato il campo `shipping_code` (Metodo di spedizione = "Bartolini") nelle sottoregole. Il metodo è stato impostato solo nella regola principale.

### Contesto
Tab Spedizione → Spedizione Principale → Griglia regole condizionali. Ho creato 9 regole condizionali per fasce di peso BRT. Ho impostato il metodo "Bartolini" solo nella form della regola principale (`combo-3010`), ma non nelle sottoregole.

### Causa radice
Ho assunto che il campo `shipping_code` si trasferisse automaticamente dalla regola base alle sottoregole al momento della creazione (come fanno gli altri campi: shipping_fee, include_vat, vat_perc, delivery_days). In realtà il campo `shipping_code` NON si eredita automaticamente, oppure va comunque verificato e impostato esplicitamente su ogni sottoregola.

### Segnale che avrei dovuto riconoscere
1. Non ho verificato il contenuto completo del `setting.details[0]` di ogni sottoregola dopo la creazione
2. Non ho controllato se il campo `shipping_code` era valorizzato nelle sottoregole
3. L'approccio corretto è: impostare PRIMA tutti i campi sulla regola base (incluso shipping_code), POI creare le sottoregole (che ereditano), OPPURE verificare e impostare shipping_code su ogni sottoregola dopo la creazione

### Correzione applicata
Alessio ha dovuto impostare manualmente il metodo Bartolini su tutte le sottoregole.

### Regola permanente
**Quando si creano regole condizionali su SellRapido:**
1. Impostare TUTTI i campi sulla regola principale PRIMA di creare le sottoregole (shipping_fee, include_vat, vat_perc, delivery_days, **shipping_code**)
2. Dopo la creazione delle sottoregole, VERIFICARE che `shipping_code` sia valorizzato in ogni sottoregola
3. Se `shipping_code` è vuoto nelle sottoregole, impostarlo esplicitamente via JavaScript su ogni record
4. Non dare MAI per scontato che un campo si erediti automaticamente — verificare SEMPRE

### Checklist preventiva
- [ ] Regola base: shipping_code impostato PRIMA di creare sottoregole
- [ ] Dopo creazione sottoregole: verificare shipping_code su ogni record
- [ ] Se shipping_code vuoto: impostarlo via `rec.get('setting').details[0].shipping_code = 'Bartolini'`
- [ ] Prima di salvare: log di verifica di TUTTI i campi su TUTTE le regole

### Impatto potenziale se non corretto
Regole di spedizione senza metodo definito → possibile rifiuto da parte del marketplace, errori di pubblicazione, o spedizioni senza corriere assegnato.

### Esempio corretto
```javascript
// ORDINE CORRETTO:
// 1. Impostare TUTTI i campi sulla regola base (incluso shipping_code)
Ext.getCmp('combo-XXXX').setValue('Bartolini');
Ext.getCmp('numberfield-XXXX').setValue(4.27);
Ext.getCmp('checkbox-XXXX').setValue(true);  // include_vat
Ext.getCmp('numberfield-XXXX').setValue(22); // vat_perc

// 2. POI creare le sottoregole (che ereditano i valori base)
btn.handler.call(btn); // + Nuovo

// 3. VERIFICARE che shipping_code sia presente in ogni sottoregola
records.forEach(function(rec) {
  var setting = rec.get('setting');
  if (!setting.details[0].shipping_code) {
    setting.details[0].shipping_code = 'Bartolini';
    rec.set('setting', setting);
  }
});
```
