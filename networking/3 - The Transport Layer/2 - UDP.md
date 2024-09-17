**UDP** is a no-frills braindead protocol used for simple multiplexing-demultiplexing and error-checking. In fact, UDP is so braindead it's as if the application layer is talking to the network layer directly. Really, the only things that UDP does is add source and destination ports for multiplexing / demultiplexing, as well as 2 small header fields, and then passes it off to the network layer.

#### Why use UDP
At first glance, it may seem hard to believe that some applications prefer UDP over TCP. After all, TCP's service model is much more expansive. But here are some reasons why:

- **control over timing / speed** - some applications wouldn't want their messages throttled by the congestion control mechanism that TCP provides when there's some congestion in the network route
- **no delays** - some applications also wouldn't want to get delayed by the connection establishment of TCP
- **no connection state** - because UDP doesn't create sockets for every connection, it doesn't have to track any parameters about the client
- **small packet overhead** - TCP has 20 bytes in every segment to support its service model, whereas UDP only has 8 bytes

Also, applications that use UDP can also tolerate a small amount of data loss in exchange for this speed. And for applications that need some level of reliability, this functionality needs to be built in the application itself when using UDP by adding acknowledgment and retransmission mechanisms.

#### UDP Segment
A UDP segment consists of:
- source port number
- destination port number
- length (header + payload)
- checksum
- payload (application message)

![[udp-segment.png]]

**UDP Checksum**
The checksum of a UDP segment is computed by adding all the words in a segment and computing its 1s complement. The receiving end then calculates the sum of all the words including the checksum. If the result is all 1s, the segment hasn't been tampered with. The reason why checksums and error-checking is still added at the transport layer despite it being added also in other layers is because there's no guarantee that the other layers *would* add error-checking, such as L2.

Also, even though UDP has error-checking, it merely just drops the packet or passes on a warning, but doesn't attempt to recover from errors.