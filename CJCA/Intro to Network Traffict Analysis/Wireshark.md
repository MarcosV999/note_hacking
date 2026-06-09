# Analysis with Wireshark
`Wireshark` is a free and open-source network traffic analyzer much like tcpdump but with a graphical interface. Wireshark is multi-platform and capable of capturing live data off many different interface types (to include WiFi, USB, and Bluetooth) and saving the traffic to several different formats. Wireshark allows the user to dive much deeper into the inspection of network packets than other tools.
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
## Termshark

Termshark is a Text-based User Interface (TUI) application that provides the user with a Wireshark-like interface right in your terminal window.
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

`Display Filters-` are used while the capture is running and after the capture has stopped. Display filters are proprietary to Wireshark, which offers many different options for almost any protocol.

|    **Display Filters**     | **Result**                                                                                    |
| :------------------------: | --------------------------------------------------------------------------------------------- |
|     ip.addr == x.x.x.x     | Capture only traffic pertaining to a certain host. This is an OR statement.                   |
|   ip.addr == x.x.x.x/24    | Capture traffic pertaining to a specific network. This is an OR statement.                    |
|   ip.src/dst == x.x.x.x    | Capture traffic to or from a specific host                                                    |
| dns / tcp / ftp / arp / ip | filter traffic by a specific protocol. There are many more options.                           |
|       tcp.port == x        | filter by a specific tcp port.                                                                |
|  tcp.port / udp.port != x  | will capture everything except the port specified                                             |
|       and / or / not       | AND will concatenate, OR will find either of two options, NOT will exclude your input option. |

#### Applying a Display Filter

Applying a display filter is even easier than a capture filter. From the main Wireshark capture window, all we need to do is: select the bookmark in the Toolbar → , then select an option from the drop-down. Alternatively, place the cursor in the text radial → and type in the filter we wish to use. If the field turns green, the filter is correct. `Just like in the image below.`
![[Apply display filter.png]]

When using capture and display filters, keep in mind that what we specify is taken in a literal sense. For example, filtering for port 80 traffic is not the same as filtering for HTTP. Think of ports and protocols more like guidelines instead of rigid rules. Ports can be bound and used for different purposes other than what they were originally intended. For example, filtering for HTTP will look for key markers that the protocol uses, such as GET/POST requests, and show results from them. Filtering for port 80 will show anything sent or received over that port regardless of the transport protocol.

http://192.168.189.192:81/settings.html
http://10.0.2.15:81/settings.html
http://10.0.2.15:81/role.html