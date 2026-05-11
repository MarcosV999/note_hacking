# Introduction to Networks
We will mostly focus on two primary types of networks: `Local Area Networks (LANs)` and `Wide Area Networks (WANs)`.
The table below shows some networking `key concepts`

| **Concepts**   | **Description**                                                                            |
| -------------- | ------------------------------------------------------------------------------------------ |
| `Nodes`        | Individual devices connected to a network.(computers, smartphones, printers, and servers.) |
| `Links`        | Communication pathways that connect nodes (wired or wireless).                             |
| `Data Sharing` | The primary purpose of a network is to enable data exchange.                               |
## Why Are Networks Important?

| **Function**       | **Description**                                                             |
| ------------------ | --------------------------------------------------------------------------- |
| `Resource Sharing` | Multiple devices can share hardware (like printers) and software resources. |
| `Communication`    | Instant messaging, emails, and video calls rely on networks.                |
| `Data Access`      | Access files and databases from any connected device.                       |
| `Collaboration`    | Work together in real-time, even when miles apart.                          |

## Types of Networks
#### Local Area Network (LAN)
A `Local Area Network (LAN)` connects devices over a short distance, such as within a home, school, or small office building.

| **Characteristic**   | **Description**                                                 |
| -------------------- | --------------------------------------------------------------- |
| `Geographical Scope` | Covers a small area.                                            |
| `Ownership`          | Typically owned and managed by a single person or organization. |
| `Speed`              | High data transfer rates.                                       |
| `Media`              | Uses wired (Ethernet cables) or wireless (Wi-Fi) connections.   |
#### Wide Area Network (WAN)
A `Wide Area Network (WAN)` spans a large geographical area, connecting multiple LANs.

| **Characteristic**   | **Description**                                                                 |
| -------------------- | ------------------------------------------------------------------------------- |
| `Geographical Scope` | Covers cities, countries, or continents.                                        |
| `Ownership`          | Often a collective or distributed ownership (e.g., internet service providers). |
| `Speed`              | Slower data transfer rates compared to LANs due to long-distance data travel.   |
| `Media`              | Utilizes fiber optics, satellite links, and leased telecommunication lines.     |
The Internet is the largest example of a `WAN`, connecting millions of `LANs` globally.

|Aspect|LAN|WAN|
|---|---|---|
|`Size`|Small, localized area|Large, broad area|
|`Ownership`|Single person or organization|Multiple organizations/service providers|
|`Speed`|High|Lower compared to LAN|
|`Maintenance`|Easier and less expensive|Complex and costly|
|`Example`|Home or office network|The Internet|

when accessing the Internet, a home LAN connects to an `Internet Service Provider's (ISP's)` WAN, which grants Internet access to all devices within the home network. An ISP is a company that provides individuals and organizations with access to the Internet.
At home, our devices—such as laptops, smartphones, and tablets—connect to our home router, forming a LAN. This router doesn't just manage local traffic; it also communicates with our ISP's WAN. Through this connection to the WAN, our home network gains the ability to access websites and online services hosted all over the world.

# Network Concepts

## OSI Model
The `Open Systems Interconnection (OSI) model` helps vendors and developers create interoperable network devices and software. Below we see the seven layers of the OSI Model.
#### Physical Layer (Layer 1)
is the first and lowest layer of the OSI model. It is responsible for transmitting raw bitstreams over a physical medium. Include the hardware components like Ethernet cables, hubs, and repeaters.
#### Data Link Layer (Layer 2)
provides node-to-node data transfer - a direct link between two physically connected nodes. Devices such as switches and bridges operate at this layer, using [MAC (Media Access Control)](https://en.wikipedia.org/wiki/MAC_address) addresses to identify network devices.
#### Network Layer (Layer 3)
handles packet forwarding, including the routing of packets through different routers to reach the destination network. Routers operate at this layer, using [IP (Internet Protocol) addresses](https://usa.kaspersky.com/resource-center/definitions/what-is-an-ip-address?srsltid=AfmBOoq0TltVlJi8PKDn6j4yNB0V5Av5Y4srTxb32Bbbg4TcAfZ5FG8H) to identify devices and determine the most efficient path for data transmission.
#### Transport Layer (Layer 4)
provides end-to-end communication services for applications. It is responsible for the reliable (or unreliable) delivery of data, segmentation, reassembly of messages, flow control, and error checking. Protocols like `TCP (Transmission Control Protocol)` and `UDP (User Datagram Protocol)` function at this layer
#### Session Layer (Layer 5)
manages sessions between applications. It establishes, maintains, and terminates connections, allowing devices to hold ongoing communications known as sessions. Protocols and `APIs` operating at this layer coordinate communication between systems and applications.
#### Presentation Layer (Layer 6)
acts as a translator between the application layer and the network format. It handles data representation, ensuring that information sent by the application layer of one system is readable by the application layer of another. Encryption protocols and data compression techniques operate at this layer to secure and optimize data transmission. 
#### Application Layer (Layer 7)
It enables resource sharing, remote file access, and other network services. Common protocols operating at this layer include `HTTP for web browsing, `FTP` for file transfers, `SMTP` for email transmission, and `DNS (Domain Name System)` for resolving domain names to IP addresses. This layer serves as the interface between the network and the application software.

## TCP/IP Model
#### Link Layer
It includes technologies such as Ethernet for wired connections and Wi-Fi for wireless connections. The Link Layer corresponds to the Physical and Data Link Layers of the OSI model, covering everything from the physical connection to data framing.
#### Internet Layer
Protocols like IP (Internet Protocol) and ICMP (Internet Control Message Protocol) operate at this layer, ensuring that data reaches its intended destination by determining logical paths for packet transmission. This layer corresponds to the Network Layer in the OSI model.
#### Transport Layer
This includes the use of TCP for reliable communication and UDP for faster, connectionless services. This layer ensures that data packets are delivered in a sequential and error-free manner, corresponding to the Transport Layer of the OSI model.
#### Application Layer
Protocols such as HTTP, FTP, and SMTP enable functionalities like web browsing, file transfers, and email services. This layer corresponds to the top three layers of the OSI model (Session, Presentation, and Application), providing interfaces and protocols necessary for data exchange between systems.
## Protocols
are standardized rules that determine the formatting and processing of data to facilitate communication between devices in a network.
#### Common Network Protocols
`HTTP (Hypertext Transfer Protocol)`
`FTP (File Transfer Protocol)`
`SMTP (Simple Mail Transfer Protocol)`
`TCP (Transmission Control Protocol)`
`UDP (User Datagram Protocol)`
`IP (Internet Protocol)`
## Transmission
in networking refers to the process of sending data signals over a medium from one device to another.
#### Transmission Types
Transmission in networking can be categorized into two main types: `analog` and `digital`. Digital transmission employs discrete signals (bits) to encode data, which is typical in modern communication technologies like computer networks and digital telephony.
#### Transmission Modes
define how data is sent between two devices. 
- `Simplex` mode allows one-way communication only, such as from a keyboard to a computer, where signals travel in a single direction. 
- `Half-duplex` mode permits two-way communication but not simultaneously; examples include walkie-talkies where users must take turns speaking. 
- `Full-duplex` mode, used in telephone calls, supports two-way communication simultaneously, allowing both parties to speak and listen at the same
#### Transmission Media
Which can be wired or wireless:
- Wired media includes:
	- `twisted pair` cables, commonly used in Ethernet networks and local area network (LAN) connections. 
	- `coaxial` cables, used for cable TV and early Ethernet.
	- `fiber optic` cables, which transmit data as light pulses and are essential for high-speed internet backbones.
- Wireless media, on the other hand:
	-  `radio waves` for Wi-Fi and cellular networks.
	- `microwaves` for satellite communications.
	- `infrared` technology used for short-range communications like remote controls.
# Components of a Network
|**Component**|**Description**|
|---|---|
|`End Devices`|Computers, Smartphones, Tablets, IoT / Smart Devices|
|`Intermediary Devices`|Switches, Routers, Modems, Access Points|
|`Network Media and Software Components`|Cables, Protocols, Management and Firewalls Software|
|`Servers`|Web Servers, File Servers, Mail Servers, Database Servers|
## Intermediary Devices
has the unique role of facilitating the flow of data between `end devices`, either within a local area network, or between different networks. Intermediary devices are responsible for `packet forwarding`, directing data packets to their destinations by reading network address information and determining the most efficient paths. Additionally, intermediary devices often incorporate security features like `firewalls` to protect certain networks from unauthorized access and potential threats.

#### Network Interface Cards (NICs)
is a hardware component installed in a computer, or other device, that enables connection to a network. It provides the physical interface between the device and the network media, handling the sending and receiving of data over the network. Each NIC has a unique Media Access Control (MAC) address, which is essential for devices to identify each other, and facilitate communication at the data link layer.
#### Routers
the forwarding of data packets between networks(layer 3), and ultimately directing internet traffic. Routers read the network address information in data packets to determine their destinations. They use routing tables and routing protocols such as `Open Shortest Path First (OSPF)` or `Border Gateway Protocol (BGP)` to find the most efficient path.
#### Switches
The `switch` is another integral component, with its primary job being to connect multiple devices within the same network, typically a Local Area Network (LAN). Operating at the data link layer (layer 2), switches use MAC addresses to forward data only to the intended recipient. They enable devices like computers, printers, and servers to communicate directly with each other within the network.
#### Hubs
A `hub` is a basic (and now antiquated) networking device. It connects multiple devices in a network segment and broadcasts incoming data to all connected ports, regardless of the destination. Operating at the physical layer (Layer 1), hubs are simpler than switches and do not manage traffic intelligently.

## Network Media and Software Components
#### Cabling and Connectors
This includes the various types of cables mentioned previously, but also connectors like the RJ-45 plug, which is used to interface cables with network devices such as computers, switches, and routers. Ethernet cables with RJ-45 connectors might connect desktop computers to network switches, enabling high-speed data transfer across the local area network.
#### Network Protocols
are the set of rules and conventions that control how data is formatted, transmitted, received, and interpreted across a network.
#### Network Management Software
consists of tools and applications used to monitor, control, and maintain network components and operations. These software solutions provide functionalities for:
- `performance monitoring`
- `configuration management`
- `fault analysis`
- `security management`
#### Software Firewalls
is a security application installed on individual computers or devices that monitors and controls incoming and outgoing network traffic based on predetermined security rules. They help prevent unauthorized access, reject incoming packets that contain suspicious or malicious data, and can be configured to restrict access to certain applications or services.

## Servers
is a powerful computer designed to provide services to other computers, known as clients, over a network. Servers are the backbone behind websites, email, files, and applications. In the realm of computer networking, servers play a crucial role by hosting services that clients access, facilitating `service provision`