Search:
```
ftp
http
tcp && tcp.port==80
http.request.method == "POST"
http.response.phrase == "OK"
ftp.response.code == 530
frame matches "(?i)unauthorized"
```