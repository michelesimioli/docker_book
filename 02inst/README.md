# Installazione

Docker non è un singolo prodotto che si può considerare separatamente, a meno che non ci si limiti ad esercizi didattici.

Chi usa Docker sul campo sta compiendo una transizione ed una adozione di tutta una _serie di nuove tecnologie_, che sono o usate in quasi tutti i campi di sviluppo, o aspetti moderni di particolari ambiti di sviluppo.

Inoltre è opportuno adottare il punto di vista di considerare Docker come un _catalizzatore_ nell'adozione di soluzioni efficienti ed innovative, che favoriscono il cambiamento, e non come una soluzione a problemi tradizionali pre-esistenti.

## Evoluzione

Il _mondo_ Docker è in continua evoluzione e, per quanto la tecnologia sia stabile, non può essere considerato finale.
Le innovazioni avvengono:
* nell'offerta Free e Commerciale di docker, incluso piattaforme di Cloud e di Repositories
* nei tools ausiliari, come strumenti di amministrazione remota ed orchestrazione
* nelle immagini disponibili in rete, Frre e Commerciali, e nella loro configurazione e _management_

Non è saggio basarsi su immegini, esempi e documentazione anteriori al 2016.

## Complessità

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

## Git

Docker include parte dei protocolli **Git** per il trasferimento di files.

_Git_ è l'ambiente di controllo versione con cui Linux è sviluppato. Serve a:
* manutenzione di tutte le varianti di un progetto software
* backup su un repository remoto
* gestione del flusso di produzione di squadra software
* scaricamento da **GitHub** di progetti, esempi, documentazione

Tutti i repositories di immagini docker, tra cui quello di default, **DockerHub**, sono repositories _Git_.

In Linux _Git_ è preinstallato. Su alcune versioni minime ove non lo sia, è facilmente installabile col comando:
* `sudo apt install git` (Ubuntu e Debian)
* `sudo yum install git` (Red Hat)

In Windows e Mac, _Git_ viene come parte del pacchetto di installazione di docker.

