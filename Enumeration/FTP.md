Connect to FTP
```shell
ftp <target_ip>
```

Download file to local
```shell
get <name_file>
```

Specific scripts
```shell
nmap -p21 --script="ftp-*" $TARGET
```

Recursive Listing
```shell
ftp> ls -R
```

Read banner
```shell
nc -nv $TARGET 21
```

Download All Available Files
```shell
wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
```

Metaexploit:
```
search auxiliary/scanner/ftp/
```

Hydra:
Si durante tu enumeración encontraste nombres de usuarios válidos pero no tienes sus contraseñas, puedes auditar la robustez de las credenciales en el servicio FTP:
```
hydra -l usuario -P /usr/share/wordlists/rockyou.txt $TARGET ftp
```
(Puedes cambiar -l usuario por -L usuarios.txt si tienes una lista de posibles nombres).

## Service Interaction
It looks slightly different if the FTP server runs with TLS/SSL encryption. Because then we need a client that can handle TLS/SSL. For this, we can use the client `openssl` and communicate with the FTP server. The good thing about using `openssl` is that we can see the SSL certificate, which can also be helpful.
```shell
openssl s_client -connect 10.129.14.136:21 -starttls ftp
```
This is because the SSL certificate allows us to recognize the `hostname`, for example, and in most cases also an `email address` for the organization or company. In addition, if the company has several locations worldwide, certificates can also be created for specific locations, which can also be identified using the SSL certificate.
## Dangerous Settings
| **Setting**                    | **Description**                                                                    |
| ------------------------------ | ---------------------------------------------------------------------------------- |
| `anonymous_enable=YES`         | Allowing anonymous login?                                                          |
| `anon_upload_enable=YES`       | Allowing anonymous to upload files?                                                |
| `anon_mkdir_write_enable=YES`  | Allowing anonymous to create new directories?                                      |
| `no_anon_password=YES`         | Do not ask anonymous for password?                                                 |
| `anon_root=/home/username/ftp` | Directory for anonymous.                                                           |
| `write_enable=YES`             | Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE? |
Some commands should be used occasionally, as these will make the server show us more information that we can use for our purposes. These commands include `debug` and `trace`.

