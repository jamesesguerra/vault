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

