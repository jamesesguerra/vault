## azure virtual networks
- enable az resources (VMs, web apps, DBs) to communicate with each other, with users on the internet, and with on-prem client computers. 
- an extension of your on-prem network that can link with other az resources
- **public endpoint** - resources have public IP; can be accessed from anywhere in the world
- **private endpoint** - resources exist within a virtual network; have private IP

## key networking capabilities
### isolation & segmentation
- **setting up virtual network**  defining a private IP address space by using either public/private IP address ranges (isn’t internet-routable)
- you can divide address space into subnets
- **name resolution** - azure name resolution service or internal/external DNS

### internet communications
- enable incoming connections from the internet by assigning a public IP to a resource, or putting it behind a public load balancer

### communicate between azure resources
- can connect VMs, VM scale sets, AKS
- can also connect to SQL DB and storage accounts

### connect with on-prem resources
- **point-to-site vpn** - client computer initiates an encrypted VPN connection to the az virtual network
- **site-to-site vpn** - link on-prem gateway to az vpn gateway in the virtual network; the devices in az will appear to be on the local network
- **azure expressroute** - dedicated private connectivity to az that doesn’t go through the internet; used when you need greater bandwidth and higher levels of security

### route network traffic
- **you can control routing by:**
  - creating custom route tables
  - propagating BGP routes to az virtual networks

### filter network traffic
- **network security groups** - contain multiple inbound/outbound security rules (like a firewall)
- **network virtual appliances** - VMs that can be compared to a hardened network appliance; carries a particular network function such as running a firewall or performing WAN optimization

### connect virtual networks
- **peering** - allows 2 virtual networks to connect directly to each other
- network traffic is private and only travels on the microsoft backbone
- **user-defined routes (udr)** - control routing tables between subnets within a virtual network; greater control over network traffic flow
