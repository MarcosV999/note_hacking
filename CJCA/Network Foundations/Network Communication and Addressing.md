# Network Communication
For a network to function and facilitate communication properly, there are three crucial components: `MAC addresses`, `IP addresses`, and `ports`
## MAC Addresses
is a unique identifier assigned to the network interface card (NIC) of a device, allowing it to be recognized on a local network. The MAC address is crucial for communication within a local network segment. Each MAC address is 48 bits long and is typically represented in hexadecimal format, appearing as six pairs of hexadecimal digits separated by colons or hyphens (e.g., `00:1A:2B:3C:4D:5E`). This design ensures that every MAC address is globally unique, allowing devices worldwide to communicate without address conflicts.
Additionally, the `Address Resolution Protocol (ARP)` plays a crucial role by mapping IP addresses to MAC addresses, allowing devices to find the MAC address associated with a known IP address within the same network.
## IP Addresses
is a numerical label assigned to each device connected to a network that utilizes the Internet Protocol for communication. IPv4 addresses consist of a 32-bit address space, typically formatted as four decimal numbers separated by dots, such as `192.168.1.1`. In contrast, IPv6 addresses have a 128-bit address space and are formatted in eight groups of four hexadecimal digits, an example being `2001:0db8:85a3:0000:0000:8a2e:0370:7334`.
## Ports
A `port` is a number assigned to specific processes or services on a network to help computers sort and direct network traffic correctly. It functions at the `Transport Layer (Layer 4)` of the OSI model and works with protocols such as TCP and UDP. Port numbers range from `0` to `65535`(2^16). 
#### Well-Known Ports (0-1023):
`Well-known ports`, numbered from 0 to 1023, are reserved for common and universally recognized services and protocols, as standardized and managed by the [Internet Assigned Numbers Authority (IANA)](https://www.iana.org/).
#### Registered Ports (1024-49151):
are not as strictly regulated as `well-known ports` but are still registered and assigned to specific services by the IANA. These ports are commonly used for external services that users might install on a device. For instance, many database services, such as Microsoft SQL Server, use port 1433.
#### Dynamic/Private Ports (49152-65535):
also known as ephemeral ports and are typically used by client applications to send and receive data from servers, such as when a web browser connects to a server on the internet. These ports are called `dynamic` because they are not fixed;

# Dynamic Host Configuration Protocol (DHCP)
`DHCP` is a network management protocol used to automate the process of configuring devices on IP networks. It allows devices to automatically receive an IP address and other network configuration parameters, such as subnet mask, default gateway, and DNS servers, without manual intervention.
The DHCP process involves a series of interactions between the client and the DHCP server. This process is often referred to as `DORA`, an acronym for `Discover`, `Offer`, `Request`, and `Acknowledge`

| **Role**      | **Description**                                                                                                                                                      |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DHCP Server` | A network device (like a router or dedicated server) that manages IP address allocation. It maintains a pool of available IP addresses and configuration parameters. |
| `DHCP Client` | Any device that connects to the network and requests network configuration parameters from the DHCP server.                                                          |
Below, we break down each step of the DORA process:

| **Step**         | **Description**                                                                                                                                                                         |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `1. Discover`    | When a device connects to the network, it broadcasts a **DHCP Discover** message to find available DHCP servers.                                                                        |
| `2. Offer`       | DHCP servers on the network receive the discover message and respond with a **DHCP Offer** message, proposing an IP address lease to the client.                                        |
| `3. Request`     | The client receives the offer and replies with a **DHCP Request** message, indicating that it accepts the offered IP address.                                                           |
| `4. Acknowledge` | The DHCP server sends a **DHCP Acknowledge** message, confirming that the client has been assigned the IP address. The client can now use the IP address to communicate on the network. |

# Network Address Translation (NAT)

One solution to this insufficiency issue(IPv4 offers a finite number of IP addresses) is `Network Address Translation (NAT)` The idea is that `NAT` allows multiple devices on a private network to share a single public IP address. This not only helps conserve the limited pool of public IP addresses but also adds a layer of security to the internal network.
#### Private vs. Public IP Addresses
 - `Public IP` addresses are globally unique identifiers assigned by Internet Service Providers (ISPs). Devices equipped with these IP addresses can be accessed from anywhere on the Internet, allowing them to communicate across the global network. For example, the IP address 8.8.8.8 is used for Google's DNS server.
 - `Private IP` addresses are designated for use within local networks such as homes, schools, and offices. These addresses are not routable on the global internet, meaning packets sent to these addresses are not forwarded by internet backbone routers. Defined by RFC 1918, common IPv4 private address ranges include 10.0.0.0 to 10.255.255.255, 172.16.0.0 to 172.31.255.255, and 192.168.0.0 to 192.168.255.255.
`Network Address Translation (NAT)` is a process carried out by a router, that modifies the source or destination IP address in the headers of IP packets as they pass through. This modification is used to translate the private IP addresses of devices within a local network to a single public IP address that is assigned to the router.
The process of NAT translation begins when a device, say the laptop, sends a request to visit a website like [www.google.com](http://www.google.com). This request packet, originating with the private IP of 192.168.1.10, is sent to the router. Here, `the NAT function of the router modifies the source IP in the packet header from the private IP to the public IP of the router`, 203.0.113.50. This packet then travels across the internet to reach the intended web server.
#### Types of NAT
| **Type**                         | **Description**                                                                                                                                                                                                                                                                                                                                         |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Static NAT`                     | Involves a one-to-one mapping, where each private IP address corresponds directly to a public IP address.                                                                                                                                                                                                                                               |
| `Dynamic NAT`                    | Assigns a public IP from a pool of available addresses to a private IP as needed, based on network demand.                                                                                                                                                                                                                                              |
| `Port Address Translation (PAT)` | Also known as NAT Overload, is the most common form of NAT in home networks. Multiple private IP addresses share a single public IP address, differentiating connections by using unique port numbers. This method is widely used in home and small office networks, allowing multiple devices to share a single public IP address for internet access. |

| **Benefits**                                                                            |
| --------------------------------------------------------------------------------------- |
| Conserves the limited IPv4 address space.                                               |
| Provides a basic layer of security by not exposing internal network structure directly. |
| Flexible for internal IP addressing schemes.                                            |

| **Trade-Offs**                                                                                                         |
| ---------------------------------------------------------------------------------------------------------------------- |
| Complex services like hosting a public server behind NAT can require additional configuration (e.g., port forwarding). |
| NAT can break certain protocols that rely on end-to-end connectivity without special handling.                         |
| Adds complexity to troubleshooting connectivity issues.                                                                |

# Domain Name System (DNS)

is like the phonebook of the internet. It helps us find the right number (an IP address) for a given name (a domain such as `www.google.com`). Without DNS, we would need to memorize long, often complex IP addresses for every website we visit. 
#### Domain Names vs. IP Addresses

| **Address**   | **Description**                                                            |
| ------------- | -------------------------------------------------------------------------- |
| `Domain Name` | A readable address like `www.example.com` that people can easily remember. |
| `IP Address`  | A numerical label (e.g., `93.184.216.34`)                                  |
#### DNS Hierarchy

| **Layer**                  | **Description**                                                                   |
| -------------------------- | --------------------------------------------------------------------------------- |
| `Root Servers`             | The top of the DNS hierarchy.                                                     |
| `Top-Level Domains (TLDs)` | Such as `.com`, `.org`, `.net`, or country codes like `.uk`, `.de`.               |
| `Second-Level Domains`     | For example, `example` in `example.com`.                                          |
| `Subdomains or Hostname`   | For instance, `www` in `www.example.com`, or `accounts` in `accounts.google.com`. |
#### DNS Resolution Process (Domain Translation)

| **Step** | **Description**                                                                                                                                                  |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Step 1` | We type `www.example.com` into our browser.                                                                                                                      |
| `Step 2` | Our computer checks its local DNS cache (a small storage area) to see if it already knows the IP address.                                                        |
| `Step 3` | If not found locally, it queries a `recursive DNS server`. This is often provided by our Internet Service Provider or a third-party DNS service like Google DNS. |
| `Step 4` | The recursive DNS server contacts a `root server`, which points it to the appropriate `TLD name server` (such as the `.com` domains, for instance).              |
| `Step 5` | The TLD name server directs the query to the `authoritative name server` for `example.com`.                                                                      |
| `Step 6` | The authoritative name server responds with the IP address for `www.example.com`.                                                                                |
| `Step 7` | The recursive server returns this IP address to your computer, which can then connect to the website’s server directly.                                          |


