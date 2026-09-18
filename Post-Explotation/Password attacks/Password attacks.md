
## John the Ripper(JtR)
```bash
MarcosV999@htb[/htb]$ hashid -j 193069ceb0461e1d40d216e32c79c704

Analyzing '193069ceb0461e1d40d216e32c79c704'
[+] MD2 [JtR Format: md2]
[+] MD5 [JtR Format: raw-md5]
[+] MD4 [JtR Format: raw-md4]
```

```bash
echo -n 'r0lf:$6$ues25dIanlctrWxg$nZHVz2z4kCy1760Ee28M1xtHdGoy0C2cYzZ8l2sVa1kIa8K9gAcdBP.GI6ng/qA4oaMrgElZ1Cb9OeXO4Fvy3/:0:0:Rolf Sebastian:/home/r0lf:/bin/bash' > hash.txt

john --single hash.txt
```

```bash
# rocyou=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
echo -n '193069ceb0461e1d40d216e32c79c704' > hash.txt
john --format=ripemd-128 --wordlist=$rockyou hash.txt
```

## HashCat

```bash
hashcat -a 0 -m 0 <hashes> [wordlist, rule, mask, ...]
```
In the command above:
- `-a` is used to specify the `attack mode`
- `-m` is used to specify the `hash type`
- `<hashes>` is a either a hash string, or a file containing one or more password hashes of the same type
- `[wordlist, rule, mask, ...]` is a placeholder for additional arguments that depend on the attack mode
```bash
MarcosV999@htb[/htb]$ hashid -m '$1$FNr44XZC$wQxY6HHLrgrGX0e1195k.1'

Analyzing '$1$FNr44XZC$wQxY6HHLrgrGX0e1195k.1'
[+] MD5 Crypt [Hashcat Mode: 500]
[+] Cisco-IOS(MD5) [Hashcat Mode: 500]
[+] FreeBSD MD5 [Hashcat Mode: 500]
```
The hashcat website hosts a comprehensive list of [example hashes](https://hashcat.net/wiki/doku.php?id=example_hashes) which can assist in manually identifying an unknown hash type and determining the corresponding Hashcat hash mode identifier.

imagine an additional md5 hash was leaked from the SQL database: `1b0556a75770563578569ae21392630c`. We weren't able to crack it using `rockyou.txt` alone, so in a subsequent attempt, we might apply some common rule-based transformations. One ruleset we could try is `best64.rule`, which contains 64 standard password modifications—such as appending numbers or substituting characters with their "leet" equivalents. To perform this kind of attack, we would append the `-r <ruleset>` option to the command, as shown below:
```bash
MarcosV999@htb[/htb]$ hashcat -a 0 -m 0 1b0556a75770563578569ae21392630c /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule

...SNIP...

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 1b0556a75770563578569ae21392630c
```

[Mask attack](https://hashcat.net/wiki/doku.php?id=mask_attack) (`-a 3`) is a type of brute-force attack in which the keyspace is explicitly defined by the user. For example, if we know that a password is eight characters long, rather than attempting every possible combination, we might define a mask that tests combinations of six letters followed by two numbers.
```
MarcosV999@htb[/htb]$ hashcat -a 3 -m 0 1e293d6912d074c0fd15844d803400dd '?u?l?l?l?l?d?s'
```

Writing Custom Wordlists and Rules
```
MarcosV999@htb[/htb]$ cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
...snip...
```

```
MarcosV999@htb[/htb]$ hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

## Generating wordlists using CeWL
```
MarcosV999@htb[/htb]$ cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
```

## Hunting for Encrypted Files
```bash
MarcosV999@htb[/htb]$ for ext in $(echo ".xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

## Mounting BitLocker-encrypted drives in Linux (or macOS)

```
MarcosV999@htb[/htb]$ sudo mkdir -p /media/bitlocker /media/bitlockermount
```

```
MarcosV999@htb[/htb]$ sudo losetup -f -P Backup.vhd
MarcosV999@htb[/htb]$ sudo dislocker /dev/loop0p1 -u<password> -- /media/bitlocker
MarcosV999@htb[/htb]$ sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount
```

```
MarcosV999@htb[/htb]$ cd /media/bitlockermount/; ls -la
```

```
MarcosV999@htb[/htb]$ sudo umount /media/bitlockermount
MarcosV999@htb[/htb]$ sudo umount /media/bitlocker
```

#### NetExec Usage
```bash
MarcosV999@htb[/htb]$ netexec <protocol> $target -u <user or userlist> -p <password or passwordlist>
```

[Password spraying](https://owasp.org/www-community/attacks/Password_Spraying_Attack) is a type of brute-force attack in which an attacker attempts to use a single password across many different user accounts. This technique can be particularly effective in environments where users are initialized with a default or standard password. 
```
MarcosV999@htb[/htb]$ netexec smb 10.100.38.0/24 -u <usernames.list> -p 'ChangeMe123!'
```

[Credential stuffing](https://owasp.org/www-community/attacks/Credential_stuffing) is another type of brute-force attack in which an attacker uses stolen credentials from one service to attempt access on others. Since many users reuse their usernames and passwords across multiple platforms (such as email, social media, and enterprise systems), these attacks are sometimes successful. 
```
MarcosV999@htb[/htb]$ hydra -C user_pass.list ssh://10.100.38.23
```
## Default credentials

Many systems—such as routers, firewalls, and databases—come with `default credentials`. While best practice dictates that administrators change these credentials during setup, they are sometimes left unchanged, posing a serious security risk.

While several lists of known default credentials are available online, there are also dedicated tools that automate the process. One widely used example is the [Default Credentials Cheat Sheet](https://github.com/ihebski/DefaultCreds-cheat-sheet), which we can install with `pip3`.
```
MarcosV999@htb[/htb]$ pip3 install defaultcreds-cheat-sheet
```

```
MarcosV999@htb[/htb]$ creds search <SERVICE>
```