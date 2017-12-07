# Consigli di Tuning e Sicurezza

## User

Nel `dockerfile` settare sempre uno USER:
```
RUN groupadd -r user_grp && useradd -r -g user_grp user
USER user
```

Altrimenti settare un utente non privilegiato in una procedura `entrypoint`:
```
#!/bin/bash
set -e
if [ "$1" = 'redis-server' ]; then
        chown -R redis .
        exec gosu redis "$@"
fi

exec "$@"
```

L'utility `gosu` è scritta in Go e va normalmente scaricata:
```
sudo apt install gosu
```
`gosu` ha dei vantaggi su `sudo`: esegue il suo argomento nella shell corrente, non in una sottoshell; inoltre riceve eventuali segnali inviati alla shell originale.
E' una buona idea anche per i comandi Linux in generale.
Cf. `https://github.com/tianon/gosu`

## Limitare il Networking

Un container dovrebbe aprire soltanto le porte che gli servono.
Didefault più containers sulla stessa rete possono vedersi anche se le porte non sono state ufficialmente aperte od esposte.

Per esempio:
```
$ docker pull amouat/network-utils

$ docker run --name nc-test -d \
  amouat/network-utils nc -l 5001
c6eb9ba8f27df239e3a7c77ed2b1c515982b44bc9c3aba1f184d68c068538c47

$ docker run \
    -e IP=$(docker inspect -f \
    {{.NetworkSettings.IPAddress}} nc-test) \
    amouat/network-utils \
    sh -c 'echo  "hello" | nc -v $IP 5001'
Connection to 172.17.0.2 5001 port [tcp/*] succeeded!

$ docker logs nc-test
hello
```
Si può disabilitare la comunicazione tra i container lanciandoli con l'opzione `--icc=false`. Inoltre l'opzione `--iptables=true` forza l'uso delle regole di _iptables_ sullo host.

Meglio settarlo nel file di configurazione `/etc/default/docker` alla voce `DOCKER_OPTS`
```
DOCKER_OPTS="--iptables=true --icc=false"
```

## SUID e SGID

E' molto probabile che non vi sia bisogno nel nostro applicativo dei programmi con il bit SUID o SGID settato. 
In tal caso può essere una buona idea disabilitarli.
Per esempio, in un `dockerfile`:
```
FROM debian:wheezy

RUN find / -perm +6000 -type f -exec chmod a-s {} \; || true
```

## Limitare la Memoria

Con l'opzione `-m` per la memoria e `--memory-swap` per l'area di swap.
Non tutti i kernel supportano la limitazione di swap.
Per abilitarla editare il file `/etc/default/grub` ed inserire in fondo la linea:
```
GRUB_CMDLINE_LINUX_DEFAULT="cgroup_enable=memory swapaccount=1"
```
Poi eseguire:
```
sudo update-grub
sudo reboot
```

Esempio della limitazione di memoria:
```
$ docker pull amouat/stress

$ docker run -m 128m --memory-swap 128m amouat/stress \
     stress --vm 1 --vm-bytes 127m -t 5s
stress: info: [1] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
stress: info: [1] successful run completed in 5s

$ docker run -m 128m --memory-swap 128m amouat/stress \
     stress --vm 1 --vm-bytes 130m -t 5s
stress: FAIL: [1] (416) <-- worker 5 got signal 9
stress: WARN: [1] (418) now reaping child worker processes
stress: FAIL: [1] (422) kill error: No such process
stress: FAIL: [1] (452) failed run completed in 0s
stress: info: [1] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd

$ docker run -m 128m amouat/stress \
     stress --vm 1 --vm-bytes 255m -t 5s
stress: info: [1] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
stress: info: [1] successful run completed in 6s
```

**Nota**

Ripulire tutti i container attivi:
```
docker ps -a| grep -v CONTAINER | awk '{print $1}' | xargs docker rm
```

## Limitare la CPU

Con l'opzione `-c`. Il peso di default è 1024.

Esempio:
```
$ docker run -d --name load1 -c 2048 amouat/stress
$ docker run -d --name load2 amouat/stress
$ docker run -d --name load3 -c 512 amouat/stress
$ docker run -d --name load4 -c 512 amouat/stress

$ docker stats $(docker inspect -f {{.Name}} $(docker ps -q))
```

## Limitare i Restart

Un container può avere problemi e compiere troppi restart.
Limitarli con, per esempio:
```
$ docker run -d --restart=on-failure:10 my-flaky-image
```

Per vedere il conto di restart corrente:
```
$ docker inspect -f "{{ .RestartCount }}" $(docker ps -lq)
```

## Limitare i Filesystem

Con l'opzione `--read-only`. Esempio:

```
$ docker run --read-only debian touch x
touch: cannot touch 'x': Read-only file system
```

Si può inoltre limitare l'accesso ai volumi. Per esempio:
```
$ docker run -v $(pwd):/pwd:ro debian touch /pwd/x
touch: cannot touch '/pwd/x': Read-only file system
```

## Limitare i Capabilities

Il kernel Linux ha una serie di **Capabilities** che si possono settare o togliere. Il contenitore di default ne ha un sottoinsieme.

Questo permette un insieme di permessi più granulari che non solo l'amministratore di sistema `root` e gli utenti normali.

E' possibile aggiungere o togliere _capabilities_ con le opzioni `--cap-add` e `--cap-drop`.

Esempio:

```
$ docker run debian \
         date -s "10 FEB 1981 10:00:00"
Tue Feb 10 10:00:00 UTC 1981
date: cannot set date: Operation not permitted
$ docker run --cap-add SYS_TIME debian \
         date -s "10 FEB 1981 10:00:00"
Tue Feb 10 10:00:00 UTC 1981
$ date
Tue Feb 10 10:00:03 GMT 1981
```

Per dare (praticamente) tutti i capabilities ad un container, l'opzione è `--privileged`. Oppure dando il capability `SYS_ADMIN`.

Una strategia migliore è di togliere tutti i capabilities e di aggiungere poi solo quelli necessari:
```
$ docker run --cap-drop all --cap-add CHOWN debian \
  chown 100 /tmp
```
Quelli necessari si possono scoprire di solito solo provando e riprovando.
