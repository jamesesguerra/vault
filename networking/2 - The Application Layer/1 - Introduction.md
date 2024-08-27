Network applications are networking's *reason for being*. If no useful network application's existed, we wouldn't have any reason to have 2 computers communicate with each other.

There's also a difference between network services and network applications. Network services are services that cater to specific networking activities that allow application's to talk to each other. Meanwhile, network applications are services that cater to user activities such as email, web surfing, instant messaging, and P2P file sharing. 

### Application Architecture
At the core of network application development is the creation of programs that run on different end systems and communicate with each other over a network. And so before you create a network application, you have to think about how your app will be structured over the various end systems. Each part of the app can be similar, as in a file-sharing app, or distinct, like in a web application where one program is a browser and another is a Web server.

#### Client-server
The first kind of architecture is the client-server architecture. In this architecture, there is an always-on host called the **server**, and another host that requests for something, called the **client**. It's also important to note that each server's IP address is known and fixed so that a client can always send a packet to it, and that 2 clients don't connect to each other directly. The most famous network apps that use this architecture are the Web, FTP, email, and Telnet.

As the number of clients increases, the load on the server also increases. This means that you'll soon have to vertically / horizontally scale the server. For the most prevalent network applications, dedicated data centers are built to service thousands of clients.

#### Peer-to peer (P2P)
In a P2P architecture, the app exploits direct connection between intermittently connecting hosts called **peers**. There is no dedicated server that services requests and so it's a decentralized architecture. One of the strongest advantages of this is its self-scalability as even if the number of hosts increases, therefore increasing the number of requests, the capability of the app as a whole to service requests also increases. However, because it does have issues with security because of its decentralized nature.


### Sockets
In reality, it's not actual network applications that communicate with each other. Instead, it's actually processes -- running instances of a program that send messages to each other. In this case as well, we aren't talking about interprocess communication ie processes communicating on the same host. We're talking about processes communicating on *different* hosts.

In the context of processes, there is always a client process that initiates a connection to a server process. A server process is the process that waits to be contacted to begin the communication session.

In order for a client process to contact a server process, its message has to go through a software interface called a **socket**. A process is analogous to a house and a socket is analogous to a door. This is where it sends and receives messages. It is the interface between L7 and L4 within a host, essentially the API between the application layer and the transport layer.

![[sockets.png]]

#### IP Address and Port Number
To send a message to a destination host, the sending process must identify the receiving process. It does this by specifying 2 pieces of information:
1. IP address - the address of the destination host
2. port number - the socket where the receiving process can be reached

This can change the analogy because this time, the house can be identified by the IP address. The port number specifies the door number to a specific room, and a room is a running process. You must specify the port number as a host can run network applications running on different sockets, just as there would be rooms in a house with their own door entrances.

#### Transport Layer Protocols
A socket is the interface that links the application and transport layers. An application sends out its message through its socket and assumes that there is some transportation service on the other side to get its message to the receiving socket. But as an application developer, you also have the choice of the transport layer protocol as different protocols offer different services. This is analogous to choosing whether your message should be sent by train / airplane etc.

#### Service Dimensions
The services that a transport layer protocol can be categorized into 4 dimensions:

1. **Reliable data transfer** - because packet loss may occur, some applications may suffer if data isn't received completely and correctly on the other end, this is why some protocols can enforce reliable data transfer that guarantees the receiving process will receive all the data. But for some applications such as video / audio streaming, this may not be the most important as some packet loss may result in a slight glitch
2. **Throughput** - some services can also guarantee a throughput at some specified rate; an application could request a service that transports bits at a rate of *r bits / sec*
3. **Timing** - applications can also request that packets be sent under a maximum time limit; this is useful for real-time applications where you'd want the receiving process to receive a packet in under 100ms, for example
4. **Security** - services can also encrypt the data being sent by the sending process, and decrypt it on the receiving process's end; this provides confidentiality as the packet moves through the network; other security services also include data integrity and end-point authentication

NOTE: In reality however, no real transport protocol provides timing and throughput guarantees. Applications have just found a way to circumvent this by coping with this lack of guarantee.

#### Internet Transport Services
1. **Transport Control Protocol (TCP)**
TCP makes available 2 services as part of its service model. 

The first is a connection-oriented service. Even before the sending process sends data, TCP alerts the client and server to prepare them for an onslaught of packets. After this *handshake* occurs, there is already a connection existing between the client and server, wherein each side can send packets at the same time, something called full-duplex mode. After they finish exchanging messages, the connection has to be teared down by the client.

The second kind of service is reliable data transfer. The communicating processes can be assured that TCP will deliver the data completely, in-order, and without errors.

Finally, TCP also adds congestion-control wherein it throttles the time at which it sends a message if the network is congested.


2. **User Datagram Protocol (UDP)**
UDP is a no-frills protocol that uses a connectionless service, and also doesn't provide reliable data transfer and congestion-control.


4. **Secure Sockets Layer (SSL)**
SSL isn't necessarily a 3rd transport protocol. Instead, it's an enhancement to TCP and UDP. Because TCP and UDP don't provide any security services, an application can implement SSL in the application layer to add data encryption, data integrity, and end-point authentication. By using SSL, the sending process's message first goes through the SSL socket, where it encrypts the data, before reaching the TCP socket. When the receiving TCP socket receives the message, it passes it to the receiving SSL socket which then decrypts the data.


### Application Layer Protocols
An application layer protocol defines how application processes running on different end systems pass messages to each other. It defines:
- the types of messages that can be passed
- the syntax of the messages
- the semantics of the messages (how fields are interpreted)
- and when and how a process sends and responds to messages

It's important to note that an network application isn't just an application layer protocol. An application layer protocol is just one (important) piece that makes up a network application. For example, a network application like the Web is composed of standards for a document format (HTML), Web servers, Web browsers, and an application layer protocol. And so as long as the client and server process follows the standard for the application layer protocol, they can communicate with each other properly and exchange Web documents.

In short, a network application is just a bunch of standards for client processes, server processes, and the format and meaning of the messages that they exchange with one another.