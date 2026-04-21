password bandit 1 = ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5Ifman uniq
password bandit 2 = 263JGJPfgU6LtdEvgfWU1XP5yac29mFx
password bandit 3 = MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
password bandit 4 = 2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
password bandit 5 = 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
password bandit 6 = HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
password bandit 7 = morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
password bandit 8 = dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
password bandit 9 = 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
password bandit 10 = FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
password bandit 11 = dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
password bandit 12 = 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
password bandit 13 = FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
password bandit 14 = MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
password bandit 15 = 8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
> [!Done]- [+] SOLUTION
 openssl s_client -crlf \
-connect localhost:30001 \
-servername localhost

password bandit 16 = kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
> [!Done]- [+] SOLUTION
nmap -p31000-32000 -T4 -vvv -n localhost
openssl s_client -crlf -quiet -connect localhost:31790 -servername localhost

password bandit 17 = key_level17.private
> [!Done]- [+] SOLUTION
>diff pasword.new password.old 

password bandit 18 = x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
> [!Done]- [+] SOLUTION
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme

password bandit 19 = cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
> [!Done]- [+] SOLUTION
./bandit20-do cat /etc/bandit_pass/bandit20

password bandit 20 = 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
> [!Done]- [+] SOLUTION
> echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" | nc -lp 1234 &

password bandit 21 = EeoULMCra2q0dSkYj561DX7s1CpBuOBt
> [!Done]- [+] SOLUTION
> cd /etc/cron.d
> cat cronjob_bandit22
> cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

password bandit 22 = tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
> [!Done]- [+] SOLUTION
cd /etc/cron.d/
cat cronjob_bandit23
cat /usr/bin/cronjob_bandit23.sh
PRUEBA=$(echo I am user bandit23 | md5sum | cut -d ' ' -f 1)
cat /tmp/8ca319486bfbbc3663ea0fbe81326349

password bandit 23 = 0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
> [!Done]- [+] SOLUTION
find / -writable 2>/dev/null
nano /tmp/script_bandit23.sh && chmod +x /tmp/script_bandit23.sh
touch /tmp/my_password_24.txt && chmod 777 /tmp/my_password_24.txt
cp /tmp/script_bandit23.sh /var/spool/bandit24/foo/
cat /tmp/my_password_24.txt

password bandit 24 = gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8