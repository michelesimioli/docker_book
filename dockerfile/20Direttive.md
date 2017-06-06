## Direttive per Dockerfile

### Dockerfile più complessa: `nginx`

I dockerfile possono presto diventare complessi:
```
FROM ubuntu

# Install Nginx.
RUN \
  add-apt-repository -y ppa:nginx/stable && \
  apt-get update && \
  apt-get install -y nginx && \
  rm -rf /var/lib/apt/lists/* && \
  echo "\ndaemon off;" >> /etc/nginx/nginx.conf && \
  chown -R www-data:www-data /var/lib/nginx

# Define mountable directories.
VOLUME ["/etc/nginx/sites-enabled", "/etc/nginx/certs", "/etc/nginx/conf.d", "/var/log/nginx", "/var/www/html"]

# Define working directory.
WORKDIR /etc/nginx

# Define default command.
CMD ["nginx"]

# Expose ports.
EXPOSE 80
EXPOSE 443
```

#### Direttive del Dockerfile

Comandi:
* `FROM` - specifica l'Immagine di Base da cui si sta costruendo. Deve essere la prima istruzione del file.
* `RUN` - esecuzione di un comando
    * `RUN comando` - il comando è eseguito in una shell, che per default è `/bin/sh -c` su Linux
    * `RUN ["eseguibile", "param1", "param2"]` - vettore di lancio di un comando qualsiasi, generato con `exec`, formato preferito
    * Un comando RUN che si estende su più linee ha ciascuna linea terminata dal carattere di escape, di default `\`, immediatamente prima del NEWLINE
* `LABEL` - etichette a chiave, `LABEL chiave=valore ...`
* `MAINTAINER` - gestore del Dockerfile corrente
    * deprecato, usare `LABEL maintainer="michele@simioli.it"`
* `CMD` - definisce il comando da eseguire al lancio del container, deve essercene esattamente uno
    * `CMD ["eseguibile","param1","param2"]` - formato _exec_, preferito
    * `CMD comando param1 param2` - formato shell
    * `CMD ["param1", "param2"]` - parametri relativi allo `ENTRYPOINT`
* `ENTRYPOINT` - come `CMD` ma solo nei due formati
    * `ENTRYPOINT ["executable", "param1", "param2"]`
    * `ENTRYPOINT comando param1 param2`
* `WORKDIR` - directory di lavoro per il comando, una sola
* `ADD sorgente... destinazione` - aggiunge i file locali _sorgente_ alla _destinazione_ sull'immagine
    * `sorgente` può contenere caratteri jolly, e deve essere nel contesto del build
    * `destinazione` deve essere un percorso assoluto o relativo a `WORKDIR`
    * la _destinazione_ appartiene a UID=0 e PID=0
    * se _sorgente_ o _destinazione_ terminano con `/` sono considerate directories
    * in caso di copia multipla la destinazione deve essere una directory
    * se la destinazione è una directory innestata, tutti i percorsi intermedi vengono creati
* `COPY sorgente... destinazione` - praticamente uguale ad `ADD`
* `ENV chiave[=]valore ...` - settaggio di variabili d'ambiente
    * ci può essere un `=` o uno spazio come separatore
    * ve ne possono essere più di uno
* `VOLUME "dir"` - crea un punto di montaggio per un volume fornito esternamente
    * deve esserci un solo argomento, ma può essere un array in formato JSON
    * `"dir"` deve essere un percorso assoluto
* `EXPOSE porta` - porta TCP/IP su cui ascoltare a runtime
* `USER utente` - definisce l'utente coi cui diritti viene eseguito `CMD`
* `SHELL ["eseguibile", "parametro"]` - definisce la shell con cui eseguire i comandi seguenti - default `SHELL ["/bin/bash", "-c"]`
    * ve ne può essere più di uno: determina il comportamento per le istruzioni seguenti
* `ONBUILD istruzione` - definisce un _trigger_, un comando che deve venire eseguito quando l'immagine corrente è nel `FROM` di un altro Dockerfile
    * ve ne può essere più di uno
* `ARG nome[=valore]` - definisce un argomento per l'opzione di build `--build-arg nome=valore`
    * ve ne può essere più di uno
    * tutti gli argomenti forniti nel comando build devono essere previsti nel Dockerfile
    * se un argomento non è fornito nel comando di build, `ARG` deve dargli un valore di default
* `STOPSIGNAL segnale` - determina il segnale che viene inviato al container dal comando `docker stop ID` - default `SIGTERM`
