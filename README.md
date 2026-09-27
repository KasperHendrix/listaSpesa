Markdown# 🛒 Spesa & Dispensa Smart

Applicazione Web Client-Side (Single-Page Application) modulare, reattiva e fully-responsive sviluppata in HTML5, CSS3 e JavaScript (Vanilla ES6+). Progettata con architettura **Zero-Backend**, senza dipendenze da server di hosting o database remoti, con persistenza locale tramite `localStorage` e supporto all'export/import JSON.

---

## 📐 Architettura dei Dati e Gerarchia

L'applicazione organizza i dati su una gerarchia rigida a due livelli (**Macro-Box $\rightarrow$ Sotto-categorie**), per garantire un'esperienza mobile touch-friendly ed evitare l'inserimento a testo libero durante la composizione della spesa.

[Macro-Box (4)]
├── 🍎 Alimenti
│     ├── Freschi
│     ├── Ortofrutta
│     ├── Pasta & Dispensa
│     └── Carni & Pesce
├── 🧹 Detersivi
│     ├── Lavatrice & Piatti
│     └── Superfici & Casa
├── 🧴 Igiene
│     └── Cura Persona
└── 🏠 Casa
└── Carta & Cancelleria
---

## ⚙️ Modello di Persistenza & Schema JSON

I dati sono persistiti interamente nel `localStorage` del browser attraverso 4 chiavi principali:

| Chiave LocalStorage | Tipo | Descrizione |
| :--- | :--- | :--- |
| `appCatalog` | `Object` | Albero dei prodotti divisi per Macro-Box e Sotto-categoria |
| `activeCart` | `Array` | Elenco degli articoli attualmente selezionati per la spesa attiva |
| `historyList` | `Array` | Storico degli acquisti chiusi con dettaglio spesa e supermercato |
| `expiryList` | `Array` | Elenco dei prodotti registrati in dispensa con data di scadenza |

### Struttura Schema JSON
```json
{
  "catalog": {
    "alimenti": {
      "Freschi": ["Latte", "Yogurt"],
      "Ortofrutta": ["Mele"]
    },
    "detersivi": { ... },
    "igiene": { ... },
    "casa": { ... }
  },
  "activeCart": [
    {
      "id": 1727448000000,
      "name": "Latte",
      "macro": "alimenti",
      "subCat": "Freschi",
      "qty": 2,
      "completed": false
    }
  ],
  "historyList": [
    {
      "id": 1727448000000,
      "date": "27/09/2026 15:30",
      "amount": 42.50,
      "store": "Esselunga",
      "items": [
        { "name": "Latte", "qty": 2, "subCat": "Freschi" }
      ]
    }
  ],
  "expiryList": [
    {
      "id": 1727448000000,
      "name": "Yogurt",
      "subCat": "Freschi",
      "date": "2026-09-30"
    }
  ]
}```

🔄 Converter e Retrocompatibilità Automatici (migrateOldDataToNewSchema)Per impedire errori di deserializzazione o TypeError: Cannot convert undefined or null to object all'importazione di backup generati dalle precedenti versioni dell'app (v1), l'algoritmo di importazione esegue una migrazione deterministica:Rilevamento Schema: Verifica la presenza delle chiavi catalog ed activeCart. Se assenti, attiva la procedura di conversione.Mappatura Categorie Storiche: Mappa le vecchie categorie stringa nei Macro-Box corrispondenti:Ortofrutta, Freschi, Pasta & Dispensa, Bevande, Carni, Pesce $\rightarrow$ alimentiIgiene & Casa $\rightarrow$ detersivi / casaCategorie Sconosciute / Personalizzate $\rightarrow$ Sotto-categoria Generale sotto alimenti (fallback sicuro a zero perdita dati).Pettinatura Storico & Scadenze: Mantiene intatti importi, date e descrizioni, iniettando i nuovi campi di default (es. store: "Supermercato").📱 Dettaglio Moduli e Flussi Funzionali1. Tab Crea SpesaGriglia Macro-Box 2x2: Alimenti, Detersivi, Igiene, Casa.Badge Dinamici: Contano in tempo reale il numero di elementi selezionati per ogni macro-box.Sub-View Catalogo: Navigazione in sottopagina con aggiunta prodotti generici, selettori di quantità [ - Qty + ] e toggle "Metti nel carrello" (activeCart).2. Tab Lista SpesaConsultazione in Sola Spunta: Visualizza esclusivamente gli articoli presenti in activeCart.Fisarmonica (Accordion): Sezioni espandibili e comprimibili per sotto-categoria per facilitare lo scorrimento in negozio.Modale Chiudi Spesa:Richiede l'inserimento dell'importo spesa.Selezione obbligatoria del Supermercato (Esselunga, Ipercoop, Lidl, Conad, Unes, Amazon fresh, Carrefour, Eurospin, Pam, Bennet, Aldi, Il Viaggiator Goloso).Checkbox per il trasferimento automatico dei deperibili nella tab Scadenze.3. Tab ScadenzeFotocamera Nativa iOS/Android: Usa <input type="file" capture="environment"> per garantire la massima risoluzione e compatibilità Safari/iOS senza crash dello streaming video.OCR Tesseract.js: Analizza lo scatto fotografico per rilevare pattern di date (DD/MM/YYYY, DD.MM.YY). In caso di lettura incerta o mancante, applica un fallback con data stimata (+3 giorni) richiedendo la conferma/modifica dell'utente.4. Tab ImpostazioniAlbero Gerarchico: Gestione ad albero delle sotto-categorie di ciascun Macro-Box.Backup & Restore: Esportazione e importazione dati in formato .json con validatore e migratore integrato.🛠️ Manutenzione FuturaQuando aggiungi nuove funzionalità o modifichi lo schema dati:Aggiorna sempre defaultCatalog per i nuovi utenti che avviano l'app per la prima volta.Aggiorna la funzione migrateOldDataToNewSchema() se introduci nuovi campi obbligatori nello schema activeCart, historyList o expiryList.Allinea la Tab Guida nell'HTML affinché i mockup e le spiegazioni rispecchino esattamente le voci della UI.
