**Scenario:** you're starting a company and want to advertise it using a web server, mail server for your employees, and a DNS server. How do you go about obtaining Internet presence

1. **Obtain Internet connectivity through connecting to a local ISP**
- your company would have a gateway router which is connected to a router in the local ISP through DSL / leased line etc.
- a SOHO router can also be considered as the gateway router for small homes / enterprises
- ISP assigns you a range of IP addresses, and you assign them to your servers

2. **Register a domain through an Internet registrar**
- try to obtain a domain like `<your-company-name>.com`

3. **Obtain presence in the DNS system**
- provide registrar with an NS record telling them to make an entry in a .com TLD server for your DNS server
- include entries in your DNS server that map the host name of your web server to its IP address, mail server, etc.

4. **Advertise IP prefix of your company**
- when you connect to your ISP, they propagate your prefix and advertise it using BGP to other routers so that other routers in the world will know where to route a packet when its destination IP is your prefix