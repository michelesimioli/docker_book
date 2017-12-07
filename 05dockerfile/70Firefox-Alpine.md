# Firefox in Alpine

Genereremo un'immagine `alpfire` basata su Alpine e contenente Firefox e Ssh.

Creare la directory di progetto:
```
mkdir -p ~/ex/alpine-firefox-ssh
cd ~/ex/alpine-firefox-ssh
```
Generare il file `docker-entrypoint.sh`:
```bash
#!/bin/sh
# generate fresh rsa key
ssh-keygen -f /etc/ssh/ssh_host_rsa_key -N '' -t rsa

# generate fresh dsa key
ssh-keygen -f /etc/ssh/ssh_host_dsa_key -N '' -t dsa

#prepare run dir
mkdir -p /var/run/sshd

#prepare sshd config
 sed -i "s/UsePrivilegeSeparation.*/UsePrivilegeSeparation no/g" /etc/ssh/sshd_config \
 && sed -i "s/UsePAM.*/UsePAM no/g" /etc/ssh/sshd_config \
 && sed -i "s/PermitRootLogin.*/PermitRootLogin yes/g" /etc/ssh/sshd_config \
 && sed -i "s/#AuthorizedKeysFile/AuthorizedKeysFile/g" /etc/ssh/sshd_config \
 && sed -i "s/#X11Forwarding no/X11Forwarding yes/g" /etc/ssh/sshd_config \
 && sed -i "s/#PermitUserEnvironment no/PermitUserEnvironment yes/g" /etc/ssh/sshd_config \
 && echo "ForwardX11Trusted yes" >> /etc/ssh/ssh_config

#prepare xauth
touch /root/.Xauthority

# generate machine-id
uuidgen > /etc/machine-id

# set keyboard for all sh users
echo "export QT_XKB_CONFIG_ROOT=/usr/share/X11/locale" >> /etc/profile
# set keyboard for direct command use
echo "QT_XKB_CONFIG_ROOT=/usr/share/X11/locale" >> /root/.ssh/environment

source /etc/profile

exec "$@"
```

Questa è una procedura shell che viene eseguita per prima al lancio del contenitore e che si assicura dell'esistenza di chiavi _rsa_ e _dsa_ per ssh, nonchè della directory di run, ove ospitare il PID di sshd.

Prepara quindi il file di configurazione per `sshd` con tutti i parametri necessari.

Quindi genera il file per il protocollo di autenticazione Xauth di X Window e un identificativo univoco per la macchina corrente.

Setta la condivisione della tastiera per tutti gli utenti e per l'utente interattivo, e riesegue il file `/etc/profile` dei settaggi per tutti gli utenti.

Notare l'ultimo comando: `exec "$@"`.
Questo lancia il programma ricevuto come argomenti.

Dare il permesso di esecuzione:
```
chmod +x docker-entrypoint.sh
```

Preparare ora il `Dockerfile`:
```dockerfile
FROM alpine

# add openssh and clean
RUN apk add --update openssh \
    && apk --update --no-cache add firefox-esr \
    && rm  -rf /tmp/* /var/cache/apk/*
# add entrypoint script
ADD docker-entrypoint.sh /usr/local/bin
#make sure we get fresh keys
RUN rm -rf /etc/ssh/ssh_host_rsa_key /etc/ssh/ssh_host_dsa_key

EXPOSE 22

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["/usr/sbin/sshd","-D"]
```

Innanzitutto viene installato il software di ssh (_openssh_) e di Firefox (_firefox-esr_), al termine ripulendo i file temporanei e le cache.

Poi viene aggiunta la procedura shell `docker-entrypoint.sh` in un percorso del PATH.

Viene naturalmente aperta la porta 22 di ssh.

Ora il trucco finale:
```dockerfile
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["/usr/sbin/sshd","-D"]
```
Il punto di ingresso `ENTRYPOINT` è la shell preparata, che riceve come argomenti gli argomenti di `CMD`. La shell esegue e al termine lancia gli argomenti di CMD.

Questo è un metodo standard per far compiere ad un container dei settaggi iniziali.

Generiamo ora l'immagine:

#### `docker build -t alpfire .`

Può impiegare un po' di tempo, poichè deve installare tutte le dipendenze del software richiesto.

L'immagine risultante è di 196 MB, un bel risparmio dai 398 MB quando è basata su Ubuntu. E in quest'immagine c'è anche ssh.

## Lancio di `alpfire`

Vogliamo alla fine accedere al container _alpfire_ tramite ssh, senza dare la password, basandoci su chiavi crittografiche.

### Generazione di Chiavi

Sulla macchina _client_, non sullo host di Docker, se già non le abbiamo, prepariamo le chiavi `rsa`:
```
ssh-keygen
```
Attiviamo in background `ssh-agent` ed aggiungiamogli la nostra chiave:
```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```
Ci viene chiesta la passprase della chiave. La presenza di questo agente fa si che non ci sia bisogno di dare la passphrase ogni volta che ci si collega con ssh. L'agente muore e perde le informazioni al logout, quindi questi due comandi sono da ripetersi ad ogni login.

Ora occorre copiare la chiave pubblica sulla macchina host, dandole un nome distintivo:
```
scp ~/.ssh/id_rsa.pub mich@192.168.0.10:ex/mich.pub
```

### Container Dati per le Chiavi

Viene creato un container docker per tenere le chiavi autorizzate:
```
cd ~/ex
tar cv --files-from /dev/null | docker import - dummy
docker create -v /root/.ssh --name ssh-container dummy /bin/true
docker cp mich.pub ssh-container:/root/.ssh/authorized_keys
```
Il primo comando crea un'immagine `dummy` di 0 bytes.
Lo fa generando un archivio `tar` ed inserendogli i dati da `/dev/null`, cioè un archivio tar vuoto (ma con la struttura giusta).
Dopo la pipe il comando `docker import` legge l'immagine da standard input, `-`, e la chiama `dummy`. Il comando `import` si aspetta sempre immagini tar.
`dummy` è ora un'immagine valida, e la possiamo vedere con `docker images`.

Il secondo comando l'abbiamo (quasi) già visto: crea un volume legato a `/home/mich/.ssh` col nome `ddh-comtainer` basato sull'immagine `dummy`.
L'interessante è l'argomento `/bin/true` alla fine. Non avevamo visto alcun argomento quando, in precedenza, l'immagine di base era `ubuntu` anziche `dummy`.

Ogni immagine deve avere un _entry point_, un comando che deve eseguire il contenitore attivato. Se non ce l'ha, allora occorre specificarlo sulla linea di run. `dummy` non ha alcun comando, quindi gli passiamo `/bin/true` che ha sempre successo.
Infatti `dummy` è vuoto, non ha nemmeno `/bin/true`. Ma non importa, tanto non è un contenitore che gira, solo che tiene dati. Se anche gli passiamo il comando `zot` va bene lo stesso, tanto per soddisfare la sintassi.

Il terzo comando palesemente copia la nostra chiave nel container che tiene le chiavi. Notare la sintassi, che sembra quella di NFS: la destinazione ha la struttura `contenitore:file`.
Questo tipo di comando si può fare con qualsiasi contenitore, e anche il comando inverso, da contenitore a file system.

## Lancio del Contenitore e Collegamento

Lanciare con:

#### `docker run -p 4848:22 --name alpine-firefox --hostname alpine-firefox --volumes-from ssh-container -d alpfire`

La nostra macchina _host_, in questo esempio, ha l'indirizzo IP 192.168.0.10.

Collegarsi dalla macchina _client_ con:
```
ssh -i ~/.ssh/id_rsa -p 4848 -X root@192.168.0.10 firefox
```

Si apre una finestra con Firefox.

Occorre notare che il trasferimento dati su canale SSH è molto più lento che col protocollo VNC, anche se molto più sicuro.

