## Gestione Contenitori

### Lancio di un contenitore

##### `docker run -it ubuntu:latest /bin/bash`
Lancia un contenitore nuovo basato sull'immagine `ubuntu:latest` e al suo interno esegue il comando `/bin/bash`.

Le opzioni sono:
* `-i` - interattivo
* `-t` - apre una sessione di terminale

Ci troviamo collegati al contenitore in una sessione di terminale con una shell di root.
Un intero sistema operativo senza macchina virtuale?

Il pronto, dello stile `username@hostname` indica che il nome della macchina corrisponde al suo ID di container.

Si possono dare comandi CLI (Command Line Interface) e vengono eseguiti.
Per esempio provare:
```
ps lax
```
Notare che non è un intero sistema operativo, solo una shell in ambiente Ubuntu.

Non uscire col comando `exit`, o si ferma il contenitore. Dare invece la sequenza di controllo `Ctrl-P Ctrl-Q`. Torniamo alla shell del sistema ospitante ma il contenitore è ancora attivo.

### Lista dei contenitori

Il comando:
##### `docker ps`
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
9ec3369900cd        ubuntu:latest       "/bin/bash"         4 minutes ago       Up 4 minutes                            sleepy_kirch
```

lista i containers attivi.

Il container ha ricevuto un nome d'ufficio, in questo caso _sleepy\_kirk_ (sono tutti nomi di fantasia del formato _attributo\_nome).
Si può controllare il container sia col suo ID che col suo nome.

Il comando:
##### `docker ps -a`
lista tutti i contenitori, anche quelli non attivi. Nel nostro caso le due liste coincidono.

Per riaprire una connessione di terminale al container:
##### `docker exec -it sleepy_kirch bash`
e ci troviamo di nuovo dentro la shell.

Notare col comando `ps lax` che stiamo eseguendo una shell diversa da quella precedente.

Provare a creare un file con un po' di contenuti:
`echo Buongiorno > file1`

Dare il comando `exit`.
Verificare con `docher ps` che il containerè ancora attivo, perchè la shell originale c'è ancora.

### Controllo di container

Il comando:
##### `docker stop sleepy_kirch`
ferma il container. Verificare con `docker ps -a`.

Inoltre il processo con PID 1 del container riceve il segnale SIGTERM, e volendo lo può intercettare e salvare il suo stato o compiere altre operazioni. Se tale processo non termina di propria volontà, entro pochi secondi riceve il segnale SIGKILL che lo termina d'autorità.

Il comando:
##### `docker start sleepy_kirch`
riavvia il container. Verificare con `docker ps`.

Ci si può riconnettere con `docker exec -it sleepy_kirch bash`.
Verificare che `file1` esiste ancora:
`cat file1`.

I contenitori conservano il file system anche quando sono in modo stop.
Notare col comando `history` che è stata mantenuta anche la storia dei comandi.

### Rimozione di un contenitore

Uscire dalla shell del container con `exit`.

Rimuovere il contenitore:
##### `docker rm sleepy_kirch`
```
Error response from daemon: You cannot remove a running container 9ec3369900cde6283e1e96dd82ea976933ed29378ff24160daf7d1406ca4c303. Stop the container before attempting removal or use -f
```

E' un errore rimuovere un contenitore attivo: occorre fermarlo, poi rimuoverlo.

Un contenitore attivo si può anche rimuovere con:
##### `docker rm -f sleepy_kirch`

Questo invial al PID 1 del contenitore subito un segnale SIGKILL, non dandogli l'opportunità di salvare lo stato, poi rimuove il contenitore.

All'atto della rimozione, evidentemente, tutti i dati del file system del contenitore sono persi comunque. Ma vedremo poi che è possibile appoggiare directories del file system del contenitore a directories del file system ospitante. In tal caso un SIGKILL non compie il flush dei buffer di I/O e si possono verificare perdite dei dati.

Inoltre in seguito vedremo che un contenitore di un applicativo complesso è in relazione con altri contenitori, e in tal caso un _graceful shutdown_ è d'uopo.

Sempre dare `docker stop ID` prima di `docker rm ID`.

### Contenitore detached

Lanciare:
##### `docker run -d -ti --name cont alpine`

Le ozioni sono:
* `-d` - lancia il contenitore in modalità _detached_
* `--name cont` - dà il nome _cont_ al contenitore, anzichè il nome di fantasia inventato d'ufficio

Verificare con `docker ps` che il contenitore è attivo.

Attaccare un processo al contenitore:
##### `docher attach cont`

Ci si aggancia al processo di PID 1 del contenitore. Quale sia questo processo di pende da come è stata costruita l'immagine, per alpine è la shell `sh`.

Alpine è una versione di Linux molto più smilza di Ubuntu.

Vale lo stesso comportamento di prima:
* `exit` esce dalla shell di alpine e ferma il container
* `Ctrl-P Ctrl-Q` esce dalla shell di alpine e non termina il container

