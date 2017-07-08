# Installazione

Docker non è un singolo prodotto che si può considerare separatamente, a meno che non ci si limiti ad esercizi didattici.

Chi usa Docker sul campo sta compiendo una transizione ed una adozione di tutta una serie di nuove tecnologie, che sono o usate in quasi tutti i campi di sviluppo, o aspetti moderni di particolari ambiti di sviluppo.

#### Git

E' l'ambiente di controllo versione. Serve a:
* manutenzione di tutte le varianti di un progetto software
* backup su un repository remoto
* gestione del flusso di produzione di squadra software
* scaricamento da GitHub di progetti, esempi, documentazione

#### NPM

_Node Package Manager_. Richiede l'installazione di **Node.js**.
NPM velocizza lo scaricamento e installazione di una quantità di pacchetti software già compilati basati su JavaScript e non solo.

#### Go

Docker è scritto in Go. Qualche utility ausiliaria, alcuni tools disponibili su GitHub, e forse lo sviluppo di propri applicativi possono giovarsi della presenza del _toolchain_ di Go.
Go richiede che l'ambiente di sviluppo venga strutturato in una certa precisa maniera, cioè con lo _scaffolding_ adeguato di directories.

---

Inoltre è opportuno adottare il punto di vista di considerare Docker come un _catalizzatore_ nell'adozione di soluzioni efficienti ed innovative, che favoriscono il cambiamento, e non come una soluzione a problemi tradizionali pre-esistenti.

## Attenzioni

### Evoluzione

Il _mondo_ Docker è in continua evoluzione e, per quanto la tecnologia sia stabile, non può essere considerato finale.
Le innovazioni avvengono:
* nell'offerta Free e Commerciale di docker, incluso piattaforme di Cloud e di Repositories
* nei tools ausiliari, come strumenti di amministrazione remota ed orchestrazione
* nelle immagini disponibili in rete, Frre e Commerciali, e nella loro configurazione e _management_

Non è saggio basarsi su immegini, esempi e documentazione anteriori al 2016.

### Complessità

Il mondo Docker ha due livelli di complessità principali:
* applicativi per singolo host
* applicativi per clusters e cloud
    * ridondanza
    * necessità di coordinamento ed orchestrazione
    * outsourcing

Corrispondentemente il suo studio è a più livelli:
* features di base
* progettazione e realizzazione di applicativi multi-host
* strumenti non-Docker ausiliari e cooperativi

