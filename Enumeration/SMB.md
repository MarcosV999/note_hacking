# Smbclient
Comando para listar carp
``` bash
smbclient -N -L //$target/
```
**Flag Breakdown:**
- `-N`: No password.
- `-L`: This option allows you to look at what services are available on a server.
# NetExec
```shell
nxc smb $target -u '' -p ''
```
# Enum4linux
```shell
enum4linux -U $target -R
enum4linux -U -o $target
```
# Rpcclient
```shell
rpcclient -U "" -N $target
```

