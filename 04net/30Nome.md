## Reti con Nome

##### `docker network create net1`

Crea una rete di nome `net1`.

![namednet](../gitbook/images/namednet.png)

Una rete con nome è simile a un container: Si può visualizzarne le proprietà:
```
docker network inspect net1 
[
    {
        "Name": "net1",
        "Id": "9aa4d0778dde156db22c4f88ecc07f6748adce397edea2a8dc8bf4de88b487e5",
        "Created": "2017-06-04T23:50:23.214967483+02:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```
E' in formato JSON.


##### `docker run -ti --net=net1 --name one alpine sh`
```
Ctrl-P Ctrl-Q
```
##### `docker run -ti --net=net1 --name two alpine sh`
```
/ # ping one
PING one (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.162 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.155 ms
^C
--- one ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.155/0.158/0.162 ms
/ #
```
C'è risoluzione nomi-indirizzi, servizio offerto da `net1`.

Il nome di un container (hostname) è:
* quello assegnato dall'opzione `--name`
    * scelta più opportuna
* quello di fantasia assegnato di default
* lo ID del container

### Agganciare una Rete

Un contenitore può essere collegato a più di una rete:

```
docker network create net2

docker network connect net2 two
```

Collega il contenitore two alla rete net2. Si può tastare con:
```
docker attach two

/ # ifconfig
```
e vengono visualizzate le interfacce eth0 ed eth1.

### Rimuovere una Rete

Il comando è semplicemente:

##### `docker network rm nomerete`

Se si tenta di rimuovere una rete con container attivi si ottiene un messaggio d'errore.

Occorre perlomeno fermare i contenitori su una rete prima di rimuovere la rete.

Se si tenta successivamente di compiere lo start di un contenitore quando la sua rete è stata rimossa, si ottiene un messaggio d'errore.

Se si ricrea la rete il contenitore riparte, anche qualora la rete avesse ora un indirizzo IP diverso: il contenitore vuole la rete per nome, non per indirizzo.

**Attenzione**

In una configurazione con molte reti e contenitori interconnessi è facile compiere pasticci dipendenti dall'ordine di creazione e collegamento.
E' meglio scrivere una procedura shell di configurazione e partire sempre da una situazione iniziale pulita.

Le tre reti predefinite non si possono rimuovere.

Un _one-liner_ shell per rimuovere tutte le reti può essere:
```
docker network ls | grep [b]ridge | awk '$2!="bridge"{print $1}'| xargs docker network rm
```
