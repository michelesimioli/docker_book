## L'Offerta Docker

Docker è un progetto Open Source, ma è promosso e gestito dalla ditta **Docker Inc**, e ospitato sul sito www.docker.com.

Docker è disponibile in due versioni:
* Docker Community Edition (CE) - libero e open source
    * Docker Engine con networking, orchestrazione e sicurezza di base
* Docker Enterprise Edition (EE) - soggetto a licenza, ulteriormente diviso in
    * Basic - Certified infrastructure, plugins e ISV containers
    * Standard - Image management e Container app management
    * Advanced - Image security scanning

Costo tipico di licenza: $750 (Basic) - $2000 (Advanced) per anno per nodo.

L'uso di Community Edition non comporta alcun piano di manutenzione o copertura assicurativa per malfunzionamenti - nessuna responsabilità legale.

Entrambe le varianti sono disponibili per 
* Ubuntu
* CentOS
* Microsoft Azure
* Amazon Web Services

I seguenti sistemi ospitano solo Docker CE:
* Debian
* Fedora
* Microsoft Windows 10
* MacOS

I seguenti sistemi ammettono solo Docker EE:
* Red Hat Enterprise Linux
* Oracle Linux
* SuSE Linux Enterprise Server
* Microsoft Windows Server 2016

#### Releases

L'etichetta di release è strutturata come anno.mese, p.es. 17.03.

A partire da marzo 2017 Docker viene aggiornato e rilasciato con i seguenti ritmi:
* trimestrali - Docker CE 'Stable' e Docker EE
* mensili - Docker CE 'Edge'

La release corrente (giugno 2017) è la 17.03.1.

Le release precedenti la 17.03 sono considerate legacy, anche se disponibili in vari archivi.

Lo sviluppo di nuove versioni pare avvenga sulla piattaforma di riferimento Ubuntu. Questa distribuzione è anche considerata lo standard per la generazione di immagini, per quanto vi sia in atto una discussione per il passaggio ad Alpine Linux.
