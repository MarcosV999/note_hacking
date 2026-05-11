# Network Layer
In short, it is responsible for the following functions:

- `Logical Addressing`
- `Routing`

The most used protocols on this layer are:
- `IPv4` / `IPv6`
- `IPsec`
- `ICMP`
- `IGMP`
- `RIP`
- `OSPF`

# IP Addresses
Each host in the network located can be identified by the so-called `Media Access Control` address (`MAC`). This would allow data exchange within this one network. If the remote host is located in another network, knowledge of the `MAC` address is not enough to establish a connection. Addressing on the Internet is done via the `IPv4` and/or `IPv6` address, which is made up of the `network address` and the `host address`.
The IP address ensures the delivery of data to the correct receiver. We can imagine the representation of `MAC` and `IPv4` / `IPv6` addresses as follows:
- `IPv4` / `IPv6` - describes the unique postal address and district of the receiver's building.
- `MAC` - describes the exact floor and apartment of the receiver.

## IPv4 Structure
The most common method of assigning IP addresses is `IPv4`, which consists of a `32`-bit binary number combined into `4 bytes` consisting of `8`-bit groups (`octets`) ranging from `0-255`. The `IPv4` format allows 4,294,967,296 unique addresses. The IP network blocks were divided into `classes A - E`
## Subnet Mask
A further separation of these classes into small networks is done with the help of `subnetting`. This separation is done using the `netmasks`, which is as long as an IPv4 address. As with classes, it describes which bit positions within the IP address act as `network part` or `host part`.
## Network and Gateway Addresses
The `two` additional `IPs` added in the `IPs column` are reserved for the so-called `network address` and the `broadcast address`. Another important role plays the `default gateway`, which is the name for the IPv4 address of the `router` that couples networks and systems with different protocols and manages addresses and transmission methods. It is common for the `default gateway` to be assigned the first or last assignable IPv4 address in a subnet.
## Broadcast Address
The `broadcast` IP address's task is to connect all devices in a network with each other. `Broadcast` in a network is a message that is transmitted to all participants of a network and does not require any response. In this way, a host sends a data packet to all other participants of the network simultaneously and, in doing so, communicates its `IP address`, which the receivers can use to contact it. This is the `last IPv4` address that is used for the `broadcast`.
## CIDR
`Classless Inter-Domain Routing` (`CIDR`) is a method of representation and replaces the fixed assignment between IPv4 address and network classes (A, B, C, D, E). The division is based on the subnet mask or the so-called `CIDR suffix`, which allows the bitwise division of the IPv4 address space and thus into `subnets` of any size. `The `CIDR suffix` indicates how many bits from the beginning of the IPv4 address belong to the network.` It is a notation that represents the `subnet mask` by specifying the number of `1`-bits in the subnet mask.
Now the whole representation of the IPv4 address and the subnet mask would look like this:

- CIDR: `192.168.10.39/24`

# Subnetting
The division of an address range of IPv4 addresses into several smaller address ranges is called `subnetting`.

Submit the decimal representation of the subnet mask from the following CIDR: 10.200.20.0/27?
answer: 255.255.255.224
Formato binario: 11111111.11111111.11111111.11100000
cuarto octeto: 1110000 (111, parte network, 00000 parte host(5 bits))
2^7 + 2^6 + 2^5 = 128 + 64 + 32 = 224 

Submit the broadcast address of the following CIDR: 10.200.20.0/27?
answer: 10.200.20.31
- **Máscara en binario:** `11111111.11111111.11111111.11100000`
- **Máscara en decimal:** `255.255.255.224`
El número total de direcciones IP en esta subred se calcula como 2n, donde n son los bits de host:
- 25=32 direcciones en total.
Dado que la red comienza en **10.200.20.0** y tiene un tamaño de 32 direcciones, el rango completo va desde el `.0` hasta el `.31`.
- **Dirección de Red:** 10.200.20.0
- **Primer Host Usable:** 10.200.20.1
- **Último Host Usable:** 10.200.20.30
- **Dirección de Broadcast:** **10.200.20.31**


Split the network 10.200.20.0/27 into 4 subnets and submit the network address of the 3rd subnet as the answer.
### 1. Análisis de la red base
- **Red original:** 10.200.20.0
- **Máscara original:** /27
- **Bits de host disponibles:** 5 bits (32−27=5).
- **Direcciones totales:** 25=32.
### 2. Cálculo para crear 4 subredes
Para obtener 4 subredes, usamos la fórmula 2n=C (donde C es el número de subredes).
- 22=4, por lo que necesitamos pedir prestados **2 bits** adicionales a la red.
- **Nueva máscara:** /27 + 2 = **/29**.
- **Nuevo tamaño de subred (salto):** Con una máscara /29, quedan 3 bits de host (32−29=3). El tamaño de cada subred es 23=8 direcciones totales.

### 3. División de las subredes
Sumamos el salto de 8 en 8 desde la dirección de red inicial:
1. **1ª Subred:** 10.200.20.0 /29
2. **2ª Subred:** 10.200.20.8 /29
3. **3ª Subred: 10.200.20.16 /29**
4. **4ª Subred:** 10.200.20.24 /29

# MAC Addresses
Each host in a network has its own `48`-bit (`6 octets`) `Media Access Control` (`MAC`) address, represented in hexadecimal format. `MAC` is the `physical address` for our network interfaces.
## MAC Address Attack Vectors
There exist several attack vectors that can potentially be exploited through the use of MAC addresses:
- `MAC spoofing`: This involves altering the MAC address of a device to match that of another device, typically to gain unauthorized access to a network.
- `MAC flooding`: This involves sending many packets with different MAC addresses to a network switch, causing it to reach its MAC address table capacity and effectively preventing it from functioning correctly.
- `MAC address filtering`: Some networks may be configured only to allow access to devices with specific MAC addresses that we could potentially exploit by attempting to gain access to the network using a spoofed MAC address.
## Address Resolution Protocol
[Address Resolution Protocol](https://en.wikipedia.org/wiki/Address_Resolution_Protocol) (`ARP`) is a network protocol. It is an important part of the network communication used to resolve a network layer (layer 3) IP address to a link layer (layer 2) MAC address. It maps a host's IP address to its corresponding MAC address to facilitate communication between devices on a [Local Area Network](https://en.wikipedia.org/wiki/Local_area_network) (`LAN`). 
ARP is an important part of the network communication process because it allows devices to send and receive data using MAC addresses rather than IP addresses, which can be more efficient.

# IPv6 Addresses
`IPv6` is the successor of IPv4. In contrast to IPv4, the `IPv6` address is `128` bit long. The `prefix` identifies the host and network parts. The Internet Assigned Numbers Authority (`IANA`) is responsible for assigning IPv4 and IPv6 addresses and their associated network portions. In the long term, `IPv6` is expected to completely replace IPv4, which is still predominantly used on the Internet. In principle, however, IPv4 and IPv6 can be made available simultaneously (`Dual Stack`).
The `hexadecimal system` (`hex`) is used to make the binary representation more readable and understandable. We can only show `10` (`0-9`) states with the decimal system and `2` (`0` / `1`) with the binary system by using a single character. In contrast to the binary and decimal system, we can use the hexadecimal system to show `16` (`0-F`) states with a single character.
## Hexadecimal System
The `hexadecimal system` (`hex`) is used to make the binary representation more readable and understandable. We can only show `10` (`0-9`) states with the decimal system and `2` (`0` / `1`) with the binary system by using a single character. In contrast to the binary and decimal system, we can use the hexadecimal system to show `16` (`0-F`) states with a single character.
An IPv6 address can look like this:

- Full IPv6: `fe80:0000:0000:0000:dd80:b1a9:6687:2d3b/64`
- Short IPv6: `fe80::dd80:b1a9:6687:2d3b/64`

# VLANs
A `VLAN` is a logical grouping of network endpoints connected to defined ports on a switch, allowing the segmentation of networks by creating logical broadcast domains that can span multiple physical LAN segments. With `VLANs`, network administrators can segment networks based on factors such as team, function, department, or application, without worrying about the physical location of endpoints and users.