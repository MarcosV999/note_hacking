```shell
nxc smb 10.129.27.88 -u '' -p '' 
```
SMB         10.129.27.88    445    DC01             [*] Windows 11 / Server 2025 Build 26100 x64 (name:DC01) (domain:checkpoint.htb) (signing:True) (SMBv1:None)

Attacking LDAP services:
```shell
ldapsearch -H ldap://10.129.27.88 -x -b "dc=checkpoint,dc=htb" "(objectClass=user)" | grep sAMAccountName
```

User enumeration with kerbrute:
```shell
kerbrute userenum --dc 10.129.27.88 -d checkpoint.htb /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
```

2026/06/17 16:59:52 >  Using KDC(s):
2026/06/17 16:59:52 >   10.129.27.88:88
2026/06/17 17:00:14 >  [+] VALID USERNAME:       administrator@checkpoint.htb
2026/06/17 17:02:32 >  [+] VALID USERNAME:       Administrator@checkpoint.htb
nota: ambas cuentas son la misma.


El puerto **5985 (TCP)** es uno de los puertos más importantes y codiciados cuando estás haciendo pentesting en entornos Windows. Ejecuta el servicio **WinRM (Windows Remote Management)** sobre HTTP.
Aunque Nmap te lo catalogue genéricamente como `Microsoft HTTPAPI httpd 2.0` (porque WinRM utiliza el servidor web interno de Microsoft para escuchar peticiones), su verdadera función no es alojar una página web común.