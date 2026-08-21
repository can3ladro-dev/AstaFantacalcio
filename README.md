# Asta Fanta a 10 · Plancia di Comando

Web App **self-contained** per la gestione dell'asta del fantacalcio in leghe a 10 partecipanti.

> **Nota sull'origine del codice**: L'intero codice di questa applicazione è stato generato da **Qwen**, inizialmente nella versione **3.8-Max**, successivamente evoluto con **Qwen-Coder**. Non c'è intervento manuale significativo: ogni funzione, stile e componente UI è prodotto AI-generated.

---

## 📋 Funzionalità Principali

### 1. **Configurazione Iniziale (Foglio 1)**
- Impostazione crediti iniziali (default: 500)
- Numero di squadre partecipanti (max 11)
- Slot per ruolo: Portieri (3), Difensori (8), Centrocampisti (8), Attaccanti (6)
- Pesi percentuali per ruolo (P: 5%, D: 15%, C: 30%, A: 50%)
- Sensibilità del prezzo (reazione del mercato)

### 2. **Log Asta (Foglio 2)**
- Registro di tutti gli acquisti (250 righe preconfigurate)
- Ricerca giocatori integrata nel listone ufficiale
- Filtro per ruolo (P/D/C/A) e ordinamento (prezzo, nome, squadra, quotazione)
- Aggiunta manuale calciatori non presenti nel listone
- Importazione CSV/XLSX del listone ufficiale (supporto SheetJS)
- Barra rapida per inserimento acquisti (Invio per confermare)
- Evidenziazione duplicati e righe colorate per ruolo
- Menu "Acquistato da" sincronizzato con le squadre configurate

### 3. **Riepilogo Crediti (Foglio 3)**
- Calcolo automatico della spesa per squadra
- Visualizzazione crediti residui con semaforico (verde/arancio/rosso)
- Ripartizione spesa per ruolo (barra visiva)
- Slot liberi per ruolo con indicatori di criticità
- Rose di tutte le squadre espandibili

### 4. **Rosa Mia Squadra (Foglio 4)**
- Vista filtrata solo degli acquisti della propria squadra
- Raggruppamento per ruolo con subtotali
- Conteggio slot riempiti / mancanti per reparto
- Pulsante "Copia rosa · Leghe FC" per trascrizione rapida nell'app ufficiale

### 5. **Obiettivi e Bonus/Malus (Foglio 5)**
- Lista giocatori nel mirino con prezzo ideale
- **Indice di Pressione Ruolo**: algoritmo proprietario che calcola il margine degli avversari
- **Bonus/Malus dinamico**: suggerimento di offerta basato su:
  - Capienza attesa iniziale (crediti × squadre × peso ruolo)
  - Speso finora dagli avversari
  - Quota slot rimanenti
  - Fattore di sensibilità configurato
- Cruscotto con indicatori di pressione per ruolo (P/D/C/A)

### 6. **Squadre Ideali (Foglio 6)**
- Costruzione rose ipotetiche con moduli personalizzabili (3-4-3, 4-4-2, etc.)
- Assegnazione calciatori cliccando sugli slot
- Budget residuo calcolato in base ai prezzi massimi
- Salvataggio multiplo squadre
- Esportazione rosa negli Obiettivi
- Toggle "Info Asta" per visualizzare calciatori già acquistati

---

## 🏗️ Struttura del Codice Self-Contained

L'applicazione è interamente contenuta in un **singolo file HTML** (`index.html`, ~2422 righe):

```
index.html
├── <head>
│   ├── Meta tag & viewport
│   ├── Google Fonts (Barlow, Barlow Condensed, JetBrains Mono)
│   └── SheetJS CDN (xlsx-0.20.1) per import XLSX
│
├── <style> (~750 righe)
│   ├── Variabili CSS (--bg, --panel, --gold, --green, etc.)
│   ├── Reset & layout generale
│   ├── Componenti UI:
│   │   ├── .topbar, .brand, .live-chip
│   │   ├── .tabs, .tab (navigazione sticky)
│   │   ├── .sheet, .sheet-head (contenitori sezione)
│   │   ├── .grid, table (tabelle dati)
│   │   ├── .btn, .quick (form rapidi)
│   │   ├── .card-search-details, .card-grid (ricerca giocatori)
│   │   ├── .adv-report (report fuzzy matching)
│   │   ├── .picker-modal (selettore calciatori)
│   │   └── .pitch-preview (campo da calcio stilizzato)
│   ├── Animazioni (@keyframes: beat, pop, rise, flash)
│   └── Media queries implicite (layout responsive)
│
├── <body>
│   ├── .wrap (contenitore principale)
│   │   ├── .topbar (header con stats live)
│   │   ├── .tabs (navigazione tra 6 view)
│   │   ├── 6 × <section class="view"> (Config, Log, Riepilogo, Rosa, Obiettivi, Squadre)
│   │   └── <footer>
│   ├── <datalist id="dlN"> (autocomplete nomi)
│   ├── <div id="toasts"> (notifiche temporanee)
│   └── <div id="pickerModal"> (modale selezione giocatori)
│
└── <script> (~1650 righe)
    ├── Costanti globali (LS, ROLES, RINFO, LOGN, MAXT, CARD_LIMIT)
    ├── Utility functions ($, esc, fN, fD, fPct, r0, RMAP)
    ├── Data layer:
    │   ├── def() → struttura dati default
    │   ├── sanitize*() → validazione input
    │   ├── load() / save() → localStorage persistence
    │   └── loadUIPrefs() / saveUIPrefs() → preferenze UI
    │
    ├── Rendering:
    │   ├── renderHeader() → tabella riepilogo
    │   ├── renderRiepilogo() → crediti per squadra
    │   ├── renderRoseSquadre() → rose espandibili
    │   ├── renderRosa() → vista rosa propria squadra
    │   ├── buildLog() → tabella log asta
    │   ├── buildObi() → tabella obiettivi
    │   ├── renderCruscotto() → indicatori pressione
    │   ├── renderCardSearch() → griglia ricerca giocatori
    │   └── renderSquadEditor() → editor squadre ideali
    │
    ├── Logica di calcolo:
    │   ├── stats() → totali spesa/ruolo per squadra
    │   ├── pressure() → indice pressione per ruolo
    │   ├── computeAll() → orchestratore calcoli
    │   └── getPP(nome) → recupero prezzo max dal listone
    │
    ├── Import/Export:
    │   ├── parseCSV() → parsing intelligente (rileva delimitatore)
    │   ├── importJSON() → ripristino backup
    │   ├── exportJSON() → download backup
    │   └── importListoneXLSX() → parsing file ufficiale (SheetJS)
    │
    ├── Fuzzy Matching (Advice Import):
    │   ├── normTxt() → normalizzazione testo (rimozione accenti)
    │   ├── levDist() / levSim() → distanza di Levenshtein
    │   ├── tokenMatchScore() → matching bidirezionale token
    │   ├── nameSim() → similarità nominativa
    │   ├── matchAdviceRows() → associazione righe CSV → listone
    │   └── renderAdvReport() → report accuratezza con score %
    │
    ├── Squadre Ideali:
    │   ├── MODULES → dizionario moduli (4-3-3, 3-5-2, etc.)
    │   ├── emptySquad() → creazione nuova squadra
    │   ├── changeModule() → riconfigurazione slot
    │   ├── openPicker() / renderPickerList() → selettore giocatori
    │   └── normalizeBench() → gestione panchina
    │
    └── Event binding:
        ├── Navigazione tab (switch view)
        ├── Form submit (quickForm, manualAddForm)
        ├── Input change (config, filtri, ordinamento)
        ├── Click handler (pulsanti, card, slot)
        └── Keyboard shortcuts (Ctrl+S, Ctrl+H)
```

---

## 💾 Persistenza Dati

- **localStorage**: `fanta10-asta-v5` (dati principali), `fanta10-asta-ui-v1` (preferenze UI)
- I dati sopravvivono al refresh/chiusura browser
- Backup/Restore via JSON (pulsanti dedicati nella Config)
- Nessun server backend: tutto client-side

---

## 🎨 Design System

- **Palette colori**: tema scuro (#0b1712) con accenti oro (#f2c94c), verde (#4ccf7a), blu (#5ec6ff), rosso (#ff6b5e)
- **Font**: Barlow (corpo), Barlow Condensed (titoli), JetBrains Mono (numeri/dati)
- **Effetti**: gradienti radiali, backdrop-filter blur, animazioni fluide
- **Iconografia**: emoji Unicode + elementi CSS puri (nessuna libreria icone)

---

## 📊 Algoritmi Notevoli

### Indice di Pressione Ruolo (Foglio 5)
```
CapienzaAttesa = crediti_iniziali × n_squadre × (peso_ruolo / 100)
SpesoAvversari = Σ(crediti_spesi_da_avversari_nel_ruolo)
CapienzaResidua = CapienzaAttesa - SpesoAvversari
QuotaSlot = slot_liberi_avversari / (slot_ruolo × n_avversari)
IndicePressione = CapienzaResidua / (CapienzaAttesa × QuotaSlot)
BonusMalus% = (IndicePressione - 1) × sensibilità
PrezzoConsigliato = prezzo_ideale × (1 + BonusMalus%)
```

### Fuzzy Matching per Import CSV
- Normalizzazione NFD (rimozione accenti)
- Distanza di Levenshtein per similarità stringhe
- Token matching bidirezionale (gestione iniziali: "Martinez L" ↔ "Lautaro Martinez")
- Penalità lunghezza differenziale
- Score soglia: ≥0.92 (ok), 0.70-0.91 (mid), <0.70 (ko)

---

## 🔮 TODO Futuri: Porting in Angular

| # | Task | Priorità | Note |
|---|------|----------|------|
| 1 | **Setup progetto Angular 18+** | Alta | Standalone components, signals, typed forms |
| 2 | **Refactoring data layer** | Alta | Service `AstaDataService` con RxJS BehaviorSubject, interfaccia TypeScript per `S` |
| 3 | **Componentizzazione view** | Alta | 6 componenti standalone: `ConfigView`, `LogView`, `RiepilogoView`, `RosaView`, `ObiettiviView`, `SquadreView` |
| 4 | **Migration CSS** | Media | Spostare stili in SCSS modulari o Tailwind; mantenere variabili CSS come theme tokens |
| 5 | **Utility pipe** | Media | Pipe pure: `fN`, `fD`, `fPct`, `esc`, `roleLabel` |
| 6 | **Directive strutturali** | Bassa | `*dupHighlight`, `*roleColor`, `*semaphore` per logica condizionale DOM |
| 7 | **Lazy loading XLSX** | Media | Caricamento dinamico SheetJS solo quando serve (import lazy) |
| 8 | **Router navigation** | Bassa | Abilitare navigazione via URL (`/config`, `/log`, `/riepilogo`, etc.) per deep linking |
| 9 | **State persistence** | Alta | Migration localStorage → NgRx SignalStore o servizio dedicato con hydration |
| 10 | **Testing** | Media | Unit test (Jest) su algoritmi pressure/fuzzy; E2E (Playwright) su flussi critici |
| 11 | **Build ottimizzata** | Bassa | Bundle splitting, code splitting per view, preloading strategico |
| 12 | **PWA support** | Bassa | Manifest, service worker, offline-first per uso in mobilità |

### Benefici Attesi dal Porting
- ✅ **Type safety** completo con TypeScript strict mode
- ✅ **Separation of concerns**: logica business isolata in services
- ✅ **Testabilità**: injection mocking, test isolati per componente
- ✅ **Scalabilità**: aggiunta feature senza aumentare complessità esponenziale
- ✅ **Maintainability**: codebase modulare, onboarding nuovi sviluppatori semplificato
- ✅ **Performance**: ChangeDetection.OnPush, memoization nativa, lazy loading

---

## 🖥️ Utilizzo

1. Aprire `index.html` in un browser moderno (Chrome, Firefox, Edge, Safari)
2. Configurare parametri asta in **Config**
3. Importare listone XLSX (opzionale, ma consigliato)
4. Registrare acquisti in **Log Asta** durante l'asta
5. Monitorare situazione in **Riepilogo Crediti** e **Obiettivi**
6. Simulare strategie in **Squadre Ideali**

---

**Nessuna dipendenza esterna** oltre a SheetJS (caricato via CDN).  
**Nessun dato inviato a server**: privacy by design.

