# Sicurezza delle Immagini

Un'immagine malefica ha completo accesso alla macchina host.

Occorre assicurarsi della provenienza delle immagini - questo è un problema di tutto il software precompilato.

Per gli applicativi ad alto livello la soluzione più completa ed opportuna è **Open Source**: ispezionando il codice sorgente e compilandolo localmente si ha una assicurazione dell'assenza di _malware_.

Similmente un applicativo Docker ad alto livello può essere in formato sorgente e compilato prima della sua Dockerizzazione.

Il sistema operativo però, e isuoi componenti maggiori come il kernel, il file system e la rete, non sono facilmente compilabili dall'inizio - _Gentoo_ ci prova, ma non molti hanno parecchi giorni di pazienza.

Parallelamente le immagini di base di Docker vengono scaricate da repositories di rete. Quello di default, il _Docker Hub_ può considerarsi fidato, supposto che non avvengano intercettazioni e sostituzioni nel flusso di rete.

Altri repositories privati devono essere associati a ditte di comprovata fiducia.

Lo scarico, invece, di applicativi finali precompilati o anche immagini di base, da repositories pubblici è **estremamente pericoloso**.

### Organizzazione Pratica

* Scaricare da Docker Hub le immagini di base necessarie
* Scaricare da GitHub, SourceForge o altri circuiti di distribuzione, gli applicativi in formato Open Source
* Sottoporre i sorgenti a controllo versione
* Ispezionare e comprendere il codice sorgente scaricato
    * Farsi aiutare da 'professionals'
* Compilare e costruire l'applicativo _in loco_
* Preparare un `dockerfile` e compiere il build dell'immagine
* Conservare l'immagine in un repository locale

### Strumenti di Ispezione

Sono disponibili un certo numero di strumenti di ispezione di immagini già compilate.
Come degli _antivirus_, questi strumenti compiono uno **Static Code Analysis** (SCA), scandendo il codice alla ricerca di pattern che denotano la presenza di malware.
Quasi sempre si fa riferimento ad archivi di **Common Vulnerabilities and Exposures** (CVE) disponibili o su siti pubblici o a pagamento.

I servizi di SCA sono di solito forniti da ditte specializzate, previa sottoscrizione, o fanno parte di una offerta _Cloud_ specifica per i contenitori Docker.

#### Strumenti Commerciali

* Docker Cloud - `https://docs.docker.com/docker-cloud/builds/image-scan/`
* TwistLock - `https://www.twistlock.com/`
* Layered Insight - `http://layeredinsight.com/`

### Strumenti Open Source

* Banyan Collector - `https://github.com/banyanops/collector`
* Mobile Security Framework - `https://github.com/MobSF/Mobile-Security-Framework-MobSF/wiki/7.-Docker-Container-for-MobSF-Static-Analysis`
* Clair - `https://github.com/coreos/clair`

![Clair](../github/images/clair.png)


### Docker Content Trust

Disponibile da Docker 1.8. I produttori di immagini compilate firmano le immagini.
Quando è abilitato, Docker compie il `pull` solo di immagini firmate.
Va abilitato:
```
export DOCKER_CONTENT_TRUST=1
```

Prima del pull di un'immagine firmata, Docker compie la prima volta il download del certificato di firma dal repository. Questo va accettato separatamente, come con SSH.

E' possibile anche compiere lo upload di immagini firmate. La prima volta viene generata una coppia di chiavi ed un certificato.
Attenzione a non perdere la chiave privata, che si trova in:
```
~/.docker/trust/private
```

Per scaricare un'immagine non firmata, quando il trust è abilitato, usare il comando
```
docker pull --disable-content-trust immagine
```

Il Docker Content Trust non protegge da attacchi **Man-In-The-Middle** (MITM) o di _Ingegneria Sociale_.

Per configurare un repository locale con _Docker Content Trust_ occorre installare un **Notary Server**.

