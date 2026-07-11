The `Incident Handling Process` defines a capability for organizations to prepare, detect, and respond to malicious events. Note that this process is suited for responding to IT security events, but its stages do not correspond to the stages of the Cyber Kill Chain in a one-to-one manner.

As defined by NIST, the incident handling process consists of the following four distinct stages:
![](Pasted%20image%2020260710105545.png)

Incident handlers spend most of their time in the first two stages, `preparation` and `detection and analysis`. This is where we, as incident handlers, spend much time improving ourselves and looking for the next malicious event. When a malicious event is detected, we move on to the next stage and respond to the event.
So, incident handling has two main activities, which are `investigating` and `recovering`. The investigation aims to:

- `Discover` the initial '`patient zero`' victim and create an ongoing (if still active) incident timeline.
- Determine which `tools` and malware the adversary used.
- `Document` the compromised systems and what the adversary has done.

# Preparation Stage
## Preparation Prerequisites

During the preparation stage, we need to ensure that we have:

- Skilled incident handling team members (incident handling team members can be outsourced, but a basic capability and understanding of incident handling are necessary in-house regardless).
- A trained workforce (as much as possible, through security awareness activities or other means of training).
- Clear policies and documentation.
- Tools (software and hardware).
## Clear Policies & Documentation

Some of the written policies and documentation should contain an up-to-date version of the following information:

- Contact information and roles of the incident handling team members.
- Contact information for the legal and compliance department, management team, IT support, communications and media relations department, law enforcement, internet service providers, facility management, and external incident response team.
- Incident response policy, plan, and procedures.
- Incident information sharing policy and procedures.
- Baselines of systems and networks, out of a golden image and a clean state environment.
- Network diagrams.
- Organization-wide asset management database.
- User accounts with excessive privileges that can be used on-demand by the team when necessary (also for business-critical systems, which are handled with the skills needed to administer that specific system). These user accounts are normally enabled when an incident is confirmed during the initial investigation and then disabled once it is over. A mandatory password reset is also performed when disabling the users.
- Ability to acquire hardware, software, or an external resource without a complete procurement process (urgent purchase of up to a certain amount). The last thing you need during an incident is to wait for weeks for the approval of a $500 tool.
- Forensic/Investigative cheat sheets.

## Tools (Software & Hardware)

Moving forward, we also need to ensure that we have the right tools to perform the job. These include, but are not limited to:

- An additional laptop or a forensic workstation for each incident handling team member to preserve disk images and log files, perform data analysis, and investigate without any restrictions (we know malware will be tested here, so tools such as antivirus should be disabled). These devices should be handled appropriately and not in a way that introduces risks to the organization.
- Digital forensic image acquisition and analysis tools.
- Memory capture and analysis tools.
- Live response capture and analysis tools.
- Log analysis tools.
- Network capture and analysis tools.
- Network cables and switches.
- Write blockers.
- Hard drives for forensic imaging.
- Power cables.
- Screwdrivers, tweezers, and other relevant tools to repair or disassemble hardware devices if needed.
- Indicator of Compromise (IOC) creator and the ability to search for IOCs across the organization.
- Chain of custody forms.
- Encryption software.
- Ticket tracking system.
- Secure facility for storage and investigation.
- Incident handling system independent of your organization's infrastructure.

Many of the tools mentioned above will be part of what is known as a `jump bag` - always ready with the necessary tools to be picked up and taken immediately. Without this prepared bag, gathering all necessary tools on the fly may take days or weeks before we are ready to respond.

Let us now take a look at some of the highly recommended protective measures, which have a high mitigation impact against the majority of threats.
## DMARC

[DMARC](https://dmarcly.com/blog/how-to-implement-dmarc-dkim-spf-to-stop-email-spoofing-phishing-the-definitive-guide#what-is-dmarc) is an email protection mechanism against phishing built on top of the already existing [SPF](https://dmarcly.com/blog/how-to-implement-dmarc-dkim-spf-to-stop-email-spoofing-phishing-the-definitive-guide#what-is-spf) and [DKIM](https://dmarcly.com/blog/how-to-implement-dmarc-dkim-spf-to-stop-email-spoofing-phishing-the-definitive-guide#what-is-dkim). The idea behind DMARC is to reject emails that 'pretend' to originate from our organization. Therefore, if an adversary is spoofing an email pretending to be an employee asking for an invoice to be paid, the system will reject the email before it reaches the intended recipient. DMARC is easy and inexpensive to implement; however, we cannot stress enough that thorough testing is mandatory; otherwise (and this is oftentimes the case), we risk blocking legitimate emails with no ability to recover them.

## Endpoint Hardening (& EDR)

Endpoint devices (workstations, laptops, etc.) are the entry points for most of the attacks that we face on a daily basis. Considering that most threats will originate from the internet and target users who are browsing websites, opening attachments, or running malicious executables, a significant percentage of this activity will occur on their corporate endpoints.

There are a few widely recognized endpoint hardening standards now, with CIS and Microsoft baselines being the most popular, and these should really be the building blocks for our organization's hardening baselines.
## Network Protection

Network segmentation is a powerful technique for preventing a breach from spreading across the entire organization. Business-critical systems must be isolated, and connections should be allowed only as required by the business. Internal resources should not face the Internet directly (unless placed in a DMZ).

Additionally, when speaking of network protection, we should consider IDS/IPS (Intrusion Detection System/Intrusion Prevention System) systems. Their power really shines when SSL/TLS interception is performed so that they can identify malicious traffic based on content on the wire and not based on the reputation of IP addresses, which is a traditional and very inefficient way of detecting malicious traffic.

Additionally, ensure that only organization-approved devices can access the network. Solutions such as 802.1x can be utilized to reduce the risk of bring your own device (BYOD) or malicious devices connecting to the corporate network.
## Privilege Identity Management / MFA / Passwords

At this point in time, stealing privileged user credentials is the most common escalation path in Active Directory environments. Additionally, a common mistake is that admin users either have a weak (but often complex) password or a shared password with their regular user account (which can be obtained via multiple attack vectors such as keylogging). For reference, a weak but complex password is "Password1!". It includes uppercase, lowercase, numerical, and special characters, but despite this, it's easily predictable and can be found in many password lists that adversaries employ in their attacks. It is recommended to teach employees to use passphrases because they are harder to guess and difficult to brute force. Multi-factor authentication (MFA) is another identity-protecting solution that should be implemented at least for any type of administrative access to `all` applications and devices.
## Vulnerability Scanning

Perform continuous vulnerability scans of our environment and remediate at least the "High" and "Critical" vulnerabilities that are discovered. While the scanning can be automated, the fixes usually require manual involvement. If we can't apply patches for some reason, we definitely need to segment the systems that are vulnerable.
## User Awareness Training

Training users to recognize suspicious behavior and report it when discovered is a big win for us. While it is unlikely to reach 100% success in this task, these training sessions are known to significantly reduce the number of successful compromises. Periodic "surprise" testing should also be part of this training, including, for example, monthly phishing emails, dropped USB sticks in the office building, etc.
## Active Directory Security Assessment

The best way to detect security misconfigurations or exposed critical vulnerabilities is by looking for them from an attacker's perspective. Conducting our own reviews (or hiring a third party if the skill set is missing from the organization) will ensure that when an endpoint device is compromised, the attacker will not have a one-step escalation possibility to high privileges on the network.
## Purple Team Exercises

We need to train incident handlers and keep them engaged. There is no question about that, and the best place to do it is inside an organization's own environment. Purple team exercises are essentially security assessments by a red team that either continuously or eventually informs the blue team about their actions, findings, any visibility or security shortcomings, etc.

# Detection & Analysis Stage

The `Detection & Analysis` stage involves all aspects of detecting an incident, such as utilizing sensors, logs, and trained personnel. It also includes information and knowledge sharing, as well as utilizing context-based threat intelligence. Segmentation of the architecture and having a clear understanding of and visibility within the network are also important factors.

Threats are introduced to the organization via an infinite number of attack vectors, and their detection can come from sources such as:

- An employee who notices abnormal behavior.
- An alert from one of our tools (EDR, IDS, Firewall, SIEM, etc.).
- Threat hunting activities.
- A third-party notification informing us that they discovered signs of our organization being compromised.

It is highly recommended to create levels of detection by logically categorizing our network as follows:

- Detection at the network perimeter (using firewalls, internet-facing network intrusion detection/prevention systems, demilitarized zone, etc.).
- Detection at the internal network level (using local firewalls, host intrusion detection/prevention systems, etc.).
- Detection at the endpoint level (using antivirus systems, endpoint detection & response systems, etc.).
- Detection at the application level (using application logs, service logs, etc.).

---

## Initial Investigation

When a security incident is detected, we should conduct some initial investigation and establish context before assembling the team and calling an organization-wide incident response. Think about how information is presented in the event of an administrative account connecting to an IP address at HH:MM:SS. Without knowing what system is on that IP address and which time zone the time refers to, we may easily jump to the wrong conclusion about what this event is about. 

With the initially gathered information, we can start building an incident timeline. This timeline will keep us organized throughout the event and provide an overall picture of what happened. The events in the timeline are sorted based on when they occurred. Note that during the investigative process later on, we will not necessarily uncover evidence in this chronological order. However, when we sort the evidence based on when it occurred, we will get context from the separate events that took place. The timeline can also shed light on whether newly discovered evidence is part of the current incident.
Let's take one event and populate the example table from above. It will look as follows:

|`Date`|`Time of the event`|`hostname`|`event description`|`data source`|
|---|---|---|---|---|
|09/09/2021|13:31 CET|SQLServer01|Hacker tool 'Mimikatz' was detected|Antivirus Software|
We can also view an alert related to this event log in the TheHive Case Management Platform.
![](Pasted%20image%2020260710113612.png)

## Incident Confidentiality & Communication

Incidents are very confidential topics, and as such, all of the information gathered should be kept on a need-to-know basis unless applicable laws or a management decision instruct us otherwise. There are multiple reasons for this. The adversary may be, for example, an employee of the company, or if a breach has occurred, the communication to internal and external parties should be handled by the appointed person in accordance with the legal department.

## The Investigation

The investigation starts based on the initially gathered (and limited) information that contains what we know about the incident so far. With this initial data, we will begin a 3-step cyclic process that will iterate over and over again as the investigation evolves. This process includes:

- Creation and usage of indicators of compromise (IOCs).
- Identification of new leads and impacted systems.
- Data collection and analysis from the new leads and impacted systems.
### Initial Investigation Data

In order to reach a conclusion, an investigation should be based on valid leads that have been discovered not only during this initial phase but throughout the entire investigation process. The incident handling team should constantly bring up new leads and not focus solely on a specific finding, such as a known malicious tool. Narrowing an investigation down to a specific activity often results in limited findings, premature conclusions, and an incomplete understanding of the overall impact.
### Creation & Usage Of IOCs

An indicator of compromise (IOC) is a `sign that an incident has occurred`. IOCs are documented in a structured manner, which represents the `artifacts` of the compromise. Examples of IOCs can be IP addresses, hash values of files, and file names. In fact, because IOCs are so important to an investigation, special languages such as `OpenIOC` have been developed to document them and share them in a standard manner. Another widely used standard for IOCs is `YARA`. There are a number of free tools that can be utilized, such as Mandiant's `IOC Editor`, to create or edit IOCs. Using these languages, we can describe and use the artifacts that we uncover during an incident investigation. We may even obtain IOCs from third parties if the adversary or the attack is known. For example, CISA publishes the IOCs in a format called `STIX` (`Structured Threat Information eXpression`). STIX is an open-source, machine-readable language and serialization format, primarily in JSON, used to exchange cyber threat intelligence (CTI) in a standardized and consistent way.

In TheHive, we can add IOCs in the observables section of an alert.
![](Pasted%20image%2020260710115822.png)

To leverage IOCs, we will have to deploy an `IOC-obtaining/IOC-searching tool` (native or third-party and possibly at scale). A common approach is to utilize `WMI` or `PowerShell` for IOC-related operations in Windows environments.

### Identification Of New Leads & Impacted Systems

After searching for IOCs, we expect to have some hits that reveal other systems with the same signs of compromise. These hits may not be directly associated with the incident we are investigating. Our IOC could be, for example, too generic. We need to identify and `eliminate false positives`. We may also end up in a position where we come across a large number of hits. In this case, we should prioritize the ones we will focus on, ideally those that can provide us with new leads after a potential forensic analysis.

### Data Collection and Analysis from the New Leads and Impacted Systems

Once we have identified systems that include our IOCs, we will want to `collect and preserve the state` of those systems for further analysis in order to uncover new leads and/or answer investigative questions about the incident. Depending on the system, there are multiple approaches to how and what data to collect. Sometimes we want to perform a '`live response`' on a system as it is running, while in other cases, we may want to shut down a system and then perform any analysis on it. Live response is the most common approach, where we collect a predefined set of data that is usually rich in artifacts that may explain what happened to a system. Shutting down a system is not an easy decision when it comes to preserving valuable information because, in many cases, much of the artifacts will only live within the RAM memory of the machine, which will be lost if the machine is turned off. Regardless of the collection approach we choose, it is vital to ensure that minimal interaction with the system occurs to avoid altering any evidence or artifacts.
Keep in mind that during the data collection process, we should keep track of the `chain of custody` to ensure that the examined data is court-admissible if legal action is to be taken against an adversary.
### Use of AI in Threat Detection

Artificial Intelligence (AI) is transforming how organizations detect, triage, and respond to security incidents. In traditional IR workflows, analysts manually review logs, alerts, and reports. This process usually takes hours or days. AI `automates much of this analysis`, reducing response time and improving accuracy by learning from historical incidents and `identifying behavioral anomalies` faster than humans.
AI Attack Discovery leverages `LLMs` (large language models) to analyze alerts in an environment and identify threats. The summary represents an attack and shows relationships among multiple alerts to help us identifying which users and hosts are involved. This also show MITRE ATT&CK mappings.

# Containment, Eradication, and Recovery Stage
## Containment

In this stage, we take action to prevent the spread of the incident. We divide the actions into `short-term containment` and `long-term containment`. It is important that containment actions are coordinated and executed across all systems simultaneously. Otherwise, we risk notifying attackers that we are after them, in which case they might change their techniques and tools in order to persist in the environment.

In short-term containment, the actions taken leave a minimal footprint on the systems on which they occur. Some of these actions can include placing a system in a separate/isolated VLAN, pulling the network cable out of the system(s), or modifying the attacker's C2 DNS name to a system under our control or to a non-existing one. The actions here contain the damage and provide time to develop a more concrete remediation strategy. Additionally, since we keep the systems unaltered (as much as possible), we have the opportunity to take forensic images and preserve evidence if this wasn't already done during the investigation (this is also known as the `backup` substage of the containment stage)
## Eradication

Once the incident is contained, eradication is necessary to eliminate both the root cause of the incident and what is left of it to ensure that the adversary is out of the systems and network. Some of the activities in this stage include removing the detected malware from systems, rebuilding some systems, and restoring others from backup. During the eradication stage, we may extend the previously performed containment activities by applying additional patches, that were not immediately required. Additional system-hardening activities are often performed during the eradication stage (not only on the impacted system but across the network in some cases).
## Recovery

In the recovery stage, we bring systems back to normal operation. Of course, the business needs to verify that a system is in fact working as expected and that it contains all the necessary data. When everything is verified, these systems are brought into the production environment. All restored systems will be subject to heavy logging and monitoring after an incident, as compromised systems tend to be targets again if the adversary regains access to the environment in a short period of time. Typical suspicious events to monitor for are:

- Unusual logons (e.g., user or service accounts that have never logged-in there before).
- Unusual processes.
- Changes to the registry in locations that are usually modified by malware.

# Post-Incident Activity Stage

In this stage, our objective is to document the incident and improve our capabilities based on lessons learned from it. This stage gives us an opportunity to reflect on the threat by understanding what occurred, what we did, and how our actions and activities worked out.
## Reporting

The final report is a crucial part of the entire process. A complete report will contain answers to questions such as:

- What happened and when?
- How did the team perform in dealing with the incident in regard to plans, playbooks, policies, and procedures?
- Did the business provide the necessary information and respond promptly to aid in handling the incident efficiently? What can be improved?
- What actions have been implemented to contain and eradicate the incident?
- What preventive measures should be put in place to prevent similar incidents in the future?
- What tools and resources are needed to detect and analyze similar incidents in the future?

Such reports can eventually provide us with measurable results. For example, they can provide us with knowledge about how many incidents have been handled, how much time the team spends per incident, and the different actions that were performed during the handling process. Additionally, incident reports provide a reference for handling future events of a similar nature. In situations where legal action is to be taken, an incident report will also be used in court and as a source for identifying the costs and impact of incidents.