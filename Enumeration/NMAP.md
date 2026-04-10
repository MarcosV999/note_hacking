``` bash
sudo nmap -p- --open -sS -n -Pn -T4 -vvv <IP>
```
**Flag Breakdown:**
- `-p-`: Scans the entire port range from 1 to 65535.
- `--open`: Only shows ports with an "open" status, ignoring closed or filtered ones.
- `-sS`: Performs a **TCP SYN Scan** (Half-open scan). It's fast and stealthy as it doesn't complete the full TCP handshake.
- `-n`: Disables **DNS resolution**. This prevents Nmap from wasting time trying to find hostnames for the IP.
- `-Pn`: Treats the host as online and skips **host discovery** (No ICMP ping).
- `-vvv`: Triple **verbose** mode. It prints open ports to the terminal the moment they are discovered.
- `-min-rate 5000`: Sets the minimum packet execution rate to 5000 packets per second, significantly speeding up the scan (T5).

``` bash
sudo nmap -sCV -p22,80,445 <IP>
```
**Flag Breakdown:**
- `-sC`: Runs **Default Scripts**. It uses the Nmap Scripting Engine (NSE) to perform common security checks and basic enumeration.
- `-sV`: Enables **Version Detection**. It probes open ports to determine what service and version are actually running (e.g., Apache 2.4.41).
- `-p[ports]`: Scans only the specific ports identified in Phase 1 to save time.



Cuando un servicio esta  
nmap -p 8443 --script http-title --script-args http.tls=true 10.129.60.191

**Host Discovery Scan ( -sn )**

Es para ver las vulnerabilidades de las versiones que encontro el -sV:
nmap -sV --script vulners <IP>

TCP Null Scan (-sN)

Es un tipo de escaneo "sigiloso" que intenta evadir firewalls enviando paquetes que técnicamente no deberían existir según las reglas normales de internet.
El Null Scan envía un paquete con **todas las banderas en cero (vacías)**.