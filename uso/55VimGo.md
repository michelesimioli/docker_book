## Esercizio: Vim editor per Go


Vogliamo un ambiente di sviluppo per il linguaggio Go in un'immagine, incluso un editor `vim` con un plugin che supporti il Go.

Partiamo con un container con Linux Alpine:
```
docker run -ti --name alpine alpine sh
```

In Alpine, acquisiamo tutti i packages e installiamo `vim`:
```
apk update

apk add vim
```
Avremo bisogno anche di `git`:
```
apk add vim
```
Ora installiamo il plugin per `go` da GitHub:
```
cd

git clone https://github.com/fatih/vim-go.git ~/.vim/pack/plugins/start/vim-go
```
Installiamo quindi `go`:
```
apk add go

apk add musl-dev
```
**Spiegazione**: Alpine Linux non usa `Glibc`, ma `musl`, una nuova libreria standard del linguaggio C. Per poter compilare dal `go` è necessario installare il pacchetto di sviluppo di musl.

Proviamo `vim`:
```
cd /tmp
vim hello.go
```
```go
package main

import "fmt"

func main() {
	fmt.Println("Hello from vim-go")
}
```
Possiamo testarlo localmente:
```
go run hello.go
Hello from vim-go
```
Il plugin di vim che abbiamo installato permette anche dal suo interno di acquisire numerose utilities di sviluppo di Go. Lanciamo semplicemente `vim` e diamo il comando:
```
:GoInstallBinaries
```
Tutte le principali utilities di Go vengono scaricate dalla rete ed installate nella corretta struttura di sviluppo: `/root/go`.

Per visualizzarla possiamo installare anche l'utility `tree`:
```
apk add tree
```
e dare il comando `tree /root/go`.

Creaiamo anche una directory `~/bin` ove ospitare eseguibili, e che sarà il volume condiviso al nostro sistema host.

Ora occorre modificare le variabili d'ambiente corrette per lo sviluppo, editando il file `/root/.profile`.
```
export GOROOT=/usr/lib/go
export GOPATH=~/go
export GOBIN=$GOPATH/bin
export PATH=~/bin:$GOBIN:$GOROOT/bin:$PATH
```

Ora il momento delicato.

Uscire dal contenitore con `Ctrl-P Ctrl-Q`, NON col comando _exit_.

Generiamo la nuova immagine:
```
docker commit alpine vim-go
```
Ci può volere qualche istante. Verifichiamone poi l'esistenza con `docker images`.

Fermiamo e rimuoviamo il vecchio contenitore

```
docker stop alpine
docker rm alpine
```

Lanciamo il nuovo contenitore:
```
docker run --rm -ti --name vim-go -v ~/bin:/root/bin vim-go su -
```

Il comando eseguito `su -` lancia una shell di login di root, che dal `.profile` setta tutte le variabili d'ambiente. E' la shell di default di `root` su Alpine (come descritto da /etc/passwd).
Se si esegue semplicemente `sh` parte una Bourne shell senza ambiente.

Il contenitore verrà rimosso automaticamente all'uscita da questa shell, vista l'opzione `--rm`.

Andiamo nella directory giusta e generiamo un file di prova:
```
cd go/src
mkdir hello
cd hello
vi hello.c
```
```go
package main

import "fmt"

func main() {
	fmt.Println("vim-go")
}
```

Attenzione alla compilazione. Se usiamo il semplice comando:
```
go build hello.go
```
l'eseguibile è compilato dinamicamente e per la libreria runtime `musl`. Non funzionerà in Ubuntu o qualsiasi altro Linux basato su Glibc.

Alpine però aveva installato anche il toolchain GCC, quindi possiamo costruire il programma eseguibile col link esterno:
```
go build --ldflags '-linkmode external -extldflags "-static" -s' hello.go
```
L'ulteriore opzione `-s` toglie all'eseguibile la tabella dei simboli e il supporto al debugging, riducendone così la dimensione.

Viene generato l'eseguibile `hello`. Copiamolo nel volume condiviso:
```
cp hello ~/bin
```
Ora possiamo uscire dal contenitore:
```
exit
```
Nella nostra directory `~/bin` c'è il file `hello`, linkato staticamente, quindi eseguibile.

Abbiamo ottenuto un ambiente di sviluppo in linguaggio Go completamente incapsulato in un contenitore.
