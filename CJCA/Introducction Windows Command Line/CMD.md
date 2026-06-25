```powershll
doskey /history
help <command>
<command>/?
tree /F
dir /A:R (files read only attribute)
#Create directory
md
mkdir
#Delete Directories
rd
rmdir
rd /S (equal rm -r in linux)

move
robocopy (important-LOTL)
fsutil file createNew
ren demo.txt superdemo.txt (change name)

# Deleting Files
del 
erase
```

# Gathering System Information
![](Pasted%20image%2020260620141646.png)

|Type|Description|
|---|---|
|`General System Information`|Contains information about the overall target system. Target system information includes but is not limited to the `hostname` of the machine, OS-specific details (`name`, `version`, `configuration`, etc.), and `installed hotfixes/patches` for the system.|
|`Networking Information`|Contains networking and connection information for the target system and system(s) to which the target is connected over the network. Examples of networking information include but are not limited to the following: `host IP address`, `available network interfaces`, `accessible subnets`, `DNS server(s)`, `known hosts`, and `network resources`.|
|`Basic Domain Information`|Contains Active Directory information regarding the domain to which the target system is connected.|
|`User Information`|Contains information regarding local users and groups on the target system. This can typically be expanded to contain anything accessible to these accounts, such as `environment variables`, `currently running tasks`, `scheduled tasks`, and `known services`.|

#### Examining the System
```powershell
hostname
ver (OS version)
ipconfig
arp /a
whoami (provides us with the current domain and the user name)
	**Note:** If the current user is not a domain-joined account, the `NetBIOS` name will be provided instead. The current `hostname` will be used in most cases.
whoami /priv
whoami /groups
whoami /all
```

[Net User](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc771865\(v=ws.11\)) allows us to display a list of all users on a host, information about a specific user, and to create or delete users.

```powershell
net user
net group
net group "Domain Admins" /domain
net share
net view
```

## Searching With CMD
#### Using Where
```powershell
C:\Users\student\Desktop>where calc.exe 
C:\Windows\System32\calc.exe 

C:\Users\student\Desktop>where bio.txt 
INFO: Could not find files for the given pattern(s).
```

This command worked because the system32 folder is in our environment variable path, so the `where` command can look through those folders automatically.
#### Recursive Where
```powershell
C:\Users\student\Desktop>where /R C:\Users\student\ bio.txt
nota: where.exe /R C:\Users waldo.txt
```
#### Using Wildcards

```powershell
C:\Users\student\Desktop>where /R C:\Users\student\ *.csv C:\Users\student\AppData\Local\live-hosts.csv
```

#### Basic Find
 Find is used to search for text strings or their absence within a file or files. You can also use `find` against the console's output or another command.
```powershell
 C:\Users\student\Desktop> find "password" "C:\Users\student\not-passwords.txt"
```
#### Find Modifiers
```powershell
C:\Users\student\Desktop> find /N /I /V "IP Address" example.txt
```
#### Findstr
Think of it as find2.0. For those familiar with Linux, `findstr` is closer to `grep`.

#### Sort and unique
```powershell
PS C:\Users\MTanaka\Desktop> sort.exe .\sort-1.md /unique
```

## View Variables
```powershell
echo %SYSTEMROOT%
set #list all variables
```

#### When to Use `set` Vs. `setx`
Both [set](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/set_1) and [setx](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/setx) are command line utilities that allow us to display, set, and remove environment variables. The `set` utility only manipulates environment variables in the current command line session. Suppose we need to make permanent changes to environment variables. In that case, we can use `setx` to make the appropriate changes to the registry, which will exist upon restart of our current command prompt session.

#### Creating Variables
```powershell
C:\htb> set DCIP=172.16.5.2
C:\htb> setx DCIP 172.16.5.2 # setx <variable name> <value> <parameters>
```
#### Editing Variables
```powershell
C:\htb> setx DCIP 172.16.5.4 
```
#### Removing Variables
```powershell
C:\htb> setx DCIP ""
```

## Service Controller
[SC](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754599\(v=ws.11\)) is a Windows executable utility that allows us to query, modify, and manage host services locally and over the network.
```powershell
sc query type= service
sc query windefend
```
**Note:** The spacing for the optional query parameters is crucial. For example, `type= service`, `type=service`, and `type =service` are completely different ways of spacing this parameter. However, only `type= service` is correct in this case.

```powershell
sc stop <service>
sc start <service>
```
#### Disabling Windows Service
```powershell
sc config <service> start= disabled
```
**Note:** To revert everything back to normal, you can set `start= auto` to make sure that the services can be restarted and function appropriately.


# Working With Scheduled Tasks

#### Display Scheduled Tasks:
```powershell
schtasks /Query /V /FO list
```
#### Create a New Scheduled Task:
```powershell
schtasks /create /sc ONSTART /tn "My Secret Task" /tr
```
#### Change the Properties of a Scheduled Task
Ok, now let us say we found the `hash` of the local admin password and want to use it to spawn our Ncat shell for us; if anything happens, we can modify the task like so to add in the credentials for it to use.
```powershell
schtasks /change /tn "My Secret Task" /ru administrator /rp "P@ssw0rd"
```

Now to make sure our changes took, we can query for the specific task using the `/tn` parameter and see:
```powershell
schtasks /query /tn "My Secret Task" /V /fo list
```

### Delete the Scheduled Task(s)

```powershell
schtasks /delete /tn "My Secret Task"
```
