# Dati Persistenti

Evidentemente dati che sopravvivono allo stop o alla rimozione di un container devono essere fuori dal container.

### Esempio: Condivisione di una Directory

Download di un web server:
#### `docker pull nginx`

`Nginx` è un server web alternativo ad Apache, attiva naturalmente la porta 80, e serve i file statici della sua directory `/usr/share/nginx/html`.

Noi creiamo una directory locale da mappare a quest'ultima.

Creare una directory web1 con il file `index.html`:
```bash
mkdir web1
cd web1
pwd
vi index.html
```
ed inserirvi:
```html
<html><body>
<h1>FUNZIONA</h1>
</body></html>
```

Lanciare un container che monta la directory:

##### `docker run -d -p -v ~/web1:/usr/share/nginx/html nginx`

**Attenzione**: il percorso lato contenitore deve essere **assoluto**.

Verificare l'indirizzo `IP` del container:
### `docker inspect` _ID_

Provare il collegamento via browser:
```
curl IP
```
In questo esempio:
```
curl 172.17.0.2
```

### Condivisione di un File

Si può condividere un singolo file:
```
cd
touch example.txt
```
Il file deve esistere _prima_ di condividerlo al container o docker pensa che sia una directory.
```sh
docker run --rm -ti --name sharing -v ~/example.txt:/tmp/example alpine sh
```
Quindi dalla shell di _alpine_:
```
echo "hello" > /tmp/example
exit
```
E poi dal sistema host (il container è morto nel frattempo, dopo la `exit`):
```
cat example.txt
```
e si nota che il file è cambiato.

## Modifica di Immagini

Lanciare un nginx con un nome assegnato:
```
docker run -d --name my_nginxtemp1 nginx

docker ps

docker inspect my_nginxtemp1 | grep IP
```
Aprire il browser all'indirizzo IP indicato, p.es.:
```
curl 172.17.0.2
```
Connettersi al container con una shell:
```
docker exec -i -t my_nginxtemp1 bash
```
Abbiamo una shell di root. Verificare:
```
cd /usr/share/nginx/html
ls
```
Sostituire index.html:
```
echo '<h1>CUSTOMIZED!</h1>' > index.html

```
Uscire dal container:
```
exit
```
Verificare:
```
curl 172.17.0.2
```
Creare una copia del container corrente come nuova immagine:
##### `docker commit my_nginxtemp1 my_nginx1`

Verificare:
```
docker images
```
Rimuovere il vecchio container e lanciarne uno nuovo con la nuova immagine:
```
docker rm -f my_nginxtemp1
docker run -d --name nuova1 my_nginx1
```

Verificare l'indirizzo IP
```
docker inspect nuova1 | grep IP
```
Verificare:
```
curl 172.17.0.2
```
E si nota che i cambiamenti sono stati inglobati nella nuova immagine.

