## Incident Scenario

The victim in this incident is `Insight Nexus`, a mid-sized market research and data analytics firm headquartered in Singapore. They provide competitive intelligence and consumer insights for global clients, including Fortune 500 companies in IT and finance. Their infrastructure includes many applications, servers, and hosts, but we'll focus on the important ones, such as an internet-facing application stack for clients, a ManageEngine server for IT administration, and a PHP-based customer reporting portal. Because of the nature of their work, they became an attractive target for adversaries interested in client data theft.
The first threat actor gained entry when system administrators forgot to change the default admin/admin password on an internet-facing application, i.e., `ManageEngine ADManager Plus`, after a product update. By leveraging this, the attackers logged in successfully, performed reconnaissance, mapped users and machines, and eventually created new privileged Active Directory accounts. Using one of the newly created accounts, the adversaries pivoted further into the environment, identifying an external RDP service exposed by misconfiguration. Exploiting that entry point, they escalated their control and eventually used Group Policy Objects (GPOs) to deploy spyware using an MSI package across multiple endpoints.
![](Pasted%20image%2020260710122157.png)
The incident was first discovered one day when an analyst from the SOC team investigated an alert on `TheHive` (Security Incident Response Platform) related to the creation of a suspicious file named `checkme.txt` in the root of a web server. Upon investigation, they discovered that it was deliberately placed there as a signature — "SilentJackal was here". This unusual artifact triggered a deeper investigation. What made the situation more complex was that the SOC team then realized two different threat actor groups were active in the same environment. While the first group was still exploring and deploying persistence mechanisms, a second actor had already compromised a vulnerable PHP application earlier, exfiltrated sensitive market research data, and significantly reduced their activity after achieving their objective, leaving only occasional connections to an external IP.
## Threat Actors

- `Crimson Fox` (Primary threat actor): A group with known links to the IT industry supply chain targeting, suspected to be state-backed. They specialize in credential theft and long-term persistence for data exfiltration. It is a capable and persistent group known for several previous successful attacks related to supply-chain and corporate intelligence.
- `Silent Jackal` (Secondary actor): A loosely organized criminal group focused on opportunistic website defacements and proof-of-concept intrusions, not necessarily financially motivated but disruptive. The members of this group are low-skill web intruders.

## Incident Analysis

A system administrator noticed unusual outbound connections from the ManageEngine server to an IP address in Eastern Europe while working on the server for scheduled maintenance. He called the SOC team and collaborated with them to investigate the alerts to find anything suspicious. One of the SOC analysts started investigating the alerts and found an alert mentioning a suspicious `checkme.txt` file on the same server.

![](Pasted%20image%2020260710123105.png)
On `2025-10-02 04:02:11`, attackers enumerated domain users and computers via queries from the ManageEngine console. Using the ManageEngine foothold, they also created a new Domain Administrator account. During Active Directory enumeration, they found that a Windows 10 machine (`DEV-021`) had a publicly exposed RDP port. This desktop machine is used occasionally by developers to perform development and release tasks by taking RDP directly on its public IP while working from home. The attacker took RDP directly into this machine using the newly created Domain Administrator account.

![](Pasted%20image%2020260710123650.png)

For this activity, the following event log was created in the Windows Event Logs with Event ID `4624`.
```
An account was successfully logged on.

 Subject:
    Security ID: SYSTEM
    Account Name: DEV-021$
    Account Domain: INSIGHT
    Time: 2025-10-04T02:03:12Z

 Logon Information:
    Logon Type: 10

 Network Information:
    Workstation Name: DEV-021
    Source Network Address: 103.112.60.117

 New Logon:
    SubjectUserName: insight\svc_deployer
    SourceNetworkAddress: 103.112.60.117
```

After a successful logon, the attackers conducted some domain reconnaissance. They found some interesting file shares on the file server, which they attempted to access multiple times. On the file server, they located client project folders that contained draft reports, survey data, and market forecasts.

![](Pasted%20image%2020260710123738.png)

After exploring and observing for a week, they started compressing and exfiltrating selected data. The attackers packaged stolen client materials into a file named `diagnostics_data.zip`, a filename chosen to resemble routine telemetry. The archive was then uploaded to the attacker-controlled host over HTTPS. Because the filename resembled legitimate diagnostics data and the upload used standard HTTPS, it did not immediately raise alarms. This tactic increases the attackers chance of exfiltrating data before defenders escalate.
![](Pasted%20image%2020260710123913.png)
Then, on `2025-10-04 02:10:45`, from `DEV-021`, they executed some PowerShell scripts that used domain administrator credentials to create a Group Policy Object (GPO) that pushes an MSI package (`java-update.msi`) across the domain. This MSI package created a scheduled task to run a process that performs spying and data exfiltration on the machines.
These events were also captured in the event logs, such as the creation of a new `.msi` file as Sysmon Event ID 11.
```
Sysmon Event 11: TargetFilename: C:\Windows\Temp\java-update.msi
```

This malware, with spying and data exfiltration capabilities, is deployed on all domain machines using GPO.
![](Pasted%20image%2020260710124008.png)
## Immediate Incident Response Actions

The first tangible discovery was `checkme.txt` by a SOC analyst. That file alone would normally be low priority, but the SOC analyst performing correlation saw that the same time window had ManageEngine events with unusual outbound traffic and multiple login events from an unfamiliar foreign IP.

The correlation of the following was done as follows:

- ManageEngine successful admin logins from foreign IPs.
- Sysmon process creation of `msiexec` installing an MSI across many hosts.
- LDAP enumeration logs and GPO changes.
- File server file compression and upload logs.
- Outbound HTTPS to an unusual IP address.

After the correlation, the SOC analyst immediately escalated the incident to the incident response team and opened a case in `TheHive`. The following actions and findings completed the investigation and response:

1. `Case creation and triage`
    - The SOC created a TheHive case titled “Insight Nexus — ManageEngine Compromise,” linked all related alerts (ManageEngine admin logins, Sysmon msiexec events, LDAP enumeration, file server uploads, and the portal `checkme.txt` event), and assigned roles: Triage Analyst, Forensics Lead, Containment Lead, and Communications Lead.
    - Priority was set to Critical due to confirmed data exfiltration.
2. `Containment — network controls`
    - Blocked outbound traffic to `103.112.60.117` at the perimeter firewall and on host-based firewalls. Added temporary egress block rules for the attacker IPs.
    - Added an IDS signature to alert on connections to `103.112.60.117` and similar endpoints.
3. `Containment — credential & account actions`
    - Disabled the ManageEngine admin account and rotated all high-privilege credentials exposed in logs (service accounts, deployer accounts, and any account showing suspicious activity).
    - Restricted the ManageEngine web console to be accessed only internally.
    - Implemented forced password changes and immediate revocation of active sessions where possible.
4. `Host isolation`
    - Isolated `manage.insightnexus.com`, `DEV-021`, and any machines that showed evidence of MSI installation from the production network for forensic collection (network access blocked, but preserved in a manner to allow analysis).
    - Suspended scheduled tasks and disabled GPO-initiated deployments until confirmation of remediation.
5. `Collect forensic artifacts`
    - On isolated hosts, collected volatile memory, process lists, registry hives, and disk images. Exported ManageEngine audit logs and the web server access logs with full timestamps.
    - Preserved copies of the MSI file (`java-update.msi`), the compressed exfiltrated package (`diagnostics_data.zip`), and any web shell files found in management app directories.

## Mapping to MITRE ATT&CK

- `Reconnaissance`: Scanning public assets; MITRE `T1595` (Active Scanning).
- `Weaponization / Initial Access`: ManageEngine default credentials (`T1078.004` - Valid Accounts), PHP upload exploitation (`T1190` - Exploit Public-Facing Application).
- `Delivery / Exploitation`: Web shell uploads, console command execution; (`T1505` - Server Software Component).
- `Installation / Persistence`: Scheduled tasks, services, GPO-deployed MSI (`T1547`, `T1543`, `T1069`).
- `Command & Control`: HTTPS to attacker-controlled IP (`T1071.001` - Web Protocols).
- `Action on Objective / Exfiltration`: Compress and upload project data (`T1560`/`T1041`).

