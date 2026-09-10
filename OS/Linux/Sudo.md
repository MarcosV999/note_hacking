## Swicth User
``` bash
sudo su -
```
es la forma correcta para looguearte como root, con el simbolo "-" indicas que te estas logueando como root, --login = -  

## See sudo user
``` bash
sudo -l
```
El comando sirve para listar los privilegios y comandos permitidos que tiene un usuario

/etc/sudoers: Este archivo es el "cerebro" del comando `sudo`. Define con total precisión **qué usuarios** tienen permitido ejecutar **qué comandos**, en **qué máquinas** del entorno y bajo la identidad de **qué cuentas** (que casi siempre es el superusuario `root`).