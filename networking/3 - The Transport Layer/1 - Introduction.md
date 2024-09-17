The transport layer's main role is to provide communicate between processes running on different hosts. It extends the functionality of the IP service's delivery between 2 end system to delivery between 2 running application processes on those end systems.

#### Relationship to network layer protocols
Transport layer protocols provide *logical communication*  between applications. This means that applications can think of it like they're talking directly to another application, without having to worry about the processes that support their communication. Meanwhile, network layer protocols provide logical communication between hosts.

**Analogy**
There are 2 houses, one in the East Coast and one in the West Coast. Each house has 12 siblings who are cousins of the siblings in the other house. Each sibling writes letters to their cousins every week by way of writing messages and putting them in envelopes. Then, each house also has 1 child, Ann and Bill, that is responsible for collecting the mail of all their siblings and passing it to the postal service. They're also responsible for receiving all the replies and distributing them to their respective recipients.
- **application messages** - letters in envelopes
- **processes** - the cousins
- **hosts** - the houses
- **transport layer protocol** - Ann and Bill
- **network layer protocol** - the postal service

It's important to note that Ann and Bill don't help out with the transportation process in any intermediate mail center. They only operate in the vicinity of their houses. In the same manner, transport layer protocols are only responsible for moving messages from their application processes to the network edge. It doesn't have any say about how messages are routed in the network core. This is why routers don't act on any information the transport layer may have added.

**Constraints**
Just like the 2 cousin distributors would be constrained by the service of the postal service ie they can't guarantee a maximum delivery date, transport layer protocols are also limited by the service of network layer protocols. However, even though IP is unreliable, best-effort, and insecure, it doesn't stop transport layer protocols from providing extra services.

At a minimum, transport layer protocols provide:
- process-to-process communication (multiplexing, demultiplexing)

+UDP provides
- error-checking

+TCP provides
- reliable data transfer
- connection-orientation
- congestion control

#### Multiplexing & demultiplexing
The job of the transport layer at the receiving end is to receive the packet from the network layer below, inspect the fields in the segment, and forward it to the socket of the destination process. This process of delivering data to the correct socket is called **demultiplexing**. The process then of collecting data chunks from different sockets, adding appropriate headers to denote port numbers, and passing them to the network layer is called **multiplexing**.

During multiplexing, the transport layer protocol needs to know (1) the port number of the sending socket and (2) the port number of the destination socket. Demultiplexing is straightforward at this point since the transport layer protocol looks at the destination port on the receiving end and sends the data to the socket bound to that specific port.

**Connectionless (UDP) demultiplexing**
UDP sockets are identified by the tuple (destination IP address, destination port number). Connectionless multiplexing doesn't care about whether the source IP address and/or source port of 2 different packets are different, as long as they have the same destination IP and port, it will route the packet to the same port.

**Connection-oriented demultiplexing**
TCP sockets are identified by the 4-tuple (source IP address, source port, destination IP address, destination port). In connection-oriented demultiplexing, the protocol uses all 4 values to route the packet to a socket. If 2 packets have different source IP addresses and ports, they will be routed to 2 separate sockets. This is because a welcoming socket creates a new socket for each source IP and port, dedicated to just that specific source. And that dedicated connection socket will then be identified by its own 4 tuple.