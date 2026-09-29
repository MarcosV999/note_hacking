# Analysis with Wireshark
`Wireshark` is a free and open-source network traffic analyzer much like tcpdump but with a graphical interface. 
#### Locating Wireshark
```
MarcosV999@htb[/htb]$ which wireshark
```

## TShark VS. Wireshark (Terminal vs. GUI)
TShark is a purpose-built terminal tool based on Wireshark. TShark shares many of the same features that are included in Wireshark and even shares syntax and options.
#### Basic TShark Switches
|**Switch Command**|**Result**|
|:-:|---|
|D|Will display any interfaces available to capture from and then exit out.|
|L|Will list the Link-layer mediums you can capture from and then exit out. (ethernet as an example)|
|i|choose an interface to capture from. (-i eth0)|
|f|packet filter in libpcap syntax. Used during capture.|
|c|Grab a specific number of packets, then quit the program. Defines a stop condition.|
|a|Defines an autostop condition. Can be after a duration, specific file size, or after a certain number of packets.|
|r (pcap-file)|Read from a file.|
|W (pcap-file)|Write into a file using the pcapng format.|
|P|Will print the packet summary while writing into a file (-W)|
|x|will add Hex and ASCII output into the capture.|
|h|See the help menu|
#### Locating TShark
```
MarcosV999@htb[/htb]$ which tshark

/usr/local/bin/tshark

MarcosV999@htb[/htb]$ tshark -D

1. en0 (Wi-Fi)
2. awdl0
3. llw0
4. utun0
5. utun1
6. lo0 (Loopback)
```

#### Selecting an Interface & Writing to a File
```
MarcosV999@htb[/htb]$ sudo tshark -i eth0 -w /tmp/test.pcap
```
#### Applying Filters
```
MarcosV999@htb[/htb]$ sudo tshark -i eth0 -f "host 172.16.146.2"

Capturing on 'eth0'
    1 0.000000000 172.16.146.2 → 172.16.146.1 DNS 70 Standard query 0x0804 A github.com
    2 0.258861645 172.16.146.1 → 172.16.146.2 DNS 86 Standard query response 0x0804 A github.com A 140.82.113.4
    3 0.259866711 172.16.146.2 → 140.82.113.4 TCP 74 48256 → 443 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 SACK_PERM=1 TSval=1321417850 TSecr=0 WS=128
    4 0.299681376 140.82.113.4 → 172.16.146.2 TCP 74 443 → 48256 [SYN, ACK] Seq=0 Ack=1 Win=65535 Len=0 MSS=1436 SACK_PERM=1 TSval=3885991869 TSecr=1321417850 WS=1024
```

`-f` allows us to apply filters to the capture. In the example, we utilized `host`, but you can use almost any filter Wireshark recognizes. We have touched on TShark a bit now. Let's take a look at a nifty tool called Termshark.
## Wireshark GUI Walkthrough
Now that we have spent time learning the art of packet capture from the command line let's spend some time in Wireshark. We will take a few minutes to examine what we are looking at in the output below. Let's dissect this view of the Wireshark GUI.
#### Capture Filters

|**Capture Filters**|**Result**|
|:-:|---|
|host x.x.x.x|Capture only traffic pertaining to a certain host|
|net x.x.x.x/24|Capture traffic to or from a specific network (using slash notation to specify the mask)|
|src/dst net x.x.x.x/24|Using src or dst net will only capture traffic sourcing from the specified network or destined to the target network|
|port #|will filter out all traffic except the port you specify|
|not port #|will capture everything except the port specified|
|port # and #|AND will concatenate your specified ports|
|portrange x-x|portrange will grab traffic from all ports within the range only|
|ip / ether / tcp|These filters will only grab traffic from specified protocol headers.|
|broadcast / multicast / unicast|Grabs a specific type of traffic. one to one, one to many, or one to all.|
#### Display Filters

|    **Display Filters**     | **Result**                                                                                    |
| :------------------------: | --------------------------------------------------------------------------------------------- |
|     ip.addr == x.x.x.x     | Capture only traffic pertaining to a certain host. This is an OR statement.                   |
|   ip.addr == x.x.x.x/24    | Capture traffic pertaining to a specific network. This is an OR statement.                    |
|   ip.src/dst == x.x.x.x    | Capture traffic to or from a specific host                                                    |
| dns / tcp / ftp / arp / ip | filter traffic by a specific protocol. There are many more options.                           |
|       tcp.port == x        | filter by a specific tcp port.                                                                |
|  tcp.port / udp.port != x  | will capture everything except the port specified                                             |
|       and / or / not       | AND will concatenate, OR will find either of two options, NOT will exclude your input option. |

| Wireshark filter                                  | Description                                                                                                                                                                          |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `ip.addr == 56.48.210.13`                         | Filters packets with a specific IP address                                                                                                                                           |
| `tcp.port == 80`                                  | Filters packets by port (HTTP in this case).                                                                                                                                         |
| `http`                                            | Filters for HTTP traffic.                                                                                                                                                            |
| `dns`                                             | Filters DNS traffic, which is useful to monitor domain name resolution.                                                                                                              |
| `tcp.flags.syn == 1 && tcp.flags.ack == 0`        | Filters SYN packets (used in TCP handshakes), useful for detecting scanning or connection attempts.                                                                                  |
| `icmp`                                            | Filters ICMP packets (used for Ping), which can be useful for reconnaissance or network issues.                                                                                      |
| `http.request.method == "POST"`                   | Filters for HTTP POST requests. In the case that POST requests are sent over unencrypted HTTP, it may be the case that passwords or other sensitive information is contained within. |
| `tcp.stream eq 53`                                | Filters for a specific TCP stream. Helps track a conversation between two hosts.                                                                                                     |
| `eth.addr == 00:11:22:33:44:55`                   | Filters packets from/to a specific MAC address.                                                                                                                                      |
| `ip.src == 192.168.24.3 && ip.dst == 56.48.210.3` | Filters traffic between two specific IP addresses. Helps track communication between specific hosts.                                                                                 |