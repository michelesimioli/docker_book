## Dockerfiles in Rete

In un mondo realistico moderno:
* il sofware è disponibile in formato Open Source
* per molti applicativi i Dockerfile reperibili in rete
* le immagini Docker già pronte possono avere problemi di sicurezza

Supponiamo di voler generare un'immagine di Nginx, un web server molto usato.

La ricerca Google "nginx docker" ci porta al sito `https://hub.docker.com/_/nginx/`, che Ha subito una lista di Dockerfile disponibili.

Scegliamo il link di `stable/alpine/Dockerfile` e siamo diretti ad un sito GitHub con una URL non scrivibile perchè contiene un identificativo sessione: ci si può proprio solo arrivare dal sito precedente.

Questo non è tutto quello che ci serve, ma solo parte di un repository GitHub. Torniamo su di un paio di cartelle, fino a `docker-nginx`, che è la radice del progetto.

Dal bottone `Clone or Download` otteniamo la URL del repository che è https://github.com/nginxinc/docker-nginx.git.

Nella nostra directory di esercizio diamo il comando:
```
git clone https://github.com/nginxinc/docker-nginx.git
```
Viene scaricato il progetto. Andiamo nella sottodirectory giusta:
```
cd docker-nginx/stable/alpine
```

Questa directory è il contesto di generazione dell'immagine.

Il Dockerfile non è per niente semplice, come ci si poteva immaginare. Le operazioni che compie, tra l'altre, sono:

1. Parte da un'immagine Alpine
2. Definisce una grossa variabile d'ambiente con le opzioni di configurazione
3. Aggiunge gruppo ed utente per nginx
4. Scarica i pacchetti Alpine necessari
5. Scarica il tar con i sorgenti di Nginx e la firma crittografica
6. Verifica la firma
7. Compila i sorgenti con la procedura Open Source
8. Compie altre operazioni di manutenzione Alpine
9. Copia due file di configurazione
10. Apre la porta 80
11. Determina il segnale di stop
12. Definisce il comando di lancio del container

Tutto quello che dobbiamo fare noi è:
```
docker build -t alp-nginx .
```
Occorre un po' di tempo per la compilazione dei sorgenti.

Non dovrebbe esserci nemmeno un errore, nemmeno un warning. Questo però non è un miracolo Docker, ma Open Source.

Al termine listiamo le nostre immagini:
```
docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
alp-nginx           latest              056f79000432        22 minutes ago      15.6 MB
...
```

Un'immagine per un server web in 15.6 MB. Questo è un miracolo Docker. E Alpine.

Testiamolo:
```
docker run -d --name nginx --rm alp-nginx

docker inspect nginx

curl 172.17.0.2
```

Funziona, con la pagina di default.

Ora rimane il problema di passare pagine statiche al contenitore.
