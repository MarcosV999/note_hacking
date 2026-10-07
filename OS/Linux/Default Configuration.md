#### vsFTPd Config File
```shell
cat /etc/vsftpd.conf | grep -v "#"
```
#### SMB Config
```shell
cat /etc/samba/smb.conf | grep -v "#\|\;" 
```
#### NFS File
```shell
cat /etc/exports
```
#### DNS Configuration
- ##### Local DNS Configuration
```shell
cat /etc/bind/named.conf.local
```
- ##### Zone Files
```shell
cat /etc/bind/db.domain.com
```
- ##### Reverse Name Resolution Zone Files
```shell
cat /etc/bind/db.10.129.14
```
#### SMTP Config
```shell
cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"
```
#### SNMP Daemon Config
```shell
cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'
```
#### MySQL Config
```shell
cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | sed -r '/^\s*$/d'
```
#### SSH Config
```
cat /etc/ssh/sshd_config  | grep -v "#" | sed -r '/^\s*$/d'
```