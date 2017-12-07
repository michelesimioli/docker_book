# Sicurezza

La sicurezza è una preoccupazione moderna pervasiva anche nell'uso di Docker.

Si intende qui _sicurezza esterna_ e non _continuità operativa_ la quale si occupa invece di backup e restore, disponibilità dati, meccanismi di accesso.

I problemi di sicurezza possono originare da:
* il sistema operativo host sottostante
* l'immagine scaricata

Raccomandazioni:
* scaricare solo immagini da repositories fidati
* gli applicativi nei container devono avere solo il minimo dei privilegi necessari per la loro operazione
    * non avere 'root' come default
* il kernel host deve essere mantenuto aggiornato e con le patch di sicurezza correnti
* non disabilitare difese di sicurezza sul sistema host
* compiere un'ispezione di sicurezza delle immagini

Vulnerabilità di cui preoccuparsi:
* Exploits del Kernel - tutti i contenitori condividono lo stesso kernel, quindi questo è un collo di bottiglia di sicurezza
* Diniego di Servizio - volontario o involontario: un contenitore che monopolizza le risorse di sistema può degradare pesantemente il funzionamento degli altri contenitori
* Breakout - uno hacker che _conquisti_ un contenitore può tentare di uscirne ed avere diretta visibilità delle risorse del sistema host o degli altri contenitori
* Immagini bacate - le immagini devono essere
    * di provenienza nota
    * basate su immagini base recenti e patchate
* Segreti Compromessi - un contenitore spesso ha bisogno di password e/o token di autenticazione per l'accesso ad altri contenitori, e questi possono
    * risiedere in file di configurazione o `dockerfile` insufficientemente protetti
    * essere trasmessi sulla rete in chiaro

## Namespaces

Un contenitore ha al suo interno un numero di `namespaces` privati e separatei da quello del kernel host.

Alcune proprietà non sono in `namespaces` ma ogni contenitore vede quelle del kernel:
* User IDs - se il container opera con i permessi di `root` e riesce a compiere un _breakout_, sarà `root` anche sullo host
* Kernel Keyring - con tutte le chiavi crittografiche registrate
* Moduli del Kernel
* Devices
* Ora di Sistema

