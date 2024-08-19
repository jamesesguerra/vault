### What is the internet?
In terms of basic hardware and software, the internet is a mechanism for computing devices to communicate with each other. The devices connected to the internet are called **hosts** or **end systems**.

**End systems** are connected to each other via **communication links** and packet switches. There are different kinds of communication links e.g. coaxial cables, UTP (ethernet) cables, optical fiber, and radio spectrum.

When one host sends data to another system, it segments the data and adds headers to each segment. These result in things called **packets** which are sent through the network, being reassembled at the last point. A **packet switch** takes an incoming packet and forwards it to one of its outgoing communication links. The 2 types of packet switches are:
- routers - used in the network core
- link-layer switches - typically used in access networks

Network communication can then be likened to transporting goods from one building to another. One building splits their cargo among multiple trucks, and they all move along highways and streets. Once they all reach their destination, they are unloaded and grouped together once again.

In terms of the perspective of what the Internet provides, it can also be thought of as service provider, providing different kinds of useful services to end systems. These services are called **distributed applications** since they involve multiple hosts that exchange information.

#### ISPs
The hosts mainly access the internet through an ISP. Each ISP in itself is also a network of communication links and packet switches. They provide a variety of types of network access to the end systems such as broadband access via cable modem or DSL, or mobile wireless access.

There are also tiers to ISPs wherein higher-tier ISPs connect lower-tier ones. Higher-tier ISPs contain high-speed routers and fiber-optic links.

#### Protocols
End systems and packet switches use protocols to control the flow of information within the internet. The 2 most popular and important ones are:
- Transmission Control Protocol (TCP)
- Internet Protocol (IP)

Protocols are important because they allow different systems to speak the same language. This is also why it's important to enforce standards. These standards are developed by the Internet Engineering Task Force (IETF).

#### Sockets
In order for distributed application to work, each application living on a host needs to know how to communicate to other applications living on other hosts. This functionality is provided by a socket interface -- rules that specify how an app running on one host can deliver data to another app on another host.

Sockets interfaces can be likened to "postal service interfaces" in a postal service. If Alice wants to send a letter to Bob, she can't just write the letter (data) and drop it out her window. She has to put the letter in an envelope, write Bob's address, seal and stamp it, and drop it off at the postal service mailbox. Similar to postal services with which Alice can choose from, there are also many internet services.

#### Network Protocols
Protocols are the rules that define communication between two hosts. For a human analogy, protocols can be thought of the rules that we abide by when we talk to strangers. If we want to ask someone what time it is, we say "hi", and if they say "hi" back, only then will we continue with what we wanna say. If they don't reply or reply something like "i'm busy", that's our signal to stop. Networking is very similar to this in that there are specific messages we send and specific actions we take in response to received messages. Furthermore, if two people don't follow the same protocol i.e. if one person has manners and the other doesn't, no useful work can be accomplished. The same is true for networking as it takes 2 communicating hosts running the same protocol to get something done.

Network protocols govern the communication between all hardware and software components of some device on the Internet. They define the format and order of messages exchanged between 2 or more communicating entities, as well as the actions taken in response.

---

### Types of Access Networks
Access networks are the network that connect end systems (computing devices at the edge of the Internet), to the first router (edge router), to other distant systems.

#### Digital Subscriber Line (DSL)
A customer gets internet access through the same company that provides them with a telephone line. Their DSL modem uses the existing telephone line to exchange data with a DSL access multiplexer at the telephone office. The home DSL takes digital data and translates it to high-frequency tones for transmission over telephone wires. The wires are setup for multiplexing so that both data and telephone signals can travel at the same time. DSL is only recommended for households that are near the central office.

#### Cable Internet Access
This is similar to DSL except the customer's cable television provider gives them Internet access through their cable TV infrastructure. They provide fiber cables to neighborhood-level junctions, and from there, coaxial cables connect the junctions to the homes. This hybrid setup is why it's also called hybrid fiber coax (HFC). One notable characteristic of cable internet is that it's a shared broadcast medium ie if multiple users are downloading a file at the same time, the speed will be significantly decreased.

#### Fiber to the Home (FTTH)
FTTH is when the central office runs a fiber optic cable directly to your house.

### Local Area Network (LAN)
A LAN is used to connect end systems to the edge router. The most popular LAN technology is Ethernet wherein end systems connect to an Ethernet switch via twisted-pair copper wires. These switch/es are then connected to the larger network via edge routers. Wireless LAN is also available via an access point that is connected to the switch which wireless devices can connect to. 

---
## The Network Core
If the LAN connects end systems to the edge router, and an ISP gives an edge router access to other distant systems, the network core is the collection of communication links and packet switches that allow the communication between 2 distant systems.

### Packet Switching
In order for one end system to send a message to another, it has to segment and encapsulate it in a **packet**. It then sends the packets to packet switches which are tasked to take incoming packets and forward them out. Most packet switches follow a rule called store-and-forward transmission, wherein they only forward a packet once all the bits of that packet are received. Otherwise, they store it in a buffer and wait for the remaining packets to arrive. 

The packet switch's output buffer is also where packets are stored whenever they're received, but the output link which they're meant to go out from is busy transmitting another packet. This is what packets can suffer from, something called **queuing delays**. If the output buffer is full, packet loss will occur. This can happen when the link *to* the router is faster than the link *from* the router.

In this example, packet loss will occur if the speed of the packets reaching the router is greater than 15Mbps.

![[packet-loss.png]]

### Forwarding Tables
A router has many outbound links that data can be sent over. The way it knows which outbound link to forward a packet is by using its forwarding table, which is a table of destination (or portions of destination) addresses and their corresponding outbound link. The end-to-end process is then similar to a person who prefers to ask for directions rather than using a map. Each router will handle a portion of an address and forward the packet accordingly. 

So for example, the address is "156 Lakeside Drive, Orlando, Florida". The first router will take care of the "Florida" portion, the next router will take care of "Orlando", so on and so forth until the packet reaches its destination address.