# -> Status: Versione 1.2 <-

![Docker](/gitbook/images/dock.png)

# Introduzione

Dal momento della sua creazione il prodotto Docker ha avuto un aumento di adozione ad un ritmo impressionante. Si stima che in media mondiale sia installato ora sul 15% dei computer.

![Adozione](/gitbook/images/adoption.png)

I maggiori adottatori di Docker sono le grandi ditte, la stessa tipologia che è da qualche anno coinvolta nella virtualizzazione.

Rispetto alla virtualizzazione, Docker offre:

* risparmio notevole di spazio
* numero più elevato di contenitori
* maggiore facilità di gestione

Non tutti sono completamente soddisfatti di Docker ed una percentuale di adottatori hanno deciso di abbandonarlo. Questo è probabilmente dovuto a

* mancata comprensione dell'ambito di applicazione
* problemi, specie di perdita di dati, nelle prime release

Docker ha avuto notevoli migliorie nel campo della sicurezza, e i timori iniziali di introdurre una specie di grosso virus nel proprio computer si sono quasi completamente affievoliti.

E' particolarmente importante in Docker l'aspetto di networking e la tendenza ad andare da grossi applicativi monolitici ad applicativi composti di piccoli microservizi cooperanti.

Docker infine sta avendo molto successo nel campo **DevOps**, favorendo l'orchestrazione dei servizi con **Swarm** ed il deployment continuo.

## Ambiti d'uso di Docker

Due figure professionali IT sono particolarmente interessate ai contenitori in luogo delle macchine virtuali:
* i sistemisti
* i programmatori

### Sistemisti

Un contenitore permette l'installazione di un applicativo di infrastruttura completo e pacchettizzato con poco sforzo. Esempi possono essere:
* web servers
* mail servers
* qualsiasi altro tipo di server
* utilities concernenti la sicurezza e il logging
* ambienti di sviluppo
* sistemi operativi completi

La quantità di applicativi già disponibili è alta e di buona qualità. I più scaricati del _Docker Hub_ sono:
* nginx
* alpine
* httpd
* redis
* busybox
* ubuntu
* mysql
* mongo
* postgres

Vi sono nel _Docker Hub_ più di 40 tali applicativi _ufficiali_ scaricati più di dieci milioni di volte.

Un contenitore è una sorta di **package** configurabile, che contiene non solo l'applicativo specifico ma anche tutti i suoi requisiti e dipendenze.

Un tipico _grosso_ contenitore ha un'immagine che richiede circa 500-1000 MB di spazio disco e circa 10-20 minuti per il download iniziale. Il suo lancio richiede pochi secondi, come pure il suo arresto.

Per loro natura i contenitori operano in **sandbox**es, separati l'uno dall'altro, con vantaggi in caso di malfunzionamento o attacchi hacker.

Insiemi di contenitori possono interagire tramite **reti virtuali** anch'esse contenitori. Possono essere inoltre organizzati in **cluster** per ottenere _ridondanza_ o _parallelismo_ operativo.

## Programmatori

I programmatori possono _pacchettizzare_ i loro prodotti in contenitori ottenendo _artefatti_ software direttamente installabili su tutte le architetture che li supportano.

I contenitori suggeriscono l'adozione di moderni paradigmi di programmazione:
* modularizzazione e interoperabilità
* uso di nuovi linguaggi di programmazione più adatti
* testing separato dei singoli contenitori
* deployment parziale e continuo
* sviluppo cooperativo di applicativi complessi



---

## Requisiti per gli Esercizi

* Ubuntu 16.04 LTS con upgrade alle ultime patch
    * **NON** dovrebbe essere una macchina virtuale
* Minimo 4 GB RAM e 200 GB HD
