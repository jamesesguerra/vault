A **firewall** is a combination of hardware and software that isolates an organization's internal network from the public Internet, allowing some packets to pass and blocking others. It has 3 goals:
- all traffic, both incoming and outgoing, must pass through the firewall
- only authorized traffic, as defined by the local security policy, will be allowed to pass
- the firewall itself must be immune to penetration

#### Traditional Packet Filters
Packet filtering occurs at the gateway router which connects a home / office / organization to its ISP. A packet filter examines each packet in isolation, determining whether or not it should be allowed to pass according to several factors:
- source / destination IP address
- protocol type in datagram field ie TCP, UDP, ICMP
- source / destination port number
- TCP flag bits

#### Stateful Packet Filters
In traditional packet filters, filtering decisions are made on each packet in isolation. Stateful packet filters track TCP connections, and use this knowledge to make filtering decisions. It basically maintains a table of ongoing TCP connections.

This allows the packet filter to block, for example, packets with source port 80 and TCP ACK bit set to 1 if its not part of an ongoing TCP connection so that it can have a rule that allows users in the organization to browse the web while safely not being sent packets from intruders.

#### Application Gateways (WAF)
While packet filters can deny / allow packets based on TCP and IP header fields, they can't, for example, require authentication first. And so to have finer-level security, firewalls must combine packet filters with application gateways. 

**Application gateways** look beyond IP/TCP/UDP headers and make policy decisions based on application data. It's an application-specific server through which all application data (inbound and outbound) must pass.
