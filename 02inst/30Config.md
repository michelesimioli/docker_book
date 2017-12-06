# Configurazione di Docker

## Aiuto di Docker

Per avere un aiuto generico il comando è `docker help`, o più semplicemente `docker`.

Vi sono una serie di comandi **diretti** con la sintassi:

##### `docker comando [opzioni] argomenti`

e un'altra serie di comandi di **management** di un certo numero di _sottosistemi_, con la sintassi:

##### `docker sottosistema comando [opzioni] argomenti

Aiuto più specifico si può ottenere con:
```
docker help comando
docker help sottosistema
docker sottosistema help comando
```

Alcuni comandi sono molto più usati di altri. Così come l'apprendimento di una lingua non si fà imparando a memoria il dizionario, l'apprendimento di Docker è guidato da specifici argomenti e casi. Comunque una visione dall'alto è molto utile.

I comandi sono listati in ordine alfabetico, e non è veramente il modo giusto per raggrupparli. Meglio:

* `docker pull` - download di un'immagine da un registry
* `docker push` - upload di un'immagine ad un registry, se si hanno i dovuti permessi
* `docker search` - ricercare un'immagine in un registry
* `docker images` - listare le immagini locali
* `docker rmi` - rimuovere immagini

* `docker run` - lancio di un contenitore basato su un'immagine
* `docker stop` - fermare un contenitore
* `docker start` - far partire un contenitore da capo
* `docker pause` - mettere in pausa un contenitore
* `docker unpause` - togliere dalla pausa un contenitore
* `docker rm` - rimuovere un contenitore
* `docker exec` - eseguire un comando in un contenitore
* `docker attach` - connettersi ad un contenitore

Vi sono i seguenti sottosistemi:
* `image`
* `container`
* `network`
* `volume`
* `system`
* `stack`
* `plugin`
* `secret`
* `node`
* `swarm`
* `service`
