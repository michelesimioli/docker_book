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

**Osservazione**

Naturalmente anzichè compilarlo noi da sorgenti tramite Dockerfile, potevamo solamente fidarci dell'immagine già compilata presente su Docker Hub e digitare:
```
docker pull nginx
```
L'immagine risultante è più grande, 106 Mb, poichè questa versione contiene anche il supporto a Perl.

### Serve di Pagine Statiche con Nginx

Ora rimane il problema di passare pagine statiche al contenitore.

Leggendo la documentazione del sito web di Nginx Docker si apprende che la directory per le pagine statiche è `/usr/share/nginx/html`

Generiamo una nostra directory per le pagine statiche: `~/ex/static`

Creiamoci dentro il file `index.html`:
```html
<html>
        <head>
                <title>Mia Pagina</title>
        </head>
        <body>
                <h1>La mia pagina di indice</h1>
        </body>
</html>
```

Ora lanciamo il container, con opportuno _port publishing_ e _volume mapping_:
```
docker run --name web_server -p 8080:80 -v ~/ex/static:/usr/share/nginx/html:ro -d nginx
```

Se l'indirizzo IP della macchina host è `192.168.0.10`, puntiamo il nostro browser a `http://192.168.0.10:8080/` e dovremmo vedere la nostra pagina di indice.

Qualsiasi cambiamento effettuato sulla directory locale `~/ex/static` è immediatamente riflesso dal web server.


