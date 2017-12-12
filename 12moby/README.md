# Il Progetto Moby

![mobyproject](../gitbook/images/mobyproject.png)

E' un progetto _community_ con _Open Governance_ (come Fedora Linux).

Il suo motto è:
> Un'infrastruttura aperta per assemblare sistemi di contenitori specializzati senza reinventare la ruota.

E' uno sviluppo di librerie di componenti, come mattoni Lego, per la costruzione di contenitori, a livello di genericità più elevato di _Docker_.

Vengono anche chiamati:
> Strumenti di innovazione di massa.

Fanno parte di uno _stack per programmare l'Internet_.

![stack](../gitbook/images/stack.md)

In quest'ottica _Docker_ stesso fa uso di _Moby_ nella sua versione Open Source, ed è possibile per lo sviluppatore generare un sistema di contenitori che non sia _Docker_.

Il Progetto Moby è destinato a ingegneri software, integratori ed entusiasti che vogliano inventare, costruire, modificare, sperimentare sistemi di build basati sui contenitori. Non è per chi cerca un sistema con supporto commerciale, ma per chi vuole lavorare ed imparare con codice open source.

I progetti attivi sviluppati con componenti Moby sono listati su: `http://mobyproject.org/projects/`. Alcuni dei più gettonati sono:
* **containerd** [Go]
    * runtime per contenitori
* **runc** [Go]
    * strumento CLI per il lancio e controllo dei contenitori secondo le specifiche dello standard OCI (`https://www.opencontainers.org/`)
* **linuxkit** [Logos]
    * toolkit per la costruzione di sistemi operativi per containers che siano sicuri, portatili e leggeri
* **swarmkit** [Go]
    * toolkit per l'orchestrazione di sistemi distribuiti su scala qualsiasi, incluso protocolli di scoperta nodi, _consenso_, schedulazione dei task
* **hyperkit** [C]
    * toolkit per l'_embedding_ di capacità di _hypervisor_ negli applicativi
* **distribution** [Go]
    * ambiente per la pacchettizzazione. lo storaggio e il delivery di contenuto basato sui contenitori
