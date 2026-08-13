
```shell
ffuf -c -w /usr/share/dirb/wordlists/common.txt -u http://$target/FUZZ 2>/dev/null

<<<<<<< HEAD
ffuf -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-small.txt -mc 200,302,301,304 -e .php,.html,.xml,.txt,.aspx,.js,.py -u http://$target/FUZZ 
## Extension Fuzzing
ffuf -w /usr/share/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
## Page Fuzzing
ffuf -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-small.txt -u http://SERVER_IP:PORT/FUZZ.php
=======
ffuf -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-small.txt -u http://$target/FUZZ 2>/dev/null

ffuf -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-small.txt -e .php,.html,.xml,.txt,.aspx,.js,.py -u http://$target/FUZZ 2>/dev/null

ffuf -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-small.txt -mc 200,302,301,304 -e .php,.html,.xml,.txt,.aspx,.js,.py -u http://$target/FUZZ 2>/dev/null
## Extension Fuzzing
ffuf -w /usr/share/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
## Page Fuzzing
ffuf -s -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-small.txt -u http://SERVER_IP:PORT/FUZZ.php
>>>>>>> 515d937 (update commands)
## Recursive Scanning
ffuf -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-small.txt -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v
```

``` bash
## Sub-domain Fuzzing
ffuf -s -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u https://FUZZ.inlanefreight.htb/
```

```bash
## Vhost
ffuf -s -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://$target:PORT/ -H "Host:FUZZ.thetoppers.htb"

ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs xxx #excluye el size xxx
```

```bash
## Parameter Fuzzing - GET
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u http://$target:PORT/index.php?FUZZ=key -fs xxx #excluye el size
## Parameter Fuzzing - POST
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u http://$target:PORT/index.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx #excluye el size
## Parameter Fuzzing - POST - username
ffuf -w /opt/useful/seclists/Usernames/Names/names.txt:FUZZ -u http://faculty.academy.htb:STMPO/courses/linux-security.php7 -X POST -d 'username=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -t 100
ffuf -w /usr/share/seclists/Usernames/Names/names.txt:FUZZ_USER -w /usr/share/wordlists/rockyou.txt:FUZZ_PASS -u http://10.67.152.245/login -X POST -d 'username=FUZZ_USER&password=FUZZ_PASS' -H 'Content-Type: application/x-www-form-urlencoded' -t 100
## the result de below command is 'id' Let's see what we get if we send a POST request with the id parameter. We can do that with curl, as follows:
curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'
```

```bash
## DNS Records
echo "10.129.25.184 thetoppers.htb" | sudo tee -a /etc/hosts
sudo sh -c 'echo "SERVER_IP  academy.htb" >> /etc/hosts'
sudo bash -c 'echo "SERVER_IP test.academy.htb archive.academy.htb faculty.academy.htb" >> /etc/hosts'
```