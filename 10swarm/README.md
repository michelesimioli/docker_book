# Docker Swarm

Docker Swarm offre un cluster basato su un pool di hosts Docker e visto come uno host singolo virtuale.

La funzionalità è integrata nel Docker Engine.

Compie l'**orchestrazione** dei contenitori, ed è giudicato migliore di precedenti strumenti come _Kubernetes_ e _Apache Mesos_.

Vantaggi:
* controllo della salute dei container
* lanciare un'immagine su più container
* bilanciare il carico sui container
* scalare il numero di container a seconda del carico
* update degli applicativi su più container

Uno **swarm** è un insieme di hosts Docker, detti **nodi**, che sono attivi in **swarm mode**. Vemgono controllati da **docker-machine**.

A posteriori un container non in _swarm mode_ viene detto **standalone**.

Si possono avere più nodi su una singola macchina Docker, ma più tipicamente i nodi sono distribuiti su più mscchine, o nel Cloud.

Vi sono due tipi di nodi:
* **manager nodes** - ricevono la definizione di un servizio e la distribuiscono come **tasks** al resto dello swarm. Possono essere _manager-only_ o anch'essi eseguire i task.
* **worker nodes** - ricevono ed eseguono i task, e notificano il loro stato al _manager_

Un **servizio** è la specifica dei task da eseguire, e specifica l'immagine da usare e i comandi da lanciare.
Due tipi di servizi:
* **replicated services** - i task vengono replicati tra i nodi basati su un fattore di scala - potenzialmente più tasks per nodo
* **global services** - un singolo task per il servizio su ogni nodo

Il manager utilizza **ingress load balancing**, ed espone il servizio con un singolo **Published Port** all'esterno dello swarm. Questo può essere deciso dalle specifiche di servizio o assegnato in automatico.

L'accesso di servizio a qualsiasi nodo dello swarm viene ridiretto internamente al nodo disponibile.

Un componente DNS interno assegna ad ogni servizio una entry DNS. I client esterni accedono al servizio tramite la entry DNS e vengono ridiretti internamente al nodo disponibile - **internal load balancing**.

