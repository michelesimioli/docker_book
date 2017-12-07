# Creazione di una Machine in Linux

![Machine](../gitbook/images/machine.png)

```
docker-machine create --driver virtualbox default
```
Risultato:
```
Creating CA: /home/mich/.docker/machine/certs/ca.pem
Creating client certificate: /home/mich/.docker/machine/certs/cert.pem
Running pre-create checks...
(default) Image cache directory does not exist, creating it at /home/mich/.docker/machine/cache...
(default) No default Boot2Docker ISO found locally, downloading the latest release...
(default) Latest release for github.com/boot2docker/boot2docker is v17.05.0-ce
(default) Downloading /home/mich/.docker/machine/cache/boot2docker.iso from https://github.com/boot2docker/boot2docker/releases/download/v17.05.0-ce/boot2docker.iso...
(default) 0%....10%....20%....30%....40%....50%....60%....70%....80%....90%....100%
Creating machine...
(default) Copying /home/mich/.docker/machine/cache/boot2docker.iso to /home/mich/.docker/machine/machines/default/boot2docker.iso...
(default) Creating VirtualBox VM...
(default) Creating SSH key...
(default) Starting the VM...
(default) Check network to re-create if needed...
(default) Found a new host-only adapter: "vboxnet0"
(default) Waiting for an IP...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env default
```

Controlliamone l'esistenza:
```
docker-machine ls

NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
default   -        virtualbox   Running   tcp://192.168.99.100:2376           v17.05.0-ce
```

Per collegarsi alla macchina `default` occorre impostare delle variabili d'ambiente. Per vederle il comando è:
```
docker-machine env default
```
Con risultato:
```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/home/mich/.docker/machine/machines/default"
export DOCKER_MACHINE_NAME="default"
# Run this command to configure your shell: 
# eval $(docker-machine env default)
```

Se questa è l'unica macchina Docker gestita, si possono impostare tali variabili nel `~/.profile`.

Se vogliamo di volta in volta collegarci a macchine Docker diverse, diamo il comando immediato:
```
eval $(docker-machine env default)
```
Per verificare:
```
env | grep DOCKER
```
E per togliere i settaggi dall'ambiente:
```
eval $(docker-machine env -u)
```

#### Comandi alla Docker Machine

Ora si possono dare comandi Docker e vengono inviati alla machine configurata dalle variabili d'ambiente:
```
docker run busybox echo hello world
```
Con output:
```
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
1cae461a1479: Pull complete 
Digest: sha256:c79345819a6882c31b41bc771d9a94fc52872fa651b36771fbe0c8461d7ee558
Status: Downloaded newer image for busybox:latest
hello world
```

Rimuovere il contenitore creato con `docker rm ID`.

## Comandi alla Docker Machine

Si inviano comandi di controllo con la sintassi:
```
docker-machine comando nome
```
ove `nome` è il nome della Docker Machine. Se si omette questo parametro il default è `default`.

I comandi che si comportano in questo modo sono:

* `docker-machine config`
* `docker-machine env`
* `docker-machine inspect`
* `docker-machine ip`
* `docker-machine kill`
* `docker-machine provision`
* `docker-machine regenerate-certs`
* `docker-machine restart`
* `docker-machine ssh`
* `docker-machine start`
* `docker-machine status`
* `docker-machine stop`
* `docker-machine upgrade`
* `docker-machine url`

