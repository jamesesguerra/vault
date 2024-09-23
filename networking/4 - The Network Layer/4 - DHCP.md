While a range of IP addresses are obtained by ISPs and organizations through ICANN, hosts obtain IP addresses automatically through the **Dynamic Host Configuration Protocol (DHCP)**.

Instead of network admins manually configuring new IP addresses at the routers, through DHCP, new hosts can request for one when trying to connect to the subnet. This is useful as there are certain places wherein IP addresses get leased for a short amount of time e.g. libraries, coffee shops. You can configure it to either give out the same IP addresses to the same hosts (renewing) or a different IP address every time (temporary).

DHCP is a L7 client-server protocol that works on top of UDP. It's considered a **zero-conf** / **plug-and-play** protocol since it allows the network to automate a certain function (giving out IP addresses) without any configuration. Also, it not only allows a host to get an IP address, but also allows them to know who is their default gateway (first hop router), their DNS server, and their subnet mask.

#### DHCP process
1. **DHCP Discover**
- the first step is for the client to perform a type of service discovery wherein it tries to look for a DHCP server on the subnet
- client sends a packet to the broadcast IP `255.255.255.255` with `0.0.0.0` (this host) as the source IP
- the packet is sent over UDP to port 67 coming from port 68 on the client
- the packet is broadcasted by the link layer to all nodes in the subnet

2. **DHCP Offer**
- the DHCP server answers with a message containing an IP address that is available, the transaction ID, network mask, and an address lease time
- the packet is once again broadcasted since the client still doesn't have an IP address

3. **DHCP Request**
- a client can receive multiple offers from DHCP servers
- it chooses one of them and requests to use the IP by sending a request message which echoes back the configuration parameter

4. **DHCP ACK**
- the server responds to the DHCP request by confirming the requested parameters

