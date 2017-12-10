# Componenti di Docker

Docker ha tre componenti principali:

* **Dockerfile**: specifica del necessario per un'immagine
* **Image**: risultato della compilazione del _Dockerfile_
* **Container**: istanza di realizzazione di una image

![Componenti](/gitbook/images/compon.png)

Docker è basato su alcune caratteristiche specifiche del kernel Linux di versione superiore alla 3.10. Tali caratteristiche non vi sono in Windows o Mac, però qualcosa di simile esiste in BSD Unix e Solaris.

## Namespaces

Uno **namespace** (spazio nomi), concetto originale del C++, è un ambiente chiuso e separato in cui i simboli gestiti sono visibili. Namespaces diversi possono fare uso degli stessi simboli, che non interferiscono tra loro.

Nel kernel Linux sono definiti i _namespaces_ (spazi nomi):
* `pid` - degli identificativi dei processi (Process ID)
* `net` - delle interfacce di rete
* `ipc` - dei meccanismi di Inter Process Communication (segnali, messaggi, ecc.)
* `mnt` - dei punti di montaggio dei file system
* `uts` - degli identificativi del kernel e della versione (UTS: Unix Timesharing System)

Ogni container riceve una propria copia dei namespaces.

## Control Groups

Un gruppo di controllo (**cgroup**) è un insieme coordinato di processi con relativa autonomia. E' possibile assegnare risorse e limiti di utilizzo a ciascun _cgroup_.

Ogni _container_ è controllato da un _cgoup_ indipendente.

## Union File System

Lo **UnionFS** è un file system a strati (_layers_). Ogni strato viene gestito indipendentemente, quindi viene fornita ai processi una visione unificata di tutti gli strati.

Una _image_ è unacollezione di _layers_ all'interno di uno _UnionFS_.

Il motore **Docker Engine** (il server di docker) può basarsi su una serie di gestori UnionFS disponibili in Linux, tra cui: _AUFS_, _btrfs_, _vfs_, e _DeviceMapper_.

Lo _AUFS_ è il driver comune di tutti i tradizionali file systems di Linux, _DeviceMapper_ è usato dal **Logical Volume Manager**, _vfs_ e _btrfs_ sono più nuovi, sperimentali, e allineati alle ultimissime release di Linux.
