**Transmission Control Protocol (TCP)** is a connection-oriented and reliable transport protocol.

#### The TCP connection
It's said to be **connection-oriented** because 2 processes must first establish a connection (handshake) ie send preliminary segments to each other to establish the connection's parameters (connection state). It's not an end-to-end system like circuit switching, instead, it's a **logical connection** between 2 processes since TCP only runs in the end systems and packet switches don't recognize it.

The connection established is also a **full-duplex** connection that is **point-to-point**. This means that while process X is sending data to process Y, process Y can send data to process X at the same time. And a single process can only send data to one another process ie multicast is not allowed.

**The 3-way handshake**
If a client process wants to connect with a server process, it must first establish one. In the socket programming exercises, this is done by:
```python
clientSocket.connect((serverName, serverPort))
```

The connection-establishment procedure goes like this:
1. the client sends a special TCP segment (no payload)
2. the server replies with a second special TCP segment (no payload)
3. the client replies with a third TCP segment

**Buffers**
Once client data is sent through a socket, this gets sent to a **send buffer**, which TCP eventually grabs chunks of "at its own convenience". Once it gets to the other host, it gets place into a **receive buffer** which the process reads from. A TCP connection between a sender and a receiver therefore has the following mechanisms:
- send / receive buffers
- variables
- socket connections

#### TCP segment structure
TCP segments contain header fields and a data field containing the application data. A **maximum segment size (MSS)** limits the size of the data field, chunking the application data if needed. TCP header fields are typically only 20 bytes compared to the 8 bytes of UDP.

Header fields include:
- source and destination port numbers
- checksum
- 32-bit sequence number 
- 32-bit acknowledgment number 
- 16-bit receive window - flow control
- 4-bit header length
- variable length options field - negotiating MSS
- 6-bit flag field
	- ACK - ACK bit is valid
	- RST - connection setup and teardown
	- SYN
	- FIN
	- CWR - explicit congestion notification
	- ECE
	- PSH - receiver should pass data to upper layer
	- URG - upper layer marks data as urgent

![[tcp-segment-structure.png]]


#### Sequence numbers and Acknowledgment numbers
These are 2 of the most important fields of a TCP segment.

**Sequence numbers**
It's important to note that a TCP sequence number is computed over the *stream of transmitted bytes* and not over the stream of transmitted segments. Therefore, the sequence number of a segment is the **byte stream number of the first byte in the segment**. For example, if a file is 500,000 bytes and the MSS is 1000 bytes, TCP will construct 500 segments out of the data (data size / MSS). The first segment will then have a sequence number of 0, the second will have 1000, the third will have 2000 and so on. And each sequence number is inserted in the header field of that TCP segment.

**Acknowledgment numbers**
On the other hand, the acknowledgment number a host uses is the sequence number of the next byte it's expecting from the host it's talking to. For example, bytes 0-535 has been received by Host A from Host B and Host A is about to send a segment to Host B. In this case, the acknowledgment number would be 536. Because of this, TCP acknowledges packets **cumulatively**, just like go-back-n. 


#### Timeout Intervals
Timeout intervals are computed with the formula: 
```
TimeoutInterval = EstimatedRTT + 4 * DevRTT
```

#### Reliable data transfer
TCP uses many mechanisms discussed in the RDT lesson to ensure reliable transfer since it works on top of IP -- an unreliable best-effort service. TCP ensures that the data stream that a receiving process reads from its buffer is uncorrupted, without gaps, without duplication, and in sequence. That is, the byte stream that was sent by the client is exactly the same byte stream that is read from the server.

TCP receiver is always ACK-ing the next in-order packet it's expecting. On the other hand, TCP sender is keeping track of the sequence number of the next byte to send. Therefore, there's a relationship between the sequence number of the sender and the acknowledgment number of the receiver and vice versa.

---
### Flow Control
When TCP accepts a packet that isn't corrupted and in-order, it doesn't actually send it directly to the application layer. Instead, it sends it to a receive buffer that will eventually get picked up by the application layer. This means that there's a possibility that the receive buffer gets overflowed if the sender keeps sending data faster than the application layer can read them.

To solve this, the TCP receiver advertises a **receive window** to tell the sender how much free space its buffer has. Because TCP is full-duplex, both sides maintain this variable and advertise it to one another. Thus, flow control is a *speed-matching service* that matches the rate at which the sender is sending against the rate the application is reading.

![[rwnd.png]]

---

### Connection management
The 3-way handshake goes something like:

1. **SYN** (client)
	- the sender sends a TCP packet with the SYN flag set to 1
	- it also adds a randomly-generated sequence number and adds it to the header field
2. **SYN ACK** (server)
	- the receiver initializes TCP buffers and variables (receive window etc) for the connection
	- the receiver sets the SYN and ACK flags to 1
	- the receiver increments the sequence number it receives from the client and sets it as its acknowledgment number, essentially saying "I received your sequence number"
	- it also chooses its own random sequence number and adds it to the header field
3. **ACK** (client)
	- the client allocates buffers and variables for the connection
	- the client sets the ACK flag to 1
	- the client increments the server's sequence number as well saying it received it, ands sets it as its acknowledgment number

When the connection wants to be closed, the client sends a packet with the FIN flag set to 1, and the server acknowledges. The server does the same as well. At that point, all the resources in both ends have been deallocated.

##### TCP connection lifecycle

1. **CLOSED** - no connection initiated yet
2. **SYN_SENT** - client initiates a connection by sending a SYN packet
3. **ESTABLISHED** - client has received SYN ACK and has responded with ACK
4. **FIN_WAIT_1** - client has sent FIN packet
5. **FIN_WAIT_2** - client has received ACK for FIN packet
6. **TIME_WAIT** - client has received FIN packet from server, and has sent ACK

The client spends a considerable amount of time < ~30 secs in the `TIME_WAIT` state as it waits in case the ACK for the server's FIN packet needs to be retransmitted (since the server won't be sending any more feedback).

If a client sends a SYN packet to a socket that isn't open, the receiver sends a packet with the RST flag set to 1, telling the sender that it doesn't have a socket bound to that port.

---
### Congestion control
How packets can be throttled to adjust to network congestion
- **end-to-end control** - TCP monitors the packet loss and delay, and adjusts its window size accordingly
- **network-assisted** - L3 helps L4 by sending it the maximum sending rate it can support on an outgoing link. Routers can also send a *choke packet* as a form of notification

The difference between congestion control and flow control is that flow control is used a speed matching mechanism between a sender and a receiver. It doesn't take into account the network path between a sender and a receiver, even though it's probably a contributing factor why a receiver's buffer's full.

**Goal**
The goal of congestion control is to limit the sender's transmission rate. It does this by making it always less than the minimum between the congestion window and the receive window.

**How it increases / decreases the transmission rate**
TCP can increase / decrease the transmission rate simply by increase / decreasing the congestion window. Because the transmission rate is a function of the congestion window, it's essentially a *self-clocking* mechanism

**How it detects TCP congestion**
It's simple: a sender can detect congestion in the network path if a timeout occurs since it knows a packet must've been dropped. Also, it can detect congestion when it receives the ACK packet followed by 3 duplicate ACK packets with the same sequence 
number. It can tell something went wrong since the receiver isn't ACK-ing the latest packets it sent.

**How does it know to increase the transmission rate**
TCP can tell when it can afford to increase the transmission rate simply by the rate at which it gets ACKs. When it gets an ACK, it knows that the path to the receiver is fine. The faster it receives ACKs, the faster it increases the transmission rate as well.

**Initially, how can it tell what speed it should be at**
If a TCP sender starts off too fast, it risks clogging the network / receiver with packets, and can result to more packet loss. On the other hand, if it starts off too slow, it might under utilize the maximum available bandwidth it actually has. And so the way it starts off is starting out slow and increasing the transmission rate more and more until it finally gets a timeout / duplicate ACK. It's somewhat like finding a *sweet spot*.




