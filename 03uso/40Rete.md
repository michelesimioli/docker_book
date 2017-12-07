# Semplice Networking

La rete di Docker fa uso di una connessione **bridge** di nome `docker0`:
#### `ifconfig docker0`
```
docker0   Link encap:Ethernet  HWaddr 02:42:ea:41:9a:7d  
          inet addr:172.17.0.1  Bcast:0.0.0.0  Mask:255.255.0.0
          inet6 addr: fe80::42:eaff:fe41:9a7d/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:75 errors:0 dropped:0 overruns:0 frame:0
          TX packets:62 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:5052 (5.0 KB)  TX bytes:14225 (14.2 KB)
```
Il bridge è la connessione ad una rete interna di default che ospita tutti i container creati.

Al bridge è stata assegnato un indirizzo di default: `172.17.0.1/16`. Questo è l'indirizzo della macchina ospite sulla rete interna.

Volendo si possono installare in Ubuntu le utilities per i bridge:

```
sudo apt install bridge-utils
```
Ora si può verificare col comando:
#### `brctl show`
```
bridge name	bridge id		STP enabled	interfaces
docker0		8000.0242ea419a7d	no		veth2f8ae0e
```

Col comando `brctl` si possono cambiare molti parametri dei bridge. Vedere la manualistica ed il comando `brctl help`.

Possiamo consultare l'interfaccia specifica di un contenitore:
#### `ifconfig veth2f8ae0e`
```
veth2f8ae0e Link encap:Ethernet  HWaddr f6:77:16:5e:6f:61  
          inet6 addr: fe80::f477:16ff:fe5e:6f61/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:8 errors:0 dropped:0 overruns:0 frame:0
          TX packets:40 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:648 (648.0 B)  TX bytes:6183 (6.1 KB)
```

In generale il nome dell'interfaccia è `vethxxxxxxx`, diverso in istanze diverse.

Se fermiamo il server con  `sudo service docker stop`  viene rimossa l'interfaccia  `vethxxx`  ma non il brige  `docker0`.

Fermare il server impiega un po' di tempo, poichè viene mandato un segnale `SIGTERM` a tutti i contenitori attivi.

Se facciamo ripartire il server con `sudo service docker start` non viene ricreata subito l'interfaccia `vethxxx`, ma solo all'attivazione del primo contenitore, ed ha un nome diverso da prima.


## Immagine con applicativo di rete

Un'immagine di test che offre un server web, attivo sulla porta 8080.

Scaricare l'immagine:
##### `docker pull adejonge/helloworld`

Lanciare un contenitore in modo detached:
##### `docker run -d adejonge/helloworld`

Ispezionare i parametri dell'immagine:
##### `docker inspect fb0f4726`
```
.........
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "0e3b7af1bbd86351636818aac71a6ca54784b442b5408596b14bea7f5fa730c1",
                    "EndpointID": "8c8944e7f92280ed908d27a54b21f7705117ab94fca0c4fcc8581ae616323ea7",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:03"
                }
            }
        }
    }
]
```
Si vede che l'indirizzo IP assegnato è `172.17.0.3`.

Test del server web:
#### `curl 172.17.0.3:8080`
```
Hello World from Go in minimal Docker container
```
Funziona. Non è possibile però collegarsi da altri computer che non siano quello ospitante.
