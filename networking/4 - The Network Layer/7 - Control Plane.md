Forwarding and flow tables are managed and installed in 2 ways:
- **per-router**
	- routing function is computed at every router
	- routers communicate with each other to configure their tables
- **logically centralized (SDN)**
	- routers communicate with 1 logically-centralized controller to get their configuration
	- control agents don't interact with each other

### Routing algorithms
The goal of a routing algorithm is to compute for the **least-cost path** between 2 nodes. A network can be modeled by a weighted graph wherein nodes are routers and edges are the physical links connecting routers. A path therefore, is a series of nodes that have an entry in the edge list of the graph ie each node is a neighbor of the next. 

#### Algorithm classifications
**Centralized or decentralized**
Centralized routing algorithms are algorithms that already know the global state of the network. That is, it takes in the physical connectivity between all nodes, as well as their costs, as input. These are also called *link state (LS) algorithms* On the other hand, decentralized routing algorithms use an iterative process to determine the state of the network links by passing messages between routers. This is typically suited to setups wherein the control plane function is performed at every router.

**Static or dynamic**
Static routing algorithms are ones that change slowly over time, typically when a network admin manually edits link costs. On the other hand, dynamic routing algorithms are ones that change based on the network traffic load as well as network topology.

**Load-sensitive or load-insensitive**
Load-sensitive algorithms are ones that vary dynamically to reflect the current congestion on the underlying link. On the other hand, load-insensitive algorithms are ones that don't explicitly reflect its links current level of congestion.

#### Link state (LS) algorithm
LS algorithms use global information in the form of the overall network topology and link costs as input. Each node broadcasts link state packets to all other nodes in the network. The result is that every node has a complete view of the network, and can run the LS algorithm themselves. 

The LS algorithm in practice is Dijkstra's algorithm, which results in a forwarding table with nodes and their respective links to forward to for the shortest path.

#### Distance-vector (DV) algorithm
The DV algorithm doesn't use global information. Instead, it's asynchronous, distributed, and iterative in that each node only receives information from one or more of its directly attached neighbors asynchronously and will stop by itself. And so unlike LS algorithms that need to know the global state of the network before running, all DV algorithms need to know to run are the costs of the links to its neighbors and information it receives from these neighbors.

---
### Autonomous Systems (AS)
The problem with using link-state or distance-vector algorithms to route packets is that there are billions of routers in the world who want to receive / send packets to other routers. This introduces 3 major issues:
- routers couldn't possibly have the memory to store a forwarding table that contains entries for every other router in the world
- network links would be swamped with router messages as routers execute their shortest-path algorithms
- there is no autonomy as to what kind of routing you can do in your organization as every router in the world would be forced to use the same routing protocol

To solve this, routers are divided into domains under their respective organizations / ISPs. These domains are called **autonomous systems (AS)**. Within an AS, network operators can choose what routing protocol to use and have full autonomy over how routing is done in their AS.

Routers inside an AS obtain routing information for routing to other routes within their AS via an *intra-AS routing protocol*. To obtain routing information for routing outside their AS, an *inter-AS routing protocol* must be used.

#### Intra-AS routing (OSPF)
1 of the most popular routing protocols implemented within an AS is Open Shortest Path First (OSPF) which is a link state routing protocol. This uses Dijkstra's algorithm.

#### Inter-AS routing (BGP)
Within an AS, there is always a *gateway router* that is the ingress / egress point for BGP routing information for that AS. A gateway router is attached to other gateway routers in other ASs and is responsible for carrying out a protocol called **Border Gateway Protocol (BGP)**.

BGP allows routers to do 2 things:
1. advertise to neighboring routers that it exists so that they know about the AS's subnet
2. determine the best routes to other subnet prefixes

There are also 2 types of BGP: 
1. **eBGP** - BGP among different gateway routers
2. **iBGP** - BGP within an AS (gateway routers propagating BGP information to the internal routers)

##### IP Anycast
BGP also naturally supports IP anycast -- a mechanism to address a packet to the same IP, but eventually deliver it to a server that is the closest to the client. IP anycast is used by DNS and CDNs. The way it works is that you have multiple servers wherein your content / data is distributed in that have the same IP address and uses BGP to advertise this information. And so when routers configure their routing table, they'll use the most efficient path to that IP address, which won't be the same physical location / server for every router.