## Download e Setup



### Aggiunta utente autorizzato

Per poter comunicare col server docker, cioè poter dare quasi tutti i comandi, è necessario che un utente appartenga al gruppo `docker`.

Per aggiungere l'utente _pippo_ a tale gruppo:
```
sudo usermod -G -a docker pippo
```

### Verifica versione

Il comando:
##### docker version
```
Client:
 Version:      17.03.1-ce
 API version:  1.27
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Mon Mar 27 17:14:09 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.03.1-ce
 API version:  1.27 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Mon Mar 27 17:14:09 2017
 OS/Arch:      linux/amd64
 Experimental: false
 ```
 
 da un rapporto sulla versione sia del client che del server, e implica che il server è attivo.

 ### Gestione del Server

 Dipende dalla variante di Linux usata.
 Su sistemi basati su init/upstart (RedHat/Centos 6, Ubuntu 14.04 LTS):
 ```
 service docker status|start|stop|restart
 ```

 Su sistemi basati su systemctl (RedHat/CentOS 7, Ubuntu 16.06 LTS:
 ```
 systemctl status|start|stop|restart docker
 ```
