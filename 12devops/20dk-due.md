## Docker e il Secondo Principio

### Velocità

L'idipendenza dei container e la loro relativa _leggerezza_ consente lo stop parziale della _pipeline_ di produzione e l'isolamento del difetto.

### Variazione

Un applicativo monolitico è _fragile_, un applicativo suddiviso in _microservizi_ indipendenti è intrinsecamente _robusto_.
I cambiamenti impattano un ambito limitato, non l'intera struttura.

### Visualizzazione

Il supporto al _feedback loop_ consiste tipicamente nell'includere nelle immagini Docker delle **metainformazioni** relative all'ambiente di controllo versione (tipicamente _Git_) e di build, per esempio:
* quando, dove e perchè è stato costruito
* quale era l'immagine precedente
* con quali comandi viene lanciato, validato, monitorato e aggiornato
* quali sono i riferimenti umani ai progettisti e sviluppatori
* quali sono i riferimenti ai repositories di controllo sorgente
