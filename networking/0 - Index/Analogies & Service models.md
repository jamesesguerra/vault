
1. **The application layer**
- the "language" or protocol that network applications speak
- the Web speaks HTTP
- email speaks POP / IMAP / SMTP / HTTP
- letter with something written on it, and the protocol is how people interpret the messages (ie how they read it and what they do in response to certain messages)
- encapsulates the letter in an envelope and slips it through your bedroom door - socket

2. **The transport layer**
- gets the application message from the source process to the destination process
- a very kind cousin that collects letters from the postal service and forwards them to the correct rooms in the house, as well as collects letters from rooms and sends them to the postal service
- they make it so that you can establish a *logical connection* with your recipient as you always don't care how your letter gets to its destination
- you can choose what kind of cousin to hire for this service: whether they synchronize with the other cousin in the destination's house, whether they are able to send feedback to the other cousin and resend letters

3. **The network layer**
- gets the message from the source host to the destination host
- the postal service that uses an IP address to figure out its destination from source house to destination house
- uses parts of an IP address (real address) and asks routers (toll booths) for the what highway to exit / take
- routers (toll booths) don't know the complete path, they just know the next exit to take, the postal service (SDN controller) has an algorithm for computing the least cost path from one house and sends them to each toll booth, without going through highways that aren't illegal
- you can pass through different cities (AS) that employ their own way of routing, but ultimately a higher body computes for the routing between cities (BGP)

4. **The link layer**
- gets the message from one network node to another network node through a communication link
- the transportation mode of the person carrying the envelope (whether car, bus, plane)
- physically gets the person from one place to another through a transport segment (highway)