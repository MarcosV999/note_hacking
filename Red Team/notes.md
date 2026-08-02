![](Pasted%20image%2020260725205532.png)

```bash
nmap -n -Pn -p <only one port> --data-length 24 --ttl 128 -sV --scripts-args http.useragent='' $target
```

useragent: https://useragents.io/
