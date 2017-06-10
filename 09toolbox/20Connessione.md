## Connessione Docker Machine

Intendiamo connetterci con `docker-machine` ad uno host remoto in cui Docker Engine è già installato.

In questo esercizio:
* lo host remoto ha indirizzo IP: 192.168.0.10
* il nome dell'utente locale è `mich`
* vi è l'utente equivalente `mich` sullo host remoto anche se ha una password diversa
* la machine remota si chiamerà `remote-docker-host`

#### Passo 1 - Disabilitare Password di `sudo`

E' indispensabile che l'utente della macchina **host** riesca ad eseguire comandi `sudo` senza che gli venga chiesta la password.

In Ubuntu i membri del gruppo `sudo` possono invocare tale comando. Cambiare la configurazione con `sudo vi /etc/sudoers`. Assicurarsi che la linea appropriata sia:
```
%sudo ALL=(ALL:ALL) NOPASSWD:ALL
```
Questo introduce un certo livello di rischio. Il file si può ripristinare alla situazione precedente dopo che la machine è stata creata.

Per attivare la nuova configurazione occorre compiere un relogin.

#### Passo 2 - Chiavi SSH

Se non l'abbiamo già occorre generare una coppia di chiavi con:
```
ssh-keygen
```
con tutte le risposte di default.
Quindi registriamola dando `ssh-agent`.

Poi copiamo la nostra chiave pubblica al file remoto `~/.ssh/authorized_keys` con l'utility:
```
ssh-copy-id mich@192.168.0.10
```
Output:
```
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
mich@192.168.0.10's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'mich@192.168.0.10'"
and check to make sure that only the key(s) you wanted were added.
```

#### Passo 3 - Creazione Machine Remota

Col comando:
```
docker-machine create --driver generic --generic-ip-address=192.168.0.10 --generic-ssh-key "/home/mich/.ssh/id_rsa" --generic-ssh-user=mich remote-docker-host```
```
