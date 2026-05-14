# Structure of a Penetration Test
A penetration test employs a carefully structured, `methodical process` designed to systematically identify and document security vulnerabilities present within computer systems, network infrastructure, and applications.

#### 1. Pre-Engagement Phase
During this phase, penetration testers work closely with the client to understand their specific needs, concerns, and objectives. This includes defining the scope of the test, establishing timelines, and determining which systems and networks will be tested.
Key documentation is created during this phase, including the `Rules of Engagement` (`RoE`) document, which outlines permitted testing activities, contact information for key personnel, and emergency procedures. Additionally, testers and clients sign necessary legal documents such as `Non-Disclosure Agreements` (`NDAs`) and service contracts to protect both parties.

#### 2. Information Gathering Phase
This phase can be divided into passive and active reconnaissance. 
`Passive reconnaissance` involves gathering information without directly interacting with the target systems. This might include analyzing public records, searching social media, reviewing company websites, and utilizing OSINT (Open Source Intelligence) tools.
`Active reconnaissance`, on the other hand, involves direct interaction with the target systems. This includes activities such as port scanning, service enumeration, and banner grabbing. While more intrusive, this provides detailed technical information about the target environment.

#### 3. Vulnerability Assessment Phase
During the vulnerability assessment phase, penetration testers `analyze the information` gathered to identify potential security weaknesses. This involves using various automated scanning tools and manual testing techniques to discover vulnerabilities in systems, applications, and network infrastructure. Skilled penetration testers must analyze the results, eliminate false positives, and understand how different vulnerabilities might be combined to create more significant security risks.

#### 4. Exploitation Phase
The exploitation phase is where penetration testers attempt to `actively exploit` the vulnerabilities identified in the previous phase. This is done to demonstrate the real-world impact of security weaknesses and to establish what an actual attacker might be able to achieve.
During exploitation, testers must carefully document their activities and maintain a precise record of the systems they've accessed. It's crucial to follow the agreed-upon Rules of Engagement and avoid causing any damage to production systems.

#### 5. Post-Exploitation Phase
This phase involves activities such as privilege escalation, lateral movement through the network, data exfiltration testing, and maintaining persistence. The goal is to understand the full extent of what an attacker could accomplish after breaching initial defenses.

#### 6. Lateral Movement Phase

Lateral movement involves navigating through the network after gaining initial access to discover additional systems, resources, and potential targets. This phase focuses on identifying and exploiting trust relationships between systems and expanding the penetration tester's foothold within the network. During lateral movement, testers employ various techniques such as credential harvesting, pass-the-hash attacks, and exploiting network protocols to move between systems. 

#### 7. Proof of Concept
The proof of concept phase involves creating `detailed documentation and evidence` that demonstrates how vulnerabilities were exploited. This includes developing reliable and repeatable methods to reproduce the identified security issues, which helps validate the findings and assists the client's technical team in understanding and fixing the vulnerabilities. The resulting documentation typically includes step-by-step procedures, required tools or configurations, and any specific conditions necessary for the exploit to work.

#### 8. Post-Engagement Phase
The reporting phase is critical as it `transforms` the technical findings into `actionable information for the client`. A well-written penetration testing report typically includes an executive summary for management, detailed technical findings for the IT team, and clear recommendations for remediation. Each vulnerability should be clearly described, including its potential impact, steps to reproduce, and specific recommendations for fixing the issue. The report should also include evidence such as screenshots and logs to support the findings. Risk ratings should be assigned to help the client prioritize their remediation efforts.

#### 9. Remediation Support and Retesting
After delivering the report, many penetration testing engagements include a period of remediation support. During this time, testers make themselves available to answer questions about their findings and provide additional guidance on implementing fixes. Once the client has addressed the identified vulnerabilities, a retest is often performed to verify that the fixes were implemented correctly and that no new vulnerabilities were introduced during the remediation process.

# Prerequisites for a Penetration Test
These foundational elements protect both the penetration tester and the client while maximizing the value of the assessment. These elements can be, but are not limited to:
- Legal Authorization & Documentation
- Scope Definition and Boundaries
- Information Gathering
- Communication Channels & Emergency Procedures
- Testing Environment Preparation
- Backup and Recovery Considerations
- Documentation & Reporting
- Professional Liability & Insurance
- Confidentiality & Data Handling

## Legal Authorization and Documentation
The cornerstone of any penetration testing engagement is proper legal authorization. This begins with a formal written agreement, often called a `Statement of Work` (`SoW`) or `Master Services Agreement` (`MSA`). Additionally, penetration testers must secure a `"Get Out of Jail Free"` letter, also known as a `Rules of Engagement` (`RoE`) document. This document serves as proof that you're authorized to perform potentially suspicious activities on the target systems. It should include contact information for key stakeholders, emergency procedures, and the defined testing windows.

## Scope Definition and Boundaries
A well-defined scope is essential for any penetration testing engagement. This scope should comprehensively document `all systems and assets` that are authorized for testing, including specific IP ranges, domain names, web applications, network segments, and individual systems.
It's crucial to establish and document the `exact testing windows`, as many organizations restrict testing to certain time periods such as after business hours, weekends, or during maintenance windows.
## Technical Information Gathering
Depending on the type and scope of the penetration test, a `comprehensive collection of technical information` about the target environment must be gathered before initiating any testing activities. This critical preparatory phase involves acquiring and analyzing various forms of documentation and intelligence. In a white-box penetration test, key documentation includes network diagrams that show system interconnections, inventories of all hardware and software assets, and detailed technical architecture documentation.

## Communication Channels and Emergency Procedures
Establishing clear communication channels is vital. You should have `contact information for key personnel`, including technical staff, project managers, and `emergency contacts`. Define escalation procedures for various scenarios, such as system outages or critical vulnerabilities discovered during testing.
Create an `incident response plan` that outlines what to do if something goes wrong.

## Testing Environment Preparation
Ensure your testing environment is properly configured before beginning. This includes setting up isolated networks if required, preparing testing tools and platforms, and configuring logging systems to document your activities. Your testing environment should be secure and separate from any personal or unrelated work to prevent cross-contamination.

## Backup and Recovery Considerations
Before testing begins, confirm that the target organization has recent `backups of all systems in scope`. While penetration testing shouldn't typically cause damage, it's essential to have recovery options available.

## Documentation and Reporting Requirements
Establish documentation requirements `upfront`. Know what kind of reporting the client expects, including format, level of detail, and any specific compliance requirements. Some organizations may require evidence of findings to be documented in particular ways for regulatory compliance.

## Professional Liability and Insurance
It is essential to maintain `professional liability insurance` coverage that covers penetration testing activities and related security assessments. This type of specialized insurance protects against potential claims arising from testing activities, including accidental system damage, data breaches, or business interruption.

## Confidentiality and Data Handling
Establish clear `procedures for handling sensitive data` discovered during testing. This includes how findings will be communicated, how data will be stored and transmitted, and when/how data will be destroyed after the engagement ends.
Usually, a critical legal document known as `Non-Disclosure Agreement` (`NDA`) should be signed before any penetration testing engagement begins. The NDA should clearly outline:
- The types of confidential information that will be protected
- Duration of confidentiality obligations
- Permitted uses of confidential information
- Data destruction requirements after project completion
- Consequences of unauthorized disclosure

# Methodologies & Frameworks
## Core Penetration Testing Methodologies
The most widely recognized methodology in the penetration testing field is the `Penetration Testing Execution Standard` ([PTES](http://www.pentest-standard.org/index.php/Main_Page)). PTES provides a framework that divides the penetration testing process into seven distinct phases: Pre-engagement Interactions, Intelligence Gathering, Threat Modeling, Vulnerability Analysis, Exploitation, Post Exploitation, and Reporting.

The Technical Guide to Information Security Testing and Assessment (NIST) represents a more formal approach. While not strictly a penetration testing methodology, it provides valuable guidance on security assessment planning, execution, and post-testing activities.

The `Open Web Application Security Project` ([OWASP](https://owasp.org/www-project-web-security-testing-guide/stable/)) Testing Guide is another widely adopted methodology that offers guidance for web application security testing. It provides a structured approach through four main phases: Information Gathering, Configuration and Deployment Management Testing, Identity Management Testing, and Authentication Testing. The guide contains distinct testing procedures, along with practical examples, for nearly every vulnerability seen in web applications.

The [MITRE ATT&CK](https://attack.mitre.org/) framework has become increasingly important in modern penetration testing. Unlike traditional methodologies, ATT&CK provides a comprehensive knowledge base of adversary tactics and techniques observed in real-world attacks.
