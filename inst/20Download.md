## Download e Setup

### Installazione

#### Installazione da Repository

Installazione di Docker su Ubuntu 16.04 LTS. Esistono altre procedure per altre varianti di Ubuntu e altre distribuzioni Linux.

L'installazione sarà della versione **Docker Community Edition** (_docker-ce_), dal repository di pacchetti Debian del sito ufficiale di Docker.

Assicurarsi di avere i pacchetti necessari sul proprio sistema:
```
sudo apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     software-properties-common
```

Aggiungere la chiave GPG ufficiale di Docker:
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Verificare il fingerprint della chiave:
```
sudo apt-key fingerprint 0EBFCD88
```
Il risultato deve essere:
```
9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
```

Aggiungere il repository:
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
Il comando `lsb_release -cs` ritorna il nome in codice corto della distribuzione corrente, nel nostro caso `xenial`.

Aggiornare la lista dei repositories:
```
sudo apt-get update
```

Controllare la disponibilità di `docker-ce`:
```
apt-cache madison docker-ce
```

Si può installare una versione specifica col comando:
```
sudo apt-get install docker-ce=<VERSION>
```

Noi installeremo la versione più recente:
```
sudo apt-get install docker-ce
```

#### Installazione da Package Debian

Accedere al sito: `https://download.docker.com/linux/ubuntu/dists/xenial/pool/stable/amd64/`.

Scaricare il file `xxx.deb` desiderato.

Installare col comando:
```
sudo dpkg -i xxx.deb
```
Attenzione: questo è un comando a basso livello e non risolve eventuali dipendenze incontrate.

E' di gran lunga preferibile l'installazione da Repository.

### Aggiunta utente autorizzato

Per poter comunicare col server docker, cioè poter dare quasi i comandi, è necessario che un utente appartenga al gruppo `docker`.

Per aggiungere l'utente _mich_ a tale gruppo:
```
sudo usermod -aG docker mich
```
Non scordarsi mai l'opzione `-a`.

E' necessario un relogin di _pippo_ per appartenere a tale gruppo.

---
# Attenzione

Far appartenere al gruppo `docker` un utente non amministratore è un **GROSSO** baco di sicurezza, poichè permetta a tale utente, con l'aiuto di Docker, una **scalata di privilegi**.

---

### Verifica versione

Il comando:
##### docker version
```
Client:
 Version:      17.03.1-ce
 API version:  1.27
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Mon Mar 27 17:14:09 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.03.1-ce
 API version:  1.27 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Mon Mar 27 17:14:09 2017
 OS/Arch:      linux/amd64
 Experimental: false
 ```
 
 da un rapporto sulla versione sia del client che del server, e implica che il server è attivo.

 #### Ulteriore Test

 Viene fornito un container di default per il testing. Dare il comando:
```
docker run hello-world
```
Il risultato è qualcosa come:
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
78445dd45222: Pull complete 
Digest: sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12a1ac8d7
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```
Occorre ora fare pulizia:
```
docker ps -a
```
Identificare lo ID del container - il grosso numero esadecimale `CONTAINER_ID` all'inizio del rapporto. Rimuovere il container:
```
docker rm CONTAINER_ID
```

 ### Gestione del Server

Dipende dalla variante di Linux usata.

Su sistemi basati su init/upstart (RedHat/Centos 6, Ubuntu 14.04 LTS):
```
sudo service docker status|start|stop|restart
```

Su sistemi basati su systemctl (RedHat/CentOS 7, Ubuntu 16.06 LTS:
```
sudo systemctl status|start|stop|restart docker
```

In Ubuntu 16.04 LTS, dopo l'installazione, docker è attivo e abilitato di default.

Per decidere se il servizio docker parte/non parte al prossimo boot:
```
sudo systemctl enable|disable docker
```

### Upgrade di Docker

Per l'upgrade all'ultima versione basta riaggiornare la lista dei pacchetti dai repositories, poi semplicemente installare. Non c'è più bisogno di preoccuparsi della chiave GPG.

```
sudo apt-get update

sudo apt-get install docker-ce
```
Lo upgrade non tocca le immagini esistenti o altre configurazioni.

Prima dell'upgrade assicurarsi che tutti i contenitori siano disattivati.

### Rimozione di Docker

Rimozione del pacchetto:
```
sudo apt-get purge docker-ce
```

Rimozione delle immagini e contenitori:
```
sudo rm -rf /var/lib/docker
```
