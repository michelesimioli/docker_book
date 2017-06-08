# Docker Compose

## Compose

![Compose](../gitbook/images/compose01.png)

E' uno strumento per definire ed eseguire applicativi consistenti in più containers collegati tra loro.

E' cioè uno strumento di _orchestrazione_ di applicativi complessi.

Tutto ciò che occorre è la stesura di un file di configurazione e l'esecuzione di un singolo comando.

Tre passi:
1. Scrivere uno o più `Dockerfile` (non necessario se le immagini sono già installate)
2. Scrivere il file di configurazione `docker-compose.yml`, con la sintassi Yaml
3. Eseguire il comando `docker-compose up`

Un esempio illustrativo di `docker-compose.yml` è il seguente:
```yaml
version: '2'

services:
  web:
    build: .
    ports:
     - "5000:5000"
    volumes:
     - .:/code
  redis:
    image: redis
```


Compose include comandi per:
* lo _start_, _stop_ e il _rebuild_ dei servizi
* esaminare lo stato dei servizi attivi
* ispezionare i log dei servizi attivi
* eseguire singoli comandi diretti ad un servizio

Docker-compose è scritto in Python.


### Installazione

Sul sito `https://github.com/docker/compose/releases` cercare la release per Linux e scaricarla nella propria directory `Downloads`.

Procedere poi come segue:
```
cd ~/Downloads
sudo cp docker-compose-Linux-x86_64 /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Testerlo con:
```
docker-compose version
```
