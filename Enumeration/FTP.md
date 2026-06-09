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

Read banner
```shell
nc -nv $TARGET 21
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