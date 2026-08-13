With the help of the `Internet Message Access Protocol` (`IMAP`), access to emails from a mail server is possible. Unlike the `Post Office Protocol` (`POP3`), IMAP allows online management of emails directly on the server and supports folder structures. Thus, it is a network protocol for the online management of emails on a remote server.
#### IMAP Commands

|**Command**|**Description**|
|---|---|
|`1 LOGIN username password`|User's login.|
|`1 LIST "" *`|Lists all directories.|
|`1 CREATE "INBOX"`|Creates a mailbox with a specified name.|
|`1 DELETE "INBOX"`|Deletes a mailbox.|
|`1 RENAME "ToRead" "Important"`|Renames a mailbox.|
|`1 LSUB "" *`|Returns a subset of names from the set of names that the User has declared as being `active` or `subscribed`.|
|`1 SELECT INBOX`|Selects a mailbox so that messages in the mailbox can be accessed.|
|`1 UNSELECT INBOX`|Exits the selected mailbox.|
|`1 FETCH <ID> all`|Retrieves data associated with a message in the mailbox.|
|`1 CLOSE`|Removes all messages with the `Deleted` flag set.|
|`1 LOGOUT`|Closes the connection with the IMAP server.|
#### POP3 Commands

| **Command**     | **Description**                                             |
| --------------- | ----------------------------------------------------------- |
| `USER username` | Identifies the user.                                        |
| `PASS password` | Authentication of the user using its password.              |
| `STAT`          | Requests the number of saved emails from the server.        |
| `LIST`          | Requests from the server the number and size of all emails. |
| `RETR id`       | Requests the server to deliver the requested email by ID.   |
| `DELE id`       | Requests the server to delete the requested email by ID.    |
| `CAPA`          | Requests the server to display the server capabilities.     |
| `RSET`          | Requests the server to reset the transmitted information.   |
| `QUIT`          | Closes the connection with the POP3 server.                 |
## Footprinting the Service
By default, ports `110` and `995` are used for POP3, and ports `143` and `993` are used for IMAP. The higher ports (`993` and `995`) use TLS/SSL to encrypt the communication between the client and server.

If we successfully figure out the access credentials for one of the employees, an attacker could log in to the mail server and read or even send the individual messages.

```shell
curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd
```
If we also use the `verbose` (`-v`) option, we will see how the connection is made. From this, we can see the version of TLS used for encryption, further details of the SSL certificate, and even the banner, which will often contain the version of the mail server.
```
curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v
```

To interact with the IMAP or POP3 server over SSL, we can use `openssl`, as well as `ncat`. The commands for this would look like this:
#### OpenSSL - TLS Encrypted Interaction POP3
```shell
openssl s_client -connect 10.129.14.128:pop3s
```
#### OpenSSL - TLS Encrypted Interaction IMAP
```shell
openssl s_client -connect 10.129.14.128:imaps
```
El protocolo IMAP requiere que cada comando comience con un **identificador único** (puede ser cualquier cosa, como `A01`, `001`, `LOGIN1`, etc.).

### What is the admin email address?
```
openssl s_client -connect STMIP:imaps
tag0 LOGIN robin robin
tag1 LIST "" "*"
tag2 SELECT "DEV.DEPARTMENT.INT"
tag3 FETCH 1 (BODY[])
```

