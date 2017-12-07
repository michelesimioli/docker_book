# Docker e Go

Esempio complesso di microservizi.

L'applicativo **cinema** simula un cinema virtuale che fornisce la possibilità di:
* **Movie Service** - informazioni sui film
* **Show Time Service** - orario delle proiezioni
* **Booking Service** - prenotazioni
* **User Service** - informazioni sugli utenti del cinema

L'applicativo è composto da 4 microservizi, scritti in **Go**, usa un database **MongoDB** ed è integrato da **Docker**.

E' disponibile su:
`https://github.com/mmorejon/microservices-docker-go-mongodb`

Richiede:
* Docker
* Docker Compose
* MongoDB - fornito da Docker
* Go - Fornito da Docker

Si basa su **Virtual Domains** e richiede che il file `/etc/hosts` venga modificato come segue:
```
127.0.0.1   movies.local bookings.local users.local showtimes.local
```

## Interfacce

Usa interfacce **HTTP REST**.

### User Service

* GET - http://users.local/users - lista gli utenti
* POST - http://users.local/users - crea un utente
* DELETE - http://users.local/users/{id} - rimuove un utente

### Movie Service

Ogni film ha il titolo, una valutazione da 1 a 10, il regista e altre informazioni.

* GET - http://movies.local/movies - lista dei film
* POST - http://movies.local/movies - crea un film
* GET - http://movies.local/movies/{id} - cerca un film tramite `id`
* DELETE - http://movies.local/movies/{id} - rimuove un film tramite `id`

### Showtime Service

* GET - http://showtimes.local/showtimes - lista gli showtimes
* POST - http://showtimes.local/showtimes - crea uno showtime
* GET - http://showtimes.local/showtimes/{id} - cerca uno showtime tramite `id`
* DELETE - http://showtimes.local/showtimes/{id} - rimuove uno showtime tramite `id`

### Booking Service

* GET - http://bookings.local/bookings - mostra tuttele prenotazioni
* POST - http://bookings.local/bookings - crea una prenotazione

## Gestione dell'applicativo

Generare e lanciare i servizi:
```
docker-compose up -d
```
Fermare i servizi:
```
docker-compose stop
```
Ricostruzione e redeploy dopo cambiamenti al codice:
```
docker-compose build
```


## Il file di descrizione `docker-compose`

_(docker-compose.yml)_

```yml
version: '2'
services:
  proxy:
    image: jwilder/nginx-proxy
    container_name: cinema-nginx-proxy
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
    ports:
      - "80:80"

  movies:
    build: ./movies
    image: cinema/movies
    container_name: cinema-movies
    depends_on:
      - db
      - proxy
    links:
      - db
    environment:
      VIRTUAL_HOST: movies.local

  bookings:
    build: ./bookings
    image: cinema/bookings
    container_name: cinema-bookings
    depends_on:
      - db
      - proxy
    links:
      - db
    environment:
      VIRTUAL_HOST: bookings.local

  showtimes:
    build: ./showtimes
    image: cinema/showtimes
    container_name: cinema-showtimes
    depends_on:
      - db
      - proxy
    links:
      - db
    environment:
      VIRTUAL_HOST: showtimes.local

  users:
    build: ./users
    image: cinema/users
    container_name: cinema-users
    depends_on:
      - db
      - proxy
    links:
      - db
    environment:
      VIRTUAL_HOST: users.local

  db:
    image: mongo:3.3
    container_name: cinema-db
    ports:
      - "27017:27017"
    volumes:
- ./backup:/backup:rw
```
