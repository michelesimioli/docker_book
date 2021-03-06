# Primo Esempio

**Passo 1.**

Preparare la procedura shell `~ex/forever.sh`:

```bash
#! /bin/bash
x=0
while true
do
  x=$[x+1]
  echo $x
  sleep 1
done
```

Questa procedura conta all'infinito, un numero al secondo.

**Passo 2.**

Eseguirla in docker:

##### `docker run -d centos /bin/bash -c "$(cat ~ex/forever.sh)"`

Tutti i sottocomandi docker sono prefissi col comando `docker`.

Questo sottocomando, `run` , lancia un contenitore, in modalità _demone_ con l'opzione `-d` .

Il contenitore è generato dall'immagine `centos` .

Dato che tale immagine sul nostro computer ancora non esiste, docker si collega al **Docker Hub** in rete e ne scarica tutti i componenti; può impiegare qualche minuto.

Il contenitore riceve un identificativo`ID` casuale, derivato da uno hash dell'immagine. Viene visualizzato a video tale ID quando il contenitore parte.

Al contenitore sono passati i parametri `/bin/bash -c "$(cat ~ex/forever.sh)"` ovvero viene lanciata una shell a cui passiamo la nostra procedura.

Non viene al momento visualizzato altro.

**Passo 3.**

Per verificare che il nostro contenitore è in funzione:

##### `docker logs ID`

dove `ID` è lo hash identificativo del contenitore.

Il contenitore non è connesso ad un terminale, quindi non può scrivere a standard output. Invia i suoi dati al file di log di docker.

**Passo 4.**

Fermare il contenitore:

##### `docker stop ID`

Verificare su log che ha smesso di emettere nuovi numeri.

**Passo 5.**

Il contenitore è fermo, ma non distrutto. Farlo ripartire con:

##### `docker start ID`

e verificare il log. Quando soddisfatti che gira, fermarlo di nuovo.

Notare che la procedura è ripartita dall'inizio. Fermare un contenitore non mantiene lo stato.

**Passo 6.**

Rimuovere il contenitore:

##### `docker rm ID`

Provare a farlo partire di nuovo. Verificare anche il log.

#### Domanda

Perchè il seguente non funziona?

##### `docker run -d centos /bin/bash forever.sh`

Perchè in questo semplice esempio il contenitore e il sistema operativo ospitante sono separati. Il contenitore non vede il file `forever.sh` , perchè questo si trova nel file system dell'ospitante.


### Mettere in Pausa

Se fermare un contenitore e riavviarlo lo resetta all'inizio, mettere in pausa un contenitore poi toglierlo dalla pausa lo fa continuare dal punto in cui era arrivato.

##### `docker pause ID`

##### `docker unpause ID`

