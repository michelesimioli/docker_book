## Updates

Idealmente:
* le immagini di base sono derivate da repositories ufficiali di fiducia
* le immagini dipendenti, che richiedono quelle base, hanno un `dockerfile` di specifica
* tutte le immagini derivate sono conservate su un repository locale

### Passi per l'Update

* identificare le immagini che richiedono update, sia dipendenti che di base
* compiere l'update di ogni immagine base e compiere il push al repository
* per ricostruire le immagini dipendenti compiere il `docker build` con l'opzione `--no-cache`; compiere poi il loro push al repository
* su ogni host che usa le immagini compiere l'opportuno `docker pull`
* restart dei contenitori
    * attenzione a non perdere volumi di dati
    * programmare il downtime
* quando soddisfatti, rimuovere le vecchie immagini dagli host e dai contenitori

Procedere sempre in modo che sia possibile un _backdown_ cioè un ritorno alla situazione precedente.

**Avvedutezza**

Operare su una macchina Linux in cui sia stato installato il **Logical Volume Manager** o qualche altro ambiente o file system che consenta gli **snapshot**. Compiere uno _snapshot_ prima dell'update.

---

### Lista delle Immagini

```
docker inspect -f "{{.Image}}" $(docker ps -q)
```

```
docker images --no-trunc | grep \
 $(docker inspect -f "-e {{.Image}}" $(docker ps -q))
```
Lista di tutte le immagini incluso le immagini base e intermedie:
```
docker inspect -f "{{.Image}}" $(docker ps -q) | \
    xargs -L 1 docker history -q
```
Con informazioni sulle immagini:
```
docker images | grep \
  $(docker inspect -f "{{.Image}}" $(docker ps -q) | \
  xargs -L 1 docker history -q | sed "s/^/\-e /")
```
Aggiungere `-a` per dettagli sulle immagini intermedie.

**Nota**

E' possibile etichettare le immagini in un `dockerfile`:
```
FROM debian
LABEL version 1.0
LABEL description "Test image for labels"
```
Con un minimo di _templating_ o anche di _programmazione shell_ si può aggiungere al _dockerfile_ lo hash Git dei sorgenti da cui è costruita l'immagine, e infatti qualsiasi altro dettaglio possa essere potenzialmente utile.

---

### Update del Docker Engine

Occorre prevedere downtime per l'upgrade dell'intero sistema Docker, poichè:
* vengono introdotte nuove features
* vengono cambiati dei drivers di base
* sono introdotte patch di sicurezza

Non si può mantenere indefinitamente una versione data del Docker Engine, poichè anche le immagini più recenti dei repositories possono diventare incompatibili.

Al veloce ritmo di cambiamento dell'ecosistema Docker è opportuno prevedere la possibilità di upgrade a scadenza annuale.

Le considerazioni sono molto simili a quelle dell'upgrade di un Kernel Linux. Occorre:
* (compiere uno snapshot e/o un backup)
* fermare tutti i contenitori, accertandosi di non perdere i volumi di dati (**spin down**)
* compiere l'upgrade delDocker Engine
* ristartare i contenitori (**spin up**)

Può essere una buona idea avere una replica completa dello host e di tutti i suoi contenitori prima dell'upgrade.

