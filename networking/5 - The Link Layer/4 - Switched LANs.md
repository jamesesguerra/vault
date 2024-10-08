> **Why do we need switches in L2 as well?**
> Simply because L3 switches are responsible for computing a path between 2 nodes, and switching until it reaches the node. Throughout the switching however, only 1 address is used for the destination node. L2 switches come in as they recognize physical / burned-in addresses and use those addresses to send a packet to an adjacent node.
> 
> In this manner, you can think of L3 switching as a high-level switching mechanism that knows how to switch packets so that a packet gets from a source host to a destination host over the Internet, whereas L2 switching is a lower-level mechanism that knows how to switch packets so that one a packet gets from a source node to a destination node in the same network. Both switches are indispensable and take care of different levels of switching.


L2 switches:
- switch link-layer frames rather than network-layer datagrams
- don't use OSPF / BGP
- recognize link-layer addresses instead of IP addresses

![[switched-lan.png]]

#### Link-layer addresses
Link-layer addresses aka MAC and physical addresses, are addresses also associated with network interface cards. It's important to note that the interfaces of L2 switches don't actually have MAC addresses themselves. This is because hosts or routers don't need to explicitly send a packet to a L2 switch, it happens transparently. That is, the host always addresses the router and a router always addresses the host directly.

MAC addresses are 6 bytes and are commonly represented in hexadecimal notation, with each byte being paired up. They're permanent (but can already by changed by software), and globally unique as manufacturers purchase chunks of the address space and fix the first 24 bits to denote their company, while creating unique combinations for the last 24.

Unlike IP addresses, MAC addresses don't have any hierarchy. That is, they're flat and don't change no matter where a host goes. They're like a social security number.

##### Sending process
When a NIC wants to send a frame to some destination NIC, it attaches the destination NIC's MAC address and sends it into the LAN. Sometimes, a switch will send a frame onto all of its interfaces, which is why when NIC's receive frames, they need to verify that the frame's MAC address is addressed to them. If it does, it sends it up to the upper layer. If it doesn't it discards the frame.

In some cases, the sending NIC *does* want all other NICs to process a frame in which case it attaches a special MAC **broadcast address**, 48 consecutive 1s (FF-FF-FF-FF-FF-FF in hex).

#### Address Resolution Protocol (ARP)
Because both IP addresses and MAC addresses will be at play in the link-layer, there's a need to translate them. This is the job of ARP. Every host and router has an ARP module used to create an ARP table. ARP tables are mappings much like DNS mappings, that map IP addresses to MAC addresses. Each entry also has a TTL.

When a host / router wants to send a packet to an IP address but doesn't know its MAC address, it sends an ARP packet into the LAN with source and destination IP addresses set, but uses the broadcast MAC address as the destination MAC. In this way, the destination host can respond with its MAC address. The source node then stores this entry into its ARP table.

##### Sending a packet out of the subnet
In order for a node to send a packet to node in another subnet, it has to go through a L3 switch. The process is something like:
- use ARP to know the MAC address of the router's interface in this LAN
- send the packet to the router interface
- router checks forwarding table and forwards packet to outgoing interface
- router has to use ARP first to resolve the MAC address of the ultimate destination host

