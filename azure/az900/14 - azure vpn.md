## virtual private network
- uses an encrypted tunnel within another network
- used to connect 2 or more trusted private networks to one another over an untrusted network
- traffic is encrypted to prevent eavesdropping or other attacks

## vpn gateway
- type of virtual network gateway deployed in a subnet of a virtual network
- **enables connection of the ff:**
  - **on-prem to virtual network via site-to-site**
  - **individual devices to virtual network via point-to-site**
  - **virtual networks to each other via network-to-network**
- you can deploy only 1 vpn gateway in each network. but one gateway can connect to multiple locations

### types of vpns
- distinction between the types is how they determine which traffic needs encryption
- the method of authentication
1. **policy-based VPN** - packets that should be encrypted through each tunnel have static IPs
2. **route-based VPN** - static / dynamic routes using IPSec tunnels; are preferred for on-premises connections since they’re more resilient to network topology changes such as the creation of new subnets

## high-availability scenarios
you want VPN to be highly-available and resilient to keep your information safe

### active/standby 
- by default there are 2 VPN instances deployed — 1 active and 1 standby
- standby assumes responsibility for connections w/o user intervention
- connections are disrupted but are restored within a few seconds for planned maintenance and within 90 seconds for unplanned disruptions

### active/active
- assign public IPs to each VPN instance and create separate tunnels from each on-prem device to each IP

### expressroute failover
- expressroute circuits has resiliency built in but aren’t immune to physical problems that affect the cables
- solution: provision a VPN that uses the internet so there’s always a connection to your az virtual network / resources

### zone-redundant gateways
- deploy gateways in different azure availability zones to protect on-prem network connectivity from zone-level failures
- use different SKUs and standard public IPs instead of basic ones