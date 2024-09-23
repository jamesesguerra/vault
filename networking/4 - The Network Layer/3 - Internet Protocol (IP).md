#### IPv4 datagram format
- **version number** - whether IPv4 or IPv6; router will know how to handle since they have different formats
- **header length** - specifies the length of the header so that the router knows where the payload actually starts
- **types of service (TOS)** - specifies whether the datagram carries real-time traffic or non-real-time traffic as it can be useful
- **datagram length** - total length of the datagram so the router knows if it should fragment it based on the MTU
- **identifier, flags, fragmentation offset** - fields for end systems to handle assembling datagram fragments
- **TTL** - to prevent packet from looping infinitely across routers
- **protocol** - specifies the number of the transport protocol to send the datagram to; this is analogous to the port number in L4 as a port number is the link between L4 and L7, whereas the protocol number is the link between L3 and L4
- **header checksum** - for detecting bit errors
- **source & destination IP** - to know where to forward packets
- **options** - rarely used bits to extend the IP header
- **data (payload)** - commonly contains either TCP or UDP data, but can also contain other traffic such as ICMP

![[datagram-format.png]]

#### IPv4 data fragmentation
The size of the protocol data units that link-layer protocols carry can vary. For example, ethernet frames can carry 1500 bytes whereas other wide-area links are restricted to 576. The maximum amount that a link-layer frame can carry is called the **maximum transmission unit (MTU)**. Link-layer protocols then place a cap on the size of the IP datagrams it can handle. This becomes a problem when a router has to forward a datagram to a link that cannot support the datagram's size.

To handle this problem, the datagram is fragmented or sliced into 2 or more pieces. These separate pieces are then encapsulated in link-layer frames. Each of these smaller datagrams is called a **fragment**. The network component responsible for reassembling these fragments are the end systems, and they can reassemble them as they know which fragments are a part of a bigger whole using the identification number which is stamped on each fragment.

---
### IPv4 Addressing
IP addresses are 32-bit numbers typically written in dotted-decimal notation. They are numbers associated with the **interfaces** of hosts or routers, the interfaces that connect a host/routers and a physical link. Each IP address in the global Internet must be globally unique, and each IP address isn't chosen randomly. Instead, part of an IP address is determined by the subnet it's connected to.

#### Subnets
A **subnet** is a network of several hosts that can communicate without passing through an intervening router (L3). Typically, devices in a subnet are connected via some link-layer technology, either Ethernet or 802.11. Each subnet is assigned an IP address with a **subnet mask** that defines how many of the leftmost of the 32-bit IP address define the subnet address. In other words, subnet masks specify how many leftmost bits every device in a subnet will have in common.

An IP address therefore has 2 parts:
- **subnet part** - defines the common bits between devices in the same subnet
- **host part** - defines lower order bits that distinguish individual interfaces

This is in the form: `a.b.c.d/x` where x is the number of bits in the subnet portion. This format is called Classless InterDomain Routing (CIDR) which generalizes the subnet format. In CIDR, the `x` specifies the network portion / prefix.

![[subnets.png]]

**Why do we need divide IP addresses this way?**
We need to divide IP address into a network portion and a host portion to make it much easier for routers to route packets to different networks. When a router is handling a packet, it need only consider the network part of the address and this drastically reduces the size of their forwarding tables since it needs only a single entry of the form `a.b.c.d/x` to forward packets to *any* destination within that organization.

Once the packet reaches the organization, the host bits are the bits considered when forwarding a packet within the organization.

**Route aggregation**
Subnets can further divide their address space into more subnets (by increasing the number of subnet bits). This makes it so that the subnets are in a hierarchy, wherein an ISP / organization can advertise just a single subnet for many lower tier subnets.

**Broadcast IP address**
The broadcast IP address is `255.255.255.255` (all 1s). When a packet is addressed to this IP, the packet is forwarded to all devices in the subnet.

---

### IPv6 addressing
IPv6 was invented to solve the problem of running out of IPv4 addresses. When IPv4 was created, people didn't know the that the Internet would be as widespread as it it today.

**Differences:**
- increased address size from 32 to 128 bits, as well as anycast address
- flow labeling to label services that require special handling (real-time non-real-time traffic)
- fragmentation is done at the end systems and not the router
- no more header checksum as it is redundant with L2 and L4 already performing error-checking

**Compatibility with IPv4**
- need to use tunneling wherein an IPv6 datagram is placed into an IPv4 datagram to route it through IPv4-enabled routers

Takeway: it's easy to change application layer protocols, but not network layer ones

