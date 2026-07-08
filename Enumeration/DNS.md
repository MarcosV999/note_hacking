| **DNS Record** | **Description**                                                                                                                                                                                                                                                                             |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `A`            | Returns an IPv4 address of the requested domain as a result.                                                                                                                                                                                                                                |
| `AAAA`         | Returns an IPv6 address of the requested domain.                                                                                                                                                                                                                                            |
| `MX`           | Returns the responsible mail servers as a result.                                                                                                                                                                                                                                           |
| `NS`           | Returns the DNS servers (nameservers) of the domain.                                                                                                                                                                                                                                        |
| `TXT`          | This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.                                           |
| `CNAME`        | This record serves as an alias for another domain name. If you want the domain [www.hackthebox.eu](http://www.hackthebox.eu) to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for [www.hackthebox.eu](http://www.hackthebox.eu). |
| `PTR`          | The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.                                                                                                                                                                               |
| `SOA`          | Provides information about the corresponding DNS zone and email address of the administrative contact.                                                                                                                                                                                      |
All DNS servers work with three different types of configuration files:
1. local DNS configuration files
2. zone files
3. reverse name resolution files

The DNS server [Bind9](https://www.isc.org/bind/) is very often used on Linux-based distributions. Its local configuration file (`named.conf`) is roughly divided into two sections, firstly the options section for general settings and secondly the zone entries for the individual domains. The local configuration files are usually:
- `named.conf.local`
- `named.conf.options`
- `named.conf.log`

`Fully Qualified Domain Name` (`FQDN`)
SecurityTrails provides a short [list](https://web.archive.org/web/20250329174745/https://securitytrails.com/blog/most-popular-types-dns-attacks) of the most popular attacks on DNS servers.
## Footprinting the Service
We do this using the NS record and the specification of the DNS server we want to query using the `@` character. This is because if there are other DNS servers, we can also use them and query the records. However, other DNS servers may be configured differently and, in addition, may be permanent for other zones.
#### DIG - NS Query
```shell
dig ns inlanefreight.htb @10.129.14.128
```
#### DIG - Version Query
Sometimes it is also possible to query a DNS server's version using a class CHAOS query and type TXT. However, this entry must exist on the DNS server.
```bash
dig CH TXT version.bind 10.129.120.85
```
#### DIG - ANY Query
We can use the option `ANY` to view all available records. This will cause the server to show us all available entries that it is willing to disclose. It is important to note that not all entries from the zones will be shown.
```shell
dig any inlanefreight.htb @10.129.14.128
```

`Zone transfer` refers to the transfer of zones to another server in DNS, which generally happens over TCP port 53. This procedure is abbreviated `Asynchronous Full Transfer Zone` (`AXFR`).
#### DIG - AXFR Zone Transfer
`Zone transfer` refers to the transfer of zones to another server in DNS, which generally happens over TCP port 53. This procedure is abbreviated `Asynchronous Full Transfer Zone` (`AXFR`). Since a DNS failure usually has severe consequences for a company, the zone file is almost invariably kept identical on several name servers. When changes are made, it must be ensured that all servers have the same data. Synchronization between the servers involved is realized by zone transfer. Using a secret key `rndc-key`, which we have seen initially in the default configuration, the servers make sure that they communicate with their own master or slave. Zone transfer involves the mere transfer of files or records and the detection of discrepancies in the data sets of the servers involved.
```
dig axfr inlanefreight.htb @10.129.14.128
```
#### DIG - AXFR Zone Transfer - Internal
```
dig axfr internal.inlanefreight.htb @10.129.14.128
```

The individual `A` records with the hostnames can also be found out with the help of a brute-force attack. To do this, we need a list of possible hostnames, which we use to send the requests in order. Such lists are provided, for example, by [SecLists](https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/subdomains-top1million-5000.txt).

An option would be to execute a `for-loop` in Bash that lists these entries and sends the corresponding query to the desired DNS server.
#### Subdomain Brute Forcing
```shell
for sub in $(cat /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

ns.inlanefreight.htb.   604800  IN      A       10.129.34.136
mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201
app.inlanefreight.htb.  604800  IN      A       10.129.18.15
```
Many different tools can be used for this, and most of them work in the same way. One of these tools is, for example [DNSenum](https://github.com/fwaeytens/dnsenum).
```shell
dnsenum --dnsserver $target --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/wordlists/seclists/Discovery/DNS/subdomains-spanish.txt inlanefreight.htb
```

# Footprinting Lab - Easy
```
nmap -A 10.129.141.200
dig AXFR inlanefreight.htb @$target
```
The one that stands out is `internal.inlanefreight.htb`, therefore, students need to add it to `/etc/hosts`:
```
sudo sh -c 'echo "STMIP internal.inlanefreight.htb" >> /etc/hosts'
```
Students need to run `dnsenum` on `internal.inlanefreight.htb`, finding the subdomain `ftp.internal.inlanefreight.htb`:
```shell
dnsenum --dnsserver $target --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/wordlists/seclists/Discovery/DNS/subdomains-spanish.txt inlanefreight.htb
```

```
sudo sh -c 'echo "STMIP ftp.internal.inlanefreight.htb" >> /etc/hosts'
```
