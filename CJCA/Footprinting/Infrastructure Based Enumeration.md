# Domain Information
The first point of presence on the Internet may be the `SSL certificate` from the company's main website that we can examine. Often, such a certificate includes more than just a subdomain, and this means that the certificate is used for several domains, and these are most likely still active.
Another source to find more subdomains is [crt.sh](https://crt.sh/).
#### Certificate Transparency
```bash
MarcosV999@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .
# If needed, we can also have them filtered by the unique subdomains.
MarcosV999@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u
```
#### Company Hosted Servers
```bash
MarcosV999@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done
```
#### Shodan - IP List
```bash
MarcosV999@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done
MarcosV999@htb[/htb]$ for i in $(cat ip-addresses.txt);do shodan host $i;done
```
#### DNS Records
```bash
MarcosV999@htb[/htb]$ dig any inlanefreight.com
```
- `A` records: We recognize the IP addresses that point to a specific (sub)domain through the A record. Here we only see one that we already know.
- `MX` records: The mail server records show us which mail server is responsible for managing the emails for the company. 
- `NS` records: These kinds of records show which name servers are used to resolve the FQDN to IP addresses. Most hosting providers use their own name servers, making it easier to identify the hosting provider.
- `TXT` records: this type of record often contains verification keys for different third-party providers and other security aspects of DNS, such as [SPF](https://datatracker.ietf.org/doc/html/rfc7208), [DMARC](https://datatracker.ietf.org/doc/html/rfc7489), and [DKIM](https://datatracker.ietf.org/doc/html/rfc6376), which are responsible for verifying and confirming the origin of the emails sent.

# Cloud Resources
There are many different ways to find such cloud storage. One of the easiest and most used is Google search combined with Google Dorks. For example, we can use the Google Dorks `inurl:` and `intext:` to narrow our search to specific terms.

```
intext: ####  inurl: amazonaws.com
intext: ####  inurl: blob.core.windows.net
```
Such content is also often included in the source code of the web pages, from where the images, JavaScript codes, or CSS are loaded. This procedure often relieves the web server and does not store unnecessary content.
![](Pasted%20image%2020260627220748.png)
#### Domain.Glass Results
Third-party providers such as [domain.glass](https://domain.glass) can also tell us a lot about the company's infrastructure.
#### GrayHatWarfare Results
Another very useful provider is [GrayHatWarfare](https://buckets.grayhatwarfare.com). We can do many different searches, discover AWS, Azure, and GCP cloud storage, and even sort and filter by file format.

# Staff
Employees can be identified on various business networks such as [LinkedIn](https://www.linkedin.com) or [Xing](https://www.xing.de). Job postings from companies can also tell us a lot about their infrastructure and give us clues about what we should be looking for.
#### LinkedIn - Employee #1 About
We try to make business contacts on social media sites and prove to visitors what skills we bring to the table, which inevitably leads to us sharing with the public what we know and what we have learned so far. Companies always hire employees whose skills they can use and apply to the business.
#### Github
Showing our projects can, of course, be of great advantage to make new business contacts and possibly even get a new job, but on the other hand, it can lead to mistakes that will be very difficult to fix. For example, in one of the files, we can discover the employee's personal email address, and upon deeper investigation, the web application has a hardcoded [JWT token](https://jwt.io/).
#### LinkedIn - Employee #2 Career
[LinkedIn](https://www.linkedin.com) offers a comprehensive search for employed, sorted by connections, locations, companies, school, industry, profile language, services, names, titles, and more. Understandably, the more detailed information we provide there, the fewer results we get.


