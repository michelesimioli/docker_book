## Volumi Condivisi

Costruiamo un'immagine che scrive alla webdir di nginx
```
mkdir ~/webcounter
cd ~/webcounter
vi webcounter.sh
```
```bash
#! /bin/bash
x=0
while true
do
  x=$[x + 1]
  echo "<br/><h1>$x</h1>" > /var/www/html/index.html
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

Il volume è creato esternamente ai container che lo usano.

Lanciare il web server e la web app montando il volume condiviso coi dati:
``` 
docker run -d --name web_server --volumes-from my_data1 nginx

docker run -d --name web_app --volumes-from my_data1 my_webapp1

docker ps

docker inspect web_server | grep IP
```
Nel nostro caso IP: 172.17.0.3

Entrambi i container usano il volume creato esternamente.
Collegarsi col browser all'IP indicato
```
curl 172.17.0.3
```
Ricollegarsi o rinfrescare più volte il browser.

Fermare e cancellare il container `web_server`.

Rilanciare un altro e differente contenitore `web_server`:
```
docker run -d --name web_server --volumes-from my_data1 nginx
```
Rivedere col browser:
```
curl 172.17.0.3
```
Funziona sempre. Il volume dati è indipendente dal container del web server, è gestito dal container del web app.


Al termine dell'esercizio fermare e rimuovere tutti i containers.

