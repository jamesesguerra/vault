### Router Internals
A router architecture contains input ports, output ports, and switching fabric that are implemented in hardware. As well as a routing processor that is implemented either in software / hardware

- **input ports**
	- terminate the incoming physical link at the router
	- communicate with layer 2 on the other end of the link
	- is where a function is computed to consult the forwarding table which output link to forward a packet to
- **switching fabric**
	- connects the input ports to the output ports
- **output ports**
	- stores packets received from the switching fabric and transmits these to the outgoing link
- **routing processor**
	- when using a traditional control plane, executes routing protocols, maintains and computes forwarding tables
	- when using SDN, communicates with the remote controller and installs forwarding table entries in the input ports

![[router-architecture.png]]

#### Types of forwarding
1. **destination-based** - an outgoing link is chosen for a packet based solely on the packet's destination IP
2. **generalized** - an outgoing link is chosen for a packet based on several factors and not just the destination IP

### Input port processing
An input port has functions for L1 (physical link termination), L2 (link-layer processing), as well as L3, which is the computation of which outgoing port a packet should be forwarded to. The routing processor computes the contents of the forwarding table, or receives if from an SDN controller. From there, it sends it to the input ports via a PCI bus, and is copied to the line card. This makes it so that an input port doesn't have to consult with the routing processor on a per-packet basis since there is already a copy of the forwarding table.

![[input-port-processing.png]]

An input port processes a packet using a *match plus action* abstraction. In the simplest case, it matches the destination IP address with the longest matching prefix entries in the forwarding table to determine which output port to send it to. In some cases, other input ports are currently using the fabric and therefore the port needs to send the packet to the queue where it's scheduled to be sent at a later time.

Apart from these, an input port also processes and updates a packet's version number, checksum, TTL, as well as counters for network management.

#### Switching
Switching can be accomplished via:
- **memory** - input port signals the CPU (routing processor) when a packet arrives. It then copies the packet to the CPU, the CPU computes for the output port using the destination IP, and copies that packet to that output port's buffers
- **a bus** - input port prepends a label to the packet, and directly forwards to a shared bus wherein all output ports will receive it, but only the appropriate port will keep it; only one packet can use the bus at any time
- **crossbar** - can send packets in parallel

#### Queuing Delays
Queuing delays can happen at both input and output ports. Factors that affect queuing delays are traffic load, switch fabric speed, and line speed.

**Input queueing**
- switch fabric isn't fast enough to transfer packets through the fabric without delay
- 2 packets destined for the same output port, one of the packets will be blocked and must wait at the input queue
- if the head-of-the-line (HOL) packet is blocked at the waiting queue, all other packets behind it must wait even if there's no contention for their output ports

![[hol-blocking.png]]

**Output queuing**
- output port can transmit only a single packet in a unit of time
- packet queues can form when the switching fabric is faster than the port line speeds