## Dati Persistenti

Evidentemente dati che sopravvivono allo stop o alla rimozione di un container devono essere fuori dal container.

#### Esempio

Download di un web server
```
docker pull nginx
```
`Nginx` è un server web alternativo ad Apache, attiva naturalmente la porta 80, e serve i file statici della sua directory `/usr/share/nginx/html`.

Noi creiamo una directory locale da mappare a quest'ultima.

Creare una directory web1 con il file `index.html`:
```
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
Verificare l'indirizzo IP del container:
```
docker inspect ID
```

Provare il collegamento via browser:
```
curl IP
```
In questo esempio:
```
curl 172.17.0.2

Si può condividere un singolo file:
```
cd
touch example.txt
```
Il file deve esistere prima di condividerlo al container o docker pensa che sia una directory.
```
docker run --rm -ti --name sharing -v ~/example.txt:/tmp/example alpine sh
echo "hello" > /tmp/example
exit
```
E poi dal sistema host (i container è morto nel frattempo):
```
cat example.txt
```
e si nota che il file è cambiato.

