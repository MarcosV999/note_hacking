``` bash
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://10.129.25.184/ -H "Host:FUZZ.thetoppers.htb"
```
nota: este comando no descubrio el subdominio s3 que si lo encontro gobuster
Comando similar con gobuster:
``` bash
sudo gobuster vhost -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://thetoppers.htb --append-domain
```

sudo echo "10.129.25.184 thetoppers.htb" | sudo tee -a /etc/hosts

sudo echo "10.129.25.184 s3.thetoppers.htb" | sudo tee -a /etc/hosts
aws --endpoint=http://s3.thetoppers.htb s3 ls s3://thetoppers.htb

ffuf -u https://jobs.hackthebox.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt -e .php,.html,.txt,.log -fc 404,429,403 -p 0.1

gobuster dir -u https://jobs.hackthebox.com/ -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt -x php,html,txt,log -status-codes-blacklist 404,429
