# Network Security
the term security refers to the measures taken to protect data, applications, devices, and systems within this network from unauthorized access or damage. The goal is to uphold and maintain the `CIA triad`:

|**Principle**|**Description**|
|---|---|
|`Confidentiality`|Only authorized users can view the data.|
|`Integrity`|The data remains accurate and unaltered.|
|`Availability`|Network resources are accessible when needed.|

## Firewalls
A `Firewall` is a network security device, either hardware, software, or a combination of both, that monitors incoming and outgoing network traffic. Firewalls enforce a set of rules (known as `firewall policies` or `access control lists`) to determine whether to `allow` or `block` specific traffic.
This process, known as traffic filtering, is defined by system administrators as permitting or denying traffic based on specific conditions, ensuring that only authorized connections are allowed.
Below are some of the different types of firewalls:
- Packet Filtering Firewall: Operates at Layer 3 (Network) and Layer 4 (Transport) of the OSI model. Examines source/destination IP, source/destination port, and protocol type. A simple router ACL that only allows HTTP (port 80) and HTTPS (port 443) while blocking other ports.
- Stateful Inspection Firewall: Tracks the state of network connections. More intelligent than packet filters because they understand the entire conversation. Only allows inbound data that matches an already established outbound request.
- Application Layer Firewall (Proxy Firewall): Operates up to Layer 7 of the OSI model. Can inspect the actual content of traffic and block malicious requests. A web proxy that filters out malicious HTTP requests containing suspicious patterns.
- Next-Generation Firewall (NGFW): Combines stateful inspection with advanced features like deep packet inspection, intrusion detection/prevention, and application control. A modern firewall that can block known malicious IP addresses, inspect encrypted traffic for threats, and enforce application-specific policies.
## Intrusion Detection and Prevention Systems (IDS/IPS)
are security solutions designed to monitor and respond to suspicious network or system activity. An Intrusion Detection System (IDS) observes traffic or system events to identify malicious behavior or policy violations, generating alerts but not blocking the suspicious traffic. In contrast, an Intrusion Prevention System (IPS) operates similarly to an IDS but takes an additional step by preventing or rejecting malicious traffic in real time.
`The key difference lies in their actions: an IDS detects and alerts, while an IPS detects and prevents.`

| **Techniques**              | **Description**                                       |
| --------------------------- | ----------------------------------------------------- |
| `Signature-based detection` | Matches traffic against a database of known exploits. |
| `Anomaly-based detection`   | Detects anything unusual compared to normal activity. |
Below are some of the different types of firewalls IDS/IPS.
#### Network-Based IDS/IPS (NIDS/NIPS)
Hardware device or software solution placed at strategic points in the network to inspect all passing traffic. Example: A sensor connected to the core switch that monitors traffic within a data center.
#### Host-Based IDS/IPS (HIDS/HIPS)
Runs on individual hosts or devices, monitoring inbound/outbound traffic and system logs for suspicious behavior on that specific machine. Example: An antivirus or endpoint security agent installed on a server.

## Best Practices

| **Practice**                   | **Description**                                                                                                                                                                                                      |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Define Clear Policies`        | Consistent firewall rules based on the principle of `least privilege` (only allow what is necessary).                                                                                                                |
| `Regular Updates`              | Keep firewall, IDS/IPS signatures, and operating systems up to date to defend against the latest threats.                                                                                                            |
| `Monitor and Log Events`       | Regularly review firewall logs, IDS/IPS alerts, and system logs to identify suspicious patterns early.                                                                                                               |
| `Layered Security`             | Use `defense in depth` (a strategy that leverages multiple security measures to slow down an attack) with multiple layers: Firewalls, IDS/IPS, antivirus, and endpoint protection to cover different attack vectors. |
| `Periodic Penetration Testing` | Test the effectiveness of the security policies and devices by simulating real attacks.                                                                                                                              |

# Data Flow Example

What happens when a user tries to access a website from their laptop. Below is a breakdown of these events in a client-server model.
#### 1. Accessing the Internet
| **Steps**                                                                                                 |
| --------------------------------------------------------------------------------------------------------- |
| The laptop first identifies the correct wireless network/SSID                                             |
| If the network uses WPA2/WPA3, the user must provide the correct password or credentials to authenticate. |
| Finally, the connection is established, and the DHCP protocol takes over the IP configuration.            |
#### 2. Checking Local Network Configuration (DHCP)
When a user opens a web browser and types in [www.example.com](http://www.example.com) to access a website, the browser prepares to send out a request for the webpage. Before a packet leaves the laptop, the operating system checks for a valid IP address for the local area network.

| **Steps**               | **Description**                                                                                                                                                                        |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `IP Address Assignment` | If the laptop does not already have an IP, it requests one from the home router's `DHCP` server. This IP address is only valid within the local network.                               |
| `DHCP Acknowledgement`  | The DHCP server assigns a private IP address (for example, _192.168.1.10_) to the laptop, along with other configuration details such as subnet mask, default gateway, and DNS server. |
#### 3. DNS Resolution
Next, the laptop needs to find the IP address of `www.example.com`. For this to happen, the following steps must be taken.

| **Steps**      | **Description**                                                                                                                                         |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DNS Query`    | The laptop sends a DNS query to the DNS server, which is typically an external DNS server provided by the ISP or a third-party service like Google DNS. |
| `DNS Response` | The DNS server looks up the domain `www.example.com` and returns its IP address (e.g., 93.184.216.34).                                                  |

#### 4. Data Encapsulation and Local Network Transmission
Now that the laptop has the destination IP address, it begins preparing the data for transmission. The following steps occur within the `OSI/TCP-IP` model:

|**Steps**|**Description**|
|---|---|
|`Application Layer`|The browser creates an HTTP (or HTTPS) request for the webpage.|
|`Transport Layer`|The request is wrapped in a TCP segment (or UDP, but for web traffic it's typically TCP). This segment includes source and destination ports (HTTP default port 80, HTTPS default port 443).|
|`Internet Layer`|The TCP segment is placed into an IP packet. The source IP is the laptop's private IP (e.g., 192.168.1.10), and the destination IP is the remote server’s IP (93.184.216.34).|
|`Link Layer`|The IP packet is finally placed into an Ethernet frame (if we're on Ethernet) or Wi-Fi frame. Here, the MAC (Media Access Control) addresses are included (source MAC is the laptop's network interface, and destination MAC is the router's interface).|

When the encapsulated frame is ready, the laptop checks its ARP table or sends an ARP request to find the MAC address of the default gateway (the router). Then, the frame is sent to the router using the router’s MAC address as the destination at the `link layer`.

#### 5. Network Address Translation (NAT)
Once the router receives the frame, it processes the IP packet. At this point, the router replaces the private IP (192.168.1.10) with its public IP address (e.g., 203.0.113.45) in the packet header. Next, the router forwards the packet to the ISP's network, and from there, it travels across the internet to the destination IP (93.184.216.34). During this process, the packet goes through many intermediate routers that look at the destination IP and determine the best path to reach that network.

#### 6. Server Receives the Request and Responds
Upon reaching the destination network, the server's firewall, if there is one, checks if the incoming traffic on port 80 or 443 is allowed. If it passes firewall rules, it goes to the server hosting `www.example.com`. Next, the web server software receives and processes the request, prepares the webpage, and sends it back as a response.

#### 7. Decapsulation and Display
Finally, our laptop receives the response and strips away the Ethernet/Wi-Fi frame, the IP header, and the TCP header, until the application layer data is extracted. The laptop's browser reads the HTML/CSS/JavaScript, and ultimately displays the webpage.

![[Data Flow Example.png]]