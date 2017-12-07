# Primo Compose

Creare la directory per gli esercizi:
```
mkdir -p ~/ex/compose/hello
cd ~/ex/compose/hello
vi docker-compose.yml
```
```yml
version: '2'
services:
  hello:
    image: tutum/hello-world
    ports:
      - 80

```
Le indentazioni devono essere con **spazi**, non con **TAB**.

Eseguirlo con:
#### `docker-compose up -d`

L'opzione `-d` indica che il container generato deve essere _detached_.

Il comando scarica l'immagine `tutum/hello-world` dal Docker Hub se non già presente e fa partire un contenitore di questa immagine, con il _port publishing_ della porta 80 del container ad una porta casuale dello host.

Verificare con: `docker ps` la porta casuale adottata.

Provare il collegamento con un browser o con, per esempio:
```
curl localhost:32768
```

Al termine dell'esercizio fermare e rimuovere il contenitore, poi rimuovere l'immagine; oppure dare il comando:

#### `docker-compose down`

Occorre essere posizionati nella directory del progetto compose.

Bisogna porre attenzione: il comando `docker-compose down` è pericoloso, poichè rimuove tutto.
Se vi sono volumi condivisi definiti nel file di configurazione, anch'essi vengono rimossi.

