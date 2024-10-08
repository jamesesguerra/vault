There are 2 kinds of network links:
- **point-to-point** - one sender and one receiver
- **broadcast** - multiple senders and receivers; one node's message will be received by every other node

Therefore, the problem with the of coordinating the multiple sending and receiving node's access to a shared broadcast channel, aka the **multiple access problem** is of central importance to the link layer. Broadcast channels are like parties in that there are multiple people who are communicating at a time. Some want to talk while others want to listen. If everyone is speaking at the same time, the receivers wouldn't be able to understand their messages properly in the same way node receivers wouldn't understand frames that collide with each other. 

#### Multiple Access Protocols
Humans have devised protocols to address the multiple access problem, protocols like:
- *"Give everyone a chance to speak"*
- *"Don't interrupt when someone is talking"*
- *"Don't monopolize the conversation"*

Computers have also addressed this with **multiple access protocols**. There are 3 categories:

#### Channel Partitioning
The goal of channel partitioning is to allocate some amount of the available bandwidth evenly to all nodes. That is, if a channel has a bandwidth of *R bps* and *N* nodes share the channel, *R / N* of something will be allocated to each node. These protocols do well when the load is high but are inefficient when the load is low.

There are 3 types:
1. **time-division multiplexing (TDM)**
- the thing you're allocating to nodes is a *time window*  to send over the link
- like having people take turns speaking
- fair, no collisions, but each node will have constrained bandwidth even if they're the only one who wants to send data

2. **frequency-division multiplexing (FDM)**
- the thing you're allocating is a portion of the band aka *frequency*
- has similar pros and cons with TDM

3. **code-division multiple access (CDMA)**
- assign a unique code to each node and encode their data using the code
- receiver has to know the code as well
- every node can send simultaneously and no collisions will occur

##### Random Access
In random access, nodes always transmit at the full rate of the channel. They do well when the load is low since each node has the channel all to itself, but struggle when the load is high as collisions are more likely.

1. **Aloha**
- nodes always transmit at the full rate of the channel
- *impolite* speaker who speaks when they want
- when a collision happens, each node is programmed to retransmit their frames repeatedly until it can get through w/o a collision
- each node sends it after a random unique delay; in this manner, because each node's delay is different, a frame is bound to get through sooner or later

2. **Carrier Sense Multiple Access with Collision Detection (CSMA / CD)**
- *polite* speaker who listens if anyone's speaking before they speak
- CD makes the polite speaker back off whenever they're already speaking but realize someone has already began speaking
- when the frame backs off, the node waits a certain interval before sending again

##### Taking turns
The protocols that take turns try to get the best of both worlds of channel partitioning and random access in that they try to do well with high and low load. However, their downsides are that each node has to wait before they can access the channel, some latency is required to facilitate the mechanism, and that each controller of the mechanism is a single point of failure

1. **Polling**
- a centralized controller polls each node sequentially if they have data to send
- if they do, the channel is all theirs

2. **Token passing**
- nodes are arranged into a ring and pass along a token
- while they have the token, they have full access to the channel
- once finished, they pass the token to the next node