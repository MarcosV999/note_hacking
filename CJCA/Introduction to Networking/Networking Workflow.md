# Networking Models
![[OSI_TCP_IP.png]]

## The OSI Model
The `OSI` model, often referred to as `ISO/OSI` layer model, is a reference model that can be used to describe and define the communication between systems. The reference model has `seven` individual layers, each with clearly separated tasks. published by the `International Telecommunication Union` (`ITU`) and the `International Organization for Standardization` (`ISO`). Therefore, the `OSI` model is often referred to as the `ISO/OSI` layer model.
## The TCP/IP Model
`TCP/IP` (`Transmission Control Protocol`/`Internet Protocol`) is a generic term for many network protocols. The Internet is entirely based on the `TCP/IP` protocol family. However, `TCP/IP` does not only refer to these two protocols but is usually used as a generic term for an entire protocol family.
## Packet Transfers
In a layered system, devices in a layer exchange data in a different format called a `protocol data unit` (`PDU`). 
![[PDU.png]]During the transmission, each layer adds a `header` to the `PDU` from the upper layer, which controls and identifies the packet. This process is called `encapsulation`. The header and the data together form the PDU for the next layer. The process continues to the `Physical Layer` or `Network Layer`, where the data is transmitted to the receiver.

# The OSI Model
|**Layer**|**Function**|
|---|---|
|`7.Application`|Among other things, this layer controls the input and output of data and provides the application functions.|
|`6.Presentation`|The presentation layer's task is to transfer the system-dependent presentation of data into a form independent of the application.|
|`5.Session`|The session layer controls the logical connection between two systems and prevents, for example, connection breakdowns or other problems.|
|`4.Transport`|Layer 4 is used for end-to-end control of the transferred data. The Transport Layer can detect and avoid congestion situations and segment data streams.|
|`3.Network`|On the networking layer, connections are established in circuit-switched networks, and data packets are forwarded in packet-switched networks. Data is transmitted over the entire network from the sender to the receiver.|
|`2.Data Link`|The central task of layer 2 is to enable reliable and error-free transmissions on the respective medium. For this purpose, the bitstreams from layer 1 are divided into blocks or frames.|
|`1.Physical`|The transmission techniques used are, for example, electrical signals, optical signals, or electromagnetic waves. Through layer 1, the transmission takes place on wired or wireless transmission lines.|
# The TCP/IP Model
| **Layer**       | **Function**                                                                                                                                                                                                                                     |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `4.Application` | The Application Layer allows applications to access the other layers' services and defines the protocols applications use to exchange data.                                                                                                      |
| `3.Transport`   | The Transport Layer is responsible for providing (TCP) session and (UDP) datagram services for the Application Layer.                                                                                                                            |
| `2.Internet`    | The Internet Layer is responsible for host addressing, packaging, and routing functions.                                                                                                                                                         |
| `1.Link`        | The Link layer is responsible for placing the TCP/IP packets on the network medium and receiving corresponding packets from the network medium. TCP/IP is designed to work independently of the network access method, frame format, and medium. |