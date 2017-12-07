# Servizio su Swarm

Su `master1` creare un servizio:

#### `master$ docker service create --replicas 5 -p 80:80 --name web nginx`
```
txhnt3f2sn4kheeomwzgkxv7x
Since --detach=false was not specified, tasks will be created in the background.
In a future release, --detach=false will become the default.
```

Verificare la creazione del servizio:

#### `master$ docker service ls`
```
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
txhnt3f2sn4k        web                 replicated          0/5                 nginx:latest        *:80->80/tcp
```
Come si nota le repliche sono ancora 0 su 5. Il servizio non è pronto. Questo è probabilmente dovuto al fatto che l'immagine di `nginx` è in via di scaricamento dalla rete.

Attendere e ripetere il comando finchè le repliche non sono 5 su 5.

Verificare poi lo stato del servizio:


#### `master$ docker service ps web`
```
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
2ta6uwfhe6a8        web.1               nginx:latest        worker3             Running             Running 56 seconds ago                       
inmiz8oz5zsw        web.2               nginx:latest        worker4             Running             Running 34 seconds ago                       
p98prruvvsd0        web.3               nginx:latest        manager1            Running             Running 45 seconds ago                       
r74s0bozf84s        web.4               nginx:latest        worker1             Running             Running 34 seconds ago                       
1g2w2dhejq7i        web.5               nginx:latest        worker2             Running             Running 28 seconds ago  
```

Il servizio è pronto.

Verificare con:

#### `master$ docker ps`
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
fc204aeb1261        nginx:latest        "nginx -g 'daemon ..."   3 minutes ago       Up 3 minutes        80/tcp              web.3.p98prruvvsd013462elb9s5u2
```
