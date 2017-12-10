# Volumi Condivisi

## Volume Persistente

**Persistente**: esiste anche dopo la rimozione dei container che lo usano.

In una directory dedicata, costruiamo una procedura shell che scrive alla webdir di nginx:
```
mkdir ~/ex/webcounter
cd ~/ex/webcounter
vim webcounter.sh
```
E inseriamo il codice:
```bash
#! /bin/bash
x=0
while true
do
  x=$[x + 1]
  echo "<br/><h1>$x</h1>" > /usr/share/nginx/html/index.html
  sleep 1 
done
```

Questa procedura incrementa un contatore ogni secondo e ne scrive il valore, formattato come _Heading 1_ di HTML, nel file di default delle pagine statiche gestite da _nginx_.

Rendiamola eseguibile:
```
chmod +x webcounter.sh
```
Costruiamo ora il `Dockerfile` in una directory dedicata:
```
vim Dockerfile
```
```dockerfile
FROM ubuntu
MAINTAINER Michele Simioli <michele@simioli.it>
COPY webcounter.sh /usr/local/bin/webcounter.sh
RUN chmod +x /usr/local/bin/webcounter.sh
CMD ["/usr/local/bin/webcounter.sh"]
```

Questo file esegue le operazioni:
* `COPY webcounter.sh /usr/local/bin/webcounter.sh`
  * copia un file dalla directory locale all'immagine in costruzione
* `CMD ["/usr/local/bin/webcounter.sh"]`
  * determina che al lancio del contenitor verrà eseguita la procedura shell stabilita

Costruiamo ora l'immagine:
#### `docker build -t my_webapp1 .`

Creare un volume per dati condivisi:

#### `docker create --name my_data1 -v /usr/share/nginx/html ubuntu`

Questo comando genera un **data container**, non un **process container**.
L'opzione `-v /usr/share/nginx/html` determina la directory corrispondente al volume, e mancano gli argomenti finali che indicano il processo da lanciare.

E' sempre opportuno dare un nome assegnato ai _data containers_.

Verifichiamo:
#### `docker ps -a`

Il volume è creato esternamente ai container che lo usano. E' esso stesso un contenitore, ma non è attivo. Per questo ci vuole `docker ps -a` e non solo `docker ps`.

Lanciare il web server e la web app montando il volume condiviso coi dati:
 
#### `docker run -d --name web_server --volumes-from my_data1 nginx`

#### `docker run -d --name web_app --volumes-from my_data1 my_webapp1`

In questo esercizio il container _web\_app_ è il **produttore** dei dati, il container _web\_server_ è il **consumatore** dei dati, e il container _my\_data_ è il **contenitore** dei dati.

Verifichiamone l'avvenuta attivazione con: `docker ps`

Verifichiamo quale sia l'indirizzo IP assegnato al _web\_server_:
`docker inspect web_server | grep IP`

Nel nostro caso IP: 172.17.0.2

Entrambi i container usano il volume creato esternamente, lo si può verificare sempre con `docker inspect contenitore`.

Per testare il funzionamento, collegarsi col browser all'IP indicato
```
curl 172.17.0.2
```
Ricollegarsi o rinfrescare più volte il browser. Il numero continua a venir aggiornato.

### Sopravvivenza dei dati

Fermare e cancellare il container `web_server`.

Rilanciare un altro e differente contenitore `web_server`:

#### `docker run -d --name web_server --volumes-from my_data1 nginx`

Rivedere col browser:
```
curl 172.17.0.2
```
Funziona ancora. Il volume dati è indipendente dal container del web server, è gestito dal container del web app.

Naturalmente se fermiamo `web_app` e la facciamo ripartire, o se la rimuoviamo e la rigeneriamo, il contatore riprende dall'inizio.

E' possibile metterla in pausa e far riprendere poi le operazioni:

#### `docker pause web_app`

#### `docker unpause web_app`

Al termine dell'esercizio fermare e rimuovere tutti i containers:
#### `docker rm -f my_data1 web_app web_server`

## Mappaggio a Directory Locale

Il container di dati è delicato. Se lo si rimuove, o se avviene un crash di sistema, i dati sono persi.

Meglio mapparlo ad una directory locale:

#### `docker create --name my_data1 -v /home/mich/ex/static:/usr/share/nginx/html ubuntu`

I mapping locale e remoto del volume devono essere percorsi assoluti. La directory `/home/mich/ex/static` deve venir creata prima.

Lanciamo gli altri contenitori come prima:

#### `docker run -d --name web_server --volumes-from my_data1 nginx`

#### `docker run -d --name web_app --volumes-from my_data1 my_webapp1`
Verifichiamo che il server _nginx_ funziona:
```
curl 172.17.0.2
```

Verifichiamo che sta aggiornando la directory locale:
```
cat ~/ex/static/index.htm
```

Se ora rimuoviamo tutti i contenitori:
#### `docker rm -f my_data1 web_app web_server`

i dati nella directory locale continuano ad esistere:
```
cat ~/ex/static/index.htm
```
## Volume Effimero

**Effimero**: scompare quando i container che lo usano sono terminati

Container _one_ che crea un volume:
#### `docker run -ti --rm --name one -v /shared_data ubuntu bash`

Container _two_ che usa un volume:
#### `docker run -ti --rm --name two --volumes-from one ubuntu bash`

Notare l'aggancio diretto al nome del container: `--volumes-from one`. Tutti i volumi definiti da _one_ vengono visti anche da _two_.

Su _two_ creiamo un file nella directory condivisa:
```
touch /shared_data/example
```

Su _one_:
```
ls /shared_data
```

E' visibile `example`.

Terminare _one_. Su _two_ è ancora visibile `example`.
Il volume viene mantenuto dal server docker.

Se rilanciamo _one_ con la stessa condivisione dati, _one_ non vede più `example`: sono istanze diverse di volume condiviso. _two_ continua a vederlo, ma non è lo stesso volume del nuovo _one_: hanno identificativi diversi.

Al termine dell'esercizio ripulire tutti i containers.
