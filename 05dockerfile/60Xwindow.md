## Docker e X Window

Genereremo un'immagine contenente il browser _Firefox_, che si collegherà al nostro ambiente grafico X Window System. Questo non è così semplice come il collegamento a terminale.

Non si può lanciare Firefox se non si ha X Window, o nativo, o tramite VNC.

### Generazione Immagine per Firefox

Creare una directory opportuna e il _Dockerfile_:
```
mkdir -p ~/ex/firefox
cd ~/ex/firefox
vi Dockerfile
```
```dockerfile
FROM ubuntu:14.04

RUN apt-get update && apt-get install -y firefox

# Replace 1000 with your user / group id
RUN export uid=1000 gid=1000 && \
mkdir -p /home/developer && \
echo "developer:x:${uid}:${gid}:Developer,,,:/home/developer:/bin/bash" >> /etc/passwd && \
echo "developer:x:${uid}:" >> /etc/group && \
echo "developer ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/developer && \
chmod 0440 /etc/sudoers.d/developer && \
chown ${uid}:${gid} -R /home/developer

USER developer
ENV HOME /home/developer
CMD /usr/bin/firefox
```

Dopo eventualmente aver cambiato i parametri `uid` e `gid`, costruiamo l'immagine:
```
docker build -t firefox .
```
Può impiegare un po' di tempo. La dimensione finale è di 398 MB.


### Firefox da Macchina Host

Lanciare il comando:
```
docker run -ti --rm -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix firefox > /dev/null 2>&1
```

Occorre settare nel container la variabile d'ambiente DISPLAY. Questo è vero per tutti i programmi client X Window.

L'opzione di lancio `-e chiave=valore` passa una variabile d'ambiente al container. Si può ripetere l'opzione `-e` più volte.

Il volume condiviso è il socket di comunicazione tra la macchina host e il container. Nel caso di X Window è `/tmp/.X11-unix` se vi è solo il display di default attivo, altrimenti è `/tmp/.X11-unix/Xn` se vi sono più display attivi, ove `n` è il numero di display.

Firefox se lanciato in _foreground_ può generare messaggi di warning a terminale; se lanciato in _background_ viene bloccato dal Job Control e va in modalità _Stopped_ quando prova a inviare i messaggi di warning.
La soluzione è la ridirezione di _stdin_ e _stderr_ a `/dev/null`.

### Firefox da VNC

#### Installazione di VNC su Ubuntu 16.04

Installare i pacchetti:
```
sudo apt install xfce4 xfce4-goodies vnc4server
```
Lanciare il server per generare alcuni files di configurazione:
```
vncserver
```
Viene chiesta la password di accesso. La si può cambiare in seguito col comando `vncpasswd`. 

Fermare il server:
```
vncserver kill :1
```
Editare il file di configurazione `~/.vnc/xstarup`
```bash
#!/bin/sh

[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
vncconfig -iconic &
startxfce4 &
```
Lanciare il server di nuovo:
```
vncserver -geometry "1024x680"
```

Nel nostro esempio l'indirizzo della macchina server VNC, la stessa dello _host_ Docker, è 192.168.0.10.

VNC apre la porta 5901 per il Display 1, 5902 per il 2, ecc.

#### Installazione di un Client VNC e Collegamento a Firefox

Vi sono alcuni client VNC. Useremo Vinagre.
```
sudo apt install vinagre
```

Aprire un collegamento al server con un Vinagre. Lanciare `vinagre`.

Cliccare `Connect`. Nella finestra di pop-up: Protocollo: `VNC`. Hostname: `192.168.0.10:5901`.
Cliccare: `Connect`. Viene chiesta la password di accesso.

Se tutto va bene si apre una sessione Xfce4. Aprire un terminale.

Come prima cosa bisogna disabilitare il controllo accesso di X Window col comando:
```
xhost +
```
Poi lanciamo il container `firefox` nel seguente modo:
```
docker run -ti --rm -e DISPLAY=$DISPLAY -v /tmp/.X11-unix/X1:/tmp/.X11-unix/X1 firefox > /dev/null 2>&1
```

L'esempio presuppone che il VNC Server abbia aperto il display `:1`.

