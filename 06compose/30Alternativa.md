# Alternative a Docker Compose

Docker Compose non è l'unico strumento di orchestrazione per Docker.

## Crane

Crane è un'alternativa, scritta in Go, mantenuta da Michael Sauter e disponibile su: `https://github.com/michaelsauter/crane`.

Per installarlo:
```
cd $GOPATH/src
git clone https://github.com/michaelsauter/crane.git
cd crane
go get github.com/michaelsauter/crane
make build-linux-amd64
```
e ci ritroveremo `crane` nella directory `$GOBIN`.

Test: `crane` da una videata d'aiuto.

## Rocker Compose

Un'altra iniziativa alternativa, di interesse crescente, è **Rocker Compose**, con lo stesso funzionamento di _Docker Compose_, ma scritto in Go.

E' disponibile alla URL: `https://github.com/grammarly/rocker-compose`.
