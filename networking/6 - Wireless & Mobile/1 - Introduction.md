![[wireless-infra.png]]

#### Elements of a wireless network
- **wireless hosts** - phone, tablet, laptop etc.; may or may not be mobile
- **wireless links** - hosts connect to a base station / to another wireless hosts through a wireless communication link; have the key characteristics of coverage rate and link rate
- **base station**
	- infrastructure responsible for sending and receiving data to and from a wireless host that is associated with that base station
	- examples of these include cell towers and access points
	- when a host moves out of a base station and into another, it changes the base station which it's associated with in a process called *handoff*
- **network infrastructure** - the larger network with which a wireless hosts wishes to communicate

#### Classifications
At a high-level, you can classify wireless networks in terms of (1) whether a packet in the network crosses a single hop or multiple hops, and (2) whether there is infrastructure such as a base station being connected to in the network.

**single-hop infrastructure-based**
- have a base station in the network that is connected to some infrastructure
- example is an WiFi access point (base station) connected to the Internet (infrastructure)

**single-hop infrastructure-less**
- no base station, instead it can coordinate the transmission of other nodes
- example of this is Bluetooth that connects small wireless devices

**multi-hop infrastructure-based**
- a base station is present but wireless nodes may need to relay their communication through other wireless nodes
- example is a mesh network

#### Wireless network characteristics
If a simple wired home network were to be replaced by a wireless one, the wired NICs of the hosts would have to be replaced by wireless NICs, and the switch would have to be replaced by an access point. But no changes would be needed from L3 and up.

There are important differences between a wired and a wireless network. These differences mean that bit errors are more common in wireless networks.
- **decreased signal strength** - EM waves attenuate as it passes through matter
- **interference from other sources** - other devices transmitting at the same frequency will interfere with the signal