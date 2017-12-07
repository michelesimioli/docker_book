# Docker Compose

## Compose

![Compose](../gitbook/images/compose01.png)

E' uno strumento per definire ed eseguire applicativi consistenti in più containers collegati tra loro.

E' cioè uno strumento di _orchestrazione_ di applicativi complessi.

Tutto ciò che occorre è la stesura di un file di configurazione e l'esecuzione di un singolo comando.

Tre passi:
1. Scrivere uno o più `Dockerfile` (non necessario se le immagini sono già installate)
2. Scrivere il file di configurazione `docker-compose.yml`, con la sintassi Yaml - il file deve chiamarsi così
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

Docker-compose non è parte di _Docker_, ma è una utility opzionale, installabile separatamente. Non è mantenuto dalla stessa squadra di sviluppo, e può avere dei problemi non connessi direttamente con _Docker_:

Docker-compose è scritto in Python, non in Go.

## Installazione di Docker Compose in Linux

Sul sito `https://github.com/docker/compose/releases` cercare la release per Linux e scaricarla nella propria directory `Downloads`.

Procedere poi come segue:
```
cd ~/Downloads
sudo cp docker-compose-Linux-x86_64 /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Testarlo con:
```
docker-compose version
```

## Installazione di Docker Compose in Window su Docker CE

Le versioni:
* Docker CE for Windows
* Docker Toolbox

hanno già installato Docker Compose

## Installazione di Docker Compose in Windows su Docker EE

Decidere quale versione si intende installare. Le versioni sono allineate a quelle del **Docker Engine** installato in precedenza.

In una Powershell 'elevata', cioè come _Administrator_, per installare p.es. la versione 1.17.0, dare il comando:
```
Invoke-WebRequest "https://github.com/docker/compose/releases/download/1.17.0/docker-compose-Windows-x86_64.exe" -UseBasicParsing -OutFile $Env:ProgramFiles\docker\docker-compose.exe
```

Viene scaricato un eseguibile. Lanciarlo per installare _Docker Compose_.
