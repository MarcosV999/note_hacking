```bash
# command CLI
host $domain
whatweb $domain
whois $target/$domain
dnsrecon -d $domain
wafw00f $target/$domain -a
theHarvester -d $domain 
# subdomain enumeration(passive)
subfinder
OWASP Amass Project
# add-on extension firefox
Wappalyzer
BuilthWith
# file search engine
robots.txt
sitemap.xml
sitemap.xml
# web
HTTrack
Netcraft
DNSdumpster
```
## Google Dorks ([link](https://www.exploit-db.com/google-hacking-database))
- **Filetype:** Directs Google to search only within the text of a particular type of file (for example, filetype:xls)
- **Inurl:** Directs Google to search only within the specified URL of a document (for example, inurl:search-text)
- **Link:** Directs Google to search within hyperlinks for a specific term (for example, link:www.domain.com)
- **Intitle:** Directs Google to search for a term within the title of a document (for example, intitle: “Index of /etc”)
```
site:example.com filetype:pdf "internal use only" OR "confidential"
site:example.com ext:xls OR ext:xlsx OR ext:csv "password" OR "email"
site:example.com ext:sql OR ext:db OR ext:tar OR ext:bak
intitle:"Index of /" "id_rsa" OR "id_rsa.pub"
intitle:"phpMyAdmin" "Welcome to phpMyAdmin"
```
## Wayback Machine
Allows you to go back in time on the Internet.
## HaveIbeenPwned([Link](https://haveibeenpwned.com/))
Leaked Password databases