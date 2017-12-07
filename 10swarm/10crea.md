# Esempio di Docker Swarm

Creare il nodo Master `master1`:

#### `docker-machine create --driver virtualbox manager1`
```
Running pre-create checks...
(manager1) Default Boot2Docker ISO is out-of-date, downloading the latest release...
(manager1) Latest release for github.com/boot2docker/boot2docker is v17.06.0-ce
(manager1) Downloading /home/mich/.docker/machine/cache/boot2docker.iso from https://github.com/boot2docker/boot2docker/releases/download/v17.06.0-ce/boot2docker.iso...
(manager1) 0%....10%....20%....30%....40%....50%....60%....70%....80%....90%....100%
Creating machine...
(manager1) Copying /home/mich/.docker/machine/cache/boot2docker.iso to /home/mich/.docker/machine/machines/manager1/boot2docker.iso...
(manager1) Creating VirtualBox VM...
(manager1) Creating SSH key...
(manager1) Starting the VM...
(manager1) Check network to re-create if needed...
(manager1) Waiting for an IP...
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
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env manager1
```
Se l'immagine ISO Boot2Docker è mancante o obsoleta, l'ultima viene automaticamente scaricata dalla rete.

Si può verificare l'ambiente generato con:

#### `docker-machine env manager1`
```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/home/mich/.docker/machine/machines/manager1"
export DOCKER_MACHINE_NAME="manager1"
# Run this command to configure your shell: 
# eval $(docker-machine env manager1)
```

Configurare la shell per riconoscere questo ambiente con:

#### `eval $(docker-machine env manager1)`


Creare cinque nodi _worker_ chiamati `worker1` ... `worker5`:

#### `docker-machine create --driver virtualbox worker1`
...

Controllare lo stato dei nodi:

#### `docker-machine ls`
```
NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
manager1   *        virtualbox   Running   tcp://192.168.99.100:2376           v17.06.0-ce   
worker1    -        virtualbox   Running   tcp://192.168.99.101:2376           v17.06.0-ce   
worker2    -        virtualbox   Running   tcp://192.168.99.102:2376           v17.06.0-ce   
worker3    -        virtualbox   Running   tcp://192.168.99.103:2376           v17.06.0-ce   
worker4    -        virtualbox   Running   tcp://192.168.99.104:2376           v17.06.0-ce   
worker5    -        virtualbox   Running   tcp://192.168.99.105:2376           v17.06.0-ce   
```

Verificare l'indirizzo IP di _manager1_:

#### `docker-machine ip manager1`
```
192.168.99.100
```

Verificare l'accesso SSH a _manager1_:

#### `docker-machine ssh manager1`
```
                        ##         .
                  ## ## ##        ==
               ## ## ## ## ##    ===
           /"""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
           \______ o           __/
             \    \         __/
              \____\_______/
 _                 _   ____     _            _
| |__   ___   ___ | |_|___ \ __| | ___   ___| | _____ _ __
| '_ \ / _ \ / _ \| __| __) / _` |/ _ \ / __| |/ / _ \ '__|
| |_) | (_) | (_) | |_ / __/ (_| | (_) | (__|   <  __/ |
|_.__/ \___/ \___/ \__|_____\__,_|\___/ \___|_|\_\___|_|
Boot2Docker version 17.06.0-ce, build HEAD : 0672754 - Thu Jun 29 00:06:31 UTC 2017
Docker version 17.06.0-ce, build 02c1d87
```

In modo simile si può verificare l'accesso ai nodi worker.
Notare che l'autorizzazione è con chiavi preinstallate: non vi è bisogno di alcuna password.

Collegarsi con SSH a `manager1` e dare il comando di inizializzazione dello swarm:

#### `docker swarm init --advertise-addr 192.168.99.100`
```
Swarm initialized: current node (ena9zlcgib0l9a0wxuhffoykk) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-0cb2soesxkvkahnfzi5br4fy4a35eydpjh89ipqll4qo80z615-8lh40pfsti3i18t219birli1p 192.168.99.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

Il manager è già inizializzato. Le informazioni delle ultime due linee indicano come fare ad aggiungere un _altro_ manager.

Collegarsi a turno con ogni worker e dare il comando indicato nelle istruzioni, per esempio:
```
host$ docker-machine ssh worker1
worker1$ docker swarm join --token SWMTKN-1-0cb2soesxkvkahnfzi5br4fy4a35eydpjh89ipqll4qo80z
615-8lh40pfsti3i18t219birli1p 192.168.99.100:2377
This node joined a swarm as a worker.
```

Per verificare lo stato dello _swarm_ dare il comando su `master1`:

#### `docker node ls`
```
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS
55spwzqadhn909v52xahrm4v8     worker5             Ready               Active              
cla9af3pvg7totmbpetfdjnc1     worker3             Ready               Active              
ena9zlcgib0l9a0wxuhffoykk *   manager1            Ready               Active              Leader
iv5mll8t24uc1n1sm4luk1rrp     worker2             Ready               Active              
rq5nfycnbqs0krznhtberssll     worker4             Ready               Active              
sqm0sl6z353ed4hp2q7spnhzw     worker1             Ready               Active
```

Per verificare il comando per aggiungere nodi allo _swarm_, su `master1` si possono dare i comandi:
```
master$ docker swarm join-token worker
master$ docker swarm join-token manager
```

Si può anche controllare lo stato del cluster con:
```
master$ docker info
....
Swarm: active
 NodeID: ena9zlcgib0l9a0wxuhffoykk
 Is Manager: true
 ClusterID: pkuw1nnjxvdq69b5i6237f9ye
 Managers: 1
 Nodes: 6
 Orchestration:
  Task History Retention Limit: 5
 Raft:
  Snapshot Interval: 10000
  Number of Old Snapshots to Retain: 0
  Heartbeat Tick: 1
  Election Tick: 3
 Dispatcher:
  Heartbeat Period: 5 seconds
 CA Configuration:
  Expiry Duration: 3 months
  Force Rotate: 0
 Root Rotation In Progress: false
 Node Address: 192.168.99.100
 Manager Addresses:
  192.168.99.100:2377
....
```
