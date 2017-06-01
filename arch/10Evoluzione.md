## Origini ed Evoluzione

Docker è un ambiente per isolamento delle risorse necessarie ad un applicativo all'interno di un contenitore, soluzione più leggera rispetto ad una macchina virtuale.

E' in sviluppo circa dal 2013, da idee iniziali di Solomon Hykes.

![](/gitbook/images/hykes.png)

Di recente ha avuto grosse ontribuzioni dalla Red Hat.

Richiede un'architettura Linux a 64 bit con Kernel 2.6+. E' basato sulla feature architettonica detta cgroups, che altri sistemi operativi non hanno. E' disponibile anche per Windows e Mac, ma questi sistemi eseguono Docker all'interno di una macchina virtuale Linux.

La versione corrente è la 1.12 \(6/16\), ma è in continua evoluzione

la liicenza è Apache \(open\).

E' scritto nel linguaggio di programmazione Go, che asua volta è un'altra grossa innovazione tecnologica.

Compie la virtualizzazione entro il sistema operativo ospitante, non entro sistemi operativi ospiti.

Direzione futura: software indipendente dal sistema operativo



