# DT Platform — Esaote SpA

> Portale web per la gestione documentale del ciclo acquisti (P2P) e della logistica attiva (DDT, firma trasportatore, POD Recovery).
> Sviluppato da **Digital Technologies S.r.l.** per **Esaote SpA**.

---

## Demo

Il portale è pubblicato su GitHub Pages. Per testarlo:

| File | URL |
|---|---|
| Portale desk | `https://<org>.github.io/<repo>/` |
| App tablet firma | `https://<org>.github.io/<repo>/esaote_tablet_firma_ddt.html` |

> Entrambi i file devono essere nella stessa repo per far funzionare la comunicazione localStorage tra portale e tablet.

---

## Struttura del repository

```
/
├── index.html                          # Portale DT Platform (rinominato da esaote_portal_mockup_v4.html)
├── esaote_tablet_firma_ddt.html        # App tablet per firma DDT del trasportatore
├── README.md                           # Questo file
└── CHANGES.md                          # Log delle modifiche
```

---

## Cosa contiene il portale

### Ciclo Passivo — P2P
| Sezione | Funzione |
|---|---|
| **Ordini di Acquisto** | Lista e dettaglio ODA con PDF, doc-chain, audit trail |
| **Conferme Ordine** | Ricezione ICR, gestione variazioni, workflow escalation |
| **DDT Inbound** | Abbinamento DDT fornitori, check pre-OCR, split multi-bolla |
| **Pre-Billing** | 3-way match, approvazione Finance, contestazione fattura, posting SAP |

### Ciclo Attivo
| Sezione | Funzione |
|---|---|
| **Trasporti** | Lista trasporti con import CSV da SAP, stato firma per trasporto |
| **Firma DDT (desk)** | Schermata desk con dropdown trasporti → invio al tablet → polling firma |
| **DDT Firmate** | Archivio DDT firmate con autista/targa/ora, bulk download per SAP |
| **POD Recovery** | Match automatico/manuale POD↔DDT, conferma consegna SAP |

### Vettori
| Sezione | Funzione |
|---|---|
| **Anagrafica Vettori** | Gestione vettori con tipo connessione (Email/SFTP/API) |
| **Log Ricezione** | Log tutti i flussi in entrata per canale e vettore |

### Gestione
Workflow, Archivio, Fornitori, Amministrazione utenti, Log Flussi, Report & Export.

---

## Comunicazione portale ↔ tablet (localStorage)

Il portale e l'app tablet si parlano tramite `localStorage` del browser. Funziona solo se entrambi i file sono serviti dallo **stesso dominio** (stesso GitHub Pages repo).

```
Portale                               Tablet app
   │                                      │
   │  localStorage.setItem(               │
   │    'dt_firma_request',               │
   │    { trId, ddts[], pickupDate }      │
   │  )                                   │
   │──────────────────────────────────►  │
   │                                      │  Trasportatore compila
   │                                      │  Nome / Cognome / Targa
   │                                      │  e firma su canvas
   │                                      │
   │  ◄──────────────────────────────────│
   │  localStorage.setItem(               │
   │    'dt_firma_response',              │
   │    { status:'signed', nome,          │
   │      cognome, targa, sigImg }        │
   │  )                                   │
   │                                      │
   │  [polling ogni 1.5s]                 │
   │  Aggiorna D.trasporti + D.ddt_firmate│
```

---

## Test in locale

Per testare in locale senza GitHub Pages (Chrome blocca localStorage su `file://`):

```bash
# Nella cartella con i due file:
python3 -m http.server 8080

# Poi apri:
# http://localhost:8080/index.html
# http://localhost:8080/esaote_tablet_firma_ddt.html
```

---

## Fase 1 vs Fase 2

Il portale è progettato per operare in due fasi:

**Fase 1 (attuale)** — Nessuna integrazione SAP diretta:
- Trasporti: CSV export SAP → upload portale
- PDF DDT: email alias o SFTP → abbinamento automatico
- PDF firmati: download portale → upload manuale SAP
- Link DDT↔Fattura: CSV DDTINV → upload portale

**Fase 2 (post S4HANA)** — Integrazione real-time via API:
- SAP → DT push automatico (DDTOUT, DDTINV)
- DT → SAP push PDF firmati + dati autista (DDTIN)
- API carrier per ricezione POD automatica (BRT, DHL, GLS)
- Posting SAP automatico dopo approvazione

---

## Documenti di progetto

| Documento | Contenuto |
|---|---|
| `DT_Platform_Manuale_Utente.docx` | Guida operativa completa per gli utenti finali |
| `DT_Platform_Documento_Progetto.docx` | Architettura, flussi, piano Fase 2 — per solution architect |

---

## Sviluppato da

**Digital Technologies S.r.l.**
Via Politi, 10 — Trezzano sul Naviglio (MI)
[www.digtechs.com](https://www.digtechs.com) · A Namirial Company
