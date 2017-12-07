# Prima Immagine

## Dockerfile

Un Dockerfile è un file di specifiche per la creazione di un'immagine Docker.

Il file deve chiamarsi `Dockerfile` e deve essere in una **directory di contesto**: vedrà tutti i files al di sotto di essa.
Non usare mai `/` come directory di contesto.

Il file è in formato ASCII (veramente UTF-8) e contiene una serie di direttive come parole chiave. Le direttive non sarebbero _case-sensitive_, ma vengono convenzionalmente poste in maiuscolo.

### Esempio: Dockerfile per `forever.sh`

Occorre creare la Dockerfile.

Vogliamo un'immagine per il programma con contatore infinito:
```
cd ~/ex
cat forever.sh
```
```bash
#! /bin/bash
x=0 
while true
do
  x=$[x + 1] 
  echo $x 
  sleep 1 
done
```
Editare la Dockerfile:
```
cd ~/ex
vim Dockerfile
```

Inserendo:
```
FROM ubuntu
MAINTAINER Michele Simioli <michele@simioli.it>
COPY forever.sh /usr/local/bin/forever.sh
RUN chmod +x /usr/local/bin/forever.sh
CMD ["/usr/local/bin/forever.sh"]
```

### Costruzione dell'Immagine

Costruire l'immagine:

#### `docker build -t forever1 .`

Non dimenticarsi il `.` (punto - la directory corrente).
* `-t forever1` : nome che diamo all'immagine
* `.` : directory di build

Verificare e lanciare la nova immagine:
#### `docker images`

#### `docker run -d --name my_forever1 forever1`

#### `docker logs my_forever1`

Al termine fermare e rimuovere il comtenitore.
