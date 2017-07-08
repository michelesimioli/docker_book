## Docker e il Primo Principio

Ottimizzare le tre **V**:
* **Velocità**
* **Variazione**
* **Visualizzazione**

### Velocità

#### Flusso di Sviluppo

Ambiente Docker sul laptop dello sviluppatore per lo sviluppo e il testing di applicazioni in containers, che implementano l'intero stack di servizi (ambiente _microservices_).
* Tempo di **spin-up** molto ridotto rispetto a servizi su più macchine fisiche
* Basso tempo di ciclo _nuova feature -> testing -> refactoring_
* Ben allineato col **Test Driven Development** e con lo **Sviluppo Incrementale**

#### Flusso di Integrazione

Velocizzazione di un sistema di Integrazione Continua (**Continuous Integration** - CI) con l'utilizzo di **Docker Build Slaves**.
Questi consistono in multipli ambienti Docker di Build entro gli ambienti Docker di Sviluppo (**Docker-in-Docker**):
* forniscono un isolamanto netto tra _sviluppo_ e _build_
* ogni ambiente di _build_ si riferisce al proprio ambiente di _sviluppo_ soprastante
* sono possibili più _build_ alternativi e simultanei
* l'ambiente di _build_ può essere completamente azzarato senza impattare l'ambiente di _sviluppo_

Altri vantaggi di Docker utilizzano in modo appropriato lo **Union File System** e la possibilità di **Copy-on-Write**.


#### Flusso di Deployment

Il problema del **Continuous Delivery** (CD) è tipicamente affrontato con la tecnica del **Blue-Green Deployment**.
Un nodo del _cluster_ (_Green_) è aggiornato con la nuova versione e testato sul campo, mentre gli altri nodi (_Blue_) continuano con la versione precedente.
In caso di problemi viene effettuato il _rollback_ dei nodi Green, altrimenti si procede col _roll-forward_ dei nodi Blue.

I Docker containers favoriscono:
* l'isolamento dei nodi
* la velocità di _roll-forward_ e _rollback_

### Variazione

Docker diminuisce la variabilità degli ambienti di deployment perchè l'ambiente operativo è pacchettizzato nel container insieme all'applicazione.

Questo è diverso, p.es. dagli applicativi Java in formato WAR o EAR che si trovano ad operare in Application Servers anche diversi.

### Visualizzazione

L'organizzazione in Microservizi Containerizzati fà sì che ogni servizio rifletta caratteristiche specifiche business di quello che offre.

Ogni servizio si trova quindi _elevato_ a livello primario **visibile** e non occorre implementare l'intero applicativo per avere visibilità del comportamento.
