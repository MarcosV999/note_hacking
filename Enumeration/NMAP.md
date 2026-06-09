### Scan all port
``` bash
sudo nmap -p- --reason -Pn -n -T1 -sS -oA empresa_allports_$(date +%Y%m%d__%H%M)  <IP>
```

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
- `T<0-5>` (Timing Templates): These flags control the overall speed, aggressiveness, and packet timing of an Nmap scan.  They automatically adjust timeouts and scan delays to fit different network conditions and auditing goals.
- `-min-rate 5000`: Sets the minimum packet execution rate to 5000 packets per second.

``` bash
sudo nmap -sCV -p22,80,445 <IP>
```
**Flag Breakdown:**
- `-sC`: Runs **Default Scripts**. It uses the Nmap Scripting Engine (NSE) to perform common security checks and basic enumeration.
- `-sV`: Enables **Version Detection**. It probes open ports to determine what service and version are actually running (e.g., Apache 2.4.41).
- `-p[ports]`: Scans only the specific ports identified in Phase 1 to save time.

**By default, if you run nmap by passing only the IP address of your target, the tool automatically scans the 1000 most well-known ports**

**Host Discovery Scan ( -sn )**

Cuando un servicio esta  
nmap -p 8443 --script http-title --script-args http.tls=true 10.129.60.191

El buscador de CVEs por base de datos: --script vulners
Es para ver las vulnerabilidades de las versiones que encontro el -sV:
```
nmap -sV --script vulners <IP>
```
>nota: es seguro de usar --script vulners a diferencia de --script=vuln que puede llegar a ser peligroso.


TCP Null Scan (-sN)
Es un tipo de escaneo "sigiloso" que intenta evadir firewalls enviando paquetes que técnicamente no deberían existir según las reglas normales de internet.
El Null Scan envía un paquete con **todas las banderas en cero (vacías)**.
```
nmap -p 8443 --script http-title --script-args http.tls=true 10.129.60.191
```

Nmap Scripting Engine
```shell
nmap --script-help "mysql-*"
nmap --script-help "http-*"
nmap --script-help "smb-vuln-*"
```

Using in specific open port:
```shell
nmap -p $PORTS --script="ftp-*" $TARGET
nmap -p $PORTS --script="http-*" $TARGET
nmap -p $PORTS --script="smb-vuln-*" $TARGET
```

PASS Buck3tH4TF0RM3!\r\n