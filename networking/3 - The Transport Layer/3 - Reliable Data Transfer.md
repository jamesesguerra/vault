Reliable data transfer is one of the most important networking topics that not only applies to TCP but also to L7 and L2.

The service model / interface of a reliable data transfer protocol is a data channel wherein bits can pass and:
- they won't be corrupted (0s won't turn into 1s and vice versa)
- they won't be lost / dropped
- they will arrive in order

#### ARQ
A reliable data transfer protocol (RDT) needs a way for two end systems to handle an unreliable connection underneath. As an analogy, humans handle long sentences while talking on the phone by replying with several acknowledgments, be it positive ("OK" if you heard the message correctly) or negative ("Please repeat that" if you need them to repeat it). Protocols based on this kind of retransmission are called **Automatic Repeat reQuest** protocols.

They are based on three capabilities:
1. **error detection** - adding checksums
2. **receiver feedback** - sending ACK / NAK packets back depending on the POV of the receiver
3. **retransmission** - sender retransmission in case NAK packets are received

#### RDT versions
These versions assume a unidirectional transfer from an application-layer POV wherein a sending process is sending a message to a receiving process. But from a transport-layer POV, the exchange is bidirectional because of the mechanisms in place that are required to achieve reliable data transfer.

**RDT 1.0**
Assume that the lower layer protocol that the transport layer protocol uses is a perfectly reliable data channel wherein there are no corruptions, and that packets are delivered and delivered completely and on time. In this scenario, there is no need to do anything special on either end, sender or receiver. 

![[rdt1.0.png]]

**RDT 2.0**
Assume that the underlying data channel is the same as RDT 1.0 with one difference, packets may now be delivered with corruptions. Now, we do need something like ARQ. 

In summary, the RDT sender waits for a call from the upper layer to `rdt_send`. Once this method is invoked, the RDT sender creates a packet and sends it to the UDT via `udt_send`. From here, it goes into a state from waiting for a call from L7 to waiting for a call from L4 for an `ACK` or a `NAK`. If RDT sender gets an `ACK`, it can go back to a state of waiting for a call from L7. If it gets a `NAK`, it resends the packet created last time, and stays in a state of waiting for an `ACK` or a `NAK`. It's important to note that this uses a *stop-and-wait* protocol wherein once the sender is in a state of waiting for an `ACK`/`NAK`, it cannot receive any more packets from `rdt_rcv`

For the RDT receiver, it starts off in a state of waiting for a call from `rdt_rcv`. From here, it receives a packet, and it will now check whether the data is corrupt or not. If it isn't, it extracts the data from the packet, delivers the data to L7 via `deliver_data`, and sends a new packet with an `ACK` message to `udt_send`. Otherwise, it simply sends a `NAK` to `udt_send`

![[rdt2.0.png]]


**RDT 2.1**
RDT 2.0 has one fatal flaw: the sender RDT has no way of knowing whether the receiver RDT got the data correctly if the `ACK`/`NAK` itself gets corrupted. To solve this, the sender adds a new field to the packet which has a **sequence number**. This is incremented per new packet. 

A sequence number's end goal is to tell the receiver whether or not the current packet it's handling is a retransmission or a completely new packet, or in short, to **handle duplicates**. For example, when a receiver is expecting a packet with sequence number 1 but receives a packet with sequence number 0, it can then tell that the sender didn't receive its acknowledgment of the previously sent packet correctly. Therefore, it sends an `ACK` again, and stays in the same state to expect a sequence number 1 again as it assumes that the sender will already receive the acknowledgment correctly.

![[rdt2.1.png]]

Mechanisms for communicating reliably over a channel that may introduce bit errors and loss:
- **error-checking** - for checking if data is corrupt; for a sender it retransmits, for a receiver it sends a `NAK`
- **receiver feedback (ACK / NAK)**  - for the sender to know it should retransmit
- **sequence numbers** - for the receiver to know that the packet it's handling is a retransmission or not; if retransmission it tells the sender it got the message and to move on (duplicate packet detection)
- **sender retransmission** - a sender can send an already-sent packet in case the receiver sends a `NAK` / the receiver feedback is corrupted
- **timeouts** - for retransmitting lost messages

**RDT 3.0**
Assume now that the unreliable data channel can not only introduce bit errors, but also packet loss. This change forces us to add a new protocol mechanism as the current mechanisms we've implemented are based on **receiver feedback**. That is, the only way a sender will know when / if to retransmit a packet is to send it a feedback packet, even if corrupted. Now, it's possible that a sender will never know it should retransmit if the receiver feedback packet gets dropped.

The protocol mechanism to be introduced is a timer that expires after some reasonable amount of time. Like in human conversations, some timer goes off after we've said something to someone, and after not hearing anything back after a few seconds, we feel the need to say the message again. So in the RDT, a timer is started once the packet is sent to the UDT, and stopped once an `ACK` is received. There is also a timeout on the timer that can interrupt the normal behavior and retransmit a packet. 


![[rdt3.0.png]]

##### Pipelined protocols
The problem with RDT 3.0 is that its sender utilization is very low because of the nature of its stop-and-wait mechanism. Thus it cannot maximize the bandwidth of the communication links. To solve this, pipelined protocols can be used to send packets without waiting for acknowledgments. For this to work, the range of sequence numbers have to be increased, as well as the buffer size.

#### Go back n
**N** - maximum number of allowable unacknowledged packets in the pipeline
**base** - sequence number of the oldest unacknowledged packet
**nextseqnum** - next available sequence number 

![[rdt-pipeline.png]]

**notable intervals**
- `[0, base - 1]` - packets that have already been transmitted and acknowledged
- `[base - nextseqnum - 1]` - packets that have been transmitted but not yet acknowledged
- `[nextseqnum, base + N - 1]` - can be used for packets that can be sent immediately
- `[base + N, rest]` - unusable

**TLDR**
Receiver acknowledges packets *cumulatively* ie only if the sequence numbers are in order. If it receives an out-of-order packet, it sends an acknowledgment for the most recent in-order packet, until it finally receives the next in-order one. On the sender side, a timer is started for the oldest unacknowledged packet. It can only send packets within the boundaries of the window and cannot move forward until the window moves. Once the timer expires, it sends *all* the packets within the window to the receiver.

#### Selective repeat
The problem with go back n is that if the window size is too big, it becomes wasteful and can slow down a network as it might resend a huge number of packets for just 1 packet that hasn't been acknowledged yet. To solve this, selective repeat *individually* acknowledges packets and buffers out-of-order packets to be sent to the upper layer later on once it can send them in order. At the sender side, it requires more memory also as it now needs to start and stop a timer for each individual packet, much like RDT 3.0.