# Internet Architecture
describes how data is organized, transmitted, and managed across networks.
## Peer-to-Peer (P2P) Architecture
In a `Peer-to-Peer (P2P`) network, each node, whether it's a computer or any other device, acts as both a client and a server. P2P networks can be fully decentralized, with no central server involved, or partially centralized, where a central server may coordinate some tasks but does not host data. A popular example of Peer-to-Peer (P2P) architecture is torrenting, as seen with applications like BitTorrent.

| **Advantage**       | **Description**                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------- |
| `Scalability`       | Adding more nodes can increase total resources (storage, CPU, etc.).                                    |
| `Resilience`        | If one node goes offline, others can continue functioning.                                              |
| `Cost distribution` | Resource burden, like bandwidth and storage, is distributed among peers, making it more cost-efficient. |

| **Disadvantage**               | **Description**                                                         |
| ------------------------------ | ----------------------------------------------------------------------- |
| `Management complexity`        | Harder to control and manage updates/security policies across all nodes |
| `Potential reliability issues` | If too many peers leave, resources could be unavailable.                |
| `Security challenges`          | Each node is exposed to potential vulnerabilities.                      |

## Client-Server Architecture
The `Client-Server` model is one of the most widely used architectures on the Internet. In this setup, clients, which are user devices, send requests, such as a web browser asking for a webpage, and servers respond to these requests. This model typically involves centralized servers where data and applications reside, with multiple clients connecting to these servers to access services and resources.

#### Single-Tier Architecture
In a `single-tier` architecture, the client, server, and database all reside on the same machine. This setup is straightforward but is rarely used for large-scale applications due to significant limitations in scalability and security.
#### Two-Tier Architecture
The `two-tier` architecture splits the application environment into a client and a server. The client handles the presentation layer, and the server manages the data layer. This model is typically seen in desktop applications where the user interface is on the user's machine, and the database is on a server.
#### Three-Tier Architecture
A `three-tier` architecture introduces an additional layer between the client and the database server, known as the application server. In this model, the client manages the presentation layer, the application server handles all the business logic and processing, and the third tier is a database server.
#### N-Tier Architecture
In more complex systems, an `N-tier` architecture is used, where `N` refers to any number of separate tiers used beyond three. This setup involves multiple levels of application servers, each responsible for different aspects of business logic, processing, or data management. N-tier architectures are highly scalable and allow for distributed deployment, making them ideal for web applications and services that demand robust, flexible solutions.

| **Advantage**         | **Description**                                     |
| --------------------- | --------------------------------------------------- |
| `Centralized control` | Easier to manage and update.                        |
| `Security`            | Central security policies can be applied.           |
| `Performance`         | Dedicated servers can be optimized for their tasks. |

| **Disadvantage**            | **Description**                                                                                                                                         |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Single point of failure`   | If the central server goes down, clients lose access.                                                                                                   |
| `High Cost and Maintenance` | Setting up and sustaining a client-server architecture is expensive, requiring constant operation and expert management , making it costly to maintain. |
| `Network Congestion`        | High traffic on the network can lead to congestion, slowing down or even disrupting connections when too many clients access the server simultaneously. |

## Hybrid Architecture
A `Hybrid` model blends elements of both `Client-Server` and `Peer-to-Peer (P2P)`

| **Advantage** | **Description**                                                                       |
| ------------- | ------------------------------------------------------------------------------------- |
| `Efficiency`  | Relieves workload from servers by letting peers share data.                           |
| `Control`     | Central server can still manage user authentication, directory services, or indexing. |

|**Disadvantage**|**Description**|
|---|---|
|`Complex Implementation`|Requires more sophisticated design to handle both centralized and distributed components.|
|`Potential Single Point of Failure`|If the central coordinating server fails, peer discovery might stop.|

## Cloud Architecture
refers to computing infrastructure that is hosted and managed by third-party providers, such as AWS, Azure, and Google Cloud. This architecture operates on a virtualized scale following a client-server model.
Services like Google Drive or Dropbox are some examples of Cloud Architecture operating under the `SaaS` (Software as a Service) model. 
Five essential characteristics that define a Cloud Architecture:
- `On-demand self-service`: Automatically set up and manage the services without human help.
- `Broad network access`: Access services from any internet-connected device.
- `Resource pooling`: Share and allocate service resources dynamically among multiple users.
- `Rapid elasticity`: Quickly scale services up or down based on demand.
- `Measured service`: Only pay for the resources you use, tracked with precision.

| **Advantage**                | **Description**                                           |
| ---------------------------- | --------------------------------------------------------- |
| `Scalability`                | Easily add or remove computing resources as needed.       |
| `Reduced cost & maintenance` | Hardware managed by the cloud provider.                   |
| `Flexibility`                | Access services from anywhere with Internet connectivity. |

|**Disadvantage**|**Description**|
|---|---|
|`Vendor lock-in`|Migrating from one cloud provider to another can be complex.|
|`Security/Compliance`|Relying on a third party for data hosting can introduce concerns about data privacy.|
|`Connectivity`|Requires stable Internet access.|

## Software-Defined Architecture (SDN)
`Software-Defined Networking (SDN)` is a modern networking approach that separates the control plane, which makes decisions about where traffic is sent, from the data plane, which actually forwards the traffic. Traditionally, network devices like routers and switches housed both of these planes. However, in SDN, the control plane is centralized within a software-based controller. `Large enterprises or cloud providers use SDN to dynamically allocate bandwidth and manage traffic flows according to real-time demands`. 

| **Advantage**                  | **Description**                                                                                             |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------- |
| `Centralized control`          | Simplifies network management.                                                                              |
| `Programmability & Automation` | Network configurations can be changed quickly through software instead of manually configuring each device. |
| `Scalability & Efficiency`     | Can optimize traffic flows dynamically, leading to better resource utilization.                             |

| **Disadvantage**           | **Description**                                                               |
| -------------------------- | ----------------------------------------------------------------------------- |
| `Controller Vulnerability` | If the central controller goes down, the network might be adversely affected. |
| `Complex Implementation`   | Requires new skill sets and specialized software/hardware.                    |

## Key Comparisons
| `Architecture`  | `Centralized`                   | `Scalability`        | `Ease of Management`               | `Typical Use Cases`                |
| --------------- | ------------------------------- | -------------------- | ---------------------------------- | ---------------------------------- |
| `P2P`           | Decentralized (or partial)      | High (as peers grow) | Complex (no central control)       | File-sharing, blockchain           |
| `Client-Server` | Centralized                     | Moderate             | Easier (server-based)              | Websites, email services           |
| `Hybrid`        | Partially central               | Higher than C-S      | More complex management            | Messaging apps, video conferencing |
| `Cloud`         | Centralized in provider’s infra | High                 | Easier (outsourced)                | Cloud storage, SaaS, PaaS          |
| `SDN`           | Centralized control plane       | High (policy-driven) | Moderate (needs specialized tools) | Datacenters, large enterprises     |

# Wireless Networks
A `wireless network` is a sophisticated communication system that employs radio waves or other wireless signals to connect various devices such as computers, smartphones, and IoT gadgets, enabling them to communicate and exchange data without the need for physical cables.

| **Advantages**         | **Description**                                        |
| ---------------------- | ------------------------------------------------------ |
| `Mobility`             | Users can move around freely within the coverage area. |
| `Ease of installation` | No need for extensive cabling.                         |
| `Scalability`          | Adding new devices is simpler than a wired network.    |

| **Disadvantages**   | **Description**                                                                                  |
| ------------------- | ------------------------------------------------------------------------------------------------ |
| `Interference`      | Wireless signals can be disrupted by walls, other electronics, or atmospheric conditions.        |
| `Security risks`    | Without proper security measures, wireless transmissions can be easier to intercept.             |
| `Speed limitations` | Generally, wireless connections are slower compared to wired connections of the same generation. |

## Wireless Router
In a home or small office setting, a `wireless router` combines the functions of:

| **Function**            | **Description**                                                                     |
| ----------------------- | ----------------------------------------------------------------------------------- |
| `Routing`               | Directing data to the correct destination (within your network or on the internet). |
| `Wireless Access Point` | Providing Wi-Fi coverage.                                                           |

| **Component**                    | **Description**                                                               |
| -------------------------------- | ----------------------------------------------------------------------------- |
| `WAN (Wide Area Network) Port`   | Connects to your internet source (e.g., a cable modem).                       |
| `LAN (Local Area Network) Ports` | For wired connections to local devices (e.g., desktop computer, printer).     |
| `Antennae`                       | Transmit and receive wireless signals. (Some routers have internal antennae.) |
| `Processor & Memory`             | Handle routing and network management tasks.                                  |

## Mobile Hotspot
A `mobile hotspot` allows a smartphone (or other hotspot devices) to share its cellular data connection via Wi-Fi. Other devices (laptops, tablets, etc.) then connect to this hotspot just like they would to a regular Wi-Fi network. A mobile hotspot uses cellular data, connecting devices to the internet via a cellular network, such as 4G or 5G.
## Cell Tower
A `cell tower` (or `cell site`) is a structure where antennas and electronic communications equipment are placed to create a cellular network cell.
Cell towers function through a combination of radio transmitters and receivers, which are equipped with antennas to communicate over specific radio frequencies. These towers are managed by Base Station Controllers (BSC), which oversee the operation of multiple towers. BSCs handle the transfer of calls and data sessions from one tower to another when users move across different cells. 
Cell towers are differentiated by their coverage capacities and categorized primarily into `macro cells` and `micro/small cells`. Macro cells consist of large towers that provide extensive coverage over several kilometers, micro and small cells are smaller installations typically located in urban centers.

## Frequencies in Wireless Communications
| **Frequency Bands**                                                                                                                                                                            |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `1.` **2.4 GHz (Gigahertz)** – Used by older Wi-Fi standards (802.11b/g/n). Better at penetrating walls, but can be more prone to interference (e.g., microwaves, Bluetooth).                  |
| `2.` **5 GHz** – Used by newer Wi-Fi standards (802.11a/n/ac/ax). Faster speeds, but shorter range.                                                                                            |
| `3.` **Cellular Bands** – For 4G (LTE) and 5G. These range from lower frequencies (700 MHz) to mid-range (2.6 GHz) and even higher frequencies for some 5G services (up to 28 GHz and beyond). |
Lower frequencies tend to travel farther but are limited in the amount of data they can carry, making them suitable for broader coverage with less data demand.