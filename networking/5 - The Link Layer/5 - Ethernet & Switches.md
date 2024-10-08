The Ethernet protocol is a link-layer protocol that has taken over the wired LAN space. It's not the only wired LAN protocol, but it emerged as the most popular due to its higher data rates and simplicity.

It started off as bus and star topologies with a hub, making it so that a LAN would be a broadcast LAN ie every other node would receive frames. **Hubs** are network devices that work with bits instead of frames, and are more like repeaters in that they simply recreates bits it receives onto all other links with a higher signal.

This changed in the early 2000s when the **switch** replaced the **hub**, making it a real switched LAN. Ethernet is a protocol that, like IP, provides an unreliable service. If frames are dropped due to a failed CRC check, it doesn't send a NAK. This is where the reliable data transfer of L4 comes in, as it makes it so that Ethernet retransmits without knowing it does.

#### Frame structure
- **preamble** - bits used to synchronize the clock of the receiving adapter based on the transmission speed of the sending adapter; *wakes up* the receiving adapter and prepares it for the important information
- **destination address** - destination MAC
- **source address** - source MAC
- **type** - multiplexes and demultiplexes Ethernet to multiple network-layer protocols; serves as glue to the network-layer, similar to how the protocol and port numbers work for L3 and L4
- **CRC** - allows for bit error detection by the receiver
- **data** - datagram payload within the range of 46-1500 bytes; if less than 46 bytes, add stuffing; if more than 1500 bytes (MTU for Ethernet), perform datagram fragmentation

![[ethernet-frame.png]]

#### Ethernet standards
Ethernet was finally standardized by the IEEE 802.3 working group. Since then, there have been several kinds of standards / technologies of Ethernet such as 100BASE-T and 40GBASE-T. The numbers part stands for the speed of the standard, BASE stands for baseband Ethernet which means that the transmission media only carries Ethernet frames, while the T stands for the physical media itself. In this case, T stands for twisted copper wire.

Certain Ethernet standards also have limits in terms of the physical length of the cable. 

It's interesting to note as well that with the emergence of the switch-based star topology of the Ethernet, links have mainly become a point-to-point link. Thus, there might not be a need for a media access control (MAC) protocol anymore.

---
#### Link-layer Switches
- receives frames from input interfaces and forwards them to outgoing links
- are transparent to hosts / routers i.e. they aren't addressed explicitly by hosts / routers
- have buffers if the rate at which frames arrive to their interfaces exceeds the link capacity of the interface
- are full-duplex, self-learning, plug-and-play packet switches

##### Forwarding & Filtering
The link-layer switch has 2 main functions:
- **filtering** - is the act of determining whether a frame should be dropped or forwarded
- **forwarding** - the act of determining which output link to forward a frame to, and forwarding it there

![[switch-table.png]]

Like routers, switches also have a table it uses to forward packets, namely the **switch table**. It has entries for its interfaces and what the MAC address is of the interface living on the other side of the link. If a switch table is configured completely and accurately, it won't broadcast a frame.

There are 3 cases:
1. There is no entry in the switch table for the destination MAC address, the switch then broadcasts the frame.
2. There is an entry in the switch table for the destination MAC address, but the interface `x` it arrived at is the same as the interface `y` for the MAC address' entry. In this case, the switch drops the packet.
3. There is an entry in the switch table for the destination MAC address, and the interfaces `x` and `y` are different, and so the switch forwards the frame into the output buffer before interface `y`.

![[typical-ethernet-setup.png]]

##### Self-learning switches
The switch tables of switches need not be configured manually by a network admin since switches are self-learning:
1. They start out empty.
2. As the switch receives frames, it records (1) the source MAC address of the frame, (2) the interface in which the frame arrived at, and (3) the current time
3. The switch deletes entries if no frames are received from that source MAC address within a certain period of time2

##### Properties of Switches
- **eliminates collisions** - never transmits more than 1 frame at any 1 time
- **heterogenous links** - can combine links with different speeds
- **management** - better management as switches can detect problems and disconnect malfunctioning adapters

##### Switches vs Routers
**Switches**
PROS
- easier management since switches are plug-and-play
- have relatively higher filtering & forwarding rates because they only operate at L2
CONS
- active topology is restricted to a spanning tree
- large switched networks would require large ARP tables

**Routers**
PROS
- IP address hierarchical structure makes it so that packets don't normally cycle (unlike flat MAC addresses)
- not limited to a spanning tree ie can use more efficient routing paths
- provide firewall protection
CONS
- not plug-and-play
- larger per-packet processing time because they have to process fields until L3
