# Architettura dei Microservizi

I Microservizi sono uno stile di programmazione di applicativi distribuiti. Di per sè non sono direttamente collegati a _Docker_ se non che la containerizzazione dei microservizi è un valore aggiunto che ne permette il deployment e la manutenzione più efficienti.

La strutturazione di un applicativo distribuito in microservizi modulari fornisce i seguenti vantaggi:
* indipendenza di ciascun modulo dagli altri moduli
    * facile sostituibilità
    * deploy indipendente
* uso di un protocollo di comunicazione _agnostico_ come **HTTP**
    * applicativi _intelligenti_ e canali di comunicazione _stupidi_
    * interscambio dati come _messaggi_
* ciascuno può essere realizzato con linguaggi di programmazione differente
    * sono importanti le interfacce
* organizzati in modo vicino al _dominio business_ affrontato
* favoriscono la scalabilità

Le idee di microservizi provengono dal _Modello UNIX_ di processi indipendenti che comunicano tramite _pipes_. Il nome specifico compare intorno al 2005 come _Web-microservices_, maa si diffonde a partire dal 2012, soprattutto con articoli di _Martin Fowler_, famoso guru del mondo UML.

Un microservizio da solo non ha senso: devono comunicare tra loro in qualche forma di rete. 

Per quanto anche altre forme di _Inter Process Communication_ (IPC), come la memoria condivisa, siano fruibili, il principale modello di comunicazione vede canali basati sui **socket**, che possono essere locali (_Unix sockets_) o in Internet (_Inet sockets_).

La _filosofia_ vuole che un microservizio sia **elastico**, **resiliente**, **componibile**, **minimo** e **completo**. Viene espressa anche col motto:
> Fare una cosa sola, e farla bene.

I microservizi sono concettualmente l'opposto di grossi applicativi monolitici ospitati a bordo di un _application server_, quali sono tipici del moderno mondo _Java_. I microservizi hanno _bassa granularità_.

Non è facile però _progettare_ microservizi, poichè occorre un modo diverso di pensare rispetto allo standard moderno, con più enfasi su:
* latenza di comunicazione in rete
* concorrenza di accesso ai dati
* formato dei messaggi interscambiati
* _fault tolerance_
* bilanciamento del carico

La versione IT della ipotesi linguistica di _Sapir-Whorf_, secondo cui è la lingua usata che determina le categorie con cui il mondo viene percepito, implica che alcuni linguaggi di programmazione siano più adatti a _pensare_ i microservizi.

Un tale linguaggio, inventato all'Università di Bologna, è **Jolie**, ma non ha avuto molto successo.

Il maggior candidato corrente è il linguaggio **Go**, della Google, specialmente in congiunzione con il **Go Kit** (`https://gokit.io/`).
