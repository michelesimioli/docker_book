# Gestione Immagini

## Registry

Un Registry è una URL, tipicamente con protocollo `https`, alla quale docker si collega per scaricare immagini, con APi definite.
Il registry di default è: `https://registry.hub.docker.com/`, o `https//hub.docker.com/`, detto Docker Hub.

Vi sono in rete altri registries, p.es.:
* Quay (`https://quay.io/`)
* Google Container Registry (`https://cloud.google.com/container-registry/`)
* AWS Container Registry (`https://aws.amazon.com/ecr/`)

E' possibile collegarsi liberamente (al momento) a Docker Hub, navigare per avere informazioni sulle immagini disponibili, e scaricare le immagini.

Il collegamento ad altri registries può prevedere una sottoscrizione a pagamento, e permette l'upload di immagini prodotte localmente.

E' anche possibile configurare e gestire un proprio registry:
* compilando i sorgenti - in linguaggio Go - disponibili su `https://github.com/docker/distribution`
    * usare solo la versione 2 con docker 1.6+
* scaricando un'immagine docker (`docker pull registry`) e leggendo il manuale (`https://docs.docker.com/registry/`) per una corretta gestione

## Repository

E' una collezione di immagini all'interno di un registry, tutte della stessa tipologia, ma con tag (versioni) diversi.

### Lista immagini

Lista delle immagini installate:
##### `docker images`
```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              48b5124b2768        4 months ago        1.84 kB
```

Versione ridotta:
##### `docker images -q`
Lista solo gli ID delle immagini.

### Ricerca immagini

Ricerca di un'immagine nel Docker Hub:
##### `docker search alpine`

Produce un (lungo) listato di tutte le immagini disponibili.

### Download di immagine

Scaricare un'immagine:
##### `docker pull alpine`
```
Using default tag: latest
latest: Pulling from library/alpine
2aecc7e1714b: Pull complete 
Digest: sha256:0b94d1d1b5eb130dd0253374552445b39470653fb1a1ec2d81490948876e462c
Status: Downloaded newer image for alpine:latest
```

Pull da un altro registry, non Docker Hub:

#### `docker pull` _registry-URL_/_immagine_[:_tag_]
#### `docker pull` _registry-ip_:_porta_/_immagine_[:_tag_]


Non necessariamente un'immagine è un nome semplice. Può essere un nome composto come `gruppo/immagine`, per esempio:
```
docker pull mysql/mysql-server`
```
Solitamente le immagini supportate ufficialmente dal Docker Hub sono nomi semplici.

### Tag di versione

Ogni immagine mantenuta nella libreria delle immagini di Docker Hub è corredata di un tag di versione. Se non viene specificato quale tag vogliamo, il default è `latest`.

Per scaricare un'immagine con un tag specifico:
##### `docker pull alpine:3.1`
```
3.1: Pulling from library/alpine
1bf4b7c8ae75: Pull complete 
Digest: sha256:c870bf18fb9e3ca8162f1f7c95e924820a1fe3ef6b142d235109853ae68772b4
Status: Downloaded newer image for alpine:3.1
```

Sfortunatamente il comando `docker` non ha sottocomandi od opzioni per verificare quali tags siano disponibili. Conoscendo però la URL dello Hub e la struttura dei dati ivi raccolti, è possibile scrivere una procedura shell che risolve il nostro problema.

_(dockertags)_

```bash
#!/bin/bash

if [ $# -lt 1 ]
then
cat << HELP

dockertags  --  list all tags for a Docker image on a remote registry.

EXAMPLE: 
    - list all tags for ubuntu:
       dockertags ubuntu

    - list all php tags containing apache:
       dockertags php apache

HELP
exit 1
fi

image="$1"
tags=`wget -q https://registry.hub.docker.com/v1/repositories/${image}/tags -O -  | sed -e 's/[][]//g' -e 's/"//g' -e 's/ //g' | tr '}' '\n'  | awk -F: '{print $3}'`

if [ -n "$2" ]
then
    tags=` echo "${tags}" | grep "$2" `
fi

echo "${tags}"
```

Proviamo:
##### `dockertags alpine`
```
latest
2.6
2.7
3.1
3.2
3.3
3.4
3.5
3.6
edge
```

### Layers

Un'immagine può essere costituita da più **strati** (_layers_). Finora _alpine_ era costituita da uno strato solo, ma per esempio _ubuntu_ è costituita da più strati.

##### `docker pull ubuntu`
```
Using default tag: latest
latest: Pulling from library/ubuntu
b6f892c0043b: Pull complete 
55010f332b04: Pull complete 
2955fb827c94: Pull complete 
3deef3fcbd30: Pull complete 
cf9722e506aa: Pull complete 
Digest: sha256:382452f82a8bbd34443b2c727650af46aced0f94a44463c62a9848133ecb1aa8
Status: Downloaded newer image for ubuntu:latest
```

Se ora listiamo le immagini presenti con `docker images` non vediamo i singoli strati di ubuntu, ma l'immagine totale.
Docker usa uno **Union File System** per combinare gli strati in una singola immagine. Gli Union File Systems consentono a files e directories di file systems separati, noti come _branches_, di essere sovrapposti un un unico e coerente file system.

Per avere un'idea degli strati di un'immagine, in formato **JSON**, e di tutte le altre sue caratteristiche:
##### `docker inspect ubuntu`
```
[
    {
        "Id": "sha256:ebcd9d4fca80e9e8afc525d8a38e7c56825dfb4a220ed77156f9fb13b14d4ab7",
        "RepoTags": [
            "ubuntu:latest"
        ],
.......
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:2df9b8def18a090592bf1cbd1079e1ac2274435c53f027ee5ce0a8faaa5d6d4b",
                "sha256:2be95f0d8a0c6cc1665ca311e240b9f6dfc824db12a6df3136bc90e0fc33eea3",
                "sha256:47c2386f248c7b10217d7bf25730b1bdb02b276760f75e0af11d28ddd350b53b",
                "sha256:b27287a6dbcef78f4855ab2d9e8dd4c21b53fcf4af77af9c2ecb08725ae02052",
                "sha256:33f1a94ed7fcbcd44e2ccf330729601e204214f34384cba790a99efd4bb29752"
            ]
        }
    }
]
```
I layers possono essere condivisi in più immagini. Se si scarica un'altra immagine, con un tag diverso, che condivide alcuni layers con un'immagine già installata, vengono scaricati solo i layers mancaanti.

## Pull di tutte le immagini di un Repository

Il comando è:
##### `docker pull -a` _immagine_

**Attenzione**

Non effettuarlo veramente.
Può richiedere molto tempo, anche con layer condivisi, ed occupare molto spazio disco.

## Digest

Il chechsum SHA256 di un'immagine si chiama il suo **Digest**. Per aumentare la sicurezza si può includere nel comando di pull:

#### `docker pull` _immagine_:_tag_@_digest_

Per vedere i digest di un'immagine scaricata:
##### docker images --digests alpine


## Rimozione di un'immagine

Per rimuovere un'immagine:
##### `docker rmi 820010c31e66`
```
Untagged: alpine:3.1
Untagged: alpine@sha256:c870bf18fb9e3ca8162f1f7c95e924820a1fe3ef6b142d235109853ae68772b4
Deleted: sha256:820010c31e666d0624507aa27e63be5f79d944b3c042f5107b32f72319990642
Deleted: sha256:ec9551abb42fbcc1fc97cf29fc1ab10a4231e22da5d06d6e8f562e4b88772be6
```
Viene rimossa l'immagine e tutti i suoi layers che non siano in condivisione con altre immagini.

Per (eventualmente) rimuovere tutte le immagini:

#### `docker rmi -f $(docker images -q)`

#### Nota

In caso di interruzione del pull di un'immagine, ripetendo il comando di pull il download ricomincia dall'inizio. La versione corrente ancora non supporta il ripristino di download.

Se ci si accorge durante il pull che l'immagine non è desiderata, non è bello solo abortirla e dimenticarsene: si lascia sporcizia nella libreria locale di docker. Meglio completare comunque il pull poi rimuovere l'immagine.
