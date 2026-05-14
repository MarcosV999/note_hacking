Open cmd as administrator
```
Start-Process cmd -Verb RunAs
```
When you manage to open a terminal as administrator, you will see that the title bar clearly says "Administrator: Command Prompt".

Add new user
```
net user <username> <password> /add
```

Add new group
```
net localgroup <name> /add
```

Adding Jim to the HR security group
```
net localgroup HR jim /add
```

Disable Inheritance
```
icacls "C:\Ruta\A\Tu\Carpeta" /inheritance:d
```

What is the name of the group that is present in the Company Data Share Permissions ACL by default?
- Everyone
What is the name of the tab that allows you to configure NTFS permissions?
- Security

**WMIC** significa **Windows Management Instrumentation Command-line**

List the SID associated with the user account Jim you created.
``` cmd
wmic useraccount where name='jim' get sid
```

``` Powershell
Get-LocalUser -Name "jim" | Select-Object Name, SID
```

List the SID associated with the HR security group you created.
``` cmd
wmic group where name='hr' get sid
```

``` Powershell
Get-LocalGroup -Name "hr" | Select-Object Name, SID
```
