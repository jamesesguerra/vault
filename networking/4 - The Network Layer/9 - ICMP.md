The **Internet Control Message Protocol (ICMP)** is a protocol used by hosts and routers to exchange network-layer information. It sits above the IP layer as it's included in an IP datagram. Even though it architecturally sits atop of it, ICMP is still considered a part of IP. IP demultiplexes the packet into the ICMP protocol the same way it would with TCP / UDP.

Some ping servers / clients aren't run as processes and are built into the OS instead.
#### Messages and Types
ICMP messages have a *type* and a *code* field. They're mainly used for signaling errors (such as not being able to reach an IP address), but are not restricted to that.
##### Traceroute
The way the traceroute program works is that it starts out by sending an ICMP packet to the destination with a TTL of 1, after which it sends more packets with incrementing TTL numbers. The effect is that when a router receives a packet and decrements the TTL, it sends back a warning ICMP message, from which the client can compute the RTT. 

The reason the ping client knows when to stop sending packets is because the destination host will eventually receive the packet and send back a reply that it's unreachable as the UDP port number is an unlikely one.
