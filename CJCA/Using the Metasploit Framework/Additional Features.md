
# Introduction to MSFVenom
`MSFVenom` is the successor of `MSFPayload` and `MSFEncode`, two stand-alone scripts that used to work in conjunction with `msfconsole` to provide users with highly customizable and hard-to-detect payloads for their exploits.
`MSFVenom` is the result of the marriage between these two tools. Before this tool, we had to pipe (`|`) the result from `MSFPayload`, which was used to generate shellcode for a specific processor architecture and OS release, into `MSFEncode`.

## Local Exploit Suggester

As a tip, there is a module called the `Local Exploit Suggester`. We will be using this module for this example, as the Meterpreter shell landed on the `IIS APPPOOL\Web` user, which naturally does not have many permissions. Furthermore, running the `sysinfo` command shows us that the system is of x86 bit architecture, giving us even more reason to trust the Local Exploit Suggester.

# Firewall and IDS/IPS Evasion
To better learn how we can efficiently and quietly attack a target, we first need to understand better how that target is defended. We are introduced to two new terms:

- Endpoint protection
- Perimeter protection
## Endpoint Protection

`Endpoint protection` refers to any localized device or service whose sole purpose is to protect a single host on the network. The host can be a personal computer, a corporate workstation, or a server in a network's De-Militarized Zone (`DMZ`). Endpoint protection usually comes in the form of software packs which include `Antivirus Protection`, `Antimalware Protection` (this includes bloatware, spyware, adware, scareware, ransomware), `Firewall`, and `Anti-DDOS` all in one.
#### Perimeter Protection

`Perimeter protection` usually comes in physical or virtualized devices on the network perimeter edge. These `edge devices` themselves provide access `inside` of the network from the `outside`, in other terms, from `public` to `private`.
## Evasion Techniques

Most host-based anti-virus software nowadays relies mainly on `Signature-based Detection` to identify aspects of malicious code present in a software sample. These signatures are placed inside the Antivirus Engine, where they are subsequently used to scan storage space and running processes for any matches. When a piece of unknown software lands on a partition and is matched by the Antivirus software, most Anti-viruses quarantine the malicious program and kill the running process. How do we circumvent all this heat? We play along with it. The examples shown in the `Encoders` section show that simply encoding payloads using different encoding schemes with multiple iterations is not enough for all AV products.