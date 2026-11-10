
``` bash
sudo nmap -p- --reason -Pn -n -T1 -sS -oA empresa_allports_$(date +%Y%m%d__%H%M) $target
```
If you are `root` Nmap by default used **`-sS`**
If you aren't `root` Nmap by default used **`-sT`**
``` bash
sudo nmap -p- --open -n -Pn -T4 -vvv $target
```
- **By default, if you run nmap by passing only the IP address of your target, the tool automatically scans the 1000 most well-known ports**
- `-p-`: Scans the entire port range from 1 to 65535.
- `--open`: Only shows ports with an "open" status, ignoring closed or filtered ones.
- `-sS`: Performs a **TCP SYN Scan** (Half-open scan). It's fast and stealthy as it doesn't complete the full TCP handshake.
- `-n`: Disables **DNS resolution**. This prevents Nmap from wasting time trying to find hostnames for the IP.
- `-Pn`: Treats the host as online and **skips host discovery** (No ICMP ping).
- `-vvv`: Triple **verbose** mode. It prints open ports to the terminal the moment they are discovered.
- `T<0-5>` (Timing Templates): These flags control the overall speed, aggressiveness, and packet timing of an Nmap scan.  They automatically adjust timeouts and scan delays to fit different network conditions and auditing goals.
- `-min-rate 5000`: Sets the minimum packet execution rate to 5000 packets per second.
- `--scan-delay $time`: use of --scan-delay is to evade threshold based intrusion detection and prevention systems (IDS/IPS).

``` bash
sudo nmap -p$ports -sCV -T4 $target
```
- `-sC`: Runs **Default Scripts**. It uses the Nmap Scripting Engine (NSE) to perform common security checks and basic enumeration.
- `-sV`: Enables **Version Detection**. It probes open ports to determine what service and version are actually running.

``` bash
nmap -sV -script banner demo.ine.local

sudo nmap -p$ports -sV --version-intensity <1-9> -T4 $target

sudo nmap -p$ports -n -Pn -sU $target

sudo nmap -O --osscan-guess $target
```