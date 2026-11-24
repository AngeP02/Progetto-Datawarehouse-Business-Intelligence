# Data Warehouse

Progetto universitario per il corso di **Business Intelligence** che prevede la progettazione e l'implementazione di un Data Warehouse per un'azienda di trasporto merci su strada.

L'obiettivo del progetto è costruire un sistema di Business Intelligence che permette all'azienda di analizzare le performance nel tempo, ottimizzare l'allocazione delle risorse e tenere sotto controllo i costi operativi.

**Autori:** Porco Angelica · Martinez Yago Otero · Andrieieva Hanna

## Architettura

Il sistema segue un'architettura a **tre livelli con approccio Bus Data Mart**:

```
Sorgenti CSV  →  Staging Area  →  ODS (Operational Data Store)  →  Data Warehouse  →  Data Mart
```

- **Staging Area** — copia grezza dei dati estratti, senza vincoli di foreign key, usata come buffer per le trasformazioni ETL
- **ODS** — dati puliti e integrati alla stessa granularità delle sorgenti, pronti anche per analisi non OLAP
- **Data Warehouse** — dati denormalizzati e ottimizzati per le query analitiche, strutturati con star schema e snowflake schema
- **Data Mart** — tre aree di analisi distinte, ognuna con il proprio cubo OLAP

## I tre Data Mart

**Rapportini**
Analisi delle operazioni di trasporto quotidiane: viaggi effettuati, chilometri percorsi, durata dei viaggi, stato dei mezzi, turni degli autisti e spese di rifornimento.

Fatti principali: `numRapportini`, `chilometriMezzo`, `durataViaggio`

**Itinerari**
Analisi geografica e logistica dei percorsi pianificati: punti di carico/scarico con maggiore traffico, durate per tipologia di merce e cliente.

Fatti principali: `numItinerario`, `durataItinerario`

**Prelievi Cisterna**
Monitoraggio dei consumi di carburante dalle cisterne aziendali: quantità prelevata, costi associati ai rifornimenti, confronto tra fornitori.

Fatti principali: `numPrelievi`, `quantitàPrelevata`

## Stack tecnologico

| Strumento | Ruolo |
|---|---|
| **MariaDB** | Database relazionale per ODS e Data Warehouse |
| **Pentaho Data Integration (PDI)** | Processo ETL (estrazione, trasformazione, caricamento) |
| **ApricotDB** | Generazione degli schemi SQL per il Data Warehouse |
| **Mondrian Schema Workbench** | Definizione dei cubi OLAP |
| **Saiku** | Analisi OLAP e drill-down sui cubi |
| **phpMyAdmin** | Gestione e ispezione del database |

## Dati sorgente

I dati arrivano da **file CSV** generati sulla base del sistema informativo aziendale. Coprono:

| File | Contenuto |
|---|---|
| `autisti.csv` | Anagrafica autisti e stato occupazionale |
| `mezzi.csv` | Parco veicoli (targa, marca, disponibilità, chilometri) |
| `rapportini.csv` | Rapportini di viaggio compilati dagli autisti |
| `itinerari.csv` | Percorsi pianificati con tipologia merce |
| `prelievi.csv` | Prelievi di carburante alle cisterne |
| `rifornimenti.csv` | Rifornimenti presso stazioni di servizio |
| `cisterne.csv` | Cisterne aziendali nei piazzali |
| `clienti_azienda.csv` | Clienti business |
| `clienti_privati.csv` | Clienti privati |
| `fornitori.csv` | Fornitori di carburante e mezzi |
| `fatture.csv` | Fatture emesse dai fornitori |
| `puntiCaricoScarico.csv` | Punti di carico/scarico geolocalizzati |
| `sedi.csv` | Sedi di clienti e fornitori |
| `revisioni.csv`, `bolli.csv`, `assicurazioni.csv` | Manutenzione e spese fisse dei mezzi |

---

## Pipeline ETL

Il processo ETL è interamente gestito con Pentaho Data Integration e si articola in tre fasi:

**1. Estrazione**
I CSV vengono caricati tramite `TextFileInput`, ordinati e deduplicati (tramite `Sort Rows` + `Unique Rows`) per rispettare i vincoli di chiave primaria, poi inseriti nel database operazionale su MariaDB.

**2. Trasformazione (Staging → ODS)**
Le trasformazioni principali applicate:
- Standardizzazione dei formati data (`yyyy-MM-dd`, `yyyy-MM-dd HH:MM`)
- Calcolo di campi derivati: `DurataViaggio`, `ChilometriPercorsi`, `DurataItinerario`, `EtàAssunzione`
- Validazione con `DataValidator`: valori negativi, date incoerenti, campi obbligatori nulli

**3. Caricamento (ODS → Data Mart)**
Popolamento di dimension table e fact table tramite join, merge e stream lookup. Le bridge table gestiscono le relazioni many-to-many tra rapportini/rifornimenti e tra prelievi/cisterne.

---

## Schema logico

Il Data Warehouse usa **Star Schema** con alcune estensioni **Snowflake** dove necessario (es. gerarchia sede per il DM Itinerari). Sono presenti viste materializzate per le aggregazioni più comuni, come il totale fatture per sede e periodo.

Indici implementati su MariaDB: **B-Tree** (il più diffuso, usato su chiavi e date), con valutazione anche di Hash, Full-Text e Spatial secondo le esigenze delle singole query.
