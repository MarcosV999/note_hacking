# Enum4Linux-ng - Enumeration
```bash
enum4linux-ng.py $target -A
# listar usuarios
enum4linux-ng -U <IP_DEL_SERVIDOR>
# listar recursos compartidos (_shares_)
enum4linux-ng -S <IP_DEL_SERVIDOR>
# información del sistema operativo y dominio
enum4linux-ng -O <IP_DEL_SERVIDOR>

enum4linux-ng -u "usuario" -p "contraseña" <IP_DEL_SERVIDOR>
```
# Smbclient
Command to list shares
``` bash
smbclient -L //$target/
smbclient -N -L //$target/
smbclient [-U|--user=[DOMAIN/]USERNAME%[PASSWORD]] //$target/
smbclient -L //$target -U "Caroline.Robinson%Marcos123!"
nxc smb $target -u "Caroline.Robinson" -p "Marcos123" --shares
```
- `-N`: No password.
- `-L`: This option allows you to look at what services are available on a server.
Command to connect share
```bash
smbclient //$target/<SHARE> -U '%' -N
```
- **`-U '%'`**: The username is nothing (empty)
- **`-N`**: It explicitly tells `smbclient` not to attempt to prompt for a password via the keyboard, forcing an anonymous connection.
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
# Netexec
```shell
nxc smb $target -u '' -p '' --shares
nxc smb $target -u "Caroline.Robinson" -p "Marcos123" --shares
```