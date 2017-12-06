## Origini ed Evoluzione

Docker è un ambiente per isolamento delle risorse necessarie ad un applicativo all'interno di un contenitore, soluzione più leggera rispetto ad una macchina virtuale.

E' in sviluppo circa dal 2013, da idee iniziali di Solomon Hykes.

![Hykes](/gitbook/images/hykes.png)

Di recente ha avuto grosse contribuzioni dalla Red Hat.

Richiede un'architettura Linux a 64 bit con Kernel 2.6+. E' basato sulla feature architettonica detta **cgroups**, che altri sistemi operativi non hanno. E' disponibile anche per Windows e Mac, ma questi sistemi eseguono Docker all'interno di una macchina virtuale Linux.

La versione corrente è la 1.12 (6/16), ma è in continua evoluzione.

La licenza è Apache (open).

E' scritto nel linguaggio di programmazione **Go**, che a sua volta è un'altra grossa innovazione tecnologica.

Compie la virtualizzazione entro il sistema operativo ospitante, non entro sistemi operativi ospiti.

Direzione futura: software indipendente dal sistema operativo.

**Attenzioni:**

* Docker non è una macchina virtuale (il kernel è condiviso)
* Docker non è automaticamente scalabile
* Al momento pochi veramente usano docker in produzione
* I containers di Linux hanno ancora alcuni problemi di sicurezza
* La calibrazione delle prestazioni è ancora un po' indietro

