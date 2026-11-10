## Techniques
- Ping Sweeps(ICMP Echo Request)
- ARP Scanning 
- TCP SYN Ping(Half Open Scan)
- UDP Ping
- TCP ACK Ping
- SYN-ACK Ping 
## Host Discovery Scan
``` bash
# Recommended
nmap -sn -PS21,22,25,80,445,3389,8080 -T4 CIDR
nmap -sn -PU137,138 -T4 CIDR
-----------------------------------------------
sudo nmap -sn CIDR --send-ip --disable-arp-ping
sudo nmap -sn -iL targets.txt
# TCP SYN
sudo nmap -sn -PS CIDR
sudo nmap -sn -PS1-100 CIDR
# TCP ACK
sudo nmap -sn -PA CIDR
# ICMP Echo Request
sudo nmap -sn -PE CIDR
# Ping Sweeps
fping -g 192.168.1.0/24 -a 2>/dev/null
```
Optimizing: `--host-timeoout 30s`
TCP Null Scan (-sN)
Es un tipo de escaneo "sigiloso" que intenta evadir firewalls enviando paquetes que técnicamente no deberían existir según las reglas normales de internet.
El Null Scan envía un paquete con **todas las banderas en cero (vacías)**.