## Volumi Condivisi

### Volume Persistente

**Persistente**: esiste anche dopo la rimozione dei container che lo usano.

Costruiamo un'immagine che scrive alla webdir di nginx
```
mkdir ~/ex/webcounter
cd ~/ex/webcounter
vi webcounter.sh
```
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
```
chmod +x webcounter.sh
vi Dockerfile
```
```dockerfile
FROM ubuntu
MAINTAINER Michele Simioli <michele@simioli.it>
COPY webcounter.sh /usr/local/bin/webcounter.sh
RUN chmod +x /usr/local/bin/webcounter.sh
CMD ["/usr/local/bin/webcounter.sh"]
```
Costruiamo l'immagine:
```
docker build -t my_webapp1 .
```

Creare un volume per dati condivisi:
```
docker create --name my_data1 -v /usr/share/nginx/html ubuntu

docker ps -a
```

Il volume è creato esternamente ai container che lo usano. E' esso stesso un contenitore, ma non è attivo. Per questo ci vuole `docker ps -a` e non solo `docker ps`.

Lanciare il web server e la web app montando il volume condiviso coi dati:
``` 
docker run -d --name web_server --volumes-from my_data1 nginx

docker run -d --name web_app --volumes-from my_data1 my_webapp1

docker ps

docker inspect web_server | grep IP
```
Nel nostro caso IP: 172.17.0.2

Entrambi i container usano il volume creato esternamente.
Collegarsi col browser all'IP indicato
```
curl 172.17.0.2
```
Ricollegarsi o rinfrescare più volte il browser.

Fermare e cancellare il container `web_server`.

Rilanciare un altro e differente contenitore `web_server`:
```
docker run -d --name web_server --volumes-from my_data1 nginx
```
Rivedere col browser:
```
curl 172.17.0.2
```
Funziona ancora. Il volume dati è indipendente dal container del web server, è gestito dal container del web app.

Naturalmente se fermiamo `web_app` e la facciamo ripartire, o se la rimuoviamo e la rigeneriamo, il contatore riprende dall'inizio.

E' possibile metterla in pausa e far riprendere poi le operazioni:
```
docker pause web_app

docker unpause web_app
```

Al termine dell'esercizio fermare e rimuovere tutti i containers:
```
docker ps -a| grep -v CON | grep -v Creat | cut -d\  -f1 | xargs docker stop

docker ps -a| grep -v CON |  cut -d\  -f1 | xargs docker rm
```

#### Mappaggio a Directory Locale

Il container di dati è delicato. Se lo si rimuove, o se avviene un crash di sistema, i dati sono persi.

Meglio mapparlo ad una directory locale:

```
docker create --name my_data1 -v /home/mich/ex/static:/usr/share/nginx/html ubuntu
```
I mapping locale e remoto del volume devono essere percorsi assoluti.

Lanciamo gli altri contenitori come prima:
```
docker run -d --name web_server --volumes-from my_data1 nginx

docker run -d --name web_app --volumes-from my_data1 my_webapp1

curl 172.17.0.2
```

Verifichiamo che sta aggiornando la directory locale:
```
cat ~/ex/static/index.htm
```

Se ora rimuoviamo tutti i contenitori:
```
docker ps -a| grep -v CON |  cut -d\  -f1 | xargs docker rm -f
```
i dati nella directory locale continuano ad esistere:
```
cat ~/ex/static/index.htm
```
### Volume Effimero

**Effimero**: scompare quando i container che lo usano sono terminati

Container che crea un volume:
```
docker run -ti --rm --name one -v /shared_data ubuntu bash
```

Container che usa un volume:
```
docker run -ti --rm --name two --volumes-from one ubuntu bash
```

Su _two_:
```
touch /shared_data/example
```

Su _one_:
```
ls /shared_data
```

E' visibile `example`.

Terminare _one_. Su _two_ è ancora visibile `example`.
Il volume mantenuto dal server docker.

Se rilanciamo _one_ con la stessa condivisione dati, _one_ non vede più `example`: sono istanze diverse di volume condiviso. _two_ continua a vederlo, ma non è lo stesso volume del nuovo _one_: hanno identificativi diversi.

Al termine dell'esercizio ripulire tutti i containers.
