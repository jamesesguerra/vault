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