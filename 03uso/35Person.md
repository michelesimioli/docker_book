# Personalizzazioni

Formato del rapporto:
```
docker ps -a --format $FORMAT
```
ove:
```
export FORMAT="\nID\t{{.ID}}\nIMAGE\t{{.Image}}\nCOMMAND\t{{.Command}}\nCREATED\t{{.RunningFor}}\nSTATUS\t{{.Status}}\nPORTS\t{{.Ports}}\nNAMES\t{{.Names}}\n"
```
Anche:
```
alias dkp='docker ps -a --format $FORMAT'
alias dki='docker images'
alias dkr='docker rm'
```
Da inserirsi in `.profile` (ubuntu) o `.bash_profile` (Red Hat).
