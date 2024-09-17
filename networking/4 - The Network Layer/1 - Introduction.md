**Goal** - deliver packets from one host to another ie facilitate host-to-host communication

#### Data & control plane
The network layer can be composed into:
- **data plane** - refers to the per-router functions of the network layer to determine how a datagram arriving on one router is forwarded to one of the output links (forwarding protocol)
- **control plane** - the network-wide logic that controls how a datagram is routed along an end-to-end path from source to destination (routing protocol); its primary role is to coordinate the local, per-router actions so that a datagram eventually gets to its destination

These are traditionally implemented together monolithically, but Software-defined networking (SDN) explicitly separates them by implementing the control plane functions in a separate service, a remote "controller".

#### Forwarding & routing
Two important network functions have to be done to deliver a packet end-to-end:
- **forwarding** - the *router-local* action of transferring a packet from an input link to an appropriate output link
- **routing** - the *network-wide* act of determining the end-to-end path a packet will take from source to destination

##### Traditional control plane
A router forwards a packet by inspecting 1 or more fields in it and indexing against its **forwarding table** to retrieve the output link. The control plane comes into play as it is the mechanism for determining the contents of a router's forwarding table. Traditionally, both the forwarding and routing functions live in each router, and they send messages to each other to coordinate their forward tables.

![[trad-routing.png]]

##### SDN control plane
Configuring routers using the traditional approach is error-prone. An alternative approach is to separate the control plane from the routers and have a separate device serve that purpose. In an SDN setup, a remote controller computes and distributes forwarding tables to the routers.

![[sdn-forwarding.png]]

In both the traditional and SDN approaches, routers and/or controllers communicate with each other by sending routing messages that contain routing information according to a routing protocol.
### Network layer service model
In reality, the Internet network layer's service model is a **best-effort service**. This means that there are no guarantees in terms of delivery, in-order delivery, E2E delay, minimum bandwidth. However, combining this with adequate bandwidth provisioning seems to be more than good enough for the applications we have today.