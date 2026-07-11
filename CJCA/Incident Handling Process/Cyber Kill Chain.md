## What Is The Cyber Kill Chain?

Before we start talking about handling incidents, we need to understand the attack lifecycle (a.k.a. the cyber kill chain). This lifecycle describes how attacks manifest themselves. Understanding this lifecycle will provide us with valuable insights into how far in the network an attacker is and what they may have access to during the investigation phase of an incident.
![](Pasted%20image%2020260710103742.png)

## Stages of the Cyber Kill Chain

The `Recon` (Reconnaissance) stage is the initial stage, and it involves the part where an attacker chooses their target. Additionally, the attacker performs information gathering to become more familiar with the target and gathers as much useful data as possible, which can be used not only in this stage but also in other stages of this chain.
![](Pasted%20image%2020260710103811.png)

In the `Weaponize` stage, the malware to be used for initial access is developed and embedded into some type of exploit or deliverable payload. This malware is crafted to be extremely lightweight and undetectable by antivirus and detection tools.
In the `Delivery` stage, the exploit or payload is delivered to the victim(s). Traditional approaches include phishing emails that either contain a malicious attachment or a link to a web page. The web page can serve two purposes: either containing an exploit or hosting the malicious payload to avoid sending it through email scanning tools.
The `Exploitation` stage is the moment when an exploit or a delivered payload is triggered. During the exploitation stage of the Cyber Kill Chain, the attacker typically attempts to execute code on the target system in order to gain access or control.
In the `Installation` stage, the initial stager is executed and is running on the compromised machine. As already discussed, the installation stage can be carried out in various ways, depending on the attacker's goals and the nature of the compromise.
In the `Command and Control` stage, the attacker establishes a remote access capability to the compromised machine. As discussed, it is not uncommon to use a modular initial stager that loads additional scripts 'on-the-fly'. However, advanced groups will utilize separate tools to ensure that multiple variants of their malware live in a compromised network, and if one of them gets discovered and contained, they still have the means to return to the environment.
The final stage of the chain is the `Action` or objective of the attack. The objective of each attack can vary. Some adversaries may aim to exfiltrate confidential data, while others may want to obtain the highest level of access possible within a network to deploy ransomware. Ransomware is a type of malware that renders all data stored on endpoint devices and servers unusable or inaccessible unless a ransom is paid within a limited timeframe (not recommended).
### Pyramid of Pain

In the diagram below, the Pyramid of Pain illustrates how much `effort it takes for an adversary to change their tactics` when defenders detect and block different types of indicators.
![](Pasted%20image%2020260710103952.png)

## MITRE ATT&CK integration in TheHive

`TheHive` is a case management platform designed for cybersecurity teams to efficiently handle incidents by processing alerts. Users can create cases and link multiple relevant alerts within them. This platform serves as a centralized hub to collect and manage all security alerts from various devices on a single, comprehensive page. Additionally, TheHive offers the capability to import all MITRE ATT&CK Framework Tactics, Techniques, and Procedures (TTPs) into its alert management system. This integration enriches incident analysis by associating discovered attack patterns with the alerts.
To access TheHive platform, navigate to `http://TARGET_IP:9000` and use the following credentials:

- `Username`: htb-analyst
- `Password`: P3n#31337@LOG
Upon logging in, the dashboard will be displayed. We can view the alerts page as shown in the 
screenshot below, allowing us to view and manage alerts effectively.
## Example of MITRE ATT&CK Mapping

The table below shows some of the techniques (MITRE ATT&CK) that were observed during the incident.

| Tactic              | Technique                                     | ID        | Description                          |
| ------------------- | --------------------------------------------- | --------- | ------------------------------------ |
| `Initial Access`    | Exploit Public-Facing Application             | T1190     | Confluence CVE exploited             |
| `Execution`         | Command and Scripting Interpreter: PowerShell | T1059.001 | PowerShell used for payload download |
| `Persistence`       | Windows Service                               | T1543.003 | Windows Service for persistence      |
| `Credential Access` | LSASS Memory Dumping                          | T1003.001 | Extracted credentials                |
| `Lateral Movement`  | Remote Desktop Protocol                       | T1021.001 | RDP lateral movement                 |
| `Impact`            | Data Encrypted for Impact                     | T1486     | LockBit ransomware                   |