# Network Types
#### Common Terminology

|**Network Type**|**Definition**|
|---|---|
|Wide Area Network (WAN)|Internet|
|Local Area Network (LAN)|Internal Networks (Ex: Home or Office)|
|Wireless Local Area Network (WLAN)|Internal Networks accessible over Wi-Fi|
|Virtual Private Network (VPN)|Connects multiple network sites to one `LAN`|

## WAN
The WAN (Wide Area Network) is commonly referred to as `The Internet`. When dealing with networking equipment, we'll often have a WAN Address and LAN Address. The WAN one is the address that is generally accessed by the Internet. That being said, it is not exclusive to the Internet; a `WAN is just a large number of LANs joined together`. Many large companies or government agencies will have an "Internal WAN" (also called Intranet, Airgap Network, etc.).
## LAN / WLAN

LANs (Local Area Network) and WLANs (Wireless Local Area Network) will typically assign IP Addresses designated for local use. In some cases, like some colleges or hotels.
## VPN

There are three main types `Virtual Private Networks` (`VPN`), but all three have the same goal of making the user feel as if they were plugged into a different network.
#### Site-To-Site VPN
Both the client and server are Network Devices, typically either `Routers` or `Firewalls`, and share entire network ranges. This is most commonly used to join company networks together over the Internet, allowing multiple locations to communicate over the Internet as if they were local.
#### Remote Access VPN
This involves the client's computer creating a virtual interface that behaves as if it is on a client's network. Hack The Box utilizes `OpenVPN`, which makes a TUN Adapter letting us access the labs. When analyzing these VPNs, an important piece to consider is the routing table that is created when joining the VPN
#### SSL VPN
This is essentially a VPN that is done within our web browser and is becoming increasingly common as web browsers are becoming capable of doing anything. Typically these will stream applications or entire desktop sessions to your web browser. A great example of this would be the HackTheBox Pwnbox.

## Book Terms

| Network Type                          | Definition                       |
| ------------------------------------- | -------------------------------- |
| Global Area Network (GAN)             | Global network (the Internet)    |
| Metropolitan Area Network (MAN)       | Regional network (multiple LANs) |
| Wireless Personal Area Network (WPAN) | Personal network (Bluetooth)     |
#### GAN
A worldwide network such as the `Internet` is known as a `Global Area Network` (`GAN`). However, the Internet is not the only computer network of this kind. Internationally active companies also maintain isolated networks that span several `WAN`s and connect company computers worldwide.
#### MAN
`Metropolitan Area Network` (`MAN`) is a broadband telecommunications network that connects several `LAN`s in geographical proximity. Internationally operating network operators provide the infrastructure for `MAN`s. Cities wired as `Metropolitan Area Networks` can be integrated supra-regionally in `Wide Area Networks` (`WAN`) and internationally in `Global Area Networks` (`GAN`).
#### PAN / WPAN
Modern end devices such as smartphones, tablets, laptops, or desktop computers can be connected ad hoc to form a network to enable data exchange. This can be done by cable in the form of a `Personal Area Network` (`PAN`). The wireless variant `Wireless Personal Area Network` (`WPAN`) is based on Bluetooth or Wireless USB technologies. A `wireless personal area network` that is established via Bluetooth is called `Piconet`. `PAN`s and `WPAN`s usually extend only a few meters and are therefore not suitable for connecting devices in separate rooms or even buildings. In the context of the `Internet of Things` (`IoT`), `WPAN`s are used to communicate control and monitor applications with low data rates.

# Networking Topologies
We can divide the entire network topology area into three areas:
#### 1. Connections

| `Wired connections`  | `Wireless connections` |
| -------------------- | ---------------------- |
| Coaxial cabling      | Wi-Fi                  |
| Glass fiber cabling  | Cellular               |
| Twisted-pair cabling | Satellite              |
| and others           | and others             |

#### 2. Nodes - Network Interface Controller (NICs)

| Repeaters    | Hubs     | Bridges   | Switches |
| ------------ | -------- | --------- | -------- |
| Router/Modem | Gateways | Firewalls |          |
#### 3. Classifications
We can imagine a topology as a virtual form or `structure of a network`. This form does not necessarily correspond to the actual physical arrangement of the devices in the network. Therefore these topologies can be either `physical` or `logical`
## Point-to-Point
The simplest network topology with a dedicated connection between two hosts is the `point-to-point` topology. In this topology, a direct and straightforward physical link exists only between `two hosts`. These two devices can use these connections for mutual communication.
## Bus
All hosts are connected via a transmission medium in the bus topology. Every host has access to the transmission medium and the signals that are transmitted over it. There is no central network component that controls the processes on it. The transmission medium for this can be, for example, a `coaxial cable`.
## Star
The star topology is a network component that maintains a connection to all hosts. Each host is connected to the `central network component` via a separate link. This is usually a router, a hub, or a switch. These handle the `forwarding function` for the data packets.
## Ring
The `physical` ring topology is such that each host or node is connected to the ring with two cables:

- One for the `incoming` signals and
- the another for the `outgoing` ones.

This means that one cable arrives at each host and one cable leaves. The ring topology typically does not require an active network component. The control and access to the transmission medium are regulated by a protocol to which all stations adhere.
## Mesh
Many nodes decide about the connections on a `physical` level and the routing on a `logical` level in meshed networks.
## Tree
The tree topology is an extended star topology that more extensive local networks have in this structure. This is especially useful when several topologies are combined. This topology is often used, for example, in larger company buildings.
## Hybrid
Hybrid networks combine two or more topologies so that the resulting network does not present any standard topologies.
## Daisy Chain
In the daisy chain topology, multiple hosts are connected by placing a cable from one node to another.
Since this creates a chain of connections, it is also known as a daisy-chain configuration in which multiple hardware components are connected in a series. This type of networking is often found in automation technology (`CAN`).

# Proxies
A proxy is when a device or service sits in the middle of a connection and acts as a mediator. The `mediator` is the critical piece of information because it means the device in the middle must be able to inspect the contents of the traffic. Without the ability to be a `mediator`, the device is technically a `gateway`, not a proxy.
If you have trouble remembering this, proxies will almost always operate at Layer 7 of the OSI Model. There are many types of proxy services, but the key ones are:
- Dedicated Proxy / Forward Proxy: A Forward Proxy is when a client makes a request to a computer, and that computer carries out the request.
- Reverse Proxy: Instead of being designed to filter outgoing requests, it filters incoming ones. The most common goal with a `Reverse Proxy`, is to listen on an address and forward it to a closed-off network. Many organizations use CloudFlare as they have a robust network that can withstand most DDOS Attacks. By using Cloudflare, organizations have a way to filter the amount (and type) of traffic that gets sent to their webservers. Another common Reverse Proxy is `ModSecurity`, a `Web Application Firewall` (`WAF`).
- (Non-) Transparent Proxy: All these proxy services act either `transparently` or `non-transparently`.With a `transparent proxy`, the client doesn't know about its existence. The transparent proxy intercepts the client's communication requests to the Internet and acts as a substitute instance. If it is a `non-transparent proxy`, we must be informed about its existence. For this purpose, we and the software we want to use are given a special proxy configuration that ensures that traffic to the Internet is first addressed to the proxy