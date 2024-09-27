There has been a proliferation of **middleboxes** -- network devices that inspect, transform, and filter packets for purposes other than forwarding. These include NAT boxes, firewalls, and load balancers. The goal is to abstract out their functions to make a device that can carry out a wide range of network-layer functions.

### Generalized match + action
In the traditional destination-based forwarding approach, the abstraction of the activity being performed at input ports by routing processors is *matching* the longest prefix, and then doing an *action* which is forwarding it to the appropriate output port based on the destination address of the packet. In SDN, this action is generalized. From matching just the destination address, middleboxes can **match over multiple header fields** associated with different protocol layers as well. And then the action can include rewriting header values (NAT), blocking packets (firewall) and so on. Each packet switch receives a match-plus-action table that's computed and distributed by a remote controller.

#### OpenFlow
OpenFlow is a standard that pioneered the match-plus-action forwarding abstraction, controllers, and SDN revolution. A match-plus-action table is called a **flow table**, and each flow table includes:
- **a set of header field values** to which an incoming packet will be matched
- **counters** that count how many packets have been matched by a table entry
- **a set of actions to be taken** when a packet is matched with a flow table entry

##### Match
In OpenFlow 1.0, there are 11 header fields values from 3 different protocol layers you can match on. Thus, OpenFlow-enabled middleboxes can perform as a router and a switch.

![[match-headers.png]]

---
### SDN
SDN represents a significant separation of concerns when it comes to network functionality such as switches, controllers, and network control applications. In a pre-SDN model, a switch was embedded with software that makes it monolithic and sold by a single vendor. Through SDN, you can unbundle computing hardware, system software, and network applications. Data plane switches, SDN controllers, and network-control applications can be provided by different vendors.

You have 1 SDN controller that maintains accurate network state information to network-control applications running in the same plane. These network-control applications are the *brains* of the control plane as they monitor, specify, and control the data plane.

![[sdn-model.png]]

#### SDN controller layers
1. **southbound layer**
- communication between SDN controller and network-controlled devices
- controller communicates via a protocol, OpenFlow, to control the devices
- devices communicate to inform the controller about the current state of the network

2. **northbound layer**
- interface to the network-control applications
- network-control applications can read/write network state data and flow tables
- applications can also subscribe to events and respond 