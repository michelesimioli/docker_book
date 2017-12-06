## Commit o Dockerfile

Due modi per creare un'immagine docker:
1. creare un `Dockerfile` e lanciare un `docker build`
2. run di un container, modificarlo, e lanciare un `docker commit` per generare una nuova immagine

Mentre il secondo metodo può essere utile in alcuni casi, p.es. test di un'idea, in generale non deve essere usato.

* L'immagine di base può non essere adatta ai nostri scopi e vogliamo successivamente cambiarla
    * col `commit` è praticamente impossibile
    * col `Dockerfile` basta cambiare una linea
* Col `commit` non si può riprodurre un'immagine
* Il `Dockerfile` produce _documentazione_ dei passi che sono stati seguiti nella costruzione dell'immagine
* Nel `Dockerfile` si possono inserire campi `LABEL` poi visibili col comando `docker inspect`
* E' possibile copiare il `Dockerfile` stasso nell'immagine che viene costruita, come ulteriore documentazione portatile
