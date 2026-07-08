### Scan all port
``` bash
sudo nmap -p- --reason -Pn -n -T1 -sS -oA empresa_allports_$(date +%Y%m%d__%H%M)  <IP>
```

``` bash
sudo nmap -p- --open -sS -n -Pn -T4 -vvv <IP>
```
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
- `-sC`: Runs **Default Scripts**. It uses the Nmap Scripting Engine (NSE) to perform common security checks and basic enumeration.
- `-sV`: Enables **Version Detection**. It probes open ports to determine what service and version are actually running (e.g., Apache 2.4.41).
- `-p[ports]`: Scans only the specific ports identified in Phase 1 to save time.

**By default, if you run nmap by passing only the IP address of your target, the tool automatically scans the 1000 most well-known ports**

### Host Discovery Scan
``` bash
sudo nmap 10.129.2.18 -PE -sn 
```
- `-PE`: -PE/PP/PM- ICMP echo, timestamp, and netmask request discovery probes
- `-sn`: Ping Scan - disable port scan
- `--disable-arp-ping`
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
nmap -p $ports --script="ftp-*" $target
nmap -p $ports --script smtp-enum-users $target
nmap -p $ports --script="http-*" $target
nmap -p $ports --script="smb-vuln-*" $target
```

| **Category** | **Description**                                                                                                                         |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| `auth`       | Determination of authentication credentials.                                                                                            |
| `broadcast`  | Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans. |
| `brute`      | Executes scripts that try to log in to the respective service by brute-forcing with credentials.                                        |
| `default`    | Default scripts executed by using the `-sC` option.                                                                                     |
| `discovery`  | Evaluation of accessible services.                                                                                                      |
| `dos`        | These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services.              |
| `exploit`    | This category of scripts tries to exploit known vulnerabilities for the scanned port.                                                   |
| `external`   | Scripts that use external services for further processing.                                                                              |
| `fuzzer`     | This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time.     |
| `intrusive`  | Intrusive scripts that could negatively affect the target system.                                                                       |
| `malware`    | Checks if some malware infects the target system.                                                                                       |
| `safe`       | Defensive scripts that do not perform intrusive and destructive access.                                                                 |
| `version`    | Extension for service detection.                                                                                                        |
| `vuln`       | Identification of specific vulnerabilities.                                                                                             |
```bash
sudo nmap <target> --script <category>
```
## Style sheets
	With the XML output, we can easily create HTML reports that are easy to read, even for non-technical people. This is later very useful for documentation, as it presents our results in a detailed and clear way. To convert the stored results from XML format to HTML, we can use the tool `xsltproc`.

```bash
xsltproc target.xml -o target.html
```
![](nmap_xml_browser.png)

## Firewall and IDS/IPS Evasion

Nmap's TCP ACK scan (`-sA`) method is much harder to filter for firewalls and IDS/IPS systems than regular SYN (`-sS`) or Connect scans (`sT`) because they only send a TCP packet with only the `ACK` flag. When a port is closed or open, the host must respond with an `RST` flag. Unlike outgoing connections, all connection attempts (with the `SYN` flag) from external networks are usually blocked by firewalls. However, the packets with the `ACK` flag are often passed by the firewall because the firewall cannot determine whether the connection was first established from the external network or the internal network.

```bash
sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace
```

|**Scanning Options**|**Description**|
|---|---|
|`10.129.2.28`|Scans the specified target.|
|`-p 21,22,25`|Scans only the specified ports.|
|`-sS`|Performs SYN scan on specified ports.|
|`-sA`|Performs ACK scan on specified ports.|
|`-Pn`|Disables ICMP Echo requests.|
|`-n`|Disables DNS resolution.|
|`--disable-arp-ping`|Disables ARP ping.|
|`--packet-trace`|Shows all packets sent and received.|
## Detect IDS/IPS
Unlike firewalls and their rules, the detection of IDS/IPS systems is much more difficult because these are passive traffic monitoring systems. `IDS systems` examine all connections between hosts. If the IDS finds packets containing the defined contents or specifications, the administrator is notified and takes appropriate action in the worst case.
## Decoys
There are cases in which administrators block specific subnets from different regions in principle. This prevents any access to the target network. Another example is when IPS should block us. For this reason, the Decoy scanning method (`-D`) is the right choice. With this method, Nmap generates various random IP addresses inserted into the IP header to disguise the origin of the packet sent. With this method, we can generate random (`RND`) a specific number (for example: `5`) of IP addresses separated by a colon (`:`). Our real IP address is then randomly placed between the generated IP addresses.

```bash
sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5
```

| **Scanning Options** | **Description**                                                                            |
| -------------------- | ------------------------------------------------------------------------------------------ |
| `10.129.2.28`        | Scans the specified target.                                                                |
| `-p 80`              | Scans only the specified ports.                                                            |
| `-sS`                | Performs SYN scan on specified ports.                                                      |
| `-Pn`                | Disables ICMP Echo requests.                                                               |
| `-n`                 | Disables DNS resolution.                                                                   |
| `--disable-arp-ping` | Disables ARP ping.                                                                         |
| `--packet-trace`     | Shows all packets sent and received.                                                       |
| `-D RND:5`           | Generates five random IP addresses that indicates the source IP the connection comes from. |
#### Scan by Using Different Source IP
```bash
sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0
```

| **Scanning Options** | **Description**                                        |
| -------------------- | ------------------------------------------------------ |
| `10.129.2.28`        | Scans the specified target.                            |
| `-n`                 | Disables DNS resolution.                               |
| `-Pn`                | Disables ICMP Echo requests.                           |
| `-p 445`             | Scans only the specified ports.                        |
| `-O`                 | Performs operation system detection scan.              |
| `-S`                 | Scans the target by using different source IP address. |
| `10.129.2.200`       | Specifies the source IP address.                       |
| `-e tun0`            | Sends all requests through the specified interface.    |
## DNS Proxying
By default, `Nmap` performs a reverse DNS resolution unless otherwise specified to find more important information about our target. These DNS queries are also passed in most cases because the given web server is supposed to be found and visited. However, `Nmap` still gives us a way to specify DNS servers ourselves (`--dns-server <ns>,<ns>`). This method could be fundamental to us if we are in a demilitarized zone (`DMZ`). The company's DNS servers are usually more trusted than those from the Internet. So, for example, we could use them to interact with the hosts of the internal network. As another example, we can use `TCP port 53` as a source port (`--source-port`) for our scans. If the administrator uses the firewall to control this port and does not filter IDS/IPS properly, our TCP packets will be trusted and passed through.

#### SYN-Scan of a Filtered Port
```bash
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace
```
#### SYN-Scan From DNS Port
```bash
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53
```

|**Scanning Options**|**Description**|
|---|---|
|`10.129.2.28`|Scans the specified target.|
|`-p 50000`|Scans only the specified ports.|
|`-sS`|Performs SYN scan on specified ports.|
|`-Pn`|Disables ICMP Echo requests.|
|`-n`|Disables DNS resolution.|
|`--disable-arp-ping`|Disables ARP ping.|
|`--packet-trace`|Shows all packets sent and received.|
|`--source-port 53`|Performs the scans from specified source port.|
Now that we have found out that the firewall accepts `TCP port 53`, it is very likely that IDS/IPS filters might also be configured much weaker than others. We can test this by trying to connect to this port by using `Netcat`.
#### Connect To The Filtered Port
```bash
MarcosV999@htb[/htb]$ ncat -nv --source-port 53 10.129.2.28 50000
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Connected to 10.129.2.28:50000.
220 ProFTPd
```


```bash
sudo nmap -p- -g53 --max-retries=1 -Pn --disable-arp-ping $target
```

| Flag                     | Long Name          | Description                                               |
| :----------------------- | :----------------- | :-------------------------------------------------------- |
| **`-PE`**                | `--icmp-echo`      | Uses ICMP Echo Request (Standard Ping) to discover hosts. |
| **`-PP`**                | `--icmp-timestamp` | Uses ICMP Timestamp Request to discover hosts.            |
| **`-PM`**                | `--icmp-netmask`   | Uses ICMP Address Mask Request to discover hosts.         |
| **`-Pn`**                | `--no-ping`        | Skips host discovery; assumes all targets are online.     |
| **`-sn`**                | `--no-portscan`    | Host discovery only; disables port scanning.              |
| **`-sV`**                | `--version-detect` | Probes open ports to determine service/version info.      |
| **`-p-`**                | `--all-ports`      | Scans all 65,535 TCP ports.                               |
| **`-F`**                 | `--fast`           | Scans only the top 100 most common ports.                 |
| **`-g`**                 | `--source-port`    | Sets a specific source port (e.g., `-g 53` for DNS).      |
| **`--disable-arp-ping`** | `--disable-arp`    | Forces Nmap to skip ARP discovery in local networks.      |
| **`--max-retries`**      | `--max-retries`    | Limits the number of retransmission attempts for probes.  |
