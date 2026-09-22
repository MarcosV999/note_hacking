*Tools to Interact with this service: smbclient, nxc, SMBMap, Impacket, psexec.py, smbexec.py*
# Enum4Linux-ng - Enumeration
```bash
enum4linux-ng $target -A
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
```
# RPCclient
Remote Procedure Call (RPC)
```shell
rpcclient -U "" -N $target
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

The information we have already obtained with `rpcclient` can also be obtained using other tools. For example, the [SMBMap](https://github.com/ShawnDEvans/smbmap) and [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) tools are also widely used and helpful for the enumeration of SMB services.

# SMBmap
```shell
MarcosV999@htb[/htb]$ smbmap -H $target --no-banner

[+] Finding open SMB ports....
[+] User SMB session established on 10.129.14.128...
[+] IP: 10.129.14.128:445       Name: 10.129.14.128                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS
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