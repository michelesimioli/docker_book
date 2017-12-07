# Port Publishing

## Publishing a porta casuale

Sulla macchina host del docker scoprire l'indirizzo `IPH`:
```
ifconfig
```
Il nostro indirizzo è 192.168.0.10 .

Lanciare un container con Port Publishing:
##### `docker run -d -p 8080 adejonge/helloworld`

Questo compie il _publish_ della porta 8080 del contenitore docker ad una porta casuale della macchina host.

Verificare con
#### `docker ps`
```
CONTAINER ID        IMAGE                 COMMAND             CREATED             STATUS              PORTS                     NAMES
b6eeec7fe2d8        adejonge/helloworld   "/helloworld"       31 seconds ago      Up 27 seconds       0.0.0.0:32768->8080/tcp   gifted_mcnulty

```
Anche con:

##### `docker port ID|name`
Nel mostro caso:
#### `docker port gifted_mcnulty`
```
8080/tcp -> 0.0.0.0:32768
```

La porta dello host che mappa al container è `PH`, nel nostro caso 32768.

Dalle altre macchine della rete, puntare il browser a: `http://IPH:PH/`.

Nel nostro caso:
#### `curl http://192.168.0.10:32768`
```
Hello World from Go in minimal Docker container
```
Il messaggio viene visualizzato.

## Publishing a porta data

Anzichè a porta casuale si può indicare a quale porta dello host effettuare il publishing.

Fermare e cancellare il contenitore precedente:
```
docker stop gifted_mcnulty
docker rm gifted_mcnulty
```

Lannciare un nuovo contenitore con:
##### `docker run -d -p 8000:8080 adejonge/helloworld`

Questo compie il publishing della porta 8080 del contenitore alla porta 8000 dello host.

Verificare:
#### `docker ps`
```
CONTAINER ID        IMAGE                 COMMAND             CREATED             STATUS              PORTS                    NAMES
f8b5fcbae348        adejonge/helloworld   "/helloworld"       7 seconds ago       Up 4 seconds        0.0.0.0:8000->8080/tcp   compassionate_wiles
```
#### `docker port compassionate_wiles` 
```
8080/tcp -> 0.0.0.0:8000
```

Testare la connettività da altre macchine della rete, connettendosi alla nostra porta `8080`, p.es. con un browser.
Dovrebbe essere visibile il messaggio di benvenuto.
