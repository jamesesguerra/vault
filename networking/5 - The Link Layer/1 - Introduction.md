If the network layer is for delivering a packet from one host to a destination host ie host-to-host communication, the **link-layer** has a much more localized task of moving packets between nodes in the same network / LAN. In other words, the network layer decides where to forward packets, and the link-layer is responsible for getting the packet over a communication link to another physical adjacent node on the network.

You can then say that the link-layer is what connects interfaces, whether using some wired / wireless technology. It allows devices on a network to talk to each other **without** going through a L3 switch.

**Analogy:**
- person - L3 datagram
- transport segment (PH to JP) - communication link
- transportation mode (car, bus, plane) - link-layer protocol

#### Services
- encapsulation
- channel access when medium is shared



