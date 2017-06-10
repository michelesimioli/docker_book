## Docker Registry

![Registry](../gitbook/images/registry.png)

Installazione:
```
docker pull registry
```
Start:
```
docker run -d -p 5000:5000 --restart=always --name registry registry
```
```
docker tag vim-go localhost:5000/vim-go
docker push localhost:5000/vim-go
docker pull localhost:5000/vim-go
```
Quando il contenitore è rimosso, però, tutti i dati vengono persi.

#### Appoggiarsi ad una directory locale

```
cd ~/ex
mkdir data
docker run -d -p 5000:5000 --restart=always --name registry -v `pwd`/data:/var/lib/registry registry
```
